---
title: 'Tutoriel : publier et s’abonner à des messages à l’aide de l’API WebSocket et du kit de développement logiciel (SDK) du service Azure Web PubSub'
description: Un tutoriel pour découvrir comment utiliser le service Azure Web PubSub et Azure Functions pour générer une application serverless.
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: tutorial
ms.date: 11/01/2021
ms.openlocfilehash: 56314c696b58f89144d171314709b5153d250a29
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132551437"
---
# <a name="tutorial-publish-and-subscribe-messages-using-websocket-api-and-azure-web-pubsub-service-sdk"></a>Tutoriel : publier et s’abonner à des messages à l’aide de l’API WebSocket et du kit de développement logiciel (SDK) du service Azure Web PubSub

Le service Azure Web PubSub vous aide à créer facilement des applications web de messagerie en temps réel avec des WebSockets et le modèle publication-abonnement. Dans ce tutoriel, vous allez apprendre à vous abonner au service à l’aide de l’API WebSocket et à publier des messages avec le kit de développement logiciel (SDK) de service.

Dans ce tutoriel, vous apprenez à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer une instance de service Azure Web PubSub
> * Générer l’URL complète pour établir la connexion WebSocket
> * S’abonner à des messages à l’aide du protocole WebSocket standard
> * Publier des messages à l’aide du kit de développement logiciel (SDK) de service

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Cette configuration nécessite la version 2.22.0 ou ultérieure d’Azure CLI. Si vous utilisez Azure Cloud Shell, la version la plus récente est déjà installée.

## <a name="create-an-azure-web-pubsub-instance"></a>Création d’une instance Azure Web PubSub

### <a name="create-a-resource-group"></a>Créer un groupe de ressources

[!INCLUDE [Create a resource group](includes/cli-rg-creation.md)]

### <a name="create-a-web-pubsub-instance"></a>Créer une instance Web PubSub

[!INCLUDE [Create a Web PubSub instance](includes/cli-awps-creation.md)]

### <a name="get-the-connectionstring-for-future-use"></a>Récupération de la chaîne de connexion pour une utilisation ultérieure

[!INCLUDE [Get the connection string](includes/cli-awps-connstr.md)]

Copiez la chaîne **ConnectionString** extraite. Elle sera utilisée comme valeur de `<connection_string>` dans la suite de ce tutoriel.

## <a name="set-up-the-project"></a>Configuration du projet
### <a name="prerequisites"></a>Prérequis

# <a name="c"></a>[C#](#tab/csharp)

