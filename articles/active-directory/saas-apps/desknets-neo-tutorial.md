---
title: 'Tutoriel : Intégration de l’authentification unique Azure Active Directory à desknet’s NEO | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et desknet’s NEO.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/08/2021
ms.author: jeedes
ms.openlocfilehash: 903c4beeb01899c47aada06f02fc5b24094dd2c3
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132348936"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-desknets-neo"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory à desknet’s NEO

Dans ce tutoriel, vous allez découvrir comment intégrer desknet’s NEO à Azure AD (Azure Active Directory). Quand vous intégrez desknet’s NEO à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à desknet’s NEO.
* Permettre à vos utilisateurs de se connecter automatiquement à desknet’s NEO avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement desknet’s NEO pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* desknet’s NEO prend en charge l’authentification unique lancée par le **fournisseur de services**.

## <a name="adding-desknets-neo-from-the-gallery"></a>Ajout de desknet’s NEO à partir de la galerie

Pour configurer l’intégration de desknet’s NEO à Azure AD, vous devez ajouter desknet’s NEO à partir de la galerie à votre liste d’applications SaaS managées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **desknet’s NEO** dans la zone de recherche.
1. Sélectionnez **desknet’s NEO** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.


## <a name="configure-and-test-azure-ad-sso-for-desknets-neo"></a>Configurer et tester l’authentification unique Azure AD pour desknet’s NEO

Configurez et testez l’authentification unique Azure AD avec desknet’s NEO pour un utilisateur test nommé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir une relation entre un utilisateur Azure AD et l’utilisateur desknet’s NEO associé.

Pour configurer et tester l’authentification unique Azure AD avec desknet’s NEO, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique desknet’s NEO](#configure-desknets-neo-sso)** pour définir les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test desknet’s NEO](#create-desknets-neo-test-user)** pour avoir un équivalent de B.Simon dans desknet’s NEO qui soit lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **desknet’s NEO**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, entrez les valeurs pour les champs suivants :

    a. Dans la zone de texte **Identificateur (ID d’entité)** , saisissez une URL au format suivant : `https://<CUSTOMER_NAME>.dn-cloud.com`

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://<CUSTOMER_NAME>.dn-cloud.com/cgi-bin/dneo/zsaml.cgi`

    c. Dans la zone de texte **URL de connexion**, saisissez une URL au format suivant : `https://<CUSTOMER_NAME>.dn-cloud.com/cgi-bin/dneo/dneo.cgi`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL d’authentification et l’URL de réponse réels. Pour obtenir ces valeurs, contactez l’[équipe du support technique de desknet’s NEO](mailto:cloudsupport@desknets.com). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **Certificat (en base64)** , puis sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

1. Dans la section **Configurer desknet’s NEO**, copiez la ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à desknet’s NEO.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **desknet’s NEO**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-desknets-neo-sso"></a>Configurer l’authentification unique desknet’s NEO

1. Connectez-vous à votre site d’entreprise desknet’s NEO en tant qu’administrateur.

1. Dans le menu, cliquez sur l’icône de **paramètres de liaison d’authentification SAML**.

    ![Capture d’écran des paramètres de liaison d’authentification SAML.](./media/desknets-neo-tutorial/saml-authentication-icon.png)

1. Dans **Common settings**, cliquez sur **use** dans SAML Authentication Collaboration.

    ![Capture d’écran de l’utilisation de l’authentification SAML.](./media/desknets-neo-tutorial/saml-authentication-use.png)

1. Effectuez les étapes ci-dessous dans la section de **paramètres de liaison d’authentification SAML**.

    ![Capture d’écran de la section des paramètres de liaison d’authentification SAML.](./media/desknets-neo-tutorial/saml-authentication.png)

    a. Dans la zone de texte **Access URL**, collez la valeur **URL de connexion** que vous avez copiée à partir du portail Azure.

    b. Dans la zone de texte **SP Entity ID**, collez la valeur **Identificateur** que vous avez copiée à partir du portail Azure.

    c. Cliquez sur **Choose File** pour charger le fichier de **Certificat (base64)** chargé à partir du portail Azure dans la zone de texte **x.509 Certificate**.

    d. Cliquez sur **change**.

### <a name="create-desknets-neo-test-user"></a>Créer un utilisateur de test desknet’s NEO

1. Connectez-vous à votre site d’entreprise desknet’s NEO en tant qu’administrateur.

1. Dans le **menu**, cliquez sur l’icône des **paramètres d’administrateur**.

    ![Capture d’écran des paramètres d’administrateur.](./media/desknets-neo-tutorial/administrator-settings.png)

1. Cliquez sur l’icône **paramètres** et sélectionnez **User management** dans **Custom settings**.

    ![Capture d’écran des paramètres de gestion utilisateur.](./media/desknets-neo-tutorial/user-management.png)

1. Cliquez sur **Create user information**.

    ![Capture d’écran du bouton User information.](./media/desknets-neo-tutorial/create-new-user.png)

1. Renseignez les champs requis dans la page ci-dessous, puis cliquez sur **creation**.

    ![Capture d’écran de la section User creation.](./media/desknets-neo-tutorial/create-new-user-2.png)

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion à desknet’s NEO, à partir de laquelle vous pouvez lancer le flux de connexion. 

* Accédez directement à l’URL de connexion à desknet’s NEO, puis lancez le flux de connexion.

* Vous pouvez utiliser Mes applications de Microsoft. Lorsque vous cliquez sur la vignette desknet’s NEO dans Mes applications, vous êtes redirigé vers l’URL de connexion à desknet’s NEO. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).


## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez configuré desknet’s NEO, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
