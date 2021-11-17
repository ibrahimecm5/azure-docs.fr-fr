---
title: 'Tutoriel : Intégration d’Azure Active Directory à Fluxx Labs | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Fluxx Labs.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/20/2021
ms.author: jeedes
ms.openlocfilehash: 61d4db378abd9bb4b9284198473ed1d76947f32d
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132285673"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-fluxx-labs"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory à Fluxx Labs

Dans ce tutoriel, vous allez découvrir comment intégrer Fluxx Labs à Azure AD (Azure Active Directory). Quand vous intégrez Fluxx Labs à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Fluxx Labs.
* Permettre à vos utilisateurs de se connecter automatiquement à Fluxx Labs avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Fluxx Labs pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* Fluxx Labs prend en charge l’authentification unique lancée par le **fournisseur d’identité**.

## <a name="add-fluxx-labs-from-the-gallery"></a>Ajouter Fluxx Labs à partir de la galerie

Pour configurer l’intégration de Fluxx Labs à Azure AD, vous devez ajouter Fluxx Labs à partir de la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Fluxx Labs** dans la zone de recherche.
1. Sélectionnez **Fluxx Labs** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-fluxx-labs"></a>Configurer et tester l’authentification unique Azure AD pour Fluxx Labs

Configurez et testez l’authentification unique Azure AD avec Fluxx Labs à l’aide d’un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Fluxx Labs associé.

Pour configurer et tester l’authentification unique Azure AD avec Fluxx Labs, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Fluxx Labs](#configure-fluxx-labs-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test Fluxx Labs](#create-fluxx-labs-test-user)** pour avoir un équivalent de B.Simon dans Fluxx Labs lié à la représentation Azure AD associée.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, dans la page d’intégration de l’application **Fluxx Labs**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Sur la page **Configurer l’authentification unique avec SAML**, effectuez les étapes suivantes :

    a. Dans la zone de texte **Identificateur**, tapez une URL en utilisant un des modèles suivants :

    | Environnement | Modèle d’URL|
    |-------------|------------|
    | Production | `https://<subdomain>.fluxx.io` |
    | Pré-production | `https://<subdomain>.preprod.fluxxlabs.com`|

    b. Dans la zone de texte **URL de réponse** , tapez une URL en utilisant un des modèles suivants :

    | Environnement | Modèle d’URL|
    |-------------|------------|
    | Production | `https://<subdomain>.fluxx.io/auth/saml/callback` |
    | Pré-production | `https://<subdomain>.preprod.fluxxlabs.com/auth/saml/callback`|

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur et l’URL de réponse réels. Pour obtenir ces valeurs, contactez l’[équipe du support Fluxx Labs](https://fluxx.zendesk.com). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **Certificat (en base64)** , puis sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

1. Dans la section **Configurer Fluxx Labs**, copiez la ou les URL appropriées, en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Fluxx Labs.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Fluxx Labs**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-fluxx-labs-sso"></a>Configurer l’authentification unique Fluxx Labs

1. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Fluxx Labs en tant qu’administrateur.

2. Sélectionnez **Admin** sous la section **Paramètres**.

    ![Capture d’écran montrant la section « Settings » avec l’option « Admin » sélectionnée.](./media/fluxxlabs-tutorial/configure-1.png)

3. Dans le volet Administration, sélectionnez **Plug-ins** > **Intégrations**, puis **SAML SSO-(Disabled)** (Authentification unique SAML-(Désactivée))

    ![Capture d’écran montrant l’onglet « Integrations » avec l’option « SAML SSO-(Disabled) » sélectionnée.](./media/fluxxlabs-tutorial/configure-2.png)

4. Dans la section d’attributs, procédez comme suit :

    ![Capture d’écran montrant la section « Attributes » avec l’option « SAML SSO » activée, les valeurs entrées dans les champs et le bouton « Save » sélectionné.](./media/fluxxlabs-tutorial/configure-3.png)

    a. Cochez la case **SAML SSO** (Authentification unique SAML).

    b. Dans la zone de texte **Request Path** (Chemin d’accès à la demande), saisissez **/auth/saml**.

    c. Dans la zone de texte **Callback Path** (Chemin de rappel), saisissez **/auth/saml/callback**.

    d. Dans la zone de texte **URL Assertion Consumer Service (URL d’authentification unique)** , entrez la valeur **URL de réponse** que vous avez saisie dans le portail Azure.

    e. Dans la zone de texte **Audience (ID d’entité SP)** , entrez la valeur **Identifier** que vous avez saisie dans le portail Azure.

    f. Dans la zone de texte **Identity Provider SSO Target URL** (URL cible d’authentification unique du fournisseur d’identité), collez la valeur de l’**URL de connexion** que vous avez copiée dans le portail Azure.

    g. Ouvrez votre certificat codé en base 64 dans le Bloc-notes, copiez son contenu dans le Presse-papiers, puis collez-le dans la zone de texte **Identity provider certificate (Certificat du fournisseur d’identité)** .

    h. Dans la zone de texte **Format de l’identificateur de nom**, saisissez la valeur `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`.

    i. Cliquez sur **Enregistrer**.

    > [!NOTE]
    > Une fois le contenu enregistré, le champ apparaît vide pour des raisons de sécurité, mais la valeur est bien enregistrée dans la configuration.

### <a name="create-fluxx-labs-test-user"></a>Créer un utilisateur de test Fluxx Labs

Pour permettre aux utilisateurs Azure AD de se connecter à Fluxx Labs, vous devez les provisionner dans Fluxx Labs. Dans le cas de Fluxx Labs, l’approvisionnement se fait manuellement.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous à votre site d’entreprise Fluxx Labs en tant qu’administrateur.

2. Cliquez sur **l’icône** affichée ci-dessous.

    ![Capture d’écran montrant les options d’administrateur avec l’icône « Plus » sélectionnée sous « Your Dashboard is Empty ».](./media/fluxxlabs-tutorial/configure-6.png)

3. Dans le tableau de bord, cliquez sur l’icône affichée ci-dessous pour ouvrir la carte **New PEOPLE** (Nouvelles personnes).

    ![Capture d’écran montrant le menu « Contact Management » avec l’icône « Plus » en regard de « People » sélectionnée.](./media/fluxxlabs-tutorial/configure-4.png)

4. Dans la section **NEW PEOPLE** (NOUVELLES PERSONNES), procédez comme suit :

    ![Configuration de Fluxx Labs](./media/fluxxlabs-tutorial/configure-5.png)

    a. Fluxx Labs utilise une adresse e-mail comme identificateur unique pour les connexions avec authentification unique. Renseignez le champ **SSO UID** (UID SSO) avec l’adresse e-mail que l’utilisateur utilise pour la connexion avec authentification unique.

    b. Cliquez sur **Enregistrer**.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes.

* Cliquez sur Tester cette application dans le portail Azure. Vous devez être connecté automatiquement à l’instance de Fluxx Labs pour laquelle vous avez configuré l’authentification unique.

* Vous pouvez utiliser Mes applications de Microsoft. Quand vous cliquez sur la vignette Fluxx Labs dans Mes applications, vous devez être connecté automatiquement à l’application Fluxx Labs pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré Fluxx Labs, vous pouvez appliquer le contrôle de session, qui protège en temps réel contre l’exfiltration et l’infiltration des données sensibles de votre organisation. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
