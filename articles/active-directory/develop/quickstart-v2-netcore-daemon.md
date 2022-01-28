---
title: 'Démarrage rapide : Obtenir un jeton et appeler Microsoft Graph dans une application console | Azure'
titleSuffix: Microsoft identity platform
description: Dans ce guide de démarrage rapide, vous allez apprendre comment un exemple d’application .NET Core peut utiliser le workflow d’informations d’identification du client pour obtenir un jeton et appeler Microsoft Graph.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: portal
ms.workload: identity
ms.date: 01/10/2022
ROBOTS: NOINDEX
ms.author: jmprieur
ms.reviewer: marsma
ms.custom: devx-track-csharp, aaddev, identityplatformtop40, "scenarios:getting-started", "languages:aspnet-core", mode-api
ms.openlocfilehash: 417fb1f2c11822b8c475324c15c27a24a1ff49f0
ms.sourcegitcommit: 3f20f370425cb7d51a35d0bba4733876170a7795
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/27/2022
ms.locfileid: "137803928"
---
# <a name="quickstart-get-a-token-and-call-the-microsoft-graph-api-by-using-a-console-apps-identity"></a>Démarrage rapide : acquérir un jeton et appeler l’API Microsoft Graph à partir d’une de l’identité de l’application d’une console

Dans ce guide de démarrage rapide, vous téléchargez et exécutez un exemple de code qui montre comment une application console .NET Core peut obtenir un jeton d’accès pour appeler l’API Microsoft Graph et afficher une [liste des utilisateurs](/graph/api/user-list) dans l’annuaire. L’exemple de code montre également comment un travail ou un service Windows peut s’exécuter avec l’identité d’une application, au lieu de l’identité d’un utilisateur. L’exemple d’application de console dans ce guide de démarrage rapide est également une application démon. Il s’agit donc d’une application cliente confidentielle.

## <a name="prerequisites"></a>Prérequis

