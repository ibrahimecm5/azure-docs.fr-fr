---
title: 'Tutoriel : Intégration d’Azure Active Directory à Appraisd | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Appraisd.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/21/2021
ms.author: jeedes
ms.openlocfilehash: 7fd83905ca7b6b81e0dc6622a5457e099a383789
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132288993"
---
# <a name="tutorial-integrate-appraisd-with-azure-active-directory"></a>Tutoriel : Intégrer Appraisd à Azure Active Directory

Dans ce tutoriel, vous allez apprendre à intégrer Appraisd à Azure Active Directory (Azure AD). Quand vous intégrez Appraisd à Azure AD, vous pouvez :

* Contrôler qui a accès à Appraisd dans Azure AD.
* Permettre à vos utilisateurs de se connecter automatiquement à Appraisd avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Appraisd pour lequel l’authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test. 

* Appraisd prend en charge l’authentification unique lancée par **le fournisseur de services et le fournisseur d’identité**.

## <a name="add-appraisd-from-the-gallery"></a>Ajouter Appraisd à partir de la galerie

Pour configurer l’intégration d’Appraisd à Azure AD, vous devez ajouter Appraisd, disponible dans la galerie, à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Appraisd** dans la zone de recherche.
1. Sélectionnez **Appraisd** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-appraisd"></a>Configurer et tester l’authentification unique Azure AD pour Appraisd

Configurez et testez l’authentification unique Azure AD avec Appraisd à l’aide d’un utilisateur de test appelé **B. Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Appraisd associé.

