---
title: 'Tutoriel : Intégration de l’authentification unique Azure AD à AlexisHR'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et AlexisHR.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/08/2021
ms.author: jeedes
ms.openlocfilehash: d0cab8f8ff33f5e402a4303f9c40529052bd0053
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132305296"
---
# <a name="tutorial-azure-ad-sso-integration-with-alexishr"></a>Tutoriel : Intégration de l’authentification unique Azure AD à AlexisHR

Dans ce tutoriel, vous allez apprendre à intégrer AlexisHR à Azure Active Directory (Azure AD). Quand vous intégrez AlexisHR à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à AlexisHR.
* Permettre à vos utilisateurs de se connecter automatiquement à AlexisHR avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement AlexisHR pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* AlexisHR prend en charge l’authentification unique lancée par le **fournisseur d’identité**.

## <a name="add-alexishr-from-the-gallery"></a>Ajouter AlexisHR à partir de la galerie

Pour configurer l’intégration d’AlexisHR à Azure AD, vous devez ajouter AlexisHR à partir de la galerie à votre liste d’applications SaaS managées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **AlexisHR** dans la zone de recherche.
1. Sélectionnez **AlexisHR** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-alexishr"></a>Configurer et tester l’authentification unique Azure AD pour AlexisHR

Configurez et testez l’authentification unique Azure AD avec AlexisHR à l’aide d’un utilisateur test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur AlexisHR associé.

Pour configurer et tester l’authentification unique Azure AD avec AlexisHR, vous devez suivre les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique AlexisHR](#configure-alexishr-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur test AlexisHR](#create-alexishr-test-user)** pour avoir un équivalent de B.Simon dans AlexisHR lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **AlexisHR**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    a. Dans la zone de texte **Identificateur**, tapez une valeur au format suivant : `urn:auth0:alexishr:<YOUR_CONNECTION_NAME>`

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://auth.alexishr.com/login/callback?connection=<YOUR_CONNECTION_NAME>`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur et l’URL de réponse réels. Pour obtenir ces valeurs, contactez l’[équipe du support technique AlexisHR](mailto:support@alexishr.com). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. L’application AlexisHR s’attend à recevoir les assertions SAML dans un certain format, ce qui vous demande d’ajouter des mappages d’attributs personnalisés à votre configuration des attributs de jetons SAML. La capture d’écran suivante montre la liste des attributs par défaut.

    ![image](common/default-attributes.png)

1. En plus de ce qui précède, l’application AlexisHR s’attend à ce que quelques attributs supplémentaires (présentés ci-dessous) soient repassés dans la réponse SAML. Ces attributs sont également préremplis, mais vous pouvez les examiner pour voir s’ils répondent à vos besoins.
    
    | Nom | Attribut source |
    | ------------ | --------- |
    | email | user.mail |

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **Certificat (en base64)** , puis sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

1. Dans la section **Configurer AlexisHR**, copiez la ou les URL appropriées selon vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à AlexisHR.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **AlexisHR**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-alexishr-sso"></a>Configurer l’authentification unique AlexisHR

1. Connectez-vous au site d’entreprise AlexisHR en tant qu’administrateur.

1. Accédez à **Paramètres** > **Authentification unique SAML**, puis cliquez sur **Nouveau fournisseur d’identité**.

1. Dans la section **Nouveau fournisseur d’identité**, effectuez les étapes suivantes :

    ![Capture d’écran montrant les paramètres du compte.](./media/alexishr-tutorial/account.png "Paramètres")

    1. Dans la zone de texte **URL SSO du fournisseur d’identité**, collez la valeur de l’**URL de connexion** que vous avez copiée à partir du portail Azure.

    1. Dans la zone de texte **URL de déconnexion du fournisseur d’identité**, collez la valeur de l’**URL de déconnexion** que vous avez copiée à partir du portail Azure.

    1. Ouvrez le **certificat (Base64)** téléchargé à partir du portail Azure dans le Bloc-notes et collez le contenu dans la zone de texte **Certificat x509 public**.

    1. Cliquez sur **Créer le fournisseur d’identité**.

1. Après avoir créé le fournisseur d’identité, vous allez recevoir les informations suivantes.

    ![Capture d’écran montrant les paramètres SSO.](./media/alexishr-tutorial/certificate.png "Configuration SSO")

    1. Copiez la valeur de l’**URL de l’audience**, puis collez-la dans la zone de texte **Identificateur** de la section **Configuration SAML de base** du portail Azure.

    1. Copiez la valeur de l’**URL Assertion Consumer Service**, puis collez-la dans la zone de texte **URL de réponse** de la section **Configuration SAML de base** du Portail Azure.

### <a name="create-alexishr-test-user"></a>Créer un utilisateur test AlexisHR

Dans cette section, vous allez créer une utilisatrice appelée Britta Simon dans AlexisHR. Contactez l’[équipe du support d’AlexisHR](mailto:support@alexishr.com) pour ajouter les utilisateurs à la plateforme AlexisHR. Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes.

* Cliquez sur Tester cette application dans le portail Azure. Vous devriez alors être automatiquement connecté à l’application AlexisHR pour laquelle vous avez configuré l’authentification unique.

* Vous pouvez utiliser Mes applications de Microsoft. Quand vous cliquez sur la vignette AlexisHR dans Mes applications, vous devriez être connecté automatiquement à l’application AlexisHR pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez configuré AlexisHR, vous pouvez appliquer le contrôle de session, qui protège votre organisation contre l’exfiltration et l’infiltration de ses données sensibles en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
