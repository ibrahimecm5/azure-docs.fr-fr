---
title: 'Tutoriel : Intégration de l’authentification unique Azure Active Directory à Freshworks | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Freshworks.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/11/2021
ms.author: jeedes
ms.openlocfilehash: b8d097bf82faf8812e10bc4cdacfc64b9d774760
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132291934"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-freshworks"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory à Freshworks

Dans ce tutoriel, vous allez apprendre à intégrer Freshworks à Azure Active Directory (Azure AD). Quand vous intégrez Freshworks à Azure AD, vous pouvez :

* Contrôler qui a accès à Freshworks dans Azure AD.
* Permettre à vos utilisateurs de se connecter automatiquement à Freshworks avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Freshworks pour lequel l’authentification unique est activée

> [!NOTE]
> Cette intégration peut également être utilisée à partir de l’environnement cloud US Government Azure AD. Cette application est disponible dans la Galerie d’applications cloud US Government Azure AD et peut être configurée de la même façon que dans le cloud public.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* Freshworks prend en charge l’authentification unique lancée par **le fournisseur de services et le fournisseur d’identité**

## <a name="add-freshworks-from-the-gallery"></a>Ajouter Freshworks à partir de la galerie

Pour configurer l’intégration de Freshworks à Azure AD, vous devez ajouter Freshworks à partir de la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Freshworks** dans la zone de recherche.
1. Sélectionnez **Freshworks** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-freshworks"></a>Configurer et tester l’authentification unique Azure AD pour Freshworks

Configurez et testez l’authentification unique Azure AD avec Freshworks pour un utilisateur de test nommé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Freshworks associé.

Pour configurer et tester l’authentification unique Azure AD avec Freshworks, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Freshworks](#configure-freshworks-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test Freshworks](#create-freshworks-test-user)** pour avoir un équivalent de B.Simon dans Freshworks lié à la représentation Azure AD associée.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Sur le portail Azure, dans la page d’intégration de l’application **Freshworks**, recherchez la section **Gérer**, puis sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, si vous souhaitez configurer l’application en mode Initié par le **fournisseur d’identité**, entrez les valeurs pour les champs suivants :

    a. Dans la zone de texte **Identificateur (ID d’entité)** , saisissez une URL au format suivant : `https://<SUBDOMAIN>.freshworks.com/sp/SAML/<MODULE_ID>/metadata`

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://<SUBDOMAIN>.freshworks.com/sp/SAML/CUSTOM_URL`

1. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<SUBDOMAIN>.freshworks.com/login`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL d’authentification et l’URL de réponse réels. Contactez [l’équipe de support Freshworks](mailto:support@freshworks.com) pour obtenir ces valeurs. Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **Certificat (en base64)** , puis sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

1. Pour modifier les options **Signature** selon vos besoins, cliquez sur le bouton **Modifier** afin d’ouvrir la boîte de dialogue **Certificat de signature SAML**.

     ![image](common/edit-certificate.png)

     ![Capture d’écran montrant la boîte de dialogue « Certificat de signature SAML » avec le bouton « Modifier » sélectionné.](./media/freshworks-tutorial/response.png)

    a. Sélectionnez **Signer la réponse SAML** comme **Option de signature**.

    b. Cliquez sur **Enregistrer**.

1. Dans la section **Configurer Freshworks**, copiez l’URL ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Freshworks.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Freshworks**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-freshworks-sso"></a>Configurer l’authentification unique Freshworks

1. Ouvrez une nouvelle fenêtre de navigateur web et connectez-vous à votre site d’entreprise Freshworks en tant qu’administrateur, puis effectuez les étapes suivantes :

2. Dans la partie gauche du menu, cliquez sur l’icône **Security** puis activez l’option **Single sign-on** (Authentification unique) et sélectionnez **SAML SSO** (SSO SAML) sous **Authentication Methods** (Méthodes d’authentification).

    ![Capture d’écran montrant la section « Security - Authentication Methods » avec l’option « Single sign-on » activée et « SAML SSO » sélectionné.](./media/freshworks-tutorial/configure01.png)

3. Dans la section **Single sign-on**, effectuez les étapes suivantes :

    ![Configuration de Freshworks](./media/freshworks-tutorial/configure02.png)

    a. Cliquez sur **Copy** pour copier l’ID d’entité du fournisseur de services (**Service Provider(SP) Entity ID**) de votre instance et collez-le dans la zone de texte **Identificateur (ID d’entité)** de la section **Configuration SAML de base** du portail Azure.

    b. Dans la zone de texte **Entity ID provided by the IdP** (ID d’entité fourni par le fournisseur d’identité), collez la valeur **Identificateur Azure AD** que vous avez copiée dans le portail Azure.

    c. Dans la zone de texte **SAML SSO URL** (URL SSO SAML), collez la valeur d’**URL de connexion** que vous avez copiée sur le portail Azure.

    d. Ouvrez le certificat encodé en Base64 dans le bloc-notes, copiez son contenu et collez-le dans la zone de texte **Security certificate** (Certificat de sécurité).

    e. Cliquez sur **Enregistrer**.

### <a name="create-freshworks-test-user"></a>Créer un utilisateur de test Freshworks

Dans cette section, vous allez créer un utilisateur appelé B.Simon dans Freshworks. Collaborez avec l’[équipe du support technique Freshworks](mailto:support@freshworks.com) pour ajouter des utilisateurs à la plateforme Freshworks. Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique. 

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

#### <a name="sp-initiated"></a>Lancée par le fournisseur de services :

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion à Freshworks, d’où vous pouvez lancer le flux de connexion.  

* Accédez directement à l’URL de connexion à Freshworks pour lancer le flux de connexion.

#### <a name="idp-initiated"></a>Lancée par le fournisseur d’identité :

* Cliquez sur **Tester cette application** dans le portail Azure. Vous devez être connecté automatiquement à l’instance de Freshworks pour laquelle vous avez configuré l’authentification unique. 

Vous pouvez aussi utiliser Mes applications de Microsoft pour tester l’application dans n’importe quel mode. Si, quand vous cliquez sur la vignette Freshworks dans Mes applications, le mode Fournisseur de services est configuré, vous êtes redirigé vers la page de connexion de l’application pour lancer le flux de connexion ; s’il s’agit du mode Fournisseur d’identité, vous êtes automatiquement connecté à l’instance de Freshworks pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).


## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré Freshworks, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
