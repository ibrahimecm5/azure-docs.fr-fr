---
title: 'Tutoriel : Utiliser la configuration dynamique App Configuration dans ASP.NET Core'
titleSuffix: Azure App Configuration
description: Dans ce tutoriel, vous allez apprendre à mettre à jour dynamiquement les données de configuration pour les applications ASP.NET Core
services: azure-app-configuration
documentationcenter: ''
author: AlexandraKemperMS
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 09/1/2020
ms.author: alkemper
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: 4e7ce4f07e49b7efc0f62ff668e72542fc3def77
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132302004"
---
# <a name="tutorial-use-dynamic-configuration-in-an-aspnet-core-app"></a>Tutoriel : Utiliser la configuration dynamique dans une application ASP.NET Core

ASP.NET Core offre un système de configuration enfichable qui peut lire des données de configuration à partir de diverses sources. Il peut gérer les changements de manière dynamique sans entraîner le redémarrage d’une application. ASP.NET Core prend en charge la liaison des paramètres de configuration pour les classes .NET fortement typées. Il les injecte dans votre code à l'aide de `IOptionsSnapshot<T>`, qui recharge automatiquement la configuration de l'application lorsque les données sous-jacentes changent.

Ce tutoriel montre comment vous pouvez implémenter des mises à jour de la configuration dynamique dans votre code. Il s’appuie sur l’application web introduite dans les guides de démarrage rapide. Avant de continuer, terminez d’abord l’étape [Créer une application ASP.NET Core avec App Configuration](./quickstart-aspnet-core-app.md).