Pour configurer et tester l’authentification unique Azure AD avec Appraisd, procédez comme suit :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique d’Appraisd](#configure-appraisd-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test Appraisd](#create-appraisd-test-user)** pour avoir un équivalent de B.Simon dans Appraisd lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **Appraisd**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon pour **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, l’application est préconfigurée et les URL nécessaires sont déjà préremplies avec Azure. L’utilisateur doit enregistrer la configuration en cliquant sur le bouton Enregistrer et effectuer les étapes suivantes :

    a. Cliquez sur **Définir des URL supplémentaires**.

    b. Dans la zone de texte **État de relais**, saisissez la valeur : `<TENANTCODE>`.

    c. Si vous souhaitez configurer l’application en mode démarré par le **fournisseur de services**, dans la zone de texte **URL d’authentification**, tapez une URL au format suivant : `https://app.appraisd.com/saml/<TENANTCODE>`

    > [!NOTE]
    > Vous obtenez les valeurs d’URL de connexion et d’état de relais réelles dans la page de configuration de l’authentification unique pour Appraisd, comme expliqué plus loin dans le tutoriel.

1. L’application Appraisd s’attend à recevoir les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à votre configuration d’attributs de jetons SAML. La capture d’écran suivante montre la liste des attributs par défaut, où **nameidentifier** est mappé avec **user.userprincipalname**. L’application Appraisd s’attend à ce que **nameidentifier** soit mappé sur **user.mail**. Vous devez donc modifier le mappage d’attribut en cliquant sur l’icône **Modifier**.

    ![Capture d’écran montrant le volet Attributs utilisateur avec l’icône de modification mise en évidence.](common/edit-attribute.png)

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **Certificat (Base64)** , puis sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

   ![Lien Téléchargement de certificat](common/certificatebase64.png)

1. Dans la section **Configurer Appraisd**, copiez la ou les URL appropriées en fonction de vos besoins.

   ![Copier les URL de configuration](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

Dans cette section, vous allez créer un utilisateur de test appelé B. Simon dans le portail Azure.

1. Dans le volet gauche du Portail Azure, sélectionnez **Azure Active Directory**, **Utilisateurs**, puis **Tous les utilisateurs**.
1. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.
1. Dans les propriétés **Utilisateur**, effectuez les étapes suivantes :
   1. Dans le champ **Nom**, entrez `B. Simon`.  
   1. Dans le champ **Nom de l’utilisateur**, entrez username@companydomain.extension. Par exemple : `B. Simon@contoso.com`.
   1. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.
   1. Cliquez sur **Créer**.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser B. Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Appraisd.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Appraisd**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-appraisd-sso"></a>Configurer l’authentification unique d’Appraisd

1. Pour automatiser la configuration dans Appraisd, vous devez installer l’**extension de navigateur My Apps Secure Sign-in** en cliquant sur **Installer l’extension**.

    ![Extension My apps](common/install-myappssecure-extension.png)

2. Après l’ajout de l’extension au navigateur, cliquez sur **Configurer Appraisd** pour être redirigé vers l’application Appraisd. À partir de là, indiquez les informations d’identification de l’administrateur pour vous connecter à Appraisd. Cette extension de navigateur configure automatiquement l’application pour vous et automatise les étapes 3 à 7.

    ![Configuration](common/setup-sso.png)

3. Si vous souhaitez configurer manuellement Appraisd, ouvrez une nouvelle fenêtre de navigateur web, connectez-vous à votre site d’entreprise Appraisd en tant qu’administrateur, puis effectuez les étapes suivantes :

4. Dans le coin supérieur droit de la page, cliquez sur l’icône **Paramètres**, puis accédez à **Configuration**.

    ![Capture d’écran montrant l’activation du lien Configuration.](./media/appraisd-tutorial/settings.png)

5. Sur le côté gauche du menu, cliquez sur **SAML single sign-on** (Authentification unique SAML).

    ![Capture d’écran montrant les options de configuration avec l’option d’authentification unique SAML mise en évidence.](./media/appraisd-tutorial/configuration.png)

6. Dans la page **SAML 2.0 Single Sign-On configuration** (Configuration de l’authentification unique SAML 2.0), effectuez les étapes suivantes :

    ![Capture d’écran montrant la page de configuration de l’authentification unique SAML 2.0, dans laquelle vous pouvez modifier l’état de relais par défaut et l’URL connexion lancée par le service.](./media/appraisd-tutorial/service-page.png)

    a. Copiez la valeur **Default Relay State** (État de relais par défaut) et collez-la dans la zone de texte **État de relais** dans **Configuration SAML de base** sur le portail Azure.

    b. Copiez la valeur **Service-initiated login URL** (URL de connexion démarrée par le service) et collez-la dans la zone de texte **État de relais** dans **Configuration SAML de base** sur le portail Azure.

7. Faites défiler la même page vers le bas jusqu’à **Identifying users** (Identification des utilisateurs), puis effectuez les étapes suivantes :

    ![Capture d’écran montrant Identification des utilisateurs, où vous pouvez entrer des valeurs à partir de cette étape.](./media/appraisd-tutorial/identifying-users.png)

    a. Dans la zone de texte **Identity Provider Single Sign-On URL** (URL d’authentification unique du fournisseur d’identité), collez l’**URL de connexion** que vous avez copiée sur le portail Azure, puis cliquez sur **Save** (Enregistrer).

    b. Dans la zone de texte **Identity Provider Issuer URL** (URL de l’émetteur du fournisseur d’identité), collez l’**Identificateur Azure AD** que vous avez copié sur le portail Azure, puis cliquez sur **Save** (Enregistrer).

    c. Dans le Bloc-notes, ouvrez le certificat codé en base 64 téléchargé dans le portail Azure, copiez son contenu, puis collez-le dans la zone **Certificat X.509** et cliquez sur **enregistrer**.

### <a name="create-appraisd-test-user"></a>Créer un utilisateur de test Appraisd

Pour se connecter à Appraisd, les utilisateurs Azure AD doivent être attribués dans Appraisd. Dans Appraisd, le provisionnement est une tâche manuelle.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous à Appraisd en tant qu’administrateur de la sécurité.

2. Dans le coin supérieur droit de la page, cliquez sur l’icône **Paramètres**, puis accédez à **Administration center** (Centre d’administration).

    ![Capture d’écran montrant les options Paramètres où vous pouvez sélectionner le centre d’administration.](./media/appraisd-tutorial/admin.png)

3. Dans la barre d’outils en haut de la page, cliquez sur **People** (Personnes), puis accédez à **Add a new user** (Ajouter un nouvel utilisateur).

    ![Capture d’écran montrant la page Appraisd avec Personnes et Ajouter un nouvel utilisateur sélectionnés.](./media/appraisd-tutorial/user.png)

4. Dans la page **Add a new user** (Ajouter un nouvel utilisateur), effectuez les étapes suivantes :

    ![Capture d’écran montrant la page Ajouter un nouvel utilisateur.](./media/appraisd-tutorial/new-user.png)

    a. Dans la zone de texte **First name** (Prénom), tapez le prénom de l’utilisateur, par exemple **Britta**.

    b. Dans la zone de texte **Last Name** (Nom), tapez le nom de famille de l’utilisateur, par exemple **Simon**.

    c. Dans la zone de texte **E-mail**, entrez l’adresse e-mail de l’utilisateur, comme `B. Simon@contoso.com`.

    d. Cliquez sur **Add User**.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

#### <a name="sp-initiated"></a>Lancée par le fournisseur de services :

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion d’Appraisd, à partir de laquelle vous pouvez lancer le flux de connexion.  

* Accédez directement à l’URL de connexion d’Appraisd pour initier le flux de connexion.

#### <a name="idp-initiated"></a>Lancée par le fournisseur d’identité :

* Cliquez sur **Tester cette application** dans Portail Azure : vous devez être connecté automatiquement à l’instance d’Appraisd pour laquelle vous avez configuré l’authentification unique. 

Vous pouvez aussi utiliser Mes applications de Microsoft pour tester l’application dans n’importe quel mode. Quand vous cliquez sur la vignette Appraisd dans Mes applications, si le mode Fournisseur de services est configuré, vous êtes redirigé vers la page de connexion de l’application pour lancer le flux de connexion ; si le mode Fournisseur d’identité est configuré, vous êtes automatiquement connecté à l’instance d’Appraisd pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez configuré Appraisd, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
