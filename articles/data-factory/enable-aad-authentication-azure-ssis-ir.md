---
title: Activer l’authentification Azure Active Directory pour le runtime d’intégration Azure-SSIS
description: Cet article décrit comment activer l’authentification Azure Active Directory avec l’identité managée affectée par le système/l’utilisateur spécifiée pour Azure Data Factory afin de créer un Azure-SSIS Integration Runtime.
ms.service: data-factory
ms.subservice: integration-services
ms.devlang: powershell
ms.topic: conceptual
author: swinarko
ms.author: sawinark
ms.custom: seo-lt-2019
ms.date: 10/22/2021
ms.openlocfilehash: c800193e027821df00167930d06d9c13ad8cb67d
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131843595"
---
# <a name="enable-azure-active-directory-authentication-for-azure-ssis-integration-runtime"></a>Activer l’authentification Azure Active Directory pour le runtime d’intégration Azure-SSIS

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Cet article vous explique comment activer l’authentification Azure Active Directory (Azure AD) avec l’identité managée affectée par le système/l’utilisateur spécifiée pour votre Azure Data Factory (ADF) et l’utiliser à la place de méthodes d’authentification conventionnelles (telles que l’authentification SQL) pour :

- Créer un Azure-SSIS Integration Runtime (IR) qui, à son tour, approvisionnera la base de données du catalogue SSIS (SSISDB) dans le serveur/l’instance gérée Azure SQL Database en votre nom.

- Connectez-vous à différentes ressources Azure lors de l’exécution de packages SSIS sur Azure-SSIS IR.

Pour plus d’informations sur l’identité managée d’une fabrique ADF, consultez [Identité managée pour Data Factory](./data-factory-service-identity.md).

> [!NOTE]
> - Dans ce scénario, l’authentification Azure AD avec l’identité managée affectée par le système/l’utilisateur spécifiée pour votre ADF est utilisée uniquement pour l’approvisionnement et les opérations de démarrage suivantes de votre Azure-SSIS IR qui, à son tour, approvisionnera et se connectera à la base de données SSISDB. Pour les exécutions de package SSIS, votre Azure-SSIS IR se connectera toujours à la base de données SSISDB pour récupérer des packages à l’aide de l’authentification SQL avec des comptes entièrement managés (*AzureIntegrationServiceDbo* et *AzureIntegrationServiceWorker*), créés lors de l’approvisionnement de la base de données SSISDB.
>
> - Si vous avez déjà créé un Azure-SSIS IR à l’aide de l’authentification SQL, vous ne pouvez pas pour l’instant le reconfigurer pour utiliser l’authentification Azure AD avec PowerShell, mais vous pouvez le faire dans l’application ADF/le Portail Azure. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="enable-azure-ad-authentication-on-azure-sql-database"></a>Activez l’authentification Azure AD sur Azure SQL Database

Azure SQL Database prend en charge la création d’une base de données avec un utilisateur Azure AD. Tout d’abord, vous devez créer un groupe Azure AD avec l’identité managée affectée par le système/l’utilisateur spécifiée pour votre ADF en tant que membre. Ensuite, vous devez définir un utilisateur Azure AD comme administrateur Active Directory pour votre serveur Azure SQL Database, puis vous y connecter sur SQL Server Management Studio (SSMS) à l’aide de cet utilisateur. Pour finir, vous devez créer un utilisateur de base de données autonome représentant le groupe Azure AD, afin que l’identité managée affectée par le système/l’utilisateur spécifiée pour votre ADF soit utilisable par Azure-SSIS IR pour créer SSISDB à votre place.

### <a name="create-an-azure-ad-group-with-the-specified-systemuser-assigned-managed-identity-for-your-adf-as-a-member"></a>Créer un groupe Azure AD avec l’identité managée affectée par le système/l’utilisateur spécifiée pour votre ADF en tant que membre

Vous pouvez utiliser un groupe Azure AD existant ou en créer un à l’aide d’Azure AD PowerShell.

1. Installez le module [Azure AD PowerShell](/powershell/azure/active-directory/install-adv2).

