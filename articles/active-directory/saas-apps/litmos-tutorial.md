---
title: 'Tutoriel : Intégration de l’authentification unique Azure Active Directory à Litmos | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Litmos.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/12/2021
ms.author: jeedes
ms.openlocfilehash: 37b7a431c38de3742a2b13c98f753823c125f4cf
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132287967"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-litmos"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory à Litmos

Dans ce tutoriel, vous allez découvrir comment intégrer Litmos à Azure Active Directory (Azure AD). Quand vous intégrez Litmos à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Litmos.
* Permettre à vos utilisateurs de se connecter automatiquement à Litmos avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Litmos pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* Litmos prend en charge l'authentification unique lancée par le **fournisseur d'identité**.
* Litmos prend en charge l'approvisionnement d'utilisateurs **Juste-à-temps**.

## <a name="add-litmos-from-the-gallery"></a>Ajouter Litmos à partir de la galerie

Pour configurer l’intégration de Litmos avec Azure AD, vous devez ajouter Litmos à partir de la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Litmos** dans la zone de recherche.
1. Sélectionnez **Litmos** dans le volet des résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-litmos"></a>Configurer et tester l'authentification unique Azure AD pour Litmos

Configurez et testez l’authentification unique Azure AD avec Litmos pour un utilisateur de test nommé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Litmos associé.

Pour configurer et tester l'authentification unique Azure AD auprès de Litmos, suivez les étapes ci-dessous :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Litmos](#configure-litmos-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test Litmos](#create-litmos-test-user)** pour avoir un équivalent de B.Simon dans Litmos lié à la représentation Azure AD associée.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Sur le portail Azure, accédez à la page d'intégration de l'application **Litmos**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la page **Configurer l’authentification unique avec SAML**, effectuez les étapes suivantes :

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<companyname>.litmos.com/account/Login`

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://<companyname>.litmos.com/integration/samllogin`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur et l’URL de réponse réels qui sont décrits plus loin dans le tutoriel, ou contactez [l’équipe de support technique Litmos](https://www.litmos.com/contact-us) pour obtenir ces valeurs. Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **Certificat (en base64)** , puis sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

1. Dans la section **Configurer Litmos**, copiez la ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Litmos.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Litmos**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-litmos-sso"></a>Configurer l’authentification unique Litmos

1. Dans une autre fenêtre de navigateur, connectez-vous à votre site d’entreprise Litmos en tant qu’administrateur.

2. Dans la barre de navigation située à gauche, cliquez sur **Accounts**.

    ![Section Comptes côté application](./media/litmos-tutorial/account.png)

3. Cliquez sur l’onglet **Integrations** .

    ![Onglet Intégration](./media/litmos-tutorial/integrate.png)

4. Sur l’onglet **Integrations**, accédez à **3rd Party Integrations**, puis cliquez sur l’onglet **SAML 2.0**.

    ![Section SAML 2.0](./media/litmos-tutorial/third-party.png)

5. Copiez la valeur sous **The SAML endpoint for litmos is** (Le point de terminaison SAML pour litmos est), puis collez-la dans la zone de texte **URL de réponse** de la section **Domaine et URL Litmos** du portail Azure.

    ![Point de terminaison SAML](./media/litmos-tutorial/certificate.png)

6. Dans votre application **Litmos** , procédez comme suit :

    ![Application Litmos](./media/litmos-tutorial/application.png)

    a. Cliquez sur **Enable SAML**.

    b. Ouvrez votre certificat codé en base 64 dans le Bloc-notes, copiez son contenu dans le Presse-papiers et collez-le dans la zone de texte **SAML X.509 Certificate** .

    c. Cliquez sur **Enregistrer les modifications**.

### <a name="create-litmos-test-user"></a>Créer un utilisateur de test Litmos

Dans cette section, un utilisateur appelé B.Simon est créé dans Litmos. Litmos prend en charge l'approvisionnement d'utilisateurs juste-à-temps (option activée par défaut). Vous n’avez aucune opération à effectuer dans cette section. S'il n'existe pas encore d'utilisateur dans Litmos, il en est créé un après l'authentification.

**Pour créer un utilisateur appelé Britta Simon dans Litmos, procédez comme suit :**

1. Dans une autre fenêtre de navigateur, connectez-vous à votre site d’entreprise Litmos en tant qu’administrateur.

2. Dans la barre de navigation située à gauche, cliquez sur **Accounts**.

    ![Section Comptes côté application](./media/litmos-tutorial/account.png)

3. Cliquez sur l’onglet **Integrations** .

    ![Onglet Intégrations](./media/litmos-tutorial/integrate.png)

4. Sur l’onglet **Integrations**, accédez à **3rd Party Integrations**, puis cliquez sur l’onglet **SAML 2.0**.

    ![SAML 2.0](./media/litmos-tutorial/third-party.png)

5. Sélectionner **Générer automatiquement les utilisateurs**
  
    ![Générer automatiquement les utilisateurs](./media/litmos-tutorial/users.png)

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes.

* Cliquez sur Tester cette application sur le portail Azure. Vous êtes alors automatiquement connecté à l'instance de Litmos pour laquelle vous avez configuré l'authentification unique.

* Vous pouvez utiliser Mes applications de Microsoft. Lorsque vous cliquez sur la vignette Litmos dans Mes applications, vous devez être automatiquement connecté à l'application Litmos pour laquelle vous avez configuré l'authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré Litmos, vous pouvez appliquer le contrôle de session, qui protège en temps réel contre l'exfiltration et l'infiltration des données sensibles de votre organisation. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
