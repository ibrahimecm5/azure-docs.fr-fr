---
title: 'Tutoriel : Intégration d’Azure Active Directory à Carbonite Endpoint Backup | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Carbonite Endpoint Backup.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/03/2021
ms.author: jeedes
ms.openlocfilehash: c5da66a5bfd91dd352dbd225bab40a65c5ae1a72
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132292286"
---
# <a name="tutorial-integrate-carbonite-endpoint-backup-with-azure-active-directory"></a>Tutoriel : Intégrer Carbonite Endpoint Backup à Azure Active Directory

Dans ce tutoriel, vous allez apprendre à intégrer Carbonite Endpoint Backup à Azure Active Directory (Azure AD). Lorsque vous intégrez Carbonite Endpoint Backup à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Carbonite Endpoint Backup
* Permettre à vos utilisateurs de se connecter automatiquement à Carbonite Endpoint Backup avec leur compte Azure AD
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Abonnement à Carbonite Endpoint Backup pour lequel l’authentification unique (SSO) est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* Carbonite Endpoint Backup prend en charge l’authentification SSO lancée par **le fournisseur de services (SP) et le fournisseur d’identité (IdP)** .

> [!NOTE]
> L’identificateur de cette application étant une valeur de chaîne fixe, une seule instance peut être configurée dans un locataire.

## <a name="add-carbonite-endpoint-backup-from-the-gallery"></a>Ajouter Carbonite Endpoint Backup à partir de la galerie

Pour configurer l’intégration de Carbonite Endpoint Backup à Azure AD, à partir de la galerie, vous devez ajouter Carbonite Endpoint Backup à votre liste d’applications SaaS managées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Carbonite Endpoint Backup** dans la zone de recherche.
1. Sélectionnez **Carbonite Endpoint Backup** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-carbonite-endpoint-backup"></a>Configurer et tester l’authentification SSO Azure AD pour Carbonite Endpoint Backup

Configurez et testez l’authentification unique Azure AD avec Carbonite Endpoint Backup à l’aide d’un utilisateur de test appelé **B. Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Carbonite Endpoint Backup associé.

