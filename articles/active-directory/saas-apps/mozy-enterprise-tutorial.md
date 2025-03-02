---
title: 'Didacticiel : Intégration d’Azure Active Directory avec Mozy Enterprise | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Mozy Enterprise.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/01/2019
ms.author: jeedes
ms.openlocfilehash: 5277e5ab395409888d52157203eb3a61a080ffc0
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124742146"
---
# <a name="tutorial-azure-active-directory-integration-with-mozy-enterprise"></a>Didacticiel : Intégration d’Azure Active Directory avec Mozy Enterprise

Dans ce didacticiel, vous allez apprendre à intégrer Mozy Enterprise avec Azure Active Directory (Azure AD).
L’intégration du logiciel Mozy Enterprise avec Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à Mozy Enterprise.
* Vous pouvez permettre aux utilisateurs de se connecter automatiquement à Mozy Enterprise (par le biais de l’authentification unique) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).
Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Conditions préalables requises

Pour configurer l’intégration d’Azure AD avec Mozy Enterprise, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).
* Abonnement Mozy Enterprise pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Mozy Enterprise prend en charge l’authentification unique démarrée par le **fournisseur de services**

## <a name="adding-mozy-enterprise-from-the-gallery"></a>Ajout de Mozy Enterprise à partir de la galerie

Pour configurer l’intégration de Mozy Enterprise à Azure AD, vous devez ajouter Mozy Enterprise à votre liste d’applications SaaS gérées, depuis la galerie.

