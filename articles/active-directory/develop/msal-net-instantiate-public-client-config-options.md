---
title: Instancier une application cliente publique (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Découvrez comment instancier une application cliente publique avec des options de configuration à l’aide de Microsoft Authentication Library for .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 04/30/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 57d7cba072e51483732d103d358910ba443a525a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131059411"
---
# <a name="instantiate-a-public-client-application-with-configuration-options-using-msalnet"></a>Instancier une application cliente publique avec des options de configuration à l’aide de MSAL.NET

Cet article décrit comment instancier une [application cliente publique](msal-client-applications.md) à l’aide de la bibliothèque d’authentification Microsoft pour .NET (MSAL.NET).  L’application est instanciée avec les options de configuration définies dans un fichier de paramètres.

Avant d’initialiser une application, vous devez d’abord [l’inscrire](quickstart-register-app.md) afin que votre application puisse être intégrée à la plateforme d’identités Microsoft. Après l’inscription, vous aurez peut-être besoin des informations suivantes (que vous trouverez sur le Portail Azure) :

- L’ID client (une chaîne représentant un GUID)
- L’URL du fournisseur d’identité (l’instance) et l’audience de connexion pour votre application. Ces deux paramètres représentent collectivement l’autorité.
- L’ID locataire si vous écrivez une application métier uniquement pour votre organisation (également nommée application à locataire unique).
- Pour les applications web et parfois pour les applications clientes publiques (en particulier lorsque votre application doit utiliser un répartiteur), vous devez également avoir défini l’URI de redirection auquel le fournisseur d’identité recontactera votre application avec les jetons de sécurité.

## <a name="default-reply-uri"></a>URI de réponse par défaut

Dans MSAL.NET 4.1+, l’URI de redirection par défaut (URI de réponse) peut maintenant être défini à l’aide de la méthode `public PublicClientApplicationBuilder WithDefaultRedirectUri()`. Cette méthode permet de définir la propriété d’URI de redirection de l’application cliente publique sur la valeur par défaut recommandée.

Le comportement de cette méthode dépend de la plateforme que vous utilisez à ce moment-là. Voici un tableau qui décrit l’URI de redirection qui est défini sur certaines plateformes :

Plateforme  | URI de redirection  
---------  | --------------
Application de bureau (.NET FW) | `https://login.microsoftonline.com/common/oauth2/nativeclient` 
UWP | Valeur de `WebAuthenticationBroker.GetCurrentApplicationCallbackUri()`
.NET Core | `http://localhost`

Pour la plateforme UWP, l’expérience a été améliorée en activant l’authentification unique avec le navigateur en définissant la valeur sur le résultat de `WebAuthenticationBroker.GetCurrentApplicationCallbackUri()`. 

Pour .NET Core, MSAL.Net définit la valeur sur l’hôte local pour permettre à l’utilisateur d’utiliser le navigateur système pour l’authentification interactive.

> [!NOTE]
> Pour les navigateurs incorporés dans les scénarios de bureau, l’URI de redirection utilisé est intercepté par MSAL pour détecter qu’une réponse est renvoyée par le fournisseur d’identité indiquant qu’un code d’authentification a été renvoyé. Cet URI peut donc être utilisé dans n’importe quel cloud sans voir une redirection réelle vers cet URI. Cela signifie que vous pouvez et devez utiliser `https://login.microsoftonline.com/common/oauth2/nativeclient` dans n’importe quel cloud. Si vous préférez, vous pouvez également utiliser un autre URI tant que vous configurez correctement l’URI de redirection avec MSAL et dans l’inscription de l’application. La spécification de l’URI par défaut dans l’inscription de l’application signifie qu’il y a un paramétrage moindre dans MSAL.


Une application de console .NET Core peut avoir le fichier de configuration *appsettings.json* suivant :

```json
{
  "Authentication": {
    "AzureCloudInstance": "AzurePublic",
    "AadAuthorityAudience": "AzureAdMultipleOrgs",
    "ClientId": "ebe2ab4d-12b3-4446-8480-5c3828d04c50"
  },

  "WebAPI": {
    "MicrosoftGraphBaseEndpoint": "https://graph.microsoft.com"
  }
}
```

Le code suivant lit ce fichier à l’aide de l’infrastructure de configuration .NET :

```csharp
public class SampleConfiguration
{
    /// <summary>
    /// Authentication options
    /// </summary>
    public PublicClientApplicationOptions PublicClientApplicationOptions { get; set; }

    /// <summary>
    /// Base URL for Microsoft Graph (it varies depending on whether the application is ran
    /// in Microsoft Azure public clouds or national / sovereign clouds
    /// </summary>
    public string MicrosoftGraphBaseEndpoint { get; set; }

    /// <summary>
    /// Reads the configuration from a json file
    /// </summary>
    /// <param name="path">Path to the configuration json file</param>
    /// <returns>SampleConfiguration as read from the json file</returns>
    public static SampleConfiguration ReadFromJsonFile(string path)
    {
        // .NET configuration
        IConfigurationRoot Configuration;
        var builder = new ConfigurationBuilder()
          .SetBasePath(Directory.GetCurrentDirectory())
        .AddJsonFile(path);
        Configuration = builder.Build();

        // Read the auth and graph endpoint config
        SampleConfiguration config = new SampleConfiguration()
        {
            PublicClientApplicationOptions = new PublicClientApplicationOptions()
        };
        Configuration.Bind("Authentication", config.PublicClientApplicationOptions);
        config.MicrosoftGraphBaseEndpoint = Configuration.GetValue<string>("WebAPI:MicrosoftGraphBaseEndpoint");
        return config;
    }
}
```

Le code suivant crée votre application à l’aide de la configuration du fichier de paramètres :

```csharp
SampleConfiguration config = SampleConfiguration.ReadFromJsonFile("appsettings.json");
var app = PublicClientApplicationBuilder.CreateWithApplicationOptions(config.PublicClientApplicationOptions)
           .Build();
```
