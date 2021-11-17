---
title: 'Tutoriel : Intégration de l’authentification unique Azure AD à AnswerHub'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et AnswerHub.
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
ms.openlocfilehash: 9eb25fd7cc96c960ff8020a29b8801d0e8ea8853
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132318362"
---
# <a name="tutorial-azure-ad-sso-integration-with-answerhub"></a>Tutoriel : Intégration de l’authentification unique Azure AD à AnswerHub

Dans ce tutoriel, vous allez apprendre à intégrer Azure Active Directory (Azure AD) à AnswerHub. Quand vous intégrez Azure AD à AnswerHub, vous pouvez :

* Contrôler dans Azure AD qui a accès à AnswerHub.
* Permettre à vos utilisateurs de se connecter automatiquement à AnswerHub avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement AnswerHub pour lequel l’authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* AnswerHub prend en charge l’authentification unique lancée par le fournisseur de services.

## <a name="add-answerhub-from-the-gallery"></a>Ajouter AnswerHub à partir de la galerie

Pour configurer l’intégration d’AnswerHub à Azure AD, vous devez ajouter AnswerHub depuis la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **AnswerHub** dans la zone de recherche.
1. Sélectionnez **AnswerHub** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="set-up-and-test-azure-ad-sso-for-answerhub"></a>Configurer et tester l’authentification unique Azure AD pour AnswerHub

Configurez et testez l’authentification unique Azure AD avec AnswerHub à l’aide d’un utilisateur test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur AnswerHub associé.

Pour configurer et tester l’authentification unique Azure AD avec AnswerHub, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique d’AnswerHub](#configure-answerhub-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur test AnswerHub](#create-answerhub-test-user)** pour avoir un équivalent de B.Simon dans AnswerHub, lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **AnswerHub**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    a. Dans la zone **Identifier (Entity ID)** [Identificateur (ID d’entité)], entrez une URL au format suivant : `https://<company>.answerhub.com`
    
    b. Dans la zone **Sign on URL** (URL de connexion), entrez une URL au format suivant : `https://<company>.answerhub.com`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur et l’URL de connexion réels. Pour obtenir ces valeurs, contactez l’[équipe du support technique AnswerHub](mailto:success@answerhub.com). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

5. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, sélectionnez le lien **Télécharger** à côté du **certificat (Base64)** correspondant à vos besoins, puis enregistrez le certificat sur votre ordinateur.

    ![Lien de téléchargement du certificat](common/certificatebase64.png)

6. Dans la section **Configurer AnswerHub**, copiez la ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à AnswerHub.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **AnswerHub**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-answerhub-sso"></a>Configurer l’authentification unique d’AnswerHub

Dans cette section, vous allez configurer l’authentification unique pour AnswerHub.  

**Pour configurer l’authentification unique AnswerHub :**

1. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise AnswerHub en tant qu’administrateur.

    > [!NOTE]
    > Si vous avez besoin d’aide pour la configuration d’AnswerHub, contactez l’[équipe du support technique AnswerHub](mailto:success@answerhub.com.).

2. Accédez à **Administration**.

3. Sous l’onglet **Users and Groups** (Utilisateurs et groupes), dans le volet de gauche, dans la section **Social Settings** (Paramètres sociaux), sélectionnez **SAML Setup** (Configurer SAML).

4. Sous l’onglet **IDP Config** (Configurer le fournisseur d’identité), effectuez les étapes suivantes :

    ![Capture d’écran montrant la page AnswerHub avec l’onglet Users & Groups sélectionné.](./media/answerhub-tutorial/admin.png "SAML Setup")  

    a. Dans la zone **IDP Login URL** (URL de connexion du fournisseur d’identité), collez la valeur de l’**URL de connexion** que vous avez copiée à partir du portail Azure.

    b. Dans la zone **IDP Logout URL** (URL de déconnexion du fournisseur d’identité), collez la valeur de l’**URL de déconnexion** que vous avez copiée à partir du portail Azure.

    c. Dans la zone **IDP Name Identifier Format** (Format de l’identificateur du nom IDP), entrez la valeur d’**identificateur** que vous avez sélectionnée dans la section **Attributs utilisateur** du portail Azure.

    d. Sélectionnez **Keys and Certificates** (Clés et certificats).

5. Dans la section **Keys and Certificates**, effectuez les étapes suivantes :

    ![Section Keys and Certificates](./media/answerhub-tutorial/users.png "Keys and Certificates")  

    a. Dans le Bloc-notes, ouvrez le certificat Base64 que vous avez téléchargé à partir du portail Azure, copiez son contenu, puis collez-le dans la zone **IDP Public Key (x509 Format)** [Clé publique IDP (format x509)].

    b. Sélectionnez **Enregistrer**.

6. Sous l’onglet **Configurer le fournisseur d’identité** (IDP Config), sélectionnez à nouveau **Enregistrer**.

### <a name="create-answerhub-test-user"></a>Créer un utilisateur de test AnswerHub

Pour autoriser les utilisateurs Azure AD à se connecter à AnswerHub, vous devez les ajouter dans AnswerHub. Dans AnswerHub, cette opération doit être effectuée manuellement.

**Pour configurer un compte d’utilisateur :**

1. Connectez-vous à votre site d’entreprise **AnswerHub** en tant qu’administrateur.

2. Accédez à **Administration**.

3. Sélectionnez l’onglet **Users & Groups** (Utilisateurs et groupes).

4. Dans le volet de gauche, dans la section **Manage Users** (Gérer les utilisateurs), sélectionnez **Create or import users** (Créer ou importer des utilisateurs), puis sélectionnez **Users & Groups** (Utilisateurs et groupes).

    ![Capture d’écran montrant la page AnswerHub avec l’onglet Users & Groups sélectionné et le lien Create or import users activé.](./media/answerhub-tutorial/groups.png "Utilisateurs et groupes")

5. Dans les zones appropriées, entrez les valeurs **Email address** (Adresse e-mail), **Username** (Nom d’utilisateur) et **Password** (Mot de passe) d’un compte Azure AD valide que vous souhaitez ajouter, puis sélectionnez **Save** (Enregistrer).

> [!NOTE]
> Pour configurer des comptes d’utilisateur Azure AD, vous pouvez utiliser n’importe quel autre outil ou API de création de compte d’utilisateur fourni par AnswerHub.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL d’authentification d’AnswerHub, à partir de laquelle vous pouvez lancer le processus de connexion. 

* Accédez directement à l’URL d’authentification d’AnswerHub pour lancer le processus de connexion.

* Vous pouvez utiliser Mes applications de Microsoft. Quand vous cliquez sur la vignette AnswerHub dans Mes applications, vous êtes redirigé vers l’URL d’authentification d’AnswerHub. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré AnswerHub, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
