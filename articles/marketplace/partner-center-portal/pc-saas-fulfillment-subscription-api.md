---
title: API d’abonnement au traitement SaaS v2 dans la Place de marché commerciale Microsoft
description: Découvrez comment utiliser les API d’abonnement, qui font partie des API de traitement SaaS version 2, pour gérer une offre SaaS sur Microsoft AppSource, la Place de marché Azure et le Portail Azure.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 10/27/2021
author: saasguide
ms.author: souchak
ms.openlocfilehash: ace7aac5a308621ca8032677e7ca7eec3858bdb7
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132064009"
---
# <a name="saas-fulfillment-subscription-apis-v2-in-microsoft-commercial-marketplace"></a>API d’abonnement au traitement SaaS v2 dans la Place de marché commerciale Microsoft

Cet article décrit la version 2 des API d’abonnement au traitement SaaS.

## <a name="resolve-a-purchased-subscription"></a>Résoudre un abonnement acheté

Le point de terminaison de résolution permet à l’éditeur d’échanger le jeton d’identification d’achat de la Place de marché commerciale (appelé *token* dans [Acheté mais pas encore activé](pc-saas-fulfillment-life-cycle.md#purchased-but-not-yet-activated-pendingfulfillmentstart)) contre un ID d’abonnement SaaS acheté de manière permanente et ses détails.

Lorsqu’un client est redirigé vers l’URL de la page d’arrivée du partenaire, le jeton d’identification du client est transmis comme paramètre *token* dans cet appel d’URL. Le partenaire doit utiliser ce jeton et effectuer une requête pour le résoudre. La réponse de l’API Résolution contient l’ID d’abonnement SaaS et d’autres détails permettant d’identifier l’achat de manière unique. Le *token* fourni avec l’appel d’URL de la page d’arrivée est généralement valide 24 heures. Si le *token* que vous recevez a déjà expiré, nous vous recommandons de fournir les instructions suivantes à l’utilisateur final :

« Nous n’avons pas pu identifier cet achat. Veuillez rouvrir cet abonnement SaaS dans le portail Azure ou dans le Centre d’administration Microsoft 365, puis resélectionner « Configurer le compte » ou « Gérer le compte ».

L’appel de l’API Résolution renverra les détails de l’abonnement et l’état des abonnements SaaS dans tous les états pris en charge.

### <a name="post-httpsmarketplaceapimicrosoftcomapisaassubscriptionsresolveapi-versionapiversion"></a>Post `https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=<ApiVersion>`

*Paramètres de requête :*

|  Paramètre         | Valeur            |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  Utilisez 2018-08-31.   |

*En-têtes de requête :*

|  Paramètre         | Valeur             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json` |
|  `x-ms-requestid`    |  Valeur de chaîne unique pour le suivi de la requête du client, de préférence un GUID. Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse. |
|  `x-ms-correlationid` |  Valeur de chaîne unique pour l’opération sur le client. Ce paramètre sert à corréler tous les événements de l’opération client avec les événements côté serveur. Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse.  |
|  `authorization`     |  Jeton d’accès unique qui identifie le serveur de publication qui effectue cet appel d’API. Le format est `"Bearer <accessaccess_token>"` lorsque la valeur de jeton est récupérée par le serveur de publication, comme expliqué dans [Obtenir un jeton basé sur l’application Azure AD](./pc-saas-registration.md#get-the-token-with-an-http-post). |
|  `x-ms-marketplace-token`  | Paramètre *token* d’identification d’achat de la Place de marché à résoudre.  Le jeton est transmis dans l’appel de l’URL de la page d’arrivée lorsque le client est redirigé vers le site web du partenaire SaaS (par exemple : `https://contoso.com/signup?token=<token><authorization_token>`). <br> <br>  La valeur codée de *token* fait partie de l’URL de la page d’arrivée et doit donc être décodée avant d’être utilisée en tant que paramètre dans cet appel d’API.  <br> <br> Voici un exemple de chaîne codée dans l’URL : `contoso.com/signup?token=ab%2Bcd%2Fef`, où *token* est `ab%2Bcd%2Fef`.  Le même jeton décodé sera : `Ab+cd/ef` |
| | |

*Codes de réponse :*

Code : 200 Retourne des identificateurs d’abonnement SaaS uniques en fonction de la valeur `x-ms-marketplace-token` fournie.

Exemple de corps de réponse :

```json
{
  "id": "<guid>", // purchased SaaS subscription ID
  "subscriptionName": "Contoso Cloud Solution", // SaaS subscription name
  "offerId": "offer1", // purchased offer ID
  "planId": "silver", // purchased offer's plan ID
  "quantity": "20", // number of purchased seats, might be empty if the plan is not per seat
  "subscription": { // full SaaS subscription details, see Get Subscription APIs response body for full description
    "id": "<guid>",
    "publisherId": "contoso",
    "offerId": "offer1",
    "name": "Contoso Cloud Solution",
    "saasSubscriptionStatus": " PendingFulfillmentStart ",
    "beneficiary": {
      "emailId": "test@test.com",
      "objectId": "<guid>",
      "tenantId": "<guid>",
      "pid": "<ID of the user>"
    },
    "purchaser": {
      "emailId": "test@test.com",
      "objectId": "<guid>",
      "tenantId": "<guid>",
      "pid": "<ID of the user>"
    },
    "planId": "silver",
    "term": {
      "termUnit": "P1M",
      "startDate": "2019-05-31",
      "endDate": "2019-06-29"
    },
    "isTest": true,
    "isFreeTrial": false,
    "allowedCustomerOperations": ["Delete", "Update", "Read"],
    "sandboxType": "None",
    "sessionMode": "None"
  }
}

```

Code : 400 Requête incorrecte. `x-ms-marketplace-token` est manquant, incorrect, non valide ou arrivé à expiration.

Code : 403 Interdit. Le jeton d’autorisation n’est pas valide, a expiré ou n’a pas été fourni.  La demande tente d’accéder à un abonnement SaaS pour une offre publiée avec un ID d’application Azure AD différent de celui utilisé pour créer le jeton d’autorisation.

Cette erreur est souvent le symptôme de l’absence d’une inscription [SaaS](pc-saas-registration.md) correcte.

Code : 500 Erreur interne du serveur.  Renouvelez l’appel d’API.  Si l’erreur persiste, contactez le [support technique Microsoft](https://go.microsoft.com/fwlink/?linkid=2165533).

## <a name="activate-a-subscription"></a>Activer un abonnement

Une fois le compte SaaS configuré pour un utilisateur final, l’éditeur doit appeler l’API Activer l’abonnement côté Microsoft.  Le client ne sera facturé que si cet appel d’API réussit.

### <a name="post-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidactivateapi-versionapiversion"></a>Post `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/activate?api-version=<ApiVersion>`

*Paramètres de requête :*

|  Paramètre         | Valeur             |
|  --------   |  ---------------  |
| `ApiVersion`  |  Utilisez 2018-08-31.   |
| `subscriptionId` | Identificateur unique de l’abonnement SaaS acheté.  Cet ID est obtenu après résolution du jeton d’autorisation de la Place de marché commerciale à l’aide de l’[API Résolution](#resolve-a-purchased-subscription).
 |

*En-têtes de requête :*

|  Paramètre         | Valeur             |
|  ---------------   |  ---------------  |
| `content-type`       |  `application/json`  |
| `x-ms-requestid`     |  Valeur de chaîne unique pour le suivi de la requête du client, de préférence un GUID.  Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse. |
| `x-ms-correlationid` |  Valeur de chaîne unique pour l’opération sur le client.  Cette chaîne sert à corréler tous les événements de l’opération client avec les événements côté serveur.  Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse. |
| `authorization`      |  Jeton d’accès unique qui identifie le serveur de publication qui effectue cet appel d’API. Le format est `"Bearer <access_token>"` lorsque la valeur de jeton est récupérée par le serveur de publication, comme expliqué dans [Obtenir un jeton basé sur l’application Azure AD](./pc-saas-registration.md#get-the-token-with-an-http-post). |

*Exemple de demande de charge utile :*

```json
{  // needed for validation of the activation request
  "planId": "gold", // purchased plan, cannot be empty
  "quantity": "" // purchased number of seats, can be empty if plan is not per seat
}
```

*Codes de réponse :*

Code : 200 L’abonnement a été marqué comme Subscribed du côté Microsoft.

Il n’y a aucun corps de réponse pour cet appel.

Code : 400 Demande incorrecte : échec de la validation.

* `planId` n’existe pas dans la charge utile de la demande.
* `planId` dans la charge utile de la demande ne correspond pas à l’achat effectué.
* `quantity` dans la charge utile de la demande ne correspond pas à l’achat effectué.
* L’abonnement SaaS est à l’état *Subscribed* ou *Suspended*.

Code : 403 Interdit. Le jeton d’autorisation n’est pas valide, a expiré ou n’a pas été fourni. La demande tente d’accéder à un abonnement SaaS pour une offre publiée avec un ID d’application Azure AD différent de celui utilisé pour créer le jeton d’autorisation.

Cette erreur est souvent le symptôme de l’absence d’une inscription [SaaS](pc-saas-registration.md) correcte.

Code : 404 Introuvable. L’abonnement SaaS est à l’état *Unsubscribed*.

Code : 500 Erreur interne du serveur.  Renouvelez l’appel d’API.  Si l’erreur persiste, contactez le [support technique Microsoft](https://go.microsoft.com/fwlink/?linkid=2165533).

## <a name="get-list-of-all-subscriptions"></a>Obtenir la liste de tous les abonnements

Cette API récupère une liste de tous les abonnements SaaS achetés pour toutes les offres publiées par l’éditeur sur la Place de marché commerciale.  Les abonnements SaaS avec tous les états possibles sont retournés. Les abonnements SaaS à l’état « Unsubscribed » sont également retournés, car ces informations ne sont pas supprimées côté Microsoft.

L’API retourne des résultats paginés (100 par page).

### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionsapi-versionapiversion"></a>Get `https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=<ApiVersion>`

*Paramètres de requête :*

|  Paramètre         | Valeur             |
|  --------   |  ---------------  |
| `ApiVersion`  |  Utilisez 2018-08-31.  |
| `continuationToken`  | Paramètre facultatif. Pour récupérer la première page de résultats, laissez vide.  Utilisez la valeur retournée dans le paramètre `@nextLink` pour récupérer la page suivante. |

*En-têtes de requête :*

|  Paramètre         | Valeur             |
|  ---------------   |  ---------------  |
| `content-type`       |  `application/json`  |
| `x-ms-requestid`     |  Valeur de chaîne unique pour le suivi de la requête du client, de préférence un GUID. Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse. |
| `x-ms-correlationid` |  Valeur de chaîne unique pour l’opération sur le client.  Ce paramètre sert à corréler tous les événements de l’opération client avec les événements côté serveur.  Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse. |
| `authorization`      |  Jeton d’accès unique qui identifie l’éditeur effectuant cet appel d’API.  Le format est `"Bearer <access_token>"` lorsque la valeur de jeton est récupérée par le serveur de publication, comme expliqué dans [Obtenir un jeton basé sur l’application Azure AD](./pc-saas-registration.md#get-the-token-with-an-http-post). |

*Codes de réponse :*

Code : 200 Retourne la liste de tous les abonnements existants pour toutes les offres proposées par cet éditeur, en fonction du jeton d’autorisation de l’éditeur.

*Exemple de corps de réponse :*

```json
{
  "subscriptions": [
    {
      "id": "<guid>", // purchased SaaS subscription ID
      "name": "Contoso Cloud Solution", // SaaS subscription name
      "publisherId": "contoso", // publisher ID
      "offerId": "offer1", // purchased offer ID
      "planId": "silver", // purchased plan ID
      "quantity": "10", // purchased amount of seats, will be empty if plan is not per seat
      "beneficiary": { // email address, user ID and tenant ID for which SaaS subscription was purchased.
        "emailId": " test@contoso.com",
        "objectId": "<guid>",
        "tenantId": "<guid>",
        "pid": "<ID of the user>"
      },
      "purchaser": { // email address, user ID and tenant ID that purchased the SaaS subscription. These could be different from beneficiary information for reseller (CSP) purchase
        "emailId": " test@contoso.com",
        "objectId": "<guid>",
        "tenantId": "<guid>",
        "pid": "<ID of the user>"
      },
      "term": { // The period for which the subscription was purchased.
        "startDate": "2019-05-31", //format: YYYY-MM-DD. This is the date when the subscription was activated by the ISV and the billing started. This field is relevant only for Active and Suspended subscriptions.
        "endDate": "2019-06-30", // This is the last day the subscription is valid. Unless stated otherwise, the automatic renew will happen the next day. This field is relevant only for Active and Suspended subscriptions.
        "termUnit": "P1M" // where P1M is monthly and P1Y is yearly. Also reflected in the startDate and endDate values
      },
      "allowedCustomerOperations": ["Read", "Update", "Delete"], // Indicates operations allowed on the SaaS subscription for beneficiary. For CSP-initiated purchases, this will always be "Read" because the customer cannot update or delete subscription in this flow.  Purchaser can perform all operations on the subscription.
      "sessionMode": "None", // not relevant
      "isFreeTrial": true, // true - the customer subscription is currently in free trial, false - the customer subscription is not currently in free trial. (Optional field -– if not returned, the value is false.)
      "isTest": false, // not relevant
      "sandboxType": "None", // not relevant
      "saasSubscriptionStatus": "Subscribed" // Indicates the status of the operation. Can be one of the following: PendingFulfillmentStart, Subscribed, Suspended or Unsubscribed.
    },
    // next SaaS subscription details, might be a different offer
    {
      "id": "<guid1>",
      "name": "Contoso Cloud Solution1",
      "publisherId": "contoso",
      "offerId": "offer2",
      "planId": "gold",
      "quantity": "",
      "beneficiary": {
        "emailId": " test@contoso.com",
        "objectId": "<guid>",
        "tenantId": "<guid>",
        "pid": "<ID of the user>"
      },
      "purchaser": {
        "emailId": "purchase@csp.com ",
        "objectId": "<guid>",
        "tenantId": "<guid>",
        "pid": "<ID of the user>"
      },
      "term": {
        "startDate": "2019-05-31",
        "endDate": "2020-04-30",
        "termUnit": "P1Y"
      },
      "allowedCustomerOperations": ["Read"],
      "sessionMode": "None",
      "isFreeTrial": false,
      "isTest": false,
      "sandboxType": "None",
      "saasSubscriptionStatus": "Suspended"
    }
  ],
  "@nextLink": "https:// https://marketplaceapi.microsoft.com/api/saas/subscriptions/?continuationToken=%5b%7b%22token%22%3a%22%2bRID%3a%7eYeUDAIahsn22AAAAAAAAAA%3d%3d%23RT%3a1%23TRC%3a2%23ISV%3a1%23FPC%3aAgEAAAAQALEAwP8zQP9%2fFwD%2b%2f2FC%2fwc%3d%22%2c%22range%22%3a%7b%22min%22%3a%22%22%2c%22max%22%3a%2205C1C9CD673398%22%7d%7d%5d&api-version=2018-08-31" // url that contains continuation token to retrieve next page of the SaaS subscriptions list, if empty or absent, this is the last page. ISV can use this url as is to retrieve the next page or extract the value of continuation token from this url.
}
```

Si aucun abonnement SaaS acheté n’est trouvé pour cet éditeur, un corps de réponse vide est retourné.

Code : 403 Interdit. Le jeton d'autorisation n'est pas disponible, n’est pas valide ou a expiré.

Cette erreur est souvent le symptôme de l’absence d’une inscription [SaaS](pc-saas-registration.md) correcte. 

Code : 500 Erreur interne du serveur. Renouvelez l’appel d’API.  Si l’erreur persiste, contactez le [support technique Microsoft](https://go.microsoft.com/fwlink/?linkid=2165533).

## <a name="get-subscription"></a>Obtenir un abonnement

Cette API récupère un abonnement SaaS acheté spécifié pour une offre SaaS publiée par l’éditeur sur la Place de marché commerciale. Utilisez cet appel afin d’obtenir toutes les informations disponibles pour un abonnement SaaS spécifique par son ID, au lieu d’appeler l’API utilisée pour obtenir la liste de tous les abonnements.

### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Get `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Paramètres de requête :*

|  Paramètre         | Valeur             |
|  ---------------   |  ---------------  |
| `ApiVersion`        |   Utilisez 2018-08-31. |
| `subscriptionId`     |  Identificateur unique de l’abonnement SaaS acheté.  Cet ID est obtenu après résolution du jeton d’autorisation de la Place de marché à l’aide de l’API Résolution. |

*En-têtes de requête :*

|  Paramètre         | Valeur             |
|  ---------------   |  ---------------  |
|  `content-type`      |  `application/json`  |
|  `x-ms-requestid`    |  Valeur de chaîne unique pour le suivi de la requête du client, de préférence un GUID. Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse. |
|  `x-ms-correlationid` |  Valeur de chaîne unique pour l’opération sur le client.  Ce paramètre sert à corréler tous les événements de l’opération client avec les événements côté serveur.  Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse. |
|  `authorization`     | Jeton d’accès unique qui identifie l’éditeur effectuant cet appel d’API. Le format est `"Bearer <access_token>"` lorsque la valeur de jeton est récupérée par le serveur de publication, comme expliqué dans [Obtenir un jeton basé sur l’application Azure AD](./pc-saas-registration.md#get-the-token-with-an-http-post).  |

*Codes de réponse :*

Code : 200 Retourne les détails d’un abonnement SaaS basé sur la valeur `subscriptionId` fournie.

*Exemple de corps de réponse :*

```json
{
  "id": "<guid>", // purchased SaaS subscription ID
  "name": "Contoso Cloud Solution", // SaaS subscription name
  "publisherId": "contoso", // publisher ID
  "offerId": "offer1", // purchased offer ID
  "planId": "silver", // purchased plan ID
  "quantity": "10", // purchased amount of seats, will be empty if plan is not per seat
  "beneficiary": { // email address, user ID and tenant ID for which SaaS subscription is purchased.
    "emailId": "test@contoso.com",
    "objectId": "<guid>",
    "tenantId": "<guid>",
    "pid": "<ID of the user>"
  },
  "purchaser": { // email address ,user ID and tenant ID that purchased the SaaS subscription.  These could be different from beneficiary information for reseller (CSP) scenario
    "emailId": "test@test.com",
    "objectId": "<guid>",
    "tenantId": "<guid>",
    "pid": "<ID of the user>"
  },
  "allowedCustomerOperations": ["Read", "Update", "Delete"], // Indicates operations allowed on the SaaS subscription for beneficiary.  For CSP-initiated purchases, this will always be "Read" because the customer cannot update or delete subscription in this flow.  Purchaser can perform all operations on the subscription.
  "sessionMode": "None", // not relevant
  "isFreeTrial": false, // true - the customer subscription is currently in free trial, false - the customer subscription is not currently in free trial. Optional field – if not returned the value is false.
  "isTest": false, // not relevant
  "sandboxType": "None", // not relevant
  "saasSubscriptionStatus": " Subscribed ", // Indicates the status of the operation: PendingFulfillmentStart, Subscribed, Suspended or Unsubscribed.
  "term": { // the period for which the subscription was purchased
    "startDate": "2019-05-31", //format: YYYY-MM-DD. This is the date when the subscription was activated by the ISV and the billing started. This field is relevant only for Active and Suspended subscriptions.
    "endDate": "2019-06-29", // This is the last day the subscription is valid. Unless stated otherwise, the automatic renew will happen the next day. This field is relevant only for Active and Suspended subscriptions.
    "termUnit": "P1M" //where P1M is monthly and P1Y is yearly. Also reflected in the startDate and endDate values.
  }
}
```

Code : 403 Interdit. Le jeton d’autorisation n’est pas valide, a expiré ou n’a pas été fourni. La demande tente d’accéder à un abonnement SaaS pour une offre publiée avec un ID d’application Azure AD différent de celui utilisé pour créer le jeton d’autorisation.

Cette erreur est souvent le symptôme de l’absence d’une inscription [SaaS](pc-saas-registration.md) correcte. 

Code : 404 Introuvable.  L’abonnement SaaS avec la valeur `subscriptionId` spécifiée est introuvable.

Code : 500 Erreur interne du serveur.  Renouvelez l’appel d’API.  Si l’erreur persiste, contactez le [support technique Microsoft](https://go.microsoft.com/fwlink/?linkid=2165533).

## <a name="list-available-plans"></a>Liste des plans disponibles

Cette API récupère tous les plans pour une offre SaaS identifiée par la valeur `subscriptionId` d’un achat spécifique de cette offre.  Utilisez cet appel pour obtenir une liste de tous les plans privés et publics que le bénéficiaire d’un abonnement SaaS peut mettre à jour pour l’abonnement.  Les plans retournés seront disponibles dans la même zone géographique que le plan déjà acheté.

Cet appel renvoie une liste des plans disponibles pour ce client en plus de celui que vous avez déjà acheté.  La liste peut être présentée à un utilisateur final sur le site de l’éditeur.  Un utilisateur final peut modifier le plan d’abonnement au profit d’un des plans de la liste retournée.  La modification du plan au profit d’un plan qui ne figure pas dans la liste échoue.

### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidlistavailableplansapi-versionapiversion"></a>Get `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/listAvailablePlans?api-version=<ApiVersion>`

*Paramètres de requête :*

|  Paramètre         | Valeur             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  Utilisez 2018-08-31.  |
|  `subscriptionId`    |  Identificateur unique de l’abonnement SaaS acheté.  Cet ID est obtenu après résolution du jeton d’autorisation de la Place de marché à l’aide de l’API Résolution. |

*En-têtes de requête :*

|  Paramètre         | Valeur             |
|  ---------------   |  ---------------  |
|   `content-type`     |  `application/json` |
|   `x-ms-requestid`   |  Valeur de chaîne unique pour le suivi de la requête du client, de préférence un GUID.  Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse. |
|  `x-ms-correlationid`  |  Valeur de chaîne unique pour l’opération sur le client.  Ce paramètre sert à corréler tous les événements de l’opération client avec les événements côté serveur.  Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse. |
|  `authorization`     |  Jeton d’accès unique qui identifie l’éditeur effectuant cet appel d’API.  Le format est `"Bearer <access_token>"` lorsque la valeur de jeton est récupérée par le serveur de publication, comme expliqué dans [Obtenir un jeton basé sur l’application Azure AD](./pc-saas-registration.md#get-the-token-with-an-http-post).  |

*Codes de réponse :*

Code : 200 Renvoie la liste de tous les plans disponibles pour un abonnement SaaS existant, y compris celui déjà acheté.

Exemple de corps de réponse :

```json
{
  "plans": [
    {
      "planId": "Platinum001",
      "displayName": "Private platinum plan for Contoso", // display name of the plan as it appears in the marketplace
      "isPrivate": true //true or false
    },
    {
      "planId": "gold",
      "displayName": "Gold plan for Contoso",
      "isPrivate": false //true or false
    }
  ]
}
```

Si `subscriptionId` est introuvable, un corps de réponse vide est retourné.

Code : 403 Interdit. Le jeton d’autorisation n’est pas valide, a expiré ou n’a pas été fourni.  La demande tente peut-être d’accéder à un abonnement SaaS pour une offre qui est publiée avec un ID d’application Azure AD différent de celui utilisé pour créer le jeton d’autorisation.

Cette erreur est souvent le symptôme de l’absence d’une inscription [SaaS](pc-saas-registration.md) correcte. 

Code : 500 Erreur interne du serveur.  Renouvelez l’appel d’API.  Si l’erreur persiste, contactez le [support technique Microsoft](https://go.microsoft.com/fwlink/?linkid=2165533).

## <a name="change-the-plan-on-the-subscription"></a>Modifier le plan de l’abonnement

Utilisez cette API pour mettre à jour le plan existant acheté pour un abonnement SaaS avec un nouveau plan (public ou privé).  L’éditeur doit appeler cette API lorsqu’un plan est modifié côté éditeur pour un abonnement SaaS acheté sur la Place de marché commerciale.

Cette API peut être appelée uniquement pour des abonnements à l’état *Active*.  Tout plan peut être remplacé par un autre plan existant (public ou privé), mais pas par lui-même.  Pour les plans privés, le locataire du client doit être défini dans le cadre de l’audience du plan dans l’Espace partenaires.

### <a name="patch-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Patch `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Paramètres de requête :*

|  Paramètre         | Valeur             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  Utilisez 2018-08-31.  |
| `subscriptionId`     | Identificateur unique de l’abonnement SaaS acheté.  Cet ID est obtenu après résolution du jeton d’autorisation de la Place de marché à l’aide de l’API Résolution. |

*En-têtes de requête :*
 
|  Paramètre         | Valeur             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json`  |
|  `x-ms-requestid`    | Valeur de chaîne unique pour le suivi de la requête du client, de préférence un GUID. Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse.  |
|  `x-ms-correlationid`  | Valeur de chaîne unique pour l’opération sur le client.  Ce paramètre sert à corréler tous les événements de l’opération client avec les événements côté serveur.  Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse.  |
|  `authorization`     |  Jeton d’accès unique qui identifie l’éditeur effectuant cet appel d’API.  Le format est `"Bearer <access_token>"` lorsque la valeur de jeton est récupérée par le serveur de publication, comme expliqué dans [Obtenir un jeton basé sur l’application Azure AD](./pc-saas-registration.md#get-the-token-with-an-http-post). |

*Exemple de demande de charge utile :*

```json
{
  "planId": "gold" // the ID of the new plan to be purchased
}
```

*Codes de réponse :*

Code : 202 La demande de modification du plan a été acceptée et traitée de façon asynchrone.  Le partenaire doit interroger l’**URL Operation-Location** pour déterminer la réussite ou l’échec de la demande de changement de plan.  L’interrogation doit être effectuée à intervalles réguliers (quelques secondes) jusqu’à ce que l’état final *Failed*, *Succeed* ou *Conflict* s’affiche pour l’opération.  L’état final de l’opération doit être retourné rapidement, mais peut prendre plusieurs minutes dans certains cas.

Le partenaire recevra également une notification de webhook lorsque l’action sera prête à se terminer sur la Place de marché commerciale.  Ce n’est qu’après avoir reçu cette notification que l’éditeur devra modifier le plan côté éditeur.

*En-têtes de réponse :*

|  Paramètre         | Valeur             |
|  ---------------   |  ---------------  |
|  `Operation-Location`        |  URL pour obtenir l’état de l’opération.  Par exemple : `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=2018-08-31`. |

Code : 400 Demande incorrecte : échecs de validation.

* Le nouveau plan n’existe pas ou n’est pas disponible pour cet abonnement SaaS spécifique.
* Le nouveau plan est identique au plan actuel.
* L’état de l’abonnement SaaS n’est pas *Subscribed*.
* L’opération de mise à jour pour un abonnement SaaS n’est pas incluse dans `allowedCustomerOperations`.

Code : 403 Interdit. Le jeton d’autorisation n’est pas valide, a expiré ou n’a pas été fourni.  La demande tente d’accéder à un abonnement SaaS pour une offre publiée avec un ID d’application Azure AD différent de celui utilisé pour créer le jeton d’autorisation.

Cette erreur est souvent le symptôme de l’absence d’une inscription [SaaS](pc-saas-registration.md) correcte.

Code : 404 Introuvable.  L’abonnement SaaS avec `subscriptionId` est introuvable.

Code : 500 Erreur interne du serveur.  Renouvelez l’appel d’API.  Si l’erreur persiste, contactez le [support technique Microsoft](https://go.microsoft.com/fwlink/?linkid=2165533).

>[!NOTE]
>Le plan ou la quantité de postes peut être modifié(e), mais pas simultanément.

>[!Note]
>Cette API peut être appelée uniquement après avoir obtenu l’approbation explicite de l’utilisateur final pour la modification.

## <a name="change-the-quantity-of-seats-on-the-saas-subscription"></a>Modifier la quantité de postes de l’abonnement SaaS

Utilisez cette API pour mettre à jour (augmenter ou diminuer) la quantité de postes achetés pour un abonnement SaaS.  L’éditeur doit appeler cette API lorsque le nombre de postes est modifié côté éditeur pour un abonnement SaaS créé sur la Place de marché commerciale.

La quantité de postes ne peut pas être supérieure à celle autorisée dans le plan actuel.  Dans ce cas, l’éditeur doit changer le plan avant de modifier la quantité de postes.

### <a name="patch-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Patch `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Paramètres de requête :*

|  Paramètre         | Valeur             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  Utilisez 2018-08-31.  |
|  `subscriptionId`     | Identificateur unique de l’abonnement SaaS acheté.  Cet ID est obtenu après résolution du jeton d’autorisation de la Place de marché à l’aide de l’API Résolution.  |

*En-têtes de requête :*
 
|  Paramètre         | Valeur             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json`  |
|  `x-ms-requestid`    | Valeur de chaîne unique pour le suivi de la requête du client, de préférence un GUID.  Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse.  |
|  `x-ms-correlationid`  | Valeur de chaîne unique pour l’opération sur le client.  Ce paramètre sert à corréler tous les événements de l’opération client avec les événements côté serveur.  Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse.  |
|  `authorization`     | Jeton d’accès unique qui identifie l’éditeur effectuant cet appel d’API.  Le format est `"Bearer <access_token>"` lorsque la valeur de jeton est récupérée par le serveur de publication, comme expliqué dans [Obtenir un jeton basé sur l’application Azure AD](./pc-saas-registration.md#get-the-token-with-an-http-post).  |

*Exemple de demande de charge utile :*

```json
{
  "quantity": 5 // the new amount of seats to be purchased
}
```

*Codes de réponse :*

Code : 202 La demande de modification de la quantité a été acceptée et traitée de façon asynchrone. Le partenaire doit interroger l’**URL Operation-Location** pour déterminer la réussite ou l’échec de la demande de changement de quantité.  L’interrogation doit être effectuée à intervalles réguliers (quelques secondes) jusqu’à ce que l’état final *Failed*, *Succeed* ou *Conflict* s’affiche pour l’opération.  L’état final de l’opération doit être retourné rapidement, mais peut prendre plusieurs minutes dans certains cas.

Le partenaire recevra également une notification de webhook lorsque l’action sera prête à se terminer sur la Place de marché commerciale.  Ce n’est qu’après avoir reçu cette notification que l’éditeur devra modifier la quantité côté éditeur.

*En-têtes de réponse :*

|  Paramètre         | Valeur             |
|  ---------------   |  ---------------  |
|  `Operation-Location`        |  Lien vers une ressource pour obtenir le statut de l’opération.  Par exemple : `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=2018-08-31`.  |

Code : 400 Demande incorrecte : échecs de validation.

* La nouvelle quantité est supérieure ou inférieure à la limite actuelle du plan.
* La nouvelle quantité est manquante.
* La nouvelle quantité est identique à la quantité actuelle.
* L’état de l’abonnement SaaS n’est pas Abonné.
* L’opération de mise à jour pour un abonnement SaaS n’est pas incluse dans `allowedCustomerOperations`.

Code : 403 Interdit.  Le jeton d’autorisation n’est pas valide, a expiré ou n’a pas été fourni.  La demande tente d’accéder à un abonnement qui n’appartient pas à l’éditeur actuel.

Cette erreur est souvent le symptôme de l’absence d’une inscription [SaaS](pc-saas-registration.md) correcte. 

Code : 404 Introuvable.  L’abonnement SaaS avec `subscriptionId` est introuvable.

Code : 500 Erreur interne du serveur.  Renouvelez l’appel d’API.  Si l’erreur persiste, contactez le [support technique Microsoft](https://go.microsoft.com/fwlink/?linkid=2165533).

>[!Note]
>Un plan ou une quantité peut être modifié(e), mais pas les deux simultanément.

>[!Note]
>Cette API peut être appelée uniquement après avoir obtenu l’approbation explicite de l’utilisateur final pour la modification.

## <a name="cancel-a-subscription"></a>Annuler un abonnement

Utilisez cette API pour annuler un abonnement SaaS spécifié.  L’éditeur n’a pas besoin d’utiliser cette API, et nous vous recommandons de diriger les clients vers la Place de marché commerciale afin d’annuler les abonnements SaaS.

Si l’éditeur décide d’implémenter l’annulation de l’abonnement SaaS acheté sur la Place de marché commerciale côté éditeur, il doit appeler cette API.  Une fois cet appel terminé, l’état de l’abonnement devient *Unsubscribed* côté Microsoft.

Le client ne sera pas facturé si un abonnement est annulé durant les périodes de grâce suivantes :

* 24 heures pour un abonnement mensuel après activation.
* 14 jours pour un abonnement annuel après activation.

Le client sera facturé si un abonnement est annulé après les périodes de grâce indiquées ci-dessus.  Le client perdra l’accès à l’abonnement SaaS côté Microsoft immédiatement après l’annulation. 

### <a name="delete-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Delete `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Paramètres de requête :*

|  Paramètre         | Valeur             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  Utilisez 2018-08-31.  |
|  `subscriptionId`     | Identificateur unique de l’abonnement SaaS acheté.  Cet ID est obtenu après résolution du jeton d’autorisation de la Place de marché à l’aide de l’API Résolution.  |

*En-têtes de requête :*
 
|  Paramètre         | Valeur             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json`  |
|  `x-ms-requestid`    | Valeur de chaîne unique pour le suivi de la requête du client, de préférence un GUID.  Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse.  |
|  `x-ms-correlationid`  | Valeur de chaîne unique pour l’opération sur le client.  Ce paramètre sert à corréler tous les événements de l’opération client avec les événements côté serveur.  Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse.  |
|  `authorization`     |  Jeton d’accès unique qui identifie le serveur de publication qui effectue cet appel d’API.  Le format est `"Bearer <access_token>"` lorsque la valeur de jeton est récupérée par le serveur de publication, comme expliqué dans [Obtenir un jeton basé sur l’application Azure AD](./pc-saas-registration.md#get-the-token-with-an-http-post). |

*Codes de réponse :*

Code : 202 La demande de désabonnement a été acceptée et traitée de façon asynchrone.  Le partenaire doit interroger l’**URL Operation-Location** pour déterminer la réussite ou l’échec de cette demande.  L’interrogation doit être effectuée à intervalles réguliers (quelques secondes) jusqu’à ce que l’état final *Failed*, *Succeed* ou *Conflict* s’affiche pour l’opération.  L’état final de l’opération doit être retourné rapidement, mais peut prendre plusieurs minutes dans certains cas.

Le partenaire recevra également une notification de webhook lorsque l’action sera terminée sur la Place de marché commerciale.  Ce n’est qu’après avoir reçu cette notification que l’éditeur devra annuler l’abonnement côté éditeur.

*En-têtes de réponse :*

|  Paramètre         | Valeur             |
|  ---------------   |  ---------------  |
|  `Operation-Location`        |  Lien vers une ressource pour obtenir le statut de l’opération.  Par exemple : `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=2018-08-31`. |

Code : 400 Requête incorrecte.  Le paramètre Delete ne fait pas partie de la liste `allowedCustomerOperations` pour cet abonnement SaaS.

Code : 403 Interdit.  Le jeton d’autorisation n’est pas valide, a expiré ou n’est pas disponible. La demande tente d’accéder à un abonnement SaaS pour une offre publiée avec un ID d’application Azure AD différent de celui utilisé pour créer le jeton d’autorisation.

Cette erreur est souvent le symptôme de l’absence d’une inscription [SaaS](pc-saas-registration.md) correcte.

Code : 404 Introuvable.  L’abonnement SaaS avec `subscriptionId` est introuvable.

Code : 500 Erreur interne du serveur. Renouvelez l’appel d’API.  Si l’erreur persiste, contactez le [support technique Microsoft](https://go.microsoft.com/fwlink/?linkid=2165533).

## <a name="next-steps"></a>Étapes suivantes

Pour découvrir plus d’options concernant les offres SaaS sur la Place de marché commerciale, consultez [API de facturation à la consommation de la Place de marché](../marketplace-metering-service-apis.md).

Examinez et utilisez les [clients pour différents langages et exemples de programmation](https://github.com/microsoft/commercial-marketplace-samples).
