---
title: 'Tutoriel : Intégration de l’authentification unique Azure Active Directory à Netskope User Authentication | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Netskope User Authentication.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/06/2021
ms.author: jeedes
ms.openlocfilehash: b90270e70ef0a54cccdde5b773d4d938d9813d19
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132346341"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netskope-user-authentication"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory à Netskope User Authentication

Ce tutoriel explique comment intégrer Netskope User Authentication à Azure Active Directory (Azure AD). En intégrant Netskope User Authentication à Azure AD, vous pouvez :

* Contrôler, dans Azure AD, qui a accès à Netskope User Authentication.
* Permettre aux utilisateurs de se connecter automatiquement à Netskope User Authentication avec leur compte Azure AD
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Netskope User Authentication pour lequel l’authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* Netskope User Authentication prend en charge l’authentification unique lancée par **le fournisseur de services et le fournisseur d’identité**.

## <a name="add-netskope-user-authentication-from-the-gallery"></a>Ajout de Netskope User Authentication à partir de la galerie

Pour configurer l’intégration de Netskope User Authentication dans Azure AD, vous devez ajouter Netskope User Authentication à votre liste d’applications SaaS managées à partir de la galerie.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Netskope User Authentication** dans la zone de recherche.
1. Sélectionnez **Netskope User Authentication** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-netskope-user-authentication"></a>Configurer et tester l’authentification unique Azure AD pour Netskope User Authentication

Configurez et testez l’authentification unique Azure AD auprès de Netskope User Authentication avec un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Netskope User Authentication associé.

