---
title: 'Tutoriel : Inscrire une application'
titleSuffix: Azure AD B2C
description: Ce tutoriel explique comment inscrire une application web dans Azure Active Directory B2C à l’aide du portail Azure.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 09/20/2021
ms.custom: project-no-code
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: afe63c06f52ba8c7b81ca46b49461d147c3f6f5f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131036150"
---
# <a name="tutorial-register-a-web-application-in-azure-active-directory-b2c"></a>Tutoriel : Inscrire une application web dans Azure Active Directory B2C

Pour que vos [applications](application-types.md) puissent interagir avec Azure Active Directory B2C (Azure AD B2C), elles doivent être inscrites dans un locataire que vous gérez. Ce tutoriel vous montre comment inscrire une application web à l’aide du portail Azure. 

Une « application web » fait référence à une application web classique exécutant la majeure partie de la logique d’application sur le serveur. Ces applications peuvent être créées au moyen de frameworks, tels que ASP.NET Core, Maven (Java), Flask (Python) et Express (Node.js).

> [!IMPORTANT]
> Si vous utilisez une application monopage (« SPA ») à la place (par exemple, en utilisant Angular, Vue ou React), consultez le [Guide pratique pour inscrire une application monopage](tutorial-register-spa.md).
> 
> Si vous utilisez une application native (par exemple, iOS, Android, mobile et de bureau), découvrez [comment inscrire une application cliente native](add-native-application.md).

## <a name="prerequisites"></a>Prérequis
Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

Si vous n’avez pas encore créé votre propre [locataire Azure AD B2C](tutorial-create-tenant.md), créez-en un maintenant. Vous pouvez utiliser un locataire Azure AD B2C existant.

## <a name="register-a-web-application"></a>Inscrire une application web

Pour inscrire une application web dans votre locataire Azure AD B2C, vous pouvez utiliser notre nouvelle expérience unifiée **Inscriptions d’applications** ou notre expérience héritée **Applications (héritées)** . [En savoir plus sur la nouvelle expérience](./app-registrations-training-guide.md).

#### <a name="app-registrations"></a>[Inscriptions des applications](#tab/app-reg-ga/)

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Veillez à bien utiliser l’annuaire qui contient votre locataire Azure AD B2C. Sélectionnez l’icône **Répertoires + abonnements** dans la barre d’outils du portail.
1. Sur la page **Paramètres du portail | Répertoires + abonnements**, recherchez votre répertoire AD B2C Azure dans la liste **Nom de répertoire**, puis sélectionnez **Basculer**.
1. Dans le portail Azure, recherchez et sélectionnez **Azure AD B2C**.
1. Sélectionnez **Inscriptions d’applications**, puis **Nouvelle inscription**.
1. Entrez un **Nom** pour l’application. Par exemple, *webapp1*.
1. Sous **Types de comptes pris en charge**, sélectionnez **Comptes dans un fournisseur d’identité ou annuaire organisationnel (pour authentifier les utilisateurs avec des flux d’utilisateurs)** .
1. Sous **URI de redirection**, sélectionnez **Web**, puis entrez `https://jwt.ms` dans la zone de texte de l’URL.

    L’URI de redirection est le point de terminaison auquel l’utilisateur est envoyé par le serveur d’autorisation (dans ce cas, Azure AD B2C) après avoir terminé son interaction avec l’utilisateur, et auquel un jeton d’accès ou un code d’autorisation est envoyé en cas d’autorisation réussie. Dans une application de production, il s’agit généralement d’un point de terminaison accessible publiquement dans lequel votre application s’exécute, comme `https://contoso.com/auth-response`. À des fins de test comme dans ce didacticiel, vous pouvez le définir sur `https://jwt.ms`, une application web Microsoft qui affiche le contenu décodé d’un jeton (le contenu du jeton ne quitte jamais votre navigateur). Pendant le développement d’une application, vous pouvez ajouter le point de terminaison sur lequel votre application écoute localement, comme `https://localhost:5000`. Vous pouvez ajouter des URI de redirection à vos applications inscrites à tout moment et les modifier.

    Les restrictions suivantes s’appliquent aux URI de redirection :

    * L’URL de réponse doit commencer par le schéma `https`.
    * L’URL de réponse respecte la casse. Sa casse doit correspondre à celle du chemin d’URL de votre application en cours d’exécution. Par exemple, si votre application comprend `.../abc/response-oidc` dans son chemin, ne spécifiez pas `.../ABC/response-oidc` dans l’URL de réponse. Étant donné que le navigateur web considère que les chemins respectent la casse, les cookies associés à `.../abc/response-oidc` peuvent être exclus s’ils sont redirigés vers l’URL `.../ABC/response-oidc` qui ne correspond pas à la casse.

1. Sous **Autorisations**, activez la case à cocher *Accorder le consentement administrateur aux autorisations openid et offline_access*.
1. Sélectionnez **Inscription**.

