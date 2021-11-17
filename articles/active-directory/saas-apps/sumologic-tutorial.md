---
title: 'Tutoriel : Intégration de l’authentification unique (SSO) Azure Active Directory avec SumoLogic | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et SumoLogic.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/14/2021
ms.author: jeedes
ms.openlocfilehash: 2c523679d02994b8a6a440fbf41b63d9f5d4d785
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132299042"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sumologic"></a>Tutoriel : Intégration de l’authentification unique (SSO) Azure Active Directory avec SumoLogic

Ce tutoriel explique comment intégrer SumoLogic avec Azure Active Directory (Azure AD). Quand vous intégrez SumoLogic avec Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à SumoLogic.
* Permettre à vos utilisateurs de se connecter automatiquement à SumoLogic avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Abonnement SumoLogic pour lequel l’authentification unique (SSO) est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* SumoLogic prend en charge l’authentification unique lancée par le **fournisseur d’identité**.

## <a name="add-sumologic-from-the-gallery"></a>Ajouter SumoLogic à partir de la galerie

Pour configurer l’intégration de SumoLogic dans Azure AD, vous devez ajouter SumoLogic à partir de la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **SumoLogic** dans la zone de recherche.
1. Sélectionnez **SumoLogic** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-sumologic"></a>Configurer et tester l’authentification unique Azure AD pour SumoLogic

Configurez et testez l’authentification unique Azure AD avec SumoLogic à l’aide d’un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur SumoLogic associé.

