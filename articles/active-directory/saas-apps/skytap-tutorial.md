---
title: 'Tutoriel : Intégration de l’authentification unique Azure Active Directory avec Single Sign-on for Skytap | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Single Sign-on for Skytap.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/28/2021
ms.author: jeedes
ms.openlocfilehash: d84a68a058fb4b4f1df5d3df3ac161121e4b6d5b
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132317008"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-single-sign-on-for-skytap"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory à Single Sign-on for Skytap

Ce tutoriel explique comment intégrer Single Sign-on for Skytap avec Azure Active Directory (Azure AD). Quand vous intégrez Single Sign-on for Skytap avec Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Single Sign-on for Skytap.
* Permettre à vos utilisateurs de se connecter automatiquement à Single Sign-on for Skytap avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central, le portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Single Sign-on for Skytap pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* Single Sign-on for Skytap prend en charge l’authentification unique lancée par le fournisseur de services et le fournisseur d’identité.

## <a name="add-single-sign-on-for-skytap-from-the-gallery"></a>Ajout de Single Sign-on for Skytap depuis la galerie

Pour configurer l’intégration de Single Sign-on for Skytap à Azure AD, vous devez ajouter Single Sign-on for Skytap à partir de la galerie à votre liste d’applications SaaS managées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire, ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Single Sign-on for Skytap** dans la zone de recherche.
1. Sélectionnez **Single Sign-on for Skytap** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-single-sign-on-for-skytap"></a>Configuration et test de l’authentification unique Azure AD pour Skytap

Configurez et testez l’authentification unique Azure AD pour Single Sign-on for Skytap à l’aide d’un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, établissez un lien entre un utilisateur Azure AD et l’utilisateur associé dans Single Sign-on for Skytap.

Voici la procédure générale pour configurer et tester l’authentification unique Azure AD pour Single Sign-on for Skytap :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Single Sign-on for Skytap](#configure-single-sign-on-for-skytap-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test Single Sign-on for Skytap](#create-single-sign-on-for-skytap-test-user)** pour avoir un équivalent de B.Simon dans Single Sign-on for Skytap. Cet équivalent est lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Sur la page d’intégration de l’application **Single Sign-on for Skytap** du Portail Azure, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)
   
1. Dans la section **Configuration SAML de base**, si vous voulez configurer l’application en mode lancé par le **fournisseur d’identité**, entrez les valeurs pour les champs suivants :

    a. Dans la zone de texte **Identificateur**, saisissez une URL au format suivant : `http://pingone.com/<custom EntityID>`

    b. Dans la zone **URL de réponse**, tapez l’URL : `https://sso.connect.pingidentity.com/sso/sp/ACS.saml2`

1. Vous pouvez facultativement sélectionner **Définir des URL supplémentaires**, puis effectuer les étapes suivantes si vous voulez configurer l’application en mode lancé par le **fournisseur de services** :

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://sso.connect.pingidentity.com/sso/sp/initsso?saasid=<saasid>&idpid=<idpid>`

    
    b. Dans la zone de texte **État de relais**, tapez une URL au format suivant : `https://pingone.com/1.0/<custom ID>`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL de réponse, l’URL de connexion et l’État de relais exacts. Pour obtenir ces valeurs, contactez l’[équipe de support client Single Sign-on for Skytap](mailto:support@skytap.com). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **XML de métadonnées de fédération**. Sélectionnez **Télécharger** pour télécharger le fichier de métadonnées et l’enregistrer sur votre ordinateur.

    ![Capture d’écran du lien de téléchargement de certificat](common/metadataxml.png)

1. Dans la section **Configurer Single Sign-on for Skytap**, copiez la ou les URL appropriées en fonction de vos besoins.

    ![Capture d’écran de copie des URL de configuration](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

Dans cette section, vous allez créer un utilisateur de test appelé B. Simon sur le portail Azure.

1. Dans le volet gauche du portail Azure, sélectionnez **Azure Active Directory** > **Utilisateurs** > **Tous les utilisateurs**.
1. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.
1. Dans les propriétés **Utilisateur**, effectuez les étapes suivantes :
   1. Dans le champ **Nom**, entrez `B.Simon`.  
   1. Dans le champ **Nom de l’utilisateur**, entrez username@companydomain.extension. Par exemple : `B.Simon@contoso.com`.
   1. Cochez la case **Afficher le mot de passe**, puis notez la valeur figurant dans le champ **Mot de passe**.
   1. Sélectionnez **Create** (Créer).

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Single Sign-on for Skytap.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Single Sign-on for Skytap**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-single-sign-on-for-skytap-sso"></a>Configurer l’authentification unique Single Sign-on for Skytap

Pour configurer l’authentification unique côté Single Sign-on for Skytap, vous devez envoyer le fichier **XML des métadonnées de fédération** téléchargé et les URL appropriées, copiées à partir du Portail Azure à l’[équipe de support client Single Sign-on for Skytap](mailto:support@skytap.com). Celle-ci configure ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

### <a name="create-single-sign-on-for-skytap-test-user"></a>Créer un utilisateur de test Single Sign-on for Skytap

Dans cette section, vous allez créer un utilisateur appelé B.Simon dans Single Sign-on for Skytap. Travaillez en collaboration avec l’[équipe de support client Single Sign-on for Skytap](mailto:support@skytap.com) pour ajouter les utilisateurs dans la plateforme Single Sign-on for Skytap. Vous ne pouvez pas utiliser l’authentification unique avant d’avoir créé et activé les utilisateurs.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

#### <a name="sp-initiated"></a>Lancée par le fournisseur de services :

* Cliquez sur **Tester cette application** dans le portail Azure. L’URL de connexion à Single Sign-on for Skytap s’ouvre. Vous pouvez alors lancer le flux de connexion.  

* Accédez directement à l’URL de connexion à Single Sign-on for Skytap pour lancer le flux de connexion.

#### <a name="idp-initiated"></a>Lancée par le fournisseur d’identité :

* Cliquez sur **Tester cette application** sur le Portail Azure. La connexion est alors établie automatiquement avec l’instance de Single Sign-on for Skytap pour laquelle vous avez configuré l’authentification unique. 

Vous pouvez aussi utiliser Mes applications de Microsoft pour tester l’application dans n’importe quel mode. Si le mode Fournisseur de services est configuré, la page de connexion à l’application (permettant de lancer le flux de connexion) s’ouvre quand vous cliquez sur la vignette Single Sign-on for Skytap dans Mes applications. Avec le mode Fournisseur d’identité, la connexion est établie automatiquement avec l’instance de Single Sign-on for Skytap pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré Single Sign-on for Skytap, vous pouvez appliquer le contrôle de session, qui empêche en temps réel l’exfiltration et l’infiltration des données sensibles de votre organisation. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
