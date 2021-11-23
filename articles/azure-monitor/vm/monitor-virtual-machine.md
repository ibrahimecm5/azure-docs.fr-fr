---
title: Analyse des machines virtuelles avec Azure Monitor
description: Découvrez comment utiliser Azure Monitor pour surveiller l’intégrité et les performances des machines virtuelles et de leurs charges de travail.
ms.service: azure-monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/02/2021
ms.openlocfilehash: c1506fe5fae652c198d53b5b354e818ede5c8a46
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132315783"
---
# <a name="monitor-virtual-machines-with-azure-monitor"></a>Analyse des machines virtuelles avec Azure Monitor
Ce scénario décrit comment utiliser Azure Monitor pour surveiller l’intégrité et les performances des machines virtuelles et de leurs charges de travail. Il comprend la collecte des données de télémétrie critiques pour la surveillance, la surveillance et la visualisation des données collectées pour identifier les tendances, et comment configurer des alertes pour être informé de manière proactive des problèmes critiques.

Cet article présente le scénario et fournit des concepts généraux pour la surveillance des machines virtuelles dans Azure Monitor. Si vous souhaitez accéder directement à une zone spécifique, consultez un des autres articles qui font partie de ce scénario décrit dans le tableau suivant.

| Article | Description |
|:---|:---|
| [Activer la supervision](monitor-virtual-machine-configure.md) | Configurez Azure Monitor pour surveiller les machines virtuelles, notamment VM insights et l’activation de chaque machine virtuelle pour la surveillance.  |
| [Analyser](monitor-virtual-machine-analyze.md) | Analysez les données de surveillance collectées par Azure Monitor à partir des machines virtuelles et de leurs systèmes d’exploitation invités et des applications pour identifier les tendances et les informations critiques. |
| [Alertes](monitor-virtual-machine-alerts.md)   | Créez des alertes pour identifier de manière proactive les problèmes critiques dans vos données de surveillance. |
| [Superviser la sécurité](monitor-virtual-machine-security.md) | Découvrez les services Azure pour la surveillance de la sécurité des machines virtuelles. |
| [Superviser les charges de travail](monitor-virtual-machine-workloads.md) | Surveillez les applications et autres charges de travail en cours d’exécution sur vos machines virtuelles. |

> [!IMPORTANT]
> Ce scénario n’inclut pas les fonctionnalités qui ne sont pas en disponibilité générale. Les fonctionnalités en préversion publique, telles que l’[intégrité de l’invité d’une machine virtuelle](vminsights-health-overview.md), peuvent modifier de manière significative les suggestions présentées ici. Le scénario sera mis à jour à mesure que les fonctionnalités d’évaluation passeront en disponibilité générale.

## <a name="types-of-machines"></a>Types de machines
Ce scénario comprend la surveillance des types suivants de machines à l’aide d’Azure Monitor. La plupart des processus décrits ici sont les mêmes, quel que soit le type de machine. Les considérations relatives aux différents types de machines sont clairement identifiées le cas échéant. Parmi les types de machines, on compte : 

- Les machines virtuelles Azure.
- Les groupes de machines virtuelles identiques Azure.
- Les machines hybrides, qui sont des machines virtuelles s’exécutant dans d’autres clouds avec un fournisseur de services managé ou en local. Elles comprennent aussi les machines physiques exécutées en local.

## <a name="layers-of-monitoring"></a>Couches de surveillance
Il existe quatre couches sur une machine virtuelle qui nécessitent une surveillance. Chaque couche présente un ensemble distinct de spécifications de télémétrie et de surveillance. 

