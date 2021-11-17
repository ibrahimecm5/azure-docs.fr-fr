---
title: 'Tutoriel : Intégration de l’authentification unique (SSO) Azure Active Directory à MongoDB Cloud | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et MongoDB Cloud.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/14/2021
ms.author: jeedes
ms.openlocfilehash: 0e1b8ad24f61bfc10daae01e19265be2e5bef2b4
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132294494"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-mongodb-cloud"></a>Tutoriel : Intégration de l’authentification unique (SSO) Azure Active Directory à MongoDB Cloud

Dans ce tutoriel, vous allez découvrir comment intégrer MongoDB Cloud à Azure Active Directory (Azure AD). Quand vous intégrez MongoDB Cloud à Azure AD, vous pouvez :

* Déterminer qui dans Azure AD a accès à MongoDB Cloud, à MongoDB Atlas, à la communauté MongoDB, à MongoDB University et au support MongoDB
* Permettre à vos utilisateurs de se connecter automatiquement à MongoDB Cloud avec leur compte Azure AD
* gérer vos comptes à un emplacement central : le portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement MongoDB Cloud pour lequel l’authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* MongoDB Cloud prend en charge l’authentification unique initiée par le **fournisseur de services** et le **fournisseur d’identité**.
* MongoDB Cloud prend en charge le provisionnement d’utilisateurs **juste-à-temps**.

## <a name="add-mongodb-cloud-from-the-gallery"></a>Ajouter MongoDB Cloud à partir de la galerie

Pour configurer l’intégration de MongoDB Cloud dans Azure AD, vous devez ajouter MongoDB Cloud à votre liste d’applications SaaS managées à partir de la galerie.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **MongoDB Cloud** dans la zone de recherche.
1. Sélectionnez **MongoDB Cloud** dans le volet des résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-mongodb-cloud"></a>Configurer et tester l’authentification unique Azure AD pour MongoDB Cloud

Configurez et testez l’authentification unique Azure AD avec MongoDB Cloud à l’aide d’un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur MongoDB Cloud associé.

Pour configurer et tester l’authentification unique Azure AD avec MongoDB Cloud, effectuez les étapes suivantes :

