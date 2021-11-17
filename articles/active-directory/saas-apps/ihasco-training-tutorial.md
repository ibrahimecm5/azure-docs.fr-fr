---
title: 'Tutoriel : Intégration de l’authentification unique Azure Active Directory à iHASCO Training | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et iHASCO Training.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/25/2021
ms.author: jeedes
ms.openlocfilehash: f1d5726df4f7e112529e6271813b9d2b732e63da
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132336028"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ihasco-training"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory à iHASCO Training

Dans ce tutoriel, vous allez apprendre à intégrer iHASCO Training à Azure Active Directory (Azure AD). Quand vous intégrez iHASCO Training à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à iHASCO Training.
* Permettre à vos utilisateurs de se connecter automatiquement à iHASCO Training avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement iHASCO Training pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* iHASCO Training prend en charge l’authentification unique initiée par le **fournisseur de services**.
* iHASCO Training prend en charge l’attribution d’utilisateurs **juste-à-temps**.


## <a name="adding-ihasco-training-from-the-gallery"></a>Ajouter iHASCO Training à partir de la galerie

Pour configurer l’intégration d’iHASCO Training dans Azure AD, vous devez ajouter iHASCO Training à partir de la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, saisissez **iHASCO Training** dans la zone de recherche.
1. Sélectionnez **iHASCO Training** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.


## <a name="configure-and-test-azure-ad-sso-for-ihasco-training"></a>Configurer et tester l’authentification unique Azure AD pour iHASCO Training

Configurez et testez l’authentification unique Azure AD avec iHASCO Training à l’aide d’un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur associé dans iHASCO Training.

Pour configurer et tester l’authentification unique Azure AD avec iHASCO Training, procédez comme suit :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique d’iHASCO Training](#configure-ihasco-training-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test iHASCO Training](#create-ihasco-training-test-user)** pour avoir un équivalent de B.Simon dans iHASCO Training lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **iHASCO Training**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, entrez les valeurs pour les champs suivants :

    a. Dans la zone de texte **Identificateur**, tapez une URL en utilisant le format suivant : `https://authentication.ihasco.co.uk/saml2/<ID>/metadata`

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://authentication.ihasco.co.uk/saml2/<ID>/acs`
    
    c. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://app.ihasco.co.uk/<ID>`.

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL de réponse et l’URL de connexion réels. Pour obtenir ces valeurs, contactez l’[équipe du support technique d’iHASCO Training](mailto:support@ihasco.co.uk). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **Certificat (en base64)** , puis sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

1. Dans la section **Configurer iHASCO Training**, copiez les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à iHASCO Training.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **iHASCO Training**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-ihasco-training-sso"></a>Configurer l’authentification unique d’iHASCO Training

1. Connectez-vous à votre site web iHASCO Training en tant qu’administrateur.

1. Cliquez sur **Settings** (Paramètres) dans la barre de navigation en haut à droite, faites défiler jusqu’à la vignette **ADVANCED** (Avancé) et cliquez sur **Configure Single Sign On** (Configurer l’authentification unique).

    ![Capture d’écran du bouton de l’authentification unique d’iHASCO Training.](./media/ihasco-training-tutorial/settings.png)

1. Dans l’onglet **IDENTITY PROVIDERS** (Fournisseurs d’identité), cliquez sur **Add provider** (Ajouter un fournisseur) et sélectionnez **SAML2**.

    ![Capture d’écran de l’onglet IDENTITY PROVIDERS d’iHASCO Training.](./media/ihasco-training-tutorial/add-provider.png)

1. Sur la page **Single Sign On / New SAML2** (Authentification unique/nouvelle connexion SAML2), procédez comme suit :

    ![Capture d’écran de l’authentification unique d’iHASCO Training.](./media/ihasco-training-tutorial/single-sign-on.png)

    a. Sous **GENERAL** (Général), entrez une **description** pour identifier cette configuration.

    b. Sous **IDENTITY PROVIDER DETAILS** (Détails du fournisseur d’identité), dans la zone de texte **Single Sign-on URL** (URL de l’authentification unique), collez l’**URL de connexion** que vous avez copiée à partir du portail Azure.

    c. Dans la zone de texte **Single Logout URL** (URL de Single Logout), collez l’**URL de déconnexion** que vous avez copiée sur le portail Azure.

    d. Dans la zone de texte **ID d'entité**, collez l'**Identificateur** que vous avez copié à partir du portail Azure.

    e. Ouvrez le **certificat (Base64)** téléchargé à partir du portail Azure dans Bloc-notes et collez le contenu dans la zone de texte **X509 (Public) Certificate** (Certificat [public] X509).

    f. Sous **USER ATTRIBUTE MAPPING** (Mappage des attributs utilisateur), dans la zone **Email address** (Adresse e-mail), entrez la valeur, par exemple `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    g. Dans la zone de texte **First name** (Prénom), entrez la valeur, par exemple `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.

    h. Dans la zone de texte **Last name** (Nom), entrez la valeur, par exemple `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`.

    i. Cliquez sur **Enregistrer**.

    j. Cliquez sur **Enable now** (Activer) après le rechargement de la page.

1. Cliquez sur **Security** (Sécurité) dans le volet de navigation gauche et sélectionnez **Single Sign On provider** (Fournisseur d’authentification unique) comme méthode **d’inscription** et votre **configuration Azure AD** comme **Selected provider** (Fournisseur sélectionné).

    ![Capture d’écran du volet Sécurité d’iHASCO Training.](./media/ihasco-training-tutorial/security.png)

1. Cliquez sur **Save changes**.

### <a name="create-ihasco-training-test-user"></a>Créer un utilisateur de test iHASCO Training

Dans cette section, un utilisateur appelé Britta Simon est créé dans iHASCO Training. iHASCO Training prend en charge l’attribution d’utilisateurs juste-à-temps, option qui est activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. Grâce à cela, s’il n’existe pas encore d’utilisateur dans iHASCO Training, il en est créé un après l’authentification.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion d’iHASCO Training, à partir de laquelle vous pouvez lancer le flux de connexion. 

* Accédez directement à l’URL de connexion d’iHASCO Training pour initier le flux de connexion.

* Vous pouvez utiliser Mes applications de Microsoft. Quand vous cliquez sur la vignette iHASCO Training dans Mes applications, vous êtes redirigé vers l’URL de connexion d’iHASCO Training. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).


## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez configuré iHASCO Training, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
