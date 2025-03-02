---
title: Personnaliser l’installation d’un Azure-SSIS Integration Runtime
description: Cet article décrit comment utiliser l’interface d’installation personnalisée d’un Azure-SSIS Integration Runtime pour installer des composants supplémentaires ou modifier des paramètres
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
author: swinarko
ms.author: sawinark
ms.custom: seo-lt-2019
ms.date: 11/01/2021
ms.openlocfilehash: 033d2d188f2e5ef9d2a72428e088ea302908e7c9
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131457921"
---
# <a name="customize-the-setup-for-an-azure-ssis-integration-runtime"></a>Personnaliser l’installation d’un Azure-SSIS Integration Runtime

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Vous pouvez personnaliser votre Azure-SQL Server Integration Services (SSIS) Integration Runtime (IR) dans Azure Data Factory (ADF) via des configurations personnalisées. Celles-ci vous permettent d’ajouter vos propres étapes lors de l’approvisionnement ou de la reconfiguration de votre Azure-SSIS IR. 

Des configurations personnalisées vous permettent de modifier la configuration ou l’environnement d’exploitation par défaut de votre Azure-SSIS IR. Par exemple, pour démarrer des services Windows supplémentaires, conserver des informations d'identification d'accès pour des partages de fichiers ou utiliser uniquement un chiffrement fort/protocole réseau plus sécurisé (TLS 1.2). Vous pouvez aussi installer des composants supplémentaires, tels que des assemblys, des pilotes ou des extensions, sur chaque nœud de votre Azure-SSIS IR. Il peut s’agir de composants personnalisés, open source ou tiers. Pour plus d’informations sur les composants intégrés/préinstallés, consultez [Composants intégrés/préinstallés sur Azure-SSIS IR](./built-in-preinstalled-components-ssis-integration-runtime.md).

Vous pouvez effectuer des installations personnalisées sur votre Azure-SSIS IR de l’une des deux manières suivantes : 
* **Installation personnalisée standard avec un script** : préparez un script et ses fichiers associés, puis chargez le tout dans un conteneur d'objets blob de votre compte Stockage Azure. Vous fournissez ensuite un URI (Uniform Resource Identifier) à signature d'accès partagé (SAP) pour votre conteneur d'objets blob lorsque vous installez ou reconfigurez votre instance d'Azure-SSIS IR. Chaque nœud de votre instance d'Azure-SSIS IR télécharge ensuite le script et ses fichiers associés à partir de votre conteneur d'objets blob, et exécute votre installation personnalisée avec des autorisations élevées. Lorsque votre installation personnalisée est terminée, chaque nœud charge la sortie standard de l'exécution et d'autres journaux d'activité dans votre conteneur d'objets blob.
* **Installation personnalisée rapide sans script** : Exécutez certaines configurations système et commandes Windows courantes ou installez des composants supplémentaires populaires ou recommandés sans utiliser le moindre script.

Vous pouvez installer des composants gratuits (sans licence) et payants (sous licence) avec des installations personnalisées rapides et standard. Si vous êtes un éditeur de logiciels indépendant (ISV), consultez [Développer des composants payants ou sous licence pour Azure-SSIS IR](how-to-develop-azure-ssis-ir-licensed-components.md).

> [!IMPORTANT]
> Afin de tirer parti des améliorations futures, nous vous recommandons d'utiliser la série de nœuds v3 ou version ultérieure pour votre runtime d'intégration Azure-SSIS IR, en l'association à une configuration personnalisée.

## <a name="current-limitations"></a>Limites actuelles

Les limitations suivantes s’appliquent uniquement aux installations personnalisées standard :

- Si vous souhaitez utiliser *gacutil.exe* dans votre script pour installer des assemblys dans Global Assembly Cache (GAC), vous devez fournir *gacutil.exe* dans le cadre de votre installation personnalisée. Vous pouvez aussi utiliser la copie fournie dans le dossier *Sample* de notre conteneur d'objets blob Préversion publique. Consultez la section **Exemples d’installation personnalisée standard** ci-dessous.

