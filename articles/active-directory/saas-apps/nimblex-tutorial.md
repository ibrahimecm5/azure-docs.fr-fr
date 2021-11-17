---
title: 'Tutoriel : Intégration d’Azure Active Directory à Nimblex | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Nimblex.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/27/2021
ms.author: jeedes
ms.openlocfilehash: b0cb2182e1733a9607d5f967e9c11eb9e4ea7f43
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132342145"
---
# <a name="tutorial-azure-active-directory-integration-with-nimblex"></a>Tutoriel : Intégration d’Azure Active Directory à Nimblex

Dans ce tutoriel, vous allez apprendre à intégrer Nimblex à Azure Active Directory (Azure AD). Quand vous intégrez Nimblex à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Nimblex.
* Permettre à vos utilisateurs de se connecter automatiquement à Nimblex avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Nimblex pour lequel l’authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Nimblex prend en charge l’authentification unique lancée par le **fournisseur de services**.

* Nimblex prend en charge le provisionnement d’utilisateurs **juste-à-temps**.

## <a name="add-nimblex-from-the-gallery"></a>Ajouter Nimblex à partir de la galerie

Pour configurer l’intégration de Nimblex à Azure AD, vous devez ajouter Nimblex à partir de la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Nimblex** dans la zone de recherche.
1. Sélectionnez **Nimblex** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-nimblex"></a>Configurer et tester l’authentification unique Azure AD pour Nimblex

Configurez et testez l’authentification unique Azure AD avec Nimblex pour un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur associé dans Nimblex.

Pour configurer et tester l’authentification unique Azure AD avec Nimblex, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Nimblex](#configure-nimblex-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test Nimblex](#create-nimblex-test-user)** pour avoir un équivalent de B.Simon dans Nimblex associé à sa représentation dans Azure AD.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **Nimblex**, recherchez la section **Gérer**, puis sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<YOUR_APPLICATION_PATH>/Login.aspx`.

    b. Dans la zone de texte **Identificateur**, tapez une URL en utilisant le format suivant : `https://<YOUR_APPLICATION_PATH>/`

    c. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://<path-to-application>/SamlReply.aspx`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion, l’identificateur et l’URL de réponse réels. Pour obtenir ces valeurs, contactez [l’équipe de support technique de Nimblex](mailto:support@ebms.com.au). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

4. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (Base64)** en fonction des options définies par rapport à vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

6. Dans la section **Configurer Nimblex**, copiez la ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez permettre à B.Simon d’utiliser l’authentification unique Azure en lui octroyant l’accès à NAVEX One.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **NAVEX One**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-nimblex-sso"></a>Configurer l’authentification unique Nimblex

1. Dans une autre fenêtre de navigateur web, connectez-vous à Nimblex comme Administrateur de la sécurité.

2. À droite de la page, cliquez sur le logo **Paramètres**.

    ![Capture d’écran montrant l’icône Settings.](./media/nimblex-tutorial/settings.png)

3. Dans la page **Panneau de configuration**, sous la section **Sécurité**, cliquez sur **Authentification unique**.

    ![Capture d’écran montrant l’élément Single Sign-on sélectionné dans le menu Security.](./media/nimblex-tutorial/security.png)

4. Dans la page **Gérer l’authentification unique**, sélectionnez le nom de votre instance, puis cliquez sur **Modifier**.

    ![Capture d’écran montrant la zone Manage Single Sign-On dans laquelle vous pouvez sélectionner Edit.](./media/nimblex-tutorial/edit-tab.png)

5. Dans la page **Modifier le fournisseur SSO**, effectuez les étapes suivantes :

    ![Capture d’écran montrant la zone Edit SSO Provider dans laquelle vous pouvez entrer les valeurs décrites.](./media/nimblex-tutorial/certificate.png)

    a. Dans la zone de texte **Description**, tapez le nom de votre instance.

    b. Dans le Bloc-notes, ouvrez le certificat codé en base 64 téléchargé dans le portail Azure, copiez son contenu, puis collez-le dans la zone **Certificat**.

    c. Dans la zone de texte **Identity Provider Sso Target Url** (URL cible d’authentification unique du fournisseur d’identité), collez la valeur de l’**URL de connexion** que vous avez copiée dans le portail Azure.

    d. Cliquez sur **Enregistrer**.

### <a name="create-nimblex-test-user"></a>Créer un utilisateur de test Nimblex

Dans cette section, un utilisateur nommé Britta Simon est créé dans Nimblex. Nimblex prend en charge l’attribution d’utilisateurs juste-à-temps, qui est activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas encore d’utilisateurs dans Nimblex, un utilisateur est créé après l’authentification.

>[!Note]
>Si vous devez créer un utilisateur manuellement, contactez [l’équipe du support technique du client Nimblex](mailto:support@ebms.com.au).

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion à Nimblex, d’où vous pouvez lancer le flux de connexion. 

* Accédez directement à l’URL de connexion à Nimblex pour lancer le flux de connexion.

* Vous pouvez utiliser Mes applications de Microsoft. Un clic sur la vignette Nimblex dans Mes applications vous redirige vers l’URL de connexion à Nimblex. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré Nimblex, vous pouvez appliquer le contrôle de session, qui protège en temps réel contre l’exfiltration et l’infiltration de données sensibles de votre organisation. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
