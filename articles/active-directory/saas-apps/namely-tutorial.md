---
title: 'Didacticiel : Intégration d’Azure Active Directory à Namely | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Namely.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/23/2021
ms.author: jeedes
ms.openlocfilehash: 127e2cd90c0e83c9783e7af117ef6832392789d8
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132287758"
---
# <a name="tutorial-azure-active-directory-integration-with-namely"></a>Didacticiel : Intégration d’Azure Active Directory à Namely

Dans ce tutoriel, vous allez apprendre à intégrer Namely à Azure Active Directory (Azure AD). Quand vous intégrez Namely à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Namely.
* Permettre à vos utilisateurs de se connecter automatiquement à Namely avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Namely pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Namely prend en charge l’authentification unique lancée par le **fournisseur de services**.

## <a name="add-namely-from-the-gallery"></a>Ajouter Namely à partir de la galerie

Pour configurer l’intégration de Namely avec Azure AD, vous devez ajouter Namely disponible à partir de la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Namely** dans la zone de recherche.
1. Sélectionnez **Namely** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-namely"></a>Configurer et tester l’authentification unique Azure AD pour Namely

Configurez et testez l’authentification unique Azure AD avec Namely à l’aide d’un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur associé dans Namely.

Pour configurer et tester l’authentification unique Azure AD dans Namely, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Namely](#configure-namely-sso)** pour configurer les paramètres d’authentification unique côté application.
    1. **[Créer un utilisateur de test Namely](#create-namely-test-user)** pour obtenir un équivalent de B.Simon dans Namely lié à la représentation Azure AD associée.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **Namely**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    a. Dans la zone de texte **URL de connexion**, saisissez une URL au format suivant : `https://<subdomain>.namely.com`

    b. Dans la zone de texte **Identificateur (ID d’entité)** , saisissez une URL au format suivant : `https://<subdomain>.namely.com/saml/metadata`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Pour obtenir ces valeurs, contactez l’[équipe de support technique Namely](https://www.namely.com/contact/). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

5. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (Base64)** en fonction des options définies par rapport à vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

6. Dans la section **Set up Namely** (Configurer Namely), copiez la ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Namely.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Namely**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-namely-sso"></a>Configurer l’authentification unique Namely

1. Dans une autre fenêtre de navigateur, connectez-vous à votre site d’entreprise Namely en tant qu’administrateur.

2. Dans la barre d’outils située en haut, cliquez sur **Company**.
   
    ![Capture d’écran montrant la valeur Company sélectionnée.](./media/namely-tutorial/company.png) 

3. Cliquez sur l'onglet **Paramètres** .
   
    ![Capture d’écran montrant l’onglet Company Settings sélectionné.](./media/namely-tutorial/settings.png) 

4. Cliquez sur **SAML**.
   
    ![Capture d’écran montrant l’élément SAML sélectionné.](./media/namely-tutorial/general.png) 

5. Sur la page **SAML Settings** , procédez comme suit :
   
    ![Capture d’écran montrant la zone des paramètres SAML dans laquelle vous pouvez indiquer les valeurs décrites.](./media/namely-tutorial/settings-page.png)
 
    a. Cliquez sur **Enable SAML**. 

    b. Dans la zone de texte **URL d’authentification unique du fournisseur d’identité**, collez la valeur de l’**URL de connexion** que vous avez copiée dans le portail Azure.
    
    c. Ouvrez le certificat que vous avez téléchargé dans le Bloc-notes, copiez son contenu, puis collez-le dans la zone de texte **Certificat du fournisseur d’identité** .
     
    d. Cliquez sur **Enregistrer**.

### <a name="create-namely-test-user"></a>Créer un utilisateur de test Namely

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans Namely.

**Pour créer un utilisateur appelé Britta Simon dans Namely, procédez comme suit :**

1. Connectez-vous à votre site d’entreprise Namely en tant qu’administrateur.

2. Dans la barre d’outils située en haut, cliquez sur **People**.
   
    ![Capture d’écran montrant la valeur People sélectionnée.](./media/namely-tutorial/people.png) 

3. Cliquez sur l’onglet **Directory** .
   
    ![Capture d’écran montrant la valeur Directory sélectionnée.](./media/namely-tutorial/directory.png) 

4. Cliquez sur **Add New Person**.

    ![Capture d’écran montrant l’option Add New Person.](./media/namely-tutorial/add-person.png)

5. Dans la boîte de dialogue **Add New Person** , procédez comme suit :

    a. Dans la zone de texte **Prénom**, tapez **Britta**.

    b. Dans la zone de texte **Nom**, tapez **Simon**.

    c. Dans la zone de texte **E-mail**, saisissez **l’adresse e-mail** de BrittaSimon.

    d. Cliquez sur **Enregistrer**.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes redirigé vers l’URL de connexion à Namely, à partir de laquelle vous pouvez lancer le flux de connexion. 

* Accédez directement à l’URL de connexion à Namely pour lancer le flux de connexion.

* Vous pouvez utiliser Mes applications de Microsoft. Un clic sur la vignette Namely dans Mes applications vous redirige vers l’URL de connexion à Namely. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré Namely, vous pouvez appliquer le contrôle de session, qui protège en temps réel contre l’exfiltration et l’infiltration des données sensibles de votre organisation. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
