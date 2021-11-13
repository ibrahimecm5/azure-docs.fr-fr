---
title: 'Surveillez les machines virtuelles avec Azure Monitor : Alertes'
description: Découvrez comment créer des alertes à partir de machines virtuelles et leurs charges de travail invitées à l’aide de Azure Monitor.
ms.service: azure-monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/21/2021
ms.openlocfilehash: b06d7c573514e0fe0471e13df3476bf5b13f20e3
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130252684"
---
# <a name="monitor-virtual-machines-with-azure-monitor-alerts"></a>Surveillez les machines virtuelles avec Azure Monitor : Alertes

Cet article fait partie du scénario [surveiller les machines virtuelles et leurs charges de travail dans Azure Monitor](monitor-virtual-machine.md). Il fournit des conseils sur la création de règles d’alerte pour vos machines virtuelles et leurs systèmes d’exploitation invités. [Les alertes dans Azure Monitor](../alerts/alerts-overview.md) vous informent de façon proactive des données et des modèles intéressants dans vos données de surveillance. Il n'existe pas de règles d'alerte préconfigurées pour les machines virtuelles, mais vous pouvez créer les vôtres en fonction des données collectées par VM insights. 

> [!NOTE]
> Cela diffère des alertes créées par [Intégrité des invités Azure Monitor pour machines virtuelles](vminsights-health-overview.md), qui est une fonctionnalité actuellement en préversion publique. À mesure que cette fonctionnalité se rapproche de la disponibilité générale, des conseils sur les alertes seront consolidés.

> [!IMPORTANT]
> La plupart des règles d’alerte ont un coût qui dépend du type de règle, du nombre de dimensions incluses et de la fréquence d’exécution. Reportez-vous à **Règles d’alerte** dans [Tarification d’Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/) avant de créer des règles d’alerte.

## <a name="choose-the-alert-type"></a>Sélectionner le type d’alerte
Les types de règles d’alerte les plus courants dans Azure Monitor sont les [alertes de métriques](../alerts/alerts-metric.md) et les [alertes de requête de journal](../alerts/alerts-log-query.md). Le type de règle d’alerte que vous créez pour un scénario particulier dépend de l’emplacement des données sur lesquelles vous souhaitez générer des alertes. Vous pouvez avoir des cas où les données d’un scénario d’alerte particulier sont disponibles dans les métriques et les journaux, et vous devez déterminer le type de règle à utiliser. Vous pouvez également disposer d’une certaine flexibilité dans la façon dont vous recueillez certaines données et laisser votre décision du type de règle d’alerte prendre votre décision en matière de méthode de collecte des données.

La meilleure stratégie consiste généralement à utiliser des alertes de métriques plutôt que des alertes de journal, car elles sont plus réactives et plus précises. Pour utiliser des alertes de métriques, les données sur lesquelles vous importez doivent être disponibles dans Métriques. VM Insights envoie actuellement toutes ses données aux journaux. Vous devez donc installer l’agent de Azure Monitor pour utiliser des alertes de métriques avec les données du système d’exploitation invité. Utilisez les alertes de requête de journal avec les données de métrique lorsqu’elles ne sont pas disponibles dans les métriques ou si vous avez besoin d’une logique au-delà de la logique relativement simple pour une règle d’alerte métrique.

### <a name="metric-alert-rules"></a>Règles d’alerte de métrique
Les [Règles d’alerte métriques](../alerts/alerts-metric.md) sont utiles pour les alertes lorsqu’une mesure donnée dépasse un seuil. C’est le cas, par exemple, lorsque l’UC d’un ordinateur est très longue. La cible d’une règle d’alerte de métrique peut être un ordinateur spécifique, un groupe de ressources ou un abonnement. Dans ce cas, vous pouvez créer une règle unique qui s’applique à un groupe d’ordinateurs.

Les règles de métrique pour les machines virtuelles peuvent utiliser les données suivantes :

- Les métriques de l’hôte pour les machines virtuelles Azure, qui sont collectées automatiquement. 
- Mesures collectées par l’agent de Azure Monitor à partir du système d’exploitation invité. 

