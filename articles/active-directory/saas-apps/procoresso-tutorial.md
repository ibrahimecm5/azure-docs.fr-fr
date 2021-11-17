---
title: 'Didacticiel : Intégration d’Azure Active Directory à Procore SSO | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Procore SSO.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/21/2021
ms.author: jeedes
ms.openlocfilehash: d7e6678db28f6edc2dfcaf36a656a309418ead8f
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132341898"
---
# <a name="tutorial-azure-active-directory-integration-with-procore-sso"></a>Didacticiel : Intégration d’Azure Active Directory à Procore SSO

Dans ce tutoriel, vous allez apprendre à intégrer Procore SSO à Azure Active Directory (Azure AD). Quand vous intégrez Procore SSO avec Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Procore SSO.
* Permettre à vos utilisateurs de se connecter automatiquement à Procore SSO avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à Procore SSO, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un [compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Procore SSO pour lequel l’authentification unique est activée.

> [!NOTE]
> Cette intégration peut également être utilisée à partir de l’environnement cloud US Government Azure AD. Cette application est disponible dans la Galerie d’applications cloud US Government Azure AD et peut être configurée de la même façon que dans le cloud public.

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Procore SSO prend en charge l’authentification unique lancée par le **fournisseur d’identité**.

## <a name="add-procore-sso-from-the-gallery"></a>Ajouter Procore SSO à partir de la galerie

Pour configurer l’intégration de Procore SSO à Azure AD, vous devez ajouter Procore SSO à partir de la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Procore SSO** dans la zone de recherche.
1. Sélectionnez **Procore SSO** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-procore-sso"></a>Configurer et tester l’authentification unique Azure AD pour Procore SSO

Configurez et testez l’authentification unique Azure AD avec Procore SSO à l’aide d’un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Procore SSO associé.

Pour configurer et tester l’authentification unique Azure AD avec Procore SSO, procédez comme suit :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Procore SSO](#configure-procore-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test Procore SSO](#create-procore-sso-test-user)** pour avoir dans Procore SSO un équivalent de B.Simon lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **Procore SSO**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, l’utilisateur n’a rien à faire, car l’application est déjà intégrée à Azure.

5. Sur la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le fichier **XML de métadonnées de fédération** en fonction des options définies selon vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

6. Dans la section **Configurer Procore SSO**, copiez la ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Procore SSO.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Procore SSO**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-procore-sso"></a>Configurer Procore SSO 

1. Pour configurer l’authentification unique sur **Procore SSO**, connectez-vous à votre site d’entreprise Procore en tant qu’administrateur.

2. Dans la liste déroulante de boîte à outils, cliquez sur **Admin** pour ouvrir la page de paramètres de l’authentification unique.

    ![Capture d’écran affichant le site d’entreprise Procore avec l’élément Directory sélectionné.](./media/procoresso-tutorial/admin.png)

3. Collez les valeurs dans les zones comme décrit ci-dessous.

    ![Capture d’écran montrant la boîte de dialogue Add a Person.](./media/procoresso-tutorial/setting.png)   

    a. Dans la zone de texte **URL de l'émetteur de l'authentification unique**, collez la valeur de l'**Identificateur Azure AD** copiée à partir du portail Azure.

    b. Dans la zone **URL de la cible de l'authentification SAML**, collez la valeur de l'**URL de connexion** copiée à partir du portail Azure.

    c. Ouvrez maintenant le fichier **XML de métadonnées de fédération** téléchargé à partir du portail Azure et copiez le certificat dans la balise nommée **X509Certificate**. Collez la valeur copiée dans la zone **Certificat x509 d’authentification unique**.

4. Cliquez sur **Enregistrer les modifications**.

5. Après ces paramètres, vous devez envoyer le **nom de domaine** (par ex. **contoso.com**) via lequel vous connectez à Procore à [l’équipe de support technique de Procore](https://support.procore.com/), qui activera l’authentification unique fédérée pour ce domaine.

### <a name="create-procore-sso-test-user"></a>Créer un utilisateur de test Procore SSO

Effectuez les étapes ci-dessous pour créer un utilisateur de test Procore côté Procore SSO.

1. Connectez-vous à votre site d’entreprise Procore en tant qu’administrateur.    

2. Dans la liste déroulante de boîte à outils, cliquez sur **Annuaire** pour ouvrir la page d’annuaire de l’entreprise.

    ![Capture d’écran affichant le site d’entreprise Procore avec l’élément Directory sélectionné dans la boîte à outils.](./media/procoresso-tutorial/directory.png)

3. Cliquez sur l’option **Ajouter une personne** pour ouvrir le formulaire et réglez les options suivantes.

    ![Capture d’écran affichant la zone d’ajout d’une personne à Boylan Construction, dans laquelle vous pouvez entrer des informations sur l’utilisateur.](./media/procoresso-tutorial/user.png)

    a. Dans la zone de texte **First Name**, tapez le prénom de l’utilisateur, par exemple **Britta**.

    b. Dans la zone de texte **Last Name**, tapez le nom de l’utilisateur, par exemple **Simon**.

    c. Dans la zone de texte **Adresse e-mail**, tapez l’adresse e-mail de l’utilisateur, par exemple BrittaSimon@contoso.com.

    d. Sélectionnez **Modèle d’autorisation** pour **Appliquer le modèle d’autorisation plus tard**.

    e. Cliquez sur **Créer**.

4. Vérifiez et mettez à jour les détails du contact nouvellement ajouté.

    ![Capture d’écran affichant une page de modification dans laquelle vous pouvez vérifier les paramètres de l’utilisateur.](./media/procoresso-tutorial/details.png)

5. Cliquez sur **Enregistrer et envoyer l’invitation** (si une invitation par e-mail est exigée) ou sur **Enregistrer** (pour enregistrer directement) afin de terminer l’inscription de l’utilisateur.
    
    ![Capture d’écran affichant les paramètres actuels du projet, dans lesquels vous pouvez enregistrer et envoyer une invitation.](./media/procoresso-tutorial/save.png)

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes.

* Cliquez sur Tester cette application dans le portail Azure. Vous êtes alors automatiquement connecté à l’instance Procore SSO pour laquelle vous avez configuré l’authentification unique.

* Vous pouvez utiliser Mes applications de Microsoft. Quand vous cliquez sur la vignette Procore SSO dans Mes applications, vous devez être connecté automatiquement à l’application Procore SSO pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez configuré l’authentification unique Procore SSO, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
