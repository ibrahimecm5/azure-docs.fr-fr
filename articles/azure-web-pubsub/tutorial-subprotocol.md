---
title: 'Tutoriel : Publication de messages et abonnement entre clients WebSocket avec le sous-protocole dans le service Azure Web PubSub'
description: Tutoriel expliquant comment utiliser le service Azure Web PubSub et son sous-protocole WebSocket pris en charge pour la synchronisation entre les clients.
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: tutorial
ms.date: 11/01/2021
ms.openlocfilehash: 8a181f48bcdf7ec186aac1b05d3aaf135fa35c09
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132550473"
---
# <a name="tutorial-publish-and-subscribe-messages-between-websocket-clients-using-subprotocol"></a>Tutoriel : Publication de messages et abonnement entre clients WebSocket avec le sous-protocole

Dans le tutoriel [Création d’une application de conversation](./tutorial-build-chat.md), vous avez appris à utiliser les API WebSocket pour envoyer et recevoir des données avec Azure Web PubSub. Comme vous pouvez le constater, aucun protocole n’est nécessaire lorsque le client communique avec le service. Par exemple, si vous exécutez `WebSocket.send()` pour envoyer des données, le serveur les reçoit en l’état. C’est facile à utiliser, mais également limité en fonctionnalités. Par exemple, vous ne pouvez pas spécifier le nom de l’événement que vous envoyez au serveur, ni publier un message sur d’autres clients au lieu de l’envoyer au serveur. Dans ce tutoriel, vous allez découvrir comment utiliser le sous-protocole pour étendre les fonctionnalités du client.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Créer une instance de service Azure Web PubSub
> * Générer l’URL complète pour établir la connexion WebSocket
> * Publier des messages entre des clients WebSocket avec le sous-protocole

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- La version 2.22.0 (ou une version ultérieure) d’Azure CLI est requise pour cette configuration. Si vous utilisez Azure Cloud Shell, la version la plus récente est déjà installée.

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

* [ASP.NET Core 3.1 (ou version ultérieure)](/aspnet/core)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

