---
title: 'Tutoriel : Intégration de l’authentification unique Azure Active Directory à Screencast-O-Matic | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Screencast-O-Matic.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/31/2021
ms.author: jeedes
ms.openlocfilehash: c1c64606600679e49ffc63c97f58a4a9a7764687
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132279605"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-screencast-o-matic"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory à Screencast-O-Matic

Dans ce tutoriel, vous allez apprendre à intégrer Screencast-O-Matic à Azure Active Directory (Azure AD). En intégrant Screencast-O-Matic à Azure AD, vous pouvez :

* Contrôler, dans Azure AD, qui a accès à Screencast-O-Matic
* Permettre aux utilisateurs de se connecter automatiquement à Screencast-O-Matic avec leur compte Azure AD
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Screencast-O-Matic pour lequel l’authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* Screencast-O-Matic prend en charge l’authentification unique lancée par le **fournisseur de services**.
* Screencast-O-Matic prend en charge l’attribution d’utilisateurs **juste-à-temps**.

> [!NOTE]
> L’identificateur de cette application étant une valeur de chaîne fixe, une seule instance peut être configurée dans un locataire.

## <a name="add-screencast-o-matic-from-the-gallery"></a>Ajouter Screencast-O-Matic à partir de la galerie

Pour configurer l’intégration de Screencast-O-Matic dans Azure AD, vous devez ajouter Screencast-O-Matic depuis la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Screencast-O-Matic** dans la zone de recherche.
1. Sélectionnez **Screencast-O-Matic** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-screencast-o-matic"></a>Configurer et tester l’authentification unique Azure AD pour Screencast-O-Matic

Configurez et testez l’authentification unique Azure AD auprès de Screencast-O-Matic avec un utilisateur de test nommé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Screencast-O-Matic associé.

Pour configurer et tester l’authentification unique Azure AD avec Screencast-O-Matic, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Screencast-O-Matic](#configure-screencast-o-matic-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test Screencast-O-Matic](#create-screencast-o-matic-test-user)** pour avoir un équivalent de B.Simon dans Screencast-O-Matic qui est lié à la représentation de l’utilisateur Azure Active Directory.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Sur le portail Azure, dans la page d’intégration de l’application **Screencast-O-Matic**, recherchez la section **Gérer**, puis sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, effectuez l’étape suivante :

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://screencast-o-matic.com/<InstanceName>`

    > [!NOTE]
    > Cette valeur n’est pas la valeur réelle. Mettez à jour la valeur avec l’URL de connexion réelle. Pour obtenir cette valeur, contactez [l’équipe du support technique de Screencast-O-Matic](mailto:support@screencast-o-matic.com). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **XML de métadonnées de fédération** et sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

1. Dans la section **Configurer Screencast-O-Matic**, copiez l’URL ou les URL appropriées en fonction de vos exigences.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Screencast-O-Matic.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Screencast-O-Matic**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-screencast-o-matic-sso"></a>Configurer l’authentification unique Screencast-O-Matic

1. Pour automatiser la configuration dans Screencast-O-Matic, vous devez installer l’**extension de navigateur My Apps Secure Sign-in** en cliquant sur **Installer l’extension**.

    ![Extension My apps](common/install-myappssecure-extension.png)

1. Après avoir ajouté l’extension au navigateur, cliquez sur **Configurer Screencast-O-Matic** pour être dirigé vers l’application Screencast-O-Matic. À partir de là, indiquez les informations d’identification de l’administrateur pour vous connecter à Screencast-O-Matic. Cette extension de navigateur configure automatiquement l’application et automatise les étapes 3 à 10.

    ![Configuration](common/setup-sso.png)

1. Si vous souhaitez configurer manuellement Screencast-O-Matic, ouvrez une nouvelle fenêtre de navigateur web, connectez-vous à votre site d’entreprise Screencast-O-Matic en tant qu’administrateur et effectuez les étapes suivantes :

1. Cliquez sur **Abonnement**.

    ![Capture d’écran montrant l’option Abonnement.](./media/screencast-tutorial/subscribe.png)

1. Sous la section **Access page** (Page d’accès), cliquez sur **Setup** (Configuration).

    ![Capture d’écran montrant la section « Access Page » avec le bouton « Setup » sélectionné.](./media/screencast-tutorial/setup.png)

1. Dans la **page Configurer l’accès**, effectuez les étapes suivantes.

1. Dans la section **Access URL** (URL d’accès), tapez le nom de votre instance dans la zone de texte spécifiée.

    ![Capture d’écran montrant la section « Access URL » avec la zone de texte du nom de l’instance mise en évidence.](./media/screencast-tutorial/access-page.png)

1. Sélectionnez **Demander un utilisateur de domaine** dans la section **Restriction d’utilisateur SAML (facultatif)** .

1. Sous **Charger le fichier XML des métadonnées IDP**, cliquez sur **Sélectionner le fichier** pour charger les métadonnées que vous avez téléchargées à partir du portail Azure.

1. Cliquez sur **OK**.

    ![Capture d’écran montrant l’accès.](./media/screencast-tutorial/metadata.png)

### <a name="create-screencast-o-matic-test-user"></a>Créer un utilisateur de test Screencast-O-Matic

Dans cette section, un utilisateur nommé Britta Simon est créé dans Screencast-O-Matic. Screencast-O-Matic prend en charge l’attribution d’utilisateurs juste-à-temps, une option activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas encore d’utilisateur dans Screencast-O-Matic, un nouvel utilisateur est créé après l’authentification. Si vous devez créer un utilisateur manuellement, contactez [l’équipe de support client Screencast-O-Matic](mailto:support@screencast-o-matic.com).

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion Screencast-O-Matic, à partir de laquelle vous pouvez lancer le flux de connexion. 

* Accédez directement à l’URL de connexion Screencast-O-Matic pour lancer le flux de connexion.

* Vous pouvez utiliser Mes applications de Microsoft. Le fait de cliquer sur la vignette Screencast-O-Matic dans Mes applications vous redirige vers l’URL de connexion Screencast-O-Matic. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré Screencast-O-Matic, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
