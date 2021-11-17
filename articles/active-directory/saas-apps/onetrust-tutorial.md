---
title: 'Didacticiel : Intégration d’Azure Active Directory avec OneTrust Privacy Management Software | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et OneTrust Privacy Management Software.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/13/2021
ms.author: jeedes
ms.openlocfilehash: f68918e90f7a20d87e508f5c62e4b8bcd5af8e32
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132329967"
---
# <a name="tutorial-azure-active-directory-integration-with-onetrust-privacy-management-software"></a>Didacticiel : Intégration d’Azure Active Directory avec OneTrust Privacy Management Software

Dans ce tutoriel, vous allez apprendre à intégrer OneTrust Privacy Management Software à Azure Active Directory (Azure AD). Quand vous intégrez OneTrust Privacy Management Software à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à OneTrust Privacy Management Software.
* Permettre à vos utilisateurs de se connecter automatiquement à OneTrust Privacy Management Software avec leur compte Azure AD.
* gérer vos comptes à un emplacement central : le portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD avec OneTrust Privacy Management Software, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).
* Un abonnement OneTrust Privacy Management Software pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* OneTrust Privacy Management Software prend en charge l’authentification unique lancée par le **fournisseur de services** et le **fournisseur d’identité**

* OneTrust Privacy Management Software prend en charge l’attribution d’utilisateurs **juste-à-temps**

> [!NOTE]
> L’identificateur de cette application étant une valeur de chaîne fixe, une seule instance peut être configurée dans un locataire.

## <a name="add-onetrust-privacy-management-software-from-the-gallery"></a>Ajouter OneTrust Privacy Management Software à partir de la galerie

Pour configurer l’intégration de OneTrust Privacy Management Software à Azure AD, vous devez ajouter OneTrust Privacy Management Software, disponible dans la galerie, à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **OneTrust Privacy Management Software** dans la zone de recherche.
1. Sélectionnez **OneTrust Privacy Management Software** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-onetrust-privacy-management-software"></a>Configurer et tester l’authentification unique Azure AD pour OneTrust Privacy Management Software

Configurez et testez l’authentification unique (SSO) Azure AD avec OneTrust Privacy Management Software à l’aide d’un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique (SSO) fonctionne, vous devez établir une relation entre un utilisateur Azure AD et l’utilisateur OneTrust Privacy Management Software associé.

Pour configurer et tester l’authentification unique Azure AD avec OneTrust Privacy Management Software, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique (SSO) OneTrust Privacy Management Software](#configure-onetrust-privacy-management-software-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test OneTrust Privacy Management Software](#create-onetrust-privacy-management-software-test-user)** pour disposer, dans OneTrust Privacy Management Software, d’un équivalent de B.Simon lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD sur le portail Azure.
 
1. Dans la page d’intégration de l’application **OneTrust Privacy Management Software** du portail Azure, recherchez la section **Gérer**, puis sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, sélectionnez l’icône de crayon pour **Configuration SAML de base** afin de modifier les paramètres.

    ![Modifier la configuration SAML de base](common/edit-urls.png)

4. À la section **Configuration SAML de base**, si vous souhaitez configurer l’application en mode initié par **IDP**, suivez les étapes ci-dessous :

    a. Dans la zone de texte **Identificateur**, tapez une l’URL : `https://www.onetrust.com/saml2`

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://<subdomain>.onetrust.com/auth/consumerservice`

5. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

     Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<subdomain>.onetrust.com/auth/login`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de réponse et l’URL de connexion réelles. Contactez [l’équipe de support technique de OneTrust Privacy Management Software](mailto:support@onetrust.com) pour obtenir ces valeurs. Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

6. Sur la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le fichier **XML de métadonnées de fédération** en fonction des options définies selon vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

7. Dans la section **Configurer OneTrust Privacy Management Software**, copiez les URL appropriées, selon vos besoins.

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD 

Dans cette section, vous allez créer un utilisateur de test appelé B. Simon sur le portail Azure.
1. Dans le volet gauche du portail Azure, sélectionnez **Azure Active Directory** > **Utilisateurs** > **Tous les utilisateurs**.
1. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.
1. Dans les propriétés **Utilisateur**, effectuez les étapes suivantes :
   1. Dans le champ **Nom**, entrez `B.Simon`.  
   1. Dans le champ **Nom de l’utilisateur**, entrez username@companydomain.extension. Par exemple : `B.Simon@contoso.com`.
   1. Cochez la case **Afficher le mot de passe** et notez le mot de passe.
   1. Sélectionnez **Create** (Créer).

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à OneTrust Privacy Management Software.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise** > **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **OneTrust Privacy Management Software**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**. Ensuite, dans la boîte de dialogue **Ajouter une attribution**, sélectionnez **Utilisateurs et groupes**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B.Simon** dans la liste des utilisateurs. Choisissez **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, sélectionnez **Affecter**.

### <a name="configure-onetrust-privacy-management-software-sso"></a>Configurer l’authentification unique OneTrust Privacy Management Software

Pour configurer l’authentification unique côté **OneTrust Privacy Management Software**, vous devez envoyer le **XML des métadonnées de fédération** téléchargé et les URL copiées correspondantes à partir du portail Azure à l’[équipe du support technique OneTrust Privacy Management Software](mailto:support@onetrust.com). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

### <a name="create-onetrust-privacy-management-software-test-user"></a>Créer un utilisateur de test OneTrust Privacy Management Software

Dans cette section, un utilisateur appelé Britta Simon est créé dans OneTrust Privacy Management Software. OneTrust Privacy Management Software prend en charge l’attribution d’utilisateurs juste-à-temps, option activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas encore d’utilisateur dans OneTrust Privacy Management Software, il en est créé un après l’authentification.

>[!Note]
>Si vous devez créer manuellement un utilisateur, contactez l’[équipe de support OneTrust Privacy Management Software](mailto:support@onetrust.com).

### <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

#### <a name="sp-initiated"></a>Lancée par le fournisseur de services :

* Cliquez sur **Tester cette application** dans le portail Azure. Cette opération redirige vers l’URL d’authentification OneTrust Privacy Management Software, où vous pouvez lancer le processus de connexion.  
 
* Accédez directement à l’URL d’authentification OneTrust Privacy Management Software pour lancer le processus de connexion.

#### <a name="idp-initiated"></a>Lancée par le fournisseur d’identité :

* Dans le portail Azure, cliquez sur **Tester cette application**. Vous êtes alors automatiquement connecté à l’instance OneTrust Privacy Management Software pour laquelle vous avez configuré l’authentification unique. 

Vous pouvez aussi utiliser Mes applications de Microsoft pour tester l’application dans n’importe quel mode. Si, quand vous cliquez sur la vignette OneTrust Privacy Management Software dans Mes applications, le mode Fournisseur de services est configuré, vous êtes redirigé vers la page de connexion de l’application pour initier le flux de connexion ; s’il s’agit du mode Fournisseur d’identité, vous êtes automatiquement connecté à l’instance de OneTrust Privacy Management Software pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez configuré OneTrust Privacy Management Software, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
