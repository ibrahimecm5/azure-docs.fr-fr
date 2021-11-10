---
title: Guide pratique pour planifier le runtime d’intégration Azure-SSIS
description: Cet article explique comment planifier le démarrage et l’arrêt d’Azure-SSIS Integration Runtime avec Azure Data Factory.
ms.service: data-factory
ms.subservice: integration-services
ms.devlang: powershell
ms.topic: conceptual
ms.date: 10/22/2021
author: swinarko
ms.author: sawinark
ms.openlocfilehash: 7615c7d3d0556aef2bd2f73148ac986709082ce0
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131845719"
---
# <a name="how-to-start-and-stop-azure-ssis-integration-runtime-on-a-schedule"></a>Guide pratique pour démarrer et arrêter Azure-SSIS Integration Runtime selon une planification

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Cet article explique comment planifier le démarrage et l’arrêt d’Azure-SSIS Integration Runtime avec Azure Data Factory. Azure-SSIS Integration Runtime est une ressource de calcul d’Azure Data Factory dédiée à l’exécution de packages SSIS (SQL Server Integration Services). Un coût est associé à l’exécution d’Azure-SSIS Integration Runtime. Il est donc généralement souhaitable d’exécuter votre runtime d’intégration seulement quand vous devez exécuter des packages SSIS dans Azure et de l’arrêter quand vous n’en avez plus besoin. Vous pouvez utiliser l’interface utilisateur/application Azure Data Factory ou Azure PowerShell pour [démarrer ou arrêter manuellement votre runtime d’intégration](manage-azure-ssis-integration-runtime.md)).

