---
title: Inclut le fichier
description: Inclut le fichier
services: service-bus-messaging, event-hubs
author: spelluru
ms.service: service-bus-messaging, event-hubs
ms.topic: include
ms.date: 12/12/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 72c98f01224e70febb5ae6189682f1275ca811f6
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132354207"
---
## <a name="what-is-a-replication-task"></a>Qu’est-ce qu’une tâche de réplication ?

Une tâche de réplication reçoit des événements d’une source et les transfère à une cible. La plupart des tâches de réplication transfèrent des événements non modifiés et, au maximum, effectuent le mappage entre les structures de métadonnées si les protocoles source et cible diffèrent.

Les tâches de réplication sont généralement sans état. Cela signifie qu’elles ne partagent pas l’état ou d’autres effets secondaires sur les exécutions séquentielles ou parallèles d’une tâche. C’est également vrai pour le traitement par lot et le chaînage, qui peuvent être implémentés, en plus de l’état existant d’un flux.

Cela différencie les tâches de réplication des tâches d’agrégation (généralement associées à un état). Celles-ci relèvent du domaine des infrastructures et services d’analyse tels qu’[Azure Stream Analytics](../articles/stream-analytics/stream-analytics-introduction.md).

## <a name="replication-applications-and-tasks-in-azure-functions"></a>Applications et tâches de réplication dans Azure Functions

