---
title: 'Tutoriel : Intégration de l’authentification unique Azure Active Directory à uniFLOW Online | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et uniFLOW Online.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/26/2021
ms.author: jeedes
ms.openlocfilehash: aa34cdfe0cf9f9f69a7d4699b5b107f4e4562722
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132320214"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-uniflow-online"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory à uniFLOW Online

Dans ce tutoriel, vous allez apprendre à intégrer uniFLOW Online à Azure Active Directory (Azure AD). Quand vous intégrez uniFLOW Online à Azure AD, vous pouvez :

* Contrôler qui dans Azure AD a accès à uniFLOW Online.
* Permettre à vos utilisateurs de se connecter à uniFLOW Online avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un locataire uniFLOW Online.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* uniFLOW Online prend en charge l’authentification unique initiée par le **fournisseur de services**.

## <a name="add-uniflow-online-from-the-gallery"></a>Ajouter uniFLOW Online à partir de la galerie

Pour configurer l’intégration d’uniFLOW Online à Azure AD, vous devez ajouter uniFLOW Online à votre liste d’applications SaaS managées à partir de la galerie.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **uniFLOW Online** dans la zone de recherche.
1. Sélectionnez **uniFLOW Online** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-uniflow-online"></a>Configurer et tester l’authentification unique Azure AD pour uniFLOW Online

Configurez et testez l’authentification unique Azure AD auprès d’uniFLOW Online pour un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur uniFLOW Online associé.

Pour configurer et tester l’authentification unique Azure AD avec uniFLOW Online, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
   1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
   1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique uniFLOW Online](#configure-uniflow-online-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Se connecter à uniFLOW Online à l’aide de l’utilisateur de test créé](#sign-in-to-uniflow-online-using-the-created-test-user)** pour tester la connexion de l’utilisateur côté application.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **uniFLOW Online**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    a. Dans la zone de texte **Identificateur (ID d’entité)** , tapez une URL en utilisant un des modèles suivants :

    | **Identificateur** |
    |---------|
    | `https://<tenant_domain_name>.eu.uniflowonline.com` |
    | `https://<tenant_domain_name>.us.uniflowonline.com` |
    | `https://<tenant_domain_name>.sg.uniflowonline.com` |
    | `https://<tenant_domain_name>.jp.uniflowonline.com` |
    | `https://<tenant_domain_name>.au.uniflowonline.com` |

    b. Dans la zone de texte **URL de connexion**, tapez une URL en utilisant un des modèles suivants :

    | **URL d’authentification** |
    |---------|
    | `https://<tenant_domain_name>.eu.uniflowonline.com` |
    | `https://<tenant_domain_name>.us.uniflowonline.com` |
    | `https://<tenant_domain_name>.sg.uniflowonline.com` |
    | `https://<tenant_domain_name>.jp.uniflowonline.com` |
    | `https://<tenant_domain_name>.au.uniflowonline.com` |

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur et l’URL de connexion réels. Pour obtenir ces valeurs, contactez [l’équipe de support uniFLOW Online](mailto:support@nt-ware.com). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure ou examiner l’URL de réponse affichée dans votre locataire uniFLOW Online.

1. L’application uniFLOW Online attend les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à votre configuration d’attributs de jeton SAML. La capture d’écran suivante montre la liste des attributs par défaut, où **nameidentifier** est mappé avec **user.userprincipalname**. L’application uniFLOW Online s’attend à ce que **nameidentifier** soit mappé sur **user.objectid**. Vous devez donc modifier le mappage d’attribut en cliquant sur l’icône **Modifier**.

    ![Capture d’écran montrant le volet Attributs utilisateur avec l’icône de modification mise en évidence.](common/edit-attribute.png)

1. En plus de ce qui précède, l’application uniFLOW Online s’attend à ce que quelques attributs supplémentaires (présentés ci-dessous) soient repassés dans la réponse SAML. Ces attributs sont également préremplis, mais vous pouvez les examiner pour voir s’ils répondent à vos besoins.

    | Nom |  Attribut source|
    | -----------| --------------- |
    | displayname | user.displayname |
    | nickname | user.onpremisessamaccountname |

   > [!NOTE]
   > L’attribut `user.onpremisessamaccountname` contient une valeur uniquement si vos utilisateurs Azure AD sont synchronisés à partir d’un annuaire Windows Active Directory local.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur le bouton Copier pour copier l’**URL des métadonnées de fédération d’application**, puis enregistrez-la sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/copy-metadataurl.png)

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