1. [Configurer l’authentification unique Azure AD](#configure-azure-ad-sso) pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. [Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user) pour tester l’authentification unique Azure AD avec B.Simon.
    1. [Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user) pour permettre à B.Simon d’utiliser l’authentification unique Azure AD.
1. [Configurer l’authentification unique MongoDB Cloud](#configure-mongodb-cloud-sso) pour configurer les paramètres de l’authentification unique côté application
    1. [Créer un utilisateur de test MongoDB Cloud](#create-a-mongodb-cloud-test-user) pour avoir un équivalent de B.Simon dans MongoDB Cloud, lié à la représentation Azure AD de l’utilisateur
1. [Tester l’authentification unique](#test-sso) pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le [portail Azure](https://portal.azure.com/), accédez à la page d’intégration de l’application **MongoDB Cloud**, puis recherchez la section **Gérer**. Sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, sélectionnez l’icône de crayon pour **Configuration SAML de base** afin de modifier les paramètres.

   ![Capture d’écran de la page Configurer l’authentification unique avec SAML avec l’icône de crayon mise en évidence](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, si vous voulez configurer l’application en mode lancé par le **fournisseur d’identité**, entrez les valeurs pour les champs suivants :

    a. Dans la zone de texte **Identificateur**, saisissez une URL au format suivant : `https://www.okta.com/saml2/service-provider/<Customer_Unique>`

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://auth.mongodb.com/sso/saml2/<Customer_Unique>`

1. Sélectionnez **Définir des URL supplémentaires**, puis effectuez l’étape suivante si vous voulez configurer l’application en mode initié par le **fournisseur de services** :

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://cloud.mongodb.com/sso/<Customer_Unique>`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL de réponse et l’URL de connexion réels. Pour obtenir ces valeurs, contactez l’[équipe du support technique de MongoDB Cloud](https://support.mongodb.com/). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. L’application MongoDB Cloud attend les assertions SAML dans un format spécifique, ce qui oblige à ajouter des mappages d’attributs personnalisés à la configuration des attributs du jeton SAML. La capture d’écran suivante montre la liste des attributs par défaut.

    ![Capture d’écran des attributs par défaut](common/default-attributes.png)

1. Outre les attributs précédents, l’application MongoDB Cloud s’attend à ce que quelques attributs supplémentaires soient repassés dans la réponse SAML. Ces attributs sont également préremplis, mais vous pouvez les examiner pour voir s’ils répondent à vos besoins.
    
    | Nom | Attribut source|
    | ---------------| --------- |
    | email | user.userprincipalname |
    | firstName | user.givenname |
    | lastName | user.surname |

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **XML de métadonnées de fédération**. Sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Capture d’écran de la section Certificat de signature SAML, avec mise en évidence du lien Télécharger](common/metadataxml.png)

1. Dans la section **Configurer MongoDB Cloud**, copiez les URL appropriées, selon vos besoins.

    ![Capture d’écran de la section Configurer MongoDB Cloud avec les URL mises en évidence](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

Dans cette section, vous allez créer un utilisateur de test appelé B. Simon sur le portail Azure.

1. Dans le volet gauche du portail Azure, sélectionnez **Azure Active Directory** > **Utilisateurs** > **Tous les utilisateurs**.
1. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.
1. Dans les propriétés **Utilisateur**, effectuez les étapes suivantes :
   1. Dans le champ **Nom**, entrez `B.Simon`.  
   1. Dans le champ **Nom de l’utilisateur**, entrez username@companydomain.extension. Par exemple : `B.Simon@contoso.com`.
   1. Cochez la case **Afficher le mot de passe** et notez le mot de passe.
   1. Sélectionnez **Create** (Créer).

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser B. Simon à utiliser l’authentification unique Azure en lui accordant l’accès à MongoDB Cloud.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **MongoDB Cloud**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-mongodb-cloud-sso"></a>Configurer l’authentification unique MongoDB Cloud

Pour configurer l’authentification unique côté MongoDB Cloud, vous avez besoin des URL appropriées, copiées à partir du portail Azure. Vous devez également configurer l’application de fédération pour votre organisation MongoDB Cloud. Suivez les instructions de la [documentation MongoDB Cloud](https://docs.atlas.mongodb.com/security/federated-auth-azure-ad/). Si vous rencontrez un problème, contactez l’[équipe du support technique MongoDB Cloud](https://support.mongodb.com/).

### <a name="create-a-mongodb-cloud-test-user"></a>Créer un utilisateur de test MongoDB Cloud

MongoDB Cloud prend en charge le provisionnement d’utilisateurs juste-à-temps, qui est activé par défaut. Vous n’avez aucune opération supplémentaire à effectuer. Si l’utilisateur souhaité n’existe pas déjà dans MongoDB Cloud, il est créé après l’authentification.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

#### <a name="sp-initiated"></a>Lancée par le fournisseur de services :

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion à MongoDB Cloud, d’où vous pouvez lancer le flux de connexion.  

* Accédez directement à l’URL de connexion à MongoDB Cloud et lancez le flux de connexion.

#### <a name="idp-initiated"></a>Lancée par le fournisseur d’identité :

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors automatiquement connecté à l’instance de MongoDB Cloud pour laquelle vous avez configuré l’authentification unique. 

Vous pouvez aussi utiliser Mes applications de Microsoft pour tester l’application dans n’importe quel mode. Quand vous cliquez sur la vignette MongoDB Cloud dans Mes applications, si le mode Fournisseur de services est configuré, vous êtes redirigé vers la page de connexion de l’application pour lancer le flux de connexion ; si le mode Fournisseur d’identité est configuré, vous êtes automatiquement connecté à l’instance de MongoDB Cloud pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez configuré MongoDB Cloud, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
