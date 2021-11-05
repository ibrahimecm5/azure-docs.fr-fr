---
title: Vue d’ensemble des agents de surveillance Azure | Microsoft Docs
description: Cet article fournit une présentation détaillée des agents Azure disponibles prenant en charge la surveillance de machines virtuelles Azure hébergées dans un environnement Azure ou hybride.
services: azure-monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/22/2021
ms.openlocfilehash: 7bd926b12a85b62b79f55be3afd3dbbd67af4a25
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131058423"
---
# <a name="overview-of-azure-monitor-agents"></a>Vue d’ensemble des agents Azure Monitor

Les machines virtuelles et autres ressources de calcul nécessitent qu’un agent collecte les données de supervision nécessaires pour mesurer les performances et la disponibilité de leur système d’exploitation invité et de leurs charges de travail. Cet article décrit les agents utilisés par Azure Monitor et vous aide à déterminer ceux dont vous avez besoin pour répondre aux exigences de votre environnement.

> [!NOTE]
> Azure Monitor récemment lancé un nouvel agent, l’agent de Azure Monitor, qui fournit toutes les fonctionnalités nécessaires pour collecter les données de surveillance du système d’exploitation invité. Bien qu’il existe plusieurs agents hérités qui existent en raison de la consolidation des Azure Monitor et des Log Analytics, chacun avec des fonctionnalités uniques avec un certain chevauchement, nous vous recommandons d’utiliser le nouvel agent qui vise à consolider les fonctionnalités de tous les agents existants et à fournir des avantages supplémentaires. [En savoir plus](./azure-monitor-agent-overview.md)

## <a name="summary-of-agents"></a>Résumé des agents

Les tableaux suivants permettent de comparer rapidement les agents Azure Monitor pour Windows et Linux. Pour plus d’informations sur chacun d’eux, reportez-vous à la section ci-après.

### <a name="windows-agents"></a>Agents Windows

| | Agent Azure Monitor | Diagnostics<br>extension (WAD) | Log Analytics<br>agent | Dépendance<br>agent |
|:---|:---|:---|:---|:---|
| **Environnements pris en charge** | Azure<br>Autre cloud (Azure Arc)<br>Local (Azure Arc)  | Azure | Azure<br>Autre cloud<br>Local | Azure<br>Autre cloud<br>Local | 
| **Exigences liées à l’agent**  | None | None | None | Requiert l’agent Log Analytics |
| **Données collectées** | Journaux des événements<br>Performances | Journaux des événements<br>Événements ETW<br>Performances<br>Journaux basés sur des fichiers<br>Journaux d’activité IIS<br>Journaux d’application .NET<br>Vidages sur incident<br>Journaux de diagnostics de l’agent | Journaux des événements<br>Performances<br>Journaux basés sur des fichiers<br>Journaux d’activité IIS<br>Insights et solutions<br>Autres services | Dépendances de processus<br>Métriques de connexion réseau |
| **Données envoyées à** | Journaux Azure Monitor<br>Métriques Azure Monitor<sup>1</sup> | Stockage Azure<br>Métriques Azure Monitor<br>Event Hub | Journaux Azure Monitor | Journaux Azure Monitor<br>(via l’agent Log Analytics) |
| **Services et**<br>**fonctionnalités**<br>**pris en charge** | Log Analytics<br>Metrics Explorer | Metrics Explorer | VM Insights<br>Log Analytics<br>Azure Automation<br>Azure Security Center<br>Azure Sentinel | VM Insights<br>Service Map |

### <a name="linux-agents"></a>Agents Linux

