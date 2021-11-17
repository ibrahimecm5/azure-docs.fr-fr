---
title: 'Tutoriel : Intégration de l’authentification unique Azure Active Directory à Envoy | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Envoy.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/25/2021
ms.author: jeedes
ms.openlocfilehash: 2cdeed1457ad19abde6675033db001b5477e8ac4
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132314263"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-envoy"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory à Envoy

Dans ce tutoriel, vous allez découvrir comment intégrer Envoy à Azure Active Directory (Azure AD). Quand vous intégrez Envoy à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Envoy.
* Permettre à vos utilisateurs de se connecter automatiquement à Envoy avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Envoy pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* Envoy prend en charge l'authentification unique initiée par le **fournisseur de services**.

* Envoy prend en charge l'attribution d'utilisateurs **juste-à-temps**.

* Envoy prend en charge l’[attribution automatique d’utilisateurs](envoy-provisioning-tutorial.md).

> [!NOTE]
> L’identificateur de cette application étant une valeur de chaîne fixe, une seule instance peut être configurée dans un locataire.

## <a name="add-envoy-from-the-gallery"></a>Ajouter Envoy à partir de la galerie

Pour configurer l’intégration d’Envoy à Azure AD, vous devez ajouter Envoy, disponible dans la galerie, à votre liste d’applications SaaS managées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Envoy** dans la zone de recherche.
1. Sélectionnez **Envoy** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-envoy"></a>Configurer et tester l'authentification unique Azure AD pour Envoy

Configurez et testez l’authentification unique Azure AD avec Envoy à l’aide d’un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir une relation entre un utilisateur Azure AD et l’utilisateur Envoy associé.

Pour configurer et tester l'authentification unique Azure AD auprès d'Envoy, procédez comme suit :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Envoy](#configure-envoy-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test Envoy](#create-envoy-test-user)** pour avoir un équivalent de B.Simon dans Envoy, associé à sa représentation dans Azure AD.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Sur le portail Azure, accédez à la page d'intégration de l'application **Envoy**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, effectuez l’étape suivante :

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://app.envoy.com/a/saml/auth/<company-ID-from-Envoy>`

    > [!NOTE]
    > Cette valeur n’est pas la valeur réelle. Mettez à jour la valeur avec l’URL de connexion réelle. Pour obtenir cette valeur, contactez l’[équipe du support client Envoy](https://envoy.com/contact/). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. Dans la section **Certificat de signature SAML**, cliquez sur le bouton **Modifier** pour ouvrir la boîte de dialogue **Certificat de signature SAML**.

    ![Modifier le certificat de signature SAML](common/edit-certificate.png)

1. Dans la section **Certificat de signature SAML**, copiez la **valeur de l’empreinte** et enregistrez-la sur votre ordinateur.

    ![Copier la valeur de l’empreinte](common/copy-thumbprint.png)

1. Dans la section **Configurer Envoy**, copiez la ou les URL appropriées, en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Envoy.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Envoy**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-envoy-sso"></a>Configurer l’authentification unique Envoy

1. Pour automatiser la configuration dans Envoy, vous devez installer l’**extension de navigateur My Apps Secure Sign-in** en cliquant sur **Installer l’extension**.

    ![Extension My apps](common/install-myappssecure-extension.png)

2. Après l’ajout de l’extension au navigateur, cliquez sur **Configurer Envoy** pour être dirigé vers l’application Envoy. À partir de là, indiquez les informations d’identification de l’administrateur pour vous connecter à Envoy. Cette extension de navigateur configure automatiquement l’application et automatise les étapes 3 à 5.

    ![Configuration](common/setup-sso.png)

3. Si vous souhaitez configurer manuellement Envoy, ouvrez une nouvelle fenêtre de navigateur web, connectez-vous à votre site d’entreprise Envoy en tant qu’administrateur et effectuez les étapes suivantes.

4. Accédez à **Intégrations** > **Toutes les intégrations**, puis cliquez pour **Installer** SAML sous **Authentification unique**.

    ![Authentification SAML](./media/envoy-tutorial/integrations.png "Authentification SAML")

5. Accédez à la section **Intégrations intégrées** et procédez comme suit :

    ![Authentification unique](./media/envoy-tutorial/configuration.png "Authentification unique")
    
    >[!NOTE]
    >La valeur de l’ID d’emplacement du siège est générée automatiquement par l’application.
    
    a. Dans la zone de texte **Fingerprint** (Empreinte digitale), collez la valeur du certificat **Empreinte** que vous avez copiée à partir du portail Azure.
    
    b. Collez la valeur **URL de connexion** copiée dans le portail Azure dans la zone de texte **IDENTITY PROVIDER HTTP SAML URL** (URL HTTP SAML du fournisseur d’identité).
    
    c. Cliquez sur **Enregistrer**.

### <a name="create-envoy-test-user"></a>Créer un utilisateur de test Envoy

Dans cette section, un utilisateur appelé Britta Simon est créé dans Envoy. Envoy prend en charge le provisionnement d’utilisateurs juste-à-temps, qui est activé par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas encore d’utilisateur dans Envoy, il en est créé un après l’authentification.

Envoy prend aussi en charge l’attribution automatique d’utilisateurs. Vous trouverez [ici](./envoy-provisioning-tutorial.md) plus d’informations sur la façon de configurer l’attribution automatique d’utilisateurs.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes redirigé vers l'URL de connexion à Envoy, à partir de laquelle vous pouvez lancer le processus de connexion. 

* Accédez directement à l'URL de connexion à Envoy pour lancer le processus de connexion.

* Vous pouvez utiliser Mes applications de Microsoft. Le fait de cliquer sur la vignette Envoy dans Mes applications vous redirige vers l'URL de connexion à Envoy. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré Envoy, vous pouvez appliquer le contrôle de session, qui protège en temps réel contre l'exfiltration et l'infiltration des données sensibles de votre organisation. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
