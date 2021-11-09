---
title: Ajouter des détails techniques pour une offre SaaS sur la Place de marché Azure
description: Fournissez des détails techniques sur une offre SaaS sur la Place de marché Azure.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 10/25/2021
ms.openlocfilehash: 6a0b7494d5ce33527640c144faf8630d523ab941
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131061368"
---
# <a name="add-technical-details-for-a-saas-offer"></a>Ajouter des détails techniques pour une offre SaaS

Cet article explique comment entrer des détails techniques qui permettent à la place de marché commerciale de Microsoft de se connecter à votre solution. Cette connexion permet d’approvisionner votre offre pour le client, s’il choisit de l’acquérir et de la gérer. Pour plus d’informations sur ces paramètres, consultez [Informations techniques](plan-saas-offer.md#technical-information).

> [!NOTE]
> Si vous décidez de traiter les transactions de façon indépendante, vous ne verrez pas cette option. Au lieu de cela, passez à [Comment vendre votre offre SaaS](create-new-saas-offer-marketing.md).

## <a name="technical-configuration"></a>Configuration technique

Sous l’onglet **Configuration technique**, vous allez définir les détails techniques que la place de marché commerciale utilise pour communiquer avec votre application ou solution SaaS.

- **URL de la page d’arrivée** (obligatoire) : définissez l’URL du site SaaS (par exemple : `https://contoso.com/signup`) sur laquelle les clients finaux arriveront après avoir acquis votre offre sur la place de marché commerciale et déclenché le processus de configuration à partir de l’abonnement SaaS nouvellement créé.

  > [!IMPORTANT]
  > Votre page d’arrivée doit être opérationnelle en permanence. C’est la seule façon pour vous d’être averti des nouveaux achats de vos offres SaaS effectuées dans la place de marché commerciale, ou des demandes de configuration d’un abonnement actif à une offre. N’incluez pas le signe dièse (#) dans l’URL de la page de destination. Dans le cas contraire, les clients ne seront pas en mesure d’accéder à votre page de destination.

- **Webhook de connexion** (obligatoire) : pour tous les événements asynchrones que Microsoft doit vous envoyer (par exemple, l’abonnement SaaS a été annulé), nous vous demandons de [fournir une URL de webhook de connexion](./partner-center-portal/pc-saas-fulfillment-api-v2.md#implementing-a-webhook-on-the-saas-service). Nous appellerons cette URL pour vous informer de l’événement.

  > [!IMPORTANT]
  > Votre webhook doit être opérationnel en permanence, car c’est pour vous la seule façon d’être informé des mises à jour concernant les abonnements SaaS de vos clients achetés via le marketplace commercial.

- **ID de locataire Azure Active Directory** (obligatoire) : pour trouver l’ID de locataire de votre application Azure Active Directory (AD), accédez au panneau [Inscriptions d’applications](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) dans Azure Active Directory. Dans la colonne **Nom d’affichage**, sélectionnez l’application. Recherchez ensuite le numéro d’**ID de répertoire (locataire)** référencé (par exemple, `50c464d3-4930-494c-963c-1e951d15360e`).

- **ID d’application Azure Active Directory** (obligatoire) : pour rechercher votre [ID d’application](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in), accédez au panneau [inscriptions d’applications](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) dans Azure Active Directory. Dans la colonne **Nom d’affichage**, sélectionnez l’application. Recherchez ensuite le numéro d’ID d’application (client) référencé (par exemple, `50c464d3-4930-494c-963c-1e951d15360e`).

Avant de passer à l’onglet suivant, sélectionnez **Enregistrer le brouillon** : Vue d’ensemble du plan.

## <a name="next-steps"></a>Étapes suivantes

- [Créer des plans pour une offre SaaS](create-new-saas-offer-plans.md)