| Couche | Description |
|:---|:---|
| Hôte de machine virtuelle | L’hôte de machine virtuelle dans Azure. Azure Monitor n’a pas accès à l’hôte dans d’autres clouds, mais doit s’appuyer sur les informations collectées auprès du système d’exploitation invité. L’hôte peut être utile pour le suivi des activités comme les modifications de configuration, mais il n’est généralement pas utilisé pour les alertes importantes. |
| Système d’exploitation invité | Le système d’exploitation s’exécutant sur la machine virtuelle, une version de Windows ou Linux. Une quantité importante de données de surveillance est disponible sur le système d’exploitation invité, par exemple les données sur les performances et les événements. VM insights dans Azure Monitor fournissent une quantité significative de logique pour surveiller l’intégrité et les performances du système d’exploitation invité. |
| Charges de travail | Charges de travail exécutées dans le système d’exploitation invité qui prennent en charge vos applications d’entreprise. Azure Monitor offre une surveillance prédéfinie pour certaines charges de travail. En général, vous devez configurer la collecte de données et les alertes pour les autres charges de travail à l’aide des données de surveillance qu’elles génèrent. |
| Application | L’application métier qui dépend de vos machines virtuelles peut être surveillée avec [Application Insights](../app/app-insights-overview.md). 

:::image type="content" source="media/monitor-virtual-machines/monitoring-layers.png" alt-text="Diagramme qui montre les couches de surveillance." lightbox="media/monitor-virtual-machines/monitoring-layers.png":::

## <a name="vm-insights"></a>Insights de machine virtuelle
Ce scénario se concentre sur [VM Insights](../vm/vminsights-overview.md), qui est la principale fonctionnalité dans Azure Monitor pour surveiller des machines virtuelles. VM Insights fournit les fonctionnalités suivantes :

- Intégration simplifiée des agents pour pouvoir surveiller le système d’exploitation invité et les charges de travail d’une machine virtuelle. 
- Classeurs et graphiques de performances des tendances prédéfinis qui vous permettent d’analyser les principales métriques de performances du système d’exploitation invité de la machine virtuelle.
- Carte de dépendances qui affiche les processus en cours d’exécution sur chaque machine virtuelle, et les composants interconnectés avec d’autres machines et sources externes.

## <a name="agents"></a>Agents
Tout outil de surveillance comme Azure Monitor requiert qu’un agent soit installé sur une machine pour collecter des données à partir de son système d’exploitation invité. Azure Monitor possède actuellement plusieurs agents qui collectent des données, envoient ces données à différents emplacements et prennent en charge différentes fonctionnalités. VM insights gère le déploiement et la configuration des agents qui seront utilisés par la plupart des clients. Les différents agents sont décrits dans le tableau suivant au cas où vous auriez besoin des scénarios pris en charge. Pour une description détaillée et une comparaison des différents agents, consultez [Vue d’ensemble des agents Azure Monitor](../agents/agents-overview.md).

> [!NOTE]
> L’agent Azure Monitor remplace complètement l’agent Log Analytics, l’extension de diagnostic et l’agent Telegraf une fois qu’il a obtenu les fonctionnalités requises. Ces autres agents sont toujours requis pour des fonctionnalités comme les insights de machine virtuelle, Microsoft Defender pour le cloud et Microsoft Sentinel.

- [Agent Azure Monitor](../agents/agents-overview.md#azure-monitor-agent) : compatible avec les machines virtuelles dans Azure, dans d’autres environnements cloud et localement. Envoie des données, des métriques et des journaux Azure Monitor. Lorsqu’il prend entièrement en charge VM insights, Microsoft Defender pour le cloud et Microsoft Sentinel, il remplace complètement l’agent Log Analytics et l’extension de diagnostic.
- [Agent Log Analytics](../agents/agents-overview.md#log-analytics-agent) : compatible avec les machines virtuelles dans Azure, dans d’autres environnements cloud et localement. Envoie les données dans les journaux Azure Monitor. Prend en charge les solutions VM Insights et de surveillance. Il s’agit du même agent que celui utilisé pour System Center Operations Manager.
- [Dependency Agent](../agents/agents-overview.md#dependency-agent) : collecte les données des processus exécutés sur la machine virtuelle et leurs dépendances. S’appuie sur l’agent Log Analytics pour transmettre des données dans Azure, et prend en charge les solutions VM Insights, Service Map et Wire Data 2.0.
- [Extension de diagnostic Azure](../agents/agents-overview.md#azure-diagnostics-extension) : disponible pour les machines virtuelles Azure Monitor uniquement. Elle peut envoyer des données à Azure Event Hubs et au stockage Azure.

## <a name="next-steps"></a>Étapes suivantes

[Analyser les données de surveillance collectées pour les machines virtuelles](monitor-virtual-machine-analyze.md)
