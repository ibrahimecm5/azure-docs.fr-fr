---
title: 'Tutoriel : Intégration d’Azure Active Directory à LogMeIn | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et LogMeIn.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/01/2021
ms.author: jeedes
ms.openlocfilehash: ca6d094c14968f2208da95c4631cb06232db2806
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132310845"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-logmein"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory à LogMeIn

Dans ce tutoriel, vous apprenez à intégrer LogMeIn à Azure Active Directory (Azure AD). Quand vous intégrez LogMeIn à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à LogMeIn.
* Permettre à vos utilisateurs de se connecter automatiquement à LogMeIn avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement LogMeIn avec l’authentification unique (SSO) activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* LogMeIn prend en charge l’authentification unique lancée par le **fournisseur de services et le fournisseur d’identité**.
* LogMeIn prend en charge le [provisionnement d’utilisateurs automatisé](logmein-provisioning-tutorial.md).

## <a name="adding-logmein-from-the-gallery"></a>Ajout de LogMeIn à partir de la galerie

Pour configurer l’intégration de LogMeIn à Azure AD, vous devez ajouter LogMeIn à votre liste d’applications SaaS managées à partir de la galerie.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **LogMeIn** dans la zone de recherche.
1. Sélectionnez **LogMeIn** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-logmein"></a>Configurer et tester l’authentification unique Azure AD pour LogMeIn

Configurez et testez l’authentification unique Azure AD dans LogMeIn à l’aide d’un utilisateur de test nommé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur associé dans LogMeIn.

Pour configurer et tester l’authentification unique Azure AD dans LogMeIn, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    * **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    * **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique dans LogMeIn](#configure-logmein-sso)** pour configurer les paramètres d’authentification unique côté application.
    * **[Créer un utilisateur de test dans LogMeIn](#create-logmein-test-user)** pour avoir un équivalent de B.Simon dans LogMeIn lié à la représentation Azure AD associée.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, dans la page d’intégration de l’application **LogMeIn**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, l’utilisateur n’a rien à faire, car l’application est déjà intégrée à Azure.

1. Cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes si vous souhaitez configurer l’application en mode lancé par le **fournisseur de services** :

    a. Dans la zone de texte **URL de connexion**, tapez l’URL : `https://authentication.logmeininc.com/login?service=https%3A%2F%2Fmyaccount.logmeininc.com`

1. Votre application LogMeIn s’attend à recevoir les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à votre configuration d’attributs du jeton SAML. La capture d’écran suivante montre la liste des attributs par défaut, où **Identificateur d’utilisateur unique** est mappé avec **user.userprincipalname**. L’application LogMeIn s’attendant à ce que **Identificateur d’utilisateur unique** soit mappé avec **user.mail**, vous devez modifier le mappage d’attributs en cliquant sur l’icône **Modifier**, et changer le mappage d’attributs.

    ![image](common/default-attributes.png)

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur le bouton Copier pour copier l’**URL des métadonnées de fédération d’application**, puis enregistrez-la sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/copy-metadataurl.png)

6. Dans la section **Configurer LogMeIn**, copiez la ou les URL appropriées, en fonction de vos besoins.

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

Dans cette section, vous autorisez B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à LogMeIn.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **LogMeIn**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-logmein-sso"></a>Configurer l’authentification unique dans LogMeIn

1. Pour automatiser la configuration dans LogMeIn, vous devez installer l’**extension de navigateur My Apps Secure Sign-in** en cliquant sur **Installer l’extension**.

    ![Extension My apps](common/install-myappssecure-extension.png)

1. Une fois l’extension ajoutée au navigateur, un clic sur **Configurer LogMeIn** vous redirige vers l’application LogMeIn. À partir de là, indiquez les informations d’identification de l’administrateur pour vous connecter à LogMeIn. Cette extension de navigateur configure automatiquement l’application et automatise les étapes 3 à 5.

    ![Configuration](common/setup-sso.png)

1. Si vous souhaitez configurer LogMeIn manuellement, dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise LogMeIn en tant qu’administrateur.

1. Accédez à l’onglet **Identity Provider** (Fournisseur d’identité) et, dans la zone de texte **Metadata url** (URL des métadonnées), collez l’**URL des métadonnées de fédération** que vous avez copiée dans le portail Azure.

    ![Capture d’écran de l’URL des métadonnées de fédération. ](./media/logmein-tutorial/configuration.png)

1. Cliquez sur **Enregistrer**.

### <a name="create-logmein-test-user"></a>Créer un utilisateur de test dans LogMeIn

1. Dans une autre fenêtre de navigateur, connectez-vous à votre site web LogMeIn en tant qu’administrateur.

1. Accédez à l’onglet **Users** (Utilisateurs) et cliquez sur **Add a user** (Ajouter un utilisateur).

    ![Capture d’écran du bouton Ajouter un utilisateur.](./media/logmein-tutorial/add-user.png)

1. Remplissez les champs obligatoires dans la page suivante et cliquez sur **Save** (Enregistrer).

    ![Capture d’écran des champs utilisateur.](./media/logmein-tutorial/create-user.png)

> [!NOTE]
> LogMeIn prend également en charge le provisionnement automatique d’utilisateurs ; vous trouverez plus d’informations [ici](./logmein-provisioning-tutorial.md) sur la façon de configurer ce dernier.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

#### <a name="sp-initiated"></a>Lancée par le fournisseur de services :

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion de LogMeIn, où vous pouvez lancer le flux de connexion.  

* Accédez directement à l’URL de connexion de LogMeIn pour lancer le flux de connexion.

#### <a name="idp-initiated"></a>Lancée par le fournisseur d’identité :

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors automatiquement connecté à l’instance de LogMeIn pour laquelle vous avez configuré l’authentification unique 

Vous pouvez aussi utiliser Mes applications de Microsoft pour tester l’application dans n’importe quel mode. Quand vous cliquez sur la vignette LogMeIn dans Mes applications, si le mode Fournisseur de services est configuré, vous êtes redirigé vers la page de connexion de l’application pour lancer le flux de connexion. S’il s’agit du mode Fournisseur d’identité, vous êtes automatiquement connecté à l’instance de LogMeIn pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré LogMeIn, vous pouvez appliquer des contrôles de session qui protègent contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Les contrôles de session sont étendus à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
