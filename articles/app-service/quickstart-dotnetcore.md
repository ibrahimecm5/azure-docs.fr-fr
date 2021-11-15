---
title: 'Démarrage rapie : Déployer une application web ASP.NET'
description: Découvrez comment exécuter des applications web dans Azure App Service en déployant votre première application ASP.NET.
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.topic: quickstart
ms.date: 10/26/2021
ms.custom: devx-track-csharp, mvc, devcenter, vs-azure, seodec18, contperf-fy21q1
zone_pivot_groups: app-service-ide
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./quickstart-dotnetcore-uiex
ms.openlocfilehash: 1f50ac2ef41186bc1799fce56ba6fd424f6fdd26
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131455989"
---
<!-- NOTES:

I'm a .NET developer who wants to deploy my web app to App Service. I may develop apps with
Visual Studio, Visual Studio for Mac, Visual Studio Code, or the .NET SDK/CLI. This article
should be able to guide .NET devs, whether they're app is .NET Core, .NET, or .NET Framework.

As a .NET developer, when choosing an IDE and .NET TFM - you map to various OS requirements.
For example, if you choose Visual Studio - you're developing the app on Windows, but you can still
target cross-platform with .NET 5.0.

| .NET / IDE         | Visual Studio | Visual Studio for Mac | Visual Studio Code | Command line   |
|--------------------|---------------|-----------------------|--------------------|----------------|
| .NET 5.0           | Windows       | macOS                 | Cross-platform     | Cross-platform |
| .NET Framework 4.8 | Windows       | N/A                   | Windows            | Windows        |

-->

# <a name="quickstart-deploy-an-aspnet-web-app"></a>Démarrage rapie : Déployer une application web ASP.NET

Dans ce guide de démarrage rapide, vous allez apprendre à créer et déployer votre première application web ASP.NET sur [Azure App Service](overview.md). App Service prend en charge différentes versions d’applications .NET et fournit un service d’hébergement web hautement évolutif appliquant des mises à jour correctives automatiques. Les applications web ASP.NET sont multiplateformes et peuvent être hébergées sur Linux ou Windows. Quand vous aurez terminé, vous disposerez d’un groupe de ressources Azure constitué d’un plan d’hébergement App Service et d’un service d’application avec une application web déployée.

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-ps"
<!-- markdownlint-enable MD044 -->

> [!NOTE]
> Azure PowerShell est recommandé pour la création d’applications sur la plateforme d’hébergement Windows. Pour créer des applications sur Linux, utilisez un autre outil, tel qu’[Azure CLI](quickstart-dotnetcore.md?pivots=development-environment-cli).

:::zone-end

## <a name="prerequisites"></a>Prérequis

:::zone target="docs" pivot="development-environment-vs"

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/dotnet).
- <a href="https://www.visualstudio.com/downloads" target="_blank">Visual Studio 2019</a> avec la charge de travail **Développement ASP.NET et web**.

    Si vous avez déjà installé Visual Studio 2019 :

    - Installez les dernières mises à jour dans Visual Studio en sélectionnant **Aide** > **Rechercher les mises à jour**.
    - Ajoutez la charge de travail en sélectionnant **Outils** > **Obtenir des outils et des fonctionnalités**.

:::zone-end

:::zone target="docs" pivot="development-environment-vscode"

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/dotnet).
- <a href="https://www.visualstudio.com/downloads" target="_blank">Visual Studio Code</a>.
- Extension <a href="https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack" target="_blank">Azure Tools</a>.

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

<a href="https://dotnet.microsoft.com/download/dotnet/5.0" target="_blank">Installez la dernière version du kit SDK .NET 5.0</a>.

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

<a href="https://dotnet.microsoft.com/download/dotnet-framework/net48" target="_blank">Installez .NET Framework 4.8 Developer Pack</a>.

> [!NOTE]
> Visual Studio Code est multiplateforme, ce qui n’est pas le cas de .NET Framework. Si vous développez des applications .NET Framework avec Visual Studio Code, envisagez d’utiliser un ordinateur Windows pour satisfaire les dépendances de build.

---

:::zone-end

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-cli"
<!-- markdownlint-enable MD044 -->

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/dotnet).
- <a href="/cli/azure/install-azure-cli" target="_blank">Interface de ligne de commande Azure</a>.
- Kit SDK .NET (avec runtime et CLI).

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

<a href="https://dotnet.microsoft.com/download/dotnet/5.0" target="_blank">Installez la dernière version du kit SDK .NET 5.0</a>.

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

<a href="https://dotnet.microsoft.com/download/dotnet/5.0" target="_blank">Installez le dernier kit SDK .NET 5.0. </a> et <a href="https://dotnet.microsoft.com/download/dotnet-framework/net48" target="_blank"> le .NET Framework 4.8 Developer Pack</a>.

