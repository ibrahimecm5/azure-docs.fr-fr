---
title: 'Tutoriel : Intégration de l’authentification unique Azure Active Directory à Exium | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Exium.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/26/2021
ms.author: jeedes
ms.openlocfilehash: 2449a8d289acb02f93f87be28c5561e4feb987d0
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132314227"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-exium"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory à Exium

Dans ce tutoriel, vous allez découvrir comment intégrer Exium à Azure AD (Azure Active Directory). Quand vous intégrez Exium à Azure AD, vous pouvez :

* Contrôler, dans Azure AD, qui a accès à Exium.
* Permettre à vos utilisateurs de se connecter automatiquement à Exium avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Exium pour lequel l’authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* Exium prend en charge l’authentification SSO lancée par le fournisseur de services (**SP**).
* Exium prend en charge l’[attribution automatique d’utilisateurs](exium-provisioning-tutorial.md).

## <a name="adding-exium-from-the-gallery"></a>Ajout d’Exium à partir de la galerie

Pour configurer l’intégration d’Exium à Azure AD, vous devez ajouter Exium à votre liste d’applications SaaS managées à partir de la galerie.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Exium** dans la zone de recherche.
1. Sélectionnez **Exium** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.


## <a name="configure-and-test-azure-ad-sso-for-exium"></a>Configurer et tester l’authentification unique Azure AD pour Exium

Configurez et testez l’authentification unique Azure AD avec Exium à l’aide d’un utilisateur de test nommé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Exium associé.

Pour configurer et tester l’authentification unique Azure AD avec Exium, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Exium](#configure-exium-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test Exium](#create-exium-test-user)** pour avoir un équivalent de B.Simon dans Exium, associé à sa représentation dans Azure AD.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **Exium**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, entrez les valeurs pour les champs suivants :

    a. Dans la zone de texte **Identificateur (ID d’entité)** , saisissez une URL au format suivant : `https://subapi.exium.net/saml/<WORKSPACE_ID>/metadata`
    
    b.  Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://subapi.exium.net/saml/<WORKSPACE_ID>/acs`

    c. Dans la zone de texte **URL de connexion**, tapez l’URL : `https://service.exium.net/sign-in`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur et l’URL de réponse réels. Pour obtenir ces valeurs, contactez l’[équipe du support technique Exium](mailto:support@exium.net). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en accordant l’accès à Exium.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Exium**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-exium-sso"></a>Configurer l’authentification unique Exium

1. Connectez-vous au site d’entreprise Exium en tant qu’administrateur.

1. Sous **Admin Console**, sélectionnez le panneau **Company Profile**.

    ![capture d’écran de la console d’administration (Admin Console)](./media/exium-tutorial/company-profile.png)

1. Dans **Profile**, cliquez sur **SSO Settings**, puis sur **Edit** pour modifier les paramètres d’authentification unique.

1. Dans la section **SSO Settings**, effectuez les étapes suivantes.

    ![capture d’écran de la section SSO Settings](./media/exium-tutorial/update.png)

    a. Dans la liste déroulante **SSO Type**, sélectionnez **AzureAD**.

    b. Collez la valeur **URL des métadonnées de fédération d’application** dans le champ **SAML 2.0 IDP Metadata URL**.

    c. Copiez la valeur du champ **SAML 2.0 SSO URL**, puis collez-la dans la zone de texte **URL de réponse** de la section **Configuration SAML de base** du portail Azure.

    d. Copiez la valeur du champ **SAML 2.0 SP Entity ID**, puis collez-la dans la zone de texte **Identificateur** de la section **Configuration SAML de base** du portail Azure.  

    e. Cliquez sur **Mettre à jour**.

### <a name="create-exium-test-user"></a>Créer un utilisateur de test Exium

1. Connectez-vous au site d’entreprise Exium en tant qu’administrateur.

1. Accédez à **User Management -> Users**, puis cliquez sur **Add User**.

    ![capture d’écran montrant la création d’un utilisateur de test](./media/exium-tutorial/add-user.png)

1. Renseignez les champs obligatoires dans la page suivante, puis cliquez sur **Save**.

    ![capture d’écran montrant la création d’un utilisateur de test avec les champs obligatoires et le bouton Save mis en évidence](./media/exium-tutorial/add-user-2.png)

> [!NOTE]
>Exium prend aussi en charge l’attribution automatique d’utilisateurs. Vous trouverez [ici](./exium-provisioning-tutorial.md) plus d’informations sur la façon de configurer l’attribution automatique d’utilisateurs.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes redirigé vers l’URL de connexion à Exium, où vous pouvez lancer le processus de connexion. 

* Accédez directement à l’URL de connexion à Exium pour lancer le processus de connexion.

* Vous pouvez utiliser Mes applications de Microsoft. Quand vous cliquez sur la vignette Exium dans Mes applications, vous êtes redirigé vers l’URL de connexion à Exium. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).


## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré Exium, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
