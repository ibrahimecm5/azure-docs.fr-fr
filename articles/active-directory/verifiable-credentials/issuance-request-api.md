---
title: Spécifier la demande d’émission de l’API REST du service de demande (préversion)
titleSuffix: Azure Active Directory Verifiable Credentials
description: Découvrez comment émettre un justificatif vérifiable que vous avez émis.
documentationCenter: ''
author: barclayn
manager: karenh444
ms.service: active-directory
ms.topic: reference
ms.subservice: verifiable-credentials
ms.date: 10/08/2021
ms.author: barclayn
ms.openlocfilehash: 40515c37de50379f84475ad5474fb54fbbfa5f0d
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131440523"
---
# <a name="request-service-rest-api-issuance-specification-preview"></a>Spécification d’émission de l’API REST du service de demande (préversion)

Les informations d’identification vérifiables Azure Active Directory (Azure AD) incluent l’API REST du service de demande. Cette API vous permet d’émettre et de vérifier des informations d’identification. Cet article spécifie l’API REST du service de demande pour une demande d’émission.

## <a name="http-request"></a>Demande HTTP

La demande d’émission de l’API REST du service de demande prend en charge la méthode HTTP suivante :

| Méthode |Notes  |
|---------|---------|
|POST | Avec la charge utile JSON spécifiée dans cet article. |

La demande d’émission de l’API REST du service de demande nécessite les en-têtes HTTP suivants :

| Méthode |Valeur  |
|---------|---------|
|`Authorization`| Attachez le jeton d’accès comme jeton du porteur à l’en-tête d’autorisation dans une requête HTTP. Par exemple : `Authorization: Bearer <token>`.|
|`Content-Type`| `Application/json`|

Construisez une requête HTTP POST adressée à l’API REST du service de demande. Remplacez `{tenantID}` par votre ID de locataire ou votre nom de locataire.

```http
https://beta.did.msidentity.com/v1.0/{tenantID}/verifiablecredentials/request
```

La requête HTTP suivante illustre une demande adressée à l’API REST du service de demande :

