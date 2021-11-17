---
title: Comment interroger des journaux à partir de Container Insights
description: Container Insights collecte des métriques et des données de journal. Cet article décrit les enregistrements correspondants et inclut des exemples de requêtes.
ms.topic: conceptual
ms.date: 07/19/2021
ms.openlocfilehash: af48255c1895142107e693595c256d2c76fdee65
ms.sourcegitcommit: c434baa76153142256d17c3c51f04d902e29a92e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132179211"
---
# <a name="how-to-query-logs-from-container-insights"></a>Comment interroger des journaux à partir de Container Insights

Container Insights collecte des métriques de performances, des données d’inventaire et des informations sur l’état d’intégrité à partir d’hôtes de conteneur et de conteneurs. Les données sont collectées toutes les trois minutes et transférées à l’espace de travail Log Analytics dans Azure Monitor où elles sont disponibles pour les [requêtes de journal](../logs/log-query-overview.md) à l’aide de [Log Analytics](../logs/log-analytics-overview.md) dans Azure Monitor Vous pouvez appliquer ces données à divers scénarios tels que la planification de la migration, l’analyse de la capacité, la détection et la résolution de problèmes de performances à la demande. Les journaux Azure Monitor peuvent vous aider à rechercher des tendances, à diagnostiquer les goulots d’étranglement, à effectuer des prévisions ou à mettre en corrélation des données afin de déterminer si la configuration de cluster actuelle garantit des performances optimales.

Voir [Utilisation des requêtes dans Azure Monitor Log Analytics](../logs/queries.md) pour des informations sur l’utilisation de ces requêtes et [Tutoriel Log Analytics](../logs/log-analytics-tutorial.md) pour un tutoriel complet sur l’utilisation de Log Analytics pour exécuter des requêtes et travailler avec leurs résultats.

## <a name="open-log-analytics"></a>Ouvrir Log Analytics
Il existe plusieurs options pour démarrer Log Analytics, chacune commençant par une [étendue](../logs/scope.md) différente. Pour accéder à toutes les données de l’espace de travail, sélectionnez **Journaux** dans le menu **Surveiller**. Pour limiter les données à un seul cluster Kubernetes, sélectionnez **Journaux** dans le menu de ce cluster. 

:::image type="content" source="media/container-insights-log-query/start-log-analytics.png" alt-text="Démarrer Log Analytics" lightbox="media/container-insights-log-query/start-log-analytics.png":::

## <a name="existing-log-queries"></a>Requêtes de journal existantes
Vous n’êtes pas tenu de savoir écrire une requête de journal pour utiliser Log Analytics. Il existe plusieurs requêtes prédéfinies que vous pouvez sélectionner et exécuter sans modification ou utiliser comme début d’une requête personnalisée. Cliquez sur **Requêtes** en haut de l’écran Log Analytics et affichez les requêtes avec un **type de ressource** de **Kubernetes Services**. 

:::image type="content" source="media/container-insights-log-query/log-analytics-queries.png" alt-text="Requêtes Log Analytics pour Kubernetes" lightbox="media/container-insights-log-query/log-analytics-queries.png":::

