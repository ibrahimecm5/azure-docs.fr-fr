---
title: 'Tutoriel : Intégration d’Azure Active Directory à SAP Qualtrics | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et SAP Qualtrics.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/18/2021
ms.author: jeedes
ms.openlocfilehash: 7ebc4387dccd7a434602ab6ad8f9713f58300233
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132307077"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-qualtrics"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory à SAP Qualtrics

Dans ce tutoriel, vous allez découvrir comment intégrer SAP Qualtrics à Azure Active Directory (Azure AD). Quand vous intégrez SAP Qualtrics à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à SAP Qualtrics.
* Permettre à vos utilisateurs de se connecter automatiquement à SAP Qualtrics avec leur compte Azure AD
* gérer vos comptes à un emplacement central : le portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement SAP Qualtrics pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* SAP Qualtrics prend en charge l’authentification unique initiée par le **fournisseur de services** et le **fournisseur d’identité**.
* SAP Qualtrics prend en charge le provisionnement d’utilisateurs **Juste-à-temps**.

## <a name="add-sap-qualtrics-from-the-gallery"></a>Ajouter SAP Qualtrics à partir de la galerie

Pour configurer l’intégration de SAP Qualtrics dans Azure AD, vous devez ajouter SAP Qualtrics à partir de la galerie à votre liste d’applications SaaS managées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire, ou avec un compte personnel Microsoft.
1. Sélectionnez **Azure Active Directory** dans le volet de gauche.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **SAP Qualtrics** dans la zone de recherche.
1. Sélectionnez **SAP Qualtrics** dans les résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sap-qualtrics"></a>Configurer et tester l’authentification unique Azure AD pour SAP Qualtrics

Configurez et testez l’authentification unique Azure AD avec SAP Qualtrics à l’aide d’un utilisateur de test nommé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur SAP Qualtrics associé.

Pour configurer et tester l’authentification unique Azure AD avec SAP Qualtrics, effectuez les modules suivants :

1. [Configurer l’authentification unique Azure AD](#configure-azure-ad-sso) pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. [Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user) pour tester l’authentification unique Azure AD avec B.Simon.
    1. [Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user) pour permettre à B.Simon d’utiliser l’authentification unique Azure AD.
1. [Configurer l’authentification unique SAP Qualtrics](#configure-sap-qualtrics-sso) pour configurer les paramètres de l’authentification unique côté application
    1. [Créer un utilisateur de test SAP Qualtrics](#create-sap-qualtrics-test-user) pour avoir un équivalent de B.Simon dans SAP Qualtrics lié à la représentation Azure AD de l’utilisateur
1. [Tester l’authentification unique](#test-sso) pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Sur le portail Azure, dans la page d’intégration de l’application **SAP Qualtrics**, recherchez la section **Gérer**. Sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, sélectionnez l’icône de crayon pour **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la page **Configurer l’authentification unique avec SAML**, si vous souhaitez configurer l’application en mode initié par le **fournisseur d’identité**, entrez les valeurs pour les champs suivants :
    
    a. Dans la zone de texte **Identificateur**, saisissez une URL au format suivant :

    `https://< DATACENTER >.qualtrics.com`
   
    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant :

    `https://< DATACENTER >.qualtrics.com/login/v1/sso/saml2/default-sp`

    c. Dans la zone de texte **État de relais**, tapez une URL au format suivant :

    `https://< brandID >.< DATACENTER >.qualtrics.com`

1. Sélectionnez **Définir des URL supplémentaires**, puis effectuez l’étape suivante si vous voulez configurer l’application en mode initié par le **fournisseur de services** :

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant :

    `https://< brandID >.< DATACENTER >.qualtrics.com`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez-les à jour avec des valeurs réelles pour URL de connexion, Identificateur, URL de réponse et État de relais. Pour obtenir ces valeurs, contactez l’[équipe du support technique de Qualtrics](https://www.qualtrics.com/support/). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, sélectionnez l’icône de copie pour copier l’**URL des métadonnées de fédération d’application** et enregistrez-la sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

Dans cette section, vous allez créer un utilisateur de test appelé B. Simon sur le portail Azure.

1. Dans le volet gauche du portail Azure, sélectionnez **Azure Active Directory** > **Utilisateurs** > **Tous les utilisateurs**.
1. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.
1. Dans les propriétés **Utilisateur**, effectuez les étapes suivantes :
   1. Dans le champ **Nom**, entrez `B.Simon`.  
   1. Dans le champ **Nom de l’utilisateur**, entrez username@companydomain.extension. Par exemple : `B.Simon@contoso.com`.
   1. Cochez la case **Afficher le mot de passe** et notez le mot de passe.
   1. Sélectionnez **Create** (Créer).

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à SAP Qualtrics.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise** > **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **SAP Qualtrics**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**. Ensuite, dans la boîte de dialogue **Ajouter une attribution**, sélectionnez **Utilisateurs et groupes**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B.Simon** dans la liste des utilisateurs. Choisissez **Sélectionner** au bas de l’écran.
1. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste. Choisissez **Sélectionner** au bas de l’écran.
1. Dans la boîte de dialogue **Ajouter une attribution**, sélectionnez **Affecter**.

## <a name="configure-sap-qualtrics-sso"></a>Configurer l’authentification unique SAP Qualtrics

Pour configurer l’authentification unique côté SAP Qualtrics, envoyez l’**URL des métadonnées de fédération d’application** copiée à partir du portail Azure à l’[équipe du support technique SAP Qualtrics](https://www.qualtrics.com/support/). Celle-ci vérifiera que l’authentification unique SAML est configurée correctement des deux côtés.

### <a name="create-sap-qualtrics-test-user"></a>Créer un utilisateur de test SAP Qualtrics

SAP Qualtrics prend en charge le provisionnement d’utilisateurs juste-à-temps, qui est activé par défaut. Vous n’avez aucune opération supplémentaire à effectuer. S’il n’existe pas encore d’utilisateur dans SAP Qualtrics, il en est créé un après l’authentification.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

#### <a name="sp-initiated"></a>Lancée par le fournisseur de services :

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion à SAP Qualtrics, d’où vous pouvez lancer le flux de connexion.  

* Accédez directement à l’URL de connexion à SAP Qualtrics pour lancer le flux de connexion.

#### <a name="idp-initiated"></a>Lancée par le fournisseur d’identité :

* Sur le portail Azure, cliquez sur **Tester cette application**. Vous êtes alors automatiquement connecté à l’application SAP Qualtrics pour laquelle vous avez configuré l’authentification unique.

Vous pouvez aussi utiliser Mes applications de Microsoft pour tester l’application dans n’importe quel mode. Si, quand vous cliquez sur la vignette SAP Qualtrics dans Mes applications, le mode Fournisseur de services est configuré, vous êtes redirigé vers la page de connexion de l’application pour lancer le flux de connexion ; s’il s’agit du mode Fournisseur d’identité, vous êtes automatiquement connecté à l’application SAP Qualtrics pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré SAP Qualtrics, vous pouvez appliquer le contrôle de session qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. Pour plus d’informations, consultez [Découvrir comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
