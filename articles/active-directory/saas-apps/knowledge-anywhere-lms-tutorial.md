---
title: 'Tutoriel : Intégration d’Azure Active Directory à Knowledge Anywhere LMS | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Knowledge Anywhere LMS.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/09/2021
ms.author: jeedes
ms.openlocfilehash: ecb309362b1c7171e7f3a2782de75f7ebd88b310
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132313638"
---
# <a name="tutorial-integrate-knowledge-anywhere-lms-with-azure-active-directory"></a>Tutoriel : Intégrer Knowledge Anywhere LMS à Azure Active Directory

Dans ce tutoriel, vous allez apprendre à intégrer Knowledge Anywhere LMS à Azure Active Directory (Azure AD). Quand vous intégrez Knowledge Anywhere LMS à Azure AD, vous pouvez :

* Contrôler dans Azure AD l’accès des utilisateurs à Knowledge Anywhere LMS.
* Permettre aux utilisateurs de se connecter automatiquement à Knowledge Anywhere LMS avec leurs comptes Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Abonnement Knowledge Anywhere LMS pour lequel l’authentification unique (SSO) est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test. 
* Knowledge Anywhere LMS prend en charge l’authentification unique lancée par le **fournisseur de services**.
* Knowledge Anywhere LMS prend en charge l’attribution d’utilisateurs **juste-à-temps**.

## <a name="add-knowledge-anywhere-lms-from-the-gallery"></a>Ajout de Knowledge Anywhere LMS à partir de la galerie

Pour configurer l’intégration de Knowledge Anywhere LMS à Azure AD, vous devez ajouter Knowledge Anywhere LMS à votre liste d’applications SaaS managées à partir de la galerie.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Knowledge Anywhere LMS** dans la zone de recherche.
1. Sélectionnez **Knowledge Anywhere LMS** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-knowledge-anywhere-lms"></a>Configuration et test de l’authentification unique Azure AD pour Knowledge Anywhere LMS

Configurez et testez l’authentification unique Azure AD avec Knowledge Anywhere LMS à l’aide d’un utilisateur de test nommé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Knowledge Anywhere LMS associé.

