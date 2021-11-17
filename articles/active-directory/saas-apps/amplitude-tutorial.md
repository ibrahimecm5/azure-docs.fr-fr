---
title: 'Tutoriel : Intégration de l’authentification unique Azure Active Directory à Amplitude | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Amplitude.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/16/2021
ms.author: jeedes
ms.openlocfilehash: 780e94b678a6155c0c5f6e0bc93cc584a764af40
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132324419"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-amplitude"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory à Amplitude

Dans ce tutoriel, vous allez apprendre à intégrer Amplitude à Azure Active Directory (Azure AD). Quand vous intégrez Amplitude à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Amplitude.
* Permettre à vos utilisateurs de se connecter automatiquement à Amplitude avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Amplitude pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* Amplitude prend en charge l’authentification unique initiée par **le fournisseur de services et le fournisseur d’identité**.
* Amplitude prend en charge le provisionnement d’utilisateurs **Juste-à-temps**.

> [!NOTE]
> L’identificateur de cette application étant une valeur de chaîne fixe, une seule instance peut être configurée dans un locataire.

## <a name="add-amplitude-from-the-gallery"></a>Ajouter Amplitude depuis la galerie

Pour configurer l’intégration d’Amplitude à Azure AD, vous devez ajouter Amplitude, disponible dans la galerie, à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Amplitude** dans la zone de recherche.
1. Sélectionnez **Amplitude** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-amplitude"></a>Configurer et tester l’authentification unique Azure AD pour Amplitude

Configurez et testez l’authentification unique Azure AD avec Amplitude à l’aide d’un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Amplitude associé.

Pour configurer et tester Azure AD SSO avec Amplitude, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Amplitude](#configure-amplitude-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test Amplitude](#create-amplitude-test-user)**  : pour avoir un équivalent de B.Simon dans Amplitude, lié à sa représentation dans Azure AD.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **Amplitude**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, si vous souhaitez configurer l’application en mode lancé par le **fournisseur d’identité**, effectuez les étapes suivantes :

    a. Dans la zone de texte **Identificateur**, tapez une l’URL : `https://amplitude.com/saml/sso/metadata`

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://analytics.amplitude.com/saml/sso/<UNIQUE_ID>`

    > [!NOTE]
    > La valeur de l’URL de réponse n’est pas réelle. Vous obtenez la valeur de l’URL de réponse plus loin dans ce didacticiel.

1. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    Dans la zone de texte **URL de connexion**, tapez l’URL : `https://analytics.amplitude.com/sso`

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **XML de métadonnées de fédération** et sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

1. Dans la section **Configurer Amplitude**, copiez la ou les URL nécessaires en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Amplitude.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Amplitude**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-amplitude-sso"></a>Configurer l’authentification unique Amplitude

1. Pour automatiser la configuration dans Amplitude, vous devez installer l’**extension de navigateur My Apps Secure Sign-in** en cliquant sur **Installer l’extension**.

    ![Extension My apps](common/install-myappssecure-extension.png)

1. Après l’ajout de l’extension au navigateur, cliquez sur **Configurer Amplitude** pour être dirigé vers l’application Amplitude. À partir de là, indiquez les informations d’identification de l’administrateur pour vous connecter à Amplitude. Cette extension de navigateur configure automatiquement l’application et automatise les étapes 3 à 6.

    ![Configuration](common/setup-sso.png)

1. Si vous souhaitez configurer manuellement Amplitude, ouvrez une nouvelle fenêtre de navigateur web, connectez-vous à votre site d’entreprise Amplitude en tant qu’administrateur et effectuez les étapes suivantes :

1. Cliquez sur **Plan Admin** (Admin de plan) dans la barre de navigation gauche.

    ![Capture d’écran montrant le menu Amplitude avec Plan Admin sélectionné.](./media/amplitude-tutorial/plan-tab.png)

1. Sélectionnez **Microsoft Azure Active Directory Metadata** (Métadonnées Microsoft Azure Active Directory) dans **SSO Integration** (Intégration SSO).

    ![Capture d’écran montrant le volet Plan Admin avec le lien Microsoft Azure Active Directory Metadata activé.](./media/amplitude-tutorial/metadata.png)

1. Dans la section **Setup Single Sign-On** , procédez comme suit :

    ![Capture d’écran montrant la section Set Up Single Sign-on avec les valeurs décrites à cette étape.](./media/amplitude-tutorial/configuration.png)

    a. Ouvrez le fichier **Metadata.Xml** téléchargé à partir du portail Azure dans le Bloc-notes, puis collez son contenu dans la zone de texte **Microsoft Azure Active Directory Metadata** (Métadonnées Microsoft Azure Active Directory).

    b. Copiez la valeur de **URL de réponse (ACS)** et collez-la dans la zone de texte **URL de réponse** de **Configuration SAML de base** du portail Azure.

    c. Cliquez sur **Enregistrer**.

### <a name="create-amplitude-test-user"></a>Créer un utilisateur de test Amplitude

Dans cette section, un utilisateur appelé B.Simon est créé dans Amplitude. Amplitude prend en charge le provisionnement d’utilisateurs juste-à-temps, option activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas encore d’utilisateur dans Amplitude, il en est créé un après l’authentification.

> [!Note]
> Si vous devez créer un utilisateur manuellement, contactez [l’équipe de support Amplitude](https://amplitude.zendesk.com).

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

#### <a name="sp-initiated"></a>Lancée par le fournisseur de services :

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion à Amplitude, où vous pouvez lancer le flux de connexion.  

* Accédez directement à l’URL de connexion à Amplitude pour lancer le flux de connexion à partir de là.

#### <a name="idp-initiated"></a>Lancée par le fournisseur d’identité :

* Dans le portail Azure, cliquez sur **Tester cette application**. Vous êtes alors automatiquement connecté à l’instance Amplitude pour laquelle vous avez configuré l’authentification unique. 

Vous pouvez aussi utiliser Mes applications de Microsoft pour tester l’application dans n’importe quel mode. Si, quand vous cliquez sur la vignette Amplitude dans Mes applications, le mode Fournisseur de services est configuré, vous êtes redirigé vers la page de connexion de l’application pour lancer le flux de connexion ; s’il s’agit du mode Fournisseur d’identité, vous êtes automatiquement connecté à l’instance d’Airtable pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez configuré Amplitude, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
