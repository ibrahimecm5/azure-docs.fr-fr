---
title: 'Tutoriel : Intégration d’Azure Active Directory à Sauce Labs - Mobile and Web Testing | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Sauce Labs - Mobile and Web Testing.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/20/2021
ms.author: jeedes
ms.openlocfilehash: 46037ff025b11af5e7acbd2ecac1d3eac485b63c
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132341347"
---
# <a name="tutorial-azure-active-directory-integration-with-sauce-labs---mobile-and-web-testing"></a>Tutoriel : Intégration d’Azure Active Directory à Sauce Labs - Mobile and Web Testing

Dans ce tutoriel, découvrez comment intégrer Sauce Labs - Mobile and Web Testing à Azure Active Directory (Azure AD). Quand vous intégrez Sauce Labs - Mobile and Web Testing à Azure AD, vous pouvez :

* Contrôler qui a accès à Sauce Labs - Mobile and Web Testing dans Azure AD.
* Permettre aux utilisateurs de se connecter automatiquement à Sauce Labs - Mobile and Web Testing avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à Sauce Labs - Mobile and Web Testing, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un [compte gratuit](https://azure.microsoft.com/free/).
* Abonnement Sauce Labs - Mobile and Web Testing avec l’authentification unique activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Sauce Labs - Mobile and Web Testing prend en charge l’authentification unique lancée par le **fournisseur de services**.
* Sauce Labs - Mobile and Web Testing prend en charge l’attribution d’utilisateurs **Juste-à-temps**.

## <a name="add-sauce-labs---mobile-and-web-testing-from-the-gallery"></a>Ajout de Sauce Labs - Mobile and Web Testing à partir de la galerie

Pour configurer l’intégration de Sauce Labs - Mobile and Web Testing dans Azure AD, vous devez ajouter Sauce Labs - Mobile and Web Testing à votre liste d’applications SaaS gérées à partir de la galerie.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, entrez **Sauce Labs - Mobile and Web Testing** dans la zone de recherche.
1. Sélectionnez **Sauce Labs - Mobile and Web Testing** dans le panneau des résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-sauce-labs---mobile-and-web-testing"></a>Configurer et tester Azure AD SSO pour Sauce Labs - Mobile and Web Testing

Configurez et testez Azure AD SSO avec Sauce Labs - Mobile and Web Testing pour un utilisateur de test nommé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur associé dans Sauce Labs - Mobile and Web Testing.

Pour configurer et tester Azure AD SSO avec Sauce Labs - Mobile and Web Testing, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Sauce Labs - Mobile and Web Testing](#configure-sauce-labs---mobile-and-web-testing-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test Sauce Labs - Mobile and Web Testing](#create-sauce-labs---mobile-and-web-testing-test-user)** pour avoir, dans Sauce Labs - Mobile and Web Testing, un équivalent de B.Simon lié à la représentation Azure AD associée.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, dans la page d’intégration de l’application **Sauce Labs - Mobile and Web Testing**, recherchez la section **Gérer**, puis sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, l’utilisateur n’a rien à faire, car l’application est déjà intégrée à Azure.

5. Sur la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le fichier **XML de métadonnées de fédération** en fonction des options définies selon vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

6. Dans la section **Configurer Sauce Labs - Mobile and Web Testing**, copiez l’URL ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Sauce Labs - Mobile and Web Testing.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Sauce Labs - Mobile and Web Testing**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-sauce-labs---mobile-and-web-testing-sso"></a>Configurer l’authentification unique pour Sauce Labs - Mobile and Web Testing

1. Dans une autre fenêtre du navigateur web, ouvrez une session sur votre site d’entreprise Sauce Labs - Mobile and Web Testing en tant qu’administrateur.

2. Cliquez sur l’**icône Utilisateur** et sélectionnez l’onglet **Gestion des équipes**.

    ![Capture d’écran montrant l’icône représentant un utilisateur et l’option « Team Management » sélectionnée](./media/saucelabs-mobileandwebtesting-tutorial/user.png)

3. Entrez votre **nom de domaine** dans la zone de texte.

    ![Capture d’écran montrant un exemple de nom de domaine dans la zone de texte](./media/saucelabs-mobileandwebtesting-tutorial/domain.png)

4. Cliquez sur l’onglet **Configurer**.

    ![Capture d’écran montrant l’onglet « Configure » sélectionné sous « Single Sign On is Enabled »](./media/saucelabs-mobileandwebtesting-tutorial/configure.png)

5. Dans la section **Configurer l’authentification unique**, procédez comme suit.

    ![Configure Single Sign-On](./media/saucelabs-mobileandwebtesting-tutorial/browse.png)

    a. Cliquez sur **Parcourir** et chargez le fichier de métadonnées téléchargé à partir d’Azure AD.

    b. Cochez la case **AUTORISER L’APPROVISIONNEMENT JUSTE-À-TEMPS**.

    c. Cliquez sur **Enregistrer**.

### <a name="create-sauce-labs---mobile-and-web-testing-test-user"></a>Créer un utilisateur de test Sauce Labs - Mobile and Web Testing

Dans cette section, vous créez un utilisateur appelé Britta Simon dans Sauce Labs - Mobile and Web Testing. Sauce Labs - Mobile and Web Testing prend en charge l’attribution d’utilisateurs juste-à-temps, une option activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. Si l’utilisateur souhaité n’existe pas déjà dans Sauce Labs - Mobile and Web Testing, il est créé après l’authentification.

> [!Note]
> Si vous devez créer un utilisateur manuellement, contactez [l’équipe de support de Sauce Labs - Mobile and Web Testing](mailto:support@saucelabs.com).

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes.

* Cliquez sur Tester cette application dans le portail Azure. Vous devez être connecté automatiquement à l’instance de Sauce Labs - Mobile and Web Testing pour laquelle vous avez configuré l’authentification unique.

* Vous pouvez utiliser Mes applications de Microsoft. Quand vous cliquez sur la vignette Sauce Labs - Mobile and Web Testing dans Mes applications, vous devez être connecté automatiquement à l’application Sauce Labs - Mobile and Web Testing pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré Sauce Labs - Mobile and Web Testing, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
