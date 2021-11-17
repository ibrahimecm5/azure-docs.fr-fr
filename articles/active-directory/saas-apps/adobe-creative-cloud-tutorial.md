---
title: 'Tutoriel : Intégration de l’authentification unique (SSO) Azure Active Directory à Adobe Creative Cloud | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Adobe Creative Cloud.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/13/2021
ms.author: jeedes
ms.openlocfilehash: 15a94bcd0dc3fa7f6eb3f18a793a562947b6d07f
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132281004"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-adobe-creative-cloud"></a>Tutoriel : Intégration de l’authentification unique (SSO) Azure Active Directory à Adobe Creative Cloud

Dans ce tutoriel, vous allez apprendre à intégrer Adobe Creative Cloud à Azure Active Directory (Azure AD). En intégrant Adobe Creative Cloud à Azure AD, vous pouvez :

* Contrôler, dans Azure AD, qui a accès à Adobe Creative Cloud
* Permettre aux utilisateurs de se connecter automatiquement à Adobe Creative Cloud avec leur compte Azure AD
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Adobe Creative Cloud pour lequel l’authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* Adobe Creative Cloud prend en charge l’authentification unique lancée par le **fournisseur de services**

## <a name="add-adobe-creative-cloud-from-the-gallery"></a>Ajouter Adobe Creative Cloud à partir de la galerie

Pour configurer l’intégration de Adobe Creative Cloud à Azure AD, vous devez ajouter Adobe Creative Cloud de la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Adobe Creative Cloud** dans la zone de recherche.
1. Sélectionnez **Adobe Creative Cloud** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-adobe-creative-cloud"></a>Configurer et tester l’authentification unique Azure AD pour Adobe Creative Cloud

Configurez et testez l’authentification unique Azure AD auprès d’Adobe Creative Cloud avec un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Adobe Creative Cloud associé.

