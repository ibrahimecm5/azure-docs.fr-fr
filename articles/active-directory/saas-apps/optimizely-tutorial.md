---
title: 'Didacticiel : Intégration d’Azure Active Directory à Optimizely |Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Optimizely.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/24/2021
ms.author: jeedes
ms.openlocfilehash: 4a2b1064845c338d0cf5f45b120748f15c4f73e2
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132334147"
---
# <a name="tutorial-azure-active-directory-integration-with-optimizely"></a>Didacticiel : Intégration d’Azure Active Directory avec Optimizely

Dans ce tutoriel, vous allez apprendre à intégrer Optimizely à Azure Active Directory (Azure AD). Lorsque vous intégrez Optimizely à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Optimizely.
* Permettre aux utilisateurs de se connecter automatiquement à Optimizely avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Optimizely pour lequel l'authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Optimizely prend en charge l'authentification unique lancée par le **fournisseur de services**.

## <a name="add-optimizely-from-the-gallery"></a>Ajouter Optimizely à partir de la galerie

Pour configurer l’intégration d’Optimizely avec Azure AD, vous devez ajouter Optimizely à partir de la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, entrez **Optimizely**  dans la zone de recherche.
1. Sélectionnez **Optimizely** dans le volet de résultats, puis ajoutez l'application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-optimizely"></a>Configurer et tester l'authentification unique Azure AD pour Optimizely

Configurez et testez l'authentification unique Azure AD auprès d'Optimizely à l'aide d'un utilisateur de test appelé **B.Simon**. Pour que l'authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l'utilisateur associé dans Optimizely.

Pour configurer et tester l'authentification unique Azure AD auprès d'Optimizely, suivez les étapes ci-dessous :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l'authentification unique Optimizely](#configure-optimizely-sso)** pour configurer les paramètres de l'authentification unique côté application.
    1. **[Créer un utilisateur de test Optimizely](#create-optimizely-test-user)** pour avoir, dans Optimizely, un équivalent de B.Simon lié à la représentation Azure AD associée.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Sur le portail Azure, accédez à la page d'intégration de l'application **Optimizely**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    a. Dans la zone de texte **URL de connexion**, saisissez une URL au format suivant : `https://app.optimizely.net/<INSTANCE_NAME>`

    b. Dans la zone de texte **Identificateur (ID d'entité)** , entre une valeur au format suivant : `urn:auth0:optimizely:contoso`

    > [!NOTE]
    > Il ne s’agit pas des valeurs réelles. Vous mettrez à jour la valeur avec l’URL de connexion et l’identificateur réels. La procédure est expliquée plus loin dans le didacticiel. Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

5. Votre application Optimizely s’attend à recevoir les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à votre configuration des attributs du jeton SAML. La capture d’écran suivante montre la liste des attributs par défaut. Cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Attributs d’utilisateur**.

    ![Capture d’écran montrant la boîte de dialogue « Attributs d’utilisateur » avec la sélection de l’icône « Modifier » en haut à droite.](common/edit-attribute.png)

6. En plus de ce qui précède, l’application Optimizely s’attend à ce que quelques attributs supplémentaires soient repassés dans la réponse SAML. Dans la section **Revendications des utilisateurs** de la boîte de dialogue **Attributs utilisateur**, effectuez les étapes suivantes pour ajouter le jeton SAML comme indiqué dans le tableau ci-dessous :

    | Name | Attribut source |
    | ---------------| --------------- |
    | email | user.mail |
    
    a. Cliquez sur le bouton **Ajouter une nouvelle revendication** pour ouvrir la boîte de dialogue **Gérer les revendications des utilisateurs**.

    ![Capture d’écran montrant la boîte de dialogue « Revendications des utilisateurs » avec les actions « Ajouter une nouvelle revendication » et « Enregistrer » mises en évidence.](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Dans la zone de texte **Attribut**, indiquez le nom d’attribut pour cette ligne.

    c. Laissez le champ **Espace de noms** vide.

    d. Sélectionnez Source comme **Attribut**.

    e. Dans la liste **Attribut de la source**, tapez la valeur d’attribut indiquée pour cette ligne.

    f. Cliquez sur **OK**

    g. Cliquez sur **Enregistrer**.

4. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (Base64)** en fonction des options définies par rapport à vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

6. Dans la section **Configurer Optimizely**, copiez la ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez permettre à B.Simon d'utiliser l'authentification unique Azure en lui accordant l'accès à Optimizely.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Optimizely**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-optimizely-sso"></a>Configurer l'authentification unique pour Optimizely

1. Pour configurer l’authentification unique côté **Optimizely**, contactez votre responsable de compte Optimizely et fournissez-lui le **certificat (Base64)** téléchargé et les URL copiées appropriées.

2. En réponse à votre e-mail, Optimizely vous fournit l’URL de connexion (authentification unique initiée par le fournisseur de service) et l’identificateur (ID d’entité du fournisseur de service).

    a. Copiez l’**URL d’authentification unique lancée par le fournisseur de services** fournie par Optimizely, puis collez-la dans la zone de texte **URL de connexion** de la section **Configuration SAML de base** sur le portail Azure.

    b. Copiez l’**ID d’entité du fournisseur de services** fourni par Optimizely, puis collez-le dans la zone de texte **Identificateur** de la section **Configuration SAML de base** sur le portail Azure.

3. Dans une autre fenêtre de navigateur, connectez-vous à votre application Optimizely.

4. Cliquez sur le nom de votre compte dans l’angle supérieur droit, puis sur **Paramètres du compte**.

    ![Capture d’écran montrant le nom de compte sélectionné dans l’angle supérieur droit, avec l’élément « Account Settings » sélectionné dans le menu.](./media/optimizely-tutorial/settings.png)

5. Sous l’onglet Compte, cochez la case **Activer l’authentification unique** sous Authentification unique dans la section **Vue d’ensemble**.
  
    ![Authentification unique Azure AD](./media/optimizely-tutorial/account.png)

6. Cliquez sur **Enregistrer**.

### <a name="create-optimizely-test-user"></a>Créer un utilisateur de test Optimizely

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans Optimizely.

1. Sur la page d’accueil, sélectionnez l’onglet **Collaborateurs**.

2. Pour ajouter un nouveau collaborateur au projet, cliquez sur **Nouveau collaborateur**.
   
    ![Capture d’écran montrant la page d’accueil Optimizely avec l’onglet « Collaborators » et le bouton « New Collaborator » sélectionnés.](./media/optimizely-tutorial/collaborator.png)

3. Renseignez l’adresse de messagerie et assignez un rôle au nouveau collaborateur. Cliquez sur **Invite**.

    ![Création d’un utilisateur de test Azure AD](./media/optimizely-tutorial/invite-collaborator.png)

4. Il reçoit une invitation par courrier électronique. À l’aide de l’adresse de messagerie, il doit se connecter à Optimizely.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l'URL de connexion à Optimizely, à partir de laquelle vous pouvez lancer le flux de connexion. 

* Accédez directement à l'URL de connexion à Optimizely pour lancer le flux de connexion.

* Vous pouvez utiliser Mes applications de Microsoft. Le fait de cliquer sur la vignette Optimizely dans Mes applications vous redirige vers l'URL de connexion à Optimizely. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré Optimizely, vous pouvez appliquer le contrôle de session, qui protège en temps réel contre l'exfiltration et l'infiltration des données sensibles de votre organisation. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
