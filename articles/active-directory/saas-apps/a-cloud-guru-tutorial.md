---
title: 'Tutoriel : Intégration de l’authentification unique Azure Active Directory à A Cloud Guru | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et A Cloud Guru.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/03/2021
ms.author: jeedes
ms.openlocfilehash: bd4a2736983740fc543677749bddda933ae13748
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132305387"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-a-cloud-guru"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory à A Cloud Guru

Dans ce tutoriel, vous allez apprendre à intégrer A Cloud Guru à Azure Active Directory (Azure AD). Quand vous intégrez A Cloud Guru à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à A Cloud Guru.
* Permettre à vos utilisateurs de se connecter automatiquement à A Cloud Guru avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement A Cloud Guru pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* A Cloud Guru prend en charge l’authentification unique initiée par le **fournisseur de services et le fournisseur d’identité**.
* A Cloud Guru prend en charge l’attribution d’utilisateurs **juste-à-temps**.

## <a name="adding-a-cloud-guru-from-the-gallery"></a>Ajouter A Cloud Guru à partir de la galerie

Pour configurer l’intégration d’A Cloud Guru dans Azure AD, vous devez ajouter A Cloud Guru à partir de la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, saisissez **A Cloud Guru** dans la zone de recherche.
1. Sélectionnez **A Cloud Guru** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.


## <a name="configure-and-test-azure-ad-sso-for-a-cloud-guru"></a>Configurer et tester l’authentification unique Azure AD pour A Cloud Guru

Configurez et testez l’authentification unique Azure AD avec A Cloud Guru à l’aide d’un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur associé dans A Cloud Guru.

Pour configurer et tester l’authentification unique Azure AD avec A Cloud Guru, procédez comme suit :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique d’A Cloud Guru](#configure-a-cloud-guru-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test A Cloud Guru](#create-a-cloud-guru-test-user)** pour avoir un équivalent de B.Simon dans A Cloud Guru lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **A Cloud Guru**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de modification/stylet de **Configuration SAML de base** pour modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, si vous souhaitez configurer l’application en mode Initié par le **fournisseur d’identité**, entrez les valeurs pour les champs suivants :

    a. Dans la zone de texte **Identificateur**, tapez une valeur au format suivant : `urn:auth0:acloudguru:<CLIENT_CONNECTION_NAME>`

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://auth.acloud.guru/login/callback?connection=<CLIENT_CONNECTION_NAME>`

1. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    Dans la zone de texte **URL de connexion**, tapez l’URL : `https://learn.acloud.guru/`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur et l’URL de réponse réels. Pour obtenir ces valeurs, contactez l’[équipe de support technique d’A Cloud Guru](mailto:sso@acloud.guru). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. Votre application A Cloud Guru s’attend à recevoir les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à la configuration des attributs de jetons SAML. La capture d’écran suivante montre un exemple : La valeur par défaut pour **Identificateur d’utilisateur unique (ID de nom)** est **user.userprincipalname**, mais A Cloud Guru s’attend à ce qu’elle soit mappée au nom de l’utilisateur. Pour cela, vous pouvez utiliser l’attribut **user.givenname** dans la liste ou utiliser la valeur d’attribut appropriée en fonction de la configuration de votre organisation.

    ![image](common/default-attributes.png)

1. En plus de ce qui précède, l’application A Cloud Guru s’attend à ce que quelques attributs supplémentaires (présentés ci-dessous) soient repassés dans la réponse SAML. Ces attributs sont également préremplis, mais vous pouvez les examiner pour voir s’ils répondent à vos besoins.
    
    | Nom |  Attribut source|
    | ----------------- | --------- |
    | email  | user.emailaddress |
    | family_name | user.surname |
    | given_name | user.givenname |

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **Certificat (en base64)** , puis sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

1. Dans la section **Configurer A Cloud Guru**, copiez les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à A Cloud Guru.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **A Cloud Guru**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-a-cloud-guru-sso"></a>Configurer l’authentification unique d’A Cloud Guru

Pour configurer l’authentification unique côté **A Cloud Guru**, vous devez envoyer le **certificat (Base64)** téléchargé et les URL appropriées, copiées à partir du portail Azure, à l’[équipe du support technique d’A Cloud Guru](mailto:sso@acloud.guru). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

### <a name="create-a-cloud-guru-test-user"></a>Créer un utilisateur de test A Cloud Guru

Dans cette section, un utilisateur appelé Britta Simon est créé dans A Cloud Guru. A Cloud Guru prend en charge l’attribution d’utilisateurs juste-à-temps, option qui est activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. Grâce à cela, s’il n’existe pas encore d’utilisateur dans A Cloud Guru, il en est créé un après l’authentification.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

#### <a name="sp-initiated"></a>Lancée par le fournisseur de services :

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion d’A Cloud Guru, à partir de laquelle vous pouvez lancer le flux de connexion.  

* Accédez directement à l’URL de connexion d’A Cloud Guru pour initier le flux de connexion.

#### <a name="idp-initiated"></a>Lancée par le fournisseur d’identité :

* Cliquez sur **Tester cette application** dans Portail Azure : vous devez être connecté automatiquement à l’instance d’A Cloud Guru pour laquelle vous avez configuré l’authentification unique. 

Vous pouvez aussi utiliser Mes applications de Microsoft pour tester l’application dans n’importe quel mode. Quand vous cliquez sur la vignette A Cloud Guru dans Mes applications, si le mode Fournisseur de services est configuré, vous êtes redirigé vers la page de connexion de l’application pour lancer le flux de connexion ; si le mode Fournisseur d’identité est configuré, vous êtes automatiquement connecté à l’instance d’A Cloud Guru pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez configuré A Cloud Guru, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
