---
title: 'Tutoriel : Intégration de l’authentification unique Azure Active Directory à AskYourTeam | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et AskYourTeam.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/18/2020
ms.author: jeedes
ms.openlocfilehash: bff068248a0ac47954507966c9c0d69826ce6b7b
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132288408"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-askyourteam"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory à AskYourTeam

Dans ce tutoriel, vous allez apprendre à intégrer AskYourTeam à Azure Active Directory (Azure AD). Quand vous intégrez AskYourTeam à Azure AD, vous pouvez :

* Contrôler qui dans Azure AD a accès à AskYourTeam.
* Permettre aux utilisateurs de se connecter automatiquement à AskYourTeam avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement AskYourTeam pour lequel l’authentification unique (SSO) est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* AskYourTeam prend en charge l’authentification unique lancée par le **fournisseur de services et le fournisseur d’identité**.

## <a name="adding-askyourteam-from-the-gallery"></a>Ajout d’AskYourTeam à partir de la galerie

Pour configurer l’intégration d’AskYourTeam à Azure AD, vous devez ajouter AskYourTeam à votre liste d’applications SaaS gérées, à partir de la galerie.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **AskYourTeam** dans la zone de recherche.
1. Sélectionnez **AskYourTeam** dans le volet des résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-askyourteam"></a>Configurer et tester l’authentification unique Azure AD pour AskYourTeam

Configurez et testez l’authentification unique Azure AD avec AskYourTeam pour un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur AskYourTeam associé.

