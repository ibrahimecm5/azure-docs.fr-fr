---
title: 'Tutoriel : Intégration d’Azure Active Directory à SolarWinds Service Desk (précédemment Samanage) | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et SolarWinds Service Desk (précédemment Samanage).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/31/2021
ms.author: jeedes
ms.openlocfilehash: d7d1684229e71049c18bd6967722b52d8589ab6a
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132329368"
---
# <a name="tutorial-azure-active-directory-integration-with-solarwinds-service-desk-previously-samanage"></a>Tutoriel : Intégration d’Azure Active Directory à SolarWinds Service Desk (précédemment Samanage)

Dans ce tutoriel, vous allez apprendre à intégrer SolarWinds à Azure Active Directory (Azure AD). Quand vous intégrez SolarWinds à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à SolarWinds.
* Permettre à vos utilisateurs de se connecter automatiquement à SolarWinds avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement SolarWinds pour lequel l’authentification unique est activée.

> [!NOTE]
> Cette intégration peut également être utilisée à partir de l’environnement cloud US Government Azure AD. Cette application est disponible dans la Galerie d’applications cloud US Government Azure AD et peut être configurée de la même façon que dans le cloud public.

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* SolarWinds prend en charge l’authentification unique initiée par **un fournisseur de services**.
* SolarWinds prend en charge l’[attribution automatique d’utilisateurs](samanage-provisioning-tutorial.md).

## <a name="add-solarwinds-from-the-gallery"></a>Ajouter SolarWinds à partir de la galerie

Pour configurer l’intégration de SolarWinds à Azure AD, vous devez ajouter SolarWinds à votre liste d’applications SaaS gérées, à partir de la galerie.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, saisissez **SolarWinds** dans la zone de recherche.
1. Sélectionnez **SolarWinds** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-solarwinds"></a>Configurer et tester l’authentification unique Azure AD pour SolarWinds

Configurez et testez l’authentification unique Azure AD avec SolarWinds à l’aide d’un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur associé dans SolarWinds.

Pour configurer et tester l’authentification unique Azure AD avec SolarWinds, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique SolarWinds](#configure-solarwinds-sso)** pour configurer les paramètres d’authentification unique côté application.
    1. **[Créer un utilisateur de test SolarWinds](#create-solarwinds-test-user)** pour avoir dans SolarWinds un équivalent de B.Simon qui soit lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **SolarWinds**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    a. Dans la zone de texte **URL de connexion**, saisissez une URL au format suivant : `https://<Company Name>.samanage.com/saml_login/<Company Name>`

    b. Dans la zone de texte **Identificateur (ID d’entité)** , saisissez une URL au format suivant : `https://<Company Name>.samanage.com`

    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeur avec l’URL de connexion et l’identificateur réels. La procédure est expliquée plus loin dans le didacticiel. Pour plus de détails, contactez l’[équipe de support technique Samanage](https://www.samanage.com/support). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

4. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (Base64)** en fonction des options définies par rapport à vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

6. Dans la section **Configurer SolarWinds**, copiez l’URL ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à SolarWinds.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **SolarWinds**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

<a name="configure-solarwinds-single-sign-on"></a>

## <a name="configure-solarwinds-sso"></a>Configurer l’authentification unique SolarWinds

1. Dans une autre fenêtre de navigateur web, connectez-vous au site de votre entreprise SolarWinds en tant qu’administrateur.

2. Cliquez sur **Dashboard** et sélectionnez **Setup** dans le volet de navigation de gauche.
   
    ![Tableau de bord](./media/samanage-tutorial/tutorial-samanage-1.png "Tableau de bord")

3. Cliquez sur **Single Sign-On**.
   
    ![Authentification unique](./media/samanage-tutorial/tutorial-samanage-2.png "Single Sign on")

4. Accédez à la section **Login using SAML** (Connexion avec SAML), puis procédez comme suit :
   
    ![Connexion avec SAML](./media/samanage-tutorial/tutorial-samanage-3.png "Login using SAML")
 
    a. Cliquez sur **Enable Single Sign-On with SAML**(Activer l’authentification unique avec SAML).  
 
    b. Dans la zone de texte **Identity Provider URL** (URL du fournisseur d’identité), entrez une valeur comme `https://YourAccountName.samanage.com`.
 
    c. Vérifiez que l’URL de connexion (**Login URL**) correspond à l’**URL de connexion** de la section **Configuration SAML de base** dans le portail Azure.
 
    d. Dans la zone de texte **Logout URL** (URL de déconnexion), collez la valeur d’**URL de déconnexion** que vous avez copiée à partir du portail Azure.
 
    e. Dans la zone de texte **SAML Issuer** (Émetteur SAML), saisissez l’URI d’ID d’application défini dans votre fournisseur d’identité.
 
    f. Ouvrez votre certificat codé en base 64 téléchargé à partir du portail Azure dans Bloc-notes, copiez son contenu dans le Presse-papiers, puis collez-le dans la zone de texte **Paste your Identity Provider x.509 Certificate below** (Collez le certificat X.509 de votre fournisseur d’identité ci-dessous).
 
    g. Cliquez sur **Create users if they do not exist in SolarWinds** (Créer des utilisateurs s’il n’en n’existe pas dans SolarWinds).
 
    h. Cliquez sur **Update**.

### <a name="create-solarwinds-test-user"></a>Créer un utilisateur de test SolarWinds

Pour permettre aux utilisateurs Azure AD de se connecter à SolarWinds, vous devez les provisionner dans SolarWinds.  
Dans le cas de SolarWinds, le provisionnement est une tâche manuelle.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous à votre site d’entreprise SolarWinds en tant qu’administrateur.

2. Cliquez sur **Dashboard** et sélectionnez **Setup** dans le volet de navigation gauche.
   
    ![Paramétrage](./media/samanage-tutorial/tutorial-samanage-1.png "Installation")

3. Cliquez sur l'onglet **Utilisateurs**.
   
    ![Utilisateurs](./media/samanage-tutorial/tutorial-samanage-6.png "Utilisateurs")

4. Cliquez sur **Nouvel utilisateur**.
   
    ![Nouvel utilisateur](./media/samanage-tutorial/tutorial-samanage-7.png "Nouvel utilisateur")

5. Entrez le **Nom** et l’**Adresse e-mail** du compte Azure Active Directory que vous souhaitez approvisionner, puis cliquez sur **Create user** (Créer un utilisateur).
   
    ![Create User](./media/samanage-tutorial/tutorial-samanage-8.png "Create User") (Créer un utilisateur)
   
   >[!NOTE]
   >Le titulaire du compte Azure Active Directory reçoit un message électronique contenant un lien à suivre pour confirmer son compte et l’activer. Vous pouvez utiliser tout autre outil ou API de création de compte d’utilisateur fournis par SolarWinds pour provisionner des comptes d’utilisateur Azure Active Directory.

> [!NOTE]
> SolarWinds prend aussi en charge l’attribution automatique d’utilisateurs. Vous trouverez [ici](./samanage-provisioning-tutorial.md) plus d’informations sur la façon de configurer l’attribution automatique d’utilisateurs.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion à SolarWinds, d’où vous pouvez lancer le flux de connexion. 

* Accédez directement à l’URL de connexion à SolarWinds pour lancer le flux de connexion.

* Vous pouvez utiliser Mes applications de Microsoft. Le fait de cliquer sur la vignette SolarWinds dans Mes applications vous redirige vers l’URL de connexion à SolarWinds. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré SolarWinds, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
