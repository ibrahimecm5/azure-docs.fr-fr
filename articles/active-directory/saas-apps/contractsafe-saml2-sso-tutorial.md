---
title: 'Tutoriel : Intégration de l’authentification unique Azure AD à ContractSafe Saml2 SSO'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et ContractSafe Saml2 SSO.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/18/2021
ms.author: jeedes
ms.openlocfilehash: 7e01f3790f14ae2aaf20da465cd9c66336ea5ce6
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132280504"
---
# <a name="tutorial-integrate-azure-ad-sso-with-contractsafe-saml2-sso"></a>Tutoriel : Intégrer l’authentification unique Azure AD à ContractSafe Saml2 SSO

Dans ce tutoriel, vous allez apprendre à intégrer ContractSafe Saml2 SSO à Azure Active Directory (Azure AD). Quand vous intégrez ContractSafe Saml2 SSO à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à ContractSafe Saml2 SSO.
* Permettre à vos utilisateurs de se connecter automatiquement à ContractSafe Saml2 SSO avec leur compte Azure AD.
* gérer vos comptes à un emplacement central : le portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement ContractSafe Saml2 SSO pour lequel l’authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* ContractSafe Saml2 SSO prend en charge l’authentification unique lancée par le **fournisseur d’identité**.

## <a name="add-contractsafe-saml2-sso-from-the-gallery"></a>Ajouter ContractSafe Saml2 SSO à partir de la galerie

Pour configurer l’intégration de ContractSafe Saml2 SSO à Azure AD, vous devrez ajouter ContractSafe Saml2 SSO à votre liste d’applications SaaS gérées à partir de la galerie.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **ContractSafe Saml2 SSO** dans la zone de recherche.
1. Sélectionnez **ContractSafe Saml2 SSO** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-contractsafe-saml2-sso"></a>Configurer et tester l’authentification unique Azure AD pour ContractSafe Saml2 SSO

Configurez et testez l’authentification unique Azure AD avec ContractSafe Saml2 SSO à l’aide d’un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur ContractSafe Saml2 SSO associé.

Pour configurer et tester l’authentification unique Azure AD avec ContractSafe Saml2 SSO, effectuez les étapes suivantes :

1. [Configurer l’authentification unique Azure AD](#configure-azure-ad-sso) pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
   1. [Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user) pour tester l’authentification unique Azure AD à l’aide du compte **B.Simon**.
   1. [Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user) pour permettre à **B.Simon** d’utiliser l’authentification unique Azure AD.
1. [Configurer ContractSafe Saml2 SSO](#configure-contractsafe-saml2-sso) pour configurer les paramètres de l’authentification unique côté application.
   1. [Créer un utilisateur de test ContractSafe Saml2 SSO](#create-a-contractsafe-saml2-sso-test-user) pour avoir un équivalent de **B.Simon** dans ContractSafe Saml2 SSO lié à la représentation Azure AD de l’utilisateur.
1. [Tester l’authentification unique](#test-sso) pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le portail Azure :

1. Dans le portail Azure, accédez à la page d’intégration de l’application **ContractSafe Saml2 SSO**, recherchez la section **Gérer**, puis sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, sélectionnez l’icône de crayon pour **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

   a. Dans la zone de texte **Identificateur**, saisissez une URL au format suivant : `https://app.contractsafe.com/saml2_auth/<UNIQUEID>/acs/`.

   b. Dans la zone de texte **URL de réponse**, saisissez une URL au format suivant : `https://app.contractsafe.com/saml2_auth/<UNIQUEID>/acs/`.

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur et l’URL de réponse réels. Pour obtenir ces valeurs, contactez l’[équipe du support technique de ContractSafe Saml2 SSO](mailto:support@contractsafe.com). Vous pouvez aussi vous reporter aux formats présentés dans la section **Configuration SAML de base** du portail Azure.

1. ContractSafe Saml2 SSO s’attend à recevoir les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à votre configuration d’attributs de jeton SAML. La capture d’écran suivante montre la liste des attributs par défaut.

    ![Attributs par défaut courants](common/default-attributes.png)

1. Outre les attributs par défaut, l’application ContractSafe Saml2 SSO s’attend à ce que quelques attributs supplémentaires soient passés dans la réponse SAML. Ces attributs sont préremplis, mais vous pouvez les examiner pour voir s’ils répondent à vos besoins. La liste suivante présente les attributs supplémentaires.

    | Name | Attribut source|
    | ---------------| --------------- |
    | emailname | user.userprincipalname |
    | email | user.onpremisesuserprincipalname |

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **XML de métadonnées de fédération**. Sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

1. Dans la section **Configurer ContractSafe Saml2 SSO**, copiez l’URL ou les URL appropriées en fonction de vos besoins.

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

Dans cette section, vous allez créer un utilisateur de test appelé **B.Simon** dans le portail Azure.

1. Dans le volet gauche du portail Azure, sélectionnez **Azure Active Directory**. Sélectionnez **Utilisateurs**, puis **Tous les utilisateurs**.
1. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.
1. Dans les propriétés **Utilisateur**, effectuez les étapes suivantes :
   1. Dans le champ **Nom**, entrez `B.Simon`.  
   1. Dans le champ **Nom d’utilisateur**, entrez une adresse e-mail au format `username@companydomain.extension`. par exemple `B.Simon@contoso.com`.
   1. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.
   1. Sélectionnez **Create** (Créer).

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser **B.Simon** à utiliser l’authentification unique Azure en lui accordant l’accès à ContractSafe Saml2 SSO.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **ContractSafe Saml2 SSO**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer**, puis sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B.Simon** dans la liste **Utilisateurs**. Cliquez ensuite sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste. Choisissez ensuite le bouton **Sélectionner** en bas de l’écran.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-contractsafe-saml2-sso"></a>Configurer ContractSafe Saml2 SSO

Pour configurer l’authentification unique côté **ContractSafe Saml2 SSO**, vous devez envoyer le fichier **XML des métadonnées de fédération** téléchargé et les URL correspondantes copiées à partir du portail Azure à l’[équipe du support technique de ContractSafe Saml2 SSO](mailto:support@contractsafe.com). L’équipe est chargée de définir correctement la connexion SSO SAML des deux côtés.

### <a name="create-a-contractsafe-saml2-sso-test-user"></a>Créer un utilisateur de test ContractSafe Saml2 SSO

Créez un utilisateur appelé B.Simon dans ContractSafe Saml2 SSO. Travaillez en collaboration avec l’[équipe du support technique de ContractSafe Saml2 SSO ](mailto:support@contractsafe.com) pour ajouter les utilisateurs dans la plateforme ContractSafe Saml2 SSO. Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes.

* Cliquez sur Tester cette application dans le portail Azure. Vous êtes alors automatiquement connecté automatiquement à l’instance de ContractSafe Saml2 SSO pour laquelle vous avez configuré l’authentification unique.

* Vous pouvez utiliser Mes applications de Microsoft. Quand vous cliquez sur la vignette ContractSafe Saml2 SSO dans Mes applications, vous êtes automatiquement connecté à l’instance de ContractSafe Saml2 SSO pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré ContractSafe Saml2 SSO, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
