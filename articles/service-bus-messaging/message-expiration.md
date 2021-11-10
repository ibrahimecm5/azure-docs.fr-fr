---
title: Azure Service Bus - Expiration des messages
description: Cet article traite de l'expiration et de la durée de vie des messages Azure Service Bus. Passé ce délai, le message n’est plus remis.
ms.topic: conceptual
ms.date: 11/01/2021
ms.openlocfilehash: 5534a2ee94db118f4429eaec2409683f68fa5547
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131460524"
---
# <a name="message-expiration-time-to-live"></a>Expiration des messages (durée de vie)
La charge utile dans un message, ou une commande ou demande transmise par un message à un destinataire, est presque toujours soumise à une forme de délai d’expiration au niveau de l’application. Passé ce délai, le contenu n’est plus remis, ou l’opération demandée n’est plus exécutée.

Dans les environnements de développement et de test où les files d’attente et les rubriques sont souvent utilisées dans le contexte d’exécutions partielles d’applications ou de parties d’application, il est également préférable que les messages de test abandonnés soient automatiquement nettoyés de la mémoire pour permettre à la prochaine série de tests de démarrer le nettoyage.

L’expiration d’un message peut être contrôlé à l’aide de la propriété système **time-to-live** qui définit une durée relative. Ce délai devient un instant absolu quand le message est mis en file d’attente dans l’entité. À ce moment, la propriété **expires-at-utc** prend la valeur **enqueued-time-utc** + **time-to-live**. Le paramètre time-to-live (TTL) sur un message réparti n’est pas appliqué quand aucun client n’écoute activement.

Une fois l’instant **expires-at-utc** passé, les messages ne sont plus éligibles pour la récupération. L’expiration n’affecte pas les messages actuellement verrouillés pour la remise. Ces messages sont traités gérés normalement. Si le verrou expire ou si le message est abandonné, le délai d’expiration prend effet immédiatement.

Lorsque le message est verrouillé, l’application peut être en possession d’un message qui a expiré. L’application peut alors poursuivre le traitement ou choisir d’abandonner le message. C’est au responsable de l’implémentation d’en décider.

Une durée de vie extrêmement faible de l’ordre de quelques millisecondes ou secondes, peut entraîner l’expiration des messages avant que les applications destinataires les reçoivent. Considérez la TTL la plus élevée qui fonctionne pour votre application.

## <a name="entity-level-expiration"></a>Expiration au niveau de l’entité
Tous les messages envoyés vers une file d’attente ou une rubrique sont soumis à un délai d’expiration par défaut qui est défini au niveau de l’entité. Ce délai peut également être défini dans le portail lors de la création, puis ajusté ultérieurement. Le délai d’expiration par défaut est utilisé pour tous les messages envoyés à l’entité où la propriété time-to-live n’est pas définie explicitement. Le délai d’expiration par défaut fait également office de plafond pour la valeur time-to-live. Les messages qui ont un délai d’expiration time-to-live plus long que le délai par défaut sont ajustés silencieusement à la valeur time-to-live de message par défaut avant d’être mis en file attente.

> [!NOTE]
> - La valeur par défaut de la durée de vie d’un message réparti correspond à la plus grande valeur possible d’un entier signé de 64 bits, sauf indication contraire.
> - Pour les entités de message (files d’attente et rubriques), le délai d’expiration par défaut est également égal à la valeur la plus grande possible d’un entier signé de 64 bits pour les niveaux Standard et Premium de Service Bus. Pour le niveau **de base**, le délai d’expiration par défaut est de **14 jours** (qui est également le délai d’expiration maximal).
> - Si la rubrique spécifie une durée de vie inférieure à celle spécifiée dans l'abonnement, la durée de vie de la rubrique est appliquée.

