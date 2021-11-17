---
title: 'Tutoriel : Intégration de l’authentification unique Azure AD à 10,000ft Plans'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et 10,000ft Plans.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/29/2021
ms.author: jeedes
ms.openlocfilehash: a521736d32b51b5a4b6c22292e7dd6e60c4c0248
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132335211"
---
# <a name="tutorial-azure-ad-sso-integration-with-10000ft-plans"></a>Tutoriel : Intégration de l’authentification unique Azure AD à 10,000ft Plans

Dans ce tutoriel, vous allez découvrir comment intégrer 10,000ft Plans à Azure Active Directory (Azure AD). Quand vous intégrez 10,000ft Plans à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à 10,000ft Plans.
* Permettre à vos utilisateurs de se connecter automatiquement à 10,000ft Plans avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD avec 10,000ft Plans, vous devez disposer des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un [compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement à 10,000ft Plans pour lequel l’authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* 10,000ft Plans prend en charge l’authentification unique lancée par le **fournisseur de services**.
* 10,000ft Plans prend en charge le provisionnement **juste-à-temps** des utilisateurs.

## <a name="add-10000ft-plans-from-the-gallery"></a>Ajouter 10,000ft Plans à partir de la galerie

Pour configurer l’intégration de 10,000ft Plans à Azure AD, vous devez ajouter 10,000ft Plans à partir de la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **10,000ft Plans** dans la zone de recherche.
1. Sélectionnez **10,000ft Plans** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-10000ft-plans"></a>Configurer et tester l’authentification unique Azure AD pour 10,000ft Plans

Configurez et testez l’authentification unique Azure AD avec 10,000ft Plans pour une utilisatrice de test appelée **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur associé dans 10,000ft Plans.

Pour configurer et tester l’authentification unique Azure AD avec 10,000ft Plans, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique 10,000ft Plans](#configure-10000ft-plans-sso)** pour configurer les paramètres d’authentification unique côté application.
    1. **[Créer un utilisateur de test 10,000ft Plans](#create-10000ft-plans-test-user)** pour avoir un équivalent de B.Simon dans 10,000ft Plans lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans la page d’intégration de l’application **10,000ft Plans** du portail Azure, recherchez la section **Gérer**, puis sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    a. Dans la zone de texte **Identificateur (ID d’entité)** , tapez l’URL suivante : `https://rm.smartsheet.com/saml/metadata`

    b. Dans la zone **URL de réponse**, tapez l’URL : `https://rm.smartsheet.com/saml/acs`
    
    c. Dans la zone de texte **URL de connexion**, tapez l’URL : ` https://rm.smartsheet.com`

    > [!NOTE]
    > La valeur de **l’identificateur** est différente si vous avez un domaine personnalisé. Contactez l’[équipe du support 10,000ft Plans](https://www.10000ft.com/plans/support) pour obtenir cette valeur. Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, sélectionnez l’icône de copie pour copier l’**URL des métadonnées de fédération d’application**. Enregistrez-le sur votre ordinateur.

    ![Capture d’écran du certificat de signature SAML, avec l’icône de copie mise en évidence](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

Dans cette section, vous allez créer un utilisateur de test appelé B. Simon dans le portail Azure.

1. Dans le portail Azure, dans le volet **Services Azure**, sélectionnez **Utilisateurs**, puis sélectionnez **Tous les utilisateurs**.
1. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.
1. Dans les propriétés **Utilisateur**, effectuez les étapes suivantes :
   1. Dans le champ **Nom**, entrez `B.Simon`.  
   1. Dans le champ **Nom de l’utilisateur**, entrez username@companydomain.extension. Par exemple : `B.Simon@contoso.com`.
   1. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.
   1. Cliquez sur **Créer**.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à 10,000ft Plans.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **10,000ft Plans**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur/groupe**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-10000ft-plans-sso"></a>Configurer l’authentification unique de 10,000ft Plans

1. Connectez-vous à votre site web 10000ft Plans en tant qu’administrateur.

1. Cliquez sur **Settings** et sélectionnez **Account Settings** dans la liste déroulante.

    ![Capture d’écran de l’icône Settings.](./media/10000ftplans-tutorial/settings.png)

1. Cliquez sur **SSO** dans le menu de gauche et effectuez les étapes suivantes :

    ![Capture d’écran de la page Settings SSO.](./media/10000ftplans-tutorial/setup-sso.png)

    a. Sélectionnez **Automatic Configuration** dans la section Setup SSO.

    b. Dans la zone de texte **IdP Metadata URL**, entrez la valeur **App Federation Metadata Url** que vous avez copiée à partir du portail Azure.

    c. Activez la case à cocher **Auto-provision authenticated users not in account**.

    d. Cliquez sur **Enregistrer**.

### <a name="create-10000ft-plans-test-user"></a>Créer un utilisateur de test 10000ft Plans

Dans cette section, un utilisateur appelé Britta Simon est créé dans 10000ft Plans. 10000ft Plans prend en charge le provisionnement d’utilisateurs juste-à-temps, option activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe encore aucun utilisateur dans 10,000ft Plans, il en est créé un après l’authentification.

> [!NOTE]
> Si vous devez créer un utilisateur manuellement, contactez l’[équipe du support 10,000ft Plans](https://www.10000ft.com/plans/support).

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion à 10,000ft Plans, où vous pouvez lancer le flux de connexion. 

* Accédez directement à l’URL de connexion à 10,000ft Plans et lancez le flux de connexion.

* Vous pouvez utiliser Mes applications de Microsoft. Quand vous cliquez sur la vignette 10,000ft Plans dans le dossier Mes applications, vous êtes redirigé vers l’URL de connexion à 10,000ft Plans. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré 10,000ft Plans, vous pouvez appliquer le contrôle de session, qui protège en temps réel contre l’exfiltration et l’infiltration des données sensibles de votre organisation. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