Dans cette section, vous allez permettre à B.Simon d’utiliser l’authentification unique Azure en lui accordant l’accès à uniFLOW Online.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **uniFLOW Online**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

> [!NOTE]
> Pour autoriser tous les utilisateurs à accéder à l’application sans attribution manuelle, accédez à la section **Gérer** et sélectionnez **Propriétés**. Ensuite, affectez **NON** au paramètre **Affectation de l’utilisateur obligatoire**.

## <a name="configure-uniflow-online-sso"></a>Configurer l’authentification unique uniFLOW Online

1. Dans une autre fenêtre de navigateur web, connectez-vous au site web uniFLOW Online en tant qu’administrateur.

1. Dans le volet de navigation de gauche, sélectionnez l’onglet **User** (Utilisateur).

    ![Capture d’écran montrant Users sélectionné sur le site uniflow Online.](./media/uniflow-online-tutorial/user.png)

1. Cliquez sur **Fournisseurs d’identité**.

    ![Capture d’écran montrant l’élément Identity Providers sélectionné.](./media/uniflow-online-tutorial/profile.png)

1. Cliquez sur **Add identity provider** (Ajouter un fournisseur d’identité).

    ![Capture d’écran montrant l’option Add identity provider sélectionnée.](./media/uniflow-online-tutorial/add-profile.png)

1. Dans la section **ADD IDENTITY PROVIDER**, effectuez les étapes suivantes :

    ![Capture d’écran montrant la section ADD IDENTITY PROVIDER où vous pouvez entrer les valeurs décrites.](./media/uniflow-online-tutorial/configuration.png)

    a. Entrez le nom d’affichage dans le champ « Display name », par exemple : **AzureAD SSO**.

    b. Pour **Provider type** (Type de fournisseur), sélectionnez l’option **WS-Fed** dans la liste déroulante.

    c. Pour **WS-Fed type**, sélectionnez l’option **Azure Active Directory** dans la liste déroulante.

    d. Cliquez sur **Enregistrer**.

1. Sous l’onglet **General**, effectuez les étapes suivantes :

    ![Capture d’écran montrant l’onglet General où vous pouvez entrer les valeurs décrites.](./media/uniflow-online-tutorial/general-tab.png)

    a. Entrez le nom d’affichage dans le champ « Display name », par exemple : **AzureAD SSO**.

    b. Sélectionnez l’option **From URL** (À partir d’une URL) pour **ADFS Federation Metadata** (Métadonnées de fédération ADFS).

    c. Dans la zone de texte **URL des métadonnées de fédération**, collez la valeur pour **l’URL des métadonnées de fédération d’application** que vous avez copiée dans le portail Azure.

    d. Pour **Identity provider** (Fournisseur d’identité), cochez la case **Enabled** (Activé).

    e. Pour **Automatic user registration** (Inscription automatique des utilisateurs), cochez la case **Activated** (Activé).

    f. Cliquez sur **Enregistrer**.

### <a name="sign-in-to-uniflow-online-using-the-created-test-user"></a>Se connecter à uniFLOW Online à l’aide de l’utilisateur de test créé

1. Dans une autre fenêtre de navigateur web, accédez à l’URL uniFLOW Online pour votre locataire.

1. Sélectionnez le fournisseur d’identité créé précédemment pour vous connecter par le biais de votre instance Azure AD.

1. Connectez-vous à l’aide de l’utilisateur de test.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion à uniFLOW Online, d’où vous pouvez lancer le flux de connexion. 

* Accédez directement à l’URL de connexion à uniFLOW Online pour lancer le flux de connexion.

* Vous pouvez utiliser Mes applications de Microsoft. Le fait de cliquer sur la vignette uniFLOW Online dans Mes applications vous redirige vers l’URL de connexion à uniFLOW Online. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré uniFLOW Online, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
