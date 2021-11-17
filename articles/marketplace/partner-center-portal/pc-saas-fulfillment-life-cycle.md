---
title: Gestion du cycle de vie d’un abonnement SaaS | Place de marché commercial Microsoft
description: Découvrez comment gérer le cycle de vie d’un abonnement SaaS à l’aide des API de traitement version 2.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 10/27/2021
author: saasguide
ms.author: souchak
ms.openlocfilehash: dfe47c220cb0dc427d9ff00bdfbaa85da949f520
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132064002"
---
# <a name="managing-the-saas-subscription-life-cycle"></a>Gestion du cycle de vie des abonnements SaaS

La Place de marché commerciale gère l’ensemble du cycle de vie d’un abonnement SaaS après son achat par le client final. Elle utilise la page d’arrivée, les API de traitement, les API d’opérations et le webhook comme mécanisme pour piloter l’activation, l’utilisation, les mises à jour et l’annulation de l’abonnement SaaS. La facture de l’utilisateur final est basée sur l’état de l’abonnement SaaS managé par Microsoft.

## <a name="states-of-a-saas-subscription"></a>États d’un abonnement SaaS

Le diagramme suivant montre les états d’un abonnement SaaS et les actions applicables.

[ ![Diagramme montrant le cycle de vie d’un abonnement SaaS sur la Place de marché.](./media/saas-subscription-lifecycle-api-v2.png) ](./media/saas-subscription-lifecycle-api-v2.png#lightbox)

### <a name="purchased-but-not-yet-activated-pendingfulfillmentstart"></a>Acheté mais pas encore activé (*PendingFulfillmentStart*)

Quand un utilisateur final ou un fournisseur de solutions Cloud (CSP) achète une offre SaaS sur le marketplace commercial, l’éditeur est informé de cet achat. L’éditeur peut ensuite créer et configurer un nouveau compte SaaS côté éditeur pour l’utilisateur final.

Pour la création du compte :

1. Le client sélectionne le bouton **Configurer le compte** disponible pour une offre SaaS après son achat dans Microsoft AppSource ou le portail Azure. Il peut également utiliser le bouton **Configurer** dans l’e-mail qu’il reçoit peu après l’achat.
2. Microsoft avertit ensuite le partenaire de l’achat en ouvrant l’URL de la page d’arrivée avec le paramètre de jeton (le jeton d’identification d’achat de la Place de marché commerciale) dans le nouvel onglet de navigateur.

Un exemple de ce type d’appel est `https://contoso.com/signup?token=<blob>`, mais l’URL de la page d’arrivée de cette offre SaaS dans l’Espace partenaires est configurée comme `https://contoso.com/signup`. Ce jeton fournit à l’éditeur un ID qui identifie de façon unique l’achat SaaS et le client.

[!INCLUDE [pound-sign-note](../includes/pound-sign-note.md)]

> [!IMPORTANT]
> L’URL de la page d’arrivée doit être disponible 24 h/24 et 7 j/7, et prête à recevoir à tout moment de nouveaux appels de Microsoft. Si la page d’accueil n’est plus disponible, les clients ne pourront pas s’inscrire au service SaaS et commencer à l’utiliser.

Ensuite, l’éditeur doit renvoyer le *jeton* à Microsoft en appelant l’[API Résolution SaaS](pc-saas-fulfillment-subscription-api.md#resolve-a-purchased-subscription) et en entrant le jeton comme valeur du paramètre d’en-tête `x-ms-marketplace-token header`. Suite à l’appel de l’API Résolution, le jeton est échangé contre les détails de l’achat SaaS, notamment l’ID unique de l’achat, l’ID de l’offre achetée et l’ID du plan acheté.

Dans la page d’arrivée, le client doit être connecté au compte SaaS nouveau ou existant par le biais de l’authentification unique Azure AD (Azure Active Directory).

>[!NOTE]
>L’éditeur n’est pas informé de l’achat SaaS tant que le client n’a pas lancé le processus de configuration côté Microsoft.

L’éditeur doit implémenter l’authentification unique pour fournir l’expérience utilisateur requise par Microsoft pour ce flux. Veillez à utiliser l’application Azure AD multilocataire et à autoriser les comptes professionnels et scolaires ou les comptes Microsoft personnels lors de la configuration de l’authentification unique. Cette exigence s’applique uniquement à la page d’arrivée et aux utilisateurs redirigés vers le service SaaS lorsqu’ils sont déjà connectés avec des informations d’identification Microsoft. L’authentification unique n’est pas nécessaire pour toutes les connexions au service SaaS.

> [!NOTE]
>Si l’authentification unique exige qu’un administrateur accorde une autorisation à une application, la description de l’offre dans l’Espace partenaires doit indiquer que l’accès au niveau de l’administrateur est requis. Cette indication est nécessaire pour se conformer aux [stratégies de certification de la Place de marché commerciale](/legal/marketplace/certification-policies#10003-authentication-options).

Une fois connecté, le client doit effectuer la configuration SaaS côté éditeur. L’éditeur doit ensuite appeler l’[API Activer l’abonnement](pc-saas-fulfillment-subscription-api.md#activate-a-subscription) pour envoyer un signal à la Place de marché Azure indiquant que le provisionnement du compte SaaS est terminé.
Cette action démarre le cycle de facturation du client. Si l’appel de l’API Activer l’abonnement échoue, le client n’est pas facturé pour l’achat.

![Diagramme montrant les appels d’API pour un scénario de provisionnement.](./media/saas-update-api-v2-calls-from-saas-service-a.png) 

### <a name="active-subscribed"></a>Actif (*Subscribed*)

Actif (*Subscribed*) est l’état stable d’un abonnement SaaS provisionné. Une fois que le côté Microsoft a traité l’appel de l’[API d’activation d’abonnement](pc-saas-fulfillment-subscription-api.md#activate-a-subscription), l’abonnement SaaS est marqué comme *Abonné*. Le client peut maintenant utiliser le service SaaS du côté éditeur, et sera facturé.

Lorsqu’un abonnement SaaS est déjà actif, le client peut sélectionner **Gérer l’expérience SaaS** à partir du portail Azure ou du Centre d’administration Microsoft 365. Cette action provoque également l’appel, par Microsoft, de l’**URL de la page d’arrivée** avec le paramètre *token*, comme c’est le cas dans le flux d’activation. L’éditeur doit faire la distinction entre les nouveaux achats et la gestion de comptes SaaS existants, et gérer cet appel d’URL de page d’arrivée en conséquence.

### <a name="being-updated-subscribed"></a>En cours de mise à jour (*Subscribed*)

Cette action signifie qu’une mise à jour d’un abonnement SaaS actif existant est traitée à la fois par Microsoft et par l’éditeur. Une telle mise à jour peut être lancée par :

- Le client à partir de la Place de marché commerciale.
- Le CSP à partir de la Place de marché commerciale.
- Le client à partir du site SaaS de l’éditeur (mais pas pour les achats effectués par le CSP).

Deux types de mises à jour sont disponibles pour un abonnement SaaS :

- Mettre à jour le plan lorsque le client choisit un autre plan pour l’abonnement.
- Mettre à jour la quantité lorsque le client modifie le nombre de postes achetés pour l’abonnement

Un seul abonnement actif peut être mis à jour. Pendant la mise à jour de l’abonnement, son état reste actif côté Microsoft.

#### <a name="update-initiated-from-the-commercial-marketplace"></a>Mise à jour lancée à partir de la Place de marché commerciale

Dans ce flux, le client modifie le plan d’abonnement ou le nombre de postes à partir du portail Azure ou du Centre d’administration Microsoft 365.

1. Après la saisie d’une mise à jour, Microsoft appelle l’URL du webhook de l’éditeur, configurée dans le champ **Webhook de connexion** de la page _Configuration technique_ d’Espace partenaires, avec une valeur appropriée pour l’*action* et d’autres paramètres pertinents.
1. Le côté éditeur doit apporter les modifications requises au service SaaS et notifier Microsoft lorsqu’il a terminé en appelant l’[API Mettre à jour l’état de l’opération](pc-saas-fulfillment-operations-api.md#update-the-status-of-an-operation).
1. Si le correctif est envoyé avec l’état *fail*, le processus de mise à jour ne se terminera pas côté Microsoft. L’abonnement SaaS conservera le plan et la quantité de postes existants.

> [!NOTE]
> L’éditeur doit appeler PATCH pour [mettre à jour l’API État de l’opération](pc-saas-fulfillment-operations-api.md#update-the-status-of-an-operation) avec une réponse Échec/Réussite *dans un délai de 10 secondes* après la réception de la notification de webhook. Si le correctif PATCH de l'état de l'opération n'est pas reçu dans les 10 secondes, une réponse *Réussite est automatiquement appliquée*.

La séquence d’appels d’API pour un scénario de mise à jour lancé à partir de la Place de marché commerciale est présentée dans le diagramme suivant.

![Diagramme montrant les appels d’API pour une mise à jour lancée par la Place de marché.](./media/saas-update-status-api-v2-calls-marketplace-side.png)

#### <a name="update-initiated-from-the-publisher"></a>Mise à jour lancée à partir de l’éditeur

Dans ce processus, le client modifie le plan d’abonnement ou le nombre de postes directement à partir du service SaaS. 

1. Avant d’apporter la modification demandée côté éditeur, le code de celui-ci doit appeler l’[API Modifier le plan](pc-saas-fulfillment-subscription-api.md#change-the-plan-on-the-subscription) et/ou l’[API Modifier la quantité](pc-saas-fulfillment-subscription-api.md#change-the-quantity-of-seats-on-the-saas-subscription).

1. Microsoft appliquera la modification à l’abonnement, puis informera l’éditeur via **Webhook de connexion** d’appliquer la même modification.

1. Ce n’est qu’à ce moment-là que l’éditeur doit apporter la modification requise à l’abonnement SaaS, et notifier Microsoft lorsque la modification est effectuée en appelant l’[API Mettre à jour l’état de l’opération](pc-saas-fulfillment-operations-api.md#update-the-status-of-an-operation).

La séquence d’appels d’API pour un scénario de mise à jour lancée du côté éditeur est présentée dans le diagramme suivant.

![Diagramme montrant les appels d’API pour une mise à jour lancée du côté éditeur.](./media/saas-update-status-api-v2-calls-publisher-side.png)

### <a name="suspended-suspended"></a>Interrompu (*Interrompu*)

Cet état indique que le paiement d’un client pour le service SaaS n’a pas été reçu. Microsoft informe l’éditeur de cette modification de l’état de l’abonnement SaaS. La notification est effectuée via un appel au webhook avec le paramètre *action* défini sur *Suspended*.

L’éditeur peut choisir d’apporter ou non des modifications au service SaaS du côté éditeur. Nous recommandons à l’éditeur de mettre ces informations à la disposition du client suspendu et de limiter ou bloquer l’accès du client au service SaaS. Il existe une probabilité que le paiement ne soit jamais reçu.

> [!NOTE]
> Microsoft accorde au client une période de grâce de 30 jours avant l’annulation automatique de l’abonnement. À l’issue de la période de grâce de 30 jours, le webhook recevra une action `Unsubscribe`.

Quand un abonnement est à l’état *Suspended* :

* Le partenaire ou l’éditeur de logiciels indépendant doit conserver le compte SaaS dans un état récupérable, afin que toutes les fonctionnalités puissent être restaurées sans perte de données ou de paramètres.
* Le partenaire ou l’éditeur de logiciels indépendant doit s’attendre à une demande de réactivation de l’abonnement, si le paiement est reçu au cours de la période de grâce, ou à une demande de déprovisionnement de l’abonnement à la fin de la période de grâce. Les deux demandes seront envoyées par le biais du mécanisme de webhook.

L’état de l’abonnement affiche Interrompu côté Microsoft tant que l’éditeur n’a pas effectué une action. Seuls les abonnements actifs peuvent être interrompus.

### <a name="reinstated-suspended"></a>Réactivé (*Interrompu*)

Cette action indique que l’instrument de paiement du client est de nouveau valide, qu’un paiement a été effectué pour l’abonnement SaaS et que l’abonnement est en cours de réactivation. Dans ce cas : 

1. Microsoft appelle webhook avec un paramètre *action* défini sur la valeur *Reinstate* (Réactiver).
1. L’éditeur s’assure que l’abonnement est entièrement opérationnel de son côté.
1. L’éditeur appelle l’[API Opération Patch](pc-saas-fulfillment-operations-api.md#update-the-status-of-an-operation) avec un état de réussite.
1. Le processus de réactivation réussit et le client est de nouveau facturé pour l’abonnement SaaS. 

Si le correctif est envoyé avec un état *fail*, le processus de réactivation ne se terminera pas du côté Microsoft et l’abonnement restera à l’état *Suspended*.

Seul un abonnement Interrompu peut être réactivé. L’abonnement SaaS suspendu reste dans un état *Suspended* pendant sa réactivation. Une fois cette opération terminée, l’état de l’abonnement devient *Active*.

### <a name="renewed-subscribed"></a>Renouvelé (*Abonné*)

L’abonnement SaaS est renouvelé automatiquement par Microsoft à la fin de la période d’abonnement d’un mois ou d’une année. La valeur par défaut du paramètre de renouvellement automatique est *true* pour tous les abonnements SaaS. Les abonnements SaaS actifs continuent d’être renouvelés à un rythme régulier. Microsoft fournit des notifications de webhook purement informatives pour les événements de renouvellement. Un client peut désactiver le renouvellement automatique d’un abonnement SaaS par le biais du portail d’administration Microsoft 365. Dans ce cas, l’abonnement SaaS sera automatiquement annulé à la fin de la période de facturation actuelle. Les clients peuvent également annuler l’abonnement SaaS à tout moment.

Seuls les abonnements actifs sont automatiquement renouvelés. Les abonnements restent actifs pendant le processus de renouvellement et si le renouvellement automatique a échoué. Après le renouvellement, les dates de début et de fin de la période d’abonnement sont mises à jour avec les dates du nouveau terme.

Si un renouvellement automatique échoue en raison d’un problème de paiement, l’abonnement devient *Suspended* et l’éditeur est informé.

### <a name="canceled-unsubscribed"></a>Annulé (*Désabonné*)

Les abonnements atteignent cet état en réponse à une action explicite de la part du client ou du CSP par annulation d’un abonnement à partir du site de l’éditeur, du portail Azure ou du Centre d’administration Microsoft 365. Un abonnement peut également être annulé implicitement en raison d’un défaut de paiement de la cotisation, après avoir été à l’état *Suspended* pendant 30 jours.

Après la réception d’un appel webhook d’annulation, l’éditeur doit conserver les données client à des fins de récupération à la demande pendant au moins sept jours. Les données client pourront uniquement être supprimées après ce délai.

Un abonnement SaaS peut être annulé à tout moment durant son cycle de vie. Une fois qu’un abonnement a été annulé, il ne plus pas être réactivé.

## <a name="next-steps"></a>Étapes suivantes

- [API d’abonnement au traitement SaaS v2](pc-saas-fulfillment-subscription-api.md)
- [API d’opérations de traitement SaaS v2](pc-saas-fulfillment-operations-api.md)
