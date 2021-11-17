---
title: 'Tutoriel : Intégration de l’authentification unique Azure Active Directory à Kronos | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Kronos.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/28/2020
ms.author: jeedes
ms.openlocfilehash: d6b272bc03151dc4a10bd3eec9434e7df3bfb3f2
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132291066"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-kronos"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory à Kronos

Dans ce tutoriel, vous allez découvrir comment intégrer Kronos à Azure Active Directory (Azure AD). Quand vous intégrez Kronos à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Kronos.
* Permettre à vos utilisateurs de se connecter automatiquement à Kronos avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.


## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Kronos pour lequel l’authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* Kronos prend en charge l’authentification unique initiée par le **fournisseur d’identité**

## <a name="adding-kronos-from-the-gallery"></a>Ajout de Kronos à partir de la galerie

Pour configurer l’intégration de Kronos avec Azure AD, vous devez ajouter Kronos à partir de la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Kronos** dans la zone de recherche.
1. Sélectionnez **Kronos** dans le volet des résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-kronos"></a>Configurer et tester l’authentification unique Azure AD pour Kronos

Configurez et testez l’authentification unique Azure AD avec Kronos pour un utilisateur de test nommé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Kronos associé.

Pour configurer et tester l’authentification unique Azure AD avec Kronos, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Kronos](#configure-kronos-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test Kronos](#create-kronos-test-user)** pour avoir un équivalent de B.Simon dans Kronos lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, dans la page d’intégration de l’application **Kronos**, recherchez la section **Gérer**, puis sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon pour **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la page **Configurer l’authentification unique avec SAML**, entrez les valeurs pour les champs suivants :

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<company name>.kronos.net/`

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://<company name>.kronos.net/wfc/navigator/logonWithUID`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur et l’URL de réponse réels. Pour obtenir ces valeurs, contactez l’[équipe de support technique Kronos ](https://www.kronos.in/contact/en-in/form). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. L’application Kronos attend les assertions SAML dans un format spécifique. Configurez les revendications suivantes pour cette application. Vous pouvez gérer les valeurs de ces attributs à partir de la section **Attributs utilisateur** sur la page d’intégration des applications. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur le bouton **Modifier** pour ouvrir la boîte de dialogue **Attributs utilisateur**.

    ![Capture d’écran montrant User Attributes avec l’icône Edit sélectionnée.](common/edit-attribute.png)

1. Dans la section **Revendications des utilisateurs** de la boîte de dialogue **Attributs utilisateur**, configurez le jeton SAML comme sur l’image ci-dessus et procédez comme suit :

    a. Cliquez sur l’**icône Modifier** pour ouvrir la boîte de dialogue **Gérer les revendications des utilisateurs**.

    ![Capture d’écran montrant User Attributes & Claims avec l’icône Edit sélectionnée.](./media/kronos-tutorial/tutorial_usermail.png)

    ![Capture d’écran montrant la boîte de dialogue Manage user claims où vous pouvez entrer les valeurs décrites.](./media/kronos-tutorial/tutorial_usermailedit.png)

    b. Dans la liste **Transformation**, sélectionnez **ExtractMailPrefix()** .

    c. Dans la liste **Paramètre 1**, sélectionnez **user.userprincipalname**.

    d. Cliquez sur **Enregistrer**.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **XML de métadonnées de fédération** et sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

1. Dans la section **Configurer Kronos**, copiez la ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Kronos.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Kronos**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-kronos-sso"></a>Configurer l’authentification unique Kronos

Pour configurer l’authentification unique côté **Kronos**, vous devez envoyer le fichier **XML des métadonnées de fédération** téléchargé et les URL appropriées copiées depuis le portail Azure à l’[équipe du support technique Kronos](https://www.kronos.in/contact/en-in/form). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

### <a name="create-kronos-test-user"></a>Créer un utilisateur de test Kronos

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans Kronos. Collaborez avec l’[équipe du support technique Kronos](https://www.kronos.in/contact/en-in/form) pour ajouter des utilisateurs dans la plateforme Kronos. Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes.

* Cliquez sur Tester cette application dans le portail Azure ; vous devez être connecté automatiquement à l’instance de Kronos pour laquelle vous avez configuré l’authentification unique.

* Vous pouvez utiliser Mes applications de Microsoft. Quand vous cliquez sur la vignette Kronos dans Mes applications, vous devez être connecté automatiquement à l’application Kronos pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré Kronos, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
