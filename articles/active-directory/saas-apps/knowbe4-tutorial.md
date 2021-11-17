---
title: 'Tutoriel : Intégration de l’authentification unique Azure AD à KnowBe4 Security Awareness Training'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et la formation de sensibilisation à la sécurité KnowBe4.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/07/2021
ms.author: jeedes
ms.openlocfilehash: 0ccc0d20937fb3986ccfb26ec179cc2f310a32ec
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132310846"
---
# <a name="tutorial-azure-ad-sso-integration-with-knowbe4-security-awareness-training"></a>Tutoriel : Intégration de l’authentification unique Azure AD à KnowBe4 Security Awareness Training

Dans ce tutoriel, vous allez apprendre à intégrer KnowBe4 Security Awareness Training à Azure Active Directory (Azure AD). Quand vous intégrez KnowBe4 Security Awareness Training à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à KnowBe4 Security Awareness Training.
* Permettre aux utilisateurs de se connecter automatiquement à KnowBe4 Security Awareness Training avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement à KnowBe4 Security Awareness Training pour lequel l’authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* KnowBe4 Security Awareness Training prend en charge l’authentification unique lancée par le **fournisseur de services**.

* KnowBe4 Security Awareness Training prend en charge l’attribution d’utilisateurs **juste-à-temps**.

> [!NOTE]
> L’identificateur de cette application étant une valeur de chaîne fixe, une seule instance peut être configurée dans un locataire.

## <a name="add-knowbe4-from-the-gallery"></a>Ajouter KnowBe4 à partir de la galerie

Pour configurer l’intégration de KnowBe4 à Azure AD, vous devez ajouter KnowBe4 à partir de la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **KnowBe4** dans la zone de recherche.
1. Sélectionnez **KnowBe4** dans le volet des résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-knowbe4-security-awareness-training"></a>Configurer et tester l’authentification unique Azure AD pour KnowBe4 Security Awareness Training

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec KnowBe4 sur un utilisateur de test appelé **Britta Simon**.
Pour que l’authentification unique fonctionne, une relation doit être établie entre l’utilisateur Azure AD et l’utilisateur associé dans KnowBe4.

Pour configurer et tester l’authentification unique Azure AD avec KnowBe4, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique (SSO) Azure AD avec Britta Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique (SSO) Azure AD.
2. **[Configurer l’authentification unique avec la formation de sensibilisation à la sécurité KnowBe4](#configure-knowbe4-security-awareness-training-sso)** pour configurer les paramètres d’authentification unique côté application.
    1. **[Créer un utilisateur de test de la formation de sensibilisation à la sécurité KnowBe4](#create-knowbe4-security-awareness-training-test-user)** pour avoir un équivalent de Britta Simon dans la formation de sensibilisation à la sécurité KnowBe4, lié à la représentation de l’utilisateur Azure AD.
3. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **KnowBe4**, recherchez la section **Gérer**, puis sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, effectuez l’étape suivante :

    Dans la zone de texte **URL de connexion**, entrez une URL au format suivant :  `https://<companyname>.KnowBe4.com/auth/saml/<instancename>`

    > [!NOTE]
    > La valeur d’URL de connexion n’est pas réelle. Mettez à jour cette valeur avec l’URL de connexion réelle. Contactez l’[équipe de support client de formation de sensibilisation à la sécurité KnowBe4](mailto:support@KnowBe4.com) pour obtenir cette valeur. Vous pouvez également consulter le modèle figurant à la section **Configuration SAML de base** dans le portail Azure.

5. Dans la page **Configurer l’authentification unique avec SAML**, à la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (Raw)** en fonction des options définies, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificateraw.png)

6. Dans la section **Configurer la formation de sensibilisation à la sécurité KnowBe4**, copiez la ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en accordant l’accès à KnowBe4.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **KnowBe4**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-knowbe4-security-awareness-training-sso"></a>Configurer l’authentification unique avec la formation de sensibilisation à la sécurité KnowBe4

Pour configurer l’authentification unique du côté de la **formation de sensibilisation à la sécurité KnowBe4**, vous devez envoyer le **Certificat (brut)** téléchargé et les URL appropriées copiées sur le portail Azure à l’[équipe du support technique de la formation de sensibilisation à la sécurité KnowBe4](mailto:support@KnowBe4.com). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

### <a name="create-knowbe4-security-awareness-training-test-user"></a>Créer un utilisateur de test de la formation de sensibilisation à la sécurité KnowBe4

Dans cette section, un utilisateur nommé Britta Simon est créé dans KnowBe4. KnowBe4 prend en charge l’attribution d’utilisateurs juste-à-temps, qui est activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas encore d’utilisateur dans KnowBe4, il en est créé un après l’authentification.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL d’authentification KnowBe4 Security Awareness Training, à partir de laquelle vous pouvez lancer le processus de connexion. 

* Accédez directement à l’URL d’authentification KnowBe4 Security Awareness Training pour lancer le processus de connexion.

* Vous pouvez utiliser Mes applications de Microsoft. Le fait de cliquer sur la vignette KnowBe4 Security Awareness Training dans Mes applications vous redirige vers l’URL d’authentification KnowBe4 Security Awareness Training. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré KnowBe4 Security Awareness Training, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
