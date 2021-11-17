---
title: 'Didacticiel : intégration d’Azure Active Directory à Learning Pool LMS | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Learning Pool LMS.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/06/2021
ms.author: jeedes
ms.openlocfilehash: ab0ada258b8df3ccca7086f2d8c84a2b4b1ee336
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132313465"
---
# <a name="tutorial-azure-active-directory-integration-with-learning-pool-lms"></a>Didacticiel : intégration d’Azure Active Directory à Learning Pool LMS

Dans ce tutoriel, vous allez apprendre à intégrer Learning Pool LMS à Azure Active Directory (Azure AD). Lorsque vous intégrez Learning Pool LMS à Azure AD, vous pouvez :

* Contrôler, dans Azure AD, qui a accès à Learning Pool LMS.
* Permettre à vos utilisateurs de se connecter automatiquement à Learning Pool LMS avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement actif à Learning Pool LMS avec l’authentification unique.

> [!NOTE]
> Quand vous démarrez un projet d’authentification unique, un membre de l’équipe Learning Pool LMS vous guide tout au long de ce processus. Si vous n’êtes pas en contact avec un membre de l’équipe Learning Pool LMS, contactez votre responsable de compte Learning Pool LMS.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* Learning Pool LMS prend en charge l’authentification SSO lancée par le fournisseur de services (**SP**).

## <a name="adding-learning-pool-lms-from-the-gallery"></a>Ajout de Learning Pool LMS à partir de la galerie

Pour configurer l’intégration de Learning Pool LMS à Azure AD, vous devez ajouter Learning Pool LMS à partir de la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Learning Pool LMS** dans la zone de recherche.
1. Sélectionnez **Learning Pool LMS** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-learning-pool-lms"></a>Configurer et tester l’authentification SSO Azure AD pour Learning Pool LMS

Configurez et testez Azure AD SSO avec Learning Pool LMS pour un utilisateur Azure existant. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Learning Pool LMS associé.

Pour configurer et tester l’authentification SSO Azure AD avec Learning Pool LMS, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
1. **[Affecter l’utilisateur Azure AD](#assign-an-azure-ad-user)** pour permettre à cet utilisateur d’employer l’authentification unique Azure AD.
1. **[Configurer Learning Pool LMS](#configure-learning-pool-lms-sso)** pour configurer les paramètres de l’authentification unique côté application.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le Portail Azure, accédez à la page d’intégration de l’application **Learning Pool LMS**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

    ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, si vous disposez d’un **fichier de métadonnées du fournisseur de services**, suivez les étapes ci-dessous :

    a. Cliquez sur **Charger un fichier de métadonnées**.

    ![Charger le fichier de métadonnées](common/upload-metadata.png)

    b. Cliquez sur le **logo du dossier** pour sélectionner le fichier de métadonnées, puis cliquez sur **Charger**.

    ![choisir le fichier de métadonnées](common/browse-upload-metadata.png)

    c. Une fois le fichier de métadonnées correctement chargé, la valeur **Identificateur** est automatiquement renseignée dans la section Configuration SAML de base.

    Dans la zone de texte **URL de connexion**, tapez l’URL : `https://parliament.preview.Learningpool.com/auth/shibboleth/index.php`

    > [!Note]
    > Si la valeur **Identificateur** n’est pas automatiquement renseignée, renseignez-la manuellement.

5. Vous devez envoyer au moins un attribut qui est utilisé pour faire correspondre vos utilisateurs Azure avec les utilisateurs dans Learning Pool LMS. Normalement, les attributs par défaut sont suffisants, mais dans certains cas, vous devrez peut-être envoyer des attributs personnalisés. La capture d’écran suivante montre la liste des attributs par défaut. Cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue des attributs utilisateur et ajouter des attributs supplémentaires si nécessaire.

    ![Capture d’écran montrant User Attributes avec l’icône Edit sélectionnée.](common/edit-attribute.png)

6. Dans la section **Revendications des utilisateurs** de la boîte de dialogue **Attributs utilisateur**, modifiez les revendications en utilisant l’icône **Modifier** ou ajoutez des revendications en utilisant l’option **Ajouter une nouvelle revendication** pour configurer l’attribut de jeton SAML comme sur l’image ci-dessus et procédez comme suit : 

    a. Cliquez sur le bouton **Ajouter une nouvelle revendication** pour ouvrir la boîte de dialogue **Gérer les revendications des utilisateurs**.

    ![Capture d’écran montrant User claims avec l’option permettant d’ajouter une nouvelle revendication.](common/new-save-attribute.png)

    ![Capture d’écran montrant la boîte de dialogue Manage user claims où vous pouvez entrer les valeurs décrites.](common/new-attribute-details.png)

    b. Dans la zone de texte **Attribut**, indiquez le nom d’attribut pour cette ligne.

    c. Laissez le champ **Espace de noms** vide.

    d. Sélectionnez Source comme **Attribut**.

    e. Dans la liste **Attribut de la source**, tapez la valeur d’attribut indiquée pour cette ligne.

    f. Cliquez sur **OK**

    g. Cliquez sur **Enregistrer**.

7. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur le bouton Copier pour copier l’**URL des métadonnées de fédération d’application**, puis transmettez cette URL à l’équipe Learning Pool Delivery.

    ![Lien Téléchargement de certificat](common/copy-metadataurl.png)

### <a name="assign-an-azure-ad-user"></a>Attribuer un utilisateur Azure AD

Dans cette section, vous allez autoriser un utilisateur Azure AD existant à utiliser l’authentification unique Azure en lui accordant l’accès à Learning Pool LMS.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Learning Pool LMS**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez un utilisateur approprié dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-learning-pool-lms-sso"></a>Configurer l’authentification unique de Learning Pool LMS

L’équipe Learning Pool Delivery utilise l’**URL de métadonnées de fédération** de l’application pour configurer le LMS afin d’accepter les connexions SAML2. Il vous sera demandé d’effectuer quelques étapes de test pour vérifier que la connexion est correctement configurée. L’équipe Learning Pool Delivery vous guidera tout au long de ce processus.

### <a name="test-sso"></a>Tester l’authentification unique (SSO)

Vous serez guidé tout au long du processus de test par l’équipe Learning Pool Delivery.

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez configuré Learning Pool LMS, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
