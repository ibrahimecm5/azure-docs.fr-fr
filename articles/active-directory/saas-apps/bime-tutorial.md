---
title: 'Tutoriel : Intégration de l’authentification unique Azure AD à Bime'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Bime.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/12/2021
ms.author: jeedes
ms.openlocfilehash: ed86fb88f221a92d829ad4f1ee59bffbd3315597
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132346588"
---
# <a name="tutorial-azure-ad-sso-integration-with-bime"></a>Tutoriel : Intégration de l’authentification unique Azure AD à Bime

Dans ce tutoriel, vous allez apprendre à intégrer Bime à Azure Active Directory (Azure AD). Quand vous intégrez Azure AD à Bime, vous pouvez :

* Contrôler dans Azure AD qui a accès à Bime.
* Permettre à vos utilisateurs de se connecter automatiquement à Bime avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Bime pour lequel l’authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Bime prend en charge l’authentification unique lancée par le **fournisseur de services**.

## <a name="add-bime-from-the-gallery"></a>Ajouter Bime à partir de la galerie

Pour configurer l’intégration de Bime à Azure AD, vous devez ajouter Bime, disponible dans la galerie, à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Bime** dans la zone de recherche.
1. Sélectionnez **Bime** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-bime"></a>Configurer et tester l’authentification unique Azure AD pour Bime

Configurez et testez l’authentification unique Azure AD avec Bime à l’aide d’un utilisateur test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Bime associé.

Pour configurer et tester l’authentification unique Azure AD avec Bime, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique de Bime](#configure-bime-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur test Bime](#create-bime-test-user)** pour avoir un équivalent de B.Simon dans Bime lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **Bime**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    a. Dans la zone de texte **Identificateur (ID d’entité)** , saisissez une URL au format suivant : `https://<tenant-name>.Bimeapp.com`

    b. Dans la zone de texte **URL de connexion**, saisissez une URL au format suivant : `https://<tenant-name>.Bimeapp.com`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur et l’URL de connexion réels. Pour obtenir ces valeurs, contactez [l’équipe du support Bime](https://bime.zendesk.com/hc/categories/202604307-Support-tech-notes-and-tips-). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

5. Dans la section **Certificat de signature SAML**, cliquez sur le bouton **Modifier** pour ouvrir la boîte de dialogue **Certificat de signature SAML**.

    ![Modifier le certificat de signature SAML](common/edit-certificate.png)

6. Dans la section **Certificat de signature SAML**, copiez l’**EMPREINTE** et enregistrez-la sur votre ordinateur.

    ![Copier la valeur de l’empreinte](common/copy-thumbprint.png)

7. Dans la section **Configurer Bime**, copiez la ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Bime.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Bime**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-bime-sso"></a>Configurer l’authentification unique de Bime

1. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Bime en tant qu’administrateur.

2. Dans la barre d’outils, cliquez sur **Admin**, puis sur **Account**.

    ![Capture d’écran montrant l’élément Admin et Account sélectionnés.](./media/bime-tutorial/account.png "Admin")

3. Dans la page de configuration du compte, procédez comme suit :

    ![Configurer l’authentification unique](./media/bime-tutorial/configuration.png "Configure Single Sign-On")

    a. Sélectionnez **Activer l’authentification SAML**.

    b. Dans la zone de texte **Remote Login URL** (URL de connexion à distance), collez la valeur **URL de connexion** que vous avez copiée dans le portail Azure.

    c. Dans la zone de texte **Certificate Fingerprint** (Empreinte digitale du certificat), collez la valeur **Empreinte** que vous avez copiée à partir du portail Azure.

    d. Cliquez sur **Enregistrer**.

### <a name="create-bime-test-user"></a>Créer un utilisateur de test Bime

Pour permettre aux utilisateurs Azure AD de se connecter à Bime, vous devez les attribuer dans Bime. En l’occurrence, cet approvisionnement est une tâche manuelle.

**Pour configurer l'approvisionnement des utilisateurs, procédez comme suit :**

1. Connectez-vous à votre locataire **Bime** .

2. Dans la barre d’outils, cliquez sur **Admin**, puis sur **Users**.

    ![Capture d’écran montrant l’élément Admin et Users sélectionnés.](./media/bime-tutorial/user.png "Admin")

3. Dans la **Users List**, cliquez sur **Add New User** (« + »).

    ![Utilisateurs](./media/bime-tutorial/add-user.png "Utilisateurs")

4. Dans la page de boîte de dialogue **User Details** , procédez comme suit :

    ![Détails de l’utilisateur](./media/bime-tutorial/create-user.png "User Details")

    a. Dans la zone de texte **First name**, entrez le prénom de l’utilisateur, par exemple **Britta**.

    b. Dans la zone de texte **Last name**, tapez le nom de l’utilisateur, par exemple **Simon**.

    c. Dans la zone de texte **E-mail**, entrez l’e-mail de l’utilisateur, par exemple **brittasimon\@contoso.com**.

    d. Cliquez sur **Enregistrer**.

> [!NOTE]
> Vous pouvez utiliser tout autre outil ou n’importe quelle API de création de compte d’utilisateur fournis par Bime pour provisionner des comptes d’utilisateurs Azure AD.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL d’authentification de Bime, à partir de laquelle vous pouvez lancer le processus de connexion. 

* Accédez directement à l’URL d’authentification de Bime pour lancer le processus de connexion.

* Vous pouvez utiliser Mes applications de Microsoft. Quand vous cliquez sur la vignette Bime dans Mes applications, vous êtes redirigé vers l’URL d’authentification de Bime. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez configuré Bime, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
