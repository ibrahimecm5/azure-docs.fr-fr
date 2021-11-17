---
title: 'Tutoriel : Intégration d’Azure Active Directory avec Ungerboeck Software | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Ungerboeck Software.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/27/2021
ms.author: jeedes
ms.openlocfilehash: 822e553d30d68990e961a29540e8d7a9c1d76f07
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132328562"
---
# <a name="tutorial-integrate-ungerboeck-software-with-azure-active-directory"></a>Tutoriel : Intégrer Ungerboeck Software avec Azure Active Directory

Dans ce didacticiel, vous allez apprendre à intégrer Ungerboeck Software avec Azure Active Directory (Azure AD). Lorsque vous intégrez Ungerboeck Software avec Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Ungerboeck Software.
* Autoriser les utilisateurs à se connecter automatiquement à Ungerboeck Software avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Ungerboeck Software pour lequel l’authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* Ungerboeck Software prend en charge l’authentification unique lancée par le **fournisseur de services**.

## <a name="add-ungerboeck-software-from-the-gallery"></a>Ajouter Ungerboeck Software à partir de la galerie

Pour configurer l’intégration de Ungerboeck Software dans Azure AD, vous devez ajouter Ungerboeck Software à partir de la galerie, à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Ungerboeck Software** dans la zone de recherche.
1. Sélectionnez **Ungerboeck Software** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-ungerboeck-software"></a>Configurer et tester l’authentification unique Azure AD pour Ungerboeck Software

Configurez et testez l’authentification unique Azure AD avec Ungerboeck Software pour un utilisateur de test nommé **B. Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Ungerboeck Software associé.

Pour configurer et tester l’authentification unique Azure AD avec Ungerboeck Software, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Ungerboeck Software](#configure-ungerboeck-software-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test Ungerboeck Software](#create-ungerboeck-software-test-user)** pour avoir, dans Ungerboeck Software, un équivalent de B.Simon lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Sur le portail Azure, dans la page d’intégration de l’application **Ungerboeck Software**, recherchez la section **Gérer**, puis sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon pour **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la page **Configuration SAML de base**, effectuez les étapes suivantes :

    1. Dans la zone de texte **URL de connexion**, saisissez une URL au format suivant : `https://<SUBDOMAIN>.ungerboeck.com/prod`

    1. Dans la zone de texte **Identificateur (ID d’entité)** , tapez une URL en utilisant un des modèles suivants :
    
    *  **Pour l’environnement de production** :

       - `https://<SUBDOMAIN>.ungerboeck.com/prod`
       - `https://<SUBDOMAIN>.ungerboeck.net/prod`
       - `https://<SUBDOMAIN>.ungerboeck.io/prod`

   * **Pour l’environnement de test** :

     - `https://<SUBDOMAIN>.ungerboeck.com/test`
     - `https://<SUBDOMAIN>.ungerboeck.net/test`
     - `https://<SUBDOMAIN>.ungerboeck.io/test`

   > [!NOTE]
   > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur et l’URL de connexion réels, tel qu’expliqué dans la section **Configurer l’authentification unique Ungerboeck Software** de ce didacticiel.

1. Dans la section **Certificat de signature SAML**, cliquez sur le bouton **Modifier** pour ouvrir la boîte de dialogue **Certificat de signature SAML**.

    ![Modifier le certificat de signature SAML](common/edit-certificate.png)

1. Dans la section **Certificat de signature SAML**, copiez l’**empreinte** et enregistrez-la sur votre ordinateur.

    ![Copier la valeur de l’empreinte](common/copy-thumbprint.png)

1. Dans la section **Configurer Ungerboeck Software**, copiez la ou les URL appropriées, selon vos besoins.

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

Dans cette section, vous allez autoriser B. Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Ungerboeck Software.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Ungerboeck Software**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-ungerboeck-software-sso"></a>Configurer l’authentification unique Ungerboeck Software

Pour configurer l’authentification unique côté **Ungerboeck Software**, vous devez envoyer la **valeur de l’empreinte** et les URL copiées correspondantes à partir du portail Azure à l’[équipe du support technique Ungerboeck Software](mailto:Rhonda.Jannings@ungerboeck.com). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

### <a name="create-ungerboeck-software-test-user"></a>Créer un utilisateur de test Ungerboeck Software

Dans cette section, vous allez créer un utilisateur appelé B.Simon dans Ungerboeck Software. Collaborez avec l’[équipe du support technique Ungerboeck Software](mailto:Rhonda.Jannings@ungerboeck.com) pour ajouter les utilisateurs à la plateforme Ungerboeck Software. Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion à Ungerboeck Software à partir de laquelle vous pouvez lancer le flux de connexion. 

* Accédez directement à l’URL de connexion à Ungerboeck Software et lancez le flux de connexion.

* Vous pouvez utiliser Mes applications de Microsoft. Le fait de cliquer sur la vignette Ungerboeck Software dans Mes applications vous redirige vers l’URL de connexion à Ungerboeck Software. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré Ungerboeck Software, vous pouvez appliquer le contrôle de session, qui protège en temps réel contre l’exfiltration et l’infiltration des données sensibles de votre organisation. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
