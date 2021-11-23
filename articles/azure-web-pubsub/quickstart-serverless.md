---
title: 'Tutoriel : Créer une application de conversation serverless avec une authentification client'
description: Tutoriel pour comprendre comment utiliser le service Azure Web PubSub et Azure Functions dans le but de créer une application de conversation serverless avec une authentification client.
author: yjin81
ms.author: yajin1
ms.service: azure-web-pubsub
ms.topic: tutorial
ms.date: 11/08/2021
ms.openlocfilehash: 7dc376bb84c52688e1f665501680f11f6bb317eb
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132488522"
---
# <a name="tutorial-create-a-serverless-real-time-chat-app-with-azure-functions-and-azure-web-pubsub-service"></a>Tutoriel : Créer une application de conversation serverless avec Azure Functions et le service Azure Web PubSub

Le service Azure Web PubSub vous aide à créer facilement des applications web de messagerie en temps réel avec des WebSockets et le modèle publication-abonnement. Azure Functions est une plateforme serverless qui vous permet d’exécuter votre code sans gérer d’infrastructures. Dans ce démarrage rapide, découvrez comment utiliser le service Azure Web PubSub et Azure Functions pour générer une application serverless avec une messagerie en temps réel et le modèle publication-abonnement.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Créer une application de conversation serverless
> * Utiliser des liaisons de déclencheur et de sortie de fonction Web PubSub
> * Déployer la fonction sur l’application de fonction Azure
> * Configurer l’authentification Azure
> * Configurer le gestionnaire d’événements Web PubSub pour acheminer les événements et les messages vers l’application

## <a name="prerequisites"></a>Prérequis

# <a name="javascript"></a>[JavaScript](#tab/javascript)

