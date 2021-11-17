---
title: Référence - Sous-protocole JSON WebSocket pris en charge par Azure Web PubSub`json.webpubsub.azure.v1`
description: La référence décrit le sous-protocole WebSocket pris en charge par Azure Web PubSub `json.webpubsub.azure.v1`
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 11/06/2021
ms.openlocfilehash: 2208c41ea49bae4ad3791a7e26f694e4cf4fbbd3
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2021
ms.locfileid: "131997853"
---
#  <a name="azure-web-pubsub-supported-json-websocket-subprotocol"></a>Sous-protocole JSON WebSocket pris en charge par Azure Web PubSub
     
Ce document décrit le sous-protocole `json.webpubsub.azure.v1`.

Lorsque le client utilise ce sous-protocole, la trame de données sortante et la trame de données entrante doivent être des charges utiles **JSON**.

## <a name="overview"></a>Vue d'ensemble

Le sous-protocole `json.webpubsub.azure.v1` permet aux clients de publier/s’abonner directement à la place d’un aller-retour au serveur en amont. Nous appelons la connexion WebSocket avec le sous-protocole `json.webpubsub.azure.v1` un client WebSocket PubSub.

Par exemple, dans JS, un client WebSocket PubSub peut être créé à l’aide des éléments suivants :
```js
// PubSub WebSocket client
var pubsub = new WebSocket('wss://test.webpubsub.azure.com/client/hubs/hub1', 'json.webpubsub.azure.v1');
```
Pour un client WebSocket simple, le *serveur* est un rôle indispensable pour gérer les événements des clients. Une connexion WebSocket simple déclenche toujours un événement `message` lors de l’envoi de messages et s’appuie toujours sur le côté serveur pour traiter les messages et effectuer d’autres opérations. Avec l’aide du sous-protocole `json.webpubsub.azure.v1`, un client autorisé peut rejoindre un groupe avec des [requêtes d’adhésion](#join-groups) et publier des messages directement dans un groupe avec des [requêtes de publication](#publish-messages). Il peut également router des messages vers différents gestionnaires d'événements en amont en personnalisant l’*événement* auquel appartient le message avec des [requêtes d’événement](#send-custom-events).

## <a name="permissions"></a>Autorisations

Vous l’avez peut-être remarqué lorsque nous avons décrit les clients WebSocket PubSub, un client ne peut publier vers d’autres clients que s’il y est *autorisé*. Les `role`s du client déterminent les autorisations *initiales* dont dispose le client :

| Rôle | Autorisation |
|---|---|
| Non spécifié | Le client peut envoyer des requêtes d’événements.
| `webpubsub.joinLeaveGroup` | Le client peut rejoindre/quitter n’importe quel groupe.
| `webpubsub.sendToGroup` | Le client peut publier des messages dans n’importe quel groupe.
| `webpubsub.joinLeaveGroup.<group>` | Le client peut rejoindre/quitter le groupe `<group>`.
| `webpubsub.sendToGroup.<group>` | Le client peut publier des messages dans le groupe `<group>`.

Le côté serveur peut également octroyer ou révoquer des autorisations du client de manière dynamique via des API REST ou des kits de développement logiciel (SDK) serveur.

## <a name="requests"></a>Demandes

### <a name="join-groups"></a>Rejoindre des groupes

Format:

```json
{
    "type": "joinGroup",
    "group": "<group_name>",
    "ackId" : 1
}
```

* `ackId` est l’identité de chaque demande et doit être unique. Le service envoie un [message de réponse Ack](#ack-response) pour notifier le résultat du processus de la demande. Pour plus d’informations, consultez [AckId et réponse Ack](./concept-client-protocols.md#ackid-and-ack-response).

### <a name="leave-groups"></a>Quitter des groupes

Format:

```json
{
    "type": "leaveGroup",
    "group": "<group_name>",
    "ackId" : 1
}
```

* `ackId` est l’identité de chaque demande et doit être unique. Le service envoie un [message de réponse Ack](#ack-response) pour notifier le résultat du processus de la demande. Pour plus d’informations, consultez [AckId et réponse Ack](./concept-client-protocols.md#ackid-and-ack-response).

### <a name="publish-messages"></a>Publier des messages

Format:

```json
{
    "type": "sendToGroup",
    "group": "<group_name>",
    "ackId" : 1,
    "noEcho": true|false,
    "dataType" : "json|text|binary",
    "data": {}, // data can be string or valid json token depending on the dataType 
}
```

* `ackId` est l’identité de chaque demande et doit être unique. Le service envoie un [message de réponse Ack](#ack-response) pour notifier le résultat du processus de la demande. Pour plus d’informations, consultez [AckId et réponse Ack](./concept-client-protocols.md#ackid-and-ack-response).
* `noEcho` est facultatif. Si la valeur est true, ce message n’est pas renvoyé à la même connexion. Si elle n’est pas définie, la valeur par défaut est false.
* `dataType` peut être `json`, `text` ou `binary` :
     * `json` : les `data` peuvent être de n’importe quel type pris en charge par JSON et seront publiées telles quelles. Si `dataType` n’est pas spécifié, ce sera `json` par défaut.
     * `text` : les `data` doivent être au format chaîne et les données de chaîne seront publiées ;
     * `binary` : les `data` doivent être au format base64, et les données binaires seront publiées ;

#### <a name="case-1-publish-text-data"></a>Cas 1 : publier des données texte :
```json
{
    "type": "sendToGroup",
    "group": "<group_name>",
    "dataType" : "text",
    "data": "text data",
    "ackId": 1
}
```

* Ce que le client de sous-protocole dans ce groupe `<group_name>` reçoit :
```json
{
    "type": "message",
    "from": "group",
    "group": "<group_name>",
    "dataType" : "text",
    "data" : "text data"
}
```
* Ce que le client brut dans ce groupe `<group_name>` reçoit sont des données de chaîne `text data`.

#### <a name="case-2-publish-json-data"></a>Cas 2 : publier des données JSON :
```json
{
    "type": "sendToGroup",
    "group": "<group_name>",
    "dataType" : "json",
    "data": {
        "hello": "world"
    }
}
```

* Ce que le client de sous-protocole dans ce groupe `<group_name>` reçoit :
```json
{
    "type": "message",
    "from": "group",
    "group": "<group_name>",
    "dataType" : "json",
    "data" : {
        "hello": "world"
    }
}
```
* Ce que le client brut dans ce groupe `<group_name>` reçoit sont des données de chaîne sérialisées `{"hello": "world"}`.


#### <a name="case-3-publish-binary-data"></a>Cas 3 : publier des données binaires :
```json
{
    "type": "sendToGroup",
    "group": "<group_name>",
    "dataType" : "binary",
    "data": "<base64_binary>",
    "ackId": 1
}
```

* Ce que le client de sous-protocole dans ce groupe `<group_name>` reçoit :
```json
{
    "type": "message",
    "from": "group",
    "group": "<group_name>",
    "dataType" : "binary",
    "data" : "<base64_binary>", 
}
```
* Ce que le client brut dans ce groupe `<group_name>` reçoit sont des données **binaires** dans la trame binaire.

### <a name="send-custom-events"></a>Envoyer des événements personnalisés

Format:

```json
{
    "type": "event",
    "event": "<event_name>",
    "ackId": 1,
    "dataType" : "json|text|binary",
    "data": {}, // data can be string or valid json token depending on the dataType 
}
```

* `ackId` est l’identité de chaque demande et doit être unique. Le service envoie un [message de réponse Ack](#ack-response) pour notifier le résultat du processus de la demande. Pour plus d’informations, consultez [AckId et réponse Ack](./concept-client-protocols.md#ackid-and-ack-response).

`dataType` peut être `text`, `binary` ou `json` :
* `json` : les données peuvent être de n’importe quel type pris en charge par JSON et seront publiées telles quelles. Si `dataType` n’est pas spécifié, ce sera `json` par défaut.
* `text` : les données doivent être au format chaîne et les données de chaîne seront publiées ;
* `binary` : les données doivent être au format base64, et les données binaires seront publiées ;

#### <a name="case-1-send-event-with-text-data"></a>Cas 1 : envoyer un événement avec des données texte :
```json
{
    "type": "event",
    "event": "<event_name>",
    "ackId": 1,
    "dataType" : "text",
    "data": "text data", 
}
```

Ce que le gestionnaire d’événements en amont reçoit ressemble à ceci, le `Content-Type` pour la requête HTTP CloudEvents est `text/plain` pour `dataType`=`text`

```HTTP
POST /upstream HTTP/1.1
Host: xxxxxx
WebHook-Request-Origin: xxx.webpubsub.azure.com
Content-Type: text/plain
Content-Length: nnnn
ce-specversion: 1.0
ce-type: azure.webpubsub.user.<event_name>
ce-source: /client/{connectionId}
ce-id: {eventId}
ce-time: 2021-01-01T00:00:00Z
ce-signature: sha256={connection-id-hash-primary},sha256={connection-id-hash-secondary}
ce-userId: {userId}
ce-connectionId: {connectionId}
ce-hub: {hub_name}
ce-eventName: <event_name>

text data

```

#### <a name="case-2-send-event-with-json-data"></a>Cas 2 : envoyer un événement avec des données JSON :
```json
{
    "type": "event",
    "event": "<event_name>",
    "ackId": 1,
    "dataType" : "json",
    "data": {
        "hello": "world"
    }, 
}
```

Ce que le gestionnaire d’événements en amont reçoit ressemble à ceci, le `Content-Type` pour la requête HTTP CloudEvents est `application/json` pour `dataType`=`json`

```HTTP
POST /upstream HTTP/1.1
Host: xxxxxx
WebHook-Request-Origin: xxx.webpubsub.azure.com
Content-Type: application/json
Content-Length: nnnn
ce-specversion: 1.0
ce-type: azure.webpubsub.user.<event_name>
ce-source: /client/{connectionId}
ce-id: {eventId}
ce-time: 2021-01-01T00:00:00Z
ce-signature: sha256={connection-id-hash-primary},sha256={connection-id-hash-secondary}
ce-userId: {userId}
ce-connectionId: {connectionId}
ce-hub: {hub_name}
ce-eventName: <event_name>

{
    "hello": "world"
}

```

#### <a name="case-3-send-event-with-binary-data"></a>Cas 3 : envoyer un événement avec des données binaires :
```json
{
    "type": "event",
    "event": "<event_name>",
    "ackId": 1,
    "dataType" : "binary",
    "data": "base64_binary", 
}
```

Ce que le gestionnaire d’événements en amont reçoit ressemble à ceci, le `Content-Type` pour la requête HTTP CloudEvents est `application/octet-stream` pour `dataType`=`binary`

```HTTP
POST /upstream HTTP/1.1
Host: xxxxxx
WebHook-Request-Origin: xxx.webpubsub.azure.com
Content-Type: application/octet-stream
Content-Length: nnnn
ce-specversion: 1.0
ce-type: azure.webpubsub.user.<event_name>
ce-source: /client/{connectionId}
ce-id: {eventId}
ce-time: 2021-01-01T00:00:00Z
ce-signature: sha256={connection-id-hash-primary},sha256={connection-id-hash-secondary}
ce-userId: {userId}
ce-connectionId: {connectionId}
ce-hub: {hub_name}
ce-eventName: <event_name>

binary

```

La trame WebSocket peut être au format `text` pour les trames de message texte ou de binaires codés en UTF8 pour les trames de message `binary`.

Le service refuse le client si le message ne correspond pas au format décrit.

## <a name="responses"></a>Réponses

Les messages reçus par le client peuvent être de plusieurs types : `ack`, `message` et `system` : 

### <a name="ack-response"></a>Réponse ACK

Si la requête contient `ackId`, le service renvoie une réponse ACK pour cette requête. L’implémentation du client doit gérer ce mécanisme ACK, y compris l’attente de la réponse ACK pour une opération `async` `await`, et la vérification du délai d’attente lorsque la réponse ACK n’est pas reçue pendant une certaine période.

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

L’implémentation du client DOIT toujours vérifier en premier si la `success` est `true` ou `false`. Ce n’est que lorsque `success` est `false` que le client lit `error`.

### <a name="message-response"></a>Réponse de message

Les clients peuvent recevoir des messages publiés par un groupe que le client a rejoint, ou par le rôle de gestion de serveur du serveur qui envoie des messages au client ou à l’utilisateur spécifique.

1. Lorsque le message provient d’un groupe

    ```json
    {
        "type": "message",
        "from": "group",
        "group": "<group_name>",
        "dataType": "json|text|binary",
        "data" : {} // The data format is based on the dataType
        "fromUserId": "abc"
    }
    ```

1. Lorsque le message provient du serveur.

    ```json
    {
        "type": "message",
        "from": "server",
        "dataType": "json|text|binary",
        "data" : {} // The data format is based on the dataType
    }
    ```

#### <a name="case-1-sending-data-hello-world-to-the-connection-through-rest-api-with-content-typetextplain"></a>Cas 1 : envoi de données `Hello World` à la connexion via l’API REST avec `Content-Type`=`text/plain` 
* Ce que reçoit un client WebSocket simple est une trame WebSocket de texte avec des données : `Hello World` ;
* Ce qu’un client WebSocket PubSub reçoit ressemble à ceci :
    ```json
    {
        "type": "message",
        "from": "server",
        "dataType" : "text",
        "data": "Hello World", 
    }
    ```

#### <a name="case-2-sending-data--hello--world-to-the-connection-through-rest-api-with-content-typeapplicationjson"></a>Cas 2 : envoi de données `{ "Hello" : "World"}` à la connexion via l’API REST avec `Content-Type`=`application/json`
* Ce que reçoit un client WebSocket simple est une trame WebSocket de texte avec des données de chaîne : `{ "Hello" : "World"}` ;
* Ce qu’un client WebSocket PubSub reçoit ressemble à ceci :
    ```json
    {
        "type": "message",
        "from": "server",
        "dataType" : "json",
        "data": {
            "Hello": "World"
        }
    }
    ```

Si l’API REST envoie une chaîne `Hello World` avec le type de contenu `application/json`, ce que le client WebSocket simple reçoit est une chaîne JSON, autrement dit `"Hello World"` qui entoure la chaîne de `"`.

#### <a name="case-3-sending-binary-data-to-the-connection-through-rest-api-with-content-typeapplicationoctet-stream"></a>Cas 3 : envoi de données binaires à la connexion via l’API REST avec `Content-Type`=`application/octet-stream`
* Ce que reçoit un client WebSocket simple est une trame WebSocket binaire avec les données binaires.
* Ce qu’un client WebSocket PubSub reçoit ressemble à ceci :
    ```json
    {
        "type": "message",
        "from": "server",
        "dataType" : "binary",
        "data": "<base64_binary>"
    }
    ```

### <a name="system-response"></a>Réponse du système

Le service Web PubSub peut également envoyer au client des réponses liées au système. 

#### <a name="connected"></a>Connecté

Lorsque la connexion se connecte au service.

```json
{
    "type": "system",
    "event": "connected",
    "userId": "user1",
    "connectionId": "abcdefghijklmnop",
}
```

#### <a name="disconnected"></a>Déconnecté

Lorsque le serveur ferme la connexion ou lorsque le service refuse le client.

```json
{
    "type": "system",
    "event": "disconnected",
    "message": "reason"
}
```

## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [next step](includes/include-next-step.md)]
