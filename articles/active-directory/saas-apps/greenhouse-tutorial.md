---
title: 'Didacticiel : intégration d’Azure Active Directory à Greenhouse | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Greenhouse.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/21/2021
ms.author: jeedes
ms.openlocfilehash: d5a71350a6d31ed275b65ebe3fb2cab574bed5bb
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132309331"
---
# <a name="tutorial-azure-active-directory-integration-with-greenhouse"></a>Didacticiel : Intégration d’Azure Active Directory avec Greenhouse

Ce tutoriel explique comment intégrer Greenhouse dans Azure Active Directory (Azure AD). Quand vous intégrez Greenhouse à Azure AD, vous pouvez :

* Contrôler qui a accès à Greenhouse dans Azure AD.
* Permettre à vos utilisateurs de se connecter automatiquement à Greenhouse avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Abonnement Greenhouse pour lequel l’authentification unique (SSO) est activée.

> [!NOTE] 
> Cette intégration peut également être utilisée à partir de l’environnement cloud US Government Azure AD. Cette application est disponible dans la Galerie d’applications cloud US Government Azure AD et peut être configurée de la même façon que dans le cloud public. 

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* Greenhouse prend en charge l’authentification unique lancée par **le fournisseur de services et le fournisseur d’identité**.

## <a name="adding-greenhouse-from-the-gallery"></a>Ajout de Greenhouse à partir de la galerie

Pour configurer l’intégration de à partir de la galerie avec Azure AD, vous devez ajouter à partir de la galerie à partir de la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Greenhouse** dans la zone de recherche.
1. Sélectionnez **Greenhouse** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.


## <a name="configure-and-test-azure-ad-sso-for-greenhouse"></a>Configurer et tester l’authentification unique Azure AD pour Greenhouse

Configurez et testez l’authentification unique Azure AD auprès de Greenhouse pour un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Greenhouse associé.

Pour configurer et tester l’authentification unique Azure AD auprès de Greenhouse, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
2. **[Configurer l’authentification unique Greenhouse](#configure-greenhouse-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer l’utilisateur de test Greenhouse](#create-greenhouse-test-user)** pour avoir dans Greenhouse un équivalent de Britta Simon lié à la représentation Azure AD associée.
3. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, dans la page d’intégration de l’application **Greenhouse**, recherchez la section **Gérer**, puis sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

    ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, si vous souhaitez configurer l’application en mode Initié par le **fournisseur d’identité**, entrez les valeurs pour les champs suivants :

    a. Dans la zone de texte **Identificateur**, tapez la valeur : `greenhouse.io`

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://<COMPANYNAME>.greenhouse.io/<ENTITY ID>/users/saml/consume`

1. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    Dans la zone de texte **URL de connexion**, tapez l’URL : `https://app.greenhouse.io`

    > [!NOTE]
    > Cette valeur n’est pas la valeur réelle. Mettez à jour la valeur avec l’URL de réponse réelle. Pour obtenir la valeur, contactez l’[équipe de support technique Greenhouse](https://www.greenhouse.io/contact). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

4. Sur la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le fichier **XML de métadonnées de fédération** en fonction des options définies selon vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

6. Dans la section **Configurer Greenhouse**, copiez l’URL ou les URL appropriées, en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Greenhouse.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Greenhouse**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-greenhouse-sso"></a>Configurer l’authentification unique de Greenhouse

1. Dans une autre fenêtre de navigateur web, connectez-vous au site web de Greenhouse en tant qu’administrateur.

1. Accédez à **Configurer > Centre de développement > Authentification unique**.

    ![Capture d’écran de la page d’authentification unique](./media/greenhouse-tutorial/configure.png)

1. Effectuez les étapes suivantes dans la page **Authentification unique**.

    ![Capture d’écran de la page de configuration de l’authentification unique](./media/greenhouse-tutorial/sso-page.png)

    a. Copiez la valeur de **SSO Assertion Consumer URL** et collez-la dans la zone de texte **URL de réponse** de la section **Configuration SAML de base** dans le portail Azure.

    b. Dans la zone de texte **ID d’entité/Émetteur**, collez la valeur **Identificateur Azure AD** que vous avez copiée dans le portail Azure.

    c. Dans la zone de texte **URL d’authentification unique**, collez l’**URL de connexion** que vous avez copiée à partir du portail Azure.

    d. Ouvrez le fichier **XML de métadonnées de Fédération** à partir du portail Azure dans le Bloc-notes et collez le contenu dans la zone de texte **Empreinte du certificat IdP**.

    e. Sélectionnez la valeur **Format de l’identification du nom** dans la liste déroulante.

    f. Cliquez sur **Commencer le test**.

    >[!NOTE]
    >Vous pouvez également charger le fichier **XML de métadonnées de fédération** en cliquant sur l’option **Choose File** (Choisir un fichier).

### <a name="create-greenhouse-test-user"></a>Créer l’utilisateur de test Greenhouse

Pour se connecter à Greenhouse, les utilisateurs d’Azure AD doivent être approvisionnés dans Greenhouse. Dans le cas de Greenhouse, l’approvisionnement est une tâche manuelle.

>[!NOTE]
>Vous pouvez utiliser tout autre outil ou n’importe quelle API de création de compte d’utilisateur fournis par Greenhouse pour provisionner des comptes d’utilisateurs Azure AD. 

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous à votre site d'entreprise **Greenhouse** en tant qu'administrateur.

2. Accédez à **Configurer > Utilisateurs > Nouveaux utilisateurs**.
   
    ![Utilisateurs](./media/greenhouse-tutorial/create-user-1.png "Utilisateurs")

4. Dans la section **Ajouter de nouveaux utilisateurs**, procédez comme suit :
   
    ![Ajouter un nouvel utilisateur](./media/greenhouse-tutorial/create-user-2.png "Add New User")

    a. Dans la zone de texte **Enter user emails**, tapez l'adresse de messagerie d'un compte Azure Active Directory valide à configurer.

    b. Cliquez sur **Enregistrer**.    
   
      >[!NOTE]
      >Le titulaire du compte Azure Active Directory recevra un message électronique contenant un lien pour confirmer le compte avant qu’il ne soit activé.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

#### <a name="sp-initiated"></a>Lancée par le fournisseur de services :

* Cliquez sur **Tester cette application** dans le portail Azure. Cette opération redirige vers l’URL de connexion à Greenhouse, où vous pouvez lancer le flux de connexion.  

* Accédez directement à l’URL de connexion à Greenhouse pour y lancer le flux de connexion.

#### <a name="idp-initiated"></a>Lancée par le fournisseur d’identité :

* Cliquez sur **Tester cette application** dans le portail Azure : vous devez être connecté automatiquement à l’instance de Greenhouse pour laquelle vous avez configuré l’authentification unique. 

Vous pouvez aussi utiliser Mes applications de Microsoft pour tester l’application dans n’importe quel mode. Si, quand vous cliquez sur la vignette Greenhouse dans Mes applications, le mode Fournisseur de services est configuré, vous êtes redirigé vers la page de connexion de l’application pour lancer le flux de connexion ; s’il s’agit du mode Fournisseur d’identité, vous êtes automatiquement connecté à l’instance de Greenhouse pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).



## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez configuré Greenhouse, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
