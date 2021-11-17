---
title: 'Tutoriel : Intégration de l’authentification unique Azure Active Directory à ServiceChannel | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et ServiceChannel.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/18/2020
ms.author: jeedes
ms.openlocfilehash: c5372588f2bf3dc6b7e720ecf42f1ecf3e50364c
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132299251"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-servicechannel"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory à ServiceChannel

Dans ce tutoriel, vous découvrez comment intégrer ServiceChannel à Azure Active Directory (Azure AD). Quand vous intégrez ServiceChannel à Azure AD, vous pouvez :

- Contrôler dans Azure AD qui a accès à ServiceChannel.
- Permettre à vos utilisateurs de se connecter automatiquement à ServiceChannel avec leur compte Azure AD.
- Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

- Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
- Un abonnement ServiceChannel pour lequel l’authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

- ServiceChannel prend en charge l’authentification unique initiée par le **point de distribution d’émission**
- ServiceChannel prend en charge l’attribution d’utilisateurs **juste-à-temps**.

## <a name="adding-servicechannel-from-the-gallery"></a>Ajout de ServiceChannel à partir de la galerie

Pour configurer l’intégration de ServiceChannel à Azure AD, vous devez ajouter ServiceChannel à votre liste d’applications SaaS gérées à partir de la galerie.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **ServiceChannel** dans la zone de recherche.
1. Sélectionnez **ServiceChannel** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-servicechannel"></a>Configurer et tester l’authentification unique Azure AD pour ServiceChannel

Configurez et testez l’authentification unique Azure AD avec ServiceChannel pour un utilisateur de test nommé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur ServiceChannel associé.

Pour configurer et tester l’authentification unique Azure AD auprès de ServiceChannel, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
   1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
   1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique ServiceChannel](#configure-servicechannel-sso)** pour configurer les paramètres de l’authentification unique côté application.
   1. **[Créer un utilisateur de test ServiceChannel](#create-servicechannel-test-user)** pour avoir un équivalent de B.Simon dans ServiceChannel lié à la représentation de l’utilisateur dans Azure AD.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le Portail Azure, accédez à la page d’intégration de l’application **ServiceChannel**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la page **Configurer l’authentification unique avec SAML**, entrez les valeurs pour les champs suivants :

   a. Dans la zone de texte **Identificateur**, tapez la valeur : `http://adfs.<domain>.com/adfs/service/trust`

   b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://<customer domain>.servicechannel.com/saml/acs`

   > [!NOTE]
   > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur et l’URL de réponse réels. Nous vous suggérons d’utiliser ici la valeur de chaîne unique dans l’identificateur. Contactez l’[équipe de support technique client de ServiceChannel](https://servicechannel.zendesk.com/hc/) pour obtenir ces valeurs. Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. La revendication de rôle est préconfigurée. Vous n’avez donc pas à la configurer, mais vous devez toujours créer les rôles dans Azure AD en suivant les instructions de cet [article](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui). Vous pouvez consulter le guide de ServiceChannel [ici](https://servicechannel.zendesk.com/hc/articles/217514326-Azure-AD-Configuration-Example) pour obtenir des instructions sur les revendications.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **Certificat (Base64)** , puis sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

   ![Lien Téléchargement de certificat](common/certificatebase64.png)

1. Dans la section **Configurer ServiceChannel**, copiez la ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à ServiceChannel.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **ServiceChannel**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous avez configuré les rôles comme indiqué ci-dessus, vous pouvez en sélectionner un dans la liste déroulante **Sélectionner un rôle**.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-servicechannel-sso"></a>Configurer l’authentification unique ServiceChannel

Pour configurer l’authentification unique côté **ServiceChannel**, vous devez envoyer le **certificat (Base64)** téléchargé et les URL copiées appropriées du portail Azure à l’[équipe du support ServiceChannel](https://servicechannel.zendesk.com/hc/). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

### <a name="create-servicechannel-test-user"></a>Créer un utilisateur de test ServiceChannel

L’application prend en charge la configuration d’utilisateur juste à temps, et après authentification, les utilisateurs sont créés automatiquement dans l’application. Pour le provisionnement complet de l’utilisateur, contactez l’[équipe de support ServiceChannel](https://servicechannel.zendesk.com/hc/).

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes.

- Dans le portail Azure, cliquez sur Tester cette application. Vous êtes alors automatiquement connecté à l’instance de ServiceChannel pour laquelle vous avez configuré l’authentification unique.

- Vous pouvez utiliser Mes applications de Microsoft. Lorsque vous cliquez sur la vignette ServiceChannel dans Mes applications, vous devez être connecté automatiquement à ServiceChannel pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré ServiceChannel, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
