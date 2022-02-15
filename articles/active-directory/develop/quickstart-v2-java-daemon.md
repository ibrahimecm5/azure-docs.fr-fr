---
title: 'Démarrage rapide : Appeler Microsoft Graph à partir d’un démon Java | Azure'
titleSuffix: Microsoft identity platform
description: Dans ce guide de démarrage rapide, vous allez découvrir comment une application Java peut obtenir un jeton d’accès et appeler une API protégée par un point de terminaison de plateforme d’identités Microsoft, à l’aide de la propre identité de l’application
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: portal
ms.workload: identity
ms.date: 01/10/2022
ROBOTS: NOINDEX
ms.author: marsma
ms.custom: aaddev, "scenarios:getting-started", "languages:Java", devx-track-java, mode-api
ms.openlocfilehash: 63a1a94fc90c96eca327043d001383f65fedbaca
ms.sourcegitcommit: 8d56da997b0b3ab07f91f6bef0c5661847758c4a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2022
ms.locfileid: "138036411"
---
# <a name="quickstart-acquire-a-token-and-call-microsoft-graph-api-from-a-java-console-app-using-apps-identity"></a>Démarrage rapide : Acquérir un jeton et appeler l’API Microsoft Graph à partir d’une application console Java à l’aide de l’identité de l’application

> [!div renderon="docs"]
> Bienvenue ! Ce n’est probablement pas la page que vous attendiez. Pendant que nous travaillons sur un correctif, ce lien devrait vous permettre d’accéder au bon article :
>
> > [Démarrage rapide : démon Java qui appelle une API protégée](console-app-quickstart.md?pivots=devlang-java)
> 
> Nous vous prions de nous excuser pour le désagrément et nous vous remercions de votre patience.

