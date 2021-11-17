---
title: 'Didacticiel : Intégration de Azure Active Directory à MobileIron | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et MobileIron.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/15/2021
ms.author: jeedes
ms.openlocfilehash: aec5032dd05a7ee6c0192dd4798d9acdedff533d
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132287899"
---
# <a name="tutorial-azure-active-directory-integration-with-mobileiron"></a>Didacticiel : Intégration de Azure Active Directory à MobileIron

 Dans ce tutoriel, vous allez apprendre à intégrer MobileIron à Azure Active Directory (Azure AD). Quand vous intégrez MobileIron à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à MobileIron.
* Permettre à vos utilisateurs de se connecter automatiquement à MobileIron avec leur compte Azure AD.
* gérer vos comptes à un emplacement central : le portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement MobileIron pour lequel l’authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* MobileIron prend en charge l’authentification unique initiée par **un fournisseur de services et un fournisseur d’identité**.

## <a name="add-mobileiron-from-the-gallery"></a>Ajouter MobileIron à partir de la galerie

Pour configurer l’intégration de MobileIron à Azure AD, vous devez ajouter MobileIron à partir de la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire, ou avec un compte personnel Microsoft.
1. Sélectionnez **Azure Active Directory** dans le volet de gauche.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **MobileIron** dans la zone de recherche.
1. Sélectionnez **MobileIron** dans les résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-mobileiron"></a>Configurer et tester l’authentification unique Azure AD pour MobileIron

Configurez et testez l’authentification unique (SSO) Azure AD avec MobileIron à l’aide d’un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique (SSO) fonctionne, vous devez établir une relation entre un utilisateur Azure AD et l’utilisateur MobileIron associé.

Pour configurer et tester l’authentification unique Azure AD avec MobileIron, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
     1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
2. **[Configurer l’authentification unique (SSO) MobileIron](#configure-mobileiron-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test MobileIron](#create-mobileiron-test-user)** pour avoir un équivalent de Britta Simon dans MobileIron, lié à la représentation Azure AD de l’utilisateur.
6. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD sur le portail Azure.
 
1. Dans la page d’intégration de l’application **MobileIron** du portail Azure, recherchez la section **Gérer**, puis sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, sélectionnez l’icône de crayon pour **Configuration SAML de base** afin de modifier les paramètres.

    ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, suivez les étapes ci-dessous si vous souhaitez configurer l’application en mode démarré par **IDP** :

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://www.MobileIron.com/<key>`

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://<host>.MobileIron.com/saml/SSO/alias/<key>`

    c. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

     Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<host>.MobileIron.com/user/login.html`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL de réponse et l’URL de connexion réels. Vous obtiendrez les valeurs de la clé et de l’hôte à partir du portail d’administration de MobileIron, expliqué plus loin dans le didacticiel.

5. Sur la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le fichier **XML de métadonnées de fédération** en fonction des options définies selon vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à MobileIron.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise** > **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **MobileIron**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**. Ensuite, dans la boîte de dialogue **Ajouter une attribution**, sélectionnez **Utilisateurs et groupes**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B.Simon** dans la liste des utilisateurs. Choisissez **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, sélectionnez **Affecter**.

## <a name="configure-mobileiron-sso"></a>Configurer l’authentification unique MobileIron

1. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise MobileIron en tant qu’administrateur.

2. Accédez à **Admin** > **Identity** (Administrateur>Identité) et sélectionnez l’option **AAD** dans le champ **Info on Cloud IDP Setup** (Informations sur la configuration de fournisseur d’identité cloud).

    ![Capture d’écran montrant l’onglet Admin du site MobileIron avec l’option Identity sélectionnée.](./media/MobileIron-tutorial/tutorial_MobileIron_admin.png)

3. Copiez les valeurs de la **clé** et de l’**hôte** et collez-les pour renseigner les URL dans la section **Configuration SAML de base** dans le portail Azure.

    ![Capture d’écran montrant l’option de configuration de SAML avec une valeur de clé et une valeur d’hôte.](./media/MobileIron-tutorial/key.png)

4. Dans le champ **Exporter le fichier de métadonnées de AAD et l’importer dans MobileIron Cloud**, cliquez sur **Choisir un fichier** pour charger les métadonnées téléchargées à partir du portail Azure. Cliquez sur **Fait** une fois le chargement terminé.

    ![Bouton de métadonnées d’administrateur de la page Configurer l’authentification unique](./media/MobileIron-tutorial/tutorial_MobileIron_adminmetadata.png)


### <a name="create-mobileiron-test-user"></a>Créer un utilisateur de test MobileIron

Pour se connecter à MobileIron, les utilisateurs de Azure AD doivent être approvisionnés dans MobileIron.  
Dans le cas de MobileIron, l’approvisionnement est une tâche manuelle.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous au site d’entreprise MobileIron en tant qu’administrateur.

1. Accédez à **Utilisateurs**, puis cliquez sur **Ajouter** > **un utilisateur unique**.

    ![Bouton utilisateur de la page Configurer l’authentification unique](./media/MobileIron-tutorial/tutorial_MobileIron_user.png)

1. Dans la boîte de dialogue **« Utilisateur unique »** , procédez comme suit :

    ![Bouton Ajouter un utilisateur de la page Configurer l’authentification unique](./media/MobileIron-tutorial/tutorial_MobileIron_useradd.png)

    a. Dans la zone de texte **Adresse e-mail**, saisissez l’adresse e-mail de l’utilisateur, par exemple brittasimon@contoso.com.

    b. Dans la zone de texte **Prénom**, saisissez le prénom de l’utilisateur, par exemple Britta.

    c. Dans la zone de texte **Nom**, saisissez le nom de famille de l’utilisateur, par exemple Simon.

    d. Cliquez sur **Done**.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

### <a name="sp-initiated"></a>Lancée par le fournisseur de services :

* Cliquez sur **Tester cette application** dans le portail Azure. Cette opération redirige vers l’URL d’authentification MobileIron, où vous pouvez lancer le processus de connexion.  

* Accédez directement à l’URL d’authentification MobileIron pour lancer le processus de connexion.

### <a name="idp-initiated"></a>Lancée par le fournisseur d’identité :

* Dans le portail Azure, cliquez sur **Tester cette application**. Vous êtes alors automatiquement connecté à l’instance MobileIron pour laquelle vous avez configuré l’authentification unique.

Vous pouvez aussi utiliser Mes applications de Microsoft pour tester l’application dans n’importe quel mode. Si, quand vous cliquez sur la vignette MobileIron dans Mes applications, le mode Fournisseur de services est configuré, vous êtes redirigé vers la page de connexion de l’application pour lancer le flux de connexion ; s’il s’agit du mode Fournisseur d’identité, vous êtes automatiquement connecté à l’instance MobileIron pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).


## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré MobileIron, vous pouvez appliquer des contrôles de session qui protègent en temps réel contre l’exfiltration et l’infiltration des données sensibles de votre organisation. Les contrôles de session sont étendus à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
