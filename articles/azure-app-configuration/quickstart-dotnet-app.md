---
title: Démarrage rapide pour Azure App Configuration avec .NET Framework | Microsoft Docs
description: Dans cet article, créez une application .NET Framework avec Azure App Configuration pour centraliser le stockage et la gestion des paramètres d’application en dehors de votre code.
services: azure-app-configuration
documentationcenter: ''
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.custom: devx-track-csharp
ms.topic: quickstart
ms.date: 09/28/2020
ms.author: alkemper
ms.openlocfilehash: f5bd0809de5aa7e7e585662ee0c98feaf9be2e79
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130245092"
---
# <a name="quickstart-create-a-net-framework-app-with-azure-app-configuration"></a>Démarrage rapide : Créer une application .NET Framework avec Azure App Configuration

Il existe deux façons d’incorporer Azure App Configuration dans une application .NET Framework.
- Le générateur de configuration pour App Configuration permet de charger des données à partir d’App Configuration dans les paramètres de l’application. Votre application accède à la configuration comme elle le fait toujours via `ConfigurationManager`. Vous n’avez pas besoin d’apporter de changements au code autres que les mises à jour des fichiers *app.config* ou *web.config*. Ce guide de démarrage rapide vous guidera tout au long de cette option.
- Comme conçu par le .NET Framework, les paramètres de l’application ne peuvent être actualisés que lors du redémarrage de l’application. Le fournisseur .NET d’App Configuration est une bibliothèque .NET Standard. Il prend en charge la mise en cache et l’actualisation dynamiques de la configuration sans redémarrage de l’application. Si la configuration dynamique est essentielle pour vous et que vous êtes disposé à apporter des changements au code, consultez les tutoriels sur la façon dont vous pouvez implémenter des mises à jour de configuration dynamiques dans une [application console .NET Framework](./enable-dynamic-configuration-dotnet.md) ou une [application web ASP.NET](./enable-dynamic-configuration-aspnet-netfx.md).

Dans ce guide de démarrage rapide, une application console .NET Framework est utilisée comme exemple, mais la même technique s’applique à une application ASP.NET Web Forms/MVC.

## <a name="prerequisites"></a>Prérequis

- Abonnement Azure : [créez-en un gratuitement](https://azure.microsoft.com/free/dotnet)
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

## <a name="connect-to-an-app-configuration-store"></a>Se connecter à un magasin App Configuration

1. Cliquez avec le bouton droit sur votre projet, puis sélectionnez **Gérer les packages NuGet**. Sous l’onglet **Parcourir**, recherchez et ajoutez les packages NuGet suivants à votre projet.

    - *Microsoft.Configuration.ConfigurationBuilders.AzureAppConfiguration* version 1.0.0 ou ultérieure
    - *Microsoft.Configuration.ConfigurationBuilders.Environment* version 2.0.0 ou ultérieure
    - *System.Configuration.ConfigurationManager* version 4.6.0 ou ultérieure

1. Mettez à jour le fichier *App.config* de votre projet comme suit :

    ```xml
    <configSections>
        <section name="configBuilders" type="System.Configuration.ConfigurationBuildersSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" restartOnExternalChanges="false" requirePermission="false" />
    </configSections>

    <configBuilders>
        <builders>
            <add name="MyConfigStore" mode="Greedy" connectionString="${ConnectionString}" type="Microsoft.Configuration.ConfigurationBuilders.AzureAppConfigurationBuilder, Microsoft.Configuration.ConfigurationBuilders.AzureAppConfiguration" />
            <add name="Environment" mode="Greedy" type="Microsoft.Configuration.ConfigurationBuilders.EnvironmentConfigBuilder, Microsoft.Configuration.ConfigurationBuilders.Environment" />
        </builders>
    </configBuilders>

    <appSettings configBuilders="Environment,MyConfigStore">
        <add key="AppName" value="Console App Demo" />
        <add key="ConnectionString" value ="Set via an environment variable - for example, dev, test, staging, or production connection string." />
    </appSettings>
    ```

   La chaîne de connexion de votre magasin App Configuration est lue à partir de la variable d’environnement `ConnectionString`. Ajoutez le générateur de configuration `Environment` avant `MyConfigStore` dans la propriété `configBuilders` à la section `appSettings`.

1. Ouvrez *Program.cs*, puis mettez à jour la méthode `Main` pour utiliser App Configuration en appelant `ConfigurationManager`.

    ```csharp
    static void Main(string[] args)
    {
        string message = System.Configuration.ConfigurationManager.AppSettings["TestApp:Settings:Message"];

        Console.WriteLine(message);
        Console.ReadKey();
    }
    ```

## <a name="build-and-run-the-app"></a>Générer et exécuter l’application

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

1. Appuyez sur Ctrl + F5 pour générer et exécuter l’application console. Vous devez voir le message généré par les sorties App Configuration dans la console.

## <a name="clean-up-resources"></a>Nettoyer les ressources

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Étapes suivantes

En suivant ce guide de démarrage rapide, vous avez créé un magasin App Configuration et vous l’avez utilisé avec une application console .NET Framework. Pour savoir comment permettre à votre application .NET Framework d’actualiser dynamiquement les paramètres de configuration, passez aux tutoriels suivants.

> [!div class="nextstepaction"]
> [Activer la configuration dynamique dans une application .NET Framework](./enable-dynamic-configuration-dotnet.md)

> [!div class="nextstepaction"]
> [Activer la configuration dynamique dans une application web ASP.NET](./enable-dynamic-configuration-aspnet-netfx.md)
