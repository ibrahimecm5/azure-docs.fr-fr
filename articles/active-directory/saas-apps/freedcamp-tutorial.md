---
title: 'Tutoriel : Intégration d’Azure Active Directory à Freedcamp | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Freedcamp.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/30/2021
ms.author: jeedes
ms.openlocfilehash: 847b3ed779777badaa5b6105f561bb2034e9aa0e
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132334242"
---
# <a name="tutorial-integrate-freedcamp-with-azure-active-directory"></a>Tutoriel : Intégrer Freedcamp à Azure Active Directory

Dans ce didacticiel, vous allez apprendre à intégrer Freedcamp à Azure Active Directory (Azure AD). Lorsque vous intégrez Freedcamp à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Freedcamp.
* Permettre à vos utilisateurs de se connecter automatiquement à Freedcamp avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Freedcamp pour lequel l’authentification unique (SSO) est activée.

> [!NOTE]
> Cette intégration peut également être utilisée à partir de l’environnement cloud US Government Azure AD. Cette application est disponible dans la Galerie d’applications cloud US Government Azure AD et peut être configurée de la même façon que dans le cloud public.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* Freedcamp prend en charge l’authentification unique lancée par le **fournisseur de services et le fournisseur d’identité**.

## <a name="add-freedcamp-from-the-gallery"></a>Ajouter Freedcamp depuis la galerie

Pour configurer l’intégration de Freedcamp avec Azure AD, vous devez ajouter Freedcamp disponible dans la galerie, à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Freedcamp** dans la zone de recherche.
1. Sélectionnez **Freedcamp** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-freedcamp"></a>Configurer et tester l’authentification unique Azure AD pour Freedcamp

Configurez et testez l’authentification unique Azure AD avec Freedcamp à l’aide d’un utilisateur de test appelé **Britta Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Freedcamp associé.

