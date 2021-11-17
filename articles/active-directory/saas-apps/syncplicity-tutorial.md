---
title: 'Didacticiel : Intégration d’Azure Active Directory à Syncplicity | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Syncplicity.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/21/2021
ms.author: jeedes
ms.openlocfilehash: 6cbb1a53d2cf32ce5b55d1cb304b3ae675ae43d8
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132298985"
---
# <a name="tutorial-integrate-syncplicity-with-azure-active-directory"></a>Tutoriel : Intégrer Syncplicity à Azure Active Directory

Dans ce didacticiel, vous allez apprendre à intégrer Syncplicity à Azure Active Directory (Azure AD). Quand vous intégrez Syncplicity à Azure AD, vous pouvez :

* Dans Azure AD, contrôlez qui a accès à Syncplicity.
* Permettre à vos utilisateurs de se connecter automatiquement à Syncplicity avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Abonnement Syncplicity pour lequel l’authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test. 
* Syncplicity prend en charge l’authentification unique lancée par le **fournisseur de services**.

## <a name="add-syncplicity-from-the-gallery"></a>Ajout de Syncplicity à partir de la galerie

Pour configurer l’intégration de Syncplicity à Azure AD, vous devez ajouter Syncplicity à partir de la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Sous **Créer**, cliquez sur **Application d’entreprise**.
1. Dans la section **Parcourir la galerie Azure AD**, tapez **Syncplicity** dans la zone de recherche.
1. Sélectionnez **Syncplicity** dans le volet de résultats, puis cliquez sur **Créer** pour ajouter l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-syncplicity"></a>Configurer et tester Azure AD SSO pour Syncplicity

Configurez et testez l’authentification unique Azure AD avec Syncplicity pour un utilisateur de test nommé **B. Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Syncplicity associé.