#### <a name="applications-legacy"></a>[Applications (héritées)](#tab/applications-legacy/)

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Veillez à bien utiliser l’annuaire qui contient votre locataire Azure AD B2C. Sélectionnez l’icône **Répertoires + abonnements** dans la barre d’outils du portail.
1. Sur la page **Paramètres du portail | Répertoires + abonnements**, recherchez votre répertoire AD B2C Azure dans la liste **Nom de répertoire**, puis sélectionnez **Basculer**.
1. Dans le portail Azure, recherchez et sélectionnez **Azure AD B2C**.
1. Sélectionnez **Applications (héritées)** , puis **Ajouter**.
1. Entrez un nom pour l’application. Par exemple, *webapp1*.
1. Pour **Inclure une application/API web**, sélectionnez **Oui**.
1. Pour l’**URL de réponse**, entrez un point de terminaison où Azure AD B2C doit retourner les jetons demandés par votre application. Par exemple, vous pouvez le définir pour qu’il écoute localement sur `http://localhost:5000`. Vous pouvez ajouter des URI de redirection à vos applications inscrites à tout moment et les modifier.

    Les restrictions suivantes s’appliquent aux URI de redirection :

    * L’URL de réponse doit commencer par le schéma `https`, sauf en cas d’utilisation de `localhost`.
    * L’URL de réponse respecte la casse. Sa casse doit correspondre à celle du chemin d’URL de votre application en cours d’exécution. Par exemple, si votre application comprend `.../abc/response-oidc` dans son chemin, ne spécifiez pas `.../ABC/response-oidc` dans l’URL de réponse. Étant donné que le navigateur web considère que les chemins respectent la casse, les cookies associés à `.../abc/response-oidc` peuvent être exclus s’ils sont redirigés vers l’URL `.../ABC/response-oidc` qui ne correspond pas à la casse.

1. Sélectionnez **Créer** pour terminer l’inscription de l’application.

* * *

> [!TIP]
> Si vous ne voyez pas la ou les applications que vous avez créées sous **Inscriptions d’applications**, actualisez le portail.

## <a name="create-a-client-secret"></a>Créer une clé secrète client

Pour une application web, vous devez créer un secret d’application. Le secret client est également appelé *mot de passe d’application*. Ce secret sera utilisé par votre application afin d’échanger un code d’autorisation pour un jeton d’accès.

#### <a name="app-registrations"></a>[Inscriptions des applications](#tab/app-reg-ga/)

1. Dans la page **Azure AD B2C – Inscriptions d’applications**, sélectionnez l’application que vous avez créée, par exemple *webapp1*.
1. Dans le menu de gauche, sous **Gérer**, sélectionnez **Certificats et secrets**.
1. Sélectionnez **Nouveau secret client**.
1. Entrez une description pour la clé secrète client dans la zone **Description**. Par exemple, *clientsecret1*.
1. Sous **Expire**, sélectionnez une durée pendant laquelle le secret est valide, puis sélectionnez **Ajouter**.
1. Enregistrez la **valeur** du secret en prévision d’une utilisation dans le code de votre application cliente. Cette valeur secrète ne sera plus jamais affichée lorsque vous aurez quitté cette page. Vous utiliserez cette valeur comme secret d’application dans le code de votre application.

#### <a name="applications-legacy"></a>[Applications (héritées)](#tab/applications-legacy/)

1. Dans la page **Azure AD B2C – Applications**, sélectionnez l’application que vous avez créée, par exemple *webapp1*.
1. Sélectionnez **Clés**, puis **Générer la clé**.
1. Sélectionnez **Enregistrer** pour afficher la clé. Prenez note de la valeur **Clé d’application** . Vous utiliserez cette valeur comme secret d’application dans le code de votre application.

* * *

> [!NOTE]
> Pour des raisons de sécurité, vous pouvez substituer le secret d’application régulièrement, ou immédiatement en cas d’urgence. Toute application intégré à Azure AD B2C doit être préparée à gérer un événement de substitution de secret, quelle que soit sa fréquence. Vous pouvez définir deux secrets d’application, ce qui permet à votre application de continuer à utiliser l’ancien secret pendant un événement de permutation de secret d’application. Pour ajouter une autre clé secrète client, répétez les étapes de cette section. 

## <a name="enable-id-token-implicit-grant"></a>Activer l’octroi implicite du jeton d’ID

La définition de l’octroi implicite stipule que les jetons, tels que les jetons d’ID et les jetons d’accès, sont retournés directement à partir d’Azure AD B2C à l’application. Pour les applications web, telles que les applications web ASP.NET Core et [https://jwt.ms](https://jwt.ms), qui demandent un jeton d’ID directement à partir du point de terminaison d’autorisation, activez le flux d’octroi implicite dans l’inscription de l’application.

1. Dans le menu de gauche, sous **Gérer**, sélectionnez **Authentification**.
1. Sous Octroi implicite, cochez les deux cases **Jetons d’accès** et **Jetons d’ID**.
1. Sélectionnez **Enregistrer**.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Inscrire une application web
> * Créer une clé secrète client

Ensuite, découvrez comment créer des flux utilisateur pour permettre à vos utilisateurs de s’inscrire, se connecter et gérer leur profil.

> [!div class="nextstepaction"]
> [Créer des flux utilisateur dans Azure Active Directory B2C >](tutorial-create-user-flows.md)
