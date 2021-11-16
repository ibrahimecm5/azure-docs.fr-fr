---
title: Protocoles clients WebSocket pour Azure Web PubSub
description: Cet article présente une vue d’ensemble des protocoles clients pour Azure Web PubSub.
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 11/08/2021
ms.openlocfilehash: 58fef1f2ada19af2ff81ef27d5ff5ab2ad4cebf7
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2021
ms.locfileid: "131997946"
---
#  <a name="websocket-client-protocols-for-azure-web-pubsub"></a>Protocoles clients WebSocket pour Azure Web PubSub

Les clients se connectent à Azure Web PubSub à l’aide du protocole [WebSocket](https://datatracker.ietf.org/doc/html/rfc6455) standard.

## <a name="service-endpoints"></a>Points de terminaison de service
Le service Web PubSub fournit deux types de points de terminaison auxquels les clients peuvent se connecter :
* `/client/hubs/{hub}`
* `/client/?hub={hub}`

`{hub}` est un paramètre obligatoire qui agit comme isolement pour différentes applications. Vous pouvez le définir soit dans le chemin d’accès, soit dans la requête.

## <a name="authorization"></a>Autorisation

Les clients se connectent au service avec un JSON Web Token (JWT). Le jeton peut être dans la chaîne de requête, sous la forme `/client/?hub={hub}&access_token={token}`, ou dans l’en-tête `Authorization`, sous la forme `Authorization: Bearer {token}`.

Voici un flux de travail d’autorisation générale :

1. Le client négocie avec votre serveur d’applications. Le serveur d’applications contient l’intergiciel d’autorisation, qui traite la demande du client et signe un JWT pour que le client puisse se connecter au service.
1. Le serveur d’applications renvoie le JWT et l’URL du service au client.
1. Le client tente de se connecter au service Web PubSub en utilisant l’URL et le JWT renvoyés par le serveur d’applications.

<a name="simple_client"></a>

## <a name="the-simple-websocket-client"></a>Client WebSocket simple

Un **client WebSocket simple**, comme son nom l’indique, est une connexion WebSocket simple. Il peut également avoir son propre sous-protocole personnalisé. 

Par exemple, dans JavaScript, vous pouvez créer un client WebSocket simple en utilisant le code suivant :

```js
// simple WebSocket client1
var client1 = new WebSocket('wss://test.webpubsub.azure.com/client/hubs/hub1');

// simple WebSocket client2 with some custom subprotocol
var client2 = new WebSocket('wss://test.webpubsub.azure.com/client/hubs/hub1', 'custom.subprotocol')
```

## <a name="the-pubsub-websocket-client"></a>Le client WebSocket PubSub

Un **client WebSocket PubSub** est le client WebSocket qui utilise des sous-protocoles définis par le service Azure Web PubSub :

* `json.webpubsub.azure.v1`
* `protobuf.webpubsub.azure.v1`

Avec le sous-protocole pris en charge par le service, les **clients WebSocket PubSub** peuvent publier directement des messages dans des groupes lorsqu’ils disposent des [autorisations](#permissions).

### <a name="the-jsonwebpubsubazurev1-subprotocol"></a>Le sous-protocole `json.webpubsub.azure.v1`

Consultez [ceci pour découvrir le sous-protocole JSON en détail](reference-json-webpubsub-subprotocol.md).

#### <a name="create-a-pubsub-websocket-client"></a>Créer un client WebSocket PubSub

```js
var pubsubClient = new WebSocket('wss://test.webpubsub.azure.com/client/hubs/hub1', 'json.webpubsub.azure.v1');
```

#### <a name="join-a-group-from-the-client-directly"></a>Rejoindre directement un groupe à partir du client

```js
let ackId = 0;
pubsubClient.send(    
    JSON.stringify({
        type: 'joinGroup',
        group: 'group1',
        ackId: ++ackId
    }));
```

#### <a name="send-messages-to-a-group-from-the-client-directly"></a>Envoyer des messages à un groupe directement à partir du client

```js
let ackId = 0;
pubsubClient.send(    
    JSON.stringify({
        type: 'sendToGroup',
        group: 'group1',
        ackId: ++ackId,
        dataType: "json",
        data: {
            "hello": "world"
        }
    }));
```

### <a name="the-protobufwebpubsubazurev1-subprotocol"></a>Le sous-protocole `protobuf.webpubsub.azure.v1`

Les mémoires tampons de protocole (protobuf) désignent un protocole binaire indépendant du langage et de la plateforme qui simplifie l’envoi de données binaires. Protobuf fournit des outils permettant de générer des clients pour de nombreux langages, tels que Java, Python, Objective-C, C# et C++. [En savoir plus sur protobuf](https://developers.google.com/protocol-buffers).

Par exemple, dans JavaScript, vous pouvez créer un **client WebSocket PubSub** avec un sous-protocole protobuf à l’aide du code suivant :

```js
// PubSub WebSocket client
var pubsub = new WebSocket('wss://test.webpubsub.azure.com/client/hubs/hub1', 'protobuf.webpubsub.azure.v1');
```

Consultez [ceci pour découvrir le sous-protocole protobuf en détail](reference-protobuf-webpubsub-subprotocol.md).


### <a name="ackid-and-ack-response"></a>AckId et réponse ACK

Le **client WebSocket PubSub** prend en charge la propriété `ackId` pour les messages `joinGroup`, `leaveGroup`, `sendToGroup` et `event`. Lorsque vous utilisez `ackId`, vous pouvez recevoir un message de réponse ACK quand votre demande est traitée. Vous pouvez choisir d’omettre `ackId` dans les scénarios « fire and forget ». Dans l’article, nous décrivons les différences de comportement entre la spécification de `ackId` ou pas.

#### <a name="behavior-when-no-ackid-specified"></a>Comportement quand `ackId` n’est pas spécifié

Si `ackId` n’est pas spécifié, il est de type « fire and forget ». Même s’il y a des erreurs lors de la répartition des messages, vous n’avez aucun moyen d’être notifié.

#### <a name="behavior-when-ackid-specified"></a>Comportement quand `ackId` est spécifié

##### <a name="idempotent-publish"></a>Publication idempotente

`ackId` est un nombre uint64 et doit être unique au sein d’un client avec le même ID de connexion. Le service Web PubSub enregistre `ackId` et les messages avec le même `ackId` seront traités comme étant le même message. Le service refuse de répartir le même message plusieurs fois, ce qui est utile dans les nouvelles tentatives pour éviter les messages en double. Par exemple, si un client envoie un message avec `ackId=5` et ne reçoit pas de réponse ACK avec `ackId=5`, le client effectue une nouvelle tentative et renvoie le même message. Dans certains cas, le message est déjà réparti et la réponse ACK est perdue pour une raison quelconque, le service rejette la nouvelle tentative et retourne une réponse ACK avec le motif `Duplicate`.

#### <a name="ack-response"></a>Réponse ACK

Le service Web PubSub envoie une réponse ACK pour chaque demande avec `ackId`.

Format:
```json
{
    "type": "ack",
    "ackId": 1, // The ack id for the request to ack
    "success": false, // true or false
    "error": {
        "name": "Forbidden|InternalServerError|Duplicate",
        "message": "<error_detail>"
    }
}
```

* `ackId` associe la demande.

* `success` est une valeur booléenne et indique si la demande est correctement traitée par le service. Si c’est `false`, les clients doivent vérifier `error`.

* `error` existe uniquement lorsque `success` est `false` et les clients doivent avoir une logique différente pour des éléments `name` différents. Vous devez supposer qu’il peut y avoir plus de types `name` à l’avenir.
    - `Forbidden` : le client n’a pas l’autorisation d’accès à la demande. Le client doit être ajouté aux rôles pertinents.
    - `InternalServerError` : une erreur interne s’est produite dans le service. Une nouvelle tentative est nécessaire.
    - `Duplicate` : le message avec le même `ackId` a déjà été traité par le service.

### <a name="permissions"></a>Autorisations

Comme vous l’avez probablement remarqué dans la description précédente du client WebSocket PubSub, un client ne peut publier vers d’autres clients que lorsqu’il est *autorisé* à le faire. Les autorisations d’un client peuvent être accordées au moment de la connexion ou pendant la durée de vie de la connexion.

| Rôle | Autorisation |
|---|---|
| Non spécifié | Le client peut envoyer des requêtes d’événements. |
| `webpubsub.joinLeaveGroup` | Le client peut rejoindre ou quitter n’importe quel groupe. |
| `webpubsub.sendToGroup` | Le client peut publier des messages dans n’importe quel groupe. |
| `webpubsub.joinLeaveGroup.<group>` | Le client peut rejoindre ou quitter le groupe `<group>`. |
| `webpubsub.sendToGroup.<group>` | Le client peut publier des messages dans le groupe `<group>`. |
| | |

L’autorisation d’un client peut être accordée de plusieurs façons :

#### <a name="1-assign-the-role-to-the-client-when-generating-the-access-token"></a>1. Attribuer le rôle au client lors de la génération du jeton d’accès

Le client peut se connecter au service à l’aide d’un jeton JWT, la charge utile de jeton peut transporter des informations telles que le `role` du client. Lors de la signature du jeton JWT au client, vous pouvez accorder des autorisations au client en lui attribuant des rôles spécifiques.

Par exemple, signons un jeton JWT qui a l’autorisation d’envoyer des messages à `group1` et à `group2` : 

```js
let token = await serviceClient.getClientAccessToken({
    roles: [ "webpubsub.sendToGroup.group1", "webpubsub.sendToGroup.group2" ]
});
```

#### <a name="2-assign-the-role-to-the-client-with-the-connect-event-handler"></a>2. Attribuer le rôle au client avec le gestionnaire d’événements `connect`

Les rôles des clients peuvent également être définis lors de l’inscription du gestionnaire d’événements `connect` et le gestionnaire d’événements en amont peut retourner le `roles` du client au service Web PubSub lors de la gestion des événements `connect`.

Par exemple, dans JavaScript, vous pouvez configurer l’événement `handleConnect` pour effectuer cette opération :

```js
let handler = new WebPubSubEventHandler("hub1", {
  handleConnect: (req, res) => {
    // auth the connection and set the userId of the connection
    res.success({
      roles: [ "webpubsub.sendToGroup.group1", "webpubsub.sendToGroup.group2" ]
    });
  },
});
```

#### <a name="3-assign-the-role-to-the-client-through-rest-apis-or-server-sdks-during-runtime"></a>3. Attribuer le rôle au client via des API REST ou des SDK serveur pendant l’exécution

```js
let service = new WebPubSubServiceClient("<your_connection_string>", "test-hub");
await service.grantPermission("<connection_id>", "joinLeaveGroup", { targetName: "group1" });
```

## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [next step](includes/include-next-step.md)]