> [!NOTE]
> Quand la machine virtuelle Insights prend en charge l’agent Azure Monitor, qui est actuellement en préversion publique, elle envoie des données de performance du système d’exploitation invité aux métriques afin que vous puissiez utiliser les alertes de métriques.

### <a name="log-alerts"></a>Alertes de journal
Les [alertes de journal](../alerts/alerts-metric.md) peuvent effectuer deux mesures différentes du résultat d’une requête de journal, chacune prenant en charge des scénarios distincts pour la surveillance des machines virtuelles :

- [Alertes de mesure de métrique](../alerts/alerts-unified-log.md#calculation-of-measure-based-on-a-numeric-column-such-as-cpu-counter-value) : créent une alerte distincte pour chaque enregistrement dans une requête dont la valeur dépasse un seuil défini dans la règle d’alerte. Les mesures métriques sont idéales pour les données non numériques telles que les événements Windows et Syslog collectés par l'agent Log Analytics ou pour l'analyse des tendances de performance sur plusieurs ordinateurs.
- [Nombre de résultats](../alerts/alerts-unified-log.md#count-of-the-results-table-rows) : crée une seule alerte lorsqu'une requête renvoie au moins un nombre spécifié d'enregistrements. Les mesures métriques sont idéales pour les données non numériques telles que les événements Windows et Syslog collectés par l'[agent Log Analytics](../agents/log-analytics-agent.md) ou pour l'analyse des tendances de performance sur plusieurs ordinateurs. Vous pouvez également choisir cette stratégie si vous souhaitez réduire le nombre d’alertes ou créer une alerte uniquement lorsque plusieurs ordinateurs ont la même condition d’erreur.

### <a name="target-resource-and-impacted-resource"></a>Ressource cible et ressource affectée

> [!NOTE]
> Les règles d’alerte de journal centrées sur les ressources, actuellement en préversion publique, simplifient les alertes de requête de journal pour les machines virtuelles et remplacent les fonctionnalités actuellement fournies par les requêtes de mesure de métrique. Vous pouvez utiliser la machine en tant que cible de la règle, qui mieux l’identifier comme ressource affectée. Vous pouvez également appliquer une seule règle d’alerte à tous les ordinateurs d’un groupe de ressources ou d’un abonnement spécifique. Quand les alertes de requête de journal du centre de ressources sont mises à la disposition générale, les instructions de ce scénario sont mises à jour.
> 
Chaque alerte dans Azure Monitor a une **propriété de ressource affectée**, qui est définie par la cible de la règle. Pour les règles d’alerte métrique, la ressource affectée est l’ordinateur, ce qui vous permet de l’identifier facilement dans l’affichage des alertes standard. Les alertes de requête de journal sont associées à la ressource d’espace de travail plutôt qu’à l’ordinateur, même si vous utilisez une alerte de mesure de métrique qui crée une alerte pour chaque ordinateur. Vous devez afficher les détails de l’alerte pour afficher l’ordinateur qui a été affecté.

Le nom de l'ordinateur est enregistré dans la propriété de la **ressource impactée**, que vous pouvez consulter dans les détails de l'alerte. Elle s’affiche également sous la forme d’une dimension dans les courriers électroniques envoyés à partir de l’alerte.

:::image type="content" source="media/monitor-virtual-machines/alert-metric-measurement.png" alt-text="Capture d’écran montrant une alerte avec une ressource affectée." lightbox="media/monitor-virtual-machines/alert-metric-measurement.png":::

Vous souhaiterez peut-être disposer d’une vue qui répertorie les alertes avec l’ordinateur affecté. Vous pouvez utiliser un classeur personnalisé qui utilise un [graphique de ressources](../../governance/resource-graph/overview.md) personnalisé pour fournir cette vue. Utilisez la requête suivante pour afficher les alertes, et utilisez la source de données **Azure Resource Graph** dans le classeur.

```kusto
alertsmanagementresources
| extend dimension = properties.context.context.condition.allOf
| mv-expand dimension
| extend dimension = dimension.dimensions
| mv-expand dimension
| extend Computer = dimension.value
| extend AlertStatus = properties.essentials.alertState
| summarize count() by Alert=name, tostring(AlertStatus), tostring(Computer)
| project Alert, AlertStatus, Computer
```
## <a name="common-alert-rules"></a>Règles d’alerte courantes
La section suivante répertorie les règles d’alerte courantes pour les machines virtuelles dans Azure Monitor. Des informations détaillées sur les alertes de métrique et les alertes de mesure du journal sont fournies pour chacune. Pour obtenir des conseils sur le type d’alerte à utiliser, consultez [Choisir le type d’alerte](#choose-the-alert-type).

Si vous n’êtes pas familiarisé avec le processus de création de règles d’alerte dans Azure Monitor, consultez les articles suivants pour obtenir de l’aide :

- [Créer, afficher et gérer des alertes de métrique à l'aide d'Azure Monitor](../alerts/alerts-metric.md)
- [Créer, afficher et gérer des alertes de journal à l’aide d’Azure Monitor](../alerts/alerts-log.md)

### <a name="machine-unavailable"></a>Ordinateur non disponible
La condition la plus simple consiste à envoyer une alerte lorsqu’un ordinateur n’est pas disponible. Il peut être arrêté, le système d’exploitation invité peut ne pas répondre ou l’agent ne répond pas. Il existe plusieurs façons de configurer cette alerte, mais la plus courante consiste à utiliser la pulsation envoyée à partir de l’agent de Log Analytics. 

#### <a name="log-query-alert-rules"></a>Alerte de requête sur un journal
Les alertes de requête de journal utilisent la [table Heartbeat](/azure/azure-monitor/reference/tables/heartbeat) , qui devrait contenir un enregistrement de Heartbeat toutes les minutes pour chaque machine. 

**Alertes distinctes**

Utilisez une règle de mesure de métrique avec la requête suivante.

```kusto
Heartbeat
| summarize TimeGenerated=max(TimeGenerated) by Computer
| extend Duration = datetime_diff('minute',now(),TimeGenerated)
| summarize AggregatedValue = min(Duration) by Computer, bin(TimeGenerated,5m)
```

**Alerte unique**

Utilisez un certain nombre d’alertes de résultats avec la requête suivante.

```kusto
Heartbeat
| summarize LastHeartbeat=max(TimeGenerated) by Computer 
| where LastHeartbeat < ago(5m)
```

#### <a name="metric-alert-rules"></a>Règles d’alerte de métrique
Une métrique appelée *Heartbeat* est incluse dans chaque espace de travail Log Analytics. Chaque ordinateur virtuel connecté à cet espace de travail envoie une valeur de métrique de pulsation chaque minute. Étant donné que l’ordinateur est une dimension de la mesure, vous pouvez déclencher une alerte quand un ordinateur ne parvient pas à envoyer une pulsation. Définissez le **type d'agrégation** sur **Compte** et la valeur de **Seuil** pour correspondre à la **granularité de l'évaluation**.

### <a name="cpu-alerts"></a>Alertes de l’UC
#### <a name="metric-alert-rules"></a>Règles d’alerte de métrique

| Cible | Métrique |
|:---|:---|
| Hôte | Pourcentage d’UC |
| Invité Windows | \Information sur le processeur (_Total)\% Temps du processeur |
| Invité Linux | cpu/usage_active |

#### <a name="log-alert-rules"></a>Règles d’alerte de journal

**Utilisation du processeur** 

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer
```

**Utilisation du processeur pour toutes les ressources de calcul dans un abonnement**

```kusto
 InsightsMetrics
 | where Origin == "vm.azm.ms"
 | where _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" and (_ResourceId contains "/providers/Microsoft.Compute/virtualMachines/" or _ResourceId contains "/providers/Microsoft.Compute/virtualMachineScaleSets/") 
 | where Namespace == "Processor" and Name == "UtilizationPercentage" | summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), _ResourceId
```

**Utilisation du processeur pour toutes les ressources de calcul dans un groupe de ressources** 

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/" or _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachineScaleSets/"
| where Namespace == "Processor" and Name == "UtilizationPercentage" | summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), _ResourceId 
```

### <a name="memory-alerts"></a>Alertes de mémoire

#### <a name="metric-alert-rules"></a>Règles d’alerte de métrique

| Cible | Métrique |
|:---|:---|
| Invité Windows | \Memory \% Octets de mémoire engagés utilisés<br>\Memory\Octets disponibles |
| Invité Linux | mem/disponible<br>mem/available_percent |

#### <a name="log-alert-rules"></a>Règles d’alerte de journal

**Mémoire disponible en Mo** 

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Memory" and Name == "AvailableMB"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer
```

**Mémoire disponible en pourcentage** 

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Memory" and Name == "AvailableMB"
| extend TotalMemory = toreal(todynamic(Tags)["vm.azm.ms/memorySizeMB"]) | extend AvailableMemoryPercentage = (toreal(Val) / TotalMemory) * 100.0
| summarize AggregatedValue = avg(AvailableMemoryPercentage) by bin(TimeGenerated, 15m), Computer  
``` 

### <a name="disk-alerts"></a>Alertes du disque

#### <a name="metric-alert-rules"></a>Règles d’alerte de métrique

| Cible | Métrique |
|:---|:---|
| Invité Windows | \Logical Disk\( _Total)\% Espace libre<br>\LogicalDisk\(_Total)\Mégaoctets libres |
| Invité Linux | disque/gratuit<br>disque/free_percent |

#### <a name="log-query-alert-rules"></a>Alerte de requête sur un journal

**Disque logique utilisé : tous les disques sur chaque ordinateur** 

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "LogicalDisk" and Name == "FreeSpacePercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId 
```

**Disque logique utilisé : disques individuels** 

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "LogicalDisk" and Name == "FreeSpacePercentage"
| extend Disk=tostring(todynamic(Tags)["vm.azm.ms/mountId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId, Disk 
```

**IOPS des disques logiques**

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms" 
| where Namespace == "LogicalDisk" and Name == "TransfersPerSecond"
| extend Disk=tostring(todynamic(Tags)["vm.azm.ms/mountId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m) ), Computer, _ResourceId, Disk 
```
**Débit de données du disque logique**

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms" 
| where Namespace == "LogicalDisk" and Name == "BytesPerSecond"
| extend Disk=tostring(todynamic(Tags)["vm.azm.ms/mountId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m) , Computer, _ResourceId, Disk 
```

## <a name="network-alerts"></a>Alertes réseau

#### <a name="metric-alert-rules"></a>Règles d’alerte de métrique

| Cible | Métrique |
|:---|:---|
| Invité Windows | \N- Interface réseau \N- Octets envoyés par seconde<br>\LogicalDisk\(_Total)\Mégaoctets libres |
| Invité Linux | disque/gratuit<br>disque/free_percent |

### <a name="log-query-alert-rules"></a>Alerte de requête sur un journal

**Octets reçus sur les interfaces réseau : toutes les interfaces**

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "ReadBytesPerSecond"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId  
```

**Octets reçus sur les interfaces réseau : interfaces individuelles**

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "ReadBytesPerSecond"
| extend NetworkInterface=tostring(todynamic(Tags)["vm.azm.ms/networkDeviceId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId, NetworkInterface 
```

**Octets envoyés sur les interfaces réseau : toutes les interfaces**

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "WriteBytesPerSecond"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId 
```

**Octets envoyés sur les interfaces réseau : interfaces individuelles**

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "WriteBytesPerSecond"
| extend NetworkInterface=tostring(todynamic(Tags)["vm.azm.ms/networkDeviceId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId, NetworkInterface 
```

## <a name="comparison-of-log-query-alert-measures"></a>Comparaison des mesures d’alerte de requête de journal
Pour comparer le comportement des deux mesures d’alerte de journal, voici une procédure pas à pas pour créer une alerte lorsque l’UC d’un ordinateur virtuel dépasse 80%. Les données dont vous avez besoin se trouvent dans la [table InsightsMetrics](/azure/azure-monitor/reference/tables/insightsmetrics). La requête suivante retourne les enregistrements qui doivent être évalués pour l’alerte. Chaque type de règle d’alerte utilise une variante de cette requête.

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms" 
| where Namespace == "Processor" and Name == "UtilizationPercentage" 
```

### <a name="metric-measurement"></a>Mesure de métriques
Les **alertes de mesure de métrique** créent une alerte distincte pour chaque enregistrement dans une requête dont la valeur dépasse un seuil défini dans la règle d’alerte. Ces règles d'alerte sont idéales pour les données de performance des machines virtuelles car elles créent des alertes individuelles pour chaque ordinateur. La requête de journal pour cette mesure doit retourner une valeur pour chaque ordinateur. Le seuil dans la règle d’alerte détermine si la valeur doit déclencher une alerte.

> [!NOTE]
> Les règles d’alerte de journal centrées sur les ressources, actuellement en préversion publique, simplifient les alertes de requête de journal pour les machines virtuelles et remplacent les fonctionnalités actuellement fournies par les requêtes de mesure de métrique. Vous pouvez utiliser la machine en tant que cible de la règle, qui mieux l’identifier comme ressource affectée. Vous pouvez également appliquer une seule règle d’alerte à tous les ordinateurs d’un groupe de ressources ou d’une description spécifique. Quand les alertes de requête de journal du centre de ressources sont mises à la disposition générale, les instructions de ce scénario sont mises à jour.

#### <a name="query"></a>Requête
La requête pour les règles qui utilisent la mesure métrique doit inclure un enregistrement pour chaque ordinateur avec une propriété numérique appelée **AggregatedValue**. Cette valeur est comparée au seuil de la règle d’alerte. La requête n’a pas besoin de comparer cette valeur à un seuil, car le seuil est défini dans la règle d’alerte.

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms" 
| where Namespace == "Processor" and Name == "UtilizationPercentage" 
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer
```

#### <a name="alert-rule"></a>Règle d’alerte
Dans le menu Azure Monitor, sélectionnez **Logs** pour ouvrir Log Analytics. Assurez-vous que l’espace de travail correct est sélectionné pour votre étendue. Si ce n'est pas le cas, cliquez sur **Sélectionner l'étendue** en haut à gauche et sélectionnez l'espace de travail correct. Collez la requête qui présente la logique que vous souhaitez, puis sélectionnez **Exécuter** pour vérifier qu'elle renvoie les bons résultats.

:::image type="content" source="media/monitor-virtual-machines/log-alert-metric-query-results.png" alt-text="Capture d’écran montrant les résultats de la requête d’alerte de mesure métrique." lightbox="media/monitor-virtual-machines/log-alert-metric-query-results.png":::

Sélectionnez **Nouvelle règle d'alerte** pour créer une règle avec la requête actuelle. La règle utilise votre espace de travail pour la **Ressource**.

Sélectionnez **Condition** pour visualiser la configuration. La requête est déjà renseignée avec une vue graphique de la valeur renvoyée par la requête pour chaque ordinateur. Sélectionnez l'ordinateur dans la liste déroulante **Pivoté sur**.

Faites défiler l'écran vers le bas jusqu'à la **logique d'alerte**, puis sélectionnez **Mesure métrique** pour la propriété **Basé sur**. Parce que vous voulez être alerté lorsque l'utilisation dépasse 80 %, définissez la **valeur Agrégée** sur **Plus que** et la **valeur Seuil** sur **80**.

Faites défiler l'écran vers le bas jusqu'à la **logique d'alerte**, puis sélectionnez **Mesure métrique** pour la propriété **Basé sur**. Fournissez une valeur de **Seuil** à comparer à la valeur renvoyée par la requête. Dans cet exemple, utilisez **80**. Dans **Déclencher l’alerte selon**, spécifiez le nombre de fois où le seuil doit être dépassé avant la création d’une alerte. Par exemple, cela ne vous pose probablement pas de problème si le processeur dépasse un seuil une fois puis revient à la normale, mais cela peut être inquiétant s’il continue de dépasser le seuil sur plusieurs mesures consécutives. Pour cet exemple, définissez les **brèches consécutives** à **3**.

Faites défiler la liste jusqu'à **Évalué sur la base de**. La **période** spécifie la durée de la requête. Spécifiez une valeur de **15** minutes, ce qui signifie que la requête utilise uniquement les données collectées au cours des 15 dernières minutes. **Fréquence** spécifie la fréquence d’exécution de la requête. Une valeur inférieure rend la règle d’alerte plus réactive, mais a également un coût plus élevé. Spécifiez **15** pour exécuter la requête toutes les 15 minutes.

:::image type="content" source="media/monitor-virtual-machines/log-alert-metric-rule.png" alt-text="Capture d’écran montrant les résultats de la requête d’alerte de mesure métrique." lightbox="media/monitor-virtual-machines/log-alert-metric-rule.png":::

### <a name="number-of-results-rule"></a>Règle du nombre de résultats
La règle du **nombre de résultats** crée une seule alerte lorsqu'une requête renvoie au moins un nombre spécifié d'enregistrements. La requête de journal dans ce type de règle d’alerte identifie généralement la condition d’alerte, tandis que le seuil de la règle d’alerte détermine si un nombre suffisant d’enregistrements est retourné.

#### <a name="query"></a>Requête
Dans cet exemple, le seuil d’utilisation de l’UC est inclus dans la requête. Le nombre d’enregistrements renvoyés par la requête est le nombre d’ordinateurs dépassant ce seuil. Le seuil de la règle d’alerte est le nombre minimal d’ordinateurs requis pour déclencher l’alerte. Si vous souhaitez une alerte quand une seule machine est erronée, le seuil de la règle d’alerte est égal à zéro.

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms" 
| where Namespace == "Processor" and Name == "UtilizationPercentage" 
| summarize AverageUtilization = avg(Val) by Computer
| where AverageUtilization > 80
```

#### <a name="alert-rule"></a>Règle d’alerte
Dans le menu Azure Monitor, sélectionnez **Logs** pour ouvrir Log Analytics. Assurez-vous que l’espace de travail correct est sélectionné pour votre étendue. Si ce n'est pas le cas, cliquez sur **Sélectionner l'étendue** en haut à gauche et sélectionnez l'espace de travail correct. Collez la requête qui présente la logique que vous souhaitez, puis sélectionnez **Exécuter** pour vérifier qu'elle renvoie les bons résultats. Vous n'avez probablement pas de machine qui dépasse actuellement le seuil, alors passez temporairement à un seuil inférieur pour vérifier les résultats. Définissez ensuite le seuil approprié avant de créer la règle d’alerte.

:::image type="content" source="media/monitor-virtual-machines/log-alert-number-query-results.png" alt-text="Capture d’écran montrant le nombre de résultats de la requête d’alerte de résultats." lightbox="media/monitor-virtual-machines/log-alert-number-query-results.png":::

Sélectionnez **Nouvelle règle d'alerte** pour créer une règle avec la requête actuelle. La règle utilise votre espace de travail pour la **Ressource**.

Sélectionnez **Condition** pour visualiser la configuration. La requête est déjà renseignée avec une vue graphique du nombre d’enregistrements retournés à partir de cette requête au cours des dernières minutes. 

Faites défiler l'écran vers le bas jusqu'à la **logique d'alerte**, puis sélectionnez **Mesure métrique** pour la propriété **Basé sur**. Pour cet exemple, vous souhaitez une alerte si des enregistrements sont retournés, ce qui signifie qu’au moins une machine virtuelle a un processeur supérieur à 80%. Sélectionnez **Plus grand** que pour l'**Opérateur** et **0** pour la **valeur de Seuil**.

Faites défiler la liste jusqu'à **Évalué sur la base de**. La **période** spécifie la durée de la requête. Spécifiez une valeur de **15** minutes, ce qui signifie que la requête utilise uniquement les données collectées au cours des 15 dernières minutes. **Fréquence** spécifie la fréquence d’exécution de la requête. Une valeur inférieure rend la règle d’alerte plus réactive, mais a également un coût plus élevé. Spécifiez **15** pour exécuter la requête toutes les 15 minutes.

:::image type="content" source="media/monitor-virtual-machines/log-alert-number-rule.png" alt-text="Capture d’écran montrant le nombre de résultats de la requête d’alerte de résultats." lightbox="media/monitor-virtual-machines/log-alert-number-rule.png":::

## <a name="next-steps"></a>Étapes suivantes

* [Surveiller les charges de travail en cours d’exécution sur des machines virtuelles.](monitor-virtual-machine-workloads.md)
* [Analyser les données d’analyse collectées pour les machines virtuelles.](monitor-virtual-machine-analyze.md)