Pour configurer et tester l’authentification unique Azure AD avec Adobe Creative Cloud, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Adobe Creative Cloud](#configure-adobe-creative-cloud-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test Adobe Creative Cloud](#create-adobe-creative-cloud-test-user)** pour avoir un équivalent de B.Simon dans Adobe Creative Cloud lié à la représentation Azure AD associée.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Sur le portail Azure, dans la page d’intégration de l’application **Adobe Creative Cloud**, recherchez la section **Gérer**, puis sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, entrez les valeurs pour les champs suivants :

    a. Dans la zone de texte **URL de connexion**, tapez l’URL : `https://adobe.com`

    b. Dans la zone de texte **Identificateur (ID d’entité)** , saisissez une URL au format suivant : `https://www.okta.com/saml2/service-provider/<token>`

    > [!NOTE]
    > La valeur de l'identificateur n'est pas réelle. Suivez les instructions de l’étape 4 de la section **Configurer l’authentification unique Adobe Cloud**. Vous pouvez ouvrir le **fichier XML de métadonnées de fédération** et obtenir la valeur d’ID d’entité, et la placer en tant que valeur d’identificateur dans la configuration Azure AD. Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. L’application Adobe Creative Cloud attend les assertions SAML dans un certain format, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à la configuration des attributs du jeton SAML. La capture d’écran suivante montre la liste des attributs par défaut.

    ![image](common/edit-attribute.png)

1. En plus de ce qui précède, l’application Adobe Creative Cloud s’attend à ce que quelques attributs supplémentaires (présentés ci-dessous) soient repassés dans la réponse SAML. Ces attributs sont également préremplis, mais vous pouvez les examiner pour voir s’ils répondent à vos besoins.

    | Nom | Attribut source|
    |----- | --------- |
    | FirstName | user.givenname |
    | LastName | user.surname |
    | E-mail | user.mail |

    > [!NOTE]
    > Les utilisateurs doivent avoir une licence Microsoft 365 ExO pour que la valeur de la revendication d’e-mail soit renseignée dans la réponse SAML.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **XML de métadonnées de fédération**, puis sélectionnez **Télécharger** pour télécharger le fichier de métadonnées XML et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

1. Dans la section **Configurer Adobe Creative Cloud**, copiez l’URL ou les URL appropriées selon vos besoins.

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

Dans cette section, vous autorisez B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Adobe Creative Cloud.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise** > **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Adobe Creative Cloud**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**. Ensuite, dans la boîte de dialogue **Ajouter une attribution**, sélectionnez **Utilisateurs et groupes**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B.Simon** dans la liste des utilisateurs. Choisissez **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, sélectionnez **Affecter**.

## <a name="configure-adobe-creative-cloud-sso"></a>Configurer l’authentification unique Adobe Creative Cloud

1. Dans une autre fenêtre de navigateur web, connectez-vous à [Adobe Admin Console](https://adminconsole.adobe.com) en tant qu’administrateur système.

1. Accédez à **Settings** (Paramètres) dans la barre de navigation supérieure, puis choisissez **Identity** (Identité). La liste des répertoires s’ouvre. Sélectionnez le répertoire fédéré de votre choix.

1. Dans la page **Directory Details** (Détails du répertoire), sélectionnez **Configure** (Configurer).

1. Copiez l’ID d’entité et l’URL ACS (URL Assertion Consumer Service ou URL de réponse). Entrez les URL dans les champs appropriés du portail Azure.

    ![Configurer l’authentification unique côté application](./media/adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_003.png)

    a. Utilisez la valeur de l’ID d’entité que vous a fournie Adobe comme **Identificateur** dans la boîte de dialogue **Configurer les paramètres d’application**.

    b. Utilisez la valeur de l’URL ACS (URL Assertion Consumer Service) que vous a fournie Adobe comme **URL de réponse** dans la boîte de dialogue **Configurer les paramètres d’application**.

1. Vers le bas de la page, chargez le fichier **XML de métadonnées de fédération** que vous avez téléchargé à partir du portail Azure. 

    ![Fichier XML de métadonnées de fédération](https://helpx.adobe.com/content/dam/help/en/enterprise/kb/configure-microsoft-azure-with-adobe-sso/jcr_content/main-pars/procedure/proc_par/step_228106403/step_par/image_copy/saml_signinig_certificate.png "XML de métadonnées IdP")

1. Sélectionnez **Enregistrer**.

### <a name="create-adobe-creative-cloud-test-user"></a>Créer un utilisateur de test Adobe Creative Cloud

Pour permettre aux utilisateurs Azure AD de se connecter à Adobe Creative Cloud, vous devez les provisionner dans Adobe Creative Cloud. Dans le cas d’Adobe Creative Cloud, cet approvisionnement est une tâche manuelle.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Pour approvisionner un compte d’utilisateur, procédez comme suit :

1. Connectez-vous au site [Adobe Admin Console](https://adminconsole.adobe.com) en tant qu’administrateur.

2. Ajoutez l’utilisateur au sein de la console Adobe en tant qu’ID fédéré et attribuez-le à un profil de produits. Pour plus d’informations sur l’ajout d’utilisateurs, consultez [Add users in Adobe Admin Console](https://helpx.adobe.com/enterprise/using/users.html#Addusers).

3. À ce stade, saisissez votre adresse e-mail/nom d’utilisateur principal dans le formulaire de connexion Adobe, appuyez sur la touche TABULATION et vous devriez être fédéré à Azure AD :
   * Accès Web : www\.adobe.com > connexion
   * Dans l’utilitaire de l’application de bureau > connexion
   * Dans l’application > aide > connexion

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion à Adobe Creative Cloud, d’où vous pouvez lancer le flux de connexion. 

* Accédez directement à l’URL de connexion à Adobe Creative Cloud pour lancer le flux de connexion.

* Vous pouvez utiliser Mes applications de Microsoft. Quand vous cliquez sur la vignette Adobe Creative Cloud dans Mes applications, vous êtes redirigé vers l’URL de connexion à Adobe Creative Cloud. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré Adobe Creative Cloud, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
