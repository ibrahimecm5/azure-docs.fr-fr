---
title: 'Tutoriel : Intégration d’Azure Active Directory à Cisco Umbrella Admin SSO | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Cisco Umbrella Admin SSO.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/16/2021
ms.author: jeedes
ms.openlocfilehash: 824fc32e4e7a74946c997274311ee5da038188b2
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132311817"
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-umbrella-admin-sso"></a>Tutoriel : Intégration d’Azure Active Directory à Cisco Umbrella Admin SSO

Dans ce tutoriel, vous allez découvrir comment intégrer Cisco Umbrella Admin SSO à Azure Active Directory (Azure AD). Quand vous intégrez Cisco Umbrella Admin SSO à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Cisco Umbrella Admin SSO.
* Autoriser les utilisateurs à se connecter automatiquement à Cisco Umbrella Admin SSO avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Cisco Umbrella Admin SSO pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Cisco Umbrella Admin SSO prend en charge l’authentification unique lancée par **le fournisseur de services et le fournisseur d’identité**.

## <a name="add-cisco-umbrella-admin-sso-from-the-gallery"></a>Ajouter Cisco Umbrella Admin SSO à partir de la galerie

Pour configurer l’intégration de Cisco Umbrella Admin SSO à Azure AD, vous devez ajouter Cisco Umbrella Admin SSO, disponible dans la galerie, à votre liste d’applications SaaS managées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Cisco Umbrella Admin SSO** dans la zone de recherche.
1. Sélectionnez **Cisco Umbrella Admin SSO** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-cisco-umbrella-admin-sso"></a>Configurer et tester l’authentification unique Azure AD pour Cisco Umbrella Admin SSO

Configurez et testez l’authentification unique Azure AD avec Cisco Umbrella Admin SSO pour un utilisateur de test nommé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Cisco Umbrella Admin SSO associé.

Pour configurer et tester l’authentification unique Azure AD avec Cisco Umbrella Admin SSO, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Cisco Umbrella Admin SSO](#configure-cisco-umbrella-admin-sso-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test Cisco Umbrella Admin SSO](#create-cisco-umbrella-admin-sso-test-user)** pour avoir, dans Cisco Umbrella Admin SSO, un équivalent de B.Simon lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, dans la page d’intégration de l’application **Cisco Umbrella Admin SSO**, recherchez la section **Gérer**, puis sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, l’utilisateur n’a rien à faire, car l’application est déjà intégrée à Azure.

    a. Si vous souhaitez configurer l’application en mode initié par le **fournisseur de service**, procédez comme suit :

    b. Cliquez sur **Définir des URL supplémentaires**.

    c. Dans la zone de texte **URL de connexion**, tapez l’URL : `https://login.umbrella.com/sso`

5. Sur la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le fichier **XML des métadonnées** en fonction des options définies, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

6. Dans la section **Configurer Cisco Umbrella Admin SSO**, copiez la ou les URL appropriées correspondant à vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Cisco Umbrella Admin SSO.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Cisco Umbrella Admin SSO**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-cisco-umbrella-admin-sso-sso"></a>Configurer l’authentification unique (SSO) pour Cisco Umbrella Admin SSO

1. Dans une autre fenêtre de navigateur, connectez-vous à votre site d’entreprise Cisco Umbrella Admin SSO en tant qu’administrateur.

2. Sur le côté gauche du menu, cliquez sur **Administrateur**, accédez à **Authentification**, puis cliquez sur **SAML**.

    ![Administrateur](./media/cisco-umbrella-tutorial/admin.png)

3. Sélectionnez **Autre**, puis cliquez sur **SUIVANT**.

    ![Autre](./media/cisco-umbrella-tutorial/other.png)

4. Dans la page **Métadonnées Cisco Umbrella Admin SSO**, cliquez sur **SUIVANT**.

    ![Métadonnées](./media/cisco-umbrella-tutorial/metadata.png)

5. Sous l'onglet **Charger les métadonnées**, si vous avez préconfiguré SAML, sélectionnez **Cliquer ici pour les modifier**, puis suivez les étapes ci-dessous.

    ![Ensuite](./media/cisco-umbrella-tutorial/next.png)

6. Dans l'**Option A : Charger le fichier XML**, chargez le fichier **XML des métadonnées de fédération** que vous avez téléchargé à partir du portail Azure. Après avoir chargé les métadonnées, les valeurs ci-dessous sont automatiquement renseignées. Cliquez ensuite sur **SUIVANT**.

    ![Sélection du fichier](./media/cisco-umbrella-tutorial/choose-file.png)

7. Sous la section **Valider la configuration SAML**, cliquez sur **TESTER VOTRE CONFIGURATION SAML**.

    ![Test](./media/cisco-umbrella-tutorial/test.png)

8. Cliquez sur **ENREGISTRER**.

### <a name="create-cisco-umbrella-admin-sso-test-user"></a>Créer un utilisateur de test Cisco Umbrella Admin SSO

Pour se connecter à Cisco Umbrella Admin SSO, les utilisateurs Azure AD doivent être provisionnés dans Cisco Umbrella Admin SSO.  
Dans Cisco Umbrella Admin SSO, le provisionnement est une tâche manuelle.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Dans une autre fenêtre de navigateur, connectez-vous à votre site d’entreprise Cisco Umbrella Admin SSO en tant qu’administrateur.

2. Sur le côté gauche du menu, cliquez sur **Administrateur** et accédez à **Comptes**.

    ![Compte](./media/cisco-umbrella-tutorial/account.png)

3. Sur la page **Comptes**, cliquez sur **Ajouter**, à droite, et procédez comme suit.

    ![L’utilisateur](./media/cisco-umbrella-tutorial/create-user.png)

    a. Dans le champ **Prénom** , entrez le prénom **Britta**.

    b. Dans le champ **Nom de famille**, entrez le prénom **Simon**.

    c. À partir de **Choisir un rôle d'administrateur délégué**, sélectionnez votre rôle.

    d. Dans le champ **Adresse e-mail**, entrez l’adresse e-mail de l’utilisateur, par exemple **brittasimon\@contoso.com**.

    e. Dans le champ **Mot de passe**, entrez votre mot de passe.

    f. Dans le champ **Confirmer le mot de passe**, entrez à nouveau votre mot de passe.

    g. Cliquez sur **CREATE** (Créer).

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

#### <a name="sp-initiated"></a>Lancée par le fournisseur de services :

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion à Cisco Umbrella Admin SSO, d’où vous pouvez lancer le flux de connexion.  

* Accédez directement à l’URL de connexion à Cisco Umbrella Admin SSO et lancez le flux de connexion.

#### <a name="idp-initiated"></a>Lancée par le fournisseur d’identité :

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors connecté automatiquement à l’instance de Cisco Umbrella Admin SSO pour laquelle vous avez configuré l’authentification unique. 

Vous pouvez aussi utiliser Mes applications de Microsoft pour tester l’application dans n’importe quel mode. Quand vous cliquez sur la vignette Cisco Umbrella Admin SSO dans Mes applications, si le mode Fournisseur de services est configuré, vous êtes redirigé vers la page de connexion de l’application pour lancer le flux de connexion ; si le mode Fournisseur d’identité est configuré, vous êtes automatiquement connecté à l’instance de Cisco Umbrella Admin SSO pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré Cisco Umbrella Admin SSO, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
