---
title: Configuration en un clic de l’authentification unique (SSO) pour votre application sur la Place de marché Azure
description: Étapes de la configuration en un clic de l’authentification unique pour votre application à partir de la Place de marché Azure.
titleSuffix: Azure AD
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: conceptual
ms.date: 06/11/2019
ms.author: davidmu
ms.collection: M365-identity-device-management
ms.reviewer: ergreenl
ms.openlocfilehash: d0e0a9fcb3052132df33d6988468cb218c3d9fc2
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132556607"
---
# <a name="one-click-app-configuration-of-single-sign-on"></a>Configuration en un clic de l’authentification unique pour une application

 Ce didacticiel explique comment effectuer en un clic à partir de la Place de marché Azure une configuration de l’authentification unique (SSO) pour des applications Azure Active Directory (Azure AD) prenant en charge SAML.

## <a name="introduction-to-one-click-sso"></a>Introduction à l’authentification SSO en un clic

La fonctionnalité d’authentification SSO en un clic a été conçue pour configurer l’authentification unique pour des applications de la Place de marché Azure qui prennent en charge le protocole SAML. Dans la page Configuration de l’authentification unique Azure AD, cette option vous permet de configurer automatiquement les métadonnées Azure AD côté application. Vous pouvez ainsi rapidement configurer l’authentification unique avec un minimum d’effort manuel.

## <a name="advantages-of-one-click-sso"></a>Avantages de l’authentification SSO en un clic

- Configuration rapide de l’authentification unique d’applications de la Place de marché Azure qui nécessitent une configuration manuelle côté application.
- Configuration de l’authentification unique plus efficace et précise.
- Aucune communication et aucun support de partenaire ne sont nécessaires pour la configuration. L’application fournit l’interface utilisateur pour la configuration SAML.

## <a name="prerequisites"></a>Prérequis

- Abonnement actif de l’application à configurer avec l’authentification unique. Vous avez également besoin d’informations d’identification de l’administrateur.
- L’**Extension de connexion sécurisée à Mes applications** de Microsoft installée dans le navigateur. Pour plus d’informations, consultez [Accéder aux applications du portail Mes applications et les utiliser](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="one-click-sso-configuration-steps"></a>Étapes de configuration SSO en un clic

1. Ajoutez l’application à partir de la Place de marché Azure.

2. Sélectionnez **Authentification unique**.

3. Sélectionnez **Activer l’authentification unique**.

4. Indiquez les valeurs de obligatoires pour la configuration dans la section **Configuration SAML de base**.

    > [!NOTE]
    > Si l’application a des revendications personnalisées que vous devez configurer, gérez-les avant d’effectuer une authentification SSO en un clic.

5. Si la fonctionnalité d’authentification SSO en un clic est disponible pour votre application de la Place de marché Azure, l’écran suivant s’affiche. Il se peut que vous deviez installer l’**Extension de navigateur de connexion sécurisée à Mes applications** en sélectionnant **Install the extension** (Installer l’extension).

   ![Installer l’extension du navigateur My Apps Secure Sign-in](./media/one-click-sso-tutorial/install-myappssecure-extension.png)

6. Après avoir ajouté l’extension au navigateur, sélectionnez **Configurer \<Application Name\>** . Une fois que vous êtes redirigé vers le portail d’administration de l’application, connectez-vous en tant qu’administrateur.

   ![Configurer le nom de l’application](./media/one-click-sso-tutorial/setup-sso.png)

7. L’extension de navigateur configure automatiquement l’authentification unique sur l’application. Confirmez en sélectionnant **Oui**.

   ![Enregistrement des données renseignées automatiquement](./media/one-click-sso-tutorial/save-autopopulate.png)

   > [!NOTE]
   > Si la configuration de l’authentification unique pour votre application nécessite des étapes supplémentaires, suivez les invites pour effectuer les étapes.

8. Une fois la configuration terminée, sélectionnez **OK** pour enregistrer les modifications.

   ![Enregistrer les données renseignées automatiquement](./media/one-click-sso-tutorial/save-data.png)

9. Une fenêtre de confirmation s’affiche pour vous informer que les paramètres d’authentification unique sont correctement configurés.

   ![Authentification unique configurée](./media/one-click-sso-tutorial/sso-configured.png)

10. Une fois la configuration réussie, vous êtes déconnecté de l’application et redirigé vers le portail Azure.

11. Vous pouvez sélectionner **Test** pour tester l’authentification unique.

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](../saas-apps/tutorial-list.md)
- [Présentation de l’Extension de navigateur de connexion sécurisée à Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)