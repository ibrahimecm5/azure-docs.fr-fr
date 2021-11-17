---
title: 'Tutoriel : Intégration de l’authentification unique (SSO) Azure Active Directory à Darwinbox | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Darwinbox.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/18/2021
ms.author: jeedes
ms.openlocfilehash: be7cf3de9889b54bb14ff093f4730b6ee5a28c0e
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132296385"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-darwinbox"></a>Tutoriel : Intégration de l’authentification unique (SSO) Azure Active Directory à Darwinbox

Dans ce tutoriel, vous allez apprendre à intégrer Darwinbox à Azure Active Directory (Azure AD). Quand vous intégrez Darwinbox à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Darwinbox.
* Permettre à vos utilisateurs de se connecter automatiquement à Darwinbox avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Darwinbox pour lequel l’authentification unique (SSO) est activée.
> [!NOTE]
> Cette intégration peut également être utilisée à partir de l’environnement cloud US Government Azure AD. Cette application est disponible dans la Galerie d’applications cloud US Government Azure AD et peut être configurée de la même façon que dans le cloud public.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* Darwinbox prend en charge l’authentification unique (SSO) lancée par le **fournisseur de services**.

## <a name="add-darwinbox-from-the-gallery"></a>Ajouter Darwinbox à partir de la galerie

Pour configurer l’intégration de Darwinbox à Azure AD, vous devez ajouter Darwinbox de la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Darwinbox** dans la zone de recherche.
1. Sélectionnez **Darwinbox** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-darwinbox"></a>Configurer et tester l’authentification unique Azure AD pour Darwinbox

Configurez et testez l’authentification unique (SSO) Azure AD avec Darwinbox pour un utilisateur de test nommé **B.Simon**. Pour que l’authentification unique (SSO) fonctionne, vous devez établir une relation entre un utilisateur Azure AD et l’utilisateur Darwinbox associé.

Pour configurer et tester l’authentification unique Azure AD avec Darwinbox, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique (SSO) Darwinbox](#configure-darwinbox-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test Darwinbox](#create-darwinbox-test-user)** pour disposer, dans Darwinbox, d’un équivalent de B.Simon lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans la page d’intégration de l’application **Darwinbox** du portail Azure, recherchez la section **Gérer**, puis sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

   1. Dans la zone de texte **Identificateur (ID d’entité)** , saisissez une URL au format suivant : `https://<SUBDOMAIN>.darwinbox.in/adfs/module.php/saml/sp/metadata.php/<CUSTOM_ID>`

    1. Dans la zone de texte **URL de connexion**, saisissez une URL au format suivant : `https://<SUBDOMAIN>.darwinbox.in/`

      > [!NOTE]
      > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur et l’URL de connexion réels. Pour obtenir ces valeurs, contactez l’[équipe de support technique de Darwinbox](https://darwinbox.com/contact-us.php). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **XML de métadonnées de fédération** et sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

1. Dans la section **Configurer Darwinbox**, copiez la ou les URL appropriées, selon vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Darwinbox.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Darwinbox**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-darwinbox-sso"></a>Configurer l’authentification unique (SSO) Darwinbox

Pour configurer l’authentification unique côté **Darwinbox**, vous devez envoyer le fichier **XML des métadonnées de fédération** téléchargé et les URL appropriées copiées à partir du portail Azure à l’[équipe du support technique Darwinbox](https://darwinbox.com/contact-us.php). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

### <a name="create-darwinbox-test-user"></a>Créer un utilisateur de test Darwinbox

Dans cette section, vous créez un utilisateur appelé B.Simon dans Darwinbox. Collaborez avec l’[équipe du support technique Darwinbox](https://darwinbox.com/contact-us.php) pour ajouter des utilisateurs sur la plateforme Darwinbox. Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Cette opération redirige vers l’URL de connexion Darwinbox, où vous pouvez lancer le processus de connexion. 

* Accédez directement à l’URL de connexion à Darwinbox pour lancer le flux de connexion.

* Vous pouvez utiliser Mes applications de Microsoft. Le fait de cliquer sur la vignette Darwinbox dans Mes applications vous redirige vers l’URL de connexion Darwinbox. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="test-sso-for-darwinbox-mobile"></a>Tester l’authentification unique pour Darwinbox (mobile)

1. Ouvrez l’application mobile Darwinbox. Cliquez sur **Entrer l’URL de l’organisation**, entrez l’URL de votre organisation dans la zone de texte, puis cliquez sur le bouton fléché.

    ![Capture d’écran montrant l’application mobile « Darwinbox » avec l’option « Enter Organization URL » sélectionnée, un exemple d’organisation et le bouton avec une flèche en évidence.](media/darwinbox-tutorial/login.png)

1. Si vous avez plusieurs domaines, cliquez sur votre domaine.

    ![Capture d’écran montrant l’écran « Choose your domain » avec un exemple de domaine sélectionné.](media/darwinbox-tutorial/domain.png)

1. Entrez l’e-mail de votre Azure AD dans l’application Darwinbox, puis cliquez sur **Suivant**.

    ![Capture d’écran montrant l’écran « Sign in » avec le bouton « Next » en évidence.](media/darwinbox-tutorial/email.png)

1. Entrez le mot de passe de votre Azure AD dans l’application Darwinbox, puis cliquez sur **Se connecter**.

    ![Capture d’écran montrant l’écran des options de connexion avec le bouton « Next » en évidence.](media/darwinbox-tutorial/account.png)

1. Une fois la connexion réussie, la page d’accueil de l’application s’affiche.

    ![Application mobile Darwinbox](media/darwinbox-tutorial/application.png)

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez configuré Darwinbox, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
