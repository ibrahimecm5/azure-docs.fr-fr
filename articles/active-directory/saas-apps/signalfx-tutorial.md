---
title: 'Tutoriel : Intégration de l’authentification unique (SSO) Azure Active Directory avec SignalFx | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et SignalFx.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/25/2021
ms.author: jeedes
ms.openlocfilehash: f97f8710df160608fb01e0b9d708c4a9d1b2373d
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132304645"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-signalfx"></a>Tutoriel : Intégration de l’authentification unique (SSO) Azure Active Directory avec SignalFx

Ce tutoriel explique comment intégrer SignalFx à Azure Active Directory (Azure AD). Lorsque vous intégrez SignalFx avec Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à SignalFx.
* Permettre à vos utilisateurs de se connecter automatiquement à SignalFx avec leur compte Azure AD.
* Gérer vos comptes à un seul et même endroit (le portail Azure).

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Abonnement SignalFx pour lequel l’authentification unique (SSO) est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* SignalFx prend en charge l’authentification unique lancée par le **fournisseur d’identité**.
* SignalFx prend en charge le provisionnement d’utilisateurs **juste-à-temps**.

## <a name="step-1-add-the-signalfx-application-in-azure"></a>Étape 1 : Ajouter l’application SignalFx dans Azure

Suivez ces instructions pour ajouter l’application SignalFx à votre liste d’applications SaaS managées.

1. Connectez-vous au portail Azure.
1. Dans la fenêtre de navigation de gauche, sélectionnez **Azure Active Directory**.
1. Sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, dans la zone de recherche, entrez puis sélectionnez **SignalFx**.
     * Vous aurez peut-être à attendre quelques minutes avant que l’application soit ajoutée à votre locataire.
1. Laissez le portail Azure ouvert, puis ouvrez un nouvel onglet web.    

## <a name="step-2-begin-signalfx-sso-configuration"></a>Étape 2 : Commencer la configuration de l’authentification unique SignalFx

Suivez ces instructions pour commencer le processus de configuration de l’authentification unique SignalFx.

1. Dans l’onglet que vous venez d’ouvrir, accédez à l’interface utilisateur SignalFx pour vous y connecter. 
1. Dans le menu du haut, cliquez sur **Integrations**. 
1. Dans le champ de recherche, entrez puis sélectionnez **Azure Active Directory**.
1. Cliquez sur **Create New Integration** (Créer une intégration).
1. Dans **Name** (Nom), entrez un nom facilement reconnaissable pour vos utilisateurs.
1. Activez **Show on Login Page** (Afficher dans la page de connexion).
    * Cette fonctionnalité affiche un bouton personnalisé dans la page de connexion sur lequel vos utilisateurs peuvent cliquer. 
    * Les informations que vous avez entrées dans **Name** s’affichent sur le bouton. Vous devez donc entrer un **nom** que vos utilisateurs pourront facilement reconnaître. 
    * Cette option ne fonctionne que si vous utilisez un sous-domaine personnalisé pour l’application SignalFx, par exemple **nom_de_votre_entreprise.signalfx.com**. Pour obtenir un sous-domaine personnalisé, contactez le support SignalFx. 
1. Copiez l’**ID d’intégration**. Vous aurez besoin de ces informations dans une prochaine étape. 
1. Ne fermez pas l’interface utilisateur SignalFx. 

## <a name="step-3-configure-azure-ad-sso"></a>Étape 3 : Configurer l’authentification unique Azure AD

Suivez ces instructions pour activer l’authentification unique Azure AD dans le portail Azure.

1. Retournez dans le portail Azure et accédez à la page d’intégration de l’application **SignalFx**. Recherchez la section **Gérer**, puis sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la page **Configurer l’authentification unique avec SAML**, effectuez les étapes suivantes : 

    a. Dans **Identificateur**, entrez l’URL `https://api.<realm>.signalfx.com/v1/saml/metadata` et remplacez `<realm>` par votre domaine SignalFx. 

    b. Dans **URL de réponse**, entrez l’URL `https://api.<realm>.signalfx.com/v1/saml/acs/<integration ID>` et remplacez `<realm>` par votre domaine SignalFx. Ensuite, remplacez `<integration ID>` par l’**ID d’intégration** que vous avez copié précédemment à partir de l’interface utilisateur SignalFx.

1. L’application SignalFx attend les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à votre configuration d’attributs de jeton SAML. 
    
