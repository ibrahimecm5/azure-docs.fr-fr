---
title: 'Tutoriel : Intégration de l’authentification unique Azure Active Directory à AlertMedia | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et AlertMedia.
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
ms.openlocfilehash: d4fc3a09ff52894928d62a8e124d92f57d51a392
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132330689"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-alertmedia"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory à AlertMedia

Ce tutoriel explique comment intégrer AlertMedia à Azure Active Directory (Azure AD). Quand vous intégrez AlertMedia à Azure AD, vous pouvez :

* Contrôler qui dans Azure AD a accès à AlertMedia
* Permettre aux utilisateurs de se connecter automatiquement à AlertMedia avec leur compte Azure AD
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement AlertMedia pour lequel l’authentification unique (SSO) est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* AlertMedia prend en charge l'authentification unique lancée par le **fournisseur d'identité**.
* AlertMedia prend en charge l'approvisionnement d'utilisateurs **juste-à-temps**.
* AlertMedia prend en charge [le provisionnement d’utilisateurs automatisé](alertmedia-provisioning-tutorial.md).

## <a name="add-alertmedia-from-the-gallery"></a>Ajouter AlertMedia à partir de la galerie

Pour configurer l’intégration d’AlertMedia à Azure AD, vous devez ajouter AlertMedia à partir de la galerie à votre liste d’applications SaaS managées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **AlertMedia** dans la zone de recherche.
1. Sélectionnez **AlertMedia** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-alertmedia"></a>Configurer et tester l'authentification unique Azure AD pour AlertMedia

Configurez et testez l’authentification unique Azure AD avec AlertMedia à l’aide d’un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur AlertMedia associé.

Pour configurer et tester l'authentification unique Azure AD auprès d'AlertMedia, suivez les étapes ci-dessous :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique AlertMedia](#configure-alertmedia-sso)** pour configurer les paramètres de l’authentification unique côté application
    1. **[Créer un utilisateur de test AlertMedia](#create-alertmedia-test-user)** pour avoir un équivalent de B.Simon dans AlertMedia, lié à la représentation Azure AD de l’utilisateur
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Sur le portail Azure, accédez à la page d'intégration de l'application **AlertMedia**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la page **Configurer l’authentification unique avec SAML**, effectuez les étapes suivantes :

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<SUBDOMAIN>.alertmedia.com`

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://<SUBDOMAIN>.alertmedia.com/api/sso/saml/`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur et l’URL de réponse réels. Pour obtenir ces valeurs, contactez l’[équipe du support client AlertMedia](mailto:support@alertmedia.com). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. L’application AlertMedia attend les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à la configuration d’attributs de jeton SAML. La capture d’écran suivante montre la liste des attributs par défaut.

    ![image](common/default-attributes.png)

1. En plus de ce qui précède, l’application AlertMedia s’attend à ce que quelques attributs supplémentaires, indiqués ci-après, soient repassés dans la réponse SAML. Ces attributs sont également préremplis, mais vous pouvez les examiner pour voir s’ils répondent à vos besoins.

| Nom | Attribut source|
| ---- | --------------- |
| email | user.userprincipalname |
| firstname | user.givenname |
| lastname | user.surname |

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur le bouton Copier pour copier l’**URL des métadonnées de fédération d’application**, puis enregistrez-la sur votre ordinateur.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à AlertMedia.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **AlertMedia**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-alertmedia-sso"></a>Configurer l’authentification unique AlertMedia

1. Dans une nouvelle fenêtre de navigateur web, connectez-vous à votre site d’entreprise AlertMedia en tant qu’administrateur.
1. Accédez à **Company**, puis sélectionnez **Single Sign-On**.

    ![Bouton Compte](./media/alertmedia-tutorial/account.png)

1. Sous **Méthode d'authentification**, sélectionnez **Métadonnées SAML distantes**.
1. Activez l'option **Signer la requête**.
1. Activez l'option **Autoriser les requêtes passives**.
1. Dans la zone de texte **MetaData URL**, collez la valeur **URL des métadonnées de fédération d’application** que vous avez copiée à partir du portail Azure.
1. Pour **Comparaison du contexte de l'authentification demandée**, choisissez **exact**.
1. Dans la zone de texte **IDP Login URL**, collez l’**URL de connexion** que vous avez copiée à partir du portail Azure.
1. Cliquez sur **Enregistrer**.

### <a name="create-alertmedia-test-user"></a>Créer un utilisateur de test AlertMedia

Dans cette section, un utilisateur appelé Britta Simon est créé dans AlertMedia. AlertMedia prend en charge le provisionnement d’utilisateurs juste-à-temps, option activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas encore d’utilisateur dans AlertMedia, il en est créé un après l’authentification.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes.

* Cliquez sur Tester cette application sur le portail Azure. Vous êtes alors automatiquement connecté à l'instance d'AlertMedia pour laquelle vous avez configuré l'authentification unique.

* Vous pouvez utiliser Mes applications de Microsoft. Lorsque vous cliquez sur la vignette AlertMedia dans Mes applications, vous devez être automatiquement connecté à l'application AlertMedia pour laquelle vous avez configuré l'authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré AlertMedia, vous pouvez appliquer le contrôle de session qui protège en temps réel contre l'exfiltration et l'infiltration des données sensibles de votre organisation. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
