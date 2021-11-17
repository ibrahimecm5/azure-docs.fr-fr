---
title: 'Tutoriel : Intégration de l’authentification unique Azure AD à Snowflake'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Snowflake.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/13/2021
ms.author: jeedes
ms.openlocfilehash: b44d6ecbe75d688de0803e0c2bf2e942f0728bfc
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132329155"
---
# <a name="tutorial-azure-ad-sso-integration-with-snowflake"></a>Tutoriel : Intégration de l’authentification unique Azure AD à Snowflake

Dans ce tutoriel, vous allez découvrir comment intégrer Snowflake à Azure Active Directory (Azure AD). Quand vous intégrez Snowflake à Azure AD, vous pouvez :

* Contrôler qui dans Azure AD a accès à Snowflake.
* Permettre aux utilisateurs de se connecter automatiquement à Snowflake avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD avec Snowflake, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un [compte gratuit](https://azure.microsoft.com/free/).
* Abonnement Snowflake pour lequel l’authentification unique est activée.

> [!NOTE]
> Cette intégration peut également être utilisée à partir de l’environnement cloud US Government Azure AD. Cette application est disponible dans la Galerie d’applications cloud US Government Azure AD et peut être configurée de la même façon que dans le cloud public.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* Snowflake prend en charge l’authentification unique lancée par **le fournisseur de services et le fournisseur d’identité**.
* Snowflake prend en charge l’[attribution et la désattribution automatisées d’utilisateurs](snowflake-provisioning-tutorial.md) (recommandé).

## <a name="add-snowflake-from-the-gallery"></a>Ajouter Snowflake depuis la galerie

Pour configurer l’intégration de Snowflake dans Azure AD, vous devez ajouter Snowflake à partir de la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Snowflake** dans la zone de recherche.
1. Sélectionnez **Snowflake** dans le volet des résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-snowflake"></a>Configurer et tester l’authentification unique Azure AD pour Snowflake

Configurez et testez l’authentification unique Azure AD avec Snowflake pour un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Snowflake associé.

Pour configurer et tester l’authentification unique Azure AD avec Snowflake, vous devez suivre les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Snowflake](#configure-snowflake-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test Snowflake](#create-snowflake-test-user)** pour avoir un équivalent de B.Simon dans Snowflake lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **Snowflake**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, suivez les étapes ci-dessous si vous souhaitez configurer l’application en mode lancé par le **fournisseur d’identité** :

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<SNOWFLAKE-URL>.snowflakecomputing.com`

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://<SNOWFLAKE-URL>.snowflakecomputing.com/fed/login`

1. Cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes si vous souhaitez configurer l’application en mode Initié par le fournisseur de services :

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<SNOWFLAKE-URL>.snowflakecomputing.com`.
    
    b. Dans la zone de texte **URL de déconnexion**, tapez une URL au format suivant : `https://<SNOWFLAKE-URL>.snowflakecomputing.com/fed/logout`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL de réponse et l’URL de connexion réels. Pour obtenir ces valeurs, contactez l’[équipe de support technique Snowflake](https://support.snowflake.net/s/). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

4. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (Base64)** en fonction des options définies par rapport à vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

6. Dans la section **Configurer Snowflake**, copiez la ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Snowflake.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Snowflake**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-snowflake-sso"></a>Configurer l’authentification unique Snowflake

1. Ouvrez une autre fenêtre de navigateur web et connectez-vous à Snowflake en tant qu’administrateur de la sécurité.

1. **Sélectionnez le rôle** **ACCOUNTADMIN** en cliquant sur **profil** en haut à droite de la page.

    > [!NOTE]
    > Ce n’est pas dans le même contexte que vous avez sélectionné en haut à droite, sous votre nom d’utilisateur.
    
    ![L’administrateur Snowflake](./media/snowflake-tutorial/account.png)

1. Ouvrez le **certificat Base 64 téléchargé** dans le Bloc-notes. Copiez la valeur entre « -----BEGIN CERTIFICATE----- » et « -----END CERTIFICATE----- », et collez-la entre les guillemets situés à côté de **certificate** ci-dessous. Dans **ssoUrl**, collez la valeur de l’**URL de connexion** que vous avez copiée sur le portail Azure. Sélectionnez **Toutes les requêtes** et cliquez sur **Exécuter**.

   ![Snowflake sql](./media/snowflake-tutorial/certificate.png)

   ```
   use role accountadmin;
   alter account set saml_identity_provider = '{
   "certificate": "<Paste the content of downloaded certificate from Azure portal>",
   "ssoUrl":"<Login URL value which you have copied from the Azure portal>",
   "type":"custom",
   "label":"AzureAD"
   }';
   alter account set sso_login_page = TRUE;
   ```

### <a name="create-snowflake-test-user"></a>Créer un utilisateur de test Snowflake

Pour permettre aux utilisateurs Azure AD de se connecter à Snowflake, vous devez les approvisionner dans Snowflake. Dans Snowflake, l’approvisionnement est une tâche manuelle.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous à Snowflake en tant qu’administrateur de la sécurité.

2. **Sélectionnez le rôle** **ACCOUNTADMIN** en cliquant sur **profil** en haut à droite de la page.  

    ![L’administrateur Snowflake](./media/snowflake-tutorial/account.png)

3. Créez l’utilisateur en exécutant la requête SQL ci-dessous. Vérifiez que la valeur de « LOGIN_NAME » correspond au nom d’utilisateur Azure AD sur la feuille de calcul, comme indiqué ci-dessous.

    ![L’adminsql Snowflake](./media/snowflake-tutorial/user.png)

    ```
    use role accountadmin;
    CREATE USER britta_simon PASSWORD = '' LOGIN_NAME = 'BrittaSimon@contoso.com' DISPLAY_NAME = 'Britta Simon';
    ```

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

#### <a name="sp-initiated"></a>Lancée par le fournisseur de services :

* Cliquez sur **Tester cette application** dans le portail Azure. Cette opération redirige vers l’URL de connexion Snowflake, où vous pouvez lancer le flux de connexion.  

* Accédez directement à l’URL de connexion Snowflake pour lancer le flux de connexion.

#### <a name="idp-initiated"></a>Lancée par le fournisseur d’identité :

* Cliquez sur **Tester cette application** dans le portail Azure, ce qui devrait vous connecter automatiquement à l’application Snowflake pour laquelle vous avez configuré l’authentification unique. 

Vous pouvez aussi utiliser Mes applications de Microsoft pour tester l’application dans n’importe quel mode. Si, quand vous cliquez sur la vignette Snowflake dans Mes applications, le mode Fournisseur de services est configuré, vous êtes redirigé vers la page de connexion de l’application pour lancer le flux de connexion ; s’il s’agit du mode Fournisseur d’identité, vous êtes automatiquement connecté à l’instance de Snowflake pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez configuré Snowflake, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
