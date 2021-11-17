---
title: 'Didacticiel : Intégration d’Azure Active Directory avec Zscaler ZSCloud | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Zscaler ZSCloud.
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
ms.openlocfilehash: a17068b0e0d4ff5193ac7e9982c8d47a4bd99908
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132348651"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-zscloud"></a>Didacticiel : Intégration d’Azure Active Directory avec Zscaler ZSCloud

L’objectif de ce tutoriel est de montrer comment intégrer Zscaler ZSCloud dans Azure Active Directory (Azure AD). Quand vous intégrez Zscaler ZSCloud à Azure AD, vous pouvez :

* Contrôler qui a accès à Zscaler ZSCloud dans Azure AD.
* Permettre à vos utilisateurs de se connecter automatiquement à Zscaler ZSCloud avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD avec Zscaler ZSCloud, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un [compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Zscaler ZSCloud pour lequel l’authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Zscaler ZSCloud prend en charge l’authentification unique lancée par le **fournisseur de services**.

* Zscaler ZSCloud prend en charge l’attribution d’utilisateurs **juste-à-temps**.

* Zscaler ZSCloud prend en charge l’[attribution automatisée d’utilisateurs](zscaler-zscloud-provisioning-tutorial.md).

## <a name="adding-zscaler-zscloud-from-the-gallery"></a>Ajouter Zscaler ZSCloud à partir de la galerie

Pour configurer l’intégration de Zscaler ZSCloud avec Azure AD, vous devez ajouter Zscaler ZSCloud, disponible dans la galerie, à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Zscaler ZSCloud** dans la zone de recherche.
1. Sélectionnez **Zscaler ZSCloud** dans le volet des résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-zscaler-zscloud"></a>Configurer et tester l’authentification unique (SSO) Azure AD pour Zscaler ZSCloud

Configurez et testez l’authentification unique Azure AD auprès de Zscaler ZSCloud à l’aide d’un utilisateur de test nommé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Zscaler ZSCloud associé.

Pour configurer et tester l’authentification unique Azure AD auprès de Zscaler ZSCloud, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
   1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
   1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Zscaler ZSCloud](#configure-zscaler-zscloud-sso)** pour configurer les paramètres de l’authentification unique côté application.
   1. **[Créer un utilisateur de test Zscaler ZSCloud](#create-zscaler-zscloud-test-user)** pour avoir un équivalent de B.Simon dans Zscaler ZSCloud qui est lié à la représentation de l’utilisateur Azure AD.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **Zscaler ZSCloud**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, entrez les valeurs pour les champs suivants :

   Dans la zone de texte **URL de connexion**, entrez l’URL utilisée par vos utilisateurs pour se connecter à votre application Zscaler ZSCloud.

   > [!NOTE]
   > Vous devez remplacer la valeur par l’URL de connexion. Pour obtenir cette valeur, contactez l’[équipe du support technique Zscaler ZSCloud](https://help.zscaler.com/). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. Votre application Zscaler ZSCloud s’attend à recevoir les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à votre configuration d’attributs du jeton SAML. La capture d’écran suivante montre la liste des attributs par défaut. Cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Attributs d’utilisateur**.

   ![Capture d’écran montrant User Attributes avec l’icône Edit sélectionnée.](common/edit-attribute.png)

1. En plus de ce qui précède, l’application Zscaler ZSCloud s’attend à ce que quelques attributs supplémentaires soient repassés dans la réponse SAML. Dans la section **Revendications des utilisateurs** de la boîte de dialogue **Attributs utilisateur**, effectuez les étapes suivantes pour ajouter le jeton SAML comme indiqué dans le tableau ci-dessous :

   | Name     | Attribut source   |
   | -------- | ------------------ |
   | memberOf | user.assignedroles |

   a. Cliquez sur le bouton **Ajouter une nouvelle revendication** pour ouvrir la boîte de dialogue **Gérer les revendications des utilisateurs**.

   ![Capture d’écran montrant User claims avec l’option permettant d’ajouter une nouvelle revendication.](common/new-save-attribute.png)

   ![Capture d’écran montrant la boîte de dialogue Manage user claims où vous pouvez entrer les valeurs décrites.](common/new-attribute-details.png)

   b. Dans la zone de texte **Attribut**, indiquez le nom d’attribut pour cette ligne.

   c. Laissez le champ **Espace de noms** vide.

   d. Sélectionnez Source comme **Attribut**.

   e. Dans la liste **Attribut de la source**, tapez la valeur d’attribut indiquée pour cette ligne.

   f. Cliquez sur **Enregistrer**.

   > [!NOTE]
   > Cliquez [ici](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui) pour savoir comment configurer un rôle dans Azure AD.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (Base64)** en fonction des options définies par rapport à vos besoins, puis enregistrez-le sur votre ordinateur.

   ![Lien Téléchargement de certificat](common/certificatebase64.png)

1. Dans la section **Configurer Zscaler ZSCloud**, copiez la ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Zscaler ZSCloud.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, **Toutes les applications**, puis **Zscaler ZSCloud**.
2. Dans la liste des applications, sélectionnez **Zscaler ZSCloud**.
3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.
4. Cliquez sur le bouton **Ajouter un utilisateur**, puis sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez l’utilisateur tel que **Britta Simon** dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

   ![Capture d’écran montrant la boîte de dialogue Utilisateurs et groupes où vous pouvez sélectionner un utilisateur.](./media/zscaler-zscloud-tutorial/users.png)

6. Dans la boîte de dialogue **Sélectionner un rôle**, choisissez le rôle d’utilisateur approprié dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

   ![Capture d’écran montrant la boîte de dialogue Sélectionner un rôle où vous pouvez sélectionner un rôle d’utilisateur.](./media/zscaler-zscloud-tutorial/roles.png)

7. Dans la boîte de dialogue **Ajouter une attribution**, sélectionnez le bouton **Attribuer**.

   ![Capture d’écran montrant la boîte de dialogue Add Assignment dans laquelle vous pouvez sélectionner Assign.](./media/zscaler-zscloud-tutorial/assignment.png)

   > [!NOTE]
   > Le rôle d’accès par défaut n’est pas pris en charge, car il empêcherait le provisionnement. Il ne peut donc pas être sélectionné lors de l’affectation d’utilisateurs.

## <a name="configure-zscaler-zscloud-sso"></a>Configurer l’authentification unique de Zscaler ZSCloud

1. Pour automatiser la configuration dans Zscaler ZSCloud, vous devez installer l’**extension de navigateur My Apps Secure Sign-in** en cliquant sur **Installer l’extension**.

   ![Extension My apps](common/install-myappssecure-extension.png)

2. Après l’ajout de l’extension au navigateur, cliquez sur **Configurer Zscaler ZSCloud** pour être dirigé vers l’application Zscaler ZSCloud. À partir de là, indiquez les informations d’identification de l’administrateur pour vous connecter à Zscaler ZSCloud. Cette extension de navigateur configure automatiquement l’application et automatise les étapes 3 à 6.

   ![Configurer l’authentification unique](common/setup-sso.png)

3. Si vous souhaitez configurer manuellement Zscaler ZSCloud, ouvrez une nouvelle fenêtre de navigateur web, connectez-vous à votre site d’entreprise Zscaler ZSCloud en tant qu’administrateur, puis effectuez les étapes suivantes :

4. Sélectionnez **Administration > Authentification > Paramètres d'authentification** et procédez comme suit :

   ![Capture d’écran montrant le site Zscaler avec les étapes décrites.](./media/zscaler-zscloud-tutorial/setting.png "Administration")

   a. Sous Type d'authentification, choisissez **SAML**.

   b. Cliquez sur **Configure SAML**.

5. Dans la fenêtre **Modifier les paramètres SAML**, procédez comme suit, puis cliquez sur Enregistrer.  

   ![Gérer les utilisateurs et l’authentification](./media/zscaler-zscloud-tutorial/attributes.png "Manage Users & Authentication")

   a. Dans la zone de texte **URL du portail SAML**, collez l'**URL de connexion** que vous avez copiée sur le portail Azure.

   b. Dans la zone de texte **Attribut de l'ID de connexion**, entrez **NameID**.

   c. Cliquez sur **Charger** pour charger le certificat de signature SAML Azure que vous avez téléchargé depuis le portail Azure dans le **Certificat SSL public**.

   d. Activez/désactivez **Activer l'approvisionnement automatique SAML**.

   e. Dans la zone de texte **Attribut du nom d'affichage de l'utilisateur**, entrez **displayName** si vous souhaitez activer l'approvisionnement automatique SAML pour les attributs displayName.

   f. Dans la zone de texte **Attribut du nom de groupe**, entrez **memberOf** si vous souhaitez activer l'approvisionnement automatique SAML pour les attributs memberOf.

   g. Dans **Attribut du nom du service**, entrez **department** si vous souhaitez activer l'approvisionnement automatique SAML pour les attributs department.

   h. Cliquez sur **Enregistrer**.

6. Dans la page **Configure User Authentication** , procédez comme suit :

   ![Capture d’écran montrant la boîte de dialogue Configure User Authentication avec l’option Activate sélectionnée.](./media/zscaler-zscloud-tutorial/active.png)

   a. Passez la souris sur le menu **Activation** en bas à gauche.

   b. Cliquez sur **Activer**.

## <a name="configuring-proxy-settings"></a>Configuration des paramètres de proxy

### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Pour configurer les paramètres de proxy dans Internet Explorer

1. Démarrez **Internet Explorer**.

2. Pour ouvrir la boîte de dialogue **Options Internet**, sélectionnez **Options Internet** dans le menu **Outils**.

   ![Options Internet](./media/zscaler-zscloud-tutorial/network.png "Options Internet")

3. Cliquez sur l’onglet **Connexions** .

   ![Connexions](./media/zscaler-zscloud-tutorial/server.png "Connexions")

4. Cliquez sur **Paramètres réseau** pour ouvrir la boîte de dialogue **Paramètres réseau**.

5. Dans la section Serveur proxy, procédez comme suit :

   ![Serveur proxy](./media/zscaler-zscloud-tutorial/internet-options.png "Serveur proxy")

   a. Sélectionnez **Utiliser un serveur proxy pour le réseau local**.

   b. Dans la zone de texte Adresse, tapez **gateway.Zscaler ZSCloud.net**.

   c. Dans la zone de texte Port, tapez **80**.

   d. Sélectionnez **Ne pas utiliser de serveur proxy pour les adresses locales**.

   e. Cliquez sur **OK** pour fermer la boîte de dialogue **Paramètres du réseau local**.

6. Cliquez sur **OK** pour fermer la boîte de dialogue **Options Internet**.

### <a name="create-zscaler-zscloud-test-user"></a>Créer un utilisateur de test Zscaler ZSCloud

Dans cette section, un utilisateur appelé Britta Simon est créé dans Zscaler ZSCloud. Zscaler ZSCloud prend en charge l’attribution d’utilisateurs juste-à-temps, qui est activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas encore d’utilisateurs dans Zscaler ZSCloud, un utilisateur est créé après l’authentification.

> [!Note]
> Si vous avez besoin de créer un utilisateur manuellement, contactez l’[équipe du support technique Zscaler ZSCloud](https://help.zscaler.com/).

> [!NOTE]
> Zscaler ZSCloud prend également en charge l’attribution automatique d’utilisateurs. Des informations supplémentaires sur la configuration de cette fonctionnalité sont disponibles [ici](./zscaler-zscloud-provisioning-tutorial.md).

### <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes.

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion à Zscaler ZSCloud, où vous pouvez lancer le flux de connexion.

* Accédez directement à l’URL de connexion à Zscaler ZSCloud pour y lancer le flux de connexion.

* Vous pouvez utiliser Mes applications de Microsoft. Lorsque vous cliquez sur la vignette Zscaler ZSCloud dans Mes applications, vous êtes redirigé vers l’URL de connexion à Zscaler ZSCloud. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré Zscaler ZSCloud, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
