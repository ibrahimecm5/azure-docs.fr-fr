---
title: 'Tutoriel : Intégration de l’authentification unique (SSO) Azure Active Directory à Saba Cloud | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Saba Cloud.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/18/2021
ms.author: jeedes
ms.openlocfilehash: 0b993380287edd4528a26dd2d9925a2b66c54fd3
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132299726"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-saba-cloud"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory à Saba Cloud

Dans ce tutoriel, vous allez découvrir comment intégrer Azure Active Directory (Azure AD) à Saba Cloud. Quand vous intégrez Saba Cloud à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Saba Cloud.
* Autoriser les utilisateurs à se connecter automatiquement à Saba Cloud avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Saba Cloud pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* Saba Cloud prend en charge l’authentification unique lancée par le **fournisseur de services et le fournisseur d’identité**.
* Saba Cloud prend en charge l’attribution d’utilisateurs **juste-à-temps**.
* L’application Saba Cloud Mobile peut désormais être configurée avec Azure AD pour activer l’authentification unique. Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

## <a name="adding-saba-cloud-from-the-gallery"></a>Ajout de Saba Cloud depuis la galerie

Pour configurer l’intégration de Saba Cloud à Azure AD, vous devez ajouter Saba Cloud depuis la galerie à votre liste d’applications SaaS managées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Saba Cloud** dans la zone de recherche.
1. Sélectionnez **Saba Cloud** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-saba-cloud"></a>Configurer et tester l’authentification unique Azure AD pour Saba Cloud

Configurez et testez l’authentification unique Azure AD avec Saba Cloud pour un utilisateur de test nommé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Saba Cloud associé.