Pour configurer et tester l’authentification unique Azure AD avec SumoLogic, procédez comme suit :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique SumoLogic](#configure-sumologic-sso)** – pour configurer les paramètres d’authentification unique côté application.
    1. **[Créer un utilisateur de test SumoLogic](#create-sumologic-test-user)** – pour avoir un équivalent de B.Simon dans SumoLogic qui soit lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **SumoLogic**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la page **Configurer l’authentification unique avec SAML**, effectuez les étapes suivantes :

    a. Dans la zone de texte **Identificateur**, tapez une URL en utilisant un des modèles suivants :

    | URL d’identificateur |
    |---|
    | `https://service.sumologic.com`|
    | `https://<tenantname>.us2.sumologic.com`|
    | `https://<tenantname>.us4.sumologic.com`|
    | `https://<tenantname>.eu.sumologic.com`|
    | `https://<tenantname>.jp.sumologic.com`|
    | `https://<tenantname>.de.sumologic.com`|
    | `https://<tenantname>.ca.sumologic.com`|
    |

    b. Dans la zone de texte **URL de réponse** , tapez une URL en utilisant un des modèles suivants :

    | URL de réponse |
    |---|
    | `https://service.sumologic.com/sumo/saml/consume/<tenantname>` |
    | `https://service.us2.sumologic.com/sumo/saml/consume/<tenantname>` |
    | `https://service.us4.sumologic.com/sumo/saml/consume/<tenantname>` |
    | `https://service.eu.sumologic.com/sumo/saml/consume/<tenantname>` |
    | `https://service.jp.sumologic.com/sumo/saml/consume/<tenantname>` |
    | `https://service.de.sumologic.com/sumo/saml/consume/<tenantname>` |
    | `https://service.ca.sumologic.com/sumo/saml/consume/<tenantname>` |
    | `https://service.au.sumologic.com/sumo/saml/consume/<tenantname>` |
    |

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur et l’URL de réponse réels. Pour obtenir ces valeurs, contactez l’[équipe de support technique SumoLogic](https://www.sumologic.com/contact-us/). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. L’application SumoLogic attend les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à votre configuration des attributs de jeton SAML. La capture d’écran suivante montre la liste des attributs par défaut.

    ![image](common/default-attributes.png)

1. En plus de ce qui précède, l’application SumoLogic s’attend à ce que quelques attributs supplémentaires, indiqués ci-dessous, soient repassés dans la réponse SAML. Ces attributs sont également préremplis, mais vous pouvez les examiner pour voir s’ils répondent à vos besoins.

    |  Nom | Attribut source |
    | ---------------| --------------- |
    | FirstName | user.givenname |
    | LastName | user.surname |
    | Rôles | user.assignedroles |

    > [!NOTE]
    > Cliquez [ici](../develop/active-directory-enterprise-app-role-management.md) pour savoir comment configurer un **rôle** dans Azure AD.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **Certificat (en base64)** , puis sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

1. Dans la section **SumoLogic**, copiez la ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à SumoLogic.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **SumoLogic**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-sumologic-sso"></a>Configurer l’authentification unique SumoLogic

1. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise SumoLogic en tant qu’administrateur.

1. Accédez à **Gérer** -> **Sécurité**.

    ![Gérer](./media/sumologic-tutorial/security.png "Gérer")

1. Cliquez sur **SAML**.

    ![Paramètres de sécurité globaux](./media/sumologic-tutorial/settings.png "Paramètres de sécurité globaux")

1. Dans la liste **Select a configuration or create a new one**, sélectionnez **Azure AD**, puis cliquez sur **Configure**.

    ![Capture d’écran montrant Configure SAML 2.0 où vous pouvez sélectionner Azure AD.](./media/sumologic-tutorial/configure.png "Configure SAML 2.0")

1. Dans la boîte de dialogue **Configure SAML 2.0** , procédez comme suit :

    ![Capture d’écran montrant la boîte de dialogue Configure SAML 2.0 où vous pouvez entrer les valeurs décrites.](./media/sumologic-tutorial/configuration.png "Configure SAML 2.0")

    a. Dans la zone de texte **Configuration Name**, entrez **Azure AD**.

    b. Sélectionnez **Debug Mode**.

    c. Dans la zone de texte **Issuer** (Émetteur), collez la valeur **Identificateur Azure AD**, que vous avez copiée à partir du portail Azure.

    d. Dans la zone de texte **Authn Request URL** (URL de la requête d’authentification), collez la valeur **URL de connexion** que vous avez copiée à partir du portail Azure.

    e. Ouvrez votre certificat codé en base 64 dans le bloc-notes, copiez son contenu dans le Presse-papiers et collez-le dans la zone de texte **X.509 Certificate** .

    f. Dans **Email Attribute**, sélectionnez **Use SAML subject**.  

    g. Sélectionnez **SP initiated Login Configuration**.

    h. Dans la zone de texte **Login Path** (Chemin d’accès de connexion), entrez **Azure**, puis cliquez sur **Save** (Enregistrer).

### <a name="create-sumologic-test-user"></a>Créer un utilisateur de test SumoLogic

Pour se connecter à SumoLogic, les utilisateurs Azure AD doivent être provisionnés dans SumoLogic. Dans le cas de SumoLogic, l’approvisionnement est une tâche manuelle.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous à votre locataire **SumoLogic**.

1. Accédez à **Gérer \> Utilisateurs**.

    ![Capture d’écran montrant Users sélectionné dans le menu Manage.](./media/sumologic-tutorial/user.png "Utilisateurs")

1. Cliquez sur **Add**.

    ![Capture d’écran montrant le bouton Add pour Users.](./media/sumologic-tutorial/add-user.png "Utilisateurs")

1. Dans la boîte de dialogue **New User** , procédez comme suit :

    ![Nouvel utilisateur](./media/sumologic-tutorial/new-account.png "Nouvel utilisateur")

    a. Indiquez dans les zones de texte **First Name**, **Last Name** et **Email** le prénom, le nom et l’adresse e-mail du compte Azure AD valide que vous souhaitez approvisionner.
  
    b. Sélectionnez un rôle
  
    c. Pour **Status (Statut)** , sélectionnez **Active (Actif)** .
  
    d. Cliquez sur **Enregistrer**.

> [!NOTE]
> Vous pouvez utiliser tout autre outil ou n’importe quelle API de création de compte d’utilisateur fournis par SumoLogic pour provisionner des comptes d’utilisateurs Azure AD.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes.

* Cliquez sur Tester cette application dans le portail Azure : vous devez être connecté automatiquement à l’instance SumoLogic pour laquelle vous avez configuré l’authentification unique.

* Vous pouvez utiliser Mes applications de Microsoft. Quand vous cliquez sur la vignette SumoLogic dans Mes applications, vous devez être automatiquement connecté à l’instance SumoLogic pour laquelle vous avez configuré l’authentification SSO. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez configuré SumoLogic, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
