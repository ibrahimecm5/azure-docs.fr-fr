---
title: 'Tutoriel : Intégration de l’authentification unique Azure Active Directory avec Resource Central – SAML SSO for Meeting Room Booking System | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Resource Central – SAML SSO for Meeting Room Booking System.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/07/2021
ms.author: jeedes
ms.openlocfilehash: f77a6d0088d9ee45c1f39a46abbb6f88806b3e57
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132329464"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-resource-central--saml-sso-for-meeting-room-booking-system"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory avec Resource Central – SAML SSO for Meeting Room Booking System

Dans ce tutoriel, vous allez apprendre à intégrer Resource Central – SAML SSO for Meeting Room Booking System à Azure Active Directory (Azure AD). Suand vous intégrez Resource Central – SAML SSO for Meeting Room Booking System à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Resource Central – SAML SSO for Meeting Room Booking System.
* Autoriser vos utilisateurs à se connecter automatiquement à Resource Central – SAML SSO for Meeting Room Booking System avec leurs comptes de Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Abonnement avec authentification unique à Resource Central – SAML SSO for Meeting Room Booking System.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* Resource Central – SAML SSO for Meeting Room Booking System prend en charge l’authentification unique initiée par le **fournisseur de services**

* Resource Central – SAML SSO for Meeting Room Booking System prend en charge le provisionnement des utilisateurs **juste-à-temps**.

## <a name="add-resource-central--saml-sso-for-meeting-room-booking-system-from-the-gallery"></a>Ajouter Resource Central – SAML SSO for Meeting Room Booking System depuis la galerie

Pour configurer l’intégration de Resource Central – SAML SSO for Meeting Room Booking System à Azure AD, vous devez ajouter Resource Central – SAML SSO for Meeting Room Booking System à partir de la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, dans la zone de recherche, entrez **Resource Central – SAML SSO for Meeting Room Booking System**.
1. Sélectionnez **Resource Central – SAML SSO for Meeting Room Booking System** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-resource-central--saml-sso-for-meeting-room-booking-system"></a>Configurer et tester Resource Central – SAML SSO for Meeting Room Booking System

Configurez et testez l’authentification unique Azure AD avec Resource Central – SAML SSO for Meeting Room Booking System avec un utilisateur appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir une relation entre un utilisateur Azure AD et l’utilisateur Resource Central associé – SAML SSO for Meeting Room Booking System.

