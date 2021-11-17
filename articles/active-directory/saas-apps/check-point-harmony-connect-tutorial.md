---
title: 'Tutoriel : Intégration de l’authentification unique (SSO) Azure Active Directory à Check Point Harmony Connect | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Check Point Harmony Connect.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/04/2021
ms.author: jeedes
ms.openlocfilehash: b7ba48cb534ebd0ec2df6038b0b5df49ce2b0e88
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132311891"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-check-point-harmony-connect"></a>Tutoriel : Intégration de l’authentification unique (SSO) Azure Active Directory à Check Point Harmony Connect

Dans ce tutoriel, vous allez découvrir comment intégrer Check Point Harmony Connect à Azure Active Directory (Azure AD). Quand vous intégrez Check Point Harmony Connect à Azure AD, vous pouvez :

* Contrôler, dans Azure AD, qui a accès à Check Point Harmony Connect.
* Permettre aux utilisateurs de se connecter automatiquement à Check Point Harmony Connect à l’aide de leurs comptes Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Check Point Harmony Connect pour lequel l’authentification SSO (authentification unique) est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* Check Point Harmony Connect prend en charge l’authentification SSO lancée par le fournisseur de services (**SP**).
> [!NOTE]
> L’identificateur de cette application étant une valeur de chaîne fixe, une seule instance peut être configurée dans un locataire.


## <a name="adding-check-point-harmony-connect-from-the-gallery"></a>Ajout de Check Point Harmony Connect à partir de la galerie

Pour configurer l’intégration de Check Point Harmony Connect à Azure AD, vous devez ajouter Check Point Harmony Connect à votre liste d’applications SaaS managées, à partir de la galerie.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, dans la zone de recherche, tapez **Check Point Harmony Connect**.
1. Sélectionnez **Check Point Harmony Connect** dans le panneau Résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.


## <a name="configure-and-test-azure-ad-sso-for-check-point-harmony-connect"></a>Configurer et tester l’authentification SSO Azure AD pour Check Point Harmony Connect

Configurez et testez l’authentification SSO Azure AD avec Check Point Harmony Connect pour une utilisatrice de test appelée **B.Simon**. Pour que l’authentification SSO fonctionne, vous devez établir une relation entre un utilisateur Azure AD et l’utilisateur associé dans Check Point Harmony Connect.

Pour configurer et tester l’authentification SSO Azure AD avec Check Point Harmony Connect, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification SSO pour Check Point Harmony Connect](#configure-check-point-harmony-connect-sso)** afin de configurer les paramètres d’authentification unique côté application.
    1. **[Créer une utilisatrice de test pour Check Point Harmony Connect](#create-check-point-harmony-connect-test-user)** afin de disposer dans Check Point Harmony Connect d’un équivalent de B.Simon lié à la représentation Azure AD de l’utilisatrice.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, dans la page d’intégration de l’application **Check Point Harmony Connect**, recherchez la section **Gérer**, puis sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, entrez les valeurs pour les champs suivants :

    Dans la zone de texte **URL de connexion**, tapez l’URL :  `https://cloudinfra-gw.portal.checkpoint.com/api/saml/sso`

1. L’application Check Point Harmony Connect attend les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à votre configuration d’attributs de jetons SAML. La capture d’écran suivante montre la liste des attributs par défaut.

    ![image](common/default-attributes.png)

1. En plus de ce qui précède, l’application Check Point Harmony Connect s’attend à ce que quelques attributs supplémentaires soient repassés dans la réponse SAML, comme indiqué ci-dessous. Ces attributs sont également préremplis, mais vous pouvez les examiner pour voir s’ils répondent à vos besoins.
    
    | Nom |  Attribut source|
    | ---------------- | --------- |
    | groups | user.groups |

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **XML de métadonnées de fédération** et sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

1. Dans la section **Configurer Check Point Harmony Connect**, copiez l’URL ou les URL appropriées selon vos besoins.

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

Dans cette section, vous allez permettre à B.Simon d’utiliser l’authentification unique Azure en lui octroyant l’accès à Check Point Harmony Connect.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Check Point Harmony Connect**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-check-point-harmony-connect-sso"></a>Configurer l’authentification SSO pour Check Point Harmony Connect

1. Connectez-vous à votre site web Check Point Harmony Connect en tant qu’administrateur.

1. Cliquez sur **SETTINGS** (PARAMÈTRES), accédez à **Identity Provider** (Fournisseur d’identité), puis cliquez sur **CONNECT NOW** (SE CONNECTER MAINTENANT).

    ![capture d’écran de la section Identity Provider.](./media/check-point-harmony-connect-tutorial/identity-provider.png)

1. Sélectionnez **Microsoft Azure AD** en tant que fournisseur d’identité, puis cliquez sur **NEXT** (SUIVANT).

    ![capture d’écran montrant la sélection du fournisseur d’identité.](./media/check-point-harmony-connect-tutorial/select-identity-provider.png)

1. Dans la page **Vérifier le domaine**, entrez le domaine de votre organisation, entrez l’enregistrement DNS généré sur votre serveur DNS en tant qu’enregistrement TXT, puis cliquez sur **NEXT**.

    ![capture d’écran de la valeur de Domain (Domaine).](./media/check-point-harmony-connect-tutorial/domain.png)

1. Dans la page Allow Connectivity (Autoriser la connectivité), effectuez les étapes suivantes :

    ![capture d’écran de la page Allow Connectivity.](./media/check-point-harmony-connect-tutorial/allow-connectivity.png)

    a. Copiez la valeur de **ENTITY ID** (ID D’ENTITÉ), puis collez-la dans la zone de texte **Identificateur** de la section **Configuration SAML de base** du portail Azure.

    b. Copiez la valeur de **REPLY URL** (URL de réponse), collez-la dans la zone de texte **URL de réponse** de la section **Configuration SAML de base** du portail Azure.

    c. Cliquez sur **NEXT**.

1. Dans la page **Configure Metadata** (Configurer les métadonnées), chargez le **fichier XML de métadonnées de fédération** que vous avez téléchargé à partir du portail Azure.

1. Dans la page **CONFIRM IDENTITY PROVIDER** (CONFIRMER LE FOURNISSEUR D’IDENTITÉ), cliquez sur **Ajouter** pour effectuer la configuration.

### <a name="create-check-point-harmony-connect-test-user"></a>Créer l’utilisateur de test Check Point Harmony Connect

1. Connectez-vous à votre site web Check Point Harmony Connect en tant qu’administrateur.

1. Accédez à **Policy (Stratégie)**  -> **Access Control (Contrôle d’accès)** , créez une **règle**, puis cliquez sur **(+)** pour ajouter un **nouvel utilisateur**. 

    ![capture d’écran montrant la création d’un utilisateur.](./media/check-point-harmony-connect-tutorial/add-new-user.png)

1. Dans la fenêtre **ADD USER** (AJOUTER UN UTILISATEUR), entrez le nom et le nom d’utilisateur appropriés dans les zones de texte respectives, puis cliquez sur **ADD** (AJOUTER).

    ![capture d’écran montrant la création d’ utilisateur.](./media/check-point-harmony-connect-tutorial/add-user.png)

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Pour tester Check Point Harmony Connect, accédez au service d’authentification correspondant, puis authentifiez-vous à l’aide du compte de test que vous avez créé dans la section **Créer un utilisateur de test Azure AD**.

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez configuré Check Point Harmony Connect, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
