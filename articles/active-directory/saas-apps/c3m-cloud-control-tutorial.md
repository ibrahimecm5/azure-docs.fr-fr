---
title: 'Tutoriel : Intégration de l’authentification unique Azure Active Directory avec C3M Cloud Control | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et C3M Cloud Control.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/11/2021
ms.author: jeedes
ms.openlocfilehash: 3d9c308e1a51e2e84fc1d28f1e21f0ef4a43b71e
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132314793"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-c3m-cloud-control"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory à C3M Cloud Control

Dans ce tutoriel, vous allez apprendre à intégrer C3M Cloud Control à Azure Active Directory (Azure AD). Quand vous intégrez C3M Cloud Control à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à C3M Cloud Control.
* Permettre à vos utilisateurs de se connecter automatiquement à C3M Cloud Control avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement C3M Cloud Control pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* C3M Cloud Control prend en charge l’authentification unique lancée par le **fournisseur de services**.
* C3M Cloud Control prend en charge le provisionnement d’utilisateurs **juste-à-temps**.

## <a name="add-c3m-cloud-control-from-the-gallery"></a>Ajouter C3M Cloud Control à partir de la galerie

Pour configurer l’intégration de C3M Cloud Control à Azure AD, vous devez ajouter C3M Cloud Control à votre liste d’applications SaaS managées à partir de la galerie.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **C3M Cloud Control** dans la zone de recherche.
1. Sélectionnez **C3M Cloud Control** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-c3m-cloud-control"></a>Configurer et tester l’authentification unique Azure AD pour C3M Cloud Control

Configurez et testez l’authentification unique Azure AD avec C3M Cloud Control pour un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur C3M Cloud Control associé.

Pour configurer et tester l’authentification unique Azure AD avec C3M Cloud Control, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique C3M Cloud Control](#configure-c3m-cloud-control-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test C3M Cloud Control](#create-c3m-cloud-control-test-user)** pour avoir un équivalent de B.Simon dans C3M Cloud Control qui soit lié à la représentation Azure AD associée.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **C3M Cloud Control**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    a. Dans la zone de texte **Identificateur (ID d’entité)** , saisissez une URL au format suivant : `https://<C3MCLOUDCONTROL_ACCESS_URL>/api/sso/saml`

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://<C3MCLOUDCONTROL_ACCESS_URL>/api/sso/saml`

    c. Dans la zone de texte **URL de connexion**, saisissez une URL au format suivant : `https://<C3MCLOUDCONTROL_ACCESS_URL>`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL de réponse et l’URL de connexion réels. Pour obtenir ces valeurs, contactez l’[équipe de support technique de C3M Cloud Control](mailto:support@c3m.io). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **Certificat (en base64)** , puis sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

1. Dans la section **Configurer C3M Cloud Control**, copiez la ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à C3M Cloud Control.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **C3M Cloud Control**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-c3m-cloud-control-sso"></a>Configurer l’authentification unique C3M Cloud Control

 Pour configurer l’authentification unique pour C3M Cloud Control, veuillez suivre la [documentation](https://c3m.freshdesk.com/support/solutions/articles/44001946272-configuring-sso-using-saml-azure).

### <a name="create-c3m-cloud-control-test-user"></a>Créer un utilisateur de test C3M Cloud Control

Dans cette section, un utilisateur appelé B.Simon est créé dans C3M Cloud Control. C3M Cloud Control Cloud prend en charge le provisionnement d’utilisateurs juste-à-temps, qui est activé par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas encore d’utilisateur dans C3M Cloud Control, il en est créé un après l’authentification.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Cette opération redirige vers l’URL de connexion C3M Cloud Control, où vous pouvez lancer le flux de connexion. 

* Accédez directement à l’URL de connexion C3M Cloud Control pour y lancer le flux de connexion.

* Vous pouvez utiliser Mes applications de Microsoft. Un clic sur la vignette C3M Cloud Control dans Mes applications vous redirige vers l’URL de connexion à C3M Cloud Control. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré C3M Cloud Control, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