Vous pouvez utiliser l’éditeur de code de votre choix pour exécuter les étapes de ce tutoriel. [Visual Studio Code](https://code.visualstudio.com/) est une excellente option qui est disponible sur les plateformes Windows, macOS et Linux.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Configurer votre application pour mettre à jour sa configuration en réponse aux changements survenant dans un magasin App Configuration.
> * Injecter la configuration la plus récente dans les contrôleurs de votre application.

## <a name="prerequisites"></a>Prérequis

Pour effectuer ce tutoriel, installez le [kit SDK .NET Core](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Avant de continuer, terminez d’abord l’étape [Créer une application ASP.NET Core avec App Configuration](./quickstart-aspnet-core-app.md).

## <a name="add-a-sentinel-key"></a>Ajouter une clé Sentinel

Une *clé Sentinel* est une clé particulière que vous mettez à jour après avoir modifié toutes les autres clés. Votre application surveille la clé Sentinel. Lorsqu'un changement est détecté, votre application actualise toutes les valeurs de configuration. Cette approche permet d'assurer la cohérence de la configuration dans votre application et réduit le nombre total de requêtes adressées à App Configuration, par rapport à la surveillance de toutes les clés.

1. Sur le portail Azure, sélectionnez **Explorateur de configuration > Créer > Clé-valeur**.
1. Pour **Clé**, entrez *TestApp:Settings:Sentinel*. Pour **Valeur**, entrez 1. Laissez **Étiquette** et **Type de contenu** vides.
1. Sélectionnez **Appliquer**.

> [!NOTE]
> Si vous n'utilisez pas de clé Sentinel, vous devez inscrire manuellement chacune des clés que vous souhaitez surveiller.

## <a name="reload-data-from-app-configuration"></a>Recharger des données à partir d’Azure App Configuration

1. Ajoutez une référence au package NuGet `Microsoft.Azure.AppConfiguration.AspNetCore` en exécutant la commande suivante :

    ```dotnetcli
    dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore
    ```

1. Ouvrez *Program.cs*, puis mettez à jour la méthode `CreateWebHostBuilder` pour ajouter la méthode `config.AddAzureAppConfiguration()`.

   #### <a name="net-5x"></a>[.NET 5.x](#tab/core5x)

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
                webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
                {
                    var settings = config.Build();
                    config.AddAzureAppConfiguration(options =>
                    {
                        options.Connect(settings["ConnectionStrings:AppConfig"])
                               .ConfigureRefresh(refresh =>
                                    {
                                        refresh.Register("TestApp:Settings:Sentinel", refreshAll: true)
                                               .SetCacheExpiration(new TimeSpan(0, 5, 0));
                                    });
                    });
                })
            .UseStartup<Startup>());
    ```

    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
                webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
                {
                    var settings = config.Build();
                    config.AddAzureAppConfiguration(options =>
                    {
                        options.Connect(settings["ConnectionStrings:AppConfig"])
                               .ConfigureRefresh(refresh =>
                                    {
                                        refresh.Register("TestApp:Settings:Sentinel", refreshAll: true)
                                               .SetCacheExpiration(new TimeSpan(0, 5, 0));
                                    });
                    });
                })
            .UseStartup<Startup>());
    ```
    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();

                config.AddAzureAppConfiguration(options =>
                {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                           .ConfigureRefresh(refresh =>
                                {
                                    refresh.Register("TestApp:Settings:Sentinel", refreshAll: true)
                                           .SetCacheExpiration(new TimeSpan(0, 5, 0));
                                });
                });
            })
            .UseStartup<Startup>();
    ```
    ---

    Dans la méthode `ConfigureRefresh`, vous inscrivez les clés dans le magasin App Configuration que vous souhaitez surveiller. Le paramètre `refreshAll` de la méthode `Register` indique que toutes les valeurs de configuration doivent être actualisées si la clé inscrite change. La méthode `SetCacheExpiration` spécifie le temps minimum qui doit s'écouler avant qu'une nouvelle requête puisse être adressée à App Configuration pour rechercher les changements de configuration. Dans cet exemple, vous modifiez le délai d'expiration en remplaçant 30 secondes par 5 minutes. Cela permet de limiter le nombre potentiel de requêtes adressées à votre magasin App Configuration.

    > [!NOTE]
    > À des fins de test, vous pouvez abaisser le délai d'expiration de l'actualisation du cache.

    Pour déclencher une actualisation de la configuration, vous devez utiliser l'intergiciel App Configuration. Vous verrez comment procéder dans une étape ultérieure.

1. Ajoutez un fichier *Settings.cs* dans le répertoire Controllers, qui définit et implémente une nouvelle classe `Settings`. Remplacez l’espace de noms par le nom de votre projet. 

    ```csharp
    namespace TestAppConfig
    {
        public class Settings
        {
            public string BackgroundColor { get; set; }
            public long FontSize { get; set; }
            public string FontColor { get; set; }
            public string Message { get; set; }
        }
    }
    ```

1. Ouvrez *Startup.cs* et mettez à jour la méthode `ConfigureServices`. Appelez `Configure<Settings>` pour lier des données de configuration à la classe `Settings`. Appelez `AddAzureAppConfiguration` pour ajouter des composants App Configuration à la collection de services de votre application.

    #### <a name="net-5x"></a>[.NET 5.x](#tab/core5x)

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<Settings>(Configuration.GetSection("TestApp:Settings"));
        services.AddControllersWithViews();
        services.AddAzureAppConfiguration();
    }
    ```
    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<Settings>(Configuration.GetSection("TestApp:Settings"));
        services.AddControllersWithViews();
        services.AddAzureAppConfiguration();
    }
    ```
    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<Settings>(Configuration.GetSection("TestApp:Settings"));
        services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
        services.AddAzureAppConfiguration();
    }
    ```
    ---

