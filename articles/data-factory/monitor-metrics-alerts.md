---
title: Métriques et alertes Data Factory
description: Découvrez les métriques disponibles pour la supervision d’Azure Data Factory.
author: joshuha-msft
ms.author: joowen
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: monitoring
ms.topic: conceptual
ms.date: 09/02/2021
ms.openlocfilehash: becbd026a45a6eedf7cbb9162335f8d6d47de4dd
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131849289"
---
# <a name="data-factory-metrics-and-alerts"></a>Métriques et alertes Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Factory fournit les métriques et les alertes suivantes pour permettre la supervision du service.

## <a name="data-factory-metrics"></a>Métriques de Data Factory

Azure Monitor vous apporte une visibilité sur les performances et l’intégrité de vos charges de travail Azure. Le type de données d’analyse le plus important de Monitor est la métrique, également appelée compteur de performances. Des métriques sont émises par la plupart des ressources Azure. Monitor propose plusieurs façons de configurer et d’utiliser ces métriques à des fins d’analyse et de résolution des problèmes.

Voici quelques-unes des métriques émises par Azure Data Factory version 2.

| **Mesure**                           | **Nom d’affichage de la métrique**                  | **Unité** | **Type d’agrégation** | **Description**                |
|--------------------------------------|------------------------------------------|----------|----------------------|--------------------------------|
| ActivityCancelledRuns                 | Métriques d’exécutions d’activité annulées           | Count    | Total                | Nombre total d’exécutions d’activité ayant été annulées dans une fenêtre d’une minute. |
| ActivityFailedRuns                   | Métriques d’exécutions d’activité ayant échoué             | Count    | Total                | Nombre total d’exécutions d’activité ayant échoué dans une fenêtre d’une minute. |
| ActivitySucceededRuns                | Métriques d’exécutions d’activité ayant abouti          | Count    | Total                | Nombre total d’exécutions d’activité ayant réussi dans une fenêtre d’une minute. |
| PipelineCancelledRuns                 | Métriques d’exécutions de pipeline annulées           | Count    | Total                | Nombre total d’exécutions de pipeline ayant été annulées dans une fenêtre d’une minute. |
| PipelineFailedRuns                   | Métriques d’exécutions de pipeline ayant échoué             | Count    | Total                | Nombre total d’exécutions de pipeline ayant échoué dans une fenêtre d’une minute. |
| PipelineSucceededRuns                | Métriques d’exécutions de pipeline ayant abouti          | Count    | Total                | Nombre total d’exécutions de pipeline ayant réussi dans une fenêtre d’une minute. |
| TriggerCancelledRuns                  | Métriques d’exécutions de déclencheur annulées            | Count    | Total                | Nombre total d’exécutions de déclencheur ayant été annulées dans une fenêtre d’une minute. |
| TriggerFailedRuns                    | Métriques d’exécutions de déclencheur ayant échoué              | Count    | Total                | Nombre total d’exécutions de déclencheur ayant échoué dans une fenêtre d’une minute. |
| TriggerSucceededRuns                 | Métriques d’exécutions de déclencheur ayant abouti           | Count    | Total                | Nombre total d’exécutions de déclencheur ayant réussi dans une fenêtre d’une minute. |
| SSISIntegrationRuntimeStartCancelled  | Métriques de démarrages annulés du runtime d’intégration SSIS           | Count    | Total                | Nombre total de démarrages du runtime d’intégration SSIS qui ont été annulés dans une fenêtre d’une minute. |
| SSISIntegrationRuntimeStartFailed    | Métriques de démarrages du runtime d’intégration de SSIS ayant échoué             | Count    | Total                | Nombre total de démarrages du runtime d’intégration de SSIS ayant échoués dans une fenêtre d’une minute. |
| SSISIntegrationRuntimeStartSucceeded | Métriques de démarrages du runtime d’intégration de SSIS réussis          | Count    | Total                | Nombre total de démarrages du runtime d’intégration de SSIS ayant réussi dans une fenêtre d’une minute. |
| SSISIntegrationRuntimeStopStuck      | Métriques d’arrêts du runtime d’intégration de SSIS bloqués               | Count    | Total                | Nombre total d’arrêts du runtime d’intégration de SSIS bloqués dans une fenêtre d’une minute. |
| SSISIntegrationRuntimeStopSucceeded  | Métriques d’arrêts du runtime d’intégration de SSIS réussis           | Count    | Total                | Nombre total d’arrêts du runtime d’intégration de SSIS ayant réussi dans une fenêtre d’une minute. |
| SSISPackageExecutionCancelled         | Métriques d’exécutions de package SSIS annulées  | Count    | Total                | Nombre total d’exécutions de packages SSIS ayant été annulés dans une fenêtre d’une minute. |
| SSISPackageExecutionFailed           | Métriques d’exécutions de package SSIS ayant échouées    | Count    | Total                | Nombre total d’exécutions de packages SSIS ayant échoué dans une fenêtre d’une minute. |
| SSISPackageExecutionSucceeded        | Métriques d’exécutions de package SSIS ayant réussies | Count    | Total                | Nombre total d’exécutions de packages SSIS ayant réussi dans une fenêtre d’une minute. |
| PipelineElapsedTimeRuns | Métriques d’exécutions de pipeline - Temps écoulé | Count | Total | Dans une fenêtre d’une minute, nombre de fois où un pipeline s’exécute plus longtemps que la durée attendue définie par l’utilisateur. [(En savoir plus)](tutorial-operationalize-pipelines.md) |

