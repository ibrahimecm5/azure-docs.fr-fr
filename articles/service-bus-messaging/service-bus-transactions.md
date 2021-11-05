---
title: Vue d'ensemble du traitement des transactions dans Azure Service Bus
description: Cet article offre une vue d'ensemble du traitement transactionnel et de la fonctionnalité « Envoyer par » dans Azure Service Bus.
ms.topic: article
ms.date: 09/21/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 5cdfa306b19c528fd66c6566f54c5c7992a2462e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131046734"
---
# <a name="overview-of-service-bus-transaction-processing"></a>Vue d’ensemble du traitement des transactions Service Bus

Cet article décrit les fonctionnalités de transaction de Microsoft Azure Service Bus. Le propos est principalement illustré avec l’exemple [AMPQ Transactions with Service Bus](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/TransactionsAndSendVia/TransactionsAndSendVia/AMQPTransactionsSendVia) (Transactions AMPQ avec Service Bus). Cet article se limite à une vue d’ensemble du traitement des transactions et de la fonctionnalité de *transfert* de Service Bus, tandis que l’exemple des transactions atomiques est de portée plus étendue et plus complexe.

> [!NOTE]
> Le niveau De base de Service Bus ne prend pas en charge les transactions. Les niveaux Standard et Premium prennent en charge les transactions. Pour connaître les différences entre ces niveaux, voir [Tarification de Service Bus](https://azure.microsoft.com/pricing/details/service-bus/).

## <a name="transactions-in-service-bus"></a>Transactions dans Service Bus

Une *transaction* regroupe plusieurs opérations dans une *étendue d’exécution*. Par nature, ce type de transaction doit garantir que toutes les opérations appartenant à un groupe d’opérations donné réussissent ou échouent ensemble. À cet égard, les transactions agissent comme une unité, ce qui est souvent désigné par le terme *atomicité*.

Service Bus est un courtier de messages transactionnel. Il garantit l’intégrité transactionnelle de toutes les opérations internes par rapport à ses banques de messages. Tous les transferts de messages dans Service Bus, notamment le déplacement des messages vers une [file d’attente de lettres mortes](service-bus-dead-letter-queues.md) ou le [transfert automatique](service-bus-auto-forwarding.md) de messages entre des entités, sont transactionnels. Par conséquent, si Service Bus accepte un message, il a déjà été stocké et étiqueté avec un numéro de séquence. Dès lors, tous les transferts de messages dans Service Bus sont des opérations coordonnées entre les entités et n’entraînent jamais de perte (réussite de la source et échec de la cible) ou de duplication (échec de la source et réussite de la cible) du message.

Service Bus prend en charge les opérations de regroupement par rapport à une entité de messagerie unique (file d’attente, rubrique, abonnement) dans l’étendue d’une transaction. Par exemple, vous pouvez envoyer plusieurs messages à une file d’attente à partir d’une étendue de transaction, et les messages seront validés dans le journal de la file d’attente uniquement lorsque la transaction se terminera correctement.

## <a name="operations-within-a-transaction-scope"></a>Opérations au sein d’une étendue de transaction

Les opérations qui peuvent être effectuées dans une étendue de transaction sont les suivantes :

- Envoyer
- Terminé
- Arrêter
- Deadletter
- Defer
- Renouveler les verrous

Les opérations de réception ne sont pas incluses, car on suppose que l’application acquiert d’abord des messages à l’aide du mode peek-lock dans une boucle de réception ou avec un rappel avant d’ouvrir une étendue de transaction pour le traitement du message.

Le traitement du message (exécution, abandon, lettre morte, report) se produit dans l’étendue du résultat global de la transaction et selon ce résultat.

## <a name="transfers-and-send-via"></a>Transferts

Pour permettre la remise transactionnelle des données d’une file d’attente ou d’une rubrique à un processeur, puis à une autre file d’attente ou rubrique, Service Bus prend en charge les *transferts*. Dans une opération de transfert, un expéditeur envoie d’abord un message à une *file d’attente ou à une rubrique de transfert*. Celle-ci déplace immédiatement le message vers la file d’attente ou rubrique de destination prévue suivant une implémentation de transfert fiable (sur laquelle repose également la fonctionnalité de transfert automatique). Le message n’est jamais validé dans le journal de la file d’attente ou de la rubrique de transfert de façon à devenir accessible aux consommateurs de la file d’attente ou rubrique de transfert.

La puissance de cette fonctionnalité transactionnelle se révèle lorsque la file d’attente ou la rubrique de transfert est elle-même la source des messages d’entrée de l’expéditeur. En d’autres termes, Service Bus peut transférer le message à la file d’attente ou à la rubrique de destination « via » la file d’attente ou rubrique de transfert, tout en effectuant une opération d’exécution complète (ou différée ou de lettres mortes) sur le message d’entrée, en une seule opération atomique. 

Si vous devez recevoir un abonnement à une rubrique, puis l’envoyer vers une file d’attente ou une rubrique de la même transaction, l’entité de transfert doit être une rubrique. Dans ce scénario, démarrez l’étendue de la transaction sur la rubrique, recevez depuis l’abonnement dans l’étendue de la transaction et envoyez via la rubrique de transfert à une destination de file d’attente ou de rubrique. 

### <a name="see-it-in-code"></a>Apparence dans le code

Pour configurer ces transferts, vous devez créer un expéditeur de message qui cible la file d’attente de destination via la file d’attente de transfert. Il vous faut également un destinataire qui extrait les messages de cette même file d’attente. Par exemple :

Une simple transaction utilise alors ces éléments, comme dans l’exemple suivant. Pour vous référer à l’exemple complet, veuillez consulter le [code source sur GitHub](https://github.com/Azure/azure-sdk-for-net/blob/main/sdk/servicebus/Azure.Messaging.ServiceBus/samples/Sample06_Transactions.md#transactions-across-entities) :

```csharp
var options = new ServiceBusClientOptions { EnableCrossEntityTransactions = true };
await using var client = new ServiceBusClient(connectionString, options);

ServiceBusReceiver receiverA = client.CreateReceiver("queueA");
ServiceBusSender senderB = client.CreateSender("queueB");

ServiceBusReceivedMessage receivedMessage = await receiverA.ReceiveMessageAsync();

using (var ts = new TransactionScope(TransactionScopeAsyncFlowOption.Enabled))
{
    await receiverA.CompleteMessageAsync(receivedMessage);
    await senderB.SendMessageAsync(new ServiceBusMessage());
    ts.Complete();
}
```


## <a name="timeout"></a>Délai d'expiration
Une transaction expire au bout de 2 minutes. Le minuteur de transaction démarre lorsque la première opération de la transaction démarre. 

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les files d’attente de lettres mortes Service Bus, consultez les articles suivants :

* [Utilisation des files d’attente Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Chaînage des entités Service Bus avec transfert automatique](service-bus-auto-forwarding.md)
* [Exemple de transfert automatique](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/AutoForward) (bibliothèque `Microsoft.ServiceBus.Messaging`)
* [Exemple de transactions atomiques avec Service Bus](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/AtomicTransactions) (bibliothèque `Microsoft.ServiceBus.Messaging`)
* [Utilisation d’exemple de transactions](https://github.com/Azure/azure-sdk-for-net/blob/main/sdk/servicebus/Azure.Messaging.ServiceBus/samples/Sample06_Transactions.md) (bibliothèque `Azure.Messaging.ServiceBus`)
* [Comparaison entre le stockage File d’attente et les files d’attentes Service Bus](service-bus-azure-and-service-bus-queues-compared-contrasted.md)
