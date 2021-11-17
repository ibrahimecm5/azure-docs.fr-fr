---
title: 'Didacticiel : Intégration d’Azure Active Directory à l’application métier avec jeton SAML 1.1 activé | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et l’application métier avec jeton SAML 1.1 activé.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/05/2021
ms.author: jeedes
ms.openlocfilehash: a21906305482e6cda4db72ec7cf2127d9b680b18
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132341575"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-11-token-enabled-lob-app"></a>Didacticiel : Intégration d’Azure Active Directory à l’application métier avec jeton SAML 1.1 activé

Dans ce tutoriel, vous allez voir comment intégrer SAML 1.1 Token enabled LOB App à Azure Active Directory (Azure AD). Lorsque vous intégrez SAML 1.1 Token enabled LOB App à Azure AD, vous pouvez :

* Contrôler qui dans Azure AD a accès à SAML 1.1 Token enabled LOB App.
* Permettre aux utilisateurs de se connecter automatiquement à SAML 1.1 Token enabled LOB App avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement à SAML 1.1 Token enabled LOB App pour lequel l’authentification unique (SSO) est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* L’application métier avec jeton SAML 1.1 activé prend en charge l’authentification unique initiée par le **fournisseur de services**

> [!NOTE]
> L’identificateur de cette application étant une valeur de chaîne fixe, une seule instance peut être configurée dans un locataire.

## <a name="add-saml-11-token-enabled-lob-app-from-the-gallery"></a>Ajouter SAML 1.1 Token enabled LOB App à partir de la galerie

Pour configurer l’intégration de l’application métier avec jeton SAML 1.1 activé à Azure AD, vous devez ajouter l’application métier avec jeton SAML 1.1 activé à partir de la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **SAML 1.1 Token enabled LOB App** dans la zone de recherche.
1. Sélectionnez **SAML 1.1 Token enabled LOB App** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-saml-11-token-enabled-lob-app"></a>Configurer et tester l’authentification unique Azure AD pour SAML 1.1 Token enabled LOB App

Configurez et testez l’authentification unique (SSO) Azure AD pour SAML 1.1 Token enabled LOB App à l’aide d’un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur SAML 1.1 Token enabled LOB App associé.

Si vous souhaitez configurer l’authentification unique Azure AD pour SAML 1.1 Token enabled LOB App, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique pour SAML 1.1 Token enabled LOB App](#configure-saml-11-token-enabled-lob-app-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test SAML 1.1 Token enabled LOB App](#create-saml-11-token-enabled-lob-app-test-user)** pour avoir un équivalent de B.Simon dans SAML 1.1 Token enabled LOB App qui soit lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **SAML 1.1 Token enabled LOB App**, recherchez la section **Gérer**, puis sélectionnez **authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    a. Dans la zone de texte **URL de connexion**, saisissez une URL au format suivant : `https://your-app-url`

    b. Dans la zone de texte **Identificateur (ID d’entité)** , saisissez une URL au format suivant : `https://your-app-url`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Contactez l’équipe du support technique de l’application métier avec jeton SAML 1.1 activé pour obtenir ces valeurs. Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

5. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (Base64)** en fonction des options définies par rapport à vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

6. Dans la section **Configurer l’application métier avec jeton SAML 1.1 activé**, copiez la ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à SAML 1.1 Token enabled LOB App.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Application métier avec jeton SAML 1.1 activé**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-saml-11-token-enabled-lob-app-sso"></a>Configurer l’authentification unique pour SAML 1.1 Token enabled LOB App

Pour configurer l’authentification unique côté **Application métier avec jeton SAML 1.1 activé**, vous devez envoyer le **Certificat (Base64)** téléchargé et les URL correspondantes copiées depuis le portail Azure à l’équipe du support technique de l’application métier avec jeton SAML 1.1 activé. Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

### <a name="create-saml-11-token-enabled-lob-app-test-user"></a>Créer un utilisateur de test d’application métier avec jeton SAML 1.1 activé

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans l’application métier avec jeton SAML 1.1 activé. Collaborez avec l’équipe du support technique de l’application métier avec jeton SAML 1.1 activé pour ajouter les utilisateurs dans la plateforme de l’application. Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Cette opération redirige vers l’URL de connexion de SAML 1.1 Token enabled LOB App d’où vous pouvez lancer le flux de connexion. 

* Accédez directement à l’URL de connexion SAML 1.1 Token enabled LOB App pour lancer le flux de connexion.

* Vous pouvez utiliser Mes applications de Microsoft. Quand vous cliquez sur la vignette SAML 1.1 Token enabled LOB App dans Mes applications, vous êtes redirigé vers l’URL d’authentification de SAML 1.1 Token enabled LOB App. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez configuré SAML 1.1 Token enabled LOB App, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
