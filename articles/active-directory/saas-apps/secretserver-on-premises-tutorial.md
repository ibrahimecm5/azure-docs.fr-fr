---
title: 'Tutoriel : Intégration d’Azure Active Directory à Secret Server (On-Premises) | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Secret Server (On-Premises).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/05/2021
ms.author: jeedes
ms.openlocfilehash: 33e230d79a0c4392cf4fa0d957cd054a71eb5b12
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132333672"
---
# <a name="tutorial-integrate-secret-server-on-premises-with-azure-active-directory"></a>Tutoriel : Intégrer Secret Server (On-Premises) à Azure Active Directory

Dans ce tutoriel, vous allez découvrir comment intégrer Secret Server (On-Premises) à Azure Active Directory (Azure AD). Quand vous intégrez Secret Server (On-Premises) à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Secret Server (On-Premises).
* Permettre aux utilisateurs de se connecter automatiquement à Secret Server (On-Premises) avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Secret Server (On-Premises) pour lequel l’authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* Secret Server (On-Premises) prend en charge l’authentification unique lancée par **le fournisseur de services et le fournisseur d’identité**

## <a name="add-secret-server-on-premises-from-the-gallery"></a>Ajouter Secret Server (On-Premises) à partir de la galerie

Pour configurer l’intégration de Secret Server (On-Premises) avec Azure AD, vous devez l’ajouter à votre liste d’applications SaaS gérées à partir de la galerie.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Secret Server (On-Premises)** dans la zone de recherche.
1. Sélectionnez **Secret Server (On-Premises)** dans le volet des résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-secret-server-on-premises"></a>Configurer et tester l’authentification unique (SSO) Azure AD pour Secret Server (On-Premises)

Configurez et testez l’authentification unique Azure AD avec Secret Server (On-Premises) pour un utilisateur de test nommé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Secret Server (On-Premises) associé.

Pour configurer et tester l’authentification unique Azure AD avec Secret Server (On-Premises), effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Secret Server (On-Premises)](#configure-secret-server-on-premises-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test Secret Server (On-Premises)](#create-secret-server-on-premises-test-user)** pour avoir un équivalent de B.Simon dans Secret Server (On-Premises) lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **Secret Server (On-Premises)** , recherchez la section **Gérer**, puis sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon pour **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, si vous souhaitez configurer l’application en mode Initié par le **fournisseur d’identité**, entrez les valeurs pour les champs suivants :

    a. Dans la zone de texte **Identificateur**, tapez une l’URL : `https://secretserveronpremises.azure`

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://<SecretServerURL>/SAML/AssertionConsumerService.aspx`

    > [!NOTE]
    > L’ID d’entité indiquée ci-dessus est seulement un exemple et vous êtes libre de choisir une valeur unique qui identifie votre instance Secret Server dans Azure AD. Vous devez envoyer cet ID d’entité à [l’équipe du support client Secret Server (On-Premises)](https://thycotic.force.com/support/s/) qui va la configurer de son côté. Pour plus de détails, veuillez lire [cet article](https://thycotic.force.com/support/s/article/Configuring-SAML-in-Secret-Server).

1. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<SecretServerURL>/login.aspx`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de réponse et l’URL de connexion réelles. Pour obtenir ces valeurs, contactez [l’équipe du support client Secret Server (On-Premises)](https://thycotic.force.com/support/s/). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **Certificat (Base64)** , puis sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Certificat de signature SAML**.

    ![Capture d’écran montrant la section « Certificat de signature SAML » avec « Certificat (en base64) » et l’action « Télécharger » sélectionnée.](./media/secretserver-on-premises-tutorial/edit-saml-signon.png)

1. Sélectionnez **Option de signature** pour **Signer la réponse et l’assertion SAML**.

    ![Options de signature](./media/secretserver-on-premises-tutorial/signing-option.png)

1. Dans la section **Configurer Secret Server (On-Premises)** , copiez la ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Secret Server (On-Premises).

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Secret Server (On-Premises)** .
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-secret-server-on-premises-sso"></a>Configurer l’authentification unique Secret Server (On-Premises)

Pour configurer l’authentification unique côté **Secret Server (On-Premises)** , vous devez envoyer le **certificat (Base64)** téléchargé et les URL appropriées, copiées à partir du portail Azure, à l’[équipe du support technique de Secret Server (On-Premises)](https://thycotic.force.com/support/s/). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

### <a name="create-secret-server-on-premises-test-user"></a>Créer un utilisateur de test Secret Server (On-Premises)

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans Secret Server (On-Premises). Pour ajouter les utilisateurs dans la plateforme Secret Server (On-Premises), faites-vous aider par [l’équipe de support de Secret Server (On-Premises)](https://thycotic.force.com/support/s/). Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

#### <a name="sp-initiated"></a>Lancée par le fournisseur de services :

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion Secret Server (On-Premises), à partir de laquelle vous pouvez démarrer le flux de connexion.  

* Accédez directement à l’URL de connexion Secret Server (On-Premises) pour lancer le processus de connexion.

#### <a name="idp-initiated"></a>Lancée par le fournisseur d’identité :

* Dans le portail Azure, cliquez sur **Tester cette application**. Vous êtes alors automatiquement connecté à l’application Secret Server (On-Premises) pour laquelle vous avez configuré l’authentification unique. 

Vous pouvez aussi utiliser Mes applications de Microsoft pour tester l’application dans n’importe quel mode. Si, quand vous cliquez sur la vignette Secret Server (On-Premises) dans Mes applications, le mode Fournisseur de services est configuré, vous êtes redirigé vers la page de connexion de l’application pour lancer le flux de connexion. S’il s’agit du mode Fournisseur d’identité, vous êtes automatiquement connecté à l’application Secret Server (On-Premises) pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez configuré Secret Server (On-Premises), vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
