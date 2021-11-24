---
title: Inscrire des applications de bureau appelant des API web | Azure
titleSuffix: Microsoft identity platform
description: Apprenez à générer une application de bureau qui appelle des API web (inscription d’application).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/09/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: ca051199e6fcbfab9a8de9e4a03d845bfa03c454
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132553740"
---
# <a name="desktop-app-that-calls-web-apis-app-registration"></a>Application de bureau qui appelle des API web : Inscription d'application

Cet article porte sur les spécificités de l’inscription d’application pour une application de bureau.

## <a name="supported-account-types"></a>Types de comptes pris en charge

Les types de compte pris en charge dans une application de bureau dépendent de l’expérience que vous souhaitez mettre en avant. En raison de cette relation, les types de compte pris en charge dépendent des flux que vous voulez utiliser.

### <a name="audience-for-interactive-token-acquisition"></a>Audience pour l’acquisition de jetons interactive

Si votre application de bureau utilise l’authentification interactive, vous pouvez connecter des utilisateurs depuis n’importe quel [type de compte](quickstart-register-app.md).

### <a name="audience-for-desktop-app-silent-flows"></a>Audience pour les flux silencieux d’application de bureau

- Pour utiliser l’authentification Windows intégrée ou un nom d’utilisateur et un mot de passe, votre application doit connecter des utilisateurs dans votre locataire, par exemple, si vous êtes développeur d’application métier. Ou bien, dans des organisations Azure Active Directory, votre application doit se connecter aux utilisateurs de votre propre locataire s’il s’agit d’un scénario de fournisseur de logiciel indépendant. Ces flux d’authentification ne sont pas pris en charge pour des comptes Microsoft personnels.
- Si vous connectez des utilisateurs avec des identités de réseaux sociaux qui passent par une stratégie et une autorité entreprise-client (B2C), vous pouvez uniquement utiliser l’authentification interactive par mot de passe et nom d’utilisateur.

## <a name="redirect-uris"></a>URI de redirection

Les URI de redirection à utiliser dans une application de bureau dépendent du flux que vous voulez utiliser.

Spécifiez l’URI de redirection de votre application en [configurant les paramètres de plateforme](quickstart-register-app.md#add-a-redirect-uri) de l’application dans **Inscriptions d’applications** dans le portail Azure.

- Pour les applications qui utilisent [Web Authentication Manager (WAM)](scenario-desktop-acquire-token-wam.md), les URI de redirection ne doivent pas être configurés dans MSAL, mais ils doivent être configurés dans l'[enregistrement de l'application](scenario-desktop-acquire-token-wam.md#redirect-uri).

- Pour les applications qui utilisent l’authentification interactive :

  - Applications qui utilisent des navigateurs incorporés : `https://login.microsoftonline.com/common/oauth2/nativeclient` (Remarque : si votre application affiche une fenêtre contextuelle qui est généralement dépourvue de barre d’adresse, elle utilise le « navigateur incorporé ».)
  - Applications qui utilisent des navigateurs système : `http://localhost` (Remarque : si votre application ouvre le navigateur par défaut de votre système (par exemple, Edge, Chrome, Firefox, etc.) pour l’accès au portail de connexion Microsoft, elle utilise le « navigateur système ».)
  
  > [!IMPORTANT]
  > Comme meilleure pratique de sécurité, nous vous recommandons de définir explicitement `https://login.microsoftonline.com/common/oauth2/nativeclient` ou `http://localhost` comme URI de redirection. Certaines bibliothèques d’authentification comme MSAL.NET utilisent la valeur par défaut `urn:ietf:wg:oauth:2.0:oob` si aucun autre URI de redirection n’est spécifié, ce qui n’est pas recommandé. Ce paramètre par défaut sera mis à jour en tant que changement cassant dans la prochaine version majeure.

- Si vous générez une application Objective-C ou Swift native pour macOS, enregistrez l’URI de redirection en fonction de l’identificateur de bundle de votre application, au format suivant : `msauth.<your.app.bundle.id>://auth`. Remplacez `<your.app.bundle.id>` par l’identificateur de bundle de votre application.
- Si vous générez une application Node.js Electron, utilisez un protocole de fichier personnalisé au lieu d’un URI de redirection web ordinaire (https://) afin de gérer l’étape de redirection du flux d’autorisation, par exemple `msal://redirect`. Le nom du protocole de fichier personnalisé ne doit pas être simple à deviner et doit suivre les suggestions de la [spécification OAuth 2.0 pour les applications natives](https://tools.ietf.org/html/rfc8252#section-7.1).
- Si votre application n’utilise que l’authentification Windows intégrée ou un mot de passe et un nom d’utilisateur, vous n’avez pas besoin d’inscrire d’URI de redirection pour votre application. Ces flux effectuent un aller-retour vers le point de terminaison de la plateforme d’identités Microsoft v2.0. Votre application ne sera pas rappelée sur un URI spécifique.
- Pour différencier le [flux de code d’appareil](scenario-desktop-acquire-token-device-code-flow.md), l’[authentification Windows intégrée](scenario-desktop-acquire-token-integrated-windows-authentication.md) et un [mot de passe associé à un nom d’utilisateur](scenario-desktop-acquire-token-username-password.md) d’une application cliente confidentielle à l’aide d’un flux d’informations d’identification client utilisé dans des [applications démon](scenario-daemon-overview.md), dont aucune ne requiert d’URI de redirection, configurez-la en tant qu’application cliente publique. Pour obtenir cette configuration :

    1. Dans le <a href="https://portal.azure.com/" target="_blank">portail Azure</a>, sélectionnez votre application dans **Inscriptions d’applications**, puis sélectionnez **Authentification**.
    1. Dans **Paramètres avancés** > **Autoriser les flux de clients publics** > **Activer les flux mobiles et de bureau suivants**, sélectionnez **Oui**.

        :::image type="content" source="media/scenarios/default-client-type.png" alt-text="Activer le paramètre client public dans le volet Authentification du portail Azure":::

## <a name="api-permissions"></a>Autorisations des API

Les applications de bureau appellent des API pour le compte de l’utilisateur connecté. Elles doivent demander des autorisations déléguées. Elles ne peuvent pas demander d’autorisations d’application, qui ne sont gérées que dans des [applications démon](scenario-daemon-overview.md).

## <a name="next-steps"></a>Étapes suivantes

Passez à l’article suivant de ce scénario, [Configuration du code de l’application](scenario-desktop-app-configuration.md).
