---
title: Créer une fonction C# à partir de la ligne de commande – Azure Functions
description: Apprenez à créer une fonction à partir de la ligne de commande, puis à publier le projet local sur un hébergement serverless dans Azure Functions.
ms.date: 09/14/2021
ms.topic: quickstart
ms.custom:
- devx-track-csharp
- devx-track-azurecli
- devx-track-azurepowershell
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./create-first-function-cli-csharp-ieux
zone_pivot_groups: runtime-version-programming-functions
ms.openlocfilehash: e025362dfb992c1c9471ccae602412bcd21cb98e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131031464"
---
# <a name="quickstart-create-a-c-function-in-azure-from-the-command-line"></a>Démarrage rapide : Créer une fonction C# dans Azure à partir de la ligne de commande

[!INCLUDE [functions-runtime-version-dotnet](../../includes/functions-runtime-version-dotnet.md)]

Dans cet article, vous utilisez les outils ligne de commande pour créer une fonction C# qui répond aux requêtes HTTP. Après avoir testé le code localement, vous le déployez dans l’environnement serverless d’Azure Functions.

[!INCLUDE [functions-dotnet-execution-model](../../includes/functions-dotnet-execution-model.md)]

Le fait de suivre ce guide de démarrage rapide entraîne une faible dépense de quelques cents USD tout au plus dans votre compte Azure.

Si vous souhaitez consulter une version de cet article adaptée à Visual Studio Code, [cliquez ici](create-first-function-vs-code-csharp.md).

## <a name="configure-your-local-environment"></a>Configurer votre environnement local

Avant de commencer la lecture cet article, vous devez disposer des éléments suivants :

::: zone pivot="programming-runtime-functions-v3"
[!INCLUDE [functions-cli-dotnet-prerequisites](../../includes/functions-cli-dotnet-prerequisites.md)]
::: zone-end
::: zone pivot="programming-runtime-functions-v4"
# <a name="in-process"></a>[In-process](#tab/in-process)

