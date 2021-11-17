---
title: 'Tutoriel : Intégration d’Azure Active Directory à ArcGIS Enterprise | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et ArcGIS Enterprise.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/11/2021
ms.author: jeedes
ms.openlocfilehash: 9fdb22f31f09fd334e2ebfa6e7253e47fc5bdfda
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132288771"
---
# <a name="tutorial-azure-active-directory-integration-with-arcgis-enterprise"></a>Tutoriel : Intégration d’Azure Active Directory à ArcGIS Enterprise

Dans ce tutoriel, vous allez découvrir comment intégrer ArcGIS Enterprise à Azure Active Directory (Azure AD). Quand vous intégrez ArcGIS Enterprise à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à ArcGIS Enterprise.
* Permettre à vos utilisateurs de se connecter automatiquement à ArcGIS Enterprise avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement à ArcGIS Enterprise pour lequel l’authentification unique est activée

> [!NOTE]
> Cette intégration peut également être utilisée à partir de l’environnement cloud US Government Azure AD. Cette application est disponible dans la Galerie d’applications cloud US Government Azure AD et peut être configurée de la même façon que dans le cloud public.

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* ArcGIS Enterprise prend en charge l’authentification unique lancée par **le fournisseur de services et le fournisseur d’identité**.
* ArcGIS Enterprise prend en charge le provisionnement d’utilisateurs **Juste-à-temps**.

## <a name="add-arcgis-enterprise-from-the-gallery"></a>Ajouter ArcGIS Enterprise à partir de la galerie

Pour configurer l’intégration d’ArcGIS Enterprise à Azure AD, vous devez ajouter ArcGIS Enterprise à votre liste d’applications SaaS gérées, à partir de la galerie.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **ArcGIS Enterprise** dans la zone de recherche.
1. Sélectionnez **ArcGIS Enterprise** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-arcgis-enterprise"></a>Configurer et tester l’authentification unique Azure AD pour ArcGIS Enterprise

Configurez et testez l’authentification unique Azure AD avec ArcGIS Enterprise à l’aide d’un utilisateur de test nommé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir une relation entre un utilisateur Azure AD et l’utilisateur ArcGIS Enterprise associé.

