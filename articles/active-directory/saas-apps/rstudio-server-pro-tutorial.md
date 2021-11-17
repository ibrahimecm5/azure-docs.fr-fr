---
title: 'Tutoriel : Intégration de l’authentification unique (SSO) Azure Active Directory à RStudio Server Pro | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et RStudio Server Pro.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/28/2020
ms.author: jeedes
ms.openlocfilehash: 13c278474f841e957612711f63486671ad28f921
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132320784"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-rstudio-server-pro"></a>Tutoriel : Intégration de l’authentification unique (SSO) Azure Active Directory à RStudio Server Pro

Dans ce tutoriel, vous allez découvrir comment intégrer RStudio Server Pro (RSP) à Azure Active Directory (Azure AD). Quand vous intégrez RSP à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à RSP.
* Permettre à vos utilisateurs de se connecter automatiquement à RSP avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Installation de RSP (version >= 1.4)

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* RSP prend en charge l’authentification unique lancée par **le fournisseur de services et le fournisseur d’identité**

## <a name="adding-rstudio-server-pro-from-the-gallery"></a>Ajout de RStudio Server Pro à partir de la galerie

Pour configurer l’intégration de RSP à Azure AD, vous devez ajouter RStudio Server Pro SAML Authentication à votre liste d’applications SaaS managées à partir de la galerie.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **RStudio Server Pro SAML Authentication** dans la zone de recherche.
1. Sélectionnez **RStudio Server Pro SAML Authentication** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.


## <a name="configure-and-test-azure-ad-sso-for-rstudio-server-pro"></a>Configurer et tester l’authentification unique (SSO) Azure AD pour RStudio Server Pro

Configurez et testez l’authentification unique Azure AD avec RSP à l’aide d’un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur RSP associé.

Pour configurer et tester l’authentification unique Azure AD avec RStudio Server Pro SAML Authentication, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique RStudio Server Pro](#configure-rstudio-server-pro-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test RStudio Server Pro](#create-rstudio-server-pro-test-user)** pour avoir un équivalent de B.Simon dans RStudio Server Pro lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **RStudio Server Pro SAML Authentication**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de modification/stylet de **Configuration SAML de base** pour modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, si vous souhaitez configurer l’application en mode Initié par le **fournisseur d’identité**, entrez les valeurs pour les champs suivants :

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<RSP-SERVER>/<PATH>/saml/metadata`

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://<RSP-SERVER>/<PATH>/saml/acs`

1. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<RSP-SERVER>/<PATH>/`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URI réel de votre installation de RSP. Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

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

Dans cette section, vous autorisez B.Simon à utiliser l’authentification unique Azure en accordant l’accès à RStudio Server Pro SAML Authentication.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **RStudio Server Pro SAML Authentication**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-rstudio-server-pro-sso"></a>Configurer l’authentification unique RStudio Server Pro

1. Mettez à jour le fichier de configuration RSP `/etc/rstudio/rserver.conf` avec ce qui suit :

    ```
    auth-saml=1
    auth-saml-metadata-url=<federation-metadata-URI>
    auth-saml-sp-name-id-format=emailaddress
    auth-saml-sp-attribute-username=NameID
    auth-saml-sp-base-uri=<RSP-Server-URI>
    ```

2. Redémarrez RSP en exécutant la commande suivante :

    ```
    sudo rstudio-server restart
    ```

### <a name="create-rstudio-server-pro-test-user"></a>Créer un utilisateur de test RStudio Server Pro

Tous les utilisateurs qui doivent utiliser RSP doivent être provisionnés sur le serveur. Vous pouvez créer l’utilisateur avec la commande `useradd` ou `adduser`.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

#### <a name="sp-initiated"></a>Lancée par le fournisseur de services :

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion RStudio Server Pro SAML Authentication, à partir de laquelle vous pouvez lancer le flux de connexion.  

* Accédez directement à l’URL de connexion RStudio Server Pro SAML Authentication pour y lancer le processus de connexion.

#### <a name="idp-initiated"></a>Lancée par le fournisseur d’identité :

* Sur le portail Azure, cliquez sur **Tester cette application**. Vous êtes alors automatiquement connecté à l’application RStudio Server Pro SAML Authentication pour laquelle vous avez configuré l’authentification unique 

Vous pouvez aussi utiliser le panneau d’accès Microsoft pour tester l’application dans n’importe quel mode. Si, au moment de cliquer sur la vignette RStudio Server Pro SAML Authentication dans le volet d’accès, le mode Fournisseur de services est configuré, vous êtes redirigé vers la page de connexion de l’application pour lancer le flux de connexion ; s’il s’agit du mode Fournisseur d’identité, vous êtes automatiquement connecté à l’application RStudio Server Pro SAML Authentication pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré RStudio Server Pro SAML Authentication, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
