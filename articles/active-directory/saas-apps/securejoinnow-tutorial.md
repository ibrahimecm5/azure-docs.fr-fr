---
title: "Tutoriel : Intégration d'Azure Active Directory à SecureW2 JoinNow Connector | Microsoft Docs"
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et SecureW2 JoinNow Connector.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/10/2021
ms.author: jeedes
ms.openlocfilehash: 33b76193d88ba73215b11340570c60f15627acd9
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132299475"
---
# <a name="tutorial-integrate-securew2-joinnow-connector-with-azure-active-directory"></a>Tutoriel : Intégrer Azure Active Directory à SecureW2 JoinNow Connector

Dans ce tutoriel, vous allez apprendre à intégrer SecureW2 JoinNow Connector à Azure Active Directory (Azure AD). Lorsque vous intégrez SecureW2 JoinNow Connector à Azure AD, vous pouvez :

* Contrôler, dans Azure AD, qui a accès à SecureW2 JoinNow Connector.
* Autoriser les utilisateurs à se connecter automatiquement à SecureW2 JoinNow Connector avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement à SecureW2 JoinNow Connector pour lequel l’authentification unique (SSO) est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* SecureW2 JoinNow Connector prend en charge l’authentification unique lancée par le **fournisseur de services**.

## <a name="add-securew2-joinnow-connector-from-the-gallery"></a>Ajouter SecureW2 JoinNow Connector à partir de la galerie

Pour configurer l’intégration de SecureW2 JoinNow Connector à Azure AD, vous devez ajouter SecureW2 JoinNow Connector à votre liste d’applications SaaS gérées, à partir de la galerie.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **SecureW2 JoinNow Connector** dans la zone de recherche.
1. Sélectionnez **SecureW2 JoinNow Connector** dans le panneau de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-securew2-joinnow-connector"></a>Configurer et tester l’authentification unique Azure AD pour SecureW2 JoinNow Connector

Configurez et testez l’authentification unique Azure AD avec SecureW2 JoinNow Connector à l’aide d’un utilisateur de test nommé **B. Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur associé dans SecureW2 JoinNow Connector.

Pour configurer et tester l’authentification unique Azure AD avec SecureW2 JoinNow Connector, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique SecureW2 JoinNow Connector](#configure-securew2-joinnow-connector-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test SecureW2 JoinNow Connector](#create-securew2-joinnow-connector-test-user)** pour avoir un équivalent de B.Simon dans SecureW2 JoinNow Connector lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **SecureW2 JoinNow Connector**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon pour **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    a. Dans la zone de texte **URL de connexion**, saisissez une URL au format suivant : `https://<organization-identifier>-auth.securew2.com/auth/saml/SSO`

    b. Dans la zone de texte **Identificateur (ID d’entité)** , saisissez une URL au format suivant : `https://<organization-identifier>-auth.securew2.com/auth/saml`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Pour obtenir ces valeurs, contactez [l’équipe de support technique SecureW2 JoinNow Connector](mailto:support@securew2.com). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **XML des métadonnées**, puis sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

1. Dans la section **Configurer SecureW2 JoinNow Connector**, copiez la ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à SecureW2 JoinNow Connector.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **SecureW2 JoinNow Connector**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-securew2-joinnow-connector-sso"></a>Configurer l’authentification unique SecureW2 JoinNow Connector

Pour configurer l’authentification unique du côté de **SecureW2 JoinNow Connector**, vous devez envoyer les **métadonnées XML** téléchargées et les URL appropriées copiées à partir du portail Azure à [l’équipe du support technique de SecureW2 JoinNow Connector](mailto:support@securew2.com). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

### <a name="create-securew2-joinnow-connector-test-user"></a>Créer un utilisateur de test SecureW2 JoinNow Connector

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans SecureW2 JoinNow Connector. Rapprochez-vous de l’[équipe de support SecureW2 JoinNow Connector](mailto:support@securew2.com) pour ajouter les utilisateurs dans la plateforme SecureW2 JoinNow Connector. Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion de SecureW2 JoinNow Connector, où vous pouvez lancer le flux de connexion. 

* Accédez directement à l’URL de connexion de SecureW2 JoinNow Connector pour lancer le flux de connexion à partir de là.

* Vous pouvez utiliser Mes applications de Microsoft. Le fait de cliquer sur la vignette SecureW2 JoinNow Connector dans Mes applications vous redirige vers l’URL de connexion de SecureW2 JoinNow Connector. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré SecureW2 JoinNow Connector, vous pouvez appliquer le contrôle de session, qui protège en temps réel contre l’exfiltration et l’infiltration des données sensibles de votre organisation. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
