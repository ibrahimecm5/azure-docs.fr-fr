---
title: 'Tutoriel : Intégration de l’authentification unique (SSO) Azure Active Directory à Leadfamly | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Leadfamly.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/05/2021
ms.author: jeedes
ms.openlocfilehash: e58a077706d97e18c0e62e66744e99ad05f553fe
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132317119"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-leadfamly"></a>Tutoriel : Intégration de l’authentification unique (SSO) Azure Active Directory à Leadfamly

Dans ce tutoriel, vous allez découvrir comment intégrer Leadfamly à Azure Active Directory (Azure AD). Quand vous intégrez Leadfamly à Azure AD, vous pouvez :

* Contrôler, dans Azure AD, qui a accès à Leadfamly.
* Permettre aux utilisateurs de se connecter automatiquement à Leadfamly avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Leadfamly pour lequel l’authentification SSO (authentification unique) est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* Leadfamly prend en charge l’authentification SSO lancée par le fournisseur de services (**SP**).

> [!NOTE]
> L’identificateur de cette application étant une valeur de chaîne fixe, une seule instance peut être configurée dans un locataire.

## <a name="add-leadfamly-from-the-gallery"></a>Ajouter Leadfamly à partir de la galerie

Pour configurer l’intégration de Leadfamly à Azure AD, vous devez ajouter Leadfamly à votre liste d’applications SaaS managées, à partir de la galerie.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, dans la zone de recherche, tapez **Leadfamly**.
1. Sélectionnez **Leadfamly** dans le panneau Résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-leadfamly"></a>Configurer et tester l’authentification SSO Azure AD pour Leadfamly

Configurez et testez l’authentification SSO Azure AD avec Leadfamly pour une utilisatrice de test appelée **B.Simon**. Pour que l’authentification SSO fonctionne, vous devez établir une relation entre un utilisateur Azure AD et l’utilisateur associé dans Leadfamly.

Pour configurer et tester l’authentification SSO Azure AD avec Leadfamly, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification SSO pour Leadfamly](#configure-leadfamly-sso)** afin de configurer les paramètres d’authentification unique côté application.
    1. **[Créer une utilisatrice de test pour Leadfamly](#create-leadfamly-test-user)** afin de disposer dans Leadfamly d’un équivalent de B.Simon lié à la représentation Azure AD de l’utilisatrice.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, dans la page d’intégration de l’application **Leadfamly**, recherchez la section **Gérer**, puis sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    a. Dans la zone de texte **URL de connexion**, saisissez une URL au format suivant : `https://appv2.leadfamly.com/saml-sso/<INSTANCE ID>`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion réelle. Pour obtenir ces valeurs, contactez l’[équipe du support technique de Leadfamly](mailto:support@leadfamly.com). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **XML de métadonnées de fédération** et sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

1. Dans la section **Configurer Leadfamly**, copiez l’URL ou les URL appropriées selon vos besoins.

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

Dans cette section, vous allez permettre à B.Simon d’utiliser l’authentification unique Azure en lui octroyant l’accès à Leadfamly.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Leadfamly**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-leadfamly-sso"></a>Configurer l’authentification SSO pour Leadfamly

1. Connectez-vous à votre site d’entreprise Leadfamly en tant qu’administrateur.

2. Accédez à **Account (Compte)**  ->**Customer information (Informations client)**  ->**SAML SSO (SSO SAML)** .

![Compte](./media/leadfamly-tutorial/configuration.png "Compte") 

3. Activez **SAML SSO**, sélectionnez le fournisseur **Azure AD** dans la liste déroulante, puis effectuez les étapes suivantes.

![Informations](./media/leadfamly-tutorial/account.png "Information") 

  a. Copiez la valeur de **Identifier** (Identificateur), puis collez-la dans la zone de texte d’URL **Identificateur** de la section **Configuration SAML de base** du portail Azure.

  b. Copiez la valeur du champ **Reply URL**, puis collez-la dans la zone de texte **URL de réponse** de la section **Configuration SAML de base** du portail Azure.

  c. Copiez la valeur du champ **Sign on URL** (URL de connexion), puis collez-la dans la zone de texte **URL de connexion** de la section **Configuration SAML de base** du portail Azure.

  d. Dans le Bloc-notes, ouvrez le fichier **XML de métadonnées de fédération** téléchargé à partir du portail Azure, puis chargez son contenu dans **Federation Metadata XML** (XML de métadonnées de fédération).

  e. Cliquez sur **Enregistrer**.

### <a name="create-leadfamly-test-user"></a>Créer l’utilisateur de test Leadfamly

1. Dans une autre fenêtre de navigateur web, connectez-vous au site web Leadfamly en tant qu’administrateur.

2. Accédez à **Account (Compte)**  -> **Users (Utilisateurs)**  -> **Invite user (Inviter un utilisateur)** .

![Section relative aux utilisateurs](./media/leadfamly-tutorial/users.png "Section relative aux utilisateurs") 

3. Remplissez les valeurs nécessaires dans les champs suivants, puis cliquez sur **Enregistrer**.

![Modifier les utilisateurs](./media/leadfamly-tutorial/modify-user.png "Modifier les utilisateurs") 

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Une redirection est effectuée vers l’URL de connexion à Leadfamly, où vous pouvez lancer le flux de connexion. 

* Accédez directement à l’URL de connexion à Leadfamly, puis lancez le flux de connexion à partir de cet emplacement.

* Vous pouvez utiliser Mes applications de Microsoft. Quand vous cliquez sur la vignette Leadfamly dans Mes applications, une redirection est effectuée vers l’URL de connexion à Leadfamly. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez configuré Leadfamly, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