| | Agent Azure Monitor | Diagnostics<br>extension (LAD) | Telegraf<br>agent | Log Analytics<br>agent | Dépendance<br>agent |
|:---|:---|:---|:---|:---|:---|
| **Environnements pris en charge** | Azure<br>Autre cloud (Azure Arc)<br>Local (Azure Arc) | Azure | Azure<br>Autre cloud<br>Local | Azure<br>Autre cloud<br>Local | Azure<br>Autre cloud<br>Local |
| **Exigences liées à l’agent**  | None | None | None | None | Requiert l’agent Log Analytics |
| **Données collectées** | syslog<br>Performances | syslog<br>Performances | Performances | syslog<br>Performances| Dépendances de processus<br>Métriques de connexion réseau |
| **Données envoyées à** | Journaux Azure Monitor<br>Métriques Azure Monitor<sup>1</sup> | Stockage Azure<br>Event Hub | Métriques Azure Monitor | Journaux Azure Monitor | Journaux Azure Monitor<br>(via l’agent Log Analytics) |
| **Services et**<br>**fonctionnalités**<br>**pris en charge** | Log Analytics<br>Metrics Explorer | | Metrics Explorer | VM Insights<br>Log Analytics<br>Azure Automation<br>Azure Security Center<br>Azure Sentinel | VM Insights<br>Service Map |

