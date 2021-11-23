---
title: Signer une requête HTTP avec C#
description: Ce tutoriel explique la version C# de la signature d’une requête HTTP avec une signature HMAC pour Azure Communication Services.
author: alexandra142
manager: soricos
services: azure-communication-services
ms.author: apistrak
ms.date: 06/30/2021
ms.topic: include
ms.service: azure-communication-services
ms.openlocfilehash: 1ed686906a8233d0300b43a03d03127f870e3ae5
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132429968"
---
## <a name="prerequisites"></a>Prérequis

Avant de commencer, assurez-vous de :

- Créer un compte Azure avec un abonnement actif. Pour plus d’informations, consultez [Créer un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Installer [Visual Studio](https://visualstudio.microsoft.com/downloads/).
- Créer une ressource Azure Communication Services. Pour plus d’informations, consultez [Créer des ressources Azure Communication Services](../../quickstarts/create-communication-resource.md). Vous devez enregistrer vos valeurs **resourceEndpoint** et **resourceAccessKey** pour ce tutoriel.

## <a name="sign-an-http-request-with-c"></a>Signer une requête HTTP avec C#

L’authentification par clé d’accès utilise une clé secrète partagée afin de générer une signature HMAC pour chaque requête HTTP. Cette signature est générée avec l’algorithme SHA256 et est envoyée dans l’en-tête `Authorization` à l’aide du schéma `HMAC-SHA256`. Par exemple :

```
Authorization: "HMAC-SHA256 SignedHeaders=x-ms-date;host;x-ms-content-sha256&Signature=<hmac-sha256-signature>"
```

La signature `hmac-sha256-signature` est constituée des éléments suivants :

- Verbe HTTP (par exemple `GET` ou `PUT`)
- Un chemin de requête HTTP
- x-ms-date
- Host
- x-ms-content-sha256

## <a name="setup"></a>Programme d’installation

Les étapes suivantes décrivent comment construire l’en-tête d’autorisation.

### <a name="create-a-new-c-application"></a>Créer une application C#

Dans une fenêtre de console (par exemple cmd, PowerShell ou Bash), utilisez la commande `dotnet new` pour créer une application console avec le nom `SignHmacTutorial`. Cette commande crée un projet C# « Hello World » simple avec un seul fichier source : **Program.cs**.

```console
dotnet new console -o SignHmacTutorial
```

Déplacez vos répertoires vers le dossier d’application nouvellement créé. Utilisez la commande `dotnet build` pour compiler votre application.

```console
cd SignHmacTutorial
dotnet build
```

## <a name="install-the-package"></a>Installer le package

Installez le package `Newtonsoft.Json`, utilisé pour la sérialisation du corps.

```console
dotnet add package Newtonsoft.Json
```

Mettez à jour la déclaration de la méthode `Main` pour prendre en charge le code asynchrone. Utilisez le code suivant pour commencer.

```csharp
using System;
using System.Globalization;
using System.Net.Http;
using System.Security.Cryptography;
using System.Text;
using System.Threading.Tasks;
using Newtonsoft.Json;
namespace SignHmacTutorial
{
    class Program
    {
        static async Task Main(string[] args)
        {
            Console.WriteLine("Azure Communication Services - Sign an HTTP request Tutorial");
            // Tutorial code goes here.
        }
    }
}

```

## <a name="create-a-request-message"></a>Créer un message de requête

Pour cet exemple, nous allons signer une demande de création d’une identité à l’aide de l’API Authentification Communication Services (version `2021-03-07`).

Ajoutez le code suivant à la méthode `Main` .

```csharp
string resourceEndpoint = "resourceEndpoint";
// Create a uri you are going to call.
var requestUri = new Uri($"{resourceEndpoint}/identities?api-version=2021-03-07");
// Endpoint identities?api-version=2021-03-07 accepts list of scopes as a body
var body = new[] { "chat" }; 
var serializedBody = JsonConvert.SerializeObject(body);
var requestMessage = new HttpRequestMessage(HttpMethod.Post, requestUri)
{
    Content = new StringContent(serializedBody, Encoding.UTF8)
};
```

Remplacez `resourceEndpoint` par votre valeur de point de terminaison de ressource réelle.

## <a name="create-a-content-hash"></a>Créer un hachage de contenu

Le hachage de contenu fait partie de votre signature HMAC. Utilisez le code suivant pour calculer le hachage de contenu. Vous pouvez ajouter cette méthode à `Progam.cs` sous la méthode `Main`.

```csharp
static string ComputeContentHash(string content)
{
    using (var sha256 = SHA256.Create())
    {
        byte[] hashedBytes = sha256.ComputeHash(Encoding.UTF8.GetBytes(content));
        return Convert.ToBase64String(hashedBytes);
    }
}
```

## <a name="compute-a-signature"></a>Calculer une signature

Utilisez le code suivant pour créer une méthode destinée à calculer votre signature HMAC.

```csharp
 static string ComputeSignature(string stringToSign)
{
    string secret = "resourceAccessKey";
    using (var hmacsha256 = new HMACSHA256(Convert.FromBase64String(secret)))
    {
        var bytes = Encoding.ASCII.GetBytes(stringToSign);
        var hashedBytes = hmacsha256.ComputeHash(bytes);
        return Convert.ToBase64String(hashedBytes);
    }
}
```

Remplacez `resourceAccessKey` par la clé d’accès de votre ressource Communication Services réelle.

## <a name="create-an-authorization-header-string"></a>Créer une chaîne d’en-tête d’autorisation

Nous allons maintenant construire la chaîne que nous ajouterons à notre en-tête d’autorisation.

1. Préparez les valeurs des en-têtes à signer.
   1. Spécifiez l’horodatage actuel avec le fuseau horaire UTC (Coordinated Universal Time).
   1. Obtenez l’autorité de demande (nom d’hôte DNS ou adresse IP et numéro de port).
   1. Calculer un hachage de contenu.
1. Préparer une chaîne à signer.
1. Calculer la signature.
1. Concaténer la chaîne qui sera utilisée dans l’en-tête d’autorisation.
 
Ajoutez le code suivant à la méthode `Main` .

```csharp
// Specify the 'x-ms-date' header as the current UTC timestamp according to the RFC1123 standard
var date = DateTimeOffset.UtcNow.ToString("r", CultureInfo.InvariantCulture);
// Get the host name corresponding with the 'host' header.
var host = requestUri.Authority;
// Compute a content hash for the 'x-ms-content-sha256' header.
var contentHash = ComputeContentHash(serializedBody);

// Prepare a string to sign.
var stringToSign = $"POST\n{requestUri.PathAndQuery}\n{date};{host};{contentHash}";
// Compute the signature.
var signature = ComputeSignature(stringToSign);
// Concatenate the string, which will be used in the authorization header.
var authorizationHeader = $"HMAC-SHA256 SignedHeaders=x-ms-date;host;x-ms-content-sha256&Signature={signature}";
```

## <a name="add-headers-to-requestmessage"></a>Ajouter des en-têtes à requestMessage

Utilisez le code suivant pour ajouter les en-têtes obligatoires à votre `requestMessage`.

```csharp
// Add a date header.
requestMessage.Headers.Add("x-ms-date", date);

// Add a host header.
// In C#, the 'host' header is added automatically by the 'HttpClient'. However, this step may be required on other platforms such as Node.js.

// Add a content hash header.
requestMessage.Headers.Add("x-ms-content-sha256", contentHash);

// Add an authorization header.
requestMessage.Headers.Add("Authorization", authorizationHeader);
```

## <a name="test-the-client"></a>Tester le client

Appelez le point de terminaison à l’aide de `HttpClient`, puis vérifiez la réponse.

```csharp
HttpClient httpClient = new HttpClient
{
    BaseAddress = requestUri
};
var response = await httpClient.SendAsync(requestMessage);
var responseString = await response.Content.ReadAsStringAsync();
Console.WriteLine(responseString);
```
