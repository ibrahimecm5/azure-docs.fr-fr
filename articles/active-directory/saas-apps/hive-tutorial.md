---
title: 'Tutoriel : Intégration de l’authentification unique (SSO) Azure Active Directory à Hive | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Hive.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/07/2021
ms.author: jeedes
ms.openlocfilehash: 2dc99eeda68a1401178b15a328307e10ed9fc994
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132311257"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-hive"></a>Tutoriel : Intégration de l’authentification unique (SSO) Azure Active Directory à Hive

Dans ce tutoriel, vous allez apprendre à intégrer Hive à Azure Active Directory (Azure AD). Quand vous intégrez Hive à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Hive.
* Permettre à vos utilisateurs de se connecter automatiquement à Hive avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Abonnement Hive pour lequel l’authentification unique (SSO) est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* Hive prend en charge l’authentification unique lancée par **le fournisseur de services et le fournisseur d’identité**.
* Hive prend en charge le provisionnement d’utilisateurs **juste-à-temps**.

> [!NOTE]
> L’identificateur de cette application étant une valeur de chaîne fixe, une seule instance peut être configurée dans un locataire.

## <a name="add-hive-from-the-gallery"></a>Ajouter Hive à partir de la galerie

Pour configurer l’intégration de Hive à Azure AD, vous devez ajouter Hive à votre liste d’applications SaaS gérées, à partir de la galerie.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Hive** dans la zone de recherche.
1. Sélectionnez **Hive** dans le panneau des résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-hive"></a>Configurer et tester l’authentification unique Azure AD pour Hive

Configurez et testez l’authentification unique (SSO) Azure AD avec Hive à l’aide d’un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique (SSO) fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur associé dans Hive.

Pour configurer et tester l’authentification unique Azure AD avec Hive, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique (SSO) Hive](#configure-hive-sso)** pour configurer les paramètres d’authentification unique côté application.
    1. **[Créer un utilisateur de test Hive](#create-hive-test-user)** pour avoir, dans Hive, un équivalent de B.Simon lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, dans la page d’intégration de l’application **Hive**, recherchez la section **Gérer**, puis sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, si vous souhaitez configurer l’application en mode lancé par le **fournisseur d’identité**, effectuez les étapes suivantes :

    a. Dans la zone de texte **Identificateur**, tapez une l’URL : `https://hive.com`

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://app.hive.com/sso/saml/${workspaceId}`

1. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://app.hive.com/sso/saml/${workspaceId}`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Remplacez ces valeurs par l’URL de réponse et l’URL de connexion réelles. `{workspaceId}` vous sera expliqué plus loin dans le tutoriel. Pour obtenir ces valeurs, contactez l’[équipe de support technique Hive](https://help.hive.com). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. Votre application Hive s’attend à recevoir les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à la configuration de vos attributs de jeton SAML. La capture d’écran suivante montre la liste des attributs par défaut.

    ![image](common/default-attributes.png)

1. En plus de ce qui précède, l’application Hive s’attend à ce que quelques attributs supplémentaires (présentés ci-dessous) soient retournés dans la réponse SAML. Ces attributs sont également préremplis, mais vous pouvez les examiner pour voir s’ils répondent à vos besoins.

    | Nom | Attribut source|
    | ------------ | --------- |
    | firstName | user.givenname |
    | lastName | user.surname |
    | email | user.mail |

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **Certificat (en base64)** , puis sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

1. Dans la section **Configurer Hive**, copiez la ou les URL appropriées, en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Hive.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Hive**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-hive-sso"></a>Configurer l’authentification unique de Hive

1. Dans une autre fenêtre de navigateur web, connectez-vous au site web Hive en tant qu’administrateur.

1. Cliquez sur **Profil utilisateur**, puis sélectionnez **Utilisateurs**.

    ![Capture d’écran montrant le site web Hive avec Your workspace sélectionné dans le menu.](./media/hive-tutorial/profile.png)

1. Cliquez sur **Authentification**, puis effectuez les étapes suivantes :

    ![Capture d’écran montrant la page Auth où vous effectuez les tâches décrites.](./media/hive-tutorial/authentication.png)

    a. Copiez **votre ID d’espace de travail**, puis ajoutez-le à l’**URL d’authentification** et à l’**URL de réponse** dans la **section Configuration SAML de base** dans le portail Azure.

    b. Dans la zone de texte **URL d’authentification unique SAML**, collez la valeur d’**URL de connexion** que vous avez copiée à partir du portail Azure.

    c. Dans la zone de texte **Émetteur du fournisseur d’identité**, collez la valeur de l’**identificateur Azure AD** que vous avez copiée à partir du portail Azure.

    d. Ouvrez le fichier de **Certificat (en base64)** téléchargé à partir du portail Azure dans le Bloc-notes, copiez son contenu, collez-le dans la zone de texte **Certificat**, puis enregistrez les changements apportés.

### <a name="create-hive-test-user"></a>Créer un utilisateur de test Hive

Dans cette section, un utilisateur appelé B.Simon est créé dans Hive. Hive prend en charge l’attribution d’utilisateurs juste-à-temps, qui est activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas encore d’utilisateur dans Hive, il en est créé un après l’authentification.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

#### <a name="sp-initiated"></a>Lancée par le fournisseur de services :

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion à Hive, d’où vous pouvez lancer le flux de connexion.  

* Accédez directement à l’URL de connexion à Hive pour lancer le flux de connexion.

#### <a name="idp-initiated"></a>Lancée par le fournisseur d’identité :

* Cliquez sur **Tester cette application** dans le portail Azure : vous devez être connecté automatiquement à l’instance de Hive pour laquelle vous avez configuré l’authentification unique. 

Vous pouvez aussi utiliser Mes applications de Microsoft pour tester l’application dans n’importe quel mode. Quand vous cliquez sur la vignette Hive dans Mes applications, si le mode Fournisseur de services est configuré, vous êtes redirigé vers la page de connexion de l’application pour lancer le flux de connexion ; si le mode Fournisseur d’identité est configuré, vous êtes automatiquement connecté à l’instance de Hive pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez configuré Hive, vous pouvez appliquer le contrôle de session, qui protège en temps réel contre l’exfiltration et l’infiltration de données sensibles de votre organisation. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
