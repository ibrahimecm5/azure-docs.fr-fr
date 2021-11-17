---
title: 'Tutoriel : Intégration de l’authentification unique Azure AD à Adobe Sign | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Adobe Sign.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/08/2021
ms.author: jeedes
ms.openlocfilehash: 14e978d554cf970c545b4108ac4baaa12836c131
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132280941"
---
# <a name="tutorial-azure-ad-sso-integration-with-adobe-sign"></a>Tutoriel : Intégration de l’authentification unique Azure AD à Adobe Sign

Dans ce tutoriel, vous allez découvrir comment intégrer Adobe Sign à Azure Active Directory (Azure AD). Quand vous intégrez Adobe Sign à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Adobe Sign.
* Permettre aux utilisateurs de se connecter automatiquement à Adobe Sign avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :
 
* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Adobe Sign pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Adobe Sign prend en charge l’authentification unique lancée par le **fournisseur de services**.

## <a name="add-adobe-sign-from-the-gallery"></a>Ajouter Adobe Sign à partir de la galerie

Pour configurer l’intégration d’Adobe Sign à Azure AD, vous devez ajouter Adobe Sign depuis la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Adobe Sign** dans la zone de recherche.
1. Sélectionnez **Adobe Sign** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-adobe-sign"></a>Configurer et tester l’authentification unique Azure AD pour Adobe Sign

