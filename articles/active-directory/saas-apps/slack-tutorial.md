---
title: 'Tutoriel : Intégration de l’authentification unique (SSO) Azure Active Directory à Slack | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Slack.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/28/2020
ms.author: jeedes
ms.openlocfilehash: 85e5c97ee903ee51b65d067f4167be84b6595abb
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132316998"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-slack"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory à Slack

Dans ce tutoriel, vous allez apprendre à intégrer Slack à Azure Active Directory (Azure AD). Lorsque vous intégrez Slack à Azure AD, vous pouvez :

* Contrôler qui a accès à Slack.
* Permettre à vos utilisateurs de se connecter automatiquement à Slack avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Abonnement Slack pour lequel l’authentification unique est activée

> [!NOTE]
> Si vous devez effectuer une intégration avec plusieurs instances de Slack dans un locataire, l’identificateur de chaque application peut être une variable.

> [!NOTE]
> Cette intégration peut également être utilisée à partir de l’environnement cloud US Government Azure AD. Cette application est disponible dans la Galerie d’applications cloud US Government Azure AD et peut être configurée de la même façon que dans le cloud public.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* Slack prend en charge l’authentification unique initiée par le **fournisseur de services**
* Slack prend en charge l’attribution d’utilisateurs **Juste-à-temps**
* Slack prend en charge [l’attribution d’utilisateurs **automatique**](./slack-provisioning-tutorial.md)

> [!NOTE]
> L’identificateur de cette application étant une valeur de chaîne fixe, une seule instance peut être configurée dans un locataire.

## <a name="adding-slack-from-the-gallery"></a>Ajout de Slack à partir de la galerie

Pour configurer l’intégration de Slack avec Azure AD, vous devez ajouter Slack à partir de la galerie, à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Slack** dans la zone de recherche.
1. Sélectionnez **Slack** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-slack"></a>Configurer et tester l’authentification unique Azure AD pour Slack

Configurez et testez l’authentification unique Azure AD avec Slack à l’aide d’un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Slack associé.

