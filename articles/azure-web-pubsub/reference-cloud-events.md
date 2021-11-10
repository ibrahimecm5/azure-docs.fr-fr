---
title: 'Référence : Extension CloudEvents pour Azure Web PubSub'
description: La référence décrit l’extension CloudEvents définie pour le service Azure Web PubSub
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 11/08/2021
ms.openlocfilehash: 52c9d3f303b657a437beba31e6e6945346397e54
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2021
ms.locfileid: "131997359"
---
#  <a name="cloudevents-extension-for-azure-web-pubsub"></a>Extension CloudEvents pour Azure Web PubSub

Le service fournit des événements client au webhook en amont à l’aide du [protocole HTTP CloudEvents](https://github.com/cloudevents/spec/blob/v1.0.1/http-protocol-binding.md).

L’envoi des données du service au serveur est toujours au format `binary` CloudEvents.

- [Validation de webhook](#protection)
- [Extension d’attribut CloudEvents pour Web PubSub](#extension)
- [Événements](#events)
    - Événements bloquants
        - [Événement `connect` système](#connect)
        - [Événements liés aux utilisateurs](#message)
    - Événements débloquants
        - [Événement `connected` système](#connected)
        - [Événement `disconnected` système](#disconnected)

## <a name="webhook-validation"></a>Validation de webhook

<a name="protection"></a>

La validation de webhook suit [CloudEvents](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection). La requête contient toujours `WebHook-Request-Origin: xxx.webpubsub.azure.com` dans l’en-tête.

Si et seulement si la cible de remise autorise la remise des événements, elle DOIT répondre à la requête en incluant l’en-tête `WebHook-Allowed-Origin`, par exemple :

`WebHook-Allowed-Origin: *`

Ou :

`WebHook-Allowed-Origin: xxx.webpubsub.azure.com`

À l’heure actuelle, [WebHook-Request-Rate](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#414-webhook-request-rate) et [WebHook-Request-Callback](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#413-webhook-request-callback) ne sont pas pris en charge.


## <a name="web-pubsub-cloudevents-attribute-extension"></a>Extension d’attribut CloudEvents pour Web PubSub
<a name="extension"></a>

> Notez également que la spécification HTTP suit à présent un modèle similaire en ne suggérant plus que les en-têtes HTTP d’extension soient précédés de X-.

Cette extension définit les attributs utilisés par Web PubSub pour chaque événement généré.

### <a name="attributes"></a>Attributs

| Nom | Type | Description | Exemple|
|--|--|--|--|
| `userId` | `string` | L’utilisateur que la connexion a authentifié | |
| `hub` | `string` | Le hub auquel appartient la connexion | |
| `connectionId` | `string` | L’ID de connexion est unique pour la connexion cliente | |
| `eventName` | `string` | Le nom de l’événement sans préfixe | |
| `subprotocol` | `string` | Le sous-protocole utilisé par le client, le cas échéant | |
| `connectionState` | `string` | Définit l’état de la connexion. Vous pouvez utiliser le même en-tête de réponse pour réinitialiser la valeur de l’état. Il est interdit d'utiliser plusieurs en-têtes `connectionState`. Codez en base64 la valeur de chaîne si elle contient des caractères complexes. Par exemple, vous pouvez `base64(jsonString)` pour transmettre un objet complexe à l’aide de cet attribut.| |
| `signature` | `string` | La signature du webhook en amont pour valider si la requête entrante provient de l’origine attendue. Le service calcule la valeur à l’aide de la clé d’accès primaire et de la clé d’accès secondaire comme la clé HMAC : `Hex_encoded(HMAC_SHA256(accessKey, connectionId))`. L’amont doit vérifier si la requête est valide avant de la traiter. | |

## <a name="events"></a>Événements

Il existe deux types d’événements : d’une part, les événements *bloquants* pour lesquels le service attend la réponse de l’événement pour continuer. D’autre part, les événements *débloquants* pour lesquels le service n’attend pas la réponse de cet événement avant de traiter le message suivant.

- Événements bloquants
    - [Événement `connect` système](#connect)
    - [Événements liés aux utilisateurs](#message)
- Événements débloquants
    - [Événement `connected` système](#connected)
    - [Événement `disconnected` système](#disconnected)

### <a name="system-connect-event"></a>Événement `connect` système
<a name="connect"></a>

* `ce-type`: `azure.webpubsub.sys.connect`
* `Content-Type`: `application/json`

#### <a name="request-format"></a>Format de la requête :

```HTTP
POST /upstream HTTP/1.1
Host: xxxxxx
WebHook-Request-Origin: xxx.webpubsub.azure.com
Content-Type: application/json; charset=utf-8
Content-Length: nnnn
ce-specversion: 1.0
ce-type: azure.webpubsub.sys.connect
ce-source: /hubs/{hub}/client/{connectionId}
ce-id: {eventId}
ce-time: 2021-01-01T00:00:00Z
ce-signature: sha256={connection-id-hash-primary},sha256={connection-id-hash-secondary}
ce-userId: {userId}
ce-connectionId: {connectionId}
ce-hub: {hub}
ce-eventName: connect

{
    "claims": {},
    "query": {},
    "headers": {},
    "subprotocols": [],
    "clientCertificates": [
        {
            "thumbprint": "ABC"
        }
    ]
}

```

#### <a name="success-response-format"></a>Format de la réponse en cas de réussite :
* Code d’état :
    * `204` : Réussite, sans contenu.
    * `200` : Réussite, le contenu DOIT être au format JSON, avec les propriétés suivantes autorisées :
* En-tête `ce-connectionState` : si cet en-tête existe, l’état de cette connexion est mis à jour avec la valeur de l’en-tête. Notez que seuls les événements *bloquants* peuvent mettre à jour l’état de la connexion. L’exemple ci-dessous utilise une chaîne JSON codée en base64 pour stocker un état complexe pour la connexion.
* 
```HTTP
HTTP/1.1 200 OK
ce-connectionState: eyJrZXkiOiJhIn0=

{
    "groups": [],
    "userId": "",
    "roles": [],
    "subprotocol": ""
}

```

* `subprotocols`

    L’événement `connect` transmet à l’amont les informations de sous-protocole et d’authentification du client. L’instance Azure SignalR Service utilise le code d’état pour déterminer si la requête sera mise à niveau vers le protocole WebSocket.

    Si la requête contient la propriété `subprotocols`, le serveur doit retourner un sous-protocole qu’il prend en charge. Si le serveur ne souhaite pas utiliser de sous-protocoles, il ne doit **pas** envoyer la propriété `subprotocol` dans sa réponse. [L’envoi d’un en-tête vide n’est pas valide](https://developer.mozilla.org/en-US/docs/Web/API/WebSockets_API/Writing_WebSocket_servers#Subprotocols).

* `userId`: `{auth-ed user ID}`

    Étant donné que le service autorise les connexions anonymes, il incombe à l’événement `connect` d’indiquer au service l’identifiant utilisateur de la connexion cliente. Le service lira l’identifiant utilisateur à partir de la charge utile de réponse `userId` le cas échéant. La connexion sera annulée si l’identifiant utilisateur ne peut pas être lu à partir des revendications de la requête ni de la charge utile de réponse de l’événement `connect`.

<a name="connect_response_header_group"></a>
 
* `groups`: `{groups to join}`

    La propriété est un moyen pratique pour l’utilisateur d’ajouter cette connexion à un ou plusieurs groupes. De cette façon, un autre appel n’est pas nécessaire pour ajouter cette connexion à un groupe.

* `roles`: `{roles the client has}`
    
    La propriété est un moyen pour l’amont d’autoriser le client. Des rôles différents définissent des autorisations initiales différentes pour le client, ce qui peut être utile lorsque le client est de type WebSocket PubSub. Des informations détaillées sur les autorisations sont fournies dans [Autorisations client](./concept-client-protocols.md#permissions).

#### <a name="error-response-format"></a>Format de la réponse en cas d’erreur :

* `4xx` : Erreur, la réponse de l’amont sera renvoyée comme réponse à la requête du client.

```HTTP
HTTP/1.1 401 Unauthorized
```

### <a name="system-connected-event"></a>Événement `connected` système
<a name="connected"></a> Le service appelle l’amont lorsque le client termine l’établissement de liaison WebSocket et est bien connecté.

* `ce-type`: `azure.webpubsub.sys.connected`
* `Content-Type`: `application/json`
* `ce-connectionState`: `eyJrZXkiOiJhIn0=`

Le corps de la requête est un JSON vide.

#### <a name="request-format"></a>Format de la requête :

```HTTP
POST /upstream HTTP/1.1
Host: xxxxxx
WebHook-Request-Origin: xxx.webpubsub.azure.com
Content-Type: application/json; charset=utf-8
Content-Length: nnnn
ce-specversion: 1.0
ce-type: azure.webpubsub.sys.connected
ce-source: /hubs/{hub}/client/{connectionId}
ce-id: {eventId}
ce-time: 2021-01-01T00:00:00Z
ce-signature: sha256={connection-id-hash-primary},sha256={connection-id-hash-secondary}
ce-userId: {userId}
ce-connectionId: {connectionId}
ce-hub: {hub}
ce-eventName: connect
ce-subprotocol: abc
ce-connectionState: eyJrZXkiOiJhIn0=

{}

```

#### <a name="response-format"></a>Format de la réponse :

`2xx` : réponse en cas de réussite.

`connected` est un événement asynchrone, si le code d’état de la réponse n’indique pas une réussite, le service consigne une erreur.

```HTTP
HTTP/1.1 200 OK
```


### <a name="system-disconnected-event"></a>Événement `disconnected` système
L’événement <a name="disconnected"></a>
`disconnected` est **toujours** déclenché lorsque la requête du client est terminée si l’événement **connect** renvoie le code d’état `2xx`.

* `ce-type`: `azure.webpubsub.sys.disconnected`
* `Content-Type`: `application/json`

#### <a name="request-format"></a>Format de la requête :

```HTTP
POST /upstream HTTP/1.1
Host: xxxxxx
WebHook-Request-Origin: xxx.webpubsub.azure.com
Content-Type: application/json; charset=utf-8
Content-Length: nnnn
ce-specversion: 1.0
ce-type: azure.webpubsub.sys.disconnected
ce-source: /hubs/{hub}/client/{connectionId}
ce-id: {eventId}
ce-time: 2021-01-01T00:00:00Z
ce-signature: sha256={connection-id-hash-primary},sha256={connection-id-hash-secondary}
ce-userId: {userId}
ce-connectionId: {connectionId}
ce-hub: {hub}
ce-eventName: disconnect
ce-subprotocol: abc
ce-connectionState: eyJrZXkiOiJhIn0=

{
    "reason": "{Reason}"
}

```

* `reason`

    `reason` décrit le motif de la déconnexion du client.


#### <a name="response-format"></a>Format de la réponse :

`2xx` : réponse en cas de réussite.

`disconnected` est un événement asynchrone, si le code d’état de la réponse n’indique pas une réussite, le service consigne une erreur.

```HTTP
HTTP/1.1 200 OK
```


### <a name="user-event-message-for-the-simple-websocket-clients"></a>Événement utilisateur `message` pour les clients WebSocket simples
<a name="message"></a> Le service appelle le gestionnaire d’événements en amont pour chaque trame de message WebSocket.

* `ce-type`: `azure.webpubsub.user.message`
* `Content-Type` : `application/octet-stream` pour une trame binaire ; `text/plain` pour une trame texte. 

UserPayload correspond à ce que le client envoie.

#### <a name="request-format"></a>Format de la requête :

```HTTP
POST /upstream HTTP/1.1
Host: xxxxxx
WebHook-Request-Origin: xxx.webpubsub.azure.com
Content-Type: application/octet-stream | text/plain | application/json
Content-Length: nnnn
ce-specversion: 1.0
ce-type: azure.webpubsub.user.message
ce-source: /hubs/{hub}/client/{connectionId}
ce-id: {eventId}
ce-time: 2021-01-01T00:00:00Z
ce-signature: sha256={connection-id-hash-primary},sha256={connection-id-hash-secondary}
ce-userId: {userId}
ce-connectionId: {connectionId}
ce-hub: {hub}
ce-eventName: message
ce-connectionState: eyJrZXkiOiJhIn0=

UserPayload

```

#### <a name="success-response-format"></a>Format de la réponse en cas de réussite

* Code d'état
    * `204` : Réussite, sans contenu.
    * `200` : Réussite, le format de `UserResponsePayload` dépend du `Content-Type` de la réponse.
* `Content-Type` : `application/octet-stream` pour une trame binaire ; `text/plain` pour une trame texte. 
* En-tête `Content-Type` : `application/octet-stream` pour une trame binaire ; `text/plain` pour une trame texte ; 
* En-tête `ce-connectionState` : si cet en-tête existe, l’état de cette connexion est mis à jour avec la valeur de l’en-tête. Notez que seuls les événements *bloquants* peuvent mettre à jour l’état de la connexion. L’exemple ci-dessous utilise une chaîne JSON codée en base64 pour stocker un état complexe pour la connexion.

Lorsque `Content-Type` est `application/octet-stream`, le service envoie `UserResponsePayload` au client à l’aide d’une trame WebSocket `binary`. Lorsque `Content-Type` est `text/plain`, le service envoie `UserResponsePayload` au client à l’aide d’une trame WebSocket `text`. 

```HTTP
HTTP/1.1 200 OK
Content-Type: application/octet-stream (for binary frame) or text/plain (for text frame)
Content-Length: nnnn
ce-connectionState: eyJrZXkiOiJhIn0=

UserResponsePayload
```

#### <a name="error-response-format"></a>Format de la réponse en cas d’erreur
Lorsque le code d’état n’indique pas une réussite, il est considéré comme une réponse d’erreur. La connexion sera **annulée** si le code d’état de la réponse `message` n’est pas une réussite.

### <a name="user-custom-event-custom_event-for-pubsub-websocket-clients"></a>Événement personnalisé utilisateur `{custom_event}` pour les clients WebSocket PubSub
<a name="custom_event"></a>

Le service appelle le webhook du gestionnaire d’événements pour chaque message d’événement personnalisé valide.

#### <a name="case-1-send-event-with-text-data"></a>Cas 1 : envoyer un événement avec des données texte :
```json
{
    "type": "event",
    "event": "<event_name>",
    "dataType" : "text",
    "data": "text data"
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
ce-subprotocol: json.webpubsub.azure.v1
ce-connectionState: eyJrZXkiOiJhIn0=

text data

```

#### <a name="case-2-send-event-with-json-data"></a>Cas 2 : envoyer un événement avec des données JSON :
```json
{
    "type": "event",
    "event": "<event_name>",
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
ce-subprotocol: json.webpubsub.azure.v1
ce-connectionState: eyJrZXkiOiJhIn0=

{
    "hello": "world"
}

```

#### <a name="case-3-send-event-with-binary-data"></a>Cas 3 : envoyer un événement avec des données binaires :
```json
{
    "type": "event",
    "event": "<event_name>",
    "dataType" : "binary",
    "data": "aGVsbG8gd29ybGQ=" // base64 encoded binary
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
ce-subprotocol: json.webpubsub.azure.v1

<binary data>

```

#### <a name="success-response-format"></a>Format de la réponse en cas de réussite

```HTTP
HTTP/1.1 200 OK
Content-Type: application/octet-stream | text/plain | application/json
Content-Length: nnnn

UserResponsePayload
```
* Code d'état
    * `204` : Réussite, sans contenu.
    * `200` : Réussite, l’envoi de données au client WebSocket PubSub dépend du `Content-Type`. 
* En-tête `ce-connectionState` : si cet en-tête existe, l’état de cette connexion est mis à jour avec la valeur de l’en-tête. Notez que seuls les événements *bloquants* peuvent mettre à jour l’état de la connexion. L’exemple ci-dessous utilise une chaîne JSON codée en base64 pour stocker un état complexe pour la connexion.
* Lorsque En-tête `Content-Type` est `application/octet-stream`, le service renvoie `UserResponsePayload` au client avec `dataType` correspondant à `binary` et une charge utile codée en base64. Exemple de réponse :
    ```json
    {
        "type": "message",
        "from": "server",
        "dataType": "binary",
        "data" : "aGVsbG8gd29ybGQ="
    }
    ```
* Lorsque `Content-Type` est `text/plain`, le service envoie `UserResponsePayload` au client avec `dataType` correspondant à `text` et une chaîne de charge utile.
* Lorsque `Content-Type` est `application/json`, le service envoie `UserResponsePayload` au client avec `dataType`=`json` et le jeton de valeur `data` comme corps de la charge utile de réponse.


#### <a name="error-response-format"></a>Format de la réponse en cas d’erreur
Lorsque le code d’état n’indique pas une réussite, il est considéré comme une réponse d’erreur. La connexion sera **annulée** si le code d’état de réponse `{custom_event}` n’est pas une réussite.

## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [next step](includes/include-next-step.md)]
