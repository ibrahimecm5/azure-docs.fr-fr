---
title: 'Tutoriel : Intégration de l’authentification unique (SSO) Azure Active Directory à Harness | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Harness.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/31/2021
ms.author: jeedes
ms.openlocfilehash: a5368ab5b5d1464bb2b5638b58366f208ba3d1fb
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132317895"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-harness"></a>Tutoriel : Intégration de l’authentification unique (SSO) Azure Active Directory à Harness

Dans ce tutoriel, vous allez apprendre à intégrer Harness à Azure Active Directory (Azure AD). Quand vous intégrez Harness à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Harness.
* Permettre à vos utilisateurs de se connecter automatiquement à Harness avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Harness pour lequel l’authentification unique (SSO) est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* Harness prend en charge l'authentification unique (SSO) lancée par **le fournisseur de services et le fournisseur d'identité**.

* Harness prend en charge l’[attribution automatique d’utilisateurs](harness-provisioning-tutorial.md).

> [!NOTE]
> L’identificateur de cette application étant une valeur de chaîne fixe, une seule instance peut être configurée dans un locataire.

## <a name="add-harness-from-the-gallery"></a>Ajouter Harness à partir de la galerie

Pour configurer l’intégration de Harness à Azure AD, vous devez ajouter Harness de la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Harness** dans la zone de recherche.
1. Sélectionnez **Harness** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-harness"></a>Configurer et tester l'authentification unique Azure AD pour Harness

Configurez et testez l’authentification unique (SSO) Azure AD avec Harness à l’aide d’un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique (SSO) fonctionne, vous devez établir une relation entre un utilisateur Azure AD et l’utilisateur Harness associé.

Pour configurer et tester l'authentification unique Azure AD auprès de Harness, suivez les étapes ci-dessous :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique (SSO) Harness](#configure-harness-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test Harness](#create-harness-test-user)** pour disposer, dans Harness, d’un équivalent de B.Simon lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Sur le portail Azure, accédez à la page d'intégration de l'application **Harness**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, si vous souhaitez configurer l’application en mode lancé par le **fournisseur d’identité**, effectuez l’étape suivante :

    Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://app.harness.io/gateway/api/users/saml-login?accountId=<harness_account_id>`

1. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    Dans la zone de texte **URL de connexion**, tapez l’URL : `https://app.harness.io/`

    > [!NOTE]
    > La valeur de l’URL de réponse n’est pas réelle. Vous obtiendrez l’URL de réponse réelle en suivant la procédure décrite dans la section **Configurer l’authentification unique (SSO) Harness**, plus loin dans ce tutoriel. Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **XML de métadonnées de fédération** et sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

1. Dans la section **Configurer Harness**, copiez la ou les URL appropriées, selon vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Harness.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Harness**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-harness-sso"></a>Configurer l’authentification unique (SSO) Harness

1. Pour automatiser la configuration dans BetterWorks, vous devez installer l’**extension de navigateur My Apps Secure Sign-in** en cliquant sur **Installer l’extension**.

    ![Extension My apps](common/install-myappssecure-extension.png)

2. Après l’ajout de l’extension au navigateur, cliquez sur **Configurer BetterWorks** pour être redirigé vers l’application BetterWorks. À partir de là, indiquez les informations d’identification de l’administrateur pour vous connecter à BetterWorks. Cette extension de navigateur configure automatiquement l’application et automatise les étapes 3 à 6.

    ![Configuration](common/setup-sso.png)

3. Si vous souhaitez configurer manuellement BetterWorks, ouvrez une nouvelle fenêtre de navigateur web, connectez-vous à votre site d’entreprise BetterWorks en tant qu’administrateur, puis effectuez les étapes suivantes :

4. En haut à droite de la page, cliquez sur **Sécurité continue** > **Gestion de l’accès** > **Paramètres d’authentification**.

    ![Capture d’écran montrant le menu « Continuous Security » avec les éléments « Access Management » et « Authentication Settings » sélectionnés.](./media/harness-tutorial/authentication.png)

