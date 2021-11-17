---
title: 'Tutoriel : Intégration de l’authentification unique Azure Active Directory à Templafy SAML2 | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Templafy SAML2.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/02/2021
ms.author: jeedes
ms.openlocfilehash: a9a265ac2e7468d1eec0db68161bca36ca8a3c24
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132282592"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-templafy-saml2"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory à Templafy SAML2

Dans ce tutoriel, vous allez apprendre à intégrer Templafy SAML2 à Azure Active Directory (Azure AD). Quand vous intégrez Templafy SAML2 à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Templafy SAML2.
* Permettre aux utilisateurs de se connecter automatiquement à Templafy SAML2 avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Templafy SAML2 pour lequel l’authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* Templafy SAML2 prend en charge l’authentification unique initiée par le **fournisseur de services**.
* Templafy SAML2 prend en charge le provisionnement d’utilisateurs **juste-à-temps**.
* Templafy SAML2 prend en charge l’[attribution automatisée d’utilisateurs](templafy-saml-2-provisioning-tutorial.md).

> [!NOTE]
> L’identificateur de cette application étant une valeur de chaîne fixe, une seule instance peut être configurée dans un locataire.

## <a name="add-templafy-saml2-from-the-gallery"></a>Ajouter Templafy SAML2 à partir de la galerie

Pour configurer l’intégration de Templafy SAML2 à Azure AD, vous devez ajouter Templafy SAML2, disponible dans la galerie, à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Templafy SAML2** dans la zone de recherche.
1. Sélectionnez **Templafy SAML2** dans le volet des résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-templafy-saml2"></a>Configurer et tester l’authentification unique Azure AD pour Templafy SAML2

Configurez et testez l’authentification unique Azure AD avec Templafy SAML2 en utilisant un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir une relation entre un utilisateur Azure AD et l’utilisateur Templafy SAML2 associé.

Pour configurer et tester l’authentification unique Azure AD avec Templafy SAML2, procédez comme suit :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Templafy SAML2](#configure-templafy-saml2-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test Templafy SAML2](#create-templafy-saml2-test-user)** pour avoir, dans Templafy SAML2, un équivalent de B.Simon lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, dans la page d’intégration de l’application **Templafy SAML2**, recherchez la section **Gérer**, puis sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, effectuez l’étape suivante :

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<CLIENT_SUBDOMAIN>.templafy.com`

    > [!NOTE]
    > Cette valeur n’est pas la valeur réelle. Mettez à jour la valeur avec l’URL de connexion réelle. Pour obtenir cette valeur, contactez l’[équipe de support technique Templafy SAML2](mailto:support@templafy.com). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. L’application Templafy SAML2 s’attend à recevoir les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à la configuration des attributs du jeton SAML. La capture d’écran suivante montre la liste des attributs par défaut.

    ![image](common/default-attributes.png)

1. En plus de ce qui précède, l’application Templafy SAML2 s’attend à ce que quelques attributs supplémentaires (présentés ci-dessous) soient repassés dans la réponse SAML. Ces attributs sont également préremplis, mais vous pouvez les examiner pour voir s’ils répondent à vos besoins.

    | Nom | Attribut source| Espace de noms  |
    | ---------------| --------------- | --------- |
    | givenname | user.givenname | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` |
    | surname | user.surname | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|
    | emailaddress | user.mail | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`
    | streetaddress | user.streetaddress | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|
    | city | user.city | `http://schemas.templafy.com/2016/06/identity/claims`|
    | postalcode | user.postalcode | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|
    | stateorprovince | user.state | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|
    | country | user.country | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|
    | jobtitle | user.jobtitle | `http://schemas.templafy.com/2016/06/identity/claims`|
    | department | user.department | `http://schemas.templafy.com/2016/06/identity/claims`|
    | phonenumber | user.telephonenumber | `http://schemas.templafy.com/2016/06/identity/claims` |
    | facsimilenumber | user.facsimiletelephonenumber | `http://schemas.templafy.com/2016/06/identity/claims`|
    | upn | user.userprincipalname | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|
    | nameidentifier | user.mail | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|
    | | |

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Templafy SAML2.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Templafy SAML2**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-templafy-saml2-sso"></a>Configurer l’authentification unique Templafy SAML2

Pour configurer l’authentification unique côté **Templafy SAML2**, vous devez envoyer l’**URL des métadonnées de fédération de l’application** à l’[équipe du support technique Templafy SAML2](mailto:support@templafy.com). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

### <a name="create-templafy-saml2-test-user"></a>Créer un utilisateur de test Templafy SAML2

Dans cette section, un utilisateur appelé B.Simon est créé dans Templafy SAML2. Templafy SAML2 prend en charge l’attribution d’utilisateurs juste-à-temps, qui est activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas encore d’utilisateur dans Templafy SAML2, il en est créé un après l’authentification.

Templafy SAML2 prend également en charge l’attribution automatique d’utilisateurs. Vous trouverez plus d’informations sur la configuration de cette fonctionnalité [ici](./templafy-saml-2-provisioning-tutorial.md).

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion à Templafy SAML2 à partir de laquelle vous pouvez lancer le flux de connexion. 

* Accédez directement à l’URL de connexion à Templafy SAML2, puis initiez le flux de connexion à partir de cet emplacement.

* Vous pouvez utiliser Mes applications de Microsoft. Quand vous cliquez sur la vignette Templafy SAML2 dans Mes applications, vous êtes redirigé vers l’URL de connexion à Templafy SAML2. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez configuré SAML2, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
