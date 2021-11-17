---
title: 'Tutoriel : Intégration de l’authentification unique Azure Active Directory à Timeclock 365 SAML | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Timeclock 365 SAML.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/02/2021
ms.author: jeedes
ms.openlocfilehash: 191a41388b793cb087211f309e11214c437f9ee6
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132282497"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-timeclock-365-saml"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory à Timeclock 365 SAML

Dans ce tutoriel, vous allez apprendre à intégrer Timeclock 365 SAML à Azure Active Directory (Azure AD). Quand vous intégrez Timeclock 365 SAML à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Timeclock 365 SAML.
* Permettre à vos utilisateurs de se connecter automatiquement à Timeclock 365 SAML avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Timeclock 365 SAML pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* Timeclock 365 SAML prend en charge l’authentification unique lancée par le **fournisseur de services**.
* Timeclock 365 SAML prend en charge le [provisionnement d’utilisateurs automatisé](timeclock-365-provisioning-tutorial.md).

## <a name="adding-timeclock-365-saml-from-the-gallery"></a>Ajout de Timeclock 365 SAML à partir de la galerie

Pour configurer l’intégration de Timeclock 365 SAML à Azure AD, vous devez ajouter Timeclock 365 SAML à partir de la galerie à votre liste d’applications SaaS managées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Timeclock 365 SAML** dans la zone de recherche.
1. Sélectionnez **Timeclock 365 SAML** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-timeclock-365-saml"></a>Configurer et tester l’authentification unique Azure AD pour Timeclock 365 SAML

Configurez et testez l’authentification unique Azure AD avec Timeclock 365 SAML à l’aide d’un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Timeclock 365 SAML associé.

Pour configurer et tester l’authentification unique Azure AD avec Timeclock 365 SAML, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Timeclock 365 SAML](#configure-timeclock-365-saml-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test Timeclock 365 SAML](#create-timeclock-365-saml-test-user)** pour avoir un équivalent de B.Simon dans Timeclock 365 SAML lié à la représentation Azure AD associée.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **Timeclock 365 SAML**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, entrez les valeurs pour les champs suivants :

    Dans la zone de texte **URL de connexion**, tapez l’URL : `https://live.timeclock365.com/login`


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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Timeclock 365 SAML.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Timeclock 365 SAML**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-timeclock-365-saml-sso"></a>Configurer l’authentification unique Timeclock 365 SAML

1. Pour automatiser la configuration dans Timeclock 365 SAML, vous devez installer l’**extension de navigateur My Apps Secure Sign-in** en cliquant sur **Installer l’extension**.

    ![Extension My apps](common/install-myappssecure-extension.png)

2. Après l’ajout de l’extension au navigateur, cliquez sur **Configurer Timeclock 365 SAML** pour être dirigé vers l’application Timeclock 365 SAML. À partir de là, indiquez les informations d’identification de l’administrateur pour vous connecter à Timeclock 365 SAML. Cette extension de navigateur configure automatiquement l’application et automatise les étapes 3 et 4.

    ![Configuration](common/setup-sso.png)

3. Si vous souhaitez configurer Timeclock 365 SAML manuellement, dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Timeclock 365 SAML en tant qu’administrateur.

1. Effectuez les étapes indiquées ci-dessous.

    ![Configuration de Timeclock](./media/timeclock-365-saml-tutorial/saml-configuration.png)

    a. Accédez à l’onglet **Settings > Company profile > Settings**

    b. Dans la zone de texte **IDP metadata path** (Chemin des métadonnées IDP), collez l’**URL des métadonnées de fédération de l’application** que vous avez copiée à partir du portail Azure.

    c. Ensuite, cliquez sur **Update**.

### <a name="create-timeclock-365-saml-test-user"></a>Créer un utilisateur de test Timeclock 365 SAML

1. Ouvrez un nouvel onglet dans votre navigateur et connectez-vous à votre site d’entreprise Timeclock 365 SAML en tant qu’administrateur.

1. Accédez à **Users > Add new user**.

    ![Créer l’utilisateur de test 1 ](./media/timeclock-365-saml-tutorial/add-user-1.png)

1. Fournissez toutes les informations requises dans la page **User information**, puis cliquez sur **Save**.

    ![Créer l’utilisateur de test 2 ](./media/timeclock-365-saml-tutorial/add-user-2.png)

1. Cliquez sur le bouton **Create** pour créer l’utilisateur de test.

> [!NOTE]
> Timeclock 365 SAML prend aussi en charge le provisionnement automatique d’utilisateurs ; vous trouverez plus d’informations [ici](./timeclock-365-provisioning-tutorial.md) sur la manière de configurer ce dernier.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Cette opération redirige vers l’URL de connexion Timeclock 365 SAML, où vous pouvez lancer le processus de connexion. 

* Accédez directement à l’URL de connexion Timeclock 365 SAML pour y lancer le processus de connexion.

* Vous pouvez utiliser Mes applications de Microsoft. Quand vous cliquez sur la vignette Timeclock 365 SAML dans Mes applications, vous êtes redirigé vers l’URL de connexion Timeclock 365 SAML. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez configuré Timeclock 365 SAML, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
