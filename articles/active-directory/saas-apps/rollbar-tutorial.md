---
title: 'Didacticiel : Intégration d’Azure Active Directory à Rollbar | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Rollbar.
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
ms.openlocfilehash: 1761979d554bc3318ef8f2e06fe872a6e674d0c4
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132341670"
---
# <a name="tutorial-azure-active-directory-integration-with-rollbar"></a>Didacticiel : Intégration d’Azure AD à Rollbar

Dans ce tutoriel, vous allez apprendre à intégrer Rollbar à Azure Active Directory (Azure AD). Quand vous intégrez Rollbar à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Rollbar.
* Permettre à vos utilisateurs de se connecter automatiquement à Rollbar avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à Rollbar, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un [compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Rollbar pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Rollbar prend en charge l’authentification unique initiée par le **fournisseur de services et le fournisseur d’identité**.
* Rollbar prend en charge l’[attribution automatisée d’utilisateurs](rollbar-provisioning-tutorial.md).

> [!NOTE]
> L’identificateur de cette application étant une valeur de chaîne fixe, une seule instance peut être configurée dans un locataire.

## <a name="add-rollbar-from-the-gallery"></a>Ajout de Rollbar depuis la galerie

Pour configurer l’intégration de Rollbar avec Azure AD, vous devez ajouter Rollbar, disponible dans la galerie, à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, saisissez **Rollbar** dans la zone de recherche.
1. Sélectionnez **Rollbar** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-rollbar"></a>Configurer et tester l’authentification unique Azure AD pour Rollbar

Configurez et testez l’authentification unique Azure AD avec Rollbar à l’aide d’un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur associé dans Rollbar.

Pour configurer et tester l’authentification unique Azure AD avec Rollbar, procédez comme suit :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique de Rollbar](#configure-rollbar-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test Rollbar](#create-rollbar-test-user)** pour avoir un équivalent de B.Simon dans Rollbar lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **Rollbar**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, si vous souhaitez configurer l’application en mode lancé par le **fournisseur d’identité**, effectuez les étapes suivantes :

    a. Dans la zone de texte **Identificateur**, tapez une l’URL : `https://saml.rollbar.com`

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://rollbar.com/<ACCOUNT_NAME>/saml/sso/azure/`

5. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://rollbar.com/<ACCOUNT_NAME>/saml/login/azure/`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de réponse et l’URL de connexion réelles. Pour obtenir ces valeurs, contactez [l’équipe de support technique Rollbar](mailto:support@rollbar.com). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

6. Sur la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le fichier **XML de métadonnées de fédération** en fonction des options définies selon vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

7. Dans la section **Configurer Rollbar**, copiez la ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Rollbar.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Rollbar**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-rollbar-sso"></a>Configurer l’authentification unique de Rollbar

1. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Rollbar en tant qu’administrateur.

1. Cliquez sur les **paramètres du profil** dans l’angle supérieur droit puis cliquez sur les **paramètres de nom du compte**.

    ![Capture d’écran affichant la sélection des paramètres d’un nom de compte dans Profile Settings.](./media/rollbar-tutorial/general.png)

1. Cliquez sur **Fournisseur d’identité** sous SÉCURITÉ.

    ![Capture d’écran montrant l’élément Identity Provider sélectionné sous SECURITY.](./media/rollbar-tutorial/security.png)

1. Dans la section **Fournisseur d’identité SAML**, procédez comme suit :

    ![Capture d’écran montrant la zone SAML Identity Provider, dans laquelle vous pouvez entrer les valeurs décrites.](./media/rollbar-tutorial/configure.png)

    a. Sélectionnez **AZURE** dans la liste déroulante **Fournisseur d’identité SAML**.

    b. Ouvrez votre fichier de métadonnées dans le Bloc-notes, copiez son contenu dans le Presse-papiers, puis collez-le dans la zone de texte **Métadonnées SAML**.

    c. Cliquez sur **Enregistrer**.

1. Après avoir cliqué sur le bouton Enregistrer, l’écran se présente comme suit :

    ![Capture d’écran affichant les résultats dans la page SAML Identity Provider.](./media/rollbar-tutorial/identity-provider.png)

    > [!NOTE]
    > Pour exécuter l’étape suivante, vous devez d’abord vous ajouter vous-même comme utilisateur dans l’application Rollbar dans Azure.
    
    a. Si vous voulez que tous les utilisateurs s’authentifient via Azure, cliquez sur **log in via your identity provider (Se connecter via votre fournisseur d’identité)** pour vous authentifier une nouvelle fois via Azure.  

    b.  Une fois que vous êtes revenu sur l’écran, sélectionnez la case à cocher **Demander la connexion via un fournisseur d’identité SAML**.

    b. Cliquez sur **Enregistrer**.

### <a name="create-rollbar-test-user"></a>Créer un utilisateur de test Rollbar

Pour permettre aux utilisateurs d’Azure AD de se connecter à Rollbar, vous devez les approvisionner dans Rollbar. Dans le cas de Rollbar, le provisionnement est une tâche manuelle.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous au site d’entreprise Rollbar en tant qu’administrateur.

1. Cliquez sur les **paramètres du profil** dans l’angle supérieur droit puis cliquez sur les **paramètres de nom du compte**.

    ![Utilisateur](./media/rollbar-tutorial/general.png)

1. Cliquez sur **Utilisateurs**.

    ![Ajouter un employé](./media/rollbar-tutorial/user.png)

1. Cliquez sur **Inviter des membres d’équipe**.

    ![Capture d’écran montrant la sélection de l’option Invite Team Members.](./media/rollbar-tutorial/invite-user.png)

1. Dans la zone de texte, entrez le nom d’un utilisateur comme **brittasimon\@contoso.com**, puis cliquez sur **Ajouter/inviter**.

    ![Capture d’écran montrant la zone Add/Invite Members avec une adresse fournie.](./media/rollbar-tutorial/add-user.png)

1. L’utilisateur reçoit une invitation et, après l’avoir acceptée, il est créé dans le système.

> [!NOTE]
> Rollbar prend également en charge l’attribution automatique d’utilisateurs. Vous trouverez plus d’informations sur la configuration de cette fonctionnalité [ici](./rollbar-provisioning-tutorial.md).

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

#### <a name="sp-initiated"></a>Lancée par le fournisseur de services :

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion de Rollbar, à partir de laquelle vous pouvez lancer le flux de connexion.  

* Accédez directement à l’URL de connexion de Rollbar pour initier le flux de connexion.

#### <a name="idp-initiated"></a>Lancée par le fournisseur d’identité :

* Cliquez sur **Tester cette application** dans Portail Azure : vous devez être connecté automatiquement à l’instance de Rollbar pour laquelle vous avez configuré l’authentification unique. 

Vous pouvez aussi utiliser Mes applications de Microsoft pour tester l’application dans n’importe quel mode. Quand vous cliquez sur la vignette Rollbar dans Mes applications, si le mode Fournisseur de services est configuré, vous êtes redirigé vers la page de connexion de l’application pour lancer le flux de connexion ; si le mode Fournisseur d’identité est configuré, vous êtes automatiquement connecté à l’instance de Rollbar pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez configuré Rollbar, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
