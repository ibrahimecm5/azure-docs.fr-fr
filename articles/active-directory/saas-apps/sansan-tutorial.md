---
title: 'Tutoriel : Intégration d’Azure Active Directory à SanSan | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et SanSan.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/25/2021
ms.author: jeedes
ms.openlocfilehash: 009120a8e9f13c4f0da28a5587fb0931858669f3
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132299389"
---
# <a name="tutorial-integrate-sansan-with-azure-active-directory"></a>Tutoriel : Intégrer Sansan à Azure Active Directory

Dans ce tutoriel, vous allez apprendre à intégrer SanSan à Azure Active Directory (Azure AD). Quand vous intégrez Sansan à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Sansan.
* Permettre à vos utilisateurs de se connecter automatiquement à Sansan avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement SanSan pour lequel l’authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.
* Sansan prend en charge l’authentification unique lancée par le **fournisseur de services**.

## <a name="add-sansan-from-the-gallery"></a>Ajouter SanSan à partir de la galerie

Pour configurer l’intégration de SanSan à Azure AD, vous devez ajouter SanSan à partir de la galerie à votre liste d’applications SaaS managées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Sansan** dans la zone de recherche.
1. Sélectionnez **Sansan** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-sansan"></a>Configurer et tester l’authentification unique (SSO) Azure AD pour Sansan

Configurez et testez l’authentification unique Azure AD avec Sansan à l’aide d’un utilisateur de test appelé **Britta Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Sansan associé.

Pour configurer et tester l’authentification unique Azure AD avec Sansan, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
   1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
   1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Sansan](#configure-sansan-sso)** pour configurer les paramètres de l’authentification unique côté application.
   1. **[Créer un utilisateur de test Sansan](#create-sansan-test-user)** pour avoir dans Sansan un équivalent de Britta Simon lié à la représentation Azure AD associée.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **Sansan**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon pour **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la page **Configuration SAML de base**, effectuez les étapes suivantes :

   1. Dans la zone de texte **Identificateur (ID d’entité)** , saisissez une URL au format suivant : `https://ap.sansan.com/saml2/<COMPANY_NAME>`

   1. Dans la zone de texte **URL de réponse** , tapez une URL en utilisant un des modèles suivants :
    
       | Environnement | URL |
      |:--- |:--- |
      | PC |`https://ap.sansan.com/v/saml2/<COMPANY_NAME>/acs` |
      | Application smartphone |`https://internal.api.sansan.com/saml2/<COMPANY_NAME>/acs` |
      | Web smartphone |`https://ap.sansan.com/s/saml2/<COMPANY_NAME>/acs` |

   1. Dans la zone de texte **URL de connexion**, tapez l’URL : `https://ap.sansan.com/`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Vérifiez les valeurs réelles de l’identificateur et de l’URL de réponse dans les **paramètres d’administration Sansan**.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **Certificat (Base64)** , puis sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

   ![Lien Téléchargement de certificat](common/certificatebase64.png)

1. Dans la section **Configurer Sansan**, copiez l’URL ou les URL appropriées en fonction de vos besoins.

   ![Copier les URL de configuration](common/copy-configuration-urls.png)

   ```Logout URL
    https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0
    ```

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

Dans cette section, vous allez créer un utilisateur de test appelé Britta Simon dans le Portail Azure.

1. Dans le volet gauche du Portail Azure, sélectionnez **Azure Active Directory**, **Utilisateurs**, puis **Tous les utilisateurs**.
1. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.
1. Dans les propriétés **Utilisateur**, effectuez les étapes suivantes :
   1. Dans le champ **Nom**, entrez `Britta Simon`.  
   1. Dans le champ **Nom de l’utilisateur**, entrez username@companydomain.extension. Par exemple : `BrittaSimon@contoso.com`.
   1. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.
   1. Cliquez sur **Créer**.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à SanSan.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **SanSan**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.
1. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-sansan-sso"></a>Configurer l’authentification unique Sansan

Pour réaliser les **paramètres d’authentification unique** du côté de **Sansan** , suivez les étapes ci-dessous en fonction de vos besoins.

   * Version [japonaise](https://jp-help.sansan.com/hc/ja/articles/900001551383 ).

   * Version [anglaise](https://jp-help.sansan.com/hc/en-us/articles/900001551383 ).


### <a name="create-sansan-test-user"></a>Créer un utilisateur de test Sansan

Dans cette section, vous allez créer un utilisateur nommé Britta Simon dans Sansan. Pour plus d’informations sur la création d’un utilisateur, consultez [ces](https://jp-help.sansan.com/hc/articles/206508997-Adding-users) étapes.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Cette opération redirige vers l’URL de connexion Sansan, où vous pouvez lancer le processus de connexion. 

* Accédez directement à l'URL de connexion à Sansan pour initier le flux de connexion.

* Vous pouvez utiliser Mes applications de Microsoft. Un clic sur la vignette Sansan dans Mes applications vous redirige vers l'URL de connexion à Sansan. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré Sansan, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
