---
title: Guide pratique pour appeler l’API REST du service de demande (préversion)
titleSuffix: Azure Active Directory Verifiable Credentials
description: Découvrez comment émettre et vérifier à l’aide de l’API REST du service de demande
documentationCenter: ''
author: barclayn
manager: karenh444
ms.service: active-directory
ms.topic: how-to
ms.subservice: verifiable-credentials
ms.date: 10/08/2021
ms.author: barclayn
ms.openlocfilehash: 805ba2fc9a24536a940e4baf02e2934d26c10d75
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131474019"
---
# <a name="request-service-rest-api-preview"></a>API REST du service de demande (préversion)

Les informations d’identification vérifiables Azure Active Directory (Azure AD) incluent l’API REST du service de demande. Cette API vous permet d’émettre et de vérifier des informations d’identification. Cet article vous montre comment commencer à utiliser l’API REST du service de demande.

> [!IMPORTANT]
> L’API REST du service de demande est actuellement en préversion. Cette version préliminaire est fournie sans contrat de niveau de service, et vous pouvez rencontrer de temps à temps des changements ainsi qu’une dépréciation de l’API pendant la préversion. La préversion de l’API n’est pas recommandée pour les charges de travail de production. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="api-access-token"></a>Jeton d’accès API

Pour que votre application puisse accéder à l’API REST du service de demande, vous devez inclure un jeton d’accès valide avec les autorisations requises. Les jetons d’accès émis par la plateforme d’identité Microsoft contiennent des informations (étendues) que l’API REST du service de demande utilise pour valider l’appelant. Un jeton d’accès assure que l’appelant dispose des autorisations nécessaires pour effectuer l’opération qu’il demande.

Pour obtenir un jeton d’accès, votre application doit être enregistrée sur la plateforme d’identité Microsoft et être autorisée par un administrateur à accéder à l’API REST du service de demande. Si vous n’avez pas inscrit l’application *verifiable-credentials-app*, consultez [comment inscrire l’application](verifiable-credentials-configure-tenant.md#register-an-application-in-azure-ad) puis [générer un secret d’application](verifiable-credentials-configure-issuer.md#configure-the-verifiable-credentials-app).

### <a name="get-an-access-token"></a>Obtention d’un jeton d’accès

Utilisez le [workflow d’octroi d’informations d’identification du client OAuth 2.0](../../active-directory/develop/v2-oauth2-client-creds-grant-flow.md) pour obtenir le jeton d’accès à l’aide du de la plateforme d’identités Microsoft. Utilisez une bibliothèque approuvée à cet effet. Dans ce tutoriel, nous utilisons la bibliothèque d’authentification Microsoft [MSAL](../../active-directory/develop/msal-overview.md). MSAL simplifie l’ajout de l’authentification et de l’autorisation à une application qui peut appeler une API web sécurisée.

# <a name="http"></a>[HTTP](#tab/http)

```http
Refer to to the Microsoft Authentication Library (MSAL) documentation for more information on how to acquire tokens via HTTP.
```

# <a name="c"></a>[C#](#tab/csharp)

```csharp
// Initialize MSAL library by using the following code
ConfidentialClientApplicationBuilder.Create(AppSettings.ClientId)
    .WithClientSecret(AppSettings.ClientSecret)
    .WithAuthority(new Uri(AppSettings.Authority))
    .Build();

// Acquire an access token
result = await app.AcquireTokenForClient(AppSettings.Scopes)
                .ExecuteAsync();
```

# <a name="nodejs"></a>[Node.JS](#tab/nodejs)

```nodejs
// Initialize MSAL library by using the following code
const msalConfig = {
  auth: {
      clientId: config.azClientId,
      authority: `https://login.microsoftonline.com/${config.azTenantId}`,
      clientSecret: config.azClientSecret,
  },
  system: {
      loggerOptions: {
          loggerCallback(loglevel, message, containsPii) {
              console.log(message);
          },
          piiLoggingEnabled: false,
          logLevel: msal.LogLevel.Verbose,
      }
  }
};
const cca = new msal.ConfidentialClientApplication(msalConfig);
const msalClientCredentialRequest = {
  scopes: ["bbb94529-53a3-4be5-a069-7eaf2712b826/.default"],
  skipCache: false, 
};
module.exports.msalCca = cca;
module.exports.msalClientCredentialRequest = msalClientCredentialRequest;

// Acquire an access token
const result = await mainApp.msalCca.acquireTokenByClientCredential(mainApp.msalClientCredentialRequest);
    if ( result ) {
      accessToken = result.accessToken;
    }
