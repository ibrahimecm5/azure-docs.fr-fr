---
title: 'Didacticiel : Intégration d’Azure Active Directory avec Clear Review | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Clear Review.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/19/2021
ms.author: jeedes
ms.openlocfilehash: 190558bcf528ff45d16a215f4cdd4b6bd1a37963
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132305158"
---
# <a name="tutorial-azure-active-directory-integration-with-clear-review"></a>Didacticiel : Intégration d’Azure Active Directory avec Clear Review

Dans ce tutoriel, vous allez apprendre à intégrer Clear Review à Azure Active Directory (Azure AD). Lorsque vous intégrez Clear Review à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Clear Review.
* Permettre aux utilisateurs de se connecter automatiquement à Clear Review avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à Clear Review, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un [compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Clear Review pour lequel l'authentification unique est activée.

> [!NOTE]
> Cette intégration peut également être utilisée à partir de l’environnement cloud US Government Azure AD. Cette application est disponible dans la Galerie d’applications cloud US Government Azure AD et peut être configurée de la même façon que dans le cloud public.

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Clear Review prend en charge l'authentification unique lancée par **le fournisseur de services et le fournisseur d'identité**.

## <a name="add-clear-review-from-the-gallery"></a>Ajouter Clear Review à partir de la galerie

Pour configurer l’intégration de Clear Review à Azure AD, vous devez ajouter Clear Review depuis la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, entrez **Clear Review** dans la zone de recherche.
1. Sélectionnez **Clear Review** dans le volet de résultats, puis ajoutez l'application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-clear-review"></a>Configurer et tester l'authentification unique Azure AD pour Clear Review

Configurez et testez l'authentification unique Azure AD auprès de Clear Review à l'aide d'un utilisateur de test appelé **B.Simon**. Pour que l'authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l'utilisateur associé dans Clear Review.

Pour configurer et tester l'authentification unique Azure AD auprès de Clear Review, suivez les étapes ci-dessous :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l'authentification unique Clear Review](#configure-clear-review-sso)** pour configurer les paramètres de l'authentification unique côté application.
    1. **[Créer un utilisateur de test Clear Review](#create-clear-review-test-user)** pour avoir, dans Clear Review, un équivalent de B.Simon lié à la représentation Azure AD associée.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Sur la page d’intégration de l'application **Clear Review** du portail Azure, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, si vous souhaitez configurer l’application en mode lancé par le **fournisseur d’identité**, effectuez les étapes suivantes :

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<CUSTOMER_NAME>.clearreview.com/sso/metadata/`

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://<CUSTOMER_NAME>.clearreview.com/sso/acs/`

5. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<CUSTOMER_NAME>.clearreview.com`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL de réponse et l’URL de connexion réels. Pour obtenir ces valeurs, contactez l’[équipe de support client Clear Review](https://clearreview.com/contact/). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

6. L’application Clear Review attend les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à votre configuration d’attributs de jeton SAML. La capture d’écran suivante montre la liste des attributs par défaut, où **nameidentifier** est mappé avec **user.userprincipalname**. L’application Clear Review s’attend à ce que **nameidentifier** soit mappé avec **user.mail**. Vous devez donc modifier le mappage d’attribut en cliquant sur l’icône **Modifier** et modifier le mappage d’attribut.

    ![Capture d’écran montrant User Attributes avec l’icône Edit sélectionnée.](common/edit-attribute.png)

7. Dans la boîte de dialogue **Attributs et revendications de l’utilisateur** , effectuez les étapes suivantes :

    a. Cliquez sur l’**icône de modification** à droite de **Valeur de nom d’identificateur**.

    ![Capture d’écran montrant User Attributes & Claims avec l’icône Edit sélectionnée.](./media/clearreview-tutorial/attribute-2.png)

    ![Capture d’écran montrant la boîte de dialogue Manage user claims où vous pouvez entrer les valeurs décrites.](./media/clearreview-tutorial/attribute-1.png)

    b. Dans la liste **Attribut de la source**, sélectionnez la valeur d’attribut **user.givenname** pour cette ligne.

    c. Cliquez sur **Enregistrer**.

8. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (Base64)** en fonction des options définies par rapport à vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

9. Dans la section **Configurer Clear Review**, copiez la ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez permettre à B.Simon d'utiliser l'authentification unique Azure en lui accordant l'accès à Clear Review.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Clear Review**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-clear-review-sso"></a>Configurer l'authentification unique pour Clear Review

1. Pour configurer l’authentification unique du côté de **Clear Review**, ouvrez le portail **Clear Review** avec des informations d’identification d’administrateur.

2. Sélectionnez **Admin** dans le volet de navigation de gauche.

    ![Capture d’écran montrant le site web Clear Review avec Admin sélectionné.](./media/clearreview-tutorial/admin.png)

3. Dans la section **Intégrations** au bas de la page, cliquez sur le bouton **Change** (Changer) à droite de **Single Sign-On Settings** (Paramètres de l’authentification unique).

    ![Capture d’écran montrant le bouton Single Sign-On Change.](./media/clearreview-tutorial/integrations.png)

4. Procédez comme suit sur la page **Paramètres de l'authentification unique**.

    ![Capture d’écran montrant la page Single Sign-on Settings où vous pouvez entrer les informations de cette étape.](./media/clearreview-tutorial/settings.png)

    a. Dans la zone de texte **URL de l’émetteur**, collez la valeur de l’**identifiant Azure AD** que vous avez copiée à partir du portail Azure.

    b. Dans la zone de texte **SAML Endpoint** (Point de terminaison SAML), collez la valeur **URL de connexion** que vous avez copiée à partir du portail Azure.  

    c. Dans la zone de texte **SLO Endpoint** (Point de terminaison SLO), collez la valeur de l’**URL de déconnexion** que vous avez copiée à partir du portail Azure.  

    d. Ouvrez le certificat que vous avez téléchargé dans le Bloc-notes, puis collez son contenu dans la zone de texte **Certificat X.509**.   

    e. Cliquez sur **Enregistrer**.

### <a name="create-clear-review-test-user"></a>Créer un utilisateur de test Clear Review

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans Clear Review. Collaborez avec [l’équipe de support Clear Review](https://clearreview.com/contact/) pour ajouter des utilisateurs dans la plateforme Clear Review.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

#### <a name="sp-initiated"></a>Lancée par le fournisseur de services :

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l'URL de connexion à Clear Review, à partir de laquelle vous pouvez lancer le flux de connexion.  

* Accédez directement à l'URL de connexion à Clear Review pour lancer le flux de connexion.

#### <a name="idp-initiated"></a>Lancée par le fournisseur d’identité :

* Cliquez sur **Tester cette application** sur le portail Azure. Vous êtes alors automatiquement connecté à l'instance de Clear Review pour laquelle vous avez configuré l'authentification unique. 

Vous pouvez aussi utiliser Mes applications de Microsoft pour tester l’application dans n’importe quel mode. Si, lorsque vous cliquez sur la vignette Clear Review dans Mes applications, le mode Fournisseur de services est configuré, vous êtes redirigé vers la page de connexion de l'application pour lancer le flux de connexion ; s'il s'agit du mode Fournisseur d'identité, vous êtes automatiquement connecté à l'instance de Clear Review pour laquelle vous avez configuré l'authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré Clear Review, vous pouvez appliquer le contrôle de session, qui protège en temps réel contre l'exfiltration et l'infiltration des données sensibles de votre organisation. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
