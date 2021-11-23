---
title: Azure Communication Services - Préversion de Log Analytics
titleSuffix: An Azure Communication Services concept document
description: À propos de l’utilisation de Log Analytics pour les journaux de résumé des appels et de diagnostic des appels
author: timmitchell
services: azure-communication-services
ms.author: timmitchell
ms.date: 10/25/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.subservice: data
ms.openlocfilehash: 05ae40fc974228bf93641be3786294aab59c243a
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132138861"
---
# <a name="log-analytics-for-communications-services-preview"></a>Log Analytics pour Communications Services en préversion

## <a name="overview-and-access"></a>Vue d’ensemble et accès

Avant de pouvoir tirer parti de [Log Analytics](/azure/azure-monitor/logs/log-analytics-overview) pour vos journaux Communication Services, vous devez d’abord suivre les étapes décrites dans [Activer la journalisation dans les paramètres de diagnostic](enable-logging.md). Une fois que vous avez activé vos journaux et un [espace de travail log Analytics](/azure/azure-monitor/logs/design-logs-deployment), vous aurez accès à de nombreux [packs de requêtes par défaut](/azure/azure-monitor/logs/query-packs#default-query-pack) utiles qui vous aideront à visualiser et à comprendre rapidement les données disponibles dans vos journaux (voir la description ci-dessous). Via Log Analytics, vous pouvez également accéder à d’autres insights Communication Services via des classeurs Azure Monitor (voir [Insights Communication Services](insights.md)), la possibilité de créer vos propres requêtes et classeurs, l’accès via l’[API REST](https://dev.loganalytics.io/) à n’importe quelle requête.

### <a name="access"></a>Access
Vous pouvez accéder aux requêtes en commençant par la page de ressources Communication Services, puis en cliquant sur « Journaux » dans le volet de navigation gauche de la section Surveiller :

:::image type="content" source="media\log-analytics\access-log-analytics.png" alt-text="Navigation Log Analytics":::

À partir de là, un écran modal contenant tous les [packs de requêtes par défaut](/azure/azure-monitor/logs/query-packs#default-query-pack) disponibles pour vos services de communication s’affiche, avec la liste des packs de requêtes disponibles à gauche.

:::image type="content" source="media\log-analytics\log-analytics-modal-resource.png" alt-text="Modal Requêtes Log Analytics" lightbox="media\log-analytics\log-analytics-modal-resource.png":::

Si vous fermez l’écran modal, vous pouvez toujours accéder aux différents packs de requêtes, accéder directement aux données sous la forme de tables en fonction du schéma des journaux et des métriques que vous avez activés dans votre paramètre de diagnostic. Ici, vous pouvez créer vos propres requêtes à partir des données à l’aide de [KQL (Kusto)](/azure/data-explorer/kusto/query/). Pour plus d’informations sur l’utilisation, la modification et la création de requêtes, consultez [Requêtes Log Analytics](/azure/azure-monitor/logs/queries).

:::image type="content" source="media\log-analytics\log-analytics-queries-resource.png" alt-text="Requêtes Log Analytics dans la ressource" lightbox="media\log-analytics\log-analytics-queries-resource.png":::

:::image type="content" source="media\log-analytics\log-analytics-tables-resource.png" alt-text="Tables Log Analytics dans la ressource" lightbox="media\log-analytics\log-analytics-tables-resource.png":::

## <a name="default-query-packs-for-call-summary-and-call-diagnostic-logs"></a>Packs de requêtes par défaut pour le résumé des appels et les journaux de diagnostic d’appel
Voici les descriptions de chaque requête du [pack de requêtes par défaut](/azure/azure-monitor/logs/query-packs#default-query-pack), pour les [journaux de résumé des appels et de diagnostic des appels](call-logs-azure-monitor.md), notamment des exemples de code et des exemples de sorties pour chaque requête disponible :
### <a name="call-overview-queries"></a>Requêtes de vue d’ensemble des appels
#### <a name="number-of-participants-per-call"></a>Nombre de participants par appel

```
// Count number of calls and participants,
// and print average participants per call
ACSCallSummary
| distinct CorrelationId, ParticipantId, EndpointId
| summarize num_participants=count(), num_calls=dcount(CorrelationId)
| extend avg_participants = todecimal(num_participants) / todecimal(num_calls)
```

Exemple de sortie :

:::image type="content" source="media\log-analytics\call-overview-sample.png" alt-text="requête de vue d’ensemble des appels":::

#### <a name="number-of-participants-per-group-call"></a>Nombre de participants par appel de groupe

```
// Count number of participants per group call
ACSCallSummary
| where CallType == 'Group'
| distinct CorrelationId, ParticipantId
| summarize num_participants=count() by CorrelationId
| summarize participant_counts=count() by num_participants
| order by num_participants asc 
| render columnchart with  (xcolumn = num_participants, title="Number of participants per group call")
```

Exemple de sortie :

:::image type="content" source="media\log-analytics\participants-group-sample.png" alt-text="requête des participants par appel de groupe":::

#### <a name="ratio-of-call-types"></a>Ratio des types d’appel

```
// Ratio of call types
ACSCallSummary
| summarize call_types=dcount(CorrelationId) by CallType
| render piechart title="Call Type Ratio"

```

Exemple de sortie :

:::image type="content" source="media\log-analytics\ratio-call-types-sample.png" alt-text="requête de ratio des types d’appel":::

#### <a name="call-duration-distribution"></a>Répartition de la durée d’appel

```
// Call duration histogram
ACSCallSummary
| distinct CorrelationId, CallDuration
|summarize duration_counts=count() by CallDuration
| order by CallDuration asc
| render columnchart with (xcolumn = CallDuration, title="Call duration histogram")
```

Exemple de sortie :

:::image type="content" source="media\log-analytics\call-duration-sample.png" alt-text="requête de durée d’appel":::

#### <a name="call-duration-percentiles"></a>Centiles de durée d’appel

```
// Call duration percentiles
ACSCallSummary
| distinct CorrelationId, CallDuration
| summarize avg(CallDuration), percentiles(CallDuration, 50, 90, 99)
```

Exemple de sortie :

:::image type="content" source="media\log-analytics\call-duration-percentile-example.png" alt-text="requête de centiles de durée d’appel":::

### <a name="endpoint-information-queries"></a>Requêtes sur les informations sur le point de terminaison

#### <a name="number-of-endpoints-per-call"></a>Nombre de points de terminaison par appel

```
// Count number of calls and endpoints,
// and print average endpoints per call
ACSCallSummary
| distinct CorrelationId, EndpointId
| summarize num_endpoints=count(), num_calls=dcount(CorrelationId)
| extend avg_endpoints = todecimal(num_endpoints) / todecimal(num_calls)
```

Exemple de sortie :

:::image type="content" source="media\log-analytics\endpoints-per-call.png" alt-text="requête de points de terminaison par appel":::

#### <a name="ratio-of-sdk-versions"></a>Ratio des versions du SDK

```
// Ratio of SDK Versions
ACSCallSummary
| distinct CorrelationId, ParticipantId, EndpointId, SdkVersion
| summarize sdk_counts=count() by SdkVersion
| order by SdkVersion asc
| render piechart title="SDK Version Ratio"
```

Exemple de sortie :

:::image type="content" source="media\log-analytics\sdk-ratio-sample.png" alt-text="Graphique à secteurs présentant le ratio des versions du SDK."::: :::image type="content" source="media\log-analytics\sdk-ratio-table-sample.png" alt-text="Tableau répertoriant les versions du SDK":::

#### <a name="ratio-of-os-versions-simplified-os-name"></a>Ratio des versions de système d’exploitation (nom simplifié du système d’exploitation)

```
// Ratio of OS Versions (simplified OS name)
ACSCallSummary
| distinct CorrelationId, ParticipantId, EndpointId, OsVersion
| extend simple_os = case(  indexof(OsVersion, "Android") != -1, tostring(split(OsVersion, ";")[0]),
                            indexof(OsVersion, "Darwin") != -1, tostring(split(OsVersion, ":")[0]),
                            indexof(OsVersion, "Windows") != -1, tostring(split(OsVersion, ".")[0]),
                            OsVersion
                        )
| summarize os_counts=count() by simple_os
| order by simple_os asc
| render piechart title="OS Version Ratio"
```

Exemple de sortie :

:::image type="content" source="media\log-analytics\os-version-graph.png" alt-text="Graphique à secteurs illustrant les ratios du système d’exploitation":::
:::image type="content" source="media\log-analytics\os-version-table.png" alt-text="Tableau répertoriant les versions de système d’exploitation":::

### <a name="media-stream-queries"></a>Requêtes de flux de média
#### <a name="streams-per-call"></a>Flux par appel

```
// Count number of calls and streams,
// and print average streams per call
ACSCallDiagnostics
| summarize num_streams=count(), num_calls=dcount(CorrelationId)
| extend avg_streams = todecimal(num_streams) / todecimal(num_calls)
```
Exemple de sortie :

:::image type="content" source="media\log-analytics\streams-call-output.png" alt-text="requête de flux par appel":::

#### <a name="streams-per-call-histogram"></a>Histogramme de flux par appel

```
// Distribution of streams per call
ACSCallDiagnostics
| summarize streams_per_call=count() by CorrelationId
| summarize stream_counts=count() by streams_per_call
| order by streams_per_call asc
| render columnchart title="Streams per call histogram"
```

:::image type="content" source="media\log-analytics\streams-call-histogram.png" alt-text="histogramme de flux par appel":::

#### <a name="ratio-of-media-types"></a>Ratio des types de médias

```
// Ratio of media types by call
ACSCallDiagnostics
| summarize media_types=count() by MediaType
| render piechart title="Media Type Ratio"
```

:::image type="content" source="media\log-analytics\media-type-ratio.png" alt-text="Graphique à secteurs illustrant les ratios de type de médias":::

### <a name="quality-metrics-queries"></a>Requêtes sur les mesures de qualité

#### <a name="average-telemetry-values"></a>Moyenne des valeurs de télémétrie

```
// Average telemetry values over all streams
ACSCallDiagnostics
| summarize Avg_JitterAvg=avg(JitterAvg),
            Avg_JitterMax=avg(JitterMax),
            Avg_RoundTripTimeAvg=avg(RoundTripTimeAvg),
            Avg_RoundTripTimeMax=avg(RoundTripTimeMax),
            Avg_PacketLossRateAvg=avg(PacketLossRateAvg),
            Avg_PacketLossRateMax=avg(PacketLossRateMax)
```

:::image type="content" source="media\log-analytics\avg-telemetry-values.png" alt-text="moyenne des valeurs de télémétrie":::

#### <a name="jitteravg-histogram"></a>Histogramme JitterAvg

```
// Jitter Average Histogram
ACSCallDiagnostics
| where isnotnull(JitterAvg)
| summarize JitterAvg_counts=count() by JitterAvg
| order by JitterAvg asc
| render columnchart with (xcolumn = JitterAvg, title="JitterAvg histogram")
```

:::image type="content" source="media\log-analytics\jitteravg-histogram.png" alt-text="histogramme de gigue moyenne":::

#### <a name="jittermax-histogram"></a>Histogramme JitterMax

```
// Jitter Max Histogram
ACSCallDiagnostics
| where isnotnull(JitterMax)
|summarize JitterMax_counts=count() by JitterMax
| order by JitterMax asc
| render columnchart with (xcolumn = JitterMax, title="JitterMax histogram")
```

:::image type="content" source="media\log-analytics\jittermax-histogram.png" alt-text="histogramme de gigue maximale":::

#### <a name="packetlossrateavg-histogram"></a>Histogramme PacketLossRateAvg
```
// PacketLossRate Average Histogram
ACSCallDiagnostics
| where isnotnull(PacketLossRateAvg)
|summarize PacketLossRateAvg_counts=count() by bin(PacketLossRateAvg, 0.01)
| order by PacketLossRateAvg asc
| render columnchart with (xcolumn = PacketLossRateAvg, title="PacketLossRateAvg histogram")
```

:::image type="content" source="media\log-analytics\packetloss-avg-histogram.png" alt-text="histogramme de moyenne de perte de paquets":::

#### <a name="packetlossratemax-histogram"></a>Histogramme PacketLossRateMax
```
// PacketLossRate Max Histogram
ACSCallDiagnostics
| where isnotnull(PacketLossRateMax)
|summarize PacketLossRateMax_counts=count() by bin(PacketLossRateMax, 0.01)
| order by PacketLossRateMax asc
| render columnchart with (xcolumn = PacketLossRateMax, title="PacketLossRateMax histogram")
```

:::image type="content" source="media\log-analytics\packetloss-max-histogram.png" alt-text="histogramme max de perte de paquets":::

#### <a name="roundtriptimeavg-histogram"></a>Histogramme RoundTripTimeAvg
```
// RoundTripTime Average Histogram
ACSCallDiagnostics
| where isnotnull(RoundTripTimeAvg)
|summarize RoundTripTimeAvg_counts=count() by RoundTripTimeAvg
| order by RoundTripTimeAvg asc
| render columnchart with (xcolumn = RoundTripTimeAvg, title="RoundTripTimeAvg histogram")
```

:::image type="content" source="media\log-analytics\rtt-avg-histogram.png" alt-text="histogramme RTT moyen":::

#### <a name="roundtriptimemax-histogram"></a>Histogramme RoundTripTimeMax
```
// RoundTripTime Max Histogram
ACSCallDiagnostics
| where isnotnull(RoundTripTimeMax)
|summarize RoundTripTimeMax_counts=count() by RoundTripTimeMax
| order by RoundTripTimeMax asc
| render columnchart with (xcolumn = RoundTripTimeMax, title="RoundTripTimeMax histogram")
```

:::image type="content" source="media\log-analytics\rtt-max-histogram.png" alt-text="histogramme RTT max":::

#### <a name="poor-jitter-quality"></a>Mauvaise qualité de la gigue
```
// Get proportion of calls with poor quality jitter
// (defined as jitter being higher than 30ms)
ACSCallDiagnostics
| extend JitterQuality = iff(JitterAvg > 30, "Poor", "Good")
| summarize count() by JitterQuality
| render piechart title="Jitter Quality"
```

:::image type="content" source="media\log-analytics\jitter-quality.png" alt-text="qualité de gigue":::


#### <a name="packetlossrate-quality"></a>Qualité PacketLossRate
```
// Get proportion of calls with poor quality packet loss
// rate (defined as packet loss being higher than 10%)
ACSCallDiagnostics
| extend PacketLossRateQuality = iff(PacketLossRateAvg > 0.1, "Poor", "Good")
| summarize count() by PacketLossRateQuality
| render piechart title="Packet Loss Rate Quality"
```

:::image type="content" source="media\log-analytics\packet-loss-quality.png" alt-text="qualité du taux de perte de paquets":::

#### <a name="roundtriptime-quality"></a>Qualité RoundTripTime
```
// Get proportion of calls with poor quality packet loss
// rate (defined as packet loss being higher than 10%)
ACSCallDiagnostics
| extend PacketLossRateQuality = iff(PacketLossRateAvg > 0.1, "Poor", "Good")
| summarize count() by PacketLossRateQuality
| render piechart title="Packet Loss Rate Quality"
```

:::image type="content" source="media\log-analytics\rtt-quality.png" alt-text="qualité RTT":::

### <a name="parameterizable-queries"></a>Requêtes paramétrables

#### <a name="daily-calls-in-the-last-week"></a>Appels quotidiens de la dernière semaine
```
// Histogram of daily calls over the last week
ACSCallSummary
| where CallStartTime > now() - 7d
| distinct CorrelationId, CallStartTime
| extend hour  = floor(CallStartTime, 1d)
| summarize event_count=count() by day
| sort by day asc
| render columnchart title="Number of calls in last week"
```

:::image type="content" source="media\log-analytics\calls-per-week.png" alt-text="appels quotidiens de la dernière semaine":::

#### <a name="calls-per-hour-in-last-day"></a>Appels par heure au cours du dernier jour
```
// Histogram of calls per hour in the last day
ACSCallSummary
| where CallStartTime > now() - 1d
| distinct CorrelationId, CallStartTime
| extend hour = floor(CallStartTime, 1h)
| summarize event_count=count() by hour
| sort by hour asc
| render columnchart title="Number of calls per hour in last day"
```

:::image type="content" source="media\log-analytics\calls-per-hour.png" alt-text="appels par heure au cours du dernier jour":::