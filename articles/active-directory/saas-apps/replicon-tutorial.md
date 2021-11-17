---
title: 'Tutoriel : Intégration d’Azure Active Directory à Replicon | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Replicon.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/13/2021
ms.author: jeedes
ms.openlocfilehash: a65906e84ac85e8c4e5282eb426d2d700bbf2c66
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132333748"
---
# <a name="tutorial-integrate-replicon-with-azure-active-directory"></a>Tutoriel : Intégrer Replicon à Azure Active Directory

Dans ce didacticiel, vous allez apprendre à intégrer Replicon à Azure Active Directory (Azure AD). Quand vous intégrez Replicon à Azure AD, vous pouvez :

* Dans Azure AD, contrôlez qui a accès à Replicon.
* Permettre à vos utilisateurs de se connecter automatiquement à Replicon avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Replicon pour lequel l’authentification unique est activée.

> [!NOTE]
> Cette intégration peut également être utilisée à partir de l’environnement cloud US Government Azure AD. Cette application est disponible dans la Galerie d’applications cloud US Government Azure AD et peut être configurée de la même façon que dans le cloud public.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test. 

* Replicon prend en charge l’authentification unique initiée par le **SP**.

## <a name="add-replicon-from-the-gallery"></a>Ajouter Replicon à partir de la galerie

Pour configurer l’intégration de Replicon à Azure AD, vous devez ajouter Replicon à partir de la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Replicon** dans la zone de recherche.
1. Sélectionnez **Replicon** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-replicon"></a>Configurer et tester l’authentification unique Azure AD pour Replicon

Configurez et testez l’authentification unique Azure AD avec Replicon pour un utilisateur de test nommé **B. Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Replicon associé.

Pour configurer et tester l’authentification unique Azure AD avec Replicon, procédez comme suit :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Replicon](#configure-replicon-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test Replicon](#create-replicon-test-user)** pour avoir un équivalent de B. Simon dans Replicon lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **Replicon**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon pour **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la page **Configuration SAML de base**, effectuez les étapes suivantes :

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://global.replicon.com/!/saml2/<client name>/sp-sso/post`.

    b. Dans la zone de texte **Identificateur**, tapez une URL en utilisant le format suivant : `https://global.replicon.com/!/saml2/<client name>`

    c. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://global.replicon.com/!/saml2/<client name>/sso/post`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion, l’identificateur et l’URL de réponse réels. Pour obtenir ces valeurs, contactez [l’équipe de support technique Replicon](https://www.replicon.com/customerzone/contact-support). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. Cliquez sur l’icône de crayon pour **Certificat de signature SAML** pour modifier les paramètres.

    ![Algorithme de signature](common/signing-algorithm.png)

    1. Dans **Option de signature**, sélectionnez **Signer l’assertion SAML**.

    1. Sélectionnez **SHA-256** comme **Algorithme de signature**.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **XML de métadonnées de fédération** et sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

   ![Lien Téléchargement de certificat](common/metadataxml.png)

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

Dans cette section, vous allez créer un utilisateur de test appelé B. Simon dans le portail Azure.

1. Dans le volet gauche du Portail Azure, sélectionnez **Azure Active Directory**, **Utilisateurs**, puis **Tous les utilisateurs**.
1. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.
1. Dans les propriétés **Utilisateur**, effectuez les étapes suivantes :
   1. Dans le champ **Nom**, entrez `B.Simon`.  
   1. Dans le champ **Nom de l’utilisateur**, entrez username@companydomain.extension. Par exemple : `BrittaSimon@contoso.com`.
   1. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.
   1. Cliquez sur **Créer**.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser B. Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Replicon.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Replicon**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-replicon-sso"></a>Configurer l’authentification unique de Replicon

1. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Replicon en tant qu’administrateur.

2. Procédez comme suit pour configurer SAML 2.0 :

    ![Activer l’authentification SAML](./media/replicon-tutorial/authentication.png "Activer l’authentification SAML")

    a. Pour afficher la boîte de dialogue **EnableSAML Authentication2**, ajoutez ce qui suit à votre URL, après la clé de votre entreprise : `/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2`

    1. Voici une illustration du schéma de l’URL complète : `https://na2.replicon.com/<YourCompanyKey>/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2`

   b. Cliquez sur **+** pour développer la section **v20Configuration**.

   c. Cliquez sur **+** pour développer la section **metaDataConfiguration**.

   d. Sélectionnez **SHA256** pour xmlSignatureAlgorithm

   e. Cliquez sur **Choose File** pour sélectionner votre fichier XML de métadonnées de fournisseur d’identité, puis cliquez sur **Submit**.

### <a name="create-replicon-test-user"></a>Créer un utilisateur de test Replicon

L’objectif de cette section est de créer un utilisateur appelé B. Simon dans Replicon.

**Si vous avez besoin de créer un utilisateur manuellement, procédez comme suit :**

1. Dans une fenêtre de navigateur web, connectez-vous à votre site d’entreprise Replicon en tant qu’administrateur.

2. Accédez à **Administration \> Users**.

    ![Utilisateurs](./media/replicon-tutorial/administration.png "Utilisateurs")

3. Cliquez sur **+Add User**.

    ![Ajouter un utilisateur](./media/replicon-tutorial/user.png "Ajouter un utilisateur")

4. Dans la section **User Profile** , procédez comme suit :

    ![Profil utilisateur](./media/replicon-tutorial/profile.png "User Profile")

    a. Dans la zone de texte **ID de connexion**, tapez l’adresse de messagerie Azure AD de l’utilisateur Azure AD que vous souhaitez approvisionner, comme `B.Simon@contoso.com`.

    > [!NOTE]
    > Le nom de connexion doit correspondre à l’adresse e-mail utilisateur dans Azure AD

    b. Pour **Authentication Type**, sélectionnez **SSO**.

    c. Définissez l’ID de jeu d’authentification sur la même valeur que le nom de connexion (l’adresse de messagerie Azure AD de l’utilisateur)

    d. Dans la zone de texte **Department** , tapez le département de l’utilisateur.

    e. Pour **Employee Type**, sélectionnez **Administrator**.

    f. Cliquez sur **Save User Profile**.

> [!NOTE]
> Vous pouvez utiliser n’importe quel outil ou API de création de compte utilisateur, fourni par Replicon, pour provisionner des comptes utilisateur Azure AD.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion à Replicon à partir de laquelle vous pouvez lancer le flux de connexion. 

* Accédez directement à l’URL de connexion Replicon pour lancer le flux de connexion.

* Vous pouvez utiliser Mes applications de Microsoft. Un clic sur la vignette Replicon dans Mes applications vous redirige vers l’URL de connexion à Replicon. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré Replicon, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
