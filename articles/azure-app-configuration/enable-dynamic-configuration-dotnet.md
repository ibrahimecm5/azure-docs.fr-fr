---
title: 'Tutoriel .NET Framework : Configuration dynamique dans Azure App Configuration'
description: Dans ce tutoriel, vous allez apprendre à mettre à jour dynamiquement les données de configuration des applications .NET Framework avec Azure App Configuration.
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.devlang: csharp
ms.custom: devx-track-csharp
ms.topic: tutorial
ms.date: 07/24/2020
ms.author: alkemper
ms.openlocfilehash: d6c3bc0ee45c214419820208598e5721309ea144
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130261041"
---
# <a name="tutorial-use-dynamic-configuration-in-a-net-framework-app"></a>Tutoriel : Utiliser la configuration dynamique dans une application .NET Framework

Les données d’App Configuration peuvent être chargées en tant que paramètres de l’application dans une application .NET Framework. Pour plus d’informations, consultez le [guide de démarrage rapide](./quickstart-dotnet-app.md). Toutefois, comme conçu par le .NET Framework, les paramètres de l’application ne peuvent être actualisés que lors du redémarrage de l’application. Le fournisseur .NET d’App Configuration est une bibliothèque .NET Standard. Il prend en charge la mise en cache et l’actualisation dynamiques de la configuration sans redémarrage de l’application. Ce tutoriel montre comment vous pouvez implémenter des mises à jour de configuration dynamiques dans une application console .NET Framework.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Configurer votre application .NET Framework pour mettre à jour sa configuration en réponse aux changements survenant dans un magasin App Configuration.
> * Injecter la configuration la plus récente dans votre application.

## <a name="prerequisites"></a>Prérequis

