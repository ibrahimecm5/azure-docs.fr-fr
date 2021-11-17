---
title: 'Démarrage rapide : Activer l’authentification unique pour une application d’entreprise'
titleSuffix: Azure AD
description: Activez l’authentification unique pour une application d’entreprise dans Azure Active Directory.
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 09/21/2021
ms.author: davidmu
ms.reviewer: ergleenl
ms.openlocfilehash: 337561853bb34c8ecc67483dc8b94448b0b853a1
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132548517"
---
# <a name="quickstart-enable-single-sign-on-for-an-enterprise-application"></a>Démarrage rapide : Activer l’authentification unique pour une application d’entreprise

Dans ce guide de démarrage rapide, vous utilisez le Centre d’administration Azure Active Directory pour activer l’authentification unique (SSO, Single Sign-On) d’une application d’entreprise que vous avez ajoutée à votre locataire Azure Active Directory (Azure AD). Une fois que l’authentification unique est configurée, vos utilisateurs peuvent se connecter avec leurs informations d’identification Azure AD. 

Azure AD a une galerie qui contient des milliers d’applications préintégrées qui utilisent l’authentification unique. Ce guide de démarrage rapide utilise une application d’entreprise appelée **Azure AD SAML Toolkit** comme exemple, mais les concepts s’appliquent à la plupart des [applications d’entreprise de la galerie](../saas-apps/tutorial-list.md) préconfigurées.

Il est recommandé d’utiliser un environnement de non-production pour tester les étapes de ce guide de démarrage rapide.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’authentification unique, vous avez besoin des éléments suivants :

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Un des rôles suivants : Administrateur général, Administrateur d’application cloud, Administrateur d’application ou propriétaire du principal de service.
- Réalisation des étapes décrites dans [Démarrage rapide : Créer et affecter un compte d’utilisateur](add-application-portal-assign-users.md).

## <a name="enable-single-sign-on"></a>Activer l’authentification unique

Pour activer l’authentification unique pour une application :

