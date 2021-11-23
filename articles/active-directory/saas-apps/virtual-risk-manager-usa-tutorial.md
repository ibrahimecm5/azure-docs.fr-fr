---
title: 'Tutoriel : Intégration de l’authentification SSO Azure AD à Virtual Risk Manager - USA'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Virtual Risk Manager - USA.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/15/2021
ms.author: jeedes
ms.openlocfilehash: a0bfc4a2a7394c1ea22921759f8ea3454e408ce2
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132557348"
---
# <a name="tutorial-azure-ad-sso-integration-with-virtual-risk-manager---usa"></a>Tutoriel : Intégration de l’authentification SSO Azure AD à Virtual Risk Manager - USA

Dans ce tutoriel, vous découvrez comment intégrer Virtual Risk Manager - USA à Azure Active Directory (Azure AD). Quand vous intégrez Virtual Risk Manager - USA à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Virtual Risk Manager - USA.
* Permettre à vos utilisateurs de se connecter automatiquement à Virtual Risk Manager - USA avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Virtual Risk Manager - USA avec l’authentification unique (SSO) activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* Virtual Risk Manager - USA prend en charge l’authentification SSO lancée par le **fournisseur d’identité**.

* Virtual Risk Manager - USA prend en charge le provisionnement d’utilisateurs **juste-à-temps**.

## <a name="add-virtual-risk-manager---usa-from-the-gallery"></a>Ajouter Virtual Risk Manager - USA à partir de la galerie

Pour configurer l’intégration de Virtual Risk Manager - USA à Azure AD, vous devez ajouter Virtual Risk Manager - USA à votre liste d’applications SaaS managées à partir de la galerie.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Virtual Risk Manager - USA** dans la zone de recherche.
1. Sélectionnez **Virtual Risk Manager - USA** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-virtual-risk-manager---usa"></a>Configurer et tester l’authentification SSO Azure AD pour Virtual Risk Manager - USA

Configurez et testez l’authentification SSO Azure AD avec Virtual Risk Manager - USA en utilisant un utilisateur de test nommé **B.Simon**. Pour que SSO fonctionne, vous devez établir une relation entre un utilisateur Azure AD et l’utilisateur associé dans Virtual Risk Manager - USA.

Pour configurer et tester l’authentification SSO Azure AD avec Virtual Risk Manager - USA, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification SSO de Virtual Risk Manager - USA](#configure-virtual-risk-manager---usa-sso)** pour configurer les paramètres d’authentification unique côté application.
    1. **[Créer un utilisateur de test Virtual Risk Manager - USA](#create-virtual-risk-manager---usa-test-user)** pour avoir, dans Virtual Risk Manager - USA, un équivalent de B.Simon lié à la représentation Azure AD associée.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **Virtual Risk Manager - USA**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, l’application est préconfigurée en mode Lancement par le **fournisseur d’identité** et les URL nécessaires sont déjà préremplies avec Azure. L’utilisateur doit enregistrer la configuration en cliquant sur le bouton **Enregistrer**.

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

Dans cette section, vous autorisez B.Simon à utiliser l’authentification unique Azure en accordant l’accès à Virtual Risk Manager - USA.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Virtual Risk Manager - USA**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-virtual-risk-manager---usa-sso"></a>Configurer SSO pour Virtual Risk Manager - USA

Pour configurer l’authentification unique côté **Virtual Risk Manager - USA**, vous devez envoyer l’**URL des métadonnées de fédération de l’application** à l’[équipe de support Virtual Risk Manager - USA](mailto:globalsupport@edriving.com). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

### <a name="create-virtual-risk-manager---usa-test-user"></a>Créer un utilisateur de test Virtual Risk Manager - USA

Dans cette section, un utilisateur appelé Britta Simon est créé dans Virtual Risk Manager - USA. Virtual Risk Manager - USA prend en charge le provisionnement d’utilisateurs juste-à-temps, option activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas encore d’utilisateur dans Virtual Risk Manager - USA, le système en créé un après l’authentification.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes.

* Cliquez sur Tester cette application dans le portail Azure. Vous êtes alors connecté automatiquement à l’instance de Virtual Risk Manager - USA pour laquelle vous avez configuré SSO.

* Vous pouvez utiliser Mes applications de Microsoft. Quand vous cliquez sur la vignette Virtual Risk Manager - USA dans Mes applications, vous êtes alors automatiquement connecté à l’instance de Virtual Risk Manager - USA pour laquelle vous avez configuré SSO. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré Virtual Risk Manager - USA, vous pouvez appliquer le contrôle de session qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).