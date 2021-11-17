---
title: 'Tutoriel : Intégration de l’authentification unique Azure Active Directory à LinkedIn Elevate | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et LinkedIn Elevate.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/03/2021
ms.author: jeedes
ms.openlocfilehash: cbf205d925029c4b960bd15775b81c0715af7813
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132326066"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-linkedin-elevate"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory à LinkedIn Elevate

Dans ce tutoriel, vous allez apprendre à intégrer LinkedIn Elevate à Azure Active Directory (Azure AD). Quand vous intégrez LinkedIn Elevate à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à LinkedIn Elevate.
* Permettre à vos utilisateurs de se connecter automatiquement à LinkedIn Elevate avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement LinkedIn Elevate pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* LinkedIn Elevate prend en charge l’authentification unique lancée par le **fournisseur de services et le fournisseur d’identité**.
* LinkedIn Elevate prend en charge l’attribution d’utilisateurs **juste-à-temps**.
* LinkedIn Elevate prend en charge l’[attribution d’utilisateurs **automatisée**](linkedinelevate-provisioning-tutorial.md).

## <a name="add-linkedin-elevate-from-the-gallery"></a>Ajouter LinkedIn Elevate à partir de la galerie

Pour configurer l’intégration de LinkedIn Elevate à Azure AD, vous devez ajouter LinkedIn Elevate à partir de la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **LinkedIn Elevate** dans la zone de recherche.
1. Sélectionnez **LinkedIn Elevate** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-linkedin-elevate"></a>Configurer et tester l’authentification unique Azure AD pour LinkedIn Elevate

Configurez et testez l’authentification unique Azure AD avec LinkedIn Elevate pour un utilisateur de test nommé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur LinkedIn Elevate associé.

