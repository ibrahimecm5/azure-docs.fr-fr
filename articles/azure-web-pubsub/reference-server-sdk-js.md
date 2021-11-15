---
title: Référence – Kit de développement logiciel (SDK) JavaScript pour Azure Web PubSub
description: Cette référence décrit le Kit de développement logiciel (SDK) JavaScript pour le service Azure Web PubSub.
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 11/01/2021
ms.openlocfilehash: 4571bb8581892525785c3f08e0c627bf20f511aa
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131578341"
---
# <a name="javascript-sdk-for-azure-web-pubsub"></a>Kit de développement logiciel (SDK) JavaScript pour Azure Web PubSub

Il existe deux bibliothèques proposées pour JavaScript : la bibliothèque de client de service et l’intergiciel Express. Les sections suivantes contiennent des informations supplémentaires sur ces bibliothèques.

<a name="service-client-library"></a>

## <a name="azure-web-pubsub-service-client-library-for-javascript"></a>Bibliothèque cliente du service Azure Web PubSub pour JavaScript

Vous pouvez utiliser cette bibliothèque pour effectuer les opérations suivantes :
- Envoyer des messages aux hubs et aux groupes. 
- Envoyer des messages à des utilisateurs particuliers et à des connexions.
- Organiser les utilisateurs et les connexions en groupes.
- Fermer les connexions.
- Accorder, révoquer et vérifier des autorisations pour une connexion existante.

