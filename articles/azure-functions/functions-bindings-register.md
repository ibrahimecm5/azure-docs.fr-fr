---
title: Inscrire des extensions de liaison Azure Functions
description: Découvrez comment inscrire une extension de liaison Azure Functions en fonction de votre environnement.
author: craigshoemaker
ms.topic: reference
ms.date: 09/14/2020
ms.author: cshoe
ms.openlocfilehash: b847243543eb8667a38d253e7eaad215af29e7b2
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131852331"
---
# <a name="register-azure-functions-binding-extensions"></a>Inscrire des extensions de liaison Azure Functions

À partir de Azure Functions version 2.x, le runtime des fonctions comprend uniquement les déclencheurs HTTP et minuteur par défaut. Autres [déclencheurs et liaisons](./functions-triggers-bindings.md) sont disponibles sous forme de packages distincts.

Les applications des fonctions de la bibliothèque de classes .NET utilisent des liaisons installées dans le projet en tant que packages NuGet. Les offres groupées d’extensions permettent aux applications de fonctions non-.NET d’utiliser les mêmes liaisons sans avoir à gérer l’infrastructure .NET.

Le tableau suivant indique quand et comment vous inscrivez des liaisons.

| Environnement de développement |Inscription<br/> dans Functions 1.x  |Inscription<br/> dans Functions 2.x ou version ultérieure  |
|-------------------------|------------------------------------|------------------------------------|
|Portail Azure|Automatique|Automatique<sup>*</sup>|
|Langages non-.NET|Automatique|Utiliser des [offres groupées d’extensions](#extension-bundles) (recommandé) ou [installer des extensions de manière explicite](#explicitly-install-extensions)|
|Bibliothèque de classes C# avec Visual Studio|[Utiliser les outils NuGet](#vs)|[Utiliser les outils NuGet](#vs)|
|Bibliothèque de classes C# avec Visual Studio Code|N/A|[Utiliser CLI .NET Core](#vs-code)|

<sup>*</sup> Le portail utilise des offres groupées d’extensions.

## <a name="access-extensions-in-non-net-languages"></a>Accéder aux extensions en langages non-.NET

Pour les applications de fonctions Java, JavaScript, PowerShell, Python et Gestionnaire personnalisé, nous vous recommandons d’utiliser des offres groupées d’extensions pour accéder aux liaisons. Dans les cas où les offres groupées d’extensions ne peuvent pas être utilisées, vous pouvez installer des extensions de liaison de manière explicite.

### <a name="extension-bundles"></a><a name="extension-bundles"></a>Offres groupées d’extensions

Les offres groupées d’extensions permettent d’ajouter un jeu d’extensions de liaison compatible à votre application de fonction. Vous activez les offres groupées d’extensions dans le fichier *host.json* de l’application.

Vous pouvez utiliser des offres groupées d’extensions avec la version 2.x et les versions ultérieures du runtime Functions.

Les offres groupées d’extensions sont associées à une version. Chaque version contient un ensemble spécifique d’extensions de liaison qui sont vérifiées pour fonctionner ensemble. Sélectionnez une version d’offre groupée en fonction des extensions dont vous avez besoin dans votre application.

Pour ajouter une offre groupée d’extensions à votre application de fonction, ajoutez la section `extensionBundle` à *host.json*. Dans de nombreux cas, Visual Studio Code et Azure Functions Core Tools l’ajoutera automatiquement pour vous.

[!INCLUDE [functions-extension-bundles-json](../../includes/functions-extension-bundles-json.md)]

Le tableau suivant répertorie les versions actuellement disponibles de l’offre groupée par défaut *Microsoft.Azure.Functions.ExtensionBundle* et fournit des liens vers les extensions qu’elles incluent.

| Version de l’offre groupée | Version dans host.json | Extensions incluses |
| --- | --- | --- |
| 1.x | `[1.*, 2.0.0)` | Consultez [extensions.json](https://github.com/Azure/azure-functions-extension-bundles/blob/v1.x/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json) utilisées pour générer l’offre groupée |
| 2.x | `[2.*, 3.0.0)` | Consultez [extensions.json](https://github.com/Azure/azure-functions-extension-bundles/blob/v2.x/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json) utilisées pour générer l’offre groupée |
| 3.x | `[3.3.0, 4.0.0)` | Consultez [extensions.json](https://github.com/Azure/azure-functions-extension-bundles/blob/v3.x/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json) utilisées pour générer l’offre groupée |

> [!NOTE]
> Tandis que vous pouvez spécifier une plage de versions personnalisée dans host.json, nous vous recommandons d’utiliser une valeur de version de ce tableau.

### <a name="explicitly-install-extensions"></a>Installer des extensions de manière explicite

Si vous ne pouvez pas utiliser les packs d’extensions, vous pouvez utiliser Azure Functions Core Tools localement pour installer les packs d’extensions spécifiques requis par votre projet.

> [!IMPORTANT]
> Vous ne pouvez pas installer explicitement des extensions dans une application de fonction qui utilise des bundles d’extension. Supprimez la section `extensionBundle` dans *host.json* avant d’installer des extensions de manière explicite.

Les éléments suivants décrivent certaines raisons pour lesquelles vous devrez peut-être installer des extensions manuellement :

* Vous devez accéder à une version spécifique d’une extension qui n’est pas disponible dans un bundle.
* Vous devez accéder à une extension personnalisée qui n’est pas disponible dans un bundle.
* Vous devez accéder à une combinaison spécifique d’extensions non disponibles dans un seul bundle.

> [!NOTE]
> Pour installer manuellement des extensions avec Core Tools, vous devez avoir installé le [SDK .NET Core 3.1](https://dotnet.microsoft.com/download). Azure Functions Core Tools installe le kit SDK .NET Core pour installer des extensions à partir de NuGet. Vous n’avez pas besoin de connaître .NET pour utiliser les extensions Azure Functions.

Lorsque vous installez explicitement des extensions, un fichier de projet .NET nommé extensions.csproj est ajouté à la racine de votre projet. Ce fichier définit l’ensemble des packages NuGet requis par vos fonctions. Bien que vous puissiez utiliser les [références de package NuGet](/nuget/consume-packages/package-references-in-project-files) dans ce fichier, Core Tools vous permet d’installer des extensions sans avoir à modifier manuellement le fichier.

Il existe plusieurs façons d’utiliser les outils de base pour installer les extensions requises dans votre projet local. 

#### <a name="install-all-extensions"></a>Installer toutes les extensions 

Utilisez la commande suivante pour ajouter automatiquement tous les packages d’extensions utilisés par les liaisons dans votre projet local :

```command
func extensions install
```

La commande lit le fichier *function.json* pour voir les packages dont vous avez besoin, les installe et regénère le projet des extensions (extensions.csproj). Il ajoute les nouvelles liaisons à la version actuelle, mais ne met pas à jour les liaisons existantes. Utilisez l’option `--force` pour mettre à jour les liaisons existantes vers la dernière version pendant les nouvelles installations. Pour plus d’informations, reportez-vous à la [commande `func extensions install`](functions-core-tools-reference.md#func-extensions-install).

Si votre application de fonction utilise des liaisons que les outils Core ne reconnaissent pas, vous devez installer manuellement l’extension spécifique.

#### <a name="install-a-specific-extension"></a>Installer une extension spécifique

Utilisez la commande suivante pour installer un package d’extension spécifique à une version spécifique, dans ce cas l’extension Storage :

```command
func extensions install --package Microsoft.Azure.WebJobs.Extensions.Storage --version 5.0.0
```

Pour plus d’informations, reportez-vous à la [commande `func extensions install`](functions-core-tools-reference.md#func-extensions-install).

## <a name="install-extensions-from-nuget-in-net-languages"></a><a name="local-csharp"></a>Installer des extensions à partir de NuGet dans les langages .NET

Pour un projet de fonctions basé sur une bibliothèque de classe C#, vous devez installer les extensions directement. Les offres groupées d’extension sont conçues spécifiquement pour les projets qui ne sont pas basés sur une bibliothèque de classes C#.

### <a name="c-class-library-with-visual-studio"></a><a name="vs"></a> Bibliothèque de classes C\# avec Visual Studio

Dans **Visual Studio**, vous pouvez installer des packages à partir de la console du Gestionnaire de package avec la commande [Install-Package](/nuget/tools/ps-ref-install-package), comme indiqué dans l’exemple suivant :

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions.ServiceBus -Version <TARGET_VERSION>
```

Le nom du package utilisé pour une liaison donnée est fourni dans l’article de référence pour cette liaison. Pour obtenir un exemple, consultez la [section Packages de l’article Informations de référence sur les liaisons Service Bus](functions-bindings-service-bus.md#functions-1x).

Remplacez `<TARGET_VERSION>` dans l’exemple par une version spécifique du package, comme `3.0.0-beta5`. Les versions valides sont répertoriées sur les pages de chaque package sur [NuGet.org](https://nuget.org). Les versions majeures qui correspondent aux runtime Functions 1.x ou 2.x sont spécifiées dans l’article de référence pour la liaison.

Si vous utilisez `Install-Package` pour faire référence à une liaison, vous n’avez pas besoin d’utiliser des [offres groupées d’extensions](#extension-bundles). Cette approche est spécifique aux bibliothèques de classes générées dans Visual Studio.

### <a name="c-class-library-with-visual-studio-code"></a><a name="vs-code"></a> Bibliothèque de classes C# avec Visual Studio Code

Dans **Visual Studio Code**, installez des packages pour un projet de bibliothèque de classes C# à partir de l’invite de commandes avec la commande [dotnet add package](/dotnet/core/tools/dotnet-add-package) dans CLI .NET Core. L’exemple suivant montre comment ajouter une liaison :

```terminal
dotnet add package Microsoft.Azure.WebJobs.Extensions.<BINDING_TYPE_NAME> --version <TARGET_VERSION>
```

CLI .NET Core peut être utilisé seulement pour le développement Azure Functions 2.x.

Remplacez `<BINDING_TYPE_NAME>` par le nom du package qui contient la liaison dont vous avez besoin. Vous trouverez l’article de référence de la liaison souhaitée dans la [liste des liaisons prises en charge](./functions-triggers-bindings.md#supported-bindings).

Remplacez `<TARGET_VERSION>` dans l’exemple par une version spécifique du package, comme `3.0.0-beta5`. Les versions valides sont répertoriées sur les pages de chaque package sur [NuGet.org](https://nuget.org). Les versions majeures qui correspondent aux runtime Functions 1.x ou 2.x sont spécifiées dans l’article de référence pour la liaison.

## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"]
> [Exemples de liaisons et de déclencheurs Azure Functions](./functions-bindings-example.md)
