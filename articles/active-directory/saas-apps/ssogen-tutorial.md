---
title: 'Tutoriel : Intégration de l’authentification unique (SSO) Azure Active Directory à SSOGEN - Azure AD SSO Gateway pour Oracle E-Business Suite - EBS, PeopleSoft et JDE | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et SSOGEN - Azure AD SSO Gateway pour Oracle E-Business Suite - EBS, PeopleSoft et JDE.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/11/2021
ms.author: jeedes
ms.openlocfilehash: 36e41163f2d2c404b54a9dd9f0daf4cc7caf6c74
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132299059"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ssogen---azure-ad-sso-gateway-for-oracle-e-business-suite---ebs-peoplesoft-and-jde"></a>Tutoriel : Intégration de l’authentification unique (SSO) Azure Active Directory à SSOGEN - Azure AD SSO Gateway pour Oracle E-Business Suite - EBS, PeopleSoft et JDE

Dans ce tutoriel, vous allez apprendre à intégrer SSOGEN - Azure AD SSO Gateway pour Oracle E-Business Suite - EBS, PeopleSoft et JDE avec Azure Active Directory (Azure AD). Quand vous intégrez SSOGEN - Azure AD SSO Gateway pour Oracle E-Business Suite - EBS, PeopleSoft et JDE à Azure AD, vous pouvez :

* Contrôler, dans Azure AD, qui a accès à SSOGEN - Azure AD SSO Gateway pour Oracle E-Business Suite - EBS, PeopleSoft et JDE.
* Permettre à vos utilisateurs d’être automatiquement connectés à SSOGEN - Azure AD SSO Gateway pour Oracle E-Business Suite - EBS, PeopleSoft et JDE avec leurs comptes Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement à SSOGEN - Azure AD SSO Gateway pour Oracle E-Business Suite - EBS, PeopleSoft et JDE pour lequel l’authentification unique (SSO) est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* SSOGEN - Azure AD SSO Gateway pour Oracle E-Business Suite - EBS, PeopleSoft et JDE prend en charge l’authentification unique lancée par **le fournisseur de services et le fournisseur d’identité**.

> [!NOTE]
> L’identificateur de cette application étant une valeur de chaîne fixe, une seule instance peut être configurée dans un locataire.

## <a name="add-ssogen---azure-ad-sso-gateway-for-oracle-e-business-suite---ebs-peoplesoft-and-jde-from-the-gallery"></a>Ajouter SSOGEN - Azure AD SSO Gateway pour Oracle E-Business Suite - EBS, PeopleSoft et JDE à partir de la galerie

Pour configurer l’intégration de SSOGEN - Azure AD SSO Gateway pour Oracle E-Business Suite - EBS, PeopleSoft et JDE à Azure AD, vous devez ajouter SSOGEN - Azure AD SSO Gateway pour Oracle E-Business Suite - EBS, PeopleSoft et JDE à partir de la galerie à votre liste d’applications SaaS managées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **SSOGEN - Azure AD SSO Gateway pour Oracle E-Business Suite - EBS, PeopleSoft et JDE** dans la zone de recherche.
1. Sélectionnez **SSOGEN - Azure AD SSO Gateway pour Oracle E-Business Suite - EBS, PeopleSoft et JDE** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-ssogen---azure-ad-sso-gateway-for-oracle-e-business-suite---ebs-peoplesoft-and-jde"></a>Configurer et tester l’authentification unique Azure AD pour SSOGEN - Azure AD SSO Gateway pour Oracle E-Business Suite - EBS, PeopleSoft et JDE

Configurez et testez l’authentification unique Azure AD auprès de SSOGEN - Azure AD SSO Gateway pour Oracle E-Business Suite - EBS, PeopleSoft et JDE à l’aide d’un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir une relation entre un utilisateur Azure AD et l’utilisateur associé dans SSOGEN - Azure AD SSO Gateway pour Oracle E-Business Suite - EBS, PeopleSoft et JDE.

