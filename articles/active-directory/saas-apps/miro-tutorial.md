---
title: 'Tutoriel : Intégration de Azure Active Directory à Miro | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Miro.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/26/2021
ms.author: jeedes
ms.openlocfilehash: 6e2b3c611c5bc06e29fb739f01dc69605509a63a
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124795181"
---
# <a name="tutorial-integrate-miro-with-azure-active-directory"></a>Tutoriel : Intégrer Miro à Azure Active Directory

Dans ce tutoriel, vous allez apprendre à intégrer Miro à Azure Active Directory (Azure AD). Une autre version de ce tutoriel est disponible sur help.miro.com. Quand vous intégrez Miro à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Miro.
* Permettre à vos utilisateurs de se connecter automatiquement à Miro avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Miro pour lequel l’authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test. 
* Miro prend en charge l’authentification unique lancée par **le fournisseur d’identité et le fournisseur de service** ainsi que le provisionnement d’utilisateurs **juste-à-temps**.
* Miro prend en charge [l’attribution d’utilisateurs et la suppression des privilèges d’accès **automatisées** des utilisateurs](miro-provisioning-tutorial.md) (recommandé).

> [!NOTE]
> L’identificateur de cette application étant une valeur de chaîne fixe, une seule instance peut être configurée dans un locataire.

## <a name="add-miro-from-the-gallery"></a>Ajouter Miro à partir de la galerie

Pour configurer l’intégration de Miro avec Azure AD, vous devez ajouter Miro à partir de la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Miro** dans la zone de recherche.
1. Sélectionnez **Miro** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-miro"></a>Configurer et tester l’authentification unique Azure AD pour Miro

Configurez et testez l’authentification unique Azure AD avec Miro à l’aide d’un utilisateur de test appelé **B. Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Miro associé.

Pour configurer et tester l’authentification unique Azure AD auprès de Miro, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Miro](#configure-miro-sso)** pour configurer les paramètres d’authentification unique côté application.
    1. **[Créer un utilisateur de test Miro](#create-miro-test-user)** pour avoir, dans Miro, un équivalent de B.Simon lié à la représentation Azure AD associée.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **Miro**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon pour **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, si vous souhaitez configurer l’application en mode lancé par le **fournisseur d’identité**, effectuez l’étape suivante :

    Dans la zone de texte **Identificateur**, tapez l’URL : `https://miro.com/`

5. Si vous souhaitez configurer l’application en mode lancé par le **fournisseur de service**, dans la zone de texte **URL de connexion**, tapez l’URL `https://miro.com/sso/login/`

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **Certificat (Base64)** , puis sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur. Vous en aurez besoin pour configurer l’authentification unique côté Miro.

   ![Lien Téléchargement de certificat](common/certificatebase64.png "Lien Téléchargement de certificat")

1. Dans la section **Configurer Miro**, copiez l’URL de connexion. Vous en aurez besoin pour configurer l’authentification unique côté Miro.

   ![Copier l’URL de connexion](./media/miro-tutorial/login.png "Copier l’URL de connexion")

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

Dans cette section, vous allez autoriser B. Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Miro.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Miro**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

* Vous pouvez également accéder aux **Propriétés** de l’application et désactiver **Affectation de l’utilisateur obligatoire**
![Désactiver l’exigence d’affectation](./media/miro-tutorial/properties.png "Désactiver l’exigence d’affectation")

## <a name="configure-miro-sso"></a>Configurer l’authentification unique Miro

Pour configurer l’authentification unique côté Miro, utilisez le certificat précédemment téléchargé et l’URL de connexion précédemment copiée. Dans les paramètres du compte Miro, accédez à la section **Security** (Sécurité), puis activez l’option **Enable SSO/SAML** (Activer SSO/SAML). 

1. Collez l’URL de connexion dans le champ **SAML Sign-in URL** (URL de connexion SAML).
1. Ouvrez le fichier de certificat avec un éditeur de texte, puis copiez la séquence de certificat. Collez la séquence dans le champ **Key x509 Certificate** (Certificat x509 de clé).
![Paramètres Miro](./media/miro-tutorial/security.png "Paramètres Miro")

1. Dans le champ **Domaines**, tapez l’adresse de votre domaine, cliquez sur **Ajouter**, puis suivez la procédure de vérification. Répétez cette procédure pour vos autres adresses de domaine, le cas échéant. La fonctionnalité d’authentification unique de Miro fonctionne pour les utilisateurs finaux dont les domaines figurent dans la liste. 
![Domaine](./media/miro-tutorial/add-domain.png "Domaine")

1. Déterminez si vous allez utiliser le provisionnement juste-à-temps (en extrayant vos utilisateurs dans votre abonnement lors de leur inscription dans Miro), puis cliquez sur **Enregistrer** pour terminer la configuration de l’authentification unique côté Miro.
![Provisionnement juste-à-temps](./media/miro-tutorial/save-configuration.png "Provisionnement juste-à-temps") 

### <a name="create-miro-test-user"></a>Créer un utilisateur de test Miro

Dans cette section, un utilisateur appelé B. Simon est créé dans Miro. Miro prend en charge l’attribution d’utilisateurs juste-à-temps, option activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas encore d’utilisateur dans Miro, il en est créé un après l’authentification.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes à l’aide de l’utilisateur de test B.Simon. 

#### <a name="sp-initiated"></a>Lancée par le fournisseur de services :

* Accédez directement à l’URL de connexion Miro et lancez le processus de connexion.

#### <a name="idp-initiated"></a>Lancée par le fournisseur d’identité :

* Cliquez sur **Tester cette application** dans le portail Azure, puis choisissez de vous connecter en tant que B.Simon. Vous devez être automatiquement connecté à l’abonnement Miro pour lequel vous avez configuré l’authentification unique. 

Vous pouvez aussi utiliser Mes applications de Microsoft pour tester l’application dans n’importe quel mode. Si, quand vous cliquez sur la vignette Miro dans Mes applications, le mode Fournisseur de services est configuré, vous êtes redirigé vers la page de connexion de l’application pour lancer le flux de connexion ; s’il s’agit du mode Fournisseur d’identité, vous êtes automatiquement connecté à l’instance de Miro pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).