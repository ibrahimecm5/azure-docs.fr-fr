---
title: 'Tutoriel : Intégration de l’authentification unique Azure Active Directory à Fabric | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Fabric.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/08/2021
ms.author: jeedes
ms.openlocfilehash: 950468ff48a0d384d46a04fa3b2a93b0419c2fbe
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132314132"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-fabric"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory à Fabric

Dans ce tutoriel, vous allez apprendre à intégrer Fabric à Azure Active Directory (Azure AD). Quand vous intégrez Fabric à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Fabric.
* Permettre à vos utilisateurs de se connecter automatiquement à Fabric avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Fabric pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* Fabric prend en charge l’authentification unique initiée par le **fournisseur de services**.

## <a name="adding-fabric-from-the-gallery"></a>Ajouter Fabric à partir de la galerie

Pour configurer l’intégration de Fabric dans Azure AD, vous devez ajouter Fabric à partir de la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, saisissez **Fabric** dans la zone de recherche.
1. Sélectionnez **Fabric** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.


## <a name="configure-and-test-azure-ad-sso-for-fabric"></a>Configurer et tester l’authentification unique Azure AD pour Fabric

Configurez et testez l’authentification unique Azure AD avec Fabric à l’aide d’un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur associé dans Fabric.

Pour configurer et tester l’authentification unique Azure AD avec Fabric, procédez comme suit :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique de Fabric](#configure-fabric-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test Fabric](#create-fabric-roles)** pour avoir un équivalent de B.Simon dans Fabric lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **Fabric**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, entrez les valeurs pour les champs suivants :

   1. Dans la zone de texte **Identificateur**, tapez une URL au format suivant :  
      `https://<HOSTNAME>`

   1. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant :  
      `https://<HOSTNAME>:<PORT>/api/authenticate`
    
   1. Dans la zone de texte **URL de connexion**, saisissez une URL au format suivant :   
      `https://<HOSTNAME>:<PORT>`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL d’authentification et l’URL de réponse réels. Pour obtenir ces valeurs, contactez l’équipe K2View COE. Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **Certificat (en base64)** , puis sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

1. Dans la section **Configurer Fabric**, copiez les URL appropriées en fonction de vos besoins.

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

1. Dans la section **Chiffrement du jeton** , sélectionnez **Importer un certificat** et chargez le fichier de certificat Fabric. Pour l’obtenir, contactez l’équipe K2View COE.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Fabric.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Fabric**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-fabric-sso"></a>Configurer l’authentification unique de Fabric

Pour configurer l’authentification unique côté **Fabric**, envoyez le **certificat (Base64)** téléchargé et les URL appropriées, copiées à partir du portail Azure, à l’équipe du support technique de K2View COE. L’équipe configure le paramètre pour que l’authentification unique SAML soit configurée correctement des deux côtés.

Pour plus d’informations, consultez *Fabric SAML Configuration* (Configuration SAML pour Fabric) et *Azure AD SAML Setup Guide* (Guide de configuration SAML pour Azure AD) dans la [base de connaissances de K2View](https://support.k2view.com/knowledge-base.html).

### <a name="create-fabric-roles"></a>Créer un utilisateur de test Fabric

Collaborez avec l’équipe du support technique de K2View COE pour définir les rôles Fabric qui correspondent aux groupes Azure AD et qui sont pertinents pour les utilisateurs qui vont utiliser Fabric. Vous fournirez à l’équipe de Fabric les identifiants de groupe, car ils sont envoyés dans la réponse SAML.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Dans le portail Azure, sélectionnez **Tester cette application**. Vous êtes alors redirigé vers l’URL de connexion de Fabric, à partir de laquelle vous pouvez lancer le flux de connexion. 

* Accédez directement à l’URL de connexion de Fabric pour initier le flux de connexion.

* Vous pouvez utiliser Mes applications de Microsoft. Lorsque vous sélectionnez la vignette **Fabric** dans le portail Mes applications, vous êtes redirigé vers l’URL de connexion de Fabric. Pour plus d’informations sur le portail Mes applications, consultez [Introduction au portail Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).


## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez configuré Fabric, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
