---
title: 'Tutoriel : Intégrer l’authentification unique Azure Active Directory à Splan Visitor | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Splan Visitor.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/21/2021
ms.author: jeedes
ms.openlocfilehash: 5c628e67b258d3d51b5f7b05b08c0ac0b2accb9a
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132304089"
---
# <a name="tutorial-integrate-azure-active-directory-single-sign-on-sso-with-splan-visitor"></a>Tutoriel : Intégrer l’authentification unique Azure Active Directory à Splan Visitor

Ce tutoriel explique comment intégrer Splan Visitor avec Azure Active Directory (Azure AD). Quand vous intégrez Splan Visitor à Azure AD, vous pouvez :

* Utiliser Azure AD pour contrôler qui a accès à Splan Visitor.
* Permettre aux utilisateurs de se connecter automatiquement à Splan Visitor avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central, le portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Splan Visitor pour lequel l’authentification unique est activée.

> [!NOTE]
> Cette intégration peut également être utilisée à partir de l’environnement cloud US Government Azure AD. Cette application est disponible dans la Galerie d’applications cloud US Government Azure AD et peut être configurée de la même façon que dans le cloud public.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification SSO Azure AD dans un environnement de test.

* Splan Visitor prend en charge l’authentification unique lancée par le fournisseur d’identité.

## <a name="add-splan-visitor-from-the-gallery"></a>Ajouter Splan Visitor à partir de la galerie

Pour configurer l’intégration de Splan Visitor à Azure AD, ajoutez Splan Visitor à votre liste d’applications SaaS managées à partir de la galerie.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou un compte personnel Microsoft.
1. Dans le volet de gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, entrez **Splan Visitor** dans la zone de recherche.
1. Sélectionnez **Splan Visitor** dans le panneau de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-splan-visitor"></a>Configurer et tester l’authentification unique Azure AD pour Splan Visitor

Configurez et testez l’authentification unique Azure AD avec Splan Visitor pour un utilisateur de test nommé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur associé dans Splan Visitor.

Pour configurer et tester l’authentification unique Azure AD avec Splan Visitor, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec l’utilisateur de test B.Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Splan Visitor](#configure-splan-visitor-sso)** pour configurer les paramètres de l’authentification unique avec Splan Visitor.
    1. **[Créer un utilisateur de test Splan Visitor](#create-a-splan-visitor-test-user)** pour avoir un équivalent de B.Simon dans Splan Visitor lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le portail Azure :

1. Dans le portail Azure, accédez à la page d’intégration de l’application **Splan Visitor**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, sélectionnez l’icône de **crayon** pour **Configuration SAML de base** afin de modifier les paramètres.

   ![Capture d’écran montrant l’icône de modification/stylet pour Configuration SAML de base.](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, l’application est préconfigurée et les URL nécessaires sont préremplies dans Azure. Sélectionnez le bouton **Enregistrer** pour enregistrer la configuration.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **XML de métadonnées de fédération**. Sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Capture d’écran montrant le lien de téléchargement du XML de métadonnées de fédération.](common/metadataxml.png)

1. Dans la section **Configurer Splan Visitor**, copiez la ou les URL appropriées en fonction de vos besoins.

    ![Capture d’écran montrant la section URL de configuration.](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

Dans cette section, vous allez créer un utilisateur de test nommé B.Simon dans le portail Azure.

1. Dans le volet gauche du portail Azure, sélectionnez **Azure Active Directory**, **Utilisateurs**, puis **Tous les utilisateurs**.
1. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.
1. Dans les propriétés **Utilisateur**, effectuez les étapes suivantes :
   1. Dans le champ **Nom**, entrez **B.Simon**.  
   1. Dans le champ **Nom d’utilisateur**, entrez votre nom d’utilisateur au format _username@companydomain.extension_ . Par exemple, entrez **B.Simon@contoso.com** .
   1. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.
   1. Sélectionnez **Create** (Créer).

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Splan Visitor.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Splan Visitor** pour ouvrir la vue d’ensemble de l’application.
1. Recherchez la section **Gérer**, puis sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B.Simon** dans la liste **Utilisateurs**, puis cliquez sur **Sélectionner** au bas de l’écran.
1. Si un rôle est attribué à l’utilisateur, sélectionnez-le dans le menu déroulant **Sélectionner un rôle**. Si aucun rôle n’a été configuré pour cette application, laissez le rôle **Accès par défaut** sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, sélectionnez **Affecter**.

## <a name="configure-splan-visitor-sso"></a>Configurer l’authentification unique Splan Visitor

Pour configurer l’authentification unique avec Splan Visitor, envoyez le **XML des métadonnées de fédération** que vous avez téléchargé et les URL appropriées copiées sur le portail Azure à l’[équipe de support technique Splan Visitor](mailto:support@splan.com). Celle-ci vérifiera que la connexion à l’authentification unique SAML est correctement configurée des deux côtés.

### <a name="create-a-splan-visitor-test-user"></a>Créer un utilisateur de test Splan Visitor

Créez un utilisateur de test nommé **Britta Simon** dans Splan Visitor. Rapprochez-vous de l’[équipe de support technique Splan Visitor](mailto:support@splan.com) pour ajouter l’utilisateur à Splan Visitor. Vous devez créer et activer l’utilisateur avant d’utiliser l’authentification unique.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Testez votre configuration de l’authentification unique Azure AD avec l’une des options suivantes :

* **Portail Azure**: Sélectionnez **Tester cette application** pour vous connecter automatiquement à l’application Splan Visitor pour laquelle vous avez configuré l’authentification unique.
* **Portail Mes applications Microsoft** : Sélectionnez **Splan Visitor** pour vous connecter automatiquement à l’application Splan Visitor pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le portail Mes applications, consultez [Se connecter et démarrer des applications à partir du portail Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré Splan Visitor, vous pouvez [découvrir comment appliquer des contrôles de session dans Microsoft Defender pour Cloud Apps](/cloud-app-security/proxy-deployment-any-app). Les contrôles de session offrent une protection contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Les contrôles de session sont étendus à partir de l’accès conditionnel.