```http
POST https://beta.did.msidentity.com/v1.0/contoso.onmicrosoft.com/verifiablecredentials/request
Content-Type: application/json
Authorization: Bearer  <token>

{
    "includeQRCode": true, 
        "callback": {  
            "url": "https://wwww.contoso.com/vc/callback",  
            "state": "Aaaabbbb11112222", 
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

## <a name="issuance-request-payload"></a>Charge utile de la demande d’émission

La charge utile de la demande d’émission contient des informations sur votre demande d’émission de justificatifs vérifiables. L’exemple suivant montre une demande d’émission effectuée en utilisant un flux de code confidentiel avec des revendications d’utilisateur, telles que le prénom et le nom. Le résultat de cette requête renvoie un code QR avec un lien pour démarrer le processus d’émission.

```json
{
    "includeQRCode": true,
    "callback": {
        "url": "https://www.contoso.com/api/issuer/issuanceCallback",
        "state": "de19cb6b-36c1-45fe-9409-909a51292a9c",
        "headers": {
            "api-key": "OPTIONAL API-KEY for VERIFIER CALLBACK API"
        }
    },
    "authority": "did:ion:EiCLL8lzCqlGLYTGbjwgR6SN6OkIjO6uUKyF5zM7fQZ8Jg:eyJkZWx0YSI6eyJwYXRjaGVzIjpbeyJhY3Rpb24iOiJyZXBsYWNlIiwiZG9jdW1lbnQiOnsicHVibGljS2V5cyI6W3siaWQiOiJzaWdfOTAyZmM2NmUiLCJwdWJsaWNLZXlKd2siOnsiY3J2Ijoic2VjcDI1NmsxIiwia3R5IjoiRUMiLCJ4IjoiTEdUOWk3aFYzN1dUcFhHcUg5c1VDek...",
    "registration": {
        "clientName": "Verifiable Credential Expert Sample"
    },
    "issuance": {
        "type": "VerifiedCredentialExpert",
        "manifest": "https://beta.did.msidentity.com/v1.0/12345678-0000-0000-0000-000000000000/verifiableCredential/contracts/VerifiedCredentialExpert",
        "pin": {
            "value": "3539",
            "length": 4
        },
        "claims": {
            "given_name": "Megan",
            "family_name": "Bowen"
        }
    }
}
```

La charge utile contient les propriétés suivantes :  

|Paramètre |Type  | Description |
|---------|---------|---------|
| `includeQRCode` |  Boolean |   Détermine si un code QR est inclus dans la réponse de cette demande. Présentez le code QR et demandez à l’utilisateur de le scanner. Le scan du code QR lance l’application d’authentification avec cette demande d’émission. Les valeurs possibles sont `true` (par défaut) ou `false`. Lorsque vous définissez la valeur sur `false`, utilisez la propriété `url` de retour pour afficher un lien profond.  |
| `authority` | chaîne|  Identificateur décentralisé de l’émetteur (DID). Pour plus d’informations, consultez [Collecter les informations d’identification et les détails de l’environnement pour configurer votre exemple d’application](verifiable-credentials-configure-issuer.md).|
| `registration` | [RequestRegistration](#requestregistration-type)|  Fournit des informations sur l’émetteur qui peuvent être affichées dans l’application d’authentification. |
| `issuance` | [RequestIssuance](#requestissuance-type)| Fournit des informations sur la demande d’émission.  |
|`callback`|  [Callback](#callback-type)| Permet au développeur d’obtenir de façon asynchrone des informations sur le flux pendant le processus d’émission de justificatifs vérifiables. Par exemple, le développeur peut souhaiter un appel une fois que l’utilisateur a scanné le code QR.|

### <a name="requestregistration-type"></a>Type RequestRegistration

Le type `RequestRegistration` fournit l’inscription des informations pour l’émetteur. Le type `RequestRegistration` contient les propriétés suivantes :

|Propriété |Type |Description |
|---------|---------|---------|
| `clientName` | string|  Nom complet de l’émetteur des justificatifs vérifiables.  |
| `logoUrl` |  string |  facultatif. URL du logo de l’émetteur.  |
| `termsOfServiceUrl` |  string | facultatif. URL des Conditions d’utilisation des justificatifs vérifiables que vous émettez.  |

> [!NOTE]
> À ce stade, les informations `RequestRegistration` ne sont pas présentées lors de l’émission de l’application Microsoft Authenticator. Toutefois, ces informations peuvent être utilisées dans la charge utile.

### <a name="requestissuance-type"></a>Type RequestIssuance

Le type `RequestIssuance` fournit les informations requises pour l’émission de justificatifs vérifiables. Il existe actuellement trois types d’entrée que vous pouvez envoyer dans le type `RequestIssuance`. Les informations d’identification vérifiables d’Azure AD utilisent ces types pour insérer des revendications dans des justificatifs vérifiables, et attestent de ces informations avec le DID de l’émetteur. Les trois types sont les suivants :

- Jeton d’ID
- Informations d’identification vérifiables via une présentation vérifiable.
- Revendications attestées automatiquement

Pour des informations détaillées sur les types d’entrée, consultez [Personnalisation de vos justificatifs vérifiables](credential-design.md). 

Le type `RequestIssuance` contient les propriétés suivantes :

|Propriété |Type |Description |
|---------|---------|---------|
| `type` |  string |  Type de justificatifs vérifiables. Doit correspondre au type défini dans le manifeste des justificatifs vérifiables. Par exemple : `VerifiedCredentialExpert`. Pour plus d’informations, consultez [Créer la carte d’expert en justificatifs vérifiés dans Azure](verifiable-credentials-configure-issuer.md). |
| `manifest` | string| URL du document manifeste des justificatifs vérifiables. Pour plus d’informations, consultez [Collecter les informations d’identification et les détails de l’environnement pour configurer votre exemple d’application](verifiable-credentials-configure-issuer.md).|
| `claims` | string| facultatif. Incluez une collection d’assertions effectuées sur le sujet dans les justificatifs vérifiables. Pour le flux de code confidentiel, il est important de fournir le prénom et le nom de l’utilisateur. Pour plus d’informations, consultez [Noms des justificatifs vérifiables](verifiable-credentials-configure-issuer.md#verifiable-credential-names). |
| `pin` | [PIN](#pin-type)| facultatif. Numéro de code confidentiel pour fournir une sécurité supplémentaire lors de l’émission. Pour le flux de code confidentiel, cette propriété est obligatoire. Vous générez un code confidentiel et le présentez à l’utilisateur dans votre application. L’utilisateur doit fournir le code confidentiel que vous avez généré. |

### <a name="pin-type"></a>Type de code confidentiel

Le `pin` type définit un code confidentiel qui peut être affiché dans le cadre de l’émission. `pin` est facultatif et, s’il est utilisé, doit toujours être envoyé hors bande. Lorsque vous utilisez un code confidentiel de hachage, vous devez définir les propriétés `salt`, `alg` et `iterations`. `pin` contient les propriétés suivantes :

|Propriété |Type |Description |
|---------|---------|---------|
| `value` | string| Contient la valeur de code confidentiel en texte brut. Lors de l’utilisation d’un code confidentiel haché, la propriété value contient le code de hachage salé, codé en base64.|
| `type` | chaîne|  Type du code confidentiel. Valeur possible : `numeric` (par défaut). |
| `length` | entier|  Longueur du code confidentiel. La longueur par défaut est 6, la longueur minimale est 4 et la longueur maximale est 16.|
| `salt` | chaîne|  Sel du code confidentiel haché. Le sel est ajouté au début du calcul du code de hachage. Encodage : UTF-8. |
| `alg` | string|  Algorithme de hachage pour le code confidentiel haché. Algorithme pris en charge : `sha256`. |
| `iterations` | entier| Nombre d’itérations de hachage. Valeur possible : `1`.|

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
    "requestId": :"799f23ea-5241-45af-99ad-cf8e5018814e",  
    "url": "openid://vc?request_uri=https://beta.did.msidentity.com/v1.0/12345678-0000-0000-0000-000000000000/verifiablecredentials/request/178319f7-20be-4945-80fb-7d52d47ae82e",  
    "expiry": 1622227690,  
    "qrCode": "data:image/png;base64,iVBORw0KggoA<SNIP>"  
} 
```

