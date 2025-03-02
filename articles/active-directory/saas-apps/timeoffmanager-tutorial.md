---
title: 'Tutoriel : Intégration de l’authentification unique Azure Active Directory à TimeOffManager | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et TimeOffManager.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/10/2019
ms.author: jeedes
ms.openlocfilehash: dffac967e42e817323c94f4b1e48403a2c779455
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124733730"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-timeoffmanager"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory à TimeOffManager

Dans ce tutoriel, vous allez découvrir comment intégrer TimeOffManager à Azure Active Directory (Azure AD). Quand vous intégrez TimeOffManager à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à TimeOffManager.
* Permettre à vos utilisateurs de se connecter automatiquement à TimeOffManager avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS à Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement TimeOffManager pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.


* TimeOffManager prend en charge l’authentification unique lancée par le **fournisseur d’identité**

* TimeOffManager prend en charge le provisionnement d’utilisateurs **juste-à-temps**

> [!NOTE]
> L’identificateur de cette application étant une valeur de chaîne fixe, une seule instance peut être configurée dans un locataire.


## <a name="adding-timeoffmanager-from-the-gallery"></a>Ajout de TimeOffManager à partir de la galerie

Pour configurer l’intégration de TimeOffManager avec Azure AD, vous devez ajouter TimeOffManager, disponible dans la galerie, à votre liste d’applications SaaS gérées.

1. Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **TimeOffManager** dans la zone de recherche.
1. Sélectionnez **TimeOffManager** dans le volet des résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.


## <a name="configure-and-test-azure-ad-single-sign-on-for-timeoffmanager"></a>Configurer et tester l’authentification unique Azure AD pour TimeOffManager

Configurez et testez l’authentification unique Azure AD avec TimeOffManager à l’aide d’un utilisateur de test nommé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur TimeOffManager associé.

Pour configurer et tester l’authentification unique Azure AD avec TimeOffManager, suivez les indications des sections ci-après :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique TimeOffManager](#configure-timeoffmanager-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test TimeOffManager](#create-timeoffmanager-test-user)** pour avoir un équivalent de B.Simon dans TimeOffManager lié à la représentation Azure AD associée.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le [portail Azure](https://portal.azure.com/), accédez à la page d’intégration de l’application **TimeOffManager**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de modification/stylet de **Configuration SAML de base** pour modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, entrez les valeurs pour les champs suivants :

    Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://www.timeoffmanager.com/cpanel/sso/consume.aspx?company_id=<companyid>`

    > [!NOTE]
    > Cette valeur n’est pas la valeur réelle. Mettez à jour la valeur avec l’URL de réponse réelle. Vous pouvez obtenir cette valeur à partir de la **page des paramètres d’authentification unique**, décrite plus loin dans le didacticiel, ou contacter l’[équipe de support technique TimeOffManager](https://www.purelyhr.com/contact-us). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. L’application TimeOffManager s’attend à recevoir les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à votre configuration des attributs de jetons SAML. La capture d’écran suivante montre la liste des attributs par défaut.

    ![image](common/edit-attribute.png)

1. En plus de ce qui précède, l’application TimeOffManager s’attend à ce que quelques attributs supplémentaires soient repassés dans la réponse SAML. Ces attributs sont également préremplis, mais vous pouvez les examiner pour voir s’ils répondent à vos besoins.

    | Nom | Attribut source|
    | --- | --- |
    | Firstname |User.givenname |
    | Lastname |User.Surname |
    | E-mail |User.mail |

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **Certificat (en base64)** , puis sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

1. Dans la section **Configurer TimeOffManager**, copiez la ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à TimeOffManager.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **TimeOffManager**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.

   ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Lien Ajouter un utilisateur](common/add-assign-user.png)

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-timeoffmanager-sso&quot;></a>Configurer l’authentification unique TimeOffManager

1. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise TimeOffManager en tant qu’administrateur.

2. Accédez à **Account \> Account Options \> Single Sign-On Settings**.
   
    ![Capture d’écran montrant Single Sign-On Settings sélectionné dans Account Options.](./media/timeoffmanager-tutorial/ic795917.png &quot;Paramètres d’authentification unique")

3. Dans la section **Single Sign-On Settings** , procédez comme suit :
   
    ![Capture d’écran montrant la section Single Sign-On où vous pouvez entrer les valeurs décrites.](./media/timeoffmanager-tutorial/ic795918.png "Paramètres d’authentification unique")
   
    a. Ouvrez votre certificat codé en base 64 dans le bloc-notes, copiez son contenu dans le Presse-papiers et collez-le dans la zone de texte **X.509 Certificate** .
   
    b. Dans la zone de texte **Idp Issuer**, collez la valeur **Identificateur Azure AD** que vous avez copiée à partir du portail Azure.
   
    c. Dans la zone de texte **IdP Endpoint URL**, collez la valeur **URL de connexion** que vous avez copiée dans le portail Azure.
   
    d. Dans **Enforce SAML**, sélectionnez **No**.
   
    e. Dans **Auto-Create Users**, sélectionnez **Yes**.
   
    f. Dans la zone de texte **Logout URL** (URL de déconnexion), collez la valeur **URL de déconnexion** que vous avez copiée dans le Portail Azure.
   
    g. Cliquez sur **Enregistrer les modifications**.

4. Dans la page **Single Sign on settings**, copiez la valeur **Assertion Consumer Service URL** et collez-la dans la zone de texte **URL de réponse** de la section **Configuration SAML de base** dans le portail Azure. 

    ![Capture d’écran montrant le lien Assertion Consumer Service URL.](./media/timeoffmanager-tutorial/ic795915.png "Paramètres d’authentification unique")

### <a name="create-timeoffmanager-test-user"></a>Créer un utilisateur de test TimeOffManager

Dans cette section, un utilisateur appelé Britta Simon est créé dans TimeOffManager. TimeOffManager prend en charge le provisionnement d’utilisateurs juste-à-temps, option activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas encore d’utilisateur dans TimeOffManager, il en est créé un après l’authentification.

>[!NOTE]
>Vous pouvez utiliser n’importe quel autre outil ou API de création de compte d’utilisateur, fourni par TimeOffManager, pour approvisionner des comptes d’utilisateur Azure AD.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette TimeOffManager dans le volet d’accès, vous devez être connecté automatiquement à l’application TimeOffManager pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de tutoriels sur l’intégration d’applications SaaS avec Azure Active Directory](./tutorial-list.md)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](../conditional-access/overview.md)

- [Essayer TimeOffManager avec Azure AD](https://aad.portal.azure.com/)