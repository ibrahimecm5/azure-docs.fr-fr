---
title: 'Tutoriel : Intégration de l’authentification unique Azure AD à Tableau Online'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Tableau Online.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/24/2021
ms.author: jeedes
ms.openlocfilehash: 63e812633bd19332fde73d3ebb908d13cb6faf6b
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132341024"
---
# <a name="tutorial-azure-ad-sso-integration-with-tableau-online"></a>Tutoriel : Intégration de l’authentification unique Azure AD à Tableau Online

Dans ce tutoriel, vous allez découvrir comment intégrer Tableau Online à Azure Active Directory (Azure AD). Quand vous intégrez Tableau Online à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Tableau Online.
* Permettre à vos utilisateurs de se connecter automatiquement à Tableau Online avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Tableau Online pour lequel l’authentification unique est activée

> [!NOTE]
> Cette intégration peut également être utilisée à partir de l’environnement cloud US Government Azure AD. Cette application est disponible dans la Galerie d’applications cloud US Government Azure AD et peut être configurée de la même façon que dans le cloud public.

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Tableau Online prend en charge l’authentification unique lancée par le **fournisseur de services**.
* Tableau Online prend en charge [l’attribution d’utilisateurs et la suppression des privilèges d’accès **automatisées**](tableau-online-provisioning-tutorial.md) (recommandé).

## <a name="add-tableau-online-from-the-gallery"></a>Ajouter Tableau Online à partir de la galerie

Pour configurer l’intégration de Tableau Online à Azure AD, vous devez ajouter Tableau Online à partir de la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Tableau Online** dans la zone de recherche.
1. Sélectionnez **Tableau Online** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-tableau-online"></a>Configurer et tester l’authentification unique Azure AD pour Tableau Online

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Tableau Online à l’aide d’un utilisateur de test appelé **Britta Simon**.
Pour que l’authentification unique fonctionne, vous devez associer l’utilisateur Azure AD à l’utilisateur Tableau Online.

