---
title: Tutoriel pour utiliser la configuration dynamique Azure App Configuration dans une application Azure Functions | Microsoft Docs
description: Dans ce tutoriel, vous allez apprendre à mettre à jour dynamiquement les données de configuration pour les applications Azure Functions
services: azure-app-configuration
documentationcenter: ''
author: zhenlan
manager: qingye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 11/17/2019
ms.author: zhenlwa
ms.custom: devx-track-csharp, azure-functions
ms.tgt_pltfrm: Azure Functions
ms.openlocfilehash: ffa2b1a4537093f9a612f0e57b7f416ee7ed0012
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132301909"
---
# <a name="tutorial-use-dynamic-configuration-in-an-azure-functions-app"></a>Tutoriel : Utiliser la configuration dynamique dans une application Azure Functions

Le fournisseur de configuration .NET App Configuration prend en charge la mise en cache et l’actualisation de la configuration pilotée dynamiquement par l’activité des applications. Ce tutoriel montre comment vous pouvez implémenter des mises à jour de la configuration dynamique dans votre code. Il s’appuie sur l’application Azure Functions mentionnée dans les guides de démarrage rapide. Avant de continuer, terminez d’abord [Créer une application Azure Functions avec Azure App Configuration](./quickstart-azure-functions-csharp.md).

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Configurer votre application Azure Functions pour mettre à jour sa configuration en réponse aux changements survenant dans un magasin App Configuration.
> * Injecter la configuration la plus récente dans vos appels Azure Functions.

## <a name="prerequisites"></a>Prérequis

