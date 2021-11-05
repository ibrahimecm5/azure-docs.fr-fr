---
title: 'Analytique sur les bases de connaissances : réponses aux questions personnalisées'
titleSuffix: Azure Cognitive Services
description: Le service Réponses aux questions personnalisées utilise la journalisation des diagnostics Azure pour stocker les données de télémétrie et les journaux de conversation
services: cognitive-services
manager: nitinme
displayName: chat history, history, chat logs, logs
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: language-service-question-answering, ignite-fall-2021
ms.openlocfilehash: fba1481ffe7aa45514e53813ccf7b14ea01bd6bf
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131097739"
---
# <a name="get-analytics-for-your-knowledge-base"></a>Obtenir une analyse de votre base de connaissances

Le service Réponses aux questions personnalisées utilise la journalisation des diagnostics Azure pour stocker les données de télémétrie et les journaux de conversation. Suivez les étapes ci-dessous pour exécuter des exemples de requêtes afin d’analyser l’utilisation de votre projet de réponses aux questions personnalisées.

1. [Activez la journalisation des diagnostics](../../../diagnostic-logging.md) pour votre ressource de langue avec la réponse à une question personnalisée activée.

2. À l’étape précédente, sélectionnez **Trace** en plus d’**Audit, RequestResponse et AllMetrics** pour la journalisation.

    ![Activer la journalisation de suivi dans les réponses aux questions personnalisées](../media/analytics/qnamaker-v2-enable-trace-logging.png)

## <a name="kusto-queries"></a>Requêtes Kusto

### <a name="chat-log"></a>Conversations

```kusto
// All QnA Traffic
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| where OperationName=="QnAMaker GenerateAnswer" // This OperationName is valid for custom question answering enabled resources
| extend answer_ = tostring(parse_json(properties_s).answer)
| extend question_ = tostring(parse_json(properties_s).question)
| extend score_ = tostring(parse_json(properties_s).score)
| extend kbId_ = tostring(parse_json(properties_s).kbId)
| project question_, answer_, score_, kbId_
```

### <a name="traffic-count-per-knowledge-base-and-user-in-a-time-period"></a>Nombre de trafics par base de connaissances et utilisateur pendant une période

```kusto
// Traffic count per KB and user in a time period
let startDate = todatetime('2019-01-01');
let endDate = todatetime('2020-12-31');
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| where OperationName=="QnAMaker GenerateAnswer" // This OperationName is valid for custom question answering enabled resources
| where TimeGenerated <= endDate and TimeGenerated >=startDate
| extend kbId_ = tostring(parse_json(properties_s).kbId)
| extend userId_ = tostring(parse_json(properties_s).userId)
| summarize ChatCount=count() by bin(TimeGenerated, 1d), kbId_, userId_
```

### <a name="latency-of-generateanswer-api"></a>Latence de l’API GenerateAnswer

```kusto
// Latency of GenerateAnswer
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| where OperationName=="Generate Answer"
| project TimeGenerated, DurationMs
| render timechart
```

### <a name="average-latency-of-all-operations"></a>Latence moyenne de toutes les opérations

```kusto
// Average Latency of all operations
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| project DurationMs, OperationName
| summarize count(), avg(DurationMs) by OperationName
| render barchart
```

### <a name="unanswered-questions"></a>Questions sans réponse

```kusto
// All unanswered questions
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| where OperationName=="QnAMaker GenerateAnswer" // This OperationName is valid for custom question answering enabled resources
| extend answer_ = tostring(parse_json(properties_s).answer)
| extend question_ = tostring(parse_json(properties_s).question)
| extend score_ = tostring(parse_json(properties_s).score)
| extend kbId_ = tostring(parse_json(properties_s).kbId)
| where score_ == 0
| project question_, answer_, score_, kbId_
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Choisir la capacité](../../../qnamaker/how-to/improve-knowledge-base.md)
