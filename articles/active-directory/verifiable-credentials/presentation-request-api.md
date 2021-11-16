---
title: Spécifier la requête de vérification de l’API REST du service de demande (préversion)
titleSuffix: Azure Active Directory Verifiable Credentials
description: Découvrez comment démarrer une demande de présentation dans les justificatifs vérifiables
documentationCenter: ''
author: barclayn
manager: karenh444
ms.service: active-directory
ms.topic: reference
ms.subservice: verifiable-credentials
ms.date: 10/08/2021
ms.author: barclayn
ms.openlocfilehash: c670f060e7849f844997c0feefd60229b90e5202
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131474000"
---
# <a name="request-service-rest-api-presentation-specification-preview"></a>Spécification de la présentation de l’API REST du service de requête (préversion)

Lesjustificatifs vérifiables Azure Active Directory (Azure AD) incluent l’API REST du service de demande. Cette API vous permet d’émettre et de vérifier des informations d’identification. Cet article spécifie l’API REST du service de demande pour une demande de présentation. La demande de présentation invite l’utilisateur à présenter des justificatifs vérifiables, puis à vérifier les informations d'identification.

## <a name="http-request"></a>Demande HTTP

La demande de présentation de l’API REST du service de demande prend en charge la méthode HTTP suivante :

| Méthode |Notes  |
|---------|---------|
|POST | Avec la charge utile JSON spécifiée dans cet article. |

La demande de présentation de l’API REST du service de demande requiert les en-têtes HTTP suivants :

| Méthode |Valeur  |
|---------|---------|
|`Authorization`| Attachez le jeton d’accès comme jeton du porteur à l’en-tête d’autorisation dans une requête HTTP. Par exemple : `Authorization: Bearer <token>`.|
|`Content-Type`| `Application/json`|

Construisez une requête HTTP POST adressée à l’API REST du service de demande. Remplacez `{tenantID}` par votre ID de locataire ou votre nom de locataire.

```http
https://beta.did.msidentity.com/v1.0/{tenantID}/verifiablecredentials/request
```

La requête HTTP suivante montre une demande de présentation adressée à l’API REST du service de demande :

```http
POST https://beta.did.msidentity.com/v1.0/contoso.onmicrosoft.com/verifiablecredentials/request
Content-Type: application/json
Authorization: Bearer  <token>

{  
    "includeQRCode": true,  
    "callback": {  
    "url": "https://www.contoso.com/api/verifier/presentationCallbac",  
    "state": "11111111-2222-2222-2222-333333333333",  
      "headers": {  
        "api-key": "an-api-key-can-go-here"  
      }  
    },  
    ...
} 
```  