Dans cette section, vous configurez et testez l’authentification unique Azure AD avec Adobe Sign au moyen d’un utilisateur de test appelé **Britta Simon**.
Pour que l’authentification unique fonctionne, une relation entre un utilisateur Azure AD et l’utilisateur Adobe Sign associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec Adobe Sign, vous devez effectuer les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Adobe Sign](#configure-adobe-sign-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test Adobe Sign](#create-adobe-sign-test-user)** pour avoir dans Adobe Sign un équivalent de Britta Simon lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure.

Pour configurer l’authentification unique Azure AD avec Adobe Sign, effectuez les étapes suivantes :

1. Dans le portail Azure, dans la page d’intégration de l’application **Adobe Sign**, cliquez sur **Authentification unique**.

1. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, sélectionnez le mode **SAML/WS-Fed** afin d’activer l’authentification unique.

1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône Crayon pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    a. Dans la zone de texte **URL de connexion**, saisissez une URL au format suivant : `https://<companyname>.echosign.com/`

    b. Dans la zone de texte **Identificateur (ID d’entité)** , saisissez une URL au format suivant : `https://<companyname>.echosign.com`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Pour obtenir ces valeurs, contactez l’[équipe du support technique d’Adobe Sign](https://helpx.adobe.com/in/contact/support.html). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

5. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (Base64)** en fonction des options définies par rapport à vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

6. Dans la section **Configurer Adobe Sign**, copiez la ou les URL appropriées, selon vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Adobe Sign.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Adobe Sign**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-adobe-sign-sso"></a>Configurer l’authentification unique Adobe Sign

1. Avant la configuration, contactez l’[équipe du support technique d’Adobe Sign](https://helpx.adobe.com/in/contact/support.html) pour ajouter votre domaine à la liste verte d’Adobe Sign. Voici comment ajouter le domaine :

    a. L’[équipe de support client d’Adobe Sign](https://helpx.adobe.com/in/contact/support.html) vous enverra un jeton généré aléatoirement. Pour votre domaine, le jeton sera dans ce format : **adobe-sign-verification= xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx**

    b. Publiez le jeton de vérification dans un enregistrement DNS texte et informez [l’équipe de support client d’Adobe Sign](https://helpx.adobe.com/in/contact/support.html).

    > [!NOTE]
    > Cela peut prendre plusieurs jours, voire plus. Notez que les retards de propagation DNS signifient qu’une valeur publiée dans le DNS peut ne pas être visible pendant une heure ou plus. Votre administrateur informatique doit savoir comment publier ce jeton dans un enregistrement DNS texte.

    c. Après que vous aurez averti [l’équipe de support client d’Adobe Sign](https://helpx.adobe.com/in/contact/support.html) via le ticket de support, une fois le jeton publié, elle validera le domaine et l’ajoutera à votre compte.

    d. Il faut généralement procéder comme suit pour publier le jeton dans un enregistrement DNS :

    * Se connecter à son compte de domaine
    * Rechercher la page de mise à jour de l’enregistrement DNS. Cette page peut s’appeler Gestion DNS, Gestion des noms de serveur ou Paramètres avancés.
    * Trouver les enregistrements TXT pour votre domaine.
    * Ajouter un enregistrement TXT avec la valeur complète du jeton fournie par Adobe.
    * Enregistrez vos modifications.

1. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Adobe Sign en tant qu’administrateur.

1. Dans le menu SAML, sélectionnez **Paramètres de compte** > **Paramètres SAML**.

    ![Capture d’écran de la page Paramètres SAML d’Adobe Sign.](./media/adobe-echosign-tutorial/settings.png "Compte")

1. Dans la section **SAML Settings** (Paramètres SAML), procédez comme suit :

    ![Capture d’écran mettant en évidence les paramètres SAML, notamment SAML Mandatory.](./media/adobe-echosign-tutorial/profile.png "SAML Settings")

   ![Capture d’écran des paramètres SAML.](./media/adobe-echosign-tutorial/certificate.png "SAML Settings")

   a. Sous **Mode SAML**, sélectionnez **SAML obligatoire**.

   b. Sélectionnez **Autoriser les administrateurs de compte EchoSign à se connecter avec leurs informations d'identification EchoSign** .

   c. Sous **Création d’utilisateurs**, sélectionnez **Ajouter automatiquement les utilisateurs authentifiés via SAML**.

   d. Collez la valeur **Identificateur Azure AD**, que vous avez copiée dans le portail Azure, dans la zone de texte **Idp Entity ID** (ID d’entité du fournisseur d’identité).

   e. Collez l’**URL de connexion**, que vous avez copiée dans le portail Azure, dans la zone de texte **Idp Login URL** (URL de connexion du fournisseur d’identité).

   f. Collez l’**URL de déconnexion**, que vous avez copiée dans le portail Azure, dans la zone de texte **Idp Logout URL** (URL de déconnexion du fournisseur d’identité).

   g. Ouvrez votre **Certificat (Base64)** téléchargé dans le Bloc-notes. Copiez son contenu dans le Presse-papiers, puis collez-le dans la zone de texte **Certificat IdP**.

   h. Sélectionnez **Enregistrer les modifications**.

### <a name="create-adobe-sign-test-user"></a>Créer un utilisateur de test Adobe Sign

Pour pouvoir se connecter à Adobe Sign, les utilisateurs d’Azure AD doivent être attribués dans Adobe Sign. Il s’agit d’une tâche manuelle.

>[!NOTE]
>Vous pouvez utiliser tout autre outil ou API de création de compte d’utilisateur fourni par Adobe Sign pour approvisionner des comptes d’utilisateurs Azure AD. 

1. Connectez-vous à votre site d’entreprise **Adobe Sign** en tant qu’administrateur.

2. Dans le menu du haut, sélectionnez **Compte**. Puis, dans le volet gauche, sélectionnez **Utilisateurs et groupes** > **Créer un utilisateur**.

    ![Capture d’écran du site d’entreprise Adobe Sign, avec Compte, Utilisateurs et groupes et Créer un utilisateur mis en surbrillance](./media/adobe-echosign-tutorial/account.png "Compte")

3. Dans la section **Create New User** (Créer un utilisateur), procédez comme suit :

    ![Capture d’écran de la section Créer un utilisateur](./media/adobe-echosign-tutorial/user.png "Create User")

    a. Tapez l’**Adresse e-mail**, le **Prénom** et le **Nom** d’un compte Azure AD valide que vous souhaitez provisionner dans les zones de texte correspondantes.

    b. Sélectionnez **Créer un utilisateur**.

>[!NOTE]
>Le titulaire du compte Azure Active Directory reçoit un e-mail contenant un lien pour confirmer le compte avant qu’il ne soit activé. 

### <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Cette opération redirige vers l’URL de connexion à Adobe Sign où vous pouvez lancer le flux de connexion. 

* Accédez directement à l’URL de connexion à Adobe Sign pour lancer le flux de connexion à partir de là.

* Vous pouvez utiliser Mes applications de Microsoft. Quand vous cliquez sur la vignette Adobe Sign dans Mes applications, vous devez être connecté automatiquement à l’application Adobe Sign pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez configuré Adobe Sign, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
