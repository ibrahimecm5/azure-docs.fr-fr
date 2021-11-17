---
title: 'Tutoriel : Intégration de l’authentification unique Azure Active Directory à Coda | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Coda.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/20/2021
ms.author: jeedes
ms.openlocfilehash: 7b1358880660e7ab78d257556f0096a71f8ecc62
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132305102"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-coda"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory à Coda

Dans ce tutoriel, vous allez apprendre à intégrer Coda à Azure Active Directory (Azure AD). Quand vous intégrez Coda à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Coda.
* Permettre à vos utilisateurs de se connecter automatiquement à Coda avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement CODA pour lequel l’authentification unique est activée et l’intégration GDrive est désactivée. Contactez l’[équipe du support technique de Coda](mailto:support@coda.io) afin de désactiver l’intégration de GDrive pour votre organisation si elle est actuellement activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* Coda prend en charge l’authentification unique lancée par le **fournisseur d’identité**.

* Coda prend en charge l’attribution d’utilisateurs **juste-à-temps**.

* Coda prend en charge le [provisionnement automatisé des utilisateurs](coda-provisioning-tutorial.md).

## <a name="add-coda-from-the-gallery"></a>Ajouter Coda depuis la galerie

Pour configurer l’intégration de Coda à Azure AD, vous devez ajouter Coda depuis la galerie à votre liste d’applications SaaS managées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Coda** dans la zone de recherche.
1. Sélectionnez **Coda** dans le panneau de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-coda"></a>Configurer et tester l’authentification unique Azure AD pour Coda

Configurez et testez l’authentification unique Azure AD avec Coda à l’aide d’un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Coda associé.

Pour configurer et tester l’authentification unique Azure AD avec Coda, procédez comme suit :

1. **[Commencer la configuration de l’authentification unique Coda](#begin-configuration-of-coda-sso)** pour commencer à configurer l’authentification unique dans Coda.
1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
   1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
   1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Coda](#configure-coda-sso)** pour effectuer la configuration des paramètres d’authentification unique dans Coda.
   1. **[Créer un utilisateur de test Coda](#create-coda-test-user)** pour avoir un équivalent de B.Simon dans Coda lié à la représentation Azure AD associée.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="begin-configuration-of-coda-sso"></a>Commencer la configuration de l’authentification unique Coda

Suivez ces étapes dans Coda pour commencer.

1. Dans Coda, ouvrez le panneau **Organization settings** (Paramètres de l’organisation).

   ![Ouvrez les paramètres d’organisation](media/coda-tutorial/settings.png)

1. Vérifiez que l’intégration GDrive est désactivée dans votre organisation. Si elle est activée, contactez l’[équipe du support technique de Coda](mailto:support@coda.io) pour vous aider à migrer hors de GDrive.

   ![GDrive désactivé](media/coda-tutorial/gdrive-off.png)

1. Sous **Authenticate with SSO (SAML)** , (S’authentifier avec l’authentification unique (SAML)), sélectionnez l’option **Configure SAML** (Configurer SAML).

   ![Paramètres SAML](media/coda-tutorial/settings-link.png)

1. Notez les valeurs **Entity ID** (ID d’entité) et **SAML Response URL** (URL de réponse SAML), dont vous aurez besoin lors des étapes suivantes.

   ![ID d’entité et URL de réponse SAML à utiliser dans Azure](media/coda-tutorial/azure-settings.png)

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans la page d’intégration de l’application **Coda** du portail Azure, recherchez la section **Gérer**, puis sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la page **Configurer l’authentification unique avec SAML**, effectuez les étapes suivantes :

   a. Dans la zone de texte **Identificateur**, entrez la valeur « Entity ID » obtenue ci-dessus. Elle doit suivre ce modèle : `https://coda.io/samlId/<CUSTOMID>`

   b. Dans la zone de texte **URL de réponse**, entrez la valeur « SAML Response URL » obtenue ci-dessus. Elle doit suivre ce modèle : `https://coda.io/login/sso/saml/<CUSTOMID>/consume`

   > [!NOTE]
   > Vos valeurs seront différentes de celles ci-dessus ; vous les trouverez dans la console « Configure SAML » de Coda. Mettez à jour ces valeurs avec l’identificateur et l’URL de réponse réels.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **Certificat (Base64)** , puis sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

   ![Lien Téléchargement de certificat](common/certificatebase64.png)

1. Dans la section **Configurer Coda**, copiez l’URL ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Coda.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Coda**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-coda-sso"></a>Configurer l’authentification unique Coda

Pour terminer la configuration, vous allez entrer des valeurs obtenues à partir d’Azure Active Directory dans le panneau **Configure SAML** de Coda.

1. Dans Coda, ouvrez le panneau **Organization settings**.
1. Sous **Authenticate with SSO (SAML)** , sélectionnez l’option **Configure SAML**.
1. Affectez la valeur **Azure Active Directory** à **SAML Provider** (Fournisseur SAML).
1. Dans **Identity Provider Login URL** (URL de connexion au fournisseur d’identité), collez l’**URL de connexion** obtenue à partir de la console Azure.
1. Dans **Identity Provider Issuer** (Émetteur du fournisseur d’identité), collez l’**Identificateur Azure AD** obtenu à partir de la console Azure.
1. Dans **Identity Provider Public Certificate** (Certificat public du fournisseur d’identité), sélectionnez l’option **Upload Certificate** (Charger le certificat) et sélectionnez le fichier de certificat que vous avez téléchargé.
1. Sélectionnez **Enregistrer**.

Cela termine le travail nécessaire pour la configuration de la connexion d’authentification unique SAML.

### <a name="create-coda-test-user"></a>Créer un utilisateur de test Coda

Dans cette section, un utilisateur appelé Britta Simon est créé dans Coda. Coda prend en charge le provisionnement d’utilisateurs juste-à-temps, option activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas encore d’utilisateur dans Coda, il en est créé un après l’authentification.

Coda prend également en charge le provisionnement automatique des utilisateurs. Vous trouverez [ici](./coda-provisioning-tutorial.md) plus d’informations sur la façon de configurer le provisionnement automatique des utilisateurs.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes.

* Cliquez sur Tester cette application dans le portail Azure : vous devez être connecté automatiquement à l’instance de Coda pour laquelle vous avez configuré l’authentification unique.

* Vous pouvez utiliser Mes applications de Microsoft. Quand vous cliquez sur la vignette Coda dans Mes applications, vous devez être connecté automatiquement à l’application Coda pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez configuré Coda, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
