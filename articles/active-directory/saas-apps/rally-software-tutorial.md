---
title: 'Didacticiel : Intégration d’Azure Active Directory à Rally Software | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Rally Software.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/16/2021
ms.author: jeedes
ms.openlocfilehash: b3df51f1ad1aef63b5cedd457f24327157c97169
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132321032"
---
# <a name="tutorial-azure-active-directory-integration-with-rally-software"></a>Didacticiel : Intégration d’Azure Active Directory à Rally Software

Dans ce didacticiel, vous allez apprendre à intégrer Rally Software à Azure Active Directory (Azure AD). Quand vous intégrez Rally Software à Azure AD, vous pouvez :

* Contrôler, dans Azure AD, qui a accès à Rally Software.
* Permettre à vos utilisateurs de se connecter automatiquement à Rally Software avec leurs comptes Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Rally Software pour lequel l’authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Rally Software prend en charge l’authentification unique lancée par le **fournisseur de services**.

## <a name="add-rally-software-from-the-gallery"></a>Ajouter Rally Software à partir de la galerie

Pour configurer l’intégration de Rally Software à Azure AD, vous devez ajouter Rally Software à partir de la galerie, à votre liste d’applications SaaS managées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Rally Software** dans la zone de recherche.
1. Sélectionnez **Rally Software** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-rally-software"></a>Configurer et tester l’authentification unique Azure AD pour Rally Software

Configurez et testez l’authentification unique Azure AD auprès de Rally Software pour un utilisateur de test nommé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir une relation entre un utilisateur Azure AD et l’utilisateur associé dans Rally Software.

Pour configurer et tester l’authentification unique Azure AD auprès de Rally Software, procédez comme suit :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Rally Software](#configure-rally-software-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test Rally Software](#create-rally-software-test-user)** pour avoir un équivalent de B.Simon dans Rally Software lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le Portail Azure, accédez la page d’intégration de l’application **Rally Software**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    a. Dans la zone de texte **Identificateur (ID d’entité)** , saisissez une URL au format suivant : `https://<TENANT_NAME>.rally.com`

    b. Dans la zone de texte **URL de connexion**, saisissez une URL au format suivant : `https://<TENANT_NAME>.rally.com`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur et l’URL de connexion réels. Pour obtenir ces valeurs, contactez [l’équipe du support Rally Software](https://help.rallydev.com/). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

5. Sur la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le fichier **XML de métadonnées de fédération** en fonction des options définies selon vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

6. Dans la section **Configurer Rally Software**, copiez la ou les URL appropriées, selon vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Rally Software.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Rally Software**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-rally-software-sso"></a>Configurer l’authentification unique logicielle Rally Software

1. Connectez-vous à votre locataire **Rally Software**.

2. Dans la barre d'outils supérieure, cliquez sur **Setup**, puis sélectionnez **Subscription**.
   
    ![Abonnement](./media/rally-software-tutorial/toolbar.png "Abonnement")

3. Cliquez sur le bouton **Action**. Sélectionnez **Modifier l’abonnement** en haut à droite de la barre d’outils.

4. Dans la page **Subscription**, procédez comme suit, puis cliquez sur **Save & Close** :
   
    ![Authentification](./media/rally-software-tutorial/configuration.png "Authentification")
   
    a. Sélectionnez **Rally or SSO authentication** dans la liste déroulante Authentication.

    b. Dans la zone de texte **Identity Provider URL** (URL du fournisseur d’identité), collez la valeur de l’**identificateur Azure AD** que vous avez copiée à partir du portail Azure. 

    c. Dans la zone de texte **SSO Logout** (Déconnexion SSO), collez la valeur de l’**URL de déconnexion** que vous avez copiée à partir du portail Azure.

### <a name="create-rally-software-test-user"></a>Créer un utilisateur de test Rally Software

Pour que les utilisateurs Azure AD puissent se connecter, ils doivent être attribués dans l’application Rally Software avec leur nom d’utilisateur Azure Active Directory.

**Pour configurer l'approvisionnement des utilisateurs, procédez comme suit :**

1. Connectez-vous à votre locataire Rally Software.

2. Accédez à **Setup \> USERS**, puis cliquez sur **+ Add New**.
   
    ![Utilisateurs](./media/rally-software-tutorial/add-user.png "Utilisateurs")

3. Tapez le nom dans la zone de texte Nouvel utilisateur, puis cliquez sur **Ajouter avec des détails**.

4. Dans la section **Create User** , procédez comme suit :
   
    ![Create User](./media/rally-software-tutorial/new-user.png "Create User") (Créer un utilisateur)

    a. Dans la zone de texte **User Name** (Nom d’utilisateur), tapez le nom complet d’un utilisateur, par exemple **Britta Simon**.
   
    b. Dans la zone de texte **E-mail Address** (Adresse e-mail), entrez l’adresse e-mail de l’utilisateur, par exemple brittasimon@contoso.com.

    c. Dans la zone de texte **Prénom**, entrez le prénom de l’utilisateur, par exemple **Britta**.

    d. Dans la zone de texte **Nom**, entrez le nom de l’utilisateur, par exemple **Simon**.

    e. Cliquez sur **Enregistrer et fermer**.

   >[!NOTE]
   >Vous pouvez utiliser tout autre outil ou n’importe quelle API de création de compte d’utilisateur fournis par Rally Software pour approvisionner des comptes d’utilisateur Azure AD.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion à Rally Software à partir de laquelle vous pouvez lancer le flux de connexion. 

* Accédez directement à l’URL de connexion à Rally Software pour initier le flux de connexion.

* Vous pouvez utiliser Mes applications de Microsoft. Quand vous cliquez sur la vignette Rally Software dans Mes applications, vous êtes redirigé vers l’URL de connexion à Rally Software. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré Rally Software, vous pouvez appliquer le contrôle de session, qui protège en temps réel contre l’exfiltration et l’infiltration des données sensibles de votre organisation. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
