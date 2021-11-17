---
title: 'Didacticiel : intégration d’Azure Active Directory avec Infinite Campus | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Infinite Campus.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/17/2021
ms.author: jeedes
ms.openlocfilehash: 817bc6c7b425af9cc02bc339967cb29b1c4b81fd
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132285231"
---
# <a name="tutorial-azure-active-directory-integration-with-infinite-campus"></a>Didacticiel : intégration d’Azure Active Directory avec Infinite Campus

Dans ce tutoriel, vous allez apprendre à intégrer Infinite Campus à Azure Active Directory (Azure AD). Lorsque vous intégrez Infinite Campus à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Infinite Campus.
* Permettre à vos utilisateurs de se connecter automatiquement à Infinite Campus avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD avec Infinite Campus, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un [compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Infinite Campus pour lequel l'authentification unique est activée.
* Au minimum, vous devez être administrateur Azure Active Directory et être titulaire du rôle de sécurité « Student Information System (SIS) » des produits Campus pour effectuer la configuration.

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Infinite Campus prend en charge l'authentification unique lancée par le **fournisseur de services**.

## <a name="add-infinite-campus-from-the-gallery"></a>Ajouter Infinite Campus à partir de la galerie

Pour configurer l’intégration d’Infinite Campus avec Azure AD, vous devez ajouter Infinite Campus à partir de la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, entrez **Infinite Campus** dans la zone de recherche.
1. Sélectionnez **Infinite Campus** dans le volet de résultats, puis ajoutez l'application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-infinite-campus"></a>Configurer et tester l'authentification unique Azure AD pour Infinite Campus

Configurez et testez l'authentification unique Azure AD auprès d'Infinite Campus pour un utilisateur de test appelé **B.Simon**. Pour que l'authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l'utilisateur associé dans Infinite Campus.

Pour configurer et tester l'authentification unique Azure AD auprès d'Infinite Campus, suivez les étapes ci-dessous :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l'authentification unique Infinite Campus](#configure-infinite-campus-sso)** pour configurer les paramètres de l'authentification unique côté application.
    1. **[Créer un utilisateur de test Infinite Campus](#create-infinite-campus-test-user)** pour avoir, dans Infinite Campus, un équivalent de B.Simon lié à la représentation Azure AD associée.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Sur le portail Azure, accédez à la page d'intégration de l'application **Infinite Campus**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section Configuration SAML de base, effectuez les étapes suivantes (notez que le domaine varie selon le modèle d’hébergement, mais que la valeur **FULLY-QUALIFIED-DOMAIN** doit correspondre à celle de votre installation Infinite Campus) :

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<DOMAIN>.infinitecampus.com/campus/SSO/<DISTRICTNAME>/SIS`

    b. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<DOMAIN>.infinitecampus.com/campus/<DISTRICTNAME>`

    c. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://<DOMAIN>.infinitecampus.com/campus/SSO/<DISTRICTNAME>`

5. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur le bouton Copier pour copier l’**URL des métadonnées de fédération d’application**, puis enregistrez-la sur votre ordinateur.

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

Dans cette section, vous allez permettre à B.Simon d'utiliser l'authentification unique Azure en lui accordant l'accès à Infinite Campus.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Infinite Campus**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-infinite-campus-sso"></a>Configurer l'authentification unique pour Infinite Campus

1. Ouvrez une autre fenêtre de navigateur web et connectez-vous à Infinite Campus en tant qu’administrateur de la sécurité.

2. Sur le côté gauche du menu, cliquez sur **Administration de système**.

    ![Administrateur](./media/infinitecampus-tutorial/admin.png)

3. Accédez à **Sécurité utilisateur** > **Gestion SAML** > **Configuration du fournisseur de services d’authentification unique**.

    ![SAML](./media/infinitecampus-tutorial/security.png)

4. Dans la page **Configuration du fournisseur de services d’authentification unique**, procédez comme suit :

    ![Authentification unique](./media/infinitecampus-tutorial/configuration.png)

    a. Sélectionnez **Activer l’authentification unique SAML**.

    b. Modifiez le champ **Nom d'attribut facultatif** pour qu'il contienne **name**.

    c. Dans la section **Select an option to retrieve Identity Provider (IDP) server data** (Sélectionnez une option pour récupérer les données de serveur du fournisseur d’identité), sélectionnez **Metadata URL** (URL des métadonnées), collez l’**URL des métadonnées de fédération d’application** que vous avez copiée à partir du portail Azure dans la zone, puis cliquez sur **Sync** (Synchroniser).

    d. Après avoir cliqué sur **Synchroniser**, les valeurs sont automatiquement renseignées dans la page **Configuration du fournisseur de services d’authentification unique**. Vérifiez que ces valeurs correspondent à celles indiquées à l’étape 4 ci-dessus.

    e. Cliquez sur **Enregistrer**.

### <a name="create-infinite-campus-test-user"></a>Créer un utilisateur de test Infinite Campus

L’architecture d’Infinite Campus est centrée sur des données démographiques. Pour ajouter des utilisateurs dans la plateforme Infinite Campus, veuillez contacter l’[équipe de support technique d’Infinite Campus](mailto:sales@infinitecampus.com).

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l'URL de connexion à Infinite Campus, à partir de laquelle vous pouvez lancer le flux de connexion. 

* Accédez directement à l'URL de connexion à Infinite Campus pour lancer le flux de connexion.

* Vous pouvez utiliser Mes applications de Microsoft. Le fait de cliquer sur la vignette Infinite Campus dans Mes applications vous redirige vers l'URL de connexion à Infinite Campus. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré Infinite Campus, vous pouvez appliquer le contrôle de session, qui protège en temps réel contre l'exfiltration et l'infiltration des données sensibles de votre organisation. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
