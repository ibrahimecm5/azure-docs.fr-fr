---
title: 'Tutoriel : Intégration de l’authentification unique (SSO) Azure Active Directory à EAB Implementation | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et EAB Implementation.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/26/2021
ms.author: jeedes
ms.openlocfilehash: c8c474f0613ec12bc763d81568f0ebed0b10f6d1
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132296114"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-eab-implementation"></a>Tutoriel : Intégration de l’authentification unique (SSO) Azure Active Directory à EAB Implementation

Dans ce tutoriel, vous allez découvrir comment intégrer EAB Implementation à Azure Active Directory (Azure AD). Lorsque vous intégrez EAB Implementation à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à EAB Implementation.
* Permettre à vos utilisateurs de se connecter automatiquement à EAB Implementation avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.


## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement EAB Implementation pour lequel l’authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* EAB Implementation prend en charge l’authentification unique lancée par le **fournisseur de services**.

> [!NOTE]
> L’identificateur de cette application étant une valeur de chaîne fixe, une seule instance peut être configurée dans un locataire.

## <a name="adding-eab-implementation-from-the-gallery"></a>Ajout de EAB Implementation depuis la galerie

Pour configurer l’intégration de EAB Implementation à Azure AD, vous devez ajouter EAB Implementation, disponible dans la galerie, à votre liste d’applications SaaS managées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, entrez **EAB Implementation** dans la zone de recherche.
1. Sélectionnez **EAB Implementation** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-eab-implementation"></a>Configurer et tester l’authentification unique Azure AD pour EAB Implementation

Configurez et testez l’authentification unique Azure AD avec EAB Implementation pour un utilisateur de test nommé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir une relation entre un utilisateur Azure AD et l’utilisateur EAB Implementation associé.

Pour configurer et tester l’authentification unique Azure AD avec EAB Implementation, procédez comme suit :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique EAB Implementation](#configure-eab-implementation-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test EAB Implementation](#create-eab-implementation-test-user)** pour disposer, dans EAB Implementation, d’un équivalent de B.Simon lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Sur le portail Azure, dans la page d’intégration de l’application **EAB Implementation**, recherchez la section **Gérer**, puis sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, entrez les valeurs pour les champs suivants :

    a. Dans la zone de texte **Identificateur (ID d’entité)** , entrez exactement la valeur suivante : `https://impl.bouncer.eab.com`
    
    b. Dans la zone de texte **URL de réponse (URL Assertion Consumer Service)** , entrez les deux valeurs suivantes sous forme de lignes distinctes : 
    
    | URL de réponse|
    | -------------- |
    | `https://impl.bouncer.eab.com/sso/saml2/acs` |
    | `https://impl.bouncer.eab.com/sso/saml2/acs/` |
    |
    
    c. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<SUBDOMAIN>.navigate.impl.eab.com/`.

    > [!NOTE]
    > Cette valeur n’est pas la valeur réelle. Mettez à jour la valeur avec l’URL de connexion réelle. Contactez l’[équipe du support EAB Implementation](mailto:EABTechSupport@eab.com) pour obtenir la valeur. Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à EAB Implementation.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **EAB Implementation**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-eab-implementation-sso"></a>Configurer l’authentification unique EAB Implementation

Pour configurer l’authentification unique côté **EAB Implementation**, vous devez envoyer l’**URL des métadonnées de fédération d’application** à l’[équipe du support EAB Implementation](mailto:EABTechSupport@eab.com). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

### <a name="create-eab-implementation-test-user"></a>Créer un utilisateur de test EAB Implementation

Dans cette section, vous allez créer un utilisateur appelé B.Simon dans EAB Implementation. Collaborez avec l’[équipe du support EAB](mailto:EABTechSupport@eab.com) pour ajouter des utilisateurs à la plateforme EAB Implementation. Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion EAB Implementation, à partir de laquelle vous pouvez initier le flux de connexion. 

* Accédez directement à l’URL de connexion EAB Implementation pour lancer le flux de connexion.

* Vous pouvez utiliser Mes applications de Microsoft. Le fait de cliquer sur la vignette EAB Implementation dans Mes applications vous redirige vers l’URL de connexion EAB Implementation. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).


## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré EAB Implementation, vous pouvez appliquer le contrôle de session, qui protège en temps réel contre l’exfiltration et l’infiltration des données sensibles de votre organisation. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
