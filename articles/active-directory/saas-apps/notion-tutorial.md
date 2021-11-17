---
title: 'Tutoriel : Intégrer l’authentification unique (SSO) Azure Active Directory avec Notion | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Notion.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/05/2021
ms.author: jeedes
ms.openlocfilehash: 24787217f146536825a03b671e7fa1c73fcbc22e
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132346051"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-notion"></a>Tutoriel : Intégrer l’authentification unique (SSO) Azure Active Directory à Notion

Dans ce tutoriel, vous allez découvrir comment intégrer Notion à Azure Active Directory (Azure AD). Quand vous intégrez Notion à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Notion.
* Autoriser les utilisateurs à se connecter automatiquement à Notion avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Notion pour lequel l’authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* Notion prend en charge l’authentification unique lancée par **le fournisseur de services et le fournisseur d’identité**
* Notion prend en charge le provisionnement d’utilisateurs **juste-à-temps**
> [!NOTE]
> L’identificateur de cette application étant une valeur de chaîne fixe, un seul espace de travail Notion peut être configuré dans un même locataire.


## <a name="adding-notion-from-the-gallery"></a>Ajout de Notion à partir de la galerie

Pour configurer l’intégration de Notion à Azure AD, vous devez ajouter Notion, à partir de la galerie, à votre liste d’applications SaaS managées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Notion** dans la zone de recherche.
1. Sélectionnez **Notion** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.


## <a name="configure-and-test-azure-ad-sso-for-notion"></a>Configurer et tester l’authentification unique Azure AD pour Notion

Configurez et testez l’authentification unique Azure AD avec Notion pour un utilisateur test nommé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir une relation entre un utilisateur Azure AD et l’utilisateur associé dans Notion.

Pour configurer et tester l’authentification unique Azure AD avec Notion, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Notion](#configure-notion-sso)** pour configurer les paramètres d’authentification unique côté application.
    1. **[Créer un utilisateur test Notion](#create-notion-test-user)** pour avoir dans Notion un équivalent de B.Simon lié à la représentation Azure AD associée.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **Notion**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, si vous souhaitez configurer l’application en mode Initié par le **fournisseur d’identité**, entrez les valeurs pour les champs suivants :

    Dans la zone de texte **URL de réponse**, entrez l’URL avec le modèle suivant, que vous pouvez obtenir à partir de votre espace de travail Notion dans **Settings & Members** > **Security & identity** > **Single sign-on URL** :  `https://www.notion.so/sso/saml/<CUSTOM_ID>`

1. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    Dans la zone de texte **URL de connexion**, entrez l’URL suivante : `https://www.notion.so/login`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de réponse et l’URL de connexion réelles. Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. L’application Notion s’attend à recevoir les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à la configuration d’attributs du jeton SAML. La capture d’écran suivante montre la liste des attributs par défaut.

    ![image](common/default-attributes.png)

1. En plus de ce qui précède, l’application Notion s’attend à ce que quelques attributs supplémentaires (présentés ci-dessous) soient repassés dans la réponse SAML. Ces attributs sont également préremplis, mais vous pouvez les examiner pour voir s’ils répondent à vos besoins.
    
    | Nom |  Attribut source|
    | ----------- | --------- |
    | email | user.mail |
    | firstName | user.givenname |
    | lastName | user.surname |


1. Dans la page **Configurer l’authentification unique avec SAML**, accédez à la section **Certificat de signature SAML** et cliquez sur le bouton Copier pour copier l’**URL des métadonnées de fédération d’application**. Accédez à **Settings & Members** > **Security & identity** dans l’espace de travail **Notion**, puis collez la valeur que vous avez copiée dans le champ **IDP metadata URL**.

    ![Lien Téléchargement de certificat](common/copy-metadataurl.png)

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

Dans cette section, vous allez autoriser B. Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Notion.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Notion**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-notion-sso"></a>Configurer l’authentification unique pour Notion

Accédez à **Settings & Members** > **Security & identity** dans l’espace de travail **Notion**, puis collez la valeur **URL des métadonnées de fédération d’application** que vous avez copiée dans le champ **IDP metadata URL**.

Dans la page même page de paramètres, sous **Email domains** cliquez sur **Contact support** pour ajouter le ou les domaines de messagerie de votre organisation.

Une fois vos domaines de messagerie approuvés et ajoutés, activez l’authentification unique SAML à l’aide du bouton bascule **Enable SAML**.

Une fois les tests réussis, vous pouvez appliquer l’authentification unique SAML à l’aide du bouton bascule **Enforce SAML**. Notez que les administrateurs de votre espace de travail Notion conservent la capacité se connecter par e-mail, mais que tous les autres membres devront utiliser l’authentification unique SAML pour se connecter à Notion.

### <a name="create-notion-test-user"></a>Créer un utilisateur test Notion

Dans cette section, une utilisatrice nommée Britta Simon est créée dans Notion. Notion prend en charge le provisionnement d’utilisateurs juste-à-temps, option qui est activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas encore d’utilisateurs dans Notion, il en est créé un après l’authentification.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

#### <a name="sp-initiated"></a>Lancée par le fournisseur de services :

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion Notion, à partir de laquelle vous pouvez initier le flux de connexion.  

* Accédez directement à l’URL de connexion Notion pour lancer le flux de connexion.

#### <a name="idp-initiated"></a>Lancée par le fournisseur d’identité :

* Dans le portail Azure, cliquez sur **Tester cette application**. Vous êtes alors automatiquement connecté à l’instance Notion pour laquelle vous avez configuré l’authentification unique 

Vous pouvez aussi utiliser Mes applications de Microsoft pour tester l’application dans n’importe quel mode. Si, quand vous cliquez sur la vignette Notion dans Mes applications, le mode Fournisseur de services est configuré, vous êtes redirigé vers la page de connexion de l’application pour lancer le flux de connexion ; s’il s’agit du mode Fournisseur d’identité, vous êtes automatiquement connecté à l’instance Notion pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).


## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré Notion, vous pouvez appliquer le contrôle de session, qui protège en temps réel contre l’exfiltration et l’infiltration des données sensibles de votre organisation. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
