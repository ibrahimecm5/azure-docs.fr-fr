---
title: 'Didacticiel : Intégration d’Azure Active Directory à Ariba | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Ariba.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/01/2021
ms.author: jeedes
ms.openlocfilehash: 3903fbaa3e72f48d8b00fb45f75f3077119b9e9b
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132288576"
---
# <a name="tutorial-azure-active-directory-integration-with-ariba"></a>Didacticiel : Intégration d’Azure Active Directory à Ariba

Dans ce tutoriel, vous allez apprendre à intégrer Ariba à Azure Active Directory (Azure AD). Quand vous intégrez Ariba à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Ariba.
* Permettre à vos utilisateurs de se connecter automatiquement à Ariba avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Ariba pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Ariba prend en charge l’authentification unique (SSO) initiée par le **fournisseur de services**.

## <a name="add-ariba-from-the-gallery"></a>Ajouter Ariba à partir de la galerie

Pour configurer l’intégration d’Ariba à Azure AD, vous devez ajouter Ariba à partir de la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Ariba** dans la zone de recherche.
1. Sélectionnez **Ariba** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-ariba"></a>Configurer et tester l’authentification unique Azure AD pour Ariba

Configurez et testez l’authentification unique Azure AD auprès d’Ariba à l’aide d’un utilisateur de test nommé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Ariba associé.

Pour configurer et tester l’authentification unique Azure AD auprès d’Ariba, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Ariba](#configure-ariba-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test Ariba](#create-ariba-test-user)** pour avoir, dans Ariba, un équivalent de B.Simon lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **Ariba**, recherchez la section **Gérer**, puis sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL Ariba](common/sp-identifier.png)

    a. Dans la zone de texte **URL de connexion**, saisissez une URL au format suivant : 
    
    ```http
    https://<subdomain>.sourcing.ariba.com
    https://<subdomain>.supplier.ariba.com
    ```

    b. Dans la zone de texte **Identificateur (ID d’entité)** , saisissez une URL au format suivant : `http://<subdomain>.procurement-2.ariba.com`

    c. Dans **URL de réponse**, entrez l’un des modèles d’URL suivants :

    | URL de réponse|
    |----------|
    | `https://<subdomain>.ariba.com/CUSTOM_URL` |
    | `https://<subdomain>.procurement-eu.ariba.com/CUSTOM_URL` |
    | `https://<subdomain>.procurement-eu.ariba.com` |
    | `https://<subdomain>.procurement-2.ariba.com` |
    | `https://<subdomain>.procurement-2.ariba.com/CUSTOM_URL` |

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de réponse, l’identificateur et l’URL de connexion réels. Nous vous suggérons d’utiliser ici la valeur de chaîne unique dans l’identificateur. Pour obtenir ces valeurs, contactez l’équipe du support technique d’Ariba au **1-866-218-2155**. Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

5. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (Base64)** en fonction des options définies par rapport à vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en accordant l’accès à Ariba.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Ariba**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-ariba-sso"></a>Configurer l’authentification unique Ariba

Pour configurer l’authentification unique dans votre application, appelez l’équipe du support technique d’Ariba au **1-866-218-2155**. Elle vous expliquera comment procéder pour lui envoyer le fichier **Certificat (Base64)** téléchargé.

### <a name="create-ariba-test-user"></a>Créer un utilisateur de test Ariba

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans Ariba. Contactez l’équipe du support technique d’Ariba en appelant le **1-866-218-2155** pour ajouter les utilisateurs à la plateforme Ariba. Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion à Ariba, d’où vous pouvez lancer le flux de connexion. 

* Accédez directement à l’URL de connexion à Ariba et lancez le flux de connexion.

* Vous pouvez utiliser Mes applications de Microsoft. Un clic sur la vignette Ariba dans Mes applications vous redirige vers l’URL de connexion à Ariba. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré Ariba, vous pouvez appliquer le contrôle de session, qui protège en temps réel contre l’exfiltration et l’infiltration des données sensibles de votre organisation. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
