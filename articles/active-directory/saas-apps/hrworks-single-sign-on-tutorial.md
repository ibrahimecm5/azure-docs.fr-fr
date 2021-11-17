---
title: 'Tutoriel : Intégration de l’authentification unique (SSO) Azure Active Directory avec HRworks Single Sign-On | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et HRworks Single Sign-On.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/29/2021
ms.author: jeedes
ms.openlocfilehash: 43e67da4ed97ea188a3cd227ac34f8f5628b8cf4
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132317553"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-hrworks-single-sign-on"></a>Tutoriel : Intégration de l’authentification unique (SSO) Azure Active Directory avec HRworks Single Sign-On

Ce tutoriel explique comment intégrer HRworks Single Sign-On avec Azure Active Directory (Azure AD). Quand vous intégrez HRworks Single Sign-On avec Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à HRworks Single Sign-On.
* Permettre à vos utilisateurs de se connecter automatiquement à HRworks Single Sign-On avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Abonnement HRworks Single Sign-On pour lequel l’authentification unique (SSO) est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* HRworks Single Sign-On prend en charge l’authentification unique lancée par le **fournisseur de services**.

> [!NOTE]
> L’identificateur de cette application étant une valeur de chaîne fixe, une seule instance peut être configurée dans un locataire.

## <a name="add-hrworks-single-sign-on-from-the-gallery"></a>Ajouter HRworks Single Sign-On à partir de la galerie

Pour configurer l’intégration de HRworks Single Sign-On à Azure AD, vous devez ajouter HRworks Single Sign-On à partir de la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **HRworks Single Sign-On** dans la zone de recherche.
1. Sélectionnez **HRworks Single Sign-On** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-hrworks-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD pour HRworks Single Sign-On

Configurez et testez l’authentification unique Azure AD avec HRworks Single Sign-On à l’aide d’un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur HRworks Single Sign-On associé.

Pour configurer et tester l’authentification unique Azure AD avec HRworks Single Sign-On, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique HRworks Single Sign-On](#configure-hrworks-single-sign-on-sso)** – pour configurer les paramètres d’authentification unique côté application.
    1. **[Créer un utilisateur de test HRworks Single Sign-On](#create-hrworks-single-sign-on-test-user)** – pour avoir un équivalent de B.Simon dans HRworks Single Sign-On qui soit lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le Portail Azure, accédez à la page d’intégration de l’application **HRworks Single Sign-On**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, effectuez l’étape suivante :

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://login.hrworks.de/?companyId=<COMPANY_ID>&directssologin=true`

    > [!NOTE]
    > Cette valeur n’est pas la valeur réelle. Mettez à jour la valeur avec l’URL de connexion réelle. Pour obtenir cette valeur, contactez l’[équipe du support technique HRworks Single Sign-On](https://www.hrworks.de/dienstleistungen/support/). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **XML de métadonnées de fédération** et sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

1. Dans la section **Configurer HRworks Single Sign-On**, copiez la ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à HRworks Single Sign-On.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **HRworks Single Sign-On**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-hrworks-single-sign-on-sso"></a>Configurer l’authentification unique HRworks Single Sign-On

1. Pour automatiser la configuration dans HRworks Single Sign-On, vous devez installer l’**extension de navigateur de connexion sécurisée à Mes applications** en cliquant sur **Installer l’extension**.

    ![Extension My apps](common/install-myappssecure-extension.png)

1. Après l’ajout de l’extension au navigateur, cliquez sur **Configurer HRworks Single Sign-On** pour être dirigé vers l’application HRworks Single Sign-On. À partir de là, fournissez les informations d’identification de l’administrateur pour vous connecter à HRworks Single Sign-On. Cette extension de navigateur configure automatiquement l’application et automatise les étapes 3 et 4.

    ![Configuration](common/setup-sso.png)

1. Si vous souhaitez configurer manuellement HRworks Single Sign-On, ouvrez une nouvelle fenêtre de navigateur web, connectez-vous à votre site d’entreprise HRworks Single Sign-On en tant qu’administrateur, puis effectuez les étapes suivantes :

1. Cliquez sur **Administrator** > **Basics** > **Security** > **Single Sign-on** (Authentification unique) sur le côté gauche de la barre de menus, puis effectuez les étapes suivantes :

    ![Configurer l’authentification unique](./media/hrworks-single-sign-on-tutorial/configure.png)

    a. Cochez la case **Use Single Sign-on** (Utiliser l’authentification unique).

    b. Sélectionnez **XML Metadata** (Métadonnées XML) comme **méthode d’entrée des métadonnées**.

    c. Sélectionnez **Individual NameID identifier** (Identificateur NameID individuel) comme **valeur pour NameID**.

    d. Dans le Bloc-notes, ouvrez le XML des métadonnées que vous avez téléchargé à partir du portail Azure, copiez son contenu, puis collez-le dans la zone de texte **Metadata** (Métadonnées).

    e. Cliquez sur **Enregistrer**.

### <a name="create-hrworks-single-sign-on-test-user"></a>Créer un utilisateur de test HRworks Single Sign-On

Pour permettre aux utilisateurs Azure AD de se connecter à HRworks Single Sign-On, ils doivent y être provisionnés. Dans HRworks Single Sign-On, le provisionnement est une tâche manuelle.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous à HRworks Single Sign-On en tant qu’administrateur.

1. Cliquez sur **Administrator** > **Persons** > **Persons** > **New person** (Nouvelle personne) sur le côté gauche de la barre de menus.

     ![Capture d’écran montrant la page HRworks avec les Persons et New person sélectionnés.](./media/hrworks-single-sign-on-tutorial/persons.png)

1. Dans le menu contextuel, cliquez sur **Next** (Suivant).

    ![Capture d’écran montrant une liste de pays que vous pouvez sélectionner pour la personne.](./media/hrworks-single-sign-on-tutorial/new-person.png)

1. Dans la fenêtre contextuelle **Create new person with country for legal terms** (Créer une personne avec un pays pour les conditions juridiques), remplissez les détails respectifs, tels que **First name**(Prénom) et **Last name** (Nom), puis cliquez sur **Create**.

    ![Capture d’écran montrant les zones de texte où vous pouvez entrer le prénom et le nom de la personne.](./media/hrworks-single-sign-on-tutorial/create-person.png)

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion à HRworks Single Sign-On, à partir de laquelle vous pouvez lancer le flux de connexion. 

* Accédez directement à l’URL de connexion à HRworks Single Sign-On pour lancer le flux de connexion.

* Vous pouvez utiliser Mes applications de Microsoft. Quand vous cliquez sur la vignette HRworks Single Sign-On dans Mes applications, vous êtes redirigé vers l’URL de connexion à HRworks Single Sign-On. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré HRworks Single Sign-on, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
