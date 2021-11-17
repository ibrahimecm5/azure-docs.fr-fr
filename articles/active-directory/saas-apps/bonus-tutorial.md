---
title: 'Didacticiel : Intégration d’Azure Active Directory avec Bonusly | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Bonusly.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/27/2021
ms.author: jeedes
ms.openlocfilehash: 07d8e90949b6902f89379e501850fcee3dea68b9
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132292301"
---
# <a name="tutorial-azure-active-directory-integration-with-bonusly"></a>Didacticiel : Intégration d’Azure Active Directory avec Bonusly

Dans ce tutoriel, vous allez apprendre à intégrer Bonusly à Azure Active Directory (Azure AD). Quand vous intégrez Bonusly à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Bonusly.
* Permettre à vos utilisateurs de se connecter automatiquement à Bonusly avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD avec Bonusly, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un [compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Bonusly pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Bonusly prend en charge l’authentification unique initiée par le **fournisseur d’identité**.
* Bonusly prend en charge l’[attribution automatique d’utilisateurs](bonusly-provisioning-tutorial.md).

> [!NOTE]
> L’identificateur de cette application étant une valeur de chaîne fixe, une seule instance peut être configurée dans un locataire.

## <a name="add-bonusly-from-the-gallery"></a>Ajouter Bonusly à partir de la galerie

Pour configurer l’intégration de Bonusly dans Azure AD, vous devez ajouter Bonusly à votre liste d’applications SaaS gérées à partir de la galerie.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, saisissez **Bonusly** dans la zone de recherche.
1. Sélectionnez **Bonusly** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-bonusly"></a>Configurer et tester l’authentification unique Azure AD pour Bonusly

Configurez et testez l’authentification unique Azure AD avec Bonusly à l’aide d’un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur associé dans Bonusly.

Pour configurer et tester l’authentification unique Azure AD avec Bonusly, procédez comme suit :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique de Bonusly](#configure-bonusly-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test Bonusly](#create-bonusly-test-user)** pour avoir un équivalent de B.Simon dans Bonusly lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **Bonusly**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, effectuez l’étape suivante :

    Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://Bonus.ly/saml/<TENANT_NAME>`

    > [!NOTE]
    > Cette valeur n’est pas la valeur réelle. Mettez à jour la valeur avec l’URL de réponse réelle. Pour obtenir cette valeur, contactez l’[équipe du support technique Bonusly](https://bonus.ly/contact). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

5. Dans la section **Certificat de signature SAML**, cliquez sur le bouton **Modifier** pour ouvrir la boîte de dialogue **Certificat de signature SAML**.

    ![Modifier le certificat de signature SAML](common/edit-certificate.png)

6. Dans la section **Certificat de signature SAML**, copiez l’**EMPREINTE** et enregistrez-la sur votre ordinateur.

    ![Copier la valeur de l’empreinte](common/copy-thumbprint.png)

7. Dans la section **Configurer Bonusly**, copiez la ou les URL appropriées, selon vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Bonusly.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Bonusly**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-bonusly-sso"></a>Configurer l’authentification unique de Bonusly

1. Dans une autre fenêtre de navigateur, connectez-vous à votre locataire **Bonusly**.

1. Dans la barre d’outils située en haut, cliquez sur **Settings** (Paramètres), puis sélectionnez **Integrations and apps** (Intégrations et applications).

    ![Section sociale Bonusly](./media/bonus-tutorial/settings.png "Bonus.ly")
1. Sous **Single Sign-On**, sélectionnez **SAML**.

1. Dans la page de boîte de dialogue **SAML** , procédez comme suit :

    ![Page de boîte de dialogue Saml Bonusly](./media/bonus-tutorial/dialog-page.png "Bonus.ly")

    a. Dans la zone de texte **IdP SSO target URL** (URL cible d’authentification unique IdP), collez la valeur **URL de connexion**, que vous avez copiée à partir du portail Azure.

    b. Dans la zone de texte **IdP Login URL** (URL de connexion IdP), collez la valeur **URL de connexion** que vous avez copiée dans le portail Azure.

    c. Dans la zone de texte **IdP Issuer** (Émetteur IdP), collez la valeur **Identificateur Azure AD** que vous avez copiée à partir du portail Azure.
    
    d. Copiez la valeur située sous **Empreinte** dans le portail Azure, puis collez-la dans la zone de texte **Empreinte du certificat**.
    
    e. Cliquez sur **Enregistrer**.

### <a name="create-bonusly-test-user"></a>Créer un utilisateur de test Bonusly

Pour permettre aux utilisateurs Azure AD de se connecter à Bonusly, vous devez les provisionner dans Bonusly. En l’occurrence, cet approvisionnement est une tâche manuelle.

> [!NOTE]
> Vous pouvez utiliser tout autre outil ou n’importe quelle API de création de compte d’utilisateur fournis par Bonusly pour provisionner des comptes d’utilisateurs Azure AD. 

**Pour configurer l'approvisionnement des utilisateurs, procédez comme suit :**

1. Dans une fenêtre de navigateur web, connectez-vous à votre locataire Bonusly.

1. Cliquez sur **Settings**.

    ![Paramètres](./media/bonus-tutorial/users.png "Paramètres")

1. Cliquez sur l’onglet **Users and bonuses** .

    ![Users and bonuses](./media/bonus-tutorial/manage-user.png "Users and bonuses") (Utilisateurs et bonus)

1. Cliquez sur **Manage Users**.

    ![Gérer les utilisateurs](./media/bonus-tutorial/new-users.png "Manage Users")

1. Cliquez sur **Add User**.

    ![Capture d’écran montrant Manage Users où vous pouvez sélectionner Add User.](./media/bonus-tutorial/add-tab.png "Ajouter un utilisateur")

1. Dans la boîte de dialogue **Ajouter un utilisateur**, procédez comme suit :

    ![Capture d’écran montrant la boîte de dialogue Add User où vous pouvez entrer ces informations.](./media/bonus-tutorial/select-user.png "Ajouter un utilisateur")  

    a. Dans la zone de texte **First name**, entrez le prénom de l’utilisateur, par exemple **Britta**.

    b. Dans la zone de texte **Last name**, tapez le nom de l’utilisateur, par exemple **Simon**.

    c. Dans la zone de texte **E-mail**, entrez l’adresse e-mail de l’utilisateur, par exemple `brittasimon@contoso.com`.

    d. Cliquez sur **Enregistrer**.

    > [!NOTE]
    > Le titulaire du compte Azure AD reçoit alors un e-mail contenant un lien pour confirmer le compte avant qu’il ne soit activé.

> [!NOTE]
>Bonusly prend aussi en charge l’attribution automatique d’utilisateurs. Vous trouverez [ici](./bonusly-provisioning-tutorial.md) plus d’informations sur la façon de configurer l’attribution automatique d’utilisateurs.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes.

* Cliquez sur Tester cette application dans Portail Azure : vous devez être connecté automatiquement à l’instance de Bonusly pour laquelle vous avez configuré l’authentification unique.

* Vous pouvez utiliser Mes applications de Microsoft. Quand vous cliquez sur la vignette Bonusly dans Mes applications, vous êtes automatiquement connecté à l’instance de Bonusly pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez configuré Bonusly, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
