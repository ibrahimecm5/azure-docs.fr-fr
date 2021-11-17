---
title: 'Tutoriel : Intégration d’Azure Active Directory à EverBridge | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Everbridge.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/10/2021
ms.author: jeedes
ms.openlocfilehash: c0fecf962a71aa05a2d0dccb4c5ba474cf287041
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132330154"
---
# <a name="tutorial-azure-active-directory-integration-with-everbridge"></a>Tutoriel : Intégration d’Azure Active Directory à EverBridge

Dans ce didacticiel, vous allez apprendre à intégrer EverBridge à Azure Active Directory (Azure AD).
Lorsque vous intégrez EverBridge à Azure AD, vous pouvez :

* Contrôler qui a accès à EverBridge dans Azure AD.
* Permettre aux utilisateurs de se connecter automatiquement à EverBridge avec leur compte Azure AD. Ce contrôle d’accès porte le nom d’authentification unique (SSO).
* Gérer vos comptes à partir d’un emplacement centralisé à l’aide du portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à Everbridge, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un [compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement EverBridge qui utilise l’authentification unique.

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* EverBridge prend en charge l’authentification unique lancée par le fournisseur d’identité.

## <a name="add-everbridge-from-the-gallery"></a>Ajouter Everbridge à partir de la galerie

Pour configurer l’intégration d’Everbridge à Azure AD, vous devez ajouter Everbridge à partir de la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, dans la zone de recherche, tapez **Everbridge**.
1. Sélectionnez **Everbridge** dans le panneau Résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-everbridge"></a>Configurer et tester l’authentification SSO Azure AD pour Everbridge

Configurez et testez l’authentification SSO Azure AD avec Everbridge à l’aide d’une utilisatrice de test appelée **B.Simon**. Pour que l’authentification SSO fonctionne, vous devez établir une relation entre un utilisateur Azure AD et l’utilisateur associé dans Everbridge.

Pour configurer et tester l’authentification SSO Azure AD avec Everbridge, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification SSO pour Everbridge](#configure-everbridge-sso)** afin de configurer les paramètres d’authentification unique côté application.
    1. **[Créer une utilisatrice de test pour Everbridge](#create-everbridge-test-user)** afin de disposer dans Everbridge d’un équivalent de B.Simon lié à la représentation Azure AD de l’utilisatrice.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, dans la page d’intégration de l’application **Everbridge**, recherchez la section **Gérer**, puis sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon pour **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

    >[!NOTE]
    >Configurez l’application en tant que portail de manager *ou* en tant que portail de membre à la fois sur le portail Azure et sur le portail EverBridge.

4. Pour configurer l’application **EverBridge** en tant que **portail de manager EverBridge**, dans la section **Configuration SAML de base**, procédez comme suit :

    a. Dans la zone **Identificateur**, entrez une URL en suivant le modèle.
    `https://sso.everbridge.net/<API_Name>`

    b. Dans la zone **URL de réponse**, entrez une URL en suivant le modèle.
    `https://manager.everbridge.net/saml/SSO/<API_Name>/alias/defaultAlias`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur et l’URL de réponse réels. Pour obtenir ces valeurs, contactez [l’équipe de support technique EverBridge](mailto:support@everbridge.com). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

5. Pour configurer l’application **EverBridge** en tant que **portail de membre EverBridge**, dans la section **Configuration SAML de base**, procédez comme suit :

  * Si vous voulez configurer l’application en mode lancé par le fournisseur d’identité, procédez comme suit :

    a. Dans la zone **Identificateur**, entrez une URL en suivant le modèle `https://sso.everbridge.net/<API_Name>/<Organization_ID>`

    b. Dans la zone **URL de réponse**, entrez une URL en suivant le modèle `https://member.everbridge.net/saml/SSO/<API_Name>/<Organization_ID>/alias/defaultAlias`

   * Si vous voulez configurer l’application en mode lancé par le fournisseur de services, sélectionnez **Définir des URL supplémentaires**, puis procédez comme suit :

     a. Dans la zone **URL de connexion**, entrez une URL en suivant le modèle `https://member.everbridge.net/saml/login/<API_Name>/<Organization_ID>/alias/defaultAlias?disco=true`

     > [!NOTE]
     > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL d’authentification, l’URL de réponse et l’URL de connexion réels. Pour obtenir ces valeurs, contactez [l’équipe de support technique EverBridge](mailto:support@everbridge.com). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

6. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, sélectionnez **Télécharger** pour télécharger le fichier **XML de métadonnées de fédération**. Enregistrez-le sur votre ordinateur.

    ![Lien de téléchargement du certificat](common/metadataxml.png)

7. Dans la section **Configurer EverBridge**, copiez les URL dont vous avez besoin :

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

Dans cette section, vous allez permettre à B.Simon d’utiliser l’authentification unique Azure en lui octroyant l’accès à Everbridge.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Everbridge**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-everbridge-sso"></a>Configurer l’authentification SSO pour Everbridge

Pour configurer l’authentification unique sur **EverBridge** comme une application de **portail de manager EverBridge**, procédez comme suit.
 
1. Dans une autre fenêtre de navigateur web, connectez-vous à EverBridge en tant qu’administrateur.

1. Dans le menu du haut, cliquez sur l’onglet **Paramètres**. Sous **Sécurité**, sélectionnez **Authentification unique pour le portail des gestionnaires**.
   
     ![Configurer l’authentification unique](./media/everbridge-tutorial/settings.png)
   
     a. Dans la zone **Nom**, entrez le nom du fournisseur d’identité. Exemple : le nom de votre société.
   
     b. Dans la zone **Nom de l’API**, entrez le nom de l’API.
   
     c. Sélectionnez **Choisir un fichier** pour charger le fichier de métadonnées que vous avez téléchargé sur le Portail Azure.
   
     d. Pour **Emplacement de l’identité SAML**, sélectionnez **L’identité est dans l’élément NameIdentifier de l’instruction Subject**.
   
     e. Dans la zone **URL de connexion du fournisseur identité**, collez la valeur de l’**URL de connexion** que vous avez copiée à partir du portail Azure.
   
     f. Pour **Liaison de demande initiée par le fournisseur de service**, sélectionnez **Redirection HTTP**.

     g. Sélectionnez **Enregistrer**.

## <a name="configure-everbridge-as-everbridge-member-portal-sso"></a>Configurer l’authentification SSO sur Everbridge sous forme d’un portail de membre Everbridge

Pour configurer l’authentification unique sur **EverBridge** en tant que **portail de membre EverBridge**, envoyez le fichier **XML des métadonnées de fédération** à l’[équipe de support technique EverBridge](mailto:support@everbridge.com). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

### <a name="create-everbridge-test-user"></a>Créer un utilisateur de test Everbridge

Dans cette section, vous allez créer l’utilisateur de test appelé Britta Simon dans EverBridge. Pour ajouter des utilisateurs dans la plateforme EverBridge, contactez l’[équipe de support technique EverBridge](mailto:support@everbridge.com). Les utilisateurs doivent être créés et activés dans EverBridge pour que vous puissiez utiliser l’authentification unique. 

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes.

* Cliquez sur Tester cette application dans le portail Azure. Vous êtes alors connecté automatiquement à l’instance de Everbridge pour laquelle vous avez configuré l’authentification SSO.

* Vous pouvez utiliser Mes applications de Microsoft. Quand vous cliquez sur la vignette Everbridge dans Mes applications, vous devez être automatiquement connecté à l’instance de Everbridge pour laquelle vous avez configuré l’authentification SSO. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez configuré Everbridge, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
