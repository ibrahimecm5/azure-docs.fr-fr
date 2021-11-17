---
title: "Tutoriel : Intégration d'Azure Active Directory à SmarterU | Microsoft Docs"
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et SmarterU.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/27/2021
ms.author: jeedes
ms.openlocfilehash: b69194889522d120bb39ae472b5f8f59a3a2d144
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132316923"
---
# <a name="tutorial-azure-active-directory-integration-with-smarteru"></a>Tutoriel : Intégration d'Azure Active Directory à SmarterU

> [!NOTE]
> Le processus d’intégration de SmarterU à Azure Active Directory est également documenté et tenu à jour dans le [système d’aide de SmarterU](https://help.smarteru.com/ID2053086).

Dans ce didacticiel, vous allez apprendre à intégrer SmarterU à Azure Active Directory (Azure AD). Quand vous intégrez SmarterU à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à SmarterU.
* Permettre à vos utilisateurs de se connecter automatiquement à SmarterU avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/)
* Abonnement SmarterU pour lequel l’authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* SmarterU prend en charge l’authentification unique initiée par le **fournisseur d’identité**.

> [!NOTE]
> L’identificateur de cette application étant une valeur de chaîne fixe, une seule instance peut être configurée dans un locataire.

## <a name="add-smarteru-from-the-gallery"></a>Ajouter SmarterU à partir de la galerie

Pour configurer l’intégration de SmarterU à Azure AD, vous devez ajouter SmarterU à partir de la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **SmarterU** dans la zone de recherche.
1. Sélectionnez **SmarterU** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-smarteru"></a>Configurer et tester l’authentification unique (SSO) Azure AD pour SmarterU

Configurez et testez l’authentification unique Azure AD avec SmarterU pour un utilisateur de test nommé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur SmarterU associé.

Pour configurer et tester l’authentification unique Azure AD avec SmarterU, procédez comme suit :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique SmarterU](#configure-smarteru-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test SmarterU](#create-smarteru-test-user)** pour avoir un équivalent de B.Simon dans SmarterU lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **SmarterU**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, effectuez l’étape suivante :

    Dans la zone de texte **Identificateur**, tapez l’URL : `https://www.smarteru.com/`

5. Sur la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le fichier **XML de métadonnées de fédération** en fonction des options définies selon vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

6. Dans la section **Configurer SmarterU**, copiez la ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à SmarterU.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **SmarterU**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-smarteru-sso"></a>Configurer l’authentification unique SmarterU

1. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise SmarterU en tant qu’administrateur.

1. Dans la barre d’outils située en haut, cliquez sur **Account Settings**.

    ![Account Settings](./media/smarteru-tutorial/settings.png)

1. Dans la page de configuration du compte, procédez comme suit :

    ![External Authorization](./media/smarteru-tutorial/configuration.png) 

    a. Sélectionnez **Enable External Authorization**.
  
    b. Dans la section **Master Login Control**, cliquez sur l’onglet **SmarterU**.
  
    c. Dans la section **User Default Login**, cliquez sur l’onglet **SmarterU**.
  
    d. Sélectionnez **Enable SAML**.
  
    e. Copiez le contenu du fichier de métadonnées téléchargé et collez-le dans la zone de texte **IdP Metadata** (Métadonnées IdP).

    f. Sélectionnez un élément **Identifier Attribute/Claim** (Attribut d’identificateur/Revendication).
  
    g. Cliquez sur **Enregistrer**.

### <a name="create-smarteru-test-user"></a>Créer un utilisateur de test SmarterU

Pour se connecter à SmarterU, les utilisateurs d’Azure AD doivent être provisionnés dans SmarterU. Dans le cas de SmarterU, l’approvisionnement est une tâche manuelle.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous à votre locataire **SmarterU**.

1. Accédez à **Users**.

1. Dans la section Users, procédez comme suit :

    ![Nouvel utilisateur](./media/smarteru-tutorial/add-user.png)  

    a. Cliquez sur **+User**.

    b. Entrez les valeurs d'attribut associées du compte d'utilisateur Azure AD dans les zones de texte suivantes : **Adresse e-mail principale**, **ID employé**, **Mot de passe**, **Vérifier le mot de passe**, **Prénom**, **Nom de famille**.

    c. Cliquez sur **Active**.

    d. Cliquez sur **Enregistrer**.

> [!NOTE]
> Vous pouvez utiliser tout autre outil ou n’importe quelle API de création de compte d’utilisateur fournis par SmarterU pour provisionner des comptes d’utilisateurs Azure AD.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes.

* Cliquez sur Tester cette application sur le portail Azure ; vous êtes alors connecté automatiquement à l’instance de SmarterU pour laquelle vous avez configuré l’authentification unique.

* Vous pouvez utiliser Mes applications de Microsoft. Quand vous cliquez sur la vignette SmarterU dans Mes applications, vous devez être connecté automatiquement à l’application SmarterU pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré SmarterU, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
