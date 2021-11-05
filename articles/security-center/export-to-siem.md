---
title: Diffusez vos alertes de Microsoft Defender pour le cloud à des systèmes Security Information and Event Management (SIEM) et d’autres solutions de surveillance
description: Découvrez comment diffuser en continu vos alertes de sécurité dans Microsoft Sentinel, des systèmes SIEM tiers, SOAR ou des solutions ITSM
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 10/06/2020
ms.author: memildin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 7235fc2fe3fb06cf342408e9aa99bf6db2e13393
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131075747"
---
# <a name="stream-alerts-to-a-siem-soar-or-it-service-management-solution"></a>Diffuser des alertes vers un système SIEM, SOAR ou une solution de gestion des services informatiques

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Microsoft Defender pour le cloud peut diffuser vos alertes de sécurité vers des systèmes SIEM tiers, SOAR ou des solutions de gestion des services informatiques populaires.

Il existe des outils Azure natifs pour vous permettre d’afficher les données d’alerte dans toutes les solutions les plus populaires actuellement utilisées, y compris :

- **Microsoft Sentinel**
- **Splunk Enterprise and Splunk Cloud**
- **QRadar d’IBM**
- **ServiceNow**
- **ArcSight**
- **Power BI**
- **Palo Alto Networks**

## <a name="stream-alerts-to-microsoft-sentinel"></a>Diffuser des alertes à Microsoft Sentinel 

Defender pour le cloud s’intègre en mode natif à Microsoft Sentinel, la solution SIEM et SOAR native du cloud Azure. 

[En savoir plus sur Microsoft Sentinel](../sentinel/overview.md).

### <a name="microsoft-sentinels-connectors-for-defender-for-cloud"></a>Connecteurs Microsoft Sentinel pour Defender pour le cloud

Microsoft Sentinel comprend des connecteurs intégrés pour Microsoft Defender pour le cloud au niveau de l’abonnement et du client :

- [Diffuser en continu des alertes vers Microsoft Sentinel au niveau de l’abonnement](../sentinel/connect-azure-security-center.md)
- [Connecter tous les abonnements de votre client à Microsoft Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/azure-security-center-auto-connect-to-sentinel/ba-p/1387539) 

Quand vous connectez Defender pour le cloud à Microsoft Sentinel, l’état des alertes Defender pour le cloud qui sont ingérées dans Microsoft Sentinel est synchronisé entre les deux services. Ainsi, par exemple, lorsqu’une alerte est fermée dans Defender pour le cloud, cette alerte s’affiche également comme étant fermée dans Microsoft Sentinel. Le changement d’état d’une alerte dans Defender pour le cloud n’affecte pas l’état des **incidents** Microsoft Sentinel qui contiennent l’alerte Microsoft Sentinel synchronisée, uniquement celui de l’alerte synchronisée.

L’activation de la fonctionnalité en préversion, **synchronisation d’alerte bidirectionnelle**, synchronise automatiquement l’état des alertes Defender pour le cloud d’origine avec les incidents Azure Sentinel qui contiennent les copies de ces alertes Defender pour le cloud. Ainsi, par exemple, lorsqu’un incident Microsoft Sentinel contenant une alerte Defender pour le cloud est fermé, Defender pour le cloud ferme automatiquement l’alerte d’origine correspondante.

Pour plus d’informations, consultez [Connecter les alertes Defender pour le cloud de Microsoft defender pour le cloud](../sentinel/connect-azure-security-center.md).

> [!NOTE]
> La fonctionnalité de synchronisation d’alerte bidirectionnelle n’est pas disponible dans le cloud Azure Government. 

### <a name="configure-ingestion-of-all-audit-logs-into-microsoft-sentinel"></a>Configurer l’ingestion de tous les journaux d’audit dans Microsoft Sentinel 

