---
title: Référence - Déclencheur et liaisons Azure Web PubSub pour Azure Functions
description: La référence décrit le déclencheur et les liaisons Azure Web PubSub pour Azure Functions
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 11/08/2021
ms.openlocfilehash: 981160fe8d0778d122dd7dbd92c0c27c468ff34a
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2021
ms.locfileid: "131997891"
---
#  <a name="azure-web-pubsub-trigger-and-bindings-for-azure-functions"></a>Déclencheur et liaisons Azure Web PubSub pour Azure Functions

Cette référence explique comment gérer les événements Web PubSub dans Azure Functions.

Web PubSub est un service managé Azure qui aide les développeurs à créer facilement des applications web avec des fonctionnalités en temps réel et un modèle publication/abonnement.

| Action | Type |
|---------|---------|
| Exécuter une fonction lorsque les messages proviennent du service | [Liaison de déclencheur](#trigger-binding) |
| Retourner l’URL du point de terminaison de service et le jeton d’accès | [Liaison d’entrée](#input-binding)
| Envoyer des messages Web PubSub |[Liaison de sortie](#output-binding) |

[Code source](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/webpubsub/) |
[Package](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.WebPubSub) |
[Documentation de référence de l’API](https://github.com/Azure/azure-sdk-for-net/blob/main/sdk/webpubsub/Microsoft.Azure.WebJobs.Extensions.WebPubSub/api/Microsoft.Azure.WebJobs.Extensions.WebPubSub.netstandard2.0.cs) |
[Documentation du produit](./index.yml) |
[Exemples][samples_ref]

## <a name="add-to-your-functions-app"></a>Ajouter à votre application Functions

Pour utiliser le déclencheur et les liaisons, vous devez référencer le package approprié. Le package NuGet est utilisé pour les bibliothèques de classes .NET, tandis que le bundle d’extensions est utilisé pour tous les autres types d’applications.

| Langage                                        | Ajouter via...                                   | Notes 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Installation du [package NuGet], préversion | |
| Script C#, JavaScript, Python, PowerShell       | [Installer des extensions de manière explicite]                    | Il est recommandé d’utiliser l’[extension Azure Tools] avec Visual Studio Code. |
| Script C# (en ligne uniquement dans le portail Azure)         | Ajout d’une liaison                                   | Pour mettre à jour des extensions de liaison existantes sans avoir à republier votre application de fonction, consultez [Mettre à jour vos extensions]. |

Installez la bibliothèque de client depuis [NuGet](https://www.nuget.org/) avec le package et la version spécifiés.

```bash
func extensions install --package Microsoft.Azure.WebJobs.Extensions.WebPubSub --version 1.0.0-beta.3
```

[Package NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.WebPubSub
[Installer des extensions de manière explicite]: ../azure-functions/functions-bindings-register.md#explicitly-install-extensions 
[Extension Azure Tools]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack
[Mettre à jour vos extensions]: ../azure-functions/functions-bindings-register.md

## <a name="key-concepts"></a>Concepts clés

![Diagramme montrant le workflow du service Azure Web PubSub fonctionnant avec les applications de fonction.](./media/reference-functions-bindings/functions-workflow.png)

(1)-(2) liaison d’entrée `WebPubSubConnection` avec HttpTrigger pour générer une connexion cliente.

(3)-(4) liaison de déclencheur `WebPubSubTrigger` ou liaison d’entrée `WebPubSubRequest` avec HttpTrigger pour gérer la requête de service.

(5)-(6) liaison de sortie `WebPubSub` pour demander au service d’effectuer une opération.

## <a name="trigger-binding"></a>Liaison de déclencheur

Utilisez le déclencheur de fonction pour gérer les requêtes du service Azure Web PubSub. 

`WebPubSubTrigger` est utilisé lorsque vous devez gérer des requêtes côté service. Le modèle de point de terminaison du déclencheur devrait ressembler à ceci et être défini du côté service Web PubSub (portail : paramètres -> gestionnaire d’événements -> modèle d’URL). Dans le modèle de point de terminaison, la partie requête `code=<API_KEY>` est **OBLIGATOIRE** lorsque vous utilisez Azure Function App pour des raisons de [sécurité](../azure-functions/security-concepts.md#system-key). La clé est disponible dans le **Portail Azure**. Localisez votre ressource d’application de fonction et accédez à **Fonctions** -> **Clés d’application** -> **Clés système** -> **webpubsub_extension** après avoir déployé l’application de fonction sur Azure. Toutefois, cette clé n’est pas nécessaire lorsque vous utilisez des fonctions locales.

```
<Function_App_Url>/runtime/webhooks/webpubsub?code=<API_KEY>
```

### <a name="example"></a>Exemple


# <a name="c"></a>[C#](#tab/csharp)

```cs
[FunctionName("WebPubSubTrigger")]
public static void Run(
    [WebPubSubTrigger("<hub>", "message", EventType.User)] 
    ConnectionContext context,
    string message,
    MessageDataType dataType)
{
    Console.WriteLine($"Request from: {context.userId}");
    Console.WriteLine($"Request message: {message}");
    Console.WriteLine($"Request message DataType: {dataType}");
}
```

La liaison `WebPubSubTrigger` prend également en charge la valeur de retour dans certains scénarios, par exemple, les événements `Connect`, `Message`, lorsque le serveur peut vérifier et refuser la requête du client, ou envoyer directement le message au client de la requête. L’événement `Connect` respecte `ConnectResponse` et `ErrorResponse`, et l’événement `Message` respecte `MessageResponse` et `ErrorResponse`, les types REST ne correspondant pas au scénario actuel seront ignorés. Et si `ErrorResponse` est retourné, le service supprimera la connexion client.

```cs
[FunctionName("WebPubSubTriggerReturnValue")]
public static MessageResponse Run(
    [WebPubSubTrigger("<hub>", "message", EventType.User)] 
    ConnectionContext context,
    string message,
    MessageDataType dataType)
{
    return new MessageResponse
    {
        Message = BinaryData.FromString("ack"),
        DataType = MessageDataType.Text
    };
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Définissez la liaison de déclencheur dans `function.json`.

```json
{
  "disabled": false,
  "bindings": [
    {
      "type": "webPubSubTrigger",
      "direction": "in",
      "name": "message",
      "hub": "<hub>",
      "eventName": "message",
      "eventType": "user"
    }
  ]
}
```

Définissez la fonction dans `index.js`.

```js
module.exports = function (context, message) {
  console.log('Request from: ', context.userId);
  console.log('Request message: ', message);
  console.log('Request message dataType: ', context.bindingData.dataType);
}
```

La liaison `WebPubSubTrigger` prend également en charge la valeur de retour dans certains scénarios, par exemple, les événements `Connect`, `Message`. Lorsque le serveur peut vérifier et refuser la requête du client, ou envoyer directement le message au client de la requête. En langage sans type JavaScript, il sera désérialisé en ce qui concerne les clés d’objet. Et `ErrorResponse` aura la priorité la plus élevée par rapport aux objets REST, de sorte que si `code` est retourné, il sera analysé sur `ErrorResponse` et la connexion client sera abandonnée.

```js
module.exports = async function (context) {
  return { 
    "message": "ack",
    "dataType" : "text"
  };
}
```

---


### <a name="attributes-and-annotations"></a>Attributs et annotations

Dans les [bibliothèques de classes C#](../azure-functions/functions-dotnet-class-library.md), utilisez l’attribut `WebPubSubTrigger`.

Voici un attribut `WebPubSubTrigger` dans une signature de méthode :

```csharp
[FunctionName("WebPubSubTrigger")]
public static void Run([WebPubSubTrigger("<hub>", "<eventName>", <eventType>)] 
ConnectionContext context, ILogger log)
{
    ...
}
```

Vous trouverez un exemple complet sur la page Exemple C#.

### <a name="configuration"></a>Configuration

Le tableau suivant décrit les propriétés de configuration de liaison que vous définissez dans le fichier *function.json*.

| Propriété function.json | Propriété d’attribut | Description |
|---------|---------|---------|
| **type** | n/a |Obligatoire : doit être défini sur `webPubSubTrigger`. |
| **direction** | n/a | Obligatoire : doit être défini sur `in`. |
| **name** | n/a | Obligatoire : nom de variable utilisé dans le code de fonction pour le paramètre qui reçoit les données de l’événement. |
| **hub** | Hub | Obligatoire : la valeur doit être définie sur le nom du hub Web PubSub pour que la fonction soit déclenchée. Nous pouvons définir la valeur dans l’attribut comme une priorité plus élevée, ou elle peut être définie dans les paramètres de l'application comme une valeur globale. |
| **eventType** | Type d’événement | Obligatoire : la valeur doit être définie comme le type d'événement des messages pour que la fonction soit déclenchée. La valeur doit être `user` ou `system`. |
| **eventName** | EventName | Obligatoire : la valeur doit être définie comme l’événement des messages pour que la fonction soit déclenchée. </br> Pour le type d’événement `system`, le nom d’événement doit être dans `connect`, `connected`, `disconnect`. </br> Pour le sous-protocole `json.webpubsub.azure.v1.` pris en charge par le système, le nom d’événement est défini par l’utilisateur. </br> Pour les sous-protocoles définis par l’utilisateur, le nom d’événement est `message`. |

### <a name="usages"></a>Utilisations

En C#, `ConnectionContext` est un paramètre de liaison reconnu par type, les paramètres REST sont liés par le nom du paramètre. Vérifiez le tableau ci-dessous répertoriant les paramètres et types disponibles.

Dans un langage sans type comme JavaScript, `name` dans `function.json`sera utilisé pour lier l’objet déclencheur en fonction de la table de mappage ci-dessous. Et respectera `dataType` dans `function.json` pour convertir le message en conséquence lorsque `name` est défini sur `message` comme l’objet de liaison pour l’entrée de déclenchement. Tous les paramètres peuvent être lus à partir de `context.bindingData.<BindingName>` et seront convertis `JObject`. 

| Nom de la liaison | Type de liaison | Description | Propriétés |
|---------|---------|---------|---------|
|connectionContext|`ConnectionContext`|Informations de requête courantes| EventType, EventName, Hub, ConnectionId, UserId, Headers, Signature |
|message|`BinaryData`,`string`,`Stream`,`byte[]`| Message de requête du client | -|
|dataType|`MessageDataType`| Type de données du message de requête, prend en charge `binary`, `text`, `json` | -|
|réclamations|`IDictionary<string, string[]>`|Revendications de l’utilisateur dans la requête `connect` | -|
|query|`IDictionary<string, string[]>`|Requête de l’utilisateur dans la requête `connect` | -|
|sous-protocoles|`string[]`|Sous-protocoles disponibles dans la requête `connect` | -|
|clientCertificates|`ClientCertificate[]`|Une liste d’empreintes de certificat provenant des clients dans la requête `connect`|-|
|reason|`string`|Raison de la requête de déconnexion|-|

### <a name="return-response"></a> Return response

`WebPubSubTrigger` respecte la réponse renvoyée par le client pour les événements synchrones de `connect` et l’événement utilisateur `message`. Seule la réponse correspondante sera renvoyée au service ; sinon, elle sera ignorée. 

| Type de retour | Description | Propriétés |
|---------|---------|---------|
|`ConnectResponse`| Réponse pour l’événement `connect` | Groupes, Rôles, UserId, Sous-protocole |
|`MessageResponse`| Réponse pour l’événement utilisateur | DataType, Message |
|`ErrorResponse`| Réponse d’erreur pour l’événement de synchronisation | Code, ErrorMessage |
|`ServiceResponse`| Type de réponse de base des éléments pris en charge utilisés pour les cas de retour incertains | - |

## <a name="input-binding"></a>Liaison d’entrée

Notre extension fournit deux liaisons d’entrée ciblant différents besoins.

- `WebPubSubConnection`

  Pour qu’un client puisse se connecter au service Azure Web PubSub, il doit connaître l’URL du point de terminaison de service et avoir un jeton d’accès valide. La liaison d’entrée `WebPubSubConnection` génère les informations requises, le client n’a donc pas besoin de gérer cette génération de jeton lui-même. Étant donné que le jeton est limité dans le temps et peut être utilisé pour authentifier un utilisateur spécifique sur une connexion, ne le mettez pas en cache et ne le partagez pas entre plusieurs clients. Un déclencheur HTTP qui utilise cette liaison d’entrée peut être utilisé pour permettre aux clients de récupérer les informations de connexion.

- `WebPubSubRequest`

  Lors de l’utilisation de Static Web Apps, `HttpTrigger` est le seul déclencheur pris en charge et, sous le scénario Web PubSub, nous fournissons la liaison d’entrée `WebPubSubRequest` qui aide les utilisateurs à désérialiser la requête HTTP en amont du côté service sous les protocoles Web PubSub. Les clients peuvent ainsi obtenir des résultats similaires en comparaison à `WebPubSubTrigger` pour une gestion facile dans les fonctions. Consultez les [exemples](#example---webpubsubrequest) ci-dessous.
  Lorsqu’il est utilisé avec `HttpTrigger`, le client doit configurer l’URL exposée par HttpTrigger en amont en conséquence.

### <a name="example---webpubsubconnection"></a>Exemple : `WebPubSubConnection`

L’exemple suivant montre une fonction C# qui acquiert des informations de connexion Web PubSub à l’aide de la liaison d’entrée et les renvoie via HTTP.

# <a name="c"></a>[C#](#tab/csharp)

```cs
[FunctionName("WebPubSubConnectionInputBinding")]
public static WebPubSubConnection Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")] HttpRequest req,
    [WebPubSubConnection(Hub = "<hub>", UserId = "{query.userid}")] WebPubSubConnection connection)
{
    Console.WriteLine("login");
    return connection;
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Définissez des liaisons d’entrée dans `function.json`.

```json
{
  "disabled": false,
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    },
    {
      "type": "webPubSubConnection",
      "name": "connection",
      "userId": "{query.userid}",
      "hub": "<hub>",
      "direction": "in"
    }
  ]
}
```

Définissez la fonction dans `index.js`.

```js
module.exports = function (context, req, connection) {
  context.res = { body: connection };
  context.done();
};
```

---

### <a name="authenticated-tokens"></a>**Jetons** authentifiés

Si la fonction est déclenchée par un client authentifié, vous pouvez ajouter une revendication d’ID d’utilisateur au jeton généré. Vous pouvez facilement ajouter l’authentification à une application de fonction à l’aide de Authentification App Service.

L’authentification App Service définit les en-têtes HTTP nommés `x-ms-client-principal-id` et `x-ms-client-principal-name` qui contiennent l’identité principale du client et le nom de l’utilisateur authentifié, respectivement.

Vous pouvez définir la propriété UserId de la liaison sur la valeur de l’un des en-têtes à l’aide d’une expression de liaison : `{headers.x-ms-client-principal-id}` ou `{headers.x-ms-client-principal-name}`.

```cs
[FunctionName("WebPubSubConnectionInputBinding")]
public static WebPubSubConnection Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")] HttpRequest req,
    [WebPubSubConnection(Hub = "<hub>", UserId = "{headers.x-ms-client-principal-name}")] WebPubSubConnection connection)
{
    Console.WriteLine("login");
    return connection;
}
```

### <a name="example---webpubsubrequest"></a>Exemple : `WebPubSubRequest`

L’exemple suivant montre une fonction C# qui acquiert des informations de requête Web PubSub à l’aide de la liaison d’entrée sous le type d’événement de connexion et les renvoie via HTTP.

# <a name="c"></a>[C#](#tab/csharp)

```cs
[FunctionName("WebPubSubRequestInputBinding")]
public static object Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")] HttpRequest req,
    [WebPubSubRequest] WebPubSubRequest wpsReq)
{
    if (wpsReq.Request.IsValidationRequest || !wpsReq.Request.Valid)
    {
        return wpsReq.Response;
    }
    var request = wpsReq.Request as ConnectEventRequest;
    var response = new ConnectResponse
    {
        UserId = wpsReq.ConnectionContext.UserId
    };
    return response;
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Définissez des liaisons d’entrée dans `function.json`.

```json
{
  "disabled": false,
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": ["get", "post"]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "webPubSubRequest",
      "name": "wpsReq",
      "direction": "in"
    }
  ]
}
```

Définissez la fonction dans `index.js`.

```js
module.exports = async function (context, req, wpsReq) {
  if (!wpsReq.request.valid || wpsReq.request.isValidationRequest)
  {
    console.log(`invalid request: ${wpsReq.response.message}.`);
    return wpsReq.response;
  }
  console.log(`user: ${context.bindings.wpsReq.connectionContext.userId} is connecting.`);
  return { body: {"userId": context.bindings.wpsReq.connectionContext.userId} };
};
```

---

### <a name="configuration"></a>Configuration

#### <a name="webpubsubconnection"></a>WebPubSubConnection

Le tableau suivant décrit les propriétés de configuration de liaison que vous définissez dans le fichier function.json et l’attribut `WebPubSubConnection`.

| Propriété function.json | Propriété d’attribut | Description |
|---------|---------|---------|
| **type** | n/a | Il doit être défini sur `webPubSubConnection` |
| **direction** | n/a | Il doit être défini sur `in` |
| **name** | n/a | Nom de variable utilisé dans le code de fonction pour l’objet de liaison de connexion d’entrée. |
| **hub** | Hub | La valeur doit être définie sur le nom du hub Web PubSub pour que la fonction soit déclenchée. Nous pouvons définir la valeur dans l’attribut comme une priorité plus élevée, ou elle peut être définie dans les paramètres de l'application comme une valeur globale. |
| **userId** | UserId | Facultatif : la valeur de la revendication de l’identificateur d’utilisateur à définir dans le jeton de clé d’accès. |
| **connectionStringSetting** | ConnectionStringSetting | Nom du paramètre d’application contenant la chaîne de connexion du service Web PubSub (« WebPubSubConnectionString » par défaut) |

#### <a name="webpubsubrequest"></a>WebPubSubRequest

Le tableau suivant décrit les propriétés de configuration de liaison que vous définissez dans le fichier functions.json et l’attribut `WebPubSubRequest`.

| Propriété function.json | Propriété d’attribut | Description |
|---------|---------|---------|
| **type** | n/a | Il doit être défini sur `webPubSubRequest` |
| **direction** | n/a | Il doit être défini sur `in` |
| **name** | n/a | Nom de variable utilisé dans le code de fonction pour la requête Web PubSub d’entrée. |

### <a name="usage"></a>Utilisation

#### <a name="webpubsubconnection"></a>WebPubSubConnection

`WebPubSubConnection` fournit les propriétés ci-dessous.

Nom de la liaison | Type de liaison | Description
---------|---------|---------
BaseUrl | string | URL de connexion du client Web PubSub
Url | string | URI absolu de la connexion Web PubSub, contenant `AccessToken` généré sur base de la requête
AccessToken | string | `AccessToken` généré sur base des informations de service et d’UserId de la requête

#### <a name="webpubsubrequest"></a>WebPubSubRequest

`WebPubSubRequest` fournit les propriétés ci-dessous.

Nom de la liaison | Type de liaison | Description | Propriétés
---------|---------|---------|---------
connectionContext | `ConnectionContext` | Informations de requête courantes| EventType, EventName, Hub, ConnectionId, UserId, Headers, Signature
request | `ServiceRequest` | Requête du client, voir le tableau ci-dessous pour plus d’informations | IsValidationRequest, Valid, Unauthorized, BadRequest, ErrorMessage, Name, etc.
réponse | `HttpResponseMessage` | L’extension crée une réponse essentiellement pour `AbuseProtection` et les cas d’erreur | -

Pour `ServiceRequest`, elle est désérialisée en différentes classes qui fournissent différentes informations sur le scénario de requête. Pour `ValidationRequest` ou `InvalidRequest`, il est conseillé de renvoyer la réponse générée par le système `WebPubSubRequest.Response` directement, ou le client peut consigner des erreurs au besoin. Dans différents scénarios, le client peut lire les propriétés de la requête comme indiqué ci-dessous.

Classe dérivée | Description | Propriétés
--|--|--
`ValidationRequest` | Utilisé dans `AbuseProtection` lorsque `IsValidationRequest` est **true** | -
`ConnectEventRequest` | Utilisé dans le type d’événement `Connect` | Revendications, Requête, Sous-protocoles, ClientCertificates
`ConnectedEventRequest` | Utilisé dans le type d’événement `Connected` | -
`MessageEventRequest` | Utilisé dans le type d’événement utilisateur | Message, DataType
`DisconnectedEventRequest` | Utilisé dans le type d’événement `Disconnected` | Motif
`InvalidRequest` | Utilisé lorsque la requête n’est pas valide | -

## <a name="output-binding"></a>Liaison de sortie

Utilisez la liaison de sortie Web PubSub pour envoyer un ou plusieurs messages à l’aide du service Azure Web PubSub. Vous pouvez diffuser un message aux clients suivants :

* Tous les clients connectés
* Clients connectés authentifiés auprès d’un utilisateur spécifique
* Clients connectés joints dans un groupe spécifique

La liaison de sortie vous permet également de gérer des groupes et d’octroyer/révoquer des autorisations ciblant un connectionId spécifique avec un groupe.

Pour plus d’informations sur les détails d’installation et de configuration, consultez la vue d’ensemble.

### <a name="example"></a>Exemple

# <a name="c"></a>[C#](#tab/csharp)

```cs
[FunctionName("WebPubSubOutputBinding")]
public static async Task RunAsync(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")] HttpRequest req,
    [WebPubSub(Hub = "<hub>")] IAsyncCollector<WebPubSubOperation> operations)
{
    await operations.AddAsync(new SendToAll
    {
        Message = BinaryData.FromString("Hello Web PubSub"),
        DataType = MessageDataType.Text
    });
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Définissez des liaisons dans `functions.json`.

```json
{
  "disabled": false,
  "bindings": [
    {
      "type": "webPubSub",
      "name": "webPubSubOperation",
      "hub": "<hub>",
      "direction": "out"
    }
  ]
}
```

Définissez la fonction dans `index.js`.

```js
module.exports = async function (context) {
  context.bindings.webPubSubOperation = {
    "operationKind": "sendToAll",
    "message": "hello",
    "dataType": "text"
  };
  context.done();
}
```

---

### <a name="webpubsuboperation"></a>WebPubSubOperation 

`WebPubSubOperation` est le type abstrait de base des liaisons de sortie. Les types dérivés représentent le serveur d’opération qui doit être invoqué par les services. Dans un langage sans type comme `javascript`, `OperationKind` est le paramètre clé pour résoudre le type. Et dans un langage de type fort comme `csharp`, l’utilisateur pourrait créer directement le type d’opération cible et la valeur `OperationKind` attribuée par le client serait ignorée.

Classe dérivée|Propriétés
--|--
`SendToAll`|Message, DataType, Excluded
`SendToGroup`|Group, Message, DataType, Excluded
`SendToUser`|UserId, Message, DataType
`SendToConnection`|ConnectionId, Message, DataType
`AddUserToGroup`|UserId, Group
`RemoveUserFromGroup`|UserId, Group
`RemoveUserFromAllGroups`|UserId
`AddConnectionToGroup`|ConnectionId, Group
`RemoveConnectionFromGroup`|ConnectionId, Group
`CloseClientConnection`|ConnectionId, Reason
`GrantGroupPermission`|ConnectionId, Group, Permission, TargetName
`RevokeGroupPermission`|ConnectionId, Group, Permission, TargetName

### <a name="configuration"></a>Configuration

#### <a name="webpubsub"></a>WebPubSub

Le tableau suivant décrit les propriétés de configuration de liaison que vous définissez dans le fichier function.json et l’attribut `WebPubSub`.

| Propriété function.json | Propriété d’attribut | Description |
|---------|---------|---------|
| **type** | n/a | Il doit être défini sur `webPubSub` |
| **direction** | n/a | Il doit être défini sur `out` |
| **name** | n/a | Nom de variable utilisé dans le code de fonction pour l’objet liaison de sortie. |
| **hub** | Hub | La valeur doit être définie sur le nom du hub Web PubSub pour que la fonction soit déclenchée. Nous pouvons définir la valeur dans l’attribut comme une priorité plus élevée, ou elle peut être définie dans les paramètres de l'application comme une valeur globale. |
| **connectionStringSetting** | ConnectionStringSetting | Nom du paramètre d’application contenant la chaîne de connexion du service Web PubSub (« WebPubSubConnectionString » par défaut) |

## <a name="troubleshooting"></a>Résolution des problèmes

### <a name="setting-up-console-logging"></a>Configuration de la journalisation de la console
Vous pouvez également [activer la journalisation de la console](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/core/Azure.Core/samples/Diagnostics.md#logging) facilement si vous souhaitez approfondir les requêtes que vous formulez sur le service.

[azure_sub]: https://azure.microsoft.com/free/
[samples_ref]: https://github.com/Azure/azure-webpubsub/tree/main/samples/functions

## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [next step](includes/include-next-step.md)]