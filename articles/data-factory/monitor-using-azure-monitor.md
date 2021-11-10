---
title: Surveiller les fabriques de données à l’aide d’Azure Monitor
description: Apprenez à utiliser Azure Monitor pour surveiller les pipelines Azure Monitor en activant les journaux de diagnostic à partir des informations de Data Factory.
author: joshuha-msft
ms.author: joowen
ms.reviewer: jburchel
ms.custom: contperf-fy22q1
ms.service: data-factory
ms.subservice: monitoring
ms.topic: conceptual
ms.date: 09/02/2021
ms.openlocfilehash: 91f377e1e092348a9d3a4ea6be3794c6f212d6b8
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131853944"
---
# <a name="monitor-and-alert-data-factory-by-using-azure-monitor"></a>Déclencher des alertes et surveiller les fabriques de données avec Azure Monitor

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Les applications cloud sont complexes, et comprennent de nombreux éléments mobiles. Les analyses fournissent des données pour vous aider à garantir que votre application reste opérationnelle et soit exécutée en toute intégrité. Les analyses vous aident également à éviter des problèmes potentiels et à résoudre les problèmes précédents. Vous pouvez utiliser les données de surveillance pour obtenir des informations détaillées sur votre application. Cette connaissance vous aide à améliorer les performances et la maintenabilité de l’application. Elle vous aide également à automatiser des actions qui nécessitent autrement une intervention manuelle.

Azure Monitor fournit des métriques et des journaux d’activité de niveau de base d’infrastructure pour la plupart des services Azure. Les journaux de diagnostic Azure sont générés par une ressource et fournissent des informations complètes et fréquentes sur le fonctionnement de cette ressource. Azure Data Factory (ADF) peut écrire les journaux de diagnostic dans Azure Monitor. Pour voir une présentation de sept minutes et la démonstration de cette fonctionnalité, regardez la vidéo suivante :

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Monitor-Data-Factory-pipelines-using-Operations-Management-Suite-OMS/player]

Pour plus d’informations, consultez [Vue d’ensemble d’Azure Monitor](../azure-monitor/overview.md).

## <a name="keeping-azure-data-factory-metrics-and-pipeline-run-data"></a>Conserver les mesures d’Azure Data Factory et les données d’exécution de pipeline

Data Factory ne stocke les données d’exécution du pipeline que pendant 45 jours. Utilisez Azure Monitor si vous souhaitez conserver ces données pendant une période plus longue. Monitor vous permet d’acheminer les journaux de diagnostic à des fins d’analyse pour multiplier les cibles.

* **Compte de stockage** : Enregistrez vos journaux de diagnostic dans un compte de stockage à des fins d’audit ou d’inspection manuelle. Vous pouvez utiliser les paramètres de diagnostic pour spécifier la durée de rétention en jours.
* **Event Hub** : Diffusez en continu les journaux vers Azure Event Hubs. Les journaux deviennent des entrées pour un service partenaire ou une solution d’analyse personnalisée telle que Power BI.
* **Log Analytics** : Analysez les journaux avec Log Analytics. L’intégration de Data Factory à Azure Monitor est utile dans les scénarios suivants :
  * Vous souhaitez écrire des requêtes complexes sur un ensemble varié de mesures publiées par la Data Factory sur Monitor. Vous pouvez créer des alertes personnalisées sur ces requêtes via Monitor.
  * Vous souhaitez surveiller l’activité de toutes les fabriques de données. Vous pouvez acheminer les données de plusieurs fabriques de données vers un seul espace de travail Monitor.

Vous pouvez également utiliser un compte de stockage ou un espace de noms Event Hub qui ne se trouve pas dans le même abonnement que la ressource émettrice des journaux. L'utilisateur qui configure le paramètre doit disposer d'un accès Azure RBAC (Contrôle d'accès en fonction du rôle Azure) approprié pour les deux abonnements.

## <a name="next-steps"></a>Étapes suivantes

- [Métriques et alertes Azure Data Factory](monitor-metrics-alerts.md)
- [Surveiller et gérer les pipelines par programmation](monitor-programmatically.md)