Une autre solution pour examiner les alertes Defender pour le cloud dans Microsoft Sentinel consiste à diffuser vos journaux d’audit dans Microsoft Sentinel :
    - [Connecter les événements de sécurité Windows](../sentinel/connect-windows-security-events.md)
    - [Collecter des données de sources Linux à l’aide de Syslog](../sentinel/connect-syslog.md)
    - [Connecter des données à partir du journal d’activité Azure](../sentinel/data-connectors-reference.md#azure-activity)

> [!TIP]
> Microsoft Sentinel est facturé en fonction du volume de données ingérées pour l’analyse dans Microsoft Sentinel et stocké dans l’espace de travail Azure Monitor Log Analytics. Microsoft Sentinel offre un modèle de tarification flexible et prévisible. [En savoir plus sur la page de tarification de Microsoft Sentinel](https://azure.microsoft.com/pricing/details/azure-sentinel/).


## <a name="stream-alerts-with-microsoft-graph-security-api"></a>Diffuser en continu des alertes avec l’API de sécurité Microsoft Graph

Defender pour le cloud dispose d’une intégration prête à l’emploi avec l’API de sécurité Microsoft Graph. Aucune configuration n’est requise et aucun coût supplémentaire n’est nécessaire. 

Vous pouvez utiliser cette API pour diffuser en continu des alertes à partir de votre **locataire entier** (et des données de nombreux autres produits de sécurité Microsoft) dans des solutions SIEM et autres plateformes tierces populaires :

- **Splunk Enterprise et Splunk Cloud** - [Utiliser le module complémentaire d’API de sécurité Microsoft Graph pour Splunk](https://splunkbase.splunk.com/app/4564/) 
- **Power BI** - [Se connecter à l’API de sécurité Microsoft Graph dans Power BI Desktop](/power-bi/connect-data/desktop-connect-graph-security)
- **ServiceNow** - [Suivre les instructions d’installation et de configuration de l’application d’API de sécurité Microsoft Graph à partir du magasin ServiceNow](https://docs.servicenow.com/bundle/orlando-security-management/page/product/secops-integration-sir/secops-integration-ms-graph/task/ms-graph-install.html)
- **QRadar** - [Module de prise en charge des appareils IBM pour Microsoft Defender pour le cloud via l’API Microsoft Graph](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/com.ibm.dsm.doc/c_dsm_guide_ms_azure_security_center_overview.html) 
- **Palo Alto Networks**, **Anomali**, **Lookout**, **InSpark**, et plus - [API de sécurité Microsoft Graph](https://www.microsoft.com/security/business/graph-security-api#office-MultiFeatureCarousel-09jr2ji)

[En savoir plus sur l’API de sécurité Microsoft Graph](https://www.microsoft.com/security/business/graph-security-api).


## <a name="stream-alerts-with-azure-monitor"></a>Diffuser en continu des alertes avec Azure Monitor 

Pour diffuser des alertes dans **ArcSight**, **Splunk**, **SumoLogic**, les serveurs syslog, **LogRhythm**, **la plateforme d’observabilité cloud Logz.io** et d’autres solutions de surveillance. Connectez Defender pour cloud à Azure Monitor via Azure Event Hubs :

1. Activez l’[exportation continue](continuous-export.md) pour diffuser des alertes Defender pour le cloud vers une instance Azure Event Hub dédiée au niveau de l’abonnement. 
    > [!TIP]
    > Pour effectuer cette opération au niveau du groupe d’administration à l’aide d’Azure Policy, consultez [Créer des configurations d’automatisation d’exportation continue à l’échelle](continuous-export.md?tabs=azure-policy#configure-continuous-export-at-scale-using-the-supplied-policies)

1. [Connectez Azure Event Hub à votre solution préférée à l’aide des connecteurs intégrés d’Azure Monitor](../azure-monitor/essentials/stream-monitoring-data-event-hubs.md#partner-tools-with-azure-monitor-integration).

1. Si vous le souhaitez, diffusez les journaux bruts dans Azure Event Hub et connectez-vous à votre solution préférée. En savoir plus sur les [données de surveillance disponibles](../azure-monitor/essentials/stream-monitoring-data-event-hubs.md#monitoring-data-available).

> [!TIP]
> Pour afficher les schémas d’événements des types de données exportés, visitez les [schémas d’événements Event Hub](https://aka.ms/ASCAutomationSchemas).


## <a name="next-steps"></a>Étapes suivantes

Cette page explique comment garantir que vos données d’alerte Microsoft Defender pour le cloud sont disponibles dans l’outil SIEM, SOAR ou ITSM de votre choix. Consultez les documents connexes suivants :

- [Présentation de Microsoft Sentinel](../sentinel/overview.md)
- [Validation d’alerte dans Microsoft Defender pour le cloud](alert-validation.md) : vérifiez que vos alertes sont correctement configurées
- [Exportation continue des données Defender pour le cloud](continuous-export.md)