Les messages expirés peuvent être envoyés vers une [file d’attente de lettres mortes](service-bus-dead-letter-queues.md). Vous pouvez configurer ce paramètre par programmation ou à l’aide du portail Azure. Si l’option reste désélectionnée, les messages expirés sont supprimés. La distinction entre les messages expirés déplacés vers la file d’attente de lettres mortes et les autres messages de lettres mortes est possible en évaluant la propriété [motif de lettres mortes](service-bus-dead-letter-queues.md#moving-messages-to-the-dlq) que le répartiteur stocke dans la section des propriétés utilisateur. 

Dans le cas cité plus haut où le message n’est pas soumis au délai d’expiration du fait qu’il est verrouillé, et si l’indicateur est défini sur l’entité, le message est déplacé dans la file d’attente de lettres mortes aussitôt après l’abandon ou l’expiration du verrou. Toutefois, le message n’est pas déplacé s’il s’agit d’un message réglé, ce qui suppose que l’application a pu le traiter, en dépit du délai d’expiration nominal.

L’utilisation combinée de la propriété time-to-live et du déplacement automatique (et transactionnel) vers la file d’attente de lettres mortes à l’expiration est une solution efficace pour garantir qu’un travail donné à un gestionnaire ou un groupe de gestionnaires avec un délai d’expiration sera bien récupéré pour être traité avant la fin du délai d’expiration.

Prenons l’exemple d’un site web qui doit exécuter des travaux de manière fiable sur un serveur principal avec des contraintes de mise à l’échelle, et qui connaît parfois des pics de trafic ou doit être isolé pendant les périodes de disponibilité de ce serveur principal. En situation normale, le gestionnaire côté serveur envoie (push) les données transmises par l’utilisateur dans une file d’attente et reçoit par la suite une réponse qui confirme le traitement réussi de la transaction dans une file d’attente de réponse. En présence d’un pic de trafic, si le gestionnaire du serveur principal ne peut pas traiter ses éléments de backlog dans le temps imparti, les travaux expirés sont retournés dans la file d’attente de lettres mortes. L’utilisateur interactif peut être averti que l’opération demandée va nécessiter un peu plus de temps que normalement, et la demande peut ensuite être placée dans une autre file d’attente pour un chemin d’accès de traitement où le résultat du traitement final est envoyé à l’utilisateur par e-mail. 


## <a name="temporary-entities"></a>Entités temporaires

Des files d’attente, rubriques et abonnements Service Bus peuvent être créés en tant qu’entités temporaires, qui sont automatiquement supprimées quand elles n’ont pas été utilisées pendant une période spécifiée.
 
Le nettoyage automatique est utile dans les scénarios de développement et de test où les entités sont créées de façon dynamique et ne sont pas nettoyées après leur utilisation, en raison d’une interruption de la série de tests ou d’un débogage. Il est également utile quand une application crée des entités dynamiques, telles qu’une file d’attente de réponse, pour recevoir des réponses dans un processus de serveur web ou dans un autre objet relativement éphémère. En effet, dans ce cas, il est difficile de nettoyer correctement ces entités quand l’instance de l’objet disparaît.

La fonctionnalité est activée à l’aide de la propriété **suppression automatique inactive** sur l’espace de noms. La propriété est définie sur la durée pendant laquelle une entité peut rester inactive (non utilisée) avant d’être automatiquement supprimée. La valeur minimale de cette propriété est 5 minutes.
 
## <a name="idleness"></a>Inactivité

Voici ce qui considéré comme inactivité d’entités (files d’attente, rubriques et abonnements) :

- Files d’attente
    - Aucun envoi  
    - Aucune réception  
    - Aucune mise à jour de la file d’attente  
    - Aucun message planifié  
    - Aucun parcours/affichage d’aperçu 
- Rubriques  
    - Aucun envoi  
    - Aucune mise à jour de la rubrique  
    - Aucun message planifié 
    - Aucune opération sur les abonnements de la rubrique (comme indiqué ci-dessous)
- Abonnements
    - Aucune réception  
    - Aucune mise à jour de l’abonnement  
    - Aucune nouvelle règle ajoutée à l’abonnement  
    - Aucun parcours/affichage d’aperçu  
 

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la messagerie Service Bus, consultez les articles suivants :

* [Files d’attente, rubriques et abonnements Service Bus](service-bus-queues-topics-subscriptions.md)
* [Prise en main des files d’attente Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Utilisation des rubriques et abonnements Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
