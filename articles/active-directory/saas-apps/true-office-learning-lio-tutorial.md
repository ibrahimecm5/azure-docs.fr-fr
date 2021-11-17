---
title: 'Tutoriel : Intégration de l’authentification unique (SSO) Azure Active Directory à True Office Learning - LIO | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et True Office Learning - LIO.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/26/2021
ms.author: jeedes
ms.openlocfilehash: 66a9a5290c956b8fc254ec6308f62410c845dbbb
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132332874"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-true-office-learning---lio"></a>Tutoriel : Intégration de l’authentification unique (SSO) Azure Active Directory à True Office Learning - LIO

Dans ce tutoriel, vous allez apprendre à intégrer True Office Learning - LIO à Azure Active Directory (Azure AD). Lorsque vous intégrez True Office Learning - LIO à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à True Office Learning - LIO.
* Permettre à vos utilisateurs de se connecter automatiquement à True Office Learning - LIO avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/)
* Abonnement True Office Learning - LIO pour lequel l’authentification unique (SSO) est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* True Office Learning - LIO prend en charge l’authentification SSO lancée par le fournisseur de services (**SP**).

## <a name="add-true-office-learning---lio-from-the-gallery"></a>Ajouter True Office Learning - LIO à partir de la galerie

Pour configurer l’intégration de True Office Learning - LIO à Azure AD, vous devez ajouter True Office Learning - LIO à partir de la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **True Office Learning - LIO** dans la zone de recherche.
1. Sélectionnez **True Office Learning - LIO** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-true-office-learning---lio"></a>Configurer et tester l’authentification SSO Azure AD pour True Office Learning - LIO

Configurez et testez l’authentification SSO Azure AD avec True Office Learning - LIO à l’aide d’un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur True Office Learning - LIO associé.

Pour configurer et tester l’authentification SSO Azure AD avec True Office Learning - LIO, procédez comme suit :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer True Office Learning - LIO](#configure-true-office-learning---lio-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test True Office Learning - LIO](#create-true-office-learning---lio-test-user)** pour avoir dans True Office Learning - LIO un équivalent de B.Simon lié à la représentation Azure AD associée.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **True Office Learning - LIO**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    1. Dans la zone de texte **Identificateur (ID d’entité)** , saisissez une URL au format suivant : `https://learn-sso.trueoffice.com/simplesaml/module.php/saml/sp/metadata.php/<CUSTOMER_NAME>-sp`

    1. Dans la zone de texte **URL de connexion**, saisissez une URL au format suivant : `https://learn-sso.trueoffice.com/<CUSTOMER_NAME>`
    
        > [!NOTE]
        > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur et l’URL de connexion réels. Pour obtenir ces valeurs, contactez l’[équipe du support technique de True Office Learning - LIO](mailto:service@trueoffice.com). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **XML de métadonnées de fédération** et sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

1. Dans la section **Configurer True Office Learning - LIO**, copiez l’URL ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en accordant l’accès à True Office Learning - LIO.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **True Office Learning - LIO**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-true-office-learning---lio-sso"></a>Configurer l’authentification unique True Office Learning - LIO

Contactez l’[équipe du support technique de True Office Learning - LIO](mailto:service@trueoffice.com) si vous avez des questions sur la configuration et/ou pour demander une copie des métadonnées. Dans votre demande, fournissez les informations suivantes :
* Nom de l’entreprise.
* ID de l’entreprise (si vous le connaissez).
* Configuration existante ou nouvelle.

### <a name="create-true-office-learning---lio-test-user"></a>Créer un utilisateur de test True Office Learning - LIO

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans True Office Learning - LIO. Collaborez avec l’[équipe du support technique de True Office Learning - LIO](mailto:service@trueoffice.com) pour ajouter les utilisateurs dans la plateforme True Office Learning - LIO. Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous testez votre configuration de l’authentification unique Azure AD en utilisant les options suivantes :

* Dans le portail Azure, sélectionnez **Tester cette application**. Vous êtes redirigé vers l’URL de connexion à True Office Learning - LIO, d’où vous pouvez lancer le flux de connexion. 
* Accédez directement à l’URL de connexion à True Office Learning - LIO, puis lancez le processus de connexion à partir de ce site.
* Vous pouvez utiliser Mes applications de Microsoft. Quand vous sélectionnez la vignette True Office Learning - LIO dans Mes applications, vous êtes redirigé vers l’URL de connexion à True Office Learning - LIO. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez configuré True Office Learning - LIO, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