5. Dans la section **Fournisseurs SSO**, cliquez sur **+ Ajouter des fournisseurs SSO** > **SAML**.

    ![Capture d’écran montrant la zone « SSO Providers » avec l’option « Add SSO Providers - SAML » sélectionnée.](./media/harness-tutorial/providers.png)

6. Dans la fenêtre contextuelle **Fournisseur SAML**, effectuez les étapes suivantes :

    ![Capture d’écran montrant la fenêtre contextuelle « SAML Provider » avec les champs « URL » et « Display Name » mis en évidence, ainsi que les boutons « File » et « Submit » sélectionnés.](./media/harness-tutorial/file.png)

    a. Copiez l’instance **Dans votre fournisseur SSO, activez la connexion basée sur SAML, puis entrez l’URL suivante**, puis collez-la dans la zone de texte URL de réponse de la section **Configuration SAML de base**.

    b. Dans la zone de texte **Nom d’affichage**, tapez votre nom d’affichage.

    c. Cliquez sur **Choisir un fichier** pour charger le fichier XML de métadonnées de fédération que vous avez téléchargé à partir d’Azure AD.

    d. Cliquez sur **ENVOYER**.

### <a name="create-harness-test-user"></a>Créer un utilisateur de test Harness

Pour permettre aux utilisateurs Azure AD de se connecter à Harness, vous devez les attribuer dans Harness. Dans Harness, le provisionnement est une tâche manuelle.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous à Harness en tant qu’administrateur.

1. En haut à droite de la page, cliquez sur **Sécurité continue** > **Gestion de l’accès** > **Utilisateurs**.

    ![Capture d’écran montrant le menu « Continuous Security » avec les éléments « Access Management » et « Users » sélectionnés.](./media/harness-tutorial/users.png)

1. À droite de la page, cliquez sur **+ Ajouter un utilisateur**.

    ![Capture d’écran montrant la page « Users » avec l’action « + Add User » sélectionnée.](./media/harness-tutorial/add-user.png)

1. Dans la fenêtre contextuelle **Ajouter un utilisateur**, effectuez les étapes suivantes :

    ![Configuration de Harness](./media/harness-tutorial/configure.png)

    a. Dans la zone de texte **Adresses e-mail**, entrez l’adresse e-mail de l’utilisateur, par exemple `B.simon@contoso.com`.

    b. Sélectionnez votre **Groupe d’utilisateurs**.

    c. Cliquez sur **Envoyer**.

Harness prend aussi en charge l’attribution automatique d’utilisateurs. Vous trouverez [ici](./harness-provisioning-tutorial.md) plus d’informations sur la façon de configurer l’attribution automatique d’utilisateurs.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

#### <a name="sp-initiated"></a>Lancée par le fournisseur de services :

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l'URL de connexion à Harness, à partir de laquelle vous pouvez lancer le flux de connexion.  

* Accédez directement à l'URL de connexion à Harness pour lancer le flux de connexion.

#### <a name="idp-initiated"></a>Lancée par le fournisseur d’identité :

* Cliquez sur **Tester cette application** sur le portail Azure. Vous êtes alors automatiquement connecté à l'instance de Harness pour laquelle vous avez configuré l'authentification unique. 

Vous pouvez aussi utiliser Mes applications de Microsoft pour tester l’application dans n’importe quel mode. Si, lorsque vous cliquez sur la vignette Harness dans Mes applications, le mode Fournisseur de services est configuré, vous êtes redirigé vers la page de connexion de l'application pour lancer le flux de connexion ; s'il s'agit du mode Fournisseur d'identité, vous êtes automatiquement connecté à l'instance de Harness pour laquelle vous avez configuré l'authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré Harness, vous pouvez appliquer le contrôle de session, qui protège en temps réel contre l'exfiltration et l'infiltration de données sensibles de votre organisation. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
