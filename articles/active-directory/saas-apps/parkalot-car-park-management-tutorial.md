---
title: 'Tutoriel : Intégration de l’authentification unique (SSO) Azure Active Directory à Parkalot - Car park management | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Parkalot - Car park management.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/11/2021
ms.author: jeedes
ms.openlocfilehash: 9fecce6322384b8a6efea1dacc6ac4a0f332d146
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132341993"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-parkalot---car-park-management"></a>Tutoriel : Intégration de l’authentification unique (SSO) Azure Active Directory à Parkalot - Car park management

Ce tutoriel explique comment intégrer Parkalot - Car park management à Azure Active Directory (Azure AD). Lorsque vous intégrez Parkalot - Car park management à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Parkalot - Car park management.
* Permettre à vos utilisateurs de se connecter automatiquement à Parkalot - Car park management avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Parkalot - Car park management pour lequel l’authentification unique (SSO) est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* Parkalot - Car park management prend en charge l’authentification unique initiée par le **fournisseur de services**

* Parkalot - Car park management prend en charge le provisionnement d’utilisateurs **juste-à-temps**

## <a name="adding-parkalot---car-park-management-from-the-gallery"></a>Ajout de Parkalot - Car park management à partir de la galerie

Pour configurer l’intégration de Parkalot - Car park management à Azure AD, vous devez ajouter Parkalot - Car park management à votre liste d’applications SaaS managées, à partir de la galerie.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Parkalot - Car park management** dans la zone de recherche.
1. Sélectionnez **Parkalot - Car park management** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.


## <a name="configure-and-test-azure-ad-sso-for-parkalot---car-park-management"></a>Configurer et tester l’authentification unique Azure AD pour Parkalot - Car park management

Configurez et testez l’authentification unique Azure AD auprès de Parkalot - Car park management à l’aide d’un utilisateur de test nommé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Parkalot - Car park management associé.

Pour configurer et tester l’authentification unique Azure AD auprès de Parkalot - Car park management, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Parkalot - Car park management](#configure-parkalot-car-park-management-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test Parkalot - Car park management](#create-parkalot-car-park-management-test-user)** pour avoir, dans Parkalot - Car park management, un équivalent de B.Simon lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **Parkalot - Car park management**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, entrez les valeurs pour les champs suivants :

    a. Dans la zone de texte **Identificateur (ID d’entité)** , tapez une URL en utilisant un des modèles suivants :

    | Identificateur (ID d'entité) |
    | -------------- |
    | `https://parkalot.io` |
    | `https://<CUSTOMERNAME>.parkalot.io` |
    |

    b. Dans la zone de texte **URL de réponse** , tapez une URL en utilisant un des modèles suivants :

    | URL de réponse |
    | -------------- |
    | `https://<CUSTOMERNAME>.parkalot.io` |
    | `https://parkalot-saml.firebaseapp.com/__/auth/handler` |
    | `https://parkalot-saml.web.app/__/auth/handler` |
    | `https://<CustomerName>.parkalot.io/__/auth/handler` |
    |

    c. Dans la zone de texte **URL de connexion**, tapez une URL en utilisant un des modèles suivants :

    | URL de connexion |
    | -------------- |
    | `https://<CUSTOMERNAME>.parkalot.io/#/login` |
    | `https://parkalot-saml.firebaseapp.com/#/login` |
    | `https://parkalot-saml.web.app/#/login` |
    |

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL de réponse et l’URL de connexion réels. Pour obtenir ces valeurs, contactez l’[équipe du support technique de Parkalot - Car park management](mailto:contact-us@parkalot.io). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **Certificat (en base64)** , puis sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

1. Dans la section **Configurer Parkalot - Car park management**, copiez la ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en accordant l’accès à Parkalot - Car park management.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Parkalot - Car park management**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-parkalot-car-park-management-sso"></a>Configurer l’authentification unique Parkalot - Car park management

1. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Parkalot - Car park management en tant qu’administrateur.

1. Sélectionnez **Setup SAML** (Configurer SAML), puis cliquez sur l’icône **Edit** (Modifier) dans la zone d’ajout d’une nouvelle carte **Add New**.

    ![Icône EDIT de la zone Add New.](./media/parkalot-car-park-management-tutorial/setup-saml.png)

1. Effectuez les étapes mentionnées ci-dessous, dans la page suivante.

    ![Configurez l’authentification unique Parkalot - Car park management.](./media/parkalot-car-park-management-tutorial/configuration.png)

    a. Dans la zone de texte **Display Name** (Nom d’affichage), indiquez un nom valide.

    b. Dans la zone de texte **IdP Entity ID** (ID d’entité de fournisseur d’identité), collez la valeur de l’**Identificateur Azure AD** que vous avez copiée dans le portail Azure.

    c. Dans la zone de texte **SSO url** (url SSO), collez la valeur de l’**URL de connexion** que vous avez copiée dans le portail Azure.

    d. Ouvrez le **Certificat (en base64)** téléchargé à partir du portail Azure dans le Bloc-notes, puis collez le contenu dans la zone de texte **Certificat**.

    e. Cliquez sur **ENREGISTRER**.

### <a name="create-parkalot-car-park-management-test-user"></a>Créer un utilisateur de test Parkalot - Car park Management

Dans cette section, un utilisateur nommé Britta Simon est créé dans Parkalot - Car park management. Parkalot - Car park management prend en charge le provisionnement d’utilisateurs juste-à-temps, option qui est activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas encore d’utilisateur dans Parkalot - Car park management, il en est créé un après l’authentification.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Cette opération redirige vers l’URL de connexion à Parkalot - Car park management, d’où vous pouvez lancer le flux de connexion. 

* Accédez directement à l’URL de connexion Parkalot - Car park management pour lancer le processus de connexion.

* Vous pouvez utiliser Mes applications de Microsoft. Le fait de cliquer sur la vignette Parkalot - Car park Management dans Mes applications redirige vers l’URL de connexion à Parkalot - Car park management. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).


## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré Parkalot - Car park management, vous pouvez appliquer le contrôle de session, qui protège en temps réel contre l’exfiltration et l’infiltration des données sensibles de votre organisation. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
