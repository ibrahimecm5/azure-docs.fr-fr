---
title: Implémentation d’un webhook sur le service SaaS | Place de marché commerciale Microsoft
description: Découvrez comment implémenter un webhook sur le service SaaS à l’aide des API de traitement version 2.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 10/27/2021
author: saasguide
ms.author: souchak
ms.openlocfilehash: 6cf50327719b434cda7d5df4e3df99b9d96a1dcd
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132064008"
---
# <a name="implementing-a-webhook-on-the-saas-service"></a>Implémentation d’un webhook sur le service SaaS

Lors de la création d’une offre SaaS payante dans Espace partenaires, le partenaire fournit l’URL du **webhook de connexion** à utiliser comme point de terminaison HTTP.  Ce webhook est appelé par Microsoft à l’aide de l’appel POST HTTP pour notifier le côté éditeur des événements suivants qui se produisent côté Microsoft :

* Lorsque l’abonnement SaaS est à l’état *Subscribed* :
    * ChangePlan
    * ChangeQuantity
    * Renouveler
    * Interrompre
    * Se désabonner
* Lorsque l’abonnement SaaS est à l’état *Suspended* :
    * Reinstate
    * Se désabonner

L’éditeur doit implémenter un webhook dans le service SaaS pour garantir la cohérence de l’état de l’abonnement SaaS avec le côté Microsoft.  Le service SaaS doit appeler l’API Obtenir l’opération pour valider et autoriser l’appel du webhook et les données de la charge utile avant de prendre des mesures basées sur la notification de webhook.  L’éditeur doit retourner HTTP 200 à Microsoft dès que l’appel de webhook est traité.  Cette valeur confirme que l’éditeur a bien reçu l’appel du webhook.

> [!IMPORTANT]
> Le service de l’URL du webhook doit être disponible 24 h/24 et 7 j/7 et prêt à recevoir à tout moment de nouveaux appels de Microsoft.  Microsoft ne propose aucune stratégie de nouvelle tentative pour l’appel du webhook (500 nouvelles tentatives sur huit heures), mais si l’éditeur n’accepte pas l’appel et retourne une réponse, l’opération notifiée par le webhook finira par échouer côté Microsoft.

*Exemple de charge utile de webhook d’un événement d’achat :*

```json
// end user changed a quantity of purchased seats for a plan on Microsoft side
{
  "id": "<guid>", // this is the operation ID to call with get operation API
  "activityId": "<guid>", // do not use
  "subscriptionId": "guid", // The GUID identifier for the SaaS resource which status changes
  "publisherId": "contoso", // A unique string identifier for each publisher
  "offerId": "offer1", // A unique string identifier for each offer
  "planId": "silver", // the most up-to-date plan ID
  "quantity": "25", // the most up-to-date number of seats, can be empty if not relevant
  "timeStamp": "2019-04-15T20:17:31.7350641Z", // UTC time when the webhook was called
  "action": "ChangeQuantity", // the operation the webhook notifies about
  "status": "Success" // Can be either InProgress or Success
}
```

*Exemple de charge utile de webhook d’un événement de réactivation d’abonnement :*

```json
// end user's payment instrument became valid again, after being suspended, and the SaaS subscription is being reinstated
{
  "id": "<guid>",
  "activityId": "<guid>",
  "subscriptionId": "<guid>",
  "publisherId": "contoso",
  "offerId": "offer2 ",
  "planId": "gold",
  "quantity": "20",
  "timeStamp": "2019-04-15T20:17:31.7350641Z",
  "action": "Reinstate",
  "status": "InProgress"
}
```

*Exemple de charge utile de webhook d’un événement de renouvellement :*

```json
// end user's payment instrument became valid again, after being suspended, and the SaaS subscription is being reinstated
{
  "id": "<guid>",
  "activityId": "<guid>",
  "subscriptionId": "<guid>",
  "publisherId": "contoso",
  "offerId": "offer1 ",
  "planId": "silver",
  "quantity": "25",
  "timeStamp": "2019-04-15T20:17:31.7350641Z",
  "action": "Renew",
  "status": "Success"
}
```

## <a name="development-and-testing"></a>Développement et test

Pour démarrer le processus de développement, nous vous recommandons de créer des réponses d’API factices côté éditeur.  Ces réponses peuvent reposer sur des exemples de réponses fournis dans cet article.

Lorsque l’éditeur est prêt pour le test de bout en bout :

* Publiez une offre SaaS sur une audience en préversion limitée et conservez-la en phase de préversion.
* Définissez le prix du plan sur 0 pour éviter de déclencher des frais de facturation réels lors des tests.  Une autre solution consiste à définir un prix différent de 0 et à annuler tous les achats de test pendant 24 heures.
* Veillez à ce que tous les flux soient appelés de bout en bout, afin de simuler un scénario client réel.
* Si le partenaire souhaite tester le flux complet d’achat et de facturation, utilisez une offre dont le prix est supérieur à $0.  L’achat sera facturé et une facture sera établie.

Un flux d’achat peut être déclenché à partir du portail Azure ou de Microsoft AppSource, selon l’endroit où l’offre est publiée.

Les actions de *modification du plan*, *modification de quantité* et de *désabonnement* sont testées côté éditeur.  Côté Microsoft, l’opération de *désabonnement* peut être déclenchée à partir du portail Azure ou du Centre d’administration (le portail où les achats Microsoft AppSource sont gérés).  Les opérations de *modification de quantité et de plan* peuvent être déclenchées uniquement à partir du Centre d’administration.

## <a name="get-support"></a>Obtenir de l’aide

Pour afficher les options de support pour les éditeurs, consultez [Support technique pour le programme commercial de la Place de marché dans l’Espace partenaires](../support.md).

## <a name="next-steps"></a>Étapes suivantes

Pour découvrir plus d’options concernant les offres SaaS sur la Place de marché commerciale, consultez [API de facturation à la consommation de la Place de marché](../marketplace-metering-service-apis.md).

Examinez et utilisez les [clients pour différents langages et exemples de programmation](https://github.com/microsoft/commercial-marketplace-samples).
