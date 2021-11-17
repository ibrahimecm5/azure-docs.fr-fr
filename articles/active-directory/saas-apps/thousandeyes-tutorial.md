---
title: 'Tutoriel : Intégration de l’authentification unique Azure Active Directory à ThousandEyes | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et ThousandEyes.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/24/2021
ms.author: jeedes
ms.openlocfilehash: b7372edd168dd49b00f034b815e0c8390dd6fdd0
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132328751"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-thousandeyes"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory à ThousandEyes

Dans ce tutoriel, vous allez apprendre à intégrer ThousandEyes à Azure Active Directory (Azure AD). Quand vous intégrez ThousandEyes à Azure AD, vous pouvez :

* Contrôler qui a accès à ThousandEyes dans Azure AD.
* Permettre à vos utilisateurs de se connecter automatiquement à ThousandEyes avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Abonnement ThousandEyes pour lequel l’authentification unique (SSO) est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* ThousandEyes prend en charge l’authentification unique (SSO) lancée par **le fournisseur de services et le fournisseur d’identité**.
* ThousandEyes prend en charge l’[attribution **automatique** d’utilisateurs](./thousandeyes-provisioning-tutorial.md).

> [!NOTE]
> L’identificateur de cette application étant une valeur de chaîne fixe, une seule instance peut être configurée dans un locataire.

## <a name="add-thousandeyes-from-the-gallery"></a>Ajouter ThousandEyes depuis la galerie

Pour configurer l’intégration de ThousandEyes à Azure AD, vous devez ajouter ThousandEyes à partir de la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **ThousandEyes** dans la zone de recherche.
1. Sélectionnez **ThousandEyes** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-thousandeyes"></a>Configurer et tester l’authentification unique Azure AD pour ThousandEyes

Configurez et testez l’authentification unique Azure AD avec ThousandEyes pour un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur ThousandEyes associé.

Pour configurer et tester l’authentification unique Azure AD avec ThousandEyes, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique ThousandEyes](#configure-thousandeyes-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test ThousandEyes](#create-thousandeyes-test-user)** pour avoir un équivalent de B.Simon dans ThousandEyes lié à la représentation Azure AD associée.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **ThousandEyes**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, l’application est préconfigurée et les URL nécessaires sont déjà préremplies avec Azure. L’utilisateur doit enregistrer la configuration en cliquant sur le bouton **Enregistrer**.

1. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    Dans la zone de texte **URL de connexion**, tapez l’URL : `https://app.thousandeyes.com/login/sso`

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **Certificat (en base64)** , puis sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

1. Dans la section **Configurer ThousandEyes**, copiez l’URL ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à ThousandEyes.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **ThousandEyes**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-thousandeyes-sso"></a>Configurer l’authentification unique ThousandEyes

1. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise **ThousandEyes** en tant qu’administrateur.

2. Dans le menu situé en haut, cliquez sur **Settings**.

    ![Capture d’écran montrant le site ThousandEyes avec Settings sélectionné.](./media/thousandeyes-tutorial/settings-tab.png "Paramètres")

3. Cliquez sur **Account**

    ![Capture d’écran montrant Account sélectionné à partir du menu Settings.](./media/thousandeyes-tutorial/menu.png "Compte")

4. Cliquez sur l’onglet **Security & Authentication (Sécurité et authentification)** .

    ![Sécurité et authentification](./media/thousandeyes-tutorial/security.png "Security & Authentication")

5. Dans la section **Setup Single Sign-On** , procédez comme suit :

    ![Configurer l’authentification unique](./media/thousandeyes-tutorial/configuration.png "Setup Single Sign-On")

    a. Sélectionnez **Activer l'authentification unique**.

    b. Dans la zone de texte **Login Page URL** (URL de page de connexion), collez l’**URL de connexion** que vous avez copiée à partir du portail Azure.

    c. Dans la zone de texte **Logout Page URL** (URL de page de déconnexion), collez l’**URL de déconnexion** que vous avez copiée à partir du portail Azure.

    d. Dans la zone de texte **Identity Provider Issuer** (Émetteur du fournisseur d’identité), collez la valeur de l’**Identificateur Azure AD** que vous avez copié à partir du portail Azure.

    e. Dans **Certificat de vérification**, cliquez sur **Choisir un fichier**, puis chargez le certificat que vous avez téléchargé à partir du portail Azure.

    f. Cliquez sur **Enregistrer**.

### <a name="create-thousandeyes-test-user"></a>Créer un utilisateur de test ThousandEyes

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans ThousandEyes. ThousandEyes prend en charge l’attribution automatique d’utilisateurs, qui est activée par défaut. Vous trouverez plus d’informations [ici](thousandeyes-provisioning-tutorial.md) sur la façon de configurer l’attribution automatique d’utilisateurs.

**Si vous avez besoin de créer un utilisateur manuellement, procédez comme suit :**

1. Connectez-vous à votre site d’entreprise ThousandEyes en tant qu’administrateur.

2. Cliquez sur **Settings**.

    ![Capture d’écran montrant le site ThousandEyes avec Settings sélectionné.](./media/thousandeyes-tutorial/settings-tab.png "Paramètres")

3. Cliquez sur **Account**.

    ![Capture d’écran montrant Account sélectionné à partir du menu Settings.](./media/thousandeyes-tutorial/menu.png "Compte")

4. Cliquez sur l’onglet **Accounts & Users (Comptes et utilisateurs)** .

    ![Comptes et utilisateurs](./media/thousandeyes-tutorial/user.png "Accounts & Users")

5. Dans la section **Add Users & Accounts (Ajouter des utilisateurs et des comptes)** , procédez comme suit :

    ![Ajouter des comptes d’utilisateur](./media/thousandeyes-tutorial/add-user.png "Add User Accounts")

    a. Dans la zone de texte **Name** (Nom), tapez le nom d’un utilisateur, par exemple **B.Simon**.

    b. Dans la zone de texte **Email**, entrez l’adresse e-mail de l’utilisateur, par exemple b.simon@contoso.com.

    b. Cliquez sur le bouton **Add New User to Account**.

    > [!NOTE]
    > Le titulaire du compte Azure Active Directory reçoit un courrier électronique contenant un lien permettant de confirmer et activer le compte.

> [!NOTE]
> Vous pouvez utiliser n’importe quels autres outils ou API de création de compte d’utilisateur, fournis par ThousandEyes, pour approvisionner des comptes d’utilisateur Azure Active Directory.


## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

#### <a name="sp-initiated"></a>Lancée par le fournisseur de services :

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion à ThousandEyes, à partir de laquelle vous pouvez lancer le flux de connexion.  

* Accédez directement à l’URL de connexion à ThousandEyes et lancez-y le flux de connexion.

#### <a name="idp-initiated"></a>Lancée par le fournisseur d’identité :

* Cliquez sur **Tester cette application** dans le portail Azure. Vous devez être connecté automatiquement à l’instance de ThousandEyes pour laquelle vous avez configuré l’authentification unique. 

Vous pouvez aussi utiliser Mes applications de Microsoft pour tester l’application dans n’importe quel mode. Si, quand vous cliquez sur la vignette ThousandEyes dans Mes applications, le mode Fournisseur de services est configuré, vous êtes redirigé vers la page de connexion de l’application pour lancer le flux de connexion ; si c’est le mode Fournisseur d’identité qui est configuré, vous êtes automatiquement connecté à l’instance de ThousandEyes pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré ThousandEyes, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