Pour configurer et tester l’authentification unique Azure AD avec SSOGEN - Azure AD SSO Gateway pour Oracle E-Business Suite - EBS, PeopleSoft et JDE, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique SSOGEN - Azure AD SSO Gateway pour Oracle E-Business Suite - EBS, PeopleSoft et JDE](#configure-ssogen---azure-ad-sso-gateway-for-oracle-e-business-suite---ebs-peoplesoft-and-jde-sso)** pour configurer les paramètres d’authentification unique côté application.
    1. **[Créer un utilisateur de test SSOGEN - Azure AD SSO Gateway pour Oracle E-Business Suite - EBS, PeopleSoft et JDE](#create-ssogen---azure-ad-sso-gateway-for-oracle-e-business-suite---ebs-peoplesoft-and-jde-test-user)** pour avoir un équivalent de B.Simon dans SSOGEN - Azure AD SSO Gateway pour Oracle E-Business Suite - EBS, PeopleSoft et JDE lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, dans la page d’intégration de l’application **SSOGEN - Azure AD SSO Gateway pour Oracle E-Business Suite - EBS, PeopleSoft et JDE**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, si vous souhaitez configurer l’application en mode Initié par le **fournisseur d’identité**, entrez les valeurs pour les champs suivants :

    Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://<customer_name>.ssogen.com/ssogen/login?client_name=<customer_name>`

1. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<customer_name>.ssogen.com/ssogen/login?client_name=<customer_name>`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de réponse et l’URL de connexion réelles. Pour obtenir ces valeurs, contactez l’[ équipe du support technique du client SSOGEN - Azure AD SSO Gateway pour Oracle E-Business Suite - EBS, PeopleSoft et JDE](mailto:support@ssogen.com). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. Votre application SSOGEN - Azure AD SSO Gateway pour Oracle E-Business Suite - EBS, PeopleSoft et JDE attend les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à votre configuration des attributs de jeton SAML. La capture d’écran suivante montre la liste des attributs par défaut, où **nameidentifier** est mappé avec **user.userprincipalname**. L’application SSOGEN - Azure AD SSO Gateway pour Oracle E-Business Suite - EBS, PeopleSoft et JDE attend le mappage de **nameidentifier** à **user.onpremisessamaccountname**. Vous devez donc modifier le mappage d’attribut en cliquant sur l’icône **Modifier** pour ce faire.

    ![image](common/edit-attribute.png)

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur le bouton Copier pour copier l’**URL des métadonnées de fédération d’application**, puis enregistrez-la sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/copy-metadataurl.png)

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à SSOGEN - Azure AD SSO Gateway pour Oracle E-Business Suite - EBS, PeopleSoft et JDE.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **SSOGEN - Azure AD SSO Gateway pour Oracle E-Business Suite - EBS, PeopleSoft et JDE**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-ssogen---azure-ad-sso-gateway-for-oracle-e-business-suite---ebs-peoplesoft-and-jde-sso"></a>Configurer l’authentification unique SSOGEN - Azure AD SSO Gateway pour Oracle E-Business Suite - EBS, PeopleSoft et JDE

Pour configurer l’authentification unique côté **SSOGEN - Azure AD SSO Gateway pour Oracle E-Business Suite - EBS, PeopleSoft et JDE**, consultez la documentation ci-dessous :

* Oracle EBS - Intégration de l’authentification unique Azure AD : [https://www.ssogen.com/oracle-ebs-sso-ldap/](https://www.ssogen.com/oracle-ebs-sso-ldap/)
* PeopleSoft - Intégration de l’authentification unique Azure AD : [https://www.ssogen.com/peoplesoft-sso/](https://www.ssogen.com/peoplesoft-sso/)
* JD Edwards - Intégration de l’authentification unique Azure AD : [https://www.ssogen.com/oracle-jde-sso/](https://www.ssogen.com/oracle-jde-sso/)
* Apache - Intégration de l’authentification unique Azure AD : [https://www.ssogen.com/apache-sso-authentication/](https://www.ssogen.com/apache-sso-authentication/)

### <a name="create-ssogen---azure-ad-sso-gateway-for-oracle-e-business-suite---ebs-peoplesoft-and-jde-test-user"></a>Créer un utilisateur de test SSOGEN - Azure AD SSO Gateway pour Oracle E-Business Suite - EBS, PeopleSoft et JDE

Azure AD envoie un identificateur d’utilisateur unique (ID de nom) à l’application utilisateur, une fois l’authentification réussie.  Vérifiez que l’identificateur d’utilisateur unique (ID de nom) correspond à l’enregistrement utilisateur dans votre application, à savoir FND_USER.USER_NAME dans Oracle EBS, par exemple.

Pour tout support technique, contactez [info@ssogen.com](mailto:info@ssogen.com) et [support@ssogen.com](mailto:support@ssogen.com).

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

#### <a name="sp-initiated"></a>Lancée par le fournisseur de services :

* Cliquez sur **Tester cette application** dans le portail Azure. Cette opération redirige vers l’URL de connexion à SSOGEN - Azure AD SSO Gateway pour Oracle E-Business Suite - EBS, PeopleSoft et JDE, où vous pouvez lancer le flux de connexion.  

* Accédez directement à l’URL de connexion à SSOGEN - Azure AD SSO Gateway pour Oracle E-Business Suite - EBS, PeopleSoft et JDE, puis lancez le flux de connexion à partir de là.

#### <a name="idp-initiated"></a>Lancée par le fournisseur d’identité :

* Cliquez sur **Tester cette application** dans le portail Azure. Vous devez être connecté automatiquement à l’instance de SSOGEN - Azure AD SSO Gateway pour Oracle E-Business Suite - EBS, PeopleSoft et JDE pour laquelle vous avez configuré l’authentification unique. 

Vous pouvez aussi utiliser Mes applications de Microsoft pour tester l’application dans n’importe quel mode. Si, quand vous cliquez sur la vignette SSOGEN - Azure AD SSO Gateway pour Oracle E-Business Suite - EBS, PeopleSoft et JDE dans Mes applications, le mode Fournisseur de services est configuré, vous êtes redirigé vers la page de connexion de l’application pour lancer le flux de connexion ; s’il s’agit du mode Fournisseur d’identité, vous êtes automatiquement connecté à l’application SSOGEN - Azure AD SSO Gateway pour Oracle E-Business Suite - EBS, PeopleSoft et JDE pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré SSOGEN - Azure AD SSO Gateway pour Oracle E-Business Suite - EBS, PeopleSoft et JDE, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
