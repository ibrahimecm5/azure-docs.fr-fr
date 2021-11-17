---
title: 'Tutoriel : Intégration de l’authentification unique Azure Active Directory avec Standard for Success Accreditation | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Standard for Success Accreditation.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/18/2021
ms.author: jeedes
ms.openlocfilehash: 37dfb407f1c787f69684de9fe5acc0d46c93fc57
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132320590"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-standard-for-success-accreditation"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory avec Standard for Success Accreditation

Ce tutoriel explique comment intégrer Standard for Success Accreditation avec Azure Active Directory (Azure AD). Quand vous intégrez Standard for Success Accreditation avec Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Standard for Success Accreditation.
* Permettre aux utilisateurs de se connecter automatiquement à Standard for Success Accreditation avec leur compte Azure AD.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Standard for Success Accreditation pour lequel l’authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* Standard for Success Accreditation prend en charge l’authentification unique lancée par **le fournisseur de services et le fournisseur d’identité**.

## <a name="add-standard-for-success-accreditation-from-the-gallery"></a>Ajouter Standard for Success Accreditation à partir de la galerie

Pour configurer l’intégration de Standard for Success Accreditation à Azure AD, vous devez ajouter Standard for Success Accreditation à votre liste d’applications managées SaaS à partir de la galerie.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, dans la zone de recherche, tapez **Standard for Success Accreditation**.
1. Dans le volet de résultats, sélectionnez **Standard for Success Accreditation**, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-standard-for-success-accreditation"></a>Configurer et tester l’authentification unique Azure AD pour Standard for Success Accreditation

Configurez et testez l’authentification unique Azure AD avec Standard for Success Accreditation pour un utilisateur de test nommé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir une relation entre un utilisateur Azure AD et l’utilisateur associé dans Standard for Success Accreditation.

