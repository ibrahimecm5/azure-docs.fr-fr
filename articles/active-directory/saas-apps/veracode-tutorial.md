---
title: 'Tutoriel : Intégration de l’authentification unique Azure Active Directory à Veracode | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Veracode.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/22/2021
ms.author: jeedes
ms.openlocfilehash: 2d430c9c09415b3c12abcef9bd213fdd07e3d126
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132320144"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-veracode"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory à Veracode

Dans ce tutoriel, vous allez apprendre à intégrer Veracode à Azure Active Directory (Azure AD). Quand vous intégrez Veracode à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Veracode.
* Permettre à vos utilisateurs de se connecter automatiquement à Veracode avec leur compte Azure AD.
* gérer vos comptes à un emplacement central : le portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Veracode pour lequel l’authentification unique (SSO) est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test. Veracode prend en charge l’authentification unique lancée par le fournisseur d’identité et l’attribution d’utilisateurs juste à temps.

## <a name="add-veracode-from-the-gallery"></a>Ajouter Veracode à partir de la galerie

Pour configurer l’intégration de Veracode à Azure AD, ajoutez Veracode à partir de la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire, ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez « Veracode » dans la zone de recherche.
1. Sélectionnez **Veracode** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-veracode"></a>Configurer et tester l’authentification unique Azure AD pour Veracode

Configurez et testez l’authentification unique Azure AD avec Veracode à l’aide d’un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Veracode associé.

Pour configurer et tester l’authentification unique Azure AD avec Veracode, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    * **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    * **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Veracode](#configure-veracode-sso)** pour configurer les paramètres de l’authentification unique côté application.
    * **[Créer un utilisateur de test Veracode](#create-veracode-test-user)** pour avoir un équivalent de B.Simon dans Veracode lié à la représentation Azure AD associée.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **Veracode**, puis recherchez la section **Gérer**. Sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, sélectionnez l’icône de crayon pour **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, l’application est préconfigurée et les URL nécessaires sont déjà préremplies avec Azure. Sélectionnez **Save** (Enregistrer).

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **Certificat (Base64)** . Sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Capture d’écran de la section Certificat de signature SAML, avec mise en évidence du lien Télécharger](common/certificatebase64.png)

1. Veracode s’attend à recevoir les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à votre configuration d’attributs de jetons SAML. La capture d’écran suivante montre la liste des attributs par défaut.

    ![Capture d’écran de la section Attributs et revendications de l’utilisateur](common/default-attributes.png)

1. Veracode s’attend également à ce que quelques attributs supplémentaires soient passés dans la réponse SAML. Ces attributs sont également préremplis, mais vous pouvez les examiner pour voir s’ils répondent à vos besoins.

    | Nom | Attribut source|
    | ---------------| --------------- |
    | firstname |User.givenname |
    | lastname |User.Surname |
    | email |User.mail |

1. Dans la section **Configurer Veracode**, copiez la ou les URL nécessaires en fonction de vos besoins.

    ![Capture d’écran de la section Configurer Veracode, avec mise en évidence des URL de configuration](common/copy-configuration-urls.png)

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Veracode.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Veracode**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-veracode-sso"></a>Configurer l’authentification unique Veracode

1. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Veracode en tant qu’administrateur.

1. Dans le menu situé en haut, sélectionnez **Settings** (Paramètres) > **Admin**.
   
    ![Capture d’écran de l’administration de Veracode, avec mise en évidence de l’icône Settings et d’Admin](./media/veracode-tutorial/admin.png "Administration")

1. Sélectionnez l’onglet **SAML**.

1. Dans la section **Organization SAML Settings** , procédez comme suit :

    ![Capture d’écran de la section Organization SAML Settings (Paramètres SAML de l’organisation)](./media/veracode-tutorial/saml.png "Administration")

    a.  Dans la zone **Issuer** (Émetteur), collez la valeur de l’**Identificateur Azure AD** copiée à partir du portail Azure.

    b. Pour **Assertion Signing Certificate** (Certificat de signature d’assertion), sélectionnez **Choose File** (Choisir un fichier) pour charger votre certificat téléchargé à partir du portail Azure.

    c. Pour **Self Registration** (Inscription automatique), sélectionnez **Enable Self Registration** (Activer l’inscription automatique).

1. Dans la section **Self Registration Settings** (Paramètre de l’inscription automatique), effectuez les étapes suivantes, puis sélectionnez **Save** (Enregistrer) :

    ![Capture d’écran de la section Self Registration Settings, avec mise en évidence de différentes options](./media/veracode-tutorial/save.png "Administration")

    a. Pour **New User Activation** (Activation du nouvel utilisateur), sélectionnez **No Activation Required** (Aucune activation requise).

    b. Dans **User Data Updates** (Mises à jour des données utilisateur), sélectionnez **Preference Veracode User Data** (Données utilisateur Veracode de préférence).

    c. Dans **SAML Attribute Details**, sélectionnez les éléments suivants :
      * **Rôles d'utilisateur**
      * **Administrateur de la stratégie**
      * **Réviseur**
      * **Responsable de la sécurité**
      * **Executive**
      * **Expéditeur**
      * **Creator**
      * **Tous les types d’analyse**
      * **Appartenance aux équipes**
      * **Équipe par défaut**

### <a name="create-veracode-test-user"></a>Créer un utilisateur de test Veracode

Dans cette section, un utilisateur appelé B.Simon est créé dans Veracode. Veracode prend en charge le provisionnement d’utilisateurs juste-à-temps, option activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas encore d’utilisateur dans Veracode, il en est créé un après l’authentification.

> [!NOTE]
> Vous pouvez utiliser n’importe quel outil ou API de création de compte d’utilisateur, fourni par Veracode, pour approvisionner des comptes d’utilisateur Azure AD.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes.

* Cliquez sur Tester cette application dans le portail Azure ; vous êtes alors connecté automatiquement à l’instance de Veracode pour laquelle vous avez configuré l’authentification unique.

* Vous pouvez utiliser Mes applications de Microsoft. Quand vous cliquez sur la vignette Veracode dans Mes applications, vous devez être connecté automatiquement à l’application Veracode pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez configuré Veracode, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
