---
title: 'Tutoriel : Créer une application de notification serverless à l’aide du service Azure Web PubSub et d’Azure Functions'
description: Tutoriel pour découvrir comment utiliser le service Azure Web PubSub et Azure Functions pour créer une application de notification serverless.
author: JialinXin
ms.author: jixin
ms.service: azure-web-pubsub
ms.topic: tutorial
ms.date: 11/01/2021
ms.openlocfilehash: 3fb4c5dbbc8ea073962cd7e0edb3e53c4c9920d5
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132494023"
---
# <a name="tutorial-create-a-serverless-notification-app-with-azure-functions-and-azure-web-pubsub-service"></a>Tutoriel : Créer une application de notification serverless avec Azure Functions et le service Azure Web PubSub

Le service Azure Web PubSub vous aide à créer des applications web de messagerie en temps réel à l’aide de WebSockets. Azure Functions est une plateforme serverless qui vous permet d’exécuter votre code sans gérer d’infrastructures. Dans ce tutoriel, vous apprenez à utiliser le service Azure Web PubSub et Azure Functions pour construire une application serverless avec une messagerie en temps réel dans le cadre de scénarios de notification. 

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Créer une application de notification serverless
> * Utiliser des liaisons d’entrée et de sortie de fonction Web PubSub
> * Exécuter les exemples de fonctions localement
> * Déployer la fonction sur l’application de fonction Azure

## <a name="prerequisites"></a>Prérequis

# <a name="javascript"></a>[JavaScript](#tab/javascript)

