---
title: Référence - Sous-protocole protobuf WebSocket pris en charge par Azure Web PubSub`protobuf.webpubsub.azure.v1`
description: La référence décrit le sous-protocole WebSocket pris en charge par Azure Web PubSub `protobuf.webpubsub.azure.v1`.
author: chenyl
ms.author: chenyl
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 11/08/2021
ms.openlocfilehash: 8e801391e5ac2ebe2a4dee276f525885ff84f449
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2021
ms.locfileid: "131997796"
---
#  <a name="the-azure-web-pubsub-supported-protobuf-websocket-subprotocol"></a>Le sous-protocole protobuf WebSocket pris en charge par Azure Web PubSub
     
Ce document décrit le sous-protocole `protobuf.webpubsub.azure.v1`.

Lorsqu’un client utilise ce sous-protocole, les trames de données entrantes et sortantes sont supposées être des charges utiles de mémoires tampon de protocole (protobuf).

## <a name="overview"></a>Vue d’ensemble

Le sous-protocole `protobuf.webpubsub.azure.v1` permet aux clients de publier/s’abonner (PubSub) directement au lieu d’effectuer un aller-retour au serveur en amont. La connexion WebSocket avec le sous-protocole `protobuf.webpubsub.azure.v1` est appelée un client WebSocket PubSub.

Par exemple, dans JavaScript, vous pouvez créer un client WebSocket PubSub avec le sous-protocole protobuf à l’aide des éléments suivants :

```js
// PubSub WebSocket client
var pubsub = new WebSocket('wss://test.webpubsub.azure.com/client/hubs/hub1', 'protobuf.webpubsub.azure.v1');
```