```

---

Dans le code précédent, fournissez les paramètres suivants :

| Paramètre | Condition | Description |
| --- | --- | --- |
| Authority | Obligatoire | Le locataire de l’annuaire sur lequel les plans d’application opèrent. Par exemple : `https://login.microsoftonline.com/{your-tenant}`. (Remplacez `your-tenant` par votre [ID ou nom de locataire](../fundamentals/active-directory-how-to-find-tenant.md).) |
| ID client | Obligatoire | Copiez l’ID d’application affecté à votre application. Ces informations sont disponibles dans le portail Azure où vous avez inscrit votre application. |
| Clé secrète client | Obligatoire | La clé secrète client que vous avez générée pour votre application.|
| Étendues | Obligatoire | Cette propriété doit être définie sur `bbb94529-53a3-4be5-a069-7eaf2712b826/.default`. |

Pour plus d’informations sur la façon d’obtenir un jeton d’accès à l’aide de l’identité d’une application de console, consultez l’un des articles suivants : [C#](../develop/quickstart-v2-netcore-daemon.md), [Python](../develop/quickstart-v2-python-daemon.md), [Node.js](../develop/quickstart-v2-nodejs-console.md) ou [Java](../develop/quickstart-v2-java-daemon.md).

Vous pouvez également [accéder à la requête de jeton avec un certificat](../develop/v2-oauth2-client-creds-grant-flow.md) au lieu d’une clé secrète client.

# <a name="http"></a>[HTTP](#tab/http)

```http
POST /{tenant}/oauth2/v2.0/token HTTP/1.1   //Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=12345678-0000-0000-00000000000000000
&scope=bbb94529-53a3-4be5-a069-7eaf2712b826/.default
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&grant_type=client_credentials
```

# <a name="c"></a>[C#](#tab/csharp)

```csharp
// Initialize MSAL library by using the following code
X509Certificate2 certificate = AppSettings.ReadCertificate(AppSettings.CertificateName);
    app = ConfidentialClientApplicationBuilder.Create(AppSettings.ClientId)
        .WithCertificate(certificate)
        .WithAuthority(new Uri(AppSettings.Authority))
        .Build();

// Acquire an access token
result = await app.AcquireTokenForClient(AppSettings.Scopes)
                .ExecuteAsync();
```

# <a name="nodejs"></a>[Node.JS](#tab/nodejs)

```nodejs
// Initialize MSAL library by using the following code
const msalConfig = {
  auth: {
      clientId: config.azClientId,
      authority: `https://login.microsoftonline.com/${config.azTenantId}`,
      clientCertificate: {
            thumbprint: "CERT_THUMBPRINT", // a 40-digit hexadecimal string
            privateKey: "CERT_PRIVATE_KEY"
        }
  },
  system: {
      loggerOptions: {
          loggerCallback(loglevel, message, containsPii) {
              console.log(message);
          },
          piiLoggingEnabled: false,
          logLevel: msal.LogLevel.Verbose,
      }
  }
};
const cca = new msal.ConfidentialClientApplication(msalConfig);
const msalClientCredentialRequest = {
  scopes: ["bbb94529-53a3-4be5-a069-7eaf2712b826/.default"],
  skipCache: false, 
};
module.exports.msalCca = cca;
module.exports.msalClientCredentialRequest = msalClientCredentialRequest;

// Acquire an access token
const result = await mainApp.msalCca.acquireTokenByClientCredential(mainApp.msalClientCredentialRequest);
    if ( result ) {
      accessToken = result.accessToken;
    }
```

---

## <a name="call-the-api"></a>Appeler l’API

Pour émettre ou vérifier des justificatifs vérifiables, procédez comme suit :

1. Construisez une requête HTTP POST vers l'API REST du service de demande. Remplacez `{tenantID}` par votre ID de locataire ou votre nom de locataire.

    ```http
    POST https://beta.did.msidentity.com/v1.0/{tenantID}/verifiablecredentials/request
    ```

1. Attachez le jeton d’accès comme jeton du porteur à l’en-tête d’autorisation dans une requête HTTP.

    ```http
    Authorization: Bearer <token>
    ```

1. Attribuez à l’en-tête `Content-Type` la valeur `Application/json`.

1. Préparez et attachez la charge utile de demande [Émission](issuance-request-api.md#issuance-request-payload)ou [Présentation](presentation-request-api.md#presentation-request-payload) au corps de la demande.

1. Envoyez la demande à l’API REST du service de demande.

## <a name="issuance-request-example"></a>Exemple de demande d’émission

L’exemple suivant illustre une demande d’émission de justificatif vérifiable. Pour plus d’informations sur la charge utile, consultez [Spécification de l’émission de l’API REST du service de demande](issuance-request-api.md).

```http
POST https://beta.did.msidentity.com/v1.0/contoso.onmicrosoft.com/verifiablecredentials/request
Content-Type: application/json
Authorization: Bearer  <token>

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
        "manifest": "https://beta.did.msidentity.com/v1.0/12345678-0000-0000-0000-000000000000/verifiableCredential/contracts/VerifiedCredentialExpert1",
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