1. Mettez à jour la méthode `Configure` et ajoutez un appel à `UseAzureAppConfiguration`. Cela permettra à votre application d'utiliser l'intergiciel App Configuration pour gérer automatiquement les mises à jour de la configuration.

    #### <a name="net-5x"></a>[.NET 5.x](#tab/core5x)

    ```csharp
    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }
            else
            {
                app.UseExceptionHandler("/Home/Error");
                // The default HSTS value is 30 days. You may want to change this for production scenarios, see https://aka.ms/aspnetcore-hsts.
                app.UseHsts();
            }

            // Add the following line:
            app.UseAzureAppConfiguration();

            app.UseHttpsRedirection();
            
            app.UseStaticFiles();

            app.UseRouting();

            app.UseAuthorization();

            app.UseEndpoints(endpoints =>
            {
                endpoints.MapControllerRoute(
                    name: "default",
                    pattern: "{controller=Home}/{action=Index}/{id?}");
            });
    }
    ```
    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }
            else
            {
                app.UseExceptionHandler("/Home/Error");
                // The default HSTS value is 30 days. You may want to change this for production scenarios, see https://aka.ms/aspnetcore-hsts.
                app.UseHsts();
            }

            // Add the following line:
            app.UseAzureAppConfiguration();

            app.UseHttpsRedirection();
            
            app.UseStaticFiles();

            app.UseRouting();

            app.UseAuthorization();

            app.UseEndpoints(endpoints =>
            {
                endpoints.MapControllerRoute(
                    name: "default",
                    pattern: "{controller=Home}/{action=Index}/{id?}");
            });
    }
    ```
    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        app.UseAzureAppConfiguration();

        services.Configure<CookiePolicyOptions>(options =>
        {
            options.CheckConsentNeeded = context => true;
            options.MinimumSameSitePolicy = SameSiteMode.None;
        });

        app.UseMvc();
    }
    ```
    ---
    
    > [!NOTE]
    > L'intergiciel App Configuration surveille la clé Sentinel ou toute autre clé que vous avez inscrite à l'étape précédente pour l'actualisation dans l'appel `ConfigureRefresh`. L'intergiciel est déclenché à chaque requête entrante adressée à votre application. Mais il n'envoie des requêtes de vérification de la valeur dans App Configuration que lorsque le délai d'expiration du cache que vous avez défini est écoulé. Lorsqu'un changement est détecté, si la clé Sentinel est utilisée, il met à jour toutes les configurations. Sinon, il met uniquement à jour les valeurs des clés inscrites.
    > - En cas d'échec d'une requête de détection de changements adressée à App Configuration, votre application continue à utiliser la configuration mise en cache. Une autre vérification est effectuée lorsque le délai d'expiration du cache configuré est à nouveau écoulé et que de nouvelles requêtes sont envoyées à votre application.
    > - L'actualisation de la configuration s'effectue de manière asynchrone par rapport au traitement des requêtes entrantes de votre application. Cela ne bloque pas ou ne ralentit pas la requête entrante qui a déclenché l'actualisation. La requête qui a déclenché l'actualisation peut ne pas obtenir les valeurs de configuration mises à jour, contrairement aux requêtes suivantes.
    > - Pour vous assurer que l'intergiciel est déclenché, appelez `app.UseAzureAppConfiguration()` le plus tôt possible dans votre pipeline de requêtes afin qu'un autre intergiciel ne le court-circuite pas dans votre application.

## <a name="use-the-latest-configuration-data"></a>Utiliser les données de configuration les plus récentes

1. Ouvrez *HomeController.cs* dans le répertoire Controllers et ajoutez une référence au package `Microsoft.Extensions.Options`.

    ```csharp
    using Microsoft.Extensions.Options;
    ```

