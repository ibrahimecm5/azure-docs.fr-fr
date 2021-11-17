---
title: 'Tutoriel : Intégration de l’authentification unique (SSO) Azure Active Directory à Autodesk SSO | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Autodesk SSO.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/20/2021
ms.author: jeedes
ms.openlocfilehash: 32e4b30ae95803c898e3189ebdbe9283513dd9e0
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132324390"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-autodesk-sso"></a>Tutoriel : Intégration de l’authentification unique (SSO) Azure Active Directory à Autodesk SSO

Dans ce tutoriel, vous allez découvrir comment intégrer Autodesk SSO à Azure Active Directory (Azure AD). Quand vous intégrez Autodesk SSO à Azure AD, vous pouvez :

* Contrôler, dans Azure AD, qui a accès à Autodesk SSO.
* Permettre aux utilisateurs de se connecter automatiquement à Autodesk SSO avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Autodesk SSO pour lequel l’authentification SSO (authentification unique) est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* Autodesk SSO prend en charge l’authentification SSO lancée par le fournisseur de services (**SP**).

* Autodesk SSO prend en charge le provisionnement d’utilisateurs **juste-à-temps**.


## <a name="adding-autodesk-sso-from-the-gallery"></a>Ajout d’Autodesk SSO à partir de la galerie

Pour configurer l’intégration d’Autodesk SSO à Azure AD, vous devez ajouter Autodesk SSO à votre liste d’applications SaaS managées, à partir de la galerie.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, dans la zone de recherche, tapez **Autodesk SSO**.
1. Sélectionnez **Autodesk SSO** dans le panneau Résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.


## <a name="configure-and-test-azure-ad-sso-for-autodesk-sso"></a>Configurer et tester l’authentification SSO Azure AD pour Autodesk SSO

Configurez et testez l’authentification SSO Azure AD avec Autodesk SSO pour une utilisatrice de test appelée **B.Simon**. Pour que l’authentification SSO fonctionne, vous devez établir une relation entre un utilisateur Azure AD et l’utilisateur associé dans Autodesk SSO.

Pour configurer et tester l’authentification SSO Azure AD avec Autodesk SSO, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Autodesk](#configure-autodesk-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer une utilisatrice de test pour Autodesk SSO](#create-autodesk-sso-test-user)** afin de disposer dans Autodesk SSO d’un équivalent de B.Simon lié à la représentation Azure AD de l’utilisatrice.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, dans la page d’intégration de l’application **Autodesk SSO**, recherchez la section **Gérer**, puis sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, entrez les valeurs pour les champs suivants :

    a. Dans la zone de texte **Identificateur (ID d’entité)** , saisissez une URL au format suivant : `https://www.okta.com/saml2/service-provider/<UNIQUE_ID>`
    
    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://autodesk-prod.okta.com/sso/saml2/<UNIQUE_ID>`

    c. Dans la zone de texte **URL de connexion**, tapez l’URL : `https://autodesk-prod.okta.com/sso/saml2/`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur et l’URL de réponse réels. Pour obtenir ces valeurs, contactez l’[équipe du support technique d’Autodesk SSO](https://knowledge.autodesk.com/contact-support). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. L’application Autodesk SSO attend les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à votre configuration d’attributs de jetons SAML. La capture d’écran suivante montre la liste des attributs par défaut.

    ![image](common/default-attributes.png)

1. En plus de ce qui précède, l’application Autodesk SSO s’attend à ce que quelques attributs supplémentaires soient repassés dans la réponse SAML, comme indiqué ci-dessous. Ces attributs sont également préremplis, mais vous pouvez les examiner pour voir s’ils répondent à vos besoins.
    
    | Nom |  Attribut source|
    | -------------- | --------- |
    | firstName | user.givenname |
    | lastName | user.surname |
    | objectGUID | user.objectid |
    | email | user.mail |

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **Certificat (en base64)** , puis sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

1. Dans la section **Configurer Autodesk SSO**, copiez l’URL ou les URL appropriées selon vos besoins.

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

Dans cette section, vous allez permettre à B.Simon d’utiliser l’authentification unique Azure en lui octroyant l’accès à Autodesk SSO.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Autodesk SSO**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-autodesk-sso"></a>Configurer l’authentification unique Autodesk

Pour configurer l’authentification unique côté **Autodesk SSO**, vous devez envoyer le **certificat (Base64)** téléchargé et les URL copiées appropriées à partir du portail Azure à l’[équipe du support technique d’Autodesk SSO](https://knowledge.autodesk.com/contact-support). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

### <a name="create-autodesk-sso-test-user"></a>Créer l’utilisateur de test Autodesk SSO

Dans cette section, une utilisatrice appelée Britta Simon est créée dans Autodesk SSO. Autodesk SSO prend en charge le provisionnement d’utilisateurs juste-à-temps, qui est activé par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas encore d’utilisateur dans Autodesk SSO, celui-ci est créé après l’authentification.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Pour tester Autodesk SSO, ouvrez la console Autodesk, cliquez sur le bouton **Test Connection** (Tester la connexion), puis authentifiez-vous à l’aide du compte de test que vous avez créé dans la section **Créer un utilisateur de test Azure AD**.

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez configuré Autodesk SSO, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
