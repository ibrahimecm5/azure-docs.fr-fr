---
title: 'Tutoriel : Intégration de l’authentification unique Azure Active Directory à Zscaler | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Zscaler.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/18/2020
ms.author: jeedes
ms.openlocfilehash: 428fde009f0b932d6dbc80e4cbcd986a7aff6aec
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132319887"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-zscaler"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory à Zscaler

Dans ce tutoriel, vous allez découvrir comment intégrer Zscaler à Azure Active Directory (Azure AD). Quand vous intégrez Zscaler à Azure AD, vous pouvez :

- Contrôler dans Azure AD qui a accès à Zscaler.
- Permettre à vos utilisateurs de se connecter automatiquement à Zscaler avec leur compte Azure AD.
- Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

- Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
- Un abonnement Zscaler pour lequel l’authentification unique (SSO) est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

- Zscaler prend en charge l’authentification unique (SSO) initiée par le **SP**
- Zscaler prend en charge l’approvisionnement des utilisateurs **juste-à-temps**

## <a name="adding-zscaler-from-the-gallery"></a>Ajouter Zscaler à partir de la galerie

Pour configurer l’intégration de Zscaler à Azure AD, vous devez ajouter Zscaler, disponible dans la galerie, à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Zscaler** dans la zone de recherche.
1. Sélectionnez **Zscaler** dans le volet des résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-zscaler"></a>Configurer et tester l’authentification SSO Azure AD pour Zscaler

Configurez et testez l’authentification unique Azure AD avec Zscaler à l’aide d’un utilisateur de test nommé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Zscaler associé.

