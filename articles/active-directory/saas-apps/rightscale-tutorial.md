---
title: 'Didacticiel : Intégration d’Azure Active Directory avec RightScale | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et RightScale.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/26/2019
ms.author: jeedes
ms.openlocfilehash: 7dae32f31c30474886bcffff296e513058829b86
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124753213"
---
# <a name="tutorial-azure-active-directory-integration-with-rightscale"></a>Didacticiel : Intégration d’Azure Active Directory avec RightScale

Dans ce didacticiel, vous allez apprendre à intégrer RightScale avec Azure Active Directory (Azure AD).
L’intégration de RightScale avec Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à RightScale.
* Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Rightscale (par le biais de l’authentification unique) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).
Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Conditions préalables requises

Pour configurer l’intégration d’Azure AD avec RightScale, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un [compte gratuit](https://azure.microsoft.com/free/)
* Un abonnement RightScale pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Rightscale prend en charge l’authentification unique lancée par le **fournisseur de services et le fournisseur d’identité**

## <a name="adding-rightscale-from-the-gallery"></a>Ajout de RightScale à partir de la galerie

Pour configurer l’intégration de RightScale à Azure AD, vous devez ajouter RightScale à partir de la galerie à votre liste d’applications SaaS managées.

**Pour ajouter RightScale à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)** , cliquez sur l’icône **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez l’option **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, tapez **Rightscale**, sélectionnez **Rightscale** dans le panneau de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

     ![Rightscale dans la liste des résultats](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec RightScale sur un utilisateur de test nommé **Britta Simon**.
Pour que l’authentification unique fonctionne, une relation entre l’utilisateur Azure AD et l’utilisateur Rightscale associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec RightScale, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Configurer l’authentification unique Rightscale](#configure-rightscale-single-sign-on)** pour configurer les paramètres de l’authentification unique côté application.
3. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Créer un utilisateur de test RightScale](#create-rightscale-test-user)** pour avoir un équivalent de Britta Simon dans RightScale lié à la représentation Azure AD associée.
6. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure.

Pour configurer l’authentification unique Azure AD avec RightScale, effectuez les étapes suivantes :

1. Dans le [portail Azure](https://portal.azure.com/), dans la page d’intégration de l’application **Rightscale**, sélectionnez **Authentification unique**.

    ![Lien Configurer l’authentification unique](common/select-sso.png)

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, sélectionnez le mode **SAML/WS-Fed** afin d’activer l’authentification unique.

    ![Mode de sélection de l’authentification unique](common/select-saml-option.png)

3. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, l’utilisateur n’a rien à faire, car l’application est déjà intégrée à Azure.

    ![Capture d’écran montrant la page Configuration SAML de base.](common/preintegrated.png)

5. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    ![Capture d’écran montrant Définir des URL supplémentaires, où vous pouvez entrer une URL de connexion.](common/metadata-upload-additional-signon.png)

    Dans la zone de texte **URL de connexion**, tapez l’URL : `https://login.rightscale.com/`

6. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (Base64)** en fonction des options définies par rapport à vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

7. Dans la section **Configurer Rightscale**, copiez la ou les URL appropriées en fonction de vos besoins.

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

    a. URL de connexion

    b. Identificateur Azure AD

    c. URL de déconnexion

### <a name="configure-rightscale-single-sign-on"></a>Configurer l’authentification unique Rightscale

1. Pour que l’authentification unique soit configurée pour votre application, vous devez vous connecter à votre locataire RightScale en tant qu’administrateur.

2. Dans le menu situé en haut, cliquez sur l’onglet **Paramètres** et sélectionnez **Authentification unique**.

    ![Capture d’écran montrant l’option d’authentification unique sélectionnée dans Settings.](./media/rightscale-tutorial/tutorial_rightscale_001.png)

3. Cliquez sur le bouton **nouveau** pour ajouter **vos fournisseurs d’identité SAML**.

    ![Capture d’écran affichant le bouton New sélectionné pour ajouter un fournisseur d’identité SAML.](./media/rightscale-tutorial/tutorial_rightscale_002.png)

4. Dans la zone de texte **Nom d'affichage**, entrez le nom de votre société.

    ![Capture d’écran indiquant l’emplacement où entrer un nom d’affichage.](./media/rightscale-tutorial/tutorial_rightscale_003.png)

5. Sélectionnez **Allow RightScale-initiated SSO using a discovery hint** et entrez votre **nom de domaine** dans la zone de texte située dessous.

    ![Capture d’écran précisant l’endroit où vous pouvez spécifier une méthode de connexion.](./media/rightscale-tutorial/tutorial_rightscale_004.png)

6. Collez la valeur de l’**URL de connexion** que vous avez copiée à partir du portail Azure dans **SAML SSO Endpoint** (Point de terminaison SSO SAML) dans RightScale.

    ![Capture d’écran indiquant où vous pouvez entrer un point de terminaison SSO SAML.](./media/rightscale-tutorial/tutorial_rightscale_006.png)

7. Collez la valeur de l’**Identificateur Azure AD** que vous avez copiée à partir du portail Azure dans **SAML EntityID** (ID d’entité SAML) dans RightScale.

    ![Capture d’écran indiquant où vous pouvez entrer un ID d’entité SAML.](./media/rightscale-tutorial/tutorial_rightscale_008.png)

8. Cliquez sur le bouton **Explorateur** pour charger le certificat que vous avez téléchargé à partir du portail Azure.

    ![Capture d’écran indiquant où vous pouvez spécifier votre certificat de signature SAML.](./media/rightscale-tutorial/tutorial_rightscale_009.png)

9. Cliquez sur **Enregistrer**.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

1. Dans le volet gauche du portail Azure, sélectionnez **Azure Active Directory**, sélectionnez **Utilisateurs**, puis sélectionnez **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](common/users.png)

2. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.

    ![Bouton Nouvel utilisateur](common/new-user.png)

3. Dans les propriétés de l’utilisateur, effectuez les étapes suivantes.

    ![Boîte de dialogue Utilisateur](common/user-properties.png)

    a. Dans le champ **Nom**, entrez **BrittaSimon**.
  
    b. Dans le champ **Nom d’utilisateur**, tapez `brittasimon@yourcompanydomain.extension`  
    Par exemple : BrittaSimon@contoso.com

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ Mot de passe.

    d. Cliquez sur **Créer**.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à RightScale.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, **Toutes les applications**, puis **Rightscale**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **RightScale**.

    ![Lien Rightscale dans la liste des applications](common/all-applications.png)

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

4. Cliquez sur le bouton **Ajouter un utilisateur**, puis sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Volet Ajouter une attribution](common/add-assign-user.png)

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

6. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

7. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

### <a name="create-rightscale-test-user"></a>Créer un utilisateur de test Rightscale

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans Rightscale. Collaborez avec l’[équipe de support technique Rightscale](mailto:support@rightscale.com) pour ajouter les utilisateurs dans la plateforme Rightscale. Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique.

### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette Rightscale dans le volet d’accès, vous devez être connecté automatiquement à l’application Rightscale pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](./tutorial-list.md)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](../conditional-access/overview.md)