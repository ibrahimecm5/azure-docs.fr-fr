---
title: 'Tutoriel : Intégration de l’authentification unique Azure Active Directory à AlacrityLaw | Microsoft Docs'
description: Découvrez la procédure de configuration de l’authentification unique entre Azure Active Directory et AlacrityLaw.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/30/2021
ms.author: jeedes
ms.openlocfilehash: ce450e7e80c14ad4864c1fc8cfd604b0a17aef28
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132330822"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-alacritylaw"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory à AlacrityLaw

Dans ce tutoriel, vous allez découvrir la procédure d’intégration d’AlacrityLaw à Azure Active Directory (Azure AD). Lorsque vous intégrez AlacrityLaw à Azure AD, vous pouvez :

* Utiliser Azure AD pour contrôler qui a accès à AlacrityLaw.
* Permettre aux utilisateurs de se connecter automatiquement à AlacrityLaw avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement pour lequel l’authentification unique AlacrityLaw est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* AlacrityLaw prend en charge l’authentification unique initiée par **SP**.

> [!NOTE]
> L’identificateur de cette application étant une valeur de chaîne fixe, une seule instance peut être configurée dans un locataire.

## <a name="adding-alacritylaw-from-the-gallery"></a>Ajout d’AlacrityLaw à partir de la galerie

Pour configurer l’intégration d’AlacrityLaw à Azure AD, vous devez ajouter AlacrityLaw à partir de la galerie à votre liste d’applications SaaS managées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **AlacrityLaw** dans la zone de recherche.
1. Sélectionnez **AlacrityLaw** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.


## <a name="configure-and-test-azure-ad-sso-for-alacritylaw"></a>Configurer et tester l’authentification unique Azure AD pour AlacrityLaw

Configurez et testez l’authentification unique Azure AD avec AlacrityLaw à l’aide d’un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur AlacrityLaw associé.

Pour configurer et tester l’authentification unique Azure AD avec AlacrityLaw, procédez comme suit :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique AlacrityLaw](#configure-alacritylaw-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test AlacrityLaw](#create-alacritylaw-test-user)** pour avoir un équivalent de B.Simon dans AlacrityLaw, associé à sa représentation dans Azure AD.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **AlacrityLaw**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, entrez les valeurs pour les champs suivants :

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://app.alacritylaw.com/auth/saml/<ID>`.

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://app.alacritylaw.com/auth/saml/<ID>/callback`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de réponse et l’URL de connexion réelles. Pour obtenir ces valeurs, contactez l’[équipe du support technique AlacrityLaw](mailto:infrastructure@alacritylaw.com). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **Certificat (en base64)** , puis sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

1. Dans la section **Configurer AlacrityLaw**, copiez la ou les URL appropriées selon vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en accordant l’accès à AlacrityLaw.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **AlacrityLaw**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-alacritylaw-sso"></a>Configurer l’authentification unique AlacrityLaw

Pour configurer l’authentification unique côté **AlacrityLaw**, vous devez envoyer le **certificat (en base64)** téléchargé et les URL appropriées, copiées à partir du portail Azure, à l’[équipe du support technique AlacrityLaw](mailto:infrastructure@alacritylaw.com). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

### <a name="create-alacritylaw-test-user"></a>Créer un utilisateur de test AlacrityLaw

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans AlacrityLaw. Collaborez avec l’[équipe du support technique AlacrityLaw](mailto:infrastructure@alacritylaw.com) pour ajouter les utilisateurs à la plateforme AlacrityLaw. Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l'URL de connexion à AlacrityLaw, à partir de laquelle vous pouvez initier le flux de connexion. 

* Accédez directement à l'URL de connexion à AlacrityLaw pour initier le flux de connexion.

* Vous pouvez utiliser Mes applications de Microsoft. Un clic sur la vignette AlacrityLaw dans Mes applications vous redirige vers l'URL de connexion à AlacrityLaw. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).


## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré AlacrityLaw, vous pouvez appliquer le contrôle de session, qui protège en temps réel contre l'exfiltration et l'infiltration des données sensibles de votre organisation. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