Pour configurer et tester l’authentification SSO Azure AD avec Zscaler, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
   1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
   1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Zscaler](#configure-zscaler-sso)** pour configurer les paramètres de l’authentification unique côté application.
   1. **[Créer un utilisateur de test Zscaler](#create-zscaler-test-user)** pour avoir un équivalent de B.Simon dans Zscaler lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, dans la page d’intégration de l’application **Zscaler**, recherchez la section **Gérer**, puis sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon pour **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, entrez les valeurs pour les champs suivants :

   Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<companyname>.zscaler.net`.

   > [!NOTE]
   > Cette valeur n’est pas la valeur réelle. Mettez à jour la valeur avec l’URL de connexion réelle. Contactez [l’équipe du support Zscaler](https://www.zscaler.com/company/contact) pour obtenir cette valeur. Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. Votre application Zscaler s’attend à recevoir les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à votre configuration d’attributs du jeton SAML. La capture d’écran suivante montre la liste des attributs par défaut. Cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Attributs d’utilisateur**.

   ![Capture d’écran montrant User Attributes avec l’icône Edit sélectionnée.](common/edit-attribute.png)

1. En plus, l’application Zscaler s’attend à ce que quelques attributs supplémentaires passent une nouvelle fois dans la réponse SAML. Dans la section **Revendications des utilisateurs** de la boîte de dialogue **Attributs utilisateur**, effectuez les étapes suivantes pour ajouter le jeton SAML comme indiqué dans le tableau ci-dessous :

   | Name     | Attribut source   |
   | -------- | ------------------ |
   | memberOf | user.assignedroles |

   a. Cliquez sur le bouton **Ajouter une nouvelle revendication** pour ouvrir la boîte de dialogue **Gérer les revendications des utilisateurs**.

   b. Dans la zone de texte **Attribut**, indiquez le nom d’attribut pour cette ligne.

   c. Laissez le champ **Espace de noms** vide.

   d. Sélectionnez Source comme **Attribut**.

   e. Dans la liste **Attribut de la source**, tapez la valeur d’attribut indiquée pour cette ligne.

   f. Cliquez sur **Enregistrer**.

   > [!NOTE]
   > Cliquez [ici](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui) pour savoir comment configurer un rôle dans Azure AD.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **Certificat (Base64)** , puis sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

   ![Lien Téléchargement de certificat](common/certificatebase64.png)

1. Dans la section **Configurer Zscaler**, copiez la ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez permettre à B.Simon d’utiliser l’authentification unique Azure en lui octroyant l’accès à Zscaler.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Zscaler**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous avez configuré les rôles comme indiqué ci-dessus, vous pouvez en sélectionner un dans la liste déroulante **Sélectionner un rôle**.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-zscaler-sso"></a>Configurer l’authentification unique Zscaler

1. Pour automatiser la configuration dans Zscaler, vous devez installer l’**extension de navigateur My Apps Secure Sign-in** en cliquant sur **Installer l’extension**.

   ![Extension My apps](common/install-myappssecure-extension.png)

1. Après l’ajout de l’extension au navigateur, cliquez sur **Configurer Zscaler** pour être dirigé vers l’application Zscaler. À partir de là, indiquez les informations d’identification de l’administrateur pour vous connecter à Zscaler. Cette extension de navigateur configure automatiquement l’application et automatise les étapes 3 à 6.

   ![Configurer l’authentification unique](common/setup-sso.png)

1. Si vous souhaitez configurer manuellement Zscaler, ouvrez une nouvelle fenêtre de navigateur web, connectez-vous à votre site d’entreprise Zscaler en tant qu’administrateur et procédez comme suit :

1. Sélectionnez **Administration > Authentification > Paramètres d'authentification** et procédez comme suit :

   ![Capture d’écran montrant le site Zscaler One avec les étapes décrites.](./media/zscaler-tutorial/ic800206.png "Administration")

   a. Sous Type d'authentification, choisissez **SAML**.

   b. Cliquez sur **Configure SAML**.

1. Dans la fenêtre **Modifier les paramètres SAML**, procédez comme suit, puis cliquez sur Enregistrer.

   ![Gérer les utilisateurs et l’authentification](./media/zscaler-tutorial/ic800208.png "Manage Users & Authentication")

   a. Dans la zone de texte **URL du portail SAML**, collez l'**URL de connexion** que vous avez copiée sur le portail Azure.

   b. Dans la zone de texte **Attribut de l'ID de connexion**, entrez **NameID**.

   c. Cliquez sur **Charger** pour charger le certificat de signature SAML Azure que vous avez téléchargé depuis le portail Azure dans le **Certificat SSL public**.

   d. Activez/désactivez **Activer l'approvisionnement automatique SAML**.

   e. Dans la zone de texte **Attribut du nom d'affichage de l'utilisateur**, entrez **displayName** si vous souhaitez activer l'approvisionnement automatique SAML pour les attributs displayName.

   f. Dans la zone de texte **Attribut du nom de groupe**, entrez **memberOf** si vous souhaitez activer l'approvisionnement automatique SAML pour les attributs memberOf.

   g. Dans **Attribut du nom du service**, entrez **department** si vous souhaitez activer l'approvisionnement automatique SAML pour les attributs department.

   h. Cliquez sur **Enregistrer**.

1. Dans la page **Configure User Authentication** , procédez comme suit :

   ![Capture d’écran montrant la boîte de dialogue Configure User Authentication avec l’option Activate sélectionnée.](./media/zscaler-tutorial/ic800207.png)

   a. Passez la souris sur le menu **Activation** en bas à gauche.

   b. Cliquez sur **Activer**.

## <a name="configuring-proxy-settings"></a>Configuration des paramètres de proxy

### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Pour configurer les paramètres de proxy dans Internet Explorer

1. Démarrez **Internet Explorer**.

1. Pour ouvrir la boîte de dialogue **Options Internet**, sélectionnez **Options Internet** dans le menu **Outils**.

   ![Options Internet](./media/zscaler-tutorial/ic769492.png "Options Internet")

1. Cliquez sur l’onglet **Connexions** .

   ![Connexions](./media/zscaler-tutorial/ic769493.png "Connexions")

1. Cliquez sur **Paramètres réseau** pour ouvrir la boîte de dialogue **Paramètres réseau**.

1. Dans la section Serveur proxy, procédez comme suit :

   ![Serveur proxy](./media/zscaler-tutorial/ic769494.png "Serveur proxy")

   a. Sélectionnez **Utiliser un serveur proxy pour le réseau local**.

   b. Dans la zone de texte Adresse, tapez **gateway.zscaler.net**.

   c. Dans la zone de texte Port, tapez **80**.

   d. Sélectionnez **Ne pas utiliser de serveur proxy pour les adresses locales**.

   e. Cliquez sur **OK** pour fermer la boîte de dialogue **Paramètres du réseau local**.

1. Cliquez sur **OK** pour fermer la boîte de dialogue **Options Internet**.

### <a name="create-zscaler-test-user"></a>Créer un utilisateur de test Zscaler

Dans cette section, un utilisateur appelé Britta Simon est créé dans Zscaler. Zscaler prend en charge l’attribution d’utilisateurs juste-à-temps. Il s’agit d’une option activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas encore d’utilisateur dans Zscaler, il en est créé un après l’authentification.

> [!Note]
> Si vous devez créer un utilisateur manuellement, contactez l’[équipe de support Zscaler](https://www.zscaler.com/company/contact).

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes.

- Cliquez sur **Tester cette application** dans le portail Azure. Cette opération effectue une redirection vers l’URL de connexion à Zscaler, où vous pouvez lancer le flux de connexion.

- Accédez directement à l’URL de connexion à Zscaler, puis lancez le flux de connexion à partir de cet emplacement.

- Vous pouvez utiliser Mes applications de Microsoft. Quand vous cliquez sur la vignette Zscaler dans Mes applications, une redirection est effectuée vers l’URL de connexion à Zscaler. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez configuré Zscaler, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