Pour configurer et tester l’authentification unique Azure AD auprès de Knowledge Anywhere LMS, procédez comme suit :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Knowledge Anywhere LMS](#configure-knowledge-anywhere-lms-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test Knowledge Anywhere LMS](#create-knowledge-anywhere-lms-test-user)** pour avoir dans Knowledge Anywhere LMS un équivalent de B.Simon qui soit lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Sur le Portail Azure, accédez à la page d’intégration de l’application **Knowledge Anywhere LMS**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon pour **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, si vous souhaitez configurer l’application en mode lancé par le **fournisseur d’identité**, effectuez les étapes suivantes :

    1. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<CLIENT_NAME>.knowledgeanywhere.com/`

    1. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://<CLIENT_NAME>.knowledgeanywhere.com/SSO/SAML/Response.aspx?<IDP_NAME>`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur et l’URL de réponse réels, procédure expliquée plus loin dans le tutoriel.

1. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<CLIENTNAME>.knowledgeanywhere.com/`

    > [!NOTE]
    > La valeur de l’URL de connexion n’est pas réelle. Mettez à jour la valeur avec l’URL de connexion réelle. Contactez l’[équipe du support client de Knowledge Anywhere LMS](https://knowany.zendesk.com/hc/en-us/articles/360000469034-SAML-2-0-Single-Sign-On-SSO-Set-Up-Guide) pour obtenir cette valeur. Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **Certificat (Base64)** , puis sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

   ![Lien Téléchargement de certificat](common/certificatebase64.png)

1. Dans la section **Configurer Knowledge Anywhere LMS**, copiez les URL appropriées, selon vos besoins.

   ![Copier les URL de configuration](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

Dans cette section, vous allez créer un utilisateur de test appelé B. Simon dans le portail Azure.

1. Dans le volet gauche du Portail Azure, sélectionnez **Azure Active Directory**, **Utilisateurs**, puis **Tous les utilisateurs**.
1. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.
1. Dans les propriétés **Utilisateur**, effectuez les étapes suivantes :
   1. Dans le champ **Nom**, entrez `B. Simon`.  
   1. Dans le champ **Nom de l’utilisateur**, entrez username@companydomain.extension. Par exemple : `BrittaSimon@contoso.com`.
   1. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.
   1. Cliquez sur **Créer**.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Knowledge Anywhere LMS.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Knowledge Anywhere LMS**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-knowledge-anywhere-lms-sso"></a>Configuration de l’authentification unique Knowledge Anywhere LMS

1. Pour automatiser la configuration dans Knowledge Anywhere LMS, vous devez installer l’**extension de connexion sécurisée à Mes applications** en cliquant sur **Installer l’extension**.

    ![Extension My apps](common/install-myappssecure-extension.png)

2. Après l’ajout de l’extension au navigateur, cliquez sur **Configurer Knowledge Anywhere LMS** pour être orienté vers l’application Knowledge Anywhere LMS. À partir de là, indiquez les informations d’identification de l’administrateur pour vous connecter à Knowledge Anywhere LMS. Cette extension de navigateur configure automatiquement l’application pour vous et automatise les étapes 3 à 7.

    ![Configuration](common/setup-sso.png)

3. Si vous souhaitez configurer manuellement Knowledge Anywhere LMS, ouvrez une nouvelle fenêtre de navigateur web, connectez-vous à votre site d’entreprise Knowledge Anywhere LMS en tant qu’administrateur, puis effectuez les étapes suivantes :

4. Sélectionnez l’onglet **Site**.

    ![Capture d’écran montrant l’onglet Site.](./media/knowledge-anywhere-lms-tutorial/site.png)

5. Sélectionnez l’onglet **Paramètres SAML**.

    ![Capture d’écran montrant la page Knowledge Anywhere avec les paramètres SAML sélectionnés.](./media/knowledge-anywhere-lms-tutorial/settings.png)

6. Cliquez sur **Ajouter un nouveau**.

    ![Capture d’écran montrant le bouton Add New dans les paramètres du fournisseur de services.](./media/knowledge-anywhere-lms-tutorial/add-settings.png)

7. Dans la page **Add/Update SAML Settings**, effectuez les étapes suivantes :

    ![Capture d’écran montrant la page Add/Update SAML Settings dans laquelle vous pouvez apporter les modifications décrites ici.](./media/knowledge-anywhere-lms-tutorial/update-settings.png)

    a. Entrez le nom IDP en fonction de votre organisation. Exemple : - `Azure`.

    b. Dans la zone de texte **IDP Entity ID**, collez la valeur de l’**identificateur Azure AD** que vous avez copiée à partir du portail Azure.

    c. Dans la zone de texte **IDP URL**, collez la valeur de l’**URL de connexion** que vous avez copiée à partir du portail Azure.

    d. Dans le Bloc-notes, ouvrez le fichier de certificat téléchargé à partir du portail Azure, copiez le contenu du certificat, puis collez-le dans la zone de texte **Certificat**.

    e. Dans la zone de texte **URL de déconnexion**, collez la valeur de l’**URL de déconnexion** que vous avez copiée à partir du portail Azure.

    f. Dans la liste déroulante, sélectionnez **Site principal** pour le **domaine**.

    g. Copiez la valeur de l’**ID d’entité du fournisseur de services**, puis collez-la dans la zone de texte **Identificateur** de la section **Configuration SAML de base** du portail Azure.

    h. Copiez la valeur de l’**URL de réponse (ACS) du fournisseur de services**, puis collez-la dans la zone de texte **URL de réponse** de la section **Configuration SAML de base** du portail Azure.

    i. Cliquez sur **Enregistrer**.

### <a name="create-knowledge-anywhere-lms-test-user"></a>Créer un utilisateur de test Knowledge Anywhere LMS

Dans cette section, un utilisateur appelé B. Simon est créé dans Knowledge Anywhere LMS. Knowledge Anywhere LMS prend en charge l’attribution d’utilisateurs juste-à-temps, option activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. Si l’utilisateur souhaité n’existe pas déjà dans Knowledge Anywhere LMS, il est créé après l’authentification.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. L’URL de connexion à Knowledge Anywhere LMS s’ouvre. Vous pouvez alors lancer le flux de connexion. 

* Accédez directement à l’URL de connexion à Knowledge Anywhere LMS pour lancer le flux de connexion.

* Vous pouvez utiliser Mes applications de Microsoft. Cliquez la vignette Knowledge Anywhere LMS dans Mes applications pour accéder directement à l’URL de connexion à Knowledge Anywhere LMS. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré Knowledge Anywhere LMS, vous pouvez appliquer le contrôle de session, qui empêche en temps réel l’exfiltration et l’infiltration des données sensibles de votre organisation. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
