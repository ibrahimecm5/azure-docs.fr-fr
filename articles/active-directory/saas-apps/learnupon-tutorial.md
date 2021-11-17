---
title: 'Didacticiel : Intégration d’Azure Active Directory à LearnUpon | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et LearnUpon.
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
ms.openlocfilehash: ba3b6e9eb68ff7365e3037d61463f7e5c8681cb3
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132344538"
---
# <a name="tutorial-azure-active-directory-integration-with-learnupon"></a>Didacticiel : intégration d’Azure Active Directory à LearnUpon

Dans ce tutoriel, vous allez apprendre à intégrer LearnUpon à Azure Active Directory (Azure AD). Lorsque vous intégrez LearnUpon à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à LearnUpon.
* Permettre à vos utilisateurs de se connecter automatiquement à LearnUpon avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à LearnUpon, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un [compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement LearnUpon pour lequel l'authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* LearnUpon prend en charge l'authentification unique lancée par le **fournisseur d'identité**.

* LearnUpon prend en charge l'approvisionnement d'utilisateurs **juste-à-temps**.

> [!NOTE]
> L’identificateur de cette application étant une valeur de chaîne fixe, une seule instance peut être configurée dans un locataire.

## <a name="add-learnupon-from-the-gallery"></a>Ajouter LearnUpon à partir de la galerie

Pour configurer l’intégration de LearnUpon à Azure AD, vous devez ajouter LearnUpon de la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, entrez **LearnUpon** dans la zone de recherche.
1. Sélectionnez **LearnUpon** dans le volet de résultats, puis ajoutez l'application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-learnupon"></a>Configurer et tester l'authentification unique Azure AD pour LearnUpon

Configurez et testez l'authentification unique Azure AD auprès de LearnUpon pour un utilisateur de test appelé **B.Simon**. Pour que l'authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l'utilisateur associé dans LearnUpon.

Pour configurer et tester l'authentification unique Azure AD auprès de LearnUpon, suivez les étapes ci-dessous :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l'authentification unique LearnUpon](#configure-learnupon-sso)** pour configurer les paramètres de l'authentification unique côté application.
    1. **[Créer un utilisateur de test LearnUpon](#create-learnupon-test-user)** pour avoir, dans LearnUpon, un équivalent de B.Simon lié à la représentation Azure AD de l'utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Sur le portail Azure, accédez à la page d'intégration de l'application **LearnUpon**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://<companyname>.learnupon.com/saml/consumer`

    > [!NOTE]
    > Cette valeur n’est pas la valeur réelle. Mettez à jour la valeur avec l’URL de réponse réelle. Pour obtenir cette valeur, contactez [l’équipe du support technique de LearnUpon](https://www.learnupon.com/contact/). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

5. Dans la page **Configurer l’authentification unique avec SAML**, recherchez l’**empreinte**, qui sera ajoutée à vos paramètres SAML LearnUpon.

    ![Lien Téléchargement de certificat](common/certificateraw.png)

6. Dans la section **Configurer LearnUpon**, copiez la ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez permettre à B.Simon d'utiliser l'authentification unique Azure en lui accordant l'accès à LearnUpon.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **LearnUpon**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-learnupon-sso"></a>Configurer l'authentification unique pour LearnUpon

1. Ouvrez une autre instance du navigateur et connectez-vous à LearnUpon avec un compte d’administrateur.

1. Cliquez sur l’onglet **Paramètres** .

    ![Capture d’écran montrant l’onglet des paramètres.](./media/learnupon-tutorial/settings.png)

1. Cliquez sur **Authentification unique - SAML**, puis cliquez sur **Paramètres généraux** pour configurer les paramètres SAML.
   
    ![Capture d’écran montrant l’élément Single Sign On - SAML sélectionné ainsi que l’élément General Settings.](./media/learnupon-tutorial/general-settings.png) 

1. Dans la section **Paramètres généraux** procédez comme suit :
   
    ![Capture d’écran montrant la section General Settings dans laquelle vous pouvez entrer les valeurs décrites.](./media/learnupon-tutorial/values.png)  
  
    a. Sélectionnez **Enabled**.

    b. Sélectionnez **2.0** pour **Version**.

    c. Sélectionnez **Non** pour **Skip conditions** (Ignorer les conditions).

    d. Dans la zone de texte **SAML Token POST param name** (Nom de paramètre POST du jeton SAML), entrez le nom du paramètre de publication de demande correspondant à l’URL de consommateur SAML indiquée ci-dessus, qui contient l’assertion SAML à vérifier et à authentifier. Par exemple, **SAMLResponse**.

    e. Dans la zone de texte **Format de l’identificateur du nom**, entrez la valeur indiquant à quel emplacement dans votre assertion SAML réside l’identificateur de l’utilisateur (adresse e-mail), par exemple, `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`.
  
    f. Dans la zone de texte **Identify Provider Location** (Identifier l’emplacement du fournisseur), entrez la valeur indiquant l’emplacement vers lequel sont envoyés les utilisateurs lorsqu’ils cliquent sur l’icône de téléchargement à partir de l’écran de connexion au portail Azure.
  
    g. Dans la zone de texte **URL de déconnexion**, collez l’**URL de déconnexion** que vous avez copiée sur le portail Azure.

    h. Cliquez sur **Manage finger prints**(Gérer les empreintes), puis téléchargez l’empreinte du certificat téléchargé.

1. Cliquez sur **Paramètres utilisateur**, puis procédez comme suit :

     ![Capture d’écran montrant la section User Settings dans laquelle vous pouvez entrer les valeurs décrites.](./media/learnupon-tutorial/user-settings.png)  

    a. Dans la zone de texte **First Name Identifier Format** (Format de l’identificateur du prénom), entrez la valeur indiquant à quel emplacement dans votre assertion SAML réside le prénom des utilisateurs, par exemple : `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.
  
    b. Dans la zone de texte **Last Name Identifier Format** (Format de l’identificateur du nom), entrez la valeur indiquant à quel emplacement dans votre assertion SAML réside le nom des utilisateurs, par exemple : `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`.

### <a name="create-learnupon-test-user"></a>Créer un utilisateur de test LearnUpon

Dans cette section, un utilisateur appelé Britta Simon est créé dans LearnUpon. LearnUpon prend en charge l’approvisionnement d’utilisateurs juste-à-temps, une option activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas encore d’utilisateur dans LearnUpon, il en est créé un après l’authentification. Si vous devez créer un utilisateur manuellement, contactez [l’équipe de support LearnUpon](https://www.learnupon.com/contact/).

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes.

* Cliquez sur Tester cette application sur le portail Azure. Vous êtes alors automatiquement connecté à l'instance de LearnUpon pour laquelle vous avez configuré l'authentification unique.

* Vous pouvez utiliser Mes applications de Microsoft. Lorsque vous cliquez sur la vignette LearnUpon dans Mes applications, vous devez être automatiquement connecté à l'application LearnUpon pour laquelle vous avez configuré l'authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré LearnUpon , vous pouvez appliquer le contrôle de session, qui protège en temps réel contre l'exfiltration et l'infiltration des données sensibles de votre organisation. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