* [Node.js 12.x (ou version ultérieure)](https://nodejs.org)

# <a name="python"></a>[Python](#tab/python)
* [Python](https://www.python.org/)

# <a name="java"></a>[Java](#tab/java)
- [Kit de développement Java (JDK)](/java/azure/jdk/), version 8 ou ultérieure
- [Apache Maven](https://maven.apache.org/download.cgi)

---

## <a name="using-a-subprotocol"></a>Utilisation d’un sous-protocole

Le client peut lancer une connexion WebSocket avec un [sous-protocole](https://datatracker.ietf.org/doc/html/rfc6455#section-1.9) spécifique. Le service Azure Web PubSub prend en charge un sous-protocole appelé `json.webpubsub.azure.v1`, qui permet aux clients de publier/s’abonner directement plutôt que d’avoir à effectuer un aller-retour au serveur en amont. Pour plus d’informations sur ce sous-protocole, consultez [Sous-protocole JSON WebSocket pris en charge par Azure Web PubSub](./reference-json-webpubsub-subprotocol.md).

> Tout autre nom de protocole utilisé est ignoré par le service et transmis au serveur dans le gestionnaire d’événements de connexion. Vous pouvez ainsi créer vos propres protocoles.

Créons maintenant une application web avec le sous-protocole `json.webpubsub.azure.v1`.

1.  Installer des dépendances

    # <a name="c"></a>[C#](#tab/csharp)
    ```bash
    mkdir logstream
    cd logstream
    dotnet new web
    dotnet add package Microsoft.Extensions.Azure
    dotnet add package Azure.Messaging.WebPubSub
    ```
    
    # <a name="javascript"></a>[JavaScript](#tab/javascript)
    
    ```bash
    mkdir logstream
    cd logstream
    npm init -y
    npm install --save express
    npm install --save ws
    npm install --save node-fetch
    npm install --save @azure/web-pubsub
    ```

    # <a name="python"></a>[Python](#tab/python)
    
    ```bash
    mkdir logstream
    cd logstream
    # Create venv
    python -m venv env
    # Active venv
    source ./env/bin/activate

    pip install azure-messaging-webpubsubservice
    ```
    
    # <a name="java"></a>[Java](#tab/java)
    
    Nous utilisons le framework web [Javalin](https://javalin.io/) pour héberger les pages web.
    
    1. Commencez par utiliser Maven pour créer une application `logstream-webserver` et basculer vers le dossier *logstream-webserver* :
    
        ```console
        mvn archetype:generate --define interactiveMode=n --define groupId=com.webpubsub.tutorial --define artifactId=logstream-webserver --define archetypeArtifactId=maven-archetype-quickstart --define archetypeVersion=1.4
        cd logstream-webserver
        ```
    
    2. Ajoutons le SDK Azure Web PubSub et la dépendance du framework web `javalin` dans le nœud `dependencies` de `pom.xml` :
    
        * `javalin` : framework web simple pour Java
        * `slf4j-simple` : enregistreur d’événements pour Java
        * `azure-messaging-webpubsub` : SDK du client de service pour l’utilisation d’Azure Web PubSub

        ```xml
        <dependency>
            <groupId>com.azure</groupId>
            <artifactId>azure-messaging-webpubsub</artifactId>
            <version>1.0.0-beta.6</version>
        </dependency>
        <!-- https://mvnrepository.com/artifact/io.javalin/javalin -->
        <dependency>
            <groupId>io.javalin</groupId>
            <artifactId>javalin</artifactId>
            <version>3.13.6</version>
        </dependency>
    
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-simple</artifactId>
            <version>1.7.30</version>
        </dependency>
        ```
    ---
    
2.  Créez le côté serveur pour héberger l’API et la page web `/negotiate`.

    # <a name="c"></a>[C#](#tab/csharp)

    Mettez à jour `Startup.cs` avec le code suivant.
    - Mettez à jour la méthode `ConfigureServices` en ajoutant le client de service et lisez la chaîne de connexion à partir de la configuration. 
    - Mettez à jour la méthode `Configure` en ajoutant `app.UseStaticFiles();` avant `app.UseRouting();` pour prendre en charge les fichiers statiques. 
    - Enfin, mettez à jour `app.UseEndpoints` pour générer le jeton d’accès client avec des demandes `/negotiate`.

    ```csharp
    using Azure.Messaging.WebPubSub;

    using Microsoft.AspNetCore.Builder;
    using Microsoft.AspNetCore.Hosting;
    using Microsoft.AspNetCore.Http;
    using Microsoft.Extensions.Azure;
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.DependencyInjection;
    using Microsoft.Extensions.Hosting;
    
    namespace logstream
    {
        public class Startup
        {
            public Startup(IConfiguration configuration)
            {
                Configuration = configuration;
            }
    
            public IConfiguration Configuration { get; }
    
            public void ConfigureServices(IServiceCollection services)
            {
                services.AddAzureClients(builder =>
                {
                    builder.AddWebPubSubServiceClient(Configuration["Azure:WebPubSub:ConnectionString"], "stream");
                });
            }
    
            public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
            {
                if (env.IsDevelopment())
                {
                    app.UseDeveloperExceptionPage();
                }
    
                app.UseStaticFiles();
    
                app.UseRouting();
    
                app.UseEndpoints(endpoints =>
                {
                    endpoints.MapGet("/negotiate", async context =>
                    {
                        var service = context.RequestServices.GetRequiredService<WebPubSubServiceClient>();
                        var response = new
                        {
                            url = service.GetClientAccessUri(roles: new string[] { "webpubsub.sendToGroup.stream", "webpubsub.joinLeaveGroup.stream" }).AbsoluteUri
                        };
                        await context.Response.WriteAsJsonAsync(response);
                    });
                });
            }
        }
    }
    
    ```
    
    # <a name="javascript"></a>[JavaScript](#tab/javascript)

    Créez un fichier `server.js` et ajoutez le code suivant :

    ```javascript
    const express = require('express');
    const { WebPubSubServiceClient } = require('@azure/web-pubsub');

    let service = new WebPubSubServiceClient(process.env.WebPubSubConnectionString, 'stream');
    const app = express();

    app.get('/negotiate', async (req, res) => {
      let token = await service.getClientAccessToken({
        roles: ['webpubsub.sendToGroup.stream', 'webpubsub.joinLeaveGroup.stream']
      });
      res.send({
        url: token.url
      });
    });

    app.use(express.static('public'));
    app.listen(8080, () => console.log('server started'));
    ```

    # <a name="python"></a>[Python](#tab/python)
    
    Créez un fichier `/negotiate` pour héberger l’API et la page web `server.py`.

    ```python
    import json
    import sys
    
    from http.server import HTTPServer, SimpleHTTPRequestHandler
    
    from azure.messaging.webpubsubservice import WebPubSubServiceClient
    
    service = WebPubSubServiceClient.from_connection_string(sys.argv[1], hub='stream')
    
    class Resquest(SimpleHTTPRequestHandler):
        def do_GET(self):
            if self.path == '/':
                self.path = 'public/index.html'
                return SimpleHTTPRequestHandler.do_GET(self)
            elif self.path == '/negotiate':
                roles = ['webpubsub.sendToGroup.stream',
                         'webpubsub.joinLeaveGroup.stream']
                token = service.get_client_access_token(roles=roles)
                self.send_response(200)
                self.send_header('Content-Type', 'application/json')
                self.end_headers()
                self.wfile.write(json.dumps({
                    'url': token['url']
                }).encode())
    
    if __name__ == '__main__':
        if len(sys.argv) != 2:
            print('Usage: python server.py <connection-string>')
            exit(1)
    
        server = HTTPServer(('localhost', 8080), Resquest)
        print('server started')
        server.serve_forever()
    
    ```
    
    # <a name="java"></a>[Java](#tab/java)

    Accédez au répertoire */src/main/java/com/webpubsub/tutorial*, ouvrez le fichier *App.java* dans votre éditeur, puis utilisez `Javalin.create` pour traiter des fichiers statiques :

    ```java
    package com.webpubsub.tutorial;
    
    import com.azure.messaging.webpubsub.WebPubSubServiceClient;
    import com.azure.messaging.webpubsub.WebPubSubServiceClientBuilder;
    import com.azure.messaging.webpubsub.models.GetClientAccessTokenOptions;
    import com.azure.messaging.webpubsub.models.WebPubSubClientAccessToken;
    
    import io.javalin.Javalin;
    
    public class App {
        public static void main(String[] args) {
            
            if (args.length != 1) {
                System.out.println("Expecting 1 arguments: <connection-string>");
                return;
            }
    
            // create the service client
            WebPubSubServiceClient service = new WebPubSubServiceClientBuilder()
                    .connectionString(args[0])
                    .hub("chat")
                    .buildClient();
    
            // start a server
            Javalin app = Javalin.create(config -> {
                config.addStaticFiles("public");
            }).start(8080);
    
            
            // Handle the negotiate request and return the token to the client
            app.get("/negotiate", ctx -> {
                GetClientAccessTokenOptions option = new GetClientAccessTokenOptions();
                option.addRole("webpubsub.sendToGroup.stream");
                option.addRole("webpubsub.joinLeaveGroup.stream");
                WebPubSubClientAccessToken token = service.getClientAccessToken(option);
    
                // return JSON string
                ctx.result("{\"url\":\"" + token.getUrl() + "\"}");
                return;
            });
        }
    }
    ```

    Selon votre configuration, vous devrez peut-être définir explicitement le niveau de langage sur Java 8. Vous pouvez le faire dans pom.xml. Ajoutez l’extrait de code suivant :
    ```xml
    <build>
        <plugins>
            <plugin>
              <artifactId>maven-compiler-plugin</artifactId>
              <version>3.8.0</version>
              <configuration>
                <source>1.8</source>
                <target>1.8</target>
              </configuration>
            </plugin>
        </plugins>
    </build>
    ```
    ---
    
3.  Créez la page web.

    # <a name="c"></a>[C#](#tab/csharp)
    Créez une page HTML comportant le contenu suivant et enregistrez-la sous `wwwroot/index.html` :
    
    # <a name="javascript"></a>[JavaScript](#tab/javascript)

    Créez une page HTML comportant le contenu suivant et enregistrez-la sous `public/index.html` :

    # <a name="python"></a>[Python](#tab/python)

    Créez une page HTML comportant le contenu suivant et enregistrez-la sous `public/index.html` :
    
    # <a name="java"></a>[Java](#tab/java)

    <a name="create-an-html-page-with-below-content-and-save-it-to-srcmainresourcespublicindexhtml"></a>Créez une page HTML avec le contenu ci-dessous et enregistrez-la dans */src/main/resources/public/index.html* :
    ---
    
    ```html
    <html>

    <body>
      <div id="output"></div>
      <script>
        (async function () {
          let res = await fetch('/negotiate')
          let data = await res.json();
          let ws = new WebSocket(data.url, 'json.webpubsub.azure.v1');
          ws.onopen = () => {
            console.log('connected');
          };

          let output = document.querySelector('#output');
          ws.onmessage = event => {
            let d = document.createElement('p');
            d.innerText = event.data;
            output.appendChild(d);
          };
        })();
      </script>
    </body>

    </html>
    ```

    Il se connecte simplement au service et imprime tous les messages reçus sur la page. La principale modification est que le sous-protocole est spécifié lors de la création de la connexion WebSocket.

4. Exécutez le serveur.
    # <a name="c"></a>[C#](#tab/csharp)
    Nous utilisons l’outil [Secret Manager](/aspnet/core/security/app-secrets#secret-manager) pour .NET Core afin de définir la chaîne de connexion. Exécutez la commande suivante, en remplaçant `<connection_string>` par la chaîne récupérée à [l’étape précédente](#get-the-connectionstring-for-future-use), et ouvrez http://localhost:5000/index.html dans un navigateur :

    ```bash
    dotnet user-secrets init
    dotnet user-secrets set Azure:WebPubSub:ConnectionString "<connection-string>"
    dotnet run
    ```
    
    # <a name="javascript"></a>[JavaScript](#tab/javascript)
    
    Exécutez la commande suivante, en remplaçant `<connection-string>` par la chaîne **ConnectionString** récupérée à [l’étape précédente](#get-the-connectionstring-for-future-use), et ouvrez http://localhost:8080 dans un navigateur :

    ```bash
    export WebPubSubConnectionString="<connection-string>"
    node server
    ```
    
    # <a name="python"></a>[Python](#tab/python)

    Exécutez la commande suivante, en remplaçant `<connection-string>` par la chaîne **ConnectionString** récupérée à [l’étape précédente](#get-the-connectionstring-for-future-use), et ouvrez http://localhost:8080 dans un navigateur :

    ```bash
    python server.py "<connection-string>"
    ```

    # <a name="java"></a>[Java](#tab/java)

    Exécutez la commande suivante, en remplaçant `<connection-string>` par la chaîne **ConnectionString** récupérée à [l’étape précédente](#get-the-connectionstring-for-future-use), et ouvrez http://localhost:8080 dans un navigateur :

    ```console
    mvn compile & mvn package & mvn exec:java -Dexec.mainClass="com.webpubsub.tutorial.App" -Dexec.cleanupDaemonThreads=false -Dexec.args="'<connection_string>'"
    ```
    ---

    Si vous utilisez Chrome, vous pouvez appuyer sur F12 ou cliquer avec le bouton droit, puis cliquer sur **Inspecter** -> **Outils de développement** et sélectionner l’onglet **Réseau**. Chargez la page web. Comme vous pouvez le constater, la connexion WebSocket est établie. Cliquez pour inspecter la connexion WebSocket. Le message d’événement `connected` est reçu dans le client (cf. ci-dessous). Vous pouvez obtenir le `connectionId` généré pour ce client.
    
    ```json
    {"type":"system","event":"connected","userId":null,"connectionId":"<the_connection_id>"}
    ```

Avec l’aide du sous-protocole, vous pouvez donc obtenir certaines métadonnées de la connexion lorsqu’elle est `connected`.

Notez également que, au lieu de texte brut, le client reçoit à présent un message JSON contenant plus d’informations, comme le type de message et son origine. Vous pouvez utiliser ces informations pour effectuer un traitement plus poussé du message (par l’exemple l’afficher dans un autre style s’il provient d’une source différente), que vous trouverez dans les sections suivantes.

## <a name="publish-messages-from-client"></a>Publication de messages provenant du client

Dans le tutoriel [Création d’une application de conversation](./tutorial-build-chat.md), lorsque le client envoie un message via une connexion WebSocket, il déclenche un événement utilisateur côté serveur. Avec le sous-protocole, il dispose de davantage de fonctionnalités en envoyant un message JSON. Par exemple, vous pouvez publier un message sur d’autres clients directement à partir du client.

Cette fonctionnalité est utile pour diffuser en temps réel une grande quantité de données vers d’autres clients. Utilisons-la pour créer une application de streaming de journaux, qui peut transmettre en temps réel les journaux de la console au navigateur.

1. Création du programme de streaming
 
    # <a name="c"></a>[C#](#tab/csharp)
    Créez un programme `stream` :
    ```bash
    mkdir stream
    cd stream
    dotnet new console
    ```
    
    Mettez à jour `Program.cs` avec le contenu suivant :
    ```csharp
    using System;
    using System.Net.Http;
    using System.Net.WebSockets;
    using System.Text;
    using System.Text.Json;
    using System.Threading.Tasks;
    
    namespace stream
    {
        class Program
        {
            private static readonly HttpClient http = new HttpClient();
            static async Task Main(string[] args)
            {
                // Get client url from remote
                var stream = await http.GetStreamAsync("http://localhost:5000/negotiate");
                var url = (await JsonSerializer.DeserializeAsync<ClientToken>(stream)).url;
                var client = new ClientWebSocket();
                client.Options.AddSubProtocol("json.webpubsub.azure.v1");
    
                await client.ConnectAsync(new Uri(url), default);
    
                Console.WriteLine("Connected.");
                var streaming = Console.ReadLine();
                while (streaming != null)
                {
                    if (!string.IsNullOrEmpty(streaming))
                    {
                        var message = JsonSerializer.Serialize(new
                        {
                            type = "sendToGroup",
                            group = "stream",
                            data = streaming + Environment.NewLine,
                        });
                        Console.WriteLine("Sending " + message);
                        await client.SendAsync(Encoding.UTF8.GetBytes(message), WebSocketMessageType.Text, true, default);
                    }
    
                    streaming = Console.ReadLine();
                }
    
                await client.CloseAsync(WebSocketCloseStatus.NormalClosure, null, default);
            }
    
            private sealed class ClientToken
            {
                public string url { get; set; }
            }
        }
    }
    
    ```

    # <a name="javascript"></a>[JavaScript](#tab/javascript)
    Créez un fichier `stream.js` comportant le contenu suivant.
    
    ```javascript
    const WebSocket = require('ws');
    const fetch = require('node-fetch');

    async function main() {
      let res = await fetch(`http://localhost:8080/negotiate`);
      let data = await res.json();
      let ws = new WebSocket(data.url, 'json.webpubsub.azure.v1');
      let ackId = 0;
      ws.on('open', () => {
        process.stdin.on('data', data => {
          ws.send(JSON.stringify({
            type: 'sendToGroup',
            group: 'stream',
            ackId: ++ackId,
            dataType: 'text',
            data: data.toString()
          }));
        });
      });
      ws.on('message', data => console.log("Received: %s", data));
      process.stdin.on('close', () => ws.close());
    }

    main();
    ```

    Le code ci-dessus crée une connexion WebSocket au service. Chaque fois qu’il reçoit des données, il utilise `ws.send()` pour les publier. Pour publier un message sur d’autres clients, il vous suffit de définir `type` sur `sendToGroup` et de spécifier un nom de groupe dans le message.

    # <a name="python"></a>[Python](#tab/python)

    Ouvrez une autre fenêtre bash pour le programme `stream`, puis installez la dépendance `websockets` :

    ```bash
    mkdir stream
    cd stream

    # Create venv
    python -m venv env
    # Active venv
    source ./env/bin/activate

    pip install websockets
    ```

    Créez un fichier `stream.py` comportant le contenu suivant.

    ```python
    import asyncio
    import sys
    import threading
    import time
    import websockets
    import requests
    import json
    
    async def connect(url):
        async with websockets.connect(url, subprotocols=['json.webpubsub.azure.v1']) as ws:
            print('connected')
            id = 1
            while True:
                data = input()
                payload = {
                    'type': 'sendToGroup',
                    'group': 'stream',
                    'dataType': 'text',
                    'data': str(data + '\n'),
                    'ackId': id
                }
                id = id + 1
                await ws.send(json.dumps(payload))
                await ws.recv()
    
    if __name__ == '__main__':
        res = requests.get('http://localhost:8080/negotiate').json()
    
        try:
            asyncio.get_event_loop().run_until_complete(connect(res['url']))
        except KeyboardInterrupt:
            pass
    
    ```

    Le code ci-dessus crée une connexion WebSocket au service. Chaque fois qu’il reçoit des données, il utilise `ws.send()` pour les publier. Pour publier un message sur d’autres clients, il vous suffit de définir `type` sur `sendToGroup` et de spécifier un nom de groupe dans le message.
    
    # <a name="java"></a>[Java](#tab/java)

    1.  Utilisons un autre terminal et revenons au dossier racine pour créer une application console de streaming `logstream-streaming` et basculer vers le dossier *logstream-streaming* :
        ```console
        mvn archetype:generate --define interactiveMode=n --define groupId=com.webpubsub.quickstart --define artifactId=logstream-streaming --define archetypeArtifactId=maven-archetype-quickstart --define archetypeVersion=1.4
        cd logstream-streaming
        ```
    
    2. Ajoutons des dépendances HttpClient dans le nœud `dependencies` de `pom.xml` :
    
        ```xml
        <!-- https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient -->
        <dependency>
            <groupId>org.apache.httpcomponents</groupId>
            <artifactId>httpclient</artifactId>
            <version>4.5.13</version>
        </dependency>
        <dependency>
          <groupId>com.google.code.gson</groupId>
          <artifactId>gson</artifactId>
          <version>2.8.9</version>
        </dependency>
        ```
    
    3. Utilisons maintenant WebSocket pour nous connecter au service. Nous allons accéder au répertoire */src/main/java/com/webpubsub/quickstart*, ouvrir le fichier *App.java* dans votre éditeur et remplacer le code par ce qui suit :
    ```java
    package com.webpubsub.quickstart;
    
    import java.io.BufferedReader;
    import java.io.IOException;
    import java.io.InputStreamReader;
    import java.net.URI;
    import java.net.http.HttpClient;
    import java.net.http.HttpRequest;
    import java.net.http.HttpResponse;
    import java.net.http.WebSocket;
    import java.util.concurrent.CompletionStage;
    
    import com.google.gson.Gson;
    
    public class App 
    {
        public static void main( String[] args ) throws IOException, InterruptedException
        {
            HttpClient client = HttpClient.newHttpClient();
            HttpRequest request = HttpRequest.newBuilder()
                .uri(URI.create("http://localhost:8080/negotiate"))
                .build();
    
            HttpResponse<String> response = client.send(request, HttpResponse.BodyHandlers.ofString());
            
            Gson gson = new Gson();
    
            String url = gson.fromJson(response.body(), Entity.class).url;
    
            WebSocket ws = HttpClient.newHttpClient().newWebSocketBuilder().subprotocols("json.webpubsub.azure.v1")
                    .buildAsync(URI.create(url), new WebSocketClient()).join();
            int id = 0;
            BufferedReader reader = new BufferedReader(new InputStreamReader(System.in));
            String streaming = reader.readLine();
            App app = new App();
            while (streaming != null && !streaming.isEmpty()){
                String frame = gson.toJson(app.new GroupMessage(streaming + "\n", ++id));
                System.out.println("Sending: " + frame);
                ws.sendText(frame, true);
                streaming = reader.readLine();
            }
        }
    
        private class GroupMessage{
            public String data;
            public int ackId;
            public final String type = "sendToGroup";
            public final String group = "stream";
            
            GroupMessage(String data, int ackId){
                this.data = data;
                this.ackId = ackId;
            }
        }
    
        private static final class WebSocketClient implements WebSocket.Listener {
            private WebSocketClient() {
            }
    
            @Override
            public void onOpen(WebSocket webSocket) {
                System.out.println("onOpen using subprotocol " + webSocket.getSubprotocol());
                WebSocket.Listener.super.onOpen(webSocket);
            }
    
            @Override
            public CompletionStage<?> onText(WebSocket webSocket, CharSequence data, boolean last) {
                System.out.println("onText received " + data);
                return WebSocket.Listener.super.onText(webSocket, data, last);
            }
    
            @Override
            public void onError(WebSocket webSocket, Throwable error) {
                System.out.println("Bad day! " + webSocket.toString());
                WebSocket.Listener.super.onError(webSocket, error);
            }
        }
    
        private static final class Entity {
            public String url;
        }
    }
    
    ```

    4. Accédez au répertoire contenant le fichier *pom.xml* et exécutez le projet avec la commande ci-dessous
    
      ```console
      mvn compile & mvn package & mvn exec:java -Dexec.mainClass="com.webpubsub.quickstart.App" -Dexec.cleanupDaemonThreads=false
      ```
    
    ---
    
    Nous rencontrons ici un nouveau concept : celui de « groupe ». Ce concept logique permet, dans un hub, de publier un message sur un groupe de connexions. Un hub peut comporter plusieurs groupes, et un client a la possibilité de s’abonner à plusieurs groupes en même temps. Lorsque vous utilisez le sous-protocole, vous pouvez publier un message sur un groupe seulement au lieu de le diffuser sur l’ensemble du hub. Pour plus d’informations sur les termes, consultez les [concepts de base](./key-concepts.md).

2.  Dans la mesure où nous utilisons un groupe, nous devons également mettre à jour la page web `index.html` pour joindre le groupe lorsque la connexion WebSocket est établie dans le rappel `ws.onopen`.
    
    ```javascript
    let ackId = 0;
    ws.onopen = () => {
      console.log('connected');
      ws.send(JSON.stringify({
        type: 'joinGroup',
        group: 'stream',
        ackId: ++ackId
      }));
    };
    ```

    Comme vous pouvez le constater, le client rejoint le groupe en envoyant un message dans le type `joinGroup`.

3.  De même, mettez légèrement à jour la logique de rappel `ws.onmessage` pour analyser la réponse JSON et imprimer uniquement les messages provenant du groupe `stream`. Elle fait ainsi office d’imprimante de stream en direct.

    ```javascript
    ws.onmessage = event => {
      let message = JSON.parse(event.data);
      if (message.type === 'message' && message.group === 'stream') {
        let d = document.createElement('span');
        d.innerText = message.data;
        output.appendChild(d);
        window.scrollTo(0, document.body.scrollHeight);
      }
    };
    ```

4.  Dans un souci de sécurité, un client ne peut par défaut pas publier sur un groupe ni s’y abonner par lui-même. Comme vous l’avez peut-être remarqué, `roles` est défini sur le client lors de la génération du jeton :

    # <a name="c"></a>[C#](#tab/csharp)
    Définissez `roles` quand `GenerateClientAccessUri` dans `Startup.cs` :
    ```csharp
    service.GenerateClientAccessUri(roles: new string[] { "webpubsub.sendToGroup.stream", "webpubsub.joinLeaveGroup.stream" })
    ```

    # <a name="javascript"></a>[JavaScript](#tab/javascript)

    Ajoutez `roles` quand `getClientAccessToken` dans `server.js` :

    ```javascript
    app.get('/negotiate', async (req, res) => {
      let token = await service.getClientAccessToken({
        roles: ['webpubsub.sendToGroup.stream', 'webpubsub.joinLeaveGroup.stream']
      });
      ...
    });
    
    ```
    
    # <a name="python"></a>[Python](#tab/python)
    
    Notez que pendant la génération du jeton d’accès, nous définissons les rôles appropriés sur le client dans `server.py` :

    ```python
    roles = ['webpubsub.sendToGroup.stream',
              'webpubsub.joinLeaveGroup.stream']
    token = service.get_client_access_token(roles=roles)
    ```
    
    # <a name="java"></a>[Java](#tab/java)
    
    Notez que pendant la génération du jeton d’accès, nous définissons les rôles appropriés sur le client dans `App.java` :

    ```java
    GetClientAccessTokenOptions option = new GetClientAccessTokenOptions();
    option.addRole("webpubsub.sendToGroup.stream");
    option.addRole("webpubsub.joinLeaveGroup.stream");
    WebPubSubClientAccessToken token = service.getClientAccessToken(option);

    ```
    ---

5.  Enfin, appliquez également un style à `index.html` pour qu’il s’affiche correctement.

    ```html
    <html>

      <head>
        <style>
          #output {
            white-space: pre;
            font-family: monospace;
          }
        </style>
      </head>
    ```

Maintenant, exécutez le code ci-dessous et tapez du texte. Il s’affiche dans le navigateur en temps réel :

# <a name="c"></a>[C#](#tab/csharp)

```bash
ls -R | dotnet run

# Or call `dir /s /b | dotnet run` when you are using CMD under Windows

```

Vous pouvez également le ralentir pour voir que les données sont diffusées en temps réel dans le navigateur :

```bash
for i in $(ls -R); do echo $i; sleep 0.1; done | dotnet run
```

L’exemple de code complet de ce tutoriel est disponible [ici][code-csharp].

# <a name="javascript"></a>[JavaScript](#tab/javascript)

`node stream`

Vous pouvez également utiliser cette application pour rediriger la sortie d’une autre application console et la diffuser dans le navigateur. Par exemple :

```bash
ls -R | node stream

# Or call `dir /s /b | node stream` when you are using CMD under Windows
```

Vous pouvez également le ralentir pour voir que les données sont diffusées en temps réel dans le navigateur :

```bash
for i in $(ls -R); do echo $i; sleep 0.1; done | node stream
```

L’exemple de code complet de ce tutoriel est disponible [ici][code-js].

# <a name="python"></a>[Python](#tab/python)

Vous pouvez maintenant exécuter `python stream.py` et taper du texte. Il s’affiche dans le navigateur en temps réel.

Vous pouvez également utiliser cette application pour rediriger la sortie d’une autre application console et la diffuser dans le navigateur. Par exemple :

```bash
ls -R | python stream.py

# Or call `dir /s /b | python stream.py` when you are using CMD under Windows
```

Vous pouvez également le ralentir pour voir que les données sont diffusées en temps réel dans le navigateur :

```bash
for i in $(ls -R); do echo $i; sleep 0.1; done | python stream.py
```

L’exemple de code complet de ce tutoriel est disponible [ici][code-python].


# <a name="java"></a>[Java](#tab/java)

Vous pouvez maintenant exécuter le code ci-dessous et taper du texte. Il s’affiche dans le navigateur en temps réel.

```console
mvn compile & mvn package & mvn exec:java -Dexec.mainClass="com.webpubsub.quickstart.App" -Dexec.cleanupDaemonThreads=false
```

L’exemple de code complet de ce tutoriel est disponible [ici][code-java].

---


## <a name="next-steps"></a>Étapes suivantes

Ce tutoriel vous donne une idée de base de la procédure à suivre pour se connecter au service Web PubSub et publier des messages sur les clients connectés avec le sous-protocole.

Pour plus d’informations sur l’utilisation du service, consultez les autres tutoriels.

> [!div class="nextstepaction"]
> [Explorer d’autres exemples Azure Web PubSub](https://aka.ms/awps/samples)

[code-csharp]: https://github.com/Azure/azure-webpubsub/tree/main/samples/csharp/logstream/

[code-js]: https://github.com/Azure/azure-webpubsub/tree/main/samples/javascript/logstream/

[code-python]: https://github.com/Azure/azure-webpubsub/tree/main/samples/python/logstream/

[code-java]: https://github.com/Azure/azure-webpubsub/tree/main/samples/java/logstream/
