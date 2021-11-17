---
title: 'Tutoriel : Intégration d’Azure Active Directory à TigerConnect Secure Messenger | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et TigerConnect Secure Messenger.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/16/2021
ms.author: jeedes
ms.openlocfilehash: 197393f123cb7d694e7d1a1929dd89723e675f16
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132333064"
---
# <a name="tutorial-azure-active-directory-integration-with-tigerconnect-secure-messenger"></a>Tutoriel : Intégration d’Azure Active Directory à TigerConnect Secure Messenger

Dans ce tutoriel, vous allez apprendre à intégrer TigerConnect Secure Messenger à Azure Active Directory (Azure AD). Quand vous intégrez TigerConnect Secure Messenger à Azure AD, vous pouvez :

* Contrôler, dans Azure AD, qui a accès à TigerConnect Secure Messenger.
* Autoriser vos utilisateurs à se connecter automatiquement à TigerConnect Secure Messenger avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à TigerConnect Secure Messenger, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.
* Un abonnement TigerConnect Secure Messenger pour lequel l’authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test et intégrer TigerConnect Secure Messenger à Azure AD.

* TigerConnect Secure Messenger prend en charge l’authentification unique lancée par le **fournisseur de services**.

## <a name="add-tigerconnect-secure-messenger-from-the-gallery"></a>Ajouter TigerConnect Secure Messenger à partir de la galerie

Pour configurer l’intégration de TigerConnect Secure Messenger à Azure AD, vous devez ajouter TigerConnect Secure Messenger, disponible dans la galerie, à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **TigerConnect Secure Messenger** dans la zone de recherche.
1. Sélectionnez **TigerConnect Secure Messenger** dans le panneau de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-tigerconnect-secure-messenger"></a>Configurer et tester l’authentification unique Azure AD pour TigerConnect Secure Messenger

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD auprès de TigerConnect Secure Messenger pour un utilisateur de test appelé **Britta Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur TigerConnect Secure Messenger associé.

Pour configurer et tester l’authentification unique Azure AD avec TigerConnect Secure Messenger, vous devez effectuer les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique TigerConnect Secure Messenger](#configure-tigerconnect-secure-messenger-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test TigerConnect Secure Messenger](#create-a-tigerconnect-secure-messenger-test-user)** pour avoir un équivalent de Britta Simon dans TigerConnect Secure Messenger, lié à l’utilisateur Azure AD nommé Britta Simon.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure.

Pour configurer l’authentification unique Azure AD auprès de TigerConnect Secure Messenger, effectuez les étapes suivantes :

1. Sur le portail Azure, accédez à la page d’intégration de l’application **TigerConnect Secure Messenger**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    1. Dans la zone **URL de connexion**, tapez l’URL :

       `https://home.tigertext.com`

    1. Dans la zone de texte **Identificateur (ID d’entité)**, saisissez une URL au format suivant :

       `https://saml-lb.tigertext.me/v1/organization/<INSTANCE_ID>`

    > [!NOTE]
    > La valeur de **Identificateur (ID d’entité)** n’est pas réelle. Mettez-la à jour avec l’identificateur réel. Contactez l’[équipe du support technique de TigerConnect Secure Messenger](mailto:prosupport@tigertext.com) pour obtenir cette valeur. Vous pouvez aussi vous référer aux modèles figurant dans le volet **Configuration SAML de base** sur le portail Azure.

1. Dans le volet **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, sélectionnez **Télécharger** pour télécharger le fichier **XML de métadonnées de fédération** à partir des options données et enregistrez-le sur votre ordinateur.

    ![Option de téléchargement du fichier XML de métadonnées de fédération](common/metadataxml.png)

1. Dans la section **Configurer TigerConnect Secure Messenger**, copiez la ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B. Simon à utiliser l’authentification unique Azure en lui accordant l’accès à TigerConnect Secure Messenger.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **TigerConnect Secure Messenger**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-tigerconnect-secure-messenger-sso"></a>Configurer l’authentification unique TigerConnect Secure Messenger

Pour configurer l’authentification unique côté TigerConnect Secure Messenger, vous devez envoyer le XML des métadonnées de fédération téléchargé et les URL copiées appropriées du Portail Azure à l’[équipe du support technique TigerConnect Secure Messenger](mailto:prosupport@tigertext.com). L’équipe TigerConnect Secure Messenger vérifiera que la connexion avec l’authentification unique SAML est correctement configurée des deux côtés.

## <a name="create-a-tigerconnect-secure-messenger-test-user"></a>Créer un utilisateur de test TigerConnect Secure Messenger

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans TigerConnect Secure Messenger. Contactez l’[équipe du support technique TigerConnect Secure Messenger](mailto:prosupport@tigertext.com) pour ajouter Britta Simon à la plateforme TigerConnect Secure Messenger. Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion à TigerConnect Secure Messenger, d’où vous pouvez lancer le flux de connexion. 

* Accédez directement à l’URL de connexion à TigerConnect Secure Messenger pour lancer le flux de connexion.

* Vous pouvez utiliser Mes applications de Microsoft. Le fait de cliquer sur la vignette TigerConnect Secure Messenger dans Mes applications vous redirige vers l’URL de connexion à TigerConnect Secure Messenger. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré TigerConnect Secure Messenger, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
