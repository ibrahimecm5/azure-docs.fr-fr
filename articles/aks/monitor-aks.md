---
title: Superviser Azure Kubernetes Service (AKS) avec Azure Monitor
description: Décrit comment utiliser Azure Monitor pour surveiller l’intégrité et les performances des clusters AKS et de leurs charges de travail.
ms.service: azure-monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/29/2021
ms.openlocfilehash: 28bf92ffeb97c65aadae3f61cf471efba943b5b7
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132491959"
---
# <a name="monitoring-azure-kubernetes-service-aks-with-azure-monitor"></a>Surveillance d’Azure Kubernetes Service (AKS) avec Azure Monitor
Ce scénario décrit comment utiliser Azure Monitor pour surveiller l’intégrité et les performances d’Azure Kubernetes Service (AKS). Il comprend la collecte des données de télémétrie critiques pour la surveillance, l’analyse et la visualisation des données collectées pour identifier les tendances, et comment configurer des alertes pour être informé de manière proactive des problèmes critiques.

Le [Guide de surveillance du cloud](/azure/cloud-adoption-framework/manage/monitor/) définit les [objectifs de surveillance principaux](/azure/cloud-adoption-framework/strategy/monitoring-strategy#formulate-monitoring-requirements) sur lesquels vous devez vous concentrer pour vos ressources Azure. Ce scénario se concentre sur l’analyse de l’intégrité et de l’état à l’aide d’Azure Monitor.

## <a name="scope-of-the-scenario"></a>Portée du scénario
Ce scénario est destiné aux clients qui utilisent Azure Monitor pour surveiller AKS. Il n’inclut pas les éléments suivants, même si ce contenu pourrait être ajouté dans des mises à jour ultérieures du scénario.

- Surveillance des clusters Kubernetes en dehors d’Azure, à l’exception de la référence au contenu existant pour Kubernetes avec Azure Arc. 
- La surveillance d’AKS avec des outils autres qu’Azure Monitor, sauf pour combler les lacunes d’Azure Monitor et de Container Insights.

> [!NOTE]
> Azure Monitor a été conçu pour surveiller la disponibilité et les performances des ressources cloud. Bien que les données opérationnelles stockées dans Azure Monitor peuvent être utiles pour l’examen des incidents de sécurité, d’autres services dans Azure ont été conçus pour surveiller la sécurité. La surveillance de la sécurité pour AKS s’effectue avec [Microsoft Sentinel](../sentinel/overview.md) et [Microsoft Defender pour le cloud](../defender-for-cloud/defender-for-cloud-introduction.md). Voir [Surveillance des machines virtuelles avec Azure Monitor - Surveillance de la sécurité](../azure-monitor/vm/monitor-virtual-machine-security.md) pour une description des outils de surveillance de la sécurité dans Azure et de leur relation avec Azure Monitor.
>
> Pour plus d’informations sur l’utilisation des services de sécurité pour surveiller AKS, voir [Microsoft Defender pour Kubernetes : avantages et fonctionnalités](../defender-for-cloud/defender-for-kubernetes-introduction.md) et [Connecter des journaux de diagnostic AKS (Azure Kubernetes Service) à Microsoft Sentinel](../sentinel/data-connectors-reference.md#azure-kubernetes-service-aks).
## <a name="container-insights"></a>Container Insights
AKS génère des [métriques de plateforme et des journaux de ressources](monitor-aks-reference.md), comme toute autre ressource Azure, que vous pouvez utiliser pour surveiller son intégrité et ses performances de base. Activez [Container Insights](../azure-monitor/containers/container-insights-overview.md) pour développer cette surveillance. Container Insights est une fonctionnalité d’Azure Monitor qui surveille l’intégrité et les performances des clusters Kubernetes gérés hébergés sur AKS, en plus d’autres configurations de cluster. Container Insights fournit des affichages et des classeurs interactifs qui analysent les données collectées pour un large éventail de scénarios d’analyse. 

[Prometheus](https://prometheus.io/) et [Grafana](https://www.prometheus.io/docs/visualization/grafana/) sont des outils open source très populaires soutenus par CNCF pour la surveillance de Kubernetes. AKS expose de nombreuses métriques au format Prometheus, ce qui fait de Prometheus un choix populaire pour la surveillance. [Container insights](../azure-monitor/containers/container-insights-overview.md) offre une intégration native avec AKS, collectant des métriques et des journaux critiques, alertant sur les problèmes identifiés et fournissant une visualisation avec des classeurs. Il collecte également certaines métriques de Prometheus, et de nombreux insights natifs d’Azure Monitor sont intégrés en plus des métriques Prometheus. Container Insights complète la surveillance E2E d’AKS, y compris la collecte de journaux, que Prometheus ne fournit pas en tant qu’outil autonome. De nombreux clients utilisent l’intégration Prometheus et Azure Monitor ensemble pour la surveillance E2E.

En savoir plus sur l’utilisation de Container Insights sur [Présentation de Container Insights](../azure-monitor/containers/container-insights-overview.md). [Surveiller les couches d’AKS avec Container Insights](#monitor-layers-of-aks-with-container-insights) présente les différentes fonctionnalités de Container Insights et les scénarios de surveillance qu’elles prennent en charge.

:::image type="content" source="media/monitor-aks/container-insights.png" alt-text="Container Insights" lightbox="media/monitor-aks/container-insights.png":::


## <a name="configure-monitoring"></a>Configuration de l’analyse
Les sections suivantes décrivent les étapes nécessaires à la configuration de la surveillance complète de votre cluster AKS à l’aide d’Azure Monitor.
### <a name="create-log-analytics-workspace"></a>Créer un espace de travail Log Analytics
Vous avez besoin d’au moins un espace de travail Log Analytics pour prendre en charge Container Insights et collecter et analyser d’autres données de télémétrie sur votre cluster AKS. Il n’y a aucun coût pour l’espace de travail, mais vous encourez des coûts d’ingestion et de rétention lorsque vous collectez des données. Pour plus d'informations, consultez [Gérer l’utilisation et les coûts avec les journaux Azure Monitor](../azure-monitor/logs/manage-cost-storage.md).

Si vous découvrez Azure Monitor, commencez avec un seul espace de travail et envisagez de créer des espaces de travail supplémentaires à métrique que vos besoins évoluent. De nombreux environnements utilisent un espace de travail unique pour toutes les ressources Azure qu’ils contrôlent. Vous pouvez même partager un espace de travail utilisé par [Microsoft Defender pour le cloud et Microsoft Sentinel](../azure-monitor/vm/monitor-virtual-machine-security.md), bien que de nombreux clients choisissent de séparer leurs télémétries de disponibilité et de performances des données de sécurité. 

Voir [Conception de votre déploiement de journaux Azure Monitor](../azure-monitor/logs/design-logs-deployment.md) pour plus de détails sur la logique que vous devez envisager lors de la conception d’une configuration d’espace de travail.

### <a name="enable-container-insights"></a>Activer Container Insights
Lorsque vous activez Container Insights pour votre cluster AKS, il déploie une version conteneurisée de l’agent [Log Analytics](../agents/../azure-monitor/agents/log-analytics-agent.md) qui envoie des données à Azure Monitor. Il existe plusieurs méthodes pour l’activer, selon que vous utilisez un cluster AKS nouveau ou existant. Voir [Activer Container Insights](../azure-monitor/containers/container-insights-onboard.md) pour les prérequis et les options de configuration.


### <a name="configure-collection-from-prometheus"></a>Configurer la collection à partir de Prometheus
Container Insights vous permet de collecter certaines métriques Prometheus dans votre espace de travail Log Analytics sans nécessiter de serveur Prometheus. Vous pouvez analyser ces données à l’aide des fonctionnalités d’Azure Monitor, ainsi que d’autres données collectées par Container Insights. Voir [Configurer la capture des métriques de Prometheus avec Container Insights](../azure-monitor/containers/container-insights-prometheus-integration.md) pour plus de détails sur cette configuration.


### <a name="collect-resource-logs"></a>Recueillir les journaux de ressources
Les journaux pour les composants du plan de contrôle AKS sont implémentés dans Azure en tant que [journaux de ressources](../azure-monitor/essentials/resource-logs.md). Container Insights n’utilise pas ces journaux pour le moment. Vous devez donc créer vos propres requêtes de journal pour les afficher et les analyser. Voir [Comment interroger les journaux depuis Container Insights](../azure-monitor/containers/container-insights-log-query.md#resource-logs) pour plus de détails sur la structure de ces journaux et comment écrire des requêtes pour eux.

Vous devez créer un paramètre de diagnostic pour collecter des journaux de ressources. Créez plusieurs paramètres de diagnostic pour envoyer différents jeux de journaux à différents emplacements. Pour plus d’informations sur la création de paramètre de diagnostics pour votre cluster AKS, consultez [Créer des paramètres de diagnostic pour envoyer des journaux et des métriques de plateforme à différentes destinations](../azure-monitor/essentials/diagnostic-settings.md). 

Il y a un coût pour l’envoi de journaux de ressources à un espace de travail. Par conséquent, vous ne devez collecter que les catégories de journaux que vous avez l’intention d’utiliser. Envoyez les journaux à un compte de stockage Azure pour réduire les coûts si vous devez conserver les informations, mais n’avez pas nécessaire besoin qu’ils soient facilement disponibles pour analyse.  Voir [Journaux de ressources](monitor-aks-reference.md#resource-logs) pour une description des catégories disponibles pour AKS et [Gérer l’utilisation et les coûts avec les journaux Azure Monitor](../azure-monitor/logs/manage-cost-storage.md) pour des détails sur le coût de l’ingestion et de conservation des données des journaux. Commencez par collecter un nombre minimal de catégories, puis modifiez le paramètre de diagnostic pour collecter des catégories supplémentaires à métrique que vos besoins augmentent et que vous comprenez vos coûts associés.

Si vous n’êtes pas sûr des journaux de ressources à activer initialement, utilisez les recommandations du tableau suivant qui sont basées sur les exigences client les plus courantes. Activez les autres catégories si vous réalisez que vous avez besoin de ces informations.

| Category | Activer ? | Destination |
|:---|:---|:---|
| cluster-autoscaler      | Activer si la mise à l’échelle automatique est activée | Espace de travail Log Analytics |
| guard                   | Activer si Azure Active Directory est activé | Espace de travail Log Analytics |
| kube-apiserver          | Activer | Espace de travail Log Analytics |
| kube-audit              | Activer | stockage Azure. Cela permet de réduire les coûts au minimum tout en conservant les journaux d’audit s’ils sont requis par un auditeur. |
| kube-audit-admin        | Activer | Espace de travail Log Analytics |
| kube-controller-manager | Activer | Espace de travail Log Analytics |
| kube-scheduler          | Désactiver | |
| AllMetrics              | Activer | Espace de travail Log Analytics |





## <a name="access-azure-monitor-features"></a>Accéder aux fonctionnalités d’Azure Monitor

Accédez aux fonctionnalités d’Azure Monitor pour tous les clusters AKS de votre abonnement à partir du menu **Surveillance** du portail Azure ou pour un seul cluster AKS à partir de la section **Surveillance** du menu **Services Kubernetes**. La capture d’écran ci-dessous affiche le menu **Surveillance** du cluster.

:::image type="content" source="media/monitor-aks/monitoring-menu.png" alt-text="Menu Supervision AKS" lightbox="media/monitor-aks/monitoring-menu.png":::

| Option de menu | Description |
|:---|:---|
| Insights | Ouvre Container Insights pour le cluster en cours. Sélectionnez **Conteneurs** dans le menu **Surveillance** pour ouvrir Container Insights pour tous les clusters.  |
| Alertes | Vues d’alerte pour le cluster actuel. |
| Mesures | Ouvrez Metrics Explorer avec l’étendue définie sur le cluster actuel. |
| Paramètres de diagnostic | Créez des paramètres de diagnostic pour le cluster afin de collecter les journaux des ressources. |
| Advisor | Recommandations pour le cluster actuel d’Azure Advisor. |
| Journaux d’activité | Ouvrez Log Analytics avec l’étendue définie sur le cluster actif pour analyser les données de journal et accéder aux requêtes prégénérées. |
| Workbooks | Ouvrez la Galerie de classeurs pour Kubernetes Service. |




## <a name="monitor-layers-of-aks-with-container-insights"></a>Surveiller les couches d’AKS avec Container Insights
En raison de la variance importante dans les implémentations de Kubernetes, chaque client aura des exigences uniques pour la surveillance AKS. L’approche que vous adoptez doit être basée sur des facteurs comme la mise à l’échelle, la topologie, les rôles organisationnels et la location de plusieurs clusters. Cette section présente une stratégie commune qui est une approche ascendante allant de l’infrastructure aux applications. Chaque couche a des exigences de surveillance distinctes. Ces couches sont illustrées dans le diagramme suivant et abordées plus en détail dans les sections suivantes.

:::image type="content" source="media/monitor-aks/layers.png" alt-text="Couches AKS"  border="false":::

### <a name="level-1---cluster-level-components"></a>Niveau 1 - Composants au niveau du cluster
Le niveau de cluster inclut les composants suivants.

| Composant | Exigences de supervision |
|:---|:---|
| Nœud |  Comprenez l’état de préparation et les performances UC, mémoire et disque pour chaque nœud et surveillez de manière proactive leurs tendances d’utilisation avant de déployer des charges de travail. |


Utilisez les vues et rapports existants dans Container Insights pour surveiller les composants de niveau cluster. La vue **Cluster** vous donne un aperçu rapide de la performance des nœuds de votre cluster, y compris leur utilisation de l’UC et de la mémoire. Utilisez la vue **Nœuds** pour voir l’intégrité de chaque nœud en plus de l’intégrité et des performances des pods exécutés sur chacun. Voir [Superviser les performances de votre cluster Kubernetes avec Container Insights](../azure-monitor/containers/container-insights-analyze.md) pour plus de détails sur l’utilisation de cette vue et d’analyser l’intégrité et les performances du nœud.

:::image type="content" source="media/monitor-aks/container-insights-cluster-view.png" alt-text="Vue de cluster de Container Insights" lightbox="media/monitor-aks/container-insights-cluster-view.png":::

Utilisez des classeurs **Node** dans Container Insights pour analyser la capacité du disque et les E/S en plus de l’utilisation du GPU. Voir [Classeurs de nœuds](../azure-monitor/containers/container-insights-reports.md#node-workbooks) pour une description de ces classeurs.

:::image type="content" source="media/monitor-aks/container-insights-node-workbooks.png" alt-text="Classeurs de nœud Container Insights" lightbox="media/monitor-aks/container-insights-node-workbooks.png":::


Pour les scénarios de résolution des problèmes, vous devrez peut-être accéder directement aux nœuds AKS pour la maintenance ou la collecte immédiate des journaux. À des fins de sécurité, les nœuds AKS ne sont pas exposés à Internet, mais vous pouvez utiliser `kubectl debug` pour accéder par SSH aux nœuds AKS. Consultez [Se connecter avec SSH à des nœuds de cluster AKS (Azure Kubernetes Service) pour effectuer des tâches de maintenance ou de dépannage](ssh.md) pour plus de détails sur ce processus.



### <a name="level-2---managed-aks-components"></a>Niveau 2 - Composants AKS gérés
Le niveau AKS géré inclut les composants suivants.

| Composant | Surveillance |
|:---|:---|
| Serveur d’API | Surveillez l’état du serveur d’API, en identifiant toute augmentation de la charge de la requête et les goulots d’étranglement si le service est défaillant. |
| Kubelet | La surveillance de Kubelet vous aide à résoudre les problèmes de gestion des pods, les pods non démarrés, les nœuds non prêts ou les pods interrompus de force.  |

Azure Monitor et Container Insights ne fournissent pas encore de surveillance complète pour le serveur d’API. Vous pouvez utiliser Metrics Explorer pour afficher le compteur **Requêtes en cours**, mais vous devez vous reporter aux métriques de Prometheus pour une vue complète des performances du serveur d’API. Cela comprend des valeurs comme la latence des requêtes et le temps de traitement des files d’attente de travaux. Un tableau de bord Grafana qui fournit des vues des métriques critiques pour le serveur d’API est disponible sur [Grafana Labs](https://grafana.com/grafana/dashboards/12006). Utilisez ce tableau de bord sur votre serveur Grafana existant ou configurez un nouveau serveur Grafana dans Azure en utilisant [Surveiller vos services Azure dans Grafana](../azure-monitor/visualize/grafana-plugin.md)

:::image type="content" source="media/monitor-aks/grafana-api-server.png" alt-text="Serveur d’API Grafana" lightbox="media/monitor-aks/grafana-api-server.png":::

Utilisez le classeur **Kubelet** pour afficher l’intégrité et les performances de chaque kubelet. Voir [Classeurs de surveillance des ressources](../azure-monitor/containers/container-insights-reports.md#resource-monitoring-workbooks) pour plus de détails sur ce classeur. Pour les scénarios de dépannage, vous pouvez accéder aux journaux de Kubelet à l’aide du processus décrit dans [Accéder aux journaux d’activité kubelet à partir de nœuds de cluster Azure Kubernetes Service (AKS)](kubelet-logs.md).

:::image type="content" source="media/monitor-aks/container-insights-kubelet-workbook.png" alt-text="Classeur kubelet Container Insights" lightbox="media/monitor-aks/container-insights-kubelet-workbook.png":::

### <a name="resource-logs"></a>Journaux d’activité de ressources
Utilisez les [requêtes de journal avec les journaux de ressources ](../azure-monitor/containers/container-insights-log-query.md#resource-logs) pour analyser les journaux de plan de contrôle générés par les composants AKS. 

### <a name="level-3---kubernetes-objects-and-workloads"></a>Niveau 3 - Objets et charges de travail Kubernetes
Le niveau Objets et charges de travail Kubernetes inclut les composants suivants.

| Composant | Exigences de supervision |
|:---|:---|
| Déploiements | Surveillez l’état réel et l’état souhaité du déploiement, ainsi que l’état et l’utilisation des ressources des pods exécutés dessus.  | 
| Pods | Surveillez l’état et l’utilisation des ressources, y compris l’UC et la mémoire, et les pods en cours d’exécution sur votre cluster AKS. |
| Conteneurs | Surveillez l’utilisation des ressources, y compris l’UC et la mémoire, et les conteneurs en cours d’exécution sur votre cluster AKS. |


Utilisez les vues et rapports existants dans Container Insights pour surveiller les conteneurs et les pods. Utilisez les vues **Nœuds** et **Contrôleurs** pour afficher l’état d’intégrité et les performances des pods qui s’exécutent dessus, ainsi que l’état d’intégrité et les performances de leurs conteneurs. Affichez l’intégrité et les performances des conteneurs directement à partir de la vue **Conteneurs**. Voir [Superviser les performances de votre cluster Kubernetes avec Container Insights](../azure-monitor/containers/container-insights-analyze.md) pour plus de détails sur cette vue et pour analyser l’intégrité et les performances du conteneur.

:::image type="content" source="media/monitor-aks/container-insights-containers-view.png" alt-text="Vue de conteneurs Container Insights" lightbox="media/monitor-aks/container-insights-containers-view.png":::

Utilisez le classeur **Déploiement** dans Container Insights pour afficher les métriques collectées pour les déploiements. Consultez [Déploiement et métriques HPA avec Container Insights](../azure-monitor/containers/container-insights-deployment-hpa-metrics.md) pour plus de détails.

> [!NOTE]
> La vue Déploiements dans Container Insights est actuellement disponible en préversion publique.

:::image type="content" source="media/monitor-aks/container-insights-deployments-workbook.png" alt-text="Classeur de déploiements Container Insights" lightbox="media/monitor-aks/container-insights-deployments-workbook.png":::

#### <a name="live-data"></a>Données actives
Dans les scénarios de dépannage, Container Insights fournit un accès aux journaux de conteneur AKS (stdout/stderr), aux événements et aux métriques de pod en temps réel. Pour plus d’informations sur cette fonctionnalité, consultez [Comment voir les journaux, métriques de pod et événements Kubernetes en temps réel](../azure-monitor/containers/container-insights-livedata-overview.md).

:::image type="content" source="media/monitor-aks/container-insights-live-data.png" alt-text="Données actives de Container Insights" lightbox="media/monitor-aks/container-insights-live-data.png":::

### <a name="level-4--applications"></a>Niveau 4 - Applications
Le niveau Applications comprend les charges de travail d’application en cours d’exécution dans le cluster AKS.

| Composant | Exigences de supervision |
|:---|:---|
| Applications | Surveillez les déploiements d’applications de microservice pour identifier les défaillances d’application et les problèmes de latence. Contient des informations comme les taux de requête, les temps de réponse et les exceptions. |

Application Insights fournit une analyse complète des applications qui s’exécutent sur AKS et d’autres environnements. Si vous avez une application Java, vous pouvez assurer la surveillance sans instrumenter votre code en suivant [Surveillance d’application sans instrumentation pour Kubernetes – Azure Monitor Application Insights](../azure-monitor/app/kubernetes-codeless.md). Toutefois, pour une analyse complète, vous devez configurer la surveillance basée sur le code en fonction de votre application.

- [Applications ASP.NET](../azure-monitor/app/asp-net.md)
- [Applications ASP.NET Core](../azure-monitor/app/asp-net-core.md)
- [Applications de console .NET](../azure-monitor/app/console.md)
- [Java](../azure-monitor/app/java-in-process-agent.md)
- [Node.JS](../azure-monitor/app/nodejs.md)
- [Python](../azure-monitor/app/opencensus-python.md)
- [autres plateformes](../azure-monitor/app/platforms.md)

Voir [Présentation d’Application Insights](../azure-monitor/app/app-insights-overview.md) 

### <a name="level-5--external-components"></a>Niveau 5 - Composants externes
Les composants externes à AKS incluent les éléments suivants.

| Composant | Exigences de supervision |
|:---|:---|
| Service Mesh, Ingress, Egress | Métriques basées sur le composant. |
| Files d’attente de travail et de base de données | Métriques basées sur le composant. |

Surveillez des composants externes comme Service Mesh, Ingress et Egress avec Prometheus et Grafana ou d’autres outils propriétaires. Surveillez les bases de données et autres ressources Azure à l’aide d’autres fonctionnalités d’Azure Monitor.

## <a name="analyze-metric-data-with-metrics-explorer"></a>Analyser les données de métriques avec Metrics Explorer
Utilisez l’Explorateur de métriques lorsque vous souhaitez effectuer une analyse personnalisée des données de métriques collectées pour vos conteneurs. Metrics Explorer vous permet de tracer des graphiques, de corréler visuellement des tendances et d’investiguer les pics et les creux dans les valeurs des métriques. Créez une alerte de métriques pour vous avertir de manière proactive lorsqu’une valeur de métrique franchit un seuil, et épinglez des graphiques aux tableaux de bord pour qu’ils soient utilisés par différents membres de votre organisation.

Pour plus d’informations sur l’utilisation de cette fonctionnalité, consultez [Prise en main d’Azure Metrics Explorer](../azure-monitor/essentials/metrics-getting-started.md). Pour obtenir la liste des métriques de plateforme collectées pour AKS, consultez [Surveillance des métriques de référence des données AKS](monitor-aks-reference.md#metrics). Lorsque Container Insights est activés pour un cluster, des [valeurs métriques d’addition](../azure-monitor/containers/container-insights-update-metrics.md) sont disponibles.

:::image type="content" source="media/monitor-aks/metrics-explorer.png" alt-text="Metrics Explorer" lightbox="media/monitor-aks/metrics-explorer.png":::



## <a name="analyze-log-data-with-log-analytics"></a>Analyser les données de journal avec Log Analytics
Utilisez Log Analytics lorsque vous souhaitez analyser les journaux des ressources ou approfondir les données utilisées pour créer les vues dans Container Insights. Log Analytics vous permet d’effectuer une analyse personnalisée de vos données de journal. 

Voir [Comment interroger des journaux depuis Container Insights](../azure-monitor/containers/container-insights-log-query.md) pour plus de détails sur l’utilisation de requêtes de journal pour analyser les données collectées par Container Insights. Voir [Utilisation des requêtes dans Azure Monitor Log Analytics](../azure-monitor/logs/queries.md) pour des informations sur l’utilisation de ces requêtes et [Tutoriel Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md) pour un tutoriel complet sur l’utilisation de Log Analytics pour exécuter des requêtes et travailler avec leurs résultats.

Pour obtenir une liste des tables collectées pour AKS que vous pouvez analyser dans l’Explorateur de métriques, voir [Surveillance des journaux de référence des données AKS](monitor-aks-reference.md#azure-monitor-logs-tables).

:::image type="content" source="media/monitor-aks/log-analytics-queries.png" alt-text="Requêtes Log Analytics pour Kubernetes" lightbox="media/monitor-aks/log-analytics-queries.png":::

En plus des données Container Insights, vous pouvez utiliser des requêtes de journal pour analyser les journaux de ressources à partir d’AKS. Pour une liste des catégories de journaux disponibles, voir [Journaux des ressources de référence des données AKS](monitor-aks-reference.md#resource-logs). Vous devez créer un paramètre de diagnostic pour collecter chaque catégorie comme décrit dans [Configurer la surveillance](#configure-monitoring) avant que ces données ne soient collectées. 




## <a name="alerts"></a>Alertes
[Les alertes dans Azure Monitor](../azure-monitor/alerts/alerts-overview.md) vous informent de façon proactive des données et des modèles intéressants dans vos données de surveillance. Elles permettent d’identifier et de résoudre les problèmes affectant votre système avant que vos clients ne les remarquent. Il n’existe aucune règle d’alerte préconfigurée pour les clusters AKS, mais vous pouvez créer vos propres règles en fonction des données collectées par Container Insights.

> [!IMPORTANT]
> La plupart des règles d’alerte ont un coût qui dépend du type de règle, du nombre de dimensions incluses et de la fréquence d’exécution. Reportez-vous à **Règles d’alerte** dans [Tarification d’Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/) avant de créer des règles d’alerte.


### <a name="choosing-the-alert-type"></a>Choisir le type d’alerte
Les types de règles d’alerte les plus courants dans Azure Monitor sont les [alertes de métriques](../azure-monitor/alerts/alerts-metric.md) et les [alertes de requête de journal](../azure-monitor/alerts/alerts-log-query.md). Le type de règle d’alerte que vous créez pour un scénario particulier dépend de l’emplacement des données sur lesquelles vous souhaitez générer des alertes. Vous pouvez avoir des cas où les données d’un scénario d’alerte particulier sont disponibles dans les métriques et les journaux, et vous devez déterminer le type de règle à utiliser. 

La meilleure stratégie consiste généralement à utiliser des alertes de métriques plutôt que des alertes de journal, car elles sont plus réactives et plus précises. Vous pouvez créer une alerte de métrique sur toutes les valeurs que vous pouvez analyser dans Metrics Explorer. Si la logique de votre règle d’alerte nécessite des données dans des journaux, ou si elle nécessite une logique plus complexe, vous pouvez utiliser une règle d’alerte de requête de journal.

Par exemple, si vous souhaitez alerter quand une charge de travail d’application consomme trop d’UC, vous pouvez créer une alerte de métrique à l’aide de la métrique d’UC. Si vous avez besoin d’une alerte lorsqu’un message particulier se trouve dans un journal de plan de contrôle, vous aurez besoin d’une alerte de journal.
### <a name="metric-alert-rules"></a>Règles d’alerte de métrique
Les règles d’alerte de métrique utilisent les mêmes valeurs de métrique que Metrics Explorer. En fait, vous pouvez créer une règle d’alerte directement à partir de Metrics Explorer avec les données que vous analysez actuellement. Vous pouvez utiliser n’importe quelle valeur des [Métriques de référence des données AKS](monitor-aks-reference.md#metrics) pour les règles d’alerte de métrique.

Container Insights comprend une fonctionnalité en préversion publique qui crée un ensemble recommandé de règles d’alerte de métrique pour votre cluster AKS. Cette fonctionnalité permet de créer des valeurs de métriques (également en préversion) utilisées par les règles d’alerte que vous pouvez également utiliser dans Metrics Explorer. Voir [Alertes de métrique recommandées (préversion) à partir de Container Insights](../azure-monitor/containers/container-insights-metric-alerts.md) pour plus de détails sur cette fonctionnalité et sur la création d’alertes métriques pour AKS.


### <a name="log-alerts-rules"></a>Règles d’alerte de journal
Utilisez les règles d’alerte de journal pour générer une alerte à partir des résultats d’une requête de journal. Il peut s’agir de données collectées par Container Insights ou de journaux de ressources AKS. Voir [Comment créer des alertes de journal Container Insights](../azure-monitor/containers/container-insights-log-alerts.md) pour plus de détails sur les règles d’alerte des journaux pour AKS et un ensemble d’exemple de requêtes conçues pour les règles d’alerte. Vous pouvez également vous reporter à [Comment interroger des journaux depuis Container Insights](../azure-monitor/containers/container-insights-log-query.md) pour plus de détails sur les requêtes de journal pouvant être modifiées pour des règles d’alerte.

### <a name="virtual-machine-alerts"></a>Alertes de machine virtuelle
AKS s’appuie sur un groupe de machines virtuelles identiques qui doit être sain pour exécuter des charges de travail AKS. Vous pouvez créer des alertes sur les métriques critiques comme l’UC, la mémoire et le stockage des machines virtuelles en utilisant les conseils de [Analyse des machines virtuelles avec Azure Monitor : Alertes](../azure-monitor/vm/monitor-virtual-machine-alerts.md).

### <a name="prometheus-alerts"></a>Alertes Prometheus
Pour les conditions dans lesquelles Azure Monitor n’a pas les données requises pour une condition d’alerte, ou si les alertes peuvent ne pas être suffisamment réactives, vous devez configurer des alertes dans Prometheus. Par exemple, la génération d’alertes pour le serveur d’API. Azure Monitor ne collecte pas les informations critiques pour le serveur d’API, notamment sa disponibilité ou s’il rencontre un goulot d’étranglement. Vous pouvez créer une alerte de requête de journal en utilisant les données de la catégorie Journal des ressources kube-apiserver, mais cette opération peut prendre plusieurs minutes avant que vous receviez une alerte, ce qui peut ne pas répondre à vos besoins. 


## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur les métriques, les journaux et d’autres valeurs importantes créées par AKS, consultez [Surveillance des informations de référence sur les données de surveillance d’AKS](monitor-aks-reference.md) .