La réponse contient les propriétés suivantes :

|Propriété |Type |Description |
|---------|---------|---------|
| `requestId`| string | ID de corrélation généré automatiquement. Le [rappel](#callback-events) utilisant la même demande, vous pouvez garder une trace de la demande d’émission et de ses rappels. |
| `url`|  string| URL qui lance l’application d’authentification et démarre le processus d’émission. Vous pouvez présenter cette URL à l’utilisateur s’il ne parvient pas à scanner le code QR. |
| `expiry`| entier| Indique quand la réponse expire. |
| `qrCode`| string | Code QR que l’utilisateur peut scanner pour démarrer le processus d’émission. |

Lorsque votre application reçoit la réponse, l’application doit présenter le code QR à l’utilisateur. L’utilisateur scanne le code QR, ce qui a pour effet d’ouvrir l’application d’authentification et de démarrer le processus d’émission.

## <a name="error-response"></a>Réponse d’erreur

Les réponses d’erreur peuvent également être renvoyées afin que l’application puisse les traiter de façon appropriée. Le code JSON suivant montre un message d’erreur non autorisée :


```json
{
    "requestId": "d60b068e7fbd975896e179b99347866a",
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
| `requestId`| string | ID de demande généré automatiquement.|
| `date`| Date| Heure de l’erreur. |
| `error.code` | string| Code d’erreur de retour. |
| `error.message`| string| Message d’erreur. |

## <a name="callback-events"></a>Événements de rappel

Le point de terminaison de rappel est appelé quand un utilisateur scanne le code QR, utilise le lien profond avec l’application d’authentification ou termine le processus d’émission. 

|Propriété |Type |Description |
|---------|---------|---------|
| `requestId`| string | Mappé à la demande d’origine lorsque la charge utile a été publiée sur le service Justificatifs vérifiables.|
| `code` |string |Code renvoyé lorsque la demande a été récupérée par l’application d’authentification. Valeurs possibles : <ul><li>`request_retrieved` : l’utilisateur a scanné le code QR ou sélectionné le lien qui démarre le flux d’émission.</li><li>`issuance_successful` : l’émission des justificatifs vérifiables a réussi.</li><li>`Issuance_error` : une erreur s’est produite pendant l’émission. Pour plus d'informations, consultez la propriété `error`.</li></ul>    |
| `state` |chaîne| Renvoie la valeur d’état que vous avez transmise dans la charge utile d’origine.   |
| `error`| error | Lorsque la valeur de la propriété `code` est `Issuance_error`, cette propriété contient des informations sur l’erreur.| 
| `error.code` | string| Code d’erreur de retour. |
| `error.message`| string| Message d’erreur. |

L’exemple suivant illustre une charge utile de rappel quand l’application d’authentification démarre la demande d’émission :

```json
{  
    "requestId":"aef2133ba45886ce2c38974339ba1057",  
    "code":"request_retrieved",  
    "state":"Wy0ThUz1gSasAjS1"
} 
```

L’exemple suivant illustre une charge utile de rappel une fois que l’utilisateur a terminé le processus d’émission :

```json
{  
    "requestId":"87e1cb24-9096-409f-81cb-9e645f23a4ba",
    "code":"issuance_successful",
    "state":"f3d94e35-ca5f-4b1b-a7d7-a88caa05e322",
} 
```

### <a name="callback-errors"></a>Erreurs de rappel  

Le point de terminaison de rappel peut être appelé avec un message d’erreur. Le tableau suivant répertorie les codes d’erreur :

|Message  |Définition    |
|---------|---------|
| `fetch_contract_error*`| Impossible de récupérer le contrat de justificatifs vérifiables. Cette erreur se produit généralement lorsque l’API ne parvient pas à extraire le manifeste que vous spécifiez dans l’[objet RequestIssuance](#requestissuance-type) de la charge utile de la demande.|
| `issuance_service_error*` | Le service des informations d’identification vérifiables n’est pas en mesure de valider les exigences, ou une erreur s’est produite dans les informations d’identification vérifiables.|
| `unspecified_error`| Cette erreur est rare mais mérite une investigation. |

L’exemple suivant illustre une charge utile de rappel lorsqu’une erreur s’est produite :

```json
{  
    "requestId":"87e1cb24-9096-409f-81cb-9e645f23a4ba",  
    "code": "issuance_error",  
    "state":"f3d94e35-ca5f-4b1b-a7d7-a88caa05e322",  
    "error": { 
      "code":"IssuanceFlowFailed", 
      "message":"issuance_service_error”, 
    } 
} 
``` 

## <a name="next-steps"></a>Étapes suivantes

Découvrez [comment appeler de l’API REST du service de demande](get-started-request-api.md).
