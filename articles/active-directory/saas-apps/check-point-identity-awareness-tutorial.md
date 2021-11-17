---
title: 'Tutoriel : Intégration de l’authentification unique Azure Active Directory à Check Point Identity Awareness | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Check Point Identity Awareness.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/15/2021
ms.author: jeedes
ms.openlocfilehash: 7cdfa5f3e36b36862d4b96cf3b15d6d97bbd7a58
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132344843"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-check-point-identity-awareness"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory à Check Point Identity Awareness

Dans ce tutoriel, vous allez apprendre à intégrer Check Point Identity Awareness à Azure Active Directory (Azure AD). Quand vous intégrez Check Point Identity Awareness à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Check Point Identity Awareness.
* Permettre aux utilisateurs de se connecter automatiquement à Check Point Identity Awareness à l’aide de leurs comptes Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Check Point Identity Awareness pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* Check Point Identity Awareness prend en charge l’authentification unique lancée par le **fournisseur de services**.

## <a name="adding-check-point-identity-awareness-from-the-gallery"></a>Ajout de Check Point Identity Awareness à partir de la galerie

Pour configurer l’intégration de Check Point Identity Awareness à Azure AD, vous devez ajouter Check Point Identity Awareness à votre liste d’applications SaaS managées à partir de la galerie.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Check Point Identity Awareness** dans la zone de recherche.
1. Sélectionnez **Check Point Identity Awareness** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.


## <a name="configure-and-test-azure-ad-sso-for-check-point-identity-awareness"></a>Configurer et tester l’authentification unique Azure AD pour Check Point Identity Awareness

Configurez et testez l’authentification unique Azure AD avec Check Point Identity Awareness pour un utilisateur de test nommé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Check Point Identity Awareness associé.

Pour configurer et tester l’authentification unique Azure AD auprès de Check Point Identity Awareness, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurez l’authentification unique Check Point Identity Awareness](#configure-check-point-identity-awareness-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créez un utilisateur de test Check Point Identity Awareness](#create-check-point-identity-awareness-test-user)** pour avoir un équivalent de B.Simon dans Check Point Identity Awareness lié à la représentation Azure AD de cet utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **Check Point Identity Awareness**, recherchez la section **Gérer**, puis sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, entrez les valeurs pour les champs suivants :

    a. Dans la zone de texte **Identificateur (ID d’entité)** , saisissez une URL au format suivant : `https://<GATEWAY_IP>/connect/spPortal/ACS/ID/<IDENTIFIER_UID>`

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://<GATEWAY_IP>/connect/spPortal/ACS/Login/<IDENTIFIER_UID>`

    c. Dans la zone de texte **URL de connexion**, saisissez une URL au format suivant : `https://<GATEWAY_IP>/connect`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL d’authentification et l’URL de réponse réels. Pour obtenir ces valeurs, contactez [l’équipe du support technique de Check Point Identity Awareness](mailto:support@checkpoint.com). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **XML de métadonnées de fédération** et sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

1. Dans la section **Configurer Check Point Identity Awareness**, copiez les URL appropriées en fonction des exigences.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Check Point Identity Awareness.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Check Point Identity Awareness**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-check-point-identity-awareness-sso"></a>Configurer l’authentification unique Check Point Identity Awareness

1. Connectez-vous au site d’entreprise Check Point Identity Awareness en tant qu’administrateur.

1. Dans SmartConsole > affichage **Gateways & Servers**, cliquez sur **New > More > User/Identity > Identity Provider**.

    ![capture d’écran du nouveau fournisseur d’identité.](./media/check-point-identity-awareness-tutorial/identity-provider.png)

1. Dans la page **New Identity Provider**, effectuez les étapes suivantes.

    ![capture d’écran de la section Identity Provider.](./media/check-point-identity-awareness-tutorial/new-identity-provider.png)

    a. Dans le champ **Gateway**, sélectionnez la passerelle de sécurité qui doit effectuer l’authentification SAML.

    b. Dans le champ **Service**, sélectionnez l’**Identity Awareness** dans la liste déroulante.

    c. Copiez la valeur du champ **Identifier(Entity ID)** , puis collez-la dans la zone de texte **Identificateur** de la section **Configuration SAML de base** du portail Azure.

    d. Copiez la valeur du champ **Reply URL**, puis collez-la dans la zone de texte **URL de réponse** de la section **Configuration SAML de base** du portail Azure.

    e. Sélectionnez **Importer un fichier de métadonnées** pour charger le **XML de métadonnées de fédération** téléchargé à partir du portail Azure.

    > [!NOTE]
    > Vous pouvez également sélectionner **Insert Manually** pour coller manuellement les valeurs **ID d’entité** et **URL de connexion** dans les champs correspondants, et pour charger le **Fichier de certificat** à partir du portail Azure.

    f. Cliquez sur **OK**.

### <a name="create-check-point-identity-awareness-test-user"></a>Créer un utilisateur de test Check Point Identity Awareness

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans Check Point Identity Awareness. Collaborez avec l’[équipe de support technique de Check Point Identity Awareness](mailto:support@checkpoint.com) pour ajouter les utilisateurs à la plateforme Check Point Identity Awareness. Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion à Check Point Identity Awareness, où vous pouvez lancer le flux de connexion. 

* Accédez directement à l’URL de connexion à Check Point Identity Awareness pour y lancer le flux de connexion.

* Vous pouvez utiliser Mes applications de Microsoft. Quand vous cliquez sur la vignette Check Point Identity Awareness dans Mes applications, vous êtes redirigé vers l’URL de connexion à Check Point Identity Awareness. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).


## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré Check Point Identity Awareness, vous pouvez appliquer le contrôle de session, qui protège en temps réel contre l’exfiltration et l’infiltration des données sensibles de votre organisation. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
