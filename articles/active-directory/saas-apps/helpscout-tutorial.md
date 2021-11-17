---
title: 'Didacticiel : Intégration d’Azure Active Directory à Help Scout | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Help Scout.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/28/2021
ms.author: jeedes
ms.openlocfilehash: cb2e7edbecfe36889ed94dc0fcdec8eac81fbfaa
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132295900"
---
# <a name="tutorial-azure-active-directory-integration-with-help-scout"></a>Didacticiel : Intégration d’Azure Active Directory à Help Scout

Dans ce tutoriel, vous allez apprendre à intégrer Help Scout à Azure Active Directory (Azure AD). Quand vous intégrez Help Scout à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Help Scout.
* Permettre à vos utilisateurs de se connecter automatiquement à Help Scout avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à Help Scout, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Abonnement Help Scout pour lequel l’authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Help Scout prend en charge l’authentification unique lancée par **le fournisseur de services et le fournisseur d’identité**.
* Help Scout prend en charge le provisionnement des utilisateurs **juste-à-temps**.

## <a name="add-help-scout-from-the-gallery"></a>Ajouter Help Scout à partir de la galerie

Pour configurer l’intégration de Help Scout à Azure AD, vous devez ajouter Help Scout à partir de la galerie à votre liste d’applications SaaS managées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Help Scout** dans la zone de recherche.
1. Sélectionnez **Help Scout** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-help-scout"></a>Configurer et tester l’authentification unique Azure AD pour Help Scout

Configurez et testez l’authentification unique Azure AD avec Help Scout pour un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur associé dans Help Scout.