Pour configurer et tester l’authentification unique Azure AD avec ArcGIS Enterprise, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique ArcGIS Enterprise](#configure-arcgis-enterprise-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test ArcGIS Enterprise](#create-arcgis-enterprise-test-user)** pour obtenir un équivalent de B.Simon dans ArcGIS Enterprise, lié à sa représentation dans Azure AD.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, dans la page d’intégration de l’application **ArcGIS Enterprise**, recherchez la section **Gérer**, puis sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, suivez les étapes ci-dessous si vous souhaitez configurer l’application en mode Initié par le **fournisseur d’identité** :

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `<EXTERNAL_DNS_NAME>.portal`

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://<EXTERNAL_DNS_NAME>/portal/sharing/rest/oauth2/saml/signin2`

    c. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<EXTERNAL_DNS_NAME>/portal/sharing/rest/oauth2/saml/signin`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL de réponse et l’URL de connexion réels. Pour obtenir ces valeurs, contactez [l’équipe du support technique ArcGIS Enterprise](mailto:support@esri.com). La valeur de l’identificateur peut être obtenue dans la section **Set Identity Provider** (Configurer le fournisseur d’identité), ce qui est expliqué plus loin dans ce tutoriel.

5. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur le bouton Copier pour copier l’**URL des métadonnées de fédération d’application**, puis enregistrez-la sur votre ordinateur.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à ArcGIS Enterprise.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **ArcGIS Enterprise**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-arcgis-enterprise-sso"></a>Configurer l’authentification unique ArcGIS Enterprise

1. Pour automatiser la configuration dans ArcGIS Enterprise, vous devez installer l’**extension de navigateur de connexion sécurisée à Mes applications** en cliquant sur **Installer l’extension**.

    ![Extension My apps](common/install-myappssecure-extension.png)

1. Une fois l’extension ajoutée au navigateur, le fait de cliquer sur **Configurer ArcGIS Enterprise** vous redirige vers l’application ArcGIS Enterprise. À partir de là, indiquez les informations d’identification de l’administrateur pour vous connecter à ArcGIS Enterprise. Cette extension de navigateur configure automatiquement l’application pour vous et automatise les étapes 3 à 7.

    ![Configuration](common/setup-sso.png)

1. Si vous voulez configurer ArcGIS Enterprise manuellement, connectez-vous à votre site d’entreprise ArcGIS Enterprise en tant qu’administrateur.


1. Sélectionnez **Organization > EDIT SETTINGS** (Organisation > Modifier les paramètres).

    ![Capture d’écran montrant l’onglet ArcGIS Enterprise Organization avec Edit settings sélectionné.](./media/arcgisenterprise-tutorial/configure-1.png)

1. Sélectionnez l’onglet **Sécurité**.

    ![Capture d’écran montrant l’onglet Security sélectionné.](./media/arcgisenterprise-tutorial/configure-2.png)

1. Faites défiler jusqu’à la section **Enterprise Logins via SAML** (Connexions d’entreprise via SAML), puis sélectionnez **SET ENTERPRISE LOGIN** (Définir les connexions d’entreprise).

    ![Capture d’écran montrant Enterprise Logins via SAML, où vous pouvez sélectionner Set Enterprise Login.](./media/arcgisenterprise-tutorial/configure-3.png)

1. Dans la section **Set Identity Provider** (Configurer le fournisseur d’identité), effectuez les étapes suivantes :

    ![Capture d’écran montrant Set Identity Provider, où vous effectuez les étapes décrites ici.](./media/arcgisenterprise-tutorial/configure-4.png)

    a. Fournissez un nom tel que **Azure Active Directory Test** dans la zone de texte **Name**.

    b. Dans la zone de texte **URL**, collez la valeur du champ **URL des métadonnées de fédération d’application** du portail Azure.

    c. Cliquez sur **Show advanced settings** (Afficher les paramètres avancés). Copiez la valeur qui se trouve sous **Entity ID** (ID d’entité), puis collez-la dans la zone de texte **Identificateur** de la section **Domaines et URL ArcGIS Enterprise** du portail Azure.

    ![Capture d’écran montrant où se procurer l’ID d’entité et mettre à jour le fournisseur d’identité.](./media/arcgisenterprise-tutorial/configure-5.png)

    d. Cliquez sur **UPDATE IDENTITY PROVIDER** (Mettre à jour le fournisseur d’identité).

### <a name="create-arcgis-enterprise-test-user"></a>Créer un utilisateur de test ArcGIS Enterprise

Dans cette section, un utilisateur appelé Britta Simon est créé dans ArcGIS Enterprise. ArcGIS Enterprise prend en charge l’attribution d’utilisateurs juste-à-temps, option activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas encore d’utilisateur dans ArcGIS Enterprise, il en est créé un après l’authentification.

> [!Note]
> Si vous devez créer un utilisateur manuellement, contactez [l’équipe du support technique ArcGIS Enterprise](mailto:support@esri.com).

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

#### <a name="sp-initiated"></a>Lancée par le fournisseur de services :

* Cliquez sur **Tester cette application** dans le portail Azure. Cette opération redirige vers l’URL de connexion à ArcGIS Enterprise, où vous pouvez lancer le flux de connexion.  

* Accédez directement à l’URL de connexion à ArcGIS Enterprise pour lancer le flux de connexion.

#### <a name="idp-initiated"></a>Lancée par le fournisseur d’identité :

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors connecté automatiquement à l’instance de ArcGIS Enterprise pour laquelle vous avez configuré l’authentification unique. 

Vous pouvez aussi utiliser Mes applications de Microsoft pour tester l’application dans n’importe quel mode. Si, quand vous cliquez sur la vignette ArcGIS Enterprise dans Mes applications, le mode Fournisseur de services est configuré, vous êtes redirigé vers la page de connexion de l’application pour lancer le flux de connexion ; s’il s’agit du mode Fournisseur d’identité, vous êtes automatiquement connecté à l’instance d’ArcGIS Enterprise pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez configuré ArcGIS Enterprise, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
