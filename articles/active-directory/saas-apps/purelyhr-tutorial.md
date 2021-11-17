---
title: 'Tutoriel : Intégration de l’authentification unique Azure Active Directory à PurelyHR | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et PurelyHR.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/26/2021
ms.author: jeedes
ms.openlocfilehash: 8a4305a838fbb74c980af82ff66575d8ef5682ac
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132329606"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-purelyhr"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory à PurelyHR

Dans ce tutoriel, vous allez apprendre à intégrer PurelyHR avec Azure Active Directory (Azure AD). Quand vous intégrez PurelyHR à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à PurelyHR.
* Permettre à vos utilisateurs de se connecter automatiquement à PurelyHR avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement PurelyHR pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* PurelyHR prend en charge l’authentification unique initiée par le **fournisseur de services et le fournisseur d’identité**.
* PurelyHR prend en charge le provisionnement d’utilisateurs **juste-à-temps**.

> [!NOTE]
> L’identificateur de cette application étant une valeur de chaîne fixe, une seule instance peut être configurée dans un locataire.

## <a name="add-purelyhr-from-the-gallery"></a>Ajouter PurelyHR à partir de la galerie

Pour configurer l’intégration de PurelyHR avec Azure AD, vous devez ajouter PurelyHR à partir de la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **PurelyHR** dans la zone de recherche.
1. Sélectionnez **PurelyHR** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-purelyhr"></a>Configurer et tester l’authentification unique Azure AD pour PurelyHR

Configurez et testez l’authentification unique Azure AD avec PurelyHR à l’aide d’un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur PurelyHR associé.

Pour configurer et tester l’authentification unique Azure AD avec PurelyHR, procédez comme suit :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique PurelyHR](#configure-purelyhr-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test PurelyHR](#create-purelyhr-test-user)** pour avoir un équivalent de B.Simon dans PurelyHR lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **PurelyHR**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, si vous souhaitez configurer l’application en mode lancé par le **fournisseur d’identité**, effectuez l’étape suivante :

    Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://<COMPANY_ID>.purelyhr.com/sso-consume`

1. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<COMPANY_ID>.purelyhr.com/sso-initiate`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de réponse et l’URL de connexion réelles. Pour obtenir ces valeurs, contactez l’[équipe de support technique PurelyHR](https://support.purelyhr.com/). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **Certificat (en base64)** , puis sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

1. Dans la section **Configurer PurelyHR**, copiez la ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en accordant l’accès à PurelyHR.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **PurelyHR**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-purelyhr-sso"></a>Configurer l’authentification unique PurelyHR

1. Pour automatiser la configuration dans PurelyHR, vous devez installer l’**extension de navigateur My Apps Secure Sign-in** en cliquant sur **Installer l’extension**.

    ![Extension My apps](common/install-myappssecure-extension.png)

1. Après l’ajout de l’extension au navigateur, cliquez sur **Configurer PurelyHR** pour être dirigé vers l’application PurelyHR. À partir de là, indiquez les informations d’identification de l’administrateur pour vous connecter à PurelyHR. Cette extension de navigateur configure automatiquement l’application et automatise les étapes 3 à 5.

    ![Configuration](common/setup-sso.png)

1. Si vous souhaitez configurer manuellement PurelyHR, ouvrez une nouvelle fenêtre de navigateur web, connectez-vous à votre site d’entreprise PurelyHR en tant qu’administrateur et effectuez les étapes suivantes :

1. Ouvrez le **tableau de bord** dans la barre d’outils et cliquez sur **SSO Settings** (Paramètres d’authentification unique).

1. Collez les valeurs dans les zones comme décrit ci-dessous :

    ![Configure Single Sign-On](./media/purelyhr-tutorial/dashboard.png)    

    a. Ouvrez le **Certificat (Base64)** téléchargé à partir du portail Azure dans le bloc-notes et copiez la valeur du certificat. Collez la valeur copiée dans la zone **Certificat x509**.

    b. Dans la zone **Idp Issuer URL** (URL d’émetteur IdP), collez l’**identificateur Azure AD** copié à partir du portail Azure.

    c. Dans la zone **Idp Endpoint URL** (URL du point de terminaison Idp), collez la valeur de l’**URL de connexion** copiée à partir du portail Azure. 

    d. Cochez la case **Auto-Create Users** (Créer automatiquement des utilisateurs) pour activer l’approvisionnement automatique des utilisateurs dans PurelyHR.

    e. Cliquez sur **Enregistrer les modifications** pour enregistrer les paramètres.

### <a name="create-purelyhr-test-user"></a>Créer un utilisateur de test PurelyHR

Cette étape n’est généralement pas nécessaire car l’application prend en charge l’approvisionnement immédiat de l’utilisateur. Si l’approvisionnement automatique de l’utilisateur n’est pas activé, la création manuelle de l’utilisateur peut être effectuée comme décrit ci-dessous.

Connectez-vous au site de votre entreprise Velpic SAML en tant qu’administrateur et effectuez les étapes suivantes :

1. Cliquez sur l’onglet Gérer et accédez à la section Utilisateurs, puis cliquez sur le bouton Nouveau pour ajouter des utilisateurs.

    ![ajouter un utilisateur](./media/purelyhr-tutorial/new-user.png)

2. Dans la boîte de dialogue **Create New User**, procédez comme suit.

    ![utilisateur](./media/purelyhr-tutorial/create-user.png)

    a. Dans la zone de texte **Prénom**, tapez le prénom de B.

    b. Dans la zone de texte **Last Name** (Nom), tapez Simon.

    c. Dans la zone de texte **Nom d’utilisateur**, tapez le nom d’utilisateur de B.Simon.

    d. Dans la zone de texte **Email**, tapez l’adresse e-mail du compte B.Simon@contoso.com.

    e. Les autres informations sont facultatives et vous pouvez les renseigner si nécessaire.

    f. Cliquez sur **ENREGISTRER**.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

#### <a name="sp-initiated"></a>Lancée par le fournisseur de services :

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion à PurelyHR à partir de laquelle vous pouvez lancer le flux de connexion.  

* Accédez directement à l’URL de connexion à PurelyHR, puis initiez le flux de connexion à partir de cet emplacement.

#### <a name="idp-initiated"></a>Lancée par le fournisseur d’identité :

* Cliquez sur **Tester cette application** dans le portail Azure. Vous devez alors être connecté automatiquement à l’instance de PurelyHR pour laquelle vous avez configuré l’authentification unique. 

Vous pouvez aussi utiliser Mes applications de Microsoft pour tester l’application dans n’importe quel mode. Quand vous cliquez sur la vignette PurelyHR dans Mes applications, si le mode Fournisseur de services est configuré, vous êtes redirigé vers la page de connexion de l’application pour initier le flux de connexion. Si le mode Fournisseur d’identité est configuré, vous êtes automatiquement connecté à l’instance de PurelyHR pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez configuré PurelyHR, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
