---
title: Créer une fonction C# à partir de la ligne de commande – Azure Functions
description: Apprenez à créer une fonction à partir de la ligne de commande, puis à publier le projet local sur un hébergement serverless dans Azure Functions.
ms.date: 10/03/2020
ms.topic: quickstart
ms.custom:
- devx-track-csharp
- devx-track-azurecli
- devx-track-azurepowershell
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: dc1f05ad07d9349b7b4906e60fcaf5b671435959
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131039408"
---
# <a name="quickstart-create-a-c-function-in-azure-from-the-command-line"></a>Démarrage rapide : Créer une fonction C# dans Azure à partir de la ligne de commande

> [!div class="op_single_selector" title1="Sélectionnez votre langage de fonction : "]
> - [C#](create-first-function-cli-csharp-ieux.md)
> - [Java](create-first-function-cli-java.md)
> - [JavaScript](create-first-function-cli-node.md)
> - [PowerShell](create-first-function-cli-powershell.md)
> - [Python](create-first-function-cli-python.md)
> - [TypeScript](create-first-function-cli-typescript.md)

Dans cet article, vous allez utiliser des outils en ligne de commande pour créer une fonction basée sur une bibliothèque de classes C# qui répond aux requêtes HTTP. Après avoir testé le code localement, vous le déployez dans l’environnement <abbr title="Environnement informatique de runtime dans lequel tous les détails du serveur sont transparents pour les développeurs d’applications, ce qui simplifie le processus de déploiement et de gestion du code.">serverless</abbr> d’ <abbr title="Service Azure qui fournit un environnement informatique serverless à faible coût pour les applications.">Azure Functions</abbr>.

Le fait de suivre ce guide de démarrage rapide entraîne une faible dépense de quelques cents USD tout au plus dans votre compte Azure.

Si vous souhaitez consulter une version de cet article adaptée à Visual Studio Code, [cliquez ici](create-first-function-vs-code-csharp.md).

## <a name="1-prepare-your-environment"></a>1. Préparation de votre environnement

+ Obtenez un <abbr title="Profil qui gère les informations de facturation pour l’utilisation d’Azure.">account</abbr> avec un <abbr title="Structure organisationnelle de base dans laquelle vous gérez les ressources dans Azure, généralement associée à une personne ou à un service au sein d’une organisation.">abonnement</abbr>. [Créez un compte gratuitement](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ Installer le [kit SDK .NET Core 3.1](https://dotnet.microsoft.com/download)

+ Installez [Azure Functions Core Tools](functions-run-local.md#v2) version 3.x.

+ Interface <abbr title="Ensemble d’outils en ligne de commande multiplateformes permettant d’utiliser des ressources Azure à partir de votre ordinateur de développement local comme alternative à l’utilisation du portail Azure.">Azure CLI</abbr> ou <abbr title="Module PowerShell qui fournit des commandes permettant d’utiliser des ressources Azure à partir de votre ordinateur de développement local comme alternative à l’utilisation du portail Azure.">Azure PowerShell</abbr> pour la création de ressources Azure :

    + [Azure CLI](/cli/azure/install-azure-cli) version 2.4 ou ultérieure.

    + Le [module Azure Az PowerShell](/powershell/azure/install-az-ps) version 5.9.0 ou ultérieure.

---

### <a name="2-verify-prerequisites"></a>2. Vérifier la configuration requise

Vérifiez vos prérequis, selon que vous utilisez Azure CLI ou Azure PowerShell pour créer des ressources Azure :

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

+ Dans une fenêtre de terminal ou de commande, exécutez `func --version` pour vérifier que vous disposez d’ <abbr title="Ensemble d’outils en ligne de commande permettant d’utiliser Azure Functions sur votre ordinateur local.">Azure Functions Core Tools</abbr> version 3.x.

+ **Exécutez** `az --version` pour vérifier que vous disposez d’Azure CLI version 2.4 ou ultérieure.

+ **Exécutez** `az login` pour vous connecter à Azure et vérifier que l’abonnement est actif.

+ **Exécutez** `dotnet --list-sdks` pour vérifier que le kit SDK .NET Core version 3.1.x est installé.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

+**Exécutez** `func --version` pour vérifier que vous disposez d’Azure Functions Core Tools version 3.x.

+ **Exécutez** `(Get-Module -ListAvailable Az).Version` et vérifiez la version 5.0 ou ultérieure.

+ **Exécutez** `Connect-AzAccount` pour vous connecter à Azure et vérifier que l’abonnement est actif.

+ **Exécutez** `dotnet --list-sdks` pour vérifier que le kit SDK .NET Core version 3.1.x est installé.

---

## <a name="3-create-a-local-function-project"></a>3. Créer un projet de fonction local

Dans cette section, vous allez créer un <abbr title="Conteneur logique pour une ou plusieurs fonctions individuelles qui peuvent être déployées et gérées ensemble.">projet Azure Functions</abbr> local en C#. Chaque fonction du projet répond à un <abbr title="Événement qui appelle le code de la fonction, par exemple une requête HTTP, un message de file d’attente ou une heure spécifique.">déclencheur</abbr>.

1. Exécutez la commande `func init` pour créer un projet Functions dans un dossier nommé *LocalFunctionProj* avec le runtime spécifié :

    ```csharp
    func init LocalFunctionProj --dotnet
    ```

1. **Exécutez** « cd LocalFunctionProj » pour accéder au <abbr title="Ce dossier contient divers fichiers pour le projet, notamment des fichiers config nommés local.settings.json et host.json. Dans la mesure où local.settings.json peut contenir des secrets téléchargés à partir d’Azure, le fichier est exclu du contrôle de code source par défaut dans le fichier .gitignore.">dossier du projet</abbr>.

    ```console
    cd LocalFunctionProj
    ```
    <br/>

1. Ajoutez une fonction à votre projet en utilisant la commande suivante :

    ```console
    func new --name HttpExample --template "HTTP trigger" --authlevel "anonymous"
    ```
    L’argument `--name` est le nom unique de votre fonction (HttpExample).

    L’argument `--template` spécifie le déclencheur de la fonction (HTTP).


    <br/>
    <details>
    <summary><strong>Facultatif : Code pour HttpExample.cs</strong></summary>

    *HttpExample.cs* contient une méthode `Run` qui reçoit des données de requête dans la variable `req` de type [HttpRequest](/dotnet/api/microsoft.aspnetcore.http.httprequest), décorée avec **HttpTriggerAttribute** qui définit le comportement du déclencheur.

    :::code language="csharp" source="~/functions-docs-csharp/http-trigger-template/HttpExample.cs":::

    L’objet retourné est un [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult) qui retourne un message de réponse sous la forme d’un [OkObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.okobjectresult) (200) ou d’un [BadRequestObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.badrequestobjectresult) (400). Pour en savoir plus, voir [Déclencheurs et liaisons HTTP Azure Functions](./functions-bindings-http-webhook.md?tabs=csharp).
    </details>

<br/>

---

## <a name="4-run-the-function-locally"></a>4. Exécuter la fonction localement

1. Exécutez votre fonction en démarrant l’hôte du runtime d’Azure Functions local à partir du dossier *LocalFunctionProj* :

    ```
    func start
    ```

    Vers la fin de la sortie, les lignes suivantes doivent s’afficher :

    <pre class="is-monospace is-size-small has-padding-medium has-background-tertiary has-text-tertiary-invert">
    ...

    Now listening on: http://0.0.0.0:7071
    Application started. Press Ctrl+C to shut down.

    Http Functions:

            HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
    ...

    </pre>

    <br/>
    <details>
    <summary><strong>Je ne vois pas HttpExample dans la sortie</strong></summary>

    Si HttpExample n’apparaît pas, vous avez probablement démarré l’hôte à partir d’un emplacement situé en dehors du dossier racine du projet. Dans ce cas, utilisez <kbd>Ctrl+C</kbd> pour arrêter l’hôte, accédez au dossier racine du projet, puis réexécutez la commande précédente.
    </details>

1. Copiez dans un navigateur l’URL de votre fonction **HttpExample** à partir de cette sortie, puis ajoutez la chaîne de requête **?name=<YOUR_NAME>** , pour obtenir une URL complète semblable à **http://localhost:7071/api/HttpExample?name=Functions** . Le navigateur doit afficher un message similaire à **Hello Functions** :

    ![Résultat de la fonction exécutée localement dans le navigateur](../../includes/media/functions-run-function-test-local-cli/function-test-local-browser.png)


1. Sélectionnez <kbd>Ctrl+C</kbd> et choisissez <kbd>y</kbd> pour arrêter l’hôte Functions.

<br/>

---

## <a name="5-create-supporting-azure-resources-for-your-function"></a>5. Créer des ressources Azure de prise en charge pour votre fonction

Avant de déployer le code de votre fonction dans Azure, vous devez créer un <abbr title="Conteneur logique pour des ressources Azure associées que vous pouvez gérer en tant qu’unité.">groupe de ressources</abbr>, un <abbr title="Compte qui contient tous vos objets de données de Stockage Azure. Le compte de stockage fournit un espace de noms unique pour vos données de stockage.">compte de stockage</abbr>et une <abbr title="Ressource cloud qui héberge des fonctions serverless dans Azure et qui fournit l’environnement de calcul sous-jacent dans lequel les fonctions s’exécutent.">application de fonction</abbr> en utilisant les commande suivantes :

1. Si vous ne l’avez pas déjà fait, connectez-vous à Azure :

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    ```azurecli
    az login
    ```


    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
    ```azurepowershell
    Connect-AzAccount
    ```


    ---

1. Créez un groupe de ressources nommé `AzureFunctionsQuickstart-rg` dans la région `westeurope`.

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

    ```azurecli
    az group create --name AzureFunctionsQuickstart-rg --location westeurope
    ```

    La commande [az group create](/cli/azure/group#az_group_create) crée un groupe de ressources. Vous créez généralement votre groupe de ressources et les ressources dans une <abbr title="Référence géographique à un centre de données Azure spécifique dans lequel des ressources sont allouées.">region</abbr> proche de chez vous, en utilisant une région disponible retournée par la commande `az account list-locations`.

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzResourceGroup -Name AzureFunctionsQuickstart-rg -Location westeurope
    ```


    ---

    Vous ne pouvez pas héberger des applications Linux et Windows dans le même groupe de ressources. Si vous disposez d’un groupe de ressources existant nommé `AzureFunctionsQuickstart-rg` avec une application de fonction Windows ou une application web, vous devez utiliser un autre groupe de ressources.

1. Créez un compte de Stockage Azure universel dans votre groupe de ressources et votre région :

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

    ```azurecli
    az storage account create --name <STORAGE_NAME> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
    ```


    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzStorageAccount -ResourceGroupName AzureFunctionsQuickstart-rg -Name <STORAGE_NAME> -SkuName Standard_LRS -Location westeurope
    ```


    ---

    Remplacez `<STORAGE_NAME>` par un nom qui vous convient et qui est <abbr title="Ce nom doit être unique pour tous les comptes de stockage utilisés par tous les clients Azure à l’échelle mondiale. Par exemple, vous pouvez utiliser une combinaison de votre nom personnel ou du nom de votre entreprise, du nom de l’application et d’un identificateur numérique, comme dans contosobizappstorage20.">unique dans le Stockage Azure</abbr>. Les noms doivent contenir entre 3 et 24 caractères, et comporter uniquement des lettres minuscules. `Standard_LRS` spécifie un compte universel, qui est [pris en charge par Functions](storage-considerations.md#storage-account-requirements).


1. Créez l’application de fonction dans Azure.
**Remplacez** « <STORAGE_NAME> » par le nom utilisé à l’étape précédente.
**Remplacez** « <APP_NAME> » par un nom global unique.

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime dotnet --functions-version 3 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```


    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzFunctionApp -Name <APP_NAME> -ResourceGroupName AzureFunctionsQuickstart-rg -StorageAccount <STORAGE_NAME> -Runtime dotnet -FunctionsVersion 3 -Location 'West Europe'
    ```


    ---

    Remplacez `<STORAGE_NAME>` par le nom du compte que vous avez utilisé à l’étape précédente.

    Remplacez `<APP_NAME>` par un <abbr title="Nom qui doit être unique pour tous les clients Azure à l’échelle mondiale. Par exemple, vous pouvez utiliser une combinaison de votre nom personnel ou du nom de votre organisation, du nom de l’application et d’un identificateur numérique, comme dans contoso-bizapp-func-20.">nom unique</abbr>. `<APP_NAME>` représente également le domaine DNS par défaut pour l’application de fonction.

    <br/>
    <details>
    <summary><strong>Quel est le coût des ressources provisionnées sur Azure ?</strong></summary>

    Cette commande crée une application de fonction qui s’exécute dans votre runtime de langage spécifié dans le [Plan de consommation Azure Functions](consumption-plan.md), qui est gratuit pour l’utilisation faite ici. La commande provisionne également une instance d’Azure Application Insights associée dans le même groupe de ressources, avec laquelle vous pouvez superviser votre application de fonction et visualiser les journaux. Pour plus d’informations, consultez [Surveiller l’exécution des fonctions Azure](functions-monitoring.md). L’instance n’entraîne aucun coût tant que vous ne l’activez pas.
    </details>

<br/>

---

## <a name="6-deploy-the-function-project-to-azure"></a>6. Déployer le projet de fonction sur Azure


**Copiez** « func azure funtionapp publish <APP_NAME> » sur votre terminal **Remplacez** `<APP_NAME>` par le nom de votre application.
**Exécuter**

```console
func azure functionapp publish <APP_NAME>
```

La commande `publish` affiche des résultats similaires à la sortie suivante (tronquée pour rester simple) :

<pre class="is-monospace is-size-small has-padding-medium has-background-tertiary has-text-tertiary-invert">
...

Getting site publishing info...
Creating archive for current directory...
Performing remote build for functions project.

...

Deployment successful.
Remote build succeeded!
Syncing triggers...
Functions in msdocs-azurefunctions-qs:
    HttpExample - [httpTrigger]
        Invoke url: https://msdocs-azurefunctions-qs.azurewebsites.net/api/httpexample
</pre>

<br/>

---

## <a name="7-invoke-the-function-on-azure"></a>7. Appeler la fonction sur Azure

Copiez l’**URL d’appel** complète affichée dans la sortie de la commande `publish` dans la barre d’adresse d’un navigateur. **Ajoutez** le paramètre de requête **&name=Functions**.

![Sortie de la fonction exécutée sur Azure dans un navigateur](../../includes/media/functions-run-remote-azure-cli/function-test-cloud-browser.png)

<br/>

---

## <a name="8-clean-up-resources"></a>8. Nettoyer les ressources

Si vous passez à l’[étape suivante](#next-steps) et que vous ajoutez une <abbr title="Connexion déclarative entre une fonction et d’autres ressources. Une liaison d’entrée fournit des données à la fonction ; une liaison de sortie fournit des données de la fonction à d’autres ressources.">liaison de sortie</abbr>, gardez toutes vos ressources en place pour tirer parti de ce que vous avez déjà fait.

Sinon, utilisez la commande suivante pour supprimer le groupe de ressources et toutes les ressources qu’il contient afin d’éviter les coûts supplémentaires.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
Remove-AzResourceGroup -Name AzureFunctionsQuickstart-rg
```

---

<br/>

---

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Se connecter à une file d’attente Stockage Azure](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-csharp)
