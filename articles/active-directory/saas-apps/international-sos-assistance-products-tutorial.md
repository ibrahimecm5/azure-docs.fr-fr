---
title: 'Tutoriel : Intégration de l’authentification unique (SSO) Azure Active Directory avec International SOS Assistance Products | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et International SOS Assistance Products.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/15/2021
ms.author: jeedes
ms.openlocfilehash: a2bf2da766a331a006ce7473b991ac5db6b49869
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132326395"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-international-sos-assistance-products"></a>Tutoriel : Intégration de l’authentification unique (SSO) Azure Active Directory avec International SOS Assistance Products

Dans ce tutoriel, vous allez découvrir comment intégrer International SOS Assistance Products avec Azure Active Directory (Azure AD). Quand vous intégrez International SOS Assistance Products avec Azure AD, vous pouvez :

* Contrôler, dans Azure AD, qui a accès à International SOS Assistance Products.
* Permettre à vos utilisateurs de se connecter automatiquement à International SOS Assistance Products avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement International SOS Assistance Products pour lequel l’authentification SSO (authentification unique) est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* International SOS Assistance Products prend en charge l’authentification SSO lancée par le **fournisseur de services**

* International SOS Assistance Products prend en charge le provisionnement d’utilisateurs **juste-à-temps**


## <a name="adding-international-sos-assistance-products-from-the-gallery"></a>Ajout d’International SOS Assistance Products à partir de la galerie

Pour configurer l’intégration d’International SOS Assistance Products avec Azure AD, vous devez ajouter International SOS Assistance Products, disponible dans la galerie, à votre liste d’applications SaaS managées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, dans la zone de recherche, tapez **International SOS Assistance Products**.
1. Sélectionnez **International SOS Assistance Products** dans le panneau Résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.


## <a name="configure-and-test-azure-ad-sso-for-international-sos-assistance-products"></a>Configurer et tester l’authentification SSO Azure AD pour International SOS Assistance Products

Configurez et testez l’authentification SSO Azure AD avec International SOS Assistance Products à l’aide d’une utilisatrice de test appelée **B.Simon**. Pour que l’authentification SSO fonctionne, vous devez établir une relation entre un utilisateur Azure AD et l’utilisateur associé dans International SOS Assistance Products.

Pour configurer et tester l’authentification SSO Azure AD avec International SOS Assistance Products, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification SSO avec International SOS Assistance Products](#configure-international-sos-assistance-products-sso)** pour configurer les paramètres d’authentification unique côté application.
    1. **[Créer une utilisatrice de test pour International SOS Assistance Products](#create-international-sos-assistance-products-test-user)** afin de disposer dans International SOS Assistance Products d’un équivalent de B.Simon lié à la représentation Azure AD de l’utilisatrice.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, dans la page d’intégration de l’application **International SOS Assistance Products**, recherchez la section **Gérer**, puis sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, entrez les valeurs pour les champs suivants :

    a. Dans la zone de texte **URL de connexion**, saisissez une URL au format suivant : `https://<SUBDOMAIN>.outsystemsenterprise.com/myassist`

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://<SUBDOMAIN>.internationalsos.com/sso/saml2/<CUSTOM_ID>`

    c. Dans la zone de texte **Identificateur (ID d’entité)** , saisissez une URL au format suivant : `https://www.okta.com/saml2/service-provider/<IN>`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion, l’URL de réponse et l’identificateur réels. Pour obtenir ces valeurs, contactez l’[équipe du support technique International SOS Assistance Products](mailto:onlinehelp@internationalsos.com). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

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

Dans cette section, vous allez permettre à B.Simon d’utiliser l’authentification unique Azure en lui octroyant l’accès à International SOS Assistance Products.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **International SOS Assistance Products**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-international-sos-assistance-products-sso"></a>Configurer l’authentification SSO pour International SOS Assistance Products

Pour configurer l’authentification unique côté **International SOS Assistance Products**, vous devez envoyer l’**URL des métadonnées de fédération d’application** à l’[équipe du support technique International SOS Assistance Products](mailto:onlinehelp@internationalsos.com). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

### <a name="create-international-sos-assistance-products-test-user"></a>Créer l’utilisateur de test International SOS Assistance Products

Dans cette section, une utilisatrice appelée Britta Simon est créée dans International SOS Assistance Products. International SOS Assistance Products prend en charge le provisionnement d’utilisateurs juste-à-temps, qui est activé par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas encore d’utilisateur dans International SOS Assistance Products, celui-ci est créé après l’authentification.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Cette opération effectue une redirection vers l’URL de connexion à International SOS Assistance Products, où vous pouvez lancer le flux de connexion. 

* Accédez directement à l’URL de connexion à International SOS Assistance Products, puis lancez le flux de connexion à partir de cet emplacement.

* Vous pouvez utiliser Mes applications de Microsoft. Quand vous cliquez sur la vignette International SOS Assistance Products dans Mes applications, une redirection est effectuée vers l’URL de connexion à International SOS Assistance Products. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).


## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez configuré International SOS Assistance Products, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
