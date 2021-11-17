---
title: 'Tutoriel : Intégration de l’authentification unique Azure Active Directory à ekarda | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et ekarda.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/24/2020
ms.author: jeedes
ms.openlocfilehash: e5b0abc3cd6aa0850f48bced115f0b43d326b081
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132285957"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ekarda"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory à ekarda

Dans ce tutoriel, vous allez découvrir comment intégrer ekarda à Azure Active Directory (Azure AD). Quand vous intégrez ekarda à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à ekarda.
* Permettre à vos utilisateurs de se connecter automatiquement à ekarda avec leur compte Azure AD.
* gérer vos comptes à un emplacement central : le portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement ekarda pour lequel l’authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* ekarda prend en charge l’authentification unique lancée par le fournisseur de services et par le fournisseur d’identité.
* ekarda prend en charge le provisionnement d’utilisateurs juste-à-temps.

## <a name="add-ekarda-from-the-gallery"></a>Ajouter ekarda à partir de la galerie

Pour configurer l’intégration d’ekarda à Azure AD, ajoutez ekarda à partir de la galerie à votre liste d’applications SaaS managées :

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire, ou avec un compte personnel Microsoft.

1. Dans le volet de gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter la nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **ekarda** dans la zone de recherche.
1. Sélectionnez **ekarda** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-ekarda"></a>Configurer et tester l’authentification unique Azure AD pour ekarda

Configurez et testez l’authentification unique Azure AD avec ekarda en utilisant un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur ekarda associé.

Pour configurer et tester l’authentification unique Azure AD avec ekarda, effectuez les étapes suivantes :

