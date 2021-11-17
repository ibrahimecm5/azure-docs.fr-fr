---
title: 'Tutoriel : Intégration d’Azure Active Directory à Sprinklr | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Sprinklr.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/18/2021
ms.author: jeedes
ms.openlocfilehash: f7dc7215f812938584438d3f212ce31ddcea23df
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132329042"
---
# <a name="tutorial-azure-active-directory-integration-with-sprinklr"></a>Tutoriel : Intégration d’Azure Active Directory à Sprinklr

Dans ce tutoriel, vous allez apprendre à intégrer Sprinklr à Azure Active Directory (Azure AD). Quand vous intégrez Sprinklr à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Sprinklr.
* Permettre à vos utilisateurs de se connecter automatiquement à Sprinklr avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Sprinklr pour lequel l’authentification unique (SSO) est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Sprinklr prend en charge l’authentification unique lancée par le **fournisseur de services**.

## <a name="add-sprinklr-from-the-gallery"></a>Ajouter Sprinklr depuis la galerie

Pour configurer l’intégration de Sprinklr avec Azure AD, vous devez ajouter Sprinklr, disponible dans la galerie, à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Sprinklr** dans la zone de recherche.
1. Sélectionnez **Sprinklr** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-sprinklr"></a>Configurer et tester l’authentification unique Azure AD pour Sprinklr

Configurez et testez l’authentification SSO Azure AD avec Sprinklr à l’aide d’une utilisatrice de test appelée **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Sprinklr associé.

Pour configurer et tester l’authentification unique Azure AD avec Sprinklr, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Sprinklr](#configure-sprinklr-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test Sprinklr](#create-sprinklr-test-user)** pour avoir, dans Sprinklr, un équivalent de B. Simon lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **Sprinklr**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    a. Dans la zone de texte **URL de connexion**, saisissez une URL au format suivant : `https://<SUBDOMAIN>.sprinklr.com`

    b. Dans la zone de texte **Identificateur (ID d’entité)** , saisissez une URL au format suivant : `https://<SUBDOMAIN>.sprinklr.com`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Pour obtenir ces valeurs, contactez l’[équipe de support technique Sprinklr](https://www.sprinklr.com/contact-us/). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

5. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (Base64)** en fonction des options définies par rapport à vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

6. Dans la section **Configurer Sprinklr**, copiez la ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Sprinklr.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Sprinklr**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-sprinklr-sso"></a>Configurer l’authentification unique de Sprinklr

1. Dans une autre fenêtre de navigateur web, connectez-vous au site de votre entreprise Sprinklr en tant qu’administrateur.

1. Accédez à **Administration \> Settings**.

    ![Administration](./media/sprinklr-tutorial/settings.png "Administration")

1. Accédez à **Manager Partner \> Single Sign** dans le volet gauche.

    ![Manage Partner](./media/sprinklr-tutorial/users.png "Manage Partner")

1. Cliquez sur **+Add Single Sign Ons**.

    ![Capture d’écran montrant le bouton Add Single Sign Ons.](./media/sprinklr-tutorial/add-user.png "Single Sign-Ons")

1. Dans la page **Single Sign on** , procédez comme suit :

    ![Capture d’écran montrant la page Single Sign on dans laquelle vous pouvez entrer les valeurs décrites.](./media/sprinklr-tutorial/configuration.png "Single Sign-Ons")

    a. Dans la zone de texte **Name** (Nom), indiquez le nom de votre configuration (par exemple, **WAADSSOTest**).

    b. Sélectionnez **Enabled**.

    c. Sélectionnez **Use new SSO Certificate**.

    d. Ouvrez votre certificat codé en base 64 dans le Bloc-notes, copiez son contenu dans le Presse-papiers, puis collez-le dans la zone de texte **Identity provider certificate (Certificat du fournisseur d’identité)** .

    e. Dans la zone de texte **Entity Id** (ID d’entité), collez la valeur **Identificateur Azure AD** que vous avez copiée dans le portail Azure.

    f. Dans la zone de texte **Identity Provider Login URL** (URL de connexion du fournisseur d’identité), collez la valeur **URL de connexion** que vous avez copiée à partir du portail Azure.

    g. Dans la zone de texte **Identity Provider Logout URL** (URL de déconnexion du fournisseur d’identité), collez la valeur **URL de déconnexion** que vous avez copiée à partir du portail Azure.

    h. Dans **SAML User ID Type**, sélectionnez **Assertion contains User’s sprinklr.com username**.

    i. Dans **SAML User ID Location**, sélectionnez **User ID is in the Name Identifier element of the Subject statement**.

    j. Cliquez sur **Enregistrer**.

    ![SAML](./media/sprinklr-tutorial/save-configuration.png "SAML")

### <a name="create-sprinklr-test-user"></a>Créer un utilisateur de test Sprinklr

1. Connectez-vous à votre site d’entreprise Sprinklr en tant qu’administrateur.

1. Accédez à **Administration \> Settings**.

    ![Administration](./media/sprinklr-tutorial/settings.png "Administration")

1. Accédez à **Manage Client \> Users** dans le volet gauche.

    ![Capture d’écran montrant le bouton Add User dans Settings/Users.](./media/sprinklr-tutorial/client.png "Paramètres")

1. Cliquez sur **Add User**.

    ![Capture d’écran montrant la boîte de dialogue Edit user dans laquelle vous pouvez entrer les valeurs décrites.](./media/sprinklr-tutorial/search-users.png "Paramètres")

1. Dans la page **Edit User** , procédez comme suit :

    ![Modifier l’utilisateur](./media/sprinklr-tutorial/update-users.png "Edit User")

    a. Dans les zones de texte **Email**, **First Name** et **Last Name**, saisissez les informations du compte utilisateur Azure AD que vous souhaitez approvisionner.

    b. Sélectionnez **Password Disabled**.

    c. Sélectionner une **Langue**.

    d. Sélectionnez un **Type d’utilisateur**.

    e. Cliquez sur **Update**.

    > [!IMPORTANT]
    > **Password Disabled** pour permettre à un utilisateur de se connecter par le biais d’un fournisseur d’identité. 

1. Accédez à **Role**, puis procédez comme suit :

    ![Rôles de partenaire](./media/sprinklr-tutorial/role.png "Partner Roles")

    a. Dans la liste **Global**, sélectionnez **ALL_Permissions**.  

    b. Cliquez sur **Update**.

> [!NOTE]
> Vous pouvez utiliser n’importe quel outil ou API de création de compte utilisateur, fourni par Sprinklr, pour approvisionner des comptes utilisateur AAD.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Une redirection est effectuée vers l’URL de connexion à Sprinklr, d’où vous pouvez lancer le flux de connexion. 

* Accédez directement à l’URL de connexion à Sprinklr et lancez-y le flux de connexion.

* Vous pouvez utiliser Mes applications de Microsoft. Quand vous cliquez sur la vignette Sprinklr dans Mes applications, une redirection est effectuée vers l’URL de connexion à Sprinklr. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré Sprinklr, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
