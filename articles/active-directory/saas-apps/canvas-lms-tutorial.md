---
title: 'Tutoriel : Intégration de l’authentification unique Azure AD à Canvas'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Canvas.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/09/2021
ms.author: jeedes
ms.openlocfilehash: bfd75937c84f11f2829920850c3148d92cccd05d
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132296534"
---
# <a name="tutorial-azure-ad-sso-integration-with-canvas"></a>Tutoriel : Intégration de l’authentification unique Azure AD à Canvas

Dans ce tutoriel, vous allez apprendre à intégrer Canvas à Azure Active Directory (Azure AD). Quand vous intégrez Canvas à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Canvas.
* Permettre à vos utilisateurs de se connecter automatiquement à Canvas avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :
 
* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Canvas pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Canvas prend en charge l’authentification unique lancée par le **fournisseur de services**.

## <a name="add-canvas-from-the-gallery"></a>Ajouter Canvas à partir de la galerie

Pour configurer l’intégration de Canvas à Azure AD, vous devez ajouter Canvas depuis la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Canvas** dans la zone de recherche.
1. Sélectionnez **Canvas** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-canvas"></a>Configurer et tester l’authentification unique Azure AD pour Canvas

Configurez et testez l’authentification unique Azure AD avec Canvas pour un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur associé dans Canvas.

Pour configurer et tester l’authentification unique Azure AD avec Canvas, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Canvas](#configure-canvas-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test Canvas](#create-canvas-test-user)** pour avoir dans Canvas un équivalent de B.Simon lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **Canvas**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    b. Dans la zone de texte **URL de connexion**, saisissez une URL au format suivant : `https://<tenant-name>.instructure.com`

    a. Dans la zone de texte **Identificateur (ID d’entité)** , saisissez une URL au format suivant : `https://<tenant-name>.instructure.com/saml2`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur et l’URL de connexion réels. Pour obtenir ces valeurs, contactez l’[équipe du support technique de Canvas](https://community.canvaslms.com/community/help). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

5. Dans la section **Certificat de signature SAML**, cliquez sur le bouton **Modifier** pour ouvrir la boîte de dialogue **Certificat de signature SAML**.

    ![Modifier le certificat de signature SAML](common/edit-certificate.png)

6. Dans la section **Certificat de signature SAML**, copiez l’**EMPREINTE** et enregistrez-la sur votre ordinateur.

    ![Copier la valeur de l’empreinte](common/copy-thumbprint.png)

7. Dans la section **Configurer Canvas**, copiez la ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Canvas.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Canvas**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-canvas-sso"></a>Configurer l’authentification unique Canvas

1. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Canvas en tant qu’administrateur.

2. Accédez à **Courses \> Managed Accounts \> Microsoft**.

    ![Canevas](./media/canvas-lms-tutorial/course.png "Canevas")

3. Dans le volet de navigation sur la gauche, sélectionnez **Authentication**, puis cliquez sur **Add New SAML Config**.

    ![Authentification](./media/canvas-lms-tutorial/tools.png "Authentification")

4. Dans la page Intégration actuelle, procédez comme suit :

    ![Intégration actuelle](./media/canvas-lms-tutorial/save.png "Intégration actuelle")

    a. Dans la zone de texte **IdP Entity ID** (Identificateur d’entité du fournisseur d’identité), collez l’**Identificateur Azure AD** que vous avez copié dans le portail Azure.

    b. Dans la zone de texte **Log On URL** (URL de connexion), collez la valeur **URL de connexion** que vous avez copiée dans le portail Azure.

    c. Dans la zone de texte **Log Out URL** (URL de déconnexion), collez la valeur **URL de déconnexion** que vous avez copiée dans le portail Azure.

    d. Dans la zone de texte **Change Password Link** (Lien de modification de mot de passe), collez la valeur de l’**URL de modification de mot de passe** que vous avez copiée à partir du portail Azure.

    e. Dans la zone de texte **Empreinte du certificat**, collez la valeur du certificat **Empreinte** que vous avez copiée à partir du portail Azure.

    f. Dans la liste **Login Attribute**, sélectionnez **NameID**.

    g. Dans la liste **Identifier Format**, sélectionnez **emailAddress**.

    h. Cliquez sur **Save Authentication Settings**.

### <a name="create-canvas-test-user"></a>Créer un utilisateur de test Canvas

Pour permettre aux utilisateurs Azure AD de se connecter à Canvas, vous devez les approvisionner dans Canvas. En l’occurrence, cette attribution d’utilisateurs est une tâche manuelle.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous à votre locataire **Canvas** .

2. Accédez à **Courses \> Managed Accounts \> Microsoft**.

   ![Canevas](./media/canvas-lms-tutorial/course.png "Canevas")

3. Cliquez sur **Utilisateurs**.

   ![Capture d’écran montrant le menu Canvas avec Users sélectionné.](./media/canvas-lms-tutorial/user.png "Utilisateurs")

4. Cliquez sur **Add New User**.

   ![Capture d’écran montrant la page Add a new User.](./media/canvas-lms-tutorial/add-user.png "Utilisateurs")

5. Dans la page Ajouter un nouvel utilisateur, procédez comme suit :

   ![Ajouter un utilisateur](./media/canvas-lms-tutorial/name.png "Ajouter un utilisateur")

   a. Dans la zone de texte **Full Name** (Nom complet), entrez le nom d’un utilisateur, par exemple **Britta Simon**.

   b. Dans la zone de texte **E-mail**, entrez l’e-mail de l’utilisateur, par exemple **brittasimon\@contoso.com**.

   c. Dans la zone de texte **Login** (Connexion), entrez l’adresse e-mail Azure AD de l’utilisateur, par exemple **brittasimon\@contoso.com**.

   d. Sélectionnez **Email the user about this account creation**.

   e. Cliquez sur **Add User**.

> [!NOTE]
> Vous pouvez utiliser tout autre outil ou n’importe quelle API de création de compte d’utilisateur fournis par Canvas pour provisionner des comptes d’utilisateurs Azure AD.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion à Canvas, d’où vous pouvez lancer le flux de connexion. 

* Accédez directement à l’URL de connexion à Canvas pour lancer le flux de connexion.

* Vous pouvez utiliser Mes applications de Microsoft. Quand vous cliquez sur la vignette Canvas dans Mes applications, vous devez être connecté automatiquement à l’application Canvas pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré Canvas, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
