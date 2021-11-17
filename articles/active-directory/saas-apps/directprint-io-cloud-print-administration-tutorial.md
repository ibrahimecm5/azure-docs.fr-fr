---
title: 'Tutoriel : Intégration de l’authentification unique Azure Active Directory à directprint.io Cloud Print Administration | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et directprint.io Cloud Print Administration.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/30/2021
ms.author: jeedes
ms.openlocfilehash: 0b8e465c23762f366c3da6ce46b16d361121487c
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132314607"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-directprintio-cloud-print-administration"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory à directprint.io Cloud Print Administration

Dans ce tutoriel, vous allez apprendre à intégrer directprint.io Cloud Print Administration à Azure Active Directory (Azure AD). Quand vous intégrez directprint.io Cloud Print Administration à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à directprint.io Cloud Print Administration.
* Permettre à vos utilisateurs de se connecter automatiquement à directprint.io Cloud Print Administration avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement directprint.io Cloud Print Administration pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* directprint.io Cloud Print Administration prend en charge l’authentification unique initiée par le **fournisseur d’identité**.

* directprint.io Cloud Print Administration prend en charge l’approvisionnement d’utilisateurs **Juste-à-temps**.

## <a name="add-directprintio-cloud-print-administration-from-the-gallery"></a>Ajouter directprint.io Cloud Print Administration à partir de la galerie

Pour configurer l’intégration de directprint.io Cloud Print Administration dans Azure AD, vous devez ajouter directprint.io Cloud Print Administration à partir de la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, saisissez **directprint.io Cloud Print Administration** dans la zone de recherche.
1. Sélectionnez **directprint.io Cloud Print Administration** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-directprintio-cloud-print-administration"></a>Configurer et tester l’authentification unique Azure AD pour directprint.io Cloud Print Administration

Configurez et testez l’authentification unique Azure AD avec directprint.io Cloud Print Administration à l’aide d’un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur associé dans directprint.io Cloud Print Administration.

Pour configurer et tester l’authentification unique Azure AD avec directprint.io Cloud Print Administration, procédez comme suit :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique de directprint.io Cloud Print Administration](#configure-directprintio-cloud-print-administration-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test directprint.io Cloud Print Administration](#create-directprintio-cloud-print-administration-test-user)** pour avoir un équivalent de B.Simon dans directprint.io Cloud Print Administration lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **directprint.io Cloud Print Administration**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, l’application est préconfigurée en mode Lancée par le fournisseur d’identité et les URL nécessaires sont déjà préremplies avec Azure. L’utilisateur doit enregistrer la configuration en cliquant sur le bouton **Enregistrer**.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur le bouton Copier pour copier l’**URL des métadonnées de fédération d’application**, puis enregistrez-la sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/copy-metadataurl.png)

1. Dans la section **Configurer directprint.io Cloud Print Administration**, copiez les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à directprint.io Cloud Print Administration.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **directprint.io Cloud Print Administration**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-directprintio-cloud-print-administration-sso"></a>Configurer l’authentification unique de directprint.io Cloud Print Administration

Pour configurer l’authentification unique côté **directprint.io Cloud Print Administration**, vous devez envoyer l’**URL des métadonnées de la fédération de l’application** à l’[équipe du support technique de directprint.io Cloud Print Administration](mailto:support@directprint.io). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

### <a name="create-directprintio-cloud-print-administration-test-user"></a>Créer un utilisateur de test directprint.io Cloud Print Administration

Dans cette section, un utilisateur appelé B.Simon est créé dans directprint.io Cloud Print Administration. directprint.io Cloud Print Administration prend en charge l’approvisionnement d’utilisateurs Juste-à-temps activé par défaut. Vous n’avez aucune opération à effectuer dans cette section. Si un utilisateur n’existe pas déjà dans directprint.io Cloud Print Administration, un nouveau est créé après l’authentification.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes.

* Cliquez sur Tester cette application dans Portail Azure : vous devez être connecté automatiquement à l’instance de directprint.io Cloud Print Administration pour laquelle vous avez configuré l’authentification unique.

* Vous pouvez utiliser Mes applications de Microsoft. Quand vous cliquez sur la vignette directprint.io Cloud Print Administration dans Mes applications, vous êtes automatiquement connecté à l’instance de directprint.io Cloud Print Administration pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez configuré directprint.io Cloud Print Administration, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
