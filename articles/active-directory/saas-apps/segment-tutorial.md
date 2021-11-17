---
title: 'Tutoriel : Intégration de l’authentification unique Azure Active Directory à Segment | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Segment.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/01/2021
ms.author: jeedes
ms.openlocfilehash: b6106b6c10a503f7bfb80b2f50656bddd387d942
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132320823"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-segment"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory à Segment

Dans ce tutoriel, vous allez apprendre à intégrer Segment à Azure Active Directory (Azure AD). Quand vous intégrez Segment à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Segment.
* Permettre à vos utilisateurs de se connecter automatiquement à Segment avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Segment pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* Segment prend en charge l’authentification unique initiée par le **fournisseur de services et le fournisseur d’identité**.
* Segment prend en charge le provisionnement d’utilisateurs **juste-à-temps**.
* Segment prend en charge l’[attribution automatique d’utilisateurs](segment-provisioning-tutorial.md).

## <a name="add-segment-from-the-gallery"></a>Ajout de Segment à partir de la galerie

Pour configurer l’intégration de Segment à Azure AD, vous devez ajouter Segment à partir de la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Segment** dans la zone de recherche.
1. Sélectionnez **Segment** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-segment"></a>Configurer et tester Azure AD SSO pour Segment

Configurez et testez l’authentification unique Azure AD avec Segment à l’aide d’un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Segment associé.

Pour configurer et tester Azure AD SSO avec Segment, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Segment](#configure-segment-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test Segment](#create-segment-test-user)** pour avoir un équivalent de B.Simon dans Segment lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **Segment**, recherchez la section **Gérer**, puis sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, si vous souhaitez configurer l’application en mode lancé par le **fournisseur d’identité**, effectuez les étapes suivantes :

    a. Dans la zone de texte **Identificateur**, tapez une valeur au format suivant : `urn:auth0:segment-prod:samlp-<CUSTOMER_VALUE>`

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://segment-prod.auth0.com/login/callback?connection=<CUSTOMER_VALUE>`

1. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    Dans la zone de texte **URL de connexion**, tapez l’URL : `https://app.segment.com`

    > [!NOTE]
    > Ces valeurs sont des espaces réservés. Vous devez les remplacer par les valeurs réelles de l’identificateur et de l’URL de réponse. Vous trouverez plus loin dans ce tutoriel les étapes permettant d’obtenir ces valeurs.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **Certificat (en base64)** , puis sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

1. Dans la section **Configurer Segment**, copiez la ou les URL appropriées selon vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Segment.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Segment**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-segment-sso"></a>Configurer l’authentification unique Segment

1. Dans une nouvelle fenêtre de navigateur web, connectez-vous à votre site d’entreprise Segment en tant qu’administrateur.

1. Cliquez sur l’**icône Paramètres** et faites défiler jusqu’à **AUTHENTIFICATION**, puis cliquez sur **Connexions**.

    ![Capture d’écran montrant l’icône « Paramètres » sélectionnée et l’option « Connexions » sélectionnée dans le menu « Authentification »](./media/segment-tutorial/connections.PNG)

1. Cliquez sur **Ajouter une nouvelle connexion**.

    ![Capture d’écran montrant la section « Connexions » avec le bouton « Ajouter une nouvelle connexion » sélectionné](./media/segment-tutorial/new-connections.PNG)

1. Sélectionnez **SAML 2.0** comme connexion à configurer, puis cliquez sur le bouton **Sélectionner la connexion**.

    ![Capture d’écran montrant la section « Choisir une connexion » avec « SAML 2.0 » et le bouton « Sélectionner la connexion » sélectionnés](./media/segment-tutorial/select-connections.PNG)

1. Dans la page suivante, effectuez les étapes suivantes :

    ![Capture d’écran montrant la page « Configurer le fournisseur d’identité » avec les zones de texte « URL d’authentification unique » et « URL de l’audience » mises en évidence et le bouton « Suivant » sélectionné](./media/segment-tutorial/configure.PNG)

    a. Copiez la valeur de l’**URL d’authentification unique**, puis collez-la dans la zone **URL de réponse** de la boîte de dialogue **Configuration SAML de base** du portail Azure.

    b. Copiez la valeur ****URL de l’audience**** et collez-la dans la zone **URL d’identificateur** de la boîte de dialogue **Configuration SAML de base** du portail Azure.

    c. Cliquez sur **Suivant**.

    ![Configuration de Segment](./media/segment-tutorial/certificate.PNG)

1. Dans la zone **URL de point de terminaison SAML 2.0**, collez la valeur de l’**URL de connexion** que vous avez copiée à partir du portail Azure.

1. Ouvrez le **certificat (base64)** téléchargé à partir du portail Azure dans le Bloc-notes et collez le contenu dans la zone de texte **Certificat public**.

1. Cliquez sur **Configurer la connexion**.

### <a name="create-segment-test-user"></a>Créer un utilisateur de test Segment

Dans cette section, un utilisateur appelé B.Simon est créé dans Segment. Segment prend en charge le provisionnement d’utilisateurs juste-à-temps, lequel est activé par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas encore d’utilisateur dans Segment, il en est créé un après l’authentification.

Segment prend aussi en charge l’attribution automatique d’utilisateurs. Vous trouverez [ici](./segment-provisioning-tutorial.md) plus d’informations sur la façon de configurer l’attribution automatique d’utilisateurs.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

#### <a name="sp-initiated"></a>Lancée par le fournisseur de services :

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion à Segment, où vous pouvez lancer le flux de connexion.  

* Accédez directement à l’URL de connexion Segment pour lancer le processus de connexion.

#### <a name="idp-initiated"></a>Lancée par le fournisseur d’identité :

* Cliquez sur **Tester cette application** dans le portail Azure : vous devez être connecté automatiquement à l’instance de Segment pour laquelle vous avez configuré l’authentification unique. 

Vous pouvez aussi utiliser Mes applications de Microsoft pour tester l’application dans n’importe quel mode. Si, quand vous cliquez sur la vignette Segment dans Mes applications, le mode Fournisseur de services est configuré, vous êtes redirigé vers la page de connexion de l’application pour lancer le flux de connexion ; s’il s’agit du mode Fournisseur d’identité, vous êtes automatiquement connecté à l’instance de Segment pour laquelle vous avez configuré l’authentification SSO. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré Segment, vous pouvez appliquer le contrôle de session, qui protège l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
