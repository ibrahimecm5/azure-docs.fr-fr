---
title: 'Tutoriel : Intégration de l’authentification unique Azure AD à TalentLMS'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et TalentLMS.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/14/2021
ms.author: jeedes
ms.openlocfilehash: 1d9d9adec6c8b9db9429cebba29fdf4a7e5a304f
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132282647"
---
# <a name="tutorial-azure-ad-sso-integration-with-talentlms"></a>Tutoriel : Intégration de l’authentification unique Azure AD à TalentLMS

Dans ce tutoriel, vous allez apprendre à intégrer TalentLMS à Azure Active Directory (Azure AD). Quand vous intégrez TalentLMS à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à TalentLMS.
* Permettre à vos utilisateurs de se connecter automatiquement à TalentLMS avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à TalentLMS, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un [compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement TalentLMS pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* TalentLMS prend en charge l’authentification unique lancée par le **fournisseur de services**.

## <a name="add-talentlms-from-the-gallery"></a>Ajouter TalentLMS à partir de la galerie

Pour configurer l’intégration de TalentLMS à Azure AD, vous devez ajouter TalentLMS à partir de la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **TalentLMS** dans la zone de recherche.
1. Sélectionnez **TalentLMS** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-talentlms"></a>Configurer et tester l’authentification unique Azure AD pour TalentLMS

Configurez et testez l’authentification unique Azure AD avec TalentLMS à l’aide d’un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur TalentLMS associé.

Pour configurer et tester l’authentification unique Azure AD avec TalentLMS, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique TalentLMS](#configure-talentlms-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test TalentLMS](#create-talentlms-test-user)** pour avoir dans TalentLMS un équivalent de B.Simon lié à la représentation Azure AD associée.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, dans la page d’intégration de l’application **TalentLMS**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    a. Dans la zone de texte **Identificateur (ID d’entité)** , saisissez une URL au format suivant : `http://<tenant-name>.talentlms.com`

    b. Dans la zone de texte **URL de connexion**, saisissez une URL au format suivant : `https://<tenant-name>.TalentLMSapp.com`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur et l’URL de connexion réels. Pour obtenir ces valeurs, contactez l’[équipe de support client de TalentLMS](https://www.talentlms.com/contact). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

5. Dans la section **Certificat de signature SAML**, cliquez sur le bouton **Modifier** pour ouvrir la boîte de dialogue **Certificat de signature SAML**.

    ![Modifier le certificat de signature SAML](common/edit-certificate.png)

6. Dans la section **Certificat de signature SAML**, copiez l’**EMPREINTE** et enregistrez-la sur votre ordinateur.

    ![Copier la valeur de l’empreinte](common/copy-thumbprint.png)

7. Dans la section **Configurer TalentLMS**, copiez l’URL ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à TalentLMS.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **TalentLMS**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-talentlms-sso"></a>Configurer l’authentification unique TalentLMS

1. Dans une autre fenêtre de navigateur web, connectez-vous au site de votre entreprise TalentLMS en tant qu’administrateur.

1. Dans la section **Account & Settings (Compte et paramètres)**, cliquez sur l’onglet **Users (Utilisateurs)**.

    ![Accounts & Setting](./media/talentlms-tutorial/user.png "Account & Settings") (Comptes et paramètres)

1. Cliquez sur **Authentification unique (SSO)**.

1. Dans la section Single Sign-On, procédez comme suit :

    ![Authentification unique](./media/talentlms-tutorial/certificate.png "Authentification unique")

    a. Dans la liste **Type d'intégration SSO**, sélectionnez **SAML 2.0**.

    b. Dans la zone de texte **Identity provider (IDP)** (Fournisseur d’identité IDP), collez la valeur **Azure AD Identifier** (Identificateur Azure AD) que vous avez copiée à partir du portail Azure.

    c. Collez la valeur de l’**empreinte** à partir du portail Azure dans la zone de texte **Empreinte du certificat**.

    d.  Dans la zone de texte **Remote sign-in URL** (URL de connexion à distance), collez l’**URL de connexion** que vous avez copiée à partir du portail Azure.

    e. Dans la zone de texte **Remote sign-out URL** (URL de déconnexion à distance), collez l’**URL de déconnexion** que vous avez copiée à partir du portail Azure.

    f. Renseignez les informations suivantes :

    * Dans la zone de texte **TargetedID**, tapez `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`

    * Dans la zone de texte **Prénom**, tapez `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

    * Dans la zone de texte **Nom**, tapez `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`

    * Dans la zone de texte **Email**, tapez `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

1. Cliquez sur **Enregistrer**.

### <a name="create-talentlms-test-user"></a>Créer un utilisateur de test TalentLMS

Pour permettre aux utilisateurs Azure AD de se connecter à TalentLMS, vous devez les provisionner dans TalentLMS. Dans le cas de TalentLMS, l’approvisionnement est une tâche manuelle.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous à votre locataire **TalentLMS**.

1. Cliquez sur **Utilisateurs**, puis sur **Ajouter un utilisateur**.

1. Dans la page de boîte de dialogue **Ajouter un utilisateur**, procédez comme suit :

    ![Ajouter un utilisateur](./media/talentlms-tutorial/account.png "Ajouter un utilisateur")  

    a. Dans la zone de texte **First Name**, entrez le prénom de l’utilisateur, par exemple `Britta`.

    b. Dans la zone de texte **Last name**, tapez le nom de l’utilisateur, par exemple `Simon`.
 
    c. Dans la zone de texte **Email**, entrez l’adresse e-mail de l’utilisateur, par exemple `brittasimon@contoso.com`.

    d. Cliquez sur **Add User**.

> [!NOTE]
> Vous pouvez utiliser tout autre outil ou n’importe quelle API de création de compte d’utilisateur fournis par TalentLMS pour provisionner des comptes d’utilisateurs Azure AD.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes redirigé vers l’URL de connexion à TalentLMS, à partir de laquelle vous pouvez lancer le flux de connexion. 

* Accédez directement à l’URL de connexion à TalentLMS pour lancer le processus de connexion.

* Vous pouvez utiliser Mes applications de Microsoft. Quand vous cliquez sur la vignette TalentLMS dans Mes applications, vous êtes redirigé vers l’URL de connexion à TalentLMS. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez configuré TalentLMS, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
