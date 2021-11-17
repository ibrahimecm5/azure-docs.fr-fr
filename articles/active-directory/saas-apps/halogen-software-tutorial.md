---
title: 'Tutoriel : Intégration d’Azure Active Directory à Saba TalentSpace | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Saba TalentSpace.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/22/2021
ms.author: jeedes
ms.openlocfilehash: 1e50d7e81699897862e0c3121795f5b4adc29569
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132321375"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-saba-talentspace"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory à Saba TalentSpace

Dans ce tutoriel, vous allez découvrir comment intégrer Saba TalentSpace à Azure Active Directory (Azure AD). Quand vous intégrez Saba TalentSpace à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Saba TalentSpace.
* Permettre à vos utilisateurs de se connecter automatiquement à Saba TalentSpace avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Saba TalentSpace pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* Saba TalentSpace prend en charge l’authentification unique lancée par le **fournisseur de services**

## <a name="add-saba-talentspace-from-the-gallery"></a>Ajouter Saba TalentSpace à partir de la galerie

Pour configurer l’intégration de Saba TalentSpace à Azure AD, vous devez ajouter Saba TalentSpace à partir de la galerie à votre liste d’applications SaaS managées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Saba TalentSpace** dans la zone de recherche.
1. Sélectionnez **Saba TalentSpace** dans le volet des résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-saba-talentspace"></a>Configurer et tester l’authentification unique Azure AD pour Saba TalentSpace

Configurez et testez l’authentification unique Azure AD avec Saba TalentSpace pour un utilisateur de test nommé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Saba TalentSpace associé.

Pour configurer et tester l’authentification unique Azure AD avec Saba TalentSpace, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    * **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    * **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Saba TalentSpace](#configure-saba-talentspace-sso)** pour configurer les paramètres de l’authentification unique côté application.
    * **[Créer un utilisateur de test Saba TalentSpace](#create-saba-talentspace-test-user)** pour disposer dans Saba TalentSpace d’un équivalent de B.Simon lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, dans la page d’intégration de l’application **Saba TalentSpace**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    a. Dans la zone de texte **URL de connexion**, tapez l’URL au format suivant : `https://global.hgncloud.com/[companyname]/saml/login`

    b. Dans la zone de texte **Identificateur (ID d’entité)** , tapez l’URL au format suivant : `https://global.hgncloud.com/[companyname]/saml/metadata`

    c. Dans la zone de texte **URL de réponse (URL Assertion Consumer Service)** , tapez l’URL au format suivant : `https://global.hgncloud.com/[companyname]/saml/SSO`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Pour obtenir ces valeurs, contactez l’[équipe du support technique de Saba TalentSpace](https://support.saba.com/). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. Sur la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le fichier **XML de métadonnées de fédération** en fonction des options définies selon vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

1. Dans la section **Configurer Saba TalentSpace**, copiez la ou les URL appropriées, en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Saba TalentSpace.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Saba TalentSpace**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-saba-talentspace-sso"></a>Configurer l’authentification unique Saba TalentSpace

1. Dans une autre fenêtre de navigateur, connectez-vous à votre application **Saba TalentSpace** en tant qu’administrateur.

2. Cliquez sur l’onglet **Options** .
  
    ![Capture d’écran montrant la page d’accueil « Saba TalentSpace » avec l’onglet « Options » sélectionné.](./media/halogen-software-tutorial/tutorial-halogen-12.png)

3. Dans le volet de navigation de gauche, cliquez sur **SAML Configuration**(Configuration SAML).
  
    ![Capture d’écran montrant le volet de navigation de gauche « User Interface » avec l’option « SAML Configuration » sélectionnée.](./media/halogen-software-tutorial/tutorial-halogen-13.png)

4. Dans la page **SAML Configuration** , procédez comme suit :

    ![Capture d’écran montrant la page « SAML Configuration » avec les options de la zone « Settings » mises en évidence.](./media/halogen-software-tutorial/tutorial-halogen-14.png)

    a. Dans **Unique Identifier** (Identificateur unique), sélectionnez **NameID**.

    b. Dans **Unique Identifier Maps To** (l’identificateur unique mappe vers), sélectionnez **Username** (Nom d’utilisateur).
  
    c. Pour charger votre fichier de métadonnées téléchargé, cliquez sur **Browse** (Parcourir) pour sélectionner le fichier, puis sur **Upload File** (Charger le fichier).

    d. Pour tester la configuration, cliquez sur **Run Test**(Exécuter le test).

    > [!NOTE]
    > Vous devez attendre que le message « *The SAML test is complete. Please close this window* » s’affiche. Ensuite, fermez la fenêtre du navigateur. La case à cocher **Enable SAML** (Activer SAML) est sélectionnée uniquement si le test a été effectué.

    e. Sélectionnez **Enable SAML**.

    f. Cliquez sur **Enregistrer les modifications**.

### <a name="create-saba-talentspace-test-user"></a>Créer un utilisateur de test Saba TalentSpace

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans Saba TalentSpace.

**Pour créer un utilisateur appelé Britta Simon dans Saba TalentSpace, effectuez les étapes suivantes :**

1. Connectez-vous à votre application **Saba TalentSpace** en tant qu’administrateur.

2. Cliquez sur l’onglet **User Center** (Centre utilisateur), puis sur **Create User** (Créer un utilisateur).

    ![Capture d’écran montrant l’onglet « User Center » et l’élément « Create User » sélectionnés.](./media/halogen-software-tutorial/tutorial-halogen-300.png)  

3. Dans la boîte de dialogue **New User** , procédez comme suit :

    ![Qu’est-ce qu’Azure AD Connect ?](./media/halogen-software-tutorial/tutorial-halogen-301.png)

    a. Dans la zone de texte **First Name** (Prénom), tapez le prénom de l’utilisateur, par exemple **B**.

    b. Dans la zone de texte **Nom**, entrez le nom de l’utilisateur, par exemple **Simon**.

    c. Dans la zone de texte **Username** (Nom d’utilisateur), entrez **B.Simon**, le nom d’utilisateur du portail Azure.

    d. Dans la zone **Password** (Mot de passe), entrez un mot de passe pour B.Simon.

    e. Cliquez sur **Enregistrer**.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Cette opération redirige vers l’URL de connexion à Saba TalentSpace, où vous pouvez lancer le processus de connexion. 

* Accédez directement à l’URL de connexion à Saba TalentSpace et lancez le processus de connexion.

* Vous pouvez utiliser Mes applications de Microsoft. Quand vous cliquez sur la vignette Saba TalentSpace dans Mes applications, vous devez être connecté automatiquement à l’application Saba TalentSpace pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

 Après avoir configuré Saba TalentSpace, vous pouvez appliquer le contrôle de session, qui protège l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