2. Connectez-vous avec `Connect-AzureAD`, exécutez la cmdlet suivante pour créer un groupe, puis enregistrez-le dans une variable :

   ```powershell
   $Group = New-AzureADGroup -DisplayName "SSISIrGroup" `
                             -MailEnabled $false `
                             -SecurityEnabled $true `
                             -MailNickName "NotSet"
   ```

   Le résultat ressemble à l’exemple suivant, qui affiche également la valeur de la variable :

   ```powershell
   $Group

   ObjectId DisplayName Description
   -------- ----------- -----------
   6de75f3c-8b2f-4bf4-b9f8-78cc60a18050 SSISIrGroup
   ```

3. Ajoutez l’identité managée système/affectée par l’utilisateur spécifiée pour votre ADF au groupe. Vous pouvez suivre l’article [Identité managée pour Data Factory](./data-factory-service-identity.md) pour obtenir l’ID objet de l’identité managée affectée par le système/l’utilisateur spécifiée (p. ex., 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc, mais n’utilisez pas l’ID d’application de l’identité managée à cette fin).

   ```powershell
   Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
   ```

   Vous pouvez également vérifier l’appartenance au groupe par la suite.

   ```powershell
   Get-AzureAdGroupMember -ObjectId $Group.ObjectId
   ```

### <a name="configure-azure-ad-authentication-for-azure-sql-database"></a>Configurer l’authentification Azure AD pour Azure SQL Database

Vous pouvez [Configurer et gérer l’authentification Azure AD pour Azure SQL Database](../azure-sql/database/authentication-aad-configure.md) en suivant ces étapes :

1. Dans le portail Azure, dans le volet de navigation gauche, sélectionnez **Tous les services** -> **Serveurs SQL**.

2. Sélectionnez votre serveur Azure SQL Database à configurer avec l’authentification Azure AD.

3. Dans la section **Paramètres** du panneau, sélectionnez **Administrateur Active Directory**.

4. Dans la barre de commandes, sélectionnez **Définir l’administrateur**.

5. Sélectionnez un compte d’utilisateur Azure AD à désigner comme administrateur du serveur, puis choisissez **Sélectionner**.

6. Dans la barre de commandes, sélectionnez **Enregistrer**.

### <a name="create-a-contained-user-in-azure-sql-database-representing-the-azure-ad-group"></a>Créer un utilisateur de base de données autonome dans Azure SQL Database représentant le groupe Azure AD

Pour cette étape suivante, vous avez besoin de [SSMS](/sql/ssms/download-sql-server-management-studio-ssms).

1. Démarrer SSMS.

2. Dans la boîte de dialogue **Se connecter au serveur**, entrez le nom de votre serveur dans le champ **Nom du serveur**.

3. Dans le champ **Authentification**, sélectionnez **Active Directory - Authentification universelle avec prise en charge de MFA** (vous pouvez également utiliser les deux autres types d’authentification Active Directory ; consultez [Configurer et gérer l’authentification Azure AD pour Azure SQL Database](../azure-sql/database/authentication-aad-configure.md)).

4. Dans le champ **Nom d’utilisateur**, entrez le nom du compte Azure AD que vous avez défini comme administrateur du serveur, par exemple testuser@xxxonline.com.

5. Sélectionnez **Se connecter** et terminez le processus de connexion.

6. Dans l’**Explorateur d’objets**, développez le dossier **Bases de données** -> **Bases de données système**.

7. Cliquez avec le bouton droit sur la base de données **master** et sélectionnez **Nouvelle requête**.

8. Dans la fenêtre de requête, entrez la commande T-SQL suivante, puis sélectionnez **Exécuter** dans la barre d’outils.

   ```sql
   CREATE USER [SSISIrGroup] FROM EXTERNAL PROVIDER
   ```

   La commande doit s’exécuter correctement, en créant l’utilisateur de base de données autonome pour représenter le groupe.

9. Effacez la fenêtre de requête, entrez la commande T-SQL suivante, puis sélectionnez **Exécuter** dans la barre d’outils.

   ```sql
   ALTER ROLE dbmanager ADD MEMBER [SSISIrGroup]
   ```

   La commande doit s’exécuter correctement, et octroyer à l’utilisateur contenu la capacité des créer une base de données (SSISDB).

10. Si votre SSISDB a été créé à l’aide de l’authentification SQL et que vous souhaitez utiliser l’authentification Azure AD pour votre que votre runtime d’intégration Azure-SSIS y accède, commencez par vérifier que les étapes précédentes permettant d’accorder les autorisations à la base de données **master** ont été accomplies avec succès. Ensuite, cliquez avec le bouton droit sur la base de données **SSISDB** et sélectionnez **Nouvelle requête**.

    1. Dans la fenêtre de requête, entrez la commande T-SQL suivante, puis sélectionnez **Exécuter** dans la barre d’outils.

       ```sql
       CREATE USER [SSISIrGroup] FROM EXTERNAL PROVIDER
       ```

       La commande doit s’exécuter correctement, en créant l’utilisateur de base de données autonome pour représenter le groupe.

    2. Effacez la fenêtre de requête, entrez la commande T-SQL suivante, puis sélectionnez **Exécuter** dans la barre d’outils.

       ```sql
       ALTER ROLE db_owner ADD MEMBER [SSISIrGroup]
       ```

       La commande doit s’exécuter correctement et octroyer à l’utilisateur contenu la capacité d’accéder à la base de données SSISDB.

## <a name="enable-azure-ad-authentication-on-azure-sql-managed-instance"></a>Activez l’authentification Azure AD sur Azure SQL Managed Instance

Azure SQL Database Managed Instance prend en charge la création directe d’une base de données avec l’identité managée affectée par le système/l’utilisateur spécifiée pour votre ADF. Vous n’avez pas besoin de joindre l’identité managée affectée par le système/l’utilisateur spécifiée pour votre ADF à un groupe Azure AD ni de créer un utilisateur autonome représentant ce groupe dans Azure SQL Managed Instance.

### <a name="configure-azure-ad-authentication-for-azure-sql-managed-instance"></a>Configurer l’authentification Azure AD pour Azure SQL Managed Instance

Suivez les étapes décrites dans [Provisionner un administrateur Azure AD pour Azure SQL Managed Instance](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance).

### <a name="add-the-specified-systemuser-assigned-managed-identity-for-your-adf-as-a-user-in-azure-sql-managed-instance"></a>Ajoutez l’identité managée affectée par l’utilisateur/le système pour votre ADF en tant qu’utilisateur d’Azure SQL Managed Instance

Pour cette étape suivante, vous avez besoin de [SSMS](/sql/ssms/download-sql-server-management-studio-ssms).

1. Démarrer SSMS.

2. Connectez-vous à Azure SQL Managed Instance à l’aide d’un compte SQL Server **administrateur système**. Il s’agit d’une limitation temporaire qui sera levée quand la prise en charge des principaux de serveur (connexions) Azure AD d’Azure SQL Managed Instance sera en disponibilité générale. L’erreur suivante s’affiche si vous essayez d’utiliser un compte d’administrateur Azure AD pour créer la connexion : *Msg 15247, Niveau 16, État 1, Ligne 1 L’utilisateur n’est pas autorisé à effectuer cette action*.

3. Dans l’**Explorateur d’objets**, développez le dossier **Bases de données** -> **Bases de données système**.

4. Cliquez avec le bouton droit sur la base de données **master** et sélectionnez **Nouvelle requête**.

5. Dans la fenêtre de requête, exécutez le script T-SQL suivant pour ajouter l’identité managée affectée par le système/l’utilisateur spécifiée pour votre ADF comme utilisateur.

   ```sql
   CREATE LOGIN [{your managed identity name}] FROM EXTERNAL PROVIDER
   ALTER SERVER ROLE [dbcreator] ADD MEMBER [{your managed identity name}]
   ALTER SERVER ROLE [securityadmin] ADD MEMBER [{your managed identity name}]
   ```

   Si vous utilisez l’identité managée par le système pour votre ADF, votre nom d’*identité managée* doit être votre nom ADF. Si vous utilisez une identité managée affectée par l’utilisateur pour votre ADF, *votre nom d’identité managée* doit être le nom d’identité managée affecté à l’utilisateur spécifié.
    
   La commande doit s’exécuter correctement et octroyer à l’identité managée affectée par le système/l’utilisateur spécifiée pour votre ADF la capacité de créer une base de données.

6. Si votre SSISDB a été créé à l’aide de l’authentification SQL et que vous souhaitez utiliser l’authentification Azure AD pour votre que votre runtime d’intégration Azure-SSIS y accède, commencez par vérifier que les étapes précédentes permettant d’accorder les autorisations à la base de données **master** ont été accomplies avec succès. Ensuite, cliquez avec le bouton droit sur la base de données **SSISDB** et sélectionnez **Nouvelle requête**.

   1. Dans la fenêtre de requête, entrez la commande T-SQL suivante, puis sélectionnez **Exécuter** dans la barre d’outils.

      ```sql
      CREATE USER [{your managed identity name}] FOR LOGIN [{your managed identity name}] WITH DEFAULT_SCHEMA = dbo
      ALTER ROLE db_owner ADD MEMBER [{your managed identity name}]
      ```

      La commande doit s’exécuter correctement et octroyer à l’identité managée affectée par le système/l’utilisateur spécifiée pour votre ADF la capacité d’accéder à SSISDB.

## <a name="provision-azure-ssis-ir-in-azure-portaladf-app"></a>Provisionner Azure-SSIS IR dans le portail Azure/l’application ADF

Lorsque vous approvisionnez votre application Azure-SSIS IR dans le portail Azure/l’application ADF, sur la page **Paramètres de déploiement**, sélectionnez la case **Créer le catalogue SSIS (SSISDB) hébergé par un serveur Azure SQL Database/une instance managée pour stocker vos projets/packages/environnements/journaux d’exécution**  et sélectionnez **Utiliser l’authentification AAD avec l’identité managée affectée par le système pour Data Factory** ou **Utiliser l’authentification AAD avec l’identité managée affectée par l’utilisateur pour Data Factory** pour choisir la méthode d’authentification Azure AD pour Azure-SSIS IR pour accéder à votre serveur de base de données hébergeant SSISDB.

Pour plus d’informations, consultez [Créer un runtime Azure-SSIS IR dans ADF](./create-azure-ssis-integration-runtime.md).

## <a name="provision-azure-ssis-ir-with-powershell"></a>Provisionner Azure-SSIS IR avec PowerShell

Pour provisionner votre runtime d’intégration Azure-SSIS avec PowerShell, procédez comme suit :

1. Installez le module [Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/v5.5.0-March2018).

2. Dans votre script, ne définissez pas le paramètre `CatalogAdminCredential`. Par exemple :

   ```powershell
   Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                         -DataFactoryName $DataFactoryName `
                                         -Name $AzureSSISName `
                                         -Description $AzureSSISDescription `
                                         -Type Managed `
                                         -Location $AzureSSISLocation `
                                         -NodeSize $AzureSSISNodeSize `
                                         -NodeCount $AzureSSISNodeNumber `
                                         -Edition $AzureSSISEdition `
                                         -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                         -CatalogServerEndpoint $SSISDBServerEndpoint `
                                         -CatalogPricingTier $SSISDBPricingTier

   Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                           -DataFactoryName $DataFactoryName `
                                           -Name $AzureSSISName
   ```

## <a name="run-ssis-packages-using-azure-ad-authentication-with-the-specified-systemuser-assigned-managed-identity-for-your-adf"></a>Exécuter des packages SSIS à l’aide de l’authentification Azure AD avec l’identité managée affectée par l’utilisateur/le système spécifiée pour votre ADF

Lorsque vous exécutez des packages SSIS sur Azure-SSIS IR, vous pouvez utiliser l’authentification Azure AD avec l’identité managée affectée par l’utilisateur/le système spécifiée pour que votre ADF se connecte à différentes ressources Azure. Nous prenons actuellement en charge l’authentification Azure AD avec l’identité managée affectée par l’utilisateur/le système spécifiée pour votre ADF sur les gestionnaires de connexion suivants.

- [Gestionnaire de connexions OLEDB](/sql/integration-services/connection-manager/ole-db-connection-manager#managed-identities-for-azure-resources-authentication)

- [Gestionnaire de connexions ADO.NET](/sql/integration-services/connection-manager/ado-net-connection-manager#managed-identities-for-azure-resources-authentication)

- [Gestionnaire de connexions Stockage Azure](/sql/integration-services/connection-manager/azure-storage-connection-manager#managed-identities-for-azure-resources-authentication)
