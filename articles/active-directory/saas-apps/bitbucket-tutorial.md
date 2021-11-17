---
title: 'Tutoriel : intégration d’Azure Active Directory avec SAML SSO for Bitbucket by resolution GmbH | Documents Microsoft'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et SAML SSO for Bitbucket by resolution GmbH.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/12/2021
ms.author: jeedes
ms.openlocfilehash: a3f969aa25e8be7925202647fd0760331e717fee
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132349145"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-bitbucket-by-resolution-gmbh"></a>Tutoriel : intégration d’Azure Active Directory avec SAML SSO for Bitbucket by resolution GmbH

Dans ce tutoriel, vous allez découvrir comment intégrer SAML SSO for Bitbucket by resolution GmbH à Azure Active Directory (Azure AD). Quand vous intégrez SAML SSO for Bitbucket by resolution GmbH à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à SAML SSO for Bitbucket by resolution GmbH.
* Permettre à vos utilisateurs de se connecter automatiquement à SAML SSO for Bitbucket by resolution GmbH avec leur compte Azure AD.
* gérer vos comptes à un emplacement central : le portail Azure.


## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD avec SSO SAML pour Bitbucket de resolution GmbH, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).
* Abonnement SSO SAML pour Bitbucket de resolution GmbH pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* SSO SAML pour Bitbucket de resolution GmbH prend en charge l’authentification unique démarrée par **le fournisseur de services et le fournisseur d’identité**
* SSO SAML pour Bitbucket de resolution GmbH prend en charge l’attribution d’utilisateurs **Juste-à-temps**


## <a name="add-saml-sso-for-bitbucket-by-resolution-gmbh-from-the-gallery"></a>Ajouter SAML SSO for Bitbucket by resolution GmbH à partir de la galerie

Pour configurer l’intégration de SSO SAML pour Bitbucket de resolution GmbH dans Azure AD, vous devez ajouter SSO SAML pour Bitbucket de resolution GmbH à votre liste d’applications SaaS gérées à partir de la galerie.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire, ou avec un compte personnel Microsoft.
1. Sélectionnez **Azure Active Directory** dans le volet de gauche.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **SAML SSO for Bitbucket by resolution GmbH** dans la zone de recherche.
1. Sélectionnez **SAML SSO for Bitbucket by resolution GmbH** dans les résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-saml-sso-for-bitbucket-by-resolution-gmbh"></a>Configurer et tester l’authentification unique Azure AD pour SAML SSO for Bitbucket by resolution GmbH

Configurez et testez l’authentification unique Azure AD avec SAML SSO for Bitbucket by resolution GmbH pour un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur associé dans SAML SSO for Bitbucket by resolution GmbH.

Pour configurer et tester l’authentification unique Azure AD avec SAML SSO for Bitbucket by resolution GmbH, effectuez les étapes suivantes :


