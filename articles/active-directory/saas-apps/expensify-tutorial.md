---
title: 'Didacticiel : Intégration d’Azure Active Directory avec Expensify | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Expensify.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/18/2021
ms.author: jeedes
ms.openlocfilehash: 888eec23b3bfb81dc656344aef7abf38b585b986
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132348823"
---
# <a name="tutorial-integrate-expensify-with-azure-active-directory"></a>Tutoriel : Intégrer Expensify avec Azure Active Directory

Dans ce didacticiel, vous allez apprendre à intégrer Expensify avec Azure Active Directory (Azure AD). Quand vous intégrez Expensify avec Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Expensify.
* Permettre à vos utilisateurs d’être automatiquement connectés à Expensify avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Expensify pour lequel l’authentification unique (SSO) est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* Expensify prend en charge l’authentification SSO lancée par le fournisseur de services (**SP**).

> [!NOTE]
> L’identificateur de cette application étant une valeur de chaîne fixe, une seule instance peut être configurée dans un locataire.

## <a name="add-expensify-from-the-gallery"></a>Ajouter Expensify à partir de la galerie

Pour configurer l’intégration de Expensify à Azure AD, vous devez ajouter Expensify à partir de la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la zone de recherche de la section **Ajouter à partir de la galerie**, tapez **Expensify**.
1. Sélectionnez **Expensify** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-expensify"></a>Configurer et tester l’authentification SSO Azure AD pour Expensify

Configurez et testez l’authentification unique Azure AD avec Expensify pour un utilisateur de test nommé **B. Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Expensify associé.

Pour configurer et tester l’authentification SSO Azure AD avec Expensify, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
2. **[Configurer l’authentification unique Expensify](#configure-expensify-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test Expensify](#create-expensify-test-user)** pour avoir un équivalent de B. Simon dans Expensify, associé à sa représentation dans Azure AD.
6. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, dans la page d’intégration de l’application **Expensify**, recherchez la section **Gérer**, puis sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon pour **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, entrez les valeurs pour les champs suivants :

    a. Dans la zone de texte **Identificateur (ID d’entité)** , tapez l’URL suivante : `https://www.expensify.com`

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://www.expensify.com/authentication/saml/loginCallback?domain=<yourdomain>`
    
    c. Dans la zone de texte **URL de connexion**, tapez l’URL : `https://www.expensify.com/authentication/saml/login`

    > [!NOTE]
    > La valeur de l’URL de réponse n’est pas réelle. Mettez à jour la valeur avec l’URL de réponse réelle. Pour obtenir cette valeur, contactez [l’équipe de support technique Expensify](mailto:help@expensify.com). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **XML des métadonnées**, puis sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

1. Dans la section **Configurer Expensify**, copiez l’URL ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B. Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Expensify.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Expensify**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-expensify-sso"></a>Configurer l’authentification unique Expensify

Pour activer l’authentification unique dans Expensify, vous devez d’abord activer le **contrôle de domaine** dans l’application. Vous pouvez activer le contrôle de domaine dans l’application via la procédure répertoriée [ici](https://help.expensify.com/domain-control). Pour une assistance supplémentaire, travaillez avec [l’équipe de support technique Expensify](mailto:help@expensify.com). Une fois que le contrôle de domaine est activé, suivez les étapes ci-dessous :

![Configure Single Sign-On](./media/expensify-tutorial/domain-control.png)

1. Connectez-vous à votre application Expensify.

2. Dans le volet gauche, pointez sur Settings (Paramètres), puis cliquez sur Domains (Domaines) et accédez à **SAML**.

3. Paramétrez l’option **Connexion SAML** sur **Activée**.

4. Dans le Bloc-notes, ouvrez les métadonnées de fédération téléchargées à partir d’Azure AD, copiez le contenu et collez-le dans la zone de texte **Identity Provider Metadata** (Métadonnées du fournisseur d’identité).

### <a name="create-expensify-test-user"></a>Créer un utilisateur de test Expensify

Dans cette section, vous créez le même utilisateur appelé B.Simon (par exemple, B.Simon@contoso.com) dans Expensify. Consultez son guide [ici](https://community.expensify.com/discussion/4869/how-to-manage-domain-members) pour inviter des membres ou collaborez avec l’[équipe du support technique d’Expensify](mailto:help@expensify.com) pour ajouter des utilisateurs à la plateforme Expensify.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Une redirection est effectuée vers l’URL de connexion à Expensify, où vous pouvez lancer le flux de connexion. 

* Accédez directement à l’URL de connexion à Expensify, puis lancez le flux de connexion à partir de cet emplacement.

* Vous pouvez utiliser Mes applications de Microsoft. Quand vous cliquez sur la vignette Expensify dans Mes applications, une redirection est effectuée vers l’URL de connexion à Expensify. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez configuré Expensify, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