2. Mettez à jour la classe `HomeController` pour recevoir `Settings` par le biais de l’injection de dépendance, puis utilisez ses valeurs.

    #### <a name="net-5x"></a>[.NET 5.x](#tab/core5x)

    ```csharp
    public class HomeController : Controller
    {
        private readonly Settings _settings;
        private readonly ILogger<HomeController> _logger;

        public HomeController(ILogger<HomeController> logger, IOptionsSnapshot<Settings> settings)
        {
            _logger = logger;
            _settings = settings.Value;
        }

        public IActionResult Index()
        {
            ViewData["BackgroundColor"] = _settings.BackgroundColor;
            ViewData["FontSize"] = _settings.FontSize;
            ViewData["FontColor"] = _settings.FontColor;
            ViewData["Message"] = _settings.Message;

            return View();
        }

        // ...
    }
    ```
    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
    public class HomeController : Controller
    {
        private readonly Settings _settings;
        private readonly ILogger<HomeController> _logger;

        public HomeController(ILogger<HomeController> logger, IOptionsSnapshot<Settings> settings)
        {
            _logger = logger;
            _settings = settings.Value;
        }

        public IActionResult Index()
        {
            ViewData["BackgroundColor"] = _settings.BackgroundColor;
            ViewData["FontSize"] = _settings.FontSize;
            ViewData["FontColor"] = _settings.FontColor;
            ViewData["Message"] = _settings.Message;

            return View();
        }

        // ...
    }
    ```
    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public class HomeController : Controller
    {
        private readonly Settings _settings;
        public HomeController(IOptionsSnapshot<Settings> settings)
        {
            _settings = settings.Value;
        }

        public IActionResult Index()
        {
            ViewData["BackgroundColor"] = _settings.BackgroundColor;
            ViewData["FontSize"] = _settings.FontSize;
            ViewData["FontColor"] = _settings.FontColor;
            ViewData["Message"] = _settings.Message;

            return View();
        }
    }
    ```
    ---
    > [!Tip]
    > Pour en savoir plus sur le modèle d’options pour la lecture des valeurs de configuration, consultez [Modèles d’options dans ASP.NET Core](/aspnet/core/fundamentals/configuration/options).

3. Ouvrez *Index.cshtml* dans le répertoire Views > Home, puis remplacez son contenu par le script suivant :

    ```html
    <!DOCTYPE html>
    <html lang="en">
    <style>
        body {
            background-color: @ViewData["BackgroundColor"]
        }
        h1 {
            color: @ViewData["FontColor"];
            font-size: @ViewData["FontSize"]px;
        }
    </style>
    <head>
        <title>Index View</title>
    </head>
    <body>
        <h1>@ViewData["Message"]</h1>
    </body>
    </html>
    ```

## <a name="build-and-run-the-app-locally"></a>Générer et exécuter l’application localement

1. Pour générer l’application à l’aide de l’interface CLI .NET Core, exécutez la commande suivante dans l’interpréteur de commandes :

    ```console
        dotnet build
    ```

1. Une fois la génération correctement terminée, exécutez la commande suivante pour exécuter l’application web localement :

    ```console
        dotnet run
    ```

1. Ouvrez une fenêtre de navigateur, puis accédez à l’URL figurant dans la sortie de `dotnet run`.

    ![Lancement local de l’application du démarrage rapide](./media/quickstarts/aspnet-core-app-launch-local-before.png)

1. Connectez-vous au [portail Azure](https://portal.azure.com). Sélectionnez **Toutes les ressources**, puis sélectionnez l’instance du magasin App Configuration que vous avez créée dans le guide de démarrage rapide.

1. Sélectionnez **Explorateur de configuration** et mettez à jour les valeurs des clés suivantes. N'oubliez pas de mettre à jour la clé Sentinelle.

    | Clé | Valeur |
    |---|---|
    | TestApp:Settings:BackgroundColor | green |
    | TestApp:Settings:FontColor | lightGray |
    | TestApp:Settings:Message | Données provenant d’Azure App Configuration, avec maintenant des mises à jour automatiques ! |
    | TestApp:Settings:Sentinel | 2 |

1. Actualisez la page de navigateur pour afficher les nouveaux paramètres de configuration. Vous devrez peut-être effectuer plusieurs actualisations pour que les changements soient pris en compte, ou redéfinir le délai d'expiration de votre cache sur une valeur inférieure à 5 minutes. 

    ![Lancement local de l’application mise à jour du démarrage rapide](./media/quickstarts/aspnet-core-app-launch-local-after.png)

## <a name="clean-up-resources"></a>Nettoyer les ressources

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez permis à votre application web ASP.NET Core d’actualiser dynamiquement les paramètres de configuration à partir d’App Configuration. Pour savoir comment utiliser une identité managée Azure de façon à simplifier l’accès à App Configuration, passez au tutoriel suivant.

> [!div class="nextstepaction"]
> [Intégration des identités managées](./howto-integrate-azure-managed-service-identity.md)
