---
title: 'Tutoriel : Intégration de l’authentification unique Azure Active Directory à Ally.io | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Ally.io.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/03/2021
ms.author: jeedes
ms.openlocfilehash: 4b0e8e12d8e7c0052941a6ccae94b1a18dc8b7df
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132330632"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-allyio"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory à Ally.io

Dans ce tutoriel, vous découvrez comment intégrer Ally.io à Azure Active Directory (Azure AD). Quand vous intégrez Ally.io à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Ally.io.
* Permettre à vos utilisateurs de se connecter automatiquement à Ally.io avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Ally.io pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* Ally.io prend en charge l'authentification unique lancée par **le fournisseur de services et le fournisseur d'identité**.
* Ally.io prend en charge l'approvisionnement d'utilisateurs **juste-à-temps**.

## <a name="add-allyio-from-the-gallery"></a>Ajouter Ally.io à partir de la galerie

Pour configurer l’intégration d’Ally.io à Azure AD, vous devez ajouter Ally.io à partir de la galerie à votre liste d’applications SaaS managées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Ally.io** dans la zone de recherche.
1. Sélectionnez **Ally.io** dans le volet des résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-allyio"></a>Configurer et tester l'authentification unique Azure AD pour Ally.io

Configurez et testez l’authentification unique Azure AD avec Ally.io à l’aide d’un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Ally.io associé.

Pour configurer et tester l'authentification SSO Azure AD auprès d'Ally.io, suivez les étapes ci-dessous :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Ally.io](#configure-allyio-sso)** pour configurer les paramètres de l’authentification unique côté application.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Sur le portail Azure, accédez à la page d'intégration de l'application **Ally.io**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, si vous souhaitez configurer l’application en mode Initié par le **fournisseur d’identité**, entrez les valeurs pour les champs suivants :

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://app.ally.io/saml/consume/<CUSTOM_GUID>`

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://app.ally.io/saml/consume/<CUSTOM_GUID>`

1. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    Dans la zone de texte **URL de connexion**, tapez l’URL : `https://app.ally.io/`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur et l’URL de réponse réels. Pour obtenir ces valeurs, contactez l’[équipe du support technique Ally.io](mailto:contact@ally.io). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. L’application Ally.io s’attend à recevoir les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à votre configuration d’attributs de jetons SAML. La capture d’écran suivante montre la liste des attributs par défaut.

    ![Capture d’écran montrant la liste d’attributs par défaut.](common/default-attributes.png)

1. En plus de ce qui précède, l’application Ally.io s’attend à ce que quelques attributs supplémentaires (présentés ci-dessous) soient repassés dans la réponse SAML. Ces attributs sont également préremplis, mais vous pouvez les examiner pour voir s’ils répondent à vos besoins.
    
    | Nom |  Attribut source|
    | --------------- | --------- |
    | email | user.userprincipalname |
    | firstName | user.givenname |
    | lastName | user.surname |

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **Certificat (en base64)** , puis sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

1. Dans la section **Configurer Ally.io**, copiez la ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Ally.io.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Ally.io**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-allyio-sso"></a>Configurer l’authentification unique Ally.io

Pour configurer l’authentification unique côté Ally.io, vous devez copier le certificat (en base64) et les URL appropriées à partir du portail Azure, et les ajouter dans Ally.io.

1. Connectez-vous à Ally.io en utilisant un compte d’administrateur.
1. En utilisant la barre de navigation à gauche de l’écran, sélectionnez **Administrateur** > **Intégrations**.
1. Faites défiler jusqu’à la section **Authentication** et sélectionnez **Authentification unique**. Ensuite, sélectionnez **Activer**.

    ![Capture d’écran montrant le bouton Activer dans Ally I O.](./media/ally-tutorial/ally-enable.png)

    La page **Configuration de l’authentification unique** s’ouvre, où vous pouvez configurer le certificat et les URL copiées à partir du portail Azure.

    ![Capture d’écran montrant le volet de configuration de l’authentification unique dans Ally I O.](./media/ally-tutorial/ally-single-sign-on-configuration.png)

1. Dans **SSO Configuration**, entrez ou sélectionnez les paramètres suivants : 

    * **Ally** : Azure AD
    * **SAML 2.0 Endpoint URL** : URL de connexion
    * **Identity Provider Issuer URL** : Identificateur Azure AD
    * **Public (X.509) Certificate** : Certificat (en base64)

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

#### <a name="sp-initiated"></a>Lancée par le fournisseur de services :

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l'URL de connexion à Ally.io, à partir de laquelle vous pouvez lancer le flux de connexion.  

* Accédez directement à l'URL de connexion à Ally.io pour lancer le flux de connexion.

#### <a name="idp-initiated"></a>Lancée par le fournisseur d’identité :

* Cliquez sur **Tester cette application** sur le portail Azure. Vous êtes alors automatiquement connecté à l'instance d'Ally.io pour laquelle vous avez configuré l'authentification unique. 

Vous pouvez aussi utiliser Mes applications de Microsoft pour tester l’application dans n’importe quel mode. Si, lorsque vous cliquez sur la vignette Ally.io dans Mes applications, le mode Fournisseur de services est configuré, vous êtes redirigé vers la page de connexion de l'application pour lancer le flux de connexion ; s'il s'agit du mode Fournisseur d'identité, vous êtes automatiquement connecté à l'instance d'Ally.io pour laquelle vous avez configuré l'authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré Ally.io, vous pouvez appliquer le contrôle de session, qui protège en temps réel contre l'exfiltration et l'infiltration des données sensibles de votre organisation. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
