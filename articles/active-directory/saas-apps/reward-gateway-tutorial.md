---
title: 'Didacticiel : Intégration d’Azure Active Directory à Reward Gateway | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Reward Gateway.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/31/2021
ms.author: jeedes
ms.openlocfilehash: c62e02cc1e0a6bd2382c0b62779e062ced65a65a
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132309053"
---
# <a name="tutorial-azure-active-directory-integration-with-reward-gateway"></a>Didacticiel : Intégration d’Azure Active Directory à Reward Gateway

Dans ce tutoriel, vous allez apprendre à intégrer Reward Gateway à Azure Active Directory (Azure AD). Lorsque vous intégrez la passerelle de récompense avec Azure AD, vous pouvez :

* Contrôler qui a accès à Reward Gateway dans Azure AD.
* Permettre à vos utilisateurs de se connecter automatiquement à Reward Gateway avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à Reward Gateway, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un [compte gratuit](https://azure.microsoft.com/free/).
* Abonnement Reward Gateway pour lequel l’authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Reward Gateway prend en charge l’authentification unique lancée par le **fournisseur d’identité**.

* Reward Gateway prend en charge le [provisionnement d’utilisateurs automatisé](reward-gateway-provisioning-tutorial.md).

## <a name="add-reward-gateway-from-the-gallery"></a>Ajouter Reward Gateway à partir de la galerie

Pour configurer l’intégration de Reward Gateway à Azure AD, vous devez ajouter Reward Gateway à partir de la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Reward Gateway** dans la zone de recherche.
1. Sélectionnez **Reward Gateway** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-reward-gateway"></a>Configurer et tester Azure AD SSO pour Reward Gateway

Configurez et testez Azure AD SSO avec Reward Gateway pour un utilisateur de test nommé **B.Simon**. Pour que l’authentification SSO fonctionne, vous devez établir une relation entre un utilisateur Azure AD et l’utilisateur associé dans Reward Gateway.

Pour configurer et tester Azure AD SSO avec Reward Gateway, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Reward Gateway](#configure-reward-gateway-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test Reward Gateway](#create-reward-gateway-test-user)** pour obtenir un équivalent de B.Simon dans Reward Gateway lié à la représentation Azure AD associée.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **Reward Gateway**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Sur la page **Configurer l’authentification unique avec SAML**, effectuez les étapes suivantes :

    a. Dans la zone de texte **Identificateur**, tapez une URL en utilisant un des modèles suivants :

    | URL d’identificateur |
    |---|
    | `https://<COMPANY_NAME>.rewardgateway.com` |
    | `https://<COMPANY_NAME>.rewardgateway.co.uk/` |
    | `https://<COMPANY_NAME>.rewardgateway.co.nz/` |
    | `https://<COMPANY_NAME>.rewardgateway.com.au/`|
    |

    b. Dans la zone de texte **URL de réponse** , tapez une URL en utilisant un des modèles suivants :

    | URL de réponse |
    |---|
    | `https://<COMPANY_NAME>.rewardgateway.com/Authentication/EndLogin?idp=<Unique Id>` |
    | `https://<COMPANY_NAME>.rewardgateway.co.uk/Authentication/EndLogin?idp=<Unique Id>` |
    | `https://<COMPANY_NAME>.rewardgateway.co.nz/Authentication/EndLogin?idp=<Unique Id>` |
    | `https://<COMPANY_NAME>.rewardgateway.com.au/Authentication/EndLogin?idp=<Unique Id>` |
    |

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur et l’URL de réponse réels. Pour obtenir ces valeurs, commencez à configurer une intégration sur le portail Reward Manager. Vous trouverez plus d’informations à l’adresse https://success.rewardgateway.com/hc/en-us/articles/360038650573-Microsoft-Azure-for-Authentication

5. Sur la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le fichier **XML de métadonnées de fédération** en fonction des options définies selon vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

6. Dans la section **Configurer Reward Gateway**, copiez la ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Reward Gateway.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Reward Gateway**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-reward-gateway-sso"></a>Configurer l’authentification unique Reward Gateway

Pour configurer l’authentification unique côté **Reward Gateway**, commencez à configurer une intégration sur le portail Reward Manager. Utilisez les métadonnées téléchargées pour obtenir votre certificat de signature et chargez-le lors de la configuration. Vous trouverez plus d’informations à l’adresse https://success.rewardgateway.com/hc/en-us/articles/360038650573-Microsoft-Azure-for-Authentication.

### <a name="create-reward-gateway-test-user"></a>Créer un utilisateur de test Reward Gateway

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans Reward Gateway. Collaborez avec l’[équipe de support Reward Gateway](mailto:clientsupport@rewardgateway.com) pour ajouter des utilisateurs à la plateforme Reward Gateway. Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique.

Reward Gateway prend aussi en charge le provisionnement automatique d’utilisateurs ; vous trouverez plus d’informations [ici](./reward-gateway-provisioning-tutorial.md) sur la manière de configurer ce dernier.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes.

* Cliquez sur Tester cette application dans le portail Azure : vous devez être connecté automatiquement à l’instance de Reward Gateway pour laquelle vous avez configuré l’authentification unique.

* Vous pouvez utiliser Mes applications de Microsoft. Quand vous cliquez sur la vignette Reward Gateway dans Mes applications, vous devez être connecté automatiquement à l’application Reward Gateway pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré Reward Gateway, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
