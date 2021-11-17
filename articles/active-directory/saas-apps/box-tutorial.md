---
title: 'Tutoriel : Intégration de l’authentification unique (SSO) Azure Active Directory à Box | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Box.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/21/2021
ms.author: jeedes
ms.openlocfilehash: dbfefc71ab35d720300018b357a57eb56b446ae4
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132336180"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-box"></a>Tutoriel : Intégration de l’authentification unique (SSO) Azure Active Directory à Box

Dans ce tutoriel, vous allez apprendre à intégrer Box à Azure Active Directory (Azure AD). Quand vous intégrez Box à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Box.
* Permettre à vos utilisateurs de se connecter automatiquement à Box avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Abonnement pour lequel l’authentification unique (SSO) de Box est activée.

> [!NOTE]
> Cette intégration peut également être utilisée à partir de l’environnement cloud US Government Azure AD. Cette application est disponible dans la Galerie d’applications cloud US Government Azure AD et peut être configurée de la même façon que dans le cloud public.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* Box prend en charge l’authentification unique (SSO) initiée par le **fournisseur de services**.
* Box prend en charge le [provisionnement et le déprovisionnement **automatisés** des utilisateurs](./box-userprovisioning-tutorial.md) (recommandé)
* Box prend en charge l’approvisionnement d’utilisateur **juste-à-temps**.

> [!NOTE]
> L’identificateur de cette application étant une valeur de chaîne fixe, une seule instance peut être configurée dans un locataire.

## <a name="adding-box-from-the-gallery"></a>Ajout de Box à partir de la galerie

Pour configurer l’intégration de Box à Azure AD, vous devez ajouter Box, disponible dans la galerie, à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Box** dans la zone de recherche.
1. Sélectionnez **Box** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-box"></a>Configurer et tester l’authentification unique Azure AD pour Box

Configurez et testez l’authentification unique (SSO) Azure AD avec Box à l’aide d’un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique (SSO) fonctionne, vous devez établir une relation entre un utilisateur Azure AD et l’utilisateur Box associé.

Pour configurer et tester l’authentification unique Azure AD avec Box, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique (SSO) Box](#configure-box-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test Box](#create-box-test-user)** pour disposer, dans Box, d’un équivalent de B.Simon lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans la page d’intégration de l’application **Box** du portail Azure, recherchez la section **Gérer**, puis sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de modification/stylet de **Configuration SAML de base** pour modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, entrez les valeurs pour les champs suivants :

    a. Dans la zone de texte **URL de connexion**, saisissez une URL au format suivant : `https://<SUBDOMAIN>.account.box.com`

    b. Dans la zone de texte **Identificateur (ID d’entité)** , saisissez une URL : `box.net`

    c. Dans la zone **URL de réponse**, tapez l’URL : `https://sso.services.box.net/sp/ACS.saml2`

    > [!NOTE]
    > La valeur de l’URL de connexion n’est pas réelle. Mettez à jour la valeur avec l’URL de connexion réelle. Pour obtenir la valeur, contactez [l’équipe du support Box](https://community.box.com/t5/custom/page/page-id/submit_sso_questionaire). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. Votre application Box s’attend à recevoir les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à votre configuration d’attributs de jetons SAML. La capture d’écran suivante montre un exemple : La valeur par défaut pour **Identificateur d’utilisateur unique** est **user.userprincipalname**, mais Box s’attend à ce qu’elle soit mappée sur l’adresse e-mail de l’utilisateur. Pour cela, vous pouvez utiliser l’attribut **user.mail** dans la liste ou utiliser la valeur d’attribut appropriée en fonction de la configuration de votre organisation.

    ![image](common/default-attributes.png)


1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **XML de métadonnées de fédération** et sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Box.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Box**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.

1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-box-sso"></a>Configurer l’authentification unique (SSO) Box

1. Pour automatiser la configuration dans Box, vous devez installer l’**extension de navigateur de connexion sécurisée à Mes applications** en cliquant sur **Installer l’extension**.

    ![Extension My apps](common/install-myappssecure-extension.png)

2. Une fois l’extension ajoutée au navigateur, le fait de cliquer sur **Configurer Box** vous redirige vers l’application Box. À partir de là, indiquez les informations d’identification de l’administrateur pour vous connecter à Box. Cette extension de navigateur configure automatiquement l’application pour vous et automatise l’étape 3.

    ![Configuration](common/setup-sso.png)

3. Si vous souhaitez configurer Box manuellement, dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Box en tant qu’administrateur et suivez la procédure indiquée dans [Set up SSO on your own](https://community.box.com/t5/How-to-Guides-for-Admins/Setting-Up-Single-Sign-On-SSO-for-your-Enterprise/ta-p/1263#ssoonyourown) (Configurer l’authentification unique vous-même).

> [!NOTE]
> Si vous ne pouvez pas configurer les paramètres de l’authentification unique pour votre compte Box, vous devez envoyer le **XML de métadonnées de fédération** à [l’équipe de support technique de Box](https://community.box.com/t5/custom/page/page-id/submit_sso_questionaire). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

### <a name="create-box-test-user"></a>Créer un utilisateur de test Box

Dans cette section, un utilisateur appelé Britta Simon est créé dans Box. Box prend en charge l’approvisionnement d’utilisateurs juste-à-temps, option activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas encore d’utilisateur dans Box, il en est créé un après l’authentification.

> [!NOTE]
> Si vous devez créer un utilisateur manuellement, contactez l’[équipe de support technique de Box](https://community.box.com/t5/custom/page/page-id/submit_sso_questionaire).

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes.

* Dans le portail Azure, sélectionnez **Tester cette application**. Vous êtes alors redirigé vers l’URL de connexion à Box, d’où vous pouvez lancer le flux de connexion.

* Accédez directement à l’URL de connexion Box pour lancer le processus de connexion.

* Vous pouvez utiliser Mes applications de Microsoft. Le fait de cliquer sur la vignette Box dans Mes applications vous redirige vers l’URL de connexion Box. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

### <a name="push-an-azure-group-to-box"></a>Envoyer (push) un groupe Azure à Box

Vous pouvez envoyer un groupe Azure à Box et synchroniser ce groupe. Azure envoie les groupes à Box via une intégration de niveau API.

1. Dans **Utilisateurs et groupes**, recherchez le groupe que vous voulez affecter à Box.
1. Dans **Provisionnement**, vérifiez que **Synchroniser les groupes Azure Active Directory sur Box** est sélectionné. Ce paramètre synchronise les groupes que vous avez alloués à l’étape précédente. L’envoi de ces groupes à partir d’Azure peut prendre un certain temps.

> [!NOTE]
> Si vous devez créer un utilisateur manuellement, contactez l’[équipe de support technique de Box](https://community.box.com/t5/custom/page/page-id/submit_sso_questionaire).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré Box, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