Pour accéder aux métriques, suivez les instructions fournies dans [Plateforme de données Azure Monitor](../azure-monitor/data-platform.md).

> [!NOTE]
> À l’exception de _PipelineElapsedTimeRuns_, seuls les événements des exécutions de pipeline et d’activité déclenchés et terminés sont émis. Les exécutions en cours et de débogage *ne sont pas* émises. En revanche, les événements de *toutes* les exécutions de packages SSIS sont émis, y compris ceux qui sont terminés et en cours, indépendamment de leurs méthodes d’invocation. Par exemple, vous pouvez invoquer des exécutions de packages sur SQL Server Data Tools avec Azure, via T-SQL sur SQL Server Management Studio, SQL Server Agent ou d’autres outils désignés, et en tant qu’exécutions déclenchées ou de débogage d’activités Exécuter le package SSIS dans des pipelines Data Factory.

## <a name="data-factory-alerts"></a>Alertes Data Factory

Connectez-vous au portail Azure, puis sélectionnez **Monitor** > **Alertes** pour créer des alertes.

:::image type="content" source="media/monitor-using-azure-monitor/alerts_image3.png" alt-text="Capture d’écran montrant des alertes dans le menu du portail.":::

### <a name="create-alerts"></a>Créez des alertes

1. Sélectionnez **+ Nouvelle règle d’alerte** pour créer une alerte.

    :::image type="content" source="media/monitor-using-azure-monitor/alerts_image4.png" alt-text="Capture d’écran montrant la création d’une règle d’alerte.":::

1. Définissez la condition de l’alerte.

    > [!NOTE]
    > Veillez à sélectionner **Toutes** dans la liste déroulante **Filtrer par type de ressource**.

    :::image type="content" source="media/monitor-using-azure-monitor/alerts_image5.png" alt-text="Capture d’écran montrant les sélections permettant d’ouvrir le volet pour choisir une ressource.":::

    :::image type="content" source="media/monitor-using-azure-monitor/alerts_image6.png" alt-text="Capture d’écran montrant les sélections permettant d’ouvrir le volet pour configurer la logique du signal.":::

    :::image type="content" source="media/monitor-using-azure-monitor/alerts_image7.png" alt-text="Capture d’écran montrant la configuration de la logique du signal.":::

1. Définissez les détails de l’alerte.

    :::image type="content" source="media/monitor-using-azure-monitor/alerts_image8.png" alt-text="Capture d’écran montrant les détails de l’alerte.":::

1. Définissez le groupe d’actions.

    :::image type="content" source="media/monitor-using-azure-monitor/alerts_image9.png" alt-text="Capture d’écran montrant la création d’une règle, avec Nouveau groupe d’actions mis en évidence.":::

    :::image type="content" source="media/monitor-using-azure-monitor/alerts_image10.png" alt-text="Capture d’écran montrant la création d’un nouveau groupe d’actions.":::

    :::image type="content" source="media/monitor-using-azure-monitor/alerts_image11.png" alt-text="Capture d’écran montrant la configuration des options E-mail/SMS/Push/Voix.":::

    :::image type="content" source="media/monitor-using-azure-monitor/alerts_image12.png" alt-text="Capture d’écran montrant la définition d’un groupe d’actions.":::

## <a name="next-steps"></a>Étapes suivantes

[Configurer les paramètres de diagnostic et l’espace de travail](monitor-configure-diagnostics.md)