1. Vérifiez que les revendications suivantes correspondent aux attributs sources qui figurent dans Active Directory. 

    | Nom |  Attribut source|
    | ------------------- | -------------------- |
    | User.FirstName  | user.givenname |
    | User.email  | user.mail |
    | PersonImmutableID       | user.userprincipalname    |
    | User.LastName       | user.surname    |

    > [!NOTE]
    > Ce processus nécessite qu’Active Directory soit configuré avec au moins un domaine personnalisé vérifié, ainsi qu’avec un accès aux comptes de messagerie de ce domaine. Si vous avez des doutes ou si vous avez besoin d’aide pour cette configuration, contactez le support SignalFx.  

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **Certificat (en base64)** , puis sélectionnez **Télécharger**. Téléchargez le certificat et enregistrez-le sur votre ordinateur. Ensuite, copiez l’**URL des métadonnées de fédération d’application**, car vous en aurez besoin pour une prochaine étape dans l’interface utilisateur SignalFx. 

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

1. Dans la section **Configurer SignalFx**, copiez l’**identificateur Azure AD**. Vous aurez besoin de ces informations pour une prochaine étape dans l’interface utilisateur SignalFx. 

## <a name="step-4-create-an-azure-ad-test-user"></a>Étape 4 : Créer un utilisateur de test Azure AD

Dans cette section, vous allez créer un utilisateur de test appelé B. Simon dans le portail Azure.

1. Dans le volet gauche du Portail Azure, sélectionnez **Azure Active Directory**, **Utilisateurs**, puis **Tous les utilisateurs**.
1. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.
1. Dans les propriétés **Utilisateur**, effectuez les étapes suivantes :
   1. Dans le champ **Nom**, entrez `B.Simon`.  
   1. Dans le champ **Nom de l’utilisateur**, entrez username@companydomain.extension. Par exemple : `B.Simon@contoso.com`.
   1. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.
   1. Cliquez sur **Créer**.

## <a name="step-5-assign-the-azure-ad-test-user"></a>Étape 5 : Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à SignalFx.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **SignalFx**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="step-6-complete-the-signalfx-sso-configuration"></a>Étape 6 : Terminer la configuration de l’authentification unique SignalFx 

1. Ouvrez l’onglet précédent, puis revenez à l’interface utilisateur SignalFx pour afficher la page d’intégration Azure Active Directory. 
1. En regard de **Certificate (Base64)** (Certificat en base64), cliquez sur **Upload File** (Charger le fichier), puis recherchez le fichier **Base64 encoded certificate** (Certificat encodé en base 64) que vous avez téléchargé précédemment à partir du portail Azure.
1. En regard de **Azure AD Identifier** collez l’**identificateur Azure AD** que vous avez copié précédemment à partir du portail Azure. 
1. En regard de **Federation Metadata URL** (URL des métadonnées de fédération), collez la valeur du champ **URL des métadonnées de fédération d’application** que vous avez copiée à partir du portail Azure. 
1. Cliquez sur **Enregistrer**.

## <a name="step-7-test-sso"></a>Étape 7 : Tester l’authentification unique (SSO)

Lisez les informations suivantes pour savoir comment tester l’authentification unique, et pour connaître les exigences relatives à une première connexion à SignalFx. 

### <a name="test-logins"></a>Tester les connexions

* Pour tester la connexion, vous devez utiliser une fenêtre privée, ou vous pouvez vous déconnecter du portail Azure. Si ce n’est pas le cas, les cookies de l’utilisateur qui a configuré l’application empêcheront la connexion de l’utilisateur de test.

* Lorsqu’un utilisateur de test se connecte pour la première fois, Azure force la modification du mot de passe. Dans ce cas, le processus de connexion SSO n’est pas finalisé et l’utilisateur de test est dirigé vers le portail Azure. Pour résoudre ce problème, l’utilisateur de test doit modifier son mot de passe, accéder à la page de connexion de SignalFx ou à Mes applications, puis réessayer.
    * Un clic sur la vignette SignalFx dans Mes applications doit vous connecter automatiquement à SignalFx. 
        * Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

* L’application SignalFx est accessible à partir de Mes applications ou via une page de connexion personnalisée attribuée à l’organisation. L’utilisateur de test doit tester l’intégration à partir de l’un de ces emplacements.
    * L’utilisateur test peut utiliser les informations d’identification créées précédemment dans cette procédure pour **b.simon\@contoso.com**.

### <a name="first-time-logins"></a>Premières connexions

* Lorsqu’un utilisateur se connecte pour la première fois à SignalFx à partir de l’authentification unique SAML, il reçoit un e-mail de SignalFx dans lequel se trouve un lien. L’utilisateur doit cliquer sur le lien pour s’authentifier. Cette validation par e-mail n’est à effectuer que lors de la première connexion. 

* SignalFx prend en charge la création d’utilisateur **juste-à-temps**, ce qui signifie que si un utilisateur n’existe pas dans SignalFx, le compte de cet utilisateur sera créé lors de sa première tentative de connexion.

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez configuré SignalFx, vous pouvez appliquer le contrôle de session, qui protège l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