Pour configurer et tester l’authentification unique Azure AD avec Help Scout, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Help Scout](#configure-help-scout-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer l’utilisateur de test Help Scout](#create-help-scout-test-user)** pour avoir dans Help Scout un équivalent de B.Simon lié à la représentation Azure AD associée.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **Help Scout**, recherchez la section **Gérer**, puis sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. À la section **Configuration SAML de base**, si vous souhaitez configurer l’application en mode initié par **IDP**, suivez les étapes ci-dessous :

    a. **Identificateur** correspond à **Audience URI (Service Provider Entity ID)** (URI d’audience (ID d’entité de fournisseur de services)) Help Scout, qui commence par `urn:`

    b. **URL de réponse** correspond à **Post-back URL (Assertion Consumer Service URL)** (URL de publication (URL Assertion Consumer Service)) Help Scout, qui commence par `https://` 

    > [!NOTE]
    > Les valeurs de ces URL servent uniquement à des fins de démonstration. Vous devez mettre à jour ces valeurs avec l’URL de réponse et l’identificateur réels. Vous obtenez ces valeurs à partir de l’onglet **Authentification unique** sous la section Authentification, qui est décrite plus loin dans le didacticiel.

1. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    Dans la zone de texte **URL de connexion**, tapez l’URL : `https://secure.helpscout.net/members/login/`

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (Base64)** en fonction des options définies par rapport à vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

1. Dans la section **Configurer Help Scout**, copiez la ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Help Scout.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Help Scout**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-help-scout-sso"></a>Configurer l’authentification unique Help Scout

1. Pour automatiser la configuration dans Help Scout, vous devez installer l’**extension de navigateur My Apps Secure Sign-in** en cliquant sur **Installer l’extension**.

    ![Extension My apps](common/install-myappssecure-extension.png)

1. Après l’ajout de l’extension au navigateur, cliquez sur **Configurer Help Scout** pour être dirigé vers l’application Help Scout. À partir de là, indiquez les informations d’identification de l’administrateur pour vous connecter à Help Scout. Cette extension de navigateur configure automatiquement l’application pour vous et automatise les étapes 3 à 7.

    ![Configuration](common/setup-sso.png)

1. Si vous souhaitez configurer manuellement Help Scout, ouvrez une nouvelle fenêtre de navigateur web, connectez-vous à votre site d’entreprise Help Scout en tant qu’administrateur, puis effectuez les étapes suivantes :

1. Cliquez sur **Manage** (Gérer) dans le menu supérieur, puis sélectionnez **Company** dans le menu déroulant.

    ![Capture d’écran montrant le menu Manage avec Company sélectionné.](./media/helpscout-tutorial/settings.png)

1. Sélectionnez **Authentication** à partir du volet de navigation gauche.

    ![Capture d’écran montrant Authentication sélectionné.](./media/helpscout-tutorial/authentication.png)

1. Vous accédez ainsi à la section des paramètres SAML où vous effectuez les étapes suivantes :

    ![Capture d’écran montrant l’onglet Single Sign-On où vous entrez les informations spécifiées.](./media/helpscout-tutorial/configuration.png)

    a. Copiez la valeur de **Post-back URL (Assertion Consumer Service URL)** (URL de publication (URL Assertion Consumer Service)) et collez-la dans la zone de texte **URL de réponse** de la section **Configuration SAML de base** sur le portail Azure.

    b. Copiez la valeur de **Audience URI (Service Provider Entity ID)** (URL d’audience (ID d’entité de fournisseur de services)) et collez-la dans la zone de texte **Identificateur** de la section **Configuration SAML de base** sur le portail Azure.

1. Activez **Activer SAML** et effectuez les étapes suivantes :

    ![Capture d’écran montrant l’onglet Single Sign-On où vous activez SAML et ajoutez d’autres informations.](./media/helpscout-tutorial/information.png)

    a. Dans la zone de texte **Single Sign-on URL** (URL d’authentification unique), collez la valeur de l’**URL de connexion** que vous avez copiée dans le portail Azure.

    b. Cliquez sur **Charger le certificat** pour charger le **certificat (en base64)** téléchargé à partir du portail Azure.

    c. Entrez les domaines de messagerie de votre organisation, par exemple `contoso.com`, dans la zone de texte **Email Domains** (Domaines de messagerie). Vous pouvez séparer plusieurs domaines par une virgule. À tout moment, un utilisateur ou administrateur Help Scout qui entre dans ce domaine spécifique dans la [page de connexion d’Help Scout](https://secure.helpscout.net/members/login/) est acheminé vers le fournisseur d’identité pour être authentifié avec ses informations d’identification.

    d. Enfin, vous pouvez activer **Force SAML Sign-on** (Forcer l’authentification SAML) si vous souhaitez que les utilisateurs se connectent uniquement à Help Scout via cette méthode. Si vous voulez toujours leur donner la possibilité de se connecter avec leurs informations d’identification Help Scout, vous pouvez laisser cette option désactivée. Même si elle est activée, un propriétaire de compte sera toujours en mesure de se connecter à Help Scout avec son mot de passe de compte.

    e. Cliquez sur **Enregistrer**.

### <a name="create-help-scout-test-user"></a>Créer l’utilisateur de test Help Scout

Dans cette section, un utilisateur appelé B.Simon est créé dans Help Scout. Help Scout prend en charge l’attribution d’utilisateurs juste-à-temps, une option activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas encore d’utilisateur dans Help Scout, il en est créé un après l’authentification.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

#### <a name="sp-initiated"></a>Lancée par le fournisseur de services :

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion à Help Scout, d’où vous pouvez lancer le flux de connexion.  

* Accédez directement à l’URL de connexion à Help Scout pour lancer le flux de connexion.

#### <a name="idp-initiated"></a>Lancée par le fournisseur d’identité :

* Cliquez sur **Tester cette application** dans le portail Azure. Vous devez alors être automatiquement connecté à l’instance de Help Scout pour laquelle vous avez configuré l’authentification unique. 

Vous pouvez aussi utiliser Mes applications de Microsoft pour tester l’application dans n’importe quel mode. Si, quand vous cliquez sur la vignette Help Scout dans Mes applications, le mode Fournisseur de services est configuré, vous êtes redirigé vers la page de connexion de l’application pour lancer le flux de connexion ; s’il s’agit du mode Fournisseur d’identité, vous êtes automatiquement connecté à l’instance de Help Scout pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré Help Scout, vous pouvez appliquer le contrôle de session, qui protège en temps réel contre l’exfiltration et l’infiltration des données sensibles de votre organisation. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