> [!div renderon="portal" class="sxs-lookup"]
> Dans ce guide de démarrage rapide, vous téléchargez et exécutez un exemple de code qui montre comment une application Java peut obtenir un jeton d’accès à l’aide de l’identité de l’application pour appeler l’API Microsoft Graph et afficher une [liste d’utilisateurs](/graph/api/user-list) dans l’annuaire. L’exemple de code montre comment un travail sans assistance ou un service Windows peut s’exécuter avec l’identité d’une application, au lieu de l’identité d’un utilisateur.
> 
> ## <a name="prerequisites"></a>Prérequis
> 
> Pour exécuter cet exemple, vous avec besoin de ce qui suit :
> 
> - [Java Development Kit (JDK)](https://openjdk.java.net/) 8 ou version ultérieure
> - [Maven](https://maven.apache.org/)
> 
> > [!div class="sxs-lookup"]
> ### <a name="download-and-configure-the-quickstart-app"></a>Télécharger et configurer l’application de démarrage rapide
> 
> #### <a name="step-1-configure-the-application-in-azure-portal"></a>Étape 1 : Configurer l’application dans le portail Azure
> Pour que l’exemple de code fonctionne dans ce guide de démarrage rapide, vous devez créer un secret client et ajouter l’autorisation d’application **User.Read.All** de l’API Graph.
> > [!div class="nextstepaction"]
> > [Apporter ces modifications pour moi]()
> 
> > [!div class="alert alert-info"]
> > ![Déjà configuré](media/quickstart-v2-netcore-daemon/green-check.png) Votre application est configurée avec ces attributs.
> 
> #### <a name="step-2-download-the-java-project"></a>Étape 2 : Télécharger le projet Java
> 
> > [!div class="sxs-lookup nextstepaction"]
> > [Téléchargez l’exemple de code](https://github.com/Azure-Samples/ms-identity-java-daemon/archive/master.zip).
> 
> > [!div class="sxs-lookup"]
> > > [!NOTE]
> > > `Enter_the_Supported_Account_Info_Here`
> 
> #### <a name="step-3-admin-consent"></a>Étape 3 : Consentement de l’administrateur
> 
> Si vous essayez d’exécuter l’application à ce stade, vous recevez l’erreur *HTTP 403 - Interdit* : `Insufficient privileges to complete the operation`. Cette erreur se produit parce que toute *autorisation d’application uniquement* nécessite le consentement de l’administrateur. Autrement dit, un administrateur général de votre annuaire doit donner son consentement à votre application. Sélectionnez l’une des options ci-dessous en fonction de votre rôle :
> 
> ##### <a name="global-tenant-administrator"></a>Administrateur de locataires général
> 
> Si vous êtes administrateur général, accédez à la page **Autorisations de l’API** et sélectionnez **Accorder le consentement administrateur pour Entrer_le_nom_du_locataire_ici**.
> > [!div id="apipermissionspage"]
> > [Accéder à la page Autorisations de l’API]()
> 
> ##### <a name="standard-user"></a>Utilisateur standard
> 
> Si vous êtes un utilisateur standard de votre locataire, vous devez demander à un administrateur général de vous accorder son consentement pour votre application. Pour ce faire, donnez l’URL suivante à votre administrateur :
> 
> ```url
> https://login.microsoftonline.com/Enter_the_Tenant_Id_Here/adminconsent?client_id=Enter_the_Application_Id_Here
> ```
> #### <a name="step-4-run-the-application"></a>Étape 4 : Exécution de l'application
> 
> Vous pouvez tester l’exemple directement en exécutant la méthode main de ClientCredentialGrant.java à partir de votre IDE.
> 
> À partir de votre interpréteur de commandes ou de votre ligne de commande :
> 
> ```
> $ mvn clean compile assembly:single
> ```
> 
> Cela entraîne la génération d’un fichier msal-client-credential-secret-1.0.0.jar dans votre répertoire /targets. Exécutez celui-ci à l’aide de votre exécutable Java, comme indiqué ci-dessous :
> 
> ```
> $ java -jar msal-client-credential-secret-1.0.0.jar
> ```
> 
> Une fois l’application exécutée, elle doit afficher la liste des utilisateurs du locataire configuré.
> 
> > [!IMPORTANT]
> > Cette application de démarrage rapide utilise un secret client pour s’identifier en tant que client confidentiel. Le secret client étant ajouté en texte brut à vos fichiers projet, il est recommandé, pour des raisons de sécurité, d’utiliser un certificat au lieu d’un secret client avant de considérer l’application comme application de production. Pour plus d’informations sur l’utilisation d’un certificat, consultez [ces instructions](https://github.com/Azure-Samples/ms-identity-java-daemon/tree/master/msal-client-credential-certificate) dans le même dépôt GitHub que celui de cet exemple, mais dans le second dossier **msal-client-credential-certificate**.
> 
> ## <a name="more-information"></a>Informations complémentaires
> 
> ### <a name="msal-java"></a>MSAL Java
> 
> [MSAL Java](https://github.com/AzureAD/microsoft-authentication-library-for-java) est la bibliothèque utilisée pour connecter les utilisateurs et demander des jetons permettant d’accéder à une API protégée par la plateforme d’identités Microsoft. Comme vous l’avez vu, ce guide de démarrage rapide demande des jetons à l’aide de l’identité de l’application au lieu d’autorisations déléguées. Le flux d’authentification utilisé dans ce cas est désigné sous le terme de *[flux d’informations d’identification du client OAuth](v2-oauth2-client-creds-grant-flow.md)* . Pour plus d’informations sur l’utilisation de MSAL Java avec des applications démon, consultez [cet article](scenario-daemon-overview.md).
> 
> Ajoutez MSAL4J à votre application en utilisant Maven ou Gradle pour gérer vos dépendances. Pour cela, apportez les modifications suivantes au fichier pom.xml (Maven) ou build.gradle (Gradle) de votre application.
> 
> Dans pom.xml :
> 
> ```xml
> <dependency>
>     <groupId>com.microsoft.azure</groupId>
>     <artifactId>msal4j</artifactId>
>     <version>1.0.0</version>
> </dependency>
> ```
> 
> Dans build.gradle :
> 
> ```$xslt
> compile group: 'com.microsoft.azure', name: 'msal4j', version: '1.0.0'
> ```
> 
> ### <a name="msal-initialization"></a>Initialisation MSAL
> 
> Ajoutez la référence à MSAL for Java en ajoutant le code suivant au début du fichier où vous allez utiliser MSAL4J :
> 
> ```Java
> import com.microsoft.aad.msal4j.*;
> ```
> 
> Ensuite, initialisez MSAL à l’aide du code suivant :
> 
> ```Java
> IClientCredential credential = ClientCredentialFactory.createFromSecret(CLIENT_SECRET);
> 
> ConfidentialClientApplication cca =
>         ConfidentialClientApplication
>                 .builder(CLIENT_ID, credential)
>                 .authority(AUTHORITY)
>                 .build();
> ```
> 
> > | Où : |Description |
> > |---------|---------|
> > | `CLIENT_SECRET` | Est le secret client créé pour l’application dans le portail Azure. |
> > | `CLIENT_ID` | Est l’**ID d’application (client)** de l’application inscrite dans le portail Azure. Vous pouvez retrouver cette valeur dans la page **Vue d’ensemble** de l’application dans le portail Azure. |
> > | `AUTHORITY`    | Point de terminaison STS pour l’utilisateur à authentifier. Généralement `https://login.microsoftonline.com/{tenant}` pour un cloud public, où {tenant} est le nom ou l’ID de votre locataire.|
> 
> ### <a name="requesting-tokens"></a>Demande de jetons
> 
> Pour demander un jeton à l’aide de l’identité d’application, utilisez la méthode `acquireToken` :
> 
> ```Java
> IAuthenticationResult result;
>      try {
>          SilentParameters silentParameters =
>                  SilentParameters
>                          .builder(SCOPE)
>                          .build();
> 
>          // try to acquire token silently. This call will fail since the token cache does not
>          // have a token for the application you are requesting an access token for
>          result = cca.acquireTokenSilently(silentParameters).join();
>      } catch (Exception ex) {
>          if (ex.getCause() instanceof MsalException) {
> 
>              ClientCredentialParameters parameters =
>                      ClientCredentialParameters
>                              .builder(SCOPE)
>                              .build();
> 
>              // Try to acquire a token. If successful, you should see
>              // the token information printed out to console
>              result = cca.acquireToken(parameters).join();
>          } else {
>              // Handle other exceptions accordingly
>              throw ex;
>          }
>      }
>      return result;
> ```
> 
> > |Où :| Description |
> > |---------|---------|
> > | `SCOPE` | Contient les étendues demandées. Pour les clients confidentiels, utilisez un format similaire à `{Application ID URI}/.default`. Ce format indique que les étendues demandées sont celles qui sont définies de manière statique dans l’objet application défini dans le portail Azure (pour Microsoft Graph, `{Application ID URI}` pointe vers `https://graph.microsoft.com`). Pour les API web personnalisées, `{Application ID URI}` est défini sous la section **Exposer une API** dans **Inscription d’applications** sur le portail Azure.|
> 
> [!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
> 
> ## <a name="next-steps"></a>Étapes suivantes
> 
> Pour en savoir plus sur les applications démon, consultez la page de destination du scénario.
> 
> > [!div class="nextstepaction"]
> > [Application démon qui appelle des API web](scenario-daemon-overview.md)
