---
title: 'Didacticiel : Intégration d’Azure Active Directory à SpaceIQ | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et SpaceIQ.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/02/2021
ms.author: jeedes
ms.openlocfilehash: 110f7bf02ba85a64a2978b23d5f43434d34b43b8
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132282896"
---
# <a name="tutorial-azure-active-directory-integration-with-spaceiq"></a>Didacticiel : Intégration d’Azure AD avec SpaceIQ

Dans ce didacticiel, vous allez apprendre à intégrer SpaceIQ à Azure Active Directory (Azure AD). Quand vous intégrez SpaceIQ à Azure AD, vous pouvez :

* Dans Azure AD, contrôler qui a accès à SpaceIQ.
* Permettre à vos utilisateurs de se connecter automatiquement à SpaceIQ avec leurs comptes Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à SpaceIQ, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un [compte gratuit](https://azure.microsoft.com/free/).
* Abonnement à SpaceIQ pour lequel l’authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* SpaceIQ prend en charge l’authentification unique lancée par le **point de distribution d’émission**.
* SpaceIQ prend en charge le [provisionnement d’utilisateurs automatisé](spaceiq-provisioning-tutorial.md).

> [!NOTE]
> L’identificateur de cette application étant une valeur de chaîne fixe, une seule instance peut être configurée dans un locataire.

## <a name="add-spaceiq-from-the-gallery"></a>Ajouter SpaceIQ à partir de la galerie

Pour configurer l’intégration de SpaceIQ avec Azure AD, vous devez ajouter SpaceIQ, disponible dans la galerie, à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **SpaceIQ** dans la zone de recherche.
1. Sélectionnez **SpaceIQ** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-spaceiq"></a>Configurer et tester l’authentification unique Azure AD pour SpaceIQ

Configurez et testez l’authentification SSO Azure AD avec SpaceIQ à l’aide d’une utilisatrice de test appelée **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur SpaceIQ associé.

Pour configurer et tester l’authentification unique Azure AD avec SpaceIQ, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique SpaceIQ](#configure-spaceiq-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test SpaceIQ](#create-spaceiq-test-user)** pour avoir un équivalent de B.Simon dans SpaceIQ lié à la représentation Azure Active Directory associée.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **SpaceIQ**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Sur la page **Configurer l’authentification unique avec SAML**, effectuez les étapes suivantes :

    a. Dans la zone de texte **Identificateur**, tapez une l’URL : `https://api.spaceiq.com`

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://api.spaceiq.com/saml/<INSTANCE_ID>/callback`

    > [!NOTE]
    > Mettez à jour ces valeur avec l’URL de réponse et l’identificateur réels. La procédure est expliquée plus loin dans le didacticiel.

5. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (Base64)** en fonction des options définies par rapport à vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

6. Dans la section **Configurer SpaceIQ**, copiez la ou les URL appropriées en fonction de vos exigences.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à SpaceIQ.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **SpaceIQ**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-spaceiq-sso"></a>Configurer l’authentification unique SpaceIQ

1. Ouvrez une nouvelle fenêtre de navigateur, puis connectez-vous au site de votre entreprise SpaceIQ en tant qu’administrateur.

1. Une fois que vous êtes connecté, cliquez sur le signe de puzzle en haut à droite, puis cliquez sur **Intégration**

    ![Paramètres de compte](./media/spaceiq-tutorial/setting.png) 

1. Sous **All PROVISIONING & SSO**, cliquez sur la vignette **Azure** pour ajouter une instance d’Azure comme fournisseur d’identité.

    ![Icône SAML](./media/spaceiq-tutorial/azure.png)

1. Dans la boîte de dialogue **SSO**, effectuez les étapes suivantes :

    ![Paramètres d’authentification SAML](./media/spaceiq-tutorial/configuration.png)

    a. Dans la zone **URL de l’émetteur SAML**, collez la valeur **Identificateur Azure Active Directory** copiée à partir de la fenêtre Configuration de l’application Azure Active Directory.

    b. Copiez la valeur **URL du point de terminaison de rappel SAML (en lecture seule)** et collez-la dans la zone **URL de réponse** dans la section **Configuration SML de base** du portail Azure.

    c. Copiez la valeur **URI de l’audience SAML (en lecture seule)** et collez-la dans la zone **Identificateur** dans la section **Configuration SML de base** du portail Azure.

    d. Ouvrez le fichier de certificat que vous avez téléchargé dans le Bloc-notes, copiez son contenu, puis collez-le dans la zone **Certificat X.509**.

    e. Cliquez sur **Enregistrer**.

### <a name="create-spaceiq-test-user"></a>Créer un utilisateur de test SpaceIQ

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans SpaceIQ. Collaborez avec l’[équipe de support technique de SpaceIQ](mailto:eng@spaceiq.com) pour ajouter les utilisateurs dans la plateforme SpaceIQ. Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique.

SpaceIQ prend également en charge le provisionnement automatique d’utilisateurs ; vous trouverez plus d’informations [ici](./spaceiq-provisioning-tutorial.md) sur la façon de configurer ce dernier.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes.

* Sur le portail Azure, cliquez sur Tester cette application. Vous êtes alors automatiquement connecté à l’instance de SpaceIQ pour laquelle vous avez configuré l’authentification unique.

* Vous pouvez utiliser Mes applications de Microsoft. Lorsque vous cliquez sur la vignette SpaceIQ dans Mes applications, vous devez être connecté automatiquement à l’application SpaceIQ pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez configuré SpaceIQ, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