Pour configurer et tester l’authentification unique Azure AD avec AskYourTeam, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique AskYourTeam](#configure-askyourteam-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test AskYourTeam](#create-askyourteam-test-user)** pour avoir un équivalent de B.Simon dans AskYourTeam lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **StatusPage**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de modification/stylet de **Configuration SAML de base** pour modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, si vous souhaitez configurer l’application en mode Initié par le **fournisseur d’identité**, entrez les valeurs pour les champs suivants :

    Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://<COMPANY>.app.askyourteam.com/users/auth/saml/callback`

1. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<COMPANY>.app.askyourteam.com/login`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec les valeurs d’URL de réponse et d’URL de connexion réelles, qui sont expliquées plus loin dans le tutoriel.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **Certificat (en base64)** , puis sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

1. Dans la section **Configurer AskYourTeam**, copiez la ou les URL appropriées, selon vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à AskYourTeam.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **AskYourTeam**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.

1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-askyourteam-sso"></a>Configurer l’authentification unique AskYourTeam

1. Pour automatiser la configuration dans AskYourTeam, vous devez installer l’**extension de navigateur My Apps Secure Sign-in** en cliquant sur **Installer l’extension**.

    ![Extension My apps](common/install-myappssecure-extension.png)

2. Une fois l’extension ajoutée au navigateur, cliquez sur **Configurer AskYourTeam** pour être redirigé vers l’application AskYourTeam. À partir de là, indiquez les informations d’identification de l’administrateur pour vous connecter à AskYourTeam. Cette extension de navigateur configure automatiquement l’application pour vous et automatise les étapes 3 à 7.

    ![Configuration](common/setup-sso.png)

3. Si vous souhaitez configurer AskYourTeam manuellement, dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise AskYourTeam en tant qu’administrateur.

1. Cliquez sur **My Organization**.

    ![Capture d’écran montrant le lien My Organization.](./media/askyourteam-tutorial/user1.png)

1. Cliquez sur **Integrations** (Intégrations).

    ![Capture d’écran montrant le lien Integrations.](./media/askyourteam-tutorial/configure1.png)

1. Cliquez sur **Edit Settings** (Modifier les paramètres).

    ![Capture d’écran montrant le message Single Sign-On avec un bouton Edit Settings.](./media/askyourteam-tutorial/configure2.png)

1. Dans la page **Edit Single Sign-On Integration** (Modifier l’intégration de l’authentification unique), procédez comme suit : 

    ![Capture d’écran montrant Edit Single Sign-On Integration où vous pouvez entrer les valeurs pour cette étape.](./media/askyourteam-tutorial/configure3.png)

    a. Dans la zone de texte **SAML Single Sign-On Service URL** (URL du service d’authentification unique SAML), collez l’**URL de connexion** que vous avez copiée à partir du portail Azure.

    b. Dans la zone de texte **SAML Entity ID** (ID d’entité SAML), collez la valeur **Identificateur Azure AD** que vous avez copiée dans le portail Azure.

    c. Dans la zone de texte **Sign-Out URL** (URL de déconnexion), collez la valeur du champ **URL de déconnexion** que vous avez copiée à partir du portail Azure.

    d. Ouvrez le **Certificat (base64)** téléchargé à partir du portail Azure dans le Bloc-notes et collez le contenu dans la zone de texte **SAML Signing Certificate - Base64** (Certificat de signature SAML - Base64).

    > [!NOTE]
    > Vous pouvez également charger le fichier **XML de métadonnées de fédération** en cliquant sur l’option **Choose File** (Choisir un fichier).

    e. Copiez la valeur de **Reply URL (Assertion Consumer Service URL)** (URL de réponse (URL Assertion Consumer Service)) et collez-la dans la zone de texte **URL de réponse** de la section **Configuration SAML de base** sur le portail Azure.

    f. Copiez la valeur du champ **Sign on URL** (URL de connexion), puis collez-la dans la zone de texte **URL de connexion** de la section **Configuration SAML de base** du portail Azure.

    g. Cliquez sur **Enregistrer**.

### <a name="create-askyourteam-test-user"></a>Créer un utilisateur de test AskYourTeam

1. Dans une autre fenêtre du navigateur web, connectez-vous au site web AskYourTeam en tant qu’administrateur.

1. Cliquez sur **My Organization**.

    ![Capture d’écran montrant le lien My Organization où vous démarrez cette tâche.](./media/askyourteam-tutorial/user1.png)

1. Cliquez sur **Users** (Utilisateurs) et sélectionnez **New User** (Nouvel utilisateur).

    ![Capture d’écran montrant le lien Users avec New User.](./media/askyourteam-tutorial/user2.png)

1. Dans la section **New User**, procédez comme suit :

    ![Capture d’écran montrant la section New User, où vous pouvez entrer des informations sur l’utilisateur.](./media/askyourteam-tutorial/user3.png)

    1. Dans la zone de texte **First name** (Prénom), entrez le prénom de l’utilisateur.

    1. Dans la zone de texte **Last name**, tapez le nom de l’utilisateur.

    1. Dans la zone de texte **Email** (E-mail), entrez l’adresse e-mail de l’utilisateur, par exemple B.Simon@contoso.com.

    1. Sélectionnez le **rôle** pour l’utilisateur en fonction des exigences de votre organisation.

    1. Cliquez sur **Enregistrer**.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes.

#### <a name="sp-initiated"></a>Lancée par le fournisseur de services :

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion à AskYourTeam, où vous pouvez lancer le flux de connexion.

* Accédez directement à l’URL de connexion à AskYourTeam pour y lancer le processus de connexion.

#### <a name="idp-initiated"></a>Lancée par le fournisseur d’identité :

* Dans le portail Azure, cliquez sur **Tester cette application**. Vous êtes alors automatiquement connecté à l’instance d’AskYourTeam pour laquelle vous avez configuré l’authentification unique

Vous pouvez aussi utiliser Mes applications de Microsoft pour tester l’application dans n’importe quel mode. Si, quand vous cliquez sur la vignette AskYourTeam dans Mes applications, le mode Fournisseur de services est configuré, vous êtes redirigé vers la page de connexion de l’application pour lancer le flux de connexion ; s’il s’agit du mode Fournisseur d’identité, vous êtes automatiquement connecté à l’instance d’AskYourTeam pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez configuré AskYourTeam, vous pouvez appliquer le contrôle de session, qui protège l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