1. [Configurer l’authentification unique Azure AD](#configure-azure-ad-sso) pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.

    1. [Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user) pour tester l’authentification unique Azure AD avec B.Simon.
    1. [Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user) pour permettre à B.Simon d’utiliser l’authentification unique Azure AD.
1. [Configurer l’authentification unique ekarda](#configure-ekarda-sso) pour configurer les paramètres de l’authentification unique côté application.
    * [Créer un utilisateur de test ekarda](#create-an-ekarda-test-user) pour avoir un équivalent de B.Simon dans ekarda lié à la représentation Azure AD de l’utilisateur.
1. [Tester l’authentification unique](#test-sso) pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes dans le portail Azure pour activer l’authentification unique Azure AD :

1. Connectez-vous au portail Azure.
1. Dans la page d’intégration de l’application **ekarda**, recherchez la section **Gérer**, puis sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, sélectionnez l’icône de crayon pour modifier les paramètres de **Configuration SAML de base**.

   ![Capture d’écran de la page Configurer l’authentification unique avec SAML, avec l’icône de crayon mise en évidence](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, si vous voyez **Fichier de métadonnées du fournisseur de services**, suivez ces étapes :
    1. Sélectionnez **Charger le fichier de métadonnées**.
    1. Sélectionnez l’icône de dossier pour sélectionner le fichier de métadonnées, puis sélectionnez **Charger**.
    1. Une fois le fichier de métadonnées correctement chargé, les valeurs pour **Identificateur** et **URL de réponse** apparaissent automatiquement dans les zones de texte de la section ekarda.

    > [!Note]
    > Si les valeurs pour **Identificateur** et **URL de réponse** n’apparaissent pas automatiquement, spécifiez-les manuellement en fonction de vos besoins.

1. Si vous ne voyez pas **Fichier de métadonnées du fournisseur de services** dans la section **Configuration SAML de base** et que vous voulez configurer l’application en mode Lancé par le fournisseur d’identité, entrez des valeurs pour les champs suivants :

    1. Dans la zone de texte **Identificateur**, entrez une URL au format suivant : `https://my.ekarda.com/users/saml_metadata/<COMPANY_ID>`
    1. Dans la zone de texte **URL de réponse**, entrez une URL au format suivant : `https://my.ekarda.com/users/saml_acs/<COMPANY_ID>`

1. Si vous voulez configurer l’application en mode Lancé par le fournisseur de services, sélectionnez **Définir des URL supplémentaires** et effectuez les actions suivantes :

    Dans la zone de texte **URL de connexion**, entrez une URL au format suivant : `https://my.ekarda.com/users/saml_sso/<COMPANY_ID>`

    > [!NOTE]
    > Les valeurs des deux étapes précédentes ne sont pas réelles. Remplacez-les par les valeurs réelles de l’identificateur, de l’URL de réponse et de l’URL d’authentification. Pour obtenir ces valeurs, contactez l’[équipe de support client ekarda](mailto:contact@ekarda.com). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, sélectionnez **Télécharger** pour enregistrer le **Certificat (Base64)** sur votre ordinateur.

    ![Capture d’écran de la section Certificat de signature SAML de la page Configurer l’authentification unique avec SAML, avec le lien de téléchargement mis en évidence pour le certificat Base64.](common/certificatebase64.png)

1. Dans la section **Configurer ekarda**, copiez la ou les URL appropriées en fonction de vos besoins.

    ![Capture d’écran de la section Configurer ekarda de la page Configurer l’authentification unique avec SAML, avec les liens de copie d’URL mis en évidence.](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

Dans cette section, vous allez utiliser le portail Azure pour créer un utilisateur de test appelé B.Simon.

1. Dans le volet gauche du portail Azure, sélectionnez **Azure Active Directory** > **Utilisateurs** > **Tous les utilisateurs**.

1. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.
1. Dans les propriétés **Utilisateur**, effectuez les étapes suivantes :
   1. Dans le champ **Nom**, entrez `B.Simon`.  
   1. Dans le champ **Nom de l’utilisateur**, entrez username@companydomain.extension. Par exemple, entrez `B.Simon@contoso.com`.
   1. Cochez la case **Afficher le mot de passe**, puis notez la valeur qui apparaît dans la zone **Mot de passe**.
   1. Sélectionnez **Create** (Créer).

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à ekarda.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise** > **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **ekarda**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.

1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B.Simon** dans la liste des utilisateurs. Choisissez ensuite **Sélectionner** dans le bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, sélectionnez **Affecter**.

## <a name="configure-ekarda-sso"></a>Configurer l’authentification unique ekarda

1. Pour automatiser la configuration dans ekarda, vous devez installer l’**extension de navigateur My Apps Secure Sign-in** en cliquant sur **Installer l’extension**.

    ![Extension My apps](common/install-myappssecure-extension.png)

2. Une fois l’extension ajoutée au navigateur, cliquez sur **Configurer ekarda** pour être dirigé vers l’application ekarda. À partir de là, indiquez les informations d’identification de l’administrateur pour vous connecter à ekarda. Cette extension de navigateur configure automatiquement l’application et automatise les étapes 3 à 6.

    ![Configuration](common/setup-sso.png)

3. Si vous souhaitez configurer ekarda manuellement, dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise ekarda en tant qu’administrateur.

1. Cliquez sur **Admin (Administrateur)**  > **My Account (Mon compte)** .

    ![Capture d’écran de l’interface utilisateur du site ekarda avec My Account mis en évidence dans le menu Admin.](./media/ekarda-tutorial/ekarda.png)

1. En bas de la page, recherchez la section **SAML SETTINGS** (Paramètres SAML). Cette section est l’endroit où vous configurez l’intégration SAML.
1. Dans la section **SAML SETTINGS**, suivez ces étapes :

    ![Capture d’écran de la page SAML SETTINGS d’ekarda avec les champs de configuration de SAML mis en évidence.](./media/ekarda-tutorial/ekarda1.png)

    1. Sélectionnez le lien **Service Provider metadata** (Métadonnées du fournisseur de services) et enregistrez-le en tant que fichier sur votre ordinateur.
    1. Activez la case à cocher **Enable SAML** (Activer SAML).
    1. Dans la zone de texte **IDP Entity ID** (ID d’entité IDP), collez la valeur de **Identificateur Azure AD** que vous avez précédemment copiée à partir du portail Azure.
    1. Dans la zone de texte **IDP Login URL** (URL de connexion IDP), collez la valeur de **URL de connexion** que vous avez précédemment copiée à partir du portail Azure.
    1. Dans la zone de texte **IDP Logout URL** (URL de déconnexion IDP), collez la valeur de **URL de déconnexion** que vous avez précédemment copiée à partir du portail Azure.
    1. Utilisez le Bloc-notes pour ouvrir le fichier **Certificat (Base64)** que vous avez téléchargé à partir du portail Azure. Collez ce contenu dans la zone de texte **IDP x509 Certificate** (Certificat x509 IDP).
    1. Cochez la case **Enable SLO** (Activer SLO) dans la section **OPTIONS**.
    1. Sélectionnez **Update**.

### <a name="create-an-ekarda-test-user"></a>Créer un utilisateur de test ekarda

Dans cette section, un utilisateur appelé B.Simon est créé dans ekarda. ekarda prend en charge le provisionnement d’utilisateurs juste-à-temps, lequel est activé par défaut. Vous n’avez aucune action à effectuer dans cette section. S’il n’existe pas déjà un utilisateur nommé B.Simon dans ekarda, il en est créé un après l’authentification.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes.

#### <a name="sp-initiated"></a>Lancée par le fournisseur de services :

* Cliquez sur **Tester cette application** dans le portail Azure. Cette opération redirige vers l’URL de connexion ekarda où vous pouvez lancer le processus de connexion.

* Accédez directement à l’URL de connexion ekarda pour lancer le processus de connexion.

#### <a name="idp-initiated"></a>Lancée par le fournisseur d’identité :

* Cliquez sur **Tester cette application** dans le portail Azure ; vous devez être connecté automatiquement à l’instance d’ekarda pour laquelle vous avez configuré l’authentification unique.

Vous pouvez aussi utiliser Mes applications de Microsoft pour tester l’application dans n’importe quel mode. Si, quand vous cliquez sur la vignette ekarda dans Mes applications, le mode Fournisseur de services est configuré, vous êtes redirigé vers la page de connexion de l’application pour lancer le flux de connexion ; s’il s’agit du mode Fournisseur d’identité, vous êtes automatiquement connecté à l’instance d’ekarda pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré ekarda, vous pouvez appliquer le contrôle de session. Cette précaution protège contre l’exfiltration et l’infiltration de données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir du contrôle d’application par accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