* [.NET Core 2.1 ou ultérieur](https://dotnet.microsoft.com/download)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

* [Node.js 12.x (ou version ultérieure)](https://nodejs.org)

# <a name="python"></a>[Python](#tab/python)
* [Python](https://www.python.org/)

# <a name="java"></a>[Java](#tab/java)
- [Kit de développement Java (JDK)](/java/azure/jdk/), version 8 ou ultérieure
- [Apache Maven](https://maven.apache.org/download.cgi)

---

## <a name="set-up-the-subscriber"></a>Configurer l’abonné

Les clients se connectent au service Azure Web PubSub via le protocole WebSocket standard à l’aide de l’authentification [JSON Web Token (JWT)](../active-directory/develop/security-tokens.md#json-web-tokens-and-claims). Le kit de développement logiciel (SDK) du service fournit des méthodes d’assistance pour générer le jeton. Dans ce tutoriel, l’abonné génère directement le jeton à partir de *ConnectionString*. Dans les applications réelles, nous utilisons généralement une application côté serveur pour gérer le workflow d’authentification/autorisation. Essayez le tutoriel [Générer une application de conversation instantanée](./tutorial-build-chat.md) pour mieux comprendre le workflow.

# <a name="c"></a>[C#](#tab/csharp)

1. Tout d’abord, créons un nouveau dossier `subscribe` pour ce projet et installons les dépendances requises :
    * Le package [Websocket.Client](https://github.com/Marfusios/websocket-client) est un package tiers qui prend en charge la connexion WebSocket. Pour ce faire, vous pouvez utiliser n’importe quelle API/bibliothèque qui prend en charge WebSocket.
    * Le package du kit de développement logiciel (SDK) `Azure.Messaging.WebPubSub` permet de générer le jeton JWT. 

    ```bash
    mkdir subscriber
    cd subscriber
    dotnet new console
    dotnet add package Websocket.Client --version 4.3.30
    dotnet add package Azure.Messaging.WebPubSub --version 1.0.0
    ```

2. Mettre à jour le fichier `Program.cs` pour se connecter au service :

    ```csharp
    using System;
    using System.Threading.Tasks;
    
    using Azure.Messaging.WebPubSub;
    
    using Websocket.Client;
    
    namespace subscriber
    {
        class Program
        {
            static async Task Main(string[] args)
            {
                if (args.Length != 2)
                {
                    Console.WriteLine("Usage: subscriber <connectionString> <hub>");
                    return;
                }
                var connectionString = args[0];
                var hub = args[1];
    
                // Either generate the URL or fetch it from server or fetch a temp one from the portal
                var serviceClient = new WebPubSubServiceClient(connectionString, hub);
                var url = serviceClient.GetClientAccessUri();
    
                using (var client = new WebsocketClient(url))
                {
                    // Disable the auto disconnect and reconnect because the sample would like the client to stay online even no data comes in
                    client.ReconnectTimeout = null;
                    client.MessageReceived.Subscribe(msg => Console.WriteLine($"Message received: {msg}"));
                    await client.Start();
                    Console.WriteLine("Connected.");
                    Console.Read();
                }
            }
        }
    }
    
    ```

    Le code ci-dessus crée une connexion WebSocket pour se connecter à un hub dans Azure Web PubSub. Le hub est une unité logique dans Azure Web PubSub où vous pouvez publier des messages sur un groupe de clients. Les [concepts clés](./key-concepts.md) contiennent une explication détaillée sur les termes utilisés dans Azure Web PubSub.
    
    Le service Azure Web PubSub utilise l’authentification [JSON Web Token (JWT)](../active-directory/develop/security-tokens.md#json-web-tokens-and-claims). Ainsi, dans l’exemple de code nous utilisons `WebPubSubServiceClient.GetClientAccessUri()` dans le kit de développement logiciel (SDK) Web PubSub pour générer une URL vers le service qui contient l’URL complète avec un jeton d’accès valide.
    
    Une fois la connexion établie, vous recevrez des messages via la connexion WebSocket. Nous utilisons donc `client.MessageReceived.Subscribe(msg => ...));` pour écouter les messages entrants.

3. Exécutez la commande ci-dessous, en remplaçant `<connection_string>` par la chaîne **ConnectionString** extraite à l’[étape précédente](#get-the-connectionstring-for-future-use) :

    ```bash
    dotnet run "<connection_string>" "myHub1"
    ```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

1. Tout d’abord, créons un nouveau dossier `subscriber` pour ce projet et installons les dépendances requises :

    ```bash
    mkdir subscriber
    cd subscriber
    npm init -y
    npm install --save ws
    npm install --save @azure/web-pubsub

    ```
2. Utilisez ensuite l’API WebSocket pour vous connecter au service. Créez un fichier `subscribe.js` avec le code ci-dessous :

    ```javascript
    const WebSocket = require('ws');
    const { WebPubSubServiceClient } = require('@azure/web-pubsub');

    async function main() {
      const hub = "pubsub";
      let service = new WebPubSubServiceClient(process.env.WebPubSubConnectionString, hub);
      let token = await service.getClientAccessToken();
      let ws = new WebSocket(token.url);
      ws.on('open', () => console.log('connected'));
      ws.on('message', data => console.log('Message received: %s', data));
    }

    main();
    ```
    
    Le code ci-dessus crée une connexion WebSocket pour se connecter à un hub dans Azure Web PubSub. Le hub est une unité logique dans Azure Web PubSub où vous pouvez publier des messages sur un groupe de clients. Les [concepts clés](./key-concepts.md) contiennent une explication détaillée sur les termes utilisés dans Azure Web PubSub.
    
    Le service Azure Web PubSub utilise l’authentification [JSON Web Token (JWT)](../active-directory/develop/security-tokens.md#json-web-tokens-and-claims). Ainsi, dans l’exemple de code nous utilisons `WebPubSubServiceClient.getClientAccessToken()` dans le kit de développement logiciel (SDK) Web PubSub pour générer une URL vers le service qui contient l’URL complète avec un jeton d’accès valide.
    
    Une fois la connexion établie, vous recevrez des messages via la connexion WebSocket. Nous utilisons donc `WebSocket.on('message', ...)` pour écouter les messages entrants.
    
3. Exécutez la commande ci-dessous, en remplaçant `<connection_string>` par la chaîne **ConnectionString** extraite à l’[étape précédente](#get-the-connectionstring-for-future-use) :

    ```bash
    export WebPubSubConnectionString="<connection-string>"
    node subscribe
    ```

# <a name="python"></a>[Python](#tab/python)

1. Tout d’abord, créons un nouveau dossier `subscriber` pour ce projet et installons les dépendances requises :

    ```bash
    mkdir subscriber
    cd subscriber
    # Create venv
    python -m venv env
    # Activate venv
    source ./env/bin/activate

    pip install azure-messaging-webpubsubservice
    pip install websockets

    ```

2. Utilisez ensuite l’API WebSocket pour vous connecter au service. Créez un fichier `subscribe.py` avec le code ci-dessous :

    ```python
    import asyncio
    import sys
    import websockets
    
    from azure.messaging.webpubsubservice import WebPubSubServiceClient
    
    
    async def connect(url):
        async with websockets.connect(url) as ws:
            print('connected')
            while True:
                print('Received message: ' + await ws.recv())
    
    if __name__ == '__main__':
    
        if len(sys.argv) != 3:
            print('Usage: python subscribe.py <connection-string> <hub-name>')
            exit(1)
    
        connection_string = sys.argv[1]
        hub_name = sys.argv[2]
    
        service = WebPubSubServiceClient.from_connection_string(connection_string, hub=hub_name)
        token = service.get_client_access_token()
    
        try:
            asyncio.get_event_loop().run_until_complete(connect(token['url']))
        except KeyboardInterrupt:
            pass
    
    ```

    Le code ci-dessus crée une connexion WebSocket pour se connecter à un hub dans Azure Web PubSub. Le hub est une unité logique dans Azure Web PubSub où vous pouvez publier des messages sur un groupe de clients. Les [concepts clés](./key-concepts.md) contiennent une explication détaillée sur les termes utilisés dans Azure Web PubSub.
    
    Le service Azure Web PubSub utilise l’authentification [JSON Web Token (JWT)](../active-directory/develop/security-tokens.md#json-web-tokens-and-claims). Ainsi, dans l’exemple de code, nous utilisons `service.get_client_access_token()` fourni par le SDK pour générer une URL vers le service qui contient l’URL complète avec un jeton d’accès valide.
    
    Une fois la connexion établie, vous recevrez des messages via la connexion WebSocket. Nous utilisons donc `await ws.recv()` pour écouter les messages entrants.

3. Exécutez la commande ci-dessous, en remplaçant `<connection_string>` par la chaîne **ConnectionString** extraite à l’[étape précédente](#get-the-connectionstring-for-future-use) :

    ```bash
    python subscribe.py "<connection_string>" "myHub1"
    ```

# <a name="java"></a>[Java](#tab/java)
1. Pour ce tutoriel, créons un nouveau dossier `pubsub`
    ```cmd
    mkdir pubsub
    cd pubsub
    ```

1. À l’intérieur de ce dossier `pubsub`, nous allons donc utiliser Maven pour créer une application console `webpubsub-quickstart-subscriber` et basculer vers le dossier *webpubsub-quickstart-publisher* :

    ```console
    mvn archetype:generate --define interactiveMode=n --define groupId=com.webpubsub.quickstart --define artifactId=webpubsub-quickstart-subscriber --define archetypeArtifactId=maven-archetype-quickstart --define archetypeVersion=1.4
    cd webpubsub-quickstart-subscriber
    ```

2. Nous allons ajouter WebSocket et la dépendance du kit de développement logiciel (SDK) Azure Web PubSub dans le nœud `dependencies` de `pom.xml` :

    * `azure-messaging-webpubsub` : kit de développement logiciel (SDK) du service Web PubSub pour Java
    * `Java-WebSocket` : kit de développement logiciel (SDK) client WebSocket pour Java

    ```xml
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-messaging-webpubsub</artifactId>
        <version>1.0.0-beta.6</version>
    </dependency>

    <dependency>
        <groupId>org.java-websocket</groupId>
        <artifactId>Java-WebSocket</artifactId>
        <version>1.5.1</version>
    </dependency>

    ```

3. Dans Azure Web PubSub, vous pouvez vous connecter au service et vous abonner à des messages via des connexions WebSocket. WebSocket est un canal de communication en duplex intégral afin que le service puisse envoyer des messages à votre client en temps réel. Pour ce faire, vous pouvez utiliser n’importe quelle API/bibliothèque qui prend en charge WebSocket. Pour cet échantillon, nous utilisons le package [Java-WebSocket](https://github.com/TooTallNate/Java-WebSocket). Nous allons accéder au répertoire */src/main/java/com/webpubsub/quickstart*, ouvrir le fichier *App.java* dans votre éditeur et remplacer le code par ce qui suit :

    ```java
    package com.webpubsub.quickstart;
    
    import com.azure.messaging.webpubsub.*;
    import com.azure.messaging.webpubsub.models.*;
    
    import org.java_websocket.client.WebSocketClient;
    import org.java_websocket.handshake.ServerHandshake;
    
    import java.io.IOException;
    import java.net.URI;
    import java.net.URISyntaxException;
    
    /**
    * Connect to Azure Web PubSub service using WebSocket protocol
    *
    */
    public class App 
    {
        public static void main( String[] args ) throws IOException, URISyntaxException
        {
            if (args.length != 2) {
                System.out.println("Expecting 2 arguments: <connection-string> <hub-name>");
                return;
            }
    
            WebPubSubServiceClient service = new WebPubSubServiceClientBuilder()
                .connectionString(args[0])
                .hub(args[1])
                .buildClient();
    
            WebPubSubClientAccessToken token = service.getClientAccessToken(new GetClientAccessTokenOptions());
    
            WebSocketClient webSocketClient = new WebSocketClient(new URI(token.getUrl())) {
                @Override
                public void onMessage(String message) {
                    System.out.println(String.format("Message received: %s", message));
                }
    
                @Override
                public void onClose(int arg0, String arg1, boolean arg2) {
                    // TODO Auto-generated method stub
                }
    
                @Override
                public void onError(Exception arg0) {
                    // TODO Auto-generated method stub
                }
    
                @Override
                public void onOpen(ServerHandshake arg0) {
                    // TODO Auto-generated method stub
                }
    
            };
    
            webSocketClient.connect();
            System.in.read();
        }
    }

    ```

    Le code ci-dessus crée une connexion WebSocket pour se connecter à un hub dans Azure Web PubSub. Le hub est une unité logique dans Azure Web PubSub où vous pouvez publier des messages sur un groupe de clients. Les [concepts clés](./key-concepts.md) contiennent une explication détaillée sur les termes utilisés dans Azure Web PubSub.
    
    Le service Azure Web PubSub utilise l’authentification [JSON Web Token (JWT)](../active-directory/develop/security-tokens.md#json-web-tokens-and-claims). Ainsi, dans l’exemple de code nous utilisons `WebPubSubServiceClient.getClientAccessToken(new GetClientAccessTokenOptions())` dans le kit de développement logiciel (SDK) Web PubSub pour générer une URL vers le service qui contient l’URL complète avec un jeton d’accès valide.
    
    Une fois la connexion établie, vous recevrez des messages via la connexion WebSocket. Nous utilisons donc `onMessage(String message)` pour écouter les messages entrants.

4. Accédez au répertoire contenant le fichier *pom.xml* et exécutez l’application avec le code ci-dessous, en remplaçant `<connection_string>` par la **ConnectionString** extraite à l’[étape suivante](#get-the-connectionstring-for-future-use) :

    ```console
    mvn compile & mvn package & mvn exec:java -Dexec.mainClass="com.webpubsub.quickstart.App" -Dexec.cleanupDaemonThreads=false -Dexec.args="'<connection_string>' 'myHub1'"
    ```

---

## <a name="publish-messages-using-service-sdk"></a>Publier des messages à l’aide du kit de développement logiciel (SDK) de service

Nous allons maintenant utiliser le kit de développement logiciel (SDK) Azure Web PubSub pour publier un message sur le client connecté.

# <a name="c"></a>[C#](#tab/csharp)

1. Tout d’abord, créons un nouveau dossier `publisher` pour ce projet et installons les dépendances requises :

    ```bash
    mkdir publisher
    cd publisher
    dotnet new console
    dotnet add package Azure.Messaging.WebPubSub
    ```

2. Mettons à jour le fichier `Program.cs` de façon à utiliser la classe `WebPubSubServiceClient` et à envoyer des messages aux clients.

    ```csharp
    using System;
    using System.Threading.Tasks;
    using Azure.Messaging.WebPubSub;
    
    namespace publisher
    {
        class Program
        {
            static async Task Main(string[] args)
            {
                if (args.Length != 3) {
                    Console.WriteLine("Usage: publisher <connectionString> <hub> <message>");
                    return;
                }
                var connectionString = args[0];
                var hub = args[1];
                var message = args[2];
                
                // Either generate the token or fetch it from server or fetch a temp one from the portal
                var serviceClient = new WebPubSubServiceClient(connectionString, hub);
                await serviceClient.SendToAllAsync(message);
            }
        }
    }
    
    ```

    L’appel `SendToAllAsync()` envoie simplement un message à tous les clients connectés dans le hub.

3. Exécutez la commande ci-dessous, en remplaçant `<connection_string>` par la chaîne **ConnectionString** extraite à l’[étape précédente](#get-the-connectionstring-for-future-use) :

    ```bash
    dotnet run "<connection_string>" "myHub1" "Hello World"
    ```

4. Vous pouvez voir que l’abonné précédent a reçu le message ci-dessous :
   
    ```
    Message received: Hello World
    ```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

1. Tout d’abord, créons un nouveau dossier `publisher` pour ce projet et installons les dépendances requises :

    ```bash
    mkdir publisher
    cd publisher
    npm init -y
    npm install --save @azure/web-pubsub

    ```
2. Nous allons maintenant utiliser le kit de développement logiciel (SDK) Azure Web PubSub pour publier un message dans le service. Créez un fichier `publish.js` avec le code ci-dessous :

    ```javascript
    const { WebPubSubServiceClient } = require('@azure/web-pubsub');

    const hub = "pubsub";
    let service = new WebPubSubServiceClient(process.env.WebPubSubConnectionString, hub);

    // by default it uses `application/json`, specify contentType as `text/plain` if you want plain-text
    service.sendToAll(process.argv[2], { contentType: "text/plain" });
    ```

    L’appel `service.sendToAll()` envoie simplement un message à tous les clients connectés dans un hub.

3. Exécutez la commande ci-dessous, en remplaçant `<connection_string>` par la chaîne **ConnectionString** extraite à l’[étape précédente](#get-the-connectionstring-for-future-use) :

    ```bash
    export WebPubSubConnectionString="<connection-string>"
    node publish "Hello World"
    ```

4. Vous pouvez voir que l’abonné précédent a reçu le message ci-dessous :
   
    ```
    Message received: Hello World
    ```

# <a name="python"></a>[Python](#tab/python)

1. Tout d’abord, créons un nouveau dossier `publisher` pour ce projet et installons les dépendances requises :
    ```bash
    mkdir publisher
    cd publisher
    # Create venv
    python -m venv env
    # Active venv
    source ./env/bin/activate

    pip install azure-messaging-webpubsubservice

    ```
2. Nous allons maintenant utiliser le kit de développement logiciel (SDK) Azure Web PubSub pour publier un message dans le service. Créez un fichier `publish.py` avec le code ci-dessous :

    ```python
    import sys
    from azure.messaging.webpubsubservice import WebPubSubServiceClient
    
    if __name__ == '__main__':
    
        if len(sys.argv) != 4:
            print('Usage: python publish.py <connection-string> <hub-name> <message>')
            exit(1)
    
        connection_string = sys.argv[1]
        hub_name = sys.argv[2]
        message = sys.argv[3]
    
        service = WebPubSubServiceClient.from_connection_string(connection_string, hub=hub_name)
        res = service.send_to_all(message, content_type='text/plain')
        print(res)
    ```

    `send_to_all()` envoie le message à tous les clients connectés dans un hub.

3. Exécutez la commande ci-dessous, en remplaçant `<connection_string>` par la chaîne **ConnectionString** extraite à l’[étape précédente](#get-the-connectionstring-for-future-use) :

    ```bash
    python publish.py "<connection_string>" "myHub1" "Hello World"
    ```

4. Vous pouvez voir que l’abonné précédent a reçu le message ci-dessous :
   
    ```
    Received message: Hello World
    ```

# <a name="java"></a>[Java](#tab/java)

1.  Utilisons un autre terminal et revenons au dossier `pubsub` pour créer une application console de serveur de publication `webpubsub-quickstart-publisher` et basculer dans le dossier *webpubsub-quickstart-publisher* :
    ```console
    mvn archetype:generate --define interactiveMode=n --define groupId=com.webpubsub.quickstart --define artifactId=webpubsub-quickstart-publisher --define archetypeArtifactId=maven-archetype-quickstart --define archetypeVersion=1.4
    cd webpubsub-quickstart-publisher
    ```

2. Nous allons ajouter la dépendance du kit de développement logiciel (SDK) Azure Web PubSub dans le nœud `dependencies` de `pom.xml` :

    ```xml
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-messaging-webpubsub</artifactId>
        <version>1.0.0-beta.6</version>
    </dependency>
    ```

3. Nous allons maintenant utiliser le kit de développement logiciel (SDK) Azure Web PubSub pour publier un message dans le service. Nous allons accéder au répertoire */src/main/java/com/webpubsub/quickstart*, ouvrir le fichier *App.java* dans votre éditeur et remplacer le code par ce qui suit :

    ```java

    package com.webpubsub.quickstart;
    
    import com.azure.messaging.webpubsub.*;
    import com.azure.messaging.webpubsub.models.*;
    
    /**
    * Publish messages using Azure Web PubSub service SDK
    *
    */
    public class App 
    {
        public static void main( String[] args )
        {
            if (args.length != 3) {
                System.out.println("Expecting 3 arguments: <connection-string> <hub-name> <message>");
                return;
            }
    
            WebPubSubServiceClient service = new WebPubSubServiceClientBuilder()
                .connectionString(args[0])
                .hub(args[1])
                .buildClient();
            service.sendToAll(args[2], WebPubSubContentType.TEXT_PLAIN);
        }
    }

    ```

    L’appel `sendToAll()` envoie simplement un message à tous les clients connectés dans un hub.

4. Accédez au répertoire contenant le fichier *pom.xml* et exécutez le projet à l’aide de la commande ci-dessous, en remplaçant `<connection_string>` par la **ConnectionString** extraite à l’[étape suivante](#get-the-connectionstring-for-future-use) :

    ```console
    mvn compile & mvn package & mvn exec:java -Dexec.mainClass="com.webpubsub.quickstart.App" -Dexec.cleanupDaemonThreads=false -Dexec.args="'<connection_string>' 'myHub1' 'Hello World'"
    ```

5. Vous pouvez voir que l’abonné précédent a reçu le message ci-dessous :
   
    ```
    Message received: Hello World
    ```

---

## <a name="next-steps"></a>Étapes suivantes

Ce tutoriel vous donne une idée de base de la façon de se connecter au service Web PubSub et de publier des messages sur les clients connectés.

Consultez d’autres tutoriels pour mieux comprendre l’utilisation du service.

> [!div class="nextstepaction"]
> [Tutoriel : créer un salon de conversation avec Azure Web PubSub](./tutorial-build-chat.md)

> [!div class="nextstepaction"]
> [Explorer d’autres exemples Azure Web PubSub](https://aka.ms/awps/samples)
