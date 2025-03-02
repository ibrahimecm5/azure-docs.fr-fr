---
title: 'Tutoriel : Intégration de l’authentification unique Azure Active Directory à In Case of Crisis - Mobile | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et In Case of Crisis - Mobile.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/04/2019
ms.author: jeedes
ms.openlocfilehash: e0eaf87b71403a3acedc79ea52f6cc1ad6a8b0fb
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124757265"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-in-case-of-crisis---mobile"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory à In Case of Crisis - Mobile

Dans ce tutoriel, vous allez apprendre à intégrer In Case of Crisis - Mobile à Azure Active Directory (Azure AD). Quand vous intégrez In Case of Crisis - Mobile à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à In Case of Crisis - Mobile.
* Permettre à vos utilisateurs de se connecter automatiquement à In Case of Crisis - Mobile avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS à Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement In Case of Crisis - Mobile pour lequel l’authentification unique (SSO) est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* In Case of Crisis - Mobile prend en charge l’authentification unique lancée par le **fournisseur d’identité**

> [!NOTE]
> L’identificateur de cette application étant une valeur de chaîne fixe, une seule instance peut être configurée dans un locataire.

## <a name="adding-in-case-of-crisis---mobile-from-the-gallery"></a>Ajout d’In Case of Crisis - Mobile à partir de la galerie

Pour configurer l’intégration d’In Case of Crisis - Mobile à Azure AD, vous devez ajouter In Case of Crisis - Mobile à partir de la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **In Case of Crisis - Mobile** dans la zone de recherche.
1. Sélectionnez **In Case of Crisis - Mobile** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-single-sign-on-for-in-case-of-crisis---mobile"></a>Configurer et tester l’authentification unique Azure AD pour In Case of Crisis - Mobile

Configurez et testez l’authentification unique Azure AD avec In Case of Crisis - Mobile à l’aide d’un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur In Case of Crisis - Mobile associé.

Pour configurer et tester l’authentification unique Azure AD avec In Case of Crisis - Mobile, suivez les indications des sections ci-après :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique In Case of Crisis - Mobile](#configure-in-case-of-crisis---mobile-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test In Case of Crisis - Mobile](#create-in-case-of-crisis---mobile-test-user)** pour avoir un équivalent de B.Simon dans In Case of Crisis - Mobile qui est lié à la représentation d’un utilisateur Azure AD.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le [portail Azure](https://portal.azure.com/), accédez à la page d’intégration de l’application **In Case of Crisis - Mobile**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de modification/stylet de **Configuration SAML de base** pour modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, l’application est préconfigurée en mode Lancement par le **fournisseur d’identité** et les URL nécessaires sont déjà préremplies avec Azure. L’utilisateur doit enregistrer la configuration en cliquant sur le bouton **Enregistrer**.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **Certificat (brut)** , puis sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificateraw.png)

1. Accédez à la section **Gérer** sur le côté gauche de la page, cliquez sur l’onglet **Propriétés**, puis copiez l’**URL de l’accès utilisateur** et enregistrez-la sur votre ordinateur.

    ![Propriétés de l’authentification unique](./media/in-case-of-crisis-mobile-tutorial/properties.png)

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à In Case of Crisis - Mobile.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **In Case of Crisis - Mobile**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.

   ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Lien Ajouter un utilisateur](common/add-assign-user.png)

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-in-case-of-crisis---mobile-sso"></a>Configurer l’authentification unique In Case of Crisis - Mobile

Pour configurer l’authentification unique côté **In Case of Crisis - Mobile**, vous devez envoyer le **Certificat (brut)** téléchargé ainsi que l’**URL de l’accès utilisateur** copiée sur le portail Azure à l’[équipe du support technique In Case of Crisis - Mobile](https://www.rockdovesolutions.com/features/enterprise-ready). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

### <a name="create-in-case-of-crisis---mobile-test-user"></a>Créer un utilisateur de test In Case of Crisis - Mobile

Dans cette section, vous créez un utilisateur appelé Britta Simon dans In Case of Crisis - Mobile. Collaborez avec l’[équipe du support technique In Case of Crisis - Mobile](https://www.rockdovesolutions.com/features/enterprise-ready) pour ajouter les utilisateurs à la plateforme In Case of Crisis - Mobile. Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Le fait de cliquer sur la vignette In Case of Crisis - Mobile dans le panneau d’accès doit vous connecter automatiquement à l’application In Case of Crisis - Mobile pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de tutoriels sur l’intégration d’applications SaaS avec Azure Active Directory](./tutorial-list.md)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](../conditional-access/overview.md)

- [Essayer In Case of Crisis - Mobile avec Azure AD](https://aad.portal.azure.com/)