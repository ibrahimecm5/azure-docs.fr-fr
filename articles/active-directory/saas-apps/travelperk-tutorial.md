---
title: 'Tutoriel : Intégration de l’authentification unique Azure Active Directory à TravelPerk | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et TravelPerk.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/02/2021
ms.author: jeedes
ms.openlocfilehash: b949bbd1d067508b58d06f780d37845a02cb480b
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132320309"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-travelperk"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory à TravelPerk

Dans ce tutoriel, vous allez apprendre à intégrer TravelPerk à Azure Active Directory (Azure AD). Quand vous intégrez TravelPerk à Azure AD, vous pouvez :

* Contrôler qui dans Azure AD a accès à TravelPerk.
* Permettre aux utilisateurs de se connecter automatiquement à TravelPerk avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un compte TravelPerk avec abonnement Premium

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* TravelPerk prend en charge l’authentification unique (SSO) lancée par le **fournisseur de services**.

* TravelPerk prend en charge l’attribution d’utilisateurs **juste-à-temps**.

* TravelPerk prend en charge l’[attribution automatique d’utilisateurs](travelperk-provisioning-tutorial.md).

## <a name="add-travelperk-from-the-gallery"></a>Ajouter TravelPerk à partir de la galerie

Pour configurer l'intégration de TravelPerk avec Azure AD, vous devez ajouter TravelPerk à partir de la galerie à votre liste d'applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **TravelPerk** dans la zone de recherche.
1. Sélectionnez **TravelPerk** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-travelperk"></a>Configurer et tester l’authentification unique Azure AD pour TravelPerk

Configurez et testez l’authentification unique Azure AD avec TravelPerk à l’aide d’un utilisateur de test appelé **B. Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur TravelPerk associé.

Pour configurer et tester l’authentification unique Azure AD avec TravelPerk, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique TravelPerk](#configure-travelperk-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test TravelPerk](#create-travelperk-test-user)** pour avoir un équivalent de B.Simon dans TravelPerk lié à la représentation Azure AD associée.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **TravelPerk**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    a. Dans la zone de texte **Identificateur (ID d’entité)** , saisissez une URL au format suivant : `https://<COMPANY>.travelperk.com/accounts/saml2/metadata/<APPLICATION_ID>`

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://<COMPANY>.travelperk.com/accounts/saml2/callback/<APPLICATION_ID>/?acs`

    c. Dans la zone de texte **URL de connexion**, saisissez une URL au format suivant : `https://<COMPANY>.travelperk.com/`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL de réponse et l’URL de connexion réels. Les valeurs sont accessibles dans votre compte TravelPerk : accédez à **Company Settings** > **Integrations** > **Single Sign On**. Pour obtenir de l’aide, accédez au [Centre d’assistance de TravelPerk](https://support.travelperk.com/hc/articles/360052450271-How-can-I-setup-SSO-for-Azure-SAML).

1. Votre application TravelPerk attend les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à votre configuration des attributs du jeton SAML. La capture d’écran suivante montre la liste des attributs par défaut. Dans le mappage par défaut, **emailaddress** est mappé avec **user.mail**. Toutefois, l’application TravelPerk s’attend à ce que **emailaddress** soit mappé à **user.userprincipalname**. Pour TravelPerk, vous devez modifier le mappage d’attribut : cliquez sur l’icône **Modifier**, puis modifiez le mappage d’attribut. Pour modifier un attribut, cliquez simplement sur l’attribut pour ouvrir le mode édition.

    ![image](common/default-attributes.png)

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **XML de métadonnées de fédération** et sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

1. Dans la section **Configurer TravelPerk**, copiez la ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à TravelPerk.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **TravelPerk**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-travelperk-sso"></a>Configurer l’authentification unique TravelPerk

Pour configurer l’authentification unique côté **TravelPerk**, vous devez envoyer le fichier **XML des métadonnées de fédération** téléchargé et les URL correspondantes copiées à partir du portail Azure à l’[équipe du support technique de TravelPerk](mailto:trex@travelperk.com). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

### <a name="create-travelperk-test-user"></a>Créer un utilisateur de test TravelPerk

Dans cette section, un utilisateur appelé B.Simon est créé dans TravelPerk. TravelPerk prend en charge l’approvisionnement juste-à-temps, option activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas encore d’utilisateur dans TravelPerk, il en est créé un quand vous tentez d’y accéder.

TravelPerk prend aussi en charge l’attribution automatique d’utilisateurs. Vous trouverez [ici](./travelperk-provisioning-tutorial.md) plus d’informations sur la façon de configurer l’attribution automatique d’utilisateurs.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Cette opération redirige vers l’URL de connexion TravelPerk, où vous pouvez lancer le processus de connexion. 

* Accédez directement à l’URL de connexion TravelPerk pour lancer le processus de connexion.

* Vous pouvez utiliser Mes applications de Microsoft. Quand vous cliquez sur la vignette TravelPerk dans Mes applications, vous êtes redirigé vers l’URL de connexion TravelPerk. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré TravelPerk, vous pouvez appliquer le contrôle de session qui protège l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
