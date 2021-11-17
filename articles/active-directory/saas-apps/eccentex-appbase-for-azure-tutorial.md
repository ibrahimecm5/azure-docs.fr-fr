---
title: 'Tutoriel : Intégration entre l’authentification unique Azure Active Directory et Eccentex AppBase pour Azure | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Eccentex AppBase pour Azure.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/02/2021
ms.author: jeedes
ms.openlocfilehash: fa342712aa07b05570fa119acab1fc30a855ff0b
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132285941"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-eccentex-appbase-for-azure"></a>Tutoriel : Intégration entre l’authentification unique Azure Active Directory et Eccentex AppBase pour Azure

Dans ce tutoriel, vous allez apprendre à intégrer Eccentex AppBase pour Azure à Azure Active Directory (Azure AD). L’intégration d’Eccentex AppBase pour Azure à Azure AD vous permet d’effectuer les actions suivantes :

* Contrôlez dans Azure AD qui a accès à Eccentex AppBase pour Azure.
* Permettez à vos utilisateurs de se connecter automatiquement à Eccentex AppBase pour Azure avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/)
* Un abonnement Eccentex AppBase pour Azure pour lequel l’authentification unique (SSO, Single Sign-On) est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* Eccentex AppBase pour Azure prend en charge l’authentification unique à l’initiative du **fournisseur de services**.

* Eccentex AppBase pour Azure prend en charge l’attribution d’utilisateurs **juste-à-temps**.

## <a name="add-eccentex-appbase-for-azure-from-the-gallery"></a>Ajout d’Eccentex AppBase pour Azure à partir de la galerie

Pour configurer l’intégration d’Eccentex AppBase pour Azure à Azure AD, vous devez ajouter Eccentex AppBase pour Azure à votre liste d’applications SaaS gérées à partir de la galerie.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Eccentex AppBase pour Azure** dans la zone de recherche.
1. Sélectionnez **Eccentex AppBase pour Azure** dans le panneau de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-eccentex-appbase-for-azure"></a>Configuration et test de l’authentification unique Azure AD pour Eccentex AppBase pour Azure

Configurez et testez l’authentification unique Azure AD avec Eccentex AppBase pour Azure à l’aide d’un utilisateur de test nommé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur associé dans Eccentex AppBase pour Azure.

Pour configurer et tester l’authentification unique Azure AD avec Eccentex AppBase pour Azure, procédez comme suit :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Eccentex AppBase pour Azure](#configure-eccentex-appbase-for-azure-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test Eccentex AppBase pour Azure](#create-eccentex-appbase-for-azure-test-user)** pour avoir dans Eccentex AppBase pour Azure un équivalent de B.Simon lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Sur le Portail Azure, accédez à la page d’intégration de l’application **Eccentex AppBase pour Azure**, recherchez la section **Gérer**, puis sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    a. Dans la zone de texte **Identificateur (ID d’entité)** , tapez une URL en utilisant un des modèles suivants :

    | **Identificateur** |
    |--------|
    | `https://<CustomerName>.appbase.com/Ecx.Web` |
    | `https://<CustomerName>.eccentex.com:<PortNumber>/Ecx.Web` |

    b. Dans la zone de texte **URL de connexion**, tapez une URL en utilisant un des modèles suivants :

    | **URL d’authentification** |
    |---------|
    | `https://<CustomerName>.appbase.com/Ecx.Web/Account/sso?tenantCode=<TenantCode>&authCode=<AuthConfigurationCode>`|
    | `https://<CustomerName>.eccentex.com:<PortNumber>/Ecx.Web/Account/sso?tenantCode=<TenantCode>&authCode=<AuthConfigurationCode>` |

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur et l’URL de connexion réels. Pour obtenir ces valeurs, contactez [l’équipe de support client Eccentex AppBase pour Azure](mailto:eccentex.support@eccentex.com). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **Certificat (brut)** , puis sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificateraw.png)

1. Dans la section **Configurer Eccentex AppBase pour Azure**, copiez la ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Eccentex AppBase pour Azure.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Eccentex AppBase pour Azure**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-eccentex-appbase-for-azure-sso"></a>Configuration de l’authentification unique Eccentex AppBase pour Azure

1. Connectez-vous à votre site d’entreprise Eccentex AppBase pour Azure en tant qu’administrateur.

1. Accédez à l’icône **Engrenage** et cliquez sur **Gérer les utilisateurs**.

    ![Capture d’écran montrant les paramètres du compte SAML.](./media/eccentex-appbase-for-azure-tutorial/settings.png "Compte")