* Un éditeur de code, comme [Visual Studio Code](https://code.visualstudio.com/).

* [Node.js](https://nodejs.org/en/download/), version 10.x.
   > [!NOTE]
   > Pour plus d’informations sur les versions prises en charge de Node.js, consultez la [documentation sur les versions du runtime d’Azure Functions](../azure-functions/functions-versions.md#languages).

* [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools#installing) (v3 ou supérieure de préférence) pour exécuter les applications Azure Function localement et les déployer vers Azure.

* [Interface de ligne de commande Azure (Azure CLI)](/cli/azure) pour gérer les ressources Azure.

# <a name="c"></a>[C#](#tab/csharp)

* Un éditeur de code, comme [Visual Studio Code](https://code.visualstudio.com/).

* [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools#installing) (v3 ou supérieure de préférence) pour exécuter les applications Azure Function localement et les déployer vers Azure.

* [Interface de ligne de commande Azure (Azure CLI)](/cli/azure) pour gérer les ressources Azure.

---

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [create-instance-portal](includes/create-instance-portal.md)]

## <a name="create-and-run-the-functions-locally"></a>Créer et exécuter les fonctions localement

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
                    "hub": "notification",
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
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
            [WebPubSubConnection(Hub = "notification")] WebPubSubConnection connection,
            ILogger log)
        {
            log.LogInformation("Connecting...");

            return connection;
        }
        ```

5. Créez une fonction `notification` avec laquelle générer des notifications `TimerTrigger`.
   ```bash
    func new -n notification -t TimerTrigger
    ```
    # <a name="javascript"></a>[JavaScript](#tab/javascript)
   - Mettez à jour `notification/function.json` et copiez les codes json suivants.
        ```json
        {
            "bindings": [
                {
                "name": "myTimer",
                "type": "timerTrigger",
                "direction": "in",
                "schedule": "*/10 * * * * *"
                },
                {
                "type": "webPubSub",
                "name": "actions",
                "hub": "notification",
                "direction": "out"
                }
            ]
        }
        ```
   - Mettez à jour `notification/index.js` et copiez les codes suivants.
        ```js
        module.exports = function (context, myTimer) {
            context.bindings.actions = {
                "actionName": "sendToAll",
                "data": `[DateTime: ${new Date()}] Temperature: ${getValue(22, 1)}\xB0C, Humidity: ${getValue(40, 2)}%`,
                "dataType": "text"
            }
            context.done();
        };

        function getValue(baseNum, floatNum) {
            return (baseNum + 2 * floatNum * (Math.random() - 0.5)).toFixed(3);
        }
        ```
   # <a name="c"></a>[C#](#tab/csharp)
   - Mettez à jour `notification.cs` et remplacez la fonction `Run` par les codes suivants.
        ```c#
        [FunctionName("notification")]
        public static async Task Run([TimerTrigger("*/10 * * * * *")]TimerInfo myTimer, ILogger log,
            [WebPubSub(Hub = "notification")] IAsyncCollector<WebPubSubAction> actions)
        {
            await actions.AddAsync(new SendToAllAction
            {
                Data = BinaryData.FromString($"[DateTime: {DateTime.Now}] Temperature: {GetValue(23, 1)}{'\xB0'}C, Humidity: {GetValue(40, 2)}%"),
                DataType = WebPubSubDataType.Text
            });
        }

        private static string GetValue(double baseNum, double floatNum)
        {
            var rng = new Random();
            var value = baseNum + floatNum * 2 * (rng.NextDouble() - 0.5);
            return value.ToString("0.000");
        }
        ``` 

6. Ajoutez la page unique du client `index.html` dans le dossier racine du projet et copiez le contenu ci-dessous.
    ```html
    <html>
        <body>
        <h1>Azure Web PubSub Notification</h1>
        <div id="messages"></div>
        <script>
            (async function () {
                let messages = document.querySelector('#messages');
                let res = await fetch(`${window.location.origin}/api/negotiate`);
                let url = await res.json();
                let ws = new WebSocket(url.url);
                ws.onopen = () => console.log('connected');

                ws.onmessage = event => {
                    let m = document.createElement('p');
                    m.innerText = event.data;
                    messages.appendChild(m);
                };
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
    ```

7. Configurer et exécuter l’application de fonction Azure

    - Dans le navigateur, ouvrez le **portail Azure** et vérifiez que l’instance de service Web PubSub que vous avez déployée a été créée. Accédez à l’instance.
    - Sélectionnez **Clés** et copiez la chaîne de connexion.

    :::image type="content" source="media/quickstart-serverless/copy-connection-string.png" alt-text="Capture d’écran de la copie de la chaîne de connexion Web PubSub.":::

    Exécutez la commande ci-dessous dans le dossier de la fonction pour définir la chaîne de connexion du service. Remplacez `<connection-string`> par votre valeur si nécessaire.

    ```bash
    func settings add WebPubSubConnectionString "<connection-string>"
    ```

    > [!NOTE]
    > Le `TimerTrigger` utilisé dans l’exemple a une dépendance sur Stockage Azure, mais vous pouvez utiliser l’émulateur de stockage local lorsque la fonction est exécutée localement. Si vous rencontrez des erreurs telles que `There was an error performing a read operation on the Blob Storage Secret Repository. Please ensure the 'AzureWebJobsStorage' connection string is valid.`, vous devez télécharger et activer l’[émulateur de stockage](../storage/common/storage-use-emulator.md).

    Vous pouvez maintenant exécuter votre fonction locale par commande ci-dessous.

    ```bash
    func start
    ```

    Et en vérifiant les journaux d’exécution, vous pouvez visiter la page statique de votre hôte local en visitant `https://localhost:7071/api/index`.

## <a name="deploy-function-app-to-azure"></a>Déployer l’application de fonction dans Azure

Avant de déployer le code de votre fonction dans Azure, vous devez créer 3 ressources :
* Un groupe de ressources, qui est un conteneur logique pour les ressources associées.
* Un compte de stockage, qui sert à conserver l’état et d’autres informations sur vos fonctions.
* Une application de fonction, qui fournit l’environnement d’exécution de votre code de fonction. Une application de fonction est mappée à votre projet de fonction local. Elle vous permet de regrouper les fonctions en tant qu’unité logique pour faciliter la gestion, le déploiement et le partage des ressources.

Utilisez les commandes suivantes pour créer ces éléments. 

1. Si vous ne l’avez pas déjà fait, connectez-vous à Azure :

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

    Après avoir créé votre application de fonction dans Azure, vous êtes prêt à déployer votre projet Functions local à l’aide de la commande [func azure functionapp publish](../azure-functions/functions-run-local.md).

    ```bash
    func azure functionapp publish <FUNCIONAPP_NAME> --publish-local-settings
    ```

    > [!NOTE]
    > Ici, nous déployons les paramètres locaux `local.settings.json` avec le paramètre de commande `--publish-local-settings`. Si vous utilisez l’Émulateur de stockage Microsoft Azure, vous pouvez taper `no` pour ne pas remplacer cette valeur sur Azure après le message d’invite : `App setting AzureWebJobsStorage is different between azure and local.settings.json, Would you like to overwrite value in azure? [yes/no/show]`. En outre, vous pouvez mettre à jour les paramètres de l’application de fonction dans le **portail Azure** -> **Paramètres** -> **Configuration**.

1. Maintenant, vous pouvez vérifier votre site depuis votre application Azure Function en accédant à l’URL `https://<FUNCIONAPP_NAME>.azurewebsites.net/api/index`.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous ne pensez pas continuer à utiliser cette application, supprimez toutes les ressources créées dans le cadre de cette documentation en procédant de la façon suivante afin d’éviter des frais :

1. Dans le portail Azure, sélectionnez **Groupes de ressources** tout à gauche, puis sélectionnez le groupe de ressources que vous avez créé. Si vous préférez, vous pouvez utiliser la zone de recherche pour trouver le groupe de ressources grâce à son nom.

1. Dans la fenêtre qui s’ouvre, sélectionnez le groupe de ressources, puis sélectionnez **Supprimer le groupe de ressources**.

1. Dans la nouvelle fenêtre, tapez le nom du groupe de ressources à supprimer, puis sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez appris à exécuter une application de conversation serverless. À présent, vous pouvez commencer à créer votre propre application. 

> [!div class="nextstepaction"]
> [Tutoriel : Créer un salon de conversation simple avec Azure Web PubSub](https://azure.github.io/azure-webpubsub/getting-started/create-a-chat-app/js-handle-events)

> [!div class="nextstepaction"]
> [Liaisons Azure Web PubSub pour Azure Functions](https://azure.github.io/azure-webpubsub/references/functions-bindings)

> [!div class="nextstepaction"]
> [Explorer d’autres exemples Azure Web PubSub](https://github.com/Azure/azure-webpubsub/tree/main/samples)