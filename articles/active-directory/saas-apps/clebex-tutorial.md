---
title: 'Tutoriel : Intégration de l’authentification unique (SSO) Azure Active Directory à Clebex | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Clebex.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/27/2021
ms.author: jeedes
ms.openlocfilehash: 82fd60f6d438703ad2c782003d06bbf0f8290394
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132307704"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-clebex"></a>Tutoriel : Intégration de l’authentification unique (SSO) Azure Active Directory à Clebex

Dans ce tutoriel, vous apprenez à intégrer Clebex à Azure Active Directory (Azure AD). Quand vous intégrez Clebex à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Clebex.
* Permettre à vos utilisateurs de se connecter automatiquement à Clebex avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Clebex avec l’authentification unique (SSO) activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* Clebex prend en charge l’authentification unique lancée par le **fournisseur de services**.

* Clebex prend en charge le provisionnement d’utilisateurs **juste-à-temps**.

* Clebex prend en charge l’[attribution automatique d’utilisateurs](clebex-provisioning-tutorial.md).


## <a name="add-clebex-from-the-gallery"></a>Ajouter Clebex à partir de la galerie

Pour configurer l’intégration de Clebex à Azure AD, vous devez ajouter Clebex à votre liste d’applications SaaS managées à partir de la galerie.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Clebex** dans la zone de recherche.
1. Sélectionnez **Clebex** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.


## <a name="configure-and-test-azure-ad-sso-for-clebex"></a>Configurer et tester l’authentification unique Azure AD pour Clebex

Configurez et testez l’authentification unique Azure AD dans Clebex à l’aide d’un utilisateur de test nommé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur associé dans Clebex.

Pour configurer et tester l’authentification unique Azure AD dans Clebex, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique dans Clebex](#configure-clebex-sso)** pour configurer les paramètres d’authentification unique côté application.
    1. **[Créer un utilisateur de test dans Clebex](#create-clebex-test-user)** pour avoir un équivalent de B.Simon dans Clebex lié à la représentation Azure AD associée.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, dans la page d’intégration de l’application **Clebex**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, entrez les valeurs pour les champs suivants :

    a. Dans la zone de texte **Identificateur**, tapez une URL en utilisant le format suivant : `https://<CustomerName>.domain.extention/<ID>`

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://<CustomerName>.domain.extention/<ID>`

    c. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<CustomerName>.domain.extention/<ID>`.

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL de réponse et l’URL de connexion réels. Pour obtenir ces valeurs, contactez l’[équipe de support du client Clebex](mailto:support@clebex.net). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **Certificat (en base64)** , puis sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

1. Dans la section **Configurer Clebex**, copiez la ou les URL appropriées, en fonction de vos besoins.

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

Dans cette section, vous autorisez B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Clebex.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Clebex**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-clebex-sso"></a>Configurer l’authentification unique dans Clebex

1. Connectez-vous à votre site web Clebex en tant qu’administrateur.

1. Accédez à COMPANY ADMIN (Administrateur de l’entreprise) -> **Connectors**(Connecteurs) -> **Single Sign On (SSO)** (Authentification unique (SSO)) et cliquez sur **select** (sélectionner).

    ![Capture d’écran montrant comment sélectionner le type de connecteur.](./media/clebex-tutorial/single-sign-on.png)

1. Dans les connecteurs, sélectionnez **Azure SSO** et suivez les étapes ci-dessous dans la section EDIT-CONNECTOR (Modifier le connecteur).

    ![Capture d’écran montrant comment sélectionner des connecteurs et la configuration.](./media/clebex-tutorial/azure-sso.png)

    a. Copiez la valeur de **IDENTIFIER** (Identificateur), collez-la dans la zone de texte **Identificateur** de la section **Configuration SAML de base** du portail Azure.

    b. Copiez la valeur de **REPLY URL** (URL de réponse), collez-la dans la zone de texte **URL de réponse** de la section **Configuration SAML de base** du portail Azure.

    c. Dans la zone de texte **ENTITY ID** (ID d’identité), collez la valeur **Identificateur Azure AD** que vous avez copiée dans le portail Azure.

    d. Dans la zone de texte **SAML**, collez la valeur de l’**URL de connexion** que vous avez copiée dans le portail Azure.

    e. Ouvrez le **Certificat (Base64)** téléchargé à partir du portail Azure dans le Bloc-notes et collez le contenu dans la zone de texte **CERTIFICATE**.

    f. Cliquez sur **SAVE-CHANGES** (Enregistrer les changements).

### <a name="create-clebex-test-user"></a>Créer un utilisateur de test dans Clebex

Dans cette section, un utilisateur nommé Britta Simon est créé dans Clebex. Clebex prend en charge le provisionnement d’utilisateurs juste-à-temps, option activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’y a pas d’utilisateur dans Clebex, il est créé après l’authentification.

Clebex prend aussi en charge l’attribution automatique d’utilisateurs. Vous trouverez [ici](./clebex-provisioning-tutorial.md) plus d’informations sur la façon de configurer l’attribution automatique d’utilisateurs.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion de Clebex, où vous pouvez démarrer le flux de connexion. 

* Accédez directement à l’URL de connexion de Clebex pour lancer le flux de connexion.

* Vous pouvez utiliser Mes applications de Microsoft. Quand vous cliquez sur la vignette Clebex dans Mes applications, vous êtes redirigé vers l’URL de connexion de Clebex. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).


## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré Clebex, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
