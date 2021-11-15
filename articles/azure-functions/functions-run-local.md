---
title: Utiliser Azure Functions Core Tools
description: Apprenez à coder et à tester des fonctions Azure à partir de l’invite de commandes ou du terminal sur votre ordinateur local avant de les exécuter dans Azure Functions.
ms.assetid: 242736be-ec66-4114-924b-31795fd18884
ms.topic: conceptual
ms.date: 10/05/2021
ms.custom: devx-track-csharp, 80e4ff38-5174-43
ms.openlocfilehash: 8512df7ba32e7fabee5d666ca119197da7d82d6b
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2021
ms.locfileid: "132028398"
---
# <a name="work-with-azure-functions-core-tools"></a>Utiliser Azure Functions Core Tools

Azure Functions Core Tools vous permet de développer et de tester vos fonctions sur votre ordinateur local à partir de l’invite de commandes ou du terminal. Vos fonctions locales peuvent être connectées aux services Azure actifs, et vous pouvez déboguer vos fonctions sur votre ordinateur local à l’aide du runtime Functions complet. Vous pouvez même déployer une application de fonction sur votre abonnement Azure.

[!INCLUDE [Don't mix development environments](../../includes/functions-mixed-dev-environments.md)]

Développez des fonctions sur votre ordinateur local et publiez-les sur Azure à l’aide de Core Tools en suivant ces étapes de base :

> [!div class="checklist"]
> * [Installez Core Tools et les dépendances.](#v2)
> * [Créez un projet d’application de fonction à partir d’un modèle spécifique au langage.](#create-a-local-functions-project)
> * [Inscrivez les extensions de liaison et de déclencheur.](#register-extensions)
> * [Définissez le stockage et les autres connexions.](#local-settings)
> * [Créez une fonction à partir d’un déclencheur et d’un modèle spécifique au langage.](#create-func)
> * [Exécutez la fonction localement.](#start)
> * [Publiez le projet sur Azure.](#publish)

## <a name="prerequisites"></a>Prérequis

Azure Functions Core Tools dépend d’[Azure CLI](/cli/azure/install-azure-cli) ou d’[Azure PowerShell](/powershell/azure/install-az-ps) pour l’authentification auprès de votre compte Azure. Cela signifie que vous devez installer l’un de ces outils pour pouvoir [publier sur Azure](#publish) à partir d’Azure Functions Core Tools. 

## <a name="core-tools-versions"></a><a name="v2"></a>Versions de Core Tools

Il existe quatre versions d’Azure Functions Core Tools. La version que vous utilisez dépend de votre environnement de développement local, du [choix du langage](supported-languages.md) et du niveau de prise en charge requis.

Choisissez un onglet version ci-dessous pour en savoir plus sur chaque version spécifique et pour obtenir des instructions d’installation détaillées :

# <a name="version-4x"></a>[Version 4.x](#tab/v4)

Prend en charge la [version 4.x](functions-versions.md) du runtime Functions. Ces versions prennent en charge Windows, macOS et Linux, et utilisent des gestionnaires de package spécifiques à la plateforme ou npm pour l’installation. Il s’agit de la version recommandée du runtime Functions et des outils principaux.

# <a name="version-3x"></a>[Version 3.x](#tab/v3)

Prend en charge la [version 3.x](functions-versions.md) du runtime Azure Functions. Ces versions prennent en charge Windows, macOS et Linux, et utilisent des gestionnaires de package spécifiques à la plateforme ou npm pour l’installation. 

# <a name="version-2x"></a>[Version 2.x](#tab/v2)

Prend en charge la [version 2.x](functions-versions.md) du runtime Azure Functions. Ces versions prennent en charge Windows, macOS et Linux, et utilisent des gestionnaires de package spécifiques à la plateforme ou npm pour l’installation. 

# <a name="version-1x"></a>[Version 1.x](#tab/v1) 

Prend en charge la version 1.x du runtime Azure Functions. Cette version des outils est uniquement prise en charge sur les ordinateurs Windows et est installée à partir d’un [package npm](https://www.npmjs.com/package/azure-functions-core-tools).

---

Vous ne pouvez installer qu’une seule version de Core Tools sur un ordinateur donné.  Sauf indication contraire, les exemples de cet article concernent la version 3.x.

## <a name="install-the-azure-functions-core-tools"></a>Installer Azure Functions Core Tools

[Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools) inclut une version du même runtime qu’Azure Functions, que vous pouvez exécuter sur votre ordinateur de développement local. Il fournit également des commandes pour créer des fonctions, se connecter à Azure et déployer des projets de fonction.

À partir de la version 2.x, les outils de base s’exécutent sur [Windows](?tabs=windows#v2), [macOS](?tabs=macos#v2) et [Linux](?tabs=linux#v2).

# <a name="windows"></a>[Windows](#tab/windows/v4)

Les étapes suivantes utilisent un programme d’installation Windows (MSI) pour installer Core Tools v4.x. Pour plus d’informations sur les autres programmes d’installation basés sur des packages, consultez le [fichier Lisezmoi des outils principaux](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#windows).

Téléchargez et exécutez le programme d’installation de Core Tools, selon votre version de Windows :

- [v4.x – Windows 64 bits](https://go.microsoft.com/fwlink/?linkid=2174087) (Recommandé. Le [débogage de Visual Studio Code](functions-develop-vs-code.md#debugging-functions-locally) nécessite la version 64 bits.)
- [v4.x – Windows 32 bits](https://go.microsoft.com/fwlink/?linkid=2174159)

# <a name="windows"></a>[Windows](#tab/windows/v3)

Les étapes suivantes utilisent un programme d’installation Windows (MSI) pour installer Core Tools v3.x. Pour plus d’informations sur les autres programmes d’installation basés sur des packages, consultez le [fichier Lisezmoi des outils principaux](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#windows).

Téléchargez et exécutez le programme d’installation de Core Tools, selon votre version de Windows :

- [v3.x – Windows 64 bits](https://go.microsoft.com/fwlink/?linkid=2135274) (Recommandé. Le [débogage de Visual Studio Code](functions-develop-vs-code.md#debugging-functions-locally) nécessite la version 64 bits.)
- [v3.x – Windows 32 bits](https://go.microsoft.com/fwlink/?linkid=2135275)

# <a name="windows"></a>[Windows](#tab/windows/v2)

L’installation de la version 2. x des outils principaux requiert actuellement NPM. Vous pouvez également [utiliser Chocolatey pour installer le package](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#azure-functions-core-tools).

1. Si vous ne l’avez pas déjà fait, [installez Node.js avec npm](https://nodejs.org/en/download/). 

1. Exécutez la commande npm suivante pour installer le package d’outils principaux :

    ```
    npm install -g azure-functions-core-tools@2 --unsafe-perm true
    ```

# <a name="windows"></a>[Windows](#tab/windows/v1)

Si vous devez installer la version 1. x des outils principaux, consultez le [référentiel GitHub](https://github.com/Azure/azure-functions-core-tools/blob/v1.x/README.md#installing) pour plus d’informations.

# <a name="macos"></a>[macOS](#tab/macos/v4)

Les étapes suivantes utilisent Homebrew pour installer les Outils Core sur macOS.

1. Installez [Homebrew](https://brew.sh/), s’il n’est pas déjà installé.

1. Installez le package Outils Core :

    ```bash
    brew tap azure/functions
    brew install azure-functions-core-tools@4
    # if upgrading on a machine that has 2.x or 3.x installed:
    brew link --overwrite azure-functions-core-tools@4
    ```

# <a name="macos"></a>[macOS](#tab/macos/v3)

Les étapes suivantes utilisent Homebrew pour installer les Outils Core sur macOS.

1. Installez [Homebrew](https://brew.sh/), s’il n’est pas déjà installé.

1. Installez le package Outils Core :

    ```bash
    brew tap azure/functions
    brew install azure-functions-core-tools@3
    # if upgrading on a machine that has 2.x installed:
    brew link --overwrite azure-functions-core-tools@3
    ```

# <a name="macos"></a>[macOS](#tab/macos/v2)

Les étapes suivantes utilisent Homebrew pour installer les Outils Core sur macOS.

1. Installez [Homebrew](https://brew.sh/), s’il n’est pas déjà installé.

1. Installez le package Outils Core :

    ```bash
    brew tap azure/functions
    brew install azure-functions-core-tools@2
    ```

# <a name="macos"></a>[macOS](#tab/macos/v1)

La version 1. x des outils principaux n’est pas prise en charge sur macOS. Choisissez la version 2.x ou une version ultérieure sur macOS.

# <a name="linux"></a>[Linux](#tab/linux/v4)

[!INCLUDE [functions-core-tools-linux-install](../../includes/functions-core-tools-linux-install.md)]

5. Installez le package Outils Core :

    ```bash
    sudo apt-get update
    sudo apt-get install azure-functions-core-tools-4
    ```

# <a name="linux"></a>[Linux](#tab/linux/v3)

[!INCLUDE [functions-core-tools-linux-install](../../includes/functions-core-tools-linux-install.md)]

5. Installez le package Outils Core :

    ```bash
    sudo apt-get update
    sudo apt-get install azure-functions-core-tools-3
    ```

# <a name="linux"></a>[Linux](#tab/linux/v2)

[!INCLUDE [functions-core-tools-linux-install](../../includes/functions-core-tools-linux-install.md)]

5. Installez le package Outils Core :

    ```bash
    sudo apt-get update
    sudo apt-get install azure-functions-core-tools-2
    ```

# <a name="linux"></a>[Linux](#tab/linux/v1)

La version 1. x des outils principaux n’est pas prise en charge sur Linux. Choisissez la version 2.x ou une version ultérieure sur Linux.

---

## <a name="changing-core-tools-versions"></a>Modifier les versions des Outils principaux

Lorsque vous passez à une autre version des outils principaux, vous devez utiliser le même gestionnaire de package que l’installation d’origine pour passer à une autre version de package. Par exemple, si vous avez installé la version 2. x des outils principaux à l’aide de NPM, vous devez utiliser la commande suivante pour effectuer une mise à niveau vers la version 3. x :

```bash
npm install -g azure-functions-core-tools@3 --unsafe-perm true
```

Si vous avez utilisé Windows installer (MSI) pour installer les outils principaux sur Windows, vous devez désinstaller l’ancienne version de la fonction ajout/suppression de programmes avant d’installer une autre version.

## <a name="create-a-local-functions-project"></a>Créer un projet Functions local

Un répertoire de projet Functions contient les fichiers et dossiers suivants, quel que soit le langage : 

| Nom de fichier | Description |
| --- | --- |
| host.json | Pour plus d’informations, consultez la [référence host.json](functions-host-json.md). |
| local.settings.json | Paramètres utilisé par les outils de base lors de l’exécution locale, y compris les paramètres de l’application. Pour en savoir plus, consultez [paramètres locaux](#local-settings). |
| .gitignore | Empêche la publication accidentelle du fichier local.settings.json dans un référentiel Git. Pour en savoir plus, consultez [paramètres locaux](#local-settings).|
| .vscode\extensions.json | Fichier de paramètres utilisé lors de l’ouverture du dossier du projet dans Visual Studio Code.  |

Pour en savoir plus sur la structure du projet Functions, consultez le [Guide de développement Azure Functions](functions-reference.md#folder-structure).

Dans la fenêtre du terminal ou à partir d’une invite de commandes, exécutez la commande suivante pour créer le projet et le référentiel Git local :

```
func init MyFunctionProj
```

Cet exemple crée un projet Functions dans un nouveau dossier `MyFunctionProj`. Vous êtes invité à choisir un langage par défaut pour votre projet. 

Les considérations suivantes s’appliquent à l’initialisation d’un projet :

+ Si vous ne fournissez pas l’option `--worker-runtime` dans la commande, vous êtes invité à choisir votre langage. Pour plus d’informations, consultez les informations de [référence sur func init](functions-core-tools-reference.md#func-init).

+ Lorsque vous ne fournissez pas de nom de projet, le dossier actif est initialisé. 

+ Si vous envisagez de publier votre projet sur un conteneur Linux personnalisé, utilisez l’option `--dockerfile` pour vous assurer qu’un fichier Dockerfile est généré pour votre projet. Pour en savoir plus, consultez [Créer une fonction sur Linux en utilisant une image personnalisée](functions-create-function-linux-custom-image.md). 

Certains langages peuvent avoir des considérations supplémentaires :

# <a name="c"></a>[C\#](#tab/csharp)

+ Par défaut, la version 2.x et les versions ultérieures des outils Core créent les projets d’application de fonctions pour le runtime .NET en tant que [projets de classes C#](functions-dotnet-class-library.md) (.csproj). La version 3.x prend également en charge la création de fonctions qui [s’exécutent sur .NET 5.0 dans un processus isolé](dotnet-isolated-process-guide.md). Ces projets C#, qui peuvent être utilisés avec Visual Studio ou Visual Studio Code, sont compilés pendant le débogage et lors de la publication sur Azure. 

+ Utilisez le paramètre `--csx` si vous souhaitez travailler localement avec des fichiers de script C# (.csx). Il s’agit des mêmes fichiers que ceux que vous recevez lorsque vous créez des fonctions dans le Portail Azure et lorsque vous utilisez la version 1.x des outils de base. Pour plus d’informations, consultez les informations de [référence sur func ini](functions-core-tools-reference.md#func-init).

# <a name="java"></a>[Java](#tab/java)

+ Java utilise un archétype Maven pour créer le projet Functions local, ainsi que votre première fonction déclenchée par HTTP. Plutôt que d’utiliser `func init` et `func new`, suivez les étapes décrites dans le [démarrage rapide sur la ligne de commande](./create-first-function-cli-java.md).  

# <a name="javascript"></a>[JavaScript](#tab/node)

+ Pour utiliser une `--worker-runtime` d’une valeur de `node`, spécifiez `--language` comme `javascript`. 

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Il n’y a pas d’autres éléments à prendre en compte pour PowerShell.

# <a name="python"></a>[Python](#tab/python)

+ Vous devez exécuter toutes les commandes, y compris `func init`, dans un environnement virtuel. Pour en savoir plus, consultez [Créer et activer un environnement virtuel](create-first-function-cli-python.md#create-venv).

# <a name="typescript"></a>[TypeScript](#tab/ts)

+ Pour utiliser une `--worker-runtime` d’une valeur de `node`, spécifiez `--language` comme `javascript`.

+ Consultez la [section TypeScript dans la référence du développeur JavaScript](functions-reference-node.md#typescript) pour en savoir plus sur les comportements `func init` spécifiques à TypeScript. 

--- 

## <a name="register-extensions"></a>Inscrire des extensions

À partir de la version 2. x du runtime, les liaisons et les déclencheurs de fonctions sont implémentés en tant que packages d’extension .NET (NuGet). Pour les projets C# compilés, il vous suffit de référencer les packages d’extension NuGet pour les déclencheurs et les liaisons spécifiques que vous utilisez. Les liaisons HTTP et les déclencheurs de minuteur ne nécessitent pas d’extensions. 

Pour améliorer l’expérience de développement pour les projets non-C#, Functions vous permet de référencer un bundle d’extension avec version dans votre fichier host.json de projet. Les [packages d’extension](functions-bindings-register.md#extension-bundles) rendent toutes les extensions disponibles pour votre application et suppriment les risques de problèmes de compatibilité entre les extensions. Les packages d’extension éliminent également la nécessité d’installer le kit de développement logiciel .NET Core 3.1. et d’avoir à gérer le fichier extensions.csproj.

Les packages d’extension constituent l’approche recommandée pour les projets Functions autres que les projets conformes à C#. Pour ces projets, le paramètre de groupe d’extension est généré dans le fichier _host.json_ au cours de l’initialisation. Si cela fonctionne pour vous, vous pouvez ignorer toute cette section.  

### <a name="use-extension-bundles"></a>Utiliser les packs d’extensions

[!INCLUDE [Register extensions](../../includes/functions-extension-bundles.md)]

 En cas de prise en charge par votre langage, les packages d’extension doivent déjà être activés après l’appel de `func init`. Vous devez ajouter des packages d’extension au fichier host.json avant d’ajouter des liaisons au fichier function.json. Pour en savoir plus, consultez [Inscrire des extensions de liaison Azure Functions](functions-bindings-register.md#extension-bundles). 

### <a name="explicitly-install-extensions"></a>Installer des extensions de manière explicite

Il peut y avoir des cas dans un projet autre que .NET dans lesquels vous ne pouvez pas utiliser les packages d’extension, par exemple lorsque vous devez cibler la version spécifique d’une extension qui n’est pas dans le bundle. Dans ce cas, vous pouvez utiliser les outils de base pour installer localement les packages d’extension nécessaires à votre projet. Pour en savoir plus, consultez [Installer des extensions de manière explicite](functions-bindings-register.md#explicitly-install-extensions).

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

Par défaut, ces paramètres ne sont pas migrés automatiquement lorsque le projet est publié dans Azure. Utilisez l’option [`--publish-local-settings` ][func azure functionapp publish] lors de la publication pour vous assurer que ces paramètres sont ajoutés à l’application de fonction dans Azure. Les valeurs de la section `ConnectionStrings` ne sont jamais publiées.

Ces valeurs de paramètres d’application de fonction peuvent aussi être lues dans votre code en tant que variables d’environnement. Pour plus d’informations, consultez la section Variables d’environnement de ces rubriques de référence spécifiques à une langue :

* [C# précompilé](functions-dotnet-class-library.md#environment-variables)
* [Script C# (.csx)](functions-reference-csharp.md#environment-variables)
* [Java](functions-reference-java.md#environment-variables)
* [JavaScript](functions-reference-node.md#environment-variables)
* [PowerShell](functions-reference-powershell.md#environment-variables)
* [Python](functions-reference-python.md#environment-variables)

Si aucune chaîne de connexion de stockage valide n’est définie pour [`AzureWebJobsStorage`] et que l’émulateur n’est pas utilisé, le message d’erreur suivant s’affiche :

> Valeur manquante pour AzureWebJobsStorage dans local.settings.json. Cette valeur est nécessaire pour tous les déclencheurs autres que HTTP. Vous pouvez exécuter 'func azure functionapp fetch-app-settings \<functionAppName\>' ou spécifier une chaîne de connexion dans local.settings.json.

### <a name="get-your-storage-connection-strings"></a>Obtenir vos chaînes de connexion de stockage

Même si vous utilisez l’Émulateur de stockage Microsoft Azure pour le développement, vous pouvez exécuter localement votre configuration avec une connexion de stockage réelle. Si vous avez déjà [créé un compte de stockage](../storage/common/storage-account-create.md), vous pouvez obtenir une chaîne de connexion de stockage valide de plusieurs façons :

# <a name="portal"></a>[Portail](#tab/portal)

1. Dans le [Azure portal], recherchez et sélectionnez **Comptes de stockage**. 

    ![Sélectionner des comptes de stockage à partir du Portail Azure](./media/functions-run-local/select-storage-accounts.png)
  
1.  Sélectionnez votre compte de stockage, sélectionnez **Clés d’accès** dans **Paramètres**, puis copiez une des valeurs **Chaîne de connexion**.

    ![Copier une chaîne de connexion à partir du portail Azure](./media/functions-run-local/copy-storage-connection-portal.png)

# <a name="core-tools"></a>[Core Tools](#tab/azurecli)

À la racine du projet, utilisez une des commandes suivantes pour télécharger la chaîne de connexion à partir d’Azure :

  + Téléchargez tous les paramètres à partir d’une application de fonction existante :

    ```
    func azure functionapp fetch-app-settings <FunctionAppName>
    ```

  + Obtenez la chaîne de connexion pour un compte de stockage spécifique :

    ```
    func azure storage fetch-connection-string <StorageAccountName>
    ```

    Si vous n’êtes pas encore connecté à Azure, vous êtes invité à le faire. Ces commandes remplacent les paramètres existants dans le fichier local.settings.json. Pour en savoir plus, consultez les commandes [`func azure functionapp fetch-app-settings`](functions-core-tools-reference.md#func-azure-functionapp-fetch-app-settings) et [`func azure storage fetch-connection-string`](functions-core-tools-reference.md#func-azure-storage-fetch-connection-string).

# <a name="storage-explorer"></a>[Explorateur Stockage](#tab/storageexplorer)

1. Exécutez [Explorateur Stockage Azure](https://storageexplorer.com/). 

1. Dans l’**Explorateur**, développez votre abonnement, puis développez **Comptes de stockage**.

1. Sélectionnez votre compte de stockage et copiez la chaîne de connexion principale ou secondaire.

    ![Copier une chaîne de connexion à partir de l’Explorateur Stockage Azure](./media/functions-run-local/storage-explorer.png)

---

## <a name="create-a-function"></a><a name="create-func"></a>Créer une fonction

Pour créer une fonction dans un projet existant, exécutez la commande suivante :

```
func new
```

Dans la version 3.x/2.x, lorsque vous exécutez `func new`, vous êtes invité à choisir un modèle dans le langage par défaut de votre application de fonction. Ensuite, vous êtes invité à choisir le nom de votre fonction. Dans la version 1.x, vous êtes également invité à choisir le langage. 

Vous pouvez également spécifier le nom et le modèle de la fonction dans la commande `func new`. L’exemple suivant utilise l’option `--template` pour créer un déclencheur HTTP nommé `MyHttpTrigger` :

```
func new --template "Http Trigger" --name MyHttpTrigger
```

Cet exemple crée un déclencheur de file d’attente de stockage nommé `MyQueueTrigger` :

```
func new --template "Queue Trigger" --name MyQueueTrigger
```

Pour plus d’informations, consultez la commande [`func new` ](functions-core-tools-reference.md#func-new).

## <a name="run-functions-locally"></a><a name="start"></a>Exécuter des fonctions localement

Pour exécuter un projet Functions, vous exécutez l’hôte Functions à partir du répertoire racine de votre projet. L’hôte active les déclencheurs pour toutes les fonctions du projet. La commande [`start` ](functions-core-tools-reference.md#func-start) varie selon le langage de votre projet.

# <a name="c"></a>[C\#](#tab/csharp)

```
func start
```

# <a name="java"></a>[Java](#tab/java)

```
mvn clean package 
mvn azure-functions:run
```

# <a name="javascript"></a>[JavaScript](#tab/node)

```
func start
```


# <a name="powershell"></a>[PowerShell](#tab/powershell)

```
func start
```

# <a name="python"></a>[Python](#tab/python)

```
func start
```
Cette commande doit être [exécutée dans un environnement virtuel](./create-first-function-cli-python.md).

# <a name="typescript"></a>[TypeScript](#tab/ts)

```
npm install
npm start     
```

---

>[!NOTE]  
> La version 1.x du runtime Functions, en revanche, exige `func host start`. Pour en savoir plus, consultez [Référence Azure Functions Core Tools](functions-core-tools-reference.md?tabs=v1#func-start). 

Quand l’hôte Functions démarre, il génère l’URL des fonctions déclenchées par HTTP, comme dans l’exemple suivant :

<pre>
Found the following functions:
Host.Functions.MyHttpTrigger

Job host started
Http Function MyHttpTrigger: http://localhost:7071/api/MyHttpTrigger
</pre>

>[!IMPORTANT]
>Lors d’une exécution locale, l’autorisation n’est pas appliquée pour les points de terminaison HTTP. Cela signifie que toutes les demandes HTTP locales sont gérées de manière `authLevel = "anonymous"`. Pour plus d’informations, consultez l’article sur la [liaison HTTP](functions-bindings-http-webhook-trigger.md#authorization-keys).

### <a name="passing-test-data-to-a-function"></a>Transmission de données de test à une fonction

Pour tester vos fonctions localement, vous [démarrez l’hôte Functions](#start) et vous appelez des points de terminaison sur le serveur local avec des requêtes HTTP. Le point de terminaison que vous appelez varie selon le type de fonction.

>[!NOTE]
> Les exemples de cette rubrique utilisent l’outil cURL pour envoyer des requêtes HTTP à partir du terminal ou d’une invite de commandes. Vous pouvez utiliser un outil de votre choix pour envoyer les requêtes HTTP au serveur local. L’outil cURL est disponible par défaut sur les systèmes Linux et Windows 10 Build 17063 et versions ultérieures. Avec les anciennes versions de Windows, vous devez d’abord télécharger et installer [l’outil cURL](https://curl.haxx.se/).

Pour des informations plus générales sur le test de fonctions, consultez [Stratégies permettant de tester votre code dans Azure Functions](functions-test-a-function.md).

#### <a name="http-and-webhook-triggered-functions"></a>Fonctions déclenchées par HTTP et par Webhook

Vous appelez le point de terminaison suivant pour exécuter localement des fonctions déclenchées par HTTP et par Webhook :

```
http://localhost:{port}/api/{function_name}
```

Vérifiez que vous utilisez le même nom de serveur et le même port que celui où l’hôte Functions écoute. Vous voyez cela dans la sortie générée lors du démarrage de l’hôte Functions. Vous pouvez appeler cette URL en utilisant n’importe quelle méthode HTTP prise en charge par le déclencheur.

La commande cURL suivante déclenche la fonction de démarrage rapide `MyHttpTrigger` à partir d’une demande GET avec le paramètre _name_ passé dans la chaîne de requête.

```
curl --get http://localhost:7071/api/MyHttpTrigger?name=Azure%20Rocks
```

L’exemple suivant est la même fonction appelée à partir d’une demande POST en passant _name_ dans le corps de la demande :

# <a name="bash"></a>[Bash](#tab/bash)
```bash
curl --request POST http://localhost:7071/api/MyHttpTrigger --data '{"name":"Azure Rocks"}'
```
# <a name="cmd"></a>[Cmd](#tab/cmd)
```cmd
curl --request POST http://localhost:7071/api/MyHttpTrigger --data "{'name':'Azure Rocks'}"
```
---

Notez que vous pouvez passer des requêtes GET depuis un navigateur en passant des données dans la chaîne de requêtes. Pour toutes les autres méthodes HTTP, vous devez utiliser cURL, Fiddler, Postman ou un outil de test HTTP similaire qui prend en charge les demandes POST.

#### <a name="non-http-triggered-functions"></a>Fonctions non déclenchées via HTTP

Pour toutes les fonctions autres que les déclencheurs HTTP et EventGrid, vous pouvez tester vos fonctions localement avec REST en appelant un point de terminaison spécial appelé _point de terminaison d’administration_. L’appel de ce point de terminaison au moyen d’une requête HTTP POST sur le serveur local déclenche la fonction. 

Pour tester des fonctions Event Grid déclenchées localement, consultez [Tests locaux avec une application web de visionneuse](functions-bindings-event-grid-trigger.md#local-testing-with-viewer-web-app).

Vous pouvez éventuellement passer des données de test à l’exécution dans le corps de la requête POST. Cette fonctionnalité est similaire à l’onglet **Test** dans le portail Azure.

Vous appelez le point de terminaison d’administrateur suivant pour déclencher des fonctions non-HTTP :

```
http://localhost:{port}/admin/functions/{function_name}
```

Pour passer des données de test au point de terminaison d’administrateur d’une fonction, vous devez fournir les données dans le corps d’un message de requête POST. Le corps du message doit avoir le format JSON suivant :

```JSON
{
    "input": "<trigger_input>"
}
```

La valeur de `<trigger_input>` contient des données dans un format attendu par la fonction. L’exemple cURL suivant est une demande POST adressée à une fonction `QueueTriggerJS`. Dans ce cas, l’entrée est une chaîne qui est équivalente au message attendu dans la file d’attente.

# <a name="bash"></a>[Bash](#tab/bash)
```bash
curl --request POST -H "Content-Type:application/json" --data '{"input":"sample queue data"}' http://localhost:7071/admin/functions/QueueTrigger
```
# <a name="cmd"></a>[Cmd](#tab/cmd)
```bash
curl --request POST -H "Content-Type:application/json" --data "{'input':'sample queue data'}" http://localhost:7071/admin/functions/QueueTrigger
```
---

Lorsque vous appelez un point de terminaison d’administrateur sur votre application de fonction dans Azure, vous devez fournir une clé d’accès. Pour en savoir plus, consultez [Clés d’accès Function](functions-bindings-http-webhook-trigger.md#authorization-keys).

## <a name="publish-to-azure"></a><a name="publish"></a>Publication dans Azure

Les Azure Functions Core Tools prennent en charge trois types de déploiement :

| Type de déploiement | Commande | Description |
| ----- | ----- | ----- |
| Fichiers projet | [`func azure functionapp publish`](functions-core-tools-reference.md#func-azure-functionapp-publish) | Déploie les fichiers de projet de fonction directement dans votre application de fonction à l’aide d’un [déploiement zip](functions-deployment-technologies.md#zip-deploy). |
| Conteneur personnalisé | `func deploy` | Déploie votre projet dans une application de fonction Linux en tant que conteneur Docker personnalisé.  |
| Cluster Kubernetes | `func kubernetes deploy` | Déploie votre application de fonction Linux en tant que conteneur docker client sur un cluster Kubernetes. | 

### <a name="before-you-publish"></a>Avant de publier 

>[!IMPORTANT]
>L’[interface de ligne de commande Azure](/cli/azure/install-azure-cli) ou [Azure PowerShell](/powershell/azure/install-az-ps) doit être installé localement pour que vous puissiez publier sur Azure à partir de Core Tools.  

Un dossier de projet peut contenir des fichiers et des répertoires spécifiques à une langue qui ne doivent pas être publiés. Les éléments exclus sont listés dans un fichier .funcignore situé dans le dossier racine du projet.  

Vous devez avoir déjà [créé une application de fonction dans votre abonnement Azure](functions-cli-samples.md#create) où vous prévoyez de déployer votre code. Les projets qui nécessitent une compilation doivent être générés pour favoriser le déploiements des fichiers binaires.

Pour découvrir comment créer une application de fonction à partir de l’invite de commandes ou d’une fenêtre de terminal à l’aide d’Azure CLI ou d’Azure PowerShell, consultez [Créer une application de fonction pour une exécution serverless](./scripts/functions-cli-create-serverless.md). 

>[!IMPORTANT]
> Lorsque vous créez une application de fonction dans le portail Azure, elle utilise par défaut la version 3.x du runtime de Function. Pour que l’application de fonction utilise la version 1.x du runtime, suivez les instructions dans [Exécution sur la version 1.x](functions-versions.md#creating-1x-apps).
> Vous ne pouvez pas modifier la version du runtime pour une application de fonction qui possède des fonctions déjà existantes.


### <a name="deploy-project-files"></a><a name="project-file-deployment"></a>Déployer des fichiers de projet

Pour publier votre code local dans une application de fonction sur Azure, utilisez la commande `publish` :

```
func azure functionapp publish <FunctionAppName>
```

Les remarques suivantes s’appliquent à ce type de déploiement :

+ La publication remplace les fichiers existants dans l’application de fonction.

+ Utilisez l’option [`--publish-local-settings` ][func azure functionapp publish] pour créer automatiquement des paramètres d’application dans votre application de fonction en fonction des valeurs dans le fichier local.settings.js.  

+ Une [compilation distante](functions-deployment-technologies.md#remote-build) est effectuée sur les projets compilés. Ceci peut être contrôlé par l’option [`--no-build` ][func azure functionapp publish].  

+ Votre projet est déployé de sorte qu’il [s’exécute à partir du package de déploiement](run-functions-from-deployment-package.md). Pour désactiver ce mode de déploiement recommandé, utilisez l’option [`--nozip` ][func azure functionapp publish].

+ Java utilise Maven pour publier votre projet local sur Azure. Utilisez plutôt la commande suivante pour publier sur Azure : `mvn azure-functions:deploy`. Les ressources Azure sont créées lors du déploiement initial.

+ Vous obtiendrez une erreur si vous tentez de publier sur une application `<FunctionAppName>` qui n’existe pas dans votre abonnement. 

### <a name="kubernetes-cluster"></a>Cluster Kubernetes

Functions vous permet également de définir votre projet Functions pour qu’il s’exécute dans un conteneur Docker. Utilisez [`--docker` option][func init] de `func init` pour générer un fichier Docker pour votre langage. Ce fichier est ensuite utilisé lors de la création d’un conteneur à déployer. 

Les outils principaux peuvent être utilisés pour déployer votre projet en tant qu’image conteneur personnalisée sur un cluster Kubernetes. La commande que vous utilisez dépend du type d’échelle utilisé dans le cluster.  

La commande suivante utilise fichier Dockerfile pour générer un conteneur et le déployer sur un cluster Kubernetes. 

# <a name="keda"></a>[KEDA](#tab/keda)

```command
func kubernetes deploy --name <DEPLOYMENT_NAME> --registry <REGISTRY_USERNAME> 
```

Pour en savoir plus, consultez [Déploiement d’une application de fonction sur Kubernetes](functions-kubernetes-keda.md#deploying-a-function-app-to-kubernetes). 

# <a name="defaultknative"></a>[Default/KNative](#tab/default)

```command
func deploy --name <FUNCTION_APP> --platform kubernetes --registry <REGISTRY_USERNAME> 
```

Dans l’exemple ci-dessus, remplacez `<FUNCTION_APP>` par le nom de l’application de fonction dans Azure et `<REGISTRY_USERNAME>` par le nom de votre compte de registre, par exemple votre nom d’utilisateur Docker. Le conteneur est créé localement et transmis à votre compte de registre Docker avec un nom d’image basé sur `<FUNCTION_APP>`. Les outils en ligne de commande de Docker doivent être installés.

Pour plus d’informations, consultez la commande [`func deploy` ](functions-core-tools-reference.md#func-deploy).

---

Pour savoir comment publier un conteneur personnalisé sur Azure sans Kubernetes, consultez [Créer une fonction sur Linux à l’aide d’un conteneur personnalisé](functions-create-function-linux-custom-image.md).

## <a name="monitoring-functions"></a>Surveillance des fonctions

Il est recommandé de superviser l’exécution de vos fonctions par l’intégration à Azure Application Insights. Vous pouvez également diffuser des journaux d’exécution sur votre ordinateur local. Pour en savoir plus, consultez [Surveiller l’exécution des fonctions Azure](functions-monitoring.md).

### <a name="application-insights-integration"></a>Intégration d’Application Insights

L’intégration d’Application Insights doit être activée lorsque vous créez votre application de fonction dans Azure. Si, pour une raison quelconque, votre application de fonction n’est pas connectée à une instance Application Insights, il est facile d’effectuer cette intégration dans le portail Azure. Pour en savoir plus, consultez [Activer l'intégration d'Application Insights](configure-monitoring.md#enable-application-insights-integration).

### <a name="enable-streaming-logs"></a>Activer les journaux de diffusion en continu

Vous pouvez afficher un flux de fichiers journaux générés par vos fonctions dans une session de ligne de commande sur votre ordinateur local. 

[!INCLUDE [functions-streaming-logs-core-tools](../../includes/functions-streaming-logs-core-tools.md)]

Les journaux de diffusion en continu de ce type nécessitent que l’intégration d’Application Insights soit activée pour votre application de fonction.   


## <a name="next-steps"></a>Étapes suivantes

Découvrez comment développer, tester et publier des fonctions Azure en utilisant Azure Functions Core Tools [module Microsoft Learn](/learn/modules/develop-test-deploy-azure-functions-with-core-tools/). Azure Functions Core Tools est [open source et hébergé sur GitHub](https://github.com/azure/azure-functions-cli).  
Pour enregistrer un bogue ou une demande de fonctionnalité, [créez un problème GitHub](https://github.com/azure/azure-functions-cli/issues).

<!-- LINKS -->

[Azure Functions Core Tools]: https://www.npmjs.com/package/azure-functions-core-tools
[Azure portal]: https://portal.azure.com 
[Node.js]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows
[`FUNCTIONS_WORKER_RUNTIME`]: functions-app-settings.md#functions_worker_runtime
[AzureWebJobsStorage]: functions-app-settings.md#azurewebjobsstorage
[extension bundles]: functions-bindings-register.md#extension-bundles
[func azure functionapp publish]: functions-core-tools-reference.md?tabs=v2#func-azure-functionapp-publish
[func init]: functions-core-tools-reference.md?tabs=v2#func-init