[Code source](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/web-pubsub/web-pubsub) |
[Package (NPM)](https://www.npmjs.com/package/@azure/web-pubsub) |
[Documentation de référence API](/javascript/api/@azure/web-pubsub/) |
[Documentation de produits](./index.yml) |
[Exemples][samples_ref]

### <a name="get-started"></a>Bien démarrer

Utilisez [Node.js](https://nodejs.org/) version 8.x.x ou ultérieure. En outre, assurez-vous de disposer des prérequis suivants :

- Un [abonnement Azure][azure_sub].
- Une instance de service Azure Web PubSub existante.

#### <a name="install-the-azureweb-pubsub-package"></a>Installez le package `@azure/web-pubsub`

```bash
npm install @azure/web-pubsub
```

#### <a name="create-and-authenticate-webpubsubserviceclient"></a>Créez et authentifiez `WebPubSubServiceClient`

```js
const { WebPubSubServiceClient } = require("@azure/web-pubsub");

const serviceClient = new WebPubSubServiceClient("<ConnectionString>", "<hubName>");
```

Vous pouvez également authentifier `WebPubSubServiceClient` en utilisant un point de terminaison et `AzureKeyCredential` :

```js
const { WebPubSubServiceClient, AzureKeyCredential } = require("@azure/web-pubsub");

const key = new AzureKeyCredential("<Key>");
const serviceClient = new WebPubSubServiceClient("<Endpoint>", key, "<hubName>");
```

### <a name="key-concepts"></a>Concepts clés

[!INCLUDE [Terms](includes/terms.md)]

### <a name="examples"></a>Exemples

#### <a name="broadcast-a-json-message-to-all-users"></a>Diffuser un message JSON à tous les utilisateurs

```js
const { WebPubSubServiceClient } = require("@azure/web-pubsub");

const serviceClient = new WebPubSubServiceClient("<ConnectionString>", "<hubName>");
await serviceClient.sendToAll({ message: "Hello world!" });
```

#### <a name="broadcast-a-plain-text-message-to-all-users"></a>Diffuser un message en texte brut à tous les utilisateurs

```js
const { WebPubSubServiceClient } = require("@azure/web-pubsub");

const serviceClient = new WebPubSubServiceClient("<ConnectionString>", "<hubName>");
await serviceClient.sendToAll("Hi there!", { contentType: "text/plain" });
```

#### <a name="broadcast-a-binary-message-to-all-users"></a>Diffuser un message binaire à tous les utilisateurs

```js
const { WebPubSubServiceClient } = require("@azure/web-pubsub");

const serviceClient = new WebPubSubServiceClient("<ConnectionString>", "<hubName>");

const payload = new Uint8Array(10);
await serviceClient.sendToAll(payload.buffer);
```

### <a name="troubleshooting"></a>Dépannage

Vous pouvez définir la variable d’environnement suivante pour obtenir les journaux de débogage quand vous utilisez cette bibliothèque :

- Obtention des journaux de débogage à partir de la bibliothèque cliente Signalr

```bash
export AZURE_LOG_LEVEL=verbose
```

Pour obtenir des instructions plus détaillées sur l’activation des journaux, consultez les [documents sur le package @azure/logger](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/core/logger).

<a name="express"></a>

## <a name="azure-web-pubsub-cloudevents-handlers-for-express"></a>Gestionnaires Azure Web Pubsub CloudEvents pour Express

Vous pouvez utilisez la bibliothèque Express pour effectuer les opérations suivantes :
- Ajouter un intergiciel Web PubSub CloudEvents pour gérer les événements entrants du client.
  - Gérer les demandes de validation d’abus.
  - Gérer les demandes d’événements client.

[Code source](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/web-pubsub/web-pubsub-express) |
[Package (NPM)](https://www.npmjs.com/package/@azure/web-pubsub-express) |
[Documentation de référence API](/javascript/api/@azure/web-pubsub-express/) |
[Documentation de produits](./index.yml) |
[Exemples][samples_ref]

### <a name="get-started"></a>Bien démarrer

Utilisez [Node.js](https://nodejs.org/) version 8.x.x ou ultérieure, ou [Express](https://expressjs.com/) version 4.x.x ou ultérieure. En outre, assurez-vous de disposer des prérequis suivants :

- Un [abonnement Azure][azure_sub].
- Un point de terminaison Azure Web PubSub existant.

#### <a name="install-the-azureweb-pubsub-express-package"></a>Installez le package `@azure/web-pubsub-express`

```bash
npm install @azure/web-pubsub-express
```

#### <a name="create-webpubsubeventhandler"></a>Créez `WebPubSubEventHandler`.

```js
const express = require("express");

const { WebPubSubEventHandler } = require("@azure/web-pubsub-express");
const handler = new WebPubSubEventHandler(
  "chat",
  ["https://<yourAllowedService>.webpubsub.azure.com"],
  {
    handleConnect: (req, res) => {
      // auth the connection and set the userId of the connection
      res.success({
        userId: "<userId>"
      });
    }
  }
);

const app = express();

app.use(handler.getMiddleware());

app.listen(3000, () =>
  console.log(`Azure WebPubSub Upstream ready at http://localhost:3000${handler.path}`)
);
```

### <a name="key-concepts"></a>Concepts clés

- **Événements clients** : un client crée des événements pendant le cycle de vie d’une connexion. Par exemple, une simple connexion cliente WebSocket crée les événements suivants :
  - Un événement `connect` quand elle tente de se connecter au service.
  - Un événement `connected` quand elle parvient à se connecter au service.
  - Un événement `message` quand elle envoie des messages au service.
  - Un événement `disconnected` quand elle se déconnecte du service.

- **Gestionnaire d’événements** : un gestionnaire d’événements contient la logique permettant de gérer les événements du client. Le gestionnaire d’événements doit être inscrit et configuré dans le service au préalable, via le portail Azure ou Azure CLI. Le serveur héberge généralement la logique du gestionnaire d’événements.

### <a name="troubleshooting"></a>Résolution des problèmes

#### <a name="dump-request"></a>Demande de vidage

Paramétrez `dumpRequest` sur `true` pour afficher les demandes entrantes.

#### <a name="live-trace"></a>Trace dynamique

Utilisez **Live Trace** du portail de service Web PubSub pour afficher le trafic.

[azure_sub]: https://azure.microsoft.com/free/
[samples_ref]: https://github.com/Azure/azure-webpubsub/tree/main/samples/javascript


## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [next step](includes/include-next-step.md)]