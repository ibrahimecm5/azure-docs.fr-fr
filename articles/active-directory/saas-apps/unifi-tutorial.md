---
title: 'Didacticiel : Intégration d’Azure Active Directory à UNIFI | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et UNIFI.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/09/2021
ms.author: jeedes
ms.openlocfilehash: b754c19d89582538fe9bdd71c34148400d51548e
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132298771"
---
# <a name="tutorial-azure-active-directory-integration-with-unifi"></a>Didacticiel : Intégration d’Azure Active Directory à UNIFI

Dans ce tutoriel, vous allez apprendre à intégrer UNIFI à Azure Active Directory (Azure AD). Quand vous intégrez UNIFI à Azure AD, vous pouvez :

* Contrôler qui dans Azure AD a accès à UNIFI.
* Permettre à vos utilisateurs de se connecter automatiquement à UNIFI avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement UNIFI pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* UNIFI prend en charge l’authentification unique lancée par **le fournisseur de services et le fournisseur d’identité**.
* UNIFI prend en charge le provisionnement d’utilisateurs **automatique**.

## <a name="add-unifi-from-the-gallery"></a>Ajouter UNIFI à partir de la galerie

Pour configurer l’intégration d’UNIFI avec Azure AD, vous devez ajouter UNIFI à partir de la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **UNIFI** dans la zone de recherche.
1. Sélectionnez **UNIFI** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-unifi"></a>Configurer et tester l’authentification unique Azure AD pour UNIFI

Configurez et testez l’authentification unique Azure AD auprès d’UNIFI pour un utilisateur de test nommé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur UNIFI associé.

Pour configurer et tester l’authentification unique Azure AD auprès d’UNIFI, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique UNIFI](#configure-unifi-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test UNIFI](#create-unifi-test-user)** pour obtenir, dans UNIFI, un équivalent de B.Simon lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **UNIFI**, recherchez la section **Gérer**, puis sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

4. À la section **Configuration SAML de base**, si vous souhaitez configurer l’application en mode initié par **IDP**, suivez les étapes ci-dessous :

    Dans la zone de texte **Identificateur**, tapez une l’URL : `INVIEWlabs`

5. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    Dans la zone de texte **URL de connexion**, tapez l’URL : `https://app.discoverunifi.com/login`

6. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (Base64)** en fonction des options définies par rapport à vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

7. Dans la section **Configurer UNIFI**, copiez l’URL ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en accordant l’accès à UNIFI.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **UNIFI**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-unifi-sso"></a>Configurer l’authentification unique UNIFI

1. Dans une autre fenêtre de navigateur web, connectez-vous au site de votre entreprise **UNIFI** en tant qu’administrateur.

2. Cliquez sur **Utilisateurs**.

    ![Capture d’écran montrant Users sélectionné sur le site UNIFI.](./media/unifi-tutorial/app-1.png)

3. Cliquez sur **Ajouter un nouveau fournisseur d’identité**.

    ![Capture d’écran montrant Add New Identity Provider sélectionné.](./media/unifi-tutorial/app-2.png)

4. Dans la section **Ajouter un fournisseur d’identité**, procédez comme suit :

    ![Capture d’écran montrant Add Identity Provider où vous pouvez entrer les valeurs décrites.](./media/unifi-tutorial/app-3.png) 

    a. Dans la zone de texte **Nom du fournisseur**, entrez le nom du fournisseur d’identité.

    b. Dans la zone de texte **Provider URL** (URL du fournisseur), collez l’**URL de connexion** que vous avez copiée sur le portail Azure.

    c. Ouvrez le certificat que vous avez téléchargé à partir du portail Azure dans le bloc-notes, supprimez les balises **---BEGIN CERTIFICATE---** et **---END CERTIFICATE---** , puis collez le contenu restant dans la zone de texte **Certificat**.

    d. Cochez la case **est le fournisseur par défaut**.

### <a name="create-unifi-test-user"></a>Créer un utilisateur de test UNIFI

Dans cette section, vous allez créer un utilisateur appelé Britta Simon. **UNIFI** prend en charge l’approvisionnement automatique de l’utilisateur. Par conséquent, aucune action manuelle n’est requise. Lorsque l’authentification a réussi à partir d’Azure AD, les utilisateurs sont créés automatiquement.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

#### <a name="sp-initiated"></a>Lancée par le fournisseur de services :

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion à UNIFI, d’où vous pouvez lancer le flux de connexion.  

* Accédez directement à l’URL de connexion UNIFI pour lancer le flux de connexion.

#### <a name="idp-initiated"></a>Lancée par le fournisseur d’identité :

* En cliquant sur **Tester cette application** dans le portail Azure, vous devez être automatiquement connecté à l’instance de UNIFI pour laquelle vous avez configuré l’authentification unique. 

Vous pouvez aussi utiliser Mes applications de Microsoft pour tester l’application dans n’importe quel mode. Si, quand vous cliquez sur la vignette UNIFI dans Mes applications, le mode Fournisseur de services est configuré, vous êtes redirigé vers la page de connexion de l’application pour lancer le flux de connexion ; s’il s’agit du mode Fournisseur d’identité, vous êtes automatiquement connecté à l’instance UNIFI pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré UNIFI, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