> [!NOTE]
> Les composants [.NET CLI](/dotnet/core/tools) et .NET 5.0 sont tous deux multiplateformes, mais pas .NET Framework. Si vous développez des applications .NET Framework avec l’interface CLI .NET, envisagez d’utiliser un ordinateur Windows pour satisfaire les dépendances de build. .NET 5.0 est multiplateforme.

---

:::zone-end

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-ps"
<!-- markdownlint-enable MD044 -->

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/dotnet).
- <a href="/powershell/azure/install-az-ps" target="_blank">Azure PowerShell</a>.
- Kit SDK .NET (avec runtime et CLI).

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

<a href="https://dotnet.microsoft.com/download/dotnet/5.0" target="_blank">Installez la dernière version du kit SDK .NET 5.0</a>.

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

<a href="https://dotnet.microsoft.com/download/dotnet/5.0" target="_blank">Installez le dernier kit SDK .NET 5.0. </a> et <a href="https://dotnet.microsoft.com/download/dotnet-framework/net48" target="_blank"> le .NET Framework 4.8 Developer Pack</a>.

> [!NOTE]
> [Azure PowerShell](/powershell/azure/) et .NET 5.0 sont tous deux multiplateformes, mais pas .NET Framework. Si vous développez des applications .NET Framework avec l’interface CLI .NET, envisagez d’utiliser un ordinateur Windows pour satisfaire les dépendances de build.

---

:::zone-end

## <a name="create-an-aspnet-web-app"></a>Créez une application web ASP.NET

:::zone target="docs" pivot="development-environment-vs"

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

1. Ouvrez Visual Studio, puis sélectionnez **Créer un projet**.
1. Dans **Créer un projet**, recherchez et choisissez **Application web ASP.NET Core**, puis sélectionnez **Suivant**.
1. Dans **Configurer votre nouveau projet**, nommez l’application _MyFirstAzureWebApp_, puis sélectionnez **Suivant**.

   :::image type="content" source="media/quickstart-dotnet/configure-webapp-net.png" alt-text="Visual Studio : configurez l’application web ASP.NET 5.0." border="true":::

1. Sélectionnez **.NET Core 5.0 (version actuelle)** .
1. Assurez-vous que **Type d’authentification** est défini sur **Aucun**. Sélectionnez **Create** (Créer).

   :::image type="content" source="media/quickstart-dotnet/vs-additional-info-net50.png" alt-text="Visual Studio : informations supplémentaires lors de la sélection de .NET Core 5.0." border="true":::

1. Dans le menu Visual Studio, sélectionnez **Déboguer** > **Démarrer sans débogage** pour exécuter l’application web localement.

   :::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Visual Studio : ASP.NET Core 5.0, exécution locale." lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

1. Ouvrez Visual Studio, puis sélectionnez **Créer un projet**.
1. Dans **Créer un projet**, recherchez et choisissez **Application web ASP.NET (.NET Framework)** , puis sélectionnez **Suivant**.
1. Dans **Configurer votre nouveau projet**, nommez l’application _MyFirstAzureWebApp_, puis sélectionnez **Créer**.

   :::image type="content" source="media/quickstart-dotnet/configure-webapp-netframework48.png" alt-text="Visual Studio : configurez l’application web ASP.NET Framework 4.8." border="true":::

1. Sélectionnez le modèle **MVC**.
1. Vérifiez que l’option **Authentification** est définie sur **Aucune authentification**. Sélectionnez **Create** (Créer).

   :::image type="content" source="media/quickstart-dotnet/vs-mvc-no-auth-netframework48.png" alt-text="Visual Studio : sélectionnez le modèle MVC." border="true":::

1. Dans le menu Visual Studio, sélectionnez **Déboguer** > **Démarrer sans débogage** pour exécuter l’application web localement.

   :::image type="content" source="media/quickstart-dotnet/vs-local-webapp-netframework48.png" alt-text="Visual Studio : ASP.NET Framework 4.8, exécution locale." lightbox="media/quickstart-dotnet/vs-local-webapp-netframework48.png" border="true":::

---

:::zone-end

:::zone target="docs" pivot="development-environment-vscode"

