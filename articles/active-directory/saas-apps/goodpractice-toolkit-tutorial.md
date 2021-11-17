---
title: 'Tutoriel : Intégration d’Azure Active Directory à Mind Tools Toolkit | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Mind Tools Toolkit.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/28/2021
ms.author: jeedes
ms.openlocfilehash: 73deb218b847fe51803f63e53055327dc8a022f8
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132285343"
---
# <a name="tutorial-azure-active-directory-integration-with-mind-tools-toolkit"></a>Tutoriel : Intégration d’Azure Active Directory à Mind Tools Toolkit

Dans ce tutoriel, vous allez apprendre à intégrer Mind Tools Toolkit à Azure Active Directory (Azure AD). Quand vous intégrez Mind Tools Toolkit à Azure AD, vous pouvez :

* Dans Azure AD, contrôlez qui a accès à Mind Tools Toolkit.
* Permettez à vos utilisateurs de se connecter automatiquement à Mind Tools Toolkit (authentification unique) avec leur compte Azure AD.
* gérer vos comptes à un emplacement central : le portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration de Mind Tools Toolkit à Azure AD, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Mind Tools Toolkit avec l’authentification unique (SSO) activé.

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Mind Tools Toolkit prend en charge l’authentification unique lancée par le fournisseur de services.
* Mind Tools Toolkit prend en charge l’attribution d’utilisateurs juste-à-temps.

> [!NOTE]
> L’identificateur de cette application étant une valeur de chaîne fixe, une seule instance peut être configurée dans un locataire.

## <a name="add-mind-tools-toolkit-from-the-gallery"></a>Ajouter Mind Tools Toolkit depuis la galerie

Pour configurer l’intégration de Mind Tools Toolkit dans Azure AD, vous devez ajouter Mind Tools Toolkit à votre liste d’applications SaaS managées à partir de la galerie.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire, ou avec un compte personnel Microsoft.
1. Dans le volet de navigation le plus à gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, saisissez **Mind Tools Toolkit** dans la zone de recherche.
1. Sélectionnez **Mind Tools Toolkit** dans les résultats de la recherche, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-mind-tools-toolkit"></a>Configurer et tester Azure AD SSO pour Mind Tools Toolkit

Configurez et testez Azure AD SSO avec Mind Tools Toolkit pour un utilisateur de test nommé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur associé dans Mind Tools Toolkit.

Pour configurer et tester Azure AD SSO avec Mind Tools Toolkit, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Mind Tools Toolkit](#configure-mind-tools-toolkit-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test Mind Tools Toolkit](#create-mind-tools-toolkit-test-user)** pour avoir un équivalent de B.Simon dans Mind Tools Toolkit lié à la représentation Azure AD associée.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **Mind Tools Toolkit**, recherchez la section **Gérer**, puis sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, dans la zone **URL de connexion**, entrez une URL au format `https://app.goodpractice.net/#/<subscriptionUrl>/s/<LOCATION_ID>`.

    > [!NOTE]
    > La valeur d’**URL de connexion** n’est pas réelle. Mettez à jour la valeur avec l’URL de connexion réelle. Pour obtenir cette valeur, contactez l’[équipe du support technique Mind Tools Toolkit](mailto:support@goodpractice.com).

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**. À droite de **XML de métadonnées de fédération**, sélectionnez **Télécharger** pour télécharger le texte XML et l’enregistrer sur votre ordinateur. Le contenu XML dépend des options que vous sélectionnez.

    ![Section Certificat de signature SAML, avec Télécharger mis en évidence en regard de XML de métadonnées de fédération](common/metadataxml.png)

1. Dans **Configurer Mind Tools Toolkit**, copiez les URL dont vous avez besoin parmi les suivantes.

    ![La section Configurer Mind Tools Toolkit, avec les URL de configuration mises en évidence](common/copy-configuration-urls.png)

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

Dans cette section, vous allez autoriser B. Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Mind Tools Toolkit.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Mind Tools Toolkit**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-mind-tools-toolkit-sso"></a>Configurer l’authentification unique Mind Tools Toolkit

Pour configurer l’authentification unique du côté de **Mind Tools Toolkit**, envoyez le **XML de métadonnées de fédération** téléchargé et les URL précédemment copiées à l’[équipe de support technique Mind Tools Toolkit](mailto:support@goodpractice.com). Celle-ci configure ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

### <a name="create-mind-tools-toolkit-test-user"></a>Créer un utilisateur de test Mind Tools Toolkit

Dans cette section, un utilisateur appelé B. Simon est créé dans Mind Tools Toolkit. Mind Tools Toolkit prend en charge le provisionnement juste-à-temps de l’utilisateur, une option activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas encore d’utilisateur dans Mind Tools Toolkit, un utilisateur est créé après l’authentification.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Cette opération redirige vers l’URL de connexion Mind Tools Toolkit où vous pouvez lancer le flux de connexion. 

* Accédez directement à l’URL de connexion Mind Tools Toolkit pour lancer le flux de connexion à partir de là.

* Vous pouvez utiliser Mes applications de Microsoft. Le fait de cliquer sur la vignette Mind Tools Toolkit dans Mes applications vous redirige vers l’URL de connexion à Mind Tools Toolkit. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré Mind Tools Toolkit, vous pouvez appliquer le contrôle de session, qui protège l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
