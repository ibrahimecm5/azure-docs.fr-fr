---
title: Sessions de messagerie Azure Service Bus | Microsoft Docs
description: Cet article explique comment utiliser des sessions pour permettre un traitement conjoint et ordonné de séquences illimitées de messages associés.
ms.topic: article
ms.date: 09/01/2021
ms.openlocfilehash: 55a5d432505d6c0aec7c2fc9b030eb9763413bee
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132063286"
---
# <a name="message-sessions"></a>Sessions de message
Les sessions Azure Service Bus permettent un traitement conjoint et chronologique de séquences illimitées de messages associés. Vous pouvez utiliser des sessions dans des modèles **premier entré, premier sorti (FIFO**) et **requête-réponse**. Cet article explique comment utiliser des sessions pour implémenter ces modèles lors de l’utilisation de Service Bus. 

> [!NOTE]
> Le niveau de base de Service Bus ne prend pas en charge les sessions. Les niveaux standard et premium prennent en charge les sessions. Pour connaître les différences entre ces niveaux, voir [Tarification de Service Bus](https://azure.microsoft.com/pricing/details/service-bus/).

## <a name="first-in-first-out-fifo-pattern"></a>Modèle premier entré, premier sorti (FIFO)
Pour garantir l’application de la méthode FIFO dans Service Bus, utilisez des sessions. Service Bus n’est pas normatif concernant la nature de la relation entre les messages, et ne définit aucun modèle spécifique pour déterminer le début et la fin d’une séquence de messages.

Lors de l’envoi de messages dans une rubrique ou dans une file d’attente, tout expéditeur peut créer une session en définissant la propriété d’**ID de session** sur un identificateur défini par l’application qui est unique à la session. Au niveau du protocole **AMQP 1.0**, cette valeur est mappée sur la propriété **group-id**.

Dans les files d’attente ou abonnements prenant en compte la session, les sessions entrent en action quand au moins un message existe avec l’ID de session. Une fois qu’une session existe, aucune heure ou API ne définissent son expiration ou sa disparition. Il est théoriquement possible de recevoir un message pour une session le jour même, puis le message suivant un an après et, si l’ID de session correspond, Service Bus considère alors qu’il s’agit de la même session.

Toutefois, en général, une application détermine précisément le début et la fin d’un ensemble de messages associés. Service Bus ne définit pas de règles spécifiques. Par exemple, votre application pourrait définir la propriété **Label** du premier message sur **start**, celle des messages intermédiaires sur **content** et celle du dernier message sur **end**. La position relative des messages de contenu peut être calculée comme étant égale à la différence entre la valeur *SequenceNumber* du message actuel et la valeur *SequenceNumber* du message présentant la valeur **start**.

> [!IMPORTANT]
> Lorsque les sessions sont activées sur une file d’attente ou un abonnement, les applications clientes ***ne peuvent plus*** _ envoyer/recevoir de messages normaux. Tous les messages doivent être envoyés dans le cadre d’une session (en définissant l’ID de session) et reçus en acceptant la session.

Les API relatives aux sessions existent sur les clients de file d’attente et d’abonnement. Il existe un modèle impératif qui contrôle le moment où les sessions et les messages sont reçus, ainsi qu’un modèle basé sur un gestionnaire qui simplifie la gestion de la boucle de réception. 

Pour obtenir des exemples, utilisez les liens de la section [Étapes suivantes](#next-steps). 

### <a name="session-features"></a>Fonctionnalités de session

Les sessions assurent un démultiplexage simultané de flux de messages entrelacés tout en préservant et en garantissant la remise chronologique des messages.

![Diagramme montrant comment la fonctionnalité Sessions préserve la livraison chronologique des messages.][1]

Un destinataire de session est créé par un client qui accepte une session. Lorsque la session est acceptée et détenue par un client, ce dernier détient un verrou exclusif sur tous les messages portant l’**ID de session** de cette session dans la file d’attente ou l’abonnement. Il détient également des verrous exclusifs sur tous les messages avec l’**ID de session** qui arriveront plus tard.

Le verrou est libéré lorsque vous appelez les méthodes connexes de fermeture sur le récepteur ou lorsque le verrou expire. Il existe également des méthodes sur le récepteur pour renouveler les verrous. Au lieu de cela, vous pouvez utiliser la fonctionnalité de renouvellement automatique du verrouillage, dans laquelle vous pouvez spécifier la durée pendant laquelle vous souhaitez que le verrou soit renouvelé. Le verrouillage de session doit être traité de la même façon qu’un verrouillage exclusif sur un fichier, ce qui signifie que l’application doit fermer la session dès qu’elle n’en a plus besoin ou qu’elle n’attend plus aucun message.

Lorsque plusieurs destinataires simultanés extraient des données de la file d’attente, les messages appartenant à une session spécifique sont envoyés au destinataire concerné qui détient actuellement le verrouillage pour cette session. Grâce à cette opération, un flux de messages entrelacé dans une file d’attente ou dans un abonnement est correctement démultiplexé vers différents destinataires qui peuvent également résider sur des machines clientes distinctes, étant donné que la gestion des verrouillages se produit côté service, au sein de Service Bus.

L’illustration précédente montre trois récepteurs de session simultanée. Une Session avec `SessionId` = 4 ne contenant aucun client actif, propriétaire, aucun message n’est remis à partir de cette session. Une session se comporte à maints égards comme une sous-file d’attente.

Le verrouillage de session détenu par le destinataire de session constitue une protection pour les verrouillages de message utilisés par le mode de règlement *peek-lock*. Un seul destinataire peut avoir un verrou sur une session. Un destinataire peut avoir de nombreux messages en vol, mais les messages seront reçus dans l’ordre. L’abandon d’un message entraîne un nouveau traitement de ce message lors de l’opération de réception suivante.

### <a name="message-session-state"></a>État d’une session de messagerie

Lorsque les workflows sont traités dans des systèmes de cloud à grande échelle et à haute disponibilité, le gestionnaire de workflows associé à une session spécifique doit pouvoir redevenir opérationnel en cas de défaillance inattendue, et peut reprendre un travail incomplet sur un processus ou une machine différents de que ceux sur lesquels le travail a commencé.

La fonction d’état de session active une annotation définie par l’application d’une session de messagerie dans le répartiteur, de sorte que l’état de traitement enregistré concernant cette session devient immédiatement disponible lorsque la session est acquise par un nouveau processeur.

Du point de vue de Service Bus, l’état d’une session de messagerie est un objet binaire opaque qui peut contenir une taille de données équivalant à un message, autrement dit 256 Ko pour Service Bus Standard et 100 Mo dans le cas de Service Bus Premium. L’état de traitement relatif à une session peut être stocké dans l’état de session, ou l’état de session peut pointer vers un emplacement de stockage ou un enregistrement de base de données contenant cette information.

Les méthodes de gestion de l’état de la session, SetState et GetState, se trouvent sur l’objet de destinataire de session. Une session pour laquelle aucun état de session n’a été précédemment défini renvoie une référence null pour GetState. L’état de session précédemment défini peut être effacé en transmettant la valeur Null à la méthode SetState sur le récepteur.

L’état de session persiste tant qu’il n’est pas effacé (retour de la valeur **Null**), même si tous les messages dans une session donnée sont consommés.

L’état de session stocké dans une file d’attente ou dans un abonnement est pris en compte dans le quota de stockage de cette entité. Lorsque l’application a terminé avec une session, il est donc recommandé de faire en sorte que l’application supprime l’état de session conservé afin d’éviter un coût de gestion externe.

### <a name="impact-of-delivery-count"></a>Impact du nombre de livraisons

La définition du nombre de livraisons par message dans le contexte de sessions diffère légèrement de la définition en l’absence de sessions. Voici une table résumant le moment où le nombre de livraisons est incrémenté.

| Scénario | Le nombre de livraisons du message est-il incrémenté |
|----------|---------------------------------------------|
| La session est acceptée, mais le verrouillage de session expire (en raison du délai d’expiration) | Oui |
| La session est acceptée, les messages de la session ne sont pas terminés (même s’ils sont verrouillés) et la session est fermée | Non |
| La session est acceptée, les messages sont terminés, puis la session est explicitement fermée | S.O. (il s’agit du flux standard. Ici, les messages sont supprimés de la session) |

## <a name="request-response-pattern"></a>Modèle requête-réponse
Le [modèle requête-réponse](https://www.enterpriseintegrationpatterns.com/patterns/messaging/RequestReply.html) est un modèle d’intégration bien établi qui permet à l’application de l’expéditeur d’envoyer une demande et fournit au destinataire un moyen de renvoyer correctement une réponse à l’application de l’expéditeur. Ce modèle a généralement besoin d’une file d’attente ou d’une rubrique de courte durée de vie à laquelle l’application puisse envoyer des réponses. Dans ce scénario, les sessions fournissent une solution alternative simple avec une sémantique comparable. 

Plusieurs applications peuvent envoyer leurs requêtes à une seule file d’attente de requêtes, avec un paramètre d’en-tête spécifique défini pour identifier de manière unique l’application de l’expéditeur. L’application du destinataire peut traiter les requêtes arrivant dans la file d’attente et envoyer des réponses sur une file d’attente activée par la session, en définissant l’ID de session sur l’identificateur unique que l’expéditeur a envoyé sur le message de requête. L’application qui a envoyé la requête peut alors recevoir des messages sur l’ID de session spécifique et traiter correctement les réponses.

> [!NOTE]
> L’application qui envoie les requêtes initiales doit connaître l’ID de session et l’utiliser pour accepter la session afin que la session sur laquelle elle attend la réponse soit verrouillée. Il est judicieux d’utiliser un GUID qui identifie de façon unique l’instance de l’application comme ID de session. Il ne doit y avoir aucun gestionnaire de session ni de délai d’attente spécifié sur le destinataire de session pour la file d’attente afin de garantir que les réponses sont disponibles pour verrouillage et traitement par des destinataires spécifiques.

## <a name="next-steps"></a>Étapes suivantes
Vous pouvez activer les sessions de message lors de la création d’une file d’attente à l’aide du portail Azure, de PowerShell, de l’interface de ligne de commande, d’un modèle Resource Manager, de .NET, Java, de Python et de JavaScript. Pour plus d’informations, consultez [Activer les sessions de messages](enable-message-sessions.md). 

Essayez les exemples dans le langage de votre choix pour explorer les fonctionnalités d’Azure Service Bus. 

- [Exemples de bibliothèque de client Azure Service Bus pour .NET (dernière version)](/samples/azure/azure-sdk-for-net/azuremessagingservicebus-samples/)
- [Exemples de bibliothèque de client Azure Service Bus pour Java (dernière version)](/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [Exemples de bibliothèque de client Azure Service Bus pour Python](/samples/azure/azure-sdk-for-python/servicebus-samples/)
- [Exemples de bibliothèque de client Azure Service Bus pour JavaScript](/samples/azure/azure-sdk-for-js/service-bus-javascript/)
- [Exemples de bibliothèque de client Azure Service Bus pour TypeScript](/samples/azure/azure-sdk-for-js/service-bus-typescript/)

Recherchez des exemples pour les anciennes bibliothèques clientes .NET et Java ci-dessous :
- [Exemples de bibliothèque de client Azure Service Bus pour .NET (version héritée)](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/)
- [Exemples de bibliothèque de client Azure Service Bus pour Java (version héritée)](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus/MessageBrowse)

[1]: ./media/message-sessions/sessions.png

