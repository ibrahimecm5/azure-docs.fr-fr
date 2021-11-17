---
title: 'Didacticiel : Intégration d’Azure Active Directory à Front | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Front.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/06/2021
ms.author: jeedes
ms.openlocfilehash: f8aaa664d03143e16336175e1eabdc860353755f
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132291913"
---
# <a name="tutorial-azure-active-directory-integration-with-front"></a>Didacticiel : Intégration d’Azure Active Directory avec Front

Dans ce tutoriel, vous allez découvrir comment intégrer Front à Azure Active Directory (Azure AD). Quand vous intégrez Front à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Front.
* Permettre à vos utilisateurs de se connecter automatiquement à Front avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Front pour lequel l’authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Front prend en charge l’authentification unique lancée par le **fournisseur d’identité**.

## <a name="adding-front-from-the-gallery"></a>Ajout de Front depuis la galerie

Pour configurer l’intégration de Front avec Azure AD, vous devez ajouter Front, disponible dans la galerie, à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Front** dans la zone de recherche.
1. Sélectionnez **Front** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-front"></a>Configurer et tester l’authentification unique Azure AD pour Front

Configurez et testez l’authentification unique Azure AD avec Front pour une utilisatrice de test appelée **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Front associé.

Pour configurer et tester l’authentification unique Azure AD avec Front, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Front](#configure-front-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test Front](#create-front-test-user)** pour avoir un équivalent de B.Simon dans Front lié à la représentation Azure AD associée.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **Front**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Sur la page **Configurer l’authentification unique avec SAML**, effectuez les étapes suivantes :

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<companyname>.frontapp.com`

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://<companyname>.frontapp.com/sso/saml/callback`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur et l’URL de réponse réels. Contactez l’[équipe du support technique du client Front](mailto:support@frontapp.com) pour obtenir ces valeurs. Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

5. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (Base64)** en fonction des options définies par rapport à vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

6. Dans la section **Configurer Front**, copiez les URL appropriées, selon vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Front.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Front**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.


## <a name="configure-front-sso"></a>Configurer l’authentification unique Front

1. Connectez-vous à votre site web Front en tant qu’administrateur.

2. Accédez aux **paramètres** et sélectionnez les **préférences**.

3. Effectuez les étapes suivantes dans la page **Company preferences** (Préférences de l’entreprise).
   
    ![Capture d’écran montrant la section « Company preferences » avec le lien « Single Sign On » sélectionné.](./media/front-tutorial/single-sign-on.png)

    a. Cliquez sur **Single Sign On** (Authentification unique) dans le volet de navigation gauche.

    b. Sélectionnez **SAML** dans la liste déroulante **Authentification unique**.

    c. Dans la zone de texte **Entry Point** (Point d’entrée), collez la valeur de **URL de connexion** que vous avez copiée dans le portail Azure.

    d. Sélectionnez le type de **Requested authentication context** (Contexte d’authentification demandé) comme **Désactivé**.

    e. Ouvrez dans le Bloc-notes votre fichier **Certificate(Base64)** téléchargé, copiez son contenu dans le Presse-papiers, puis collez-le dans la zone de texte **Certificat de signature**.

7. Dans la section **Service provider settings** (Paramètres du fournisseur de services), procédez comme suit :

    ![Configurer l’authentification unique côté application](./media/front-tutorial/service-provider.png)

    a. Copiez la valeur de l’**ID d’entité** et collez-la dans la zone de texte **Identificateur** de la section **Domaine et URL Front** du portail Azure.

    b. Copiez la valeur de **l’URL ACS** et collez-la dans la zone de texte **URL de réponse** de la section **Domaine et URL Front** du Portail Azure.
    
8. Cliquez sur le bouton **Enregistrer** .


### <a name="create-front-test-user"></a>Créer un utilisateur de test Front

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans Front. Collaborez avec l’[équipe de support technique Front](mailto:support@frontapp.com) pour ajouter des utilisateurs dans la plateforme Front. Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes.

* Cliquez sur Tester cette application dans le portail Azure : vous devez être connecté automatiquement à l’instance de Front pour laquelle vous avez configuré l’authentification unique.

* Vous pouvez utiliser Mes applications de Microsoft. Quand vous cliquez sur la vignette Front dans Mes applications, vous devez être automatiquement connecté à l’application Front pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez configuré Front, vous pouvez appliquer le contrôle de session, qui protège en temps réel contre l’exfiltration et l’infiltration de données sensibles de votre organisation. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
