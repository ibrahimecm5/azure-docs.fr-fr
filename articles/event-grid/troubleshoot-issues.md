---
title: Résoudre les problèmes liés à Event Grid
description: Cet article propose différentes solutions pour résoudre les problèmes liés à Azure Event Grid.
ms.topic: conceptual
ms.date: 06/10/2021
ms.openlocfilehash: 94370af8a3325d1798c3e2bcb65c2ccb3e54a43b
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132546345"
---
# <a name="troubleshoot-azure-event-grid-issues"></a>Résoudre les problèmes liés à Azure Event Grid
Cet article fournit des informations qui vous aideront à résoudre les problèmes liés à Azure Event Grid. 

## <a name="diagnostic-logs"></a>Journaux de diagnostic
Activez les paramètres de diagnostic des rubriques ou domaines Event Grid pour capturer et afficher les journaux des échecs de publication et de remise. Pour plus d'informations, consultez [Journaux de diagnostic](enable-diagnostic-logs-topic.md).

## <a name="metrics"></a>Mesures
Vous pouvez consulter les métriques des rubriques et abonnements Event Grid, et créer des alertes correspondantes. Pour plus d'informations, consultez [Métriques Event Grid](monitor-event-delivery.md).

## <a name="alerts"></a>Alertes
Créez des alertes sur les opérations des journaux d'activité et les métriques Azure Event Grid. Pour plus d'informations, consultez [Alertes sur les journaux d'activité et les métriques Event Grid](set-alerts.md).

## <a name="subscription-validation-issues"></a>Problèmes liés à la validation de l'abonnement
Lors de la création de l'abonnement aux événements, vous pouvez recevoir un message d'erreur indiquant que la validation du point de terminaison fourni a échoué. Pour résoudre les problèmes liés à la validation de l'abonnement, consultez [Résoudre les problèmes de validation de l'abonnement Event Grid](troubleshoot-subscription-validation.md). 

## <a name="network-connectivity-issues"></a>Problèmes de connectivité réseau
Il existe plusieurs raisons pour lesquelles des applications clientes peuvent ne pas parvenir à se connecter à une rubrique/un domaine Event Grid. Les problèmes de connectivité que vous rencontrez peuvent être permanents ou transitoires. Pour plus d'informations sur la résolution de ces problèmes, consultez [Résoudre les problèmes de connectivité](troubleshoot-network-connectivity.md).

## <a name="error-codes"></a>Codes d’erreur
Si vous recevez des messages d'erreur avec des codes d'erreur tels que 400, 409 et 403, consultez [Résoudre les erreurs liées à Event Grid](troubleshoot-errors.md). 

## <a name="distributed-tracing"></a>Traçage distribué
Les bibliothèques Event Grid dans .NET, Java, Python et JavaScript prennent en charge la distribution du suivi. Pour se conformer aux [directives de la spécification CloudEvents](https://github.com/cloudevents/spec/blob/v1.0.1/extensions/distributed-tracing.md) sur la distribution du suivi, la bibliothèque définit les attributs `traceparent` et `tracestate` sur l’extension `CloudEvent` lorsque le suivi distribué est activé.

Pour en savoir plus sur l’activation du suivi distribué dans votre application, consultez la documentation Azure SDK qui parle de ce sujet :

- [.NET](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/core/Azure.Core/samples/Diagnostics.md#Distributed-tracing)
- [Java](/azure/developer/java/sdk/tracing)
- [Python](https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/core/azure-core-tracing-opentelemetry)
- [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/main/sdk/core/README.md#tracing)

Pour activer le traçage de bout en bout pour un abonnement Event Grid [Azure Event Hubs](handler-event-hubs.md) ou [Azure Service Bus](handler-service-bus.md), configurez des [propriétés de remise personnalisées](delivery-properties.md) pour transférer `traceparent` l’attribut d’extension CloudEvent à la `Diagnostic-Id` propriété d’application AMQP. 

Voici un exemple d’abonnement dont les propriétés de remise de traçage sont configurées pour Event Hubs :

```azurecli
az eventgrid event-subscription create --name <event-grid-subscription-name> \
    --source-resource-id <event-grid-resource-id>
    --endpoint-type eventhub \
    --endpoint <event-hubs-endpoint> \
    --delivery-attribute-mapping Diagnostic-Id dynamic traceparent
```

### <a name="sample"></a>Exemple
Consultez l'[exemple de compteur de lignes](/samples/azure/azure-sdk-for-net/line-counter/). Cet exemple d'application illustre l'utilisation des clients Stockage, Event Hubs et Event Grid, ainsi que l'intégration d'ASP.NET Core, le suivi distribué et les services hébergés. Cette application permet aux utilisateurs de charger un fichier dans un blob, ce qui déclenche un événement Event Hubs contenant le nom du fichier. Le processeur Event Hubs reçoit l'événement, puis l'application télécharge le blob et compte le nombre de lignes du fichier. L'application affiche un lien vers une page contenant le nombre de lignes. Lorsque l'utilisateur clique sur le lien, un CloudEvent contenant le nom du fichier est publié à l'aide d'Event Grid.

## <a name="next-steps"></a>Étapes suivantes
Si vous avez besoin d’une aide supplémentaire, publiez votre problème sur le forum [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-eventgrid) ou ouvrez un [ticket de support](https://azure.microsoft.com/support/options/). 