Dans Azure Functions, une tâche de réplication est implémentée à l’aide d’un [déclencheur](../articles/azure-functions/functions-triggers-bindings.md) qui acquiert des messages d’entrée à partir d’une source configurée, et d’une [liaison de sortie](../articles/azure-functions/functions-triggers-bindings.md#binding-direction) qui transfère les messages copiés de la source vers une cible configurée.

| Déclencheur  | Output |
|----------|--------|
| [Déclencheur Azure Event Hubs](../articles/azure-functions/functions-bindings-event-hubs-trigger.md?tabs=csharp) | [Liaison de sortie Azure Event Hubs](../articles/azure-functions/functions-bindings-event-hubs-output.md?tabs=csharp) |
| [Déclencheur Azure Service Bus](../articles/azure-functions/functions-bindings-service-bus-trigger.md?tabs=csharp) | [Liaison de sortie Azure Service Bus](../articles/azure-functions/functions-bindings-service-bus-output.md?tabs=csharp) |
| [Déclencheur Azure IoT Hub](../articles/azure-functions/functions-bindings-event-iot-trigger.md?tabs=csharp) | [Liaison de sortie Azure IoT Hub](../articles/azure-functions/functions-bindings-event-iot-output.md?tabs=csharp) |
| [Déclencheur Azure Event Grid](../articles/azure-functions/functions-bindings-event-grid-trigger.md?tabs=csharp) | [Liaison de sortie Azure Event Grid](../articles/azure-functions/functions-bindings-event-grid-output.md?tabs=csharp) |
| [Déclencheur Stockage File d’attente Azure](../articles/azure-functions/functions-bindings-storage-queue-trigger.md?tabs=csharp) | [Liaison de sortie Stockage File d’attente Azure](../articles/azure-functions/functions-bindings-storage-queue-output.md?tabs=csharp) |
| [Déclencheur Apache Kafka](https://github.com/azure/azure-functions-kafka-extension) | [Liaison de sortie Apache Kafka](https://github.com/azure/azure-functions-kafka-extension) |
| [Déclencheur RabbitMQ](https://github.com/azure/azure-functions-rabbitmq-extension) | [Liaison de sortie RabbitMQ](https://github.com/azure/azure-functions-rabbitmq-extension) |
| | [Liaison de sortie Azure Notification Hubs](../articles/azure-functions/functions-bindings-notification-hubs.md) |
| | [Liaison de sortie Azure SignalR Service](../articles/azure-functions/functions-bindings-signalr-service-output.md?tabs=csharp) |
| | [Liaison de sortie Twilio SendGrid](../articles/azure-functions/functions-bindings-sendgrid.md?tabs=csharp) |

Les tâches de réplication sont déployées dans l’application de réplication à l’aide des mêmes méthodes de déploiement que toute autre application Azure Functions. Vous pouvez configurer plusieurs tâches dans la même application.

Avec Azure Functions Premium, plusieurs applications de réplication peuvent partager le même pool de ressources sous-jacent (appelé plan App Service). Vous pouvez ainsi facilement colocaliser les tâches de réplication écrites en .NET avec des tâches de réplication écrites en Java, par exemple. Il s’agit d’un aspect important si vous souhaitez tirer parti de bibliothèques spécifiques telles qu’Apache Camel qui ne sont disponibles que pour Java. Il en va de même si celles-ci constituent la meilleure option pour un chemin d’intégration particulier, même si vous préféreriez généralement utiliser d’autres langage et runtime pour vos autres tâches de réplication.

Lorsque c’est possible, vous devez préférer les déclencheurs axés sur le traitement par lot aux déclencheurs délivrant des événements ou messages individuels. Vous devez par ailleurs toujours obtenir la structure complète des événements ou messages plutôt que de vous appuyer sur les [expressions de liaison de paramètre](../articles/azure-functions/functions-bindings-expressions-patterns.md) d’Azure Functions.

Le nom de la fonction doit refléter la paire de la source et la cible que vous connectez, et vous devez préfixer les références aux chaînes de connexion ou à d’autres éléments de configuration dans les fichiers de configuration d’application portant ce nom.

### <a name="data-and-metadata-mapping"></a>Mappage des données et métadonnées

Une fois que vous avez choisi une paire de déclencheur d’entrée et de liaison de sortie, vous devez effectuer un mappage entre les différents types d’événements ou de messages, sauf si le type de votre déclencheur et la sortie sont identiques.

Pour les tâches de réplication simples qui copient des messages entre Event Hubs et Service Bus, il n’est pas nécessaire d’écrire votre propre code, mais vous pouvez vous appuyer sur une [bibliothèque d’utilitaires fournie](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/src/Azure.Messaging.Replication) avec les exemples de réplication.

### <a name="retry-policy"></a>Stratégie de nouvelles tentatives

Pour éviter de perdre des données pendant l’événement de disponibilité de part et d’autre d’une fonction de réplication, vous devez configurer la stratégie de nouvelles tentatives de façon à ce qu’elle soit robuste. Reportez-vous à la [documentation d’Azure Functions sur les nouvelles tentatives](../articles/azure-functions/functions-bindings-error-pages.md) pour configurer la stratégie de nouvelles tentatives.

Les paramètres de stratégie choisis pour les exemples de projets dans le [référentiel d’exemples](https://github.com/Azure-Samples/azure-messaging-replication-dotnet) configurent une stratégie de backoff exponentiel avec des intervalles avant nouvelle tentative de 5 secondes à 15 minutes, et un nombre infini de nouvelles tentatives pour éviter de perdre des données.

Pour Service Bus, consultez la section [Utilisation de la prise en charge des nouvelles tentatives en plus de la résilience de déclencheur](../articles/azure-functions/functions-bindings-error-pages.md#using-retry-support-on-top-of-trigger-resilience) pour comprendre l’interaction des déclencheurs et connaître le nombre maximal de remises défini pour la file d’attente.

### <a name="setting-up-a-replication-application-host"></a>Configuration d’un hôte d’application de réplication

Une application de réplication est un hôte d’exécution pour une ou plusieurs tâches de réplication.

Il s’agit d’une application Azure Functions configurée pour s’exécuter sur le plan de consommation ou (recommandé) sur un plan Azure Functions Premium. Toutes les applications de réplication doivent être exécutées sous une [identité managée affectée par le système ou l’utilisateur](../articles/app-service/overview-managed-identity.md).

Les modèles ARM (Azure Resource Manager) liés créent et configurent une application de réplication avec :

* Un compte de stockage Azure pour le suivi de la progression de la réplication et les journaux.
* Une identité managée affectée par le système.
* Intégration d’Azure Monitor et d’Application Insights pour la surveillance.

Les applications de réplication qui doivent accéder au service Event Hubs lié à un réseau virtuel (VNet) Azure doivent utiliser le plan Azure Functions Premium et être configurées pour rejoindre le même réseau virtuel, qui est également l’une des options disponibles.

| | Déployer | Visualiser |
|--|--|--|
| **Plan de consommation Azure Functions** | [![Déployer sur Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2Fconsumption%2Fazuredeploy.json)|[![Visualiser](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/visualizebutton.svg?sanitize=true)](http://armviz.io/#/?load=https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2Fconsumption%2Fazuredeploy.json) |
| **Plan Premium Azure Functions** |[![Déployer sur Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2Fpremium%2Fazuredeploy.json) | [![Visualiser](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/visualizebutton.svg?sanitize=true)](http://armviz.io/#/?load=https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2Fpremium%2Fazuredeploy.json) |
| **Plan Azure Functions Premium avec VNet** | [![Déployer sur Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2Fpremium-vnet%2Fazuredeploy.json)|[![Visualiser](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/visualizebutton.svg?sanitize=true)](http://armviz.io/#/?load=https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2Fpremium-vnet%2Fazuredeploy.json) |

### <a name="examples"></a>Exemples

Le [référentiel d’exemples](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/) contient plusieurs exemples de tâches de réplication qui copient des événements entre des Event Hubs et/ou des entités Service Bus.

Pour copier un événement entre des Event Hubs, vous utilisez le déclencheur et la liaison de sortie Event Hub :

```csharp
[FunctionName("telemetry")]
[ExponentialBackoffRetry(-1, "00:00:05", "00:05:00")]
public static Task Telemetry(
    [EventHubTrigger("telemetry", ConsumerGroup = "$USER_FUNCTIONS_APP_NAME.telemetry", Connection = "telemetry-source-connection")] EventData[] input,
    [EventHub("telemetry-copy", Connection = "telemetry-target-connection")] EventHubClient outputClient,
    ILogger log)
{
    return EventHubReplicationTasks.ForwardToEventHub(input, outputClient, log);
}
```

Pour copier des messages entre des entités Service Bus, vous utilisez le déclencheur et la liaison de sortie Service Bus :

```csharp
[FunctionName("jobs-transfer")]
[ExponentialBackoffRetry(-1, "00:00:05", "00:05:00")]
public static Task JobsTransfer(
    [ServiceBusTrigger("jobs-transfer", Connection = "jobs-transfer-source-connection")] Message[] input,
    [ServiceBus("jobs", Connection = "jobs-target-connection")] IAsyncCollector<Message> output,
    ILogger log)
{
    return ServiceBusReplicationTasks.ForwardToServiceBus(input, output, log);
}
```

Les méthodes d’assistance peuvent faciliter la réplication entre Event Hubs et Service Bus :

| Source      | Cible      | Point d'entrée |
|-------------|-------------|------------------------------------------------------------------------
| Event Hub   | Event Hub   | `Azure.Messaging.Replication.EventHubReplicationTasks.ForwardToEventHub`
| Event Hub   | Service Bus | `Azure.Messaging.Replication.EventHubReplicationTasks.ForwardToServiceBus`
| Service Bus | Event Hub   | `Azure.Messaging.Replication.ServiceBusReplicationTasks.ForwardToEventHub`
| Service Bus | Service Bus | `Azure.Messaging.Replication.ServiceBusReplicationTasks.ForwardToServiceBus`

### <a name="monitoring"></a>Supervision

Pour plus d’informations sur la surveillance de votre application de réplication, reportez-vous à la section [Surveillance](../articles/azure-functions/configure-monitoring.md) de la documentation d’Azure Functions.

Un outil visuel particulièrement utile pour la surveillance des tâches de réplication est la [cartographie d’application](../articles/azure-monitor/app/app-map.md) d’Application Insights. Celle-ci est générée automatiquement à partir des informations de surveillance capturées et permet d’explorer la fiabilité et les performances des transferts source et cible de la tâche de réplication.

Pour consulter des informations de diagnostic immédiates, vous pouvez utiliser l’outil de portail [Métriques temps réel](../articles/azure-monitor/app/live-stream.md). Celui-ci fournit une visualisation à faible latence des détails des journaux.

## <a name="next-steps"></a>Étapes suivantes

* [Déploiements d’Azure Functions](../articles/azure-functions/functions-deployment-technologies.md)
* [Diagnostics d’Azure Functions](../articles/azure-functions/functions-diagnostics.md)
* [Options de mise en réseau d’Azure Functions](../articles/azure-functions/functions-networking-options.md)
* [Azure Application Insights](../articles/azure-monitor/app/app-insights-overview.md)