1. Accédez à **Gestion des utilisateurs** > **Configurations de l’authentification**, puis cliquez sur le bouton **Ajouter du code SAML**.

    ![Capture d’écran montrant les paramètres SAML.](./media/eccentex-appbase-for-azure-tutorial/users.png "Paramètres SAML")

1. Sur la page **Nouvelle configuration SAML**, procédez comme suit.

    ![Capture d’écran montrant la configuration SAML Azure.](./media/eccentex-appbase-for-azure-tutorial/configuration.png "Configuration SAML")

    1. Dans la zone de texte **Nom**, tapez un nom de configuration court. 

    1. Dans la zone de texte **URL de l’émetteur**, collez la valeur **ID de l’application** que vous avez copiée sur le Portail Azure.

    1. Copiez la valeur **URL d’application**, puis collez-la dans la zone de texte **Identificateur (ID d’entité)** de la section **Configuration SAML de base** du Portail Azure.

    1. Pour **Intégration de nouveaux utilisateurs AppBase**, sélectionnez **Invitation uniquement** dans la liste déroulante.

    1. Pour **Comportement d’échec d’authentification AppBase**, sélectionnez **Afficher la page d’erreur** dans la liste déroulante.

    1. Sélectionnez **Méthode avec signature Digest** et **Méthode avec signature** en fonction du chiffrement de votre certificat.

    1. Pour **Utiliser un certificat**, sélectionnez **Chargement manuel** dans la liste déroulante.

    1. Pour **Nom de la classe du contexte d’authentification**, sélectionnez **Mot de passe** dans la liste déroulante.

    1. Pour **Liaison entre le fournisseur de service et le fournisseur d’identité**, sélectionnez **Redirection HTTP** dans la liste déroulante.

        > [!NOTE]
        > Vérifiez que la case **Signer les demandes sortantes** n’est pas cochée.

    1. Copiez la valeur **URL Assertion Consumer Service**, puis collez-la dans la zone de texte **URL de réponse** de la section **Configuration SAML de base** du Portail Azure.

    1. Dans la zone de texte **URL de destination de la demande d’authentification**, collez la valeur **URL de connexion** que vous avez copiée sur le Portail Azure.

    1. Dans la zone de texte **URL de la ressource du fournisseur de services**, collez la valeur **URL de connexion** que vous avez copiée sur le Portail Azure.

    1. Dans la zone de texte **URL d’identification de l’artefact**, collez la valeur **URL de connexion** que vous avez copiée sur le Portail Azure.

    1. Pour **Liaison du protocole de demande d’authentification**, sélectionnez **HTTP-POST** dans la liste déroulante.

    1. Pour **Stratégie d’ID de nom de la demande d’authentification**, sélectionnez **Persistante** dans la liste déroulante.

    1. Dans la zone de texte **URL du répondeur de l’artefact**, collez la valeur **URL de connexion** que vous avez copiée sur le Portail Azure.

    1. Cochez la case **Activer la vérification de la signature de la réponse**.

    1. Ouvrez le **Certificat (brut)** que vous avez téléchargé sur le Portail Azure dans le Bloc-notes et collez le contenu dans la zone de texte **Chargement de certificat mutuel SAML**.

    1. Pour **Liaison du protocole de réponse à la déconnexion**, sélectionnez **HTTP-POST** dans la liste déroulante.

    1. Dans la zone de texte **URL de déconnexion personnalisée AppBase**, collez la valeur **URL de déconnexion** que vous avez copiée sur le Portail Azure.
    
    1. Cliquez sur **Enregistrer**.

### <a name="create-eccentex-appbase-for-azure-test-user"></a>Création d’un utilisateur de test Eccentex AppBase pour Azure

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans Eccentex AppBase pour Azure. Eccentex AppBase pour Azure prend en charge l’attribution d’utilisateurs juste-à-temps, qui est activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas encore d’utilisateur dans Eccentex AppBase pour Azure, il en est créé un après l’authentification.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. L’URL de connexion à Eccentex AppBase pour Azure, qui permet d’amorcer le flux de connexion, s’ouvre. 

* Accédez directement à l’URL de connexion à Eccentex AppBase pour Azure pour lancer le flux de connexion.

* Vous pouvez utiliser Mes applications de Microsoft. Quand vous cliquez sur la vignette Eccentex AppBase pour Azure dans Mes applications, l’URL de connexion à Eccentex AppBase pour Azure s’ouvre. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez configuré Eccentex AppBase pour Azure, vous pouvez appliquer le contrôle de session, qui protège votre organisation en temps réel contre l’exfiltration et l’infiltration de données sensibles. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