<sup>1</sup>[Cliquez ici](../essentials/metrics-custom-overview.md#quotas-and-limits) pour passer en revue les autres limitations liées à l’utilisation d’Azure Monitor Metrics. Sur Linux, l’utilisation d’Azure Monitor Metrics comme seule destination est prise en charge dans la version v. 1.10.9.0 ou ultérieure. 

## <a name="azure-monitor-agent"></a>Agent Azure Monitor

L’agent [Azure Monitor](azure-monitor-agent-overview.md) est prévu pour remplacer l’agent Log Analytics, l’extension Azure Diagnostics et l’agent Telegraf pour les machines Windows et Linux. Il peut envoyer des données aux Journaux Azure Monitor et aux Métriques Azure Monitor, et utilise des [règles de collecte de données (DCR)](data-collection-rule-overview.md) qui offrent une méthode plus scalable pour la configuration de la collecte de données et des destinations pour chaque agent.

Utilisez l’agent Azure Monitor si vous avez besoin de faire ce qui suit :

- Collecter les journaux et les métriques d’invités sur n’importe quelle machine sur Azure, dans d’autres clouds ou en local. ([Serveurs avec Azure Arc](../../azure-arc/servers/overview.md) requis pour les machines extérieures à Azure.) 
- Gérer la configuration de la collecte de données de manière centralisée, à l’aide de [règles de collecte de données](./data-collection-rule-overview.md) et d’utilisation de modèles ou de stratégies Azure Resource Manager (ARM) pour la gestion globale
- Envoyer des données aux Journaux Azure Monitor et aux Métriques Azure Monitor (Préversion) à des fins d’analyse avec Azure Monitor. 
- Tirez parti du filtrage des événements Windows ou de l’hébergement multiple pour les journaux sur Windows et Linux
<!--- Send data to Azure Storage for archiving.
- Send data to third-party tools using [Azure Event Hubs](./diagnostics-extension-stream-event-hubs.md).
- Manage the security of your machines using [Azure Security Center](../../security-center/security-center-introduction.md)  or [Azure Sentinel](../../sentinel/overview.md). (Available in private preview.)
- Use [VM insights](../vm/vminsights-overview.md) which allows you to monitor your machines at scale and monitors their processes and dependencies on other resources and external processes..  
- Manage the security of your machines using [Azure Security Center](../../security-center/security-center-introduction.md)  or [Azure Sentinel](../../sentinel/overview.md).
- Use different [solutions](../monitor-reference.md#insights-and-curated-visualizations) to monitor a particular service or application. */
-->
Les limites de l’agent Azure Monitor sont les suivantes :
- Impossible d’utiliser les solutions de Log Analytics en production (disponibles uniquement en préversion, [consultez ce qui est pris en charge](./azure-monitor-agent-overview.md#supported-services-and-features)).
- Pas encore de prise en charge pour les scénarios de mise en réseau impliquant des liens privés. 
- Aucune prise en charge n’a encore collecté les journaux personnalisés (fichiers) ou les fichiers journaux IIS. 
- Pas encore de prise en charge desEvent Hubs et des comptes de stockage en tant que destinations.
- Pas de prise en charge des Runbook Workers hybrides.


## <a name="log-analytics-agent"></a>Agent Log Analytics

[L’agent Log Analytics](./log-analytics-agent.md) collecte des données de monitoring à partir du système d’exploitation invité et des charges de travail des machines virtuelles sur Azure, des machines virtuelles d’autres fournisseurs de cloud et des machines locales. Il envoie les données à un espace de travail Log Analytics. L’agent Log Analytics est le même agent que celui utilisé par System Center Operations Manager et les ordinateurs d’agent multirésidents peuvent communiquer avec votre groupe d’administration et Azure Monitor simultanément. Cet agent est également nécessaire pour certains insights dans Azure Monitor et d’autres services dans Azure.

> [!NOTE]
> L’agent Log Analytics pour Windows est souvent appelé Microsoft Monitoring Agent (MMA). L’agent Log Analytics pour Linux est souvent appelé agent OMS.

Utilisez l’agent Log Analytics si vous avez besoin d’effectuer les opérations suivantes :

* Collecter des journaux et des données de performances à partir de machines virtuelles Azure ou de machines hybrides hébergées en dehors d’Azure.
* Envoyer les données à un espace de travail Log Analytics pour tirer parti des fonctionnalités prises en charge par les [journaux Azure Monitor](../logs/data-platform-logs.md) comme les [requêtes de journal](../logs/log-query-overview.md).
* Utilisez [VM Insights](../vm/vminsights-overview.md), qui vous permet de superviser vos machines à grande échelle, y compris leurs processus et dépendances vis-à-vis d’autres ressources et de processus externes.  
* Gérer la sécurité des machines avec [Azure Security Center](../../security-center/security-center-introduction.md) ou [Azure Sentinel](../../sentinel/overview.md).
* Utiliser [Azure Automation Update Management](../../automation/update-management/overview.md), [Azure Automation State Configuration](../../automation/automation-dsc-overview.md) ou le [Suivi des modifications et inventaire Azure Automation](../../automation/change-tracking/overview.md) pour assurer une gestion complète de vos machines Azure et autres.
* Utiliser des [solutions](../monitor-reference.md#insights-and-curated-visualizations) différentes pour superviser un service ou une application spécifique.

Les limitations de l’agent Log Analytics sont les suivantes :

- Impossible d’envoyer des données aux métriques Azure Monitor, à Stockage Azure ou à Azure Event Hubs.
- Difficile de configurer des définitions de supervision uniques pour chaque agent.
- Difficile de gérer à grande échelle dans la mesure où chaque machine virtuelle possède une configuration unique.

## <a name="azure-diagnostics-extension"></a>Extension Diagnostics Azure

L’[extension Diagnostics Azure](./diagnostics-extension-overview.md) collecte des données de supervision à partir du système d’exploitation invité et des charges de travail des machines virtuelles Azure et autres ressources de calcul. Elle collecte principalement les données dans le Stockage Azure, mais vous permet également de définir des récepteurs de données pour envoyer les données à d’autres destinations, telles que des métriques Azure Monitor et Azure Event Hubs.

Utilisez l’extension Diagnostics Azure si vous devez effectuer les opérations suivantes :

- Envoyer les données au Stockage Azure à des fins d’archivage ou pour les analyser à l’aide d’outils tels que l’[Explorateur Stockage Azure](../../vs-azure-tools-storage-manage-with-storage-explorer.md).
- Envoyer les données aux [métriques Azure Monitor](../essentials/data-platform-metrics.md) pour les analyser avec [Metrics Explorer](../essentials/metrics-getting-started.md) et tirer parti des fonctionnalités telles que les [alertes de métrique](../alerts/alerts-metric-overview.md) en temps quasi-réel et la [mise à l’échelle automatique](../autoscale/autoscale-overview.md) (Windows uniquement).
- Envoyer les données à des outils tiers à l’aide d’[Azure Event Hubs](./diagnostics-extension-stream-event-hubs.md).
- Collecter des [diagnostics de démarrage](/troubleshoot/azure/virtual-machines/boot-diagnostics) afin d’étudier les problèmes de démarrage de machines virtuelles.

Les limitations de l’extension Diagnostics Azure sont les suivantes :

- Peut uniquement être utilisée avec des ressources Azure.
- Capacité limitée à envoyer des données aux journaux Azure Monitor.

## <a name="telegraf-agent"></a>Agent Telegraf

L’[agent InfluxData Telegraf](../essentials/collect-custom-metrics-linux-telegraf.md) est utilisé pour collecter les données de performances des ordinateurs Linux sur les métriques Azure Monitor.

Utilisez l’agent Telegraf si vous avez besoin d’effectuer l’opération suivante :

* Envoyer les données aux [métriques Azure Monitor](../essentials/data-platform-metrics.md) pour les analyser avec [Metrics Explorer](../essentials/metrics-getting-started.md) et tirer parti des fonctionnalités telles que les [alertes de métrique](../alerts/alerts-metric-overview.md) en temps quasi-réel et la [mise à l’échelle automatique](../autoscale/autoscale-overview.md) (Linux uniquement).

## <a name="dependency-agent"></a>Agent de dépendances

L’agent de dépendances collecte les données découvertes sur les processus exécutés sur la machine virtuelle et les dépendances vis-à-vis de processus externes. 

Utilisez l’agent de dépendances si vous avez besoin d’effectuer l’opération suivante :

* Utilisez la fonctionnalité de carte [VM Insights](../vm/vminsights-overview.md) ou la solution [Service Map](../vm/service-map.md).

Tenez compte des éléments suivants lors de l’utilisation de l’agent de dépendances :

- L’agent Log Analytics doit être installé sur la même machine que l’agent de dépendances.
- Sur les ordinateurs Linux, l’agent Log Analytics doit être installé avant l’extension Diagnostics Azure.
- Sur les versions Windows et Linux du Dependency Agent, la collecte de données s’effectue à l’aide d’un service d’espace utilisateur et d’un pilote de noyau. 

## <a name="virtual-machine-extensions"></a>Extensions de machine virtuelle

L'[agent Azure Monitor](./azure-monitor-agent-install.md#virtual-machine-extension-details) est uniquement disponible en tant qu'extension de machine virtuelle. L’extension Log Analytics pour [Windows](../../virtual-machines/extensions/oms-windows.md) et [Linux](../../virtual-machines/extensions/oms-linux.md) installe l’agent Log Analytics sur les machines virtuelles Azure. L’extension Azure Monitor Dependency pour [Windows](../../virtual-machines/extensions/agent-dependency-windows.md) et [Linux](../../virtual-machines/extensions/agent-dependency-linux.md) installe l’agent de dépendances sur les machines virtuelles Azure. Il s’agit des mêmes agents que ceux décrits ci-dessus, mais vous pouvez les gérer par le biais d’[extensions de machine virtuelle](../../virtual-machines/extensions/overview.md). Dans la mesure du possible, utilisez des extensions pour installer et gérer les agents.

Sur les machines hybrides, utilisez des [serveurs avec Azure Arc](../../azure-arc/servers/manage-vm-extensions.md) pour déployer l’agent Azure Monitor, les extensions de machines virtuelles Log Analytics et Azure Monitor Dependency.

## <a name="supported-operating-systems"></a>Systèmes d’exploitation pris en charge

Les tableaux suivants listent les systèmes d’exploitation pris en charge par les agents Azure Monitor. Consultez la documentation de chaque agent pour en connaître les spécificités et le processus d’installation. Consultez la documentation Telegraf pour connaître les systèmes d’exploitation pris en charge. Tous les systèmes d’exploitation sont censés être x64. L’architecture x86 n’est prise en charge pour aucun système d’exploitation.

### <a name="windows"></a>Windows

| Système d’exploitation | Agent Azure Monitor | Agent Log Analytics | Agent de dépendances | Extension Diagnostics | 
|:---|:---:|:---:|:---:|:---:|
| Windows Server 2022                                      | X |   |   |   |
| Windows Server 2019                                      | X | X | X | X |
| Windows Server 2019 Core                                 | X |   |   |   |
| Windows Server 2016                                      | X | X | X | X |
| Windows Server 2016 Core                                 | X |   |   | X |
| Windows Server 2012 R2                                   | X | X | X | X |
| Windows Server 2012                                      | X | X | X | X |
| Windows Server 2008 R2 SP1                               | X | X | X | X |
| Windows Server 2008 R2                                   |   |   | X | X |
| Windows Server 2008 SP2                                   |   | X |  |  |
| Windows 10 Entreprise<br>(multisession inclus) et Pro<br>(Scénarios de serveur uniquement<sup>1</sup>)  | X | X | X | X |
| Windows 8 Entreprise et Professionnel<br>(Scénarios de serveur uniquement<sup>1</sup>)  |   | X | X |   |
| Windows 7 SP1<br>(Scénarios de serveur uniquement<sup>1</sup>)                 |   | X | X |   |
| Azure Stack HCI                                          |   | X |   |   |

<sup>1</sup> Exécuter le système d’exploitation sur le matériel du serveur, c’est-à-dire les machines toujours connectées, toujours sous tension et sans exécuter d’autres charges de travail (PC, bureau, navigateur, etc.)

### <a name="linux"></a>Linux

| Système d’exploitation | Agent Azure Monitor<sup>1</sup> | Agent Log Analytics<sup>1</sup> | Agent de dépendances | Extension Diagnostics<sup>2</sup>| 
|:---|:---:|:---:|:---:|:---:
| Amazon Linux 2017.09                                        |   | X |   |   |
| Amazon Linux 2                                              |   | X |   |   |
| CentOS Linux 8                                              | X<sup>3</sup> | X | X |   |
| CentOS Linux 7                                              | X | X | X | X |
| CentOS Linux 6                                              |   | X |   |   |
| CentOS Linux 6.5+                                           |   | X | X | X |
| Debian 10 <sup>1</sup>                                      | X |   |   |   |
| Debian 9                                                    | X | X | x | X |
| Debian 8                                                    |   | X | X |   |
| Debian 7                                                    |   |   |   | X |
| OpenSUSE 13.1+                                              |   |   |   | X |
| Oracle Linux 8                                              | X<sup>3</sup> | X |   |   |
| Oracle Linux 7                                              | X | X |   | X |
| Oracle Linux 6                                              |   | X |   |   |
| Oracle Linux 6.4+                                           |   | X |   | X |
| Red Hat Enterprise Linux Server 8.1, 8.2, 8.3, 8.4          | X<sup>3</sup> | X | X |   |
| Red Hat Enterprise Linux Server 8                           | X<sup>3</sup> | X | X |   |
| Red Hat Enterprise Linux Server 7                           | X | X | X | X |
| Red Hat Enterprise Linux Server 6                           |   | X | X |   |
| Red Hat Enterprise Linux Server 6.7+                        |   | X | X | X |
| SUSE Linux Enterprise Server 15.2                           | X<sup>3</sup> |   |   |   |
| SUSE Linux Enterprise Server 15.1                           | X<sup>3</sup> | X |   |   |
| SUSE Linux Enterprise Server 15 SP1                         | X | X | X |   |
| SUSE Linux Enterprise Server 15                             | X | X | X |   |
| SUSE Linux Enterprise Server 12 SP5                         | X | X | X | X |
| SUSE Linux Enterprise Server 12                             | X | X | X | X |
| Ubuntu 20.04 LTS                                            | X | X | X | X |
| Ubuntu 18.04 LTS                                            | X | X | X | X |
| Ubuntu 16.04 LTS                                            | X | X | X | X |
| Ubuntu 14.04 LTS                                            |   | X |   | X |

<sup>1</sup> Exige l’installation de Python (2 or 3) sur la machine.

<sup>3</sup> Problème connu de collecte des événements Syslog dans les versions antérieures à 1.9.0
#### <a name="dependency-agent-linux-kernel-support"></a>Prise en charge du noyau Linux par Dependency Agent

Sachant que Dependency Agent fonctionne au niveau du noyau, la prise en charge dépend aussi de la version du noyau. Le tableau suivant liste les versions majeures et mineures du système d’exploitation Linux ainsi que les versions de noyau prises en charge pour Dependency Agent.

| Distribution | Version du SE | Version du noyau |
|:---|:---|:---|
|  Red Hat Linux 8   | 8,4     | 4.18.0-305.\*el8.x86_64, 4.18.0-305.\*el8_4.x86_64 |
|                    | 8.3     |  4.18.0-240.\*el8_3.x86_64 |
|                    | 8,2     | 4.18.0-193.\*el8_2.x86_64 |
|                    | 8.1     | 4.18.0-147.\*el8_1.x86_64 |
|                    | 8.0     | 4.18.0-80.\*el8.x86_64<br>4.18.0-80.\*el8_0.x86_64 |
|  Red Hat Linux 7   | 7.9     | 3.10.0-1160 |
|                    | 7.8     | 3.10.0-1136 |
|                    | 7,7     | 3.10.0-1062 |
|                    | 7.6     | 3.10.0-957  |
|                    | 7.5     | 3.10.0-862  |
|                    | 7.4     | 3.10.0-693  |
| Red Hat Linux 6    | 6.10    | 2.6.32-754 |
|                    | 6.9     | 2.6.32-696  |
| CentOS Linux 8     | 8,4     | 4.18.0-305.\*el8.x86_64, 4.18.0-305.\*el8_4.x86_64 |
|                    | 8.3     | 4.18.0-240.\*el8_3.x86_64 |
|                    | 8,2     | 4.18.0-193.\*el8_2.x86_64 |
|                    | 8.1     | 4.18.0-147.\*el8_1.x86_64 |
|                    | 8.0     | 4.18.0-80.\*el8.x86_64<br>4.18.0-80.\*el8_0.x86_64 |
| CentOS Linux 7     | 7.9     | 3.10.0-1160 |
|                    | 7.8     | 3.10.0-1136 |
|                    | 7,7     | 3.10.0-1062 |
| CentOS Linux 6     | 6.10    | 2.6.32-754.3.5<br>2.6.32-696.30.1 |
|                    | 6.9     | 2.6.32-696.30.1<br>2.6.32-696.18.7 |
| Serveur Ubuntu      | 20.04   | 5.4\* |
|                    | 18,04   | 5.3.0-1020<br>5.0 (inclut un noyau optimisé pour Azure)<br>4.18 *<br>4.15* |
|                    | 16.04.3 | 4.15.\* |
|                    | 16.04   | 4.13.\*<br>4.11.\*<br>4.10.\*<br>4.8.\*<br>4.4.\* |
| SUSE Linux 12 Enterprise Server | 12 SP5     | 4.12.14-122.\*-default, 4.12.14-16.\*-azure|
|                                 | 12 SP4 | 4.12.\* (inclut un noyau optimisé pour Azure) |
|                                 | 12 SP3 | 4.4.\* |
|                                 | 12 SP2 | 4.4.\* |
| SUSE Linux 15 Enterprise Server | 15 SP1 | 4.12.14-197.\*-default, 4.12.14-8.\*-azure |
|                                 | 15     | 4.12.14-150.\*-default |
| Debian                          | 9      | 4,9  | 

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur chacun des agents, consultez les articles suivants :

- [Vue d’ensemble de l’agent Azure Monitor](./azure-monitor-agent-overview.md)
- [Vue d’ensemble de l’agent Log Analytics](./log-analytics-agent.md)
- [Vue d’ensemble de l’extension Diagnostics Azure](./diagnostics-extension-overview.md)
- [Collecter des métriques personnalisées pour une machine virtuelle Linux avec l’agent InfluxData Telegraf](../essentials/collect-custom-metrics-linux-telegraf.md)
