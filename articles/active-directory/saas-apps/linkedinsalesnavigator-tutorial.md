---
title: 'Tutoriel : Intégration de l’authentification unique (SSO) Azure Active Directory avec LinkedIn Sales Navigator | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et LinkedIn Sales Navigator.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/10/2021
ms.author: jeedes
ms.openlocfilehash: 34a4439af8421b2e829744c5d33beea4e74d934a
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132290853"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-linkedin-sales-navigator"></a>Tutoriel : Intégration de l’authentification unique (SSO) Azure Active Directory avec LinkedIn Sales Navigator

Ce tutoriel explique comment intégrer LinkedIn Sales Navigator avec Azure Active Directory (Azure AD). Quand vous intégrez LinkedIn Sales Navigator avec Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à LinkedIn Sales Navigator.
* Permettre à vos utilisateurs de se connecter automatiquement à LinkedIn Sales Navigator avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Abonnement LinkedIn Sales Navigator pour lequel l’authentification unique (SSO) est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* LinkedIn Sales Navigator prend en charge l’authentification unique initiée par le **fournisseur de services et le fournisseur d’identité**.
* LinkedIn Sales Navigator prend en charge le provisionnement d’utilisateurs **juste-à-temps**.
* LinkedIn Sales Navigator prend en charge le provisionnement d’utilisateurs **automatisé**.

> [!NOTE]
> L’identificateur de cette application étant une valeur de chaîne fixe, une seule instance peut être configurée dans un locataire.

## <a name="add-linkedin-sales-navigator-from-the-gallery"></a>Ajouter LinkedIn Sales Navigator à partir de la galerie

Pour configurer l’intégration de LinkedIn Sales Navigator avec Azure AD, vous devez ajouter LinkedIn Sales Navigator à votre liste d’applications SaaS gérées à partir de la galerie.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **LinkedIn Sales Navigator** dans la zone de recherche.
1. Sélectionnez **LinkedIn Sales Navigator** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-linkedin-sales-navigator"></a>Configurer et tester l’authentification unique Azure AD pour LinkedIn Sales Navigator

Configurez et testez l’authentification unique Azure AD avec LinkedIn Sales Navigator à l’aide d’un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur LinkedIn Sales Navigator associé.

