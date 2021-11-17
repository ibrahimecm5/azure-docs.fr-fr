---
title: 'Tutoriel : Intégration de l’authentification unique Azure Active Directory à Helpshift | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Helpshift.
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
ms.openlocfilehash: 0934ac39c12fe61a5c2d9135999df9e7a6e793f9
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132317836"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-helpshift"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory à Helpshift

Dans ce tutoriel, vous allez découvrir comment intégrer Helpshift à Azure AD (Azure Active Directory). Quand vous intégrez Helpshift à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Helpshift.
* Permettre à vos utilisateurs de se connecter automatiquement à Helpshift avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Helpshift pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* Helpshift prend en charge l’authentification unique lancée par le **fournisseur de services et le fournisseur d’identité**.

## <a name="add-helpshift-from-the-gallery"></a>Ajouter Helpshift à partir de la galerie

Pour configurer l’intégration de Helpshift à Azure AD, vous devez ajouter Helpshift à partir de la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Helpshift** dans la zone de recherche.
1. Sélectionnez **Helpshift** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-helpshift"></a>Configurer et tester l’authentification unique Azure AD pour Helpshift

Configurez et testez l’authentification unique Azure AD avec Helpshift à l’aide d’un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Helpshift associé.

Pour configurer et tester l’authentification unique Azure AD avec Helpshift, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Helpshift](#configure-helpshift-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test Helpshift](#create-helpshift-test-user)** pour avoir un équivalent de B.Simon dans Helpshift lié à la représentation Azure AD associée.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Sur le portail Azure, dans la page d’intégration de l’application **Helpshift**, recherchez la section **Gérer**, puis sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, si vous souhaitez configurer l’application en mode lancé par le **fournisseur d’identité**, effectuez les étapes suivantes :

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<YOUR_DOMAIN>.helpshift.com/`

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://<YOUR_DOMAIN>.helpshift.com/login/saml/acs/`

1. Cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes si vous souhaitez configurer l’application en mode lancé par le **fournisseur de services** :

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<YOUR_DOMAIN>.helpshift.com/login/saml/idp-login/`.

    b. Dans la zone de texte **État de relais**, entrez une URL en utilisant le modèle suivant : `https://<YOUR_DOMAIN>.helpshift.com/`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL de réponse, l’URL de connexion et l’État de relais exacts. Pour obtenir ces valeurs, contactez l’[équipe de support client de Helpshift](mailto:support@helpshift.com). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **Certificat (en base64)** , puis sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

1. Dans la section **Configurer Helpshift**, copiez la ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Helpshift.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Helpshift**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-helpshift-sso"></a>Configurer l’authentification unique Helpshift

1. Dans un autre navigateur web, connectez-vous à votre application Helpshift en tant qu’administrateur.

1. Ouvrez le tableau de bord **Helpshift**, puis cliquez sur l’icône **Paramètres**.

    ![Capture d’écran montrant l’icône des paramètres Helpshift.](./media/helpshift-tutorial/dashboard.png)

1. Cliquez sur l’onglet **Integrations** et effectuez les étapes suivantes :

    ![Capture d’écran montrant l’onglet Integration où vous pouvez effectuer les étapes décrites.](./media/helpshift-tutorial/configuration.png)

    a. Activez **Single Sign-On(SAML – SSO)** .

    b. Sélectionnez **Azure Active Directory** comme **Identity Provider(IDP)** .

    c. Dans la zone de texte **SAML 2.0 Endpoint URL** (URL de point de terminaison SAML 2.0), collez l’**URL de connexion** que vous avez copiée à partir du portail Azure.

    d. Ouvrez le fichier de **Certificat (base64)** téléchargé dans le Bloc-notes, copiez le contenu du fichier (sans les lignes « —–BEGIN CERTIFICATE—– » et « —–END CERTIFICATE—– »), puis collez-le dans la zone de texte **X.509 Certificate**.

    e. Dans la zone de texte **Issuer URL** (URL de l’émetteur), collez l’**Identificateur Azure AD** que vous avez copié à partir du portail Azure.

    f. Cliquez sur **APPLY CHANGES** (Appliquer les modifications).

### <a name="create-helpshift-test-user"></a>Créer un utilisateur de test Helpshift

Dans cette section, vous allez créer un utilisateur appelé B.Simon dans Helpshift. Collaborez avec l’[équipe de support technique Helpshift](mailto:support@helpshift.com) pour ajouter des utilisateurs à la plateforme Helpshift. Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

#### <a name="sp-initiated"></a>Lancée par le fournisseur de services :

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion à Helpshift à partir de laquelle vous pouvez lancer le flux de connexion.  

* Accédez directement à l’URL de connexion à Helpshift et lancez le flux de connexion.

#### <a name="idp-initiated"></a>Lancée par le fournisseur d’identité :

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors automatiquement connecté à l’instance de Helpshift pour laquelle vous avez configuré l’authentification unique. 

Vous pouvez aussi utiliser Mes applications de Microsoft pour tester l’application dans n’importe quel mode. Si, quand vous cliquez sur la vignette Helpshift dans Mes applications, le mode Fournisseur de services est configuré, vous êtes redirigé vers la page de connexion de l’application pour lancer le flux de connexion ; s’il s’agit du mode Fournisseur d’identité, vous êtes automatiquement connecté à l’instance de Helpshift pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré Helpshift, vous pouvez appliquer le contrôle de session, qui protège en temps réel contre l’exfiltration et l’infiltration des données sensibles de votre organisation. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