Ce guide de démarrage rapide nécessite le [SDK .NET Core 3.1](https://dotnet.microsoft.com/download), mais fonctionne également avec le SDK .NET 5.0.

> [!div class="sxs-lookup"]
### <a name="download-and-configure-your-quickstart-app"></a>Télécharger et configurer votre application de démarrage rapide

#### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Étape 1 : Configurer votre application dans le portail Azure
Pour que l’exemple de code de ce guide de démarrage rapide fonctionne, créez un secret client et ajoutez l’autorisation d’application **User.Read.All** de l’API Graph.
> [!div class="nextstepaction"]
> [Apporter ces modifications pour moi]()

> [!div class="alert alert-info"]
> ![Déjà configuré](media/quickstart-v2-netcore-daemon/green-check.png) Votre application est configurée avec ces attributs.

#### <a name="step-2-download-your-visual-studio-project"></a>Étape 2 : Télécharger votre projet Visual Studio

> [!div class="sxs-lookup"]
> Exécutez le projet avec Visual Studio 2019.
> [!div class="sxs-lookup" id="autoupdate" class="nextstepaction"]
> [Téléchargez l’exemple de code](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/archive/master.zip).

[!INCLUDE [active-directory-develop-path-length-tip](../../../includes/active-directory-develop-path-length-tip.md)]

> [!div class="sxs-lookup"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

#### <a name="step-3-admin-consent"></a>Étape 3 : Consentement de l’administrateur

Si vous essayez d’exécuter l’application à ce stade, vous recevrez une erreur *HTTP 403 - Interdit* : « privilèges insuffisants pour terminer l’opération ». Cette erreur se produit parce que les autorisations d’application uniquement nécessitent un administrateur général de votre annuaire pour donner son consentement à votre application. Sélectionnez l’une des options suivantes en fonction de votre rôle.

##### <a name="global-tenant-administrator"></a>Administrateur de locataires général

Si vous êtes administrateur général, accédez à la page **Autorisations de l’API** et sélectionnez **Accorder le consentement administrateur pour Entrer_le_nom_du_locataire_ici**.
> [!div id="apipermissionspage"]
> [Accéder à la page Autorisations de l’API]()

##### <a name="standard-user"></a>Utilisateur standard

Si vous êtes un utilisateur standard de votre locataire, demandez à un administrateur général de vous accorder le consentement administrateur pour votre application. Pour ce faire, donnez l’URL suivante à votre administrateur :

```url
https://login.microsoftonline.com/Enter_the_Tenant_Id_Here/adminconsent?client_id=Enter_the_Application_Id_Here
```

L’erreur « AADSTS50011 : aucune adresse de réponse n’est inscrite pour l’application » peut s’afficher après avoir l’octroi du consentement pour l’application à l’aide de l’URL précédente. Cette erreur se produit parce que cette application et l’URL n’ont pas d’URI de redirection. Vous pouvez l’ignorer.

#### <a name="step-4-run-the-application"></a>Étape 4 : Exécution de l'application

Si vous utilisez Visual Studio ou Visual Studio pour Mac, appuyez sur **F5** pour exécuter l’application. Dans le cas contraire, exécutez l’application via l’invite de commandes, la console ou le terminal :

```dotnetcli
cd {ProjectFolder}\1-Call-MSGraph\daemon-console
dotnet run
```
Dans ce code :
* `{ProjectFolder}` est le dossier où vous avez extrait le fichier zip. par exemple `C:\Azure-Samples\active-directory-dotnetcore-daemon-v2`.

Une liste des utilisateurs dans Azure Active Directory s’affiche normalement.

Cette application de démarrage rapide utilise un secret client pour s’identifier en tant que client confidentiel. La clé secrète client est ajoutée sous la forme d’un fichier de texte brut à vos fichiers projet. Pour des raisons de sécurité, nous vous recommandons d’utiliser un certificat plutôt qu’une clé secrète client avant de considérer l’application comme une application de production. Pour plus d’informations sur la façon d’utiliser un certificat, consultez [ces instructions](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/#variation-daemon-application-using-client-credentials-with-certificates) dans le dépôt GitHub pour cet exemple.

## <a name="more-information"></a>Informations complémentaires
Cette section offre une vue d’ensemble du code requis pour connecter les utilisateurs. Cette vue d’ensemble peut être utile pour comprendre comment le code fonctionne, quels sont les principaux arguments et comment ajouter une connexion à une application de console .NET Core existante.

> [!div class="sxs-lookup"]
### <a name="how-the-sample-works"></a>Fonctionnement de l’exemple

![Diagramme montrant le fonctionnement de l’exemple d’application généré par ce guide de démarrage rapide.](media/quickstart-v2-netcore-daemon/netcore-daemon-intro.svg)

### <a name="msalnet"></a>MSAL.NET

La bibliothèque d’authentification Microsoft (MSAL, dans le package [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)) est la bibliothèque utilisée pour connecter les utilisateurs et demander des jetons pour accéder à une API protégée par la plateforme d’identité Microsoft. Ce guide de démarrage rapide demande des jetons à l’aide de l’identité de l’application au lieu d’autorisations déléguées. Le flux d’authentification utilisé dans ce cas est désigné sous le terme de [flux d’informations d’identification du client OAuth](v2-oauth2-client-creds-grant-flow.md). Pour plus d’informations sur l’utilisation de MSAL.NET avec un flux d’informations d’identification du client, consultez [cet article](https://aka.ms/msal-net-client-credentials).

 Vous pouvez installer MSAL.NET en exécutant la commande suivante dans la console du gestionnaire de package de Visual Studio :

```dotnetcli
dotnet add package Microsoft.Identity.Client
```

### <a name="msal-initialization"></a>Initialisation MSAL

Vous pouvez ajouter la référence de MSAL en ajoutant le code suivant :

```csharp
using Microsoft.Identity.Client;
```

Ensuite, initialisez MSAL à l’aide du code suivant :

```csharp
IConfidentialClientApplication app;
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientSecret(config.ClientSecret)
                                          .WithAuthority(new Uri(config.Authority))
                                          .Build();
```

 | Élément | Description |
 |---------|---------|
 | `config.ClientSecret` | La clé secrète client créée pour l’application dans le portail Azure. |
 | `config.ClientId` | L’ID d’application (client) de l’application inscrite dans le portail Azure. Vous pouvez retrouver cette valeur dans la page **Vue d’ensemble** de l’application dans le portail Azure. |
 | `config.Authority`    | (Facultatif) Point de terminaison du service d’émission de jeton de sécurité (STS) pour l’utilisateur à authentifier. Il s’agit généralement de `https://login.microsoftonline.com/{tenant}` pour le cloud public, où `{tenant}` est le nom ou l’ID de votre locataire.|

Pour plus d’informations, consultez la [documentation de référence pour `ConfidentialClientApplication`](/dotnet/api/microsoft.identity.client.iconfidentialclientapplication).

### <a name="requesting-tokens"></a>Demande de jetons

Pour demander un jeton à l’aide de l’identité d’application, utilisez la méthode `AcquireTokenForClient` :

```csharp
result = await app.AcquireTokenForClient(scopes)
                  .ExecuteAsync();
```

|Élément| Description |
|---------|---------|
| `scopes` | Contient les étendues demandées. Pour les clients confidentiels, cette valeur doit utiliser un format similaire à `{Application ID URI}/.default`. Ce format indique que les étendues demandées sont celles qui sont définies statiquement dans l’objet d’application défini sur le Portail Azure. Pour Microsoft Graph, `{Application ID URI}` pointe vers `https://graph.microsoft.com` . Pour les API web personnalisées, `{Application ID URI}` est défini sur le portail Azure, sous **Inscription d’application (préversion)**  > **Exposer une API**. |

Pour plus d’informations, consultez la [documentation de référence pour `AcquireTokenForClient`](/dotnet/api/microsoft.identity.client.confidentialclientapplication.acquiretokenforclient).

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les applications démon, consultez la vue d’ensemble du scénario :

> [!div class="nextstepaction"]
> [Application démon qui appelle des API web](scenario-daemon-overview.md)