Pour configurer et tester Azure AD SSO avec Syncplicity, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Syncplicity](#configure-syncplicity-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test Syncplicity](#create-syncplicity-test-user)** pour avoir un équivalent de B. Simon dans Syncplicity lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.
1. **[Mettre à jour l’authentification unique](#update-sso)** pour apporter les modifications nécessaires dans Syncplicity, si vous avez modifié les paramètres d’authentification unique dans Azure AD.

### <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **Syncplicity**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    a. Dans la zone de texte **Identificateur (ID d’entité)** , saisissez une URL au format suivant : `https://<COMPANY_NAME>.syncplicity.com/sp`

    b. Dans la zone de texte **URL de connexion**, saisissez une URL au format suivant : `https://<COMPANY_NAME>.syncplicity.com`
    
    c. Dans la zone de texte **URL de réponse (URL Assertion Consumer Service)** , tapez une URL à l’aide du modèle suivant : `https://<COMPANY_NAME>.syncplicity.com/Auth/AssertionConsumerService.aspx`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de réponse, l’URL de connexion et l’identificateur réels. Pour obtenir ces valeurs, contactez [l’équipe de support technique Syncplicity](https://www.syncplicity.com/contact-us). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

5. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Modifier**. Ensuite, dans la boîte de dialogue, cliquez sur le bouton de sélection en regard de votre certificat actif, puis sélectionnez **Téléchargement du certificat PEM**.

   ![Lien Téléchargement de certificat](common/certificatebase64.png)

    > [!NOTE]
    > Vous avez besoin du certificat PEM, car Syncplicity n’accepte pas les certificats au format CER.

6. Dans la section **Configurer Syncplicity**, copiez la ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B. Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Syncplicity.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Syncplicity**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-syncplicity-sso"></a>Configurer l’authentification unique Syncplicity

1. Connectez-vous à votre client **Syncplicity**.

1. Dans le menu en haut, cliquez sur **Admin**, sélectionnez **Settings**, puis cliquez sur **Custom domain and single sign-on** (Domaine personnalisé et authentification unique).

    ![Syncplicity](./media/syncplicity-tutorial/admin.png "Syncplicity")

1. Dans la page de boîte de dialogue **Single Sign-On (SSO)**, procédez comme suit :

    ![Authentification unique \(SSO\)](./media/syncplicity-tutorial/configuration.png "Single Sign-On \\\(SSO\\\)")

    a. Dans la zone de texte **Custom Domain**, tapez le nom de votre domaine.
  
    b. Sélectionnez **Enabled** pour **Single Sign-On Status**.

    c. Dans la zone de texte **ID d’entité**, collez la valeur **Identificateur (ID d’entité)** que vous avez copiée dans la section **Configuration SAML de base** du portail Azure.

    d. Dans la zone de texte **Sign-in page URL**, collez **l’URL de connexion** que vous avez copiée à partir du portail Azure.

    e. Dans la zone de texte **Logout page URL**, collez l’**URL de déconnexion** que vous avez copiée à partir du portail Azure.

    f. Dans **Certificat du fournisseur d’identité**, cliquez sur **Choisir un fichier**, puis chargez le certificat que vous avez téléchargé à partir du portail Azure.

    g. Cliquez sur **ENREGISTRER LES MODIFICATIONS**.

### <a name="create-syncplicity-test-user"></a>Créer un utilisateur de test Syncplicity

Pour que les utilisateurs Azure AD puissent se connecter, ils doivent être provisionnés pour l’application Syncplicity. Cette section explique comment créer des comptes d’utilisateurs Azure AD dans Syncplicity.

**Pour approvisionner un compte d’utilisateur dans Syncplicity, procédez comme suit :**

1. Connectez-vous à votre locataire **Syncplicity** (par exemple : `https://company.Syncplicity.com`).

1. Cliquez sur **Admin** et sélectionnez **User Accounts**, puis cliquez sur **Add a User**.

    ![Gérer les utilisateurs](./media/syncplicity-tutorial/users.png "Manage Users")

1. Entrez l’**adresse e-mail** d’un compte Azure AD que vous souhaitez provisionner, sélectionnez **User** pour **Role**, puis cliquez sur **Next**.

    ![Informations sur le compte](./media/syncplicity-tutorial/roles.png "Informations sur le compte")

    > [!NOTE]
    > Le titulaire du compte Azure AD reçoit un e-mail contenant un lien pour confirmer et activer le compte.

1. Sélectionnez un groupe de votre société auquel le nouvel utilisateur doit appartenir, puis cliquez sur **Next**.

    ![Group Membership](./media/syncplicity-tutorial/group.png "Appartenance au groupe") (Appartenance aux groupes)

    > [!NOTE]
    > Si aucun groupe n’est listé, cliquez sur **Next**.

1. Sélectionnez les dossiers que vous souhaitez placer sous le contrôle de Syncplicity sur l'ordinateur de l'utilisateur, puis cliquez sur **Next**.

    ![Syncplicity Folders](./media/syncplicity-tutorial/folder.png "Dossiers Syncplicity") (Dossiers Syncplicity)

> [!NOTE]
> Vous pouvez utiliser tout autre outil ou n’importe quelle API de création de compte d’utilisateur fournis par Syncplicity pour provisionner des comptes d’utilisateurs Azure AD.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Cette opération redirige vers l’URL d’authentification Syncplicity, d’où vous pouvez lancer le processus de connexion. 

* Accédez directement à l’URL de connexion de Syncplicity pour lancer le flux de connexion.

* Vous pouvez utiliser Mes applications de Microsoft. Un clic sur la vignette Syncplicity dans Mes applications vous redirige vers l’URL de connexion Syncplicity. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

### <a name="update-sso"></a>Mettre à jour l’authentification unique

Chaque fois que vous devez apporter des modifications à l’authentification unique, vous devez vérifier le **certificat de signature SAML** utilisé. Si le certificat a changé, veillez à charger le nouveau sur Syncplicity comme décrit dans **[Configurer l’authentification unique Syncplicity](#configure-syncplicity-sso)** .

Si vous utilisez l’application mobile Syncplicity, contactez le support client de Syncplicity (support@syncplicity.com) pour obtenir de l’aide.

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré Syncplicity, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
