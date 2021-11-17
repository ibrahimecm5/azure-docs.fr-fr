---
title: 'Tutoriel : Intégration de l’authentification unique Azure Active Directory à Velpic SAML | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Velpic SAML.
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
ms.openlocfilehash: 5d8ded66ff7fd04c699a2dd48dc4391eb91ea9ee
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132282402"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-velpic-saml"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory à Velpic SAML

Dans ce tutoriel, vous allez apprendre à intégrer Velpic SAML à Azure Active Directory (Azure AD). Quand vous intégrez Velpic SAML à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Velpic SAML.
* Permettre à vos utilisateurs de se connecter automatiquement à Velpic SAML avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Abonnement Velpic SAML pour lequel l’authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* Velpic SAML prend en charge l’authentification unique lancée par le **fournisseur de services**.
* Velpic SAML prend en charge l’[attribution automatisée d’utilisateurs](velpic-provisioning-tutorial.md).

## <a name="adding-velpic-saml-from-the-gallery"></a>Ajout de Velpic SAML à partir de la galerie

Pour configurer l’intégration de Velpic SAML à Azure AD, vous devez ajouter Velpic SAML à partir de la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Velpic SAML** dans la zone de recherche.
1. Sélectionnez **Velpic SAML** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.    

## <a name="configure-and-test-azure-ad-sso-for-velpic-saml"></a>Configurer et tester l’authentification unique Azure AD pour Velpic SAML

Configurez et testez l’authentification unique Azure AD avec Velpic SAML à l’aide d’un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Velpic SAML associé.

