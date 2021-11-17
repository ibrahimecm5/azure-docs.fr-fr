---
title: 'Tutoriel : Intégration d’Azure Active Directory à Signagelive | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Signagelive.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/01/2021
ms.author: jeedes
ms.openlocfilehash: 4848e395d62629f5e104e275a5430bfaf2c6f3e6
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132304664"
---
# <a name="tutorial-azure-active-directory-integration-with-signagelive"></a>Tutoriel : Intégration d’Azure Active Directory à Signagelive

Dans ce tutoriel, vous allez découvrir comment intégrer Signagelive à Azure Active Directory (Azure AD). Quand vous intégrez Signagelive à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Signagelive.
* Permettre à vos utilisateurs de se connecter automatiquement à Signagelive avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Signagelive pour lequel l’authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Signagelive prend en charge l’authentification unique lancée par le fournisseur de services.
* Signagelive prend en charge l’[attribution automatisée d’utilisateurs](signagelive-provisioning-tutorial.md).

## <a name="add-signagelive-from-the-gallery"></a>Ajouter Signagelive à partir de la galerie

Pour configurer l’intégration de Signagelive avec Azure AD, vous devez ajouter Signagelive à partir de la galerie à votre liste d’applications SaaS managées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Signagelive** dans la zone de recherche.
1. Sélectionnez **Signagelive** dans le volet des résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-signagelive"></a>Configurer et tester l’authentification unique Azure AD pour Signagelive

Configurez et testez l’authentification unique Azure AD avec Signagelive à l’aide d’un utilisateur test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Signagelive associé.

Pour configurer et tester l’authentification unique Azure AD avec Signagelive, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Signagelive](#configure-signagelive-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur test Signagelive](#create-signagelive-test-user)** pour avoir un équivalent de B.Simon dans Signagelive lié à la représentation Azure AD associée de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, dans la page d’intégration d’application **Signagelive**, recherchez la section **Gérer**, puis sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, effectuez l’étape suivante :

    Dans la boîte **URL de connexion**, entrez une URL dont le modèle est le suivant : `https://login.signagelive.com/sso/<ORGANIZATIONALUNITNAME>`

    > [!NOTE]
    > Cette valeur n’est pas la valeur réelle. Mettez à jour la valeur avec l’URL de connexion réelle. Pour obtenir cette valeur, contactez [l’équipe de support client Signagelive](mailto:support@signagelive.com). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

5. Dans la page **Configurer l’authentification unique avec SAML**, à la section **Certificat de signature SAML**, sélectionnez **Télécharger** pour télécharger le **Certificat (brut)** en fonction des options définies. Enregistrez-le ensuite sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificateraw.png)

6. Dans la section **Configurer Signagelive**, copiez l’URL dont vous avez besoin.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Signagelive.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Signagelive**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-signagelive-sso"></a>Configurer l’authentification unique Signagelive

Pour configurer l’authentification unique côté Signagelive, envoyez le **certificat (brut)** téléchargé et les URL copiées depuis le portail Azure à [l’équipe du support technique Signagelive](mailto:support@signagelive.com). L’équipe vérifiera que l’authentification unique SAML est correctement configurée des deux côtés.

### <a name="create-signagelive-test-user"></a>Créer un utilisateur de test Signagelive

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans Signagelive. Contactez l’[équipe du support technique Signagelive](mailto:support@signagelive.com) pour ajouter des utilisateurs dans la plateforme Signagelive. Vous devez créer et activer les utilisateurs avant d’utiliser l’authentification unique.

Signagelive prend également en charge l’attribution automatique d’utilisateurs. Vous trouverez plus d’informations sur la configuration de cette fonctionnalité [ici](./signagelive-provisioning-tutorial.md).

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Cette opération redirige vers l’URL d’authentification Signagelive, à partir de laquelle vous pouvez lancer le processus de connexion. 

* Accédez directement à l’URL d’authentification Signagelive pour lancer le processus de connexion.

* Vous pouvez utiliser Mes applications de Microsoft. Quand vous cliquez sur la vignette Signagelive dans Mes applications, vous êtes redirigé vers l’URL d’authentification Signagelive. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez configuré Signagelive, vous pouvez appliquer le contrôle de session, qui protège l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
