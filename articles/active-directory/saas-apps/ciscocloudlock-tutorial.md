---
title: 'Tutoriel : Intégration de l’authentification unique Azure AD à The Cloud Security Fabric'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et The Cloud Security Fabric.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/17/2021
ms.author: jeedes
ms.openlocfilehash: 17c3275b022c1b32b452eac3e672aea938cc57eb
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132344785"
---
# <a name="tutorial-azure-ad-sso-integration-with-the-cloud-security-fabric"></a>Tutoriel : Intégration de l’authentification unique Azure AD à The Cloud Security Fabric

Dans ce tutoriel, vous allez découvrir comment intégrer The Cloud Security Fabric avec Azure Active Directory (Azure AD). Quand vous intégrez The Cloud Security Fabric à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à The Cloud Security Fabric.
* Permettre à vos utilisateurs de se connecter automatiquement à The Cloud Security Fabric avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement The Cloud Security Fabric pour lequel l’authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* The Cloud Security Fabric prend en charge l’authentification unique lancée par le **fournisseur de services**.

## <a name="add-the-cloud-security-fabric-from-the-gallery"></a>Ajouter The Cloud Security Fabric à partir de la galerie

Pour configurer l’intégration de The Cloud Security Fabric à Azure AD, vous devrez ajouter The Cloud Security Fabric à votre liste d’applications SaaS gérées à partir de la galerie.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **The Cloud Security Fabric** dans la zone de recherche.
1. Sélectionnez **The Cloud Security Fabric** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-the-cloud-security-fabric"></a>Configurer et tester l’authentification unique Azure AD pour The Cloud Security Fabric

Configurez et testez l’authentification unique Azure AD avec The Cloud Security Fabric avec un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur The Cloud Security Fabric associé.

Pour configurer et tester l’authentification unique Azure AD avec The Cloud Security Fabric, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique The Cloud Security Fabric](#configure-the-cloud-security-fabric-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test The Cloud Security Fabric](#create-the-cloud-security-fabric-test-user)**  : pour avoir un équivalent de B.Simon dans The Cloud Security Fabric lié à la représentation Azure AD associée.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **The Cloud Security Fabric**, recherchez la section **Gérer**, puis sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon pour **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    a. Dans la zone de texte **URL de connexion**, tapez une URL en utilisant un des modèles suivants :

      | **URL d’authentification** |
      |--------|
      | `https://platform.cloudlock.com` |
      | `https://app.cloudlock.com` |
      
   b. Dans la zone de texte **Identificateur (ID d’entité)** , tapez une URL en utilisant un des modèles suivants :

      | **Identificateur** |
      |---------|
      | `https://platform.cloudlock.com/gate/saml/sso/<subdomain>` |
      | `https://app.cloudlock.com/gate/saml/sso/<subdomain>` |
     
    > [!NOTE]
    > La valeur de l'identificateur n'est pas réelle. Mettez à jour cette valeur avec l’identificateur réel. Contactez [l’équipe de support client de The Cloud Security Fabric](mailto:support@cloudlock.com) pour obtenir cette valeur. Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

4. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **XML de métadonnées de fédération** et sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

5. Pour modifier les options **Signature** en fonction de vos besoins, cliquez sur le bouton **Modifier** afin d’ouvrir la boîte de dialogue **Certificat de signature SAML**.

    ![Réponse SAML](./media/ciscocloudlock-tutorial/saml.png)

    a. Sélectionnez l’option **Signer la réponse et l’assertion SAML** dans **Option de signature**.

    b. Sélectionnez l’option **SHA-256** pour **Algorithme de signature**.

    c. Cliquez sur **Enregistrer**.  

6. Dans la section **Configurer The Cloud Security Fabric**, copiez la ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à The Cloud Security Fabric.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **The Cloud Security Fabric**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-the-cloud-security-fabric-sso"></a>Configurer l’authentification unique The Cloud Security Fabric

Pour configurer l’authentification unique côté **The Cloud Security Fabric**, vous devez envoyer le **XML des métadonnées de fédération** téléchargé et les URL appropriées copiées à partir du portail Azure à l’[équipe du support technique The Cloud Security Fabric](mailto:support@cloudlock.com). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

### <a name="create-the-cloud-security-fabric-test-user"></a>Créer un utilisateur de test The Cloud Security Fabric

Dans cette section, vous allez créer un utilisateur nommé B.Simon dans The Cloud Security Fabric. Travaillez en collaboration avec [l’équipe de support de The Cloud Security Fabric](mailto:support@cloudlock.com) pour ajouter des utilisateurs à la plateforme The Cloud Security Fabric. Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion à The Cloud Security Fabric, d’où vous pouvez lancer le flux de connexion. 

* Accédez directement à l’URL de connexion à The Cloud Security Fabric pour lancer le flux de connexion.

* Vous pouvez utiliser Mes applications de Microsoft. Un clic sur la vignette The Cloud Security Fabric dans Mes applications vous redirige vers l’URL de connexion à The Cloud Security Fabric. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré The Cloud Security Fabric, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
