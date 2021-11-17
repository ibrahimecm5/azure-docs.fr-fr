---
title: 'Tutoriel : Intégration de l’authentification unique (SSO) Azure Active Directory à Zscaler B2B User Portal | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Zscaler B2B User Portal.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/08/2021
ms.author: jeedes
ms.openlocfilehash: 4d0af3b72d9a175c44e43d8a1ca0281e4bdab5f5
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132340473"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-zscaler-b2b-user-portal"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory à Zscaler B2B User Portal

Dans ce tutoriel, vous allez découvrir comment intégrer Zscaler B2B User Portal à Azure AD (Azure Active Directory). Quand vous intégrez Zscaler B2B User Portal à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Zscaler B2B User Portal.
* Permettre aux utilisateurs de se connecter automatiquement à Zscaler B2B User Portal avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Zscaler B2B User Portal pour lequel l’authentification unique est activée.

> [!NOTE]
> Cette intégration peut également être utilisée à partir de l’environnement cloud US Government Azure AD. Cette application est disponible dans la Galerie d’applications cloud US Government Azure AD et peut être configurée de la même façon que dans le cloud public.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* Zscaler B2B User Portal prend en charge l’authentification unique lancée par le **fournisseur d’identité**.

* Zscaler B2B User Portal prend en charge l’affectation d’utilisateurs **juste-à-temps**.

## <a name="add-zscaler-b2b-user-portal-from-the-gallery"></a>Ajouter Zscaler B2B User Portal à partir de la galerie

Pour configurer l’intégration de Zscaler B2B User Portal à Azure AD, vous devez ajouter Zscaler B2B User Portal, disponible dans la galerie, à votre liste d’applications SaaS managées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Zscaler B2B User Portal** dans la zone de recherche.
1. Sélectionnez **Zscaler B2B User Portal** dans le panneau des résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-zscaler-b2b-user-portal"></a>Configurer et tester l’authentification unique Azure AD pour Zscaler B2B User Portal

Configurez et testez l’authentification unique Azure AD avec Zscaler B2B User Portal pour un utilisateur de test nommé **B. Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Zscaler B2B User Portal associé.

Pour configurer et tester l’authentification unique Azure AD avec Zscaler B2B User Portal, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Zscaler B2B User Portal](#configure-zscaler-b2b-user-portal-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test Zscaler B2B User Portal](#create-zscaler-b2b-user-portal-test-user)** pour avoir un équivalent de B.Simon dans Zscaler B2B User Portal, associé à sa représentation dans Azure AD.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Sur le portail Azure, dans la page d’intégration de l’application **Zscaler B2B User Portal**, recherchez la section **Gérer**, puis sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la page **Configurer l’authentification unique avec SAML**, effectuez les étapes suivantes :

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://samlsp.private.zscaler.com/auth/metadata/<UniqueID>`

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://samlsp.private.zscaler.com/auth/login?domain=EXAMPLE`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur et l’URL de réponse réels. Pour obtenir ces valeurs, contactez l’[équipe du support client de Zscaler B2B User Portal](https://help.zscaler.com/). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **XML de métadonnées de fédération** et sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

1. Dans la section **Configurer Zscaler B2B User Portal**, copiez la ou les URL appropriées à vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Zscaler B2B User Portal.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Zscaler B2B User Portal**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-zscaler-b2b-user-portal-sso"></a>Configurer l’authentification unique Zscaler B2B User Portal

1. Ouvrez une nouvelle fenêtre de navigateur web et connectez-vous à votre site d’entreprise Zscaler B2B User Portal en tant qu’administrateur, puis effectuez les étapes suivantes :

1. En haut, cliquez sur **Administration** et accédez à la section **AUTHENTICATION** puis cliquez sur **IdP Configuration**.

    ![Administrateur Zscaler Private Access - Administration](./media/zscaler-b2b-user-tutorial/tutorial-zscaler-private-access-administration.png)

1. Dans le coin supérieur droit, cliquez sur **Ajouter une configuration IdP**. 

    ![Administrateur Zscaler Private Access - idp](./media/zscaler-b2b-user-tutorial/tutorial-zscaler-private-access-idp.png)

1. Sur la **page Ajouter une configuration IdP**, procédez comme suit :
 
    ![Administrateur Zscaler Private Access - Sélection](./media/zscaler-b2b-user-tutorial/tutorial-zscaler-private-access-select.png)

    a. Cliquez sur **Sélectionner fichier** pour charger le fichier de métadonnées téléchargé depuis Azure AD dans le champ **Charger fichier de métadonnée IdP**.

    b. Les **métadonnées IdP** sont lu à partir d’Azure AD et tous les champs sont renseignés, comme montré ci-dessous.

    ![Administrateur Zscaler Private Access - Configuration](./media/zscaler-b2b-user-tutorial/config.png)

    c. Sélectionnez votre domaine dans le champ **Domaines**.
    
    d. Cliquez sur **Enregistrer**.

### <a name="create-zscaler-b2b-user-portal-test-user"></a>Créer un utilisateur de test Zscaler B2B User Portal

Dans cette section, un utilisateur appelé Britta Simon est créé dans Zscaler B2B User Portal. Zscaler B2B User Portal prend en charge le provisionnement d’utilisateurs juste-à-temps, qui est activé par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas encore d’utilisateur dans Zscaler B2B User Portal, il en est créé un après l’authentification.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes.

* Cliquez sur Tester cette application dans le portail Azure. Vous êtes alors automatiquement connecté à l’instance de Zscaler B2B User Portal pour laquelle vous avez configuré l’authentification unique.

* Vous pouvez utiliser Mes applications de Microsoft. Le fait de cliquer sur la vignette Zscaler B2B User Portal dans Mes applications vous connecte automatiquement à l’instance de Zscaler B2B User Portal pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré Zscaler B2B User Portal, vous pouvez appliquer le contrôle de session, qui protège en temps réel contre l’exfiltration et l’infiltration des données sensibles de votre organisation. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
