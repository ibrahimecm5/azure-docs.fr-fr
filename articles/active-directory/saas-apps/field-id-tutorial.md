---
title: 'Tutoriel : Intégration de l’authentification unique Azure AD à Field iD'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Field iD.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/07/2021
ms.author: jeedes
ms.openlocfilehash: ea79e2dfe4a66fe7fe05a29ccf20db7c99a33206
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132337949"
---
# <a name="tutorial-azure-ad-sso-integration-with-field-id"></a>Tutoriel : Intégration de l’authentification unique Azure AD à Field iD

Dans ce tutoriel, vous allez découvrir comment intégrer Field iD à Azure AD (Azure Active Directory). Quand vous intégrez Field iD à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Field iD.
* Permettre à vos utilisateurs de se connecter automatiquement à Field iD avec leur compte Azure AD.
* gérer vos comptes à un emplacement central : le portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Field iD pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* Field iD prend en charge l’authentification unique lancée par le fournisseur d’identité.

## <a name="add-field-id-from-the-gallery"></a>Ajouter Field iD à partir de la galerie

Pour configurer l’intégration de Field iD à Azure AD, vous devez ajouter Field iD, disponible dans la galerie, à votre liste d’applications SaaS managées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire, ou avec un compte personnel Microsoft.
1. Dans le volet de navigation de gauche, sélectionnez **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter la nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Field iD** dans la zone de recherche.
1. Sélectionnez **Field iD** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-field-id"></a>Configurer et tester l’authentification unique Azure AD pour Field iD

Configurez et testez l’authentification unique Azure AD avec Field iD à l’aide d’un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Field iD associé.

Pour configurer et tester l’authentification unique Azure AD avec Field iD, effectuez les étapes suivantes :

1. [Configurer l’authentification unique Azure AD](#configure-azure-ad-sso) pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
   1. [Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user) pour tester l’authentification unique Azure AD avec B.Simon.
   1. [Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user) pour permettre à B.Simon d’utiliser l’authentification unique Azure AD.
1. [Configurer l’authentification unique Field iD](#configure-field-id-sso) pour configurer les paramètres d’authentification unique côté application.
   1. [Créer un utilisateur de test Field iD](#create-a-field-id-test-user) pour avoir un équivalent de B.Simon dans Field iD lié à la représentation Azure AD associée.
1. [Tester l’authentification unique](#test-sso) pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **Field iD** et recherchez la section **Gérer**. Ensuite, sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, sélectionnez l’icône de crayon pour **Configuration SAML de base** afin de modifier les paramètres.

   ![Capture d’écran de la page Configurer l’authentification unique avec SAML avec l’icône de crayon mise en évidence](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

   a. Dans la zone de texte **Identificateur**, saisissez une URL au format suivant : `https://<tenantname>.fieldid.com/fieldid`

   b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://<tenantname>.fieldid.com/fieldid/saml/SSO/alias/<Tenant Name>`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur et l’URL de réponse réels. Pour obtenir ces valeurs, contactez l’[équipe de support technique Field iD](mailto:support@ecompliance.com). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, sélectionnez l’icône de copie pour copier l’**URL des métadonnées de fédération d’application**. Enregistrez-le sur votre ordinateur.

    ![Capture d’écran du certificat de signature SAML, avec l’icône de copie mise en évidence](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

Dans cette section, vous allez créer un utilisateur de test appelé B. Simon dans le portail Azure.

1. Dans le volet gauche du portail Azure, sélectionnez **Azure Active Directory** > **Utilisateurs** > **Tous les utilisateurs**.
1. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.
1. Dans les propriétés **Utilisateur**, effectuez les étapes suivantes :
   1. Pour **Nom**, entrez `B.Simon`.  
   1. Pour **Nom d’utilisateur**, entrez le username@companydomain.extension (par exemple, `B.Simon@contoso.com`).
   1. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans la zone **Mot de passe**.
   1. Sélectionnez **Create** (Créer).

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Field iD.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise** > **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Field iD**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B.Simon** dans la liste **Utilisateurs**, puis choisissez **Sélectionner** en bas de l’écran.
1. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste. Choisissez **Sélectionner** au bas de l’écran.
1. Dans la boîte de dialogue **Ajouter une attribution**, sélectionnez **Affecter**.

## <a name="configure-field-id-sso"></a>Configurer l’authentification unique Field iD

Pour configurer l’authentification unique côté Field iD, envoyez l’**URL des métadonnées de fédération d’application** à l’[équipe de support technique Field iD](mailto:support@ecompliance.com). L’équipe vérifiera que l’authentification unique SAML est correctement configurée des deux côtés.

### <a name="create-a-field-id-test-user"></a>Créer un utilisateur de test Field iD

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans Field iD. Collaborez avec l’[équipe de support technique Field iD](mailto:support@ecompliance.com) pour ajouter les utilisateurs à la plateforme Field iD. Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes.

* Cliquez sur Tester cette application dans le portail Azure, ce qui devrait vous connecter automatiquement à l’application Field iD pour laquelle vous avez configuré l’authentification unique.

* Vous pouvez utiliser Mes applications de Microsoft. Quand vous cliquez sur la vignette Field iD dans Mes applications, vous devriez être connecté automatiquement à l’application Field iD pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré Field iD, vous pouvez appliquer le contrôle de session, qui protège l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
