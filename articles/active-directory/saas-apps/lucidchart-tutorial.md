---
title: 'Tutoriel : Intégration d’Azure Active Directory à Lucidchart | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Lucidchart.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/15/2021
ms.author: jeedes
ms.openlocfilehash: b10162c1a79f30eb20ef57827c533e9fc93ee284
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132307558"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-lucidchart"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory à Lucidchart

Dans ce tutoriel, vous allez apprendre à intégrer Lucidchart à Azure Active Directory (Azure AD). Quand vous intégrez Lucidchart à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Lucidchart.
* Permettre à vos utilisateurs de se connecter automatiquement à Lucidchart avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Lucidchart pour lequel l’authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* Lucidchart prend en charge l’authentification unique initiée par le **fournisseur de services**
* Lucidchart prend en charge [l’attribution d’utilisateurs et la suppression des privilèges d’accès **automatisées**](lucidchart-provisioning-tutorial.md) (recommandé).
* Lucidchart prend en charge le provisionnement des utilisateurs **Juste-à-temps**

## <a name="add-lucidchart-from-the-gallery"></a>Ajouter LucidChart à partir de la galerie

Pour configurer l’intégration de Lucidchart à Azure AD, vous devez ajouter Lucidchart à partir de la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Lucidchart** dans la zone de recherche.
1. Sélectionnez **Lucidchart** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.


## <a name="configure-and-test-azure-ad-sso-for-lucidchart"></a>Configurer et tester l’authentification unique Azure AD pour Lucidchart

Configurez et testez l’authentification unique Azure AD avec Lucidchart à l’aide d’un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Lucidchart associé.

Pour configurer et tester l’authentification unique Azure AD avec Lucidchart, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    * **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    * **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Lucidchart](#configure-lucidchart-sso)** pour configurer les paramètres de l’authentification unique côté application.
    * **[Créer un utilisateur de test Lucidchart](#create-lucidchart-test-user)** pour avoir un équivalent de B.Simon dans Lucidchart lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans la page d’intégration de l’application **Lucidchart** du portail Azure, recherchez la section **Gérer**, puis sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, entrez les valeurs pour les champs suivants :

   Dans la zone de texte **URL de connexion**, tapez l’URL : `https://chart2.office.lucidchart.com/saml/sso/azure`

5. Sur la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le fichier **XML de métadonnées de fédération** en fonction des options définies selon vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

6. Dans la section **Set up Lucidchart** (Configurer Lucidchart), copiez l’URL ou les URL appropriées, en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Lucidchart.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Lucidchart**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-lucidchart-sso"></a>Configurer l’authentification unique Lucidchart

1. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Lucidchart en tant qu’administrateur.

2. Dans le menu du haut, cliquez sur **Team**.

    ![Team](./media/lucidchart-tutorial/ic791190.png "Équipe")

3. Cliquez sur **Applications \> Gérer SAML**.

    ![Gérer SAML](./media/lucidchart-tutorial/ic791191.png "Gérer SAML")

4. Dans la page de boîte de dialogue **SAML Authentication Settings**, procédez comme suit :

    a. Sélectionnez **Enable SAML Authentication**, puis cliquez sur **Optional**.

    ![SAML Authentication Settings](./media/lucidchart-tutorial/ic791192.png "Paramètres d’authentification SAML")

    b. Dans la zone de texte **Domain**, tapez votre nom de domaine, puis cliquez sur **Change Certificate**.

    ![Change Certificate](./media/lucidchart-tutorial/ic791193.png "Changer de certificat")

    c. Ouvrez votre fichier de métadonnées téléchargé, copiez le contenu, puis collez-le dans la zone de texte **Upload Metadatas**.

    ![Upload Metadata](./media/lucidchart-tutorial/ic791194.png "Charger les métadonnées")

    d. Sélectionnez **Automatically Add new user to the team** (Ajouter automatiquement un utilisateur à l’équipe), puis cliquez sur **Enregistrer les modifications**.

    ![Save Changes](./media/lucidchart-tutorial/ic791195.png "Enregistrer les modifications")

### <a name="create-lucidchart-test-user"></a>Créer l’utilisateur de test Lucidchart

Aucun élément d’action ne vous permet de configurer l’approvisionnement des utilisateurs dans Lucidchart.  Lorsqu’un utilisateur tente de se connecter à Lucidchart à l’aide du panneau d’accès, Lucidchart vérifie si cet utilisateur existe.  

Si aucun compte d’utilisateur n’est disponible, Lucidchart le crée automatiquement.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Cette opération redirige vers l’URL de connexion Lucidchart, où vous pouvez lancer le processus de connexion. 

* Accédez directement à l’URL de connexion Lucidchart pour lancer le processus de connexion.

* Vous pouvez utiliser Mes applications de Microsoft. Le fait de cliquer sur la vignette Lucidchart dans Mes applications doit vous connecter automatiquement à l’application Lucidchart pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

 Après avoir configuré Lucidchart, vous pouvez appliquer des contrôles de session qui protègent l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Les contrôles de session sont étendus à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
