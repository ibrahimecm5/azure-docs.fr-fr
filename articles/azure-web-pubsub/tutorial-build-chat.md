---
title: 'Tutoriel : Création d’une application de conversation avec le service Azure Web PubSub'
description: Tutoriel qui explique comment créer une application de conversation avec le service Azure Web PubSub.
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: tutorial
ms.date: 11/01/2021
ms.openlocfilehash: fd055903c0a5969c6facd881fc1b0f676c9596c8
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131579214"
---
# <a name="tutorial-create-a-chat-app-with-azure-web-pubsub-service"></a>Tutoriel : Création d’une application de conversation avec le service Azure Web PubSub

Dans le tutoriel [Publication de messages et abonnement](./tutorial-pub-sub-messages.md), vous avez appris les bases de la publication de messages et de l’abonnement aux messages avec Azure Web PubSub. Dans ce tutoriel, vous allez découvrir le système d’événements d’Azure Web PubSub. Vous l’utiliserez pour créer une application web complète avec une fonctionnalité de communication en temps réel. 

Dans ce tutoriel, vous apprenez à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer une instance de service Azure Web PubSub
> * Configurer les paramètres du gestionnaire d’événements pour Azure Web PubSub
> * Créer une application de conversation en temps réel

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

# <a name="java"></a>[Java](#tab/java)