Pour configurer et tester l’authentification unique Azure AD avec LinkedIn Sales Navigator, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique LinkedIn Sales Navigator](#configure-linkedin-sales-navigator-sso)** – pour configurer les paramètres d’authentification unique côté application.
    1. **[Créer un utilisateur de test LinkedIn Sales Navigator](#create-linkedin-sales-navigator-test-user)** – pour avoir un équivalent de B.Simon dans LinkedIn Sales Navigator qui soit lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **LinkedIn Sales Navigator**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, si vous souhaitez configurer l’application en mode lancé par le **fournisseur d’identité**, effectuez les étapes suivantes :

    a. Dans la zone de texte **Identificateur**, entrez la valeur **Entity ID** (la procédure à suivre pour copier cette valeur à partir du portail Linkedin est expliquée plus loin dans ce tutoriel).

    b. Dans la zone de texte **URL de réponse**, entrez la valeur **Assertion Consumer Access (ACS) URL** (la procédure à suivre pour copier cette valeur à partir du portail Linkedin est expliquée plus loin dans ce tutoriel).

1. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://www.linkedin.com/checkpoint/enterprise/login/<account id>?application=salesNavigator`

1. L’application LinkedIn Sales Navigator attend les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à votre configuration Attributs du jeton SAML. La capture d’écran suivante montre la liste des attributs par défaut.

    ![image](common/default-attributes.png)

1. En plus de ce qui précède, l’application LinkedIn Sales Navigator s’attend à ce que quelques attributs supplémentaires, indiqués ci-dessous, soient repassés dans la réponse SAML. Ces attributs sont également préremplis, mais vous pouvez les examiner pour voir s’ils répondent à vos besoins.

    | Nom | Attribut source|
    | --- | --- |
    | email| user.mail |
    | department| user.department |
    | firstname| user.givenname |
    | lastname| user.surname |
    | Identificateur d’utilisateur unique | user.mail |

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **XML de métadonnées de fédération** et sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

1. Dans la section **Configurer LinkedIn Sales Navigator**, copiez la ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à LinkedIn Sales Navigator.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **LinkedIn Sales Navigator**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-linkedin-sales-navigator-sso"></a>Configurer l’authentification unique LinkedIn Sales Navigator

1. Dans une autre fenêtre de navigateur web, connectez-vous à votre site web **LinkedIn Sales Navigator** en tant qu’administrateur.

1. Dans le **Centre des comptes**, cliquez sur **Paramètres globaux** sous **Paramètres**. En outre, sélectionnez **Sales Navigator** dans la liste déroulante.

    ![Capture d’écran montrant la section Authentication Settings dans laquelle vous pouvez sélectionner Sales Navigator.](./media/linkedinsalesnavigator-tutorial/settings.png)

1. Cliquez sur **OR Click Here to load and copy individual fields from the form** (OU cliquez ici pour charger et copier des champs individuels à partir du formulaire) et effectuez les étapes suivantes :

    ![Capture d’écran montrant l’authentification unique pour laquelle vous pouvez entrer les valeurs décrites.](./media/linkedinsalesnavigator-tutorial/values.png)

    a. Copiez la valeur **Entity ID** et collez-la dans la zone de texte **Identificateur** de la section **Configuration SAML de base** du portail Azure.

    b. Copiez la valeur **Assertion Consumer Access (ACS) URL** et collez-la dans la zone de texte **URL de réponse** de la section **Configuration SAML de base** du portail Azure.

1. Accédez à la section **Paramètres de l’administrateur LinkedIn**. Chargez le fichier XML que vous avez téléchargé à partir du portail Azure en cliquant sur l’option **Upload XML file** (Charger un fichier XML).

    ![Capture d’écran montrant la section Configure the LinkedIn service provider SSO settings, dans laquelle vous pouvez charger un fichier XML.](./media/linkedinsalesnavigator-tutorial/metadata.png)

1. Cliquez sur **Activer** pour activer l’authentification unique. L’état de l’authentification unique passe de **Non connecté** à **Connecté**

    ![Capture d’écran montrant l’authentification unique pour laquelle vous pouvez activer l’option Authenticate users with SSO.](./media/linkedinsalesnavigator-tutorial/authentication.png)

### <a name="create-linkedin-sales-navigator-test-user"></a>Créer un utilisateur de test LinkedIn Sales Navigator

L’application LinkedIn Sales Navigator prend en charge la configuration d’utilisateur juste à temps (JIT), et après authentification, les utilisateurs sont créés automatiquement dans l’application. Activez **Affecter automatiquement les licences** pour affecter une licence à l’utilisateur.

   ![Création d’un utilisateur de test Azure AD](./media/linkedinsalesnavigator-tutorial/provisioning.png)

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

#### <a name="sp-initiated"></a>Lancée par le fournisseur de services :

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion de LinkedIn Sales Navigator, où vous pouvez lancer le flux de connexion.  

* Accédez directement à l’URL de connexion de LinkedIn Sales Navigator et démarrez le flux de connexion.

#### <a name="idp-initiated"></a>Lancée par le fournisseur d’identité :

* Cliquez sur **Tester cette application** dans le portail Azure : vous devez être connecté automatiquement à l’instance de LinkedIn Sales Navigator pour laquelle vous avez configuré l’authentification unique. 

Vous pouvez aussi utiliser Mes applications de Microsoft pour tester l’application dans n’importe quel mode. Si, quand vous cliquez sur la vignette LinkedIn Sales Navigator dans Mes applications, le mode Fournisseur de services est configuré, vous êtes redirigé vers la page de connexion de l’application pour lancer le flux de connexion ; s’il s’agit du mode Fournisseur d’identité, vous êtes automatiquement connecté à l’instance de LinkedIn Sales Navigator pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré LinkedIn Sales Navigator, vous pouvez appliquer le contrôle de session, qui protège en temps réel contre l’exfiltration et l’infiltration de données sensibles de votre organisation. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
