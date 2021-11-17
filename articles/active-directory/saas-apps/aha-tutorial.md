---
title: 'Tutoriel : Intégration d’Azure Active Directory à Aha! | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Aha!.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/20/2021
ms.author: jeedes
ms.openlocfilehash: bb3b3038bdf6b210d1fcd4d40798b43e4cdab2ef
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132280770"
---
# <a name="tutorial-integrate-aha-with-azure-active-directory"></a>Tutoriel : Intégrer Aha! à Azure Active Directory

Dans ce tutoriel, vous allez découvrir comment intégrer Aha! à Azure Active Directory (Azure AD). Quand vous intégrez Aha! à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Aha!.
* Permettre à vos utilisateurs de se connecter automatiquement à Aha! avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Aha! pour lequel l’authentification unique (SSO) est activée.

> [!NOTE]
> Cette intégration peut également être utilisée à partir de l’environnement cloud US Government Azure AD. Cette application est disponible dans la Galerie d’applications cloud US Government Azure AD et peut être configurée de la même façon que dans le cloud public.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* Aha! prend en charge l’authentification unique lancée par le **fournisseur de services**
* Aha! prend en charge le provisionnement d’utilisateurs **juste-à-temps**

## <a name="add-aha-from-the-gallery"></a>Ajouter Aha! à partir de la galerie

Pour configurer l’intégration d’Aha! à Azure AD, vous devez ajouter Aha! depuis la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Aha!** dans la zone de recherche.
1. Sélectionnez **Aha!** dans le volet des résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-aha"></a>Configurer et tester l’authentification unique Azure AD pour Aha!

Configurer et tester l’authentification unique Azure AD avec Aha! en utilisant un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Aha! associé.

Pour configurer et tester l’authentification unique Azure AD avec Aha!, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
2. **[Configurer l’authentification unique Aha !](#configure-aha-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test Aha!](#create-aha-test-user)** pour avoir un équivalent de B.Simon dans Aha! lié à la représentation Azure AD de l’utilisateur.
3. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, sur la page d’intégration de l’application **Aha!** , recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon pour **Configuration SAML de base** afin de modifier les paramètres.

    ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    a. Dans la zone de texte **URL de connexion**, saisissez une URL au format suivant : `https://<companyname>.aha.io/session/new`

    b. Dans la zone de texte **Identificateur (ID d’entité)** , saisissez une URL au format suivant : `https://<companyname>.aha.io`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Pour obtenir ces valeurs, contactez [ l’équipe du support technique d’Aha!](https://www.aha.io/company/contact). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

4. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **XML de métadonnées de fédération** et sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

6. Dans la section **Configurer Aha!** , copiez la ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Aha!.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Aha!** .
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-aha-sso"></a>Configurer Aha ! SSO

1. Pour automatiser la configuration dans Aha!, vous devez installer l’**extension de navigateur My Apps Secure Sign-in** en cliquant sur **Installer l’extension**.

    ![Extension My apps](common/install-myappssecure-extension.png)

2. Après avoir ajouté l’extension au navigateur, cliquer sur **Configurer Aha!** vous redirige vers l’application application Aha!. À partir de là, indiquez les informations d’identification de l’administrateur pour vous connecter à Aha!. Cette extension de navigateur configure automatiquement l’application et automatise les étapes 3 à 8.

    ![Configuration](common/setup-sso.png)

3. Si vous voulez configurer Aha! manuellement, ouvrez une nouvelle fenêtre de navigateur web et connectez-vous à votre site d’entreprise Aha! en tant qu’administrateur et effectuez les étapes suivantes :

4. Dans le menu situé en haut, cliquez sur **Settings**.

    ![Paramètres](./media/aha-tutorial/setting.png "Paramètres")

5. Cliquez sur **Account**.

    ![Profil](./media/aha-tutorial/account.png "Profil")

6. Cliquez sur **Security and single sign-on**.

    ![Capture d’écran mettant en évidence l’option de menu Security and single sign-on](./media/aha-tutorial/security.png "Security and single sign-on").

7. Dans la section **Single Sign-On**, pour **Identity Provider**, sélectionnez **SAML2.0**.

    ![Sécurité et authentification unique](./media/aha-tutorial/saml.png "Security and single sign-on")

8. Dans la page de configuration **Single Sign on** , procédez comme suit :

    ![Authentification unique](./media/aha-tutorial/sso.png "Single Sign on")

    a. Dans la zone de texte **Name** , tapez le nom de votre configuration.

    b. Pour **Configure using**, sélectionnez **Metadata File**.

    c. Pour charger le fichier de métadonnées téléchargé, cliquez sur **Browse**.

    d. Cliquez sur **Update**.

### <a name="create-aha-test-user"></a>Création d’un utilisateur de test Aha!

Dans cette section, un utilisateur appelé B.Simon est créé dans Aha!. Aha! prend en charge le provisionnement d’utilisateurs juste-à-temps, option activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas encore d’utilisateur dans Aha!, il en est créé un après l’authentification.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Cette opération redirige vers l’URL de connexion à Aha!, où vous pouvez lancer le flux de connexion. 

* Accédez directement à l’URL de connexion à Aha! et lancez le flux de connexion.

* Vous pouvez utiliser Mes applications de Microsoft. Quand vous cliquez sur la vignette Aha! dans Mes applications, vous êtes redirigé vers l’ URL de connexion. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez configuré Aha!, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