- Abonnement Azure : [créez-en un gratuitement](https://azure.microsoft.com/free/)
- [Visual Studio](https://visualstudio.microsoft.com/vs)
- [.NET Framework 4.7.2 ou version ultérieure](https://dotnet.microsoft.com/download/dotnet-framework)

## <a name="create-an-app-configuration-store"></a>Créer un magasin App Configuration

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7. Sélectionnez **Explorateur de configurations** >  **+ Créer** > **Clé-valeur** pour ajouter la paire clé-valeur suivante :

    | Clé                        | Valeur                               |
    |----------------------------|-------------------------------------|
    | *TestApp:Settings:Message* | *Data from Azure App Configuration* |

    Laissez **Étiquette** et **Type de contenu** vides.

## <a name="create-a-net-framework-console-app"></a>Créer une application console .NET Framework

1. Démarrez Visual Studio et sélectionnez **Créer un projet**.

1. Dans **Créer un projet**, filtrez sur le type de projet **Console** et sélectionnez **Application console (.NET Framework)** avec C# dans la liste de modèles de projet. Appuyez sur **Suivant**.

1. Dans **Configurer votre nouveau projet** , entrez un nom de projet. Sous **Framework**, sélectionnez **.NET Framework 4.7.2** ou une version ultérieure. Appuyez sur **Créer**.

## <a name="reload-data-from-app-configuration"></a>Recharger des données à partir d’Azure App Configuration
1. Cliquez avec le bouton droit sur votre projet, puis sélectionnez **Gérer les packages NuGet**. Sous l’onglet **Parcourir**, recherchez et ajoutez la dernière version du package NuGet suivant à votre projet.

   *Microsoft.Extensions.Configuration.AzureAppConfiguration*

1. Ouvrez *Program.cs* et ajoutez les espaces de noms suivants.

    ```csharp
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

1. Ajoutez deux variables pour stocker les objets liés à la configuration.

    ```csharp
    private static IConfiguration _configuration;
    private static IConfigurationRefresher _refresher;
    ```

1. Mettez à jour la méthode `Main` pour vous connecter à App Configuration avec les options d’actualisation spécifiées.

    ```csharp
    static void Main(string[] args)
    {
        var builder = new ConfigurationBuilder();
        builder.AddAzureAppConfiguration(options =>
        {
            options.Connect(Environment.GetEnvironmentVariable("ConnectionString"))
                    // Load all keys that start with `TestApp:`.
                    .Select("TestApp:*")
                    // Configure to reload the key 'TestApp:Settings:Message' if it is modified.
                    .ConfigureRefresh(refresh =>
                    {
                        refresh.Register("TestApp:Settings:Message")
                               .SetCacheExpiration(TimeSpan.FromSeconds(10));
                    });

            _refresher = options.GetRefresher();
        });

        _configuration = builder.Build();
        PrintMessage().Wait();
    }
    ```

    Dans la méthode `ConfigureRefresh`, une clé de votre magasin App Configuration est inscrite pour la supervision des changements. La méthode `Register` a un paramètre booléen facultatif `refreshAll` qui permet d’indiquer si toutes les valeurs de configuration doivent être actualisées en cas de changement de la clé inscrite. Dans cet exemple, seule la clé *TestApp:Settings:Message* est actualisée. La méthode `SetCacheExpiration` spécifie le temps minimum qui doit s'écouler avant qu'une nouvelle requête puisse être adressée à App Configuration pour rechercher les changements de configuration. Dans cet exemple, vous remplacez le délai d’expiration par défaut de 30 secondes par un délai de 10 secondes à des fins de démonstration.

1. Ajoutez une méthode appelée `PrintMessage()` qui déclenche une actualisation des données de configuration à partir d’App Configuration.

    ```csharp
    private static async Task PrintMessage()
    {
        Console.WriteLine(_configuration["TestApp:Settings:Message"] ?? "Hello world!");

        // Wait for the user to press Enter
        Console.ReadLine();

        await _refresher.TryRefreshAsync();
        Console.WriteLine(_configuration["TestApp:Settings:Message"] ?? "Hello world!");
    }
    ```

    L’appel de la méthode `ConfigureRefresh` seule n’entraîne pas l’actualisation automatique de la configuration. Vous appelez la méthode `TryRefreshAsync` à partir de l’interface `IConfigurationRefresher` pour déclencher une actualisation. Cette façon de procéder permet d’éviter l’envoi de requêtes fantômes à App Configuration même quand votre application est inactive. Vous pouvez inclure l’appel de `TryRefreshAsync` quand vous considérez que votre application est active. Par exemple, cela peut se produire quand vous traitez un message entrant, une commande ou l’itération d’une tâche complexe. Il peut également s’agir d’un minuteur si votre application est active en permanence. Dans cet exemple, vous appelez `TryRefreshAsync` quand vous appuyez sur la touche Entrée. Notez que, même en cas d’échec de l’appel de `TryRefreshAsync` pour une raison quelconque, votre application continue à utiliser la configuration mise en cache. Une autre tentative a lieu quand le délai d’expiration configuré pour le cache est écoulé et que l’appel de `TryRefreshAsync` est à nouveau déclenché par l’activité de votre application. L’appel de `TryRefreshAsync` est une non-opération avant l’expiration du délai configuré pour le cache. Son impact sur les performances est donc minimal, même si l’appel est effectué fréquemment.

## <a name="build-and-run-the-app-locally"></a>Générer et exécuter l’application localement

1. Définissez une variable d’environnement nommée **ConnectionString** sur la chaîne de connexion de la clé en lecture seule obtenue lors de la création du magasin App Configuration. 

    Si vous utilisez l’invite de commandes Windows, exécutez la commande suivante :
    ```console
    setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Si vous utilisez Windows PowerShell, exécutez la commande suivante :
    ```powershell
    $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

1. Redémarrez Visual Studio pour que la modification soit prise en compte. 

1. Appuyez sur Ctrl + F5 pour générer et exécuter l’application console.

    ![Lancement local de l’application](./media/dotnet-app-run.png)

1. Dans le portail Azure, accédez à l’**Explorateur de configurations** de votre magasin App Configuration, puis mettez à jour la valeur de la clé suivante.

    | Clé                        | Valeur                                         |
    |----------------------------|-----------------------------------------------|
    | *TestApp:Settings:Message* | *Données issues d’Azure App Configuration - Mise à jour* |

1. De retour dans l’application en cours d’exécution, appuyez sur la touche Entrée pour déclencher une actualisation et afficher la valeur mise à jour dans l’invite de commandes ou dans la fenêtre PowerShell.

    ![Actualisation locale de l’application](./media/dotnet-app-run-refresh.png)
    
    > [!NOTE]
    > Étant donné que le délai d’expiration du cache a été défini sur 10 secondes à l’aide de la méthode `SetCacheExpiration` lors de la spécification de la configuration de l’opération d’actualisation, la valeur du paramètre de configuration ne sera actualisée que si 10 secondes se sont écoulées depuis la dernière actualisation de ce paramètre.

## <a name="clean-up-resources"></a>Nettoyer les ressources

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez permis à votre application .NET Framework d’actualiser dynamiquement les paramètres de configuration à partir d’App Configuration. Pour savoir comment activer la configuration dynamique dans une application web ASP.NET (.NET Framework), passez au tutoriel suivant :

> [!div class="nextstepaction"]
> [Activer la configuration dynamique dans les applications web ASP.NET](./enable-dynamic-configuration-aspnet-netfx.md)

Pour savoir comment utiliser une identité managée Azure de façon à simplifier l’accès à App Configuration, passez au tutoriel suivant :

> [!div class="nextstepaction"]
> [Intégration des identités managées](./howto-integrate-azure-managed-service-identity.md)