Pour configurer et tester l’authentification unique Azure AD avec Slack, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Slack](#configure-slack-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test Slack](#create-slack-test-user)** pour avoir un équivalent de B.Simon dans Slack, lié à la représentation Azure AD associée.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **Slack**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de modification/stylet de **Configuration SAML de base** pour modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, entrez les valeurs pour les champs suivants :

    a. Dans la zone de texte **URL de connexion**, saisissez une URL au format suivant : `https://<DOMAIN NAME>.slack.com/sso/saml/start`

    b. Dans la zone de texte **Identificateur (ID d’entité)** , tapez l’URL suivante : `https://slack.com`
    
    c. Dans **URL de réponse**, entrez l’un des modèles d’URL suivants :
    
    | URL de réponse|
    |----------|
    | `https://<DOMAIN NAME>.slack.com/sso/saml` |
    | `https://<DOMAIN NAME>.enterprise.slack.com/sso/saml` |

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Vous devez mettre à jour ces valeurs avec l’URL de connexion et l’URL de réponse réelles. Pour obtenir la valeur, contactez l’[équipe de support client Allbound SSO](https://slack.com/help/contact). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

    > [!NOTE]
    > La valeur d’**Identificateur (ID d’entité)** peut être une variable si vous devez intégrer plus d’une instance de Slack au locataire. Utilisez le modèle `https://<DOMAIN NAME>.slack.com`. Dans ce scénario, vous devez également effectuer un couplage avec un autre paramètre dans Slack en utilisant la même valeur.

1. L’application Slack attend les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à votre configuration d’attributs de jetons SAML. La capture d’écran suivante montre la liste des attributs par défaut.

    ![image](common/edit-attribute.png)

1. En plus de ce qui précède, l’application Slack s’attend à ce que quelques attributs supplémentaires (présentés ci-dessous) soient repassés dans la réponse SAML. Ces attributs sont également préremplis, mais vous pouvez les examiner pour voir s’ils répondent à vos besoins. Vous devez également ajouter l’attribut `email`. Si l’utilisateur n’a pas d’adresse e-mail, mappez **emailaddress** à **user.userprincipalname** et mappez **e-mail** à **user.userprincipalname**.

    | Nom | Attribut source |
    | -----|---------|
    | emailaddress | user.userprincipalname |
    | email | user.userprincipalname |

   > [!NOTE]
   > Pour définir la configuration du fournisseur de services, vous devez cliquer sur **Développer** en regard d’**Options avancées** dans la page Configuration SAML. Dans la zone **Émetteur du fournisseur de services**, entrez l’URL de l’espace de travail. La valeur par défaut est slack.com. 

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **Certificat (en base64)** , puis sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

1. Dans la section **Configurer Slack**, copiez la ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Slack.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Slack**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.

1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-slack-sso"></a>Configurer l’authentification unique Slack

1. Pour automatiser la configuration dans Slack, vous devez installer l’**extension de navigateur My Apps Secure Sign-in** en cliquant sur **Installer l’extension**.

    ![Extension My apps](common/install-myappssecure-extension.png)

2. Une fois l’extension ajoutée au navigateur, cliquez sur **Configurer Slack** pour être dirigé vers l’application Slack. À partir de là, indiquez les informations d’identification de l’administrateur pour vous connecter à Slack. Cette extension de navigateur configure automatiquement l’application et automatise les étapes 3 à 6.

    ![Configuration](common/setup-sso.png)

3. Si vous souhaitez configurer Slack manuellement, dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Slack en tant qu’administrateur.

2. Accédez à **Microsoft Azure AD**, puis à **Team Settings**.

     ![Configurer l’authentification unique sur Microsoft Azure AD](./media/slack-tutorial/tutorial-slack-team-settings.png)

3. Dans la section **Team Settings**, cliquez sur l’onglet **Authentication** puis sur **Change Settings**.

    ![Configurer l’authentification unique sur Team Settings](./media/slack-tutorial/tutorial-slack-authentication.png)

4. Dans la boîte de dialogue **SAML Authentication Settings** , procédez comme suit :

    ![Configurer l’authentification unique sur les paramètres d’authentification SAML](./media/slack-tutorial/tutorial-slack-save-authentication.png)

    a.  Dans la zone de texte **Point de terminaison SAML 2.0 (HTTP)** , collez la valeur de **l’URL de connexion** que vous avez copiée à partir du portail Azure.

    b.  Dans la zone de texte **Émetteur du fournisseur d’identité**, collez la valeur de **l’identificateur Azure AD** que vous avez copié à partir du portail Azure.

    c.  Ouvrez votre fichier de certificat téléchargé dans le Bloc-notes, copiez son contenu dans le Presse-papiers, puis collez-le dans la zone de texte **Certificat public**.

    d. Configurez les trois paramètres ci-dessus comme nécessaire pour votre équipe Slack. Pour plus d’informations sur les paramètres, vous trouverez le **guide de configuration de l’authentification unique sur Slack**. `https://get.slack.help/hc/articles/220403548-Guide-to-single-sign-on-with-Slack%60`

    ![Configurer l’authentification unique côté application](./media/slack-tutorial/tutorial-slack-expand.png)

    e. Cliquez sur **Développer** et entrez `https://slack.com` dans la zone de texte **Émetteur du fournisseur de service**.

    f.  Cliquez sur **Enregistrer la configuration**.
    
    > [!NOTE]
    > Si vous devez intégrer plus d’une instance de Slack à Azure AD, définissez `https://<DOMAIN NAME>.slack.com` sur **Émetteur du fournisseur de service** afin qu’il puisse être associé au paramètre **Identificateur** de l’application Azure.

### <a name="create-slack-test-user"></a>Créer un utilisateur de test Slack

L’objectif de cette section est de créer un utilisateur appelé B. Simon dans Slack. Slack prend en charge l’approvisionnement juste-à-temps, option activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. Un utilisateur est créé lors d’une tentative d’accès à Slack s’il n’existe pas déjà. Slack prend également en charge l’attribution automatique d’utilisateurs, vous trouverez plus d’informations [ici](slack-provisioning-tutorial.md) sur la façon de configurer l’attribution automatique d’utilisateurs.

> [!NOTE]
> Si vous devez créer un utilisateur manuellement, contactez [l’équipe de support technique Slack](https://slack.com/help/contact).

> [!NOTE]
> Azure AD Connect est l’outil de synchronisation qui peut synchroniser les identités Active Directory locales avec Azure AD. Ces utilisateurs synchronisés peuvent également utiliser les applications comme d’autres utilisateurs du cloud.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes.

* Cliquez sur **Tester cette application** dans le portail Azure. Cette opération redirige vers l’URL de connexion Slack, où vous pouvez lancer le processus de connexion.

* Accédez directement à l’URL de connexion Slack pour lancer le processus de connexion.

* Vous pouvez utiliser Mes applications de Microsoft. Un clic sur la vignette Slack dans Mes applications vous redirige vers l’URL de connexion Slack. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré Slack, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