Pour configurer et tester l’authentification unique Azure AD avec Saba Cloud, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Saba Cloud](#configure-saba-cloud-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test Saba Cloud](#create-saba-cloud-test-user)** pour avoir un équivalent de B.Simon dans Saba Cloud, lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.
1. **[Tester l’authentification unique pour Saba Cloud (mobile)](#test-sso-for-saba-cloud-mobile)** afin de vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, dans la page d’intégration de l’application **Saba Cloud**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, si vous souhaitez configurer l’application en mode Initié par le **fournisseur d’identité**, entrez les valeurs pour les champs suivants :

    a. Dans la zone de texte **Identificateur**, tapez une URL en utilisant le modèle suivant (vous obtiendrez cette valeur dans la section Configurer l’authentification unique de Saba Cloud à l’étape 6, mais elle est généralement au format `<CUSTOMER_NAME>_sp`) : `<CUSTOMER_NAME>_sp`

    b. Dans la zone de texte **URL de réponse**, tapez une URL en utilisant le format suivant (ENTITY_ID fait référence à l’étape précédente, en général `<CUSTOMER_NAME>_sp`) : `https://<CUSTOMER_NAME>.sabacloud.com/Saba/saml/SSO/alias/<ENTITY_ID>`
    
    > [!NOTE]
    > Si vous spécifiez l’URL de réponse de façon incorrecte, vous devrez probablement la rectifier dans la section **Inscription d’application** d’Azure AD, et non pas dans la section **Application d’entreprise**. Apporter des modifications à la section **Configuration SAML de base** ne met pas toujours à jour l’URL de réponse.

1. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<CUSTOMER_NAME>.sabacloud.com`.

    b. Dans la zone de texte **État de relais**, tapez une URL au format suivant : `IDP_INIT---SAML_SSO_SITE=<SITE_ID> ` ou, si SAML est configuré pour un microsite, tapez une URL au format suivant : `IDP_INIT---SAML_SSO_SITE=<SITE_ID>---SAML_SSO_MICRO_SITE=<MicroSiteId>`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL de réponse, l’URL de connexion et l’État de relais exacts. Pour obtenir ces valeurs, contactez l’[équipe de support technique de Saba Cloud](mailto:support@saba.com). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.
    > 
    > Pour plus d’informations sur la configuration de RelayState, consultez [Authentification unique lancée par le fournisseur d’identité et le fournisseur de services pour un microsite](https://help.sabacloud.com/sabacloud/help-system/topics/help-system-idp-and-sp-initiated-sso-for-a-microsite.html).

1. Dans la section **Attributs et revendications de l’utilisateur**, ajustez l’identificateur d’utilisateur unique à ce que votre organisation prévoit d’utiliser comme nom d’utilisateur principal pour les utilisateurs de Saba.

   Cette étape est nécessaire seulement si vous tentez de convertir un nom d’utilisateur/mot de passe en authentification unique. S’il s’agit d’un nouveau déploiement de Saba Cloud qui n’a pas d’utilisateurs existants, vous pouvez ignorer cette étape.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **XML de métadonnées de fédération** et sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

1. Dans la section **Configurer Saba Cloud**, copiez la ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Saba Cloud.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Saba Cloud**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-saba-cloud-sso"></a>Configurer l’authentification unique Saba Cloud

1. Pour automatiser la configuration dans Saba Cloud, vous devez installer l’**extension de navigateur My Apps Secure Sign-in** en cliquant sur **Installer l’extension**.

    ![Extension My apps](common/install-myappssecure-extension.png)

1. Une fois l’extension ajoutée au navigateur, un clic sur **Configurer Saba Cloud** vous redirige vers l’application Saba Cloud. À partir de là, indiquez les informations d’identification de l’administrateur pour vous connecter à Saba Cloud. Cette extension de navigateur configure automatiquement l’application et automatise les étapes 3 à 9.

    ![Configuration](common/setup-sso.png)

1. Si vous souhaitez configurer Saba Cloud manuellement, dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Saba Cloud en tant qu’administrateur.

1. Cliquez sur l’icône **Menu**, cliquez sur **Admin**, puis sélectionnez l’onglet **System Admin** (Administrateur système).

    ![Capture d’écran mettant en évidence l’administration système](./media/saba-cloud-tutorial/system.png)

1. Dans **Configure System**, sélectionnez **SAML SSO Setup** (Configuration de l’authentification unique SAML), puis cliquez sur le bouton **SETUP SAML SSO** (Configurer l’authentification unique SAML). 

    ![Capture d’écran de la configuration](./media/saba-cloud-tutorial/configure.png)

1. Dans la fenêtre contextuelle, sélectionnez **Microsite** dans la liste déroulante, puis cliquez sur **ADD AND CONFIGURE** (Ajouter et configurer).

    ![Capture d’écran de l’ajout de site/microsite](./media/saba-cloud-tutorial/microsite.png)

1. Dans la section **Configure IDP**, cliquez sur **BROWSE** (Parcourir) pour charger le fichier **XML de métadonnées de fédération** que vous avez téléchargé à partir du portail Azure. Cochez la case **Site Specific IDP** (Fournisseur d’identité propre au site), puis cliquez sur **IMPORT**.

    ![Capture d’écran de l’importation de certificat](./media/saba-cloud-tutorial/certificate.png) 

1. Dans la section **Configure SP** (Configurer le fournisseur de services), copiez la valeur **Entity Alias** (Alias d’entité), puis collez-la dans la zone de texte **Identificateur (ID d’entité)** de la section **Configuration SAML de base** du portail Azure. Cliquez sur **GENERATE**.

    ![Capture d’écran de la configuration du fournisseur de services](./media/saba-cloud-tutorial/generate-metadata.png) 

1. Dans la section **Configure Properties** (Configurer les propriétés), vérifiez les champs remplis, puis cliquez sur **SAVE** (Enregistrer). 

    ![Capture d’écran de la configuration des propriétés](./media/saba-cloud-tutorial/configure-properties.png) 
    
    Vous devrez peut-être définir **Max Authentication Age (in seconds)** (Durée maximale de l’authentification (en secondes)) sur **7776000** (90 jours) pour qu’il corresponde à la durée maximale par défaut autorisée par Azure AD pour une connexion. Dans le cas contraire, vous êtes susceptible de recevoir l’erreur `(109) Login failed. Please contact system administrator.`.

### <a name="create-saba-cloud-test-user"></a>Créer un utilisateur de test Saba Cloud

Dans cette section, un utilisateur appelé Britta Simon est créé dans Saba Cloud. Saba Cloud prend en charge l’attribution d’utilisateurs juste-à-temps, option activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. Si l’utilisateur souhaité n’existe pas déjà dans Saba Cloud, il est créé après l’authentification.

> [!NOTE]
> Pour activer l’attribution juste-à-temps des utilisateurs SAML avec Saba Cloud, reportez-vous à [cette documentation](https://help.sabacloud.com/sabacloud/help-system/topics/help-system-user-provisioning-with-saml.html).

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

#### <a name="sp-initiated"></a>Lancée par le fournisseur de services :

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion à Saba Cloud, d’où vous pouvez lancer le flux de connexion.  

* Accédez directement à l’URL de connexion à Saba Cloud et lancez le flux de connexion.

#### <a name="idp-initiated"></a>Lancée par le fournisseur d’identité :

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors automatiquement connecté à l’instance de Saba Cloud pour laquelle vous avez configuré l’authentification unique. 

Vous pouvez aussi utiliser Mes applications de Microsoft pour tester l’application dans n’importe quel mode. Quand vous cliquez sur la vignette Saba Cloud dans Mes applications, si le mode Fournisseur de services est configuré, vous êtes redirigé vers la page de connexion de l’application pour lancer le flux de connexion ; si le mode Fournisseur d’identité est configuré, vous êtes automatiquement connecté à l’instance de Saba Cloud pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

> [!NOTE]
> Si l’URL de connexion n’est pas renseignée dans Azure AD, l’application est traitée comme étant en mode lancé par le fournisseur d’identité. Sinon, Azure AD redirige toujours l’utilisateur vers l’application Saba Cloud pour le flux lancé par le fournisseur de services.

## <a name="test-sso-for-saba-cloud-mobile"></a>Tester l’authentification unique pour Saba Cloud (mobile)

1. Ouvrez l’application Saba Cloud Mobile, indiquez le **nom du site** (Site Name) dans la zone de texte, puis cliquez sur **Enter**.

    ![Capture d’écran mettant en évidence la zone de texte Site Name](./media/saba-cloud-tutorial/site-name.png)

1. Entrez votre **adresse e-mail** et cliquez sur **Next** (Suivant).

    ![Capture d’écran montrant où entrer l’adresse e-mail](./media/saba-cloud-tutorial/email-address.png)

1. Une fois la connexion réussie, la page de l’application s’affiche.

    ![Capture d’écran indiquant que la connexion a réussi](./media/saba-cloud-tutorial/dashboard.png)

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez configuré Saba Cloud, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
