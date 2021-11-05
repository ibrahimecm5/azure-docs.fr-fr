---
title: Inscrire et analyser une base de données Azure SQL
description: Cet article décrit le processus d’inscription d’une base de données Azure SQL dans Azure Purview, et donne des instructions pour s’authentifier et interagir avec la source de base de données Azure SQL
author: athenads
ms.author: athenadsouza
ms.service: purview
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: c2ed5a0a65bd4d10620b0c72dbb5efb2aac4152f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131061522"
---
# <a name="connect-to-azure-sql-database-in-azure-purview"></a>Se connecter à Azure SQL Database dans Azure Purview

Cet article décrit le processus d’inscription d’une source de données Azure SQL dans Azure Purview, et donne des instructions pour s’authentifier et interagir avec la source de base de données Azure SQL

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

|**Extraction des métadonnées**|  **Analyse complète**  |**Analyse incrémentielle**|**Analyse délimitée**|**Classification**|**Stratégie d'accès**|**Traçabilité**|
|---|---|---|---|---|---|---|
| [Oui](#register) | [Oui](#scan)|[Oui](#scan) | [Oui](#scan)|[Oui](#scan)| Non |[Traçabilité des données Data Factory](how-to-link-azure-data-factory.md)|

### <a name="known-limitations"></a>Limitations connues

* Azure Purview prend en charge 300 colonnes au maximum sous l’onglet Schéma. Au-delà, il affiche « Additional-Columns-Truncated » (Colonnes-Supplémentaires-Tronquées).

## <a name="prerequisites"></a>Prérequis

* Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Une [ressource Purview](create-catalog-portal.md) active.

* Vous devez être un administrateur de source de données et un lecteur de données pour inscrire une source et la gérer dans Purview Studio. Pour plus d’informations, consultez notre [page d’autorisations Azure Purview](catalog-permissions.md).

## <a name="register"></a>Inscrire

Cette section vous permet d’inscrire la source de données Azure SQL Database et de configurer un mécanisme d’authentification approprié pour garantir la réussite de l’analyse de la source de données.

### <a name="steps-to-register"></a>Procédure d’inscription

Il est important d’inscrire la source de données dans Azure Purview avant de configurer une analyse de la source de données.

1. Allez sur le [portail Azure](https://portal.azure.com) et accédez à la page des **comptes Purview** et sélectionnez votre _compte Azure Purview_

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-purview-acct.png" alt-text="Capture d’écran montrant le compte Purview utilisé pour enregistrer la source de données":::

1. **Ouvrez Purview Studio** et accédez à **Data Map**

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-open-purview-studio.png" alt-text="Capture d’écran qui navigue jusqu’au lien sources dans le mappage de données":::

1. Créez la [hiérarchie de collection](./quickstart-create-collection.md) à l’aide du menu **Collections** et attribuez des autorisations à des sous-collections individuelles, selon les besoins

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-collections.png" alt-text="Capture d’écran montrant le menu de collection pour attribuer des autorisations de contrôle d’accès à la hiérarchie de collection":::

1. Accédez à la collection appropriée sous le menu **Sources** et sélectionnez l’icône **Inscrire** pour inscrire une nouvelle base de données Azure SQL

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-data-source.png" alt-text="Capture d’écran montrant la collection utilisée pour enregistrer la source de données":::

1. Sélectionnez la source de données **Azure SQL Database**, puis **Continuer**

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-select-ds.png" alt-text="Capture d’écran qui permet de sélectionner la source de données":::

1. Fournissez un **Nom** approprié pour la source de données, sélectionnez l’**Abonnement Azure** approprié, le **Nom de serveur** du serveur SQL et la **collection**, puis sélectionnez **Appliquer**

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-ds-details.png" alt-text="Capture d’écran montrant les détails à entrer pour inscrire la source de données":::

1. La base de données Azure SQL Server s’affiche sous la collection sélectionnée

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-ds-collections.png" alt-text="Capture d’écran montrant la source de données mappée à la collection pour lancer l’analyse":::

## <a name="scan"></a>Analyser

### <a name="authentication-for-a-scan"></a>Authentification pour une analyse

Pour pouvoir analyser la source de données, vous devez configurer une méthode d’authentification dans la base de données Azure SQL.
Les options suivantes sont prises en charge :

* **Authentification SQL**

* **Identité managée** : dès que le compte Azure Purview est créé, une **identité managée** système est créée automatiquement dans le locataire Azure AD. Selon le type de ressource, des attributions de rôles RBAC spécifiques sont nécessaires pour permettre au MSI Azure Purview d’effectuer les analyses.
Si vous utilisez une identité managée, votre compte Purview a sa propre identité managée, qui est le nom de votre compte Purview quand vous l’avez créé

* **Principal de service** : dans cette méthode, vous pouvez créer un principal de service ou en utiliser un existant dans votre locataire Azure Active Directory.

##### <a name="configure-azure-ad-authentication-in-the-database-account"></a>Configurer l’authentification Azure AD dans le compte de base de données

Le principal de service ou l’identité managée doivent avoir l’autorisation d’obtenir des métadonnées pour la base de données, les schémas et les tables. Ils doivent également être en mesure d’interroger les tables à échantillonner pour la classification.

- [Configurer et gérer l’authentification Azure AD avec Azure SQL](../azure-sql/database/authentication-aad-configure.md)
- Vous devez créer un utilisateur Azure AD dans Azure SQL Database avec l’identité managée exacte de Purview ou votre propre principal de service en suivant le tutoriel [Créer l’utilisateur de principal de service dans Azure SQL Database](../azure-sql/database/authentication-aad-service-principal-tutorial.md#create-the-service-principal-user-in-azure-sql-database). Vous devez affecter l’autorisation appropriée (par exemple `db_datareader`) à l’identité. Exemple de syntaxe SQL pour créer l’utilisateur et accorder l’autorisation :

    ```sql
    CREATE USER [Username] FROM EXTERNAL PROVIDER
    GO
    
    EXEC sp_addrolemember 'db_datareader', [Username]
    GO
    ```

    > [!Note]
    > `Username` est soit votre propre principal de service, soit l’identité managée de Purview. En savoir plus sur les [rôles de base de données fixes et leurs fonctionnalités](/sql/relational-databases/security/authentication-access/database-level-roles#fixed-database-roles).

#### <a name="using-sql-authentication-for-scanning"></a>Utilisation de l’authentification SQL pour l’analyse

> [!Note]
> Seule la connexion principale au niveau du serveur (créée par le processus de configuration) ou les membres du rôle de base de données `loginmanager` dans la base de données master peuvent créer des connexions. Cela nécessite environ **15 minutes** une fois l’autorisation accordée. Le compte Purview doit disposer des autorisations appropriées pour pouvoir analyser la ou les ressources.

Vous pouvez suivre les instructions fournies dans [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-current&preserve-view=true#examples-1) pour créer une connexion pour Azure SQL Database si vous n’en avez pas. Vous aurez besoin d’un **nom d’utilisateur** et d’un **mot de passe** pour les étapes suivantes.

1. Accédez à votre coffre de clés dans le portail Azure.

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-key-vault.png" alt-text="Capture d’écran montrant le coffre de clés":::

1. Sélectionnez **Paramètres > Secrets**, puis **+ Générer/Importer**

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-secret.png" alt-text="Capture d’écran montrant l’option de coffre de clés pour générer une clé secrète":::

1. Entrez le **Nom** et la **Valeur** du *mot de passe* de votre base de données Azure SQL

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-secret-sql.png" alt-text="Capture d’écran montrant l’option de coffre de clés pour entrer les valeurs de secret sql":::

1. Sélectionnez **Créer** pour terminer.

1. Si votre coffre de clés n’est pas encore connecté à Purview, vous devrez [créer une connexion de coffre de clés](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account).

1. Enfin, [créez des informations d’identification](manage-credentials.md#create-a-new-credential) à l’aide de la clé pour configurer votre analyse.

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-credentials.png" alt-text="Capture d’écran montrant l’option de coffre de clés pour configurer les informations d’identification":::

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-key-vault-options.png" alt-text="Capture d’écran montrant l’option de coffre de clés pour créer un secret":::

#### <a name="using-managed-identity-for-scanning"></a>Utilisation d’une identité managée pour l’analyse

Vous devez donner à votre compte Purview l’autorisation d’analyser la base de données Azure SQL. Vous pouvez ajouter le MSI de Data Catalog au niveau de l’abonnement, du groupe de ressources ou de la ressource, en fonction des autorisations d’analyse que vous souhaitez lui accorder.

> [!Note] 
> Vous devez être propriétaire de l’abonnement pour pouvoir ajouter une identité managée sur une ressource Azure.

1. Dans le [portail Azure](https://portal.azure.com), recherchez l’abonnement, le groupe de ressources ou la ressource (par exemple, une base de données Azure SQL) que le catalogue doit être autorisé à analyser.

1. Sélectionnez **Contrôle d’accès (IAM)** dans le menu de navigation de gauche, puis sélectionnez **+ Ajouter** --> **Ajouter une attribution de rôle**

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-sql-ds.png" alt-text="Capture d’écran montrant la base de données Azure SQL":::

1. Définissez le **Rôle** sur **Lecteur** et entrez le _Nom du compte Azure Purview_ sous la zone d’entrée **Sélectionner**. Ensuite, sélectionnez **Enregistrer** pour fournir cette attribution de rôle à votre compte Purview.

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-access-managed-identity.png" alt-text="Capture d’écran montrant les détails pour attribuer des autorisations au compte Purview":::

#### <a name="using-service-principal-for-scanning"></a>Utilisation du principal de service pour l’analyse

##### <a name="creating-a-new-service-principal"></a>Création d’un principal de service

Si vous avez besoin de [Créer un principal de service](./create-service-principal-azure.md), vous devez inscrire une application dans votre locataire Azure AD et fournir l’accès au principal de service dans vos sources de données. Votre administrateur général Azure AD ou d’autres rôles, comme l’administrateur d’application, peuvent effectuer cette opération.

##### <a name="getting-the-service-principals-application-id"></a>Obtention de l’ID d’application du principal de service

1. Copiez l’**ID d’application (client)** présent dans la **Vue d’ensemble** du [_Principal de service_](./create-service-principal-azure.md) déjà créé

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-sp-appln-id.png" alt-text="Capture d’écran montrant l’ID d’application (client) pour le principal de service":::

##### <a name="granting-the-service-principal-access-to-your-azure-sql-database"></a>Octroi au principal de service de l’accès à votre base de données Azure SQL

1. Accédez à votre coffre de clés dans le portail Azure.

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-key-vault.png" alt-text="Capture d’écran montrant le coffre de clés afin d’ajouter un secret pour le principal de service":::

1. Sélectionnez **Paramètres > Secrets**, puis **+ Générer/Importer**

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-secret.png" alt-text="Capture d’écran montrant l’option de coffre de clés afin de générer un secret pour le principal de service":::

1. Entrez le **Nom** de votre choix et la **Valeur** du **Secret client** de votre principal de service

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-create-secret.png" alt-text="Capture d’écran montrant l’option de coffre de clés pour entrer les valeurs de secret":::

1. Sélectionnez **Créer** pour terminer.

1. Si votre coffre de clés n’est pas encore connecté à Purview, vous devrez [créer une connexion de coffre de clés](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account).

1. Enfin, [créez des informations d’identification](manage-credentials.md#create-a-new-credential) à l’aide de la clé pour configurer votre analyse.

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-credentials.png" alt-text="Capture d’écran montrant l’option de coffre de clés permettant d’ajouter des informations d’identification pour le principal de service":::

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-sp-cred.png" alt-text="Capture d’écran montrant l’option de coffre de clés permettant de créer un secret pour le principal de service":::

### <a name="firewall-settings"></a>Paramètres du pare-feu

Si un pare-feu est activé sur votre serveur de base de données, vous devez mettre à jour le pare-feu pour autoriser l’accès de l’une des deux manières suivantes :

1. Autorisez les connexions Azure via le pare-feu.
1. Installez un runtime d'intégration auto-hébergé et donnez-lui accès par le biais du pare-feu.

#### <a name="allow-azure-connections"></a>Autoriser les connexions Azure

L’activation des connexions Azure permettra à Azure Purview d’atteindre et de connecter le serveur sans mettre à jour le pare-feu lui-même. Vous pouvez suivre le guide pratique pour des [connexions à partir d’Azure](../azure-sql/database/firewall-configure.md#connections-from-inside-azure).

1. Accédez à votre compte de base de données.
1. Dans la page **Vue d’ensemble**, sélectionnez le nom du serveur.
1. Sélectionnez **Sécurité > Pare-feux et réseaux virtuels**.
1. Sélectionnez **Oui** pour **Autoriser les services et ressources Azure à accéder à ce serveur**
:::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-firewall.png" alt-text="Autoriser les services et ressources Azure à accéder à ce serveur." border="true":::

#### <a name="self-hosted-integration-runtime"></a>Runtime d’intégration auto-hébergé

Un runtime d’intégration auto-hébergé (SHIR) peut être installé sur un ordinateur pour se connecter à une ressource d’un réseau privé.

1. [Créez et installez un runtime d’intégration auto-hébergé](./manage-integration-runtimes.md) sur un ordinateur personnel ou sur un ordinateur situé dans le même réseau virtuel que votre serveur de base de données.
1. Vérifiez le pare-feu de votre serveur de base de données pour vérifier que l’ordinateur SHIR a accès par le biais du pare-feu. Ajoutez l’adresse IP de la machine si elle ne dispose pas déjà d’un accès.
1. Si votre SQL Server Azure se trouve derrière un point de terminaison privé ou dans un réseau virtuel, vous pouvez utiliser un [point de terminaison privé](catalog-private-link-ingestion.md#deploy-self-hosted-integration-runtime-ir-and-scan-your-data-sources) d’ingestion pour garantir l’isolement réseau de bout en bout.

### <a name="creating-the-scan"></a>Création de l’analyse

1. Ouvrez votre **compte Purview** et sélectionnez **Ouvrir Purview Studio**
1. Accéder aux **sources** -->  **de mappage de données** pour afficher la hiérarchie de collection
1. Sélectionnez l’icône **Nouvelle analyse** sous la **Base de données Azure SQL** inscrite précédemment

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-new-scan.png" alt-text="Capture d’écran montrant l’écran de création d’une nouvelle analyse":::

#### <a name="if-using-sql-authentication"></a>Si vous utilisez l’authentification SQL

1. Donnez un **Nom** à l’analyse, sélectionnez comme **Méthode de sélection de la base de données** _Entrer manuellement_, entrez le **Nom de la base de données** et les **Informations d’identification** créées précédemment, choisissez la collection appropriée pour l’analyse et sélectionnez **Tester la connexion** pour valider la connexion. Une fois la connexion effectuée, sélectionnez **Continuer**

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-sql-auth.png" alt-text="Capture d’écran montrant l’option d’authentification SQL pour l’analyse":::

#### <a name="if-using-managed-identity"></a>Si vous utilisez l’identité managée

1. Donnez un **Nom** à l’analyse, sélectionnez **MSI Purview** sous **Informations d’identification**, choisissez la collection appropriée pour l’analyse

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-managed-id.png" alt-text="Capture d’écran montrant l’option Identité managée pour exécuter l’analyse":::

1. Sélectionnez **Tester la connexion**. Sur une connexion réussie, sélectionnez **Continuer**

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-test.png" alt-text="Capture d’écran montrant l’option Identité managée pour exécuter l’analyse":::

#### <a name="if-using-service-principal"></a>Si vous utilisez le principal de service

1. Donnez un **Nom** à l’analyse, choisissez la collection appropriée pour l’analyse et sélectionnez la liste déroulante **Informations d’identification** pour sélectionner les informations d’identification créées précédemment

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-sp.png" alt-text="Capture d’écran montrant l’option permettant au principal de service d’activer l’analyse":::

1. Sélectionnez **Tester la connexion**. Sur une connexion réussie, sélectionnez **Continuer**

### <a name="scoping-and-running-the-scan"></a>Étendue et exécution de l’analyse

1. Vous pouvez limiter votre analyse à des dossiers ou sous-dossiers spécifiques en choisissant les éléments appropriés dans la liste.

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-scope-scan.png" alt-text="Définir la portée de votre analyse":::

1. Sélectionnez ensuite un ensemble de règles pour l’analyse. Vous pouvez choisir entre l’ensemble système par défaut, les ensembles de règles personnalisés existants ou créer un ensemble de règles inline.

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-scan-rule-set.png" alt-text="Ensemble de règles d’analyse":::

1. Si vous créez un _ensemble de règles d’analyse_

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-new-scan-rule-set.png" alt-text="Nouvel ensemble de règles d’analyse":::

1. Vous pouvez sélectionner les **règles de classification** à inclure dans la règle d’analyse

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-classification.png" alt-text="Règles de classification de l’ensemble de règles d’analyse":::

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-sel-scan-rule.png" alt-text="Sélection de l’ensemble de règles d’analyse":::

1. Choisissez votre déclencheur d’analyse. Vous pouvez configurer une planification ou exécuter l’analyse une seule fois.

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-scan-trigger.png" alt-text="déclencheur d’analyse":::

1. Passez en revue votre analyse et sélectionnez **Enregistrer et exécuter**.

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-review-scan.png" alt-text="Vérifier l’analyse":::

### <a name="view-scan"></a>Voir l’analyse

1. Accédez à la _source de données_ dans la _Collection_ et sélectionnez **Afficher les détails** pour vérifier l’état de l’analyse

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-view-scan.png" alt-text="Afficher l’analyse":::

1. Les détails de l’analyse indiquent la progression de l’analyse dans l’**état de la dernière exécution** et le nombre de ressources _analysées_ et _classées_

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-view-scan-details.png" alt-text="Afficher les détails de l’analyse":::

1. L’**État de la dernière exécution** est mis à jour sur **En cours**, puis **Terminé** une fois l’analyse complète exécutée avec succès

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-scan-complete.png" alt-text="afficher l’analyse terminée":::

### <a name="manage-scan"></a>Gérer l’analyse

Les analyses peuvent être gérées ou réexécutées après leur exécution

1. Sélectionner le **Nom de l’analyse** pour gérer l’analyse

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-manage scan.png" alt-text="Gérer l’analyse":::

1. Vous pouvez _réexécuter l'analyse_, _modifier l’analyse_, _Supprimer l’analyse_  

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-manage-scan-options.png" alt-text="gérer les options d’analyse":::

1. Vous pouvez de nouveau _exécuter une analyse incrémentielle_ ou une _analyse complète_

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-full-inc.png" alt-text="analyse complète ou incrémentielle":::

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez inscrit votre source, suivez les guides ci-dessous pour en savoir plus sur Purview et sur vos données.

- [Insights de données dans Azure Purview](concept-insights.md)
- [Lignage dans Azure Purview](catalog-lineage-user-guide.md)
- [Rechercher dans un catalogue de données](how-to-search-catalog.md)
