---
title: Vue d’ensemble d’Azure Application Insights Agent | Microsoft Docs
description: Vue d’ensemble d’Application Insights Agent. Surveillez les performances de site web sans avoir à redéployer le site web. Fonctionne avec les applications web ASP.NET hébergées en local, dans des machines virtuelles ou sur Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 09/16/2019
ms.openlocfilehash: 3337cf0eb5bfff686f9047d0f5ffea6dde670f3e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131078196"
---
# <a name="deploy-azure-monitor-application-insights-agent-for-on-premises-servers"></a>Déployer Azure Monitor Application Insights Agent pour les serveurs locaux

> [!IMPORTANT]
> Ces informations d’aide sont recommandées pour les déploiements locaux et les déploiements sur les environnements cloud non Azure d’Application Insights Agent. Voici l’approche recommandée pour les [déploiements de machines virtuelles et de groupes de machines virtuelles identiques Azure](./azure-vm-vmss-apps.md).

Application Insights Agent (anciennement appelé Status Monitor V2) est un module PowerShell publié sur [PowerShell Gallery](https://www.powershellgallery.com/packages/Az.ApplicationMonitor).
Il remplace Status Monitor.
Les données de télémétrie sont envoyées au Portail Azure, dans lequel vous pouvez [surveiller](./app-insights-overview.md) votre application.

> [!NOTE]
> Le module prend actuellement en charge l’instrumentation sans code des applications web .NET et .NET Core hébergées par IIS. Utilisez un Kit de développement logiciel (SDK) pour instrumenter les applications Java et Node.js.

## <a name="powershell-gallery"></a>PowerShell Gallery

Application Insights Agent se trouve ici : https://www.powershellgallery.com/packages/Az.ApplicationMonitor.

![PowerShell Gallery](https://img.shields.io/powershellgallery/v/Az.ApplicationMonitor.svg?color=Blue&label=Current%20Version&logo=PowerShell&style=for-the-badge)


## <a name="instructions"></a>Instructions
- Consultez les [instructions de prise en main](status-monitor-v2-get-started.md) pour vous familiariser avec les exemples de code concis.
- Consultez les [instructions détaillées](status-monitor-v2-detailed-instructions.md) pour une présentation approfondie de la prise en main.

## <a name="powershell-api-reference"></a>Référence API PowerShell
- [Disable-ApplicationInsightsMonitoring](./status-monitor-v2-api-reference.md#disable-applicationinsightsmonitoring)
- [Disable-InstrumentationEngine](./status-monitor-v2-api-reference.md#disable-instrumentationengine)
- [Enable-ApplicationInsightsMonitoring](./status-monitor-v2-api-reference.md#enable-applicationinsightsmonitoring)
- [Enable-InstrumentationEngine](./status-monitor-v2-api-reference.md#enable-instrumentationengine)
- [Get-ApplicationInsightsMonitoringConfig](./status-monitor-v2-api-reference.md#get-applicationinsightsmonitoringconfig)
- [Get-ApplicationInsightsMonitoringStatus](./status-monitor-v2-api-reference.md#get-applicationinsightsmonitoringstatus)
- [Set-ApplicationInsightsMonitoringConfig](./status-monitor-v2-api-reference.md#set-applicationinsightsmonitoringconfig)
- [Start-ApplicationInsightsMonitoringTrace](./status-monitor-v2-api-reference.md#start-applicationinsightsmonitoringtrace)

## <a name="troubleshooting"></a>Résolution des problèmes
- [Dépannage](status-monitor-v2-troubleshoot.md)
- [Problèmes connus](status-monitor-v2-troubleshoot.md#known-issues)


## <a name="faq"></a>Questions fréquentes (FAQ)

- Application Insights Agent prend-il en charge les installations basées sur un serveur proxy ?

  *Oui*. Il existe plusieurs façons de télécharger Application Insights Agent. Si votre ordinateur a accès à Internet, vous pouvez intégrer PowerShell Gallery à l’aide des paramètres `-Proxy`.
Vous pouvez également télécharger le module manuellement et l’installer sur votre ordinateur ou l’utiliser directement.
Chacune de ces options est décrite dans les [instructions détaillées](status-monitor-v2-detailed-instructions.md).

- Status Monitor v2 prend-il en charge les applications ASP.NET Core ?

  *Oui*. À partir de l’[agent Application Insights 2.0.0-beta1](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/2.0.0-beta1), les applications ASP.NET Core hébergées dans IIS sont prises en charge.

- Comment vérifier que l’activation a réussi ?

  - Le cmdlet [Get-ApplicationInsightsMonitoringStatus](./status-monitor-v2-api-reference.md#get-applicationinsightsmonitoringstatus) peut être utilisé pour vérifier que l’activation a réussi.
  - Nous vous recommandons d’utiliser les [métriques en temps réel](./live-stream.md) pour déterminer rapidement si votre application envoie les données de télémétrie.

  - Vous pouvez également utiliser [Log Analytics](../logs/log-analytics-tutorial.md) pour répertorier tous les rôles de cloud qui envoient des données de télémétrie :
      ```Kusto
      union * | summarize count() by cloud_RoleName, cloud_RoleInstance
      ```


## <a name="release-notes"></a>Notes de publication

### <a name="200-beta2"></a>2.0.0-beta2

- Mise à jour d’ApplicationInsights .NET/Kit SDK .NET Core vers 2.18.1-redfield.

### <a name="200-beta1"></a>2.0.0-beta1

- Ajout de la fonction d’instrumentation automatique ASP.NET Core.


## <a name="next-steps"></a>Étapes suivantes

Affichez vos données de télémétrie :

* [Explorez les métriques](../essentials/metrics-charts.md) pour surveiller les performances et l’utilisation.
* [Effectuez des recherches dans les événements et les journaux](./diagnostic-search.md) pour diagnostiquer les problèmes.
* [Utilisez la fonctionnalité Analytics](../logs/log-query-overview.md) pour des requêtes plus élaborées.
* [Créez des tableaux de bord](./overview-dashboard.md).

Ajoutez des données de télémétrie :

* [Créez des tests web](monitor-web-app-availability.md) pour vous assurer que votre site reste actif.
* [Ajoutez la télémétrie de client web](./javascript.md) pour afficher les exceptions à partir du code de la page web et activer le suivi des appels.
* [Ajoutez le kit de développement logiciel (SDK) Application Insights à votre code](./asp-net.md) afin de pouvoir insérer un suivi et journaliser les appels.
