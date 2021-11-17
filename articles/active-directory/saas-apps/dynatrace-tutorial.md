---
title: 'Tutoriel : Intégration de l’authentification unique Azure Active Directory à Dynatrace | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Dynatrace.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/16/2021
ms.author: jeedes
ms.openlocfilehash: e448c380d1755ebe36c0a1e8c459f3ae37f177a7
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132344576"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-dynatrace"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory à Dynatrace

Dans ce tutoriel, vous allez découvrir comment intégrer Dynatrace à Azure Active Directory (Azure AD). Quand vous intégrez Dynatrace à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Dynatrace.
* Permettre à vos utilisateurs de se connecter automatiquement à Dynatrace avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Abonnement Dynatrace pour lequel l’authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* Dynatrace prend en charge l’authentification unique lancée par **le fournisseur de services et le fournisseur d’identité**.
* Dynatrace prend en charge le provisionnement d’utilisateurs **juste-à-temps**.

> [!NOTE]
> L’identificateur de cette application est une valeur de chaîne fixe. Une seule instance peut être configurée dans un locataire.

## <a name="add-dynatrace-from-the-gallery"></a>Ajouter Dynatrace à partir de la galerie

Pour configurer l’intégration de Dynatrace à Azure AD, vous devez ajouter Dynatrace à partir de la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Dynatrace** dans la zone de recherche.
1. Sélectionnez **Dynatrace** dans le volet des résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-dynatrace"></a>Configurer et tester l’authentification unique Azure AD pour Dynatrace

Configurez et testez l’authentification unique Azure AD avec Dynatrace pour un utilisateur de test nommé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir une relation entre un utilisateur Azure AD et l’utilisateur Dynatrace associé.

Pour configurer et tester l’authentification unique Azure AD avec Dynatrace, suivez les indications des sections ci-après :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Dynatrace](#configure-dynatrace-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test Dynatrace](#create-dynatrace-test-user)** pour disposer dans Dynatrace d’un équivalent de B.Simon lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **Dynatrace**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, l’application est préconfigurée en mode Lancement par le **fournisseur d’identité** et les URL nécessaires sont déjà préremplies avec Azure. L’utilisateur doit enregistrer la configuration en cliquant sur le bouton **Enregistrer**.

1. Cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes pour configurer l’application en mode lancement par le **fournisseur de services** :

    Dans la zone de texte **URL de connexion**, tapez l’URL : `https://sso.dynatrace.com/`

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **XML de métadonnées de fédération**. Sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

1. Dans la section **Certificat de signature SAML**, sélectionnez le bouton **Modifier** pour ouvrir la boîte de dialogue **Certificat de signature SAML**. Suivez les étapes ci-dessous :

    ![Modifier le certificat de signature SAML](common/edit-certificate.png)

    a. Le paramètre **Option de signature** est prérempli. Vérifiez les paramètres selon votre organisation.

    b. Cliquez sur **Enregistrer**.

    ![Option de signature Communifire](./media/dynatrace-tutorial/tutorial-dynatrace-signing-option.png)

1. Dans la section **Configurer Dynatrace**, copiez l’URL ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Dynatrace.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Dynatrace**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-dynatrace-sso"></a>Configurer l’authentification unique Dynatrace

Pour configurer l’authentification unique côté **Dynatrace**, vous devez envoyer le fichier **XML des métadonnées de fédération** téléchargé et les URL appropriées, copiées à partir du portail Azure, à [Dynatrace](https://www.dynatrace.com/support/help/shortlink/users-sso-hub). Vous pouvez suivre les instructions fournies sur le site web Dynatrace pour configurer la connexion SSO SAML des deux côtés.

### <a name="create-dynatrace-test-user"></a>Créer un utilisateur de test Dynatrace

Dans cette section, un utilisateur appelé B.Simon est créé dans Dynatrace. Dynatrace prend en charge l’attribution d’utilisateurs juste-à-temps, option activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas encore d’utilisateur dans Dynatrace, il en est créé un après l’authentification.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

#### <a name="sp-initiated"></a>Lancée par le fournisseur de services :

* Cliquez sur **Tester cette application** dans le portail Azure. Vous allez être redirigé vers l’URL d’authentification de Dynatrace, où vous pouvez lancer le processus de connexion.  

* Accédez directement à l’URL d’authentification de Dynatrace pour lancer le processus de connexion.

#### <a name="idp-initiated"></a>Lancée par le fournisseur d’identité :

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors automatiquement connecté à l’instance de Dynatrace pour laquelle vous avez configuré l’authentification unique. 

Vous pouvez aussi utiliser Mes applications de Microsoft pour tester l’application dans n’importe quel mode. Si, quand vous cliquez sur la vignette Dynatrace dans Mes applications, le mode Fournisseur de services est configuré, vous êtes redirigé vers la page d’authentification de l’application pour lancer le flux de connexion ; s’il s’agit du mode Fournisseur d’identité, vous êtes automatiquement connecté à l’instance Dynatrace pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez configuré Dynatrace, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