Pour configurer et tester l’authentification SSO Azure AD avec Carbonite Endpoint Backup, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification SSO pour Carbonite Endpoint Backup](#configure-carbonite-endpoint-backup-sso)** afin de configurer les paramètres d’authentification unique côté application.
    1. **[Créer un utilisateur de test Carbonite Endpoint Backup](#create-carbonite-endpoint-backup-test-user)** pour avoir dans Carbonite Endpoint Backup un équivalent de B. Simon associé à sa représentation dans Azure AD.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, dans la page d’intégration de l’application **Carbonite Endpoint Backup**, recherchez la section **Gérer**, puis sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon pour **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, si vous souhaitez configurer l’application en mode Initié par le **fournisseur d’identité**, entrez les valeurs pour les champs suivants :

    a. Dans la zone de texte **Identificateur**, tapez l’une des URL suivantes :

    ```http
    https://red-us.mysecuredatavault.com
    https://red-apac.mysecuredatavault.com
    https://red-fr.mysecuredatavault.com
    https://red-emea.mysecuredatavault.com
    https://kamino.mysecuredatavault.com
    ```

    b. Dans la zone de texte **URL de réponse**, tapez l’une des URL suivantes :

    ```http
    https://red-us.mysecuredatavault.com/AssertionConsumerService.aspx
    https://red-apac.mysecuredatavault.com/AssertionConsumerService.aspx
    https://red-fr.mysecuredatavault.com/AssertionConsumerService.aspx
    https://red-emea.mysecuredatavault.com/AssertionConsumerService.aspx
    ```

1. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    Dans la zone de texte **URL d’authentification**, tapez l’une des URL suivantes :

    ```http
    https://red-us.mysecuredatavault.com/
    https://red-apac.mysecuredatavault.com/
    https://red-fr.mysecuredatavault.com/
    https://red-emea.mysecuredatavault.com/
    ```

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **Certificat (Base64)** , puis sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

1. Dans la section **Configurer Carbonite Endpoint Backup**, copiez la ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en accordant l’accès à Carbonite Endpoint Backup.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Carbonite Endpoint Backup**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-carbonite-endpoint-backup-sso"></a>Configurer l’authentification unique de Carbonite Endpoint Backup

1. Pour automatiser la configuration dans Carbonite Endpoint Backup, vous devez installer l’**extension de navigateur My Apps Secure Sign-in** en cliquant sur **Installer l’extension**.

    ![Extension My apps](common/install-myappssecure-extension.png)

2. Après avoir ajouté l’extension au navigateur, cliquez sur **Installer Carbonite Endpoint Backup** pour être redirigé vers l’application Carbonite Endpoint Backup. Indiquez ici les informations d’identification de l’administrateur pour vous connecter à Carbonite Endpoint Backup. Cette extension de navigateur configure automatiquement l’application pour vous et automatise les étapes 3 à 7.

    ![Configuration](common/setup-sso.png)

3. Si vous voulez configurer manuellement Carbonite Endpoint Backup, ouvrez une nouvelle fenêtre de navigateur web, connectez-vous à votre site d’entreprise Carbonite Endpoint Backup en tant qu’administrateur et procédez comme suit :

4. Cliquez sur **Company** dans le volet gauche.

    ![Capture d’écran montrant Carbonite Endpoint avec Company sélectionné.](media/carbonite-endpoint-backup-tutorial/company.png)

5. Cliquez sur **Single sign-on** (Authentification unique).

    ![Capture d’écran montrant Company avec Single sign-on sélectionné.](media/carbonite-endpoint-backup-tutorial/single-sign-on.png)

6. Cliquez sur **Enable** (Activer), puis sur **Edit settings** (Modifier les paramètres) pour configurer.

    ![Capture d’écran montrant l’onglet Single sign-on avec Enable et Edit settings sélectionnés.](media/carbonite-endpoint-backup-tutorial/settings.png)

7. Dans la page des paramètres **Single Sign on** (Authentification unique), effectuez les étapes suivantes :

    ![Capture d’écran montrant l’onglet Single sign-on avec les informations décrites à cette étape.](media/carbonite-endpoint-backup-tutorial/save.png)

    1. Dans la zone de texte **Identity provider URL** (ID du fournisseur d’identité), collez la valeur de **Identificateur Azure AD** que vous avez copiée à partir du portail Azure.

    1. Dans la zone de texte **Identity Provider Login URL** (URL de connexion du fournisseur d’identité), collez la valeur de l’**URL de connexion** que vous avez copiée à partir du portail Azure.

    1. Cliquez sur **Choose file** (Choisir un fichier) pour charger le fichier **Certificate (Base64)** que vous avez téléchargé depuis le portail Azure.

    1. Cliquez sur **Enregistrer**.

### <a name="create-carbonite-endpoint-backup-test-user"></a>Créer un utilisateur de test Carbonite Endpoint Backup

1. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Carbonite Endpoint Backup en tant qu’administrateur.

1. Cliquez sur **Users** dans le volet gauche, puis sur **Add user**.

    ![Capture d’écran montrant la page Carbonite Endpoint avec Users et Add users sélectionnés.](media/carbonite-endpoint-backup-tutorial/add-user-1.png)

1. Dans la page **Add user**, effectuez les étapes suivantes :

    ![Capture d’écran montrant la page Add user, où vous pouvez effectuer les étapes décrites ici.](media/carbonite-endpoint-backup-tutorial/add-user-2.png)

    1. Entrez les informations **Email**, **First name** (Prénom) et **Last name** (Nom) de l’utilisateur, puis fournissez les autorisations exigées pour l’utilisateur en fonction des exigences de l’organisation.

    1. Cliquez sur **Add User**.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

#### <a name="sp-initiated"></a>Lancée par le fournisseur de services :

* Cliquez sur **Tester cette application** dans le portail Azure. Une redirection est effectuée vers l’URL de connexion à Carbonite Endpoint Backup, où vous pouvez lancer le flux de connexion.  

* Accédez directement à l’URL de connexion à Carbonite Endpoint Backup, puis lancez le flux de connexion à partir de cet emplacement.

#### <a name="idp-initiated"></a>Lancée par le fournisseur d’identité :

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors connecté automatiquement à l’instance de Carbonite Endpoint Backup pour laquelle vous avez configuré l’authentification SSO. 

Vous pouvez aussi utiliser Mes applications de Microsoft pour tester l’application dans n’importe quel mode. Si, quand vous cliquez sur la vignette Carbonite Endpoint Backup dans Mes applications, le mode Fournisseur de services est configuré, vous êtes redirigé vers la page de connexion de l’application pour lancer le flux de connexion ; s’il s’agit du mode Fournisseur d’identité, vous êtes automatiquement connecté à l’instance de Carbonite Endpoint Backup pour laquelle vous avez configuré l’authentification SSO. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez configuré Carbonite Endpoint Backup, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