* Un éditeur de code, comme [Visual Studio Code](https://code.visualstudio.com/).

* [Node.js](https://nodejs.org/en/download/), version 10.x.
   > [!NOTE]
   > Pour plus d’informations sur les versions prises en charge de Node.js, consultez la [documentation sur les versions du runtime d’Azure Functions](../azure-functions/functions-versions.md#languages).
* [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools#installing) (v3 ou supérieure de préférence) pour exécuter les applications Azure Function localement et les déployer vers Azure.

* [Interface de ligne de commande Azure (Azure CLI)](/cli/azure) pour gérer les ressources Azure.

* (Facultatif) [ngrok](https://ngrok.com/download) pour exposer la fonction locale comme un gestionnaire d’événements pour le service Web PubSub. Cette option est facultative uniquement si vous exécutez localement l’application de fonction.

# <a name="c"></a>[C#](#tab/csharp)

* Un éditeur de code, comme [Visual Studio Code](https://code.visualstudio.com/).

* [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools#installing) (v3 ou supérieure de préférence) pour exécuter les applications Azure Function localement et les déployer vers Azure.

* [Interface de ligne de commande Azure (Azure CLI)](/cli/azure) pour gérer les ressources Azure.

* (Facultatif) [ngrok](https://ngrok.com/download) pour exposer la fonction locale comme un gestionnaire d’événements pour le service Web PubSub. Cette option est facultative uniquement si vous exécutez localement l’application de fonction.

---

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [create-instance-portal](includes/create-instance-portal.md)]

## <a name="create-the-functions"></a>Créer les fonctions

1. Vérifiez qu’[Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools#installing) est installé. Puis créez un répertoire vide pour le projet. Exécutez la commande sous ce répertoire de travail.

    # <a name="javascript"></a>[JavaScript](#tab/javascript)
    ```bash
    func init --worker-runtime javascript
    ```

    # <a name="c"></a>[C#](#tab/csharp)
    ```bash
    func init --worker-runtime dotnet
    ```

2. *Installez le package d’extension de fonction `Microsoft.Azure.WebJobs.Extensions.WebPubSub`.

    > [!NOTE]
    > L’étape est facultative quand les [bundles d’extension](/azure/azure-functions/functions-bindings-register#extension-bundles) sont pris en charge.

   a. Supprimez la section `extensionBundle` dans `host.json` pour activer l’installation d’un package d’extension spécifique à l’étape suivante. Ou rendez simplement l’hôte json aussi simple que ci-dessous.
    ```json
    {
        "version": "2.0"
    }
    ```
   b. Exécutez la commande pour installer un package d’extension de fonction spécifique.
    ```bash
    func extensions install --package Microsoft.Azure.WebJobs.Extensions.WebPubSub --version 1.0.0
    ```

3. Créez une fonction `index` pour lire et héberger une page web statique pour les clients.
    ```bash
    func new -n index -t HttpTrigger
    ```
   # <a name="javascript"></a>[JavaScript](#tab/javascript)
   - Mettez à jour `index/function.json` et copiez les codes json suivants.
        ```json
        {
            "bindings": [
                {
                    "authLevel": "anonymous",
                    "type": "httpTrigger",
                    "direction": "in",
                    "name": "req",
                    "methods": [
                      "get",
                      "post"
                    ]
                },
                {
                    "type": "http",
                    "direction": "out",
                    "name": "res"
                }
            ]
        }
        ```
   - Mettez à jour `index/index.js` et copiez les codes suivants.
        ```js
        var fs = require('fs');
        var path = require('path');

        module.exports = function (context, req) {
            var index = 'index.html';
            if (process.env["HOME"] != null)
            {
                index = path.join(process.env["HOME"], "site", "wwwroot", index);
            }
            context.log("index.html path: " + index);
            fs.readFile(index, 'utf8', function (err, data) {
                if (err) {
                console.log(err);
                context.done(err);
                }
                context.res = {
                status: 200,
                headers: {
                    'Content-Type': 'text/html'
                },
                body: data
                };
                context.done();
            });
        }
        ```

   # <a name="c"></a>[C#](#tab/csharp)
   - Mettez à jour `index.cs` et remplacez la fonction `Run` par les codes suivants.
        ```c#
        [FunctionName("index")]
        public static IActionResult Run([HttpTrigger(AuthorizationLevel.Anonymous)] HttpRequest req)
        {
            string indexFile = "index.html";
            if (Environment.GetEnvironmentVariable("HOME") != null)
            {
                indexFile = Path.Join(Environment.GetEnvironmentVariable("HOME"), "site", "wwwroot", indexFile);
            }
            log.LogInformation($"index.html path: {indexFile}.");
            return new ContentResult
            {
                Content = File.ReadAllText(indexFile),
                ContentType = "text/html",
            };
        }
        ```

4. Créez une fonction `negotiate` pour aider les clients à obtenir l’URL de connexion au service avec le jeton d’accès.
    ```bash
    func new -n negotiate -t HttpTrigger
    ```
    > [!NOTE]
    > Dans cet exemple, nous utilisons l’en-tête de l’identité de l’utilisateur [AAD](../app-service/configure-authentication-user-identities.md)`x-ms-client-principal-name` pour récupérer `userId`. Cela ne fonctionnera pas dans une fonction locale. Vous pouvez l’utiliser vide ou le modifier de manière à obtenir ou générer `userId` lorsque vous travaillez en local. Par exemple, permettez au client de taper un nom d’utilisateur et de le passer dans une requête comme `?user={$username}` lorsque vous appelez la fonction `negotiate` pour obtenir l’URL de connexion du service. Dans la fonction `negotiate`, définissez `userId` sur la valeur `{query.user}`.
    
    # <a name="javascript"></a>[JavaScript](#tab/javascript)
   - Mettez à jour `negotiate/function.json` et copiez les codes json suivants.
        ```json
        {
            "bindings": [
                {
                    "authLevel": "anonymous",
                    "type": "httpTrigger",
                    "direction": "in",
                    "name": "req"
                },
                {
                    "type": "http",
                    "direction": "out",
                    "name": "res"
                },
                {
                    "type": "webPubSubConnection",
                    "name": "connection",
                    "hub": "simplechat",
                    "userId": "{headers.x-ms-client-principal-name}",
                    "direction": "in"
                }
            ]
        }
        ```
   - Mettez à jour `negotiate/index.js` et copiez les codes suivants.
        ```js
        module.exports = function (context, req, connection) {
            context.res = { body: connection };
            context.done();
        };
        ```
   # <a name="c"></a>[C#](#tab/csharp)
   - Mettez à jour `negotiate.cs` et remplacez la fonction `Run` par les codes suivants.
        ```c#
        [FunctionName("negotiate")]
        public static WebPubSubConnection Run(
            [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req,
            [WebPubSubConnection(Hub = "simplechat", UserId = "{headers.x-ms-client-principal-name}")] WebPubSubConnection connection,
            ILogger log)
        {
            log.LogInformation("Connecting...");
            return connection;
        }
        ```

5. Créez une fonction `message` pour diffuser des messages clients via le service.
   ```bash
   func new -n message -t HttpTrigger
   ```

   > [!NOTE]
   > Cette fonction utilise `WebPubSubTrigger`. Toutefois, étant donné que le service est toujours en préversion, `WebPubSubTrigger` n’est pas intégré au modèle de la fonction. Nous utilisons `HttpTrigger` pour initialiser le modèle de fonction et pour modifier le type de déclencheur dans le code.

   # <a name="javascript"></a>[JavaScript](#tab/javascript)
   - Mettez à jour `message/function.json` et copiez les codes json suivants.
        ```json
        {
            "bindings": [
                {
                    "type": "webPubSubTrigger",
                    "direction": "in",
                    "name": "data",
                    "hub": "simplechat",
                    "eventName": "message",
                    "eventType": "user"
                },
                {
                    "type": "webPubSub",
                    "name": "actions",
                    "hub": "simplechat",
                    "direction": "out"
                }
            ]
        }
        ```
   - Mettez à jour `message/index.js` et copiez les codes suivants.
        ```js
        module.exports = async function (context, data) {
            context.bindings.actions = {
                "actionName": "sendToAll",
                "data": `[${context.bindingData.request.connectionContext.userId}] ${data}`,
                "dataType": context.bindingData.dataType
            };
            // UserEventResponse directly return to caller
            var response = { 
                "data": '[SYSTEM] ack.',
                "dataType" : "text"
            };
            return response;
        };
        ```

   # <a name="c"></a>[C#](#tab/csharp)
   - Mettez à jour `message.cs` et remplacez la fonction `Run` par les codes suivants.
        ```c#
        [FunctionName("message")]
        public static async Task<UserEventResponse> Run(
            [WebPubSubTrigger(WebPubSubEventType.User, "message")] UserEventRequest request,
            BinaryData data,
            WebPubSubDataType dataType,
            [WebPubSub(Hub = "simplechat")] IAsyncCollector<WebPubSubAction> actions)
        {
            await actions.AddAsync(WebPubSubAction.CreateSendToAllAction(
                BinaryData.FromString($"[{request.ConnectionContext.UserId}] {message.ToString()}"),
                dataType
            );
            return new UserEventResponse
            {
                Data = BinaryData.FromString("[SYSTEM] ack"),
                DataType = WebPubSubDataType.Text
            };
        }
        ```

6. Ajoutez la page unique du client `index.html` dans le dossier racine du projet et copiez le contenu ci-dessous.
    ```html
    <html>
        <body>
            <h1>Azure Web PubSub Serverless Chat App</h1>
            <div id="login"></div>
            <p></p>
            <input id="message" placeholder="Type to chat...">
            <div id="messages"></div>
            <script>
                (async function () {
                    let authenticated = window.location.href.includes('?authenticated=true');
                    if (!authenticated) {
                        // auth
                        let login = document.querySelector("#login");
                        let link = document.createElement('a');
                        link.href = `${window.location.origin}/.auth/login/aad?post_login_redirect_url=/api/index?authenticated=true`;
                        link.text = "login";
                        login.appendChild(link);
                    }
                    else {
                        // negotiate
                        let messages = document.querySelector('#messages');
                        let res = await fetch(`${window.location.origin}/api/negotiate`, {
                            credentials: "include"
                        });
                        let url = await res.json();
                        // connect
                        let ws = new WebSocket(url.url);
                        ws.onopen = () => console.log('connected');
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
                    }
                })();
            </script>
        </body>
    </html>
    ```

    # <a name="javascript"></a>[JavaScript](#tab/javascript)

    # <a name="c"></a>[C#](#tab/csharp)
    Dans la mesure où le projet C# compile des fichiers dans un autre dossier de sortie, vous devez mettre à jour votre `*.csproj` pour que la page de contenu soit insérée.
    ```xml
    <ItemGroup>
        <None Update="index.html">
            <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
        </None>
    </ItemGroup>
    ``

## Create and Deploy the Azure Function App

Before you can deploy your function code to Azure, you need to create 3 resources:
* A resource group, which is a logical container for related resources.
* A storage account, which is used to maintain state and other information about your functions.
* A function app, which provides the environment for executing your function code. A function app maps to your local function project and lets you group functions as a logical unit for easier management, deployment and sharing of resources.

Use the following commands to create these items. 

1. If you haven't done so already, sign in to Azure:

    ```bash
    az login
    ```

1. Créez un groupe de ressources, ou vous pouvez ignorer cette étape en réutilisant l’un des services Azure Web PubSub :

    ```bash
    az group create -n WebPubSubFunction -l <REGION>
    ```

1. Créez un compte de stockage universel dans votre groupe de ressources et votre région :

    ```bash
    az storage account create -n <STORAGE_NAME> -l <REGION> -g WebPubSubFunction
    ```

1. Créer l’application de fonction dans Azure :

    # <a name="javascript"></a>[JavaScript](#tab/javascript)

    ```bash
    az functionapp create --resource-group WebPubSubFunction --consumption-plan-location <REGION> --runtime node --runtime-version 12 --functions-version 3 --name <FUNCIONAPP_NAME> --storage-account <STORAGE_NAME>
    ```

    # <a name="c"></a>[C#](#tab/csharp)

    ```bash
    az functionapp create --resource-group WebPubSubFunction --consumption-plan-location <REGION> --runtime dotnet --functions-version 3 --name <FUNCIONAPP_NAME> --storage-account <STORAGE_NAME>
    ```

1. Déployez le projet de fonction sur Azure :

    Après avoir créé votre application de fonction dans Azure, vous êtes prêt à déployer votre projet Functions local à l’aide de la commande [func azure functionapp publish](./../azure-functions/functions-run-local.md).

    ```bash
    func azure functionapp publish <FUNCIONAPP_NAME>
    ```
1. Configurez `WebPubSubConnectionString` pour l’application de fonction :

   En premier lieu, recherchez votre ressource Web PubSub dans le **portail Azure**, et copiez la chaîne de connexion sous **Clés**. Ensuite, accédez aux paramètres de l’application de fonction dans **Portail Azure** -> **Paramètres** -> **Configuration**. Ajoutez un nouvel élément sous **Paramètres de l’application**, avec le nom égal à `WebPubSubConnectionString`, et la valeur correspondant à la chaîne de connexion de votre ressource Web PubSub.

## <a name="configure-the-web-pubsub-service-event-handler"></a>Configurer le `Event Handler` du service Web PubSub

Dans cet exemple, nous utilisons `WebPubSubTrigger` pour écouter les demandes de service en amont. Web PubSub doit donc connaître les informations de point de terminaison de la fonction pour envoyer des demandes de clients cibles. De plus, Azure Function App nécessite une clé système pour la sécurité des méthodes de webhook relatives aux extensions. Dans l’étape précédente, après avoir déployé l’application de fonction avec les fonctions `message`, nous avons pu obtenir la clé système.

Accédez au **portail Azure** -> Recherchez votre ressource d’application de fonction -> **Clés d’application** -> **Clés système** ->  **`webpubsub_extension`** . Copiez la valeur au format suivant : `<APP_KEY>`.

:::image type="content" source="media/quickstart-serverless/func-keys.png" alt-text="Capture d’écran des clés système d’obtention de la clé.":::

Définissez `Event Handler` dans le service Azure Web PubSub. Accédez au **portail Azure** -> Recherchez votre ressource Web PubSub -> **Paramètres**. Ajoutez un nouveau mappage de paramètres de hub à la fonction en cours d’utilisation comme indiqué ci-dessous. Remplacez `<FUNCTIONAPP_NAME>` et `<APP_KEY>` par vos propres valeurs.

   - Nom du hub : `simplechat`
   - Modèle d’URL : **https://<NOM_APPLICATION_DE_FONCTION>.azurewebsites.net/runtime/webhooks/webpubsub?code=<CLÉ_APPLICATION>**
   - Modèle d’événement utilisateur : *
   - Événements système : (inutile de les configurer dans cet exemple)

:::image type="content" source="media/quickstart-serverless/set-event-handler.png" alt-text="Capture d’écran de la définition du gestionnaire d’événements.":::

> [!NOTE]
> Si vous exécutez les fonctions en local, vous pouvez exposer l’URL de la fonction avec [ngrok](https://ngrok.com/download) à l’aide de la commande `ngrok http 7071` après le démarrage de la fonction et configurer le `Event Handler` du service Web PubSub avec l’URL `https://<NGROK_ID>.ngrok.io/runtime/webhooks/webpubsub`. 

## <a name="configure-to-enable-client-authentication"></a>Configurer l’activation de l’authentification client

Accédez au **portail Azure** -> Recherchez votre ressource d’application de fonction -> **Authentification**. Cliquez sur **`Add identity provider`** Configurez **Paramètres d’authentification App Service** sur **Autoriser l’accès non authentifié**, de manière à permettre aux utilisateurs anonymes de visiter la page d’index du client avant de les rediriger pour l’authentification. **Enregistrez**.

Ici, nous choisissons `Microsoft` comme fournisseur d’identification, lequel utilisera `x-ms-client-principal-name` comme `userId` dans la fonction `negotiate`. Vous pouvez configurer d’autres fournisseurs d’identité en suivant les liens ci-dessous. N’oubliez pas de mettre à jour la valeur `userId` en conséquence dans la fonction `negotiate`.

* [Microsoft(Azure AD)](../app-service/configure-authentication-provider-aad.md)
* [Facebook](../app-service/configure-authentication-provider-facebook.md)
* [Google](../app-service/configure-authentication-provider-google.md)
* [Twitter](../app-service/configure-authentication-provider-twitter.md)

## <a name="try-the-application"></a>Tester l’application

Maintenant, vous pouvez tester votre page à partir de votre application de fonction `https://<FUNCTIONAPP_NAME>.azurewebsites.net/api/index`. Regardez la capture d’écran ci-dessous.
1. Cliquez sur `login` pour vous authentifier.
2. Tapez un message dans la zone de saisie pour commencer la conversation.

Dans la fonction de message, nous allons diffuser le message de l’appelant vers tous les clients, puis retourner le message `[SYSTEM] ack` à l’appelant. Nous pouvons donc savoir d’après la capture d’écran de l’exemple de conversation ci-dessous que les 4 premiers messages proviennent du client actuel et que les 2 derniers messages proviennent d’un autre client.

:::image type="content" source="media/quickstart-serverless/chat-sample.png" alt-text="Capture d’écran de l’exemple de conversation.":::

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous ne pensez pas continuer à utiliser cette application, supprimez toutes les ressources créées dans le cadre de cette documentation en procédant de la façon suivante afin d’éviter des frais :

1. Dans le portail Azure, sélectionnez **Groupes de ressources** tout à gauche, puis sélectionnez le groupe de ressources que vous avez créé. Si vous préférez, vous pouvez utiliser la zone de recherche pour trouver le groupe de ressources grâce à son nom.

1. Dans la fenêtre qui s’ouvre, sélectionnez le groupe de ressources, puis sélectionnez **Supprimer le groupe de ressources**.

1. Dans la nouvelle fenêtre, tapez le nom du groupe de ressources à supprimer, puis sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez appris à exécuter une application de conversation serverless. À présent, vous pouvez commencer à créer votre propre application. 

> [!div class="nextstepaction"]
> [Liaisons Azure Web PubSub pour Azure Functions](https://azure.github.io/azure-webpubsub/references/functions-bindings)

> [!div class="nextstepaction"]
> [Démarrage rapide : Créer un salon de conversation simple avec Azure Web PubSub](https://azure.github.io/azure-webpubsub/getting-started/create-a-chat-app/js-handle-events)

> [!div class="nextstepaction"]
> [Explorer d’autres exemples Azure Web PubSub](https://github.com/Azure/azure-webpubsub/tree/main/samples)
