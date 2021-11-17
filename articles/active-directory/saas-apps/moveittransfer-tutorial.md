---
title: 'Didacticiel : Intégration d’Azure Active Directory à MOVEit Transfer - Azure AD integration | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et MOVEit Transfer - Azure AD integration.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/27/2021
ms.author: jeedes
ms.openlocfilehash: 618cc7b60da58101995d98b6c04155255dcc916c
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132342297"
---
# <a name="tutorial-azure-active-directory-integration-with-moveit-transfer---azure-ad-integration"></a>Didacticiel : Intégration d’Azure Active Directory à MOVEit Transfer - Azure AD integration

Dans ce tutoriel, vous allez apprendre à intégrer MOVEit Transfer - Azure AD integration à Azure Active Directory (Azure AD). Quand vous intégrez MOVEit Transfer - Azure AD integration à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à MOVEit Transfer - Azure AD integration.
* Permettre à vos utilisateurs de se connecter automatiquement à MOVEit Transfer - Azure AD integration avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement MOVEit Transfer - Azure AD integration pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* MOVEit Transfer - Azure AD integration prend en charge l’authentification unique initiée par le **fournisseur de services**.

## <a name="add-moveit-transfer---azure-ad-integration-from-the-gallery"></a>Ajouter MOVEit Transfer - Azure AD integration à partir de la galerie

Pour configurer l’intégration de MOVEit Transfer - Azure AD integration avec Azure AD, vous devez ajouter MOVEit Transfer - Azure AD integration, disponible dans la galerie, à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **MOVEit Transfer - Azure AD integration** dans la zone de recherche.
1. Sélectionnez **MOVEit Transfer - Azure AD integration** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-moveit-transfer---azure-ad-integration"></a>Configurer et tester l’authentification unique Azure AD pour MOVEit Transfer - Azure AD integration

Configurez et testez l’authentification unique Azure AD avec MOVEit Transfer - Azure AD integration pour un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur associé dans MOVEit Transfer - Azure AD integration.

