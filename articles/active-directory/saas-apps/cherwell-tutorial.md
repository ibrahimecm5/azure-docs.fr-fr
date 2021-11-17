---
title: 'Didacticiel : Intégration d’Azure Active Directory à Cherwell | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Cherwell.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/14/2021
ms.author: jeedes
ms.openlocfilehash: 9f252a4b6b8b82d6a992c6b5a18b3d519cd244d9
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132296421"
---
# <a name="tutorial-azure-active-directory-integration-with-cherwell"></a>Didacticiel : Intégration d’Azure Active Directory à Cherwell

Dans ce tutoriel, vous allez apprendre à intégrer Cherwell à Azure Active Directory (Azure AD). Quand vous intégrez Cherwell à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Cherwell.
* Permettre à vos utilisateurs de se connecter automatiquement à Cherwell avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à Cherwell, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un [compte gratuit](https://azure.microsoft.com/free/).

* Un abonnement Cherwell pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Cherwell prend en charge l’authentification unique initiée par le **fournisseur de services**

> [!NOTE]
> L’identificateur de cette application étant une valeur de chaîne fixe, une seule instance peut être configurée dans un locataire.

## <a name="add-cherwell-from-the-gallery"></a>Ajouter Cherwell à partir de la galerie

Pour configurer l’intégration de Cherwell à Azure AD, vous devez ajouter Cherwell à partir de la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Cherwell** dans la zone de recherche.
1. Sélectionnez **Cherwell** dans le panneau de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-cherwell"></a>Configurer et tester l’authentification unique Azure AD pour Cherwell

Configurez et testez l’authentification unique (SSO) Azure AD avec Cherwell à l’aide d’un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique (SSO) fonctionne, vous devez établir une relation entre un utilisateur Azure AD et l’utilisateur Cherwell associé.

Pour configurer et tester l’authentification unique Azure AD avec Cherwell, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    2. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
2. **[Configurer l’authentification unique Cherwell](#configure-cherwell-sso)** pour configurer les paramètres d’authentification unique côté application.
    1. **[Créer un utilisateur de test Cherwell](#create-cherwell-test-user)** pour disposer, dans Cherwell, d’un équivalent de B.Simon lié à la représentation Azure AD de l’utilisateur.
3. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans la page d’intégration de l’application **Cherwell** du portail Azure, recherchez la section **Gérer**, puis sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<companyname>.cherwellondemand.com/cherwellclient`.

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://*.cherwellondemand.com`
    
    > [!NOTE]
    > Cette valeur n’est pas la valeur réelle. Mettez à jour les valeurs avec l’URL de connexion et l’URL de réponse réelles. Pour obtenir cette valeur, contactez l’[équipe du support technique de Cherwell](https://cherwellsupport.com/CherwellPortal). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

5. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (Base64)** en fonction des options définies par rapport à vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

6. Dans la section **Configurer Cherwell**, copiez la ou les URL appropriées en fonction de vos besoins.

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

Dans cette section, vous allez créer un utilisateur de test nommé B.Simon dans le portail Azure.

1. Dans le volet gauche du portail Azure, sélectionnez **Azure Active Directory**, **Utilisateurs**, puis **Tous les utilisateurs**.
1. Sélectionnez **Nouvel utilisateur**, en haut de l’écran.
1. Dans les propriétés **Utilisateur**, effectuez les étapes suivantes :
   1. Dans le champ **Nom**, entrez **B.Simon**.  
   1. Dans le champ **Nom de l’utilisateur**, entrez `<username>@<companydomain>.<extension>`. Par exemple : `B.Simon@contoso.com`.
   1. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.
   1. Sélectionnez **Create** (Créer).

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Cherwell.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Cherwell**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-cherwell-sso"></a>Configurer l’authentification unique Cherwell

Pour configurer l’authentification unique côté **Cherwell**, vous devez envoyer le **Certificat (Base64)** téléchargé et les URL copiées correspondantes, depuis le portail Azure à l’[équipe du support technique Cherwell](https://cherwellsupport.com/CherwellPortal). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

> [!NOTE]
> L’équipe de support technique Cherwell doit se charger de la configuration de l’authentification unique. Vous recevrez une notification dès que l’authentification unique aura été activée pour votre abonnement.

### <a name="create-cherwell-test-user"></a>Créer un utilisateur de test Cherwell

Pour pouvoir se connecter à Cherwell, les utilisateurs d’Azure AD doivent être provisionnés dans Cherwell. Dans le cas de Cherwell, les comptes d’utilisateur doivent être créés par [l’équipe du support Cherwell](https://cherwellsupport.com/CherwellPortal).

> [!NOTE]
> Vous pouvez utiliser tout autre outil ou n’importe quelle API de création de compte d’utilisateur fournis par Cherwell pour approvisionner des comptes d’utilisateur Azure Active Directory.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Cette opération redirige vers l’URL d’authentification Cherwell, où vous pouvez lancer le processus de connexion. 

* Accédez directement à l’URL d’authentification Cherwell pour lancer le processus de connexion.

* Vous pouvez utiliser Mes applications de Microsoft. Le fait de cliquer sur la vignette Cherwell dans Mes applications doit vous connecter automatiquement à l’application Cherwell pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré Cherwell, vous pouvez appliquer le contrôle de session qui protège l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