- Abonnement Azure : [créez-en un gratuitement](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs) avec la charge de travail **Développement Azure**
- [Outils Azure Functions](../azure-functions/functions-develop-vs.md#check-your-tools-version)
- Terminez le guide de démarrage rapide [Créer une application Azure Functions avec Azure App Configuration](./quickstart-azure-functions-csharp.md)

## <a name="reload-data-from-app-configuration"></a>Recharger des données à partir d’Azure App Configuration

1. Ouvrez *Startup.cs* et mettez à jour la méthode `ConfigureAppConfiguration`. 

   La méthode `ConfigureRefresh` inscrit un paramètre pour lequel une recherche de modifications doit être effectuée dès qu’une actualisation est déclenchée dans l’application, ce que vous effectuerez ultérieurement lors de l’ajout de `_configurationRefresher.TryRefreshAsync()`. Le paramètre `refreshAll` indique au fournisseur App Configuration de recharger la configuration entière chaque fois qu’une modification est détectée dans le paramètre inscrit.

    Tous les paramètres inscrits pour l’actualisation ont un délai d’expiration du cache de 30 secondes par défaut. Vous pouvez changer ce délai en appelant la méthode `AzureAppConfigurationRefreshOptions.SetCacheExpiration`.

    ```csharp
    public override void ConfigureAppConfiguration(IFunctionsConfigurationBuilder builder)
    {
        builder.ConfigurationBuilder.AddAzureAppConfiguration(options =>
        {
            options.Connect(Environment.GetEnvironmentVariable("ConnectionString"))
                   // Load all keys that start with `TestApp:`
                   .Select("TestApp:*")
                   // Configure to reload configuration if the registered sentinel key is modified
                   .ConfigureRefresh(refreshOptions =>
                      refreshOptions.Register("TestApp:Settings:Sentinel", refreshAll: true));
        });
    }
    ```

   > [!TIP]
   > Quand vous modifiez plusieurs paires clé-valeur dans App Configuration, vous ne souhaitez généralement pas que votre application recharge la configuration tant que toutes les modifications n’ont pas été appliquées. Vous pouvez inscrire une *clé Sentinel* et la mettre à jour uniquement quand tous les autres changements de configuration ont été effectués. Cela permet de garantir la cohérence de la configuration dans votre application.

2. Mettez à jour la méthode `Configure` pour rendre les services Azure App Configuration disponibles via l’injection de dépendances.

    ```csharp
    public override void Configure(IFunctionsHostBuilder builder)
    {
        builder.Services.AddAzureAppConfiguration();
    }
    ```

3. Ouvrez *Function1.cs* et ajoutez les espaces de noms suivants.

    ```csharp
    using System.Linq;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

   Mettez à jour le constructeur pour obtenir l’instance de `IConfigurationRefresherProvider` via l’injection de dépendances, à partir de laquelle vous pouvez obtenir l’instance de `IConfigurationRefresher`.

    ```csharp
    private readonly IConfiguration _configuration;
    private readonly IConfigurationRefresher _configurationRefresher;

    public Function1(IConfiguration configuration, IConfigurationRefresherProvider refresherProvider)
    {
        _configuration = configuration;
        _configurationRefresher = refresherProvider.Refreshers.First();
    }
    ```

4. Mettez à jour la méthode `Run` et indiquez d’actualiser la configuration à l’aide de la méthode `TryRefreshAsync` au début de l’appel Functions. Aucune opération n’est effectuée si la fenêtre de délai d’expiration du cache n’est pas atteinte. Supprimez l’opérateur `await` si vous préférez que la configuration soit actualisée sans bloquer l’appel Functions en cours. Dans ce cas, les appels Functions ultérieurs obtiendront une valeur mise à jour.

    ```csharp
    public async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        await _configurationRefresher.TryRefreshAsync(); 

        string keyName = "TestApp:Settings:Message";
        string message = _configuration[keyName];
            
        return message != null
            ? (ActionResult)new OkObjectResult(message)
            : new BadRequestObjectResult($"Please create a key-value with the key '{keyName}' in App Configuration.");
    }
    ```

## <a name="test-the-function-locally"></a>Tester la fonction en local

1. Définissez une variable d’environnement nommée **ConnectionString** et affectez-lui la valeur de la clé d’accès à votre magasin de configuration d’application. Si vous utilisez l’invite de commandes Windows, exécutez la commande suivante et redémarrez l’invite pour que la modification soit prise en compte :

    ```console
    setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Si vous utilisez Windows PowerShell, exécutez la commande suivante :

    ```powershell
    $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

    Si vous utilisez macOS ou Linux, exécutez la commande suivante :

    ```console
    export ConnectionString='connection-string-of-your-app-configuration-store'
    ```

2. Pour tester votre fonction, appuyez sur F5. Si vous y êtes invité, acceptez la requête dans Visual Studio pour télécharger et installer **Azure Functions Core (CLI) Tools**. Vous devrez peut-être aussi activer une exception de pare-feu afin de permettre aux outils de prendre en charge les requêtes HTTP.

3. Copiez l’URL de votre fonction à partir de la sortie runtime Azure Functions.

    ![Démarrage rapide du débogage de fonctions dans Visual Studio](./media/quickstarts/function-visual-studio-debugging.png)

4. Collez l’URL de la demande HTTP dans la barre d’adresses de votre navigateur. La capture d’écran suivante du navigateur montre la réponse retournée par la fonction, suite à la requête GET locale.

    ![Démarrage rapide du lancement de fonctions local](./media/quickstarts/dotnet-core-function-launch-local.png)

5. Connectez-vous au [portail Azure](https://portal.azure.com). Sélectionnez **Toutes les ressources**, puis sélectionnez le magasin App Configuration que vous avez créé dans le guide de démarrage rapide.

6. Sélectionnez **Explorateur de configuration**, puis mettez à jour la valeur de la clé suivante :

    | Clé | Valeur |
    |---|---|
    | TestApp:Settings:Message | Données issues d’Azure App Configuration - Mise à jour |

   Créez ensuite la clé Sentinel ou modifiez sa valeur si elle existe déjà, par exemple

    | Clé | Valeur |
    |---|---|
    | TestApp:Settings:Sentinel | v1 |


7. Actualisez le navigateur à plusieurs reprises. Lorsque le paramètre mis en cache expire au bout de 30 secondes, la page affiche la réponse de l’appel Functions avec la valeur mise à jour.

    ![Démarrage rapide de l’actualisation de fonctions en local](./media/quickstarts/dotnet-core-function-refresh-local.png)

> [!NOTE]
> L’exemple de code utilisé dans ce tutoriel peut être téléchargé à partir du [dépôt GitHub App Configuration](https://github.com/Azure/AppConfiguration/tree/master/examples/DotNetCore/AzureFunction).

## <a name="clean-up-resources"></a>Nettoyer les ressources

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez permis à votre application Azure Functions d’actualiser dynamiquement les paramètres de configuration à partir d’App Configuration. Pour savoir comment utiliser une identité managée Azure afin de simplifier l’accès à App Configuration, passez au tutoriel suivant.

> [!div class="nextstepaction"]
> [Intégration des identités managées](./howto-integrate-azure-managed-service-identity.md)