Pour configurer et tester l’authentification unique Azure AD auprès de Netskope User Authentication, suivez les indications des modules ci-après :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Netskope User Authentication](#configure-netskope-user-authentication-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test Netskope User Authentication](#create-netskope-user-authentication-test-user)** pour avoir un équivalent de B.Simon dans Netskope User Authentication lié à la représentation Azure AD associée.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **Netskope User Authentication**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, si vous souhaitez configurer l’application en mode Initié par le **fournisseur d’identité**, entrez les valeurs pour les champs suivants :

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<tenantname>.goskope.com/<customer entered string>`

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://<tenantname>.goskope.com/nsauth/saml2/http-post/<customer entered string>`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur et l’URL de réponse réels. Ces valeurs vous seront expliquées plus loin dans le tutoriel.

1. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<tenantname>.goskope.com`

    > [!NOTE]
    > La valeur de l’URL de connexion n’est pas réelle. Modifiez-la selon l’URL de connexion réelle. Contactez l’[équipe du support technique de Netskope User Authentication](mailto:support@netskope.com) pour connaître la valeur de l’URL de connexion. Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. Sur la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le fichier **XML de métadonnées de fédération** en fonction des options définies selon vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

1. Dans la section **Configurer Netskope User Authentication**, copiez l’URL ou les URL appropriées selon à vos besoins.

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

Dans cette section, vous autorisez B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Netskope User Authentication.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Netskope User Authentication**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-netskope-user-authentication-sso"></a>Configurer l’authentification unique Netskope User Authentication

1. Ouvrez un nouvel onglet dans votre navigateur et connectez-vous à votre site d’entreprise Netskope User Authentication en tant qu’administrateur.

1. Cliquez sur l’onglet **Active Platform**.

    ![Capture d’écran montrant l’élément Active Platform sélectionné à partir de Settings.](./media/netskope-user-authentication-tutorial/user-1.png)

1. Faites défiler la page vers le bas jusqu’à **FORWARD PROXY** (Proxy de transfert) et sélectionnez **SAML**.

    ![Capture d’écran montrant l’option SAML sélectionnée à partir de Active Platform.](./media/netskope-user-authentication-tutorial/configuration.png)

1. Sur la page **SAML Settings** , procédez comme suit :

    ![Capture d’écran montrant la zone des paramètres SAML dans laquelle vous pouvez indiquer les valeurs décrites.](./media/netskope-user-authentication-tutorial/configure-copyurls.png)

    a. Copiez la valeur **SAML Entity ID**, puis collez-la dans la zone de texte **Identificateur** de la section **Configuration SAML de base** du portail Azure.

    b. Copiez la valeur **SAML ACS URL**, puis collez-la dans la zone de texte **URL de réponse** de la section **Configuration SAML de base** du portail Azure.

1. Cliquez sur **ADD ACCOUNT** (Ajouter un compte).

    ![Capture d’écran montrant l’option ADD ACCOUNT sélectionnée dans le volet SAML.](./media/netskope-user-authentication-tutorial/add-account.png)

1. Dans la page **Add SAML Account** (Ajouter un compte SAML), effectuez les étapes suivantes :

    ![Capture d’écran montrant la page Add SAML Account dans laquelle vous pouvez indiquer les valeurs décrites.](./media/netskope-user-authentication-tutorial/configure-settings.png)

    a. Dans la zone de texte **NAME**, indiquez un nom comme Azure AD.

    b. Dans la zone de texte **IDP URL** (URL du fournisseur d’identité), collez la valeur de l’**URL de connexion** que vous avez copiée sur le portail Azure.

    c. Dans la zone de texte **IDP ENTITY ID** (ID d’entité de fournisseur d’identité), collez la valeur **Identificateur Azure AD** que vous avez copiée dans le portail Azure.

    d. Ouvrez le fichier de métadonnées téléchargé dans le Bloc-notes, copiez son contenu dans le Presse-papiers, puis collez-le dans la zone de texte **IDP CERTIFICATE** (Certificat de fournisseur d’identité).

    e. Cliquez sur **ENREGISTRER**.

### <a name="create-netskope-user-authentication-test-user"></a>Créer un utilisateur de test Netskope User Authentication

1. Ouvrez un nouvel onglet dans votre navigateur et connectez-vous à votre site d’entreprise Netskope User Authentication en tant qu’administrateur.

1. Cliquez sur l’onglet **Settings** dans le volet de navigation de gauche.

    ![Capture d’écran montrant l’élément Settings sélectionné.](./media/netskope-user-authentication-tutorial/configuration-settings.png)

1. Cliquez sur l’onglet **Active Platform**.

    ![Capture d’écran montrant l’élément Active Platform sélectionné à partir de Settings.](./media/netskope-user-authentication-tutorial/user-1.png)

1. Cliquez sur l’onglet **Users** (Utilisateurs).

    ![Capture d’écran montrant l’option Users sélectionnée à partir de Active Platform.](./media/netskope-user-authentication-tutorial/add-user.png)

1. Cliquez sur **ADD USERS**.

    ![Capture d’écran montrant la boîte de dialogue Users dans laquelle vous pouvez sélectionner ADD USERS.](./media/netskope-user-authentication-tutorial/user-add.png)

1. Entrez l’adresse e-mail de l’utilisateur à ajouter, puis cliquez sur **ADD**.

    ![Capture d’écran montrant la zone Add Users dans laquelle vous pouvez entrer une liste d’utilisateurs.](./media/netskope-user-authentication-tutorial/add-user-popup.png)

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

#### <a name="sp-initiated"></a>Lancée par le fournisseur de services :

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion Netskope User Authentication, à partir de laquelle vous pouvez lancer le flux de connexion.  

* Accédez directement à l’URL de connexion à Netskope User Authentication pour lancer le flux de connexion.

#### <a name="idp-initiated"></a>Lancée par le fournisseur d’identité :

* Dans le portail Azure, cliquez sur **Tester cette application**. Vous êtes alors automatiquement connecté à l’instance de Netskope User Authentication pour laquelle vous avez configuré l’authentification unique. 

Vous pouvez aussi utiliser Mes applications de Microsoft pour tester l’application dans n’importe quel mode. Si, quand vous cliquez sur la vignette Netskope User Authentication dans Mes applications, le mode Fournisseur de services est configuré, vous êtes redirigé vers la page de connexion de l’application pour lancer le flux de connexion ; s’il s’agit du mode Fournisseur d’identité, vous êtes automatiquement connecté à l’instance de Netskope User Authentication pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez configuré Netskope User Authentication, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