Pour configurer et tester Resource Central – SAML SSO for Meeting Room Booking System, procédez comme suit :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
    1. **[Créer un utilisateur de test Resource Central – SAML SSO for Meeting Room Booking System](#create-resource-central-saml-sso-for-meeting-room-booking-system-test-user)** pour avoir un équivalent de B.Simon dans Resource Central – SAML SSO for Meeting Room Booking System lié à la représentation Azure AD de l’utilisateur.
1. **[Configurer l’authentification unique Resource Central SAML SSO for Meeting Room Booking System](#configure-resource-central-saml-sso-for-meeting-room-booking-system-sso)** pour configurer les paramètres d’authentification unique du côté application.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, sur la page d’intégration de l’application **Resource Central – SAML SSO for Meeting Room Booking System**, trouvez la section **Gérer** et sélectionnez **authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de modification/stylet de **Configuration SAML de base** pour modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans **Configuration SAML de base**, entrez les valeurs pour les champs suivants :

   1. Dans la zone de texte **URL de connexion**, saisissez une URL au format suivant : `https://<DOMAIN_NAME>/ResourceCentral`

   1. Dans la zone de texte **Identificateur (ID d’entité)** , tapez une URL au format suivant :  `https://<DOMAIN_NAME>/ResourceCentral`

   1. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://<DOMAIN_NAME>/ResourceCentral/ExAuth/Saml2Authentication/Acs`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Vous devez les mettre à jour avec les valeurs réelles d’URL de connexion, d’identificateur et d’URL de réponse. Contactez [le support technique Resource Central – SAML SSO for Meeting Room Booking System](mailto:st@aod.vn) pour obtenir ces valeurs.  Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans **Certificat de signature SAML**, recherchez **Certificat (Base64)** et sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

1. Dans **Configurer Resource Central – SAML SSO for Meeting Room Booking System**, copiez les URL appropriées selon vos exigences.

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

Dans cette section, vous allez créer un utilisateur de test appelé B.Simon dans le portail Azure.

1. Dans le volet gauche du Portail Azure, sélectionnez **Azure Active Directory**, **Utilisateurs**, puis **Tous les utilisateurs**.
1. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.
1. Dans les propriétés **Utilisateur**, effectuez les étapes suivantes :
   1. Dans le champ **Nom**, entrez `B.Simon`.  
   1. Dans le champ **Nom de l’utilisateur**, entrez `username@companydomain.extension`. Par exemple : `B.Simon@contoso.com`.
   1. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.
   1. Cliquez sur **Créer**.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en accordant l’accès à Resource Central – SAML SSO for Meeting Room Booking System.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste d’applications, sélectionnez **Resource Central – SAML SSO for Meeting Room Booking System**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans le volet **Ajouter une attribution**.
1. Dans le volet **Utilisateurs et groupes**, sélectionnez **B.Simon** dans la liste **Utilisateurs**, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous vous attendez à ce qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans **Sélectionner un rôle**. Si aucun rôle n’a été configuré pour cette application, le rôle **Accès par défaut** est sélectionné.
1. Dans le volet **Ajouter une affectation**, cliquez sur le bouton **Affecter**.

### <a name="create-resource-central-saml-sso-for-meeting-room-booking-system-test-user"></a>Créer un utilisateur de test Resource Central SAML SSO for Meeting Room Booking System

Dans cette section, un utilisateur appelé **B.Simon** est créé dans **Resource Central – SAML SSO for Meeting Room Booking System**.

1. Dans Resource Central – SAML SSO for Meeting Room Booking System, sélectionnez **Sécurité** > **Personnes** > **Nouveau**.
  
    :::image type="content" source="./media/resource-central/new-person.png" alt-text="Capture d’écran qui montre le volet Persons dans Resource central, avec le bouton New mis en évidence.":::

1. Dans **Person Details**, pour **Display name**, entrez l’utilisateur **B.Simon**. Pour **SMTP Address**, entrez le nom d’utilisateur Azure AD de l’utilisateur. Par exemple : `B.Simon@contoso.com`.

    :::image type="content" source="./media/resource-central/person.png" alt-text="Capture d’écran montrant le volet Person Details dans Resource central.":::

## <a name="configure-resource-central-saml-sso-for-meeting-room-booking-system-sso"></a>Configurer l’authentification unique pour Resource Central SAML SSO for Meeting Room Booking System

Dans cette section, vous allez configurer l’authentification unique dans **Resource Central System Administrator**.

1. Dans Resource Central – SAML SSO for Meeting Room Booking System, Administrateur système, sélectionnez **Authentification externe**.
1.  Pour **Enable Configuration**, sélectionnez **Yes**.

    ![Capture d’écran qui montre l’option Enable Configuration sélectionnée dans le volet External Authentication dans Resource Central SAML SSO for Meeting Room Booking System.](./media/resource-central/enable.png)

1. Dans **Authentication Protocol**, sélectionnez **SAML2**. 

   :::image type="content" source="./media/resource-central/protocol.png" alt-text="Capture d’écran qui montre l’option SAML2 sélectionnée pour Authentication Protocol dans Resource central.":::

1. Sous **SAML2 Configuration**, entrez les valeurs pour les champs suivants :

    1. Pour **Identifier (Entity ID)** , **Login URL**, **Logout URL** et **Azure AD Identifier**, entrez les URL appropriées :

       :::image type="content" source="./media/resource-central/auth.png" alt-text="Capture d’écran du volet SAML2 Configuration dans Resource Central.":::

        Copiez les URL du volet **Configurer Resource Central – SAML SSO for Meeting Room Booking System** :

        :::image type="content" source="./media/resource-central/setup.png" alt-text="Capture d’écran du volet Set up Resource Central dans Resource Central.":::

   1. Pour **Return URL**, entrez `https://<DOMAIN_NAME>/ResourceCentral/ExAuth/Saml2Authentication/CallbackHandler`.
  
1. Pour **Certificate**, chargez votre certificat et entrez votre mot de passe.

   ![Capture d’écran de la section Certificat dans Resource Central – SAML SSO for Meeting Room Booking System.](./media/resource-central/cert.png)
   
1. Sélectionnez **Enregistrer**.

1. Revenez au **portail Azure**. Dans **Certificat de signature SAML**, chargez votre certificat et entrez votre mot de passe.

   ![Capture d’écran du volet Importer un certificat dans le portail Azure.](./media/resource-central/cert2.png).

1. Sélectionnez **Ajouter**.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD. Pour tester l’authentification unique, vous avez trois options :

* Dans le portail Azure, sélectionnez **Tester cette application**. Le lien redirige vers la ressource Resource Central – SAML SSO for Meeting Room Booking System, où vous pouvez initialiser la connexion.

* Accédez à l’URL de connexion Resource Central – SAML SSO for Meeting Room Booking System directement et lancez la connexion.

   :::image type="content" source="./media/resource-central/test.png" alt-text="Capture d’écran de la page web de test de l’authentification unique Resource Central":::

* Utilisez le portail Mes applications de Microsoft. Dans le portail Mes applications, sélectionnez la vignette **Resource Central – SAML SSO for Meeting Room Booking System** pour accéder à l’URL de connexion de Resource Central – SAML SSO for Meeting Room Booking System. Pour plus d’informations, consultez [Se connecter et démarrer des applications à partir du portail Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré Resource Central SAML SSO for Meeting Room Booking System pour l’authentification unique avec Azure AD, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