Pour configurer et tester l’authentification unique Azure AD avec Velpic SAML, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Velpic SAML](#configure-velpic-saml-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test Velpic SAML](#create-velpic-saml-test-user)** pour avoir un équivalent de B.Simon dans Velpic SAML lié à la représentation Azure AD associée.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **Velpic SAML**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    a. Dans la zone de texte **URL de connexion**, saisissez une URL au format suivant : `https://<sub-domain>.velpicsaml.net`

    b. Dans la zone de texte **Identificateur (ID d’entité)** , saisissez une URL au format suivant : `https://auth.velpic.com/saml/v2/<entity-id>/login`

    > [!NOTE]
    > Notez que l’URL d’authentification unique sera fournie par l’équipe Velpic SAML et la valeur Identificateur sera disponible lorsque vous configurez le plug-in SSO du côté Velpic SAML. Vous devez copier cette valeur à partir de la page d’application Velpic SAML et la coller ici.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **XML de métadonnées de fédération** et sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

1. Dans la section **Configurer Velpic SAML**, copiez la ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous autorisez B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Velpic SAML.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Velpic SAML**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-velpic-saml-sso"></a>Configurer l’authentification unique Velpic SAML

1. Pour automatiser la configuration dans Velpic SAML, vous devez installer l’**extension de navigateur de connexion sécurisée à Mes applications** en cliquant sur **Installer l’extension**.

    ![Extension My apps](common/install-myappssecure-extension.png)

2. Après l’ajout de l’extension au navigateur, cliquez sur **Configurer Velpic SAML** pour être dirigé vers l’application Velpic SAML. À partir de là, indiquez les informations d’identification de l’administrateur pour vous connecter à Velpic SAML. Cette extension de navigateur configure automatiquement l’application et automatise les étapes 3 à 8.

    ![Configuration](common/setup-sso.png)

3. Si vous souhaitez configurer manuellement Velpic SAML, ouvrez une nouvelle fenêtre de navigateur web, connectez-vous à votre site d’entreprise Velpic SAML en tant qu’administrateur et effectuez les étapes suivantes :

4. Cliquez sur l’onglet **Gérer** et accédez à la section **Intégration** dans laquelle vous devez cliquer sur **Plug-ins** afin de créer le nouveau plug-in pour la connexion.

    ![Capture d’écran montrant la page Integration dans laquelle vous pouvez sélectionner Plugins](./media/velpicsaml-tutorial/plugin.png)

5. Cliquez sur le bouton **Ajouter un plug-in**.
    
    ![Capture d’écran montrant le bouton Add Plugin sélectionné](./media/velpicsaml-tutorial/add-button.png)

6. Cliquez sur la mosaïque **SAML** dans la page Ajouter un plug-in.
    
    ![Capture d’écran montrant SAML sélectionné dans la page Add Plugin](./media/velpicsaml-tutorial/integration.png)

7. Entrez le nom du nouveau plug-in SAML et cliquez sur le bouton **Ajouter**.

    ![Capture d’écran montrant la boîte de dialogue Add new SAML plugin avec Azure AD entré](./media/velpicsaml-tutorial/new-plugin.png)

8. Entrez les informations comme suit :

    ![Capture d’écran montrant la page Azure AD dans laquelle vous pouvez indiquer les valeurs décrites](./media/velpicsaml-tutorial/details.png)

    a. Dans la zone de texte **Nom**, saisissez le nom du plug-in SAML.

    b. Dans la zone de texte **Issuer URL** (URL de l’émetteur), collez l’**Identificateur Azure AD** que vous avez copié à partir de la fenêtre **Configurer l’authentification** du portail Azure.

    c. Dans la section **Configuration des métadonnées du fournisseur**, téléchargez le fichier XML de métadonnées que vous avez téléchargé à partir du portail Azure.

    d. Vous pouvez également choisir d’activer l’attribution juste-à-temps SAML en cochant la case **Créer automatiquement les nouveaux utilisateurs**. Si un utilisateur n’existe pas dans Velpic et que cet indicateur n’est pas activé, la connexion à partir d’Azure échouera. Si l’indicateur est activé, l’utilisateur sera automatiquement approvisionné dans Velpic au moment de la connexion. 

    e. Copiez l **’URL d’authentification unique** à partir de la zone de texte et collez-la dans le portail Azure.
    
    f. Cliquez sur **Enregistrer**.

### <a name="create-velpic-saml-test-user"></a>Créer un utilisateur de test Velpic SAML

Cette étape n’est généralement pas nécessaire car l’application prend en charge l’approvisionnement immédiat de l’utilisateur. Si l’approvisionnement automatique de l’utilisateur n’est pas activé, la création manuelle de l’utilisateur peut être effectuée comme décrit ci-dessous.

Connectez-vous au site de votre entreprise Velpic SAML en tant qu’administrateur et effectuez les étapes suivantes :
    
1. Cliquez sur l’onglet Gérer et accédez à la section Utilisateurs, puis cliquez sur le bouton Nouveau pour ajouter des utilisateurs.

    ![Ajouter un utilisateur](./media/velpicsaml-tutorial/new-user.png)

2. Dans la boîte de dialogue **Create New User**, procédez comme suit.

    ![Utilisateur](./media/velpicsaml-tutorial/create-user.png)
    
    a. Dans la zone de texte **Prénom**, tapez le prénom de B.

    b. Dans la zone de texte **Last Name** (Nom), tapez Simon.

    c. Dans la zone de texte **Nom d’utilisateur**, tapez le nom d’utilisateur de B.Simon.

    d. Dans la zone de texte **Email**, tapez l’adresse e-mail du compte B.Simon@contoso.com.

    e. Les autres informations sont facultatives et vous pouvez les renseigner si nécessaire.
    
    f. Cliquez sur **ENREGISTRER**.

> [!NOTE]
> Velpic SAML prend également en charge l’attribution automatique d’utilisateurs. Vous trouverez plus d’informations sur la configuration de cette fonctionnalité [ici](./velpic-provisioning-tutorial.md).

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide de Mes applications.

1. Lorsque vous cliquez sur la vignette Velpic SAML dans Mes applications, vous devriez voir la page de connexion de l’application Velpic SAML apparaître. Vous devriez voir le bouton **Se connecter avec Azure AD** dans la page de connexion.

    ![Capture d’écran montrant la boîte de dialogue Learning Portal avec l’option Log In With Azure AD sélectionnée](./media/velpicsaml-tutorial/login.png)

1. Cliquez sur le bouton **Se connecter avec Azure AD** pour vous connecter à Velpic en utilisant votre compte Azure AD.

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez configuré Velpic SAML, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
