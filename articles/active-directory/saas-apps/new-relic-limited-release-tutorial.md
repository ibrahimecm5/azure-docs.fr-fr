---
title: 'Tutoriel : Intégration de l’authentification unique Azure Active Directory à New Relic | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et New Relic.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/31/2021
ms.author: jeedes
ms.openlocfilehash: 67bbd26e4c565a5c7372be81fdf2398afe53c277
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132307216"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-new-relic"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory à New Relic

Dans ce tutoriel, vous allez découvrir comment intégrer New Relic à Azure Active Directory (Azure AD). Quand vous intégrez New Relic à Azure AD, vous pouvez :

* Contrôler qui dans Azure AD a accès à New Relic.
* Permettre à vos utilisateurs de se connecter automatiquement à New Relic avec leur compte Azure AD.
* gérer vos comptes à un emplacement central : le portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Une organisation New Relic sur le [compte/modèle d’utilisateur New Relic One](https://docs.newrelic.com/docs/accounts/original-accounts-billing/original-product-based-pricing/overview-changes-pricing-user-model/#user-models) et sur l’édition Pro ou Enterprise. Pour plus d’informations, consultez [Configuration New Relic requise](https://docs.newrelic.com/docs/accounts/accounts-billing/new-relic-one-user-management/authentication-domains-saml-sso-scim-more).

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* New Relic prend en charge l’authentification unique lancée par le fournisseur de services ou le fournisseur d’identité.

* New Relic prend en charge l’[attribution automatisée d’utilisateurs](new-relic-by-organization-provisioning-tutorial.md).

## <a name="add-new-relic-from-the-gallery"></a>Ajouter New Relic depuis la galerie

Pour configurer l’intégration de New Relic à Azure AD, vous devez ajouter **New Relic (By Organization)** à votre liste d’applications SaaS managées à partir de la galerie.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire, ou avec un compte personnel Microsoft.
1. Sélectionnez le service **Azure Active Directory**.
1. Sélectionnez **Applications d’entreprise** > **Nouvelle application**.
1. Dans la page **Parcourir la galerie Azure AD**, tapez **New Relic (By Organization)** dans la zone de recherche.
1. Sélectionnez **New Relic (By Organization)** dans les résultats, puis sélectionnez **Créer**. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-new-relic"></a>Configurer et tester l’authentification unique Azure AD pour New Relic

Configurez et testez l’authentification unique Azure AD avec New Relic en utilisant un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur associé dans New Relic.

Pour configurer et tester l’authentification unique Azure AD avec New Relic :

1. [Configurer l’authentification unique Azure AD](#configure-azure-ad-sso) pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
   1. [Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user) pour tester l’authentification unique Azure AD avec B.Simon.
   1. [Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user) pour permettre à B.Simon d’utiliser l’authentification unique Azure AD.
1. [Configurer l’authentification unique New Relic](#configure-new-relic-sso) pour configurer les paramètres d’authentification unique côté New Relic.
   1. [Créer un utilisateur de test New Relic](#create-a-new-relic-test-user) pour obtenir un équivalent de B.Simon dans New Relic lié à l’utilisateur Azure AD.
1. [Tester l’authentification unique](#test-sso) pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **New Relic by Organization**, puis recherchez la section **Gérer**. Ensuite, sélectionnez **Authentification unique**.

1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.

1. Dans la page **Configurer l’authentification unique avec SAML**, sélectionnez l’icône de crayon pour **Configuration SAML de base** afin de modifier les paramètres.

   ![Capture d’écran de Configurer l’authentification unique avec SAML, où l’icône de crayon est mise en évidence.](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, entrez des valeurs pour **Identificateur** et **URL de réponse**.

   * Récupérez ces valeurs à partir de l’[interface utilisateur du domaine d’authentification New Relic](https://docs.newrelic.com/docs/accounts/accounts-billing/new-relic-one-user-management/authentication-domains-saml-sso-scim-more/#ui). À partir de là : 
      1. Si vous avez plusieurs domaines d’authentification, choisissez celui auquel vous souhaitez que l’authentification unique Azure AD se connecte. La plupart des entreprises n’ont qu’un seul domaine d’authentification appelé **Default** (Par défaut). S’il n’existe qu’un seul domaine d’authentification, vous n’avez rien à sélectionner.
      1. Dans la section **Authentication** (Authentification), **Assertion consumer URL** (URL du consommateur d’assertion) contient la valeur à utiliser pour **Reply URL** (URL de réponse).
      1. Dans la section **Authentication** (Authentification), **Our entity ID** (Notre ID d’entité) contient la valeur à utiliser pour **Identifier** (Identificateur).

1. Dans la section **User Attributes & Claims** (Attributs et revendications utilisateur), vérifiez que **Unique User Identifier** (Identificateur utilisateur unique) est mappé à un champ qui contient l’adresse e-mail utilisée dans New Relic.

   * Le champ par défaut **user.userprincipalname** fonctionne pour vous si ses valeurs correspondent aux adresses e-mail New Relic.
   * Il se peut que le champ **user.mail** fonctionne mieux si **user.userprincipalname** n’est pas l’adresse e-mail New Relic.

1. Dans la section **Certificat de signature SAML**, copiez la valeur de **URL des métadonnées de fédération d’application**, puis enregistrez-la pour une utilisation ultérieure.

1. Dans la section **Configurer New Relic by Organization**, copiez la valeur de **URL de connexion**, puis enregistrez-la pour une utilisation ultérieure.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à New Relic.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **New Relic**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-new-relic-sso"></a>Configurer l’authentification unique pour New Relic

Pour configurer l’authentification unique dans New Relic, suivez ces étapes. 

1. [Connectez-vous](https://login.newrelic.com/) à New Relic.

1. Accédez à l’[interface utilisateur du domaine d’authentification](https://docs.newrelic.com/docs/accounts/accounts-billing/new-relic-one-user-management/authentication-domains-saml-sso-scim-more/#ui). 

1. Choisissez le domaine d’authentification auquel l’authentification unique Azure AD doit se connecter (si vous avez plusieurs domaines d’authentification). La plupart des entreprises n’ont qu’un seul domaine d’authentification appelé **Default** (Par défaut). S’il n’existe qu’un seul domaine d’authentification, vous n’avez rien à sélectionner.

1. Dans la section **Authentication** (Authentification), sélectionnez **Configure** (Configurer).

   1. Pour **Source of SAML metadata** (Source des métadonnées SAML), entrez la valeur que vous avez enregistrée à partir du champ **URL des métadonnées de fédération d’application** dans Azure AD.

   1. Pour **SSO target URL** (URL cible SSO), entrez la valeur que vous avez enregistrée à partir du champ **URL de connexion** dans Azure AD.

   1. Après avoir vérifié que les paramètres sont bien configurés du côté d’Azure AD et de New Relic, sélectionnez **Save** (Enregistrer). Si la configuration est incorrecte des deux côtés, vos utilisateurs ne pourront pas se connecter à New Relic.

### <a name="create-a-new-relic-test-user"></a>Créer un utilisateur de test New Relic

Dans cette section, vous allez créer un utilisateur appelé B.Simon dans New Relic.

1. [Connectez-vous](https://login.newrelic.com/) à New Relic.

1. Accédez à l’interface utilisateur de [**gestion des utilisateurs**](https://docs.newrelic.com/docs/accounts/accounts-billing/new-relic-one-user-management/add-manage-users-groups-roles/#where).

1. Sélectionnez **Ajouter un utilisateur**.

   1. Pour **Name** (Nom), entrez **B.Simon**.
   
   1. Pour **Email** (E-mail), entrez la valeur qui sera envoyée par l’authentification unique Azure AD.
   
   1. Choisissez un type d’utilisateur dans **Type** et un groupe d’utilisateurs dans **Group** pour l’utilisateur. Pour un utilisateur de test, **Basic user** (Utilisateur de base) pour Type et **User** (Utilisateur) pour Group sont des choix raisonnables.
   
   1. Pour enregistrer l’utilisateur, sélectionnez **Add User** (Ajouter un utilisateur).

> [!NOTE]
> New Relic prend également en charge l’attribution automatique d’utilisateurs. Vous trouverez plus d’informations sur la configuration de cette fonctionnalité [ici](./new-relic-by-organization-provisioning-tutorial.md).

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

#### <a name="sp-initiated"></a>Lancée par le fournisseur de services :

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion à New Relic, d’où vous pouvez lancer le flux de connexion.  

* Accédez directement à l’URL de connexion à New Relic et lancez le flux de connexion.

#### <a name="idp-initiated"></a>Lancée par le fournisseur d’identité :

* Cliquez sur **Tester cette application** dans le portail Azure ; vous devez être connecté automatiquement à l’instance de New Relic pour laquelle vous avez configuré l’authentification unique 

Vous pouvez aussi utiliser Mes applications de Microsoft pour tester l’application dans n’importe quel mode. Si, quand vous cliquez sur la vignette New Relic dans Mes applications, le mode Fournisseur de services est configuré, vous êtes redirigé vers la page de connexion de l’application pour lancer le flux de connexion ; s’il s’agit du mode Fournisseur d’identité, vous êtes automatiquement connecté à l’instance de New Relic pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez terminé, vous pouvez consulter l’[interface utilisateur de **gestion des utilisateurs**](https://docs.newrelic.com/docs/accounts/accounts-billing/new-relic-one-user-management/add-manage-users-groups-roles/#where) pour vérifier que vos utilisateurs ont été ajoutés à New Relic. 

Ensuite, vous voudrez probablement affecter vos utilisateurs à des rôles ou des comptes Relic spécifiques. Pour en savoir plus à ce sujet, consultez [Concepts de gestion des utilisateurs](https://docs.newrelic.com/docs/accounts/accounts-billing/new-relic-one-user-management/add-manage-users-groups-roles/#understand-concepts). 

Dans l’interface utilisateur du domaine d’authentification New Relic, vous pouvez configurer d’[autres paramètres](https://docs.newrelic.com/docs/accounts/accounts-billing/new-relic-one-user-management/authentication-domains-saml-sso-scim-more/#session-mgmt), comme le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
