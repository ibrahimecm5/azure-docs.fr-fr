---
title: Scénario d’application monopage JavaScript
titleSuffix: Microsoft identity platform
description: Découvrez comment créer une application monopage (vue d’ensemble du scénario) à l’aide de la plateforme d’identités Microsoft.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/12/2021
ms.author: marsma
ms.custom: aaddev, identityplatformtop40, devx-track-js
ms.openlocfilehash: 19692739814a4db3ef2f981461254de415c9bdcc
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131444171"
---
# <a name="scenario-single-page-application"></a>Scénario : Application monopage

Découvrez tout ce que vous devez savoir pour créer une application monopage. Pour obtenir des instructions concernant Azure Static Web Apps, consultez [Authentification et autorisation pour Static Web Apps](../../static-web-apps/authentication-authorization.md) à la place.

## <a name="getting-started"></a>Prise en main

Si vous ne l’avez pas déjà fait, créez votre première application en suivant le guide de démarrage rapide SPA JavaScript :

[Démarrage rapide : application monopage](./quickstart-v2-javascript-auth-code.md)

## <a name="overview"></a>Vue d’ensemble

De nombreuses applications web modernes sont créées en tant qu’applications monopages côté client. Les développeurs les écrivent à l’aide de JavaScript ou d’un framework d’application monopage, comme Angular, Vue et React. Ces applications s’exécutent sur un navigateur web et présentent des caractéristiques d’authentification différentes de celles des applications web classiques côté serveur.

La plateforme d’identités Microsoft offre **deux** options pour permettre aux applications monopages d’effectuer la connexion des utilisateurs et d’obtenir des jetons pour accéder aux services back-end ou aux API web :

- [Flux de code d’autorisation OAuth 2.0 (avec PKCE)](./v2-oauth2-auth-code-flow.md). Le flux de code d’autorisation permet à l’application d’échanger un code d’autorisation pour obtenir des jetons d’**ID** afin de représenter l’utilisateur authentifié et des jetons d’**accès** nécessaires pour appeler des API protégées. 

    La clé de vérification pour l’échange de code, ou _PKCE_, est une extension du flux de code d’autorisation pour empêcher les attaques par injection de code d’autorisation. Cette norme IETF atténue la menace d’interception d’un code d’autorisation, et active l’échange OAuth sécurisé à partir de clients publics, comme expliqué dans le document [RFC 7636](https://datatracker.ietf.org/doc/html/rfc7636). De plus, elle retourne des jetons d’**actualisation** qui fournissent à votre application un accès à long terme à des ressources au nom d’utilisateurs sans nécessiter d’interaction de ces utilisateurs. 

    L’utilisation du flux de code d’autorisation avec PKCE est l’approche d’autorisation plus sécurisée et **recommandée**, non seulement dans des applications JavaScript natives et basées sur un navigateur, mais pour tout type de client OAuth.

![Authentification des applications monopages](./media/scenarios/spa-app-auth.svg)

- [Flux implicite OAuth 2.0](./v2-oauth2-implicit-grant-flow.md). Le flux d’octroi implicite permet à l’application d’obtenir des jetons d’**ID** et d’**accès**. Contrairement au flux de code d’autorisation, le flux d’octroi implicite ne retourne pas de **jeton d’actualisation**.

![Flux implicite des applications monopages](./media/scenarios/spa-app.svg)

Ce flux d’authentification n’inclut pas de scénarios d’application utilisant des frameworks JavaScript multiplateformes, tels qu’Electron et React-Native. Ils exigent davantage de fonctionnalités pour l’interaction avec les plateformes natives.

## <a name="specifics"></a>Spécificités

Afin d’activer ce scénario pour votre application, vous avez besoin des éléments suivants :

* Inscription de l’application auprès d’Azure Active Directory (Azure AD). Les étapes d’inscription diffèrent entre le flux d’octroi implicite et le flux de code d’autorisation.
* Configuration de l’application avec les propriétés de l’application inscrite, comme l’ID d’application.
* Utilisation de la bibliothèque d’authentification Microsoft pour JavaScript (MSAL.js) pour permettre au flux d’authentification de se connecter et d’acquérir des jetons.

## <a name="recommended-reading"></a>Lectures recommandées

[!INCLUDE [recommended-topics](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="next-steps"></a>Étapes suivantes

Passez à l’article suivant de ce scénario, [Inscription d’application](scenario-spa-app-registration.md).
