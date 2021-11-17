---
title: 'Tutoriel : Intégration de l’authentification unique Azure Active Directory à InVision | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et InVision.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/01/2021
ms.author: jeedes
ms.openlocfilehash: df92ea58544a35c157ba91ad95412d557eabdb05
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132313926"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-invision"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory à InVision

Dans ce tutoriel, vous allez apprendre à intégrer InVision à Azure Active Directory (Azure AD). Quand vous intégrez InVision à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à InVision.
* Autoriser les utilisateurs à se connecter automatiquement à InVision avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement InVision pour lequel l’authentification unique (SSO) est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* InVision prend en charge l’authentification unique lancée par **le fournisseur de services et par le fournisseur d’identité**.
* InVision prend en charge l’[attribution automatisée d’utilisateurs](invision-provisioning-tutorial.md).

## <a name="adding-invision-from-the-gallery"></a>Ajout d’InVision à partir de la galerie

Pour configurer l’intégration d’InVision à Azure AD, vous devez ajouter InVision à votre liste d'applications SaaS managées, à partir de la galerie.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **InVision** dans la zone de recherche.
1. Sélectionnez **InVision** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-invision"></a>Configurer et tester l’authentification unique Azure AD pour InVision

Configurez et testez l’authentification unique Azure AD avec InVision pour un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur InVision associé.

Pour configurer et tester l’authentification unique Azure AD avec InVision, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique InVision](#configure-invision-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test InVision](#create-invision-test-user)** pour avoir un équivalent de B.Simon dans InVision lié à la représentation Azure AD associée.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **InVision**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, si vous souhaitez configurer l’application en mode Initié par le **fournisseur d’identité**, entrez les valeurs pour les champs suivants :

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<SUBDOMAIN>.invisionapp.com`

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://<SUBDOMAIN>.invisionapp.com//sso/auth`

1. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<SUBDOMAIN>.invisionapp.com`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL de réponse et l’URL de connexion réels. Pour obtenir ces valeurs, contactez l’[équipe du support technique InVision](mailto:support@invisionapp.com). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **Certificat (en base64)** , puis sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

1. Dans la section **Configurer InVision**, copiez la ou les URL appropriées selon vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à InVision.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **InVision**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.

1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-invision-sso"></a>Configurer l’authentification unique InVision

1. Pour automatiser la configuration dans InVision, vous devez installer l’**extension de navigateur My Apps Secure Sign-in** en cliquant sur **Installer l’extension**.

    ![Extension My apps](common/install-myappssecure-extension.png)

2. Une fois l’extension ajoutée au navigateur, un clic sur **Configurer InVision** vous redirige vers l’application InVision. À partir de là, indiquez les informations d’identification de l’administrateur pour vous connecter à InVision. Cette extension de navigateur configure automatiquement l’application et automatise les étapes 3 à 6.

    ![Configuration](common/setup-sso.png)

3. Si vous souhaitez configurer InVision manuellement, dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise InVision en tant qu’administrateur.

1. Cliquez sur **Team** (Équipe), puis sélectionnez **Settings** (Paramètres).

    ![Capture d’écran montrant l’onglet Team avec l’élément Settings, tous deux sélectionnés.](./media/invision-tutorial/config1.png)

1. Faites défiler la page jusqu’à **Single sign-on** (Authentification unique) et cliquez sur **Change** (Changer).

    ![Capture d’écran montrant le bouton Change pour Single Sign-On.](./media/invision-tutorial/config3.png)

1. Dans la page **Single sign-on** (Authentification unique), effectuez les étapes suivantes :

    ![Capture d’écran montrant la page Single Sign-On dans laquelle vous pouvez entrer les valeurs de cette étape.](./media/invision-tutorial/config4.png)

    a. Changez **Require SSO for every member of < account name >** (Exiger l’authentification unique pour chaque membre de < nom du compte >) sur **On** (Activé).

    b. Dans la zone de texte **Name**, entrez le nom, par exemple `azureadsso`.

    c. Entrez la valeur de l’URL de connexion dans la zone de texte **Sign-in URL**.

    d. Dans la zone de texte **Sign-Out URL** (URL de déconnexion), collez la valeur du champ **URL de déconnexion** que vous avez copiée à partir du portail Azure.

    e. Dans la zone de texte **SAML Certificate** (Certificat SAML), ouvrez le fichier **Certificat (en base64)** téléchargé du portail Azure dans le Bloc-notes, copiez son contenu et collez-le dans la zone de texte SAML Certificate.

    f. Dans la zone de texte **Name** **ID Format**, utilisez `urn:oasis:names:tc:SAML:1.1:nameid-format:Unspecified`.

    g. Sélectionnez **SHA-256** dans la liste déroulante **HASH Algorithm** (Algorithme de hachage).

    h. Dans **SSO Button Label**, entrez un nom comme étiquette du bouton SSO.

    i. Activez **Allow Just-in-Time provisioning** (Autoriser le provisionnement juste-à-temps).

    j. Cliquez sur **Update**.

### <a name="create-invision-test-user"></a>Créer un utilisateur de test InVision

1. Dans une autre fenêtre du navigateur web, connectez-vous au site InVision en tant qu’administrateur.

1. Cliquez sur **Team** (Équipe), puis sélectionnez **People** (Personnes).

    ![Capture d’écran montrant l’onglet Team avec l’élément People, tous deux sélectionnés.](./media/invision-tutorial/config2.png)

1. Cliquez sur l’**ICÔNE +** pour ajouter un nouvel utilisateur.

    ![Capture d’écran montrant l’icône + pour ajouter un utilisateur.](./media/invision-tutorial/user1.png)

1. Entrez l’adresse e-mail de l’utilisateur, puis cliquez sur **Next** (Suivant).

    ![Capture d’écran montrant la boîte de dialogue Invite to dans laquelle vous pouvez entrer des adresses.](./media/invision-tutorial/user2.png)

1. Une fois l’adresse e-mail vérifiée, cliquez sur **Invite** (Inviter).

    ![Capture d’écran montrant la boîte de dialogue Invite, dans laquelle vous pouvez sélectionner Invite pour continuer.](./media/invision-tutorial/user3.png)

> [!NOTE]
> InVision prend également en charge l’attribution automatique d’utilisateurs. Des informations supplémentaires sur la configuration de cette fonctionnalité sont disponibles [ici](./invision-provisioning-tutorial.md).

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes.

#### <a name="sp-initiated"></a>Lancée par le fournisseur de services :

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion InVision, à partir de laquelle vous pouvez initier le flux de connexion.

* Accédez directement à l’URL de connexion InVision pour lancer le flux de connexion.

#### <a name="idp-initiated"></a>Lancée par le fournisseur d’identité :

* Dans le portail Azure, cliquez sur **Tester cette application**. Vous êtes alors automatiquement connecté à l’instance d’InVision pour laquelle vous avez configuré l’authentification unique

Vous pouvez aussi utiliser Mes applications de Microsoft pour tester l’application dans n’importe quel mode. Si, quand vous cliquez sur la vignette InVision dans Mes applications, le mode Fournisseur de services est configuré, vous êtes redirigé vers la page de connexion de l’application pour lancer le flux de connexion ; s’il s’agit du mode Fournisseur d’identité, vous êtes automatiquement connecté à l’instance d’InVision pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré InVision, vous pouvez appliquer le contrôle de session, qui protège l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
