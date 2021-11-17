---
title: 'Didacticiel : Intégration d’Azure Active Directory avec Pingboard | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Pingboard.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/01/2021
ms.author: jeedes
ms.openlocfilehash: 286176b662f9f02d2d9dd39a1e13cc010ae134e4
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132329815"
---
# <a name="tutorial-azure-active-directory-integration-with-pingboard"></a>Didacticiel : Intégration d’Azure Active Directory avec Pingboard

Dans ce tutoriel, vous allez découvrir comment intégrer Pingboard à Azure Active Directory (Azure AD). Quand vous intégrez Pingboard à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Pingboard.
* Permettre à vos utilisateurs de se connecter automatiquement à Pingboard avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Abonnement Pingboard avec l’authentification unique (SSO) activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Pingboard prend en charge l’authentification unique initiée par le **fournisseur de services** et le **fournisseur d’identité**.

* Pingboard prend en charge [le provisionnement d’utilisateurs automatisé](./pingboard-provisioning-tutorial.md). 

> [!NOTE]
> L’identificateur de cette application étant une valeur de chaîne fixe, une seule instance peut être configurée dans un locataire.

## <a name="add-pingboard-from-the-gallery"></a>Ajouter Pingboard à partir de la galerie

Pour configurer l’intégration de Pingboard avec Azure AD, vous devez ajouter Pingboard, disponible dans la galerie, à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Pingboard** dans la zone de recherche.
1. Sélectionnez **Pingboard** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-pingboard"></a>Configurer et tester l’authentification unique Azure AD pour Pingboard

Configurez et testez l’authentification unique Azure AD auprès de Pingboard pour un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur associé dans Pingboard.

Pour configurer et tester l’authentification unique Azure AD avec Pingboard, procédez comme suit :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Pingboard](#configure-pingboard-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test Pingboard](#create-pingboard-test-user)** pour avoir un équivalent de B.Simon dans Pingboard lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **Pingboard**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, si vous souhaitez configurer l’application en mode lancé par le **fournisseur d’identité**, effectuez les étapes suivantes :

    a. Dans la zone de texte **Identificateur**, tapez une l’URL : `http://app.pingboard.com/sp`

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://<ENTITY_ID>.pingboard.com/auth/saml/consume`

5. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<SUBDOMAIN>.pingboard.com/sign_in`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de réponse et l’URL de connexion réelles. Pour obtenir ces valeurs, contactez l’[équipe de support technique Pingboard](https://support.pingboard.com/). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

6. Sur la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le fichier **XML de métadonnées de fédération** en fonction des options définies selon vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

7. Dans la section **Configurer Pingboard**, copiez la ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Pingboard.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Pingboard**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-pingboard-sso"></a>Configurer l’authentification unique Pingboard

1. Pour configurer l’authentification unique côté Pingboard, ouvrez une nouvelle fenêtre de navigateur et connectez-vous à votre compte Pingboard. Pour configurer l’authentification unique, vous devez être un administrateur Pingboard.

2. Dans le menu supérieur, sélectionnez **Applications > Intégrations**

    ![Configure Single Sign-On](./media/pingboard-tutorial/integration.png)

3. Sur la page **Intégrations**, recherchez la mosaïque **« Azure Active Directory »** , puis cliquez dessus.

    ![Intégration de l’authentification unique Pingboard](./media/pingboard-tutorial/directory.png)

4. Dans la fenêtre modale qui suit, cliquez sur **« Configurer »**

    ![Bouton de configuration Pingboard](./media/pingboard-tutorial/configure.png)

5. Sur la page suivante, vous remarquez que « l’intégration Azure SSO est activée ». Ouvrez le fichier XML de métadonnées téléchargé dans un bloc-notes puis collez le contenu dans **IDP Metadata** (métadonnées du fournisseur d’identité).

    ![Écran de configuration SSO de Pingboard](./media/pingboard-tutorial/metadata.png)

6. Le fichier est validé et, si tout est correct, l’authentification unique est alors activée.

### <a name="create-pingboard-test-user"></a>Créer un utilisateur de test Pingboard

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans Pingboard. Pingboard prend en charge l’attribution automatique d’utilisateurs, qui est activée par défaut. Vous trouverez plus d’informations [ici](pingboard-provisioning-tutorial.md) sur la façon de configurer l’attribution automatique d’utilisateurs.

**Si vous avez besoin de créer un utilisateur manuellement, procédez comme suit :**

1. Connectez-vous au site d’entreprise Pingboard en tant qu’administrateur.

2. Cliquez sur le bouton **« Ajouter un employé »** sur la page **Annuaire**.

    ![Ajouter un employé](./media/pingboard-tutorial/test-user.png)

3. Dans la boîte de dialogue **Ajouter un employé** , procédez comme suit :

    ![Inviter des personnes](./media/pingboard-tutorial/create-name.png)

    a. Dans la zone de texte **Nom complet**, tapez le nom complet d’un utilisateur, par exemple, **Britta Simon**.

    b. Dans la zone de texte **E-mail**, tapez l’adresse e-mail d’un utilisateur, par exemple, **brittasimon@contoso.com** .

    c. Dans la zone de texte **Poste**, tapez le poste de Britta Simon.

    d. Dans la liste déroulante **Emplacement**, sélectionnez l’emplacement de Britta Simon.

    e. Cliquez sur **Ajouter**.

4. Un écran de confirmation s’affiche pour confirmer l’ajout de l’utilisateur.

    ![Confirmer](./media/pingboard-tutorial/confirm-user.png)

    > [!NOTE]
    > Le titulaire du compte Azure Active Directory reçoit un e-mail contenant un lien à suivre pour confirmer son compte et l’activer.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

#### <a name="sp-initiated"></a>Lancée par le fournisseur de services :

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion à Pingboard à partir de laquelle vous pouvez lancer le flux de connexion.  

* Accédez directement à l’URL de connexion à Pingboard, puis initiez le flux de connexion à partir de cet emplacement.

#### <a name="idp-initiated"></a>Lancée par le fournisseur d’identité :

* Cliquez sur **Tester cette application** dans le portail Azure. Vous devez alors être connecté automatiquement à l’instance de Pingboard pour laquelle vous avez configuré l’authentification unique. 

Vous pouvez aussi utiliser Mes applications de Microsoft pour tester l’application dans n’importe quel mode. Quand vous cliquez sur la vignette Pingboard dans Mes applications, si le mode Fournisseur de services est configuré, vous êtes redirigé vers la page de connexion de l’application pour initier le flux de connexion. Si le mode Fournisseur d’identité est configuré, vous êtes automatiquement connecté à l’instance de Pingboard pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez configuré Pingboard, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
