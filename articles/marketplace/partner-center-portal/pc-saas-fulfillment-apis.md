---
title: API de traitement SaaS dans la Place de marché commerciale Microsoft
description: Présentation des API de traitement qui vous permettent d’intégrer vos offres SaaS dans Microsoft AppSource et Place de marché Azure.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/18/2020
author: saasguide
ms.author: souchak
ms.openlocfilehash: f5db31caffe420f7b53a554e858b8752e9c51223
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132054350"
---
# <a name="saas-fulfillment-apis-in-the-microsoft-commercial-marketplace"></a>API de traitement SaaS dans la Place de marché commerciale Microsoft

Les API de traitement SaaS permettent aux éditeurs de logiciels indépendants (ISV) de publier et vendre leurs applications SaaS dans Microsoft AppSource et Place de marché Azure. Ces API permettent aux applications d’éditeurs de logiciels indépendants d’utiliser tous les canaux commerciaux : directs, gérés par des partenaires (revendeurs) et gérés par des acteurs de terrain. L’intégration avec ces API est une condition requise pour la création et la publication d’une offre SaaS dans l’espace partenaires.

Pour découvrir les API de traitement SaaS, nous vous recommandons également de lire les articles suivants :
- [Gestion du cycle de vie des abonnements SaaS](pc-saas-fulfillment-life-cycle.md)
- [API d’abonnement au traitement SaaS v2](pc-saas-fulfillment-subscription-api.md)
- [API d’opérations de traitement SaaS v2](pc-saas-fulfillment-operations-api.md)
- [Implémentation d’un webhook sur le service SaaS](pc-saas-fulfillment-webhook.md)
- [Questions courantes sur les API de traitement SaaS](saas-fulfillment-apis-faq.yml)

## <a name="api-flows"></a>Flux d’API

Les éditeurs de logiciels indépendants doivent implémenter les flux d’API suivants en les ajoutant à leur code de service SaaS pour conserver le même état d’abonnement pour les éditeurs de logiciels indépendants (ISV) et Microsoft :

* Flux de la page d’accueil :  Microsoft informe l’éditeur que l’offre SaaS de l’éditeur a été achetée par un client sur la place de marché.
* Flux d’activation :  L’éditeur avertit Microsoft qu’un compte SaaS nouvellement acheté a été configuré côté éditeur.
* Mise à jour du flux : Modification du plan acheté et/ou du nombre de postes achetés.
* Flux de suspension et de réactivation : Interruption de l’offre SaaS achetée si le mode de paiement du client n’est plus valide. L’offre interrompue peut être rétablie lorsque le problème de mode de paiement est résolu.
* Flux webhook : Microsoft informe l’éditeur des modifications d’abonnement SaaS et de l’annulation déclenchée par le client côté Microsoft.

Pour l’annulation de l’abonnement SaaS acheté, l’intégration est facultative, car elle peut être effectuée par le client du côté Microsoft.

Une intégration correcte avec les API de réalisation SaaS est essentielle pour s’assurer que :

* Les clients finaux qui ont acheté l’offre SaaS de l’éditeur sont facturés correctement par Microsoft.
* Les clients finaux bénéficient de la bonne expérience utilisateur pour l’achat, la configuration, l’utilisation et la gestion des abonnements SaaS achetés sur la place de marché.

Ces API permettent aux offres de l’éditeur de participer à tous les canaux de commerce activés :

* Direct
* Initiative d’un partenaire (revendeur, CSP)
* Initiative d’un partenaire

Dans le scénario revendeur (CSP), un CSP achète l’offre SaaS au nom du client final. Un client est supposé utiliser l’offre SaaS, mais le CSP est l’entité qui effectue les tâches suivantes :

* Facture le client.
* Modifie les plans d’abonnement ou la quantité de postes achetés.
* Annule les abonnements.

L’éditeur n’est pas tenu d’implémenter un flux d’appels d’API différent pour ce scénario.

Pour plus d’informations sur le CSP, consultez https://partner.microsoft.com/licensing.

>[!Warning]
>La version actuelle de cette API (version 2) doit être utilisée pour toutes les nouvelles offres SaaS. La version 1 de l’API est déconseillée. Elle est conservée pour prendre en charge des offres existantes.

>[!Note]
>Les API de traitement SaaS sont uniquement destinées à être appelées à partir d’un service principal de l’éditeur. L’intégration avec les API directement à partir de la page web de l’éditeur n’est pas prise en charge. Seul le workflow d’authentification de service à service doit être utilisé.

## <a name="next-steps"></a>Étapes suivantes

- Si ce n’est déjà fait, inscrivez votre application SaaS via le [portail Azure](https://ms.portal.azure.com), comme expliqué dans [Inscrire une application Azure AD](./pc-saas-registration.md).  Ensuite, utilisez la version la plus récente de cette interface pour le développement : [API d’abonnement au traitement SaaS v2](pc-saas-fulfillment-subscription-api.md) et [API d’opérations de traitement SaaS v2](pc-saas-fulfillment-operations-api.md).