Créez un dossier nommé _MyFirstAzureWebApp_, puis ouvrez-le dans Visual Studio Code. Ouvrez la fenêtre <a href="https://code.visualstudio.com/docs/editor/integrated-terminal" target="_blank">Terminal</a> et créez une application web .NET à l’aide de la commande [`dotnet new webapp`](/dotnet/core/tools/dotnet-new#web-options).

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

```dotnetcli
dotnet new webapp -f net5.0
```

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

```dotnetcli
dotnet new webapp --target-framework-override net48
```

> [!IMPORTANT]
> L'indicateur `--target-framework-override` est un remplacement de texte de forme libre du moniker de framework cible (TFM) pour le projet et *ne garantit pas* que le modèle de prise en charge existe ou qu’il est compilé. Vous pouvez uniquement générer et exécuter des applications .NET Framework sur Windows.

---

À partir du **Terminal** de Visual Studio Code, exécutez l’application localement à l’aide de la commande [`dotnet run`](/dotnet/core/tools/dotnet-run).

```dotnetcli
dotnet run
```

Ouvrez un navigateur web et accédez à l’application à l’adresse `https://localhost:5001`.


### <a name="net-50"></a>[.NET 5.0](#tab/net50)

Vous noterez que le modèle d’application web ASP.NET Core 5.0 s’affiche sur la page.

:::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Visual Studio Code : exécutez .NET 5.0 dans le navigateur localement." lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

Vous noterez que le modèle d’application web ASP.NET Framework 4.8 s’affiche sur la page.

:::image type="content" source="media/quickstart-dotnet/local-webapp-net48.png" alt-text="Visual Studio Code : exécutez .NET 4.8 dans le navigateur localement." lightbox="media/quickstart-dotnet/local-webapp-net48.png" border="true":::

---

:::zone-end

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-cli,development-environment-ps"
<!-- markdownlint-enable MD044 -->

Sur votre ordinateur, ouvrez une fenêtre de terminal dans un répertoire de travail. Créez une application web .NET à l’aide de la commande [`dotnet new webapp`](/dotnet/core/tools/dotnet-new#web-options), puis modifiez les répertoires dans l’application nouvellement créée.

<!-- Please keep the following commands in two lines instead of one && separated line. The latter doesn't work in PowerShell -->

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

```dotnetcli
dotnet new webapp -n MyFirstAzureWebApp --framework net5.0
cd MyFirstAzureWebApp
```

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

```dotnetcli
dotnet new webapp -n MyFirstAzureWebApp --target-framework-override net48
cd MyFirstAzureWebApp
```

> [!IMPORTANT]
> L'indicateur `--target-framework-override` est un remplacement de texte de forme libre du moniker de framework cible (TFM) pour le projet et *ne garantit pas* que le modèle de prise en charge existe ou qu’il est compilé. Vous pouvez uniquement créer des applications .NET Framework sur Windows.

---

À partir de la même session de terminal, exécutez l’application localement à l’aide de la commande [`dotnet run`](/dotnet/core/tools/dotnet-run).

```dotnetcli
dotnet run
```

Ouvrez un navigateur web et accédez à l’application à l’adresse `https://localhost:5001`.

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

Vous noterez que le modèle d’application web ASP.NET Core 5.0 s’affiche sur la page.

:::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Visual Studio Code : ASP.NET Core 5.0 dans le navigateur local." lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

Vous noterez que le modèle d’application web ASP.NET Framework 4.8 s’affiche sur la page.

:::image type="content" source="media/quickstart-dotnet/local-webapp-net48.png" alt-text="Visual Studio Code : ASP.NET Framework 4.8 dans le navigateur local." lightbox="media/quickstart-dotnet/local-webapp-net48.png" border="true":::

---

:::zone-end

## <a name="publish-your-web-app"></a>Publier votre application web

Pour publier votre application web, vous devez d’abord créer et configurer un nouveau service d’application sur lequel publier votre application.

Lors de la configuration du service d’application, vous allez créer :

- un nouveau [groupe de ressources](../azure-resource-manager/management/overview.md#terminology) pour contenir toutes les ressources Azure du service ;
- un nouveau [plan d’hébergement](overview-hosting-plans.md) qui spécifie l’emplacement, la taille et les fonctionnalités de la batterie de serveurs web qui héberge votre application.

Pour créer votre service d’application et publier votre application web, suivez ces étapes :

:::zone target="docs" pivot="development-environment-vs"

1. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le projet **MyFirstAzureWebApp**, puis sélectionnez **Publier**.
1. Dans **Publier**, sélectionnez **Azure**, puis **Suivant**.

    :::image type="content" source="media/quickstart-dotnet/vs-publish-target-Azure.png" alt-text="Visual Studio : publiez l’application web et ciblez Azure." border="true":::

1. Vos options varient si vous êtes déjà connecté à Azure et si vous avez un compte Visual Studio lié à un compte Azure. Sélectionnez **Ajouter un compte** ou **Connexion** pour vous connecter à votre abonnement Azure. Si vous êtes déjà connecté, sélectionnez le compte souhaité.

    :::image type="content" source="media/quickstart-dotnetcore/sign-in-Azure-vs2019.png" border="true" alt-text="Visual Studio : sélectionnez la boîte de dialogue de connexion à Azure.":::

1. Choisissez la **cible spécifique**, **Azure App Service (Linux)** ou **Azure App Service (Windows)** .

    > [!IMPORTANT]
    > Lorsque vous ciblez ASP.NET Framework 4.8, vous utilisez **Azure App Service (Windows)** .

1. À droite de **Instances App Service**, sélectionnez **+** .

    :::image type="content" source="media/quickstart-dotnetcore/publish-new-app-service.png" border="true" alt-text="Visual Studio : boîte de dialogue Nouvelle application App Service.":::

1. Pour **Abonnement**, acceptez l’abonnement qui est listé ou sélectionnez-en un nouveau dans la liste déroulante.
1. Pour **Groupe de ressources**, sélectionnez **Nouveau**. Dans **Nouveau nom du groupe de ressources**, entrez *myResourceGroup*, puis sélectionnez **OK**.
1. Pour **Plan d’hébergement**, sélectionnez **Nouveau**.
1. Dans la boîte de dialogue **Plan d’hébergement : Créer nouveau**, entrez les valeurs spécifiées dans le tableau suivant :

    | Paramètre          | Valeur suggérée          | Description                                                           |
    |------------------|--------------------------|-----------------------------------------------------------------------|
    | **Plan d’hébergement** | *MyFirstAzureWebAppPlan* | Nom du plan App Service.                                         |
    | **Lieu**     | *Europe Ouest*            | Centre de données dans lequel l’application web est hébergée.                           |
    | **Taille**         | *Gratuit*                   | Le [niveau tarifaire][app-service-pricing-tier] détermine les fonctionnalités d’hébergement. |

    :::image type="content" source="media/quickstart-dotnetcore/create-new-hosting-plan-vs2019.png" border="true" alt-text="Créer un plan d’hébergement":::

1. Dans **Nom**, entrez un nom d’application unique qui inclut uniquement les caractères valides `a-z`, `A-Z`, `0-9` et `-`. Vous pouvez accepter le nom unique généré automatiquement. L’URL de l’application web est `http://<app-name>.azurewebsites.net`, où `<app-name>` est le nom de votre application.
1. Sélectionnez **Créer** pour créer les ressources Azure.

    :::image type="content" source="media/quickstart-dotnetcore/web-app-name-vs2019.png" border="true" alt-text="Visual Studio : boîte de dialogue Créer les ressources d’application.":::

   Une fois l’Assistant terminé, les ressources Azure sont créées pour vous et vous êtes prêt à publier.

1. Sélectionnez **Terminer** pour fermer l’Assistant.
1. Dans la page **Publier**, sélectionnez **Publier**. Visual Studio génère, empaquète et publie l’application sur Azure, puis la démarre dans le navigateur par défaut.

    ### <a name="net-50"></a>[.NET 5.0](#tab/net50)

    L’application web ASP.NET Core 5.0 s’affiche sur la page.

    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="Visual Studio : Application web ASP.NET Core 5.0 dans Azure.":::

    ### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

    L’application web ASP.NET Framework 4.8 s’affiche sur la page.

    :::image type="content" source="media/quickstart-dotnet/vs-Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/vs-Azure-webapp-net48.png" border="true" alt-text="Visual Studio : Application web ASP.NET Framework 4.8 dans Azure.":::

    ---

:::zone-end

:::zone target="docs" pivot="development-environment-vscode"

Pour déployer votre application Web à l’aide de l’extension Azure Tools Visual Studio :

1. Dans Visual Studio Code, ouvrez la [**palette de commandes**](https://code.visualstudio.com/docs/getstarted/userinterface#_command-palette), <kbd>Ctrl</kbd>+<kbd>Maj</kbd>+<kbd>P</kbd>.
1. Recherchez et sélectionnez « Azure App Service : Déployer sur l’application Web ».
1. Répondez aux invites de la façon suivante :

    - Sélectionnez *MyFirstAzureWebApp* en tant que dossier à déployer.
    - Lorsque vous y êtes invité, sélectionnez **Ajouter une configuration**.
    - Si vous y êtes invité, connectez-vous à votre compte Azure existant.

    :::image type="content" source="media/quickstart-dotnet/vscode-sign-in-to-Azure.png" alt-text="Visual Studio Code : connectez-vous à Azure." border="true":::

    - Sélectionnez votre **abonnement**.
    - Sélectionnez **Créer une application Web...Avancé**.
    - Pour **Entrer un nom global unique**, utilisez un nom unique dans Azure (*les caractères valides sont `a-z`, `0-9` et `-`* ). Un bon modèle consiste à utiliser une combinaison du nom de votre société et d’un identificateur d’application.
    - Sélectionnez **Create new resource group** (Créer un groupe de ressources), puis indiquez un nom similaire à `myResourceGroup`.
    - Lorsque vous êtes invité à **sélectionner une pile d’exécution** :
      - Pour *.NET 5.0*, sélectionnez **.NET 5**
      - Pour *.NET Framework 4.8*, sélectionnez **ASP.NET V4.8**
    - Sélectionnez un système d’exploitation (Windows ou Linux).
        - Pour *.NET Framework 4.8*, Windows est sélectionné implicitement.
    - Sélectionnez **Créer un plan App Service**, indiquez un nom et sélectionnez le **niveau tarifaire** [F1 Gratuit][app-service-pricing-tier].
    - Pour la ressource Application Insights, sélectionnez **Skip for now** (Ignorer pour le moment).
    - Sélectionnez un emplacement près de chez vous.

1. Une fois la publication terminée, sélectionnez **Parcourir le site web** dans la notification et sélectionnez **Ouvrir** lorsque vous y êtes invité.

    ### <a name="net-50"></a>[.NET 5.0](#tab/net50)

    L’application web ASP.NET Core 5.0 s’affiche sur la page.

    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="Visual Studio Code : Application web ASP.NET Core 5.0 dans Azure.":::

    ### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

    L’application web ASP.NET Framework 4.8 s’affiche sur la page.

    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/vs-Azure-webapp-net48.png" border="true" alt-text="Visual Studio Code : Application web ASP.NET Framework 4.8 dans Azure.":::

    ---

:::zone-end

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-cli"
<!-- markdownlint-enable MD044 -->

1. Connectez-vous à votre compte Azure à l’aide de la commande [`az login`](/cli/reference-index#az_login) et suivez les instructions de l’invite :

    ```azurecli
    az login
    ```
    
1. Déployez le code dans votre répertoire local *MyFirstAzureWebApp* à l’aide de la commande [`az webapp up`](/cli/azure/webapp#az_webapp_up) :

    ```azurecli
    az webapp up --sku F1 --name <app-name> --os-type <os>
    ```

    - Si la commande `az` n’est pas reconnue, vérifiez qu’Azure CLI est installé, comme décrit dans [Prérequis](#prerequisites).
    - Remplacez `<app-name>` par un nom unique sur l’ensemble d’Azure (*les caractères valides sont `a-z`, `0-9` et `-`* ). Un bon modèle consiste à utiliser une combinaison du nom de votre société et d’un identificateur d’application.
    - L’argument `--sku F1` crée l’application web sur le **niveau tarifaire** [Gratuit][app-service-pricing-tier]. Omettez cet argument pour utiliser un niveau Premium plus rapide, ce qui entraîne un coût horaire.
    - Remplacez `<os>` par `linux` ou `windows`. Vous devez utiliser `windows` lorsque vous ciblez *ASP.NET Framework 4.8*.
    - Vous pouvez éventuellement inclure l’argument `--location <location-name>` où `<location-name>` est une région Azure disponible. Vous pouvez récupérer une liste de régions autorisées pour votre compte Azure en exécutant la commande [`az account list-locations`](/cli/azure/appservice#az_appservice_list_locations).
    
    La commande peut prendre quelques minutes. Lors de son exécution, elle fournit des messages sur la création du groupe de ressources, le plan App Service et l’application d’hébergement, la configuration de la journalisation, puis le déploiement ZIP. Elle génère ensuite un message avec l’URL de l’application :
    
    ```azurecli
    You can launch the app at http://<app-name>.azurewebsites.net
    ```

1. Ouvrez un navigateur web et accédez à l’URL :

    ### <a name="net-50"></a>[.NET 5.0](#tab/net50)
    
    L’application web ASP.NET Core 5.0 s’affiche sur la page.
    
    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="CLI : Application web ASP.NET Core 5.0 dans Azure.":::
    
    ### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)
    
    L’application web ASP.NET Framework 4.8 s’affiche sur la page.
    
    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/Azure-webapp-net48.png" border="true" alt-text="CLI : Application web ASP.NET Framework 4.8 dans Azure.":::

    -----

:::zone-end

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-ps"
<!-- markdownlint-enable MD044 -->

> [!NOTE]
> Azure PowerShell est recommandé pour la création d’applications sur la plateforme d’hébergement Windows. Pour créer des applications sur Linux, utilisez un autre outil, tel qu’[Azure CLI](quickstart-dotnetcore.md?pivots=development-environment-cli).

1. Connectez-vous à votre compte Azure à l’aide de la commande [`Connect-AzAccount`](/powershell/module/az.accounts/connect-azaccount) et suivez les instructions de l’invite :

    ```azurepowershell-interactive
    Connect-AzAccount
    ```

<!-- ### [Deploy to Windows](#tab/windows) -->

2. Créez une application à l’aide de la commande [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) :

    ```azurepowershell-interactive
    New-AzWebApp -Name <app-name> -Location westeurope
    ```

    - Remplacez `<app-name>` par un nom unique sur l’ensemble d’Azure (*les caractères valides sont `a-z`, `0-9` et `-`* ). Un bon modèle consiste à utiliser une combinaison du nom de votre société et d’un identificateur d’application.
    - Si vous le souhaitez, vous pouvez inclure le paramètre `-Location <location-name>` où `<location-name>` est une région Azure disponible. Vous pouvez récupérer une liste de régions autorisées pour votre compte Azure en exécutant la commande [`Get-AzLocation`](/powershell/module/az.resources/get-azlocation).

    La commande peut prendre quelques minutes. Pendant son exécution, elle crée un groupe de ressources, un plan App Service et la ressource App Service.

    <!-- ### [Deploy to Linux](#tab/linux)
    
    2. Create the Azure resources you need:
    
        ```azurepowershell-interactive
        New-AzResourceGroup -Name myResourceGroup -Location westeurope
        New-AzAppServicePlan -ResourceGroupName myResourceGroup -Name myAppServicePlan -Location westeurope -Linux
        New-AzWebApp -ResourceGroupName myResourceGroup -AppServicePlan myAppServicePlan -Name <app-name>
        Set-AzWebApp -
        ```
    
        - Replace `<app-name>` with a name that's unique across all of Azure (*valid characters are `a-z`, `0-9`, and `-`*). A good pattern is to use a combination of your company name and an app identifier.
        - You can optionally specify a different location in the `-Location` parameter. You can retrieve a list of allowable regions for your Azure account by running the [`Get-AzLocation`](/powershell/module/az.resources/get-azlocation) command.
        - [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) creates a resource group to contain the resources.
        - [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) uses `-Linux` to create a Linux App Service plan, which hosts your app. The default pricing tier is `Free`, but you can change it with the `-Tier` parameter.
        - [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) creates the app itself.
    
    --- -->
    
1. À partir du dossier racine de l’application, préparez votre application *MyFirstAzureWebApp* locale pour le déploiement à l’aide de la commande [`dotnet publish`](/dotnet/core/tools/dotnet-publish) :

    ```dotnetcli
    dotnet publish --configuration Release
    ```

1. Accédez au répertoire de la version et créez un fichier zip à partir de son contenu :

    ### <a name="net-50"></a>[.NET 5.0](#tab/net50)

    ```powershell-interactive
    cd bin\Release\net5.0\publish
    Compress-Archive -Path * -DestinationPath deploy.zip
    ```

    ### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

    ```powershell-interactive
    cd bin\Release\net48\publish
    Compress-Archive -Path * -DestinationPath deploy.zip
    ```

    -----

1. Publiez le fichier zip dans l’application Azure à l’aide de la commande [Publish-AzWebApp](/powershell/module/az.websites/publish-azwebapp) :

    ```azurepowershell-interactive
    Publish-AzWebApp -ResourceGroupName myResourceGroup -Name <app-name> -ArchivePath (Get-Item .\deploy.zip).FullName -Force
    ```

    > [!NOTE]
    > `-ArchivePath` nécessite le chemin d’accès complet du fichier zip.

1. Ouvrez un navigateur web et accédez à l’URL :

    ### <a name="net-50"></a>[.NET 5.0](#tab/net50)
    
    L’application web ASP.NET Core 5.0 s’affiche sur la page.
    
    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="CLI : Application web ASP.NET Core 5.0 dans Azure.":::
    
    ### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)
    
    L’application web ASP.NET Framework 4.8 s’affiche sur la page.
    
    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/Azure-webapp-net48.png" border="true" alt-text="CLI : Application web ASP.NET Framework 4.8 dans Azure.":::

    -----

:::zone-end

## <a name="update-the-app-and-redeploy"></a>Mise à jour de l’application et redéploiement

Pour mettre à jour et redéployer votre application web, suivez ces étapes :

:::zone target="docs" pivot="development-environment-vs"

1. Dans l’**Explorateur de solutions**, sous votre projet, ouvrez *Index.cshtml*.
1. Remplacez le premier élément `<div>` par le code suivant :

    ```razor
    <div class="jumbotron">
        <h1>.NET 💜 Azure</h1>
        <p class="lead">Example .NET app to Azure App Service.</p>
    </div>
    ```

   Enregistrez vos modifications.

1. Pour effectuer un redéploiement dans Azure, cliquez avec le bouton droit sur le projet **MyFirstAzureWebApp** dans **l’Explorateur de solutions**, puis sélectionnez **Publier**.
1. Dans la page récapitulative intitulée **Publier**, sélectionnez **Publier**.

    Une fois la publication terminée, Visual Studio lance un navigateur en accédant à l’URL de l’application web.

    ### <a name="net-50"></a>[.NET 5.0](#tab/net50)

    L’application web mise à jour ASP.NET Core 5.0 s’affiche sur la page.

    :::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="Visual Studio : Application web mise à jour ASP.NET Core 5.0 dans Azure.":::

    ### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

    L’application web mise à jour ASP.NET Framework 4.8 s’affiche sur la page.

    :::image type="content" source="media/quickstart-dotnet/vs-updated-Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/vs-updated-Azure-webapp-net48.png" border="true" alt-text="Visual Studio : Application web mise à jour ASP.NET Framework 4.8 dans Azure.":::

    ---

:::zone-end

:::zone target="docs" pivot="development-environment-vscode"

1. Ouvrez la page *Index.cshtml*.
1. Remplacez le premier élément `<div>` par le code suivant :

    ```razor
    <div class="jumbotron">
        <h1>.NET 💜 Azure</h1>
        <p class="lead">Example .NET app to Azure App Service.</p>
    </div>
    ```

   Enregistrez vos modifications.

1. Ouvrez la **barre latérale** Visual Studio Code, sélectionnez l’icône **Azure** pour développer ses options.
1. Sous le nœud **APP SERVICE**, développez votre abonnement et cliquez avec le bouton droit sur **MyFirstAzureWebApp**.
1. Sélectionnez **Déployer sur l’application Web...** .
1. Lorsque vous y êtes invité, sélectionnez **Déployer**.
1. Une fois la publication terminée, sélectionnez **Parcourir le site web** dans la notification et sélectionnez **Ouvrir** lorsque vous y êtes invité.

    ### <a name="net-50"></a>[.NET 5.0](#tab/net50)

    L’application web mise à jour ASP.NET Core 5.0 s’affiche sur la page.

    :::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="Visual Studio Code : Application web mise à jour ASP.NET Core 5.0 dans Azure.":::

    ### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

    L’application web mise à jour ASP.NET Framework 4.8 s’affiche sur la page.

    :::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net48.png" border="true" alt-text="Visual Studio Code : Application web mise à jour ASP.NET Framework 4.8 dans Azure.":::

    ---

:::zone-end

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-cli"
<!-- markdownlint-enable MD044 -->

Dans le répertoire local, ouvrez le fichier *Index.cshtml*. Remplacez le premier élément `<div>` :

```razor
<div class="jumbotron">
    <h1>.NET 💜 Azure</h1>
    <p class="lead">Example .NET app to Azure App Service.</p>
</div>
```

Enregistrez vos modifications, puis redéployez l’application en réexécutant la commande `az webapp up` :

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

ASP.NET Core 5.0 est multiplateforme, selon votre précédent déploiement, remplacez `<os>` par `linux` ou `windows`.

```azurecli
az webapp up --os-type <os>
```

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

ASP.NET Framework 4.8 présente des dépendances de framework et doit être hébergé sur Windows.

```azurecli
az webapp up --os-type windows
```

> [!TIP]
> Si vous souhaitez héberger vos applications .NET sur Linux, envisagez une migration de [ASP.NET Framework vers ASP.NET Core](/aspnet/core/migration/proper-to-2x).

---

Cette commande utilise des valeurs qui sont mises en cache localement dans le fichier *.azure/config*, notamment le nom de l’application, le groupe de ressources et le plan App Service.

Une fois le déploiement terminé, revenez à la fenêtre du navigateur ouverte à l’étape **Accéder à l’application**, puis cliquez sur Actualiser.

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

L’application web mise à jour ASP.NET Core 5.0 s’affiche sur la page.

:::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="CLI : Application web mise à jour ASP.NET Core 5.0 dans Azure.":::

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

L’application web mise à jour ASP.NET Framework 4.8 s’affiche sur la page.

:::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net48.png" border="true" alt-text="CLI : Application web mise à jour ASP.NET Framework 4.8 dans Azure.":::

---

:::zone-end

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-ps"
<!-- markdownlint-enable MD044 -->

1. Dans le répertoire local, ouvrez le fichier *Index.cshtml*. Remplacez le premier élément `<div>` :

    ```razor
    <div class="jumbotron">
        <h1>.NET 💜 Azure</h1>
        <p class="lead">Example .NET app to Azure App Service.</p>
    </div>
    ```

1. À partir du dossier racine de l’application, préparez votre application *MyFirstAzureWebApp* locale pour le déploiement à l’aide de la commande [`dotnet publish`](/dotnet/core/tools/dotnet-publish) :

    ```dotnetcli
    dotnet publish --configuration Release
    ```

1. Accédez au répertoire de la version et créez un fichier zip à partir de son contenu :

    ### <a name="net-50"></a>[.NET 5.0](#tab/net50)

    ```powershell-interactive
    cd bin\Release\net5.0\publish
    Compress-Archive -Path * -DestinationPath deploy.zip
    ```

    ### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

    ```powershell-interactive
    cd bin\Release\net48\publish
    Compress-Archive -Path * -DestinationPath deploy.zip
    ```

    -----

1. Publiez le fichier zip dans l’application Azure à l’aide de la commande [Publish-AzWebApp](/powershell/module/az.websites/publish-azwebapp) :

    ```azurepowershell-interactive
    Publish-AzWebApp -ResourceGroupName myResourceGroup -Name <app-name> -ArchivePath (Get-Item .\deploy.zip).FullName -Force
    ```

    > [!NOTE]
    > `-ArchivePath` nécessite le chemin d’accès complet du fichier zip.

1. Une fois le déploiement terminé, revenez à la fenêtre du navigateur ouverte à l’étape **Accéder à l’application**, puis cliquez sur Actualiser.

    ### <a name="net-50"></a>[.NET 5.0](#tab/net50)
    
    L’application web mise à jour ASP.NET Core 5.0 s’affiche sur la page.
    
    :::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="CLI : Application web mise à jour ASP.NET Core 5.0 dans Azure.":::
    
    ### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)
    
    L’application web mise à jour ASP.NET Framework 4.8 s’affiche sur la page.
    
    :::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net48.png" border="true" alt-text="CLI : Application web mise à jour ASP.NET Framework 4.8 dans Azure.":::
    
    ---
    
:::zone-end

## <a name="manage-the-azure-app"></a>Gérer l’application Azure

Pour gérer votre application web, accédez au [Portail Azure](https://portal.azure.com), puis recherchez et sélectionnez **App Services**.

:::image type="content" source="media/quickstart-dotnetcore/app-services.png" alt-text="Portail Azure : sélectionnez l’option App Services.":::

Dans la page **App Services**, sélectionnez le nom de votre application web.

:::image type="content" source="./media/quickstart-dotnetcore/select-app-service.png" alt-text="Portail Azure : Page App Services avec un exemple d’application web sélectionnée.":::

La page **Vue d’ensemble** de votre application web contient des options de gestion de base, telles que parcourir, arrêter, démarrer, redémarrer et supprimer. Le menu de gauche fournit d’autres pages vous permettant de configurer votre application.

:::image type="content" source="media/quickstart-dotnetcore/web-app-overview-page.png" alt-text="Portail Azure : Page de vue d’ensemble d’App Service.":::

<!--
## Clean up resources - H2 added from the next three includes
-->
:::zone target="docs" pivot="development-environment-vs"
[!INCLUDE [Clean-up Portal web app resources](../../includes/clean-up-section-portal-web-app.md)]
:::zone-end

:::zone target="docs" pivot="development-environment-vscode"
[!INCLUDE [Clean-up Portal web app resources](../../includes/clean-up-section-portal-web-app.md)]
:::zone-end

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-cli"
<!-- markdownlint-enable MD044 -->
[!INCLUDE [Clean-up CLI resources](../../includes/cli-samples-clean-up.md)]
:::zone-end

:::zone target="docs" pivot="development-environment-ps"
<!-- markdownlint-enable MD044 -->
[!INCLUDE [Clean-up PowerShell resources](../../includes/powershell-samples-clean-up.md)]
:::zone-end

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez créé et déployé une application web ASP.NET dans Azure App Service.

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

Passez à l’article suivant pour savoir comment créer une application .NET Core et la connecter à une base de données SQL :

> [!div class="nextstepaction"]
> [Tutoriel : Application ASP.NET Core avec SQL Database](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [Configurer une application ASP.NET Core](configure-language-dotnetcore.md)

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

Passez à l’article suivant pour savoir comment créer une application .NET Framework et la connecter à une instance SQL Database :

> [!div class="nextstepaction"]
> [Tutoriel : Application ASP.NET avec SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md)

> [!div class="nextstepaction"]
> [Configurer une application ASP.NET Framework](configure-language-dotnet-framework.md)

---

[app-service-pricing-tier]: https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
