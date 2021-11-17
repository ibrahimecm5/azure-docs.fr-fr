---
title: 'Didacticiel : Intégration d’Azure Active Directory dans Asana | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Asana.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/27/2021
ms.author: jeedes
ms.openlocfilehash: 11ac0e3496a42e5e2318111dc96fd51881e35def
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132288572"
---
# <a name="tutorial-azure-active-directory-integration-with-asana"></a>Didacticiel : Intégration d’Azure Active Directory dans Asana

Dans ce tutoriel, vous allez découvrir comment intégrer Asana à Azure Active Directory (Azure AD). Quand vous intégrez Asana à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Asana.
* Permettre à vos utilisateurs de se connecter automatiquement à Asana avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Asana pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Asana prend en charge l’authentification unique (SSO) initiée par le **fournisseur de services**

* Asana prend en charge [**l’attribution d’utilisateurs** automatisée](asana-provisioning-tutorial.md)

## <a name="add-asana-from-the-gallery"></a>Ajouter Asana à partir de la galerie

Pour configurer l’intégration d’Asana dans Azure AD, vous devez ajouter Asana à partir de la galerie dans votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Asana** dans la zone de recherche.
1. Sélectionnez **Asana** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-asana"></a>Configurer et tester l’authentification unique Azure AD pour Asana

Configurez et testez l’authentification unique Azure AD avec Asana à l’aide d’un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Asana associé.

Pour configurer et tester l’authentification unique Azure AD avec Asana, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Asana](#configure-asana-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test Asana](#create-asana-test-user)** pour disposer, dans Asana, d’un équivalent de B.Simon lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, dans la page d’intégration de l’application **Asana**, recherchez la section **Gérer**, puis sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon pour **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL Asana](common/sp-identifier.png)

    a. Dans la zone de texte **URL de connexion**, tapez l’URL : `https://app.asana.com/`

    b. Dans la zone de texte **Identificateur (ID d’entité)** , tapez l’URL suivante : `https://app.asana.com/`

5. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (Base64)** en fonction des options définies par rapport à vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

6. Dans la section **Configurer Asana**, copiez la ou les URL appropriées, selon vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Asana.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Asana**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

### <a name="configure-asana-sso"></a>Configurer l’authentification unique Asana

1. Dans une autre fenêtre de navigateur, connectez-vous à votre application Asana. Pour configurer l’authentification unique dans Asana, accédez aux paramètres de l’espace de travail en cliquant sur son nom dans le coin supérieur droit de l’écran. Ensuite, cliquez sur **Paramètres \<your workspace name\>** .

    ![Paramètre d’authentification unique Asana](./media/asana-tutorial/settings.png)

2. Dans la fenêtre **Paramètres de l’organisation**, cliquez sur **Administration**. Ensuite, cliquez sur **Members must log in via SAML** (Les membres doivent se connecter via SAML) pour configurer l’authentification unique. Puis, procédez comme suit :

    ![Configurer les paramètres d’authentification unique](./media/asana-tutorial/save.png)  

    a. Dans la zone de texte **URL de la page de connexion**, collez la valeur **URL de connexion**.

    b. Cliquez avec le bouton droit sur le certificat téléchargé dans le portail Azure, puis ouvrez le fichier dans le Bloc-notes ou tout autre éditeur de texte. Copiez le texte situé entre le début et la fin du titre du certificat, puis collez-le dans la zone de texte **Certificat X.509**.

3. Cliquez sur **Enregistrer**. Consultez le [Guide Asana pour configurer l’authentification unique](https://asana.com/guide/help/premium/authentication#gl-saml) si vous avez besoin d’aide.

### <a name="create-asana-test-user"></a>Créer un utilisateur de test Asana

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans Asana. Asana prend en charge l’attribution automatique d’utilisateurs, qui est activée par défaut. Vous trouverez plus d’informations [ici](asana-provisioning-tutorial.md) sur la façon de configurer l’attribution automatique d’utilisateurs.

**Si vous avez besoin de créer un utilisateur manuellement, procédez comme suit :**

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans Asana.

1. Dans **Asana**, accédez à la section **Teams** (Équipes) dans le volet gauche. Cliquez sur le signe plus.

    ![Création d’un utilisateur de test Azure AD](./media/asana-tutorial/teams.png)

2. Tapez l’e-mail de l’utilisateur, par exemple **britta.simon\@contoso.com**, dans la zone de texte, puis sélectionnez **Inviter**.

3. Cliquez sur **Send Invite**(Envoyer l’invitation). Le nouvel utilisateur recevra un e-mail dans son compte e-mail. Il devra créer et valider le compte.

### <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion à Asana, d’où vous pouvez lancer le flux de connexion. 

* Accédez directement à l’URL de connexion à Asana pour lancer le flux de connexion.

* Vous pouvez utiliser Mes applications de Microsoft. Un clic sur la vignette Asana dans Mes applications vous redirige vers l’URL de connexion à Asana. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré Asana, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
