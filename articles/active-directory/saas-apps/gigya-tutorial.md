---
title: 'Didacticiel : Intégration d’Azure Active Directory à Gigya | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Gigya.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/06/2021
ms.author: jeedes
ms.openlocfilehash: 6555b52447491834983d1b00417be69f4a096331
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132291613"
---
# <a name="tutorial-azure-active-directory-integration-with-gigya"></a>Didacticiel : Intégration d’Azure Active Directory à Gigya

Dans ce tutoriel, vous allez apprendre à intégrer Gigya à Azure Active Directory (Azure AD). Quand vous intégrez Gigya à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Gigya.
* Permettre à vos utilisateurs de se connecter automatiquement à Gigya avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement à Gigya pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Gigya prend en charge l’authentification unique lancée par le **fournisseur de services**.

## <a name="add-gigya-from-the-gallery"></a>Ajouter Gigya à partir de la galerie

Pour configurer l’intégration de Gigya à Azure AD, vous devez ajouter Gigya à partir de la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Gigya** dans la zone de recherche.
1. Sélectionnez **Gigya** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-gigya"></a>Configurer et tester l’authentification unique Azure AD pour Gigya

Configurez et testez l’authentification unique Azure AD avec Gigya à l’aide d’un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Gigya associé.

Pour configurer et tester l’authentification unique Azure AD avec Gigya, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Gigya](#configure-gigya-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test Gigya](#create-gigya-test-user)** pour avoir un équivalent de B.Simon dans Gigya lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **Gigya**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    a. Dans la zone de texte **URL de connexion**, saisissez une URL au format suivant : `http://<companyname>.gigya.com`

    b. Dans la zone de texte **Identificateur (ID d’entité)** , saisissez une URL au format suivant : `https://fidm.gigya.com/saml/v2.0/<companyname>`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Pour obtenir ces valeurs, contactez [l’équipe de support technique Gigya](https://developers.gigya.com/display/GD/Opening+A+Support+Incident). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

5. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (Base64)** en fonction des options définies par rapport à vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

6. Dans la section **Configurer Gigya**, copiez l’URL ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Gigya.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Gigya**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-gigya-sso"></a>Configurer l’authentification unique Gigya

1. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Gigya en tant qu’administrateur.

2. Accédez à **Settings \> SAML Login**, puis cliquez sur le bouton **Add**.
   
    ![Connexion SAML](./media/gigya-tutorial/login.png "SAML login")

3. Dans la section **Connexion SAML**, procédez comme suit :
   
    ![Configuration SAML](./media/gigya-tutorial/configuration.png "Configuration SAML")
   
    a. Dans la zone de texte **Name** , tapez le nom de votre configuration.
   
    b. Dans la zone de texte **Issuer** (Émetteur), collez la valeur **Identificateur Azure AD** que vous avez copiée à partir du portail Azure. 
   
    c. Dans la zone de texte **Single Sign-On Service URL** (URL du service d’authentification unique), collez la valeur de l’**URL de connexion** que vous avez copiée à partir du portail Azure.
   
    d. Dans la zone de texte **Name ID Format** (Format d’ID de nom), collez la valeur de **Format de l’identificateur du nom** que vous avez copiée à partir du portail Azure.
   
    e. Ouvrez dans le Bloc-notes votre certificat codé en base 64 téléchargé à partir du portail Azure, copiez son contenu dans le Presse-papiers, puis collez-le dans la zone de texte **Certificat X.509**.
   
    f. Cliquez sur **Save Settings**.

## <a name="create-gigya-test-user"></a>Créer un utilisateur de test Gigya

Pour se connecter à Gigya, les utilisateurs d’Azure AD doivent être approvisionnés dans Gigya. Dans le cas de Gigya, l’approvisionnement est une tâche manuelle.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Pour approvisionner un compte d’utilisateur, procédez comme suit :

1. Connectez-vous à votre site d'entreprise **Gigya** en tant qu'administrateur.

2. Accédez à **Admin \> Manage Users**, puis cliquez sur **Invite Users**.
   
    ![Gérer les utilisateurs](./media/gigya-tutorial/users.png "Manage Users")

3. Dans la boîte de dialogue Invite Users, procédez comme suit :
   
    ![Inviter des utilisateurs](./media/gigya-tutorial/invite-user.png "Inviter des utilisateurs")
   
    a. Dans la zone de texte **Courrier électronique**, tapez l'alias d'adresse de messagerie d'un compte Azure Active Directory valide à configurer.
    
    b. Cliquez sur **Inviter des utilisateurs**.
      
    > [!NOTE]
    > Le titulaire du compte Azure Active Directory recevra un message électronique contenant un lien pour confirmer le compte avant qu’il ne soit activé.
    > 

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion à Gigya, d’où vous pouvez lancer le flux de connexion. 

* Accédez directement à l’URL de connexion à Gigya pour lancer le flux de connexion.

* Vous pouvez utiliser Mes applications de Microsoft. Un clic sur la vignette Gigya dans Mes applications vous redirige vers l’URL de connexion à Gigya. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré Gigya, vous pouvez appliquer le contrôle de session, qui protège en temps réel contre l’exfiltration et l’infiltration des données sensibles de votre organisation. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
