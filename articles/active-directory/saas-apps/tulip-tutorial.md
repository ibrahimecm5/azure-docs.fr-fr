---
title: 'Tutoriel : Intégration de l’authentification unique Azure Active Directory à Tulip | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Tulip.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/30/2021
ms.author: jeedes
ms.openlocfilehash: 78922b738e470084c0218be829a02d57b85527e7
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132332798"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-tulip"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory à Tulip

Dans ce tutoriel, vous allez apprendre à intégrer Tulip à Azure Active Directory (Azure AD). Quand vous intégrez Tulip à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Tulip.
* Permettre à vos utilisateurs de se connecter automatiquement à Tulip avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Tulip pour lequel l’authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.


* Tulip prend en charge l’authentification unique initiée par le **fournisseur d’identité**.

## <a name="adding-tulip-from-the-gallery"></a>Ajout de Tulip à partir de la galerie

Pour configurer l’intégration de Tulip à Azure AD, vous devez ajouter Tulip depuis la galerie à votre liste d’applications SaaS managées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Tulip** dans la zone de recherche.
1. Sélectionnez **Tulip** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.


## <a name="configure-and-test-azure-ad-sso-for-tulip"></a>Configurer et tester l’authentification unique Azure AD pour Tulip

Configurez et testez l’authentification unique Azure AD avec Tulip pour une utilisatrice de test appelée **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur associé dans Tulip.

Pour configurer et tester l’authentification unique Azure AD avec Tulip, procédez comme suit :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Tulip](#configure-tulip-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test Tulip](#create-tulip-test-user)** pour avoir, dans Tulip, un équivalent de B.Simon lié à la représentation Azure AD associée.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **Tulip**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, si vous disposez d’un **fichier de métadonnées du fournisseur de services**, suivez les étapes ci-dessous :

    a. Cliquez sur **Charger un fichier de métadonnées**.

    ![image1](common/upload-metadata.png)

    b. Cliquez sur le **logo du dossier** pour sélectionner le fichier de métadonnées, puis cliquez sur **Charger**.

    ![image2](common/browse-upload-metadata.png)

    c. Une fois le fichier de métadonnées chargé, les valeurs **Identificateur** et **URL de réponse** sont automatiquement renseignées dans la section Configuration SAML de base :

    ![image3](common/idp-intiated.png)

    > [!Note]
    > Si les valeurs **Identificateur** et **URL de réponse** ne sont pas automatiquement renseignées, renseignez-les manuellement en fonction de vos besoins.

1. L’application Tulip s’attend à recevoir les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à votre configuration des attributs du jeton SAML. La capture d’écran suivante montre la liste des attributs par défaut.

    ![image](common/default-attributes.png)

1. En plus de ce qui précède, l’application Tulip s’attend à ce que quelques attributs supplémentaires, indiqués ci-après, soient passés dans la réponse SAML. Ces attributs sont également préremplis, mais vous pouvez les examiner pour voir s’ils répondent à vos besoins.
    
    | Nom |Attribut source|
    | -------------- | --------- |
    | displayName | user.displayname |
    | emailAddress |user.mail |
    | badgeID | user.employeeid |
    | groups |user.groups |


1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **Certificat (en base64)** , puis sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

1. Dans la section **Configurer Tulip**, copiez la ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Tulip.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Tulip**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-tulip-sso"></a>Configurer l’authentification unique de Tulip

1. Connectez-vous à votre site Tulip en tant qu’administrateur.

1. Accédez à **Paramètres** -> **SAML** et effectuez les étapes suivantes dans la page ci-dessous.

    ![Capture d’écran de la configuration de Tulip.](./media/tulip-tutorial/configuration.png)

    a. **Activez les connexions SAML**. 

    b. Cliquez sur **fichier XML de métadonnées** pour télécharger le **fichier de métadonnées du fournisseur de services** et utilisez ce fichier pour le télécharger dans la section de **Configuration SAML de base** du portail Azure.

    c. Dans la zone de texte **URL d’authentification unique**, collez la valeur d’**URL de connexion** que vous avez copiée à partir du portail Azure.

    d. Dans la zone de texte **URL de déconnexion de l’authentification unique**, collez la valeur de l’**URL de déconnexion** que vous avez copiée dans le portail Azure.

    e. Ouvrez le **Certificat (Base64)** téléchargé à partir du portail Azure dans le Bloc-notes et collez le contenu dans la zone de texte **Certificats**.

    f. Dans **Mappage d'attributs**,
    
     * donnez à la valeur **Attribut de nom** **displayName**.

     * donnez à la valeur **Attribut d’e-mail** **emailAdress**.

     * donnez à la valeur **Attribut de badge** **badgeID**.

     * donnez à la valeur **Attribut de rôle** **groups**.

    g. Dans **Mappage de rôle**, mappez l’ObjectID du groupe avec le propriétaire du compte.

    h. Cliquez sur **Enregistrer la configuration SAML**.

### <a name="create-tulip-test-user"></a>Créer un utilisateur test pour Tulip

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans Tulip. Collaborez avec l’[équipe du support technique Tulip](mailto:support@tulip.co) pour ajouter les utilisateurs à la plateforme Tulip. Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes.

* Cliquez sur Tester cette application dans le portail Azure : vous devez être connecté automatiquement à l’instance de Tulip pour laquelle vous avez configuré l’authentification unique

* Vous pouvez utiliser Mes applications de Microsoft. Quand vous cliquez sur la vignette Tulip dans Mes applications, vous devez vous connecter automatiquement à l’application Tulip pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez configuré Tulip, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
