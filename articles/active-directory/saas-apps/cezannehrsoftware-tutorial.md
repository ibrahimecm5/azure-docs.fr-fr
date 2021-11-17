---
title: 'Didacticiel : Intégration d’Azure Active Directory avec Cezanne HR Software | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Cezanne HR Software.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/11/2021
ms.author: jeedes
ms.openlocfilehash: 437427e80870fc4f86a6f5953a2ac4a29affed0b
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132349126"
---
# <a name="tutorial-azure-active-directory-integration-with-cezanne-hr-software"></a>Didacticiel : Intégration d’Azure Active Directory avec Cezanne HR Software

Dans ce tutoriel, vous allez découvrir comment intégrer Cezanne HR Software avec Azure Active Directory (Azure AD). Quand vous intégrez Cezanne HR Software à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Cezanne HR Software.
* Permettre à vos utilisateurs de se connecter automatiquement à Cezanne HR Software avec leurs comptes Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Cezanne HR Software pour lequel l’authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Cezanne HR Software prend en charge l’authentification unique initiée par le **fournisseur de services**.

> [!NOTE]
> L’identificateur de cette application étant une valeur de chaîne fixe, une seule instance peut être configurée dans un locataire.

## <a name="add-cezanne-hr-software-from-the-gallery"></a>Ajouter Cezanne HR Software à partir de la galerie

Pour configurer l’intégration de Cezanne HR Software avec Azure AD, vous devez ajouter Cezanne HR Software, disponible dans la galerie, à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Cezanne HR Software** dans la zone de recherche.
1. Sélectionnez **Cezanne HR Software** dans le panneau des résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-cezanne-hr-software"></a>Configurer et tester l’authentification unique Azure AD pour Cezanne HR Software

Configurez et testez l’authentification unique Azure AD auprès de Cezanne HR Software pour un utilisateur de test nommé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur associé dans Cezanne HR Software.

