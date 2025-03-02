---
title: 'Tutoriel : Intégration de l’authentification unique Azure Active Directory à Grovo | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Grovo.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/28/2019
ms.author: jeedes
ms.openlocfilehash: 82324eca3d638724f946885fe809f2aba261a692
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129229694"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-grovo"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory à Grovo

Dans ce tutoriel, vous allez découvrir comment intégrer Grovo à Azure Active Directory (Azure AD). Quand vous intégrez Grovo à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Grovo.
* Permettre à vos utilisateurs de se connecter automatiquement à Grovo avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS à Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Grovo pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* Grovo prend en charge l’authentification unique lancée par **le fournisseur de services et le fournisseur d’identité**
* Grovo prend en charge l’attribution d’utilisateurs **Juste-à-temps**

## <a name="adding-grovo-from-the-gallery"></a>Ajout de Grovo depuis la galerie

Pour configurer l’intégration de Grovo à Azure AD, vous devez ajouter Grovo, disponible dans la galerie, à votre liste d’applications SaaS managées.

1. Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Grovo** dans la zone de recherche.
1. Sélectionnez **Grovo** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-single-sign-on-for-grovo"></a>Configurer et tester l’authentification unique Azure AD pour Grovo

Configurez et testez l’authentification unique Azure AD avec Grovo à l’aide d’un utilisateur de test appelé **B. Simon**. Pour que l’authentification unique fonctionne, vous devez établir une relation entre un utilisateur Azure AD et l’utilisateur Grovo associé.

Pour configurer et tester l’authentification unique Azure AD avec Grovo, suivez les indications des sections ci-après :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Grovo](#configure-grovo-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test Grovo](#create-grovo-test-user)** pour obtenir dans Grovo un équivalent de B.Simon lié à la représentation Azure AD associée.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le [portail Azure](https://portal.azure.com/), accédez à la page d’intégration de l’application **Grovo**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de modification/stylet de **Configuration SAML de base** pour modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, si vous souhaitez configurer l’application en mode Initié par le **fournisseur d’identité**, entrez les valeurs pour les champs suivants :

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<subdomain>.grovo.com/sso/saml2/metadata`

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://<subdomain>.grovo.com/sso/saml2/saml-assertion`

    c. Cliquez sur **Définir des URL supplémentaires**.

    d. Dans la zone de texte **État de relais**, entrez une URL en utilisant le modèle suivant : `https://<subdomain>.grovo.com`

1. Cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes si vous souhaitez configurer l’application en mode lancé par le **fournisseur de services** :

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<subdomain>.grovo.com/sso/saml2/saml-assertion`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL de réponse, l’URL de connexion et l’État de relais exacts. Pour obtenir ces valeurs, contactez l’[équipe de support technique de Grovo](https://www.grovo.com/contact-us). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **Certificat (en base64)** , puis sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

1. Dans la section **Configurer Grovo**, copiez la ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Grovo.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Grovo**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.

   ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Lien Ajouter un utilisateur](common/add-assign-user.png)

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-grovo-sso"></a>Configurer l’authentification unique Grovo

1. Dans une autre fenêtre du navigateur web, connectez-vous à Grovo en tant qu’administrateur.

2. Accédez à **Admin** > **Integrations** (Intégrations).
 
    ![Capture d’écran montrant le menu « Admin » avec l’élément « Integrations » sélectionné.](./media/grovo-tutorial/tutorial_grovo_admin.png) 

3. Cliquez sur **SET UP** (CONFIGURER) sous la section **SP Initiated SAML 2.0** (SAML 2.0 initié SP).

    ![Capture d’écran montrant la section « SP initiated SAML 2.0 » avec le bouton « Set up » sélectionné.](./media/grovo-tutorial/tutorial_grovo_setup.png)

4. Dans la fenêtre contextuelle **SP initiated SAML 2.0**, effectuez les actions suivantes :

    ![Configuration de Grovo](./media/grovo-tutorial/tutorial_grovo_saml.png)

    a. Dans la zone de texte **Entity ID** (ID d’entité), collez la valeur de l’**identificateur Azure AD** que vous avez copiée à partir du portail Azure.

    b. Dans la zone de texte **Single sign-on service endpoint** (Point de terminaison du service d’authentification unique), collez la valeur de l’**URL de connexion** que vous avez copiée à partir du portail Azure.

    c. Sélectionnez **Single sign on service endpoint binding** (Liaison de point de terminaison du service d’authentification unique) comme étant `urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect`.
    
    d. Ouvrez le **Certificat codé en base 64** téléchargé à partir du portail Azure dans le bloc-notes, collez-le dans la zone de texte **Public key** (Clé publique).

    e. Cliquez sur **Suivant**.

### <a name="create-grovo-test-user"></a>Créer un utilisateur de test Grovo

Dans cette section, un utilisateur appelé B.Simon est créé dans Grovo. Grove prend en charge le provisionnement d’utilisateurs juste-à-temps, option activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas encore d’utilisateur dans Grovo, il en est créé un après l’authentification.

> [!Note]
> Si vous devez créer un utilisateur manuellement, contactez l’[équipe du support technique Grovo](https://www.grovo.com/contact-us).

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Le fait de cliquer sur la vignette Grovo dans le panneau d’accès doit vous connecter automatiquement à l’application Grovo pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de tutoriels sur l’intégration d’applications SaaS avec Azure Active Directory](./tutorial-list.md)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](../conditional-access/overview.md)

- [Essayer Grovo avec Azure AD](https://aad.portal.azure.com/)