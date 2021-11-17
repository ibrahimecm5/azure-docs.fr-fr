---
title: 'Didacticiel : intégration d’Azure Active Directory à Andromeda | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Andromeda.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/12/2021
ms.author: jeedes
ms.openlocfilehash: e15c0cf41010fb5336dcc49ebba4ea43c7c91b7b
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132289411"
---
# <a name="tutorial-azure-active-directory-integration-with-andromeda"></a>Didacticiel : intégration d’Azure Active Directory à Andromeda

Dans ce didacticiel, vous apprenez à intégrer Andromeda à Azure Active Directory (Azure AD). Quand vous intégrez Andromeda à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Andromeda.
* Permettre à vos utilisateurs de se connecter automatiquement à Andromeda avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à Andromeda, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un [compte gratuit](https://azure.microsoft.com/free/).
* Abonnement Andromeda pour lequel l’authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Andromeda prend en charge l’authentification unique lancée par le **fournisseur de services et le fournisseur d’identité**.
* Andromeda prend en charge le provisionnement d’utilisateurs **juste-à-temps**.

## <a name="add-andromeda-from-the-gallery"></a>Ajouter Andromeda à partir de la galerie

Pour configurer l’intégration d’Andromeda à Azure AD, vous devez ajouter Andromeda, disponible dans la galerie, à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Andromeda** dans la zone de recherche.
1. Sélectionnez **Andromeda** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-andromeda"></a>Configurer et tester l’authentification unique Azure AD pour Andromeda

Configurez et testez l’authentification unique Azure AD auprès d’Andromeda pour un utilisateur de test nommé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Andromeda associé.

Pour configurer et tester l’authentification unique Azure AD auprès d’Andromeda, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
   1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
   1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
2. **[Configurer l’authentification unique (SSO) Andromeda](#configure-andromeda-sso)** pour configurer les paramètres de l’authentification unique côté application.
   1. **[Créer un utilisateur de test Andromeda](#create-andromeda-test-user)** pour avoir un équivalent de Britta Simon dans Andromeda, associé à sa représentation dans Azure AD.
3. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **Andromeda**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, si vous souhaitez configurer l’application en mode lancé par le **fournisseur d’identité**, effectuez les étapes suivantes : 

   a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<tenantURL>.ngcxpress.com/`

   b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://<tenantURL>.ngcxpress.com/SAMLConsumer.aspx`

1. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

   Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<tenantURL>.ngcxpress.com/SAMLLogon.aspx`.

   > [!NOTE]
   > Il ne s’agit pas de valeurs réelles. Vous mettrez à jour la valeur avec l’identificateur, l’URL de réponse et l’URL de connexion réels. La procédure est expliquée plus loin dans le didacticiel.

1. L’application Andromeda attend les assertions SAML dans un format spécifique. Configurez les revendications suivantes pour cette application. Vous pouvez gérer les valeurs de ces attributs à partir de la section **Attributs utilisateur** sur la page d’intégration des applications. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur le bouton **Modifier** pour ouvrir la boîte de dialogue **Attributs utilisateur**.

   ![Capture d’écran montrant les attributs utilisateur.](common/edit-attribute.png)

   > [!NOTE]
   > Effacez les définitions NameSpace lorsque vous configurez celles-ci.

1. Dans la section **Revendications des utilisateurs** de la boîte de dialogue **Attributs utilisateur**, modifiez les revendications en utilisant l’icône **Modifier** ou ajoutez des revendications en utilisant l’option **Ajouter une nouvelle revendication** pour configurer l’attribut de jeton SAML comme sur l’image ci-dessus et procédez comme suit :

   | Name    | Attribut source  |
   | ------- | ----------------- |
   | rôle    | Rôle spécifique aux applications |
   | type    | Type d'application          |
   | société | CompanyName       |

   > [!NOTE]
   > Andromeda attend des rôles pour les utilisateurs affectés à l’application. Veuillez configurer ces rôles dans Azure AD pour pouvoir affecter les rôles appropriés aux utilisateurs. Pour comprendre comment configurer des rôles dans Azure AD, consultez [cette page](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui).

   a. Cliquez sur le bouton **Ajouter une nouvelle revendication** pour ouvrir la boîte de dialogue **Gérer les revendications des utilisateurs**.

   ![Capture d’écran montrant Revendications des utilisateurs avec des options permettant d’ajouter une nouvelle revendication et d’enregistrer.](common/new-save-attribute.png)

   ![Capture d’écran montrant Gérer les revendications des utilisateurs, où vous pouvez entrer les valeurs décrites à cette étape.](common/new-attribute-details.png)

   b. Dans la zone de texte **Attribut**, indiquez le nom d’attribut pour cette ligne.

   c. Laissez le champ **Espace de noms** vide.

   d. Sélectionnez Source comme **Attribut**.

   e. Dans la liste **Attribut de la source**, tapez la valeur d’attribut indiquée pour cette ligne.

   f. Cliquez sur **OK**

   g. Cliquez sur **Enregistrer**.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (Base64)** en fonction des options définies par rapport à vos besoins, puis enregistrez-le sur votre ordinateur.

   ![Lien Téléchargement de certificat](common/certificatebase64.png)

1. Dans la section **Configurer Andromeda**, copiez la ou les URL appropriées, selon vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Andromeda.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Andromeda**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous avez configuré les rôles comme indiqué ci-dessus, vous pouvez en sélectionner un dans la liste déroulante **Sélectionner un rôle**.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

### <a name="configure-andromeda-sso"></a>Configurer l’authentification unique Andromeda

1. Connectez-vous à votre site d’entreprise Andromeda en tant qu’administrateur.

2. En haut de la barre de menus, cliquez sur **Admin** et accédez à **Administration**.

   ![Administrateur Andromeda.](./media/andromedascm-tutorial/admin.png)

3. Sur le côté gauche de la barre d’outils, dans la section **Interfaces**, cliquez sur **SAML Configuration** (Configuration SAML).

   ![SAML Andromeda.](./media/andromedascm-tutorial/interface.png)

4. Dans la section **SAML Configuration** (Configuration SAML), procédez comme suit :

   ![Configuration d’Andromeda.](./media/andromedascm-tutorial/configure.png)

   a. Cochez **Enable SSO with SAML** (Activer l’authentification unique avec SAML).

   b. Dans la section **Informations Andromeda**, copiez la valeur **Identité SP** et collez-la dans la zone de texte **Identificateur** de la section **Configuration SAML de base**.

   c. Copiez la valeur **URL du consommateur** et collez-la dans la zone de texte **URL de réponse** de la section **Configuration SAML de base**.

   d. Copiez la valeur **URL d’ouverture de session** et collez-la dans la zone de texte **URL de connexion** de la section **Configuration SAML de base**.

   e. Dans la section **Fournisseur d’identité SAML**, tapez votre nom IDP.

   f. Dans la zone de texte **Point de terminaison du service d’authentification unique**, collez la valeur de l’**URL de connexion** que vous avez copiée à partir du portail Azure.

   g. Ouvrez le **certificat codé en base 64** téléchargé à partir du portail Azure dans le bloc-notes et collez-le dans la zone de texte **Certificat X.509**.

   h. Mappez les attributs suivants avec la valeur correspondante pour faciliter la connexion avec authentification unique à partir d’Azure AD. L’attribut **ID utilisateur** est requis pour la connexion. Pour l’approvisionnement, **E-mail**, **Société**, **Type d’utilisateur** et **Rôle** sont requis. Dans cette section, nous définissons le mappage des attributs (nom et valeurs) qui correspondent à ceux définis dans le portail Azure.

   ![Attributs Andromeda.](./media/andromedascm-tutorial/mapping.png)

   i. Cliquez sur **Enregistrer**.

### <a name="create-andromeda-test-user"></a>Créer un utilisateur de test Andromeda

Dans cette section, un utilisateur appelé Britta Simon est créé dans Andromeda. Andromeda prend en charge le provisionnement d’utilisateurs juste-à-temps, option activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas encore d’utilisateur dans Andromeda, il en est créé un après l’authentification. Si vous devez créer un utilisateur manuellement, contactez [l’équipe d’assistance client Andromeda](https://www.ngcsoftware.com/support/).

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes.

#### <a name="sp-initiated"></a>Lancée par le fournisseur de services :

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion à Andromeda, où vous pouvez lancer le flux de connexion.

* Accédez directement à l’URL de connexion à Andromeda pour y lancer le flux de connexion.

#### <a name="idp-initiated"></a>Lancée par le fournisseur d’identité :

* Cliquez sur **Tester cette application** dans le portail Azure ; vous devez être connecté automatiquement à l’instance d’Andromeda pour laquelle vous avez configuré l’authentification unique.

* Vous pouvez aussi utiliser Mes applications de Microsoft pour tester l’application dans n’importe quel mode. Si, quand vous cliquez sur la vignette Andromeda dans Mes applications, le mode Fournisseur de services est configuré, vous êtes redirigé vers la page de connexion de l’application pour lancer le flux de connexion ; s’il s’agit du mode Fournisseur d’identité, vous êtes automatiquement connecté à l’instance d’Andromeda pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré Andromeda, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
