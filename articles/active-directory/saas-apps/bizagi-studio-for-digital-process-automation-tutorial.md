---
title: 'Tutoriel : Intégration de l’authentification unique (SSO) Azure Active Directory avec Bizagi for Digital Process Automation | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Bizagi for Digital Process Automation.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/27/2021
ms.author: jeedes
ms.openlocfilehash: 16389290e20f74c24b683818502eaff96f380d77
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132335059"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-bizagi-for-digital-process-automation"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory avec Bizagi for Digital Process Automation

Dans ce tutoriel, vous allez apprendre à intégrer Bizagi for Digital Process Automation Services ou Server avec Azure AD (Azure Active Directory). Quand vous intégrez Bizagi for Digital Process Automation avec Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Bizagi for Digital Process Automation Services ou Server.
* Permettre à vos utilisateurs de se connecter automatiquement à un projet de Bizagi for Digital Process Automation Services ou Server avec leurs comptes Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/)
* Un projet Bizagi utilisant Automation Services ou Server 
* Disposer de vos propres certificats pour les signatures d’assertion SAML. Ces certificats doivent être générés au format p12 ou pfx
* Avoir un fichier de métadonnées au format XML généré à partir du projet Bizagi 

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un projet Bizagi à l’aide des services ou du serveur Automation.

* Bizagi for Digital Process Automation prend en charge l’authentification unique lancée par le **fournisseur de services**.
* Bizagi for Digital Process Automation prend en charge l’[attribution automatisée d’utilisateurs](bizagi-studio-for-digital-process-automation-provisioning-tutorial.md).

## <a name="add-bizagi-for-digital-process-automation-from-the-gallery"></a>Ajouter Bizagi for Digital Process Automation à partir de la galerie

Pour configurer l’intégration de Bizagi for Digital Process Automation avec Azure AD, vous devez ajouter Bizagi for Digital Process Automation à votre liste d’applications SaaS managées à partir de la galerie.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, dans la zone de recherche, tapez **Bizagi for Digital Process Automation**.
1. Sélectionnez **Bizagi for Digital Process Automation** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-bizagi-for-digital-process-automation"></a>Configurer et tester l’authentification unique Azure AD pour Bizagi for Digital Process Automation

Configurez et testez l’authentification unique Azure AD avec Bizagi for Digital Process Automation à l’aide d’un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur associé dans le projet Bizagi.

Pour configurer et tester l’authentification unique Azure AD avec Bizagi for Digital Process Automation, procédez comme suit :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Bizagi for Digital Process Automation](#configure-bizagi-for-digital-process-automation-sso)** pour configurer les paramètres d’authentification unique côté application.
    1. **[Créer un utilisateur de test Bizagi for Digital Process Automation](#create-bizagi-for-digital-process-automation-test-user)** pour avoir un équivalent de B.Simon dans Bizagi for Digital Process Automation qui soit lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, dans la page d’intégration de l’application **Bizagi for Digital Process Automation**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    a. Dans la zone de texte **Identificateur (ID d’entité)** , saisissez une URL au format suivant : `https://<COMPANY_NAME>.bizagi.com/<PROJECT_NAME>`

    b. Dans la zone de texte **URL de connexion**, saisissez une URL au format suivant : `https://<COMPANY_NAME>.bizagi.com/<PROJECT_NAME>`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur et l’URL de connexion réels. Pour obtenir ces valeurs, contactez l’[équipe du support technique de Bizagi for Digital Process Automation](mailto:jarvein.rivera@bizagi.com). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur le bouton Copier pour copier l’**URL des métadonnées de fédération d’application**, puis enregistrez-la sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/copy-metadataurl.png)
    
    Cette URL de métadonnées doit être inscrite dans les options d’authentification de votre projet Bizagi.
    
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon pour **Attributs utilisateur et revendications** afin de modifier l’identificateur d’utilisateur unique.
    
    Définissez l’identifiant utilisateur unique en tant que user.mail.

### <a name="create-an-azure-ad-test"></a>Créer un utilisateur de test Azure AD 

Dans cette section, vous allez créer un utilisateur de test appelé B. Simon dans le portail Azure.

1. Dans le volet gauche du Portail Azure, sélectionnez **Azure Active Directory**, **Utilisateurs**, puis **Tous les utilisateurs**.
1. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.
1. Dans les propriétés **Utilisateur**, effectuez les étapes suivantes :
   1. Dans le champ **Nom**, entrez `B.Simon`.  
   1. Dans le champ **Nom de l’utilisateur**, entrez username@companydomain.extension. Par exemple : `B.Simon@contoso.com`.
   1. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.
   1. Cliquez sur **Créer**.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez permettre à B.Simon d’utiliser l’authentification unique Azure en lui octroyant l’accès à Bizagi for Digital Process Automation.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Bizagi for Digital Process Automation**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-bizagi-for-digital-process-automation-sso"></a>Configurer l’authentification unique pour Bizagi for Digital Process Automation

Pour configurer l’authentification unique côté **Bizagi for Digital Process Automation**, vous devez envoyer l’**URL des métadonnées de fédération d’application** à l’[équipe du support technique de Bizagi for Digital Process Automation](mailto:jarvein.rivera@bizagi.com). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

### <a name="create-bizagi-for-digital-process-automation-test-user"></a>Créer un utilisateur de test Bizagi for Digital Process Automation

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans Bizagi for Digital Process Automation. Collaborez avec l’[équipe du support technique de Bizagi for Digital Process Automation](mailto:jarvein.rivera@bizagi.com) pour ajouter les utilisateurs à la plateforme Bizagi for Digital Process Automation. Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique.

Bizagi for Digital Process Automation prend également en charge l’attribution automatique d’utilisateurs. Vous trouverez plus d’informations sur la configuration de cette fonctionnalité [ici](./bizagi-studio-for-digital-process-automation-provisioning-tutorial.md).

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion à Bizagi for Digital Process Automation, à partir de laquelle vous pouvez lancer le flux de connexion. 

* Accédez directement à l’URL de connexion à Bizagi for Digital Process Automation pour lancer le flux de connexion.

* Vous pouvez utiliser Mes applications de Microsoft. Quand vous cliquez sur la vignette Bizagi for Digital Process Automation dans Mes applications, cette opération redirige vers l’URL de connexion à Bizagi for Digital Process Automation. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez configuré Bizagi for Digital Process Automation, vous pouvez appliquer le contrôle de session, qui protège l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
