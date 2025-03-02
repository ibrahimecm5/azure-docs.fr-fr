---
title: 'Tutoriel : Intégration de l’authentification unique (SSO) Azure Active Directory avec la plateforme d’accès distant de sécurité confiance zéro | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et la plateforme d’accès distant confiance zéro Banyan Security.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/08/2021
ms.author: jeedes
ms.openlocfilehash: fcb12035d97448793b5f074c6deb54fa685fabdb
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132288625"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-banyan-security-zero-trust-remote-access-platform"></a>Tutoriel : Intégration de l’authentification unique (SSO) Azure Active Directory à la plateforme d’accès distant confiance zéro Banyan Security

Dans ce tutoriel, vous allez découvrir comment intégrer la plateforme d’accès distant confiance zéro Banyan Security à Azure Active Directory (Azure AD). Quand vous intégrez la plateforme d’accès distant confiance zéro Banyan Security à Azure AD, vous pouvez :

* contrôler dans Azure AD qui a accès à la plateforme d’accès distant confiance zéro Banyan Security,
* permettre aux utilisateurs de se connecter automatiquement à la plateforme d’accès distant confiance zéro Banyan Security avec leurs comptes Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/)
* Un abonnement à la plateforme d’accès distante confiance zéro Banyan Security pour lequel l’authentification unique (SSO) est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* La plateforme d’accès distant confiance zéro Banyan Security prend en charge l’authentification unique lancée par le **fournisseur de services et le fournisseur d’identité**.
* La plateforme d’accès distant confiance zéro Banyan Security prend en charge l’approvisionnement des utilisateurs **juste-à-temps**.

## <a name="add-banyan-security-zero-trust-remote-access-platform-from-the-gallery"></a>Ajouter la plateforme d’accès distant confiance zéro Banyan Security à partir de la galerie

Pour configurer l’intégration de la plateforme d’accès distant confiance zéro Banyan Security à Azure AD, vous devez ajouter la plateforme d’accès distant confiance zéro Banyan Security à votre liste d’applications SaaS managées, à partir de la galerie.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, dans la zone de recherche, tapez **Plateforme d’accès distant confiance zéro Banyan Security**.
1. Sélectionnez **Plateforme d’accès distant confiance zéro Banyan Security** dans le panneau Résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-banyan-security-zero-trust-remote-access-platform"></a>Configurer et tester l’authentification unique Azure AD pour la plateforme d’accès distant confiance zéro Banyan Security

Configurez et testez l’authentification unique Azure AD avec la plateforme d’accès distant confiance zéro Banyan Security à l’aide d’un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir une relation entre un utilisateur Azure AD et l’utilisateur associé dans la plateforme d’accès distant confiance zéro Banyan Security.

Pour configurer et tester l’authentification unique Azure AD avec la plateforme d’accès distant confiance zéro Banyan Security, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique de la plateforme d’accès distant confiance zéro Banyan Security](#configure-banyan-security-zero-trust-remote-access-platform-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test pour la plateforme d’accès distant confiance zéro Banyan Security](#create-banyan-security-zero-trust-remote-access-platform-test-user)** afin de disposer dans la plateforme d’accès distant confiance zéro Banyan Security d’un équivalent de B.Simon dans la plateforme d’accès distant confiance zéro Banyan Security lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le Portail Azure, accédez à la page d’intégration de l’application **Plateforme d’accès distant confiance zéro Banyan Security**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, si vous souhaitez configurer l’application en mode lancé par le **fournisseur d’identité**, effectuez les étapes suivantes :

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://net.banyanops.com/api/v1/sso?orgname=<YOUR_ORG_NAME>`

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://net.banyanops.com/api/v1/sso?orgname=<YOUR_ORG_NAME>`

1. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://net.banyanops.com/api/v1/sso?orgname=<YOUR_ORG_NAME>`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL de réponse et l’URL de connexion réels. Pour obtenir ces valeurs, contactez l’[équipe du support technique de la plateforme d’accès distant confiance zéro Banyan Security](mailto:support@banyansecurity.io). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur le bouton Copier pour copier l’**URL des métadonnées de fédération d’application**, puis enregistrez-la sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/copy-metadataurl.png)

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

Dans cette section, vous allez permettre à B.Simon d’utiliser l’authentification unique Azure en lui octroyant l’accès à la plateforme d’accès distant confiance zéro Banyan Security.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Plateforme d’accès distant confiance zéro Banyan Security**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-banyan-security-zero-trust-remote-access-platform-sso"></a>Configurer l’authentification unique de la plateforme d’accès distant confiance zéro Banyan Security

1. Connectez-vous au site web Plateforme d’accès distant confiance zéro Banyan Security en tant qu’administrateur.

1. Accédez à **Admin Settings (Paramètres d’administration) -> Admin Sign-on (Connexion de l’administrateur)** .

1. Effectuez les étapes suivantes dans la page **Sign-on Settings** (Paramètres de connexion).

    ![Capture d’écran des paramètres de connexion.](./media/banyan-command-center-tutorial/configuration.png)

    a. Pour **Sign-On Method** (Méthode de connexion) sélectionnez **Single Sign On - SAML 2.0** (Authentification unique - SAML 2.0) dans la liste déroulante.

    b. Copiez la valeur du champ **IDP Issuer** (émetteur du fournisseur d’identité), collez-la dans la zone de texte **Identificateur Azure AD** dans la section Configuration SAML de base du portail Azure.

    c. Collez la valeur de l’**URL des métadonnées de fédération d’application** dans la zone de texte **IDP Metadata URL** (URL des métadonnées du fournisseur d’identité).

    d. Cliquez sur le bouton **Update** (Mettre à jour).

### <a name="create-banyan-security-zero-trust-remote-access-platform-test-user"></a>Créer un utilisateur de test de la plateforme d’accès distant confiance zéro Banyan Security

Dans cette section, un utilisateur appelé Britta Simon est créé dans la plateforme d’accès distant confiance zéro Banyan Security. La plateforme d’accès distant confiance zéro Banyan Security prend en charge l’approvisionnement des utilisateurs juste-à-temps, une option activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas encore d’utilisateur dans la plateforme d’accès distant confiance zéro Banyan Security, il en est créé un après l’authentification.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

#### <a name="sp-initiated"></a>Lancée par le fournisseur de services :

* Cliquez sur **Tester cette application** dans le portail Azure. Cette opération redirige vers l’URL de connexion Plateforme d’accès distant confiance zéro Banyan Security, où vous pouvez lancer le flux de connexion.  

* Accédez directement à l’URL de connexion de la plateforme d’accès distant confiance zéro Banyan Security pour y lancer le flux de connexion.

#### <a name="idp-initiated"></a>Lancée par le fournisseur d’identité :

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors connecté automatiquement à la plateforme d’accès distant confiance zéro Banyan Security pour laquelle vous avez configuré l’authentification unique. 

Vous pouvez aussi utiliser Mes applications de Microsoft pour tester l’application dans n’importe quel mode. Si, quand vous cliquez sur la mosaïque Plateforme d’accès distant confiance zéro Banyan Security dans Mes applications, le mode Fournisseur de services est configuré, vous êtes redirigé vers la page de connexion de l’application pour lancer le flux de connexion ; s’il s’agit du mode Fournisseur d’identité, vous êtes automatiquement connecté à la plateforme d’accès distant confiance zéro Banyan Security pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré la plateforme d’accès distant confiance zéro Banyan Security, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