Pour configurer et tester l’authentification unique Azure AD avec Standard for Success Accreditation, vous devez effectuer les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Standard for Success Accreditation](#configure-standard-for-success-accreditation-sso)** pour configurer les paramètres d’authentification unique côté application.
    1. **[Créer un utilisateur de test Standard for Success Accreditation](#create-standard-for-success-accreditation-test-user)** pour avoir dans Standard for Success Accreditation un équivalent de B.Simon lié à la représentation d’utilisateur Azure AD.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, dans la page d’intégration de l’application **Standard for Success Accreditation**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, si vous souhaitez configurer l’application en mode lancé par le **fournisseur d’identité**, effectuez les étapes suivantes :

    a. Dans la zone de texte **Identificateur**, tapez une valeur au format suivant : `api://<ApplicationId>`

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://edu.sfsed.com/access/saml_consume?did=<INSTITUTION-ID>`

1. Cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes si vous souhaitez configurer l’application en mode lancé par le **fournisseur de services** :

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://edu.sfsed.com/access/saml_int?did=<INSTITUTION-ID>`.

    b. Dans la zone de texte **État de relais**, entrez une URL en utilisant le modèle suivant : `https://edu.sfsed.com/access/saml_consume?did=<INSTITUTION-ID>`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL de réponse, l’URL de connexion et l’État de relais exacts. Pour obtenir ces valeurs, contactez l’[équipe de support technique Standard for Success Accreditation](mailto:help_he@standardforsuccess.com) . Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. Dans la section **Certificat de signature SAML**, cliquez sur le bouton **Modifier** pour ouvrir la boîte de dialogue **Certificat de signature SAML**.

    ![Modifier le certificat de signature SAML](common/edit-certificate.png)

1. Dans la section **Certificat de signature SAML**, copiez la **valeur de l’empreinte** et enregistrez-la sur votre ordinateur.

    ![Copier la valeur de l’empreinte](common/copy-thumbprint.png)

1. Dans la section **Configurer Standard for Success Accreditation**, copiez les URL appropriées en fonction de vos besoins.

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

Dans cette section, vous allez créer un utilisateur de test appelé B. Simon dans le portail Azure.

1. Dans le volet gauche du Portail Azure, sélectionnez **Azure Active Directory**, **Utilisateurs**, puis **Tous les utilisateurs**.
1. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.
1. Dans les propriétés **Utilisateur**, effectuez les étapes suivantes :
   1. Dans le champ **Nom**, entrez `B.Simon`.  
   1. Dans le champ **Nom de l’utilisateur**, entrez username@institutiondomain.extension. Par exemple : `B.Simon@contoso.com`.
   1. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.
   1. Cliquez sur **Créer**.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Standard for Success Accreditation.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Standard for Success Accreditation**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-standard-for-success-accreditation-sso"></a>Configurer l’authentification unique Standard for Success Accreditation

1. Ouvrez une nouvelle fenêtre de navigateur web et connectez-vous au site Standard for Success Accreditation en tant qu’administrateur disposant d’un accès de superutilisateur.

1. Dans le menu, cliquez sur **Portail d’administration**.

1. Faites défiler la liste jusqu’à **Paramètres d’authentification unique**, cliquez sur le lien **Authentification unique Microsoft Azure**, puis procédez comme suit.

    :::image type="content" source="./media/standard-for-success-accreditation-tutorial/configuration.png" alt-text="Capture d’écran montrant comment activer l’authentification unique Azure dans Standard for Success Accreditation.":::

    a. Cochez la case **Enable Azure Single Sign On** (Activer l’authentification unique Azure).

    b. Renseignez les champs URL et Identifier (Identificateur) avec les URL appropriées que vous avez copiées à partir de la configuration de SAML dans le portail Azure.

    c. Entrez l’ID de l’application dans la zone de texte **Application ID**.

    d. Dans la zone de texte **Certificate Thumbprint** (Empreinte du certificat), collez la **valeur d’empreinte** que vous avez copiée à partir du portail Azure.

    e. Cliquez sur **Enregistrer**. 

### <a name="create-standard-for-success-accreditation-test-user"></a>Créer un utilisateur de test Standard for Success Accreditation

1.  Connectez-vous à Standard for Success Accreditation en tant qu’administrateur disposant de privilèges de superutilisateur.

1. Dans le menu, cliquez sur **Admin Portal** > **Create New Evaluatee** (Portail d’administration -> Créer une évaluation), puis procédez comme suit.

    ![création d’un utilisateur de test.](./media/standard-for-success-accreditation-tutorial/new-user.png)

    a. Dans la zone de texte **Prénom**, entrez B.

    b. Dans la zone de texte **Nom**, entrez Simon.

    c. Dans la zone de texte **E-mail universitaire**, entrez l’adresse e-mail que vous avez ajoutée pour B.Simon dans Azure.

    d. Faites défiler vers le bas, puis cliquez sur **Créer un utilisateur**.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

#### <a name="sp-initiated"></a>Lancée par le fournisseur de services :

* Cliquez sur **Tester cette application** dans le portail Azure. Cela a pour effet de vous rediriger vers l’URL de connexion à Standard for Success Accreditation où vous pouvez lancer le flux de connexion.  

* Accédez directement à l’URL de connexion à Standard for Success Accreditation et lancez le flux de connexion.

#### <a name="idp-initiated"></a>Lancée par le fournisseur d’identité :

* Sur le portail Azure, cliquez sur **Tester cette application**. Cela a pour effet de vous connecter automatiquement à l’application Standard for Success Accreditation pour laquelle vous avez configuré l’authentification unique 

Vous pouvez aussi utiliser Mes applications de Microsoft pour tester l’application dans n’importe quel mode. Quand vous cliquez sur la vignette Standard for Success Accreditation dans Mes applications, si le mode Fournisseur de services est configuré, vous êtes redirigé vers la page de connexion de l’application pour lancer le flux de connexion, et si le mode Fournisseur d’identité est configuré, vous êtes automatiquement connecté à l’instance de Standard for Success Accreditation pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré Standard for Success Accreditation, vous pouvez appliquer le contrôle de session, qui protège en temps réel contre l’exfiltration et l’infiltration des données sensibles de votre organisation. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