Pour configurer et tester l’authentification unique Azure AD avec Freedcamp, procédez comme suit :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Freedcamp](#configure-freedcamp-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test Freedcamp](#create-freedcamp-test-user)** pour avoir un équivalent de B.Simon dans Freedcamp lié à la représentation Azure AD associée.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **Freedcamp**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon pour **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, si vous souhaitez configurer l’application en mode lancé par le **fournisseur d’identité**, effectuez les étapes suivantes :

    1. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<SUBDOMAIN>.freedcamp.com/sso/<UNIQUEID>`

    2. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://<SUBDOMAIN>.freedcamp.com/sso/acs/<UNIQUEID>`

1. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<SUBDOMAIN>.freedcamp.com/login`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL de réponse et l’URL de connexion réels. Les utilisateurs peuvent entrer également les valeurs d’URL de leur propre domaine client qui ne sont pas nécessairement dans le modèle `freedcamp.com` ; il peut s’agir de n’importe quelle valeur de domaine client propre à leur instance d’application. Vous pouvez également contacter [l’équipe du support client de Freedcamp ](mailto:devops@freedcamp.com) pour plus d’informations sur les modèles d’URL.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **Certificat (Base64)** , puis sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

   ![Lien Téléchargement de certificat](common/certificatebase64.png)

1. Dans la section **Configurer Freedcamp**, copiez la ou les URL appropriées en fonction de vos besoins.

   ![Copier les URL de configuration](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

Dans cette section, vous allez créer un utilisateur de test appelé Britta Simon dans le Portail Azure.

1. Dans le volet gauche du Portail Azure, sélectionnez **Azure Active Directory**, **Utilisateurs**, puis **Tous les utilisateurs**.
1. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.
1. Dans les propriétés **Utilisateur**, effectuez les étapes suivantes :
   1. Dans le champ **Nom**, entrez `Britta Simon`.  
   1. Dans le champ **Nom de l’utilisateur**, entrez username@companydomain.extension. Par exemple : `BrittaSimon@contoso.com`.
   1. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.
   1. Cliquez sur **Créer**.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Freedcamp.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Freedcamp**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.
1. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-freedcamp-sso"></a>Configurer l’authentification unique Freedcamp

1. Pour automatiser la configuration dans Freedcamp, vous devez installer l’**extension de navigateur My Apps Secure Sign-in** en cliquant sur **Install the extension** (Installer l’extension).

    ![Extension My apps](common/install-myappssecure-extension.png)

2. Après l’ajout de l’extension au navigateur, cliquez sur **Configurer Freedcamp** pour être dirigé vers l’application Freedcamp. À partir de là, indiquez les informations d’identification de l’administrateur pour vous connecter à Freedcamp. Cette extension de navigateur configure automatiquement l’application et automatise les étapes 3 à 5.

    ![Configuration](common/setup-sso.png)

3. Si vous souhaitez configurer manuellement Freedcamp, ouvrez une nouvelle fenêtre de navigateur web, connectez-vous à votre site d’entreprise Freedcamp en tant qu’administrateur et procédez comme suit :

4. Dans le coin supérieur droit de la page, cliquez sur **Profil**, puis accédez à **Mon compte**.

    ![Capture d’écran montrant « Profile » et « My Account » sélectionnés.](./media/freedcamp-tutorial/config01.png)

5. Sur le côté gauche de la barre de menus, cliquez sur **SSO** et dans la page **Vos connexions SSO**, effectuez les étapes suivantes :

    ![Capture d’écran montrant « SSO » sélectionné dans la barre de menus de gauche et la page « Your SSO connections » avec des valeurs entrées et le bouton « Submit » sélectionné.](./media/freedcamp-tutorial/config02.png)

    a. Dans la zone de texte **Title** , tapez le titre.

    b. Dans la zone de texte **ID d’entité**, collez la valeur **Identificateur Azure AD** que vous avez copiée dans le Portail Azure.

    c. Dans la zone de texte **URL de connexion**, collez la valeur d’**URL de connexion** que vous avez copiée sur le Portail Azure.

    d. Ouvrez le certificat encodé en Base64 dans le bloc-notes, copiez son contenu et collez-le dans la zone de texte **Certificat**.

    e. Cliquez sur **Envoyer**.

### <a name="create-freedcamp-test-user"></a>Créer un utilisateur de test Freedcamp

Pour se connecter à Freedcamp, les utilisateurs d’Azure AD doivent être approvisionnés dans Freedcamp. Dans Freedcamp, le provisionnement est une tâche manuelle.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Dans une autre fenêtre de navigateur web, connectez-vous à Freedcamp en tant qu’administrateur de la sécurité.

2. Dans l’angle supérieur droit de la page, cliquez sur **Profile**, puis accédez à **Manage System**.

    ![Configuration de Freedcamp](./media/freedcamp-tutorial/config03.png)

3. Dans la partie droite de la page Gérer le système, procédez comme suit :

    ![Capture d’écran montrant le bouton « Add Or Invite Users » sélectionné, le champ « Email » mis en évidence et le bouton « Add User » sélectionné.](./media/freedcamp-tutorial/config04.png)

    a. Cliquez sur **Ajouter ou inviter les utilisateurs**.

    b. Dans la zone de texte **Email** (E-mail), entrez l’adresse e-mail de l’utilisateur, par exemple `Brittasimon@contoso.com`.

    c. Cliquez sur **Add User**.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

#### <a name="sp-initiated"></a>Lancée par le fournisseur de services :

* Cliquez sur **Tester cette application** dans le portail Azure. Cette opération redirige vers l’URL de connexion Freedcamp, d’où vous pouvez lancer le processus de connexion.  

* Accédez directement à l'URL de connexion à Freedcamp pour lancer le flux de connexion.

#### <a name="idp-initiated"></a>Lancée par le fournisseur d’identité :

* Cliquez sur **Tester cette application** dans le portail Azure : vous devez être connecté automatiquement à l’instance de Freedcamp pour laquelle vous avez configuré l’authentification unique. 

Vous pouvez aussi utiliser Mes applications de Microsoft pour tester l’application dans n’importe quel mode. Si, quand vous cliquez sur la vignette Freedcamp dans Mes applications, le mode Fournisseur de services est configuré, vous êtes redirigé vers la page de connexion de l’application pour lancer le flux de connexion ; s’il s’agit du mode Fournisseur d’identité, vous êtes automatiquement connecté à l’instance Freedcamp pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré Freedcamp, vous pouvez appliquer le contrôle de session, qui protège en temps réel contre l’exfiltration et l’infiltration des données sensibles de votre organisation. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