1. Accédez au [Centre d’administration Azure Active Directory](https://aad.portal.azure.com) et connectez-vous avec l’un des rôles listés dans les prérequis.
1. Dans le menu de gauche, sélectionnez **Applications d’entreprise**. Le volet **Toutes les applications** s’ouvre et affiche une liste des applications de votre locataire Azure AD. Recherchez et sélectionnez l’application que vous souhaitez utiliser. Par exemple, **Azure AD SAML Toolkit 1**.
1. Dans la section **Gérer** du menu de gauche, sélectionnez **Authentification unique** pour ouvrir le volet **Authentification unique** à des fins de modification.
1. Sélectionnez **SAML** pour ouvrir la page de configuration de SSO. Une fois que l’application est configurée, les utilisateurs peuvent s’y connecter avec leurs informations d’identification du locataire Azure AD.
1. Le processus de configuration d’une application afin d’utiliser Azure AD pour l’authentification unique basée sur SAML varie d’une application à l’autre. Pour les applications d’entreprise de la galerie, utilisez le lien pour rechercher des informations sur les étapes nécessaires à la configuration de l’application. Les étapes pour **Azure AD SAML Toolkit** sont listées dans ce guide de démarrage rapide.

    :::image type="content" source="media/add-application-portal-setup-sso/saml-configuration.png" alt-text="Configurez l’authentification unique pour une application d’entreprise.":::

1. Dans la section **Configurer Azure AD SAML Toolkit 1**, enregistrez les valeurs des propriétés **URL de connexion**, **Identificateur Azure AD** et **URL de déconnexion** à utiliser ultérieurement.

## <a name="configure-single-sign-on-in-the-tenant"></a>Configurer l’authentification unique dans le locataire

Ajoutez des valeurs d’URL de connexion et de réponse, et téléchargez un certificat pour commencer la configuration de l’authentification unique dans Azure AD.

Pour configurer l’authentification unique dans Azure AD :

1. Dans le portail Azure, sélectionnez **Modifier** dans la section **Configuration SAML de base** du volet **Configurer l’authentification unique**. 
1. Pour **URL de réponse (URL Assertion Consumer Service)** , entrez `https://samltoolkit.azurewebsites.net/SAML/Consume`.
1. Pour **URL de connexion**, entrez `https://samltoolkit.azurewebsites.net/`.
1. Sélectionnez **Enregistrer**.
1. Dans la section **Certificat de signature SAML**, sélectionnez **Télécharger** pour **Certificat (brut)** afin de télécharger le certificat de signature SAML et de l’enregistrer pour l’utiliser ultérieurement.

## <a name="configure-single-sign-on-in-the-application"></a>Configurer l’authentification unique dans l’application

L’utilisation de l’authentification unique dans l’application vous oblige à inscrire le compte d’utilisateur auprès de l’application et à ajouter les valeurs de la configuration SAML que vous avez enregistrées précédemment.

### <a name="register-the-user-account"></a>Inscrire le compte d’utilisateur

Pour inscrire un compte d’utilisateur auprès de l’application :

1. Ouvrez une nouvelle fenêtre de navigateur et accédez à l’URL de connexion de l’application. Pour l’application **Azure AD SAML Toolkit**, l’adresse est `https://samltoolkit.azurewebsites.net`.
1. Sélectionnez **Inscrire** en haut à droite de la page.

    :::image type="content" source="media/add-application-portal-setup-sso/toolkit-register.png" alt-text="Inscrivez un compte d’utilisateur dans l’application Azure AD SAML Toolkit.":::

1. Pour **E-mail**, entrez l’adresse e-mail de l’utilisateur qui accédera à l’application. Par exemple, dans un guide de démarrage rapide précédent, le compte d’utilisateur créé utilise l’adresse `contosouser1@contoso.com`. Veillez à remplacer `contoso.com` par le domaine de votre locataire.
1. Entrez un **mot de passe** et confirmez-le.
1. Sélectionnez **Inscription**.

### <a name="configure-saml-settings"></a>Configurer les paramètres SAML

Pour configurer les paramètres SAML pour l’application :

1. Connectez-vous avec les informations d’identification du compte d’utilisateur que vous avez créé et sélectionnez **Configuration SAML** en haut à gauche de la page.
1. Sélectionnez **Créer** au milieu de la page.
1. Pour **URL de connexion**, **Identificateur Azure AD** et **URL de déconnexion**, entrez les valeurs que vous avez enregistrées.
1. Sélectionnez **Choisir un fichier** pour charger le certificat que vous avez téléchargé précédemment.
1. Sélectionnez **Create** (Créer).
1. Copiez les valeurs de l’**URL de connexion lancée par le fournisseur de services** et l’**URL Assertion Consumer Service (ACS)** à utiliser plus tard.

## <a name="update-single-sign-on-values"></a>Mettre à jour les valeurs de l’authentification unique

Utilisez les valeurs que vous avez enregistrées pour l’**URL de connexion lancée par le fournisseur de services** et l’**URL Assertion Consumer Service (ACS)** afin de mettre à jour les valeurs de l’authentification unique dans votre locataire.

Pour mettre à jour les valeurs de l’authentification unique :

1. Dans le portail Azure, sélectionnez **Modifier** dans la section **Configuration SAML de base** du volet **Configurer l’authentification unique**. 
1. Pour l’**URL de réponse (Assertion Consumer Service)** , entrez la valeur de l’**URL Assertion Consumer Service (ACS)** que vous avez enregistrée précédemment.
1. Pour l’**URL de connexion**, entrez la valeur de l’**URL de connexion lancée par le fournisseur de services** que vous avez enregistrée précédemment.
1. Sélectionnez **Enregistrer**.

## <a name="test-single-sign-on"></a>Tester l’authentification unique

Vous pouvez tester la configuration de l’authentification unique à partir du volet **Configurer l’authentification unique**.

Pour tester l’authentification unique :

1. Dans la section **Tester l’authentification unique avec Azure AD SAML Toolkit 1** du volet **Configurer l’authentification unique**, sélectionnez **Tester**.
1. Connectez-vous à l’application avec les informations d’identification Azure AD du compte d’utilisateur que vous avez affecté à l’application.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous envisagez de suivre le prochain guide de démarrage rapide, gardez l’application d’entreprise que vous avez créée. Dans le cas contraire, vous pouvez la supprimer pour nettoyer votre locataire. Pour plus d’informations, consultez [Supprimer une application](delete-application-portal.md).

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment configurer les propriétés d’une application d’entreprise.
> [!div class="nextstepaction"]
> [Configurer une application](add-application-portal-configure.md)
