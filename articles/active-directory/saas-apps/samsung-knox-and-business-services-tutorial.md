---
title: 'Tutoriel : Intégration de l’authentification unique Azure Active Directory à Samsung Knox and Business Services | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Samsung Knox and Business Services.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/11/2021
ms.author: jeedes
ms.openlocfilehash: 444dd939222e402a14940d1934b32044997cf01b
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132299398"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-samsung-knox-and-business-services"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory à Samsung Knox and Business Services

Dans ce tutoriel, vous allez apprendre à intégrer Samsung Knox and Business Services à Azure Active Directory (Azure AD). Lorsque vous intégrez Samsung Knox and Business Services avec Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Samsung Knox and Business Services.
* Permettre aux utilisateurs de se connecter automatiquement à Samsung Knox and Business Services avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un compte Samsung Knox.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* Samsung Knox and Business Services prend en charge l’authentification unique lancée par le **fournisseur de services**.

> [!NOTE]
> L’identificateur de cette application étant une valeur de chaîne fixe, une seule instance peut être configurée dans un locataire.

## <a name="adding-samsung-knox-and-business-services-from-the-gallery"></a>Ajout de Samsung Knox and Business Services à partir de la galerie

Pour configurer l’intégration de Samsung Knox and Business Services avec Azure AD, vous devez ajouter Samsung Knox and Business Services à votre liste d’applications SaaS managées à partir de la galerie.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Samsung Knox and Business Services** dans la zone de recherche.
1. Sélectionnez **Samsung Knox and Business Services** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-samsung-knox-and-business-services"></a>Configurer et tester l’authentification unique Azure AD pour Samsung Knox and Business Services

Configurez et testez l’authentification unique Azure AD avec Samsung Knox and Business Services pour un utilisateur de test nommé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur associé dans [SamsungKnox.com](https://samsungknox.com/).

Pour configurer et tester l’authentification unique Azure AD avec Samsung Knox and Business Services, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Samsung Knox and Business Services](#configure-samsung-knox-and-business-services-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test Samsung Knox and Business Services](#create-samsung-knox-and-business-services-test-user)** pour avoir un équivalent de B.Simon dans Samsung Knox and Business Services lié à la représentation Azure AD associée.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **Samsung Knox and Business Services**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, entrez les valeurs pour les champs suivants :

    * Dans la zone de texte **URL de connexion**, tapez l’URL : `https://www2.samsungknox.com/en/sso/login/ad`
    * Dans la zone de texte **URL de réponse (URL d’assertion consumer service)** , saisissez l’URL `https://central.samsungknox.com/ams/ad/saml/acs`.
    
    ![Valeurs de la configuration SAML de base](https://docs.samsungknox.com/assets/merge/ad-sso/basic-saml-configuration.png)

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Samsung Knox and Business Services.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Samsung Knox and Business Services**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-samsung-knox-and-business-services-sso"></a>Configurer l’authentification unique Samsung Knox and Business Services

1. Dans une autre fenêtre de navigateur web, connectez-vous à [SamsungKnox.com](https://samsungknox.com/) en tant qu’administrateur.

1. Cliquez sur l’**Avatar** en haut à droite.

    ![Avatar Samsung Knox](./media/samsung-knox-and-business-services-tutorial/avatar.png)

1. Dans la barre latérale gauche, cliquez sur **ACTIVE DIRECTORY SETTINGS** et effectuez les étapes suivantes.

    ![ACTIVE DIRECTORY SETTINGS](https://docs.samsungknox.com/assets/merge/ad-sso/ad-5.png)

    a. Dans la zone de texte **Identifier(entity ID)** , collez la valeur **Identificateur** que vous avez entrée dans le portail Azure.

    b. Dans la zone de texte **App federation metadata URL**, collez la valeur du champ **URL des métadonnées de fédération d’application** que vous avez copiée à partir du portail Azure.

    c. Cliquez sur **CONNECT TO AD SSO**.

### <a name="create-samsung-knox-and-business-services-test-user"></a>Créer un utilisateur de test Samsung Knox and Business Services

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans Samsung Knox and Business Services. Pour obtenir des instructions sur l’invitation d’un sous-administrateur ou d’un utilisateur de test à rejoindre votre organisation Samsung Knox, reportez-vous aux guides d’administration relatifs à la [configuration de Knox](https://docs.samsungknox.com/admin/knox-configure/Administrators.htm) ou à l’[inscription mobile de Knox](https://docs.samsungknox.com/admin/knox-mobile-enrollment/kme-add-an-admin.htm). Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers le site [SamsungKnox.com](https://samsungknox.com/), d’où vous pouvez lancer le flux de connexion. 

* Accédez directement à [SamsungKnox.com](https://samsungknox.com/) pour lancer le flux de connexion.

* Vous pouvez utiliser Mes applications de Microsoft. Le fait de cliquer sur la vignette Samsung Knox et services métier dans Mes applications vous redirige vers le site [SamsungKnox.com](https://samsungknox.com/). Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez configuré Samsung Knox and Business Services, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
