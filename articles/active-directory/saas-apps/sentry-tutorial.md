---
title: 'Tutoriel : Intégration de l’authentification unique (SSO) Azure Active Directory à Sentry | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Sentry.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/23/2020
ms.author: jeedes
ms.openlocfilehash: 6aa1d82982203a3d2c057fc0c4190dfbc7e60e71
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132279877"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sentry"></a>Tutoriel : Intégration de l’authentification unique (SSO) Azure Active Directory à Sentry

Dans ce tutoriel, vous allez apprendre à intégrer Sentry à Azure Active Directory (Azure AD). Quand vous intégrez Sentry à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Sentry.
* Permettre à vos utilisateurs de se connecter automatiquement à Sentry avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Sentry pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* Sentry prend en charge l’authentification unique (SSO) lancée par **le fournisseur de services et le fournisseur d’identité**.
* Sentry prend en charge l’attribution d’utilisateurs **juste-à-temps**.

## <a name="adding-sentry-from-the-gallery"></a>Ajout de Sentry à partir de la galerie

Pour configurer l’intégration de Sentry à Azure AD, vous devez ajouter Sentry, disponible dans la galerie, à votre liste d’applications SaaS managées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Sentry** dans la zone de recherche.
1. Sélectionnez **Sentry** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.


## <a name="configure-and-test-azure-ad-sso-for-sentry"></a>Configurer et tester l’authentification unique Azure AD pour Sentry

Configurez et testez l’authentification unique Azure AD avec Sentry pour un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir une relation entre un utilisateur Azure AD et l’utilisateur associé dans Sentry.

Pour configurer et tester l’authentification unique Azure AD avec Sentry, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    * **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    * **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Sentry](#configure-sentry-sso)** pour configurer les paramètres de l’authentification unique côté application.
    * **[Créer un utilisateur de test Sentry](#create-sentry-test-user)** pour avoir un équivalent de B.Simon dans Sentry lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Sur le portail Azure, dans la page d’intégration de l’application **Sentry**, recherchez la section **Gérer**, puis sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de modification/stylet de **Configuration SAML de base** pour modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, si vous souhaitez configurer l’application en mode Initié par le **fournisseur d’identité**, entrez les valeurs pour les champs suivants :

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://sentry.io/saml/metadata/<ORGANIZATION_SLUG>/`

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://sentry.io/saml/acs/<ORGANIZATION_SLUG>/`

1. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://sentry.io/organizations/<ORGANIZATION_SLUG>/`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez ces valeurs à jour avec les valeurs réelles de l’identificateur, de l’URL de réponse et de l’URL de connexion. Pour plus d’informations sur la recherche de ces valeurs, consultez la [documentation de Sentry](https://docs.sentry.io/product/accounts/sso/azure-sso/#installation). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. Sur la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur l’icône Copier pour copier la valeur **URL des métadonnées de l’application** et enregistrez-la sur votre ordinateur.

   ![Lien Téléchargement de certificat](common/copy-metadataurl.png)
    
### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

Dans cette section, vous allez créer un utilisateur de test appelé B.Simon dans le portail Azure.

1. Dans le volet gauche du Portail Azure, sélectionnez **Azure Active Directory**, **Utilisateurs**, puis **Tous les utilisateurs**.
1. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.
1. Dans les propriétés **Utilisateur**, effectuez les étapes suivantes :
   1. Dans le champ **Nom**, entrez `B.Simon`.  
   1. Dans le champ **Nom de l’utilisateur**, entrez username@companydomain.extension. Par exemple : `B.Simon@contoso.com`.
   1. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.
   1. Cliquez sur **Créer**.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Sentry.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Sentry**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-sentry-sso"></a>Configurer l’authentification unique Sentry

Pour configurer l’authentification unique côté **Sentry**, accédez à **Org Settings** (Paramètres de l’organisation) > **Auth** (Authentification) [ou accédez à `https://sentry.io/settings/<YOUR_ORG_SLUG>/auth/`] et sélectionnez **Configure** (Configurer) pour Active Directory. Collez l’URL des métadonnées de fédération de l’application provenant de votre configuration Azure SAML.

### <a name="create-sentry-test-user"></a>Créer un utilisateur de test Sentry

Dans cette section, un utilisateur appelé B.Simon est créé dans Sentry. Sentry prend en charge l’attribution d’utilisateurs juste-à-temps, option activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas encore d’utilisateur dans Sentry, il en est créé un après l’authentification.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

#### <a name="sp-initiated"></a>Lancée par le fournisseur de services :

1. Dans le portail Azure, sélectionnez **Tester cette application**. Vous êtes alors redirigé vers l’URL de connexion de Sentry, à partir de laquelle vous pouvez lancer le flux de connexion.  

1. Accédez directement à l’URL de connexion de Sentry pour initier le flux de connexion.

#### <a name="idp-initiated"></a>Lancée par le fournisseur d’identité :

* Dans le portail Azure, sélectionnez **Tester cette application**. Vous devez être connecté automatiquement à l’application Sentry pour laquelle vous avez configuré l’authentification unique. 

#### <a name="either-mode"></a>L’un ou l’autre mode :

Vous pouvez utiliser le portail Mes applications pour tester l’application dans n’importe quel mode. Quand vous cliquez sur la vignette Sentry dans le portail Mes applications, si le mode Fournisseur de services est configuré, vous êtes redirigé vers la page de connexion de l’application pour lancer le flux de connexion. Si le mode Fournisseur d’identité est configuré, vous êtes automatiquement connecté à l’application Sentry pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le portail Mes applications, consultez [Se connecter et démarrer des applications à partir du portail Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré Sentry, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