- [Kit de développement Java (JDK)](/java/azure/jdk/), version 8 ou ultérieure
- [Apache Maven](https://maven.apache.org/download.cgi)

---

## <a name="create-the-application"></a>Création de l'application

Dans Azure Web PubSub, il existe deux rôles : serveur et client. Ce concept est similaire aux rôles serveur et client d’une application web. Le serveur est responsable de la gestion des clients, de l’écoute et de la réponse aux messages du client. Le rôle du client, lui, consiste à envoyer les messages de l’utilisateur au serveur, à recevoir des messages du serveur et à permettre à l’utilisateur final de les visualiser.

Dans ce tutoriel, nous allons créer une application web de conversation en temps réel. Dans une application web réelle, la responsabilité du serveur implique également d’authentifier les clients et de traiter les pages web statiques pour l’interface utilisateur de l’application. 

# <a name="c"></a>[C#](#tab/csharp)

Nous allons utiliser [ASP.NET Core](/aspnet/core) pour héberger les pages web et gérer les demandes entrantes.

Commençons par créer une application ASP.NET Core vide.

1.  Créer une application web

    ```bash
    dotnet new web
    dotnet add package Azure.Messaging.WebPubSub --version 1.0.0-beta.3
    ```

2.  Ensuite, ajoutez `app.UseStaticFiles();` avant `app.UseRouting();` dans `Startup.cs` pour prendre en charge les fichiers statiques. Supprimez la valeur `endpoints.MapGet` par défaut dans `app.UseEndpoints`.

    ```csharp
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
        });
    }
    ```

3.  Créez également un fichier HTML et enregistrez-le sous `wwwroot/index.html`. Nous l’utiliserons par la suite pour l’interface utilisateur de l’application de conversation.

    ```html
    <html>
    <body>
      <h1>Azure Web PubSub Chat</h1>
    </body>

    </html>
    ```

Vous pouvez tester le serveur en exécutant `dotnet run --urls http://localhost:8080` et en accédant à http://localhost:8080/index.html dans le navigateur.

Dans le tutoriel [Publication de messages et abonnement](./tutorial-pub-sub-messages.md), comme vous vous en souvenez peut-être, l’abonné utilise une API du kit de développement logiciel (SDK, Software Development Kit) Web PubSub pour générer, à partir de la chaîne de connexion, un jeton d’accès qui lui sert à se connecter au service. Cette façon de procéder n’est généralement pas sécurisée dans une application réelle. En effet, la chaîne de connexion est dotée de privilèges élevés lui permettant d’effectuer tous types d’opérations sur le service. Il ne faut donc pas la partager avec un client. Nous allons remplacer ce processus de génération de jetons d’accès par une API REST côté serveur. Ainsi, le client pourra appeler cette API pour demander un jeton d’accès chaque fois qu’il devra se connecter, sans qu’il soit nécessaire de maintenir la chaîne de connexion.

1.  Installez les dépendances et utilisez l’outil [Gestionnaire de secret](/aspnet/core/security/app-secrets#secret-manager) pour .NET Core afin de définir la chaîne de connexion. Exécutez la commande ci-dessous, en remplaçant `<connection_string>` par la chaîne récupérée à l’[étape précédente](#get-the-connectionstring-for-future-use)

    ```bash
    dotnet add package Microsoft.Extensions.Azure
    dotnet user-secrets init
    dotnet user-secrets set Azure:WebPubSub:ConnectionString "<connection-string>"
    ```

2. Ajoutez le client de service dans `ConfigureServices`. N’oubliez pas de remplacer `<connection_string>` par l’un de vos services.

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddAzureClients(builder =>
        {
            builder.AddWebPubSubServiceClient(Configuration["Azure:WebPubSub:ConnectionString"], "chat");
        });
    }
    ```
2.  Ajoutez une API `/negotiate` au serveur dans `app.UseEndpoints` pour générer le jeton.

    ```csharp
    app.UseEndpoints(endpoints =>
    {
        endpoints.MapGet("/negotiate", async context =>
        {
            var id = context.Request.Query["id"];
            if (id.Count != 1)
            {
                context.Response.StatusCode = 400;
                await context.Response.WriteAsync("missing user id");
                return;
            }
            var serviceClient = context.RequestServices.GetRequiredService<WebPubSubServiceClient>();
            await context.Response.WriteAsync(serviceClient.GenerateClientAccessUri(userId: id).AbsoluteUri);
        });
    });
    ```

    Ce code de génération de jeton est similaire à celui que nous avons utilisé dans le tutoriel [Publication de messages et abonnement](./tutorial-pub-sub-messages.md), à ceci près qu’un argument de plus (`userId`) est transmis lors de la génération du jeton. L’ID d’utilisateur peut servir à identifier l’identité du client. Ainsi, lorsque vous recevez un message, vous en connaissez l’origine.

    Pour tester cette API, exécutez `dotnet run --urls http://localhost:8080` et accédez à `http://localhost:8080/negotiate?id=<user-id>`. Vous obtenez ainsi l’URL complète d’Azure Web PubSub avec un jeton d’accès.

3.  Ensuite, mettez à jour `index.html` en incluant le script suivant pour extraire le jeton du serveur et vous connecter au service.
 
    ```html
    <html>
      <body>
        <h1>Azure Web PubSub Chat</h1>
      </body>
  
      <script>
        (async function () {
          let id = prompt('Please input your user name');
          let res = await fetch(`/negotiate?id=${id}`);
          let url = await res.text();
          let ws = new WebSocket(url);
          ws.onopen = () => console.log('connected');
        })();
      </script>
    </html>
    ```

    Si vous utilisez Chrome, vous pouvez le tester en ouvrant la page d’accueil, puis en entrant votre nom d’utilisateur. Appuyez sur F12 pour ouvrir la fenêtre Outils de développement et basculez vers la table **Console**. `connected` s’imprime dans la console du navigateur.


# <a name="javascript"></a>[JavaScript](#tab/javascript)

Nous allons utiliser [express.js](https://expressjs.com/), un framework web populaire pour node.js qui permet d’accomplir cette tâche.

Commençons par créer une application express vide.

1.  Installez express.js.

    ```bash
    npm init -y
    npm install --save express
    ```

2.  Ensuite, créez un serveur express et enregistrez-le sous le nom `server.js`.

    ```javascript
    const express = require('express');

    const app = express();
    app.use(express.static('public'));
    app.listen(8080, () => console.log('server started'));
    ```

3.  Créez également un fichier HTML et enregistrez-le sous `public/index.html`. Nous l’utiliserons par la suite pour l’interface utilisateur de l’application de conversation.

    ```html
    <html>

    <body>
      <h1>Azure Web PubSub Chat</h1>
    </body>

    </html>
    ```

Vous pouvez tester le serveur en exécutant `node server` et en accédant à http://localhost:8080 dans le navigateur.


Dans le tutoriel [Publication de messages et abonnement](./tutorial-pub-sub-messages.md), comme vous vous en souvenez peut-être, l’abonné utilise une API du kit de développement logiciel (SDK, Software Development Kit) Web PubSub pour générer, à partir de la chaîne de connexion, un jeton d’accès qui lui sert à se connecter au service. Cette façon de procéder n’est généralement pas sécurisée dans une application réelle. En effet, la chaîne de connexion est dotée de privilèges élevés lui permettant d’effectuer tous types d’opérations sur le service. Il ne faut donc pas la partager avec un client. Nous allons remplacer ce processus de génération de jetons d’accès par une API REST côté serveur. Ainsi, le client pourra appeler cette API pour demander un jeton d’accès chaque fois qu’il devra se connecter, sans qu’il soit nécessaire de maintenir la chaîne de connexion.

1.  Installez le kit SDK Azure Web PubSub.

    ```bash
    npm install --save @azure/web-pubsub@1.0.0-alpha.20211102.4
    ```

2.  Ajoutez une API `/negotiate` au serveur pour générer le jeton.

    ```javascript
    const express = require('express');
    const { WebPubSubServiceClient } = require('@azure/web-pubsub');

    const app = express();
    const hubName = 'chat';
    const port = 8080;

    let serviceClient = new WebPubSubServiceClient(process.env.WebPubSubConnectionString, hubName);

    app.get('/negotiate', async (req, res) => {
      let id = req.query.id;
      if (!id) {
        res.status(400).send('missing user id');
        return;
      }
      let token = await serviceClient.getClientAccessToken({ userId: id });
      res.json({
        url: token.url
      });
    });

    app.use(express.static('public'));
    app.listen(8080, () => console.log('server started'));
    ```

    Ce code de génération de jeton est similaire à celui que nous avons utilisé dans le tutoriel [Publication de messages et abonnement](./tutorial-pub-sub-messages.md), à ceci près qu’un argument de plus (`userId`) est transmis lors de la génération du jeton. L’ID d’utilisateur peut servir à identifier l’identité du client. Ainsi, lorsque vous recevez un message, vous en connaissez l’origine.

    Exécutez la commande ci-dessous pour tester cette API :

    ```bash
    export WebPubSubConnectionString="<connection-string>"
    node server
    ```

    Accédez à `http://localhost:8080/negotiate?id=<user-id>` pour obtenir l’URL complète d’Azure Web PubSub avec un jeton d’accès.

3.  Ensuite, mettez à jour `index.html` avec le script suivant pour extraire le jeton du serveur et vous connecter au service.
 
    ```html

    <html>
      <body>
        <h1>Azure Web PubSub Chat</h1>
      </body>
  
      <script>
        (async function () {
          let id = prompt('Please input your user name');
          let res = await fetch(`/negotiate?id=${id}`);
          let data = await res.json();
          let ws = new WebSocket(data.url);
          ws.onopen = () => console.log('connected');
        })();
      </script>
    </html>
    ```

    Si vous utilisez Chrome, vous pouvez le tester en ouvrant la page d’accueil, puis en entrant votre nom d’utilisateur. Appuyez sur F12 pour ouvrir la fenêtre Outils de développement et basculez vers la table **Console**. `connected` s’imprime dans la console du navigateur.

# <a name="java"></a>[Java](#tab/java)

Nous allons utiliser le framework web [Javalin](https://javalin.io/) pour héberger les pages web et gérer les demandes entrantes.

1. Tout d’abord, utilisez Maven pour créer une application `webpubsub-tutorial-chat` et basculer vers le dossier *webpubsub-tutorial-chat* :

    ```console
    mvn archetype:generate --define interactiveMode=n --define groupId=com.webpubsub.tutorial --define artifactId=webpubsub-tutorial-chat --define archetypeArtifactId=maven-archetype-quickstart --define archetypeVersion=1.4
    cd webpubsub-tutorial-chat
    ```

2. Ajoutez la dépendance du framework web `javalin` dans le nœud `dependencies` de `pom.xml` :

    * `javalin` : framework web simple pour Java
    * `slf4j-simple` : enregistreur d’événements pour Java

    ```xml
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

3. Accédez au répertoire */src/main/java/com/webpubsub/tutorial*, ouvrez le fichier *App.java* dans votre éditeur, puis utilisez `Javalin.create` pour traiter des fichiers statiques :

    ```java
    package com.webpubsub.tutorial;
    
    import io.javalin.Javalin;
    
    public class App {
        public static void main(String[] args) {
            // start a server
            Javalin app = Javalin.create(config -> {
                config.addStaticFiles("public");
            }).start(8080);
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

4. Créez un fichier HTML et enregistrez-le dans */src/main/resources/public/index.html*. Nous l’utiliserons par la suite pour l’interface utilisateur de l’application de conversation.

    ```html
    <html>
    <body>
      <h1>Azure Web PubSub Chat</h1>
    </body>

    </html>
    ```

Vous pouvez tester le serveur en exécutant la commande suivante sous le répertoire contenant le fichier *pom.xml* et en accédant à http://localhost:8080 dans le navigateur.

```console
mvn compile & mvn package & mvn exec:java -Dexec.mainClass="com.webpubsub.tutorial.App" -Dexec.cleanupDaemonThreads=false
```

Dans le tutoriel [Publication de messages et abonnement](./tutorial-pub-sub-messages.md), comme vous vous en souvenez peut-être, l’abonné utilise une API du kit de développement logiciel (SDK, Software Development Kit) Web PubSub pour générer, à partir de la chaîne de connexion, un jeton d’accès qui lui sert à se connecter au service. Cette façon de procéder n’est généralement pas sécurisée dans une application réelle. En effet, la chaîne de connexion est dotée de privilèges élevés lui permettant d’effectuer tous types d’opérations sur le service. Il ne faut donc pas la partager avec un client. Nous allons remplacer ce processus de génération de jetons d’accès par une API REST côté serveur. Ainsi, le client pourra appeler cette API pour demander un jeton d’accès chaque fois qu’il devra se connecter, sans qu’il soit nécessaire de maintenir la chaîne de connexion.

1. Ajoutez la dépendance du kit SDK Azure Web PubSub dans le nœud `dependencies` de `pom.xml` :

    ```xml
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-messaging-webpubsub</artifactId>
        <version>1.0.0-beta.2</version>
    </dependency>
    ```

2. Ajoutez une API `/negotiate` au fichier `App.java` pour générer le jeton :

    ```java
    package com.webpubsub.tutorial;
    
    import com.azure.messaging.webpubsub.WebPubSubClientBuilder;
    import com.azure.messaging.webpubsub.WebPubSubServiceClient;
    import com.azure.messaging.webpubsub.models.GetAuthenticationTokenOptions;
    import com.azure.messaging.webpubsub.models.WebPubSubAuthenticationToken;
    
    import io.javalin.Javalin;
    
    public class App {
        public static void main(String[] args) {
            
            if (args.length != 1) {
                System.out.println("Expecting 1 arguments: <connection-string>");
                return;
            }
    
            // create the service client
            WebPubSubServiceClient client = new WebPubSubClientBuilder()
                    .connectionString(args[0])
                    .hub("chat")
                    .buildClient();
    
            // start a server
            Javalin app = Javalin.create(config -> {
                config.addStaticFiles("public");
            }).start(8080);
    
            
            // Handle the negotiate request and return the token to the client
            app.get("/negotiate", ctx -> {
                String id = ctx.queryParam("id");
                if (id == null) {
                    ctx.status(400);
                    ctx.result("missing user id");
                    return;
                }
                GetAuthenticationTokenOptions option = new GetAuthenticationTokenOptions();
                option.setUserId(id);
                WebPubSubAuthenticationToken token = client.getAuthenticationToken(option);
                ctx.result(token.getUrl());
                return;
            });
        }
    }
    ```

    Ce code de génération de jeton est similaire à celui que nous avons utilisé dans le tutoriel [Publication de messages et abonnement](./tutorial-pub-sub-messages.md), à ceci près que la méthode `setUserId` est appelée pour définir l’ID d’utilisateur lors de la génération du jeton. L’ID d’utilisateur peut servir à identifier l’identité du client. Ainsi, lorsque vous recevez un message, vous en connaissez l’origine.

    Pour tester cette API, exécutez la commande suivante, en remplaçant `<connection_string>` par la valeur **ConnectionString** extraite [précédemment](#get-the-connectionstring-for-future-use), puis accédez à `http://localhost:8080/negotiate?id=<user-id>`. Vous obtenez ainsi l’URL complète d’Azure Web PubSub avec un jeton d’accès.

    ```console
    mvn compile & mvn package & mvn exec:java -Dexec.mainClass="com.webpubsub.tutorial.App" -Dexec.cleanupDaemonThreads=false -Dexec.args="'<connection_string>'"
    ```

3. Ensuite, mettez à jour `index.html` avec le script suivant pour extraire le jeton du serveur et vous connecter au service.

    ```html
    <html>
      <body>
        <h1>Azure Web PubSub Chat</h1>
      </body>
  
      <script>
        (async function () {
          let id = prompt('Please input your user name');
          let res = await fetch(`/negotiate?id=${id}`);
          let url = await res.text();
          let ws = new WebSocket(url);
          ws.onopen = () => console.log('connected');
        })();
      </script>
    </html>
    ```

    Si vous utilisez Chrome, vous pouvez le tester en ouvrant la page d’accueil, puis en entrant votre nom d’utilisateur. Appuyez sur F12 pour ouvrir la fenêtre Outils de développement et basculez vers la table **Console**. `connected` s’imprime dans la console du navigateur.

---

## <a name="handle-events"></a>Gérez les événements

Lorsque certaines activités se produisent côté client (par exemple si un client est connecté ou déconnecté), le service Azure Web PubSub envoie des notifications au serveur pour lui permettre de réagir à ces événements.

Les événements sont remis au serveur sous la forme d’un Webhook. Celui-ci est traité et exposé par le serveur d’applications, et inscrit du côté du service Azure Web PubSub. Le service appelle le Webhook chaque fois qu’un événement se produit.

Azure Web PubSub suit [CloudEvents](./reference-cloud-events.md) pour décrire les données d’événement. 

# <a name="c"></a>[C#](#tab/csharp)
Vous devez maintenant implémenter le gestionnaire d’événements en C# par vous-même. La procédure, simple et illustrée ci-dessous, consiste à suivre les [spécifications du protocole](./reference-cloud-events.md).

1. Ajoutez des gestionnaires d’événements dans `UseEndpoints`. Spécifiez le chemin du point de terminaison des événements, par exemple `/eventhandler`. 

2. Il faut tout d’abord gérer les demandes OPTIONS de protection contre les abus. Pour cela, vérifiez si l’en-tête contient un en-tête `WebHook-Request-Origin` et retournez l’en-tête `WebHook-Allowed-Origin`. Pour simplifier la démonstration, nous retournons `*` pour autoriser toutes les origines.
    ```csharp
    app.UseEndpoints(endpoints =>
    {
        // abuse protection
        endpoints.Map("/eventhandler/{*path}", async context =>
        {
            if (context.Request.Method == "OPTIONS")
            {
                if (context.Request.Headers["WebHook-Request-Origin"].Count > 0)
                {
                    context.Response.Headers["WebHook-Allowed-Origin"] = "*";
                    context.Response.StatusCode = 200;
                    return;
                }
            }
        });
    });
    ```

3. Il s’agit ensuite de vérifier si les demandes entrantes correspondent aux événements attendus. Intéressons-nous maintenant à l’événement système `connected`, qui doit contenir l’en-tête `ce-type` `azure.webpubsub.sys.connected`. Ajoutez la logique après la protection contre les abus :
    ```csharp
    app.UseEndpoints(endpoints =>
    {
        // abuse protection
        endpoints.Map("/eventhandler/{*path}", async context =>
        {
            if (context.Request.Method == "OPTIONS")
            {
                if (context.Request.Headers["WebHook-Request-Origin"].Count > 0)
                {
                    context.Response.Headers["WebHook-Allowed-Origin"] = "*";
                    context.Response.StatusCode = 200;
                    return;
                }
            }
            else if (context.Request.Method == "POST")
            {
                // get the userId from header
                var userId = context.Request.Headers["ce-userId"];
                if (context.Request.Headers["ce-type"] == "azure.webpubsub.sys.connected")
                {
                    // the connected event
                    Console.WriteLine($"{userId} connected");
                    context.Response.StatusCode = 200;
                    return;
                }
            }
        });
    });
    ```

Le code ci-dessus affiche simplement un message sur la console lorsqu’un client est connecté. Comme vous pouvez le constater, `context.Request.Headers["ce-userId"]` est utilisé pour voir l’identité du client connecté.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Si vous utilisez le kit SDK Web PubSub, il existe déjà une implémentation permettant d’analyser et de traiter le schéma CloudEvents. Vous n’avez donc pas besoin de gérer ces détails.

Ajoutez le code suivant pour exposer une API REST dans `/eventhandler` (ce qui est effectué par le middleware express fourni par le kit SDK Web PubSub) et ainsi gérer l’événement client connecté :

```bash
npm install --save @azure/web-pubsub-express@1.0.0-alpha.20211102.4
```

```javascript
const { WebPubSubEventHandler } = require('@azure/web-pubsub-express');

let handler = new WebPubSubEventHandler(hubName, {
  path: '/eventhandler',
  onConnected: async req => {
    console.log(`${req.context.userId} connected`);
  }
});

app.use(handler.getMiddleware());
```

Le code ci-dessus affiche simplement un message sur la console lorsqu’un client est connecté. Comme vous pouvez le constater, `req.context.userId` est utilisé pour voir l’identité du client connecté.

# <a name="java"></a>[Java](#tab/java)
Vous devez maintenant implémenter le gestionnaire d’événements en Java par vous-même. La procédure, simple et illustrée ci-dessous, consiste à suivre les [spécifications du protocole](./reference-cloud-events.md).

1. Ajoutez le gestionnaire HTTP pour le chemin du gestionnaire d’événements, par exemple `/eventhandler`. 

2. Il faut tout d’abord gérer les demandes OPTIONS de protection contre les abus. Pour cela, vérifiez si l’en-tête contient un en-tête `WebHook-Request-Origin` et retournez l’en-tête `WebHook-Allowed-Origin`. Pour simplifier la démonstration, nous retournons `*` pour autoriser toutes les origines.
    ```java
    
    // validation: https://azure.github.io/azure-webpubsub/references/protocol-cloudevents#validation
    app.options("/eventhandler", ctx -> {
        ctx.header("WebHook-Allowed-Origin", "*");
    });
    ```

3. Il s’agit ensuite de vérifier si les demandes entrantes correspondent aux événements attendus. Intéressons-nous maintenant à l’événement système `connected`, qui doit contenir l’en-tête `ce-type` `azure.webpubsub.sys.connected`. Ajoutez la logique après la protection contre les abus :
    ```java
    // validation: https://azure.github.io/azure-webpubsub/references/protocol-cloudevents#validation
    app.options("/eventhandler", ctx -> {
        ctx.header("WebHook-Allowed-Origin", "*");
    });

    // handle events: https://azure.github.io/azure-webpubsub/references/protocol-cloudevents#events
    app.post("/eventhandler", ctx -> {
        String event = ctx.header("ce-type");
        if ("azure.webpubsub.sys.connected".equals(event)) {
            String id = ctx.header("ce-userId");
            System.out.println(id + " connected.");
        }
        ctx.status(200);
    });

    ```

Le code ci-dessus affiche simplement un message sur la console lorsqu’un client est connecté. Comme vous pouvez le constater, `ctx.header("ce-userId")` est utilisé pour voir l’identité du client connecté.

---

## <a name="set-up-the-event-handler"></a>Configuration du gestionnaire d’événements

### <a name="expose-localhost"></a>Exposition de localhost

Il s’agit maintenant de définir l’URL du Webhook dans le service afin qu’il sache où appeler lorsqu’un nouvel événement se présente. Un problème se pose cependant : comme le serveur s’exécute sur localhost, il ne possède pas de point de terminaison accessible sur Internet. Nous utilisons ici [ngrok](https://ngrok.com/) pour exposer localhost sur Internet.

1.  Tout d’abord, téléchargez ngrok à l’adresse https://ngrok.com/download et extrayez l’exécutable dans votre dossier local ou dans votre dossier bin système.
2.  Démarrer ngrok
    
    ```bash
    ngrok http 8080
    ```

ngrok imprime une URL (`https://<domain-name>.ngrok.io`) accessible sur Internet.

### <a name="set-event-handler"></a>Définition du gestionnaire d’événements

Mettons maintenant à jour le gestionnaire d’événements du service et définissons l’URL du Webhook.

Utilisez la commande Azure CLI [az webpubsub hub create](/cli/azure/webpubsub/hub#az_webpubsub_hub_update) pour créer les paramètres du gestionnaire d’événements pour le hub de conversations

  > [!Important]
  > Remplacez &lt;your-unique-resource-name&gt; par le nom de votre ressource Web PubSub créée précédemment.
  > Remplacez &lt;domain-name&gt; par le nom ngrok imprimé.

```azurecli-interactive
az webpubsub hub create -n "<your-unique-resource-name>" -g "myResourceGroup" --hub-name "chat" --event-handler url-template="https://<domain-name>.ngrok.io/eventHandler" user-event-pattern="*" system-event="connected"
```

Une fois la mise à jour terminée, ouvrez la page d’accueil http://localhost:8080/index.html, puis entrez votre nom d’utilisateur. Le message connected s’affiche dans la console du serveur.

## <a name="handle-message-events"></a>Gestion des événements message

Outre les événements système comme `connected` et `disconnected`, le client peut également envoyer des messages via la connexion WebSocket. Ces messages sont remis au serveur sous la forme d’un type spécial d’événement, `message`. Cet événement permet de recevoir des messages d’un client et de les diffuser à tous les clients pour qu’ils puissent communiquer entre eux.

# <a name="c"></a>[C#](#tab/csharp)

Le `ce-type` de l’événement `message` est toujours `azure.webpubsub.user.message`. Pour plus d’informations, consultez [Événement message](./reference-cloud-events.md#message).

1. Gérez l’événement message.

    ```csharp
    app.UseEndpoints(endpoints =>
    {
        // abuse protection
        endpoints.Map("/eventhandler/{*path}", async context =>
        {
            var serviceClient = context.RequestServices.GetRequiredService<WebPubSubServiceClient>();
            if (context.Request.Method == "OPTIONS")
            {
                if (context.Request.Headers["WebHook-Request-Origin"].Count > 0)
                {
                    context.Response.Headers["WebHook-Allowed-Origin"] = "*";
                    context.Response.StatusCode = 200;
                    return;
                }
            }
            else if (context.Request.Method == "POST")
            {
                // get the userId from header
                var userId = context.Request.Headers["ce-userId"];
                if (context.Request.Headers["ce-type"] == "azure.webpubsub.sys.connected")
                {
                    // the connected event
                    Console.WriteLine($"{userId} connected");
                    context.Response.StatusCode = 200;
                    return;
                }
                else if (context.Request.Headers["ce-type"] == "azure.webpubsub.user.message")
                {
                    using var stream = new StreamReader(context.Request.Body);
                    await serviceClient.SendToAllAsync($"[{userId}] {await stream.ReadToEndAsync()}");
                    context.Response.StatusCode = 200;
                    return;
                }
            }
        });
    });
    ```

    Ce gestionnaire d’événements utilise `WebPubSubServiceClient.SendToAllAsync()` pour diffuser le message reçu à tous les clients.

2.  Mettez à jour `index.html` pour ajouter la logique permettant d’envoyer un message de l’utilisateur au serveur et d’afficher les messages reçus sur la page.

    ```html
    <html>

    <body>
      <h1>Azure Web PubSub Chat</h1>
      <input id="message" placeholder="Type to chat...">
      <div id="messages"></div>
      <script>
        (async function () {
          let id = prompt('Please input your user name');
          let res = await fetch(`/negotiate?id=${id}`);
          let url = await res.text();
          let ws = new WebSocket(url);
          ws.onopen = () => console.log('connected');

          let messages = document.querySelector('#messages');
          ws.onmessage = event => {
            let m = document.createElement('p');
            m.innerText = event.data;
            messages.appendChild(m);
          };

          let message = document.querySelector('#message');
          message.addEventListener('keypress', e => {
            if (e.charCode !== 13) return;
            ws.send(message.value);
            message.value = '';
          });
        })();
      </script>
    </body>

    </html>
    ```

    Comme vous pouvez le constater dans le code ci-dessus, `WebSocket.send()` est utilisé pour envoyer un message et `WebSocket.onmessage` pour l’écouter à partir du service.

3.  Enfin, mettez à jour le gestionnaire `onConnected` pour diffuser l’événement connected à tous les clients. Ces derniers voient ainsi qui a rejoint la salle de conversation.

    ```csharp
    app.UseEndpoints(endpoints =>
    {
        // abuse protection
        endpoints.Map("/eventhandler/{*path}", async context =>
        {
            if (context.Request.Method == "OPTIONS")
            {
                if (context.Request.Headers["WebHook-Request-Origin"].Count > 0)
                {
                    context.Response.Headers["WebHook-Allowed-Origin"] = "*";
                    context.Response.StatusCode = 200;
                    return;
                }
            }
            else if (context.Request.Method == "POST")
            {
                // get the userId from header
                var userId = context.Request.Headers["ce-userId"];
                if (context.Request.Headers["ce-type"] == "azure.webpubsub.sys.connected")
                {
                    // the connected event
                    Console.WriteLine($"{userId} connected");
                    await serviceClient.SendToAllAsync($"[SYSTEM] {userId} joined.");
                    context.Response.StatusCode = 200;
                    return;
                }
            }
        });
    });
    ```

Maintenant, exécutez le serveur avec `dotnet run --urls http://localhost:8080` et ouvrez plusieurs instances de navigateur pour accéder à http://localhost:8080/index.html. Vous pouvez ainsi les faire converser les unes avec les autres.

L’exemple de code complet de ce tutoriel est disponible [ici][code-csharp].

# <a name="javascript"></a>[JavaScript](#tab/javascript)

1. Ajoutez un gestionnaire `handleUserEvent`.

    ```javascript
    let handler = new WebPubSubEventHandler(hubName, {
      path: '/eventhandler',
      onConnected: async req => {
        console.log(`${req.context.userId} connected`);
      },
      handleUserEvent: async (req, res) => {
        if (req.context.eventName === 'message') 
            await serviceClient.sendToAll(`[${req.context.userId}] ${req.data}`, { contentType: 'text/plain' });
        res.success();
      }
    });
    ```

    Ce gestionnaire d’événements utilise `WebPubSubServiceClient.sendToAll()` pour diffuser le message reçu à tous les clients.

    Comme vous pouvez le constater, `handleUserEvent` possède également un objet `res` dans lequel vous pouvez renvoyer le message à l’expéditeur de l’événement. Ici, nous appelons simplement `res.success()` pour que le Webhook retourne 200. (Notez que cet appel est requis même s’il ne faut rien renvoyer au client. Sinon, le Webhook ne retourne jamais rien, et la connexion cliente est fermée.)

2.  Mettez à jour `index.html` pour ajouter la logique permettant d’envoyer un message de l’utilisateur au serveur et d’afficher les messages reçus sur la page.

    ```html
    <html>

      <body>
        <h1>Azure Web PubSub Chat</h1>
        <input id="message" placeholder="Type to chat...">
        <div id="messages"></div>
        <script>
          (async function () {
            let id = prompt('Please input your user name');
            let res = await fetch(`/negotiate?id=${id}`);
            let data = await res.json();
            let ws = new WebSocket(data.url);
            ws.onopen = () => console.log('connected');
  
            let messages = document.querySelector('#messages');
            ws.onmessage = event => {
              let m = document.createElement('p');
              m.innerText = event.data;
              messages.appendChild(m);
            };
  
            let message = document.querySelector('#message');
            message.addEventListener('keypress', e => {
              if (e.charCode !== 13) return;
              ws.send(message.value);
              message.value = '';
            });
          })();
        </script>
      </body>
  
      </html>
    ```

    Comme vous pouvez le constater dans le code ci-dessus, `WebSocket.send()` est utilisé pour envoyer un message et `WebSocket.onmessage` pour l’écouter à partir du service.

3. `sendToAll` accepte l’objet comme entrée et envoie du texte JSON aux clients. Dans les scénarios réels, un objet complexe est généralement nécessaire pour contenir plus d’informations sur le message. Enfin, mettez à jour les gestionnaires pour diffuser des objets JSON à tous les clients :

    ```javascript
    let handler = new WebPubSubEventHandler(hubName, {
      path: '/eventhandler',
      onConnected: async req => {
        console.log(`${req.context.userId} connected`);
        await serviceClient.sendToAll({
          type: "system",
          message: `${req.context.userId} joined`
        });
      },
      handleUserEvent: async (req, res) => {
        if (req.context.eventName === 'message') {
          await serviceClient.sendToAll({
            from: req.context.userId,
            message: req.data
          });
        }
        res.success();
      }
    });
    ```

4. Mettez à jour le client pour analyser les données JSON :
    ```html
    <html>

      <body>
        <h1>Azure Web PubSub Chat</h1>
        <input id="message" placeholder="Type to chat...">
        <div id="messages"></div>
        <script>
          (async function () {
            let id = prompt('Please input your user name');
            let res = await fetch(`/negotiate?id=${id}`);
            let data = await res.json();
            let ws = new WebSocket(data.url);
            ws.onopen = () => console.log('connected');
  
            let messages = document.querySelector('#messages');
            
            ws.onmessage = event => {
              let m = document.createElement('p');
              let data = JSON.parse(event.data);
              m.innerText = `[${data.type || ''}${data.from || ''}] ${data.message}`;
              messages.appendChild(m);
            };
  
            let message = document.querySelector('#message');
            message.addEventListener('keypress', e => {
              if (e.charCode !== 13) return;
              ws.send(message.value);
              message.value = '';
            });
          })();
        </script>
      </body>
  
    </html>
    ```

Maintenant, exécutez le serveur et ouvrez plusieurs instances de navigateur. Vous pouvez ainsi les faire converser les unes avec les autres.

L’exemple de code complet de ce tutoriel est disponible [ici][code-js].

# <a name="java"></a>[Java](#tab/java)

Le `ce-type` de l’événement `message` est toujours `azure.webpubsub.user.message`. Pour plus d’informations, consultez [Événement message](./reference-cloud-events.md#message).

1. Gérez l’événement message.

    ```java
    // handle events: https://azure.github.io/azure-webpubsub/references/protocol-cloudevents#events
    app.post("/eventhandler", ctx -> {
        String event = ctx.header("ce-type");
        if ("azure.webpubsub.sys.connected".equals(event)) {
            String id = ctx.header("ce-userId");
            System.out.println(id + " connected.");
        } else if ("azure.webpubsub.user.message".equals(event)) {
            String id = ctx.header("ce-userId");
            String message = ctx.body();
            client.sendToAll(String.format("[%s] %s", id, message), WebPubSubContentType.TEXT_PLAIN);
        }
        ctx.status(200);
    });
    ```

    Ce gestionnaire d’événements utilise `client.sendToAll()` pour diffuser le message reçu à tous les clients.

2.  Mettez à jour `index.html` pour ajouter la logique permettant d’envoyer un message de l’utilisateur au serveur et d’afficher les messages reçus sur la page.

    ```html
    <html>

    <body>
      <h1>Azure Web PubSub Chat</h1>
      <input id="message" placeholder="Type to chat...">
      <div id="messages"></div>
      <script>
        (async function () {
          let id = prompt('Please input your user name');
          let res = await fetch(`/negotiate?id=${id}`);
          let url = await res.text();
          let ws = new WebSocket(url);
          ws.onopen = () => console.log('connected');

          let messages = document.querySelector('#messages');
          ws.onmessage = event => {
            let m = document.createElement('p');
            m.innerText = event.data;
            messages.appendChild(m);
          };

          let message = document.querySelector('#message');
          message.addEventListener('keypress', e => {
            if (e.charCode !== 13) return;
            ws.send(message.value);
            message.value = '';
          });
        })();
      </script>
    </body>

    </html>
    ```

    Comme vous pouvez le constater dans le code ci-dessus, `WebSocket.send()` est utilisé pour envoyer un message et `WebSocket.onmessage` pour l’écouter à partir du service.

3.  Enfin, mettez à jour le gestionnaire d’événements `connected` pour diffuser l’événement connected à tous les clients. Ces derniers voient ainsi qui a rejoint la salle de conversation.

    ```java

    // handle events: https://azure.github.io/azure-webpubsub/references/protocol-cloudevents#events
    app.post("/eventhandler", ctx -> {
        String event = ctx.header("ce-type");
        if ("azure.webpubsub.sys.connected".equals(event)) {
            String id = ctx.header("ce-userId");
            client.sendToAll(String.format("[SYSTEM] %s joined", id), WebPubSubContentType.TEXT_PLAIN);
        } else if ("azure.webpubsub.user.message".equals(event)) {
            String id = ctx.header("ce-userId");
            String message = ctx.body();
            client.sendToAll(String.format("[%s] %s", id, message), WebPubSubContentType.TEXT_PLAIN);
        }
        ctx.status(200);
    });

    ```

Maintenant, exécutez le serveur avec la commande ci-dessous et ouvrez plusieurs instances de navigateur. Vous pouvez ainsi les faire converser les unes avec les autres.

```console
mvn compile & mvn package & mvn exec:java -Dexec.mainClass="com.webpubsub.tutorial.App" -Dexec.cleanupDaemonThreads=false -Dexec.args="'<connection_string>'"
```

L’exemple de code complet de ce tutoriel est disponible [ici][code-java].

---

## <a name="next-steps"></a>Étapes suivantes

Ce tutoriel vous donne une idée de base du fonctionnement du système d’événements dans le service Azure Web PubSub.

Pour plus d’informations sur l’utilisation du service, consultez les autres tutoriels.

> [!div class="nextstepaction"]
> [Explorer d’autres exemples Azure Web PubSub](https://aka.ms/awps/samples)


[code-js]: https://github.com/Azure/azure-webpubsub/tree/main/samples/javascript/chatapp/
[code-java]: https://github.com/Azure/azure-webpubsub/tree/main/samples/java/chatapp/
[code-csharp]: https://github.com/Azure/azure-webpubsub/tree/main/samples/csharp/chatapp/