**Pour ajouter Mozy Enterprise à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)** , cliquez sur l’icône **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez l’option **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, tapez **Mozy Enterprise**, sélectionnez **Mozy Enterprise** dans le volet des résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

     ![Mozy Enterprise dans la liste des résultats](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Mozy Enterprise avec un utilisateur de test appelé **Britta Simon**.
Pour que l’authentification unique fonctionne, une relation entre l’utilisateur Azure AD et l’utilisateur Mozy Enterprise associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec Mozy Enterprise, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Configurer l’authentification unique Mozy Enterprise](#configure-mozy-enterprise-single-sign-on)** pour configurer les paramètres de l’authentification unique côté application.
3. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Créez un utilisateur de test Mozy Enterprise](#create-mozy-enterprise-test-user)** pour obtenir un équivalent de Britta Simon dans Mozy Enterprise, lié à la représentation Azure AD associée.
6. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure.

Pour configurer l’authentification unique Azure AD avec Mozy Enterprise, procédez comme suit :

1. Dans le [portail Azure](https://portal.azure.com/), dans la page d’intégration de l’application **Mozy Enterprise**, sélectionnez **Authentification unique**.

    ![Lien Configurer l’authentification unique](common/select-sso.png)

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, sélectionnez le mode **SAML/WS-Fed** afin d’activer l’authentification unique.

    ![Mode de sélection de l’authentification unique](common/select-saml-option.png)

3. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    ![Informations d’authentification unique de domaine et d’URL Mozy Enterprise](common/sp-signonurl.png)

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<tenantname>.Mozyenterprise.com`

    > [!NOTE]
    > Cette valeur n’est pas la valeur réelle. Mettez à jour la valeur avec l’URL de connexion réelle. Contactez l’[équipe de support Mozy Enterprise](https://www.safenames.net/about-us/contact-us) pour obtenir la valeur. Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

5. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (Base64)** en fonction des options définies par rapport à vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

6. Dans la section **Configurer Mozy Enterprise**, copiez la ou les URL appropriées en fonction de vos besoins.

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

    a. URL de connexion

    b. Identificateur Azure AD

    c. URL de déconnexion

### <a name="configure-mozy-enterprise-single-sign-on&quot;></a>Configurer l’authentification unique Mozy Enterprise

1. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Mozy Enterprise en tant qu’administrateur.

2. Dans la section **Configuration**, cliquez sur **Authentication Policy**.
   
    ![Capture d’écran montrant l’élément Authentication Policy sélectionné à partir de Configuration.](./media/mozy-enterprise-tutorial/ic777314.png &quot;Authentication Policy")

3. Dans la section **Authentication Policy** , procédez comme suit :
   
    ![Capture d’écran montrant la section Authentication Policy, dans laquelle vous pouvez entrer les valeurs décrites.](./media/mozy-enterprise-tutorial/ic777315.png "Authentication Policy")
   
    a. Sélectionnez **Directory Service** comme **Provider**.
   
    b. Sélectionnez **Use LDAP Push**.
   
    c. Cliquez sur l’onglet **SAML Authentication** .
   
    d. Collez l’**URL de connexion**, que vous avez copiée dans le portail Azure, dans la zone de texte **Authentication URL** (URL d’authentification).
   
    e. Collez l’**Identificateur Azure AD**, que vous avez copié sur le portail Azure, dans la zone de texte **Point de terminaison SAML**.
   
    f. Ouvrez votre certificat téléchargé, codé en base 64, dans le bloc-notes, copiez son contenu dans le Presse-papiers et collez-le dans la zone de texte **Certificat SAML**.
   
    g. Sélectionnez **Enable SSO for Admins to log in with their network credentials**.
   
    h. Cliquez sur **Enregistrer les modifications**.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD 

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

1. Dans le volet gauche du portail Azure, sélectionnez **Azure Active Directory**, sélectionnez **Utilisateurs**, puis sélectionnez **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](common/users.png)

2. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.

    ![Bouton Nouvel utilisateur](common/new-user.png)

3. Dans les propriétés de l’utilisateur, effectuez les étapes suivantes.

    ![Boîte de dialogue Utilisateur](common/user-properties.png)

    a. Dans le champ **Nom**, entrez **BrittaSimon**.
  
    b. Dans le champ **Nom d’utilisateur**, tapez **brittasimon\@domainedevotreentreprise.extension**.  
    Par exemple : BrittaSimon@contoso.com

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ Mot de passe.

    d. Cliquez sur **Créer**.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Mozy Enterprise.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, **Toutes les applications**, puis **Mozy Enterprise**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **Mozy Enterprise**.

    ![Lien Mozy Enterprise dans la liste des applications](common/all-applications.png)

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

4. Cliquez sur le bouton **Ajouter un utilisateur**, puis sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Volet Ajouter une attribution](common/add-assign-user.png)

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

6. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

7. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

### <a name="create-mozy-enterprise-test-user&quot;></a>Créer un utilisateur de test Mozy Enterprise

Pour permettre aux utilisateurs Azure AD de se connecter à Mozy Enterprise, vous devez les approvisionner dans Mozy Enterprise. Dans le cas de Mozy Enterprise, l’approvisionnement est une tâche manuelle.

>[!NOTE]
>Vous pouvez utiliser tout autre outil ou API de création de compte d’utilisateur Mozy Enterprise fourni par Mozy Enterprise pour provisionner des comptes d’utilisateur Azure AD.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous à votre locataire **Mozy Enterprise** .

2. Cliquez sur **Users**, puis sur **Add New User**.
   
    ![Utilisateurs](./media/mozy-enterprise-tutorial/ic777317.png &quot;Utilisateurs")
   
    >[!NOTE]
    >L’option **Add New User** ne s’affiche que si **Mozy** est sélectionné comme fournisseur sous **Authentication policy**. Si l’authentification SAML est configurée, les utilisateurs sont ajoutés automatiquement lors de la première connexion à l’aide de l’authentification unique.
    
3. Dans la boîte de dialogue New User, procédez comme suit :
   
    ![Ajout d’utilisateurs](./media/mozy-enterprise-tutorial/ic777318.png "Ajouter des utilisateurs")
   
    a. Dans la liste **Choose a Group** , sélectionnez un groupe.
   
    b. Dans la liste **What type of user** , sélectionnez un type.
   
    c. Dans la zone de texte **Username** , tapez le nom de l’utilisateur Azure AD.
   
    d. Dans la zone de texte **Email** , tapez l’adresse de messagerie de l’utilisateur Azure AD.
   
    e. Sélectionnez **Send user instruction email**.
   
    f. Cliquez sur **Add User(s)** .

     >[!NOTE]
     > Après la création de l’utilisateur, un message électronique sera envoyé à l’utilisateur Azure AD avec un lien pour confirmer le compte avant qu’il ne soit activé.

### <a name="test-single-sign-on"></a>Tester l’authentification unique 

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Le fait de cliquer sur la vignette Mozy Enterprise dans le panneau d’accès doit vous connecter automatiquement à l’application Mozy Enterprise pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](./tutorial-list.md)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](../conditional-access/overview.md)