L’autorisation suivante est requise pour appeler l’API REST du service de demande. Pour plus d’informations, consultez [Accorder des autorisations pour obtenir des jetons d’accès](verifiable-credentials-configure-tenant.md#grant-permissions-to-get-access-tokens).

| Type d'autorisation | Autorisation  |
|---------|---------|
| Application | bbb94529-53a3-4be5-a069-7eaf2712b826/.default|

## <a name="presentation-request-payload"></a>Charge utile de la demande de présentation

La charge utile de la demande de présentation contient des informations sur votre demande de présentation de justificatifs vérifiables. L’exemple suivant montre une demande de présentation d’un émetteur spécifique. Le résultat de cette demande retourne un code QR avec un lien pour démarrer le processus de présentation.

```json
{
  "includeQRCode": true,
  "callback": {
    "url": "https://www.contoso.com/api/verifier/presentationCallback",
    "state": "92d076dd-450a-4247-aa5b-d2e75a1a5d58",
    "headers": {
      "api-key": "OPTIONAL API-KEY for VERIFIER CALLBACK API"
    }
  },
  "authority": "did:ion:EiCLL8lzCqlGLYTGbjwgR6SN6OkIjO6uUKyF5zM7fQZ8Jg:eyJkZWx0YSI6eyJwYXRjaGVzIjpbeyJhY3Rpb24iOiJyZXBsYWNlIiwiZG9jdW1lbnQiOnsicHVibGljS2V5cyI6W3siaWQiOiJzaWdfOTAyZmM2NmUiLCJwdWJsaWNLZXlKd2siOnsiY3J2Ijoic2VjcDI1NmsxIiwia3R5IjoiRUMiLCJ4IjoiTEdUOWk3aFYzN1dUcFhHcUg5c1VDekxTVlFWcVl3S2JNY1Fsc0RILUZJUSIsInkiOiJiRWo5MDY...",
  "registration": {
    "clientName": "Veritable Credential Expert Verifier"
  },
  "presentation": {
    "includeReceipt": true,
    "requestedCredentials": [
      {
        "type": "VerifiedCredentialExpert",
        "purpose": "So we can see that you a veritable credentials expert",
        "acceptedIssuers": [
          "did:ion:EiCLL8lzCqlGLYTGbjwgR6SN6OkIjO6uUKyF5zM7fQZ8Jg:eyJkZWx0YSI6eyJwYXRjaGVzIjpbeyJhY3Rpb24iOiJyZXBsYWNlIiwiZG9jdW1lbnQiOnsicHVibGljS2V5cyI6W3siaWQiOiJzaWdfOTAyZmM2NmUiLCJwdWJsaWNLZXlKd2siOnsiY3J2Ijoic2VjcDI1NmsxIiwia3R5IjoiRUMiLCJ4IjoiTEdUOWk3aFYzN1dUcFhHcUg5c1VDekxTVlFWcVl3S2JNY1Fsc0RILUZJUSIsInkiO..."
        ]
      }
    ]
  }
}
```

La charge utile contient les propriétés suivantes.  

|Paramètre |Type  | Description |
|---------|---------|---------|
| `includeQRCode` |  Boolean |   Détermine si un code QR est inclus dans la réponse de cette demande. Présentez le code QR et demandez à l’utilisateur de le scanner. L’analyse du code QR lance l’application d’authentification avec cette demande de présentation. Les valeurs possibles sont `true` (par défaut) ou `false`. Lorsque vous définissez la valeur sur `false`, utilisez la propriété `url` de retour pour afficher un lien profond.  |
| `authority` | chaîne|  L’identificateur décentralisé (DID) de votre locataire Azure AD vérificateur. Pour plus d’informations, consultez [Rassembler les détails du locataire pour configurer votre exemple d’application](verifiable-credentials-configure-verifier.md#gather-tenant-details-to-set-up-your-sample-application).|
| `registration` | [RequestRegistration](#requestregistration-type)|  Fournit des informations sur le vérificateur. |
| `presentation` | [RequestPresentation](#requestpresentation-type)| Fournit des informations sur la demande de présentation de justificatifs vérifiables.  |
|`callback`|  [Callback](#callback-type)| Permet au développeur de mettre à jour l’interface utilisateur pendant le processus de présentation des justificatifs vérifiables. Quand l’utilisateur termine le processus, poursuivez le processus une fois les résultats retournés à l’application.|

### <a name="requestregistration-type"></a>Type RequestRegistration

Le type `RequestRegistration` fournit l’inscription des informations pour l’émetteur. Le type `RequestRegistration` contient les propriétés suivantes :

|Propriété |Type |Description |
|---------|---------|---------|
| `clientName` | string|  Nom complet de l’émetteur des justificatifs vérifiables. Ce nom sera présenté à l’utilisateur dans l’application d’authentification. |

La capture d’écran suivante montre la propriété `clientName` et le nom d’affichage de l’`authority` (le vérificateur) dans la demande de présentation.

![Capture d’écran montrant comment approuver la demande de présentation.](media/presentation-request-api/approve-presentation-request.jpg)

### <a name="requestpresentation-type"></a>Type RequestPresentation

Le type `RequestPresentation` fournit les informations requises pour la présentation de justificatifs vérifiables. `RequestPresentation` contient les propriétés suivantes :

|Propriété |Type |Description |
|---------|---------|---------|
| `includeReceipt` |  Boolean | Détermine si un accusé de réception doit être inclus dans la réponse de cette demande. Les valeurs possibles sont `true` ou `false`(par défaut). L’accusé de réception contient la charge utile d’origine envoyée de l’authentificateur au service des justificatifs vérifiables. L’accusé de réception est utile pour la résolution des problèmes et ne doit pas être défini par défaut. Dans la demande `OpenId Connect SIOP`, l’accusé de réception contient le jeton d’ID de la demande d’origine. |
| `requestedCredentials` | collection| Collection d’objets [RequestCredential](#requestcredential-type).|

### <a name="requestcredential-type"></a>Type RequestCredential

`RequestCredential` fournit des informations sur les informations d’identification demandées que l’utilisateur doit fournir. `RequestCredential` contient les propriétés suivantes :

|Propriété |Type |Description |
|---------|---------|---------|
| `type`| string| Type de justificatifs vérifiables. Le `type` doit correspondre au type défini dans le manifeste des justificatifs vérifiables `issuer` (par exemple, `VerifiedCredentialExpert`). Pour obtenir le manifeste de l’émetteur, consultez [Collecter les justificatifs et les détails de l’environnement pour configurer votre exemple d’application](verifiable-credentials-configure-issuer.md). Copiez l’**URL des informations d’identification du problème**, ouvrez-la dans un navigateur web, puis vérifiez la propriété **ID**. |
| `purpose`| string | Fournissez des informations sur l’objectif de la demande de ces justificatifs vérifiables. |
| `acceptedIssuers`| collection de chaînes | Collection d’identificateurs décentralisés (DID) d’émetteurs qui pourraient émettre le type de justificatifs vérifiables que les sujets peuvent présenter. Pour obtenir votre DID d’émetteur, consultez [Collecter les justificatifs et les détails de l’environnement pour configurer votre exemple d’application](verifiable-credentials-configure-issuer.md), puis copiez la valeur du **DID**. |

### <a name="callback-type"></a>Type de rappel

L’API REST du service de demande génère plusieurs événements pour le point de terminaison de rappel. Ces événements vous permettent de mettre à jour l’interface utilisateur et de poursuivre le processus une fois que les résultats sont renvoyés à l’application. Le type `Callback` contient les propriétés suivantes :

|Propriété |Type |Description |
|---------|---------|---------|
| `url` | string| URI du point de terminaison de rappel de votre application. |
| `state` | string| S’associe à l’état transmis dans la charge utile d’origine. |
| `headers` | string| facultatif. Vous pouvez inclure une collection d’en-têtes HTTP requis par le destinataire du message POST. Les en-têtes doivent inclure uniquement l’`api-key` ou tout en-tête requis pour l’autorisation.|

## <a name="successful-response"></a>Réponse correcte

En cas de réussite, cette méthode renvoie un code de réponse (*HTTP 201 Créé*) et une collection d’objets d’événement dans le corps de la réponse. Le code JSON suivant illustre une réponse réussie :

```json
{  
    "requestId": "e4ef27ca-eb8c-4b63-823b-3b95140eac11",
    "url": "openid://vc/?request_uri=https://beta.did.msidentity.com/v1.0/87654321-0000-0000-0000-000000000000/verifiablecredentials/request/e4ef27ca-eb8c-4b63-823b-3b95140eac11",
    "expiry": 1633017751,
    "qrCode": "data:image/png;base64,iVBORw0KGgoA<SNIP>"
} 
```

La réponse contient les propriétés suivantes :

|Propriété |Type |Description |
|---------|---------|---------|
| `requestId`| chaîne | ID de corrélation généré automatiquement. Le [rappel](#callback-events) utilisant la même demande, vous pouvez garder une trace de la demande de présentation et de ses rappels. |
| `url`|  string| URL qui lance l’application d’authentification et démarre le processus de présentation. Vous pouvez présenter cette URL à l’utilisateur s’il ne parvient pas à scanner le code QR. |
| `expiry`| entier| Indique quand la réponse expire. |
| `qrCode`| string | Code QR que l’utilisateur peut scanner pour démarrer le flux de présentation. |

Lorsque votre application reçoit la réponse, l’application doit présenter le code QR à l’utilisateur. L’utilisateur scanne le code QR, qui ouvre l’application d’authentification et démarre le processus de présentation.

## <a name="error-response"></a>Réponse d’erreur

Les réponses d’erreur peuvent également être renvoyées afin que l’application puisse les traiter de façon appropriée. Le code JSON suivant montre un message d’erreur non autorisée :


```json
{
    "requestId": "fb888ac646c96083de83b099b2678de0",
    "date": "Wed, 29 Sep 2021 21:49:00 GMT",
    "error": {
        "code": "unauthorized",
        "message": "Failed to authenticate the request."
    }
}
```

La réponse contient les propriétés suivantes :

|Propriété |Type |Description |
|---------|---------|---------|
| `requestId`| chaîne | ID de demande généré automatiquement.|
| `date`| Date | Heure de l’erreur. |
| `error.code` | string | Code d’erreur de retour. |
| `error.message`| string | Message d’erreur. |

## <a name="callback-events"></a>Événements de rappel

Le point de terminaison de rappel est appelé quand un utilisateur scanne le code QR, utilise le lien profond avec son application d’authentification ou termine le processus de présentation. 

|Propriété |Type |Description |
|---------|---------|---------|
| `requestId`| chaîne | Mappé à la demande d’origine lorsque la charge utile a été publiée sur le service Justificatifs vérifiables.|
| `code` |string |Code renvoyé lorsque la demande a été récupérée par l’application d’authentification. Valeurs possibles : <ul><li>`request_retrieved` : L’utilisateur a scanné le code QR ou sélectionné le lien qui démarre le processus de présentation.</li><li>`presentation_verified` : La validation des justificatifs vérifiables s’est correctement déroulée.</li></ul>    |
| `state` |chaîne| Renvoie la valeur d’état que vous avez transmise dans la charge utile d’origine.   |
| `subject`|string | DID d’utilisateur des justificatifs vérifiables.|
| `issuers`| tableau |Retourne un tableau de justificatifs vérifiables demandés. Pour chacun des justificatifs vérifiables, elle fournit les éléments suivants : </li><li>Type de justificatifs vérifiables.</li><li>Revendications récupérées.</li><li>Domaine de l’émetteur des justificatifs vérifiables. </li><li>État de validation du domaine de l’émetteur des justificatifs vérifiables. </li></ul> |
| `receipt`| string | facultatif. L’accusé de réception contient la charge utile d’origine envoyée de l’authentificateur aux justificatifs vérifiables.  |

L’exemple suivant illustre une charge utile de rappel lorsque l’application de l’authentificateur démarre la demande de présentation :

```json
{  
    "requestId":"aef2133ba45886ce2c38974339ba1057",  
    "code":"request_retrieved",  
    "state":"Wy0ThUz1gSasAjS1"
} 
```

L’exemple suivant illustre une charge utile de rappel après la réussite de la présentation des justificatifs vérifiables :

```json
{
  "requestId": "87e1cb24-9096-409f-81cb-9e645f23a4ba",
  "code": "presentation_verified",
  "state": "f3d94e35-ca5f-4b1b-a7d7-a88caa05e322",
  "subject": "did:ion:EiAlrenrtD3Lsw0GlbzS1O2YFdy3Xtu8yo35W<SNIP>…",
  "issuers": [
    {
      "type": [
        "VerifiableCredential",
        "VerifiedCredentialExpert"
      ],
      "claims": {
        "firstName": "John",
        "lastName": "Doe"
      },
      "domain": "https://contoso.com/",
      "verified": "DNS",
      "issuer": "did:ion:….."
    }
  ],
  "receipt": {
    "id_token": "eyJraWQiOiJkaWQ6aW<SNIP>"
  }
} 
```

## <a name="next-steps"></a>Étapes suivantes

Découvrez [comment appeler de l’API REST du service de demande](get-started-request-api.md).
