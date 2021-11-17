---
title: 'Tutoriel : Intégration d’Azure Active Directory à GoToMeeting | Microsoft Docs'
description: Découvrez les étapes à effectuer pour intégrer GoToMeeting à Azure Active Directory (Azure AD).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/20/2021
ms.author: jeedes
ms.openlocfilehash: 918a78006b53a47253fa36a3b9a38e6de7993db1
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132307761"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-gotomeeting"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory à GoToMeeting

Dans ce tutoriel, vous allez apprendre à intégrer GoToMeeting à Azure Active Directory (Azure AD). Quand vous intégrez GoToMeeting à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à GoToMeeting.
* Permettre aux utilisateurs de se connecter automatiquement à GoToMeeting avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement GoToMeeting pour lequel l’authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* GoToMeeting prend en charge l’authentification unique lancée par le **fournisseur d’identité**.
* GoToMeeting prend en charge l’[attribution automatisée d’utilisateurs](citrixgotomeeting-provisioning-tutorial.md).

> [!NOTE]
> L’identificateur de cette application étant une valeur de chaîne fixe, une seule instance peut être configurée dans un locataire.

## <a name="add-gotomeeting-from-the-gallery"></a>Ajouter GoToMeeting à partir de la galerie

Pour configurer l’intégration de GoToMeeting à Azure AD, vous devez ajouter GoToMeeting, disponible dans la galerie, à votre liste d’applications SaaS managées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **GoToMeeting** dans la zone de recherche.
1. Sélectionnez **GoToMeeting** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-gotomeeting"></a>Configurer et tester l’authentification unique Azure AD pour GoToMeeting

Configurez et testez l’authentification unique Azure AD avec GoToMeeting à l’aide d’un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur GoToMeeting associé.

Pour configurer et tester l’authentification unique Azure AD auprès de GoToMeeting, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique GoToMeeting](#configure-gotomeeting-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test GoToMeeting](#create-gotomeeting-test-user)** pour avoir un équivalent de B.Simon dans GoToMeeting lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **GoToMeeting**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    a. Dans la zone de texte **Identificateur**, tapez une l’URL : `https://authentication.logmeininc.com/saml/sp`

    b. Dans la zone **URL de réponse**, tapez l’URL : `https://authentication.logmeininc.com/saml/acs`

    c. Cliquez sur **Définir des URL supplémentaires** et configurez les URL ci-après.

    d. **URL de connexion** (gardez cela vide)

    e. Dans la zone de texte **RelayState** (État de relais), tapez l’une des URL suivantes :

   - Pour l’application GoToMeeting, utilisez `https://global.gotomeeting.com`

   - Pour GoToTraining, utilisez `https://global.gototraining.com`

   - Pour GoToWebinar, utilisez `https://global.gotowebinar.com` 

   - Pour GoToAssist, utilisez `https://app.gotoassist.com`

5. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (Base64)** en fonction des options définies par rapport à vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

6. Dans la section **Configurer GoToMeeting**, copiez la ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à GoToMeeting.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **GoToMeeting**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-gotomeeting-sso"></a>Configurer l’authentification unique GoToMeeting

1. Dans une fenêtre différente du navigateur, connectez-vous à votre [Centre d’organisation GoToMeeting](https://organization.logmeininc.com/). Vous êtes invité à confirmer que le fournisseur d’identité a été mis à jour.

2. Activez la case à cocher « Mon fournisseur d’identité a été mis à jour avec le nouveau domaine ». Lorsque vous avez terminé, cliquez sur **Terminé**.

### <a name="create-gotomeeting-test-user"></a>Créer un utilisateur de test GoToMeeting

Dans cette section, un utilisateur appelé Britta Simon est créé dans GoToMeeting. GoToMeeting prend en charge le provisionnement juste-à-temps, option activée par défaut.

Vous n’avez aucune opération à effectuer dans cette section. Si un utilisateur n’existe pas dans GoToMeeting, un nouvel utilisateur est créé lorsque vous tentez d’accéder à GoToMeeting.

> [!NOTE]
> Si vous devez créer un utilisateur manuellement, contactez [l’équipe du support GoToMeeting](https://support.logmeininc.com/gotomeeting).

> [!NOTE]
>GoToMeeting prend également en charge l’attribution automatique d’utilisateurs. Vous trouverez plus d’informations sur la configuration de cette fonctionnalité [ici](./citrixgotomeeting-provisioning-tutorial.md).

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes.

* Cliquez sur Tester cette application dans le portail Azure : vous devez être connecté automatiquement à l’instance de GoToMeeting pour laquelle vous avez configuré l’authentification unique.

* Vous pouvez utiliser Mes applications de Microsoft. Quand vous cliquez sur la vignette GoToMeeting dans Mes applications, vous devez être connecté automatiquement à l’application GoToMeeting pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez configuré GoToMeeting, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
