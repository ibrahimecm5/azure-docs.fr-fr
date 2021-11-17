---
title: 'Didacticiel : Intégration d’Azure Active Directory à Zscaler Beta | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Zscaler Beta.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/02/2021
ms.author: jeedes
ms.openlocfilehash: 96a73d9bba60f14c4f9b70aca9cf546cd3cb7e3f
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132323583"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-beta"></a>Didacticiel : Intégration d’Active Directory à Zscaler Beta

Dans ce didacticiel, vous allez découvrir comment intégrer Zscaler Beta à Azure Active Directory (Azure AD).
Quand vous intégrez Zscaler Beta à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Zscaler Beta.
* Permettre aux utilisateurs de se connecter automatiquement à Zscaler Beta avec leur compte Azure AD. Ce contrôle d’accès porte le nom d’authentification unique (SSO).
* Gérer vos comptes à partir d’un emplacement centralisé à l’aide du portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à Zscaler Beta, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un [compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Zscaler Beta qui utilise l’authentification unique.

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Zscaler Beta prend en charge l’authentification SSO lancée par le fournisseur de services (**SP**).
* Zscaler Beta prend en charge le provisionnement d’utilisateurs **juste-à-temps**.
* Zscaler Beta prend en charge l’[attribution automatique d’utilisateurs](zscaler-beta-provisioning-tutorial.md).

## <a name="adding-zscaler-beta-from-the-gallery"></a>Ajouter Zscaler Beta à partir de la galerie

Pour configurer l’intégration de Zscaler Beta à Azure AD, vous devez ajouter Zscaler Beta, disponible dans la galerie, à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, dans la zone de recherche, tapez **Zscaler Beta**.
1. Sélectionnez **Zscaler Beta** dans le panneau Résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-zscaler-beta"></a>Configurer et tester l’authentification SSO Azure AD pour Zscaler Beta

Configurez et testez l’authentification SSO Azure AD avec Zscaler Beta à l’aide d’une utilisatrice de test appelée **B.Simon**. Pour que l’authentification SSO fonctionne, vous devez établir une relation entre un utilisateur Azure AD et l’utilisateur associé dans Zscaler Beta.

Pour configurer et tester l’authentification SSO Azure AD avec Zscaler Beta, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
   1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
   1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification SSO Zscaler Beta](#configure-zscaler-beta-sso)** pour configurer les paramètres d’authentification unique côté application.
   1. **[Créer une utilisatrice de test pour Zscaler Beta](#create-zscaler-beta-test-user)** afin de disposer dans Zscaler Beta d’un équivalent de B.Simon lié à la représentation Azure AD de l’utilisatrice.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, dans la page d’intégration de l’application **Zscaler Beta**, recherchez la section **Gérer**, puis sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon pour **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, entrez les valeurs pour les champs suivants :

   Dans la zone **URL de connexion**, entrez l’URL utilisée par les utilisateurs pour se connecter à votre application Zscaler Beta.

   > [!NOTE]
   > Cette valeur n’est pas réelle. Remplacez-la par la valeur d’URL de connexion réelle. Pour obtenir la valeur, contactez l’[équipe de support technique de Zscaler Beta](https://www.zscaler.com/company/contact).

1. L’application Zscaler Beta attend les assertions SAML dans un format spécifique. L’utilisateur doit ajouter des mappages d’attributs personnalisés à la configuration d’attributs de jeton SAML. La capture d’écran suivante montre la liste des attributs par défaut. Sélectionnez **Modifier** pour ouvrir la boîte de dialogue **Attributs utilisateur**.

   ![Boîte de dialogue Attributs utilisateur](common/edit-attribute.png)

1. L’application Zscaler Beta s’attend à ce que quelques attributs supplémentaires soient passés dans la réponse SAML. Dans la section **Revendications des utilisateurs** de la boîte de dialogue **Attributs utilisateur**, effectuez les étapes suivantes pour ajouter le jeton SAML comme indiqué dans le tableau ci-dessous.

   | Nom     | Attribut source   |
   | -------- | ------------------ |
   | memberOf | user.assignedroles |

   a. Sélectionnez **Ajouter une nouvelle revendication** pour ouvrir la boîte de dialogue **Gérer les revendications des utilisateurs**.

   b. Dans la zone de texte **Nom**, entrez le nom d’attribut affiché pour cette ligne.

   c. Laissez la zone **Espace de noms** vide.

   d. Dans **Source**, sélectionnez **Attribut**.

   e. Dans la liste **Attribut de la source**, entrez la valeur d’attribut affichée pour cette ligne.

   f. Sélectionnez **OK**.

   g. Sélectionnez **Enregistrer**.

   > [!NOTE]
   > Cliquez [ici](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui) pour savoir comment configurer un rôle dans Azure AD.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, sélectionnez **Télécharger** pour télécharger le **Certificat (Base64)**. Enregistrez-le sur votre ordinateur.

   ![Lien de téléchargement du certificat](common/certificatebase64.png)

1. Dans la section **Configurer Zscaler Beta**, copiez les URL dont vous avez besoin :

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

Dans cette section, vous allez permettre à B.Simon d’utiliser l’authentification unique Azure en lui octroyant l’accès à Zscaler Beta.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Zscaler Beta**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous avez configuré les rôles comme indiqué ci-dessus, vous pouvez en sélectionner un dans la liste déroulante **Sélectionner un rôle**.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-zscaler-beta-sso"></a>Configurer l’authentification SSO pour Zscaler Beta

1. Pour automatiser la configuration dans Zscaler Beta, vous devez installer l’**extension de navigateur My Apps Secure Sign-in** en sélectionnant **Install the extension** (Installer l’extension).

   ![Extension My apps](common/install-myappssecure-extension.png)

2. Une fois que vous avez ajouté l’extension au navigateur, la sélection de **Configurer Zscaler Beta** vous dirige vers l’application Zscaler Beta. À partir de là, indiquez les informations d’identification de l’administrateur pour vous connecter à Zscaler Beta. L’extension de navigateur configure automatiquement l’application pour vous et automatise les étapes 3 à 6.

   ![Configuration](common/setup-sso.png)

3. Pour configurer manuellement Zscaler Beta, ouvrez une nouvelle fenêtre de navigateur web. Connectez-vous à votre site d’entreprise Zscaler Beta en tant qu’administrateur et effectuez les étapes suivantes.

4. Accédez à **Administration** > **Authentication** > **Authentication Settings** et effectuez les étapes suivantes.

   ![Administration](./media/zscaler-beta-tutorial/settings.png "Administration")

   a. Sous **Authentication Type** (Type d’authentification), sélectionnez **SAML**.

   b. Sélectionnez **Configure SAML** (Configurer SAML).

5. Dans la fenêtre **Edit SAML** (Modifier SAML), effectuez les étapes suivantes : ![Manage Users & Authentication](./media/zscaler-beta-tutorial/certificate.png "Manage Users & Authentication") (Gérer les utilisateurs et l’authentification)

   a. Dans la zone **SAML Portal URL** (URL du portail SAML), collez la valeur de l’**URL de connexion** que vous avez copiée à partir du portail Azure.

   b. Dans la zone de texte **Login Name Attribute** (Attribut de nom de connexion), entrez **NameID**.

   c. Dans la zone **Public SSL Certificate** (Certificat SSL public), sélectionnez **Upload** pour charger le certificat de signature SAML Azure que vous avez téléchargé à partir du portail Azure.

   d. Cliquez sur le bouton bascule **Enable SAML Auto-Provisioning** (Activer le provisionnement automatique SAML).

   e. Dans la zone **User Display Name Attribute** (Attribut du nom d’affichage de l’utilisateur), entrez **displayName** si vous souhaitez activer le provisionnement automatique SAML pour les attributs displayName.

   f. Dans la zone **Group Name Attribute** (Attribut du nom de groupe), entrez **memberOf** si vous souhaitez activer le provisionnement automatique SAML pour les attributs memberOf.

   g. Dans **Department Name Attribute** (Attribut du nom du service), entrez **department** si vous souhaitez activer le provisionnement automatique SAML pour les attributs department.

   h. Sélectionnez **Enregistrer**.

6. Dans la page **Configure User Authentication** (Configurer l’authentification utilisateur), effectuez les étapes suivantes :

   ![Menu Activation et bouton Activer](./media/zscaler-beta-tutorial/status.png)

   a. Passez la souris sur le menu **Activation** en bas à gauche.

   b. Sélectionnez **Activer**.

## <a name="configure-proxy-settings"></a>Configuration des paramètres de proxy

Pour configurer les paramètres de proxy dans Internet Explorer, effectuez les étapes suivantes :

1. Démarrez **Internet Explorer**.

2. Dans le menu **Outils**, sélectionnez **Options Internet** pour ouvrir la boîte de dialogue **Options Internet**.

   ![Boîte de dialogue Options Internet](./media/zscaler-beta-tutorial/connection.png "Options Internet")

3. Sélectionnez l’onglet **Connexions** .

   ![Onglet Connexions](./media/zscaler-beta-tutorial/server.png "Connexions")

4. Sélectionnez **Paramètres réseau** pour ouvrir la boîte de dialogue **Paramètres du réseau local**.

5. Dans la section **Serveur proxy**, effectuez les étapes suivantes :

   ![Section Serveur proxy](./media/zscaler-beta-tutorial/network.png "Serveur proxy")

   a. Cochez la case **Utiliser un serveur proxy pour le réseau local**.

   b. Dans la zone **Adresse**, entrez **gateway.Zscaler Beta.net**.

   c. Dans la zone **Port**, entrez **80**.

   d. Cochez la case **Ne pas utiliser de serveur proxy pour les adresses locales**.

   e. Sélectionnez **OK** pour fermer la boîte de dialogue **Paramètres du réseau local**.

6. Sélectionnez **OK** pour fermer la boîte de dialogue **Options Internet**.

### <a name="create-zscaler-beta-test-user"></a>Créer un utilisateur de test Zscaler Beta

Dans cette section, l’utilisateur Britta Simon est créé dans Zscaler Beta. Zscaler Beta prend en charge l’**attribution d’utilisateurs juste-à-temps**, option activée par défaut. Vous n’avez rien à faire dans cette section. S’il n’existe pas encore d’utilisateur dans Zscaler Beta, il en est créé un après l’authentification.

> [!Note]
> Pour créer un utilisateur manuellement, contactez l’[équipe de support technique Zscaler Beta](https://www.zscaler.com/company/contact).

> [!NOTE]
> Zscaler Beta prend aussi en charge l’attribution automatique d’utilisateurs. Vous trouverez [ici](./zscaler-beta-provisioning-tutorial.md) plus d’informations sur la façon de configurer l’attribution automatique d’utilisateurs.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes.

* Cliquez sur **Tester cette application** dans le portail Azure. Cette opération effectue une redirection vers l’URL de connexion à Zscaler Beta, où vous pouvez lancer le flux de connexion.

* Accédez directement à l’URL de connexion à Zscaler Beta, puis lancez le flux de connexion à partir de cet emplacement.

* Vous pouvez utiliser Mes applications de Microsoft. Quand vous cliquez sur la vignette Zscaler Beta dans Mes applications, une redirection est effectuée vers l’URL de connexion à Zscaler Beta. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez configuré Zscaler Beta, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
