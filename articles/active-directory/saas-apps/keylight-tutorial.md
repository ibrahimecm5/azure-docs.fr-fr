---
title: 'Didacticiel : Intégration d’Azure Active Directory avec LockPath Keylight | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et LockPath Keylight.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/11/2021
ms.author: jeedes
ms.openlocfilehash: abaa441410f5cbccad3374fc75a074a7678e0d8f
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132294755"
---
# <a name="tutorial-azure-active-directory-integration-with-lockpath-keylight"></a>Didacticiel : Intégration d’Azure Active Directory avec LockPath Keylight

Dans ce tutoriel, vous allez apprendre à intégrer LockPath Keylight avec Azure Active Directory (Azure AD). Quand vous intégrez LockPath Keylight avec Azure AD, vous pouvez :

* Contrôler, dans Azure AD, qui a accès à LockPath Keylight.
* Permettre à vos utilisateurs de se connecter automatiquement à LockPath Keylight avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD avec LockPath Keylight, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un [compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement LockPath Keylight pour lequel l’authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* LockPath Keylight prend en charge l’authentification unique lancée par le **fournisseur de services**.
* LockPath Keylight prend en charge le provisionnement d’utilisateurs **juste-à-temps**.

## <a name="add-lockpath-keylight-from-the-gallery"></a>Ajouter LockPath Keylight à partir de la galerie

Pour configurer l’intégration de LockPath Keylight à Azure AD, vous devez ajouter LockPath Keylight à partir de la galerie à votre liste d’applications SaaS managées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **LockPath Keylight** dans la zone de recherche.
1. Sélectionnez **LockPath Keylight** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-lockpath-keylight"></a>Configurer et tester l’authentification unique Azure AD pour LockPath Keylight

Configurez et testez l’authentification unique Azure AD avec LockPath Keylight pour un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur associé dans LockPath Keylight.

Pour configurer et tester l’authentification unique Azure AD avec LockPath Keylight, procédez comme suit :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique LockPath Keylight](#configure-lockpath-keylight-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test LockPath Keylight](#create-lockpath-keylight-test-user)** pour avoir un équivalent de B.Simon dans LockPath Keylight, lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure.

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **LockPath Keylight**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    a. Dans la zone de texte **Identificateur (ID d’entité)** , saisissez une URL au format suivant : `https://<COMPANY_NAME>.keylightgrc.com`

    b. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://<COMPANY_NAME>.keylightgrc.com/Login.aspx`

    c. Dans la zone de texte **URL de connexion**, saisissez une URL au format suivant : `https://<COMPANY_NAME>.keylightgrc.com/`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL d’authentification et l’URL de réponse réels. Pour obtenir ces valeurs, contactez l’[équipe de support technique LockPath Keylight](https://www.lockpath.com/contact/). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

5. Dans la page **Configurer l’authentification unique avec SAML**, à la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (Raw)** en fonction des options définies, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificateraw.png)

6. Dans la section **Configurer LockPath Keylight**, copiez la ou les URL appropriées en fonction de vos besoins.

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

Dans cette section, vous allez créer un utilisateur de test appelé B. Simon dans le portail Azure.

1. Dans le volet gauche du Portail Azure, sélectionnez **Azure Active Directory**, **Utilisateurs**, puis **Tous les utilisateurs**.
1. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.
1. Dans les propriétés **Utilisateur**, effectuez les étapes suivantes :
   1. Dans le champ **Nom**, entrez `B.Simon`.  
   1. Dans le champ **Nom de l’utilisateur**, entrez username@companydomain.extension. Par exemple : `B.Simon@contoso.com`.
   1. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.
   1. Cliquez sur **Créer**.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à LockPath Keylight.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **LockPath Keylight**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-lockpath-keylight-sso"></a>Configurer l’authentification unique LockPath Keylight

1. Pour activer l’authentification unique dans LockPath Keylight, procédez comme suit :

    a. Connectez-vous à votre compte LockPath Keylight en tant qu’administrateur.

    b. Dans le menu situé en haut, cliquez sur **Person** (Personne), puis sélectionnez **Keylight Setup** (Installation de Keylight).

    ![Capture d’écran montrant l’icône « Person » mise en évidence et l’option « Keylight Setup » sélectionnée dans la liste déroulante.](./media/keylight-tutorial/setup-icon.png)

    c. Dans l’arborescence sur la gauche, cliquez sur **SAML**.

    ![Capture d’écran montrant l’élément « SAML » sélectionné dans l’arborescence.](./media/keylight-tutorial/treeview.png)

    d. Dans la boîte de dialogue **SAML Settings** (Paramètres SAML), cliquez sur **Edit** (Modifier).

    ![Capture d’écran montrant la fenêtre « SAML Settings » avec le bouton « Edit » sélectionné.](./media/keylight-tutorial/edit-icon.png)

1. Dans la page de boîte de dialogue **Edit SAML Settings** (Modifier les paramètres SAML), procédez comme suit :

    ![Configure Single Sign-On](./media/keylight-tutorial/settings.png)

    a. Affectez à **SAML authentication** (Authentication SAML) la valeur **Active**.

    b. Dans la zone de texte **Identity Provider Login URL** (URL de connexion du fournisseur d’identité), collez la valeur de l’**URL de connexion** que vous avez copiée à partir du portail Azure.

    c. Dans la zone de texte **Identity Provider Logout URL** (URL de déconnexion du fournisseur d’identité), collez la valeur de l’**URL de déconnexion** que vous avez copiée à partir du portail Azure.

    d. Cliquez sur **Choose File** (Choisir un fichier) pour sélectionner votre certificat LockPath Keylight téléchargé, puis sur **Ouvrir** pour charger le certificat.

    e. Affectez à **SAML User Id location** (Emplacement de l’ID utilisateur SAML) la valeur **NameIdentifier element of the subject statement** (Élément NameIdentifier de l’instruction Subject).

    f. Indiquez la valeur **Keylight Service Provider** (Fournisseur de service Keylight) au format suivant : `https://<CompanyName>.keylightgrc.com`.

    g. Affectez à **Auto-provision users** (Configuration automatique des utilisateurs) la valeur **Active**.

    h. Affectez à **Auto-provision account type** (Configuration automatique du type de compte) la valeur **Full User** (Utilisateur global).

    i. Définissez **Auto-provision security role** (Configuration automatique du rôle de sécurité), sélectionnez **Standard User with SAML** (Utilisateur standard avec SAML).

    j. Définissez **Auto-provision security config** (Configuration automatique des paramètres de sécurité), sélectionnez **Standard User Configuration** (Configuration utilisateur standard).

    k. Dans la zone de texte **Email Attribute** (Attribut d’e-mail), entrez `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    l. Dans la zone de texte **First name attribute** (Attribut de prénom), entrez `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.

    m. Dans la zone de texte **Last name attribute** (Attribut de nom), entrez `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`.

    n. Cliquez sur **Enregistrer**.

### <a name="create-lockpath-keylight-test-user"></a>Créer un utilisateur de test LockPath Keylight

Dans cette section, un utilisateur appelé Britta Simon est créé dans LockPath Keylight. LockPath Keylight prend en charge le provisionnement d’utilisateur juste-à-temps, option activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas encore d’utilisateur dans LockPath Keylight, il en est créé un après l’authentification. Si vous devez créer un utilisateur manuellement, contactez l’[équipe de support technique LockPath Keylight](https://www.lockpath.com/contact/).

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion à LockPath Keylight à partir de laquelle vous pouvez lancer le flux de connexion. 

* Accédez directement à l’URL de connexion à LockPath Keylight et lancez le flux de connexion.

* Vous pouvez utiliser Mes applications de Microsoft. Quand vous cliquez sur la vignette LockPath Keylight dans Mes applications, vous êtes redirigé vers l’URL de connexion à LockPath Keylight. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré LockPath Keylight, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
