---
title: 'Didacticiel : Intégration d’Azure Active Directory avec Workfront | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Workfront.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/23/2021
ms.author: jeedes
ms.openlocfilehash: 5cf9ff5a847203369c3ade70bd22df4cdb9bc062
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132298418"
---
# <a name="tutorial-azure-active-directory-integration-with-workfront"></a>Didacticiel : Intégration d’Azure Active Directory avec Workfront

Dans ce tutoriel, vous allez apprendre à intégrer Workfront à Azure Active Directory (Azure AD). Quand vous intégrez Workfront à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Workfront.
* Permettre à vos utilisateurs de se connecter automatiquement à Workfront avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à Workfront, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un [compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement à Workfront pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Workfront prend en charge l’authentification unique lancée par le **fournisseur de services**.

## <a name="add-workfront-from-the-gallery"></a>Ajouter Workfront à partir de la galerie

Pour configurer l’intégration de Workfront à Azure AD, vous devez ajouter Workfront à partir de la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Workfront** dans la zone de recherche.
1. Sélectionnez **Workfront** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-workfront"></a>Configurer et tester l’authentification unique Azure AD pour Workfront

Configurez et testez l’authentification unique Azure AD avec Workfront à l’aide d’un utilisateur de test nommé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Workfront associé.

Pour configurer et tester l’authentification unique Azure AD avec Workfront, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Workfront](#configure-workfront-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test Workfront](#create-workfront-test-user)** pour avoir un équivalent de B.Simon dans Workfront lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **Workfront**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    a. Dans la zone de texte **URL de connexion**, saisissez une URL au format suivant : `https://<companyname>.attask-ondemand.com`

    b. Dans la zone de texte **Identificateur (ID d’entité)** , saisissez une URL au format suivant : `https://<companyname>.attasksandbox.com/SAML2`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Pour obtenir ces valeurs, contactez l’[équipe de support technique Workfront](https://www.workfront.com/services-and-support). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

5. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (Base64)** en fonction des options définies par rapport à vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

6. Dans la section **Configurer Workfront**, copiez la ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Workfront.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Workfront**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-workfront-sso"></a>Configurer l’authentification unique Workfront

1. Connectez-vous à votre site d’entreprise Workfront en tant qu’administrateur.

2. Accédez à **Single Sign On Configuration**.

3. Dans la boîte de dialogue **Authentification unique** , procédez comme suit :
    
    ![Configure Single Sign-On](./media/workfront-tutorial/single-sign-on.png)
   
    a. Comme **Type**, sélectionnez **SAML 2.0**.
   
    b. Sélectionnez **///ID fournisseur de services**.
   
    c. Collez la valeur de l’**URL de connexion** dans la zone de texte **URL du portail de connexion**.
   
    d. Collez la valeur du champ **URL de déconnexion** dans la zone de texte **URL de déconnexion**.
   
    e. Collez l’**URL de modification du mot de passe** dans la zone de texte **URL de modification du mot de passe**.
   
    f. Cliquez sur **Enregistrer**.

### <a name="create-workfront-test-user"></a>Créer un utilisateur de test Workfront

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans Workfront.

**Pour créer un utilisateur appelé Britta Simon dans Workfront, effectuez les étapes suivantes :**

1. Connectez-vous à votre site d’entreprise Workfront en tant qu’administrateur.
 
2. Dans le menu situé en haut, cliquez sur **Utilisateurs**.
 
3. Cliquez sur **New Person**. 

4. Dans la boîte de dialogue New User, procédez comme suit :
   
    ![Créer un utilisateur de test Workfront](./media/workfront-tutorial/add-person.png)
   
    a. Dans la zone de texte **Prénom**, tapez « Britta ».
   
    b. Dans la zone de texte **Nom**, tapez « Simon ».
   
    c. Dans la zone de texte **Adresse de messagerie** , tapez l'adresse de messagerie de Simon Britta dans Azure Active Directory.
   
    d. Cliquez sur **Add Person**.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion à Workfront où vous pouvez lancer le flux de connexion. 

* Accédez directement à l’URL de connexion à Workfront pour lancer le flux de connexion.

* Vous pouvez utiliser Mes applications de Microsoft. Un clic sur la vignette Workfront dans Mes applications vous redirige vers l’URL de connexion à Workfront. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré Workfront, vous pouvez appliquer le contrôle de session, qui protège en temps réel contre l’exfiltration et l’infiltration des données sensibles de votre organisation. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
