---
title: 'Tutoriel : Intégration de l’authentification unique Azure AD à Keeper Password Manager'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Keeper Password Manager.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/24/2021
ms.author: jeedes
ms.openlocfilehash: 26f56e452b2065fd61180d9e6cc3f757d8392c16
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132294843"
---
# <a name="tutorial-azure-ad-sso-integration-with-keeper-password-manager"></a>Tutoriel : Intégration de l’authentification unique Azure AD à Keeper Password Manager

Dans ce tutoriel, vous allez découvrir comment intégrer Keeper Password Manager à Azure Active Directory (Azure AD). Quand vous intégrez Keeper Password Manager à Azure AD, vous pouvez :

* Contrôler, dans Azure AD, qui a accès à Keeper Password Manager.
* Autoriser les utilisateurs à se connecter automatiquement à Keeper Password Manager avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Keeper Password Manager pour lequel l’authentification unique est activée.

> [!NOTE]
> Cette intégration peut également être utilisée à partir de l’environnement cloud US Government Azure AD. Cette application est disponible dans la Galerie d’applications cloud US Government Azure AD et peut être configurée de la même façon que dans le cloud public.

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Keeper Password Manager prend en charge l’authentification unique lancée par le fournisseur de services.
* Keeper Password Manager prend en charge [l’attribution d’utilisateurs et la suppression des privilèges d’accès **automatisées**](keeper-password-manager-digitalvault-provisioning-tutorial.md) (recommandé).
* Keeper Password Manager prend en charge l’attribution d’utilisateurs juste-à-temps.

## <a name="add-keeper-password-manager-from-the-gallery"></a>Ajouter Keeper Password Manager à partir de la galerie

Pour configurer l’intégration de Keeper Password Manager à Azure AD, vous devez ajouter Keeper Password Manager depuis la galerie à votre liste d’applications SaaS managées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire, ou avec un compte personnel Microsoft.
1. Dans le volet de gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans **Ajouter à partir de la galerie**, tapez **Keeper Password Manager** dans la zone de recherche.
1. Sélectionnez **Keeper Password Manager** dans le volet des résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-keeper-password-manager"></a>Configurer et tester l’authentification unique Azure AD pour Keeper Password Manager

Configurez et testez l’authentification unique Azure AD avec Keeper Password Manager pour un utilisateur test nommé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur correspondant dans Keeper Password Manager.

Pour configurer et tester l’authentification unique Azure AD avec Keeper Password Manager :

