---
title: 'Tutoriel : Intégration de l’authentification unique (SSO) Azure Active Directory à Workteam | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Workteam.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/02/2021
ms.author: jeedes
ms.openlocfilehash: 6ddafdeb15379bee87344e56e51def4a73bc1bf8
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132303659"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workteam"></a>Tutoriel : Intégration de l’authentification unique (SSO) Azure Active Directory à Workteam

Dans ce tutoriel, vous allez apprendre à intégrer Workteam à Azure Active Directory (Azure AD). Quand vous intégrez Workteam à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Workteam.
* Permettre à vos utilisateurs de se connecter automatiquement à Workteam avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Workteam pour lequel l’authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* Workteam prend en charge l’authentification unique lancée par le **fournisseur de services et par le fournisseur d’identité**.
* Workteam prend en charge l’[attribution automatisée d’utilisateurs](workteam-provisioning-tutorial.md).

## <a name="add-workteam-from-the-gallery"></a>Ajouter Workteam à partir de la galerie

Pour configurer l’intégration de Workteam à Azure AD, vous devez ajouter Workteam à partir de la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Workteam** dans la zone de recherche.
1. Sélectionnez **Workteam** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-workteam"></a>Configurer et tester l’authentification unique Azure AD pour Workteam

Configurez et testez l’authentification unique Azure AD avec Workteam pour un utilisateur de test nommé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Workteam associé.

Pour configurer et tester l’authentification unique Azure AD avec Workteam, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Workteam](#configure-workteam-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test Workteam](#create-workteam-test-user)** pour avoir un équivalent de B.Simon dans Workteam lié à la représentation Azure AD associée.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, dans la page d’intégration de l’application **Workteam**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, l’application est préconfigurée en mode Lancement par le **fournisseur d’identité** et les URL nécessaires sont déjà préremplies avec Azure. L’utilisateur doit enregistrer la configuration en cliquant sur le bouton **Enregistrer**.

1. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    Dans la zone de texte **URL de connexion**, tapez l’URL : `https://app.workte.am`

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **Certificat (en base64)** , puis sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

1. Dans la section **Configurer Workteam**, copiez l’URL ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Workteam.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Workteam**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-workteam-sso"></a>Configurer l’authentification unique Workteam

1. Pour automatiser la configuration dans Workteam, vous devez installer l’**extension de navigateur My Apps Secure Sign-in** en cliquant sur **Installer l’extension**.

    ![Extension My apps](common/install-myappssecure-extension.png)

2. Après l’ajout de l’extension au navigateur, cliquez sur **Configurer Workteam** pour être redirigé vers l’application Workteam. À partir de là, indiquez les informations d’identification de l’administrateur pour vous connecter à Workteam. Cette extension de navigateur configure automatiquement l’application et automatise les étapes 3 à 6.

    ![Configuration](common/setup-sso.png)

3. Si vous souhaitez configurer manuellement Workteam, ouvrez une nouvelle fenêtre de navigateur web, connectez-vous à votre site d’entreprise Workteam en tant qu’administrateur et effectuez les étapes suivantes :

4. Dans l’angle supérieur droit, cliquez sur **l’icône de profil**, puis cliquez sur **Organization settings** (Paramètres de l’organisation). 

    ![Capture d’écran montrant les paramètres Workteam.](./media/workteam-tutorial/settings.png)

5. Dans la section **AUTHENTICATION** (Authentification), cliquez sur **l’icône Paramètres**.

     ![Capture d’écran montrant les paramètres Azure.](./media/workteam-tutorial/azure.png)

6. Sur la page **SAML Settings** , procédez comme suit :

     ![Capture d’écran montrant les paramètres Workteam SAML.](./media/workteam-tutorial/certificate.png)

    a. Sous **SAML IdP** (Fournisseur d’identité SAML), sélectionnez **AD Azure**.

    b. Dans la zone de texte **URL du service d’authentification unique SAML**, collez la valeur de l’**URL de connexion** que vous avez copiée à partir du Portail Azure.

    c. Dans la zone de texte **ID d’entité SAML**, collez l’**Identificateur Azure AD** que vous avez copié dans le Portail Azure.

    d. Dans le Bloc-notes, ouvrez le **certificat codé en base 64** téléchargé à partir du Portail Azure, copiez son contenu, puis collez-le dans la zone **SAML Signing Certificate (Base64)** (Certificat de signature SAML (Base64)).

    e. Cliquez sur **OK**.

### <a name="create-workteam-test-user"></a>Créer un utilisateur de test Workteam

Pour se connecter à Workteam, les utilisateurs d’Azure AD doivent être approvisionnés dans Workteam. Dans Workteam, l’approvisionnement est une tâche manuelle.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous à Workteam en tant qu’administrateur de la sécurité.

2. En haut et au milieu de la page **Organization settings** (Paramètres de l’organisation), cliquez sur **USERS** (Utilisateurs), puis cliquez sur **NEW USER** (Nouvel utilisateur).

    ![Capture d’écran montrant l’utilisateur Workteam.](./media/workteam-tutorial/user.png)

3. Dans la page **New employee** (Nouvel employé), effectuez les étapes suivantes :

    ![Capture d’écran montrant le nouvel utilisateur Workteam.](./media/workteam-tutorial/new-user.png)

    a. Dans la zone de texte **Name** (Name), entrez le nom d’un utilisateur, par exemple **B.Simon**.

    b. Dans la zone de texte **E-mail**, entrez l’adresse e-mail de l’utilisateur, comme `B.Simon\@contoso.com`.

    c. Cliquez sur **OK**.

> [!NOTE]
> Workteam prend également en charge l’attribution automatique d’utilisateurs. Des informations supplémentaires sur la configuration de cette fonctionnalité sont disponibles [ici](./workteam-provisioning-tutorial.md).

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

#### <a name="sp-initiated"></a>Lancée par le fournisseur de services :

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL d’authentification Workteam, à partir de laquelle vous pouvez lancer le processus de connexion.  

* Accédez directement à l’URL d’authentification Workteam pour lancer le processus de connexion.

#### <a name="idp-initiated"></a>Lancée par le fournisseur d’identité :

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors automatiquement connecté à l’application Workteam pour laquelle vous avez configuré l’authentification unique. 

Vous pouvez aussi utiliser Mes applications de Microsoft pour tester l’application dans n’importe quel mode. Quand vous cliquez sur la vignette Workteam dans Mes applications, si le mode Fournisseur de services est configuré, vous êtes redirigé vers la page d’authentification de l’application pour lancer le processus de connexion. Si le mode Fournisseur d’identité est configuré, vous êtes automatiquement connecté à l’application Workteam pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré Workteam, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
