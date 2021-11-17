---
title: 'Tutoriel : Intégration de l’authentification unique Azure AD à Igloo Software'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Igloo Software.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/04/2021
ms.author: jeedes
ms.openlocfilehash: cf601bbb7894640c512f00d736e93252cbf8dde7
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132305082"
---
# <a name="tutorial-azure-ad-sso-integration-with-igloo-software"></a>Tutoriel : Intégration de l’authentification unique Azure AD à Igloo Software

Dans ce tutoriel, vous allez apprendre à intégrer Igloo Software à Azure Active Directory (Azure AD). Quand vous intégrez Azure AD à Igloo Software, vous pouvez :

* Contrôler dans Azure AD qui a accès à Igloo Software.
* Permettre à vos utilisateurs de se connecter automatiquement à Igloo Software avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Igloo Software pour lequel l’authentification unique (SSO) est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Igloo Software prend en charge l’authentification unique lancée par le **fournisseur de services**.
* Igloo Software prend en charge l’attribution d’utilisateurs **juste-à-temps**.

## <a name="add-igloo-software-from-the-gallery"></a>Ajouter Igloo Software à partir de la galerie

Pour configurer l’intégration d’Igloo Software à Azure AD, vous devez ajouter Igloo Software à partir de la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Igloo Software** dans la zone de recherche.
1. Sélectionnez **Igloo Software** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-igloo-software"></a>Configurer et tester l’authentification unique Azure AD pour Igloo Software

Configurez et testez l’authentification unique Azure AD avec Igloo Software à l’aide d’un utilisateur test appelé **B. Simon**. Pour que l’authentification unique fonctionne, vous devez établir une relation entre un utilisateur Azure AD et l’utilisateur associé dans Igloo Software.

Pour configurer et tester l’authentification unique Azure AD avec Igloo Software, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique d’Igloo Software](#configure-igloo-software-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur test Igloo Software](#create-igloo-software-test-user)** pour avoir un équivalent de B.Simon dans Igloo Software lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **Igloo Software**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    a. Dans la zone de texte **Identificateur**, tapez une URL en utilisant le format suivant : `https://<company name>.igloocommmunities.com/saml.digest`

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://<company name>.igloocommmunities.com/saml.digest`
    
    c. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<company name>.igloocommmunities.com`.

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL d’authentification et l’URL de réponse réels. Pour obtenir ces valeurs, contactez [l’équipe de support technique Igloo Software](https://www.igloosoftware.com/services/support). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

5. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (Base64)** en fonction des options définies par rapport à vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

6. Dans la section **Configurer Igloo Software**, copiez les URL appropriées, selon vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Igloo Software.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Igloo Software**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-igloo-software-sso"></a>Configurer l’authentification unique d’Igloo Software

1. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Igloo Software en tant qu’administrateur.

2. Accédez au **Panneau de configuration**.

     ![Panneau de configuration](./media/igloo-software-tutorial/settings.png "Control panel")

3. Sous l’onglet **Appartenance**, cliquez sur **Paramètres de connexion**.

    ![Sign In Settings](./media/igloo-software-tutorial/resource.png "Sign In Settings")

4. Dans la section Configuration SAML, cliquez sur **Configurer l’authentification SAML**.

    ![Configuration SAML](./media/igloo-software-tutorial/authentication.png "Configuration SAML")

5. Dans la section **Configuration générale**, procédez comme suit :

    ![General Configuration](./media/igloo-software-tutorial/certificate.png "General Configuration")

    a. Dans la zone de texte **Nom de connexion**, tapez un nom personnalisé pour votre configuration.

    b. Dans la zone de texte **IdP Login URL** (URL de connexion IdP), collez la valeur **URL de connexion** que vous avez copiée dans le portail Azure.

    c. Dans la zone de texte **IdP Logout URL** (URL de déconnexion IdP), collez la valeur de l’**URL de déconnexion** que vous avez copiée à partir du portail Azure.

    d. Pour **Logout Response and Request HTTP Type** (Type de réponse de déconnexion et de requête HTTP), sélectionnez **POST**.

    e. Ouvrez dans le Bloc-notes votre certificat codé en **base 64** téléchargé à partir du portail Azure, copiez son contenu dans le Presse-papiers, puis collez-le dans la zone de texte **Certificat public**.

6. Dans **Configuration de l’authentification et de la réponse**, procédez comme suit :

    ![Response and Authentication Configuration](./media/igloo-software-tutorial/response.png "Response and Authentication Configuration")
  
    a. Dans **Fournisseur d’identité**, sélectionnez **Microsoft ADFS**.

    b. Dans **Type d’identificateur**, sélectionnez **Adresse de messagerie**. 

    c. Dans la zone de texte **Attribut du message**, tapez **emailaddress**.

    d. Dans la zone de texte **Attribut de prénom**, tapez **givenname**.

    e. Dans la zone de texte **Attribut de nom**, tapez **surname**.

7. Pour terminer la configuration, procédez comme suit :

    ![Création d’utilisateurs à l’authentification](./media/igloo-software-tutorial/users.png "Création d’utilisateurs à l’authentification") 

    a. Dans **Création de l’utilisateur lors de la connexion**, sélectionnez **Créer un nouvel utilisateur dans votre site lors de sa connexion**.

    b. Dans **Paramètres de connexion**, sélectionnez **Utiliser le bouton SAML dans l’écran « Connexion »**.

    c. Cliquez sur **Enregistrer**.

### <a name="create-igloo-software-test-user"></a>Créer un utilisateur de test Igloo Software

Aucun élément d’action ne vous permet de configurer l’approvisionnement des utilisateurs dans Igloo Software.  

Lorsqu’un utilisateur assigné tente de se connecter à Igloo Software à l’aide du panneau d’accès, Igloo Software vérifie si cet utilisateur existe.  Si aucun compte d’utilisateur n’est disponible, Igloo Software le crée automatiquement.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL d’authentification d’Igloo Software, à partir de laquelle vous pouvez lancer le processus de connexion. 

* Accédez directement à l’URL d’authentification d’Igloo Software pour lancer le processus de connexion.

* Vous pouvez utiliser Mes applications de Microsoft. Quand vous cliquez sur la vignette Igloo Software dans Mes applications, vous êtes redirigé vers l’URL d’authentification d’Igloo Software. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré Igloo Software, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