1. [Configurer l’authentification unique Azure AD](#configure-azure-ad-sso) pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.

    1. [Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user) pour tester l’authentification unique Azure AD avec Britta Simon.
    1. [Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user) pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.

1. [Configurer l’authentification unique Keeper Password Manager](#configure-keeper-password-manager-sso) pour configurer les paramètres d’authentification unique côté application.
    1. [Créer un utilisateur test Keeper Password Manager](#create-a-keeper-password-manager-test-user) pour avoir, dans Keeper Password Manager, un équivalent de Britta Simon lié à la représentation Azure AD de l’utilisateur.
1. [Tester l’authentification unique](#test-sso) pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **Keeper Password Manager** et recherchez la section **Gérer**. Sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, sélectionnez l’icône de crayon pour **Configuration SAML de base** afin de modifier les paramètres.

   ![Capture d’écran de Configurer l’authentification unique avec SAML, où l’icône de crayon est mise en évidence.](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    a. Dans **Identificateur (ID d’entité)** , tapez une URL en respectant l’un des formats suivants :
    * Pour l’authentification unique cloud : `https://keepersecurity.com/api/rest/sso/saml/<CLOUD_INSTANCE_ID>`
    * Pour l’authentification unique locale : `https://<KEEPER_FQDN>/sso-connect`

    b. Dans **URL de réponse**, tapez une URL en respectant l’un des formats suivants :
    * Pour l’authentification unique cloud : `https://keepersecurity.com/api/rest/sso/saml/sso/<CLOUD_INSTANCE_ID>`
    * Pour l’authentification unique locale : `https://<KEEPER_FQDN>/sso-connect/saml/sso`

    c. Dans **URL d’authentification**, tapez une URL en respectant l’un des formats suivants :
    * Pour l’authentification unique cloud : `https://keepersecurity.com/api/rest/sso/saml/sso/<CLOUD_INSTANCE_ID>`
    * Pour l’authentification unique locale : `https://<KEEPER_FQDN>/sso-connect/saml/login`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL de réponse et l’URL de connexion réels. Pour obtenir ces valeurs, contactez l’[équipe du support technique Keeper Password Manager](https://keepersecurity.com/contact.html). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. L’application Keeper Password Manager s’attend à recevoir les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à votre configuration d’attributs de jeton SAML. La capture d’écran suivante montre la liste des attributs par défaut.

    ![Capture d’écran Attributs et revendications de l’utilisateur.](common/default-attributes.png)

1. En plus, l’application Keeper Password Manager s’attend à ce que quelques attributs supplémentaires soient repassés dans la réponse SAML. C’est ce qui est illustré par le tableau suivant. Ces attributs sont également préremplis, mais vous pouvez les examiner pour voir s’ils répondent à vos besoins.

    | Nom | Attribut source|
    | ------------| --------- |
    | Premier | user.givenname |
    | Dernier | user.surname |
    | E-mail | user.mail |

5. Dans **Configurer l’authentification unique avec SAML**, à la section **Certificat de signature SAML**, sélectionnez **Télécharger**. Le fichier **XML des métadonnées de fédération** est ainsi téléchargé selon vos besoins à partir des options, et enregistré sur votre ordinateur.

    ![Capture d’écran de la section Certificat de signature SAML, montrant l’option Télécharger sélectionnée.](common/metadataxml.png)

6. Dans la section **Configurer Keeper Password Manager**, copiez les URL appropriées en fonction de vos besoins.

    ![Capture d’écran de la section Configurer Keeper Password Manager, montrant les URL sélectionnées.](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD 

Dans cette section, vous créez un utilisateur de test nommé `B.Simon` dans le portail Azure.

1. Dans le volet gauche du portail Azure, sélectionnez **Azure Active Directory** > **Utilisateurs** > **Tous les utilisateurs**.
1. Sélectionnez **Nouvel utilisateur**, en haut de l’écran.
1. Dans les propriétés **Utilisateur**, effectuez les étapes suivantes :
   1. Pour **Nom**, entrez `B.Simon`.  
   1. Entrez `username@companydomain.extension` comme **Nom d’utilisateur**. Par exemple : `B.Simon@contoso.com`.
   1. Sélectionnez **Afficher le mot de passe** et notez la valeur affichée.
   1. Sélectionnez **Create** (Créer).

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Keeper Password Manager.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise** > **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Keeper Password Manager**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**. Dans **Ajouter une attribution**, sélectionnez **Utilisateurs et groupes**.
1. Dans **Utilisateurs et groupes**, sélectionnez **B.Simon** dans la liste des utilisateurs. Choisissez **Sélectionner** au bas de l’écran.
1. Si vous vous attendez à ce qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste **Sélectionner un rôle**. Si aucun rôle n’a été configuré pour cette application, le rôle **Accès par défaut** est sélectionné.
1. Sous **Ajouter une attribution**, sélectionnez **Attribuer**.

## <a name="configure-keeper-password-manager-sso"></a>Configurer l’authentification unique Keeper Password Manager

Configurez l’authentification unique pour l’application en vous reportant aux instructions fournies dans le [Guide de prise en charge de Keeper](https://docs.keeper.io/sso-connect-guide/).

### <a name="create-a-keeper-password-manager-test-user"></a>Créer un utilisateur test Keeper Password Manager

Pour permettre aux utilisateurs Azure AD de se connecter à Keeper Password Manager, vous devez les attribuer. L’application prend en charge le provisionnement d’utilisateurs juste-à-temps ; après l’authentification, les utilisateurs sont créés automatiquement dans l’application. Si vous voulez configurer des utilisateurs manuellement, contactez le [support technique de Keeper](https://keepersecurity.com/contact.html).

> [!NOTE]
> Keeper Password Manager prend également en charge l’attribution automatique d’utilisateurs. Vous trouverez plus d’informations sur la configuration de cette fonctionnalité [ici](./keeper-password-manager-digitalvault-provisioning-tutorial.md).

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL d’authentification Keeper Password Manager, d’où vous pouvez lancer le flux de connexion. 

* Accédez directement à l’URL d’authentification Keeper Password Manager pour lancer le flux de connexion.

* Vous pouvez utiliser Mes applications de Microsoft. Quand vous cliquez sur la vignette Keeper Password Manager dans Mes applications, vous êtes redirigé vers l’URL d’authentification Keeper Password Manager. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré Keeper Password Manager, vous pouvez appliquer le contrôle de session. Cela protège l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. Pour plus d’informations, consultez [Découvrir comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
