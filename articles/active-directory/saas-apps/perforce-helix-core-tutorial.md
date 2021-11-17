---
title: 'Tutoriel : Intégration de l’authentification unique Azure AD à Perforce Helix Core - Helix Authentication Service'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Perforce Helix Core - Helix Authentication Service.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/24/2021
ms.author: jeedes
ms.openlocfilehash: 6917af20d95e4c9fcaa3b1f1e3528bd64e4d0736
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132287549"
---
# <a name="tutorial-azure-ad-sso-integration-with-perforce-helix-core---helix-authentication-service"></a>Tutoriel : Intégration de l’authentification unique Azure AD à Perforce Helix Core - Helix Authentication Service

Dans ce tutoriel, vous allez apprendre à intégrer Perforce Helix Core - Helix Authentication Service à Azure Active Directory (Azure AD). Quand vous intégrez Perforce Helix Core - Helix Authentication Service à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Perforce Helix Core - Helix Authentication Service.
* Permettre à vos utilisateurs de se connecter automatiquement à Perforce Helix Core - Helix Authentication Service avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Perforce Helix Core - Helix Authentication Service pour lequel l’authentification unique est activée.

> [!NOTE]
> Cette intégration peut également être utilisée à partir de l’environnement cloud US Government Azure AD. Cette application est disponible dans la Galerie d’applications cloud US Government Azure AD et peut être configurée de la même façon que dans le cloud public.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* Perforce Helix Core - Helix Authentication Service prend en charge l’authentification unique lancée par le **fournisseur de services**.

## <a name="add-perforce-helix-core---helix-authentication-service-from-the-gallery"></a>Ajouter Perforce Helix Core - Helix Authentication Service depuis la galerie

Pour configurer l’intégration de Perforce Helix Core - Helix Authentication Service dans Azure AD, vous devez ajouter Perforce Helix Core - Helix Authentication Service, disponible dans la galerie, à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Perforce Helix Core - Helix Authentication Service** dans la zone de recherche.
1. Sélectionnez **Perforce Helix Core - Helix Authentication Service** dans le volet des résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-perforce-helix-core---helix-authentication-service"></a>Configurer et tester l’authentification unique Azure AD pour Perforce Helix Core - Helix Authentication Service

Configurez et testez l’authentification unique Azure AD avec Perforce Helix Core - Helix Authentication Service en utilisant un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir une relation entre un utilisateur Azure AD et l’utilisateur Perforce Helix Core - Helix Authentication Service associé.

Pour configurer et tester l’authentification unique Azure AD avec Perforce Helix Core - Helix Authentication Service, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Perforce Helix Core - Helix Authentication Service](#configure-perforce-helix-core---helix-authentication-service-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test Perforce Helix Core - Helix Authentication Service](#create-perforce-helix-core---helix-authentication-service-test-user)** pour avoir, dans Perforce Helix Core - Helix Authentication Service, un équivalent de B.Simon lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **Perforce Helix Core - Helix Authentication Service**, recherchez la section **Gérer**, puis sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    a. Dans la zone de texte **Identificateur (ID d’entité)** , saisissez une URL au format suivant : `https://<HELIX-AUTH-SERVICE>.<CUSTOMER_HOSTNAME>.com/saml`

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://<HELIX-AUTH-SERVICE>.<CUSTOMER_HOSTNAME>.com/saml/sso`

    c. Dans la zone de texte **URL de connexion**, saisissez une URL au format suivant : `https://<HELIX-AUTH-SERVICE>.<CUSTOMER_HOSTNAME>.com/`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL d’authentification et l’URL de réponse réels. Pour connaître ces valeurs, contactez l’[équipe de support technique Perforce Helix Core - Helix Authentication Service](mailto:support@perforce.com). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Perforce Helix Core - Helix Authentication Service.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Perforce Helix Core - Helix Authentication Service**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-perforce-helix-core---helix-authentication-service-sso"></a>Configurer l’authentification unique Perforce Helix Core - Helix Authentication Service

Pour configurer l’authentification unique côté **Perforce Helix Core - Helix Authentication Service**, vous devez envoyer l’**URL des métadonnées de fédération d’application** à l’[équipe de support technique Perforce Helix Core - Helix Authentication Service](mailto:support@perforce.com). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

### <a name="create-perforce-helix-core---helix-authentication-service-test-user"></a>Créer un utilisateur de test Perforce Helix Core - Helix Authentication Service

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans Perforce Helix Core - Helix Authentication Service. Travaillez en collaboration avec l’[équipe de support technique Perforce Helix Core - Helix Authentication Service](mailto:support@perforce.com) pour ajouter les utilisateurs à la plateforme Perforce Helix Core - Helix Authentication Service. Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Une redirection est alors effectuée vers l’URL de connexion à Perforce Helix Core - Helix Authentication Service, où vous pouvez lancer le flux de connexion. 

* Accédez directement à l’URL de connexion à Perforce Helix Core - Helix Authentication Service et lancez-y le flux de connexion.

* Vous pouvez utiliser Mes applications de Microsoft. Le fait de cliquer sur la vignette Perforce Helix Core - Helix Authentication Service dans My Apps vous redirige vers l’URL de connexion à Perforce Helix Core - Helix Authentication Service. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez configuré Perforce Helix Core - Helix Authentication Service, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
