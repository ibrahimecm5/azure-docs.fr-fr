---
title: 'Tutoriel : Intégrer l’authentification unique (SSO) Azure Active Directory à Oktopost SAML | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Oktopost SAML.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/05/2021
ms.author: jeedes
ms.openlocfilehash: 77b651283a20c055ad43b6cca7d5a1aa7dad4609
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132300258"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-oktopost-saml"></a>Tutoriel : Intégrer l’authentification unique (SSO) Azure Active Directory à Oktopost SAML

Dans ce tutoriel, vous allez apprendre à intégrer Oktopost SAML à Azure Active Directory (Azure AD). Quand vous intégrez Oktopost SAML à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Oktopost SAML.
* Autoriser les utilisateurs à se connecter automatiquement à Oktopost SAML avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Oktopost SAML pour lequel l’authentification unique (SSO) est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* Oktopost SAML prend en charge l’authentification unique lancée par **le fournisseur de services et le fournisseur d’identité**

> [!NOTE]
> L’identificateur de cette application étant une valeur de chaîne fixe, une seule instance peut être configurée dans un locataire.


## <a name="adding-oktopost-saml-from-the-gallery"></a>Ajout d’Oktopost SAML à partir de la galerie

Pour configurer l’intégration d’Oktopost SAML à Azure AD, vous devez ajouter Oktopost SAML à partir de la galerie à votre liste d’applications SaaS managées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Oktopost SAML** dans la zone de recherche.
1. Sélectionnez **Oktopost SAML** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.


## <a name="configure-and-test-azure-ad-sso-for-oktopost-saml"></a>Configurer et tester l’authentification unique Azure AD pour Oktopost SAML

Configurez et testez l’authentification unique Azure AD avec Oktopost SAML pour un utilisateur test nommé **B. Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Oktopost SAML associé.

Pour configurer et tester l’authentification unique Azure AD avec Oktopost SAML, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Oktopost SAML](#configure-oktopost-saml-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur test Oktopost SAML](#create-oktopost-saml-test-user)** pour avoir un équivalent de B.Simon dans Oktopost SAML lié à la représentation Azure AD associée.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **Oktopost SAML**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, l’utilisateur n’a rien à faire, car l’application est déjà intégrée à Azure.

1. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    Dans la zone de texte **URL de connexion**, tapez l’URL : `https://app.oktopost.com/auth/login`


1. Cliquez sur **Enregistrer**.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **Certificat (en base64)** , puis sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

1. Dans la section **Configurer Oktopost SAML**, copiez la ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Oktopost SAML.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Oktopost SAML**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-oktopost-saml-sso"></a>Configurer l’authentification unique pour Oktopost SAML

1. Connectez-vous à Oktopost SAML en tant qu’administrateur.

1. Cliquez sur **User Icon > Settings** (Icône Utilisateur > Paramètres).

    ![Paramètres d’Oktopost SAML](./media/oktopost-saml-tutorial/settings.png)

1. Dans **Settings** (Paramètres), accédez à la page **Security > Single Sign-on** (Sécurité > Authentification unique) et effectuez les étapes suivantes.

    ![Configuration d’Oktopost SAML](./media/oktopost-saml-tutorial/configure-sso.png)

    a. Définissez l’option **Enable Single Sign-on** (Activer l’authentification unique) sur **Yes** (Oui).

    b. Dans la zone de texte **SAML Endpoint** (Point de terminaison SAML), collez l’**URL de connexion** que vous avez copiée à partir du portail Azure.

    c. Dans la zone de texte **Issuer** (Émetteur), collez la valeur **Identificateur Azure AD** que vous avez copiée à partir du portail Azure.

    d. Ouvrez le **Certificat (en base64)** téléchargé à partir du portail Azure dans le Bloc-notes, puis collez le contenu dans la zone de texte **X.509 Certificate**.
    
    e. Cliquez sur **Enregistrer**.

### <a name="create-oktopost-saml-test-user"></a>Créer un utilisateur test Oktopost SAML

1. Connectez-vous à Oktopost SAML en tant qu’administrateur.

1. Cliquez sur **User Icon > Settings** (Icône Utilisateur > Paramètres).

    ![Utilisateur test 1 Oktopost SAML](./media/oktopost-saml-tutorial/settings.png)

1.  Accédez à **User Management > Users > Add User** (Gestion des utilisateurs > Utilisateurs > Ajouter un utilisateur).

    ![Utilisateur test 2 Oktopost SAML](./media/oktopost-saml-tutorial/add-user-1.png)

1. Renseignez les champs obligatoires dans la fenêtre contextuelle, puis cliquez sur **Send** (Envoyer).

    ![Utilisateur test 3 Oktopost SAML](./media/oktopost-saml-tutorial/add-user-2.png)

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

#### <a name="sp-initiated"></a>Lancée par le fournisseur de services :

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion Oktopost SAML, à partir de laquelle vous pouvez lancer le flux de connexion.  

* Accédez directement à l’URL de connexion Oktopost SAML pour y lancer le processus de connexion.

#### <a name="idp-initiated"></a>Lancée par le fournisseur d’identité :

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors automatiquement connecté à l’instance Oktopost SAML pour laquelle vous avez configuré l’authentification unique 

Vous pouvez aussi utiliser Mes applications de Microsoft pour tester l’application dans n’importe quel mode. Si, quand vous cliquez sur la vignette Oktopost SAML dans Mes applications, le mode Fournisseur de services est configuré, vous êtes redirigé vers la page de connexion de l’application pour lancer le flux de connexion ; s’il s’agit du mode Fournisseur d’identité, vous êtes automatiquement connecté à l’instance Oktopost SAML pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).


## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez configuré Oktopost SAML, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
