---
title: API Opérations de traitement SaaS v2 dans la place de marché commerciale Microsoft
description: Découvrez comment utiliser les API Opérations, qui font partie des API Traitement SaaS version 2, pour gérer une offre SaaS sur Microsoft AppSource, la Place de marché Azure et le Portail Azure.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 11/10/2021
author: saasguide
ms.author: souchak
ms.openlocfilehash: ef0e735206df6556bd7a7a7b2cf584bc79566a0f
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132064005"
---
# <a name="saas-fulfillment-operations-apis-v2-in-the-microsoft-commercial-marketplace"></a>API Opérations de traitement SaaS v2 dans la place de marché commerciale Microsoft

Cet article décrit la version 2 des API d’opérations de traitement SaaS.

## <a name="list-outstanding-operations"></a>Liste des opérations en attente

Obtenir la liste des opérations en attente pour l’abonnement SaaS spécifié. L’éditeur doit accuser réception des opérations retournées en appelant l’[API Patch d’opérations](#update-the-status-of-an-operation).

### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsapi-versionapiversion"></a>Get `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations?api-version=<ApiVersion>`

*Paramètres de requête :*

|  Paramètre         | Valeur             |
|  ---------------   |  ---------------  |
|    `ApiVersion`    |  Utilisez 2018-08-31.         |
|    `subscriptionId` | Identificateur unique de l’abonnement SaaS acheté.  Cet ID est obtenu après résolution du jeton d’autorisation de la Place de marché à l’aide de l’API Résolution.  |

*En-têtes de requête :*
 
|  Paramètre         | Valeur             |
|  ---------------   |  ---------------  |
|  `content-type`     |  `application/json` |
|  `x-ms-requestid`    |  Valeur de chaîne unique pour le suivi de la requête du client, de préférence un GUID.  Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse.  |
|  `x-ms-correlationid` |  Valeur de chaîne unique pour l’opération sur le client.  Ce paramètre sert à corréler tous les événements de l’opération client avec les événements côté serveur.  Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse.  |
|  `authorization`     |  Le format est `"Bearer <access_token>"` lorsque la valeur de jeton est récupérée par le serveur de publication, comme expliqué dans [Obtenir un jeton basé sur l’application Azure AD](./pc-saas-registration.md#get-the-token-with-an-http-post).  |

*Codes de réponse :*

Code : 200 Renvoie les opérations en attente sur l’abonnement SaaS spécifié.

*Exemple de charge utile de réponse :*

```json
{
  "operations": [
    {
      "id": "<guid>", //Operation ID, should be provided in the operations patch API call
      "activityId": "<guid>", //not relevant
      "subscriptionId": "<guid>", // subscriptionId of the SaaS subscription that is being reinstated
      "offerId": "offer1", // purchased offer ID
      "publisherId": "contoso",
      "planId": "silver", // purchased plan ID
      "quantity": "20", // purchased amount of seats, will be empty is not relevant
      "action": "Reinstate",
      "timeStamp": "2018-12-01T00:00:00", // UTC
      "status": "InProgress" // the only status that can be returned in this case
    }
  ]
}
```

Renvoie un objet json vide si aucune opération n’est en attente.

Code : 400 Demande incorrecte : échecs de validation.

Code : 403 Interdit. Le jeton d’autorisation n’est pas valide, a expiré ou n’a pas été fourni. La demande tente d’accéder à un abonnement SaaS pour une offre publiée avec un ID d’application Azure AD différent de celui utilisé pour créer le jeton d’autorisation.

Cette erreur est souvent le symptôme de l’absence d’une inscription [SaaS](pc-saas-registration.md) correcte.

Code : 404 Introuvable.  L’abonnement SaaS avec `subscriptionId` est introuvable.

Code : 500 Erreur interne du serveur. Renouvelez l’appel d’API.  Si l’erreur persiste, contactez le [support technique Microsoft](https://go.microsoft.com/fwlink/?linkid=2165533).

## <a name="get-operation-status"></a>Obtenir l’état d’une opération

Cette API permet à l’éditeur de suivre l’état de l’opération asynchrone spécifiée :  **Unsubscribe**, **ChangePlan** ou **ChangeQuantity**.

La valeur `operationId` pour cet appel d’API peut être récupérée à partir de la valeur retournée par **Operation-Location**, l’appel d’API pour obtenir les opérations en attente, ou la valeur de paramètre `<id>` reçue dans un appel webhook.

### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>Get `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*Paramètres de requête :*

|  Paramètre         | Valeur             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  Utilisez 2018-08-31.  |
|  `subscriptionId`    |  Identificateur unique de l’abonnement SaaS acheté.  Cet ID est obtenu après résolution du jeton d’autorisation de la Place de marché à l’aide de l’API Résolution. |
|  `operationId`       |  Identificateur unique de l'opération en cours de récupération. |

*En-têtes de requête :*

|  Paramètre         | Valeur             |
|  ---------------   |  ---------------  |
|  `content-type`      |  `application/json`   |
|  `x-ms-requestid`    |  Valeur de chaîne unique pour le suivi de la requête du client, de préférence un GUID.  Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse. |
|  `x-ms-correlationid` |  Valeur de chaîne unique pour l’opération sur le client.  Ce paramètre sert à corréler tous les événements de l’opération client avec les événements côté serveur.  Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse.  |
|  `authorization`     |  Jeton d’accès unique qui identifie le serveur de publication qui effectue cet appel d’API.  Le format est `"Bearer <access_token>"` lorsque la valeur de jeton est récupérée par le serveur de publication, comme expliqué dans [Obtenir un jeton basé sur l’application Azure AD](./pc-saas-registration.md#get-the-token-with-an-http-post).  |

*Codes de réponse :*

Code : 200 Obtient les détails de l’opération SaaS spécifiée. 

*Exemple de charge utile de réponse :*

```json
Response body:
{
  "id  ": "<guid>", //Operation ID, should be provided in the patch operation API call
  "activityId": "<guid>", //not relevant
  "subscriptionId": "<guid>", // subscriptionId of the SaaS subscription for which this operation is relevant
  "offerId": "offer1", // purchased offer ID
  "publisherId": "contoso",
  "planId": "silver", // purchased plan ID
  "quantity": "20", // purchased amount of seats
  "action": "ChangePlan", // Can be ChangePlan, ChangeQuantity or Reinstate
  "timeStamp": "2018-12-01T00:00:00", // UTC
  "status": "InProgress", // Possible values: NotStarted, InProgress, Failed, Succeeded, Conflict (new quantity / plan is the same as existing)
  "errorStatusCode": "",
  "errorMessage": ""
}
```

Code : 403 Interdit. Le jeton d’autorisation n’est pas valide, a expiré ou n’a pas été fourni. La demande tente d’accéder à un abonnement SaaS pour une offre publiée avec un ID d’application Azure AD différent de celui utilisé pour créer le jeton d’autorisation.

Cette erreur est souvent le symptôme de l’absence d’une inscription [SaaS](pc-saas-registration.md) correcte.

Code : 404 Introuvable.  

* L’abonnement avec `subscriptionId` est introuvable.
* L’opération avec `operationId` est introuvable.

Code : 500 Erreur interne du serveur.  Renouvelez l’appel d’API.  Si l’erreur persiste, contactez le [support technique Microsoft](https://go.microsoft.com/fwlink/?linkid=2165533).

## <a name="update-the-status-of-an-operation"></a>Mettre à jour l’état d’une opération

Utilisez cette API afin de mettre à jour l’état d’une opération en attente pour indiquer la réussite ou l’échec de l’opération côté éditeur.

La valeur `operationId` pour cet appel d’API peut être récupérée à partir de la valeur retournée par **Operation-Location**, l’appel d’API pour obtenir les opérations en attente, ou la valeur de paramètre `<id>` reçue dans un appel webhook.

### <a name="patch-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>Patch `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*Paramètres de requête :*

|  Paramètre         | Valeur             |
|  ---------------   |  ---------------  |
|   `ApiVersion`       |  Utilisez 2018-08-31.  |
|   `subscriptionId`   |  Identificateur unique de l’abonnement SaaS acheté.  Cet ID est obtenu après résolution du jeton d’autorisation de la Place de marché à l’aide de l’API Résolution.  |
|   `operationId`      |  Identificateur unique de l’opération terminée. |

*En-têtes de requête :*

|  Paramètre         | Valeur             |
|  ---------------   |  ---------------  |
|   `content-type`   | `application/json`   |
|   `x-ms-requestid`   |  Valeur de chaîne unique pour le suivi de la requête du client, de préférence un GUID.  Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse. |
|   `x-ms-correlationid` |  Valeur de chaîne unique pour l’opération sur le client.  Ce paramètre sert à corréler tous les événements de l’opération client avec les événements côté serveur.  Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse. |
|  `authorization`     |  Jeton d’accès unique qui identifie l’éditeur effectuant cet appel d’API.  Le format est `"Bearer <access_token>"` lorsque la valeur de jeton est récupérée par le serveur de publication, comme expliqué dans [Obtenir un jeton basé sur l’application Azure AD](./pc-saas-registration.md#get-the-token-with-an-http-post). |

*Exemple de demande de charge utile :*

```json
{
  "status": "Success" // Allowed Values: Success/Failure. Indicates the status of the operation on ISV side.
}
```

*Codes de réponse :*

Code : 200 Appel pour informer de la fin d’une opération côté partenaire.  Par exemple, cette réponse peut signaler la fin de la modification de postes ou de plans côté éditeur.

Code : 403
- Interdit.  Le jeton d’autorisation n’est pas disponible, n’est pas valide ou a expiré. La demande tente peut-être d’accéder à un abonnement qui n’appartient pas à l’éditeur actuel.
- Interdit.  Le jeton d’autorisation n’est pas valide, a expiré ou n’a pas été fourni. La demande tente d’accéder à un abonnement SaaS pour une offre publiée avec un ID d’application Azure AD différent de celui utilisé pour créer le jeton d’autorisation.

Cette erreur est souvent le symptôme de l’absence d’une inscription [SaaS](pc-saas-registration.md) correcte.

Code : 404 Introuvable.

* L’abonnement avec `subscriptionId` est introuvable.
* L’opération avec `operationId` est introuvable.

Code : 409 Conflit.  Par exemple, une mise à jour plus récente est déjà traitée.

Code : 500 Erreur interne du serveur.  Renouvelez l’appel d’API.  Si l’erreur persiste, contactez le [support technique Microsoft](https://go.microsoft.com/fwlink/?linkid=2165533).

## <a name="next-steps"></a>Étapes suivantes

Pour découvrir plus d’options concernant les offres SaaS sur la Place de marché commerciale, consultez [API de facturation à la consommation de la Place de marché](../marketplace-metering-service-apis.md).

Examinez et utilisez les [clients pour différents langages et exemples de programmation](https://github.com/microsoft/commercial-marketplace-samples).
