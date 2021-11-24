---
title: Référence - Déclencheur et liaisons Azure Web PubSub pour Azure Functions
description: La référence décrit le déclencheur et les liaisons Azure Web PubSub pour Azure Functions
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 11/08/2021
ms.openlocfilehash: 40904b087d58ee6a07ca7acecdd2b2927348799e
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132493245"
---
#  <a name="azure-web-pubsub-trigger-and-bindings-for-azure-functions"></a>Déclencheur et liaisons Azure Web PubSub pour Azure Functions

Cette référence explique comment gérer les événements Web PubSub dans Azure Functions.

Web PubSub est un service managé Azure qui aide les développeurs à créer facilement des applications web avec des fonctionnalités en temps réel et un modèle publication/abonnement.

| Action | Type |
|---------|---------|
| Exécuter une fonction lorsque les messages proviennent du service | [Liaison de déclencheur](#trigger-binding) |
| Lier la demande à l’objet cible sous le déclencheur HTTP pour la négociation et les demandes en amont | [Liaison d’entrée](#input-binding)
| Appeler le service pour effectuer des actions | [Liaison de sortie](#output-binding) |

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
| Script C#, JavaScript, Python, PowerShell       | [Installer des extensions de manière explicite], [Utiliser des packs d’extension] | Il est recommandé d’utiliser l’[extension Azure Tools] avec Visual Studio Code. |
| Script C# (en ligne uniquement dans le portail Azure)         | Ajout d’une liaison                                   | Pour mettre à jour des extensions de liaison existantes sans avoir à republier votre application de fonction, consultez [Mettre à jour vos extensions]. |

> [!NOTE]
> Installez la bibliothèque de client depuis [NuGet](https://www.nuget.org/) avec le package et la version spécifiés.
> 
> ```bash
> func extensions install --package Microsoft.Azure.WebJobs.Extensions.WebPubSub --version 1.0.0
> ```

[Package NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.WebPubSub
[Utiliser les packs d’extensions]: ../azure-functions/functions-bindings-register.md#extension-bundles
[Installer des extensions de manière explicite]: ../azure-functions/functions-bindings-register.md#explicitly-install-extensions 
[Extension Azure Tools]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack
[Mettre à jour vos extensions]: ../azure-functions/functions-bindings-register.md

## <a name="key-concepts"></a>Concepts clés

![Diagramme montrant le workflow du service Azure Web PubSub fonctionnant avec les applications de fonction.](./media/reference-functions-bindings/functions-workflow.png)

(1)-(2) liaison d’entrée `WebPubSubConnection` avec HttpTrigger pour générer une connexion cliente.

(3)-(4) liaison de déclencheur `WebPubSubTrigger` ou liaison d’entrée `WebPubSubContext` avec HttpTrigger pour gérer la requête de service.

(5)-(6) liaison de sortie `WebPubSub` pour demander au service d’effectuer une opération.

## <a name="trigger-binding"></a>Liaison de déclencheur

Utilisez le déclencheur de fonction pour gérer les requêtes du service Azure Web PubSub. 

`WebPubSubTrigger` est utilisé lorsque vous devez gérer des requêtes côté service. Le modèle de point de terminaison du déclencheur devrait ressembler à ceci et être défini du côté service Web PubSub (portail : paramètres -> gestionnaire d’événements -> modèle d’URL). Dans le modèle de point de terminaison, la partie requête `code=<API_KEY>` est **OBLIGATOIRE** lorsque vous utilisez Azure Function App pour des raisons de [sécurité](../azure-functions/security-concepts.md#system-key). La clé est accessible dans le **portail Azure**. Localisez votre ressource d’application de fonction et accédez à **Fonctions** -> **Clés d’application** -> **Clés système** -> **webpubsub_extension** après avoir déployé l’application de fonction sur Azure. Toutefois, cette clé n’est pas nécessaire lorsque vous utilisez des fonctions locales.

```
<Function_App_Url>/runtime/webhooks/webpubsub?code=<API_KEY>
```

:::image type="content" source="media/quickstart-serverless/func-keys.png" alt-text="Capture d’écran des clés système d’obtention de la clé.":::

### <a name="example"></a>Exemple


# <a name="c"></a>[C#](#tab/csharp)

```cs
[FunctionName("WebPubSubTrigger")]
public static void Run(
    [WebPubSubTrigger("<hub>", "message", EventType.User)]
    UserEventRequest request,
    WebPubSubConnectionContext context,
    string data,
    WebPubSubDataType dataType)
{
    Console.WriteLine($"Request from: {context.userId}");
    Console.WriteLine($"Request message data: {data}");
    Console.WriteLine($"Request message dataType: {dataType}");
}
```

La liaison `WebPubSubTrigger` prend également en charge la valeur renvoyée dans les scénarios de synchronisation, par exemple, l’événement utilisateur et `Connect` système, lorsque le serveur peut vérifier et refuser la requête du client ou envoyer directement les messages à l’appelant. L’événement `Connect` respecte `ConnectEventResponse` et `EventErrorResponse`, l’événement utilisateur respecte `UserEventResponse` et `EventErrorResponse` et les types REST ne correspondant pas au scénario actuel seront ignorés. Et si `EventErrorResponse` est retourné, le service supprimera la connexion client.

```cs
[FunctionName("WebPubSubTriggerReturnValue")]
public static MessageResponse Run(
    [WebPubSubTrigger("<hub>", "message", EventType.User)]
    UserEventRequest request,
    ConnectionContext context,
    string data,
    WebPubSubDataType dataType)
{
    return new UserEventResponse
    {
        Data = BinaryData.FromString("ack"),
        DataType = WebPubSubDataType.Text
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
      "name": "data",
      "hub": "<hub>",
      "eventName": "message",
      "eventType": "user"
    }
  ]
}
```

Définissez la fonction dans `index.js`.

```js
module.exports = function (context, data) {
  console.log('Request from: ', context.bindingData.request.connectionContext.userId);
  console.log('Request message data: ', data);
  console.log('Request message dataType: ', context.bindingData.request.dataType);
}
```

La liaison `WebPubSubTrigger` prend également en charge la valeur renvoyée dans les scénarios de synchronisation, par exemple, l’événement utilisateur et `Connect` système, lorsque le serveur peut vérifier et refuser la requête du client ou envoyer directement le message au client de la requête. En langage JavaScript faiblement typé, il sera désérialisé en ce qui concerne les clés d’objet. Et `EventErrorResponse` aura la priorité la plus élevée par rapport aux objets REST, de sorte que si `code` est retourné, il sera analysé sur `EventErrorResponse` et la connexion client sera abandonnée.

```js
module.exports = async function (context) {
  return { 
    "data": "ack",
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
public static void Run([WebPubSubTrigger("<hub>", "<event-Name>", <WebPubSubEventType>)] 
WebPubSubConnectionContext context, ILogger log)
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
| **eventName** | EventName | Obligatoire : la valeur doit être définie comme l’événement des messages pour que la fonction soit déclenchée. </br> Pour le type d’événement `system`, le nom d’événement doit être dans `connect`, `connected`, `disconnected`. </br> Pour les sous-protocoles définis par l’utilisateur, le nom d’événement est `message`. </br> Pour le sous-protocole `json.webpubsub.azure.v1.` pris en charge par le système, le nom d’événement est défini par l’utilisateur. |
| **connection** | Connexion | Facultatif : Nom d’un regroupement de paramètres d’application ou de paramètres qui spécifie le service Azure Web PubSub en amont. La valeur sera utilisée pour la validation de la signature. La valeur sera automatiquement résolue avec les paramètres d’application « WebPubSubConnectionString » par défaut. La valeur `null` signifie que la validation n’est pas nécessaire et qu’elle réussira toujours. |

### <a name="usages"></a>Utilisations

En C#, `WebPubSubEventRequest` est un paramètre de liaison reconnu par type, les paramètres REST sont liés par le nom du paramètre. Vérifiez le tableau ci-dessous répertoriant les paramètres et types disponibles.

Dans un langage faiblement typé comme JavaScript, `name` dans `function.json` sera utilisé pour lier l’objet déclencheur en fonction de la table de mappage ci-dessous. Et respectera `dataType` dans `function.json` pour convertir le message en conséquence lorsque `name` est défini sur `data` comme l’objet de liaison pour l’entrée de déclenchement. Tous les paramètres peuvent être lus à partir de `context.bindingData.<BindingName>` et seront convertis `JObject`. 

| Nom de la liaison | Type de liaison | Description | Propriétés |
|---------|---------|---------|---------|
|request|`WebPubSubEventRequest`|Décrit la requête en amont|La propriété diffère selon les différents types d’événements, notamment les classes dérivées `ConnectEventRequest`, `ConnectedEventRequest`, `UserEventRequest` et `DisconnectedEventRequest` |
|connectionContext|`WebPubSubConnectionContext`|Informations de requête courantes| EventType, EventName, Hub, ConnectionId, UserId, Headers, Origin, Signature, States |
|data|`BinaryData`,`string`,`Stream`,`byte[]`| Demande les données de message au client dans l’événement `message` utilisateur | -|
|dataType|`WebPubSubDataType`| Type de données du message de requête, prend en charge `binary`, `text`, `json` | -|
|réclamations|`IDictionary<string, string[]>`|Revendications de l’utilisateur dans la requête `connect` système | -|
|query|`IDictionary<string, string[]>`|Requête de l’utilisateur dans la requête `connect` système | -|
|sous-protocoles|`IList<string>`|Sous-protocoles disponibles dans la requête `connect` système | -|
|clientCertificates|`IList<ClientCertificate>`|Une liste d’empreintes de certificat provenant des clients dans la requête `connect` système|-|
|reason|`string`|Motif dans la requête `disconnected` système|-|

### <a name="return-response"></a> Return response

`WebPubSubTrigger` respecte la réponse renvoyée par le client pour les événements synchrones de `connect` et l’événement utilisateur. Seule la réponse correspondante sera renvoyée au service ; sinon, elle sera ignorée. En outre, l’objet renvoyée `WebPubSubTrigger` prend en charge les utilisateurs dans `SetState()` et `ClearStates()` pour gérer les métadonnées de la connexion. L’extension fusionnera les résultats de la valeur renvoyée avec les résultats d’origine de la demande `WebPubSubConnectionContext.States`. La valeur dans la clé existante sera remplacée et celle dans la nouvelle clé sera ajoutée.

| Type de retour | Description | Propriétés |
|---------|---------|---------|
|`ConnectEventResponse`| Réponse pour l’événement `connect` | Groupes, Rôles, UserId, Sous-protocole |
|`UserEventResponse`| Réponse pour l’événement utilisateur | DataType, Data |
|`EventErrorResponse`| Réponse d’erreur pour l’événement de synchronisation | Code, ErrorMessage |
|`*WebPubSubEventResponse`| Type de réponse de base des éléments pris en charge utilisés pour les cas de retour incertains | - |

## <a name="input-binding"></a>Liaison d’entrée

Notre extension fournit deux liaisons d’entrée ciblant différents besoins.

- `WebPubSubConnection`

  Pour qu’un client puisse se connecter au service Azure Web PubSub, il doit connaître l’URL du point de terminaison de service et avoir un jeton d’accès valide. La liaison d’entrée `WebPubSubConnection` génère les informations requises, le client n’a donc pas besoin de gérer cette génération de jeton lui-même. Étant donné que le jeton est limité dans le temps et peut être utilisé pour authentifier un utilisateur spécifique sur une connexion, ne le mettez pas en cache et ne le partagez pas entre plusieurs clients. Un déclencheur HTTP qui utilise cette liaison d’entrée peut être utilisé pour permettre aux clients de récupérer les informations de connexion.

- `WebPubSubContext`

  Lors de l’utilisation de Static Web Apps, `HttpTrigger` est le seul déclencheur pris en charge et, sous le scénario Web PubSub, nous fournissons la liaison d’entrée `WebPubSubContext` qui aide les utilisateurs à désérialiser la requête HTTP en amont du côté service sous les protocoles Web PubSub. Les clients peuvent ainsi obtenir des résultats similaires en comparaison à `WebPubSubTrigger` pour une gestion facile dans les fonctions. Consultez les [exemples](#example---webpubsubcontext) ci-dessous.
  Lorsqu’il est utilisé avec `HttpTrigger`, le client doit configurer l’URL exposée par HttpTrigger dans le gestionnaire d’événements en conséquence.

### <a name="example---webpubsubconnection"></a>Exemple : `WebPubSubConnection`

L’exemple suivant montre une fonction C# qui acquiert des informations de connexion Web PubSub à l’aide de la liaison d’entrée et les renvoie via HTTP. Dans l’exemple ci-dessous, l’élément `UserId` est transmis par la partie requête de la demande du client, comme `?userid={User-A}`.

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

### <a name="example---webpubsubcontext"></a>Exemple : `WebPubSubContext`

L’exemple suivant montre une fonction C# qui acquiert des informations de requête Web PubSub en amont à l’aide de la liaison d’entrée sous le type d’événement `connect` et les renvoie via HTTP.

# <a name="c"></a>[C#](#tab/csharp)

```cs
[FunctionName("WebPubSubContextInputBinding")]
public static object Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")] HttpRequest req,
    [WebPubSubContext] WebPubSubContext wpsContext)
{
    if (wpsContext.IsPreflight || !wpsContext.HasError)
    {
        return wpsReq.Response;
    }
    var request = wpsReq.Request as ConnectEventRequest;
    var response = new ConnectEventResponse
    {
        UserId = wpsContext.Request.ConnectionContext.UserId
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
      "type": "webPubSubContext",
      "name": "wpsContext",
      "direction": "in"
    }
  ]
}
```

Définissez la fonction dans `index.js`.

```js
module.exports = async function (context, req, wpsContext) {
  if (!wpsContext.hasError || wpsContext.isPreflight)
  {
    console.log(`invalid request: ${wpsContext.response.message}.`);
    return wpsReq.response;
  }
  console.log(`user: ${wpsContext.connectionContext.userId} is connecting.`);
  return { body: {"userId": wpsContext.connectionContext.userId} };
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
| **hub** | Hub | Obligatoire : La valeur doit être définie sur le nom du hub Web PubSub pour que la fonction soit déclenchée. Nous pouvons définir la valeur dans l’attribut comme une priorité plus élevée, ou elle peut être définie dans les paramètres de l'application comme une valeur globale. |
| **userId** | UserId | Facultatif : la valeur de la revendication de l’identificateur d’utilisateur à définir dans le jeton de clé d’accès. |
| **connection** | Connexion | Obligatoire : Nom du paramètre d’application contenant la chaîne de connexion du service Web PubSub (« WebPubSubConnectionString » par défaut). |

#### <a name="webpubsubcontext"></a>WebPubSubContext

Le tableau suivant décrit les propriétés de configuration de liaison que vous définissez dans le fichier functions.json et l’attribut `WebPubSubContext`.

| Propriété function.json | Propriété d’attribut | Description |
|---------|---------|---------|
| **type** | n/a | Cette propriété doit être définie sur `webPubSubContext`. |
| **direction** | n/a | Cette propriété doit être définie sur `in`. |
| **name** | n/a | Nom de variable utilisé dans le code de fonction pour la requête Web PubSub d’entrée. |
| **connection** | Connexion | Facultatif : Nom d’un regroupement de paramètres d’application ou de paramètres qui spécifie le service Azure Web PubSub en amont. Cette valeur sera utilisée pour la [protection contre les abus](https://github.com/cloudevents/spec/blob/v1.0.1/http-webhook.md#4-abuse-protection) et la validation de la signature. La valeur sera automatiquement résolue avec « WebPubSubConnectionString » par défaut. La valeur `null` signifie que la validation n’est pas nécessaire et qu’elle réussira toujours. |

### <a name="usage"></a>Utilisation

#### <a name="webpubsubconnection"></a>WebPubSubConnection

# <a name="c"></a>[C#](#tab/csharp)

`WebPubSubConnection` fournit les propriétés ci-dessous.

| Nom de la liaison | Type de liaison | Description |
|---------|---------|---------|
| BaseUri | Uri | URI de connexion du client Web PubSub. |
| Uri | Uri | URI absolu de la connexion Web PubSub, contenant `AccessToken` généré en fonction de la requête. |
| AccessToken | string | `AccessToken` généré en fonction des informations de service et d’UserId de la requête. |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

`WebPubSubConnection` fournit les propriétés ci-dessous.

| Nom de la liaison | Description |
|---------|---------|
| baseUrl | URI de connexion du client Web PubSub. |
| url | URI absolu de la connexion Web PubSub, contenant `AccessToken` généré en fonction de la requête. |
| accessToken | `AccessToken` généré en fonction des informations de service et d’UserId de la requête. |

---

#### <a name="webpubsubcontext"></a>WebPubSubContext

`WebPubSubContext` fournit les propriétés ci-dessous.

| Nom de la liaison | Type de liaison | Description | Propriétés |
|---------|---------|---------|---------|
| request | `WebPubSubEventRequest` | Requête du client, voir le tableau ci-dessous pour plus d’informations. | `WebPubSubConnectionContext` de l’en-tête de demande et les autres propriétés désérialisées du corps de la demande décrivent la requête, par exemple `Reason` pour `DisconnectedEventRequest`. |
| réponse | `HttpResponseMessage` | L’extension crée une réponse essentiellement pour `AbuseProtection` et les cas d’erreur. | - |
| errorMessage | string | Décrit les détails de l’erreur lors du traitement de la requête en amont. | - |
| hasError | bool | Indicateur qui spécifie s’il s’agit d’une requête Web PubSub en amont valide. | - |
| isPreflight | bool | Indicateur qui spécifie s’il s’agit d’une requête préliminaire de `AbuseProtection`. | - |

Pour `WebPubSubEventRequest`, elle est désérialisée en différentes classes qui fournissent différentes informations sur le scénario de requête. Pour `PreflightRequest` ou les cas non valides, l’utilisateur peut vérifier les indicateurs `IsPreflight` et `HasError` pour savoir. Il est conseillé de renvoyer la réponse générée par le système `WebPubSubContext.Response` directement, ou le client peut consigner des erreurs à la demande. Dans différents scénarios, le client peut lire les propriétés de la requête comme indiqué ci-dessous.

| Classe dérivée | Description | Propriétés |
| -- | -- | -- |
| `PreflightRequest` | Utilisée dans `AbuseProtection` lorsque `IsPreflight` est **true** | - |
| `ConnectEventRequest` | Utilisée dans le type d’événement `Connect` système | Revendications, Requête, Sous-protocoles, ClientCertificates |
| `ConnectedEventRequest` | Utilisée dans le type d’événement `Connected` système | - |
| `UserEventRequest` | Utilisée dans le type d’événement utilisateur | Data, DataType |
| `DisconnectedEventRequest` | Utilisée dans le type d’événement `Disconnected` système | Motif |

> [!NOTE]
> Bien que `WebPubSubContext` soit une liaison d’entrée qui fournit une méthode de désérialisation des requêtes similaire à celle de `HttpTrigger` par rapport à `WebPubSubTrigger`, il existe des limites, c’est-à-dire que l’état de connexion après fusion n’est pas pris en charge. La réponse de retour sera toujours respectée par le service, mais les utilisateurs doivent construire la réponse eux-mêmes. Si les utilisateurs ont besoin de définir la réponse à l’événement, vous devriez renvoyer un `HttpResponseMessage` contenant `ConnectEventResponse` ou des messages pour l’événement utilisateur en tant que **corps de la réponse** et placer l’état de connexion avec la clé `ce-connectionstate` dans l’**en-tête de réponse**.

## <a name="output-binding"></a>Liaison de sortie

Utilisez la liaison de sortie Web PubSub pour appeler le service Azure Web PubSub afin d’effectuer une opération. Vous pouvez diffuser un message aux clients suivants :

* Tous les clients connectés
* Clients connectés authentifiés auprès d’un utilisateur spécifique
* Clients connectés joints dans un groupe spécifique
* Une connexion client spécifique

La liaison de sortie vous permet également de gérer les clients et les groupes, ainsi que d’accorder ou de révoquer des autorisations ciblant une connectionId spécifique avec un groupe.

* Ajouter une connexion au groupe
* Ajouter un utilisateur à un groupe
* Supprimer une connexion d’un groupe
* Supprimer un utilisateur d’un groupe
* Supprimer un utilisateur de tous les groupes
* Fermer toutes les connexions clientes
* Fermer une connexion cliente spécifique
* Fermer les connexions d’un groupe
* Accorder l’autorisation d’une connexion
* Révoquer l’autorisation d’une connexion

Pour plus d’informations sur les détails d’installation et de configuration, consultez la vue d’ensemble.

### <a name="example"></a>Exemple

# <a name="c"></a>[C#](#tab/csharp)

```cs
[FunctionName("WebPubSubOutputBinding")]
public static async Task RunAsync(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")] HttpRequest req,
    [WebPubSub(Hub = "<hub>")] IAsyncCollector<WebPubSubAction> actions)
{
    await actions.AddAsync(WebPubSubAction.CreateSendToAllAction("Hello Web PubSub!", WebPubSubDataType.Text));
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
      "name": "actions",
      "hub": "<hub>",
      "direction": "out"
    }
  ]
}
```

Définissez la fonction dans `index.js`.

```js
module.exports = async function (context) {
  context.bindings.actions = {
    "actionName": "sendToAll",
    "data": "hello",
    "dataType": "text"
  };
  context.done();
}
```

---

### <a name="webpubsubaction"></a>WebPubSubAction 

`WebPubSubAction` est le type abstrait de base des liaisons de sortie. Les types dérivés représentent le serveur d’action qui doit être appelé par les services. 

# <a name="c"></a>[C#](#tab/csharp)

En langage C#, nous fournissons quelques méthodes statiques sous `WebPubSubAction` pour vous aider à découvrir les actions disponibles. Par exemple, l’utilisateur peut créer l’action `SendToAllAction` en appelant `WebPubSubAction.CreateSendToAllAction()`.

| Classe dérivée | Propriétés |
| -- | -- |
| `SendToAllAction`|Data, DataType, Excluded |
| `SendToGroupAction`|Group, Data, DataType, Excluded |
| `SendToUserAction`|UserId, Data, DataType |
| `SendToConnectionAction`|ConnectionId, Data, DataType |
| `AddUserToGroupAction`|UserId, Group |
| `RemoveUserFromGroupAction`|UserId, Group |
| `RemoveUserFromAllGroupsAction`|UserId |
| `AddConnectionToGroupAction`|ConnectionId, Group |
| `RemoveConnectionFromGroupAction`|ConnectionId, Group |
| `CloseAllConnectionsAction`|Excluded, Reason |
| `CloseClientConnectionAction`|ConnectionId, Reason |
| `CloseGroupConnectionsAction`|Group, Excluded, Reason |
| `GrantPermissionAction`|ConnectionId, Permission, TargetName |
| `RevokePermissionAction`|ConnectionId, Permission, TargetName |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Dans un langage faiblement typé comme `javascript`, **`actionName`** est le paramètre clé pour résoudre le type ; les actions disponibles sont répertoriées comme ci-dessous.

| ActionName | Propriétés |
| -- | -- |
| `SendToAll`|Data, DataType, Excluded |
| `SendToGroup`|Group, Data, DataType, Excluded |
| `SendToUser`|UserId, Data, DataType |
| `SendToConnection`|ConnectionId, Data, DataType |
| `AddUserToGroup`|UserId, Group |
| `RemoveUserFromGroup`|UserId, Group |
| `RemoveUserFromAllGroups`|UserId |
| `AddConnectionToGroup`|ConnectionId, Group |
| `RemoveConnectionFromGroup`|ConnectionId, Group |
| `CloseAllConnections`|Excluded, Reason |
| `CloseClientConnection`|ConnectionId, Reason |
| `CloseGroupConnections`|Group, Excluded, Reason |
| `GrantPermission`|ConnectionId, Permission, TargetName |
| `RevokePermission`|ConnectionId, Permission, TargetName |

> [!IMPORTANT]
> La propriété des données du message dans les actions liées à l’envoi de messages doit être `string` si le type de données est défini sur `json` ou `text` afin d’éviter toute ambiguïté dans la conversion des données. Utilisez `JSON.stringify()` pour convertir l’objet JSON le cas échéant. Cela s’applique à n’importe quel emplacement à l’aide de la propriété de message, par exemple, `UserEventResponse.Data` fonctionnant avec `WebPubSubTrigger`. 
> 
> Quand le type de données est défini sur `binary`, il est permis d’exploiter la liaison naturellement prise en charge par `dataType` en tant que `binary`, configurée dans le fichier `function.json`. Consultez [Définitions de liaisons et de déclencheurs](/azure/azure-functions/functions-triggers-bindings?tabs=csharp#trigger-and-binding-definitions) pour plus d’informations.

---

### <a name="configuration"></a>Configuration

#### <a name="webpubsub"></a>WebPubSub

Le tableau suivant décrit les propriétés de configuration de liaison que vous définissez dans le fichier function.json et l’attribut `WebPubSub`.

| Propriété function.json | Propriété d’attribut | Description |
|---------|---------|---------|
| **type** | n/a | Il doit être défini sur `webPubSub` |
| **direction** | n/a | Il doit être défini sur `out` |
| **name** | n/a | Nom de variable utilisé dans le code de fonction pour l’objet liaison de sortie. |
| **hub** | Hub | La valeur doit être définie sur le nom du hub Web PubSub pour que la fonction soit déclenchée. Nous pouvons définir la valeur dans l’attribut comme une priorité plus élevée, ou elle peut être définie dans les paramètres de l'application comme une valeur globale. |
| **connection** | Connexion | Nom du paramètre d’application contenant la chaîne de connexion du service Web PubSub (« WebPubSubConnectionString » par défaut). |

## <a name="troubleshooting"></a>Résolution des problèmes

### <a name="setting-up-console-logging"></a>Configuration de la journalisation de la console
Vous pouvez également [activer la journalisation de la console](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/core/Azure.Core/samples/Diagnostics.md#logging) facilement si vous souhaitez approfondir les requêtes que vous formulez sur le service.

[azure_sub]: https://azure.microsoft.com/free/
[samples_ref]: https://github.com/Azure/azure-webpubsub/tree/main/samples/functions

## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [next step](includes/include-next-step.md)]