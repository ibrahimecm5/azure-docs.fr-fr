---
title: 'Tutoriel : Intégration de l’authentification unique (SSO) Azure Active Directory à Litmus | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Litmus.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/24/2020
ms.author: jeedes
ms.openlocfilehash: 7b80f3714c6cc85fabd086a15493c48594da49c2
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132313405"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-litmus"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory à Litmus

Dans ce tutoriel, vous allez découvrir comment intégrer Litmus à Azure Active Directory (Azure AD). Quand vous intégrez Litmus à Azure AD, vous pouvez :

* Contrôler qui dans Azure AD a accès à Litmus.
* Permettre à vos utilisateurs de se connecter automatiquement à Litmus avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Litmus pour lequel l’authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* Litmus prend en charge l’authentification unique initiée par le **fournisseur de services et le fournisseur d’identité**

## <a name="adding-litmus-from-the-gallery"></a>Ajout de Litmus à partir de la galerie

Pour configurer l’intégration de Litmus à Azure AD, vous devez ajouter Litmus à votre liste d’applications SaaS gérées à partir de la galerie.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Litmus** dans la zone de recherche.
1. Sélectionnez **Litmus** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.


## <a name="configure-and-test-azure-ad-sso-for-litmus"></a>Configurer et tester l’authentification unique Azure AD pour Litmus

Configurez et testez l’authentification unique Azure AD avec Litmus pour un utilisateur de test nommé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur associé dans Litmus.

Pour configurer et tester l’authentification unique Azure AD avec Litmus, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Litmus](#configure-litmus-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test Litmus](#create-litmus-test-user)** pour avoir un équivalent de B.Simon dans Litmus lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, dans la page d’intégration de l’application **Litmus**, recherchez la section **Gérer**, puis sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de modification/stylet de **Configuration SAML de base** pour modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, l’utilisateur n’a rien à faire, car l’application est déjà intégrée à Azure.

1. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    Dans la zone de texte **URL de connexion**, tapez l’URL : `https://litmus.com/sessions/new`

1. Cliquez sur **Enregistrer**.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **Certificat (brut)** , puis sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificateraw.png)

1. Dans la section **Configurer Litmus**, copiez la ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Litmus.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Litmus**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.

1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-litmus-sso"></a>Configurer l’authentification unique Litmus

1. Pour automatiser la configuration dans Litmus, vous devez installer l’**extension de navigateur My Apps Secure Sign-in** en cliquant sur **Installer l’extension**.

    ![Extension My apps](common/install-myappssecure-extension.png)

2. Une fois l’extension ajoutée au navigateur, un clic sur **Configurer Litmus** vous redirige vers l’application Litmus. À partir de là, indiquez les informations d’identification de l’administrateur pour vous connecter à Litmus. Cette extension de navigateur configure automatiquement l’application et automatise les étapes 3 à 6.

    ![Configuration](common/setup-sso.png)

3. Si vous souhaitez configurer Litmus manuellement, dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Litmus en tant qu’administrateur.

1. Cliquez sur **Security** (Sécurité) dans le panneau de navigation gauche.

    ![Capture d’écran montrant l’élément Security sélectionné.](./media/litmus-tutorial/security-img.png)

1. Dans la section **Configure SAML Authentication** (Configurer l’authentification SAML), effectuez les étapes suivantes :

    ![Capture d’écran montrant la section Configure SAML Authentication, dans laquelle vous pouvez entrer les valeurs décrites.](./media/litmus-tutorial/configure1.png)

    a. Mettez le bouton bascule **Enable SAML** (Activer SAML) sur la position activé.

    b. Sélectionnez **Generic** (Générique) pour le fournisseur.

    c. Entrez le nom du fournisseur d’identité dans **Identity Provider Name**. Par exemple, `Azure AD`

1. Procédez comme suit :

    ![Capture d’écran affichant la section dans laquelle vous pouvez indiquer les valeurs décrites.](./media/litmus-tutorial/configure3.png)

    a. Dans la zone de texte **SAML 2.0 Endpoint(HTTP)** [Point de terminaison SAML 2.0 (HTTP)], collez la valeur de l’**URL de connexion** que vous avez copiée à partir du portail Azure.

    b. Ouvrez le fichier de **certificat** téléchargé à partir du portail Azure dans le Bloc-notes, puis collez le contenu dans la zone de texte **X.509 Certificate** (Certificat X.509).

    c. Cliquez sur **Save SAML settings** (Enregistrer les paramètres SAML).

### <a name="create-litmus-test-user"></a>Créer un utilisateur de test Litmus

1. Dans une autre fenêtre de navigateur web, connectez-vous à l’application Litmus en tant qu’administrateur.

1. Cliquez sur **Accounts** (Comptes) dans le panneau de navigation gauche.

    ![Capture d’écran montrant l’élément Accounts sélectionné.](./media/litmus-tutorial/accounts-img.png)

1. Cliquez sur l’onglet **Add New User** (Ajouter un nouvel utilisateur).

    ![Capture d’écran montrant l’élément Add New User sélectionné.](./media/litmus-tutorial/add-new-user.png)

1. Dans la section **Add User** (Ajouter un utilisateur), effectuez les étapes suivantes :

    ![Capture d’écran affichant la section Add User dans laquelle vous pouvez indiquer les valeurs décrites.](./media/litmus-tutorial/user-profile.png)

    a. Dans la zone de texte **Email**, entrez l’adresse e-mail de l’utilisateur, par exemple **B.Simon\@contoso.com**

    b. Dans la zone de texte **First Name**, entrez le prénom de l’utilisateur, par exemple **B**.

    c. Dans la zone de texte **Last Name**, tapez le nom de l’utilisateur, par exemple **Simon**.

    d. Cliquez sur **Créer l’utilisateur**.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes.

#### <a name="sp-initiated"></a>Lancée par le fournisseur de services :

* Cliquez sur **Tester cette application** dans le portail Azure. Cette opération redirige vers l’URL de connexion Litmus, à partir de laquelle vous pouvez lancer le processus de connexion.

* Accédez directement à l’URL de connexion Litmus pour lancer le processus de connexion.

#### <a name="idp-initiated"></a>Lancée par le fournisseur d’identité :

* Cliquez sur **Tester cette application** dans le portail Azure ; vous devez être connecté automatiquement à l’instance de Litmus pour laquelle vous avez configuré l’authentification unique.

Vous pouvez aussi utiliser Mes applications de Microsoft pour tester l’application dans n’importe quel mode. Si, quand vous cliquez sur la vignette Litmus dans Mes applications, le mode Fournisseur de services est configuré, vous êtes redirigé vers la page de connexion de l’application pour lancer le flux de connexion ; s’il s’agit du mode Fournisseur d’identité, vous êtes automatiquement connecté à l’instance de Litmus pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré Litmus, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
