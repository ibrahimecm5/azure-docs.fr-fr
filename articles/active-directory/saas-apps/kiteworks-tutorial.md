---
title: 'Tutoriel : Intégration d’Azure Active Directory à Kiteworks | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Kiteworks.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/12/2021
ms.author: jeedes
ms.openlocfilehash: 146831c17f48d06d0df49cc00a4176133bfea5da
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132346273"
---
# <a name="tutorial-integrate-kiteworks-with-azure-active-directory"></a>Tutoriel : Intégrer Kiteworks à Azure Active Directory

Dans ce tutoriel, vous allez apprendre à intégrer Kiteworks à Azure Active Directory (Azure AD). Quand vous intégrez Kiteworks à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Kiteworks.
* Permettre à vos utilisateurs de se connecter automatiquement à Kiteworks avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Kiteworks pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* Kiteworks prend en charge l’authentification unique initiée par le **fournisseur de services**.
* Kiteworks prend en charge l’attribution d’utilisateurs **Juste-à-temps**.

## <a name="add-kiteworks-from-the-gallery"></a>Ajouter Kiteworks à partir de la galerie

Pour configurer l’intégration de Kiteworks avec Azure AD, vous devez ajouter Kiteworks à partir de la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Kiteworks** dans la zone de recherche.
1. Sélectionnez **Kiteworks** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-kiteworks"></a>Configurer et tester Azure AD SSO pour Kiteworks

Configurez et testez l’authentification unique Azure AD avec Kiteworks pour un utilisateur de test nommé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur associé dans Kiteworks.

Pour configurer et tester Azure AD SSO pour Kiteworks, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Kiteworks](#configure-kiteworks-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Création d’un utilisateur de test Kiteworks](#create-kiteworks-test-user)** pour avoir un équivalent de B.Simon dans Kiteworks lié à la représentation Azure AD associée.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **Kiteworks**, recherchez la section **Gérer**, puis sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon pour **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    a. Dans la zone de texte **URL de connexion**, saisissez une URL au format suivant : `https://<kiteworksURL>.kiteworks.com`

    b. Dans la zone de texte **Identificateur (ID d’entité)** , saisissez une URL au format suivant : `https://<kiteworksURL>/sp/module.php/saml/sp/saml2-acs.php/sp-sso`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Pour obtenir ces valeurs, contactez l’[équipe de support technique de Kiteworks](https://accellion.com/support). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **Certificat (Base64)** , puis sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

1. Dans la section **Configurer Kiteworks**, copiez l’URL ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui octroyant l’accès à Kiteworks.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Kiteworks**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-kiteworks-sso"></a>Configurer l’authentification unique Kiteworks

1. Connectez-vous à votre site d’entreprise Kiteworks en tant qu’administrateur.

1. Dans la barre d’outils située en haut, cliquez sur **Settings**.

    ![Capture d’écran montrant l’icône « Settings » sélectionnée dans la barre d’outils.](./media/kiteworks-tutorial/settings.png)

1. Dans la section **Authentification et autorisation**, cliquez sur **SSO Setup** (Configuration de l’authentification unique).

    ![Capture d’écran montrant l’élément « SSO Setup » sélectionné dans la section « Authentication and Authorization ».](./media/kiteworks-tutorial/authentication.png)

1. Sur la page de configuration de l’authentification unique, procédez comme suit :

    ![Configure Single Sign-On](./media/kiteworks-tutorial/setup-page.png)

    a. Sélectionnez **Authenticate via SSO**.

    b. Sélectionnez **Initiate AuthnRequest**.

    c. Dans la zone de texte **IDP Entity ID**, collez l’**Identificateur Azure AD** que vous avez copié dans le portail Azure.

    d. Dans la zone de texte **Single Sign-On Service URL** (URL du service d’authentification unique), collez la valeur de l’**URL de connexion** que vous avez copiée à partir du portail Azure.

    e. Dans la zone de texte **Single Logout Service URL** (URL du service de déconnexion unique), collez la valeur de l’**URL de connexion** que vous avez copiée à partir du portail Azure.

    f. Ouvrez le certificat que vous avez téléchargé dans le Bloc-notes, copiez son contenu, puis collez-le dans la zone de texte **RSA Public Key Certificate** .

    g. Cliquez sur **Enregistrer**.

### <a name="create-kiteworks-test-user"></a>Créer un utilisateur de test Kiteworks

Dans cette section, un utilisateur nommé B.Simon est créé dans Kiteworks. Kiteworks prend en charge le provisionnement d’utilisateurs juste-à-temps, qui est activé par défaut. Vous n’avez aucune opération à effectuer dans cette section. Si l’utilisateur n’existe pas déjà dans Kiteworks, un nouveau est créé après l’authentification.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion Kiteworks où vous pouvez initier le flux de connexion. 

* Accédez directement à l’URL de connexion à Kiteworks pour lancer le flux de connexion.

* Vous pouvez utiliser Mes applications de Microsoft. Un clic sur la vignette Kiteworks dans Mes applications vous redirige vers l’URL de connexion à Kiteworks. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré Kiteworks, vous pouvez appliquer le contrôle de session, qui protège en temps réel contre l’exfiltration et l’infiltration de données sensibles de votre organisation. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