+ [SDK .NET 6.0](https://dotnet.microsoft.com/download)

+ [Azure Functions Core Tools](./functions-run-local.md#v2) version 4.x.

+ Un des outils suivants pour créer des ressources Azure :

    + [Azure CLI](/cli/azure/install-azure-cli) version 2.4 ou ultérieure.

    + Le [module Azure Az PowerShell](/powershell/azure/install-az-ps) version 5.9.0 ou ultérieure.

# <a name="isolated-process"></a>[Processus isolé](#tab/isolated-process)

+ [SDK .NET 6.0](https://dotnet.microsoft.com/download/dotnet/6.0)

+ [Kit SDK .NET Core 3.1](https://dotnet.microsoft.com/download). Exigé par le processus de génération.

+ [Azure Functions Core Tools](./functions-run-local.md#v2) version 4.x.

+ Un des outils suivants pour créer des ressources Azure :

    + [Azure CLI](/cli/azure/install-azure-cli) version 2.4 ou ultérieure.

    + Le [module Azure Az PowerShell](/powershell/azure/install-az-ps) version 5.9.0 ou ultérieure.
---
::: zone-end

Vous avez aussi besoin d’un compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

### <a name="prerequisite-check"></a>Vérification du prérequis

Vérifiez vos prérequis, selon que vous utilisez Azure CLI ou Azure PowerShell pour la création de ressources Azure :

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

+ Dans une fenêtre de terminal ou de commande, exécutez `func --version` pour vérifier que vous disposez d’Azure Functions Core Tools version 3.x.

+ Exécutez `dotnet --list-sdks` pour vérifier que les versions requises sont installées.

+ Exécutez `az --version` pour vérifier que vous disposez d’Azure CLI version 2.4 ou ultérieure.

+ Exécutez `az login` pour vous connecter à Azure et vérifier que l’abonnement est actif.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

+ Dans une fenêtre de terminal ou de commande, exécutez `func --version` pour vérifier que vous disposez d’Azure Functions Core Tools version 3.x.

+ Exécutez `dotnet --list-sdks` pour vérifier que les versions requises sont installées.

+ Exécutez `(Get-Module -ListAvailable Az).Version` et vérifiez la version 5.0 ou ultérieure.

+ Exécutez `Connect-AzAccount` pour vous connecter à Azure et vérifier que l’abonnement est actif.

---

## <a name="create-a-local-function-project"></a>Créer un projet de fonction local

Dans Azure Functions, un projet de fonction est un conteneur pour une ou plusieurs fonctions qui répondent chacune à un déclencheur spécifique. Toutes les fonctions d’un projet partagent les mêmes configurations locale et d’hébergement. Dans cette section, vous créez un projet de fonction qui contient une seule fonction.

1. Exécutez la commande `func init`, de la façon suivante, pour créer un projet Functions dans un dossier nommé *LocalFunctionProj* avec le runtime spécifié :

    # <a name="in-process"></a>[In-process](#tab/in-process)

    ```console
    func init LocalFunctionProj --dotnet
    ```

    # <a name="isolated-process"></a>[Processus isolé](#tab/isolated-process)

    ```console
    func init LocalFunctionProj --worker-runtime dotnet-isolated
    ```
    ---

1. Accédez au dossier du projet :

    ```console
    cd LocalFunctionProj
    ```

    Ce dossier contient divers fichiers pour le projet, notamment des fichiers config nommés [local.settings.json](functions-develop-local.md#local-settings-file) et [host.json](functions-host-json.md). Dans la mesure où *local.settings.json* peut contenir des secrets téléchargés à partir d’Azure, le fichier est exclu du contrôle de code source par défaut dans le fichier *.gitignore*.

1. Ajoutez une fonction à votre projet à l’aide de la commande suivante, où l’argument `--name` est le nom unique de votre fonction (HttpExample) et où l’argument `--template` spécifie le déclencheur de la fonction (HTTP).

    ```console
    func new --name HttpExample --template "HTTP trigger" --authlevel "anonymous"
    ```

    `func new` crée un fichier de code HttpExample.cs.

### <a name="optional-examine-the-file-contents"></a>(Facultatif) Examiner le contenu du fichier

Si vous le souhaitez, vous pouvez passer à [Exécuter la fonction localement](#run-the-function-locally) et examiner le contenu du fichier plus tard.

#### <a name="httpexamplecs"></a>HttpExample.cs

Le code de fonction généré à partir du modèle dépend du type de projet C# compilé.

# <a name="in-process"></a>[In-process](#tab/in-process)

*HttpExample.cs* contient une méthode `Run` qui reçoit des données de requête dans la variable `req` de type [HttpRequest](/dotnet/api/microsoft.aspnetcore.http.httprequest), décorée avec **HttpTriggerAttribute** qui définit le comportement du déclencheur.

:::code language="csharp" source="~/functions-docs-csharp/http-trigger-template/HttpExample.cs":::

L’objet retourné est un [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult) qui retourne un message de réponse sous la forme d’un [OkObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.okobjectresult) (200) ou d’un [BadRequestObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.badrequestobjectresult) (400).

# <a name="isolated-process"></a>[Processus isolé](#tab/isolated-process)

*HttpExample.cs* contient une méthode `Run` qui reçoit des données de requête dans la variable `req` de type [HttpRequestData](/dotnet/api/microsoft.azure.functions.worker.http.httprequestdata), décorée avec **HttpTriggerAttribute** qui définit le comportement du déclencheur. En raison du modèle de processus isolé,    `HttpRequestData` est une représentation du réel `HttpRequest` et non de l’objet de requête lui-même.

:::code language="csharp" source="~/functions-docs-csharp/http-trigger-isolated/HttpExample.cs":::

L’objet de retour est un objet [HttpResponseData](/dotnet/api/microsoft.azure.functions.worker.http.httpresponsedata) qui contient les données retournées à la réponse HTTP.

---

Pour en savoir plus, voir [Déclencheurs et liaisons HTTP Azure Functions](./functions-bindings-http-webhook.md?tabs=csharp).

## <a name="run-the-function-locally"></a>Exécuter la fonction localement

1. Exécutez votre fonction en démarrant l’hôte du runtime d’Azure Functions local à partir du dossier *LocalFunctionProj* :

    ```
    func start
    ```

    Vers la fin de la sortie, les lignes suivantes doivent s’afficher :

    <pre>
    ...

    Now listening on: http://0.0.0.0:7071
    Application started. Press Ctrl+C to shut down.

    Http Functions:

            HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
    ...

    </pre>

    >[!NOTE]
    > Si HttpExample n’apparaît pas comme indiqué ci-dessus, cela signifie probablement que vous avez démarré l’hôte à partir d’un emplacement autre que le dossier racine du projet. Dans ce cas, utilisez **Ctrl**+**C** pour arrêter l’hôte, accédez au dossier racine du projet, puis réexécutez la commande précédente.

1. Copiez l’URL de votre fonction `HttpExample` à partir de cette sortie vers un navigateur :

    # <a name="in-process"></a>[In-process](#tab/in-process)

     Pour l’URL de la fonction, ajoutez la chaîne de requête `?name=<YOUR_NAME>`, en effectuant l’URL complète, par exemple `http://localhost:7071/api/HttpExample?name=Functions`. Le navigateur doit afficher un message de réponse qui renvoie la valeur de votre chaîne de requête. Le terminal dans lequel vous avez démarré votre projet affiche également une sortie de journal quand vous effectuez des requêtes.

    # <a name="isolated-process"></a>[Processus isolé](#tab/isolated-process)

    Accédez à l’URL de la fonction et vous devriez recevoir un message _d’accueil à Azure Functions_.

    ---

1. Quand vous avez terminé, utilisez **Ctrl**+**C**, puis choisissez `y` pour arrêter l’hôte Functions.

[!INCLUDE [functions-create-azure-resources-cli](../../includes/functions-create-azure-resources-cli.md)]

4. Créer l’application de fonction dans Azure :

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli/in-process)

    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location <REGION> --runtime dotnet --functions-version 3 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    La commande [az functionapp create](/cli/azure/functionapp#az_functionapp_create) crée l’application de fonction dans Azure.

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli/isolated-process)

    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location <REGION> --runtime dotnet-isolated --functions-version 3 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```

    La commande [az functionapp create](/cli/azure/functionapp#az_functionapp_create) crée l’application de fonction dans Azure.

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell/in-process)

    ```azurepowershell
    New-AzFunctionApp -Name <APP_NAME> -ResourceGroupName AzureFunctionsQuickstart-rg -StorageAccount <STORAGE_NAME> -Runtime dotnet -FunctionsVersion 3 -Location '<REGION>'
    ```

    L’applet de commande [New-AzFunctionApp](/powershell/module/az.functions/new-azfunctionapp) crée l’application de fonction dans Azure.

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell/isolated-process)

    ```azurepowershell
    New-AzFunctionApp -Name <APP_NAME> -ResourceGroupName AzureFunctionsQuickstart-rg -StorageAccount <STORAGE_NAME> -Runtime dotnet-isolated -FunctionsVersion 3 -Location '<REGION>'
    ```

    L’applet de commande [New-AzFunctionApp](/powershell/module/az.functions/new-azfunctionapp) crée l’application de fonction dans Azure.

    ---

    ::: zone pivot="programming-runtime-functions-v4"
    > [!NOTE]
    > Cette commande crée une application de fonction avec version 3.x du runtime Azure Functions. La commande `func azure functionapp publish` que vous exécuterez ultérieurement mettra à jour l’application vers la version 4.x.
    ::: zone-end

    Dans l’exemple précédent, remplacez `<STORAGE_NAME>` par le nom du compte que vous avez utilisé à l’étape précédente, puis remplacez `<APP_NAME>` par le nom global unique qui vous convient. `<APP_NAME>` représente également le domaine DNS par défaut pour l’application de fonction.

    Cette commande crée une application de fonction qui s’exécute dans votre runtime de langage spécifié dans le [Plan Consommation Azure Functions](consumption-plan.md), qui est gratuit pour l’utilisation faite ici. La commande provisionne également une instance d’Azure Application Insights associée dans le même groupe de ressources, avec laquelle vous pouvez superviser votre application de fonction et visualiser les journaux. Pour plus d’informations, consultez [Surveiller l’exécution des fonctions Azure](functions-monitoring.md). L’instance n’entraîne aucun coût tant que vous ne l’activez pas.

[!INCLUDE [functions-publish-project-cli](../../includes/functions-publish-project-cli.md)]

## <a name="invoke-the-function-on-azure"></a>Appeler la fonction sur Azure

Dans la mesure où votre fonction utilise un déclencheur HTTP et prend en charge des requêtes GET, vous l’appelez en adressant une requête HTTP à son URL. Il est plus facile d’effectuer cette opération dans un navigateur.

# <a name="in-process"></a>[In-process](#tab/in-process)

Copiez l’**URL d’appel** complète affichée au sein de la sortie de la commande de publication dans la barre d’adresse d’un navigateur, en ajoutant le paramètre de requête `?name=Functions`. Quand vous accédez à cette URL, le navigateur doit afficher une sortie similaire à celle générée au moment de l’exécution locale de la fonction.

# <a name="isolated-process"></a>[Processus isolé](#tab/isolated-process)

Copiez l’**URL d’appel** complète affichée dans la sortie de la commande de publication dans la barre d’adresse d’un navigateur. Quand vous accédez à cette URL, le navigateur doit afficher une sortie similaire à celle générée au moment de l’exécution locale de la fonction.

---

[!INCLUDE [functions-streaming-logs-cli-qs](../../includes/functions-streaming-logs-cli-qs.md)]

[!INCLUDE [functions-cleanup-resources-cli](../../includes/functions-cleanup-resources-cli.md)]

## <a name="next-steps"></a>Étapes suivantes

# <a name="in-process"></a>[In-process](#tab/in-process)

> [!div class="nextstepaction"]
> [Se connecter au Stockage File d’attente Azure](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-csharp&tabs=in-process)

# <a name="isolated-process"></a>[Processus isolé](#tab/isolated-process)

> [!div class="nextstepaction"]
> [Se connecter au Stockage File d’attente Azure](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-csharp&tabs=isolated-process)

---
