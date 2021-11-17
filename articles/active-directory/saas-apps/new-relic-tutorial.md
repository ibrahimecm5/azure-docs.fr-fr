---
title: 'Tutoriel : Intégration d’Azure Active Directory à New Relic by Account | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et New Relic by Account.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/02/2021
ms.author: jeedes
ms.openlocfilehash: a14a99fe949f0902d9bd861975418ee0e7d04026
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132294539"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-new-relic-by-account"></a>Tutoriel : Intégration de l’authentification unique (SSO) Azure Active Directory à New Relic by Account

Dans ce tutoriel, vous allez apprendre à intégrer New Relic by Account à Azure Active Directory (Azure AD). Quand vous intégrez New Relic by Account à Azure AD, vous pouvez :

* Contrôler, dans Azure AD, qui a accès à New Relic by Account.
* Permettre aux utilisateurs de se connecter automatiquement à New Relic by Account avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement New Relic by Account pour lequel l’authentification unique (SSO) est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* New Relic by Account prend en charge l’authentification unique lancée par le **fournisseur de services**
* New Relic prend en charge le [**provisionnement et le déprovisionnement automatisés des utilisateurs**](new-relic-by-organization-provisioning-tutorial.md) (recommandé).


## <a name="add-new-relic-by-account-from-the-gallery"></a>Ajouter New Relic by Account à partir de la galerie

Pour configurer l’intégration de New Relic by Account à Azure AD, vous devez ajouter New Relic by Account, disponible dans la galerie, à votre liste d’applications SaaS managées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **New Relic by Account** dans la zone de recherche.
1. Sélectionnez **New Relic by Account** dans le panneau des résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-new-relic-by-account"></a>Configurer et tester l’authentification unique Azure AD pour New Relic by Account

Configurez et testez l’authentification unique (SSO) Azure AD avec New Relic by Account pour un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique (SSO) fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur New Relic by Account associé.

Pour configurer et tester l’authentification unique Azure AD auprès de New Relic by Account, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    * **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    * **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique New Relic by Account](#configure-new-relic-by-account-sso)** pour configurer les paramètres d’authentification unique côté application.
    * **[Créer un utilisateur de test New Relic by Account](#create-new-relic-by-account-test-user)** pour avoir, dans New Relic by Account, un équivalent de B.Simon lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **New Relic by Account**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)
   
1. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    a. Dans la zone de texte **URL de connexion**, tapez l’URL au format suivant : 

    `https://rpm.newrelic.com:443/accounts/{acc_id}/sso/saml/finalize`. Veillez à remplacer `acc_id` par votre propre ID de compte New Relic by Account.

    b. Dans la zone de texte **Identificateur (ID d’entité)** , tapez l’URL suivante : `rpm.newrelic.com`

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (Base64)** en fonction des options définies par rapport à vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

1. Dans la section **Configurer New Relic by Account**, copiez la ou les URL appropriées correspondant à vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à New Relic by Account.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **New Relic by Account**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-new-relic-by-account-sso"></a>Configurer l’authentification unique côté New Relic by Account

1. Dans une autre fenêtre de navigateur web, connectez-vous au site de votre entreprise **New Relic by Account** en tant qu’administrateur.

2. Dans le menu situé en haut, cliquez sur **Account Settings**.
   
    ![Capture d’écran montrant la page d’accueil avec l’élément Account settings sélectionné.](./media/new-relic-tutorial/settings.png "Account Settings")

3. Cliquez sur l’onglet **Security and authentication**, puis sur l’onglet **Single sign on**.
   
    ![Authentification unique](./media/new-relic-tutorial/single-sign-on-tab.png "Single Sign on")

4. Dans la page de boîte de dialogue SAML, procédez comme suit :
   
    ![SAML](./media/new-relic-tutorial/save.png "SAML")
   
    a. Pour charger le certificat Azure Active Directory téléchargé, cliquez sur **Choose File** .

    b. Dans la zone de texte **Remote Login URL** (URL de connexion à distance), collez la valeur **URL de connexion** que vous avez copiée dans le portail Azure.
   
    c. Dans la zone de texte **Logout landing URL** (URL de la page de déconnexion), collez la valeur de **URL de déconnexion** que vous avez copiée à partir du portail Azure.

    d. Cliquez sur **Save my changes**.

### <a name="create-new-relic-by-account-test-user"></a>Créer un utilisateur de test New Relic by Account

1. Connectez-vous à votre site d’entreprise **New Relic by Account** en tant qu’administrateur.

2. Dans le menu situé en haut, cliquez sur **Account Settings**.
   
    ![Capture d’écran montrant l’élément Account settings sélectionné dans la page d’accueil.](./media/new-relic-tutorial/account.png "Account Settings")

3. Dans le volet **Account** situé sur le côté gauche, cliquez sur **Summary**, puis sur **Add user**.
   
    ![Capture d’écran montrant le volet Summary dans lequel vous pouvez sélectionner Add user.](./media/new-relic-tutorial/add.png "Account Settings")

4. Dans la boîte de dialogue **Active users** , procédez comme suit :
   
    ![Utilisateurs actifs](./media/new-relic-tutorial/user.png "Active users")
   
    a. Dans la zone de texte **Email** , tapez l’adresse de messagerie d’un utilisateur Azure Active Directory valide à approvisionner.

    b. Pour **Role**, sélectionnez **User**.

    c. Cliquez sur **Add this user**.

> [!NOTE]
> Vous pouvez utiliser tout autre outil ou n’importe quelle API de création de compte d’utilisateur fournis par New Relic by Account pour provisionner des comptes d’utilisateurs Azure AD.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Cette opération redirige vers l’URL de connexion à New Relic by Account, d’où vous pouvez lancer le processus de connexion. 

* Accédez directement à l’URL de connexion New Relic by Account pour lancer le processus de connexion.

* Vous pouvez utiliser Mes applications de Microsoft. Le fait de cliquer sur la vignette New Relic by Account dans Mes applications vous redirige vers l’URL de connexion New Relic by Account. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez configuré New Relic by Account, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
