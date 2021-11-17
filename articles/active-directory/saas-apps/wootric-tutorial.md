---
title: 'Tutoriel : Intégration de l’authentification unique Azure Active Directory à Wootric | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Wootric.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/19/2021
ms.author: jeedes
ms.openlocfilehash: ef484784517b93a2fa935d558aed29598d782e42
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132316410"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-wootric"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory à Wootric

Dans ce tutoriel, vous allez découvrir comment intégrer Wootric avec Azure Active Directory (Azure AD). Quand vous intégrez Wootric à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Wootric.
* Permettre aux utilisateurs de se connecter automatiquement à Wootric avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Wootric pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* Wootric prend en charge l’authentification unique lancée par le **fournisseur d’identité**.
* Wootric prend en charge l’attribution d’utilisateurs **juste-à-temps**.

## <a name="adding-wootric-from-the-gallery"></a>Ajout de Wootric à partir de la galerie

Pour configurer l’intégration de Wootric à Azure AD, vous devez ajouter Wootric à partir de la galerie à votre liste d’applications SaaS managées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Wootric** dans la zone de recherche.
1. Sélectionnez **Wootric** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.


## <a name="configure-and-test-azure-ad-sso-for-wootric"></a>Configurer et tester l’authentification unique Azure AD pour Wootric

Configurez et testez l’authentification unique Azure AD avec Wootric pour un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Wootric associé.

Pour configurer et tester l’authentification unique Azure AD avec Wootric, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Wootric](#configure-wootric-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test Wootric](#create-wootric-test-user)** pour avoir un équivalent de B.Simon dans Wootric, associé à sa représentation dans Azure AD.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **Wootric**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, l’application est préconfigurée et les URL nécessaires sont déjà préremplies avec Azure. L’utilisateur doit enregistrer la configuration en cliquant sur le bouton **Enregistrer**.


1. Votre application Wootric s’attend à recevoir les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à votre configuration d’attributs de jetons SAML. La capture d’écran suivante montre la liste des attributs par défaut.

    ![image](common/default-attributes.png)

1. En plus de ce qui précède, l’application Wootric s’attend à ce que quelques attributs supplémentaires (présentés ci-dessous) soient repassés dans la réponse SAML. Ces attributs sont également préremplis, mais vous pouvez les examiner pour voir s’ils répondent à vos besoins.
    
    | Nom |  Attribut source |
    | -------------- | --------- |
    | id | user.objectid |

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **Certificat (en base64)** , puis sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

1. Dans la section **Configurer Wootric**, copiez la ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Wootric.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Wootric**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-wootric-sso"></a>Configurer l’authentification unique Wootric

1. Afin d’automatiser la configuration dans Wootric, vous devez installer l’**extension de navigateur permettant la connexion sécurisée à Mes applications** en cliquant sur **Installer l’extension**.

    ![Extension My apps](common/install-myappssecure-extension.png)

2. Une fois l’extension ajoutée au navigateur, le fait de cliquer sur **Configurer Wootric** vous redirige vers l’application Wootric. À partir de là, indiquez les informations d’identification de l’administrateur pour vous connecter à Wootric. Cette extension de navigateur configure automatiquement l’application et automatise les étapes 3 à 6.

    ![Configuration](common/setup-sso.png)

3. Si vous souhaitez configurer Wootric manuellement, dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Wootric en tant qu’administrateur.

1. Cliquez sur l’**icône de paramètres** dans le menu supérieur.

    ![Capture d’écran montrant l’icône de paramètres sélectionnée sur le site Wootric.](./media/wootric-tutorial/configure-1.PNG)

1. Dans **INTEGRATIONS**, sélectionnez **Authentication** dans le menu de gauche et cliquez sur **Enable Single Sign On with Azure Active Directory** (Activer l’authentification unique avec Azure Active Directory).

    ![Capture d’écran montrant Enable Single Sign On with Azure Active Directory connected dans l’élément Authentication.](./media/wootric-tutorial/configure-2.PNG)

1. Effectuez les étapes ci-après dans la page suivante :

    ![Capture d’écran montrant la page Settings (Paramètres) dans laquelle vous pouvez indiquer les valeurs décrites.](./media/wootric-tutorial/configure-3.PNG)

    a. Dans la zone de texte **Identity Provider Single Sign-On URL** (URL d’authentification unique du fournisseur d’identité), collez l’**URL de connexion** que vous avez copiée à partir du portail Azure.

    b. Dans la zone de texte **Identity Provider Issuer** (Émetteur du fournisseur d’identité), collez la valeur **ID d’entité** que vous avez copiée à partir du portail Azure.

    c. Ouvrez le **Certificat (en base64)** téléchargé à partir du portail Azure dans le Bloc-notes, puis collez le contenu dans la zone de texte **X.509 Certificate**.

    d. Cochez la case **Automatically grant access to new users** (Accorder automatiquement l’accès aux nouveaux utilisateurs).
    
    e. Cliquez sur **Save**(Enregistrer).

### <a name="create-wootric-test-user"></a>Créer un utilisateur de test Wootric

Dans cette section, un utilisateur appelé B.Simon est créé dans Wootric. Wootric prend en charge le provisionnement d’utilisateurs juste-à-temps, qui est activé par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas encore d’utilisateur dans Wootric, il en est créé un après l’authentification.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes.

* Cliquez sur Tester cette application dans le portail Azure : vous devez être connecté automatiquement à l’instance de Wootric pour laquelle vous avez configuré l’authentification unique.

* Vous pouvez utiliser Mes applications de Microsoft. Quand vous cliquez sur la vignette Wootric dans Mes applications, vous devez être connecté automatiquement à l’application Wootric pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).


## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré Wootric, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