1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
2. **[Configurer l’authentification unique SAML SSO for Bitbucket by resolution GmbH](#configure-saml-sso-for-bitbucket-by-resolution-gmbh-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un SSO SAML pour Bitbucket de resolution GmbH](#create-saml-sso-for-bitbucket-by-resolution-gmbh-test-user)** pour avoir un équivalent de Britta Simon dans SSO SAML pour Bitbucket de resolution GmbH qui soit lié à la représentation Azure AD de l’utilisateur.
6. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD sur le portail Azure.
 
1. Sur le portail Azure, dans la page d’intégration de l’application **SAML SSO for Bitbucket by resolution GmbH**, recherchez la section **Gérer**, puis sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, sélectionnez l’icône de crayon pour **Configuration SAML de base** afin de modifier les paramètres.

    ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, suivez les étapes ci-dessous si vous souhaitez configurer l’application en mode démarré par **IDP** :


    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<server-base-url>/plugins/servlet/samlsso`

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://<server-base-url>/plugins/servlet/samlsso`

    c. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :
    
    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL de réponse et l’URL de connexion réels. Pour obtenir ces valeurs, contactez l’[équipe de support de SSO SAML pour Bitbucket de resolution GmbH](https://marketplace.atlassian.com/apps/1217045/saml-single-sign-on-sso-bitbucket?hosting=server&tab=support). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

5. Sur la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le fichier **XML de métadonnées de fédération** en fonction des options définies selon vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

Dans cette section, vous allez créer un utilisateur de test appelé B. Simon sur le portail Azure.

1. Dans le volet gauche du portail Azure, sélectionnez **Azure Active Directory** > **Utilisateurs** > **Tous les utilisateurs**.
1. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.
1. Dans les propriétés **Utilisateur**, effectuez les étapes suivantes :
   1. Dans le champ **Nom**, entrez `B.Simon`.  
   1. Dans le champ **Nom de l’utilisateur**, entrez username@companydomain.extension. Par exemple : `B.Simon@contoso.com`.
   1. Cochez la case **Afficher le mot de passe** et notez le mot de passe.
   1. Sélectionnez **Create** (Créer).

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez permettre à B.Simon d’utiliser l’authentification unique Azure en lui accordant l’accès à SAML SSO for Bitbucket by resolution GmbH.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise** > **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **SSO SAML pour Bitbucket de resolution GmbH**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**. Ensuite, dans la boîte de dialogue **Ajouter une attribution**, sélectionnez **Utilisateurs et groupes**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B.Simon** dans la liste des utilisateurs. Choisissez **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, sélectionnez **Affecter**.

## <a name="configure-saml-sso-for-bitbucket-by-resolution-gmbh-sso"></a>Configurer l’authentification unique SAML SSO for Bitbucket by resolution GmbH

1. Connectez-vous à votre site d’entreprise SSO SAML pour Bitbucket de resolution GmbH en tant qu’administrateur.

2. À droite dans la barre d’outils principale, cliquez sur **Paramètres**.

3. Accédez à la section COMPTES, cliquez sur **SAML SingleSignOn** sur la barre de menus.

    ![Samlsingle](./media/bitbucket-tutorial/tutorial_bitbucket_samlsingle.png)

4. Sur la **page Configuration du plug-in SAML SIngleSignOn**, cliquez sur **Ajouter idp**. 

    ![Ajout d’un fournisseur d’identité (idp)](./media/bitbucket-tutorial/tutorial_bitbucket_addidp.png)

5. Sur la page **Choisir votre fournisseur d’identité SAML**, procédez comme suit :

    ![Fournisseur d’identité](./media/bitbucket-tutorial/tutorial_bitbucket_identityprovider.png)

    a. Sélectionnez **Type d’Idp** en tant qu’**AD AZURE**.

    b. Dans la zone de texte **Nom**, tapez le nom.

    c. Dans la zone de texte **Description**, tapez la description.

    d. Cliquez sur **Suivant**.

6. Sur la page **Configuration du fournisseur d’identité**, cliquez sur le bouton **Suivant**.

    ![La configuration de l’identité](./media/bitbucket-tutorial/tutorial_bitbucket_identityconfig.png)

7.  Sur la page **Importer les métadonnées SAML Idp**, cliquez sur **Charger le fichier** pour charger le fichier **METADATA XML** que vous avez téléchargé à partir du portail Azure.

    ![idpmetadata](./media/bitbucket-tutorial/tutorial_bitbucket_idpmetadata.png)

8. Cliquez sur **Suivant**.

9. Cliquez sur **Enregistrer les paramètres**.

    ![Enregistrement](./media/bitbucket-tutorial/tutorial_bitbucket_save.png)


## <a name="create-saml-sso-for-bitbucket-by-resolution-gmbh-test-user"></a>Créer un utilisateur de test de SSO SAML pour Bitbucket de resolution GmbH

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans SSO SAML pour Bitbucket de resolution GmbH. SSO SAML pour Bitbucket de resolution GmbH prend en charge l’approvisionnement juste-à-temps et des utilisateurs peuvent également être créés manuellement. Contactez [équipe de support SSO SAML pour Bitbucket de resolution GmbH Client](https://marketplace.atlassian.com/plugins/com.resolution.atlasplugins.samlsso-bitbucket/server/support) en fonction de vos besoins.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

#### <a name="sp-initiated"></a>Lancée par le fournisseur de services :

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion à SAML SSO for Bitbucket by resolution GmbH, d’où vous pouvez lancer le flux de connexion.  

* Accédez directement à l’URL de connexion à SAML SSO for Bitbucket by resolution GmbH pour lancer le flux de connexion.

#### <a name="idp-initiated"></a>Lancée par le fournisseur d’identité :

* Cliquez sur **Tester cette application** sur le portail Azure. Vous êtes alors automatiquement connecté à l’application SAML SSO for Bitbucket by resolution GmbH pour laquelle vous avez configuré l’authentification unique.

Vous pouvez aussi utiliser Mes applications de Microsoft pour tester l’application dans n’importe quel mode. Si, quand vous cliquez sur la vignette SAML SSO for Bitbucket by resolution GmbH dans Mes applications, le mode Fournisseur de services est configuré, vous êtes redirigé vers la page de connexion de l’application pour lancer le flux de connexion ; s’il s’agit du mode Fournisseur d’identité, vous êtes automatiquement connecté à l’application SAML SSO for Bitbucket by resolution GmbH pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).


## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré SAML SSO for Bitbucket by resolution GmbH, vous pouvez appliquer des contrôles de session, qui protègent contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Les contrôles de session sont étendus à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