Pour configurer et tester l’authentification unique Azure AD avec LinkedIn Elevate, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique LinkedIn Elevate](#configure-linkedin-elevate-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test LinkedIn Elevate](#create-linkedin-elevate-test-user)** pour avoir un équivalent de B.Simon dans LinkedIn Elevate lié à la représentation Azure AD associée.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **LinkedIn Elevate**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, si vous souhaitez configurer l’application en mode lancé par le **fournisseur d’identité**, effectuez les étapes suivantes :

    a. Dans la zone de texte **Identificateur**, entrez la valeur **Entity ID** (la procédure à suivre pour copier cette valeur à partir du portail Linkedin est expliquée plus loin dans ce tutoriel).

    b. Dans la zone de texte **URL de réponse**, entrez la valeur **Assertion Consumer Access (ACS) URL** (la procédure à suivre pour copier cette valeur à partir du portail Linkedin est expliquée plus loin dans ce tutoriel).

5. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://www.linkedin.com/checkpoint/enterprise/login/<ACCOUNT_ID>?application=elevate&applicationInstanceId=<INSTANCE_ID>`

1. L’application LinkedIn Elevate attend les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à votre configuration Attributs du jeton SAML. La capture d’écran suivante montre la liste des attributs par défaut, où **nameidentifier** est mappé avec **user.userprincipalname**. L’application LinkedIn Elevate s’attend à ce que nameidentifier soit mappé à **user.mail**. Vous devez donc modifier le mappage d’attribut en cliquant sur l’icône Modifier et en procédant aux changements.

    ![image](common/edit-attribute.png)

1. En plus de ce qui précède, l’application LinkedIn Elevate s’attend à ce que quelques attributs supplémentaires, indiqués ci-après, soient passés dans la réponse SAML. Ces attributs sont également préremplis, mais vous pouvez les examiner pour voir s’ils répondent à vos besoins.

    | Nom | Attribut source|
    | -------| -------------|
    | department | user.department |

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **XML de métadonnées de fédération** et sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

1. Dans la section **Configurer LinkedIn Elevate**, copiez la ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à LinkedIn Elevate.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **LinkedIn Elevate**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-linkedin-elevate-sso"></a>Configurer l’authentification unique LinkedIn Elevate

1. Dans une autre fenêtre de navigateur web, connectez-vous à votre client LinkedIn Elevate en tant qu’administrateur.

1. Dans le **Centre des comptes**, cliquez sur **Paramètres globaux** sous **Paramètres**. En outre, sélectionnez **Elevate - Test AAD Elevate** dans la liste déroulante.

    ![Capture d’écran montrant les paramètres globaux pour lesquels vous pouvez sélectionner l’option Elevate AAD Test.](./media/linkedinelevate-tutorial/admin.png)

1. Cliquez sur **OR Click Here to load and copy individual fields from the form** (OU cliquez ici pour charger et copier des champs individuels à partir du formulaire) et effectuez les étapes suivantes :

    ![Capture d’écran montrant l’authentification unique pour laquelle vous pouvez entrer les valeurs décrites.](./media/linkedinelevate-tutorial/test.png)

    a. Copiez la valeur **Entity ID** et collez-la dans la zone de texte **Identificateur** de la section **Configuration SAML de base** du portail Azure.

    b. Copiez la valeur **Assertion Consumer Access (ACS) URL** et collez-la dans la zone de texte **URL de réponse** de la section **Configuration SAML de base** du portail Azure.

1. Accédez à la section **Paramètres de l’administrateur LinkedIn**. Chargez le fichier XML que vous avez téléchargé à partir du portail Azure en cliquant sur l’option Upload XML file (Charger un fichier XML).

    ![Capture d’écran montrant la section Configure the LinkedIn service provider SSO settings, dans laquelle vous pouvez charger un fichier XML.](./media/linkedinelevate-tutorial/metadata.png)

1. Cliquez sur **Activer** pour activer l’authentification unique. L’état de l’authentification unique passera de **Non connecté** à **Connecté**.

    ![Capture d’écran montrant l’authentification unique pour laquelle vous pouvez sélectionner l’option Automatically assign licenses.](./media/linkedinelevate-tutorial/connected.png)

### <a name="create-linkedin-elevate-test-user"></a>Créer un utilisateur de test LinkedIn Elevate

L’application LinkedIn Elevate prend en charge l’attribution d’utilisateurs juste-à-temps ; après authentification, les utilisateurs sont créés automatiquement dans l’application. Sur la page des paramètres administrateur du portail LinkedIn Elevate, basculez le commutateur **Affecter automatiquement les licences** sur Configuration juste-à-temps active. Cette action affectera également une licence à l’utilisateur. LinkedIn Elevate prend également en charge l’attribution automatique d’utilisateurs. Des informations supplémentaires sur la configuration de l’attribution automatique d’utilisateurs sont disponibles [ici](linkedinelevate-provisioning-tutorial.md).

   ![Création d’un utilisateur de test Azure AD](./media/linkedinelevate-tutorial/switch.png)

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

#### <a name="sp-initiated"></a>Lancée par le fournisseur de services :

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion LinkedIn Elevate où vous pouvez lancer le flux de connexion.  

* Accédez directement à l’URL de connexion LinkedIn Elevate et démarrez le flux de connexion.

#### <a name="idp-initiated"></a>Lancée par le fournisseur d’identité :

* Cliquez sur **Tester cette application** dans le portail Azure : vous devez être connecté automatiquement à l’instance de LinkedIn Elevate pour laquelle vous avez configuré l’authentification unique. 

Vous pouvez aussi utiliser Mes applications de Microsoft pour tester l’application dans n’importe quel mode. Si, quand vous cliquez sur la vignette LinkedIn Elevate dans Mes applications, le mode Fournisseur de services est configuré, vous êtes redirigé vers la page de connexion de l’application pour lancer le flux de connexion ; s’il s’agit du mode Fournisseur d’identité, vous êtes automatiquement connecté à l’instance LinkedIn Elevate pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré LinkedIn Elevate, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
