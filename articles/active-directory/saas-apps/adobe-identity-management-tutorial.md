---
title: 'Tutoriel : Intégration de l’authentification unique Azure Active Directory à Adobe Identity Management | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Adobe Identity Management.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/15/2021
ms.author: jeedes
ms.openlocfilehash: d0155a54238f42636aeee9121b586852b26196f8
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132280973"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-adobe-identity-management"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory à Adobe Identity Management

Dans ce tutoriel, vous allez apprendre à intégrer Adobe Identity Management à Azure Active Directory (Azure AD). Quand vous intégrez Adobe Identity Management à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Adobe Identity Management.
* Permettre à vos utilisateurs de se connecter automatiquement à Adobe Identity Management avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Abonnement Adobe Identity Management pour lequel l’authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* Adobe Identity Management prend en charge l’authentification unique lancée par le **fournisseur de services**
* Adobe Identity Management prend en charge [l’attribution d’utilisateurs et la suppression des privilèges d’accès **automatisées**](adobe-identity-management-provisioning-tutorial.md) (recommandé).

## <a name="adding-adobe-identity-management-from-the-gallery"></a>Ajout d’Adobe Identity Management à partir de la galerie

Pour configurer l’intégration d’Adobe Identity Management à Azure AD, vous devez ajouter Adobe Identity Management à partir de la galerie à votre liste d’applications SaaS managées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Adobe Identity Management** dans la zone de recherche.
1. Sélectionnez **Adobe Identity Management** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-adobe-identity-management"></a>Configurer et tester l’authentification SSO Azure AD pour Adobe Identity Management

Configurez et testez l’authentification unique Azure AD avec Adobe Identity Management à l’aide d’un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Adobe Identity Management associé.

Pour configurer et tester l’authentification SSO Azure AD avec Adobe Identity Management, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Adobe Identity Management](#configure-adobe-identity-management-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test Adobe Identity Management](#create-adobe-identity-management-test-user)** pour avoir un équivalent de B.Simon dans Adobe Identity Management lié à la représentation Azure AD associée.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, dans la page d’intégration de l’application **Adobe Identity Management**, recherchez la section **Gérer**, puis sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, entrez les valeurs pour les champs suivants :

    a. Dans la zone de texte **URL de connexion**, tapez l’URL : `https://adobe.com`

    b. Dans la zone de texte **Identificateur (ID d’entité)** , saisissez une URL au format suivant : `https://federatedid-na1.services.adobe.com/federated/saml/metadata/alias/<CUSTOM_ID>`

    > [!NOTE]
    > La valeur de l'identificateur n'est pas réelle. Mettez à jour cette valeur avec l’identificateur réel. Pour connaître la valeur, contactez l’[équipe de support technique Adobe Identity Management](mailto:identity@adobe.com). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **XML de métadonnées de fédération** et sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

1. Dans la section **Configurer Adobe Identity Management**, copiez l’URL ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous autorisez B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Adobe Identity Management.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Adobe Identity Management**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-adobe-identity-management-sso"></a>Configurer l’authentification unique Adobe Identity Management

1. Pour automatiser la configuration dans Adobe Identity Management, vous devez installer l’**extension de navigateur de connexion sécurisée à Mes applications** en cliquant sur **Installer l’extension**.

    ![Extension My apps](common/install-myappssecure-extension.png)

2. Une fois l’extension ajoutée au navigateur, cliquez sur **Configurer Adobe Identity Management** pour être redirigé vers l’application Adobe Identity Management. À partir de là, indiquez les informations d’identification de l’administrateur pour vous connecter à Adobe Identity Management. Cette extension de navigateur configure automatiquement l’application et automatise les étapes 3 à 8.

    ![Configuration](common/setup-sso.png)

3. Si vous souhaitez configurer Adobe Identity Management manuellement, dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Adobe Identity Management en tant qu’administrateur.

4. Accédez à l’onglet **Paramètres**, puis cliquez sur **Créer un annuaire**.

    ![Paramètres d’Adobe Identity Management](./media/adobe-identity-management-tutorial/settings.png)

5. Indiquez le nom de l’annuaire dans la zone de texte, sélectionnez **ID fédéré**, puis cliquez sur **Suivant**.

    ![Création d’annuaire pour Adobe Identity Management](./media/adobe-identity-management-tutorial/create-directory.png)

6. Sélectionnez **Autres fournisseurs SAML**, puis cliquez sur **Suivant**.
 
    ![Fournisseurs SAML d’Adobe Identity Management](./media/adobe-identity-management-tutorial/saml-providers.png)

7. Cliquez sur **sélectionner** pour charger le fichier **XML de métadonnées** que vous avez téléchargé à partir du portail Azure.

    ![Configuration SAML d’Adobe Identity Management](./media/adobe-identity-management-tutorial/saml-configuration.png)

8. Cliquez sur **Done** (Terminé).

### <a name="create-adobe-identity-management-test-user"></a>Créer un utilisateur de test Adobe Identity Management

1. Accédez à l’onglet **Utilisateurs**, puis cliquez sur **Ajouter un utilisateur**.

    ![Ajout d’utilisateur pour Adobe Identity Management](./media/adobe-identity-management-tutorial/add-user.png)

2. Dans la zone de texte **Entrez l’adresse e-mail de l’utilisateur**, indiquez l’**adresse e-mail**.

    ![Enregistrement d’utilisateur pour Adobe Identity Management](./media/adobe-identity-management-tutorial/save-user.png)

3. Cliquez sur **Enregistrer**.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes.

* Cliquez sur **Tester cette application** dans le portail Azure. Cette opération effectue une redirection vers l’URL de connexion à Adobe Identity Management, où vous pouvez lancer le flux de connexion.

* Accédez directement à l’URL de connexion à Adobe Identity Management, puis lancez le flux de connexion à partir de cet emplacement.

* Vous pouvez utiliser Mes applications de Microsoft. Quand vous cliquez sur la vignette Adobe Identity Management dans Mes applications, une redirection est effectuée vers l’URL de connexion à Adobe Identity Management. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez configuré Adobe Identity Management, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
