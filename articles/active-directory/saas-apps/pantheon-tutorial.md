---
title: 'Tutoriel : Intégration de l’authentification unique Azure Active Directory à Pantheon | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Pantheon.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/09/2021
ms.author: jeedes
ms.openlocfilehash: e85a65a619f858a91b5b375bbb60c47adbb3af86
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132300160"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-pantheon"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory à Pantheon

Dans ce tutoriel, vous allez apprendre à intégrer Pantheon à Azure Active Directory (Azure AD). Quand vous intégrez Pantheon à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Pantheon.
* Permettre à vos utilisateurs de se connecter automatiquement à Pantheon avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Pantheon pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* Pantheon prend en charge l’authentification unique lancée par le **fournisseur d’identité**.

## <a name="add-pantheon-from-the-gallery"></a>Ajouter Pantheon à partir de la galerie

Pour configurer l’intégration de Pantheon avec Azure AD, vous devez ajouter Pantheon à partir de la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Pantheon** dans la zone de recherche.
1. Sélectionnez **Pantheon** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-pantheon"></a>Configurer et tester l’authentification unique Azure AD pour Pantheon

Configurez et testez l’authentification unique Azure AD avec Pantheon pour un utilisateur de test nommé **B. Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Pantheon associé.

Pour configurer et tester l’authentification unique Azure AD avec Pantheon, procédez comme suit :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Pantheon](#configure-pantheon-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test Pantheon](#create-pantheon-test-user)** pour obtenir un équivalent de B.Simon dans Pantheon lié à la représentation Azure AD associée.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le Portail Azure, accédez à la page d’intégration de l’application **Pantheon**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la page **Configurer l’authentification unique avec SAML**, effectuez les étapes suivantes :

    a. Dans la zone de texte **Identificateur**, tapez une valeur au format suivant : `urn:auth0:pantheon:<orgname>-SSO`

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://pantheon.auth0.com/login/callback?connection=<orgname>-SSO`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur et l’URL de réponse réels. Pour obtenir ces valeurs, contactez l’[équipe du support technique Pantheon](https://pantheon.io/docs/getting-support/). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. L’application Pantheon s’attend à recevoir les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à votre configuration des attributs du jeton SAML. La capture d’écran suivante montre la liste des attributs par défaut, où **nameidentifier** est mappé avec **user.userprincipalname**. L’application Pantheon s’attend à ce que **nameidentifier** soit mappé avec **user.mail**. Vous devez donc modifier le mappage d’attribut en cliquant sur l’icône **Modifier**.

    ![image](common/edit-attribute.png)

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **Certificat (en base64)** , puis sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

1. Dans la section **Configurer Pantheon**, copiez la ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Pantheon.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Pantheon**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-pantheon-sso"></a>Configurer l’authentification unique Pantheon

Pour configurer l’authentification unique côté **Pantheon**, vous devez envoyer le **Certificat(Base64)** téléchargé et les URL copiées appropriées à l’[équipe du support technique Pantheon](https://pantheon.io/docs/getting-support/).

> [!Note]
> Vous devez également fournir les informations des domaines de messagerie et des dates/heures auxquelles vous souhaitez activer cette connexion. Vous trouverez plus de détails à ce propos [ici](https://pantheon.io/docs/sso-organizations/).

### <a name="create-pantheon-test-user"></a>Créer un utilisateur de test Pantheon

Dans cette section, vous allez créer un utilisateur appelé B.Simon dans Pantheon. Suivez les étapes ci-dessous pour ajouter l’utilisateur dans Pantheon. 

>[!NOTE] 
>Pour que l’authentification unique fonctionne, vous devez d’abord créer un utilisateur dans Pantheon.

1. Connectez-vous à Pantheon avec les informations d’identification de l’administrateur.

2. Accédez à la page de tableau de bord **Organisation**.
 
3. Cliquez sur **People**.

4. Cliquez sur **Add User**.

5. Saisissez l’adresse de messagerie de l’utilisateur.

6. Choisissez le rôle de l’utilisateur.

7. Cliquez sur **Add User**.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes.

* Cliquez sur Tester cette application dans le Portail Azure : vous devez être connecté automatiquement à l’application de Pantheon pour laquelle vous avez configuré l’authentification unique.

* Vous pouvez utiliser Mes applications de Microsoft. Lorsque vous cliquez sur la mosaïque Pantheon dans Mes applications, vous devez être connecté automatiquement à l’application Pantheon pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez configuré Pantheon, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
