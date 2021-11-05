---
title: 'Tutoriel : Intégration d’Azure Active Directory à ClickUp Productivity Platform | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et ClickUp Productivity Platform.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/30/2021
ms.author: jeedes
ms.openlocfilehash: bf21fbec454dc16e00c67bb9885e5694ad73ed57
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131059037"
---
# <a name="tutorial-azure-active-directory-integration-with-clickup-productivity-platform"></a>Tutoriel : Intégration d’Azure Active Directory à ClickUp Productivity Platform

Dans ce tutoriel, vous allez apprendre à intégrer ClickUp Productivity Platform à Azure Active Directory (Azure AD). En intégrant ClickUp Productivity Platform à Azure AD, vous pouvez :

* Utiliser Azure AD pour contrôler qui a accès à ClickUp Productivity Platform.
* Permettre à vos utilisateurs d'être automatiquement connectés à ClickUp Productivity Platform avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement ClickUp Productivity Platform pour lequel l'authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* ClickUp Productivity Platform prend en charge l'authentification unique initiée par le **fournisseur de services**.

## <a name="add-clickup-productivity-platform-from-the-gallery"></a>Ajouter ClickUp Productivity Platform depuis la galerie

Pour configurer l’intégration de ClickUp Productivity Platform à Azure AD, vous devez ajouter ClickUp Productivity Platform, disponible depuis la galerie, à votre liste d’applications SaaS managées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Entrez **ClickUp Productivity Platform** dans la zone de recherche de la section **Ajouter à partir de la galerie**.
1. Sélectionnez **ClickUp Productivity Platform** dans le volet de résultats, puis ajoutez l'application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-clickup-productivity-platform"></a>Configurer et tester l'authentification unique Azure AD pour ClickUp Productivity Platform

Configurez et testez l'authentification unique Azure AD auprès de ClickUp Productivity Platform pour un utilisateur de test nommé **B.Simon**. Pour que l'authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l'utilisateur ClickUp Productivity Platform associé.

Pour configurer et tester l'authentification unique Azure AD auprès de ClickUp Productivity Platform, procédez comme suit :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l'authentification unique de ClickUp Productivity Platform](#configure-clickup-productivity-platform-sso)** pour configurer les paramètres de l'authentification unique côté application.
    1. **[Créer l'utilisateur de test ClickUp Productivity Platform](#create-clickup-productivity-platform-test-user)** pour avoir dans ClickUp Productivity Platform un équivalent de B.Simon lié à la représentation Azure AD de l'utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Sur le portail Azure, accédez à la page d'intégration de l'application **ClickUp Productivity Platform**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    a. Dans la zone de texte **URL de connexion**, tapez l’URL : `https://app.clickup.com/login/sso`

    b. Dans la zone de texte **Identificateur (ID d’entité)** , saisissez une URL au format suivant : `https://api.clickup.com/v1/team/<team_id>/microsoft`

    > [!NOTE]
    > La valeur de l'identificateur n'est pas réelle. Vous devez remplacer cette valeur par l’identificateur réel. La procédure à suivre est expliquée plus loin dans ce tutoriel.

5. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur le bouton Copier pour copier l’**URL des métadonnées de fédération d’application**, puis enregistrez-la sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/copy-metadataurl.png)

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

Dans cette section, vous allez autoriser B.Simon à utiliser l'authentification unique Azure en accordant l'accès à ClickUp Productivity Platform.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **ClickUp Productivity Platform**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-clickup-productivity-platform-sso"></a>Configurer l'authentification unique pour ClickUp Productivity Platform

1. Dans une autre fenêtre de navigateur web, connectez-vous à votre locataire ClickUp Productivity Platform en tant qu’administrateur.

2. Cliquez sur **User profile** (Profil utilisateur), puis sélectionnez **Settings** (Paramètres).

    ![Capture d’écran montrant le locataire ClickUp Productivity avec l’icône de paramètres sélectionnée.](./media/clickup-productivity-platform-tutorial/configure-0.png)

    ![Capture d’écran montrant les paramètres.](./media/clickup-productivity-platform-tutorial/configure-1.png)

3. Sélectionnez **Microsoft**, sous Single Sign-On (SSO) Provider (Fournisseur d’authentification unique (SSO)).

    ![Capture d’écran montrant le volet d’authentification avec Microsoft sélectionné.](./media/clickup-productivity-platform-tutorial/configure-2.png)

4. Dans la page **Configure Microsoft Single Sign On** (Configurer l’authentification unique de Microsoft), effectuez les étapes suivantes :

    ![Capture d’écran montrant la page Configure Microsoft Single Sign On, dans laquelle vous pouvez copier l’ID d’entité et enregistrer l’URL des métadonnées de fédération Azure.](./media/clickup-productivity-platform-tutorial/configure-3.png)

    a. Cliquez sur **Copy** (Copier) pour copier la valeur de l’ID d’entité et collez-la dans la zone de texte **Identificateur (ID d’entité)** de la section **Configuration SAML de base** du portail Azure.

    b. Dans la zone de texte **URL des métadonnées de la fédération Azur**, collez la valeur de l’URL des métadonnées de la fédération de l’application que vous avez copiée à partir du Portail Azure, puis cliquez sur **Enregistrer**.

5. Pour terminer l’installation, cliquez sur **Authenticate With Microsoft to complete setup** (S’authentifier auprès de Microsoft pour terminer l’installation) et authentifiez-vous avec un compte Microsoft.

    ![Capture d’écran montrant le bouton Authenticate With Microsoft to complete setup.](./media/clickup-productivity-platform-tutorial/configure-4.png)


### <a name="create-clickup-productivity-platform-test-user"></a>Créer l’utilisateur de test de ClickUp Productivity Platform

1. Dans une autre fenêtre de navigateur web, connectez-vous à votre locataire ClickUp Productivity Platform en tant qu’administrateur.

2. Cliquez sur **User profile** (Profil utilisateur), puis sélectionnez **People** (Personnes).

    ![Capture d’écran montrant le locataire ClickUp Productivity.](./media/clickup-productivity-platform-tutorial/configure-0.png)

    ![Capture d’écran montrant le lien People sélectionné.](./media/clickup-productivity-platform-tutorial/user-1.png)

3. Entrez l’adresse e-mail de l’utilisateur dans la zone de texte et cliquez sur **Invite** (Inviter).

    ![Capture d’écran montrant les paramètres des utilisateurs de l’équipe, où vous pouvez inviter des personnes par e-mail.](./media/clickup-productivity-platform-tutorial/user-2.png)

    > [!NOTE]
    > L’utilisateur reçoit la notification et doit accepter l’invitation pour activer le compte.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Cette opération redirige vers l'URL de connexion ClickUp Productivity Platform où vous pouvez lancer le flux de connexion. 

* Accédez directement à l'URL de connexion à ClickUp Productivity Platform pour lancer le flux de connexion à partir de là.

* Vous pouvez utiliser Mes applications de Microsoft. Lorsque vous cliquez sur la vignette de ClickUp Productivity Platform dans Mes applications, vous êtes redirigez vers l'URL de connexion à ClickUp Productivity Platform. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré ClickUp Productivity Platform, vous pouvez appliquer le contrôle de session, qui protège en temps réel contre l'exfiltration et l'infiltration des données sensibles de votre organisation. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
