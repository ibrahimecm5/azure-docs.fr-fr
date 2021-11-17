---
title: 'Tutoriel : Intégration de l’authentification unique Azure Active Directory à Michigan Data Hub Single Sign-On | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Michigan Data Hub Single Sign-On.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/30/2021
ms.author: jeedes
ms.openlocfilehash: f6fc113177f4f32898143b3126a853bc6d53bbc2
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132280206"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-michigan-data-hub-single-sign-on"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory à Michigan Data Hub Single Sign-On

Dans ce tutoriel, vous allez découvrir comment intégrer Michigan Data Hub Single Sign-On à Azure Active Directory (Azure AD). Quand vous intégrez Michigan Data Hub Single Sign-On à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Michigan Data Hub Single Sign-On.
* Permettre aux utilisateurs de se connecter automatiquement à Michigan Data Hub Single Sign-On avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Michigan Data Hub Single Sign-On pour lequel l’authentification unique est activée

> [!NOTE]
> Cette intégration peut également être utilisée à partir de l’environnement cloud US Government Azure AD. Cette application est disponible dans la Galerie d’applications cloud US Government Azure AD et peut être configurée de la même façon que dans le cloud public.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* L’authentification unique Michigan Data Hub prend en charge l’authentification unique lancée par le **fournisseur de services**.

> [!NOTE]
> L’identificateur de cette application étant une valeur de chaîne fixe, une seule instance peut être configurée dans un locataire.

## <a name="add-michigan-data-hub-single-sign-on-from-the-gallery"></a>Ajouter L’authentification unique Michigan Data Hub à partir de la galerie

Pour configurer l’intégration de Michigan Data Hub Single Sign-On à Azure AD, vous devez ajouter Michigan Data Hub Single Sign-On, disponible dans la galerie, à votre liste d’applications SaaS managées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Michigan Data Hub Single Sign-On** dans la zone de recherche.
1. Sélectionnez **Michigan Data Hub Single Sign-On** dans le volet des résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-michigan-data-hub-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD pour Michigan Data Hub Single Sign-On

Configurez et testez l’authentification unique Azure AD avec Michigan Data Hub Single Sign-On en utilisant un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir une liaison entre un utilisateur Azure AD et l’utilisateur Michigan Data Hub Single Sign-On associé.

Pour configurer et tester l’authentification unique Azure AD avec L’authentification unique Michigan Data Hub, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Michigan Data Hub Single Sign-On](#configure-michigan-data-hub-single-sign-on-sso)** pour configurer les paramètres d’authentification unique côté application.
    1. **[Créer un utilisateur de test Michigan Data Hub Single Sign-On](#create-michigan-data-hub-single-sign-on-test-user)** pour avoir, dans Michigan Data Hub Single Sign-On, un équivalent de B.Simon lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le Portail Azure, dans la page d’intégration de l’application **Authentification unique Michigan Data Hub**, recherchez la section **Gérer**, puis sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, effectuez l’étape suivante :

    Dans la zone de texte **URL de connexion**, tapez l’URL : `https://launchpad.midatahub.org`

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Michigan Data Hub Single Sign-On.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Michigan Data Hub Single Sign-On**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-michigan-data-hub-single-sign-on-sso"></a>Configurer l’authentification unique Michigan Data Hub Single Sign-On

Pour configurer l’authentification unique côté **Michigan Data Hub Single Sign-On**, vous devez envoyer l’**URL des métadonnées de fédération d’application** à l’[équipe du support technique de Michigan Data Hub Single Sign-On](mailto:support@midatahub.org). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

### <a name="create-michigan-data-hub-single-sign-on-test-user"></a>Créer un utilisateur de test Michigan Data Hub Single Sign-On

Dans cette section, vous allez créer un utilisateur appelé B.Simon dans Michigan Data Hub Single Sign-On. Travaillez en collaboration avec l’[équipe de support technique de Michigan Data Hub Single Sign-On](mailto:support@midatahub.org) pour ajouter les utilisateurs à la plateforme Michigan Data Hub Single Sign-On. Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion à Authentification unique Michigan Data Hub, à partir de laquelle vous pouvez lancer le flux de connexion. 

* Accédez directement à l’URL de connexion Authentification unique Michigan Data Hub pour lancer le flux de connexion.

* Vous pouvez utiliser Mes applications de Microsoft. Quand vous cliquez sur la mosaïque Authentification unique Michigan Data Hub dans Mes applications, vous êtes redirigé vers l’URL de connexion Authentification unique Michigan Data Hub. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré Michigan Data Hub Single Sign-On, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