Pour configurer et tester l’authentification unique Azure AD auprès de Cezanne HR Software, procédez comme suit :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Cezanne HR Software](#configure-cezanne-hr-software-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test Cezanne HR Software](#create-cezanne-hr-software-test-user)** pour avoir un équivalent de B.Simon dans Cezanne HR Software lié à la représentation Azure AD associée.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **Cezanne HR Software**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    a. Dans la zone de texte **URL de connexion**, saisissez une URL au format suivant : `https://w3.cezanneondemand.com/CezanneOnDemand/-/<tenantidentifier>`

    b. Dans la zone de texte **Identificateur (ID d’entité)** , tapez l’URL suivante : `https://w3.cezanneondemand.com/CezanneOnDemand/`

    c. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://w3.cezanneondemand.com:443/cezanneondemand/-/<tenantidentifier>/Saml/samlp`
    
    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de réponse et l’URL de connexion réelles. Pour obtenir ces valeurs, contactez [l’équipe du support Cezanne HR Software](https://cezannehr.com/services/support/).

5. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (Base64)** en fonction des options définies par rapport à vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

6. Dans la section **Configurer Cezanne HR Software**, copiez les URL appropriées, selon vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Cezanne HR Software.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Cezanne HR Software**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-cezanne-hr-software-sso"></a>Configurer l’authentification unique Cezanne HR Software

1. Dans une autre fenêtre de navigateur web, connectez-vous à votre client Cezanne HR Software en tant qu’administrateur.

2. Dans le menu latéral, cliquez sur **Administration**. Accédez ensuite à **Security Settings** (Paramètres de sécurité), puis cliquez sur **Single Sign-On** (Authentification unique).

    ![Capture d’écran montrant le locataire Cezanne HR avec Security Settings et Single Sign-On Configuration sélectionnés.](./media/cezannehrsoftware-tutorial/settings.png)

3. Dans le panneau **Autoriser les utilisateurs à se connecter à l’aide de l’authentification unique**, cochez la case **SAML 2.0** et sélectionnez l’option **Configuration avancée**.

    ![Capture d’écran montrant le volet Allow users avec SAML 2.0 et Advanced Configuration sélectionnés.](./media/cezannehrsoftware-tutorial/configuration.png)

4. Cliquez sur le bouton **Ajouter nouveau** .

    ![Capture d’écran montrant le bouton Add New.](./media/cezannehrsoftware-tutorial/new-button.png)

5. Entrez les champs suivants dans la section **SAML 2.0 IDENTITY PROVIDERS** (FOURNISSEURS D’IDENTITÉ SAML 2.0), puis cliquez sur **OK**.

    ![Capture d’écran montrant un volet dans lequel vous pouvez entrer les valeurs décrites à cette étape.](./media/cezannehrsoftware-tutorial/identity-provider.png)

    a. **Display Name** (Nom d’affichage) : entrez le nom de votre fournisseur d’identité pour Nom d’affichage.

    b. **Entity Identifier** (Identificateur d’entité) : dans la zone de texte Identificateur d’entité, collez la valeur de l’identificateur Azure AD que vous avez copiée à partir du portail Azure.

    c. **SAML Binding** (Liaison SAML) : Changez la liaison SAML en « POST ».

    d. **Security Token Service Endpoint** (Point de terminaison de service de jeton de sécurité) : dans la zone de texte Security Token Service Endpoint, collez la valeur de l’URL de connexion que vous avez copiée à partir du portail Azure.

    e. **Nom de l’attribut d’ID d’utilisateur** : dans la zone de texte Nom de l’attribut d’ID d’utilisateur, entrez « http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress  ».

    f. **Public Key Certificate** (Certificat de clé publique) : cliquez sur l’icône Charger pour charger le certificat téléchargé auprès du portail Azure.

6. Cliquez sur OK.

7. Cliquez sur le bouton Enregistrer . 

    ![Capture d’écran montrant le bouton Save pour Single Sign-on Configuration.](./media/cezannehrsoftware-tutorial/save-button.png)

### <a name="create-cezanne-hr-software-test-user"></a>Créer un utilisateur de test Cezanne HR Software

Pour permettre aux utilisateurs Azure AD de se connecter à Cezanne HR Software, vous devez les approvisionner dans Cezanne HR Software. Dans le cas de Cezanne HR Software, cet approvisionnement est une tâche manuelle.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous à votre site d’entreprise Cezanne HR Software en tant qu’administrateur.

2. Dans le menu latéral, cliquez sur **Administration**. Accédez ensuite à **Users** (Utilisateurs), puis cliquez sur **Add New User** (Ajouter un nouvel utilisateur).

    ![Capture d’écran montrant le locataire Cezanne HR Software avec Manage Users et Add New User sélectionnés.](./media/cezannehrsoftware-tutorial/manage-users.png "Nouvel utilisateur")

3. Dans la section **DÉTAILS DE LA PERSONNE**, effectuez les étapes suivantes :

    ![Capture d’écran montrant la section PERSON DETAILS, dans laquelle vous pouvez entrer les valeurs décrites à cette étape.](./media/cezannehrsoftware-tutorial/details.png "Nouvel utilisateur")

    a. Désactivez l’option **Utilisateur interne** .

    b. Entrez le prénom   

    c. Entrez le nom

    d. Entrez l’adresse e-mail.

4. À la section **Informations sur le compte** , procédez comme suit :

    ![Capture d’écran montrant ACCOUNT INFORMATION, où vous pouvez entrer les valeurs décrites à cette étape.](./media/cezannehrsoftware-tutorial/account.png "Nouvel utilisateur")

    a. Dans la zone de texte **Username** (Nom d’utilisateur), tapez l’e-mail d’un utilisateur, par exemple, Brittasimon@contoso.com.

    b. Dans la zone de texte **Password** (Mot de passe), tapez le mot de passe de l’utilisateur.

    c. Sélectionnez **Professionnel des RH** en tant que **Rôle de sécurité**.

    d. Cliquez sur **OK**.
    ![Capture d’écran montrant le bouton OK.](https://user-images.githubusercontent.com/80324891/115694644-f6eb5700-a358-11eb-9b23-a87a24921052.png)

5. Accédez à l’onglet **Authentification unique** et sélectionnez **Ajouter nouveau** dans la zone **Identificateurs SAML 2.0**.

    ![Capture d’écran montrant l’onglet Single Sign-On, où vous pouvez sélectionner Add New.](./media/cezannehrsoftware-tutorial/single-sign-on.png "Utilisateur")

6. Choisissez votre fournisseur d’identité pour **Identity Provider** (Fournisseur d’identité) et, dans la zone de texte **User Identifier** (Identificateur d’utilisateur), entrez l’adresse e-mail de l’utilisateur.

    ![Capture d’écran montrant les identificateurs SAML 2.0, où vous pouvez sélectionner votre fournisseur d’identité et votre identificateur d’utilisateur.](./media/cezannehrsoftware-tutorial/user-identifier.png "Utilisateur")

7. Cliquez sur le bouton **Enregistrer** .

    ![Capture d’écran montrant le bouton Save pour User Settings.](./media/cezannehrsoftware-tutorial/save.png "Utilisateur")

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion à Cezanne HR Software à partir de laquelle vous pouvez lancer le flux de connexion. 

* Accédez directement à l’URL de connexion à Cezanne HR Software pour initier le flux de connexion.

* Vous pouvez utiliser Mes applications de Microsoft. Quand vous cliquez sur la vignette Cezanne HR Software dans Mes applications, vous êtes redirigé vers l’URL de connexion à Cezanne HR Software. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez configuré Cezanne HR Software, vous pouvez appliquer le contrôle de session qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
