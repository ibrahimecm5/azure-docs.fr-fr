---
title: 'Didacticiel : Intégration de Azure Active Directory avec XaitPorter | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et XaitPorter.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/31/2021
ms.author: jeedes
ms.openlocfilehash: 71ea5cbb1e2c0c5db8933696f5d1797fabcbda62
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132332551"
---
# <a name="tutorial-azure-active-directory-integration-with-xaitporter"></a>Didacticiel : Intégration de Azure Active Directory à XaitPorter

Dans ce tutoriel, vous allez apprendre à intégrer XaitPorter à Azure Active Directory (Azure AD). Quand vous intégrez XaitPorter à Azure AD, vous pouvez :

* Contrôler qui a accès à XaitPorter dans Azure AD.
* Permettre à vos utilisateurs de se connecter automatiquement à XaitPorter avec leurs comptes Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration de Azure AD à XaitPorter, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un [compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement XaitPorter pour lequel l’authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* XaitPorter prend en charge l’authentification unique lancée par le **fournisseur de services**.

## <a name="add-xaitporter-from-the-gallery"></a>Ajouter XaitPorter depuis la galerie

Pour configurer l’intégration de XaitPorter avec Azure AD, vous devez ajouter XaitPorter, disponible dans la galerie, à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **XaitPorter** dans la zone de recherche.
1. Sélectionnez **XaitPorter** dans le volet des résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-xaitporter"></a>Configurer et tester l’authentification unique Azure AD pour XaitPorter

Configurez et testez l’authentification unique Azure AD avec XaitPorter à l’aide d’un utilisateur de test nommé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur associé dans XaitPorter.

Pour configurer et tester l’authentification unique Azure AD auprès de XaitPorter, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique XaitPorter](#configure-xaitporter-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test XaitPorter](#create-xaitporter-test-user)** pour avoir un équivalent de B.Simon dans XaitPorter lié à la représentation Azure AD associée.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **XaitPorter**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    a. Dans la zone de texte **Identificateur (ID d’entité)** , saisissez une URL au format suivant : `https://<SUBDOMAIN>.xaitporter.com`

    b. Dans la zone de texte **URL de connexion**, saisissez une URL au format suivant : `https://<SUBDOMAIN>.xaitporter.com/saml/login`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur et l’URL de connexion réels. Pour obtenir ces valeurs, contactez l’[équipe de support technique XaitPorter](https://www.xait.com/support/). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

5. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur le bouton Copier pour copier l’**URL des métadonnées de fédération d’application**, puis enregistrez-la sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/copy-metadataurl.png)

6. Fournissez **l’adresse IP** ou **l’URL des métadonnées de fédération de l’application** à [l’équipe de support SmartRecruiters](https://www.smartrecruiters.com/about-us/contact-us/), de sorte que XaitPorter puisse assurer l’accessibilité de l’adresse IP à partir de votre instance XaitPorter, en établissant une liste approuvée de leur côté. 

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à XaitPorter.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **XaitPorter**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-xaitporter-sso"></a>Configurer l’authentification unique XaitPorter

1. Pour automatiser la configuration dans XaitPorter, vous devez installer l’**extension de navigateur My Apps Secure Sign-in** en cliquant sur **Install the extension**.

    ![Extension My apps](common/install-myappssecure-extension.png)

2. Après l’ajout de l’extension au navigateur, cliquez sur **Setup XaitPorter** pour être orienté vers l’application XaitPorter. À partir de là, indiquez les informations d’identification de l’administrateur pour vous connecter à XaitPorter. Cette extension de navigateur configure automatiquement l’application et automatise les étapes 3 à 6.

    ![Configuration](common/setup-sso.png)

3. Si vous souhaitez configurer manuellement XaitPorter, ouvrez une nouvelle fenêtre de navigateur web, connectez-vous à votre site d’entreprise XaitPorter en tant qu’administrateur et effectuez les étapes suivantes :

4. Cliquez sur le titre **Admin**.

    ![Capture d’écran montrant l’élément Admin sélectionné sur le site XaitPorter.](./media/xaitporter-tutorial/admin.png)

5. Sélectionnez **Gérer l’authentification unique** à partir de la liste déroulante **Configuration système**.

    ![Capture d’écran montrant l’option Manage Single Sign-On sélectionnée dans System Setup.](./media/xaitporter-tutorial/user.png)

6. Dans la section **GÉRER L’AUTHENTIFICATION UNIQUE**, procédez comme suit :

    ![Capture d’écran montrant la section MANAGE SINGLE SIGN-ON où vous pouvez effectuer ces étapes.](./media/xaitporter-tutorial/authentication.png)

    a. Sélectionnez **Activer l'authentification unique**.

    b. Dans la zone de texte **Paramètres de fournisseur d’identité**, collez **l’URL de métadonnées de fédération de l’application** que vous avez copiée dans le portail Azure et cliquez sur **Récupérer**.

    c. Sélectionnez **Activer la création automatique d’utilisateurs**.

    d. Cliquez sur **OK**.

### <a name="create-xaitporter-test-user"></a>Créer un utilisateur de test XaitPorter

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans XaitPorter. Collaborez avec l’[équipe du support technique XaitPorter](https://www.xait.com/support/) pour ajouter des utilisateurs à la plateforme XaitPorter. Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Cette opération effectue une redirection vers l’URL de connexion à XaitPorter, où vous pouvez lancer le flux de connexion. 

* Accédez directement à l’URL de connexion XaitPorter pour lancer le processus de connexion.

* Vous pouvez utiliser Mes applications de Microsoft. Le fait de cliquer sur la vignette XaitPorter dans Mes applications vous redirige vers l’URL de connexion à XaitPorter. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez configuré XaitPorter, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
