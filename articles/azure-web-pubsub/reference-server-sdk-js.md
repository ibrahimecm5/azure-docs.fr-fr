---
title: Référence – Kit de développement logiciel (SDK) JavaScript pour Azure Web PubSub
description: Cette référence décrit le Kit de développement logiciel (SDK) JavaScript pour le service Azure Web PubSub.
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 11/11/2021
ms.openlocfilehash: 899f9845721412ceef9171c334383e596fd798e6
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132493052"
---
# <a name="javascript-sdk-for-azure-web-pubsub"></a>Kit de développement logiciel (SDK) JavaScript pour Azure Web PubSub

Le [service Azure Web PubSub](https://aka.ms/awps/doc) est un service managé Azure qui aide les développeurs à créer facilement des applications web avec des fonctionnalités en temps réel et un modèle publication/abonnement. Tout scénario qui nécessite une messagerie de type publication-abonnement en temps réel entre un serveur et des clients ou entre des clients peut avoir recours au service Azure Web PubSub. Les fonctionnalités en temps réel traditionnelles qui demandent souvent d’interroger un serveur ou d’envoyer des requêtes HTTP peuvent aussi utiliser le service Azure Web PubSub.

Il existe deux bibliothèques proposées pour JavaScript : la bibliothèque de client de service et l’intergiciel Express. Les sections suivantes contiennent des informations supplémentaires sur ces bibliothèques.

- [Bibliothèque cliente de services](#service-client-library)
- [Intergiciel Express](#express)

<a name="service-client-library"></a>

## <a name="azure-web-pubsub-service-client-library-for-javascript"></a>Bibliothèque cliente du service Azure Web PubSub pour JavaScript

Vous pouvez utiliser cette bibliothèque dans vos applications côté serveur pour gérer les connexions client WebSocket, comme indiqué dans le diagramme ci-dessous :

![Le diagramme de dépassement montre le dépassement de l’utilisation de la bibliothèque de client du service.](media/sdk-reference/service-client-overflow.png)

- Envoyer des messages aux hubs et aux groupes.
- Envoyer des messages à des utilisateurs particuliers et à des connexions.
- Organiser les utilisateurs et les connexions en groupes.
- Fermer les connexions
- Accorder, révoquer et vérifier des autorisations pour une connexion existante

Les détails des termes utilisés ici sont décrits dans la section [Concepts clés](#key-concepts).

[Code source](https://github.com/Azure/azure-sdk-for-js/blob/main/sdk/web-pubsub/web-pubsub) |
[Package (NPM)](https://www.npmjs.com/package/@azure/web-pubsub) |
[Documentation de référence API](https://aka.ms/awps/sdk/js) |
[Documentation de produits](https://aka.ms/awps/doc) |
[Exemples][samples_ref]

### <a name="getting-started"></a>Prise en main

#### <a name="currently-supported-environments"></a>Environnements actuellement pris en charge

- [Versions LTS de Node.js](https://nodejs.org/about/releases/)

#### <a name="prerequisites"></a>Prérequis

- Un [abonnement Azure][azure_sub].
- Une instance de service Azure Web PubSub existante.

#### <a name="1-install-the-azureweb-pubsub-package"></a>1. Installez le package `@azure/web-pubsub`

```bash
npm install @azure/web-pubsub
```

#### <a name="2-create-and-authenticate-a-webpubsubserviceclient"></a>2. Créer et authentifier un client WebPubSubService

```js
const { WebPubSubServiceClient } = require("@azure/web-pubsub");

const serviceClient = new WebPubSubServiceClient("<ConnectionString>", "<hubName>");
```

Vous pouvez également authentifier le `WebPubSubServiceClient` qui utilise un point de terminaison et un `AzureKeyCredential` :

```js
const { WebPubSubServiceClient, AzureKeyCredential } = require("@azure/web-pubsub");

const key = new AzureKeyCredential("<Key>");
const serviceClient = new WebPubSubServiceClient("<Endpoint>", key, "<hubName>");
```

ou authentifier le `WebPubSubServiceClient` à l’aide de [Azure Active Directory][aad_doc]

1. Installez la dépendance `@azure/identity`.

```batch
npm install @azure/identity
```

2. Mettez à jour le code source pour qu’il utilise `DefaultAzureCredential` :

```js
const { WebPubSubServiceClient, AzureKeyCredential } = require("@azure/web-pubsub");

const key = new DefaultAzureCredential();
const serviceClient = new WebPubSubServiceClient("<Endpoint>", key, "<hubName>");
```

### <a name="key-concepts"></a>Concepts clés

#### <a name="connection"></a>Connexion

Une connexion, également appelée client ou connexion client, représente une connexion WebSocket individuelle connectée au service Web PubSub. Une fois la connexion réussie, un ID de connexion unique est affecté à cette connexion par le service Web PubSub.

#### <a name="hub"></a>Hub

Un hub est un concept logique pour un ensemble de connexions client. En règle générale, vous utilisez un hub dans un seul but, par exemple un hub de conversations ou un hub de notifications. Lorsqu’une connexion client est créée, elle se connecte à un hub et, pendant toute sa durée de vie, elle appartient à ce hub. Différentes applications peuvent partager un service Azure Web PubSub en utilisant différents noms de hubs.

#### <a name="group"></a>Groupe

Un groupe est un sous-ensemble de connexions au hub. Vous pouvez ajouter une connexion cliente à un groupe, ou la supprimer du groupe, quand vous le souhaitez. Par exemple, quand un client rejoint une salle de conversation ou quand il la quitte, cette salle de conversation peut être considérée comme un groupe. Un client peut rejoindre plusieurs groupes, et un groupe peut contenir plusieurs clients.

#### <a name="user"></a>Utilisateur

Les connexions à Web PubSub ne peuvent appartenir qu’à un seul utilisateur. Un utilisateur peut avoir plusieurs connexions. C’est le cas, par exemple, quand un seul utilisateur est connecté sur plusieurs appareils ou plusieurs onglets de navigateur.

#### <a name="message"></a>Message

Quand le client est connecté, il peut envoyer des messages à l’application en amont ou recevoir des messages de l’application en amont par le biais de la connexion WebSocket.

### <a name="examples"></a>Exemples

#### <a name="get-the-access-token-for-a-client-to-start-the-websocket-connection"></a>Obtenir le jeton d’accès pour qu’un client démarre la connexion WebSocket

```js
const { WebPubSubServiceClient } = require("@azure/web-pubsub");

const serviceClient = new WebPubSubServiceClient("<ConnectionString>", "<hubName>");

// Get the access token for the WebSocket client connection to use
let token = await serviceClient.getClientAccessToken();

// Or get the access token and assign the client a userId
token = await serviceClient.getClientAccessToken({ userId: "user1" });

// return the token to the WebSocket client
```

#### <a name="broadcast-messages-to-all-connections-in-a-hub"></a>Diffuser des messages à toutes les connexions dans le hub

```js
const { WebPubSubServiceClient } = require("@azure/web-pubsub");

const serviceClient = new WebPubSubServiceClient("<ConnectionString>", "<hubName>");

// Send a JSON message
await serviceClient.sendToAll({ message: "Hello world!" });

// Send a plain text message
await serviceClient.sendToAll("Hi there!", { contentType: "text/plain" });

// Send a binary message
const payload = new Uint8Array(10);
await serviceClient.sendToAll(payload.buffer);
```

#### <a name="send-messages-to-all-connections-in-a-group"></a>Envoyer des messages à toutes les connexions d’un groupe

```js
const { WebPubSubServiceClient } = require("@azure/web-pubsub");

const serviceClient = new WebPubSubServiceClient("<ConnectionString>", "<hubName>");

const groupClient = serviceClient.group("<groupName>");

// Add user to the group
await groupClient.addUser("user1");

// Send a JSON message
await groupClient.sendToAll({ message: "Hello world!" });

// Send a plain text message
await groupClient.sendToAll("Hi there!", { contentType: "text/plain" });

// Send a binary message
const payload = new Uint8Array(10);
await groupClient.sendToAll(payload.buffer);
```

#### <a name="send-messages-to-all-connections-for-a-user"></a>Envoyer des messages à toutes les connexions d’un utilisateur

```js
const { WebPubSubServiceClient } = require("@azure/web-pubsub");

const serviceClient = new WebPubSubServiceClient("<ConnectionString>", "<hubName>");

// Send a JSON message
await serviceClient.sendToUser("user1", { message: "Hello world!" });

// Send a plain text message
await serviceClient.sendToUser("user1", "Hi there!", { contentType: "text/plain" });

// Send a binary message
const payload = new Uint8Array(10);
await serviceClient.sendToUser("user1", payload.buffer);
```

#### <a name="check-if-the-group-has-any-connection"></a>Vérifier si le groupe a une connexion

```js
const { WebPubSubServiceClient } = require("@azure/web-pubsub");
const WebSocket = require("ws");

const serviceClient = new WebPubSubServiceClient("<ConnectionString>", "<hubName>");

const groupClient = serviceClient.group("<groupName>");

// close all the connections in the group
await groupClient.closeAllConnections({ reason: "<closeReason>" });

// check if the group has any connections
const hasConnections = await serviceClient.groupExists("<groupName>");
```

#### <a name="access-the-raw-http-response-for-an-operation"></a>Accéder à la réponse HTTP brute pour une opération

```js
const { WebPubSubServiceClient } = require("@azure/web-pubsub");

function onResponse(rawResponse: FullOperationResponse): void {
  console.log(rawResponse);
}
const serviceClient = new WebPubSubServiceClient("<ConnectionString>", "<hubName>");
await serviceClient.sendToAll({ message: "Hello world!" }, { onResponse });
```

### <a name="troubleshooting"></a>Dépannage

#### <a name="enable-logs"></a>Activer les journaux d’activité

Vous pouvez définir la variable d’environnement suivante pour afficher les journaux de débogage quand vous utilisez cette bibliothèque.

- Obtention des journaux de débogage à partir de la bibliothèque cliente Signalr

```bash
export AZURE_LOG_LEVEL=verbose
```

Pour obtenir des instructions plus détaillées sur l’activation des journaux, consultez les [@azure/loggerdocuments sur le package ](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/core/logger).

#### <a name="live-trace"></a>Trace dynamique

Utilisez **Live Trace** du portail de service Web PubSub pour afficher le trafic.

<a name="express"></a>

## <a name="azure-web-pubsub-cloudevents-handlers-for-express"></a>Gestionnaires Azure Web Pubsub CloudEvents pour Express

Lorsqu’une connexion WebSocket se connecte, le service Web PubSub transforme le cycle de vie de la connexion et les messages en [événements au format CloudEvents](concept-service-internals.md#workflow). Cette bibliothèque fournit un intergiciel (middleware) express pour gérer les événements représentant le cycle de vie et les messages de la connexion WebSocket, comme indiqué dans le diagramme ci-dessous :

![Le diagramme de dépassement montre le dépassement de capacité de l’intergiciel (middleware) du gestionnaire d’événements.](media/sdk-reference/event-handler-middleware.png)

Les détails des termes utilisés ici sont décrits dans la section [Concepts clés](#key-concepts).

[Code source](https://github.com/Azure/azure-sdk-for-js/blob/main/sdk/web-pubsub/web-pubsub-express) |
[Package (NPM)](https://www.npmjs.com/package/@azure/web-pubsub-express) |
[Documentation de référence API](https://aka.ms/awps/sdk/js) |
[Documentation de produits](https://aka.ms/awps/doc) |
[Exemples][samples_ref]

### <a name="getting-started"></a>Prise en main

#### <a name="currently-supported-environments"></a>Environnements actuellement pris en charge

- [Versions LTS de Node.js](https://nodejs.org/about/releases/)
- [Node.js](https://expressjs.com/) version 4.x.x ou ultérieure

#### <a name="prerequisites"></a>Prérequis

- Un [abonnement Azure][azure_sub].
- Un point de terminaison Azure Web PubSub existant.

#### <a name="1-install-the-azureweb-pubsub-express-package"></a>1. Installez le package `@azure/web-pubsub-express`

```bash
npm install @azure/web-pubsub-express
```

#### <a name="2-create-a-webpubsubeventhandler"></a>2. Créez un `WebPubSubEventHandler`

```js
const express = require("express");

const { WebPubSubEventHandler } = require("@azure/web-pubsub-express");
const handler = new WebPubSubEventHandler("chat");

const app = express();

app.use(handler.getMiddleware());

app.listen(3000, () =>
  console.log(`Azure WebPubSub Upstream ready at http://localhost:3000${handler.path}`)
);
```

### <a name="key-concepts"></a>Concepts clés

#### <a name="connection"></a>Connexion

Une connexion, également appelée client ou connexion client, représente une connexion WebSocket individuelle connectée au service Web PubSub. Une fois la connexion réussie, un ID de connexion unique est affecté à cette connexion par le service Web PubSub.

#### <a name="hub"></a>Hub

Un hub est un concept logique pour un ensemble de connexions client. En règle générale, vous utilisez un hub dans un seul but, par exemple un hub de conversations ou un hub de notifications. Lorsqu’une connexion client est créée, elle se connecte à un hub et, pendant toute sa durée de vie, elle appartient à ce hub. Différentes applications peuvent partager un service Azure Web PubSub en utilisant différents noms de hubs.

#### <a name="group"></a>Groupe

Un groupe est un sous-ensemble de connexions au hub. Vous pouvez ajouter une connexion cliente à un groupe, ou la supprimer du groupe, quand vous le souhaitez. Par exemple, quand un client rejoint une salle de conversation ou quand il la quitte, cette salle de conversation peut être considérée comme un groupe. Un client peut rejoindre plusieurs groupes, et un groupe peut contenir plusieurs clients.

#### <a name="user"></a>Utilisateur

Les connexions à Web PubSub ne peuvent appartenir qu’à un seul utilisateur. Un utilisateur peut avoir plusieurs connexions. C’est le cas, par exemple, quand un seul utilisateur est connecté sur plusieurs appareils ou plusieurs onglets de navigateur.

#### <a name="client-events"></a>Événements du client

Les événements sont créés pendant le cycle de vie d’une connexion cliente. Par exemple, une connexion cliente WebSocket simple crée un événement `connect` lorsqu’il tente de se connecter au service, un événement `connected` quand il s’est correctement connecté au service, un événement `message` lorsqu’il envoie des messages au service et à un événement `disconnected` lorsqu’il se déconnecte du service.

#### <a name="event-handler"></a>Gestionnaire d'événements

Le gestionnaire d’événements contient la logique permettant de gérer les événements du client. Le gestionnaire d’événements doit être inscrit et configuré dans le service via le portail ou Azure CLI au préalable. L’emplacement d’hébergement de la logique du gestionnaire d’événements est généralement considéré comme côté serveur.

### <a name="examples"></a>Exemples

#### <a name="handle-the-connect-request-and-assign-userid"></a>Gérer la demande `connect` et assigner `<userId>`

```js
const express = require("express");

const { WebPubSubEventHandler } = require("@azure/web-pubsub-express");
const handler = new WebPubSubEventHandler("chat", {
  handleConnect: (req, res) => {
    // auth the connection and set the userId of the connection
    res.success({
      userId: "<userId>"
    });
  },
  allowedEndpoints: ["https://<yourAllowedService>.webpubsub.azure.com"]
});

const app = express();

app.use(handler.getMiddleware());

app.listen(3000, () =>
  console.log(`Azure WebPubSub Upstream ready at http://localhost:3000${handler.path}`)
);
```

#### <a name="only-allow-specified-endpoints"></a>Autoriser uniquement les points de terminaison spécifiés

```js
const express = require("express");

const { WebPubSubEventHandler } = require("@azure/web-pubsub-express");
const handler = new WebPubSubEventHandler("chat", {
  allowedEndpoints: [
    "https://<yourAllowedService1>.webpubsub.azure.com",
    "https://<yourAllowedService2>.webpubsub.azure.com"
  ]
});

const app = express();

app.use(handler.getMiddleware());

app.listen(3000, () =>
  console.log(`Azure WebPubSub Upstream ready at http://localhost:3000${handler.path}`)
);
```

#### <a name="set-custom-event-handler-path"></a>Définir le chemin du gestionnaire d’événements personnalisé

```js
const express = require("express");

const { WebPubSubEventHandler } = require("@azure/web-pubsub-express");
const handler = new WebPubSubEventHandler("chat", {
  path: "customPath1"
});

const app = express();

app.use(handler.getMiddleware());

app.listen(3000, () =>
  // Azure WebPubSub Upstream ready at http://localhost:3000/customPath1
  console.log(`Azure WebPubSub Upstream ready at http://localhost:3000${handler.path}`)
);
```

#### <a name="set-and-read-connection-state"></a>Définir et lire l’état de la connexion

```js
const express = require("express");

const { WebPubSubEventHandler } = require("@azure/web-pubsub-express");

const handler = new WebPubSubEventHandler("chat", {
  handleConnect(req, res) {
    // You can set the state for the connection, it lasts throughout the lifetime of the connection
    res.setState("calledTime", 1);
    res.success();
  },
  handleUserEvent(req, res) {
    var calledTime = req.context.states.calledTime++;
    console.log(calledTime);
    // You can also set the state here
    res.setState("calledTime", calledTime);
    res.success();
  }
});

const app = express();

app.use(handler.getMiddleware());

app.listen(3000, () =>
  console.log(`Azure WebPubSub Upstream ready at http://localhost:3000${handler.path}`)
);
```

### <a name="troubleshooting"></a>Dépannage

#### <a name="enable-logs"></a>Activer les journaux d’activité

Vous pouvez définir la variable d’environnement suivante pour afficher les journaux de débogage quand vous utilisez cette bibliothèque.

- Obtention des journaux de débogage à partir de la bibliothèque cliente Signalr

```bash
export AZURE_LOG_LEVEL=verbose
```

Pour obtenir des instructions plus détaillées sur l’activation des journaux, consultez les [@azure/loggerdocuments sur le package ](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/core/logger).

#### <a name="live-trace"></a>Trace dynamique

Utilisez **Live Trace** du portail de service Web PubSub pour afficher le trafic.

[azure_sub]: https://azure.microsoft.com/free/
[samples_ref]: https://github.com/Azure/azure-webpubsub/tree/main/samples/javascript/

## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [next step](includes/include-next-step.md)]