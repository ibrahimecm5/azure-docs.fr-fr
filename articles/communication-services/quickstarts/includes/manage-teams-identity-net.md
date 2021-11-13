---
title: Fichier include
description: Fichier include
services: azure-communication-services
author: gistefan
manager: soricos
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 10/08/2021
ms.topic: include
ms.custom: include file
ms.author: gistefan
ms.openlocfilehash: 717eefe0795809a9dd974ce6557d39486fbf9dd7
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131461141"
---
## <a name="set-up-prerequisites"></a>Configurer les prérequis

- Dernière version du [SDK .NET](https://dotnet.microsoft.com/download/dotnet) pour votre système d’exploitation.

## <a name="set-up"></a>Configurer

### <a name="create-a-new-c-application"></a>Créer une application C#

Dans une fenêtre de console (par exemple cmd, PowerShell ou Bash), utilisez la commande `dotnet new` pour créer une application console avec le nom `CommunicationAccessTokensQuickstart`. Cette commande crée un projet C# « Hello World » simple avec un seul fichier source : **Program.cs**.

```console
dotnet new console -o CommunicationAccessTokensQuickstart
```

Remplacez votre répertoire par le dossier d’application que vous venez de créer, puis utilisez la commande `dotnet build` pour compiler votre application.

```console
cd CommunicationAccessTokensQuickstart
dotnet build
```

### <a name="install-the-package"></a>Installer le package

Alors que vous êtes toujours dans le répertoire de l’application, installez le package de la bibliothèque Azure Communication Services Identity pour .NET à l’aide de la commande `dotnet add package`.

```console
dotnet add package Azure.Communication.Identity --prerelease
dotnet add package Microsoft.Identity.Client --version 4.36.2
```

### <a name="set-up-the-app-framework"></a>Configurer le framework d’application

À partir du répertoire de projet :

1. Ouvrez le fichier **Program.cs** dans un éditeur de texte
1. Ajoutez une directive `using` pour inclure l’espace de noms `Azure.Communication.Identity`
1. Mettez à jour la déclaration de méthode `Main` pour prendre en charge le code asynchrone

Utilisez le code suivant pour commencer :

```csharp
using System;
using System.Collections.Generic;
using System.Threading.Tasks;
using Azure.Communication.Identity;
using Microsoft.Identity.Client;

namespace CommunicationAccessTokensQuickstart
{
    class Program
    {
        static async Task Main(string[] args)
        {
            Console.WriteLine("Azure Communication Services - Teams Access Tokens Quickstart");

            // Quickstart code goes here
        }
    }
}
```

### <a name="step-1-receive-the-azure-ad-user-token-via-the-msal-library"></a>Étape 1 : Recevoir le jeton utilisateur Azure AD par le biais de la bibliothèque MSAL

La première étape du flux d’échange de jetons consiste à obtenir un jeton pour votre utilisateur Teams à l’aide de [Microsoft.Identity.Client](../../../active-directory/develop/reference-v2-libraries.md).

```csharp
string appId = "<contoso_application_id>";
string tenantId = "<contoso_tenant_id>";
string authority = $"https://login.microsoftonline.com/{tenantId}";
string redirectUri = "http://localhost";

var aadClient = PublicClientApplicationBuilder
                .Create(appId)
                .WithAuthority(authority)
                .WithRedirectUri(redirectUri)
                .Build();

string scope = "https://auth.msft.communication.azure.com/VoIP";

var teamsUserAadToken = await aadClient
                        .AcquireTokenInteractive(new List<string> { scope })
                        .ExecuteAsync();
```

### <a name="step-2-initialize-the-communicationidentityclient"></a>Étape 2 : Initialiser CommunicationIdentityClient

Initialisez un `CommunicationIdentityClient` avec votre chaîne de connexion. Le code ci-dessous récupère la chaîne de connexion de la ressource à partir d’une variable d’environnement nommée `COMMUNICATION_SERVICES_CONNECTION_STRING`. Découvrez comment [gérer la chaîne de connexion de la ressource](../create-communication-resource.md#store-your-connection-string).

Ajoutez le code suivant à la méthode `Main` :

```csharp
// This code demonstrates how to fetch your connection string
// from an environment variable.
string connectionString = Environment.GetEnvironmentVariable("COMMUNICATION_SERVICES_CONNECTION_STRING");
var client = new CommunicationIdentityClient(connectionString);
```

### <a name="step-3-exchange-the-azure-ad-user-token-for-the-teams-access-token"></a>Étape 3 : Échanger le jeton utilisateur Azure AD contre le jeton d’accès Teams

Utilisez la méthode `GetTokenForTeamsUser` afin d’émettre, pour l’utilisateur Teams, un jeton d’accès qui peut être utilisé avec les SDK Azure Communication Services.

```csharp
var accessToken = await client.GetTokenForTeamsUser(teamsUserAadToken.AccessToken);
Console.WriteLine($"Token: {accessToken.Value.Token}");
```

## <a name="run-the-code"></a>Exécuter le code

Exécutez l’application à partir de votre répertoire d’application avec la commande `dotnet run`.

```console
dotnet run
```
