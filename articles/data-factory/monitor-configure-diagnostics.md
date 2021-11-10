---
title: Configurer des paramètres de diagnostic et un espace de travail
description: Découvrez comment configurer des paramètres de diagnostic et un espace de travail Log Analytics pour analyser Azure Data Factory.
author: joshuha-msft
ms.author: joowen
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: monitoring
ms.topic: conceptual
ms.date: 09/02/2021
ms.openlocfilehash: 6637cd45f6c7dd9260eb3bb5af1601457213158f
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131845282"
---
# <a name="configure-diagnostic-settings-and-a-workspace"></a>Configurer des paramètres de diagnostic et un espace de travail

Créez ou ajoutez des paramètres de diagnostic pour votre fabrique de données.

1. Dans le portail Azure, accédez à **Surveillance**. Sélectionnez **Paramètres** > **Paramètres de diagnostic**.

1. Sélectionnez la fabrique de données pour laquelle vous souhaitez définir un paramètre de diagnostic.

1. S’il n’existe aucun paramètre sur la fabrique de données sélectionnée, vous êtes invité à en créer un. Sélectionnez **Activer les diagnostics**.

   :::image type="content" source="media/data-factory-monitor-oms/monitor-oms-image1.png" alt-text="Capture d’écran montrant la création d’un paramètre de diagnostic si aucun paramètre n’existe.":::

   S’il existe des paramètres sur la fabrique de données, vous voyez une liste de paramètres déjà configurés sur cette fabrique de données. Sélectionnez **Ajouter le paramètre de diagnostic**.

   :::image type="content" source="media/data-factory-monitor-oms/add-diagnostic-setting.png" alt-text="Capture d’écran montrant l’ajout d’un paramètre de diagnostic si des paramètres existent.":::

1. Nommez votre paramètre, sélectionnez **Envoyer à Log Analytics**, puis sélectionnez un espace de travail dans **Espace de travail Log Analytics**.

    * En mode _Diagnostics Azure_, les journaux de diagnostic circulent dans la table _AzureDiagnostics_.
    * En mode _Spécifique à la ressource_, les journaux de diagnostic d’Azure Data Factory circulent dans les tables suivantes :
      - _ADFActivityRun_
      - _ADFPipelineRun_
      - _ADFTriggerRun_
      - _ADFSSISIntegrationRuntimeLogs_
      - _ADFSSISPackageEventMessageContext_
      - _ADFSSISPackageEventMessages_
      - _ADFSSISPackageExecutableStatistics_
      - _ADFSSISPackageExecutionComponentPhases_
      - _ADFSSISPackageExecutionDataStatistics_

      Vous pouvez sélectionner différents journaux pertinents pour vos charges de travail à envoyer vers les tableaux Log Analytics. Par exemple : 
        - Si vous n’utilisez pas du tout SQL Server Integration Services (SSIS), vous n’avez pas besoin de sélectionner des journaux SSIS. 
        - Si vous souhaitez consigner les opérations de démarrage, d’arrêt ou de maintenance du runtime d’intégration SSIS, sélectionnez les journaux SSIS IR. 
        - Si vous appelez des exécutions de package SSIS via T-SQL sur SQL Server Management Studio, SQL Server Agent ou d’autres outils désignés, sélectionnez des journaux de packages SSIS. 
        - Si vous appelez des exécutions de package SSIS via des activités Exécuter le package SSIS dans des pipelines Data Factory, sélectionnez tous les journaux.

    * Si vous sélectionnez _AllMetrics_, plusieurs métriques Data Factory sont mises à votre disposition pour surveiller vos charges de travail ou déclencher des alertes. Ces métriques incluent celles des exécutions de déclencheur, de pipeline et d’activité Data Factory, ainsi que des opérations SSIS IR et des exécutions de package SSIS.

   :::image type="content" source="media/data-factory-monitor-oms/monitor-oms-image2.png" alt-text="Capture d’écran montrant l’attribution d’un nom à vos paramètres et la sélection d’un espace de travail Log Analytics.":::

    > [!NOTE]
    > Étant donné qu’une table de journal Azure ne peut pas comporter plus de 500 colonnes, nous vous *recommandons vivement* de sélectionner le _mode spécifique aux ressources_. Pour plus d’informations, consultez [Référence des journaux diagnostics Azure](/azure/azure-monitor/reference/tables/azurediagnostics).

1. Sélectionnez **Enregistrer**.

Après quelques instants, le nouveau paramètre apparaît dans la liste des paramètres pour cette fabrique de données. Les journaux de diagnostic sont diffusés en continu vers cet espace de travail dès que de nouvelles données d’événement sont générées. Jusqu’à 15 minutes peuvent s’écouler entre l’émission d’un événement et son affichage dans Log Analytics.

## <a name="next-steps"></a>Étapes suivantes

[Configurer les journaux de diagnostic via l’API REST Azure Monitor](monitor-logs-rest.md)