- Si vous souhaitez faire référence à un sous-dossier dans votre script, *msiexec.exe* ne prend pas en charge la notation `.\` pour faire référence au dossier racine. Utilisez une commande telle que `msiexec /i "MySubfolder\MyInstallerx64.msi" ...` au lieu de `msiexec /i ".\MySubfolder\MyInstallerx64.msi" ...`.

- Les partages administratifs, ou des partages réseau masqués qui sont créés automatiquement par Windows, ne sont actuellement pas pris en charge sur l’Azure-SSIS IR.

- Le pilote ODBC IBM iSeries Access n’est pas pris en charge sur Azure-SSIS IR. Il se peut que des erreurs d’installation s’affichent lors de votre installation personnalisée. Si c’est le cas, contactez le support IBM pour obtenir de l’aide.

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Pour personnaliser votre Azure-SSIS IR, vous avez besoin des éléments suivants :

- [Un abonnement Azure](https://azure.microsoft.com/)

- [Approvisionner votre IR Azure-SSIS](./tutorial-deploy-ssis-packages-azure.md)

- [Un compte Stockage Azure](https://azure.microsoft.com/services/storage/). Non requis pour les installations personnalisées rapides. Pour des installations personnalisées standard, vous chargez et stockez votre script d’installation personnalisée et ses fichiers associés dans un conteneur d’objets blob. Le processus d’installation personnalisée télécharge également ses journaux d’activité d’exécution sur le même conteneur d’objets blob.

## <a name="instructions"></a>Instructions

Vous pouvez approvisionner ou reconfigurer votre IR Azure-SSIS avec des installations personnalisées sur l’interface utilisateur ADF. Si vous souhaitez faire de même avec PowerShell, téléchargez et installez [Azure PowerShell](/powershell/azure/install-az-ps).

### <a name="standard-custom-setup"></a>Installation personnalisée standard

Pour approvisionner ou reconfigurer votre Azure-SSIS Integration Runtime à l’aide d’installations personnalisées standard sur l’interface utilisateur d’Azure Data Factory, procédez comme suit.

1. Préparer votre script d’installation personnalisée et ses fichiers associés (par exemple, les fichiers .bat, .cmd, .exe, .dll, .msi ou .ps1).

   * Vous devez avoir un fichier de script nommé *main.cmd*, qui est le point d’entrée de votre installation personnalisée.  
   * Pour vous assurer que le script peut être exécuté en mode silencieux, vous devez le tester sur votre ordinateur local.  
   * Si vous souhaitez que des journaux supplémentaires générés par d'autres outils (par exemple, *msiexec.exe*) soient chargés dans votre conteneur d'objets blob, spécifiez la variable d'environnement prédéfinie `CUSTOM_SETUP_SCRIPT_LOG_DIR` comme dossier de journal d'activité dans vos scripts (par exemple, *msiexec /i xxx.msi /quiet /lv %CUSTOM_SETUP_SCRIPT_LOG_DIR%\install.log*).

1. Téléchargez, installez et ouvrez [Explorateur Stockage Azure](https://storageexplorer.com/).

   a. Sous **Local et attaché**, cliquez avec le bouton droit sur **Comptes de stockage**, puis sélectionnez **Se connecter au service Stockage Azure**.

      :::image type="content" source="media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image1.png" alt-text="Se connecter à Stockage Azure":::

   b. Sélectionnez **Compte de stockage ou service**, **Nom et clé du compte**, puis **Suivant**.

   c. Entrez le nom et la clé de votre compte de stockage Azure, sélectionnez **Suivant**, puis **Se connecter**.

      :::image type="content" source="media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image3.png" alt-text="Fournir le nom et la clé du compte de stockage":::

   d. Dans votre compte Stockage Azure connecté, cliquez avec le bouton droit sur **Conteneurs d'objets blob**, sélectionnez **Créer un conteneur d'objets blob** et nommez le nouveau conteneur d'objets blob.

      Créer un :::image type="content" source="media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image4.png" alt-text="conteneur d’objets blob":::

   e. Sélectionnez le nouveau conteneur d'objets blob et chargez votre script d'installation personnalisée et ses fichiers associés. Veillez à charger *main.cmd* au niveau supérieur de votre conteneur d'objets blob, et non dans un dossier. Votre conteneur d'objets blob ne doit comprendre que les fichiers d'installation personnalisée nécessaires, afin que leur téléchargement ultérieur sur votre instance d'Azure-SSIS IR ne prenne pas beaucoup de temps. La durée maximale d'une installation personnalisée est actuellement fixée à 45 minutes avant qu'elle n'expire. Cela comprend le temps nécessaire pour télécharger tous les fichiers de votre conteneur d'objets blob et les installer sur l'instance d'Azure-SSIS IR. Si l’installation nécessite plus de temps, créez un ticket de support.

      :::image type="content" source="media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image5.png" alt-text="Chargez des fichiers vers le conteneur d’objets blob":::

   f. Cliquez avec le bouton droit sur le conteneur d'objets blob, puis sélectionnez **Obtenir une signature d'accès partagé**.

      :::image type="content" source="media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image6.png" alt-text="Obtenez la signature d'accès partagé pour le conteneur d'objets blob":::

   g. Créez l'URI de SAP pour votre conteneur d'objets blob avec un délai d'expiration suffisamment long et une autorisation de lecture, d'écriture ou de liste. Vous devez utiliser l’URI SAP pour télécharger et exécuter votre script d’installation personnalisée et ses fichiers associés. Cela se produit chaque fois qu’un nœud de votre Azure-SSIS IR est réinitialisé. Vous avez également besoin d’une autorisation d’écriture pour charger les journaux d’activité d’exécution de l’installation.

      > [!IMPORTANT]
      > Vérifiez que l’URI de SAP n’arrive pas à expiration et que les ressources d’installation personnalisée restent disponibles pendant la totalité du cycle de vie de votre Azure-SSIS IR, de la création à la suppression, en particulier si vous l’arrêtez et le redémarrez régulièrement au cours de cette période.

      :::image type="content" source="media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image7.png" alt-text="Générez la signature d'accès partagé pour le conteneur d'objets blob":::

   h. Copiez et enregistrez l'URI de SAP de votre conteneur d'objets blob.

      :::image type="content" source="media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image8.png" alt-text="Copiez et enregistrez la signature d’accès partagé.":::

1. Activez la case à cocher **Personnaliser votre Azure-SSIS Integration Runtime avec des installations de composants/configurations système supplémentaires** sur la page **Paramètres avancés** du volet **Configuration du runtime d’intégration**. Ensuite, entrez l'URI SAP de votre conteneur d'objets blob dans la zone de texte **URI SAP du conteneur d'installation personnalisée**.

   :::image type="content" source="./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-custom.png" alt-text="Paramètres avancés avec des configurations personnalisées":::

Une fois votre installation personnalisée standard terminée et votre instance d'Azure-SSIS Integration Runtime démarrée, vous trouverez tous les journaux d'activité d'installation personnalisée dans le dossier *main.cmd.log* de votre conteneur d'objets blob. Ils incluent la sortie standard de *main. cmd* et d’autres journaux d’activité d’exécution.

### <a name="express-custom-setup"></a>Installation personnalisée rapide

Pour approvisionner ou reconfigurer votre Azure-SSIS IR à l’aide d’installations personnalisées rapides sur l’interface utilisateur d’Azure Data Factory, procédez comme suit.

1. Activez la case à cocher **Personnaliser votre Azure-SSIS Integration Runtime avec des installations de composants/configurations système supplémentaires** sur la page **Paramètres avancés** du volet **Configuration du runtime d’intégration**. 

1. Sélectionnez **Nouveau** pour ouvrir le volet **Ajouter une installation personnalisée rapide**, puis sélectionnez un type de votre choix dans la liste déroulante **Type d’installation personnalisée rapide**. Nous proposons actuellement des configurations personnalisées rapides pour exécuter la commande cmdkey, ajouter des variables d’environnement, installer Azure PowerShell et installer des composants sous licence.

#### <a name="running-cmdkey-command"></a>Exécution de la commande cmdkey

Si vous sélectionnez le type **Exécuter la commande cmdkey** pour votre installation personnalisée rapide, vous pouvez exécuter la commande cmdkey Windows sur votre Azure-SSIS IR. Pour ce faire, entrez le nom ou nom de domaine, le nom d’utilisateur ou nom de compte, ainsi que le mot de passe ou la clé de compte de l’ordinateur ciblé respectivement dans les zones de texte **/Add**, **/User** et **/Pass**. Cela vous permettra de conserver les informations d’identification d’accès pour les serveurs SQL Server, les partages de fichiers ou Azure Files sur votre Azure-SSIS IR. Par exemple, pour accéder à Azure Files, vous pouvez entrer `YourAzureStorageAccountName.file.core.windows.net`, `azure\YourAzureStorageAccountName` et `YourAzureStorageAccountKey` respectivement pour **/Add**, **/User** et **/Pass**. Cela est similaire à l’exécution de la commande Windows [cmdkey](/windows-server/administration/windows-commands/cmdkey) sur votre ordinateur local. 

#### <a name="adding-environment-variables"></a>Ajout de variables d’environnement

Si vous sélectionnez le type **Ajouter une variable d’environnement** pour votre installation personnalisée rapide, vous pouvez ajouter une variable d’environnement Windows à votre Azure-SSIS IR. Pour ce faire, entrez le nom et la valeur de votre variable d’environnement respectivement dans les zones de texte **Nom de la variable** et **Valeur de la variable**. Cela vous permet d’utiliser la variable d’environnement dans vos packages s’exécutant sur Azure-SSIS IR, par exemple, dans Script Components/Tasks. Cela est similaire à l’exécution de la commande Windows [set](/windows-server/administration/windows-commands/set_1) sur votre ordinateur local.

#### <a name="installing-azure-powershell"></a>Installation d’Azure PowerShell

Si vous sélectionnez le type **Installer Azure PowerShell** pour votre installation personnalisée rapide, vous pouvez installer le module Az de PowerShell sur votre Azure-SSIS IR. Pour ce faire, entrez le numéro de version du module Az (x.y.z) de votre choix dans une [liste des versions prises en charge](https://www.powershellgallery.com/packages/az). Cela vous permet des cmdlets/scripts Azure PowerShell dans vos packages pour gérer les ressources Azure, par exemple [Azure Analysis Services (AAS)](../analysis-services/analysis-services-powershell.md).

#### <a name="installing-licensed-components"></a>Installation de composants sous licence

Si vous sélectionnez le type **Installer un composant sous licence** pour votre installation personnalisée rapide, vous pouvez sélectionner un composant intégré de nos Partenaires éditeurs de logiciels indépendants dans la liste déroulante **Nom du composant** :

* Si vous sélectionnez le composant **Task Factory de SentryOne**, vous pouvez installer la suite de composants [Task Factory](https://www.sentryone.com/products/task-factory/high-performance-ssis-components) de SentryOne sur votre Azure-SSIS IR en entrant la clé de licence du produit que vous leur avez achetée dans la zone **Clé de licence**. La version intégrée actuelle est **2020.21.2**.

* Si vous sélectionnez le composant **HEDDA.IO d’oh22**, vous pouvez installer le composant de qualité/nettoyage des données [HEDDA. IO](https://github.com/oh22is/HEDDA.IO/tree/master/SSIS-IR) d’oh22 sur votre Azure-SSIS IR. Pour ce faire, vous devez acheter leur service au préalable. La version intégrée actuelle est **1.0.14**.

* Si vous sélectionnez le composant **SQLPhonetics.NET d’oh22**, vous pouvez installer le composant de qualité/mise en correspondance des données [SQLPhonetics.NET](https://appsource.microsoft.com/product/web-apps/oh22.sqlphonetics-ssis) d’oh22 sur votre Azure-SSIS IR. Pour ce faire, entrez la clé de licence du produit que vous leur avez achetée au préalable dans la zone de texte **Clé de licence**. La version intégrée actuelle est **1.0.45**.
   
* Si vous sélectionnez le composant **SSIS Integration Toolkit de KingswaySoft**, vous pouvez installer la suite de connecteurs de [SSIS Integration Toolkit](https://www.kingswaysoft.com/products/ssis-integration-toolkit-for-microsoft-dynamics-365) pour les applications CRM/ERP/marketing/collaboration, telles que Microsoft Dynamics, SharePoint, Project Server, Oracle ou Salesforce Marketing Cloud, à partir de KingswaySoft sur votre Azure-SSIS IR en entrant la clé de licence du produit que vous avez achetée dans la zone **Clé de licence** La version intégrée actuelle est **20.2**.

* Si vous sélectionnez le composant **SSIS Productivity Pack de KingswaySoft**, vous pouvez installer la suite de composants [SSIS Productivity Pack](https://www.kingswaysoft.com/products/ssis-productivity-pack) à partir de KingswaySoft sur votre Azure-SSIS IR en entrant la clé de licence du produit que vous leur avez achetée dans la zone **Clé de licence**. La version intégrée actuelle est **20.2**.

* Si vous sélectionnez le composant **Xtract IS de Theobald Software**, vous pouvez installer la suite de connecteurs [Xtract IS](https://theobald-software.com/en/xtract-is/) pour SAP System (ERP, S/4HANA, BW) de Theobald Software sur votre Azure-SSIS IR en glissant-déposant/chargeant le fichier de licence produit que vous leur avez acheté dans la zone **Fichier de licence**. La version intégrée actuelle est **6.5.13.18**.

* Si vous sélectionnez le composant **Service d’intégration d’AecorSoft**, vous pouvez installer la suite de connecteurs [Service d’intégration](https://www.aecorsoft.com/en/products/integrationservice) d’AecorSoft pour les systèmes SAP et Salesforce sur votre Azure-SSIS IR. Pour ce faire, entrez la clé de licence du produit que vous leur avez achetée au préalable dans la zone de texte **Clé de licence**. La version intégrée actuelle est **3.0.00**.

* Si vous sélectionnez le composant **SSIS Standard Package de CData**, vous pouvez installer la suite [SSIS Standard Package](https://www.cdata.com/kb/entries/ssis-adf-packages.rst#standard) comprenant les composants les plus populaires de CData (par exemple, les connecteurs Microsoft SharePoint) sur votre runtime Azure-SSIS IR. Pour ce faire, entrez la clé de licence du produit que vous leur avez achetée au préalable dans la zone de texte **Clé de licence**. La version intégrée actuelle est **19.7354**.

* Si vous sélectionnez le composant **SSIS Extended Package de CData**, vous pouvez installer la suite [SSIS Extended Package](https://www.cdata.com/kb/entries/ssis-adf-packages.rst#extended) comprenant tous les composants de CData (par exemple, les connecteurs Microsoft Dynamics 365 Business Central et d’autres composants de **SSIS Standard Package**) sur votre runtime Azure-SSIS IR. Pour ce faire, entrez la clé de licence du produit que vous leur avez achetée au préalable dans la zone de texte **Clé de licence**. La version intégrée actuelle est **19.7354**. En raison de sa grande taille, veillez à ce que votre runtime Azure-SSIS IR possède au moins quatre cœurs de CPU par nœud pour éviter une expiration de la commande d’installation.

Vos nouvelles installations personnalisées rapides s’affichent sur la page **Paramètres avancés**. Pour les supprimer, activez leurs cases à cocher, puis sélectionnez **Supprimer**.

### <a name="azure-powershell"></a>Azure PowerShell

Pour approvisionner ou reconfigurer votre IR Azure-SSIS avec des installations personnalisées en utilisant Azure PowerShell, procédez comme suit.

1. Si votre IR Azure-SSIS est déjà démarré ou en cours d’exécution, arrêtez-le d’abord.

1. Vous pouvez ensuite ajouter ou supprimer des installations personnalisées en exécutant la cmdlet `Set-AzDataFactoryV2IntegrationRuntime` avant de démarrer votre IR Azure-SSIS.

   ```powershell
   $ResourceGroupName = "[your Azure resource group name]"
   $DataFactoryName = "[your data factory name]"
   $AzureSSISName = "[your Azure-SSIS IR name]"
   # Custom setup info: Standard/express custom setups
   $SetupScriptContainerSasUri = "" # OPTIONAL to provide a SAS URI of blob container for standard custom setup where your script and its associated files are stored
   $ExpressCustomSetup = "[RunCmdkey|SetEnvironmentVariable|InstallAzurePowerShell|SentryOne.TaskFactory|oh22is.SQLPhonetics.NET|oh22is.HEDDA.IO|KingswaySoft.IntegrationToolkit|KingswaySoft.ProductivityPack|Theobald.XtractIS|AecorSoft.IntegrationService|CData.Standard|CData.Extended or leave it empty]" # OPTIONAL to configure an express custom setup without script

   # Add custom setup parameters if you use standard/express custom setups
   if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
   {
       Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
           -DataFactoryName $DataFactoryName `
           -Name $AzureSSISName `
           -SetupScriptContainerSasUri $SetupScriptContainerSasUri
   }
   if(![string]::IsNullOrEmpty($ExpressCustomSetup))
   {
       if($ExpressCustomSetup -eq "RunCmdkey")
       {
           $addCmdkeyArgument = "YourFileShareServerName or YourAzureStorageAccountName.file.core.windows.net"
           $userCmdkeyArgument = "YourDomainName\YourUsername or azure\YourAzureStorageAccountName"
           $passCmdkeyArgument = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourPassword or YourAccessKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.CmdkeySetup($addCmdkeyArgument, $userCmdkeyArgument, $passCmdkeyArgument)
       }
       if($ExpressCustomSetup -eq "SetEnvironmentVariable")
       {
           $variableName = "YourVariableName"
           $variableValue = "YourVariableValue"
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.EnvironmentVariableSetup($variableName, $variableValue)
       }
       if($ExpressCustomSetup -eq "InstallAzurePowerShell")
       {
           $moduleVersion = "YourAzModuleVersion"
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.AzPowerShellSetup($moduleVersion)
       }
       if($ExpressCustomSetup -eq "SentryOne.TaskFactory")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "oh22is.SQLPhonetics.NET")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "oh22is.HEDDA.IO")
       {
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup)
       }
       if($ExpressCustomSetup -eq "KingswaySoft.IntegrationToolkit")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "KingswaySoft.ProductivityPack")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }    
       if($ExpressCustomSetup -eq "Theobald.XtractIS")
       {
           $jsonData = Get-Content -Raw -Path YourLicenseFile.json
           $jsonData = $jsonData -replace '\s',''
           $jsonData = $jsonData.replace('"','\"')
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString($jsonData)
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "AecorSoft.IntegrationService")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "CData.Standard")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "CData.Extended")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }    
       # Create an array of one or more express custom setups
       $setups = New-Object System.Collections.ArrayList
       $setups.Add($setup)

       Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
           -DataFactoryName $DataFactoryName `
           -Name $AzureSSISName `
           -ExpressCustomSetup $setups
   }
   Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
       -DataFactoryName $DataFactoryName `
       -Name $AzureSSISName `
       -Force
   ```

### <a name="standard-custom-setup-samples"></a>Exemples d’installation personnalisée standard

Pour afficher et réutiliser certains exemples d’installation personnalisée standard, procédez comme suit.

1. Connectez-vous à notre conteneur d'objets blob Préversion publique à l'aide de l'Explorateur Stockage Azure.

   a. Sous **Local et attaché**, cliquez avec le bouton droit sur **Comptes de stockage**, puis sélectionnez **Se connecter au service Stockage Azure**.

      :::image type="content" source="media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image1.png" alt-text="Se connecter à Stockage Azure":::

   b. Sélectionnez **Conteneur d'objets blob**, **URL de signature d'accès partagé (SAP)** , puis **Suivant**.

   c. Dans la zone de texte **URL de SAP du conteneur d'objets blob**, entrez l'URI de SAP de notre conteneur d'objets blob Préversion publique ci-dessous, puis sélectionnez **Suivant** et **Se connecter**.

      `https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2020-03-25T04:00:00Z&se=2025-03-25T04:00:00Z&sv=2019-02-02&sr=c&sig=WAD3DATezJjhBCO3ezrQ7TUZ8syEUxZZtGIhhP6Pt4I%3D`

   d. Dans le volet gauche, sélectionnez le conteneur d'objets blob **publicpreview** connecté, puis double-cliquez sur le dossier *CustomSetupScript*. Ce dossier contient les éléments suivants :

      * Un dossier *Sample*, qui contient une installation personnalisée pour une tâche de base sur chaque nœud de votre Azure-SSIS IR. La tâche est juste mise en veille pendant quelques secondes. Le dossier contient également un dossier *gacutil*, dont le contenu entier (*gacutil.exe*, *gacutil.exe.config* et *1033\gacutlrc.dll*) peut être copié tel quel dans votre conteneur d'objets blob.

      * Un dossier *UserScenarios* contenant plusieurs exemples d’installations personnalisées pour des scénarios d’utilisateurs réels. Si vous souhaitez installer plusieurs exemples sur votre instance d'Azure-SSIS IR, vous pouvez combiner leurs fichiers de script d'installation personnalisée (*main.cmd*) en un seul et charger ce dernier avec tous les fichiers associés dans votre conteneur d'objets blob.

        :::image type="content" source="media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image11.png" alt-text="Contenu du conteneur d'objets blob Préversion publique":::

   e. Double-cliquez sur le dossier *UserScenarios* pour rechercher les éléments suivants :

      * Un dossier *.NET FRAMEWORK 3.5* contenant un script d’installation personnalisée (*main.cmd*) pour installer une version antérieure de .NET Framework sur chaque nœud de votre Azure-SSIS IR. Cette version peut être requise par certains composants personnalisés.

      * Un dossier *BCP* contenant un script d’installation personnalisée (*main.cmd*) pour installer les utilitaires de ligne de commande SQL Server (*MsSqlCmdLnUtils.msi*) sur chaque nœud de votre Azure-SSIS IR. L’un de ces utilitaires est le programme de copie en bloc (*bcp*).

      * Un dossier *DNS SUFFIX* contenant un script d’installation personnalisée (*main.cmd*) pour ajouter votre propre suffixe DNS (par exemple *test.com*) à un nom de domaine à étiquette unique non qualifié et le convertir en un nom de domaine complet (FQDN) avant de l’utiliser dans les requêtes DNS de votre Azure-SSIS IR.

      * Un dossier *EXCEL* contenant un script d’installation personnalisée (*main.cmd*) pour installer des assemblys et bibliothèques C# sur chaque nœud de votre Azure-SSIS IR. Vous pouvez les utiliser dans les tâches de script pour lire et écrire des fichiers Excel de façon dynamique. 
      
        Tout d’abord, téléchargez les fichiers [*ExcelDataReader.dll*](https://www.nuget.org/packages/ExcelDataReader/) et [*DocumentFormat.OpenXml.dll*](https://www.nuget.org/packages/DocumentFormat.OpenXml/), puis chargez-les tous les deux avec *main.cmd* dans votre conteneur d'objets blob. Autrement, si vous souhaitez simplement utiliser les connecteurs Excel standard (Gestionnaire de connexions, Source et Destination), le composant redistribuable d’accès qui les contient est préinstallé sur votre Azure-SSIS IR, et vous n’avez donc pas besoin d’une installation personnalisée.
      
      * Un dossier *MYSQL ODBC* contenant un script d’installation personnalisée (*main.cmd*) pour installer les pilotes ODBC MySQL sur chaque nœud de votre Azure-SSIS IR. Cette installation vous permet d’utiliser les connecteurs ODBC (Gestionnaire de connexions, Source et Destination) pour la connexion au serveur MySQL. 
     
        Tout d’abord, [téléchargez les dernières versions 64 bits et 32 bits des programmes d'installation de pilotes ODBC MySQL](https://dev.mysql.com/downloads/connector/odbc/) (par exemple, *mysql-connector-odbc-8.0.13-winx64.msi* et *mysql-connector-odbc-8.0.13-win32.msi*), puis chargez-les tous avec *main.cmd* dans votre conteneur d'objets blob.
        
        Si le nom de la source de données (DSN) est utilisé dans la connexion, la configuration DSN est nécessaire dans le script de configuration. Par exemple : C:\Windows\SysWOW64\odbcconf.exe /A {CONFIGSYSDSN "MySQL ODBC 8.0 Unicode Driver" "DSN=\<dsnname\>|PORT=3306|SERVER=\<servername\>"}

      * Un dossier *ORACLE ENTERPRISE*, qui contient un script d’installation personnalisée (*main.cmd*) et le fichier config de l’installation sans assistance (*client.rsp*) pour installer les connecteurs Oracle et le pilote OCI sur chaque nœud de votre instance Azure-SSIS IR Édition Entreprise. Cette installation vous permet d’utiliser le Gestionnaire des connexions, la Source et la Destination Oracle pour la connexion au serveur Oracle. 
      
        Commencez par télécharger Microsoft Connectors v5.0 pour Oracle (*AttunitySSISOraAdaptersSetup.msi* et *AttunitySSISOraAdaptersSetup64.msi*) à partir du [Centre de téléchargement Microsoft](https://www.microsoft.com/en-us/download/details.aspx?id=55179), et le client Oracle le plus récent (par exemple, *winx64_12102_client.zip*) à partir du site [Oracle](https://www.oracle.com/database/technologies/oracle19c-windows-downloads.html). Ensuite, chargez le tout avec *main.cmd* et *client.rsp* dans votre conteneur d'objets blob. Si vous utilisez TNS pour la connexion à Oracle, vous devez également télécharger le fichier *tnsnames.ora*, le modifier et le charger dans votre conteneur d'objets blob. Cela permet de le copier dans le dossier d’installation d’Oracle lors de l’installation.

      * Un dossier *ORACLE STANDARD ADO.NET*, qui contient un script d’installation personnalisée (*main.cmd*) pour installer le pilote Oracle ODP.NET sur chaque nœud de votre Azure-SSIS IR. Cette installation vous permet d’utiliser le Gestionnaire des connexions, la Source et la Destination ADO.NET pour la connexion au serveur Oracle. 
      
        Tout d'abord, [téléchargez le dernier pilote Oracle ODP.NET](https://www.oracle.com/technetwork/database/windows/downloads/index-090165.html), par exemple *ODP.NET_Managed_ODAC122cR1.zip*, puis chargez-le dans votre conteneur d'objets blob avec *main.cmd*.
       
      * Un dossier *ORACLE STANDARD OLEDB* contenant un script d’installation personnalisée (*main.cmd*) pour installer le pilote Oracle ODBC sur chaque nœud de votre Azure-SSIS IR. Le script configure également le nom de la source de données (DSN). Cette installation vous permet d’utiliser le Gestionnaire des connexions, la Source et la Destination ODBC ou le Gestionnaire des connexions et la Source Power Query avec le type de source de données ODBC pour la connexion au serveur Oracle. 
      
        Tout d'abord, téléchargez la dernière version d'Oracle Instant Client (package de base ou package de base Lite) et le package ODBC, puis chargez-les tous avec *main.cmd* dans votre conteneur d'objets blob :
        * [Télécharger les packages 64 bits](https://www.oracle.com/technetwork/topics/winx64soft-089540.html) (package de base : *instantclient-basic-windows.x64-18.3.0.0.0dbru.zip* ; package de base Lite : *instantclient-basiclite-windows.x64-18.3.0.0.0dbru.zip* ; package ODBC : *instantclient-odbc-windows.x64-18.3.0.0.0dbru.zip*) 
        * [Télécharger les packages 32 bits](https://www.oracle.com/technetwork/topics/winsoft-085727.html) (package de base : *instantclient-basic-nt-18.3.0.0.0dbru.zip* ; package de base Lite : *instantclient-basiclite-nt-18.3.0.0.0dbru.zip* ; package ODBC : *instantclient-odbc-nt-18.3.0.0.0dbru.zip*)

      * Un dossier *ORACLE STANDARD OLEDB*, qui contient un script d’installation personnalisée (*main.cmd*) pour installer le pilote Oracle OLEDB sur chaque nœud de votre Azure-SSIS IR. Cette installation vous permet d’utiliser le Gestionnaire des connexions, la Source et la Destination OLEDB pour la connexion au serveur Oracle. 
     
        Tout d'abord, [téléchargez le dernier pilote Oracle OLEDB](https://www.oracle.com/partners/campaign/index-090165.html), par exemple *ODAC122010Xcopy_x64.zip*, puis chargez-le dans votre conteneur d'objets blob avec *main.cmd*.

      * Un dossier *POSTGRESQL ODBC* contenant un script d’installation personnalisée (*main.cmd*) pour installer les pilotes ODBC PostgreSQL sur chaque nœud de votre Azure-SSIS IR. Cette installation vous permet d’utiliser le Gestionnaire de connexions, la Source et la Destination ODBC pour la connexion au serveur PostgreSQL. 
     
        Tout d'abord, [téléchargez les dernières versions 64 bits et 32 bits des programmes d'installation de pilotes ODBC PostgreSQL](https://www.postgresql.org/ftp/odbc/versions/msi/) (par exemple, *psqlodbc_x64.msi* et *psqlodbc_x86.msi*), puis chargez-les avec *main.cmd* dans votre conteneur d'objets blob.

      * Un dossier *SAP BW* contenant un script d’installation personnalisée (*main.cmd*) pour installer l’assembly de connecteurs SAP .NET (*librfc32.dll*) sur chaque nœud de votre Azure-SSIS IR Édition Entreprise. Cette installation vous permet d’utiliser le Gestionnaire de connexions, la Source et la Destination SAP BW pour la connexion au serveur SAP BW. 
      
        Commencez par charger la version 64 bits ou 32 bits de *librfc32.dll* dans votre conteneur d'objets blob à partir du dossier d'installation SAP avec *main.cmd*. Le script copie ensuite l’assembly SAP dans le dossier *%windir%\SysWow64* ou *%windir%\System32* lors de l’installation.

      * Un dossier *STORAGE* contenant un script d’installation personnalisée (*main.cmd*) pour installer Azure PowerShell sur chaque nœud de votre Azure-SSIS IR. Cette installation vous permet de déployer et d’exécuter des packages SSIS qui exécutent les [cmdlets/scripts PowerShell pour gérer votre Stockage Azure](../storage/blobs/storage-quickstart-blobs-powershell.md). 
      
        Copiez *main.cmd*, un exemple d'*AzurePowerShell.msi* (ou utilisez la version la plus récente) et *storage.ps1* dans votre conteneur d'objets blob. Utilisez *PowerShell.dtsx* comme modèle pour vos packages. Le modèle de package combine une [Tâche de téléchargement de Blob Azure](/sql/integration-services/control-flow/azure-blob-download-task), qui télécharge un script PowerShell modifiable (*storage.ps1*), et une [Tâche d’exécution de processus](https://blogs.msdn.microsoft.com/ssis/2017/01/26/run-powershell-scripts-in-ssis/) qui exécute le script sur chaque nœud.

      * Un dossier *TERADATA*, qui contient un script d’installation personnalisée (*main.cmd*), son fichier associé (*install.cmd*) et les packages du programme d’installation ( *.msi*). Ces fichiers installent les connecteurs Teradata, l’API Teradata Parallel Transporter (TPT) et le pilote ODBC sur chaque nœud de votre Azure-SSIS IR Édition Entreprise. Cette installation vous permet d’utiliser le Gestionnaire des connexions, la Source et la Destination Teradata pour la connexion au serveur Teradata. 
      
        Tout d'abord, [téléchargez le fichier zip Teradata Tools and Utilities 15.x](http://partnerintelligence.teradata.com) (par exemple, *TeradataToolsAndUtilitiesBase__windows_indep.15.10.22.00.zip*), puis chargez-le avec les fichiers *.cmd* et *.msi* précédemment mentionnés dans votre conteneur d'objets blob.

      * Un dossier *TLS 1.2* contenant un script d'installation personnalisée (*main. cmd*) pour utiliser uniquement un chiffrement fort/protocole réseau plus sécurisé (TLS 1.2) sur chaque nœud de votre instance d'Azure-SSIS IR. Le script désactive également les versions antérieures de SSL/TLS (SSL 3.0, TLS 1.0, TLS 1.1) en même temps.

      * Un dossier *ZULU OPENJDK* contenant un script d’installation personnalisée (*main.cmd*) et un fichier PowerShell (*install_openjdk.ps1*) pour installer Zulu OpenJDK sur chaque nœud de votre Azure-SSIS IR. Cette installation vous permet d’utiliser des connecteurs Azure Data Lake Store et Flexible File pour traiter des fichiers ORC et Parquet. Pour plus d’informations, consultez [Feature Pack Azure pour Integration Services](/sql/integration-services/azure-feature-pack-for-integration-services-ssis#dependency-on-java). 
      
        Tout d'abord, [téléchargez la dernière version de Zulu OpenJDK](https://www.azul.com/downloads/zulu/zulu-windows/) (par exemple, *zulu8.33.0.1-jdk8.0.192-win_x64.zip*), puis téléchargez-la avec *main.cmd* et *install_openjdk.ps1* dans votre conteneur d'objets blob.

        :::image type="content" source="media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image12.png" alt-text="Dossiers dans le dossier de scénarios utilisateur":::

   f. Pour réutiliser ces exemples d'installation personnalisée standard, copiez le contenu du dossier sélectionné et collez-le dans votre conteneur d'objets blob.

1. Lorsque vous approvisionnez ou reconfigurez votre IR Azure-SSIS sur l’interface utilisateur ADF, activez la case à cocher **Personnaliser votre IR Azure-SSIS avec des installations de composants/configurations système supplémentaires** sur la page **Paramètres avancés** du volet **Configuration du runtime d’intégration**. Ensuite, entrez l'URI SAP de votre conteneur d'objets blob dans la zone de texte **URI SAP du conteneur d'installation personnalisée**.
   
1. Lorsque vous approvisionnez ou reconfigurez votre instance d'Azure-SSIS IR à l'aide de PowerShell, arrêtez-la si elle est déjà démarrée ou en cours d'exécution, exécutez la cmdlet `Set-AzDataFactoryV2IntegrationRuntime` avec l'URI de SAP de votre conteneur d'objets blob comme valeur du paramètre `SetupScriptContainerSasUri`, puis démarrez votre instance d'Azure-SSIS IR.

1. Une fois votre installation personnalisée standard terminée et votre instance d'Azure-SSIS Integration Runtime démarrée, vous trouverez tous les journaux d'activité d'installation personnalisée dans le dossier *main.cmd.log* de votre conteneur d'objets blob. Ils incluent la sortie standard de *main. cmd* et d’autres journaux d’activité d’exécution.

## <a name="next-steps"></a>Étapes suivantes

- [Configurer Enterprise Edition pour Azure-SSIS IR](how-to-configure-azure-ssis-ir-enterprise-edition.md)
- [Développer des composants payants ou sous licence pour Azure-SSIS IR](how-to-develop-azure-ssis-ir-licensed-components.md)