Vous pouvez aussi créer des activités web dans des pipelines Azure Data Factory pour démarrer/arrêter votre runtime d’intégration sur la planification, par exemple en le démarrant le matin avant l’exécution de vos charges de travail ETL quotidiennes, et en l’arrêtant dans l’après-midi une fois qu’elles sont terminées.  Vous pouvez également chaîner une activité Exécuter un package SSIS entre deux activités web qui démarrent et arrêtent votre runtime d’intégration : votre runtime d’intégration démarre/s’arrête donc à la demande, juste à temps avant/après l’exécution du package. Pour plus d’informations sur l’activité Exécuter un package SSIS, consultez l’article [Exécuter un package SSIS avec l’activité Exécuter un package SSIS dans Azure Data Factory](how-to-invoke-ssis-package-ssis-activity.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prérequis
Si vous n’avez pas déjà provisionné votre runtime d’intégration Azure SSIS, provisionnez-le en suivant les instructions données dans ce [tutoriel](./tutorial-deploy-ssis-packages-azure.md). 

## <a name="create-and-schedule-adf-pipelines-that-start-and-or-stop-azure-ssis-ir"></a>Créer et planifier des pipelines Azure Data Factory qui démarrent et/ou arrêtent Azure-SSIS Integration Runtime
Cette section vous montre comment utiliser des activités web dans des pipelines Azure Data Factory pour démarrer/arrêter votre runtime d’intégration Azure-SSIS selon une planification ou à la demande. Nous vous guiderons pour créer des trois pipelines : 

1. Le premier pipeline contient une activité web qui démarre votre runtime d’intégration Azure-SSIS. 
2. Le premier pipeline contient une activité web qui arrête votre runtime d’intégration Azure-SSIS.
3. Le troisième pipeline contient une activité Exécuter un package SSIS chaînée entre deux activités Web qui démarrent/arrêtent votre runtime d’intégration Azure-SSIS. 

Après avoir créé et testé le pipeline, vous pouvez créer un déclencheur de planification et l’associez à un pipeline. Le déclencheur de planification définit une planification pour l’exécution du pipeline associé. 

Par exemple, vous pouvez créer deux déclencheurs : le premier est planifié pour s’exécuter quotidiennement à 6h00 et est associé au premier pipeline, tandis que le second est planifié pour s’exécuter tous les jours à 18h00 et est associé au deuxième pipeline.  De cette façon, vous avez une période de temps de 6h00 à 18h00 tous les jours, où votre runtime d’intégration est en cours d’exécution, prêt à exécuter vos charges de travail ETL quotidiennes.  

Si vous créez un troisième déclencheur qui est planifié pour s’exécuter tous les jours à minuit et qui est associé au troisième pipeline, ce pipeline est exécuté à minuit chaque jour, démarrant votre runtime d’intégration juste avant l’exécution du package, exécutant ensuite votre package et arrêtant immédiatement votre runtime d’intégration juste après l’exécution du package : ainsi, votre runtime d’intégration ne reste pas inactif.

### <a name="create-your-adf"></a>Créer votre fabrique Azure Data Factory

1. Connectez-vous au [portail Azure](https://portal.azure.com/).    
2. Cliquez sur **Nouveau** dans le menu de gauche, puis sur **Données + analyse** et sur **Data Factory**. 
   
   :::image type="content" source="./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png" alt-text="Nouveau -> DataFactory":::
   
3. Dans la page **Nouvelle fabrique de données**, entrez **MyAzureSsisDataFactory** comme **Nom**. 
      
   :::image type="content" source="./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png" alt-text="Page Nouvelle fabrique de données":::
 
   Le nom de votre fabrique Azure Data Factory doit être unique à l’échelle mondiale. Si l’erreur suivante s’affiche, changez le nom de votre fabrique Azure Data Factory (par exemple votrenomMyAzureSsisDataFactory), puis réessayez de la créer. Pour savoir comment nommer les artefacts Azure Data Factory, consultez l’article [Data Factory - Règles de nommage](naming-rules.md).
  
   `Data factory name MyAzureSsisDataFactory is not available`
      
4. Sélectionnez l’**abonnement** Azure sous lequel vous voulez créer votre fabrique Azure Data Factory. 
5. Pour **Groupe de ressources**, effectuez l’une des opérations suivantes :
     
   - Sélectionnez **Utiliser l’existant**, puis sélectionnez un groupe de ressources existant dans la liste déroulante. 
   - Sélectionnez **Créer**, puis entrez le nom de votre groupe de ressources.   
         
   Pour plus d’informations sur les groupes de ressources, consultez l’article [Utilisation des groupes de ressources pour gérer vos ressources Azure](../azure-resource-manager/management/overview.md).
   
6. Pour **Version**, sélectionnez **V2**.
7. Pour **Emplacement**, sélectionnez un des emplacements pris en charge pour la création de fabriques Azure Data Factory dans la liste déroulante.
8. Sélectionnez **Épingler au tableau de bord**.     
9. Cliquez sur **Créer**.
10. Sur le tableau de bord Azure, vous voyez la vignette suivante avec l’état : **Déploiement de Data Factory**. 

    :::image type="content" source="media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png" alt-text="mosaïque déploiement de fabrique de données":::
   
11. Une fois la création terminée, vous pouvez voir votre page Azure Data Factory comme illustré ci-dessous.
   
    :::image type="content" source="./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png" alt-text="Page d’accueil Data Factory":::
   
12. Cliquez sur **Créer et surveiller** pour lancer l’interface utilisateur/application Azure Data Factory sous un onglet distinct.

### <a name="create-your-pipelines"></a>Créer vos pipelines

1. Dans la page d’accueil, sélectionnez **Orchestrer**. 

   :::image type="content" source="./media/doc-common-process/get-started-page.png" alt-text="Capture d’écran montrant la page d’accueil ADF.":::
   
2. Dans la boîte à outils **Activités**, développez le menu **Général**, puis glissez-déposez une activité **Web** sur la surface du concepteur de pipeline. Sous l’onglet **Général** de la fenêtre Propriétés, changez le nom de l’activité en **startMyIR**. Basculez sur l’onglet **Paramètres** et effectuez les actions suivantes :

   1. Pour **URL**, entrez l’URL suivante pour l’API REST qui démarre Azure-SSIS Integration Runtime, en remplaçant `{subscriptionId}`, `{resourceGroupName}`, `{factoryName}` et `{integrationRuntimeName}` par les valeurs réelles pour votre runtime d’intégration : `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}/integrationRuntimes/{integrationRuntimeName}/start?api-version=2018-06-01`.  Vous pouvez aussi copier et coller l’ID de ressource de votre runtime d’intégration à partir de sa page de supervision sur l’interface utilisateur/application Azure Data Factory pour remplacer la partie suivante de l’URL ci-dessus : `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}/integrationRuntimes/{integrationRuntimeName}`
    
      :::image type="content" source="./media/how-to-schedule-azure-ssis-integration-runtime/adf-ssis-ir-resource-id.png" alt-text="ID de ressource d’Azure-SSIS Integration Runtime":::
  
   2. Pour **Méthode**, sélectionnez **POST**. 
   3. Pour **Corps**, entrez `{"message":"Start my IR"}`.
   4. Pour **Authentification**, sélectionnez **Identité managée** afin d’utiliser l’identité managée affectée par le système spécifiée pour votre ADF. Pour plus d’informations, consultez l’article [Identité managée pour Data Factory](./data-factory-service-identity.md).
   5. Pour **Ressource**, entrez `https://management.azure.com/`.
    
      :::image type="content" source="./media/how-to-schedule-azure-ssis-integration-runtime/adf-web-activity-schedule-ssis-ir.png" alt-text="Activité web Azure Data Factory - Planifier le runtime d’intégration SSIS":::
  
3. Clonez le premier pipeline pour en créer un deuxième, en changeant le nom en **stopMyIR** et en remplaçant les propriétés suivantes.

   1. Pour **URL**, entrez l’URL suivante pour l’API REST qui arrête Azure-SSIS Integration Runtime, en remplaçant `{subscriptionId}`, `{resourceGroupName}`, `{factoryName}` et `{integrationRuntimeName}` par les valeurs réelles pour votre runtime d’intégration : `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}/integrationRuntimes/{integrationRuntimeName}/stop?api-version=2018-06-01`.
   2. Pour **Corps**, entrez `{"message":"Stop my IR"}`. 

4. Créez un troisième pipeline, faites glisser et déposez une activité **Exécuter un package SSIS** depuis la boîte à outils **Activités** vers la surface du concepteur de pipeline, puis configurez-la en suivant les instructions de l’article [Appeler un package SSIS en utilisant l’activité Exécuter un package SSIS dans Azure Data Factory](how-to-invoke-ssis-package-ssis-activity.md).  Ensuite, chaînez l’activité Exécuter le package SSIS entre deux activités Web qui démarrent/arrêtent votre runtime d’intégration, similaires aux activités Web des premier et deuxième pipelines.

   :::image type="content" source="./media/how-to-schedule-azure-ssis-integration-runtime/adf-web-activity-on-demand-ssis-ir.png" alt-text="Activité Web Azure Data Factory – Runtime d’intégration SSIS à la demande":::

5. Au lieu de créer manuellement le troisième pipeline, vous pouvez également le créer automatiquement à partir d’un modèle. Pour ce faire, sélectionnez le symbole **…** à côté de **Pipeline** pour dérouler un menu d’actions de pipeline. Ensuite, sélectionnez l’action **Pipeline à partir d’un modèle**, cochez la case **SSIS** sous **Catégorie**, sélectionnez le modèle **Planifier un pipeline ADF pour démarrer et arrêter Azure-SSIS IR juste-à-temps avant et après l’exécution du package SSIS** et sélectionnez votre runtime d’intégration dans le menu déroulant **Azure-SSIS Integration Runtime**. Enfin, sélectionnez le bouton **Utiliser ce modèle**. Votre pipeline est automatiquement créé avec le package SSIS restant à attribuer à l’activité Exécuter le package SSIS.

   :::image type="content" source="./media/how-to-schedule-azure-ssis-integration-runtime/adf-on-demand-ssis-ir-template.png" alt-text="Modèle de runtime d’intégration SSIS à la demande ADF":::

6. Pour rendre le troisième pipeline plus robuste, vous pouvez faire en sorte que les activités Web de démarrage/arrêt de votre runtime d’intégration soient retentées en cas d’erreurs temporaires dues à la connectivité réseau ou à d’autres problèmes et qu’elles ne soient terminées que lorsque votre runtime d’intégration est effectivement démarré/arrêté. Pour ce faire, vous pouvez remplacer chaque activité Web par une activité Until, qui contient à son tour deux activités Web, l’une pour démarrer/arrêter votre runtime d’intégration et l’autre pour vérifier l’état de ce dernier. Appelons les activités Until *Démarrer un runtime d’intégration SSIS* et *Arrêter un runtime d’intégration SSIS*.  L’activité Until *Démarrer un runtime d’intégration SSIS* contient les activités Web *Essayer de démarrer un runtime d’intégration SSIS* et *Obtenir l’état du runtime d’intégration SSIS*. L’activité Until *Arrêter un runtime d’intégration SSIS* contient les activités Web *Essayer d’arrêter un runtime d’intégration SSIS* et *Obtenir l’état du runtime d’intégration SSIS*. Dans l’onglet **Paramètres** des activités Web *Démarrer un runtime d’intégration SSIS*/*Arrêter un runtime d’intégration SSIS*, pour **Expression**, entrez `@equals('Started', activity('Get SSIS IR Status').output.properties.state)`/`@equals('Stopped', activity('Get SSIS IR Status').output.properties.state)`, respectivement.

   :::image type="content" source="./media/how-to-schedule-azure-ssis-integration-runtime/adf-until-activity-on-demand-ssis-ir.png" alt-text="Activité Until Azure Data Factory – Runtime d’intégration SSIS à la demande":::

   Dans les deux activités Until, les activités Web *Essayer de démarrer un runtime d’intégration SSIS*/*Essayer d’arrêter un runtime d’intégration SSIS* sont similaires à celles des premier et deuxième pipelines. Dans l’onglet **Paramètres** des activités Web *Obtenir l’état du runtime d’intégration SSIS*, effectuez les actions suivantes :

   1. Pour **URL**, entrez l’URL suivante pour l’API REST qui obtient l’état d’Azure-SSIS Integration Runtime, en remplaçant `{subscriptionId}`, `{resourceGroupName}`, `{factoryName}` et `{integrationRuntimeName}` par les valeurs réelles pour votre runtime d’intégration : `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}/integrationRuntimes/{integrationRuntimeName}?api-version=2018-06-01`.
   2. Pour **Méthode**, sélectionnez **GET**. 
   3. Pour **Authentification**, sélectionnez **Identité managée** afin d’utiliser l’identité managée affectée par le système spécifiée pour votre ADF. Pour plus d’informations, consultez l’article [Identité managée pour Data Factory](./data-factory-service-identity.md).
   4. Pour **Ressource**, entrez `https://management.azure.com/`.
    
      :::image type="content" source="./media/how-to-schedule-azure-ssis-integration-runtime/adf-until-activity-on-demand-ssis-ir-open.png" alt-text="Activité Until Azure Data Factory – Runtime d’intégration SSIS à la demande ouvert":::

7. Attribuez à l’identité managée pour votre fabrique Azure Data Factory un rôle **Contributeur** pour elle-même, afin que les activités web dans ses pipelines puissent appeler l’API REST pour démarrer/arrêter les runtimes d’intégration Azure-SSIS qui y sont provisionnés.  Dans votre page Azure Data Factory du portail Azure, cliquez sur **Contrôle d’accès (IAM)** , cliquez sur **+ Ajouter une attribution de rôle**, puis, dans le panneau **Ajouter une attribution de rôle**, effectuez les actions suivantes :

   1. Pour **Rôle**, sélectionnez **Contributeur**. 
   2. Pour **Attribuer l’accès à**, sélectionnez **Utilisateur, groupe ou principal du service Azure AD**. 
   3. Pour **Sélectionner**, recherchez le nom de votre fabrique Azure Data Factory et sélectionnez-le. 
   4. Cliquez sur **Enregistrer**.
    
   :::image type="content" source="./media/how-to-schedule-azure-ssis-integration-runtime/adf-managed-identity-role-assignment.png" alt-text="Attribution de rôle d’identité managée Azure Data Factory":::

8. Validez votre fabrique Azure Data Factory et tous les paramètres des pipelines en cliquant sur **Valider tout/Valider** sur la barre d’outils de la fabrique/des pipelines. Fermez la fenêtre **Sortie de la validation de fabrique/pipeline** en cliquant sur le bouton **>>** .  

   :::image type="content" source="./media/how-to-schedule-azure-ssis-integration-runtime/validate-pipeline.png" alt-text="Valider le pipeline":::

### <a name="test-run-your-pipelines"></a>Testez l’exécution de vos pipelines

1. Sélectionnez **Série de tests** sur la barre d’outils pour chaque pipeline et consultez la fenêtre **Sortie** dans le volet inférieur. 

   :::image type="content" source="./media/how-to-schedule-azure-ssis-integration-runtime/test-run-output.png" alt-text="Série de tests":::
    
2. Pour tester le troisième pipeline, si vous stockez votre package SSIS dans le catalogue SSIS (SSISDB), vous pouvez lancer SQL Server Management Studio (SSMS) pour vérifier son exécution. Dans la fenêtre **Se connecter au serveur**, effectuez les actions suivantes. 

    1. Dans le champ **Nom du serveur**, entrez **&lt;nom de votre serveur&gt;.database.windows.net**.
    2. Sélectionnez **Options >>** .
    3. Pour **Se connecter à la base de données**, sélectionnez **SSISDB**.
    4. Sélectionnez **Connecter**. 
    5. Développez **Catalogues Integration Services** -> **SSISDB** -> Votre dossier -> **Projets** -> Votre projet SSIS -> **Packages**. 
    6. Cliquez avec le bouton droit sur votre package SSIS à exécuter, puis sélectionnez **Rapports** -> **Rapports standard** -> **Toutes les exécutions**. 
    7. Vérifiez qu’il s’est exécuté. 

   :::image type="content" source="./media/how-to-schedule-azure-ssis-integration-runtime/verify-ssis-package-run.png" alt-text="Vérifier l’exécution du package SSIS":::

### <a name="schedule-your-pipelines"></a>Planifier vos pipelines

Maintenant que vos pipelines fonctionnent comme prévu, vous pouvez créer des déclencheurs pour les exécuter à des cadences spécifiées. Pour plus d’informations sur l’association de déclencheurs à des pipelines, consultez l’article [Déclencher le pipeline selon une planification](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule).

1. Dans la barre d’outils du pipeline, sélectionnez **Déclencheur**, puis **Nouveau/Modifier**. 

   :::image type="content" source="./media/how-to-schedule-azure-ssis-integration-runtime/trigger-new-menu.png" alt-text="Capture d’écran mettant en évidence l’option de menu Déclencheur -> Nouveau/Modifier.":::

2. Dans le volet **Ajouter des déclencheurs**, sélectionnez **+ Nouveau**.

   :::image type="content" source="./media/how-to-schedule-azure-ssis-integration-runtime/add-triggers-new.png" alt-text="Ajouter des déclencheurs - Nouveau":::

3. Dans le volet **Nouveau déclencheur**, effectuez les actions suivantes : 

    1. Dans le champ **Nom**, entrez un nom pour le déclencheur. Dans l’exemple suivant, **Run daily** est le nom du déclencheur. 
    2. Pour **Type**, sélectionnez **Planification**. 
    3. Pour **Date de début (UTC)** , sélectionnez une date et une heure de début au format UTC. 
    4. Pour **Périodicité**, spécifiez la cadence du déclencheur. Dans l’exemple suivant, la cadence est une fois **Tous les jours**. 
    5. Pour **Fin**, sélectionnez **Pas de fin**, ou entrez une date et une heure de fin après avoir sélectionné **Date**. 
    6. Sélectionnez **Activé** pour activer le déclencheur dès que vous publiez tous les paramètres Azure Data Factory. 
    7. Sélectionnez **Suivant**.

   :::image type="content" source="./media/how-to-schedule-azure-ssis-integration-runtime/new-trigger-window.png" alt-text="Déclencheur -> Nouveau/Modifier":::
    
4. Dans la page **Paramètres d’exécution du déclencheur**, passez en revue les éventuels avertissements, puis cliquez sur **Terminer**. 
5. Publiez tous les paramètres Azure Data Factory en sélectionnant **Publier tout** dans la barre d’outils de la fabrique. 

   :::image type="content" source="./media/how-to-schedule-azure-ssis-integration-runtime/publish-all.png" alt-text="Publier tout":::

### <a name="monitor-your-pipelines-and-triggers-in-azure-portal"></a>Superviser vos pipelines et vos déclencheurs dans le portail Azure

1. Pour superviser les exécutions des déclencheurs et des pipelines, utilisez l’onglet **Surveiller** sur la gauche de Interface utilisateur/application Azure Data Factory. Pour obtenir des instructions détaillées, consultez l’article [Surveiller le pipeline](quickstart-create-data-factory-portal.md#monitor-the-pipeline).

   :::image type="content" source="./media/how-to-schedule-azure-ssis-integration-runtime/pipeline-runs.png" alt-text="Exécutions de pipeline":::

2. Pour voir les exécutions d’activité associées à l’exécution d’un pipeline, sélectionnez le premier lien (**Voir les exécutions d’activités**) dans la colonne **Actions**. Pour le troisième pipeline, vous verrez trois exécutions d’activité, une pour chaque activité chaînée dans le pipeline (l’activité Web pour démarrer votre runtime d’intégration, l’activité Exécuter le package SSIS pour exécuter votre package et l’activité Web pour arrêter votre runtime d’intégration). Pour revenir à l’affichage des exécutions du pipeline, sélectionnez le lien **Pipelines** en haut.

   :::image type="content" source="./media/how-to-schedule-azure-ssis-integration-runtime/activity-runs.png" alt-text="Exécutions d’activités":::

3. Pour voir les exécutions du déclencheur, sélectionnez **Exécutions du déclencheur** dans la liste déroulante sous **Exécutions du pipeline** en haut. 

   :::image type="content" source="./media/how-to-schedule-azure-ssis-integration-runtime/trigger-runs.png" alt-text="Exécutions de déclencheur":::

### <a name="monitor-your-pipelines-and-triggers-with-powershell"></a>Superviser vos pipelines et vos déclencheurs avec PowerShell

Utilisez des scripts comme les exemples suivants pour superviser vos pipelines et vos déclencheurs.

1. Récupérez l’état d’une exécution de pipeline.

   ```powershell
   Get-AzDataFactoryV2PipelineRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -PipelineRunId $myPipelineRun
   ```

2. Obtenez des informations sur un déclencheur.

   ```powershell
   Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name  "myTrigger"
   ```

3. Récupérez l’état d’une exécution de déclencheur.

   ```powershell
   Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "myTrigger" -TriggerRunStartedAfter "2018-07-15" -TriggerRunStartedBefore "2018-07-16"
   ```

## <a name="create-and-schedule-azure-automation-runbook-that-startsstops-azure-ssis-ir"></a>Créer et planifier un runbook Azure Automation qui démarre/arrête Azure-SSIS Integration Runtime

Dans cette section, vous apprenez à créer un runbook Azure Automation qui exécute un script PowerShell, démarrant/arrêtant votre runtime d’intégration Azure-SSIS selon une planification.  Ceci est pratique quand vous voulez exécuter des scripts supplémentaires avant/après le démarrage/arrêt de votre runtime d’intégration pour un pré/post-traitement.

### <a name="create-your-azure-automation-account"></a>Créer votre compte Azure Automation

Si vous n’avez pas de compte Azure Automation, créez-en un en suivant les instructions indiquées de cette étape. Pour obtenir des instructions détaillées, consultez l’article [Créer un compte Azure Automation](../automation/quickstarts/create-account-portal.md). Dans le cadre de cette étape, vous créez un compte **d’identification Azure** (un principal du service dans votre annuaire Azure Active Directory), puis vous lui attribuez un rôle **Contributeur** dans votre abonnement Azure. Vérifiez qu’il s’agit du même abonnement que celui qui contient votre fabrique Azure Data Factory avec Azure SSIS Integration Runtime. Azure Automation utilise ce compte pour s’authentifier auprès d’Azure Resource Manager et pour agir sur vos ressources. 

1. Lancez le navigateur web **Microsoft Edge** ou **Google Chrome**. L’interface utilisateur/application Azure Data Factory est actuellement prise en charge seulement dans les navigateurs web Microsoft Edge et Google Chrome.
2. Connectez-vous au [portail Azure](https://portal.azure.com/).    
3. Sélectionnez **Nouveau** dans le menu de gauche, sélectionnez **Surveillance + gestion**, puis sélectionnez **Automation**. 

   :::image type="content" source="./media/how-to-schedule-azure-ssis-integration-runtime/new-automation.png" alt-text="Capture d’écran mettant en évidence l’option Surveillance + Gestion > Automation.":::
    
2. Dans le volet **Ajouter un compte Automation**, effectuez les actions suivantes.

    1. Pour **Nom**, entrez un nom pour votre compte Azure Automation. 
    2. Dans **Abonnement**, sélectionnez l’abonnement Azure où se trouve votre fabrique Azure Data Factory avec Azure-SSIS Integration Runtime. 
    3. Pour **Groupe de ressources**, sélectionnez **Créer** pour créer un groupe de ressources, ou sélectionnez **Existant** pour sélectionner un groupe de ressources existant. 
    4. Pour **Emplacement**, sélectionnez un emplacement pour votre compte Azure Automation. 
    5. Confirmez **Créer un compte d’identification Azure** en cliquant sur **Oui**. Un principal du service est créé dans votre annuaire Azure Active Directory et un rôle **Contributeur** lui est attribué dans votre abonnement Azure.
    6. Sélectionnez **Épingler au tableau de bord** pour l’afficher de façon permanente dans le tableau de bord Azure. 
    7. Sélectionnez **Create** (Créer). 

   :::image type="content" source="./media/how-to-schedule-azure-ssis-integration-runtime/add-automation-account-window.png" alt-text="Nouveau -> Surveillance + gestion -> Automation":::
   
3. Vous verrez l’état du déploiement de votre compte Azure Automation dans le tableau de bord Azure et dans les notifications. 
    
   :::image type="content" source="./media/how-to-schedule-azure-ssis-integration-runtime/deploying-automation.png" alt-text="Déploiement d’Automation"::: 
    
4. Vous verrez la page d’accueil de votre compte Azure Automation une fois créée. 

   :::image type="content" source="./media/how-to-schedule-azure-ssis-integration-runtime/automation-home-page.png" alt-text="Page d’accueil Automation":::

### <a name="import-adf-modules"></a>Importer des modules Azure Data Factory

1. Sélectionnez **Modules** dans la section **RESSOURCES PARTAGÉES** située dans le menu de gauche, puis vérifiez si **Az.DataFactory** + **Az.Profile** figurent dans la liste des modules.

   :::image type="content" source="media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image1.png" alt-text="Vérifier les modules requis":::

2.  Si vous ne voyez pas **Az.DataFactory**, accédez à PowerShell Gallery pour le [module Az.DataFactory](https://www.powershellgallery.com/packages/Az.DataFactory/). Sélectionnez ensuite **Déployer sur Azure Automation**, votre compte Azure Automation, puis **OK**. Revenez à l’affichage des **Modules** dans la section **RESSOURCES PARTAGÉES** située dans le menu de gauche, puis attendez que l’**ÉTAT** du module **Az.DataFactory** devienne **Disponible**.

    :::image type="content" source="media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image2.png" alt-text="Vérifier le module Data Factory":::

3.  Si vous ne voyez pas **Az.Profile**, accédez à PowerShell Gallery pour le [module Az.Profile](https://www.powershellgallery.com/packages/Az.profile/). Sélectionnez ensuite **Déployer sur Azure Automation**, votre compte Azure Automation, puis **OK**. Revenez à l’affichage des **Modules** dans la section **RESSOURCES PARTAGÉES** située dans le menu de gauche, puis attendez que l’**ÉTAT** du module **Az.Profile** devienne **Disponible**.

    :::image type="content" source="media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image3.png" alt-text="Vérifier le module Profil":::

### <a name="create-your-powershell-runbook"></a>Créer votre runbook PowerShell

La section suivante indique comment créer un runbook PowerShell. Le script associé à votre runbook démarre ou arrête Azure-SSIS Integration Runtime en fonction de la commande que vous spécifiez pour le paramètre **OPERATION**. Cette section ne fournit pas tous les détails de la création d’un runbook. Pour plus d’informations, consultez l’article [Créer un runbook](../automation/learn/powershell-runbook-managed-identity.md).

1. Passez à l’onglet **Runbooks**, puis sélectionnez **+ Ajouter un runbook** dans la barre d’outils. 

   :::image type="content" source="./media/how-to-schedule-azure-ssis-integration-runtime/runbooks-window.png" alt-text="Capture d’écran mettant en évidence le bouton +Ajouter un runbook.":::
   
2. Sélectionnez **Créer un runbook**, puis effectuez les actions suivantes : 

    1. Pour **Nom**, tapez **StartStopAzureSsisRuntime**.
    2. Pour **Type de runbook**, sélectionnez **PowerShell**.
    3. Sélectionnez **Create** (Créer).
    
   :::image type="content" source="./media/how-to-schedule-azure-ssis-integration-runtime/add-runbook-window.png" alt-text="Bouton Ajouter un runbook":::
   
3. Copiez et collez le script PowerShell suivant dans la fenêtre de script de votre runbook. Enregistrez, puis publiez votre runbook en utilisant les boutons **Enregistrer** et **Publier** de la barre d’outils. 

    ```powershell
    Param
    (
          [Parameter (Mandatory= $true)]
          [String] $ResourceGroupName,
    
          [Parameter (Mandatory= $true)]
          [String] $DataFactoryName,
    
          [Parameter (Mandatory= $true)]
          [String] $AzureSSISName,
    
          [Parameter (Mandatory= $true)]
          [String] $Operation
    )
    
    $connectionName = "AzureRunAsConnection"
    try
    {
        # Get the connection "AzureRunAsConnection "
        $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         
    
        "Logging in to Azure..."
        Connect-AzAccount `
            -ServicePrincipal `
            -TenantId $servicePrincipalConnection.TenantId `
            -ApplicationId $servicePrincipalConnection.ApplicationId `
            -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint 
    }
    catch {
        if (!$servicePrincipalConnection)
        {
            $ErrorMessage = "Connection $connectionName not found."
            throw $ErrorMessage
        } else{
            Write-Error -Message $_.Exception
            throw $_.Exception
        }
    }
    
    if($Operation -eq "START" -or $operation -eq "start")
    {
        "##### Starting #####"
        Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $AzureSSISName -Force
    }
    elseif($Operation -eq "STOP" -or $operation -eq "stop")
    {
        "##### Stopping #####"
        Stop-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
    }  
    "##### Completed #####"    
    ```

   :::image type="content" source="./media/how-to-schedule-azure-ssis-integration-runtime/edit-powershell-runbook.png" alt-text="Modifier un runbook PowerShell":::
    
4. Testez votre runbook en sélectionnant le bouton **Démarrer** dans la barre d’outils. 

   :::image type="content" source="./media/how-to-schedule-azure-ssis-integration-runtime/start-runbook-button.png" alt-text="Bouton Démarrer le runbook":::
    
5. Dans le volet **Démarrer le Runbook**, procédez comme suit : 

    1. Pour **NOM DU GROUPE DE RESSOURCES**, entrez le nom du groupe de ressources ayant la fabrique Azure Data Factory avec Azure-SSIS Integration Runtime. 
    2. Pour **NOM DE LA FABRIQUE DE DONNÉES**, entrez le nom de votre fabrique Azure Data Factory avec Azure-SSIS Integration Runtime. 
    3. Pour **AZURESSISNAME**, entrez le nom du runtime d’intégration Azure SSIS. 
    4. Pour **OPERATION**, entrez **START** (Démarrer). 
    5. Sélectionnez **OK**.  

   :::image type="content" source="./media/how-to-schedule-azure-ssis-integration-runtime/start-runbook-window.png" alt-text="Fenêtre Démarrer le runbook":::
   
6. Dans la fenêtre du travail, sélectionnez la vignette **Résultat**. Dans la fenêtre de résultat, attendez qu’apparaisse le message **##### Terminé #####** , après le message **##### Démarrage #####** . Le démarrage d’Azure-SSIS Integration Runtime prend 20 minutes environ. Fermez la fenêtre **Travail** et revenez à la fenêtre **Runbook**.

   :::image type="content" source="./media/how-to-schedule-azure-ssis-integration-runtime/start-completed.png" alt-text="Capture d’écran mettant en évidence la vignette Sortie.":::
    
7. Répétez les deux étapes précédentes, mais en utilisant **STOP** comme valeur pour **OPERATION**. Redémarrez votre runbook en sélectionnant le bouton **Démarrer** dans la barre d’outils. Entrez les noms de votre groupe de ressources, de la fabrique Azure Data Factory et du runtime d’intégration Azure-SSIS. Pour **OPERATION**, entrez **STOP** (Arrêter). Dans la fenêtre de résultat, attendez qu’apparaisse le message **##### Terminé #####** , après le message **##### Arrêt #####** . L’arrêt d’Azure-SSIS Integration Runtime prend moins de temps que son démarrage. Fermez la fenêtre **Travail** et revenez à la fenêtre **Runbook**.

8. Vous pouvez également déclencher votre runbook via un webhook qui peut être créé en sélectionnant l’élément de menu **Webhooks** ou selon une planification qui peut être créée en sélectionnant l’élément de menu **Planifications** comme indiqué ci-dessous.  

## <a name="create-schedules-for-your-runbook-to-startstop-azure-ssis-ir"></a>Créer des planifications pour que le runbook démarre ou arrête Azure-SSIS Integration Runtime

Dans la section précédente, vous avez créé un runbook Azure Automation qui peut démarrer ou arrêter Azure-SSIS Integration Runtime. Dans cette section, vous créez deux planifications pour votre runbook. Quand vous configurez la première planification, vous spécifiez **START** pour **OPERATION**. De même, quand vous configurez la seconde planification, vous spécifiez **STOP** pour **OPERATION**. Pour obtenir des instructions détaillées sur la création des planifications, consultez l’article [Créer une planification](../automation/shared-resources/schedules.md#create-a-schedule).

1. Dans la fenêtre **Runbook**, sélectionnez **Planifications**, puis sélectionnez **+ Ajouter une planification** dans la barre d’outils. 

   :::image type="content" source="./media/how-to-schedule-azure-ssis-integration-runtime/add-schedules-button.png" alt-text="Runtime d’intégration Azure SSIS - Démarré":::
   
2. Dans le volet **Planifier le Runbook**, effectuez les actions suivantes : 

    1. Sélectionnez **Associer une planification à votre runbook**. 
    2. Sélectionnez **Créer une planification**.
    3. Dans le volet **Nouvelle planification**, entrez **Start IR daily** (Démarrer le runtime d’intégration tous les jours) pour **Nom**. 
    4. Pour **Démarre**, entrez une heure postérieure à l’heure actuelle de quelques minutes. 
    5. Pour **Périodicité**, sélectionnez **Récurrent**. 
    6. Pour **Répéter tous les**, entrez **1** et sélectionnez **Jour**. 
    7. Sélectionnez **Create** (Créer). 

   :::image type="content" source="./media/how-to-schedule-azure-ssis-integration-runtime/new-schedule-start.png" alt-text="Planifier le démarrage du runtime d’intégration Azure SSIS":::
    
3. Passez à l’onglet **Paramètres et valeurs pour l’exécution**. Spécifiez les noms de votre groupe de ressources, de la fabrique Azure Data Factory et du runtime d’intégration Azure-SSIS. Pour **OPERATION**, entrez **START**, puis sélectionnez **OK**. Resélectionnez **OK** pour voir la planification dans la page **Planifications** de votre runbook. 

   :::image type="content" source="./media/how-to-schedule-azure-ssis-integration-runtime/start-schedule.png" alt-text="Capture d’écran mettant en évidence le champ Opération.":::
    
4. Répétez les deux étapes précédentes pour créer une planification nommée **Arrêter le runtime d’intégration tous les jours**. Entrez une heure postérieure d’au moins 30 minutes à l’heure que vous avez spécifiée pour la planification **Start IR daily**. Pour **OPERATION**, entrez **STOP**, puis sélectionnez **OK**. Resélectionnez **OK** pour voir la planification dans la page **Planifications** de votre runbook. 

5. Dans la fenêtre **Runbook**, sélectionnez **Travaux** dans le menu de gauche. Vous devez voir les travaux créés par vos planifications aux heures spécifiées et leurs états. Vous pouvez voir les détails des travaux, comme leur sortie, similaires à ce que vous avez vu quand vous avez testé votre runbook. 

   :::image type="content" source="./media/how-to-schedule-azure-ssis-integration-runtime/schedule-jobs.png" alt-text="Planifier le démarrage du runtime d’intégration Azure SSIS":::
    
6. Une fois que vous avez terminé les tests, désactivez vos planifications en les modifiant. Sélectionnez **Planifications** dans le menu de gauche, sélectionnez **Start IR daily/Stop IR daily**, puis sélectionnez **Non** pour **Activé**. 

## <a name="next-steps"></a>Étapes suivantes
Consultez le billet de blog suivant :
-   [Moderniser et étendre vos flux de travail ETL/ELT avec des activités SSIS dans des pipelines ADF](https://techcommunity.microsoft.com/t5/SQL-Server-Integration-Services/Modernize-and-Extend-Your-ETL-ELT-Workflows-with-SSIS-Activities/ba-p/388370)

Consultez les articles suivants de la documentation relative à SSIS : 

- [Déployer, exécuter et surveiller un package SSIS sur Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Se connecter au catalogue SSIS sur Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Planifier l’exécution des packages sur Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
- [Se connecter aux sources de données locales avec l’authentification Windows](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth)