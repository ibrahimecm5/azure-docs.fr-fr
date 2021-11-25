---
title: Surveiller les machines virtuelles avec Azure Monitor-configurer l’analyse
description: Découvrez comment configurer des machines virtuelles pour la surveillance dans Azure Monitor. Surveillez les machines virtuelles et leurs charges de travail à l’aide d’un scénario d’Azure Monitor.
ms.service: azure-monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/21/2021
ms.openlocfilehash: 6eb624fd132823afe25a91414c2a316f9dc7bab3
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132310335"
---
# <a name="monitor-virtual-machines-with-azure-monitor-configure-monitoring"></a>Surveiller les machines virtuelles avec Azure Monitor-configurer l’analyse
Cet article fait partie du scénario [Surveiller les machines virtuelles et leurs charges de travail dans Azure Monitor](monitor-virtual-machine.md). Il décrit comment configurer la surveillance de vos machines virtuelles Azure et hybrides dans Azure Monitor.

Cet article présente les fonctionnalités d’Azure Monitor les plus courantes pour surveiller l’ordinateur hôte de la machine virtuelle et son système d’exploitation invité. Selon votre environnement et vos besoins professionnels spécifiques, vous pouvez ne pas vouloir implémenter toutes les fonctionnalités activées par cette configuration. Chaque section décrit les fonctionnalités activées par cette configuration et indique si cela peut entraîner des coûts supplémentaires. Ces informations vous aideront à déterminer s’il faut exécuter chaque étape de la configuration. Pour obtenir des informations de tarification détaillées, consultez la page [Tarification Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).

Une description générale de chaque fonctionnalité activée par cette configuration est fournie dans la [vue d’ensemble du scénario](monitor-virtual-machine.md). Cet article inclut également des liens vers du contenu qui fournit une description détaillée de chaque fonctionnalité pour vous aider à évaluer vos besoins.

> [!NOTE]
> Les fonctionnalités activées par la configuration prennent en charge la surveillance des charges de travail en cours d’exécution sur votre machine virtuelle. Toutefois, en fonction de vos charges de travail spécifiques, vous aurez généralement besoin de configuration supplémentaire. Pour plus d’informations sur cette configuration supplémentaire, consultez [Surveillance de la charge de travail](monitor-virtual-machine-workloads.md).

## <a name="configuration-overview"></a>Présentation de la configuration
Le tableau suivant liste les étapes à appliquer pour cette configuration. Chacun d’entre eux est lié à la section avec la description détaillée de cette étape de configuration.

| Étape | Description |
|:---|:---|
| [Pas de configuration](#no-configuration) | Les métriques du journal d’activité et de la plateforme pour les ordinateurs hôtes de machines virtuelles Azure sont collectées automatiquement sans configuration. |
| [Créer et préparer un espace de travail Log Analytics](#create-and-prepare-a-log-analytics-workspace) | Créez un espace de travail Log Analytics et configurez-le pour les informations de machine virtuelle. En fonction de vos besoins spécifiques, vous pouvez configurer plusieurs espaces de travail. |
| [Envoyer le journal d’activité à un espace de travail Log Analytics](#send-an-activity-log-to-a-log-analytics-workspace) | Envoyez le journal d’activité à l’espace de travail pour l’analyser avec d’autres données de journal. |
| [Préparer des machines hybrides](#prepare-hybrid-machines) | Les machines hybrides nécessitent que les agents de serveur activés par Azure Arc soient installés, de sorte qu’ils puissent être gérés comme des machines virtuelles Azure ou que leurs agents doivent être installés manuellement. |
| [Activer VM Insights sur les machines](#enable-vm-insights-on-machines) | Intégrer des machines à des informations sur les machines virtuelles, ce qui permet de déployer des agents requis et de commencer à collecter des données à partir du système d’exploitation invité. |
| [Envoyer les données de performances de l’invité aux métriques](#send-guest-performance-data-to-metrics) |Installez l’agent Azure Monitor pour envoyer des données de performances aux métriques Azure Monitor. |

## <a name="no-configuration"></a>Pas de configuration
Azure Monitor fournit un niveau de base de surveillance pour les machines virtuelles Azure gratuitement et sans aucune configuration. Les métriques de plateforme pour les machines virtuelles Azure incluent des métriques importantes telles que l’utilisation du processeur, du réseau et du disque. Ils peuvent être consultés sur la [page Présentation](monitor-virtual-machine-analyze.md#single-machine-experience) de la machine dans le portail Azure. Le journal d’activité est également collecté automatiquement et comprend l’activité récente de la machine, notamment les modifications de configuration et le moment où il a été arrêté et démarré.

## <a name="create-and-prepare-a-log-analytics-workspace"></a>Créer et préparer un espace de travail Log Analytics
Vous avez besoin d’au moins un espace de travail Log Analytics pour prendre en charge VM insights et collecter les données de télémétrie auprès de l’agent Log Analytics. Il n’y a aucun coût pour l’espace de travail, mais vous encourez des coûts d’ingestion et de rétention lorsque vous collectez des données. Pour plus d’informations, consultez [Gérer l’utilisation et les coûts avec les journaux Azure Monitor](../logs/manage-cost-storage.md).

De nombreux environnements utilisent un espace de travail unique pour toutes leurs machines virtuelles et d’autres ressources Azure qu’ils surveillent. Vous pouvez même partager un espace de travail utilisé par [Microsoft Defender pour le cloud et Microsoft Sentinel](monitor-virtual-machine-security.md), bien que de nombreux clients choisissent de séparer leurs télémétries de disponibilité et de performances des données de sécurité. Si vous découvrez Azure Monitor, commencez avec un seul espace de travail et envisagez de créer des espaces de travail supplémentaires à métrique que vos besoins évoluent.

Voir [Conception de votre déploiement de journaux Azure Monitor](../logs/design-logs-deployment.md) pour plus de détails sur la logique que vous devez envisager lors de la conception d’une configuration d’espace de travail.

### <a name="multihoming-agents"></a>Agents à hébergement multiple
L’hébergement multiple fait référence à une machine virtuelle qui se connecte à plusieurs espaces de travail. En règle générale, il y a peu de raisons pour les agents multirésidents pour Azure Monitor seul. Le fait de faire en sorte qu’un agent envoie des données à plusieurs espaces de travail crée très probablement des données en double dans chaque espace de travail, ce qui augmente le coût global. Vous pouvez combiner les données de plusieurs espaces de travail à l'aide de [requêtes](../logs/cross-workspace-query.md) et de [classeurs inter-espaces de travail](../visualizations/../visualize/workbooks-overview.md).

Toutefois, vous pouvez envisager l’hébergement multiple si vous avez un environnement doté de Microsoft Defender pour le cloud ou Microsoft Sentinel stocké dans un espace de travail distinct d’Azure Monitor. Une machine surveillée par chaque service doit envoyer des données à chaque espace de travail. L’agent de Windows prend en charge ce scénario, car il peut envoyer jusqu’à quatre espaces de travail. L’agent Linux ne peut actuellement envoyer qu’à un seul espace de travail. Si vous souhaitez qu’Azure Monitor et Microsoft Defender pour le cloud ou Microsoft Sentinel surveille un ensemble commun de machines Linux, les services doivent partager le même espace de travail.

Si vous utilisez un [modèle d’analyse hybride](/azure/cloud-adoption-framework/manage/monitor/cloud-models-monitor-overview#hybrid-cloud-monitoring), vous pouvez également avoir recours au multihébergement de vos agents. Dans ce modèle, vous utilisez Azure Monitor et Operations Manager ensemble pour surveiller les mêmes machines. L’agent de Log Analytics et Microsoft Management Agent pour les Operations Manager sont le même agent. Parfois, ils sont référencés avec des noms différents.

### <a name="workspace-permissions"></a>Autorisations d’espace de travail
Le mode d’accès de l’espace de travail définit les utilisateurs qui peuvent accéder à différents jeux de données. Pour plus d’informations sur la définition du mode d’accès et la configuration des autorisations, consultez [Gérer l’accès aux données de journal et aux espaces de travail dans Azure Monitor](../logs/manage-access.md). Si vous venez de commencer à utiliser Azure Monitor, envisagez d’accepter les valeurs par défaut lorsque vous créez votre espace de travail et configurez ses autorisations ultérieurement.

### <a name="prepare-the-workspace-for-vm-insights"></a>Préparer l’espace de travail pour VM Insights
Préparez chaque espace de travail pour VM insights avant d’activer l’analyse pour les machines virtuelles. Cette étape installe les solutions requises qui prennent en charge la collecte de données à partir de l’agent de Log Analytics. Vous n’effectuez cette configuration qu’une seule fois pour chaque espace de travail. Pour plus d’informations sur cette configuration à l’aide du Portail Azure en plus d’autres méthodes, consultez [Activer la vue d’ensemble de VM insights](vminsights-enable-overview.md).

## <a name="send-an-activity-log-to-a-log-analytics-workspace"></a>Envoyer un journal d’activité à un espace de travail Log Analytics
Vous pouvez voir les métriques de plateforme et le journal d’activité collectés pour chaque hôte de machine virtuelle dans le portail Azure. Envoyez ces données dans le même espace de travail Log Analytics que celui de VM Insights afin de les analyser avec les autres données de surveillance collectées pour la machine virtuelle. Vous avez peut-être déjà effectué cette tâche lors de la configuration de l’analyse pour d’autres ressources Azure, car il existe un seul journal d’activité pour toutes les ressources dans un abonnement Azure.

Il n’y a aucun coût pour l’ingestion ou la conservation des données du journal d’activité. Pour plus d’informations sur la création d’un paramètre de diagnostic pour envoyer le journal d’activité à votre espace de travail Log Analytics, consultez [Créer des paramètres de diagnostic](../essentials/diagnostic-settings.md).

### <a name="network-requirements"></a>Conditions requises en matière de réseau
L’agent Log Analytics pour Linux et Windows communique en sortie avec le service Azure Monitor sur le port TCP 443. L’agent de dépendances utilise l’agent Log Analytics pour toutes les communications, de sorte qu’il ne nécessite pas d’autres ports. Pour plus d’informations sur la configuration de votre pare-feu et de votre proxy, consultez [Configuration réseau requise](../agents/log-analytics-agent.md#network-requirements).

:::image type="content" source="media/monitor-virtual-machines/network-diagram.png" alt-text="Diagramme montrant le réseau." lightbox="media/monitor-virtual-machines/network-diagram.png":::

### <a name="gateway"></a>Passerelle
Avec la passerelle Log Analytics, vous pouvez canaliser les communications à partir de vos machines locales via une passerelle unique. Toutefois, vous ne pouvez pas utiliser les agents de serveur Azure Arc avec la passerelle Log Analytics. Si votre stratégie de sécurité requiert une passerelle, vous devez installer manuellement les agents pour vos machines locales. Pour plus d’informations sur la configuration et l’utilisation de la passerelle Log Analytics, consultez [Passerelle Log Analytics](../agents/gateway.md).

### <a name="azure-private-link"></a>Azure Private Link
À l’aide d’Azure Private Link, vous pouvez créer un point de terminaison privé pour votre espace de travail Log Analytics. Une fois configurées, toutes les connexions à l’espace de travail doivent être effectuées via ce point de terminaison privé. Private Link fonctionne à l’aide de remplacements DNS. Il n’existe donc aucune exigence de configuration sur les agents individuels. Pour plus d’informations sur une liaison privée, consultez [Utiliser Azure Private Link pour connecter en toute sécurité des réseaux à Azure Monitor](../logs/private-link-security.md).

## <a name="prepare-hybrid-machines"></a>Préparer des machines hybrides
Une machine hybride est une machine qui n’est pas en cours d’exécution dans Azure. Il s’agit d’une machine virtuelle qui s’exécute dans un autre Cloud ou qui est hébergée ou d’une machine virtuelle ou physique exécutée localement dans votre centre de données. Utilisez des [serveurs Azure Arc](../../azure-arc/servers/overview.md) sur des machines hybrides pour pouvoir les gérer de la même façon que vos machines virtuelles Azure. Vous pouvez utiliser VM insights dans Azure Monitor pour utiliser le même processus pour activer l’analyse des serveurs Azure Arc, comme vous le feriez pour des machines virtuelles Azure. Pour obtenir un guide complet sur la préparation de vos machines hybrides pour Azure, consultez [Planifier et déployer des serveurs Azure Arc](../../azure-arc/servers/plan-at-scale-deployment.md). Cette tâche comprend l’activation de machines individuelles et l’utilisation de [Azure Policy](../../governance/policy/overview.md) pour activer l’ensemble de votre environnement hybride à l’échelle.

Il n’existe aucun coût supplémentaire pour les serveurs Azure Arc, mais il peut y avoir un coût pour les différentes options que vous activez. Pour plus d’informations, consultez [Tarification d’Azure Arc](https://azure.microsoft.com/pricing/details/azure-arc/). Il y a un coût pour les données collectées dans l’espace de travail une fois que les machines hybrides sont activées pour la machine virtuelle Insights.

### <a name="machines-that-cant-use-azure-arc-enabled-servers"></a>Machines qui ne peuvent pas utiliser de serveurs Azure Arc
Si vous avez des machines hybrides qui correspondent aux critères suivants, ils ne peuvent pas utiliser les serveurs Azure Arc :

- Le système d’exploitation de la machine n’est pas pris en charge par les agents de serveur activés par Azure Arc. Pour plus d’informations, consultez [Systèmes d’exploitation pris en charge](../../azure-arc/servers/agent-overview.md#prerequisites).
- Votre stratégie de sécurité ne permet pas aux machines de se connecter directement à Azure. L'agent Log Analytics peut utiliser la [passerelle Log Analytics](../agents/gateway.md), que des serveurs Azure Arc soient installés ou non. Les agents de serveur activés par Azure Arc doivent se connecter directement à Azure.

Vous pouvez toujours analyser ces machines avec Azure Monitor, mais vous devez installer manuellement leurs agents. Pour installer manuellement l’agent de Log Analytics et l’agent de dépendances sur ces machines hybrides, consultez [Activer les informations de machine virtuelle pour une machine virtuelle hybride](vminsights-enable-hybrid.md).

> [!NOTE]
> Le point de terminaison privé pour les serveurs Azure Arc est actuellement disponible en préversion publique. Le point de terminaison permet à vos machines hybrides de se connecter en toute sécurité à Azure à l’aide d’une adresse IP privée de votre réseau virtuel.

## <a name="enable-vm-insights-on-machines"></a>Activer VM Insights sur les machines
Une fois que vous avez activé VM insights sur une machine, il installe l’agent de Log Analytics et l’agent de dépendances, se connecte à un espace de travail et commence à collecter les données de performances. Vous pouvez commencer à utiliser des affichages et des classeurs de performances pour analyser les tendances d’un large éventail de métriques du système d’exploitation invité, activer la fonctionnalité de mappage de machines virtuelles pour analyser les processus en cours d’exécution et les dépendances entre les machines, et collecter les données requises pour créer diverses règles d’alerte.

Vous pouvez activer VM insights sur des machines individuelles en utilisant les mêmes méthodes pour les machines virtuelles Azure et les serveurs Azure Arc. Ces méthodes incluent l’intégration de machines individuelles avec les modèles Portail Azure ou Azure Resource Manager ou l’activation de machines à grande échelle à l’aide de Azure Policy. Il n’existe aucun coût direct pour les informations relatives aux machines virtuelles, mais il existe un coût pour l’ingestion et la rétention des données collectées dans l’espace de travail Log Analytics.

Pour connaître les différentes options permettant d’activer VM insights pour vos machines, consultez la page [Activer la vue d’ensemble de VM Insights](vminsights-enable-overview.md). Pour créer une stratégie qui active automatiquement les informations sur les machines virtuelles sur les nouvelles machines à mesure qu’ils sont créés, consultez [Activer VM insights à l’aide de Azure Policy](vminsights-enable-policy.md).


## <a name="send-guest-performance-data-to-metrics"></a>Envoyer les données de performances de l’invité aux métriques
[L’agent d’Azure Monitor](../agents/azure-monitor-agent-overview.md) remplace l’agent Log Analytics lorsqu’il prend entièrement en charge Azure Monitor, Microsoft Defender pour le cloud et Microsoft Sentinel. Jusqu’à ce moment-là, il peut être installé avec l’agent Log Analytics pour envoyer des données de performances du système d’exploitation invité des machines vers les métriques Azure Monitor. Cette configuration vous permet d’évaluer ces données avec Metrics Explorer et d’utiliser des alertes de métriques.

L’agent Azure Monitor nécessite au moins une règle de collecte de données (DCR) qui définit les données à collecter et l’emplacement où il doit envoyer ces données. Une seule DCR peut être utilisée par n’importe quelle machine du même groupe de ressources.

Créez une DCR unique pour chaque groupe de ressources avec des machines à surveiller à l’aide de la source de données suivante : 

- **Type de source de données** : compteurs de performance
- **Destination** : Azure Monitor Metrics

Veillez à ne pas envoyer de données aux journaux, car ils seraient redondants avec les données déjà collectées par l’agent de Log Analytics.

Vous pouvez installer un agent Azure Monitor sur des machines individuelles en utilisant les mêmes méthodes pour les machines virtuelles Azure et les serveurs Azure Arc. Ces méthodes incluent l’intégration de machines individuelles avec les modèles Portail Azure ou Resource Manager ou l’activation de machines à grande échelle à l’aide de Azure Policy. Pour les machines hybrides qui ne peuvent pas utiliser les serveurs Azure Arc, installez l’agent manuellement.

Pour créer un DCR et déployer l’agent Azure Monitor sur un ou plusieurs agents à l’aide du Portail Azure, consultez [Créer une règle et une association dans le portail Azure](../agents/data-collection-rule-azure-monitor-agent.md). D’autres méthodes d’installation sont décrites dans [Installer l’agent d’Azure Monitor](../agents/azure-monitor-agent-install.md). Pour créer une stratégie qui déploie automatiquement l’agent et DCR sur les nouvelles machines à mesure qu’elles sont créées, consultez [Déployer Azure Monitor à l’échelle à l’aide de Azure Policy](../best-practices.md).

## <a name="next-steps"></a>Étapes suivantes

* [Analyser les données d’analyse collectées pour les machines virtuelles](monitor-virtual-machine-analyze.md)
* [Créer des alertes à partir de données collectées](monitor-virtual-machine-alerts.md)
* [Superviser les charges de travail en cours d’exécution sur des machines virtuelles](monitor-virtual-machine-workloads.md)
