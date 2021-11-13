---
title: Liaisons Azure Service Bus pour Azure Functions
description: Apprenez à envoyer des déclencheurs et des liaisons Azure Service Bus dans Azure Functions.
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 02/19/2020
ms.author: cshoe
ms.custom: fasttrack-edit
ms.openlocfilehash: 124e1f104bf7607b63eac916754c86b7b28cd8a2
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131470430"
---
# <a name="azure-service-bus-bindings-for-azure-functions"></a>Liaisons Azure Service Bus pour Azure Functions

Azure Functions s’intègre avec [Azure Service Bus](https://azure.microsoft.com/services/service-bus) via [des déclencheurs et des liaisons](./functions-triggers-bindings.md). L’intégration avec Service Bus vous permet de créer des fonctions qui réagissent et envoient des messages de file d’attente ou de rubrique.

| Action | Type |
|---------|---------|
| Exécuter une fonction durant la création d’un message de file d’attente ou de rubrique Service Bus | [Déclencheur](./functions-bindings-service-bus-trigger.md) |
| Envoyer des messages Azure Service Bus |[Liaison de sortie](./functions-bindings-service-bus-output.md) |

## <a name="add-to-your-functions-app"></a>Ajouter à votre application de fonction

### <a name="functions-2x-and-higher"></a>Functions 2.x et versions ultérieures

Pour utiliser le déclencheur et les liaisons, vous devez référencer le package approprié. Le package NuGet est utilisé pour les bibliothèques de classes .NET, tandis que le bundle d’extensions est utilisé pour tous les autres types d’applications.

| Langage                                        | Ajouter via...                                   | Notes 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | L’installation du [package NuGet], version 4.x | |
| Script C#, Java, JavaScript, Python, PowerShell | L’inscription du [bundle d’extensions]          | Il est recommandé d’utiliser l’[extension Azure Tools] avec Visual Studio Code. |
| Script C# (en ligne uniquement dans le portail Azure)         | Ajout d’une liaison                            | Pour mettre à jour des extensions de liaison existantes sans avoir à republier votre application de fonction, voir [Mettre à jour vos extensions]. |

[Package NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus/
[core tools]: ./functions-run-local.md
[Bundle d’extensions]: ./functions-bindings-register.md#extension-bundles
[Mettre à jour vos extensions]: ./functions-bindings-register.md
[Extension Azure Tools]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

#### <a name="service-bus-extension-5x-and-higher"></a>Extension Service Bus 5.x et versions ultérieures

Une nouvelle version de l’extension de liaisons Service Bus est désormais disponible. Elle introduit la possibilité de [se connecter à l’aide d’une identité plutôt que d’un secret](./functions-reference.md#configure-an-identity-based-connection). Pour obtenir un tutoriel sur la configuration de vos applications de fonction avec des identités managées, consultez le [tutoriel sur la création d’une application de fonction avec des connexions basées sur l’identité](./functions-identity-based-connections-tutorial.md). Pour les applications .NET, la nouvelle version de l’extension change également les types vers lesquels vous pouvez effectuer une liaison, en remplaçant les types `Microsoft.ServiceBus.Messaging` et `Microsoft.Azure.ServiceBus` par des types plus récents [Azure.Messaging.ServiceBus](/dotnet/api/azure.messaging.servicebus).

Cette version de l’extension est disponible en installant le [package NuGet] version 5.x, ou elle peut être ajoutée à partir du groupe d’extensions v3 en ajoutant le code suivant dans votre fichier `host.json` :

```json
{
  "version": "2.0",
  "extensionBundle": {
    "id": "Microsoft.Azure.Functions.ExtensionBundle",
    "version": "[3.3.0, 4.0.0)"
  }
}
```

Pour en savoir plus, consultez [Mettre à jour vos extensions].

[core tools]: ./functions-run-local.md
[Bundle d’extensions]: ./functions-bindings-register.md#extension-bundles
[Package NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus
[Mettre à jour vos extensions]: ./functions-bindings-register.md
[Extension Azure Tools]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

Les applications Functions 1.x ont automatiquement une référence au package NuGet [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs), version 2.x.


<a name="host-json"></a>  

## <a name="hostjson-settings"></a>Paramètres host.json

[!INCLUDE [functions-host-json-section-intro](../../includes/functions-host-json-section-intro.md)]

> [!NOTE]
> Pour obtenir une référence de host.json dans Functions 1.x, consultez [Informations de référence sur le fichier host.json pour Azure Functions 1.x](functions-host-json-v1.md).

```json
{
    "version": "2.0",
    "extensions": {
        "serviceBus": {
            "prefetchCount": 100,
            "messageHandlerOptions": {
                "autoComplete": true,
                "maxConcurrentCalls": 32,
                "maxAutoRenewDuration": "00:05:00"
            },
            "sessionHandlerOptions": {
                "autoComplete": false,
                "messageWaitTimeout": "00:00:30",
                "maxAutoRenewDuration": "00:55:00",
                "maxConcurrentSessions": 16
            },
            "batchOptions": {
                "maxMessageCount": 1000,
                "operationTimeout": "00:01:00",
                "autoComplete": true
            }
        }
    }
}
```

Si vous avez défini `isSessionsEnabled` sur `true`, `sessionHandlerOptions` est respecté.  Si vous avez défini `isSessionsEnabled` sur `false`, `messageHandlerOptions` est respecté.

|Propriété  |Default | Description |
|---------|---------|---------|
|prefetchCount|0|Obtient ou définit le nombre de messages que le destinataire des messages peut demander simultanément.|
|messageHandlerOptions.maxAutoRenewDuration|00:05:00|Durée maximale pendant laquelle le verrouillage de message doit être renouvelé automatiquement.|
|messageHandlerOptions.autoComplete|true|Indique si le déclencheur doit terminer l’appel automatiquement après le traitement, ou si le code de la fonction termine manuellement l’appel.<br><br>La définition de `false` est prise en charge uniquement dans C# .<br><br>Si la valeur est `true`, le déclencheur termine automatiquement le message si l’exécution de la fonction se termine correctement et abandonne le message dans le cas contraire.<br><br>Lorsque la valeur est `false`, il vous incombe d’appeler des méthodes [MessageReceiver](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver) pour terminer, abandonner ou mettre au rebut le message. Si une exception est levée (et qu’aucune des méthodes `MessageReceiver` n’est appelée), le verrou reste. Une fois le verrou expiré, le message est de nouveau mis en file d’attente avec `DeliveryCount` incrémenté, et le verrou est automatiquement renouvelé.<br><br>Dans les fonctions non C#, les exceptions de la fonction entraînent l’appel par le runtime de `abandonAsync` en arrière-plan. Si aucune exception ne se produit, `completeAsync` est appelé en arrière-plan. |
|messageHandlerOptions.maxConcurrentCalls|16|Nombre maximal d’appels simultanés pour le rappel que la pompe de messages doit initier par instance mise à l’échelle. Par défaut, le runtime Functions traite plusieurs messages simultanément.|
|sessionHandlerOptions.maxConcurrentSessions|2000|Nombre maximal de sessions qui peuvent être traitées simultanément par instance mise à l’échelle.|
|batchOptions.maxMessageCount|1 000| Nombre maximal de messages envoyés à la fonction lorsqu’elle est déclenchée. |
|batchOptions.operationTimeout|00:01:00| Valeur de l’intervalle de temps exprimée en `hh:mm:ss`. |
|batchOptions.autoComplete|true| Consultez la description ci-dessus pour `messageHandlerOptions.autoComplete`. |

### <a name="additional-settings-for-version-5x"></a>Paramètres supplémentaires pour la version 5.x et ultérieure

L’exemple de fichier host.json ci-dessous contient uniquement les paramètres pour les versions 5.0.0 et ultérieures de l’extension Service Bus.

```json
{
    "version": "2.0",
    "extensions": {
        "serviceBus": {
            "clientRetryOptions":{
                "mode": "exponential",
                "tryTimeout": "00:01:00",
                "delay": "00:00:00.80",
                "maxDelay": "00:01:00",
                "maxRetries": 3
            },
            "prefetchCount": 0,
            "autoCompleteMessages": true,
            "maxAutoLockRenewalDuration": "00:05:00",
            "maxConcurrentCalls": 16,
            "maxConcurrentSessions": 8,
            "maxMessages": 1000,
            "sessionIdleTimeout": "00:01:00"
        }
    }
}
```

Lorsque vous utilisez les versions 5.x et ultérieures de l’extension Service Bus, les paramètres de configuration globale suivants sont pris en charge en plus des paramètres 2.x dans `ServiceBusOptions`.

|Propriété  |Default | Description |
|---------|---------|---------|
|prefetchCount|0|Obtient ou définit le nombre de messages que le destinataire des messages peut demander simultanément.|
|autoCompleteMessages|true|Détermine s’il faut ou non terminer automatiquement les messages après l’exécution réussie de la fonction et doit être utilisé à la place du paramètre de configuration `autoComplete`.|
|maxAutoLockRenewalDuration|00:05:00|À utiliser à la place de `maxAutoRenewDuration`.|
|maxConcurrentCalls|16|Nombre maximal d’appels simultanés pour le rappel que la pompe de messages doit initier par instance mise à l’échelle. Par défaut, le runtime Functions traite plusieurs messages simultanément.|
|maxConcurrentSessions|8|Nombre maximal de sessions qui peuvent être traitées simultanément par instance mise à l’échelle.|
|maxMessages|1 000|Nombre maximal de messages qui seront transmis à chaque appel de fonction. Cela s’applique uniquement aux fonctions qui reçoivent un lot de messages.|
|sessionIdleTimeout|n/a|Délai d’attente maximal pour la réception d’un message pour la session active. Une fois ce délai écoulé, l’UC ferme la session et tente de traiter une autre session.|

### <a name="retry-settings"></a>Paramètres de nouvelle tentative

Outre les propriétés de configuration ci-dessus, lorsque vous utilisez les versions 5.x et ultérieures de l’extension Service Bus, vous pouvez également configurer `RetryOptions` à partir de `ServiceBusOptions`. Ces paramètres déterminent si une opération qui a échoué doit faire l’objet d’une nouvelle tentative et, le cas échéant, la durée d’attente entre chaque tentative. Les options contrôlent également la durée autorisée pour la réception des messages et d’autres interactions avec le service Service Bus.

|Propriété  |Default | Description |
|---------|---------|---------|
|mode|Exponentielle|Approche à utiliser pour calculer les délais de nouvelle tentative. Le mode exponentiel par défaut effectue de nouvelles tentatives avec un délai basé sur une stratégie de backoff, où chaque tentative augmente la durée d’attente avant la nouvelle tentative. Le mode `Fixed` effectue de nouvelles tentatives à intervalles fixes, chaque délai ayant la même durée.|
|tryTimeout|00:01:00|Durée maximale d’attente d’une opération par tentative.|
|delay|00:00:00.80|Délai ou facteur de backoff à appliquer entre les nouvelles tentatives.|
|maxDelay|00:01:00|Délai maximal à accorder entre les nouvelles tentatives.|
|maxRetries|3|Nombre maximal de nouvelles tentatives avant de considérer que l’opération associée a échoué.|

---

## <a name="next-steps"></a>Étapes suivantes

- [Exécuter une fonction durant la création d’un message de file d’attente ou de rubrique Service Bus (déclencheur)](./functions-bindings-service-bus-trigger.md)
- [Envoyer des messages Azure Service Bus à partir d’Azure Functions (liaison de sortie)](./functions-bindings-service-bus-output.md)
