---
title: 'Tutoriel : Intégration de l’authentification unique Azure AD à embed signage'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et embed signage.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/01/2021
ms.author: jeedes
ms.openlocfilehash: 6f5a8742e831be54073cf23d820706be1df7fb2e
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132330353"
---
# <a name="tutorial-azure-ad-sso-integration-with-embed-signage"></a>Tutoriel : Intégration de l’authentification unique Azure AD à embed signage

Dans ce tutoriel, vous allez apprendre à intégrer Azure Active Directory (Azure AD) à embed signage. Quand vous intégrez Azure AD à embed signage, vous pouvez :

* Contrôler dans Azure AD qui a accès à embed signage.
* Permettre à vos utilisateurs de se connecter automatiquement à embed signage avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement embed signage pour lequel l’authentification unique (SSO) est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* embed signage prend en charge l’authentification unique lancée par le **fournisseur d’identité**.

## <a name="add-embed-signage-from-the-gallery"></a>Ajouter embed signage à partir de la galerie

Pour configurer l’intégration d’embed signage avec Azure AD, vous devez ajouter embed signage à partir de la galerie à votre liste d’applications SaaS managées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **embed signage** dans la zone de recherche.
1. Sélectionnez **embed signage** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-embed-signage"></a>Configurer et tester l’authentification unique Azure AD pour embed signage

Configurez et testez l’authentification unique Azure AD avec embed signage pour un utilisateur test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur embed signage associé.

Pour configurer et tester l’authentification unique Azure AD avec embed signage, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique d’embed signage](#configure-embed-signage-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur test embed signage](#create-embed-signage-test-user)** pour disposer, dans embed signage, d’un équivalent de B.Simon lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **embed signage**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la page **Configuration SAML de base**, effectuez les étapes suivantes :

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://app.embedsignage.com/auth/saml/<ID>`

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://app.embedsignage.com/auth/saml/login/<ID>`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur et l’URL de réponse réels. Pour obtenir ces valeurs, contactez [l’équipe de support d’embed signage](mailto:support@embedsignage.com). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **XML de métadonnées de fédération** et sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

1. Dans la section **Configurer embed signage**, copiez la ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à embed signage.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **embed signage**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-embed-signage-sso"></a>Configurer l’authentification unique d’embed signage

1. Connectez-vous à votre site d’entreprise embed signage en tant qu’administrateur.

1. Accédez à **Account settings** et cliquez sur **Security** > **Single sign on**.

1. Dans la section **Single sign on**, effectuez les étapes suivantes :

    ![Capture d’écran montrant le compte SSO.](./media/embed-signage-tutorial/settings.png "Compte SSO")

    1. **Activez** la case à cocher Single sign on.

    1. Ouvrez le fichier **XML de métadonnées de fédération** téléchargé à partir du portail Azure, puis chargez le fichier dans le **fichier XML de métadonnées**.

    1. Cliquez sur **Enregistrer les modifications**.

### <a name="create-embed-signage-test-user"></a>Créer un utilisateur test embed signage

1. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise embed signage en tant qu’administrateur.

1. Accédez à **Account settings** et cliquez sur **Users** > **New User**.

1. Dans la section **Settings**, renseignez manuellement les champs obligatoires dans la page suivante et cliquez sur **Create User**.  

    ![Capture d’écran montrant comment créer des utilisateurs.](./media/embed-signage-tutorial/account.png "Utilisateurs SSO")

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes.

* Cliquez sur Tester cette application dans le portail Azure, ce qui devrait vous connecter automatiquement à l’application embed signage pour laquelle vous avez configuré l’authentification unique.

* Vous pouvez utiliser Mes applications de Microsoft. Quand vous cliquez sur la vignette embed signage dans Mes applications, vous êtes connecté automatiquement à l’application embed signage pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez configuré embed signage, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
