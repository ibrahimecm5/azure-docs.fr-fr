---
title: Éléments internes du service Azure Web PubSub
description: Découvrez les éléments internes Azure Web PubSub Service, l’architecture, les connexions et la façon dont les données sont transmises.
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 11/08/2021
ms.openlocfilehash: ec1a0bfcc883e9434c8c80264c574c3630e395b0
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2021
ms.locfileid: "131995083"
---
#  <a name="azure-web-pubsub-service-internals"></a>Éléments internes du service Azure Web PubSub

Azure Web PubSub Service offre un moyen simple de publier des messages ou de s’abonner à des messages à l’aide de simples connexions [WebSocket](https://tools.ietf.org/html/rfc6455).

- Le client peut être écrit dans n’importe quel langage prenant en charge WebSocket.
- Les messages texte et binaires sont pris en charge dans une même connexion.
- Protocole simple permettant aux clients de publier en direct des messages client-client.
- Le service gère les connexions WebSocket pour vous.

## <a name="terms"></a>Termes
* **Service** : Azure Web PubSub Service.

[!INCLUDE [Terms](includes/terms.md)]

* **Connexion cliente** et **ID de connexion** : un client se connecte au point de terminaison `/client`. Lorsqu’il est connecté, un `connectionId` unique est généré par le service en tant qu’identité unique de la connexion cliente. Les utilisateurs peuvent ensuite gérer la connexion cliente à l’aide de ce `connectionId`. Des informations détaillées sont disponibles dans la section [Protocole client](#client_protocol).

* **Événements clients** : les événements sont créés pendant le cycle de vie d’une connexion cliente. Par exemple, une connexion cliente WebSocket simple crée un événement `connect` lorsqu’il tente de se connecter au service, un événement `connected` quand il s’est correctement connecté au service, un événement `message` lorsqu’il envoie des messages au service et un événement `disconnected` lorsqu’il se déconnecte du service. Des informations détaillées sur les *événements clients* sont présentées dans la section [Protocole client](#client_protocol).

* **Gestionnaire d’événements** : le gestionnaire d’événements contient la logique permettant de gérer les événements du client. Inscrivez et configurez les gestionnaires d’événements dans le service via le portail ou Azure CLI au préalable. Des informations détaillées sont disponibles dans la section [Gestionnaire d’événements](#event_handler). L’emplacement d’hébergement de la logique du gestionnaire d’événements est côté serveur.

* **Serveur** : le serveur peut gérer les événements du client, gérer les connexions clientes et publier des messages dans des groupes. Le serveur, en comparaison avec le client, est digne de confiance. Des informations détaillées sur le **serveur** sont présentées dans la section [Protocole serveur](#server_protocol).

<a name="workflow"></a>

## <a name="workflow"></a>Workflow

![Diagramme montrant le workflow Web PubSub Service.](./media/concept-service-internals/workflow.png)

Comme illustré par le graphique de workflow ci-dessus :
1. Un *client* se connecte au point de terminaison `/client` du service à l’aide du transport WebSocket. Le service transfère chaque trame WebSocket vers le serveur en amont configuré. La connexion WebSocket peut se connecter à n’importe quel sous-protocole personnalisé à gérer par le serveur ou se connecter au sous-protocole pris en charge par le service, ce qui permet aux clients `json.webpubsub.azure.v1` d’effectuer des actions Pub/Sub directement. Des informations détaillées sont disponibles dans la section [Protocole client](#client_protocol).
2. Le service appelle le serveur à l'aide du **protocole HTTP CloudEvents** sur différents événements clients. [**CloudEvents**](https://github.com/cloudevents/spec/blob/v1.0.1/http-protocol-binding.md) est une définition standardisée et indépendante du protocole de la description de la structure et des métadonnées des événements hébergés par la Cloud Native Computing Foundation (CNCF). Des informations détaillées sont disponibles dans la section [Protocole serveur](#server_protocol).
3. Le serveur peut appeler le service à l’aide de l’API REST pour envoyer des messages aux clients ou pour gérer les clients connectés. Des informations détaillées sont disponibles dans la section [Protocole serveur](#server_protocol).

<a name="client_protocol"></a>

## <a name="client-protocol"></a>Protocole client

Une connexion cliente se connecte au point de terminaison `/client` du service à l'aide du [protocole WebSocket](https://tools.ietf.org/html/rfc6455). Le protocole WebSocket fournit des canaux de communication duplex intégral sur une connexion TCP unique et a été normalisé par l’IETF sous la dénomination RFC 6455 en 2011. La plupart des langages ont une prise en charge native pour démarrer les connexions WebSocket. 

Notre service prend en charge deux types de clients :
- [Client WebSocket simple](#simple_client)
- [Client WebSocket PubSub](#pubsub_client)

<a name="simple_client"></a>

### <a name="the-simple-websocket-client"></a>Client WebSocket simple
Un client WebSocket simple, comme son nom l’indique, est une connexion WebSocket simple. Il peut également avoir son sous-protocole personnalisé. 

Par exemple, dans JS, un client WebSocket simple peut être créé à l’aide des éléments suivants :
```js
// simple WebSocket client1
var client1 = new WebSocket('wss://test.webpubsub.azure.com/client/hubs/hub1');

// simple WebSocket client2 with some custom subprotocol
var client2 = new WebSocket('wss://test.webpubsub.azure.com/client/hubs/hub1', 'custom.subprotocol')

```

Un client WebSocket simple suit une architecture client<->serveur, comme le montre le diagramme de séquence ci-dessous : ![Diagramme montrant la séquence d’une connexion cliente.](./media/concept-service-internals/simple-client-sequence.png)


1. Lorsque le client démarre le protocole de transfert WebSocket, le service tente d’appeler le gestionnaire d’événements `connect` (le serveur) pour le protocole de transfert WebSocket. Les utilisateurs peuvent utiliser ce gestionnaire pour gérer le protocole de transfert WebSocket, déterminer le sous-protocole à utiliser, authentifier le client et joindre le client à certains groupes.
2. Lorsque le client est correctement connecté, le service appelle un gestionnaire d’événements `connected`. Il fonctionne comme une notification et ne bloque pas l’envoi de messages par le client. Les utilisateurs peuvent utiliser ce gestionnaire pour effectuer un stockage de données et peuvent répondre avec des messages envoyés au client.
2. Lorsque le client envoie des messages, les services déclenchent l’événement `message` au gestionnaire d’événements (le serveur) pour gérer les messages envoyés. Cet événement est un événement général qui contient les messages envoyés dans une trame WebSocket. L’utilisateur doit distribuer les messages lui-même à l’intérieur de ce gestionnaire d’événements.
3. Lorsque le client se déconnecte, le service tente de déclencher l’événement `disconnected` vers le gestionnaire d’événements (le serveur) une fois qu’il a détecté la déconnexion.

Les événements sont répartis en deux catégories :
* Les événements synchrones (bloquants) Des événements synchrones bloquent le workflow du client. Lorsqu’un déclencheur d’événement de ce type échoue, le service abandonne la connexion cliente.
    * `connect`
    * `message`
* Les événements asynchrones (non bloquants) Des événements asynchrones ne bloquent pas le workflow client. Ils agissent en tant que notification au gestionnaire d’événements en amont. Lorsqu’un déclencheur d’événement de ce type échoue, le service journalise le détail de l’erreur.
    * `connected`
    * `disconnected`
    
#### <a name="scenarios"></a>Scénarios :
Une telle connexion peut être utilisée dans une architecture client-serveur classique, où le client envoie des messages au serveur et où le serveur gère les messages entrants à l’aide de [gestionnaires d’événements](#event_handler). Elle peut également être utilisée lorsque les clients appliquent des [sous-protocoles](https://www.iana.org/assignments/websocket/websocket.xml) existants dans leur logique d’application.

<a name="pubsub_client"></a>

### <a name="the-pubsub-websocket-client"></a>Le client WebSocket PubSub
Le service prend également en charge un sous-protocole spécifique appelé `json.webpubsub.azure.v1`, qui permet aux clients de publier/de s’abonner directement à la place d’un aller-retour au serveur en amont. Nous appelons la connexion WebSocket avec le sous-protocole `json.webpubsub.azure.v1` un client WebSocket PubSub.

Par exemple, dans JS, un client WebSocket PubSub peut être créé à l’aide des éléments suivants :
```js
// PubSub WebSocket client
var pubsub = new WebSocket('wss://test.webpubsub.azure.com/client/hubs/hub1', 'json.webpubsub.azure.v1');
```

Le client WebSocket PubSub peut :
* Rejoindre un groupe, par exemple :
    ```json
    {
        "type": "joinGroup",
        "group": "<group_name>"
    }
    ```
* Quitter un groupe, par exemple :
    ```json
    {
        "type": "leaveGroup",
        "group": "<group_name>"
    }
    ```
* Publier des messages dans un groupe, par exemple :
    ```json
    {
        "type": "sendToGroup",
        "group": "<group_name>",
        "data": { "hello": "world" }
    }
    ```
* Envoyer des événements personnalisés au serveur en amont, par exemple :

    ```json
    {
        "type": "event",
        "event": "<event_name>",
        "data": { "hello": "world" }
    }
    ```

Le [Sous-protocole WebSocket PubSub](./reference-json-webpubsub-subprotocol.md) contient les détails du sous-protocole `json.webpubsub.azure.v1`.

Vous avez peut-être remarqué que pour un [client WebSocket simple](#simple_client), le *serveur* est un rôle indispensable pour gérer les événements des clients. Une connexion WebSocket simple déclenche toujours un événement `message` lors de l’envoi de messages et s’appuie toujours sur le côté serveur pour traiter les messages et effectuer d’autres opérations. Avec l’aide du sous-protocole `json.webpubsub.azure.v1`, un client autorisé peut rejoindre un groupe et publier des messages directement dans un groupe. Il peut également router des messages vers différents gestionnaires d'événements en amont en personnalisant l’*événement* auquel appartient le message. 

#### <a name="scenarios"></a>Scénarios :
Ces clients peuvent être utilisés lorsque les clients souhaitent communiquer entre eux. Les messages sont envoyés à partir du service `client1` et le service remet le message directement à `client2` si les clients sont autorisés à le faire.

Client 1 :

```js
var client1 = new WebSocket("wss://xxx.webpubsub.azure.com/client/hubs/hub1", "json.webpubsub.azure.v1");
client1.onmessage = e => {
    if (e.data) {
        var message = JSON.parse(e.data);
        if (message.type === "message" 
        && message.group === "Group1"){
            // Only print messages from Group1
            console.log(message.data);
        }
    }
};

client1.onopen = e => {
    client1.send(JSON.stringify({
        type: "joinGroup",
        group: "Group1"
    }));
};
```

Client 2 :

```js
var client2 = new WebSocket("wss://xxx.webpubsub.azure.com/client/hubs/hub1", "json.webpubsub.azure.v1");
client2.onopen = e => {
    client2.send(JSON.stringify({
        type: "sendToGroup",
        group: "Group1",
        data: "Hello Client1"
    });
};
```

Comme le montre l’exemple ci-dessus, `client2` envoie des données directement à `client1` en publiant des messages dans le `Group1` dans lesquel se trouve `client1`.

<a name="client_message_limit"></a>

### <a name="client-message-limit"></a>Limite du message client
La taille de message maximale autorisée pour une trame WebSocket est de **1 Mo**.

<a name="client_auth"></a>

### <a name="client-auth"></a>Authentification du client

#### <a name="auth-workflow"></a>Workflow d’authentification

Le client utilise un jeton JWT signé pour se connecter au service. Le serveur en amont peut également rejeter le client lorsqu’il s’agit d’un gestionnaire d’événements `connect` du client entrant. Le gestionnaire d’événements authentifie le client en spécifiant le `userId` et le ou les `role`s du client dans la réponse du webhook, ou décline le client avec l’erreur 401. La section [Gestionnaire d’événements](#event_handler) décrit cela en détails.

Le graphique ci-dessous décrit le workflow :

![Diagramme montrant le workflow d’authentification du client.](./media/concept-service-internals/client-connect-workflow.png)

Comme vous l’avez peut-être remarqué lorsque nous avons décrit les clients WebSocket PubSub, un client ne peut publier vers d’autres clients que s’il y est *autorisé*. Le `role` du client détermine les autorisations *initiales* dont dispose le client :

| Rôle | Autorisation |
|---|---|
| Non spécifié | Le client peut envoyer des événements.
| `webpubsub.joinLeaveGroup` | Le client peut rejoindre/quitter n’importe quel groupe.
| `webpubsub.sendToGroup` | Le client peut publier des messages dans n’importe quel groupe.
| `webpubsub.joinLeaveGroup.<group>` | Le client peut rejoindre/quitter le groupe `<group>`.
| `webpubsub.sendToGroup.<group>` | Le client peut publier des messages dans le groupe `<group>`.

Le côté serveur peut également accorder ou révoquer des autorisations de manière dynamique au client via le [protocole serveur](#connection_manager), comme illustré dans une section ultérieure.

<a name="server_protocol"></a>

## <a name="server-protocol"></a>Protocole serveur

Le protocole serveur fournit les fonctionnalités permettant au serveur de gérer les connexions clientes et les groupes.

En général, le protocole serveur contient deux rôles :
1. [Gestionnaire d’événements](#event_handler)
2. [Connection manager](#connection_manager)

<a name="event_handler"></a>

### <a name="event-handler"></a>Gestionnaire d'événements
Le gestionnaire d’événements gère les événements clients entrants. Les gestionnaires d’événements sont enregistrés et configurés dans le service via le portail ou Azure CLI au préalable, de sorte que lorsqu’un événement client est déclenché, le service peut déterminer si l’événement est supposé être géré ou non. Pendant la période de la préversion publique, nous utilisons le mode `PUSH` pour appeler le gestionnaire d’événements : le gestionnaire d’événements côté serveur expose un point de terminaison accessible publiquement au service à appeler lorsque l’événement est déclenché. Il agit comme un **webhook**. 

Le service fournit des événements client au webhook en amont à l’aide du [protocole HTTP CloudEvents](https://github.com/cloudevents/spec/blob/v1.0.1/http-protocol-binding.md).

Pour chaque événement, il formule une requête HTTP POST au service en amont enregistré et attend une réponse HTTP. 

L’envoi des données du service au serveur est toujours au format `binary` CloudEvents.

![Diagramme montrant le mode Push des événements du service Web PubSub.](./media/concept-service-internals/event-push.png)

#### <a name="upstream-and-validation"></a>Serveur en amont et validation

Les gestionnaires d’événements doivent être enregistrés et configurés dans le service via le portail ou Azure CLI au préalable, de sorte que lorsqu’un événement client est déclenché, le service peut déterminer si l’événement est supposé être géré ou non. Pendant la période de la préversion publique, nous utilisons le mode `PUSH` pour appeler le gestionnaire d’événements : le gestionnaire d’événements côté serveur expose un point de terminaison accessible publiquement au service à appeler lorsque l’événement est déclenché. Il agit comme un **webhook** en **amont**. 

Lors de la configuration du point de terminaison webhook, l’URL peut utiliser le paramètre `{event}` pour définir un modèle d’URL. Le service calcule de manière dynamique la valeur de l’URL du webhook lorsque la demande du client arrive. Par exemple, lorsqu’une demande `/client/hubs/chat` arrive, avec un modèle d'URL de gestionnaire d’événements configuré `http://host.com/api/{event}` pour le hub `chat`, lorsque le client se connecte, il publie d’abord vers cette URL : `http://host.com/api/connect`. Cela peut être utile lorsqu’un client WebSocket PubSub envoie des événements personnalisés, et le gestionnaire d’événements aide à distribuer différents événements vers différents serveurs en amont. Notez que le paramètre `{event}` n’est pas autorisé dans le nom de domaine de l’URL.

Quand vous configurez le gestionnaire d’événements en amont par le biais du portail Azure ou de l’interface CLI, le service suit la [protection contre les abus CloudEvents](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection) pour valider le webhook en amont. L’en-tête de demande `WebHook-Request-Origin` est défini sur le nom de domaine de service `xxx.webpubsub.azure.com` et il s’attend à ce que la réponse ayant l’en-tête `WebHook-Allowed-Origin` contienne ce nom de domaine.

Lors de la validation, le paramètre `{event}` est résolu en `validate`. Par exemple, lorsqu’il essaie de définir l’URL sur `http://host.com/api/{event}`, le service essaie de poser des **OPTIONS** sur une demande vers `http://host.com/api/validate` et c’est seulement lorsque la réponse est valide que la configuration peut être définie avec succès.

À l’heure actuelle, [WebHook-Request-Rate](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#414-webhook-request-rate) et [WebHook-Request-Callback](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#413-webhook-request-callback) ne sont pas pris en charge.

#### <a name="authentication-between-service-and-webhook"></a>Authentification entre le service et le webhook
- Mode anonyme
- Authentification simple `code` qui est fournie par le biais de l’URL de webhook configurée.
- Authentification AAD. 
   - Ajoutez une clé secrète client dans les [inscriptions d’applications] de AAD et fournissez la [clé secrète client] à Azure Web PubSub via le portail/l’interface CLI.
   - Fournissez l’[identité](../app-service/overview-managed-identity.md?tabs=dotnet) à Azure Web PubSub via le portail/l’interface CLI.

<a name="connection_manager"></a>

### <a name="connection-manager"></a>Gestionnaire de connexions

Le serveur est par nature un utilisateur autorisé. Avec l’aide du *rôle de gestionnaire d’événements*, le serveur connaît les métadonnées des clients, par exemple, `connectionId` et `userId`. Il peut donc :
   - Fermer une connexion cliente
   - Envoyer des messages à un client
   - Envoyer des messages aux clients qui appartiennent au même utilisateur
   - Ajouter un client à un groupe
   - Ajouter des clients authentifiés en tant qu’utilisateur à un groupe
   - Supprimer un client d’un groupe
   - Supprimer les clients authentifiés en tant qu’utilisateur d’un groupe
   - Publier des messages dans un groupe

Il peut également accorder ou révoquer des autorisations de publication/jointure pour un client PubSub :
   - Accorder des autorisations de jointure/publication à un groupe spécifique ou à tous les groupes
   - Révoquer des autorisations de jointure/publication pour un groupe spécifique ou pour tous les groupes
   - Vérifier si le client a l’autorisation de joindre/publier dans un groupe spécifique ou dans tous les groupes
   
Pendant la préversion publique, le service fournit des API REST pour le serveur afin de gérer les connexions :

![Diagramme montrant le workflow du gestionnaire de connexions du service Web PubSub.](./media/concept-service-internals/manager-rest.png)

Le protocole détaillé en lien avec l’API REST est défini [ici][rest].

### <a name="summary"></a>Résumé
Vous avez peut-être remarqué que le *rôle de gestionnaire d’événements* gère la communication du service au serveur tandis que le *rôle de manager* gère la communication du serveur au service. Si vous combinez les deux rôles, le workflow de données entre le service et le serveur ressemble à l’exemple ci-dessous qui utilise le protocole HTTP :

![Diagramme montrant le workflow bidirectionnel Web PubSub Service.](./media/concept-service-internals/http-service-server.png)

[rest]: /rest/api/webpubsub/

## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [next step](includes/include-next-step.md)]