Pour obtenir le code complet, consultez l’un des exemples de code suivants :

- [C#](https://github.com/Azure-Samples/active-directory-verifiable-credentials-dotnet/blob/main/AspNetCoreVerifiableCredentials/IssuerController.cs)
- [Node.JS](https://github.com/Azure-Samples/active-directory-verifiable-credentials-node/blob/main/1-node-api-idtokenhint/issuer.js)

## <a name="presentation-request-example"></a>Exemple de demande de présentation

L’exemple suivant illustre une demande de présentation de justificatif vérifiable. Pour plus d’informations sur la charge utile, consultez [Spécification de la présentation de l’API REST de Request Service](presentation-request-api.md).

```http
POST https://beta.did.msidentity.com/v1.0/contoso.onmicrosoft.com/verifiablecredentials/request
Content-Type: application/json
Authorization: Bearer  <token>

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

Pour obtenir le code complet, consultez l’un des exemples de code suivants :

- [C#](https://github.com/Azure-Samples/active-directory-verifiable-credentials-dotnet/blob/main/1-asp-net-core-api-idtokenhint/VerifierController.cs) 
- [Node.JS](https://github.com/Azure-Samples/active-directory-verifiable-credentials-node/blob/main/1-node-api-idtokenhint/verifier.js)

## <a name="callback-events"></a>Événements de rappel

La charge utile de la demande contient le point de terminaison de rappel d’[émission](issuance-request-api.md#callback-events) et de [présentation](presentation-request-api.md#callback-events). Le point de terminaison fait partie de votre application web et doit être publiquement disponible. Le service de justificatifs vérifiables Microsoft Azure AD appelle votre point de terminaison pour informer votre application de certains événements. Par exemple, lorsqu’un utilisateur scanne le code QR, utilise le lien ciblé avec son application d’authentification, ou termine le processus de présentation.

Le diagramme suivant décrit l’appel que votre application fait à l’API REST du service de demande, et les rappels à votre application.

![Le diagramme décrit l’appel de l’API et les événements de rappel.](media/get-started-request-api/callback-events.png)

Configurez votre point de terminaison pour écouter les requêtes HTTP POST entrantes. L’extrait de code suivant montre comment traiter la demande HTTP de rappel d’émission et mettre à jour l’interface utilisateur en conséquence :

# <a name="http"></a>[HTTP](#tab/http)

Non applicable. Choisissez l’un des autres langages de programmation.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[HttpPost]
public async Task<ActionResult> IssuanceCallback()
{
try
{
    string content = new System.IO.StreamReader(this.Request.Body).ReadToEndAsync().Result;
    _log.LogTrace("callback!: " + content);
    JObject issuanceResponse = JObject.Parse(content);
    
    // More code here
    if (issuanceResponse["code"].ToString() == "request_retrieved")
    {
        var cacheData = new
        {
            status = "request_retrieved",
            message = "QR Code is scanned. Waiting for issuance...",
        };
        _cache.Set(state, JsonConvert.SerializeObject(cacheData));

        // More code here
    }
}
```

Pour obtenir le code complet, consultez le code d’[émission](https://github.com/Azure-Samples/active-directory-verifiable-credentials-dotnet/blob/main/AspNetCoreVerifiableCredentials/IssuerController.cs) et de [présentation](https://github.com/Azure-Samples/active-directory-verifiable-credentials-dotnet/blob/main/AspNetCoreVerifiableCredentials/VerifierController.cs) sur le référentiel GitHub.

# <a name="nodejs"></a>[Node.JS](#tab/nodejs)

```nodejs
mainApp.app.post('/api/issuer/issuance-request-callback', parser, async (req, res) => {
  var body = '';
  req.on('data', function (data) {
    body += data;
  });
  req.on('end', function () {
    requestTrace( req );
    console.log( body );
    var issuanceResponse = JSON.parse(body.toString());
    var message = null;
    
    if ( issuanceResponse.code == "request_retrieved" ) {
      message = "QR Code is scanned. Waiting for issuance to complete...";
    }
    if ( issuanceResponse.code == "issuance_successful" ) {
      message = "Credential successfully issued";
    }
    if ( issuanceResponse.code == "issuance_error" ) {
      message = issuanceResponse.error.message;
    }
    
    // More code here
    res.send()
  });  
  res.send()
})
```

Pour obtenir le code complet, consultez le code d’[émission](https://github.com/Azure-Samples/active-directory-verifiable-credentials-node/blob/main/1-node-api-idtokenhint/issuer.js) et de [présentation](https://github.com/Azure-Samples/active-directory-verifiable-credentials-node/blob/main/1-node-api-idtokenhint/verifier.js) sur le référentiel GitHub.

---

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur ces spécifications :

- [Spécification d’une API d’émission](issuance-request-api.md)
- [Spécification d’une API de présentation](presentation-request-api.md)
