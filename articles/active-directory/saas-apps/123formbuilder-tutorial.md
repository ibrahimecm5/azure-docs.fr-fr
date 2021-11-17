---
title: 'Tutoriel : Intégration d’Azure Active Directory à 123FormBuilder SSO | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et 123FormBuilder SSO.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/25/2021
ms.author: jeedes
ms.openlocfilehash: dee24978615ca1e22f0404093c72d1ed7e1a5194
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132339045"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-123formbuilder-sso"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory à 123FormBuilder SSO

Dans ce tutoriel, vous allez découvrir comment intégrer 123FormBuilder SSO à Azure Active Directory (Azure AD). Quand vous intégrez 123FormBuilder SSO à Azure AD, vous pouvez :

* Contrôler qui dans Azure AD a accès à 123FormBuilder SSO.
* Permettre à vos utilisateurs de se connecter automatiquement à 123FormBuilder SSO avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement 123FormBuilder SSO pour lequel l’authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* 123FormBuilder SSO prend en charge l’authentification unique initiée par le **fournisseur de services et le fournisseur d’identité**.
* 123FormBuilder SSO prend en charge le provisionnement d’utilisateurs **juste-à-temps**.

## <a name="add-123formbuilder-sso-from-the-gallery"></a>Ajouter 123FormBuilder SSO depuis la galerie

Pour configurer l’intégration de 123FormBuilder SSO à Azure AD, vous devez ajouter 123FormBuilder SSO à votre liste d’applications SaaS gérées à partir de la galerie.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **123FormBuilder SSO** dans la zone de recherche.
1. Sélectionnez **123FormBuilder SSO** dans le panneau de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-123formbuilder-sso"></a>Configurer et tester l’authentification unique Azure AD pour 123FormBuilder SSO

Configurez et testez l’authentification unique Azure AD avec 123FormBuilder SSO pour un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur associé dans 123FormBuilder SSO.

Pour configurer et tester l’authentification unique Azure AD avec 123FormBuilder SSO, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique 123FormBuilder SSO](#configure-123formbuilder-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test 123FormBuilder SSO](#create-123formbuilder-sso-test-user)** pour avoir un équivalent de B.Simon dans 123FormBuilder SSO lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **123FormBuilder SSO**, recherchez la section **Gérer**, puis sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

4. À la section **Configuration SAML de base**, si vous souhaitez configurer l’application en mode initié par **IDP**, suivez les étapes ci-dessous :

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://www.123formbuilder.com/saml/azure_ad/<TENANT_ID>/metadata`


    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://www.123formbuilder.com/saml/azure_ad/<TENANT_ID>/acs`

5. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://www.123formbuilder.com/saml/azure_ad/<TENANT_ID>/sso`.

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Vous devez changer cette valeur pour les URL et l’identificateur réels. Ceci est expliqué plus loin dans le didacticiel.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **XML de métadonnées de fédération** et sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

1. Dans la section **Configurer 123FormBuilder SSO**, copiez la ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à 123FormBuilder SSO.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **123FormBuilder SSO**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-123formbuilder-sso"></a>Configurer 123FormBuilder SSO

1. Pour configurer l’authentification unique côté **123FormBuilder SSO**, accédez à [https://www.123formbuilder.com/form-2709121/](https://www.123formbuilder.com/form-2709121/) et effectuez les étapes suivantes :

    ![Capture d’écran montrant l’écran de configuration SSO SAML - Identity Provider.](./media/123formbuilder-tutorial/submit.png) 

    a. Dans la zone de texte **Email**, entrez l’adresse e-mail de l’utilisateur, par exemple `B.Simon@Contoso.com`.

    b. Cliquez sur **Charger** et accédez au fichier XML de métadonnées que vous avez téléchargé à partir du portail Azure.

    c. Cliquez sur **Envoyer le formulaire**.

2. Dans **Microsoft Azure AD - Authentification unique - Configurer les paramètres de l’application**, procédez comme suit :

    ![Configure Single Sign-On](./media/123formbuilder-tutorial/configuration.png)

    a. Si vous voulez configurer l’application en **mode lancé par le fournisseur d’identité**, copiez la valeur de **Identificateur** pour votre instance et collez-la dans la zone de texte **Identificateur** de la section **Configuration SAML de base** sur le portail Azure.

    b. Si vous voulez configurer l’application en **mode lancé par le fournisseur d’identité**, copiez la valeur de **URL de réponse** pour votre instance et collez-la dans la zone de texte **URL de réponse** de la section **Configuration SAML de base** sur le portail Azure.

    c. Si vous voulez configurer l’application en **mode lancé par le fournisseur d’identité**, copiez la valeur de **URL de connexion** pour votre instance et collez-la dans la zone de texte **URL de connexion** de la section **Configuration SAML de base** sur le portail Azure.

### <a name="create-123formbuilder-sso-test-user"></a>Créer un utilisateur de test 123FormBuilder SSO

Dans cette section, un utilisateur appelé Britta Simon est créé dans 123FormBuilder SSO. 123FormBuilder SSO prend en charge le provisionnement d’utilisateurs juste-à-temps, qui est activé par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas encore d’utilisateur dans 123FormBuilder SSO, un nouvel utilisateur est créé après l’authentification.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

#### <a name="sp-initiated"></a>Lancée par le fournisseur de services :

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes redirigé vers l’URL de connexion à 123FormBuilder SSO, à partir de laquelle vous pouvez lancer le flux de connexion.  

* Accédez directement à l’URL de connexion à 123FormBuilder SSO et lancez-y le flux de connexion.

#### <a name="idp-initiated"></a>Lancée par le fournisseur d’identité :

* Un clic sur **Tester cette application** dans le portail Azure doit vous connecter automatiquement à l’instance de 123FormBuilder SSO pour laquelle vous avez configuré l’authentification unique. 

Vous pouvez aussi utiliser Mes applications de Microsoft pour tester l’application dans n’importe quel mode. Si, quand vous cliquez sur la vignette 123FormBuilder SSO dans Mes applications, le mode Fournisseur de services est configuré, vous êtes redirigé vers la page de connexion de l’application pour lancer le flux de connexion ; si c’est le mode Fournisseur d’identité qui est configuré, vous êtes automatiquement connecté à l’instance de 123FormBuilder SSO pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré 123FormBuilder SSO, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
