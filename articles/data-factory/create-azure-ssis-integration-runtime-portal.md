---
title: Créer un runtime d’intégration Azure-SSIS via le portail Azure
description: Découvrez comment créer un runtime d'intégration Azure-SSIS dans Azure Data Factory via le portail Azure afin de pouvoir déployer et exécuter des packages SSIS dans Azure.
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 10/27/2021
author: swinarko
ms.author: sawinark
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: bcbb939f35e7bb4fa1a3508f7403f990edd6e282
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131038749"
---
# <a name="create-an-azure-ssis-integration-runtime-via-azure-portal"></a>Créer un runtime d’intégration Azure-SSIS via le portail Azure 

Cet article explique comment créer un runtime d’intégration (IR) Azure-SQL Server Integration Services (SSIS) dans Azure Data Factory (ADF) via le portail Azure/l’interface utilisateur ADF.

## <a name="create-a-data-factory"></a>Créer une fabrique de données

Pour créer votre fabrique de données à l’aide du portail Azure, suivez les instructions pas à pas dans [Créer une fabrique de données à l’aide de l’interface utilisateur](./quickstart-create-data-factory-portal.md#create-a-data-factory). Durant la procédure, sélectionnez **Épingler au tableau de bord** pour y accéder rapidement après sa création. 

Une fois la fabrique de données créée, ouvrez la page de vue d’ensemble dans le portail Azure. Sélectionnez la vignette **Créer et surveiller** pour ouvrir la page **Commençons** dans un onglet séparé. Vous pourrez continuer à créer votre IR Azure-SSIS à partir de cette page.   

## <a name="provision-an-azure-ssis-integration-runtime"></a>Approvisionner un runtime d’intégration Azure-SSIS

Dans la page d’accueil, sélectionnez la vignette **Configurer SSIS** pour ouvrir le volet **Configuration du runtime d’intégration**.

   :::image type="content" source="./media/doc-common-process/get-started-page.png" alt-text="Capture d’écran montrant la page d’accueil ADF.":::

   Le volet **Configuration du runtime d’intégration** comporte trois pages dans lesquelles vous pouvez configurer successivement les paramètres généraux, de déploiement et avancés.

### <a name="general-settings-page"></a>Page Paramètres généraux

Dans la page **Paramètres généraux** du volet **Configuration du runtime d’intégration**, effectuez les étapes suivantes.

:::image type="content" source="./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png" alt-text="Paramètres généraux":::

1. Dans **Nom**, saisissez le nom de votre runtime d’intégration.

2. Dans **Description**, saisissez la description de votre runtime d’intégration.

3. Pour **Emplacement**, sélectionnez l’emplacement de votre runtime d’intégration. Seuls les emplacements pris en charge sont affichés. Nous vous conseillons de sélectionner le même emplacement que votre serveur de base de données pour héberger SSISDB.

4. Pour **Taille du nœud**, sélectionnez la taille du nœud du cluster de votre runtime d’intégration. Seules les tailles de nœuds prises en charge sont affichées. Sélectionnez une taille de nœud importante (scale-up) si vous voulez exécuter de nombreux packages nécessitant beaucoup de calcul ou mémoire.

   > [!NOTE]
   > Si vous avez besoin d’[isolation de calcul](../azure-government/azure-secure-isolation-guidance.md#compute-isolation), sélectionnez la taille de nœud **Standard_E64i_v3**. Cette taille de nœud représente des machines virtuelles isolées qui consomment la totalité de leur hôte physique et fournissent le niveau d’isolation requis pour certaines charges de travail, telles que les charges de travail Impact Level 5 (IL5) du ministère de la défense des États-Unis.
   
5. Pour **Nombre de nœud**, sélectionnez le nombre de nœuds du cluster de votre runtime d’intégration. Seuls les nombres de nœuds pris en charge sont affichés. Sélectionnez un grand cluster avec de nombreux nœuds (scale-out) si vous voulez exécuter de nombreux packages en parallèle.

6. Pour **Édition/Licence**, sélectionnez l’édition SQL Server pour votre runtime d’intégration : Standard ou Entreprise. Sélectionnez Entreprise si vous voulez utiliser les fonctionnalités avancées de votre runtime d’intégration.

7. Pour **Économiser de l’argent**, sélectionnez l’option Azure Hybrid Benefit pour votre runtime d’intégration : **Oui** ou **Non**. Sélectionnez **Oui** si vous souhaitez utiliser votre propre licence SQL Server avec Software Assurance pour faire des économies grâce à une utilisation hybride.

8. Sélectionnez **Continuer**.

### <a name="deployment-settings-page"></a>Page Paramètres de déploiement

Dans la page **Paramètres de déploiement** du volet **Installation du runtime d’intégration**, vous disposez des options permettant de créer des magasins de packages Azure-SSIS IR ou SSISDB.

#### <a name="creating-ssisdb"></a>Création de SSISDB

Dans la page **Paramètres de déploiement** du volet **Installation du runtime d’intégration**, si vous souhaitez déployer vos packages dans SSISDB (Modèle de déploiement de projet), cochez la case **Créer un catalogue SSIS (SSISDB) hébergé par un serveur Azure SQL Database/Managed Instance pour stocker vos projets/packages/environnements/journaux d’exécution**. Autrement, si vous souhaitez déployer vos packages dans le système de fichiers, Azure Files ou une base de données SQL Server (MSDB) hébergée par Azure SQL Managed Instance (modèle de déploiement de package), il n’est pas nécessaire de créer de SSISDB ni de cocher la case.

Quel que soit votre modèle de déploiement, si vous souhaitez utiliser SQL Server Agent hébergé par Azure SQL Managed Instance afin d’orchestrer ou de planifier les exécutions de vos packages, cela est activé par SSISDB ; vous pouvez donc cocher quand même cette case. Pour plus d’informations, consultez [Planifier des exécutions de packages SSIS par le biais de l’agent Azure SQL Managed Instance](./how-to-invoke-ssis-package-managed-instance-agent.md).
   
Si vous cochez la case, effectuez les étapes suivantes pour apporter votre propre serveur de base de données afin d’héberger la base de données SSISDB que nous allons créer et gérer en votre nom.

:::image type="content" source="./media/tutorial-create-azure-ssis-runtime-portal/deployment-settings.png" alt-text="Paramètres de déploiement pour SSISDB":::
   
1. Pour **Abonnement**, sélectionnez l’abonnement Azure comprenant votre serveur de base de données pour héberger la base de données SSISDB. 

1. Pour **Emplacement**, sélectionnez l’emplacement de votre serveur de base de données pour héberger la base de données SSISDB. Nous vous recommandons de sélectionner le même emplacement que celui de runtime d’intégration.

1. Pour **Point de terminaison du serveur de base de données du catalogue**, sélectionnez le point de terminaison de votre serveur de base de données pour héberger SSISDB. 
   
   En fonction du serveur de base de données sélectionné, l’instance SSISDB peut être créée à pour vous en tant que base de données unique, dans le cadre d’un pool élastique ou dans une instance managée. Elle peut être accessible dans un réseau public ou en la joignant à un réseau virtuel. Pour obtenir des conseils sur le choix du type de serveur de base de données pour héberger SSISDB, consultez [Comparer SQL Database et SQL Managed Instance](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-sql-database-and-sql-managed-instance).   

   Si vous sélectionnez un serveur Azure SQL Database avec des règles de pare-feu IP/points de terminaison de service de réseau virtuel ou une instance managée avec un point de terminaison privé pour héberger le catalogue SSISDB, ou si vous avez besoin d’accéder à des données locales sans configurer un runtime d’intégration (IR) auto-hébergé, vous devez joindre votre Azure-SSIS IR à un réseau virtuel. Pour plus d’informations, consultez [Joindre un runtime d’intégration Azure-SSIS à un réseau virtuel](./join-azure-ssis-integration-runtime-virtual-network.md).
      
1. Cochez la case **Utiliser l’authentification AAD avec l’identité managée affectée par le système pour Data Factory** ou **Utiliser l’authentification AAD avec une identité managée affectée par l’utilisateur pour Data Factory** pour choisir la méthode d’authentification Azure AD pour Azure-SSIS IR afin d’accéder à votre serveur de base de données qui héberge SSISDB. Pour choisir la méthode d’authentification SQL à la place, ne cochez aucune des cases.

   Si vous cochez l’une des cases, vous devrez ajouter l’identité managée affectée par le système ou l’utilisateur spécifiée pour votre fabrique de données à un groupe Azure AD avec autorisations d’accès à votre serveur de base de données. Si vous cochez la case **Utiliser l’authentification AAD avec une identité managée affectée par l’utilisateur pour Data Factory**, vous pouvez alors sélectionner toutes les informations d’identification existantes créées à l’aide de vos identités managées affectées par l’utilisateur spécifiées ou en créer de nouvelles. Pour plus d’informations, consultez [Activer l’authentification Azure AD pour le runtime d’intégration (IR) Azure-SSIS](./enable-aad-authentication-azure-ssis-ir.md).

1. Pour **Nom d’utilisateur administrateur**, entrez le nom d’utilisateur d’authentification SQL de votre serveur de base de données qui héberge SSISDB. 

1. Pour **Mot de passe administrateur**, entrez le mot de passe d’authentification SQL de votre serveur de base de données qui héberge SSISDB. 

1. Cochez la case **Utiliser la paire Runtime d’intégration Azure-SSIS de secours double avec le basculement SSISDB** pour configurer une paire IR Azure SSIS de secours double qui fonctionne en synchronisation avec le groupe de basculement Azure SQL Database/Managed Instance pour la continuité d’activité et la reprise d’activité (BCDR).
   
   Si vous cochez cette case, entrez un nom pour identifier votre paire d’IR Azure-SSIS principal et secondaire dans la zone de texte **Nom de paire de secours double**. Vous devez entrer le même nom de paire lors de la création de vos IR Azure-SSIS principal et secondaire.

   Pour plus d’informations, consultez [Configurer votre IR Azure-SSIS pour BCDR](./configure-bcdr-azure-ssis-integration-runtime.md).

1. Pour **Niveau de service de base de données de catalogue**, sélectionnez le niveau de service de votre serveur de base de données pour héberger le catalogue SSISDB. Sélectionnez le niveau De base, Standard ou Premium, ou sélectionnez un nom de pool élastique.

Sélectionnez **Tester la connexion** le cas échéant et, si l’opération réussit, sélectionnez **Continuer**.

> [!NOTE]
> Si vous utilisez Azure SQL Database Server pour héberger SSISDB, vos données seront stockées dans le stockage géoredondant pour les sauvegardes par défaut. Si vous ne souhaitez pas répliquer vos données dans d’autres régions, suivez les instructions pour [configurer la redondance du stockage de sauvegarde à l’aide de PowerShell](../azure-sql/database/automated-backups-overview.md?tabs=single-database#configure-backup-storage-redundancy-by-using-powershell).
   
#### <a name="creating-azure-ssis-ir-package-stores"></a>Création de magasins de packages Azure-SSIS IR

Dans la page **Paramètres de déploiement** du volet **Installation du runtime d’intégration**, si vous souhaitez gérer vos packages qui sont déployés dans MSDB, le système de fichiers ou Azure Files (modèle de déploiement de package) avec des magasins de packages Azure-SSIS IR, cochez la case **Créer des magasins de packages pour gérer les packages qui sont déployés dans le système de fichiers/Azure Files/une base de données SQL Server (MSDB) hébergée par Azure SQL Managed Instance**.
   
Le magasin de packages Azure-SSIS IR vous permet d’importer/d’exporter/de supprimer/d’exécuter des packages et de superviser/d’arrêter l’exécution de packages par le biais de SSMS, à l’image du [magasin de packages SSIS hérités](/sql/integration-services/service/package-management-ssis-service). Pour plus d’informations, consultez [Gérer les packages SSIS avec des magasins de packages Azure-SSIS IR](./azure-ssis-integration-runtime-package-store.md).
   
Si vous cochez cette case, vous pouvez ajouter plusieurs magasins de packages à votre runtime d’intégration Azure-SSIS en sélectionnant **Nouveau**. À l’inverse, un magasin de packages peut être partagé par plusieurs runtimes d’intégration Azure-SSIS.

:::image type="content" source="./media/tutorial-create-azure-ssis-runtime-portal/deployment-settings2.png" alt-text="Paramètres de déploiement pour MSDB/le système de fichiers/Azure Files":::

Dans le volet **Ajouter un magasin de packages**, effectuez les étapes suivantes.
   
   1. Dans **Nom du magasin de packages**, entrez le nom de votre magasin de packages. 

   1. Dans **Service lié du magasin de packages**, sélectionnez votre service lié existant qui stocke les informations d’accès pour le système de fichiers/Azure Files/Azure SQL Managed Instance où vos packages sont déployés ou créez-en un en sélectionnant **Nouveau**. Dans le volet **Nouveau service lié**, effectuez les étapes suivantes.
   
      > [!NOTE]
      > Vous pouvez utiliser les services liés **Stockage Fichier Azure** ou **Système de fichiers** pour accéder à Azure Files. Si vous utilisez le service lié **Stockage Fichier Azure**, le magasin de packages Azure-SSIS IR prend en charge uniquement la méthode d’authentification **De base** (pas les méthodes **Clé de compte** et **URI SAS**).  

      :::image type="content" source="./media/tutorial-create-azure-ssis-runtime-portal/deployment-settings-linked-service.png" alt-text="Paramètres de déploiement pour les services liés":::

      1. Dans **Nom**, entrez le nom de votre service lié. 
         
      1. Dans **Description**, entrez la description de votre service lié. 
         
      1. Dans **Type**, sélectionnez **Stockage Fichier Azure**, **Azure SQL Managed Instance** ou **Système de fichiers**.

      1. Vous pouvez ignorer **Se connecter via le runtime d’intégration**, car nous utilisons toujours votre runtime d’intégration Azure-SSIS pour extraire les informations d’accès des magasins de packages.

      1. Si vous sélectionnez **Stockage Fichier Azure** comme **Méthode d’authentification**, sélectionnez **De base**, puis procédez comme suit. 

         1. Pour **Méthode de sélection de compte**, sélectionnez **À partir d’un abonnement Azure** ou **Entrer manuellement**.
         
         1. Si vous sélectionnez **À partir d’un abonnement Azure**, sélectionnez l’**abonnement Azure**, le **nom du compte de stockage** et le **partage de fichiers** appropriés.
            
         1. Si vous sélectionnez **Entrer manuellement**, entrez `\\<storage account name>.file.core.windows.net\<file share name>` pour **Hôte**, `Azure\<storage account name>` pour **Nom d’utilisateur** et `<storage account key>` pour **Mot de passe** ou sélectionnez le **coffre de clés Azure** où le mot de passe est stocké en tant que secret.

      1. Si vous sélectionnez **Azure SQL Managed Instance**, effectuez les étapes suivantes. 

         1. Sélectionnez **Chaîne de connexion** ou le **coffre de clés Azure Key Vault** où elle est stockée en tant que secret.

         1. Si vous sélectionnez **Chaîne de connexion**, effectuez les étapes suivantes. 
             1. Pour la **Méthode de sélection de compte**, si vous choisissez **À partir d’un abonnement Azure**, sélectionnez l’**abonnement Azure**, le **nom du serveur**, le **type de point de terminaison** et le **nom de base de données** appropriés. Si vous choisissez **Entrer manuellement**, effectuez les étapes suivantes. 
                1.  Pour **Nom de domaine complet**, entrez `<server name>.<dns prefix>.database.windows.net` ou `<server name>.public.<dns prefix>.database.windows.net,3342` comme point de terminaison privé ou public de votre instance gérée SQL Azure, respectivement. Si vous entrez le point de terminaison privé, **Tester la connexion** n’est pas applicable, car l’interface utilisateur d’ADF ne peut pas l’atteindre.

                1. Pour **Nom de la base de données**, entrez `msdb`.

            1. Pour **Type d’authentification**, sélectionnez **Authentification SQL**, **Identité managée**, **Principal de service** ou **Identité managée affectée par l’utilisateur**.

                - Si vous sélectionnez **Authentification SQL**, entrez les **nom d’utilisateur** et **mot de passe** appropriés ou sélectionnez le **coffre de clés Azure** où le mot de passe est stocké en tant que secret.

                -  Si vous sélectionnez **Identité managée**, accordez à l’identité managée affectée par le système de votre ADF l’accès à votre instance Azure SQL Managed Instance.

                - Si vous sélectionnez **Principal du service**, entrez les **ID principal de service** et la **clé du principal de service** appropriés ou sélectionnez le **coffre de clés Azure** où la clé est stockée en tant que secret.

                -  Si vous sélectionnez **Identité managée affectée par l’utilisateur**, accordez à l’identité managée affectée par l’utilisateur spécifiée pour votre ADF l’accès à votre instance Azure SQL Managed Instance. Vous pouvez ensuite sélectionner toutes les informations d’identification existantes créées à l’aide de vos identités managées affectées par l’utilisateur spécifiées ou en créer de nouvelles.

      1. Si vous sélectionnez **Système de fichiers**, entrez le chemin d’accès UNC du dossier dans lequel vos packages sont déployés pour **Hôte** ainsi que les **nom d’utilisateur** et **mot de passe** appropriés, ou sélectionnez le **coffre de clés Azure** où le mot de passe est stocké en tant que secret.

      1. Sélectionnez **Tester la connexion** le cas échéant et, si l’opération réussit, sélectionnez **Créer**.

   1. Vos magasins de packages ajoutés s’affichent dans la page **Paramètres de déploiement**. Pour les supprimer, activez leurs cases à cocher, puis sélectionnez **Supprimer**.

Sélectionnez **Tester la connexion** le cas échéant et, si l’opération réussit, sélectionnez **Continuer**.

### <a name="advanced-settings-page"></a>Page Paramètres avancés

Dans la page **Paramètres avancés** du volet **Configuration du runtime d’intégration**, effectuez les étapes suivantes.

   :::image type="content" source="./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png" alt-text="Paramètres avancés":::

   1. Pour **Exécutions parallèles maximales par nœud**, sélectionnez le nombre maximal de packages exécutables simultanément par nœud au sein de votre cluster de runtime d’intégration. Seuls les numéros des packages pris en charge sont affichés. Sélectionnez un petit nombre si vous souhaitez utiliser plusieurs cœurs pour exécuter un seul package volumineux nécessitant beaucoup de calcul ou de mémoire. Sélectionnez un nombre élevé si vous souhaitez exécuter un ou plusieurs petits packages dans un seul cœur.

   1. Cochez la case **Personnaliser votre Azure-SSIS Integration Runtime avec des installations de composants/configurations système supplémentaires** pour indiquer si vous souhaitez ajouter des configurations personnalisées standard/Express sur votre Azure-SSIS IR. Pour plus d’informations, consultez [Personnalisation de l’installation du runtime d’intégration Azure-SSIS](./how-to-configure-azure-ssis-ir-custom-setup.md).

      Si vous cochez la case, effectuez les étapes suivantes.

      :::image type="content" source="./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-custom.png" alt-text="Paramètres avancés avec des configurations personnalisées":::
   
      1. Dans **Custom setup container SAS URI** (URI SAP du conteneur de configurations personnalisées), entrez l’URI SAP de votre conteneur dans lequel vous stockez les scripts et les fichiers associés pour les configurations personnalisées standard.

      1. Pour **Installation personnalisée rapide**, sélectionnez **Nouveau** afin d’ouvrir le panneau **Ajouter une installation personnalisée rapide**, puis sélectionnez les types de votre choix dans le menu déroulant **Type d’installation personnalisée rapide**, p. ex. **Exécuter la commande cmdkey**, **Ajouter une variable d'environnement**, **Installer un composant sous licence**, etc.

         Si vous sélectionnez le type **Installer le composant sous licence**, vous pouvez sélectionner tous les composants intégrés de nos partenaires ISV dans le menu déroulant **Nom du composant** et, si nécessaire, entrez la clé de licence du produit ou chargez le fichier de licence du produit que vous avez acheté auprès d’eux dans la case **Clé de licence**/**Fichier de licence**.
  
         Vos nouvelles installations personnalisées rapides s’affichent sur la page **Paramètres avancés**. Pour les supprimer, vous pouvez activer leurs cases à cocher, puis sélectionner **Supprimer**.

   1. Cochez la case **Sélectionner un réseau virtuel à joindre à votre Azure-SSIS Integration Runtime, autoriser ADF à créer certaines ressources réseau et apporter éventuellement vos propres adresses IP publiques statiques** pour indiquer si vous souhaitez joindre votre Azure-SSIS IR à un réseau virtuel. 

      Cochez cette case si vous utilisez un serveur Azure SQL Database configuré avec un point de terminaison de service réseau virtuel/une règle de pare-feu IP/un point de terminaison privé ou Azure SQL Managed Instance qui joins un réseau virtuel pour héberger SSISDB, ou si vous avez besoin d’accéder à des données locales (si vous avez des sources/destinations de données locales dans vos packages SSIS) sans configurer d’IR auto-hébergé. Pour plus d’informations, consultez [Joindre le runtime d’intégration Azure-SSIS à un réseau virtuel](join-azure-ssis-integration-runtime-virtual-network.md). 

      Si vous cochez la case, effectuez les étapes suivantes.

      :::image type="content" source="./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png" alt-text="Paramètres avancés avec un réseau virtuel":::

      1. Dans **Abonnement**, sélectionnez l’abonnement Azure possédant votre réseau virtuel.

      1. Pour **Emplacement**, sélectionnez le même emplacement que celui de votre runtime d’intégration.

      1. Pour **Type**, sélectionnez le type de votre réseau virtuel : **réseau virtuel Azure Resource Manager**/réseau virtuel classique. Nous vous recommandons de sélectionner **Réseau virtuel Azure Resource Manager**, car le réseau virtuel classique sera bientôt déconseillé.

      1. Pour **Nom du réseau virtuel**, sélectionnez le nom de votre réseau virtuel. Il doit être le même que celui utilisé pour configurer un point de terminaison de service de réseau virtuel/un point de terminaison privé pour votre serveur Azure SQL Database qui héberge SSISDB. Ou il doit être le même que celui joint par votre Azure SQL Managed Instance qui héberge SSISDB. Il peut également s’agir de celui connecté à votre réseau local. Sinon, vous pouvez utiliser n’importe quel réseau virtuel pour apporter vos propres adresses IP publiques statiques pour Azure-SSIS IR.

      1. Pour **Nom du sous-réseau**, sélectionnez le nom du sous-réseau de votre réseau virtuel. Il doit être le même que celui utilisé pour configurer un point de terminaison de service de réseau virtuel pour votre serveur Azure SQL Database qui héberge SSISDB. Ou il doit s’agir d’un sous-réseau différent de celui joint par votre Azure SQL Managed Instance qui héberge SSISDB. Sinon, vous pouvez utiliser n’importe quel sous-réseau pour apporter vos propres adresses IP publiques statiques pour Azure-SSIS IR.

      1. Pour la **méthode d’injection de réseau virtuel**, sélectionnez la méthode de votre injection de réseau virtuel : **Express**/**Standard**. 
   
         Pour comparer ces méthodes, consultez l’article [Comparer les méthodes d’injection de réseau virtuel Standard et Express](azure-ssis-integration-runtime-virtual-network-configuration.md#compare). 
   
         Si vous sélectionnez **Express**, consultez l’article [Méthode d’injection de réseau virtuel Express](azure-ssis-integration-runtime-express-virtual-network-injection.md). 
      
         Si vous sélectionnez **Standard**, consultez l’article [Méthode d’injection de réseau virtuel Standard](azure-ssis-integration-runtime-standard-virtual-network-injection.md).  Avec cette méthode, vous pouvez également :

         1. Sélectionner la case à cocher **Apporter des adresses IP publiques statiques pour votre Azure-SSIS Integration Runtime** pour choisir d’apporter vos propres adresses IP publiques statiques pour Azure-SSIS IR, de manière à pouvoir les autoriser sur le pare-feu de vos banques de données.

            Si vous cochez la case, effectuez les étapes suivantes.

            1. Pour **Première adresse IP publique statique**, sélectionnez la première adresse IP publique statique qui [répond aux exigences](azure-ssis-integration-runtime-standard-virtual-network-injection.md#ip) de votre Azure-SSIS IR. Si vous n’en avez pas, cliquez sur le lien **Créer** pour créer des adresses IP publiques statiques sur le portail Azure, puis cliquez sur le bouton Actualiser du portail pour pouvoir les sélectionner.
      
            1. Pour **Deuxième adresse IP publique statique**, sélectionnez la deuxième adresse IP publique statique qui [répond aux exigences](azure-ssis-integration-runtime-standard-virtual-network-injection.md#ip) de votre Azure-SSIS IR. Si vous n’en avez pas, cliquez sur le lien **Créer** pour créer des adresses IP publiques statiques sur le portail Azure, puis cliquez sur le bouton Actualiser du portail pour pouvoir les sélectionner.

   1. Cochez la case **Configurer le runtime d’intégration auto-hébergé en tant que proxy pour votre Azure-SSIS Integration Runtime** pour indiquer si vous voulez configurer un runtime d’intégration auto-hébergé en tant que proxy pour votre Azure-SSIS IR. Pour plus d’informations, consultez [Configurer un runtime d’intégration auto-hébergé en tant que proxy](./self-hosted-integration-runtime-proxy-ssis.md). 

      Si vous cochez la case, effectuez les étapes suivantes.

      :::image type="content" source="./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-shir.png" alt-text="Paramètres avancés avec un IR auto-hébergé":::

      1. Sous **Runtime d’intégration auto-hébergé**, sélectionnez votre IR auto-hébergé existant en tant que proxy pour Azure-SSIS IR.

      1. Sous **Service lié de stockage de préproduction**, sélectionnez votre service lié de stockage Blob Azure existant ou créez-en un pour la préproduction.

      1. Sous **Chemin d’accès intermédiaire**, spécifiez un conteneur d’objets blob dans le compte de stockage Blob Azure sélectionné ou laissez ce champ pour utiliser un conteneur par défaut pour la préproduction.

   1. Sélectionnez **Validation de réseau virtuel**. Si la validation réussit, sélectionnez **Continuer**. 

Dans la page **Résumé**, passez en revue tous les paramètres pour votre runtime d'intégration Azure-SSIS, ajoutez les liens de documentation recommandés aux favoris, puis sélectionnez **Terminer** pour lancer la création de votre runtime d’intégration.

> [!NOTE]
> En dehors des réglages personnalisés, ce processus devrait se terminer dans les 5 minutes. Toutefois, l’IR Azure-SSIS peut prendre jusqu’à 20-30 minutes pour joindre un réseau virtuel avec une méthode d’injection standard.
>
> Si vous utilisez le catalogue SSISDB, le service Data Factory se connecte à votre serveur de base de données pour le préparer. Il configure également les autorisations et les paramètres de votre réseau virtuel, s’ils sont spécifiés, et joint votre runtime d’intégration Azure-SSIS au réseau virtuel.
>
> Quand vous provisionnez un runtime d’intégration Azure-SSIS, les composants Access Redistributable et Azure Feature Pack pour SSIS sont également installés. Ces composants assurent la connectivité aux fichiers Excel et Access ainsi qu’à diverses sources de données Azure, en plus des sources de données déjà prises en charge par les composants intégrés. Pour plus d’informations sur les composants intégrés/préinstallés, consultez [Composants intégrés/préinstallés sur Azure-SSIS IR](./built-in-preinstalled-components-ssis-integration-runtime.md). Pour plus d’informations sur les composants supplémentaires que vous pouvez installer, consultez [Personnalisation des configurations du runtime d’intégration Azure-SSIS](./how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="connections-pane"></a>Volet Connexions

Dans le volet **Connexions** du hub **Gestion**, basculez sur la page **Runtimes d’intégration** et sélectionnez **Actualiser**. 

   :::image type="content" source="./media/tutorial-create-azure-ssis-runtime-portal/connections-pane.png" alt-text="Volet Connexions":::

   Vous pouvez modifier/reconfigurer votre runtime d’intégration Azure-SSIS en sélectionnant son nom. Vous pouvez également sélectionner les boutons appropriés pour superviser/démarrer/arrêter/supprimer votre runtime d’intégration Azure-SSIS, générer automatiquement un pipeline ADF avec l’activité Exécuter le package SSIS à exécuter sur votre runtime d’intégration Azure-SSIS et afficher le code JSON/la charge utile de votre runtime d’intégration Azure-SSIS.  Vous ne pouvez modifier ou supprimer votre runtime d’intégration Azure-SSIS que lorsqu’il est arrêté.

## <a name="azure-ssis-integration-runtimes-in-the-portal"></a>Runtimes d’intégration Azure SSIS sur le portail

1. Dans l’interface utilisateur d’Azure Data Factory, basculez vers l’onglet **Gérer**, puis sur **Runtimes d’intégration** pour afficher le volet **Connexions** pour afficher les runtimes d’intégration existants dans votre Data Factory.

   :::image type="content" source="./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png" alt-text="Afficher les runtimes d’intégration existants":::

1. Sélectionnez **Nouveau** pour créer un runtime d’intégration Azure-SSIS, puis ouvrez le volet **Configuration du runtime d’intégration**.

   :::image type="content" source="./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png" alt-text="Runtime d’intégration via le menu":::

1. Dans le volet **Configuration du runtime d’intégration**, sélectionnez la vignette **Faire une migration lift-and-shift des packages SSIS existants à exécuter dans Azure**, puis sélectionnez **Continuer**.

   :::image type="content" source="./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png" alt-text="Spécifier le type de runtime d’intégration":::

1. Consultez la section [Provisionner un runtime d’intégration Azure-SSIS](#provision-an-azure-ssis-integration-runtime) pour connaître les autres étapes de configuration d’un runtime d’intégration Azure-SSIS.
 
## <a name="next-steps"></a>Étapes suivantes

- [Créer un Azure-SSIS IR via Azure PowerShell](create-azure-ssis-integration-runtime-powershell.md).
- [Créez un modèle Azure-SSIS IR via Azure Resource Manager](create-azure-ssis-integration-runtime-resource-manager-template.md).
- [Déployez et exécutez vos packages SSIS sur Azure-SSIS IR](create-azure-ssis-integration-runtime-deploy-packages.md).

Pour plus d’informations sur le runtime d’intégration Azure-SSIS IR, voir les articles suivants : 

- [Runtime d’intégration Azure SSIS IR](concepts-integration-runtime.md#azure-ssis-integration-runtime). Cet article fournit des informations conceptuelles générales sur les runtimes d’intégration, notamment sur Azure-SSIS IR. 
- [Monitor an Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime) (Surveiller le runtime d’intégration Azure-SSIS). Cet article vous montre comment récupérer et comprendre des informations sur votre runtime d’intégration Azure-SSIS.
- [Manage an Azure-SSIS IR](manage-azure-ssis-integration-runtime.md) (Gérer un runtime d’intégration Azure-SSIS). Cet article vous explique comment arrêter, démarrer ou supprimer votre Azure-SSIS IR. Il vous montre également comment effectuer un scale-out de votre IR Azure-SSIS en ajoutant des nœuds.
- [Déployer, exécuter et surveiller des packages SSIS dans Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Se connecter à SSISDB dans Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Se connecter aux banques de données locales avec l’authentification Windows](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 
- [Planifier l’exécution de packages SSIS déployés dans Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