## <a name="container-tables"></a>Tables de conteneurs
Consultez la [référence des tables Azure Monitor](/azure/azure-monitor/reference/tables/tables-resourcetype#kubernetes-services) pour obtenir la liste des tables utilisées par Container Insights ainsi que leurs descriptions détaillées. Toutes ces tables sont disponibles pour les requêtes de journal.


## <a name="example-log-queries"></a>Exemples de requêtes de journal
Il est souvent utile de créer des requêtes en commençant par un exemple ou deux, puis en les modifiant afin de les adapter à vos besoins. Pour créer des requêtes plus avancées, vous pouvez utiliser les exemples de requêtes suivants :

### <a name="list-all-of-a-containers-lifecycle-information"></a>Liste de toutes les informations sur le cycle de vie d’un conteneur

```kusto
ContainerInventory
| project Computer, Name, Image, ImageTag, ContainerState, CreatedTime, StartedTime, FinishedTime
| render table
```

### <a name="kubernetes-events"></a>Événements Kubernetes

``` kusto
KubeEvents
| where not(isempty(Namespace))
| sort by TimeGenerated desc
| render table
```

### <a name="container-cpu"></a>Processeur du conteneur

``` kusto
Perf
| where ObjectName == "K8SContainer" and CounterName == "cpuUsageNanoCores" 
| summarize AvgCPUUsageNanoCores = avg(CounterValue) by bin(TimeGenerated, 30m), InstanceName 
```

### <a name="container-memory"></a>Mémoire du conteneur

```kusto
Perf
| where ObjectName == "K8SContainer" and CounterName == "memoryRssBytes"
| summarize AvgUsedRssMemoryBytes = avg(CounterValue) by bin(TimeGenerated, 30m), InstanceName
```

### <a name="requests-per-minute-with-custom-metrics"></a>Demandes par minute avec des métriques personnalisées

```kusto
InsightsMetrics
| where Name == "requests_count"
| summarize Val=any(Val) by TimeGenerated=bin(TimeGenerated, 1m)
| sort by TimeGenerated asc
| project RequestsPerMinute = Val - prev(Val), TimeGenerated
| render barchart 
```
### <a name="pods-by-name-and-namespace"></a>Pods par nom et espace de noms

```kusto
let startTimestamp = ago(1h);
KubePodInventory
| where TimeGenerated > startTimestamp
| project ContainerID, PodName=Name, Namespace
| where PodName contains "name" and Namespace startswith "namespace"
| distinct ContainerID, PodName
| join
(
    ContainerLog
    | where TimeGenerated > startTimestamp
)
on ContainerID
// at this point before the next pipe, columns from both tables are available to be "projected". Due to both
// tables having a "Name" column, we assign an alias as PodName to one column which we actually want
| project TimeGenerated, PodName, LogEntry, LogEntrySource
| summarize by TimeGenerated, LogEntry
| order by TimeGenerated desc
```

### <a name="pod-scale-out-hpa"></a>Scale-out des pods (HPA)
Retourne le nombre de réplicas avec scale-out dans chaque déploiement. Calcule le pourcentage de scale-out avec le nombre maximal de réplicas configurés dans HPA.


```kusto
let _minthreshold = 70; // minimum threshold goes here if you want to setup as an alert
let _maxthreshold = 90; // maximum threshold goes here if you want to setup as an alert
let startDateTime = ago(60m);
KubePodInventory
| where TimeGenerated >= startDateTime 
| where Namespace !in('default', 'kube-system') // List of non system namespace filter goes here.
| extend labels = todynamic(PodLabel)
| extend deployment_hpa = reverse(substring(reverse(ControllerName), indexof(reverse(ControllerName), "-") + 1))
| distinct tostring(deployment_hpa)
| join kind=inner (InsightsMetrics 
    | where TimeGenerated > startDateTime 
    | where Name == 'kube_hpa_status_current_replicas'
    | extend pTags = todynamic(Tags) //parse the tags for values
    | extend ns = todynamic(pTags.k8sNamespace) //parse namespace value from tags
    | extend deployment_hpa = todynamic(pTags.targetName) //parse HPA target name from tags
    | extend max_reps = todynamic(pTags.spec_max_replicas) // Parse maximum replica settings from HPA deployment
    | extend desired_reps = todynamic(pTags.status_desired_replicas) // Parse desired replica settings from HPA deployment
    | summarize arg_max(TimeGenerated, *) by tostring(ns), tostring(deployment_hpa), Cluster=toupper(tostring(split(_ResourceId, '/')[8])), toint(desired_reps), toint(max_reps), scale_out_percentage=(desired_reps * 100 / max_reps)
    //| where scale_out_percentage > _minthreshold and scale_out_percentage <= _maxthreshold
    )
    on deployment_hpa
```

### <a name="nodepool-scale-outs"></a>Scale-out de pool de nœud 
Retourne le nombre de nœuds actifs dans chaque pool de nœuds. Calcule le nombre de nœuds actifs disponibles et la configuration de nœuds max dans les paramètres de mise à l’échelle automatique pour déterminer le pourcentage de scale-out. Consultez les lignes commentées de la requête pour l’utiliser pour une règle d’alerte de **nombre de résultats**.

```kusto
let nodepoolMaxnodeCount = 10; // the maximum number of nodes in your auto scale setting goes here.
let _minthreshold = 20;
let _maxthreshold = 90;
let startDateTime = 60m;
KubeNodeInventory
| where TimeGenerated >= ago(startDateTime)
| extend nodepoolType = todynamic(Labels) //Parse the labels to get the list of node pool types
| extend nodepoolName = todynamic(nodepoolType[0].agentpool) // parse the label to get the nodepool name or set the specific nodepool name (like nodepoolName = 'agentpool)'
| summarize nodeCount = count(Computer) by ClusterName, tostring(nodepoolName), TimeGenerated
//(Uncomment the below two lines to set this as an log search alert)
//| extend scaledpercent = iff(((nodeCount * 100 / nodepoolMaxnodeCount) >= _minthreshold and (nodeCount * 100 / nodepoolMaxnodeCount) < _maxthreshold), "warn", "normal")
//| where scaledpercent == 'warn'
| summarize arg_max(TimeGenerated, *) by nodeCount, ClusterName, tostring(nodepoolName)
| project ClusterName, 
    TotalNodeCount= strcat("Total Node Count: ", nodeCount),
    ScaledOutPercentage = (nodeCount * 100 / nodepoolMaxnodeCount),  
    TimeGenerated, 
    nodepoolName
```

### <a name="system-containers-replicaset-availability"></a>Disponibilité des conteneurs système (replicatset)
Retourne les conteneurs système (replicasets) et signale le pourcentage d’indisponibilité. Consultez les lignes commentées de la requête pour l’utiliser pour une règle d’alerte de **nombre de résultats**.

```kusto
let startDateTime = 5m; // the minimum time interval goes here
let _minalertThreshold = 50; //Threshold for minimum and maximum unavailable or not running containers
let _maxalertThreshold = 70;
KubePodInventory
| where TimeGenerated >= ago(startDateTime)
| distinct ClusterName, TimeGenerated
| summarize Clustersnapshot = count() by ClusterName
| join kind=inner (
    KubePodInventory
    | where TimeGenerated >= ago(startDateTime)
    | where Namespace in('default', 'kube-system') and ControllerKind == 'ReplicaSet' // the system namespace filter goes here
    | distinct ClusterName, Computer, PodUid, TimeGenerated, PodStatus, ServiceName, PodLabel, Namespace, ContainerStatus
    | summarize arg_max(TimeGenerated, *), TotalPODCount = count(), podCount = sumif(1, PodStatus == 'Running' or PodStatus != 'Running'), containerNotrunning = sumif(1, ContainerStatus != 'running')
        by ClusterName, TimeGenerated, ServiceName, PodLabel, Namespace
    )
    on ClusterName
| project ClusterName, ServiceName, podCount, containerNotrunning, containerNotrunningPercent = (containerNotrunning * 100 / podCount), TimeGenerated, PodStatus, PodLabel, Namespace, Environment = tostring(split(ClusterName, '-')[3]), Location = tostring(split(ClusterName, '-')[4]), ContainerStatus
//Uncomment the below line to set for automated alert
//| where PodStatus == "Running" and containerNotrunningPercent > _minalertThreshold and containerNotrunningPercent < _maxalertThreshold
| summarize arg_max(TimeGenerated, *), c_entry=count() by PodLabel, ServiceName, ClusterName
//Below lines are to parse the labels to identify the impacted service/component name
| extend parseLabel = replace(@'k8s-app', @'k8sapp', PodLabel)
| extend parseLabel = replace(@'app.kubernetes.io/component', @'appkubernetesiocomponent', parseLabel)
| extend parseLabel = replace(@'app.kubernetes.io/instance', @'appkubernetesioinstance', parseLabel)
| extend tags = todynamic(parseLabel)
| extend tag01 = todynamic(tags[0].app)
| extend tag02 = todynamic(tags[0].k8sapp)
| extend tag03 = todynamic(tags[0].appkubernetesiocomponent)
| extend tag04 = todynamic(tags[0].aadpodidbinding)
| extend tag05 = todynamic(tags[0].appkubernetesioinstance)
| extend tag06 = todynamic(tags[0].component)
| project ClusterName, TimeGenerated,
    ServiceName = strcat( ServiceName, tag01, tag02, tag03, tag04, tag05, tag06),
    ContainerUnavailable = strcat("Unavailable Percentage: ", containerNotrunningPercent),
    PodStatus = strcat("PodStatus: ", PodStatus), 
    ContainerStatus = strcat("Container Status: ", ContainerStatus)
```

### <a name="system-containers-daemonsets-availability"></a>Disponibilité des conteneurs système (daemonsets)
Retourne les conteneurs système (daemonsets) et signale le pourcentage d’indisponibilité. Consultez les lignes commentées de la requête pour l’utiliser pour une règle d’alerte de **nombre de résultats**.

```kusto
let startDateTime = 5m; // the minimum time interval goes here
let _minalertThreshold = 50; //Threshold for minimum and maximum unavailable or not running containers
let _maxalertThreshold = 70;
KubePodInventory
| where TimeGenerated >= ago(startDateTime)
| distinct ClusterName, TimeGenerated
| summarize Clustersnapshot = count() by ClusterName
| join kind=inner (
    KubePodInventory
    | where TimeGenerated >= ago(startDateTime)
    | where Namespace in('default', 'kube-system') and ControllerKind == 'DaemonSet' // the system namespace filter goes here
    | distinct ClusterName, Computer, PodUid, TimeGenerated, PodStatus, ServiceName, PodLabel, Namespace, ContainerStatus
    | summarize arg_max(TimeGenerated, *), TotalPODCount = count(), podCount = sumif(1, PodStatus == 'Running' or PodStatus != 'Running'), containerNotrunning = sumif(1, ContainerStatus != 'running')
        by ClusterName, TimeGenerated, ServiceName, PodLabel, Namespace
    )
    on ClusterName
| project ClusterName, ServiceName, podCount, containerNotrunning, containerNotrunningPercent = (containerNotrunning * 100 / podCount), TimeGenerated, PodStatus, PodLabel, Namespace, Environment = tostring(split(ClusterName, '-')[3]), Location = tostring(split(ClusterName, '-')[4]), ContainerStatus
//Uncomment the below line to set for automated alert
//| where PodStatus == "Running" and containerNotrunningPercent > _minalertThreshold and containerNotrunningPercent < _maxalertThreshold
| summarize arg_max(TimeGenerated, *), c_entry=count() by PodLabel, ServiceName, ClusterName
//Below lines are to parse the labels to identify the impacted service/component name
| extend parseLabel = replace(@'k8s-app', @'k8sapp', PodLabel)
| extend parseLabel = replace(@'app.kubernetes.io/component', @'appkubernetesiocomponent', parseLabel)
| extend parseLabel = replace(@'app.kubernetes.io/instance', @'appkubernetesioinstance', parseLabel)
| extend tags = todynamic(parseLabel)
| extend tag01 = todynamic(tags[0].app)
| extend tag02 = todynamic(tags[0].k8sapp)
| extend tag03 = todynamic(tags[0].appkubernetesiocomponent)
| extend tag04 = todynamic(tags[0].aadpodidbinding)
| extend tag05 = todynamic(tags[0].appkubernetesioinstance)
| extend tag06 = todynamic(tags[0].component)
| project ClusterName, TimeGenerated,
    ServiceName = strcat( ServiceName, tag01, tag02, tag03, tag04, tag05, tag06),
    ContainerUnavailable = strcat("Unavailable Percentage: ", containerNotrunningPercent),
    PodStatus = strcat("PodStatus: ", PodStatus), 
    ContainerStatus = strcat("Container Status: ", ContainerStatus)
```

## <a name="resource-logs"></a>Journaux d’activité de ressources
Les journaux de ressources pour AKS sont stockés dans la table [AzureDiagnostics](/azure/azure-monitor/reference/tables/azurediagnostics). Vous pouvez distinguer les différents journaux à l’aide de la colonne **Catégorie**. Pour obtenir une description de chaque catégorie, consultez [Journaux des ressources de référence AKS](../../aks/monitor-aks-reference.md). Les exemples suivants requièrent une extension de diagnostic afin d’envoyer des journaux de ressources pour un cluster AKS vers un espace de travail Log Analytics. Pour plus d'informations, consultez [Configurer l’analyse](../../aks/monitor-aks.md#configure-monitoring).

### <a name="api-server-logs"></a>Journaux de serveur d’API

```kusto
AzureDiagnostics 
| where Category == "kube-apiserver"
```

### <a name="count-logs-for-each-category"></a>Compter les journaux pour chaque catégorie

```kusto
AzureDiagnostics
| where ResourceType == "MANAGEDCLUSTERS"
| summarize count() by Category
```

## <a name="query-prometheus-metrics-data"></a>Interroger les données de métriques Prometheus

L’exemple suivant est une requête de mesures Prometheus indiquant le nombre de lectures de disque par seconde, par disque et par nœud.

```
InsightsMetrics
| where Namespace == 'container.azm.ms/diskio'
| where TimeGenerated > ago(1h)
| where Name == 'reads'
| extend Tags = todynamic(Tags)
| extend HostName = tostring(Tags.hostName), Device = Tags.name
| extend NodeDisk = strcat(Device, "/", HostName)
| order by NodeDisk asc, TimeGenerated asc
| serialize
| extend PrevVal = iif(prev(NodeDisk) != NodeDisk, 0.0, prev(Val)), PrevTimeGenerated = iif(prev(NodeDisk) != NodeDisk, datetime(null), prev(TimeGenerated))
| where isnotnull(PrevTimeGenerated) and PrevTimeGenerated != TimeGenerated
| extend Rate = iif(PrevVal > Val, Val / (datetime_diff('Second', TimeGenerated, PrevTimeGenerated) * 1), iif(PrevVal == Val, 0.0, (Val - PrevVal) / (datetime_diff('Second', TimeGenerated, PrevTimeGenerated) * 1)))
| where isnotnull(Rate)
| project TimeGenerated, NodeDisk, Rate
| render timechart

```

Pour voir les métriques Prometheus récupérées par Azure Monitor et filtrées par espace de noms, spécifiez « prometheus ». Voici un exemple de requête permettant de voir les métriques Prometheus à partir de l’espace de noms kubernetes `default`.

```
InsightsMetrics 
| where Namespace == "prometheus"
| extend tags=parse_json(Tags)
| summarize count() by Name
```

Les données Prometheus peuvent également être interrogées directement par nom.

```
InsightsMetrics 
| where Namespace == "prometheus"
| where Name contains "some_prometheus_metric"
```

### <a name="query-config-or-scraping-errors"></a>Interroger les erreurs de récupération ou de configuration

Pour examiner les erreurs de récupération ou de configuration, l’exemple de requête suivant retourne des événements d’information à partir de la table `KubeMonAgentEvents`.

```
KubeMonAgentEvents | where Level != "Info" 
```

Les résultats obtenus sont similaires à ceux de l’exemple suivant :

![Enregistrer les résultats de requête des événements d’information à partir de l’agent](./media/container-insights-log-query/log-query-example-kubeagent-events.png)

## <a name="next-steps"></a>Étapes suivantes

Container Insights n’inclut pas d’ensemble prédéfini d’alertes. Consultez [Créer des alertes de performances avec Container Insights](./container-insights-log-alerts.md) pour savoir comment créer les alertes recommandées pour une utilisation élevée du processeur et de la mémoire, afin de prendre en charge vos procédures et processus opérationnels ou DevOps.
