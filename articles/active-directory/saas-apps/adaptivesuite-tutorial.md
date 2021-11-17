---
title: 'Tutoriel : Intégration d’Azure Active Directory à Adaptive Insights | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Adaptive Insights.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/19/2021
ms.author: jeedes
ms.openlocfilehash: 6f3fa17c5464ac8cdf580e7d13b7d17941cb1abf
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132281074"
---
# <a name="tutorial-integrate-adaptive-insights-with-azure-active-directory"></a>Tutoriel : Intégrer Adaptive Insights à Azure Active Directory

Dans ce tutoriel, vous allez découvrir comment intégrer Adaptive Insights à Azure Active Directory (Azure AD). Quand vous intégrez Adaptive Insights à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Adaptive Insights.
* Permettre à vos utilisateurs de se connecter automatiquement à Adaptive Insights avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Adaptive Insights pour lequel l’authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* Adaptive Insights prend en charge l’authentification unique initiée par le **fournisseur d’identité**

## <a name="add-adaptive-insights-from-the-gallery"></a>Ajouter Adaptive Insights à partir de la galerie

Pour configurer l’intégration d’Adaptive Insights dans Azure AD, vous devez ajouter Adaptive Insights, disponible dans la galerie, à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Adaptive Insights** dans la zone de recherche.
1. Sélectionnez **Adaptive Insights** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-adaptive-insights"></a>Configurer et tester l’authentification unique Azure AD pour Adaptive Insights

Configurez et testez l’authentification unique Azure AD avec Adaptive Insights pour un utilisateur de test nommé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Adaptive Insights associé.

Pour configurer et tester l’authentification unique Azure AD avec Adaptive Insights, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Adaptive Insights](#configure-adaptive-insights-sso)**  : pour définir les paramètres de l’authentification unique côté application.
    1. **[Créez un utilisateur de test Adaptive Insights](#create-adaptive-insights-test-user)**  : pour avoir un équivalent de B.Simon dans Adaptive Insights, lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, dans la page d’intégration de l’application **Adaptive Insights**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon pour **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    > [!NOTE]
    > Vous pouvez obtenir les valeurs de l’Identificateur (ID d’entité) et l’URL de réponse sur la page **Paramètres d’authentification unique SAML** d’Adaptive Insights.

4. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **Certificat (Base64)** , puis sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

6. Dans la section **Configurer Adaptive Insights**, copiez la ou les URL appropriées, en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Adaptive Insights.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Adaptive Insights**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

### <a name="configure-adaptive-insights-sso"></a>Configurer l’authentification unique dans Adaptive Insights

1. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Adaptive Insights en tant qu’administrateur.

2. Accédez à **Administration**.

    ![Capture d’écran mettant en évidence Administration dans le volet de navigation.](./media/adaptivesuite-tutorial/administration.png "Admin")

3. Dans la section **Utilisateurs et rôles**, cliquez sur **Gérer les paramètres d’authentification unique SAML**.

    ![Gérer les paramètres d’authentification unique SAML](./media/adaptivesuite-tutorial/settings.png "Manage SAML SSO Settings")

4. Dans la page **SAML SSO Settings** , procédez comme suit :

    ![Paramètres d’authentification unique SAML](./media/adaptivesuite-tutorial/saml.png "SAML SSO Settings")

    a. Dans la zone de texte **Identity provider name** , attribuez un nom à votre configuration.

    b. Collez la valeur de **Identificateur Azure AD** copiée à partir du portail Azure dans la zone de texte **ID d’entité du fournisseur d’identité**.

    c. Collez la valeur **URL de connexion** copiée à partir du portail Azure dans la zone de texte **URL SSO du fournisseur d’identité**.

    d. Collez la valeur **URL de déconnexion** copiée à partir du portail Azure dans la zone de texte **URL de déconnexion personnalisée**.

    e. Pour charger votre certificat téléchargé, cliquez sur **Choisir un fichier**.

    f. Sélectionnez les options suivantes :

     * Pour **SAML user id**, sélectionnez **User’s Adaptive Insights user name**.

     * Pour **SAML user id location**, sélectionnez **User id in NameID of Subject**.

     * Pour **SAML NameID format**, sélectionnez **Email address**.

     * Pour **Enable SAML**, sélectionnez **Allow SAML SSO and direct Adaptive Insights login**.

    g. Copiez l’**URL d’authentification unique Adaptive Insights** et collez-la dans les zones de texte **Identificateur (ID d’entité)** et **URL de réponse** de la section **Configuration SAML de base** dans le portail Azure.

    h. Cliquez sur **Enregistrer**.

### <a name="create-adaptive-insights-test-user"></a>Créer un utilisateur de test Adaptive Insights

Pour permettre aux utilisateurs Azure AD de se connecter à Adaptive Insights, vous devez les approvisionner dans Adaptive Insights. Dans le cas d’Adaptive Insights, cet approvisionnement est une tâche manuelle.

**Pour configurer l'approvisionnement des utilisateurs, procédez comme suit :**

1. Connectez-vous à votre site d’entreprise **Adaptive Insights** en tant qu’administrateur.

2. Accédez à **Administration**.

   ![Administrateur](./media/adaptivesuite-tutorial/administration.png "Admin")

3. Dans la section **Utilisateurs et rôles**, cliquez sur **Utilisateurs**.

   ![Ajouter un utilisateur](./media/adaptivesuite-tutorial/users.png "Ajouter un utilisateur")

4. Dans la section **New User**, procédez comme suit :

   ![Envoyer](./media/adaptivesuite-tutorial/new.png "Submit")

   a. Tapez les informations correspondant à **Nom**, **Nom d’utilisateur**, **E-mail**, **Mot de passe** d’un utilisateur Azure Active Directory valide que vous voulez provisionner dans les zones de texte associées.

   b. Sélectionnez un **rôle**.

   c. Cliquez sur **Envoyer**.

> [!NOTE]
> Vous pouvez utiliser tout autre outil ou n’importe quelle API de création de compte d’utilisateur fournis par Adaptive Insights pour provisionner des comptes d’utilisateurs Azure AD.

### <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes.

* Dans le portail Azure, cliquez sur Tester cette application. Vous êtes alors automatiquement connecté à l’instance d’Adaptive Insights pour laquelle vous avez configuré l’authentification unique.

* Vous pouvez utiliser Mes applications de Microsoft. Lorsque vous cliquez sur la vignette Adaptive Insights dans Mes applications, vous devez être connecté automatiquement à l’application Adaptive Insights pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré Adaptive Insights, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
