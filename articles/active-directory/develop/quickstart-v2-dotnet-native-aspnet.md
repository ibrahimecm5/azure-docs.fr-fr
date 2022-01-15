---
title: 'Démarrage rapide : Appeler une API web ASP.NET qui est protégée par la plateforme d’identités Microsoft | Azure'
titleSuffix: Microsoft identity platform
description: Ce guide de démarrage rapide explique comment appeler une API web ASP.NET qui est protégée par la plateforme d’identités Microsoft à partir d’une application Windows Desktop (WPF).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: portal
ms.workload: identity
ms.date: 01/11/2022
ms.author: jmprieur
ms.custom: devx-track-csharp, aaddev, identityplatformtop40, "scenarios:getting-started", "languages:ASP.NET", mode-api
ms.openlocfilehash: 57bcd6562862210be21c17680842de44168c435b
ms.sourcegitcommit: 04420fb4695bd70408d9854ad5b2af8a9bbfbc64
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/15/2022
ms.locfileid: "136847809"
---
# <a name="quickstart-call-an-aspnet-web-api-thats-protected-by-microsoft-identity-platform"></a>Démarrage rapide : Appeler une API web ASP.NET qui est protégée par la plateforme d’identités Microsoft

Dans ce guide de démarrage rapide, vous téléchargez et exécutez un exemple de code qui montre comment protéger une API web ASP.NET en limitant l’accès à ses ressources aux comptes autorisés uniquement. L’exemple prend en charge l’autorisation des comptes Microsoft personnels et comptes de n’importe quelle organisation Azure Active Directory (Azure AD).

L’article utilise également une application Windows Presentation Foundation (WPF) pour montrer comment vous pouvez demander un jeton d’accès afin d’accéder à une API web.

## <a name="prerequisites"></a>Prérequis

* Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Visual Studio 2017 ou 2019. Téléchargez [Visual Studio gratuitement](https://www.visualstudio.com/downloads/).

## <a name="clone-or-download-the-sample"></a>Cloner ou télécharger l’exemple

Vous pouvez obtenir l’exemple de deux manières :

* Le cloner à partir de votre interpréteur de commandes ou de votre ligne de commande :

   ```console
   git clone https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git
   ```

* [Le télécharger sous la forme d’un fichier Zip](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/complete.zip).

[!INCLUDE [active-directory-develop-path-length-tip](../../../includes/active-directory-develop-path-length-tip.md)]

## <a name="register-the-web-api-todolistservice"></a>Inscrire l’API web (TodoListService)

Inscrivez votre API web dans **Inscriptions d’applications** au sein du portail Azure.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
1. Si vous avez accès à plusieurs locataires, utilisez le filtre **Répertoire + abonnement**:::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: dans le menu du haut pour sélectionner le locataire dans lequel vous voulez inscrire une application.
1. Recherchez et sélectionnez **Azure Active Directory**.
1. Sous **Gérer**, sélectionnez **Inscriptions d’applications** > **Nouvelle inscription**.
1. Entrez un **nom** pour votre application (par exemple, `AppModelv2-NativeClient-DotNet-TodoListService`). Les utilisateurs de votre application peuvent voir ce nom, et vous pouvez le changer ultérieurement.
1. Pour **Types de comptes pris en charge**, sélectionnez **Comptes dans un annuaire organisationnel**.
1. Sélectionnez **Inscrire** pour créer l’application.
1. Dans la page **Vue d’ensemble** de l’application, recherchez la valeur **ID d’application (client)** , puis notez-la pour une utilisation ultérieure. Vous en aurez besoin pour configurer le fichier de configuration Visual Studio pour ce projet (autrement dit, `ClientId` dans le fichier *TodoListService\Web.config*).
1. Sous **Gérer**, sélectionnez **Exposer une API** > **Ajouter une étendue**. Acceptez l’URI d’ID d’application proposé (`api://{clientId}`) en sélectionnant **Enregistrer et continuer**, puis entrez les informations suivantes :

    1. Pour **Nom de l’étendue**, entrez `access_as_user`.
    1. Pour **Qui peut donner son consentement**, vérifiez que l’option **Administrateurs et utilisateurs** est sélectionnée.
    1. Dans la zone **Nom d’affichage du consentement administrateur**, entrez `Access TodoListService as a user`.
    1. Dans **Description du consentement de l’administrateur**, entrez `Accesses the TodoListService web API as a user`.
    1. Dans la zone **Nom d’affichage du consentement utilisateur**, entrez `Access TodoListService as a user`.
    1. Dans la zone **Description du consentement de l’utilisateur**, entrez `Accesses the TodoListService web API as a user`.
    1. Pour **État**, conservez **Activé**.
1. Sélectionnez **Ajouter une étendue**.

### <a name="configure-the-service-project"></a>Configurer le projet de service

Configurez le projet de service en fonction de l’API web inscrite.

1. Ouvrez la solution dans Visual Studio, puis ouvrez le fichier *Web.config* sous la racine du projet TodoListService.

1. Remplacez la valeur du paramètre `ida:ClientId` par la valeur ID client (ID d’application) de l’application que vous avez inscrite dans le portail **Inscriptions d’applications**.

### <a name="add-the-new-scope-to-the-appconfig-file"></a>Ajouter la nouvelle étendue au fichier app.config

Pour ajouter la nouvelle étendue au fichier *app.config* de TodoListClient, effectuez les étapes suivantes :

1. Dans le dossier racine du projet TodoListClient, ouvrez le fichier *app.config*.

1. Collez l’ID d’application à partir de l’application que vous avez inscrite pour votre projet TodoListService dans le paramètre `TodoListServiceScope`, en remplaçant la chaîne `{Enter the Application ID of your TodoListService from the app registration portal}`.

  > [!NOTE]
  > Vérifiez que l’ID d’application utilise le format suivant : `api://{TodoListService-Application-ID}/access_as_user` (où `{TodoListService-Application-ID}` est le GUID représentant l’ID d’application de votre application TodoListService).

## <a name="register-the-web-app-todolistclient"></a>Inscrire l’application web (TodoListClient)

Inscrivez votre application TodoListClient dans **Inscriptions d’applications** au sein du portail Azure, puis configurez le code dans le projet TodoListClient. Si le client et le serveur sont considérés comme étant la même application, vous pouvez réutiliser l’application inscrite à l’étape 2. Utilisez la même application si vous voulez que les utilisateurs se connectent avec un compte Microsoft personnel.

### <a name="register-the-app"></a>Enregistrer l'application

Pour inscrire l’application TodoListClient, effectuez les étapes suivantes :

1. Accédez au portail [Inscriptions d’applications](https://go.microsoft.com/fwlink/?linkid=2083908) de la plateforme d’identités Microsoft pour les développeurs.
1. Sélectionnez **Nouvelle inscription**.
1. Quand la page **Inscrire une application** s’ouvre, entrez les informations d’inscription de votre application :

    1. Dans la section **Nom**, entez un nom d’application explicite qui s’affichera pour les utilisateurs de l’application (par exemple, **NativeClient-DotNet-TodoListClient**).
    1. Pour **Types de comptes pris en charge**, sélectionnez **Comptes dans un annuaire organisationnel**.
    1. Sélectionnez **Inscrire** pour créer l’application.

   > [!NOTE]
   > Dans le fichier *app.config* du projet TodoListClient, la valeur par défaut de `ida:Tenant` est définie sur `common`. Les valeurs possibles sont les suivantes :
   >
   > - `common` : Vous pouvez vous connecter à l’aide d’un compte professionnel ou scolaire ou d’un compte Microsoft personnel (car vous avez sélectionné **Comptes dans un annuaire organisationnel** lors d’une étape précédente).
   > - `organizations` : Vous pouvez vous connecter à l’aide d’un compte professionnel ou scolaire.
   > - `consumers` : Vous pouvez vous connecter à l’aide d’un compte Microsoft personnel uniquement.

1. Dans la page **Vue d’ensemble** de l’application, sélectionnez **Authentification**, puis effectuez les étapes suivantes pour ajouter une plateforme :

    1. Sous **Configurations de plateformes**, sélectionnez le bouton **Ajouter une plateforme**.
    1. Pour l’option **Applications de bureau et mobiles**, sélectionnez **Application de bureau et mobiles**.
    1. Pour les **URI de redirection**, activez la case à cocher `https://login.microsoftonline.com/common/oauth2/nativeclient` .
    1. Sélectionnez **Configurer**.

1. Sélectionnez **API autorisées**, puis effectuez les étapes suivantes pour ajouter des autorisations :

    1. Cliquez sur le bouton **Ajouter une autorisation**.
    1. Sélectionnez l’onglet **Mes API**.
    1. Dans la liste des API, sélectionnez **API AppModelv2-NativeClient-DotNet-TodoListService** ou le nom que vous avez entré pour l’API web.
    1. Si ce n’est pas déjà fait, cochez la case de l’autorisation **access_as_user**. Utilisez la zone de recherche, si nécessaire.
    1. Sélectionnez le bouton **Ajouter des autorisations**.

### <a name="configure-your-project"></a>Configurer votre projet

Configurez votre projet TodoListClient en ajoutant l’ID d’application au fichier *app.config*.

1. Dans le portail **Inscriptions d’applications**, dans la page **Vue d’ensemble**, copiez la valeur de l’**ID d’application (client)** .

1. Dans le dossier racine du projet TodoListClient, ouvrez le fichier *app.config*, puis collez la valeur ID de l’application dans le paramètre `ida:ClientId`.

## <a name="run-your-projects"></a>Exécuter vos projets

Démarrez les deux projets. Si vous utilisez Visual Studio :

1. Cliquez avec le bouton droit sur la solution Visual Studio, puis sélectionnez **Propriétés**

1. Dans **Propriétés communes**, sélectionnez **Projet de démarrage**, puis **Plusieurs projets de démarrage**. 

1. Pour les deux projets, choisissez **Démarrer** en tant qu’action

1. Assurez-vous que le service TodoListService démarre en premier en le plaçant en première position dans la liste à l’aide de la touche flèche haut.

Connectez-vous pour exécuter votre projet TodoListClient.

1. Appuyez sur F5 pour démarrer les projets. La page du service s’ouvre, ainsi que l’application de bureau.

1. Dans le projet TodoListClient, en haut à droite, sélectionnez **Se connecter**, puis connectez-vous avec les informations d’identification que vous avez utilisées pour inscrire votre application ou connectez-vous en tant qu’utilisateur dans le même annuaire.

   Si vous vous connectez pour la première fois, vous pouvez être invité à donner votre consentement à l’API web TodoListService.

   Pour vous permettre d’accéder à l’API web TodoListService et d’arranger la liste *À faire*, la connexion demande également le jeton d’accès à l’étendue *access_as_user*.

## <a name="pre-authorize-your-client-application"></a>Pré-autoriser votre application cliente

Vous pouvez permettre aux utilisateurs d’autres annuaires d’accéder à votre API web en autorisant préalablement l’application cliente à accéder à votre API web. Pour ce faire, ajoutez l’ID d’application de l’application cliente à la liste des applications pré-autorisées pour votre API web. En ajoutant un client pré-autorisé, vous autorisez les utilisateurs à accéder à votre API web sans avoir à fournir de consentement.

1. Dans le portail **Inscriptions d’applications**, ouvrez les propriétés de votre application TodoListService.
1. Dans la section **Exposer une API**, sous **Applications clientes autorisées**, sélectionnez **Ajouter une application cliente**.
1. Dans la zone **ID client**, collez l’ID de l’application TodoListClient.
1. Dans la section **Étendues autorisées**, sélectionnez l’étendue de l’API web `api://<Application ID>/access_as_user`.
1. Sélectionnez **Ajouter une application**.

### <a name="run-your-project"></a>Exécuter votre projet

1. Appuyez sur <kbd>F5</kbd> pour exécuter votre projet. Votre application TodoListClient s’ouvre.
1. En haut à droite, sélectionnez **Se connecter**, puis connectez-vous en utilisant un compte Microsoft personnel, comme un compte *live.com* ou *hotmail.com*, ou encore un compte professionnel ou scolaire.

## <a name="optional-limit-sign-in-access-to-certain-users"></a>Facultatif : Limiter l’accès de connexion à certains utilisateurs

Par défaut, tous les comptes personnels, comme les comptes *outlook.com* ou *live.com*, ou encore les comptes professionnels ou scolaires d’organisations qui sont intégrés à Azure AD peuvent demander des jetons et accéder à votre API web.

Pour spécifier qui peut se connecter à votre application, utilisez l’une des options suivantes :

### <a name="option-1-limit-access-to-a-single-organization-single-tenant"></a>Option 1 : Limiter l’accès à une seule organisation (monolocataire)

Vous pouvez limiter l’accès à votre application aux comptes d’utilisateur qui se trouvent dans un seul locataire Azure AD, notamment les comptes d’invités de ce locataire. Ce scénario est courant pour les applications métier.

1. Ouvrez le fichier *App_Start\Startup.Auth*, puis remplacez la valeur du point de terminaison de métadonnées qui est passée dans `OpenIdConnectSecurityTokenProvider` par `https://login.microsoftonline.com/{Tenant ID}/v2.0/.well-known/openid-configuration`. Vous pouvez également utiliser le nom du locataire, par exemple `contoso.onmicrosoft.com`.
1. Dans le même fichier, affectez à la propriété `ValidIssuer` définie sur `TokenValidationParameters` la valeur `https://sts.windows.net/{Tenant ID}/`, et définissez l’argument `ValidateIssuer` sur `true`.

### <a name="option-2-use-a-custom-method-to-validate-issuers"></a>Option n°2 : Utiliser une méthode personnalisée pour valider les émetteurs

Vous pouvez implémenter une méthode personnalisée pour valider les émetteurs à l’aide du paramètre `IssuerValidator`. Pour plus d’informations sur ce paramètre, consultez la rubrique relative à la [classe TokenValidationParameters](/dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters).

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Étapes suivantes

Découvrez-en plus sur le scénario d’API web protégée pris en charge par la plateforme d’identité Microsoft.
> [!div class="nextstepaction"]
> [Scénario d’API web protégée](scenario-protected-web-api-overview.md)