Pour configurer et tester l’authentification unique Azure AD avec MOVEit Transfer - Azure AD integration, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique MOVEit Transfer - Azure AD integration](#configure-moveit-transfer---azure-ad-integration-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test MOVEit Transfer - Azure AD integration](#create-moveit-transfer---azure-ad-integration-test-user)** pour avoir un équivalent de B.Simon dans MOVEit Transfer - Azure AD integration lié à la représentation Azure AD associée.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, dans la page d’intégration de l’application **MOVEit Transfer - Azure AD integration**, recherchez la section **Gérer**, puis sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, si vous disposez d’un **fichier de métadonnées du fournisseur de services**, suivez les étapes ci-dessous :

    a. Cliquez sur **Charger un fichier de métadonnées**.

    ![Charger le fichier de métadonnées](common/upload-metadata.png)

    b. Cliquez sur le **logo du dossier** pour sélectionner le fichier de métadonnées, puis cliquez sur **Charger**.

    ![choisir le fichier de métadonnées](common/browse-upload-metadata.png)

    c. Une fois le fichier de métadonnées chargé, les valeurs **Identificateur** et **URL de réponse** sont automatiquement renseignées dans la section **Configuration SAML de base** :

    ![Informations d’authentification unique dans Domaine et URL MOVEit Transfer - Azure AD integration](common/sp-identifier-reply.png)

    Dans la zone de texte **URL de connexion**, tapez l’URL : `https://contoso.com`

    > [!NOTE]
    > La valeur d’**URL de connexion** n’est pas réelle. Mettez à jour la valeur avec l’URL de connexion réelle. Contactez l’équipe du [support technique MOVEit Transfer - Azure AD integration](https://community.ipswitch.com/s/support) pour obtenir la valeur. Vous pouvez télécharger le **fichier de métadonnées du fournisseur de services** à partir de l’**URL de métadonnées du fournisseur de services**, comme qui est expliqué plus loin dans la section **Configurer l’authentification unique MOVEit Transfer - Azure AD integration** du didacticiel. Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

4. Sur la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le fichier **XML de métadonnées de fédération** en fonction des options définies selon vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

6. Dans la section **Configurer MOVEit Transfer - Azure AD integration**, copiez les URL appropriées, selon vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à MOVEit Transfer - Azure AD integration.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **MOVEit Transfer - Azure AD integration**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

### <a name="configure-moveit-transfer---azure-ad-integration-sso"></a>Configurer l’authentification unique MOVEit Transfer - Azure AD integration

1. Connectez-vous à votre client MOVEit Transfer - Azure AD integration en tant qu’administrateur.

2. Dans le volet de navigation gauche, cliquez sur **Paramètres**.

    ![Section Paramètres côté application](./media/moveittransfer-tutorial/settings.png)

3. Cliquez sur le lien **Authentification unique** qui se trouve sous **Stratégies de sécurité -> Authentification des utilisateurs**.

    ![Stratégies de sécurité côté application](./media/moveittransfer-tutorial/sso.png)

4. Cliquez sur le lien URL de métadonnées pour télécharger le document de métadonnées.

    ![URL de métadonnées du fournisseur de service](./media/moveittransfer-tutorial/metadata.png)
    
   * Vérifiez qu’**entityID** correspond à **Identifier** dans la section **Configuration SAML de base**.
   * Vérifiez que l’URL de l’emplacement **AssertionConsumerService** correspond à **l’URL de réponse** dans la section **Configuration SAML de base**.
    
     ![Configurer l’authentification unique côté application](./media/moveittransfer-tutorial/xml.png)

5. Cliquez sur **Add Identity Provider** (Ajouter un fournisseur d’identité) pour ajouter un nouveau fournisseur d’identité fédéré.

    ![Ajouter un fournisseur d’identité](./media/moveittransfer-tutorial/idp.png)

6. Cliquez sur **Parcourir...** pour sélectionner le fichier de métadonnées que vous avez téléchargé à partir du portail Azure, puis cliquez sur **Ajouter un fournisseur d’identité** pour charger le fichier téléchargé.

    ![Fournisseur d’identité SAML](./media/moveittransfer-tutorial/saml.png)

7. Sélectionnez **Oui** sous **Activé** dans la page **Edit Federated Identity Provider Settings** (Modifier les paramètres du fournisseur d’identité fédéré...), puis cliquez sur **Enregistrer**.

    ![Paramètres de fournisseur d’identité fédérée](./media/moveittransfer-tutorial/save.png)

8. Dans la page **Edit Federated Identity Provider User Settings** (Modifier les paramètres utilisateur du fournisseur d’identité fédérée), effectuez les actions suivantes :
    
    ![Modifier les paramètres de fournisseur d’identité fédérée](./media/moveittransfer-tutorial/attributes.png)
    
    a. Sélectionnez **SAML NameID** comme **Nom de connexion**.
    
    b. Sélectionnez **Autre** comme **Nom complet** et dans la zone de texte **Nom de l’attribut**, insérez la valeur : `http://schemas.microsoft.com/identity/claims/displayname`.
    
    c. Sélectionnez **Autre** comme **E-mail** et dans la zone de texte **Nom de l’attribut**, insérez la valeur : `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.
    
    d. Sélectionnez **Oui** sous **Auto-create account on signon** (Créer automatiquement un compte lors de l’authentification).
    
    e. Cliquez sur le bouton **Enregistrer** .

### <a name="create-moveit-transfer---azure-ad-integration-test-user"></a>Créer un utilisateur de test MOVEit Transfer - Azure AD integration

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans MOVEit Transfer - Azure AD integration. MOVEit Transfer - Azure AD integration prend en charge l’approvisionnement juste-à-temps que vous avez activé. Vous n’avez aucune opération à effectuer dans cette section. Un utilisateur est créé lors d’une tentative d’accès à MOVEit Transfer - Azure AD integration s’il n’existe pas déjà.

>[!NOTE]
>Si vous devez créer un utilisateur manuellement, contactez l’[équipe du support technique MOVEit Transfer - Azure AD integration](https://community.ipswitch.com/s/support).

### <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Cette opération redirige vers l’URL de connexion MOVEit Transfer - Azure AD integration où vous pouvez lancer le flux de connexion. 

* Accédez directement à l’URL de connexion MOVEit Transfer - Azure AD integration pour y lancer le flux de connexion.

* Vous pouvez utiliser Mes applications de Microsoft. Lorsque vous cliquez sur la vignette MOVEit Transfer - Azure AD integration dans Mes applications, vous devez être connecté automatiquement à l’application MOVEit Transfer - Azure AD integration pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré MOVEit Transfer - Azure AD integration, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
