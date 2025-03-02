---
title: 'Didacticiel : Intégration d’Azure Active Directory à iQualify LMS | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et iQualify LMS.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: 399cd4ca84918b8d925de8349b36cac97bbab2c4
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124809521"
---
# <a name="tutorial-azure-active-directory-integration-with-iqualify-lms"></a>Didacticiel : Intégration d’Azure Active Directory à iQualify LMS

Dans ce didacticiel, vous allez apprendre à intégrer iQualify LMS avec Azure Active Directory (Azure AD).
L’intégration d’iQualify LMS avec Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à iQualify LMS.
* Vous pouvez permettre à vos utilisateurs d’être connectés automatiquement à iQualify LMS (par le biais de l’authentification unique) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).
Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD avec iQualify LMS, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).
* Abonnement à iQualify LMS pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* iQualify LMS prend en charge l’authentification unique lancée par le **fournisseur de services et le fournisseur d’identité**
* iQualify LMS prend en charge l’attribution d’utilisateurs **Juste-à-temps**

## <a name="adding-iqualify-lms-from-the-gallery"></a>Ajout d’iQualify LMS à partir de la galerie

Pour configurer l’intégration d’iQualify LMS avec Azure AD, vous devez ajouter iQualify LMS à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter iQualify LMS à partir de la galerie, effectuez les étapes suivantes :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)** , cliquez sur l’icône **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez l’option **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, tapez **iQualify LMS**, sélectionnez **iQualify LMS** dans le panneau de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

     ![iQualify LMS dans la liste des résultats](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec iQualify LMS, avec un utilisateur de test appelé **Britta Simon**.
Pour que l’authentification unique fonctionne, une relation entre un utilisateur Azure AD et l’utilisateur iQualify LMS associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec iQualify LMS, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Configurer l’authentification unique iQualify LMS](#configure-iqualify-lms-single-sign-on)** pour configurer les paramètres de l’authentification unique côté application.
3. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Créer un utilisateur de test iQualify LMS](#create-iqualify-lms-test-user)** pour avoir un équivalent de Britta Simon dans iQualify LMS lié à la représentation Azure AD de l’utilisateur.
6. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure.

Pour configurer l’authentification unique Azure AD avec iQualify LMS, effectuez les étapes suivantes :

1. Dans le [portail Azure](https://portal.azure.com/), dans la page d’intégration de l’application **iQualify LMS**, sélectionnez **Authentification unique**.

   ![Lien Configurer l’authentification unique](common/select-sso.png)

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, sélectionnez le mode **SAML/WS-Fed** afin d’activer l’authentification unique.

   ![Mode de sélection de l’authentification unique](common/select-saml-option.png)

3. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

4. À la section **Configuration SAML de base**, si vous souhaitez configurer l’application en mode initié par **IDP**, suivez les étapes ci-dessous :

   ![Capture d’écran montrant Configuration SAML de base, où vous pouvez entrer l’identificateur, l’URL de réponse, et sélectionner Enregistrer.](common/idp-intiated.png)
   
   1. Dans la zone de texte **Identificateur**, tapez une URL au format suivant :

      * Environnement de production : `https://<yourorg>.iqualify.com/`
      * Environnement de test : `https://<yourorg>.iqualify.io`

   2. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant :

      * Environnement de production : `https://<yourorg>.iqualify.com/auth/saml2/callback` 
      * Environnement de test : `https://<yourorg>.iqualify.io/auth/saml2/callback`

5. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

   ![Capture d’écran montrant Set additional URLs, où vous pouvez entrer une URL de connexion.](common/metadata-upload-additional-signon.png)

   Dans la zone de texte **URL d’authentification**, tapez une URL au format suivant :

      * Environnement de production : `https://<yourorg>.iqualify.com/login`
      * Environnement de test : `https://<yourorg>.iqualify.io/login`

   > [!NOTE]
   > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL de réponse et l’URL de connexion réels. Pour obtenir ces valeurs, contactez l’[équipe de support technique d’iQualify LMS](https://www.iqualify.com/). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

6. L’application iQualify LMS attend les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à la configuration des attributs du jeton SAML. La capture d’écran suivante montre la liste des attributs par défaut. Cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Attributs d’utilisateur**.

    ![Capture d’écran montrant User Attributes avec l’icône Edit sélectionnée.](common/edit-attribute.png)

7. Dans la section **Revendications des utilisateurs** de la boîte de dialogue **Attributs utilisateur**, modifiez les revendications en utilisant l’icône **Modifier** ou ajoutez des revendications en utilisant l’option **Ajouter une nouvelle revendication** pour configurer l’attribut de jeton SAML comme sur l’image ci-dessus et procédez comme suit :

    | Nom | Attribut source|
    | --- | --- |
    | email | user.userprincipalname |
    | first_name | user.givenname |
    | last_name | user.surname |
    | person_id | « votre attribut » |

    a. Cliquez sur le bouton **Ajouter une nouvelle revendication** pour ouvrir la boîte de dialogue **Gérer les revendications des utilisateurs**.

    ![Capture d’écran montrant User claims avec l’option permettant d’ajouter une nouvelle revendication.](common/new-save-attribute.png)

    ![Capture d’écran montrant la boîte de dialogue Manage user claims où vous pouvez entrer les valeurs décrites.](common/new-attribute-details.png)

    b. Dans la zone de texte **Attribut**, indiquez le nom d’attribut pour cette ligne.

    c. Laissez le champ **Espace de noms** vide.

    d. Sélectionnez Source comme **Attribut**.

    e. Dans la liste **Attribut de la source**, tapez la valeur d’attribut indiquée pour cette ligne.

    f. Cliquez sur **OK**

    g. Cliquez sur **Enregistrer**.

    > [!Note]
    > L’attribut **person_id** est **facultatif**.

8. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (Base64)** en fonction des options définies par rapport à vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

9. Dans la section **Configurer iQualify LMS**, copiez la ou les URL appropriées, selon vos besoins.

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

    a. URL de connexion

    b. Identificateur Azure AD

    c. URL de déconnexion

### <a name="configure-iqualify-lms-single-sign-on"></a>Configurer l’authentification unique iQualify LMS

1. Ouvrez une nouvelle fenêtre de navigateur, puis connectez-vous à votre environnement iQualify LMS en tant qu’administrateur.

1. Une fois connecté, cliquez sur votre avatar en haut à droite, puis sur **Account settings**.

    ![Paramètres de compte](./media/iqualify-tutorial/setting1.png)

1. Dans la zone de paramètres de compte, cliquez sur le menu de ruban sur la gauche et cliquez sur **INTEGRATIONS**.

    ![INTEGRATIONS](./media/iqualify-tutorial/setting2.png)

1. Sous INTEGRATIONS, cliquez sur l’icône **SAML**.

    ![Icône SAML](./media/iqualify-tutorial/setting3.png)

1. Dans la boîte de dialogue **SAML Authentication Settings** , procédez comme suit :

    ![Paramètres d’authentification SAML](./media/iqualify-tutorial/setting4.png)

    a. Dans la zone **SAML SINGLE SIGN-ON SERVICE URL**, entrez la valeur **URL de connexion** copiée à partir de la fenêtre Configuration de l’application Azure AD.

    b. Dans la zone **SAML LOGOUT URL**, collez la valeur **URL de déconnexion** copiée à partir de la fenêtre Configuration de l’application Azure AD.

    c. Ouvrez le fichier de certificat que vous avez téléchargé dans le Bloc-notes, copiez son contenu, puis collez-le dans la zone **PUBLIC CERTIFICATE**.

    d. Dans **LOGIN BUTTON LABEL**, entrez le nom du bouton à afficher dans la page de connexion.

    e. Cliquez sur **ENREGISTRER**.

    f. Cliquez sur **UPDATE**.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

1. Dans le volet gauche du portail Azure, sélectionnez **Azure Active Directory**, sélectionnez **Utilisateurs**, puis sélectionnez **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](common/users.png)

2. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.

    ![Bouton Nouvel utilisateur](common/new-user.png)

3. Dans les propriétés de l’utilisateur, effectuez les étapes suivantes.

    ![Boîte de dialogue Utilisateur](common/user-properties.png)

    a. Dans le champ **Nom**, entrez **BrittaSimon**.
  
    b. Dans le champ **Nom d’utilisateur**, tapez **brittasimon@yourcompanydomain.extension**  
    Par exemple : BrittaSimon@contoso.com

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ Mot de passe.

    d. Cliquez sur **Créer**.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à iQualify LMS.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, **Toutes les applications**, puis **iQualify LMS**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **iQualify LMS**.

    ![Lien iQualify LMS dans la liste des applications](common/all-applications.png)

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

4. Cliquez sur le bouton **Ajouter un utilisateur**, puis sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Volet Ajouter une attribution](common/add-assign-user.png)

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

6. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

7. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

### <a name="create-iqualify-lms-test-user"></a>Créer un utilisateur de test iQualify LMS

Dans cette section, un utilisateur appelé Britta Simon est créé dans iQualify LMS. iQualify LMS prend en charge l’attribution d’utilisateurs juste-à-temps, option activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas encore d’utilisateur dans iQualify LMS, il en est créé un après l’authentification.

### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette iQualify LMS dans le panneau d’accès, la page de connexion de l’application iQualify LMS doit s’afficher. 

   ![page de connexion](./media/iqualify-tutorial/login.png) 

Cliquez sur le bouton **Sign in with Azure AD**. Vous devriez être authentifié automatiquement auprès de votre application iQualify LMS.

Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510). 

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](./tutorial-list.md)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](../conditional-access/overview.md)