Pour un client WebSocket simple, le serveur a le rôle *nécessaire* de gestion des événements des clients. Une connexion WebSocket simple déclenche toujours un événement `message` lors de l’envoi de messages, et il s’appuie toujours sur le côté serveur pour traiter les messages et effectuer d’autres opérations. Avec l’aide du sous-protocole `protobuf.webpubsub.azure.v1`, un client autorisé peut rejoindre un groupe avec des [requêtes d’adhésion](#join-groups) et publier des messages directement dans un groupe avec des [requêtes de publication](#publish-messages). Le client peut également router des messages vers différents gestionnaires d’événements en amont avec des [requêtes d’événement](#send-custom-events) pour personnaliser l’*événement* auquel le message appartient.

> [!NOTE]
> Actuellement, le service Web PubSub ne prend en charge que [proto3](https://developers.google.com/protocol-buffers/docs/proto3).

## <a name="permissions"></a>Autorisations

Dans la description précédente du client WebSocket PubSub, vous avez peut-être remarqué qu’un client ne peut publier vers d’autres clients que lorsqu’il est *autorisé* à le faire. Les rôles du client déterminent ses autorisations *initiales*, comme indiqué dans le tableau suivant :

| Rôle | Autorisation |
|---|---|
| Non spécifié | Le client peut envoyer des requêtes d’événements. |
| `webpubsub.joinLeaveGroup` | Le client peut rejoindre ou quitter n’importe quel groupe. |
| `webpubsub.sendToGroup` | Le client peut publier des messages dans n’importe quel groupe. |
| `webpubsub.joinLeaveGroup.<group>` | Le client peut rejoindre ou quitter le groupe `<group>`. |
| `webpubsub.sendToGroup.<group>` | Le client peut publier des messages dans le groupe `<group>`. |
| | |

Le côté serveur peut également octroyer ou révoquer les autorisations d’un client de manière dynamique via des API REST ou des kits de développement logiciel (SDK) serveur.

## <a name="requests"></a>Demandes

Tous les messages de requête adhèrent au format protobuf suivant :

```protobuf
syntax = "proto3";

import "google/protobuf/any.proto";

message UpstreamMessage {
    oneof message {
        SendToGroupMessage send_to_group_message = 1;
        EventMessage event_message = 5;
        JoinGroupMessage join_group_message = 6;
        LeaveGroupMessage leave_group_message = 7;
    }

    message SendToGroupMessage {
        string group = 1;
        optional uint64 ack_id = 2;
        MessageData data = 3;
    }

    message EventMessage {
        string event = 1;
        MessageData data = 2;
        optional uint64 ack_id = 3;
    }
    
    message JoinGroupMessage {
        string group = 1;
        optional uint64 ack_id = 2;
    }

    message LeaveGroupMessage {
        string group = 1;
        optional uint64 ack_id = 2;
    }
}

message MessageData {
    oneof data {
        string text_data = 1;
        bytes binary_data = 2;
        google.protobuf.Any protobuf_data = 3;
    }
}
```

### <a name="join-groups"></a>Rejoindre des groupes

Format:

Définissez `join_group_message.group` sur le nom du groupe.

* `ackId` est l’identité de chaque demande et doit être unique. Le service envoie un [message de réponse Ack](#ack-response) pour notifier le résultat du processus de la demande. Pour plus d’informations, consultez [AckId et réponse Ack](./concept-client-protocols.md#ackid-and-ack-response).

### <a name="leave-groups"></a>Quitter des groupes

Format:

Définissez `leave_group_message.group` sur le nom du groupe.

* `ackId` est l’identité de chaque demande et doit être unique. Le service envoie un [message de réponse Ack](#ack-response) pour notifier le résultat du processus de la demande. Pour plus d’informations, consultez [AckId et réponse Ack](./concept-client-protocols.md#ackid-and-ack-response).

### <a name="publish-messages"></a>Publier des messages

Format:

* `ackId` est l’identité de chaque demande et doit être unique. Le service envoie un [message de réponse Ack](#ack-response) pour notifier le résultat du processus de la demande. Pour plus d’informations, consultez [AckId et réponse Ack](./concept-client-protocols.md#ackid-and-ack-response).

Il existe un `dataType` implicite, qui peut être `protobuf`, `text` ou `binary`, en fonction des `data` de `MessageData` que vous définissez. Les clients récepteurs peuvent utiliser `dataType` pour traiter correctement le contenu.

* `protobuf` : si vous définissez `send_to_group_message.data.protobuf_data`, la valeur implicite de `dataType` est `protobuf`. `protobuf_data` peut être [Tout](https://developers.google.com/protocol-buffers/docs/proto3#any) type de message. Tous les autres clients reçoivent un binaire encodé en protobuf, qui peut être désérialisé par le kit de développement logiciel (SDK) protobuf. Les clients qui ne prennent en charge que le contenu texte (par exemple, `json.webpubsub.azure.v1`) reçoivent un binaire encodé en Base64.

* `text` : si vous définissez `send_to_group_message.data.text_data`, la valeur implicite de `dataType` est `text`. `text_data` doit être une chaîne. Tous les clients avec d’autres protocoles reçoivent une chaîne encodée en UTF-8.

* `binary` : si vous définissez `send_to_group_message.data.binary_data`, la valeur implicite de `dataType` est `binary`. `binary_data` doit être un tableau d’octets. Tous les clients avec d’autres protocoles reçoivent un binaire brut sans encodage protobuf. Les clients qui ne prennent en charge que le contenu texte (par exemple, `json.webpubsub.azure.v1`) reçoivent un binaire encodé en Base64.

#### <a name="case-1-publish-text-data"></a>Cas 1 : publier des données texte

Définissez `send_to_group_message.group` sur `group`, et `send_to_group_message.data.text_data` sur `"text data"`.

* Le client de sous-protocole protobuf du groupe `group` reçoit la trame binaire et peut utiliser [DownstreamMessage](#responses) pour la désérialiser.

* Le client de sous-protocole JSON du groupe `group` reçoit :

    ```json
    {
        "type": "message",
        "from": "group",
        "group": "group",
        "dataType" : "text",
        "data" : "text data"
    }
    ```

* Le client brut du groupe `group` reçoit une chaîne `text data`.

#### <a name="case-2-publish-protobuf-data"></a>Cas 2 : publier des données protobuf

Supposons que vous avez un message client :

```
message MyMessage {
    int32 value = 1;
}
```

Définissez `send_to_group_message.group` sur `group`, et `send_to_group_message.data.protobuf_data` sur `Any.pack(MyMessage)` avec `value = 1`.

* Le client de sous-protocole protobuf du groupe `group` reçoit la trame binaire et peut utiliser [DownstreamMessage](#responses) pour la désérialiser.

* Le client de sous-protocole du groupe `group` reçoit :

    ```json
    {
        "type": "message",
        "from": "group",
        "group": "G",
        "dataType" : "protobuf",
        "data" : "Ci90eXBlLmdvb2dsZWFwaXMuY29tL2F6dXJlLndlYnB1YnN1Yi5UZXN0TWVzc2FnZRICCAE=" // Base64-encoded bytes
    }
    ```

    > [!NOTE]
    > Les données correspondent à un binaire protobuf désérialisable, encodé en Base64. 

Vous pouvez utiliser la définition protobuf suivante et la désérialiser avec `Any.unpack()` :

```protobuf
syntax = "proto3";

message MyMessage {
    int32 value = 1;
}
```

* Le client brut du groupe `group` reçoit la trame binaire :

    ```
    # Show in hexadecimal
    0A 2F 74 79 70 65 2E 67 6F 6F 67 6C 65 61 70 69 73 2E 63 6F 6D 2F 61 7A 75 72 65 2E 77 65 62 70 75 62 73 75 62 2E 54 65 73 74 4D 65 73 73 61 67 65 12 02 08 01
    ```

#### <a name="case-3-publish-binary-data"></a>Cas 3 : publier des données binaires

Définissez `send_to_group_message.group` sur `group`, et `send_to_group_message.data.binary_data` sur `[1, 2, 3]`.

* Le client de sous-protocole protobuf du groupe `group` reçoit la trame binaire et peut utiliser [DownstreamMessage](#responses) pour la désérialiser.

* Le client de sous-protocole JSON du groupe `group` reçoit :

    ```json
    {
        "type": "message",
        "from": "group",
        "group": "group",
        "dataType" : "binary",
        "data" : "AQID", // Base64-encoded [1,2,3]
    }
    ```

    Étant donné que le client de sous-protocole JSON ne prend en charge que la messagerie texte, le binaire est toujours encodé en Base64.

* Le client brut du groupe `group` reçoit les données binaires dans la trame binaire :

    ```
    # Show in hexadecimal
    01 02 03
    ```

### <a name="send-custom-events"></a>Envoyer des événements personnalisés

Il existe un `dataType` implicite, qui peut être `protobuf`, `text` ou `binary`, en fonction du `dataType` que vous définissez. Les clients récepteurs peuvent utiliser `dataType` pour traiter correctement le contenu.

* `protobuf` : si vous définissez `event_message.data.protobuf_data`, a valeur implicite de `dataType` est `protobuf`. `protobuf_data` peut être n’importe quel type protobuf pris en charge. Le gestionnaire d’événements reçoit le binaire encodé en protobuf, qui peut être désérialisé par n’importe quel kit de développement logiciel (SDK) protobuf.

* `text` : si vous définissez `event_message.data.text_data`, la valeur implicite est `text`. `text_data` doit être une chaîne. Le gestionnaire d’événements reçoit une chaîne encodée en UTF-8 ;

* `binary` : si vous définissez `event_message.data.binary_data`, la valeur implicite est `binary`. `binary_data` doit être un tableau d’octets. Le gestionnaire d’événements reçoit la trame binaire brute.

#### <a name="case-1-send-an-event-with-text-data"></a>Cas 1 : envoyer un événement avec des données texte

Définissez `event_message.data.text_data` sur `"text data"`.

Le gestionnaire d’événements en amont reçoit une demande semblable à la suivante. Notez que `Content-Type` pour la requête HTTP CloudEvents est `text/plain`, où `dataType`=`text`.

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

#### <a name="case-2-send-an-event-with-protobuf-data"></a>Cas 2 : envoyer un événement avec des données protobuf

Supposons que vous avez reçu le message de client suivant :

```
message MyMessage {
    int32 value = 1;
}
```

Définissez `event_message.data.protobuf_data` sur `any.pack(MyMessage)` avec `value = 1`

Le gestionnaire d’événements en amont reçoit une demande semblable à la suivante. Notez que la valeur de `Content-Type` pour la requête HTTP CloudEvents est `application/x-protobuf`, où `dataType`=`protobuf`.

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

// Just show in hexadecimal; read it as binary
0A 2F 74 79 70 65 2E 67 6F 6F 67 6C 65 61 70 69 73 2E 63 6F 6D 2F 61 7A 75 72 65 2E 77 65 62 70 75 62 73 75 62 2E 54 65 73 74 4D 65 73 73 61 67 65 12 02 08 01
```

Les données correspondent à un binaire protobuf valide. Vous pouvez utiliser les paramètres `proto` et `any.unpack()` suivants pour le désérialiser :

```protobuf
syntax = "proto3";

message MyMessage {
    int32 value = 1;
}
```

#### <a name="case-3-send-an-event-with-binary-data"></a>Cas 3 : envoyer un événement avec des données binaires

Définissez `send_to_group_message.binary_data` sur `[1, 2, 3]`.

Le gestionnaire d’événements en amont reçoit une demande semblable à la suivante. Pour `dataType`=`binary`, la valeur de `Content-Type` pour la requête HTTP CloudEvents est `application/octet-stream`. 

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

// Just show in hexadecimal; you need to read it as binary
01 02 03 
```

La trame WebSocket peut être au format `text` pour les trames de message texte ou de binaires encodés en UTF-8 pour les trames de message `binary`.

Le service refuse le client si le message ne correspond pas au format prescrit.

## <a name="responses"></a>Réponses

Tous les messages de réponse adhèrent au format protobuf suivant :

```protobuf
message DownstreamMessage {
    oneof message {
        AckMessage ack_message = 1;
        DataMessage data_message = 2;
        SystemMessage system_message = 3;
    }
    
    message AckMessage {
        uint64 ack_id = 1;
        bool success = 2;
        optional ErrorMessage error = 3;
    
        message ErrorMessage {
            string name = 1;
            string message = 2;
        }
    }

    message DataMessage {
        string from = 1;
        optional string group = 2;
        MessageData data = 3;
    }

    message SystemMessage {
        oneof message {
            ConnectedMessage connected_message = 1;
            DisconnectedMessage disconnected_message = 2;
        }
    
        message ConnectedMessage {
            string connection_id = 1;
            string user_id = 2;
        }

        message DisconnectedMessage {
            string reason = 2;
        }
    }
}
```

Les messages reçus par le client peuvent être de trois types : `ack`, `message` ou `system`. 

### <a name="ack-response"></a>Réponse ACK

Si la requête contient `ackId`, le service renvoie une réponse ACK pour cette requête. L’implémentation du client doit gérer ce mécanisme d’acquittement (ack), y compris :
* Attendre la réponse ACK pour une opération `async``await`. 
* Vérification du délai d’attente lorsque la réponse ACK n’est pas reçue au cours d’une certaine période.

L’implémentation du client doit toujours d’abord vérifier si l’état `success` est `true` ou `false`. Lorsque l’état `success` est `false`, le client peut lire les détails de l’erreur dans la propriété `error`.

### <a name="message-response"></a>Réponse de message

Les clients peuvent recevoir des messages publiés à partir d’un groupe auquel le client s’est joint. Autrement, ils peuvent recevoir des messages du rôle gestion de serveur lorsque le serveur envoie des messages à un client spécifique ou à un utilisateur spécifique.

Vous obtiendrez toujours un message `DownstreamMessage.DataMessage` dans les scénarios suivants :

- Lorsque le message provient d’un groupe, la valeur de `from` est `group`. Lorsque le message provient du serveur, la valeur de `from` est `server`.
- Lorsque le message provient d’un groupe, la valeur de `group` est le nom du groupe.

Le paramètre `dataType` de l’expéditeur entraînera l’envoi de l’un des messages suivants : 
* Si la valeur de `dataType` est `text`, utilisez `message_response_message.data.text_data`. 
* Si la valeur de `dataType` est `binary`, utilisez `message_response_message.data.binary_data`. 
* Si la valeur de `dataType` est `protobuf`, utilisez `message_response_message.data.protobuf_data`. 
* Si la valeur de `dataType` est `json`, utilisez `message_response_message.data.text_data`, et le contenu est une chaîne JSON sérialisée.

### <a name="system-response"></a>Réponse du système

Le service Web PubSub peut également envoyer au client des réponses liées au système. 

#### <a name="connected"></a>Connecté

Lorsque le client se connecte au service, vous recevez un message `DownstreamMessage.SystemMessage.ConnectedMessage`.

#### <a name="disconnected"></a>Déconnecté

Lorsque le serveur clôt la connexion ou que le service refuse le client, vous recevez un message `DownstreamMessage.SystemMessage.DisconnectedMessage`.

## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [next step](includes/include-next-step.md)]
