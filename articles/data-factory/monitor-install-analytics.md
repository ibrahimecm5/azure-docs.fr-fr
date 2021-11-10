---
title: Installer la solution Azure Data Factory Analytics à partir de la Place de marché Azure
description: Découvrez comment installer une solution Azure Data Factory Analytics à partir de la Place de marché Azure.
author: joshuha-msft
ms.author: joowen
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: monitoring
ms.topic: conceptual
ms.date: 09/02/2021
ms.openlocfilehash: 07e60aef3e6e3a968d22f76351d00cb722e99f78
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131850410"
---
# <a name="install-azure-data-factory-analytics-solution-from-azure-marketplace"></a>Installer la solution Azure Data Factory Analytics à partir de la Place de marché Azure

Cette solution fournit un résumé de l’intégrité globale de votre fabrique de données, avec des options permettant d’examiner les détails et de résoudre les problèmes de comportements inattendus. Avec des affichages enrichis et prêts à l’emploi, vous pouvez vous familiariser avec le traitement des clés, notamment :

* Un récapitulatif des exécutions de pipeline, d’activité et de déclencheur de la fabrique de données.
* Une possibilité d’effectuer des recherches dans les exécutions d’activité de la fabrique de données par type.
* Un résumé des principales erreurs liées à l’activité du pipeline de la fabrique de données.

1. Accédez à **Place de marché Azure**, sélectionnez le filtre **Analytics**, puis cherchez **Azure Data Factory Analytics (préversion)** .

   :::image type="content" source="media/data-factory-monitor-oms/monitor-oms-image3.png" alt-text="Capture d’écran montrant comment accéder à Place de marché Azure, sélectionner le filtre Analytics et choisir Azure Data Factory Analytics (préversion).":::

1. Examinez les détails d’**Azure Data Factory Analytics (préversion)** .

   :::image type="content" source="media/data-factory-monitor-oms/monitor-oms-image4.png" alt-text="Capture d’écran montrant les détails d’Azure Data Factory Analytics (préversion).":::

1. Sélectionnez **Créer**, puis créez ou sélectionnez **Espace de travail Log Analytics**.

   :::image type="content" source="media/data-factory-monitor-oms/monitor-log-analytics-image-5.png" alt-text="Capture d’écran montrant la création d’une nouvelle solution.":::

## <a name="monitor-data-factory-metrics"></a>Surveiller les métriques de Data Factory

L’installation de cette solution crée un ensemble de vues par défaut à l’intérieur de la section classeurs de l’espace de travail Log Analytics choisi. Par conséquent, les métriques suivantes sont activées :

* Exécutions de données : 1) Exécutions de pipelines par la fabrique de données
* Exécutions d’ADF : 2) Exécutions d’activités par la fabrique de données
* Exécutions d’ADF : 3) Exécutions de déclencheur par la fabrique de données
* Erreurs d’ADF : 1) Les 10 principales erreurs de pipeline par la fabrique de données
* Erreurs d’ADF : 2) Les 10 principales exécutions d’activités par la fabrique de données
* Erreurs d’ADF : 3) Les 10 principales erreurs de déclencheur par la fabrique de données
* Statistiques d’ADF : 1) Exécutions d’activités par type
* Statistiques d’ADF : 2) Exécutions de déclencheurs par type
* Statistiques d’ADF : 3) Durée maximale d’exécutions de pipeline

:::image type="content" source="media/data-factory-monitor-oms/monitor-oms-image6.png" alt-text="Capture d’écran montrant une fenêtre avec Classeurs (préversion) et AzureDataFactoryAnalytics en évidence.":::

Vous pouvez visualiser les métriques ci-dessus, consulter les requêtes derrière ces métriques, modifier les requêtes, créer des alertes, et exécuter d’autres action.

:::image type="content" source="media/data-factory-monitor-oms/monitor-oms-image8.png" alt-text="Capture d’écran montrant une représentation graphique des exécutions de pipeline par la fabrique de données.":::

> [!NOTE]
> Azure Data Factory Analytics (préversion) envoie des journaux de diagnostic à des tables de destination _spécifiques aux ressources_. Vous pouvez écrire des requêtes portant sur les tables suivantes : _ADFPipelineRun_, _ADFTriggerRun_ et _ADFActivityRun_.

## <a name="next-steps"></a>Étapes suivantes

[Surveiller et gérer les pipelines par programmation](monitor-programmatically.md)
