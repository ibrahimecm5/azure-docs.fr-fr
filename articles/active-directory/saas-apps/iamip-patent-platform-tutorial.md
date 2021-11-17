---
title: 'Tutoriel : Intégration de l’authentification unique Azure AD à IamIP Platform'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et IamIP Platform.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/21/2021
ms.author: jeedes
ms.openlocfilehash: e3264114f9bf8ec4f39225c80862c4b42016ed33
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132311172"
---
# <a name="tutorial-azure-ad-sso-integration-with-iamip-platform"></a>Tutoriel : Intégration de l’authentification unique Azure AD à IamIP Platform

Dans ce tutoriel, vous allez découvrir comment effectuer une intégration entre IamIP Platform et Azure Active Directory (Azure AD). L’intégration entre IamIP Platform et Azure AD permet d’effectuer plusieurs actions :

* Contrôler dans Azure AD qui a accès à IamIP Platform
* Permettre aux utilisateurs de se connecter automatiquement à IamIP Platform avec leur compte Azure AD
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement IamIP Platform pour lequel l’authentification unique (SSO, Single Sign-On) est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification SSO Azure AD dans un environnement de test.

* IamIP Platform prend en charge l’authentification unique à l’initiative du fournisseur de services et l’authentification unique à l’initiative du fournisseur d’identité.
* IamIP Platform prend en charge l’attribution d’utilisateurs **juste-à-temps**.

> [!NOTE]
> L’identificateur de cette application étant une valeur de chaîne fixe, une seule instance peut être configurée dans un locataire.

## <a name="add-iamip-platform-from-the-gallery"></a>Ajout d’IamIP Platform à partir de la galerie

Pour configurer l’intégration d’IamIP Platform à Azure AD, vous devez ajouter IamIP Platform à votre liste d’applications SaaS gérées à partir de la galerie.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire, ou avec un compte Microsoft personnel.
1. Sélectionnez **Azure Active Directory** dans le volet de gauche.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **IamIP Platform** dans la zone de recherche.
1. Sélectionnez **IamIP Platform** dans le panneau de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-iamip-platform"></a>Configuration et test de l’authentification unique Azure AD pour IamIP Platform

Configurez et testez l’authentification unique Azure AD avec IamIP Platform à l’aide d’un utilisateur de test nommé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur IamIP Platform associé.

Pour configurer et tester l’authentification unique Azure AD avec IamIP Platform, procédez comme suit :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique IamIP Platform](#configure-iamip-platform-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test IamIP Platform](#create-iamip-platform-test-user)** pour qu’il existe dans IamIP Platform un équivalent de B.Simon lié à la représentation Azure AD associée.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Sur le Portail Azure, accédez à la page d’intégration de l’application **IamIP Platform**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, l’utilisateur n’a rien à faire, car l’application est déjà intégrée à Azure.

1. Dans la section **Configuration SAML de base**, si vous souhaitez configurer l’application avec le mode à l’initiative du **fournisseur de services**, procédez comme suit :

    a. Dans la zone de texte **Identificateur**, tapez l’URL : `https://accounts.iamip.com/`
    
    b. Dans la zone de texte **URL de réponse**, tapez l’une des URL suivantes :

    | **URL de réponse** |
    |---------|
    | `https://accounts.iamip.com/sso-callback` |
    | `https://accounts.iamip.com/sso-login` |
    | `https://accounts.iamip.com/sso-logout` |

    c. Dans la zone de texte **URL d’authentification**, tapez l’une des URL suivantes :

    | **URL d’authentification** |
    |------|
    | `https://accounts.iamip.com/login` |
    | `https://patents.iamip.com/login-user` |

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **Certificat (en base64)** , puis sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

1. Dans la section **Configurer IamIP Platform**, copiez la ou les URL appropriées en fonction de vos besoins.

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

Dans cette section, vous allez créer un utilisateur de test nommé B.Simon dans le portail Azure.

1. Dans le volet gauche du portail Azure, sélectionnez **Azure Active Directory**. Sélectionnez **Utilisateurs**, puis **Tous les utilisateurs**.
1. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.
1. Dans les propriétés **Utilisateur**, effectuez les étapes suivantes :
   1. Dans la zone **Nom**, entrez **B.Simon**.  
   1. Dans la zone **Nom d’utilisateur**, entrez \<username>@\<companydomain>.\<extension>. Par exemple : `B.Simon@contoso.com`.
   1. Sélectionnez **Afficher le mot de passe**, puis notez la valeur affichée dans la zone **Mot de passe**.
   1. Sélectionnez **Create** (Créer).

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à IamIP Platform.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **IamIP Platform**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-iamip-platform-sso"></a>Configuration de l’authentification unique IamIP Platform

Pour configurer l’authentification unique côté **IamIP Platform**, vous devez envoyer le **certificat (Base64)** téléchargé et les URL copiées sur le Portail Azure à [l’équipe de support IamIP Platform](mailto:info@iamip.com). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

### <a name="create-iamip-platform-test-user"></a>Création d’un utilisateur de test IamIP Platform

Dans cette section, un utilisateur nommé Britta Simon est créé dans IamIP Platform. IamIP Platform prend en charge l’attribution d’utilisateurs juste-à-temps, qui est activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas encore d’utilisateur dans IamIP Platform, il en est créé un après l’authentification.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

#### <a name="sp-initiated"></a>Lancée par le fournisseur de services :

* Cliquez sur **Tester cette application** dans le portail Azure. Vous accédez alors automatiquement à l’URL de connexion à IamIP Platform, d’où vous pouvez lancer le flux de connexion.  

* Accédez directement à l’URL de connexion à IamIP Platform pour lancer le flux de connexion.

#### <a name="idp-initiated"></a>Lancée par le fournisseur d’identité :

* Cliquez sur **Tester cette application** sur le Portail Azure. La connexion à l’application IamIP Platform pour laquelle vous avez configuré l’authentification unique est établie automatiquement. 

Vous pouvez aussi utiliser Mes applications de Microsoft pour tester l’application dans n’importe quel mode. Quand vous cliquez sur la vignette IamIP Platform dans Mes applications, vous accédez automatiquement à la page de connexion à l’application pour lancer le flux de connexion si le mode Fournisseur de services est configuré ; s’il s’agit du mode Fournisseur d’identité, la connexion est automatiquement établie avec l’application IamIP Platform pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez configuré IamIP Platform, vous pouvez appliquer le contrôle de session, qui protège votre organisation en temps réel contre l’exfiltration et l’infiltration de ses données sensibles. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