Pour configurer et tester l’authentification unique Azure AD avec Tableau Online, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Tableau Online](#configure-tableau-online-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test Tableau Online](#create-tableau-online-test-user)** pour avoir un équivalent de B.Simon dans Tableau Online lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **Tableau Server**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    a. Dans la zone de texte **URL de connexion**, tapez l’URL : `https://sso.online.tableau.com/public/sp/login?alias=<entityid>`

    b. Dans la zone de texte **Identificateur (ID d’entité)** , tapez l’URL suivante : `https://sso.online.tableau.com/public/sp/metadata?alias=<entityid>`

    > [!NOTE]
    > Vous obtiendrez la valeur `<entityid>` à partir de la section **Configurer Tableau Online** de ce didacticiel. La valeur d’ID d’entité sera la valeur **Identificateur Azure AD** figurant dans la section **Configurer Tableau Online**.

5. Sur la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le fichier **XML de métadonnées de fédération** en fonction des options définies selon vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

6. Dans la section **Configurer Tableau Online**, copiez la ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en accordant l’accès à Tableau Online.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Tableau Online**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-tableau-online-sso"></a>Configurer l’authentification unique Tableau Online

1. Pour automatiser la configuration dans Tableau Online, vous devez installer l’**extension de navigateur My Apps Secure Sign-in** en cliquant sur **Installer l’extension**.

    ![Extension My apps](common/install-myappssecure-extension.png)

2. Une fois l’extension ajoutée au navigateur, cliquez sur **Configurer Tableau Online** pour être redirigé vers l’application Tableau Online. À partir de là, indiquez les informations d’identification de l’administrateur pour vous connecter à Tableau Online. Cette extension de navigateur configure automatiquement l’application pour vous et automatise les étapes 3 à 7.

    ![Configuration](common/setup-sso.png)

3. Si vous souhaitez configurer Tableau Online manuellement, dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Tableau Online en tant qu’administrateur.

1. Cliquez sur **Settings** (Paramètres), puis sur **Authentication** (Authentification).

    ![Capture d’écran montrant l’élément Authentication sélectionné à partir du menu Settings.](./media/tableauonline-tutorial/menu.png)

2. Pour activer SAML, dans la section **Types d’authentification**, cochez **Enable an additional authentication method**  (Activer une méthode d’authentification supplémentaire), puis cochez la case **SAML**.

    ![Capture d’écran montrant la section Authentication types où vous pouvez sélectionner les valeurs.](./media/tableauonline-tutorial/authentication.png)

3. Faites défiler la page jusqu’à la section **Import metadata file into Tableau Online** (Importer le fichier de métadonnées dans Tableau Online).  Cliquez sur Browse et importez le fichier de métadonnées que vous avez téléchargé à partir d’Azure AD. Cliquez alors sur **Apply (Appliquer)** .

   ![Capture d’écran montrant la section où vous pouvez importer le fichier de métadonnées.](./media/tableauonline-tutorial/metadata.png)

4. Dans la section **Match assertions** (Faire correspondre les assertions), insérez les noms d’assertion du fournisseur d’identité correspondants pour **l’adresse e-mail**, le **prénom** et le **nom**. Pour obtenir ces informations à partir d’Azure AD : 
  
    a. Dans le portail Azure, accédez à la page d’intégration de l’application **Tableau Online**.

    b. Dans la section **Attributs et revendications de l’utilisateur**, cliquez sur l’icône de modification.

   ![Capture d’écran montrant la section User Attributes & Claims où vous pouvez sélectionner l’icône de modification.](./media/tableauonline-tutorial/attribute-section.png)

    c. Copiez la valeur de l’espace de noms de ces attributs : prénom, adresse e-mail et nom de famille en procédant comme suit :

   ![Capture d’écran montrant les attributs Givenname, Surname et Emailaddress.](./media/tableauonline-tutorial/name.png)

    d. Cliquez sur la valeur **user.givenname**.

    e. Copiez la valeur à partir de la zone de texte **Espace de noms**.

    ![Capture d’écran montrant la section Manage user claims où vous pouvez entrer l’espace de noms.](./media/tableauonline-tutorial/attributes.png)

    f. Pour copier les valeurs d’espace de noms pour l’adresse e-mail et le nom de famille, répétez les étapes ci-dessus.

    g. Basculez dans l’application Tableau Online, puis définissez la section **Attributs et revendications de l’utilisateur** comme suit :

    * Email (Adresse de messagerie) : **mail** ou **userPrincipalName**

    * First name (Prénom) : **givenName**

    * Last name (Nom) : **surname**

    ![Capture d’écran montrant la section Match attributes où vous pouvez entrer les valeurs.](./media/tableauonline-tutorial/claims.png)

### <a name="create-tableau-online-test-user"></a>Créer un utilisateur de test Tableau Online

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans Tableau Online.

1. Dans **Tableau Online**, cliquez sur **Paramètres**, puis sur la section **Authentification**. Faites défiler la page jusqu’à la section **Manage Users**. Cliquez sur **Ajouter des utilisateurs**, puis sur **Entrer les adresses de messagerie**.
  
    ![Capture d’écran montrant la section Manage users où vous pouvez sélectionner Add users.](./media/tableauonline-tutorial/users.png)

2. Sélectionnez **Add users for (SAML) authentication**  (Ajouter des utilisateurs pour l’authentification (SAML)). Dans la zone de texte **Enter email addresses**, ajoutez britta.simon\@contoso.com
  
    ![Capture d’écran montrant la page Add Users dans laquelle vous pouvez entrer une adresse e-mail.](./media/tableauonline-tutorial/add-users.png)

3. Cliquez sur **Add Users**.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes.

* Cliquez sur **Tester cette application** dans le portail Azure. Cette opération redirige vers l’URL de connexion Tableau Online, où vous pouvez lancer le processus de connexion.

* Accédez directement à l’URL de connexion Tableau Online pour lancer le processus de connexion.

* Vous pouvez utiliser Mes applications de Microsoft. Un clic sur la vignette Tableau Online dans Mes applications vous redirige vers l’URL de connexion Tableau Online. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré Tableau Online, vous pouvez appliquer des contrôles de session, qui protègent l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
