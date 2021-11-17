---
title: 'Tutoriel : Intégration d’Azure Active Directory à Evidence.com | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Evidence.com.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/17/2021
ms.author: jeedes
ms.openlocfilehash: 0eb4ba039deba2cac2059c71deb7cd0d55aa2d9b
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132285799"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-evidencecom"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory à Evidence.com

Dans ce tutoriel, vous allez découvrir comment intégrer Evidence.com à Azure Active Directory (Azure AD). Quand vous intégrez Evidence.com à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Evidence.com.
* Permettre à vos utilisateurs de se connecter automatiquement à Evidence.com avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Evidence.com pour lequel l’authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* Evidence.com prend en charge l’authentification unique lancée par le **fournisseur de services**.

## <a name="add-evidencecom-from-the-gallery"></a>Ajouter Evidence.com à partir de la galerie

Pour configurer l’intégration d’Evidence.com à Azure AD, vous devez ajouter Evidence.com à partir de la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Evidence.com** dans la zone de recherche.
1. Sélectionnez **Evidence.com** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-evidencecom"></a>Configurer et tester l’authentification unique Azure AD pour Evidence.com

Configurez et testez l’authentification unique Azure AD avec Evidence.com pour un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Evidence.com associé.

Pour configurer et tester l’authentification unique Azure AD avec Evidence.com, procédez comme suit :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Evidence.com](#configure-evidencecom-sso)** – pour configurer les paramètres d’authentification unique côté application.
    1. **[Créer un utilisateur de test Evidence.com](#create-evidencecom-test-user)** pour avoir un équivalent de B.Simon dans Evidence.com lié à la représentation Azure AD associée.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, dans la page d’intégration de l’application **Evidence.com**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la page **Configurer l’authentification unique avec SAML**, effectuez les étapes suivantes :

    1. Dans la zone de texte **URL de connexion**, saisissez une URL au format suivant : `https://<yourtenant>.evidence.com`

    1. Dans la zone de texte **Identificateur (ID d’entité)** , saisissez une URL au format suivant : `https://<yourtenant>.evidence.com`

    1. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://<your tenant>.evidence.com/?class=UIX&proc=Login`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion, l’identificateur et l’URL de réponse réels. Pour obtenir ces valeurs, contactez l’[équipe de support technique Evidence.com](https://communities.taser.com/support/SupportContactUs?typ=LE). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

5. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (Base64)** en fonction des options définies par rapport à vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

1. Dans la section **Configurer Evidence.com**, copiez la ou les URL appropriées, en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Evidence.com.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Evidence.com**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-evidencecom-sso"></a>Configurer l’authentification unique Evidence.com

1. Dans une fenêtre de navigateur web distincte, connectez-vous à votre client Evidence.com en tant qu’administrateur et accédez à l’onglet **Admin**.

2. Cliquez sur **Authentification unique Agence**.

3. Sélectionnez **Authentification unique SAML**.

4. Copiez les valeurs **Identificateur Azure AD**, **URL de connexion** et **URL de déconnexion** dans le portail Azure et collez-les dans les champs correspondants dans Evidence.com.

5. Ouvrez dans le Bloc-notes votre fichier Certificate(Base64) téléchargé, copiez son contenu dans le Presse-papiers, puis collez-le dans la zone de texte **Certificat de sécurité**. 

6. Enregistrez la configuration dans Evidence.com.

### <a name="create-evidencecom-test-user"></a>Créer un utilisateur de test Evidence.com

Pour que les utilisateurs Azure AD puissent se connecter, leur accès doit être approvisionné dans l’application Evidence.com. Cette section décrit comment créer des comptes d’utilisateur Azure AD dans Evidence.com.

**Pour approvisionner un compte d’utilisateur dans Evidence.com :**

1. Dans une fenêtre de navigateur web, connectez-vous à votre site d’entreprise Evidence.com en tant qu’administrateur.

2. Accédez à l’onglet **Admin** .

3. Cliquez sur **Add User**.

4. Cliquez sur le bouton **Add** .

5. La valeur **Email Address** (Adresse de messagerie) de l’utilisateur ajouté doit correspondre au nom d’utilisateur de l’utilisateur auquel vous souhaitez accorder l’accès dans Azure AD. Si le nom d’utilisateur et l’adresse de messagerie n’ont pas la même valeur dans votre organisation, vous pouvez modifier la section **Evidence.com > Attributs > Authentification unique** du portail Azure de sorte que l’identificateur du nom envoyé à Evidence.com soit l’adresse de messagerie.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion à Evidence.com à partir de laquelle vous pouvez lancer le flux de connexion. 

* Accédez directement à l’URL de connexion à Evidence.com pour lancer le flux de connexion.

* Vous pouvez utiliser Mes applications de Microsoft. Quand vous cliquez sur la vignette Evidence.com dans Mes applications, vous êtes redirigé vers l’URL de connexion à Evidence.com. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré Evidence.com, vous pouvez appliquer le contrôle de session qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
