---
title: 'Tutoriel : Intégration d’Azure Active Directory à Vtiger CRM (SAML) | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Vtiger CRM (SAML).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/31/2021
ms.author: jeedes
ms.openlocfilehash: b108bbd77676c172f97b1ec224e1380af6f5f030
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132298484"
---
# <a name="tutorial-integrate-vtiger-crm-saml-with-azure-active-directory"></a>Tutoriel : Intégrer Vtiger CRM (SAML) à Azure Active Directory

Dans ce tutoriel, vous allez apprendre à intégrer Vtiger CRM (SAML) à Azure Active Directory (Azure AD). Quand vous intégrez Vtiger CRM (SAML) à Azure AD, vous pouvez :

* Contrôler qui a accès à Vtiger CRM (SAML) dans Azure AD.
* Permettre à vos utilisateurs de se connecter automatiquement à Vtiger CRM (SAML) avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Vtiger CRM (SAML) pour lequel l’authentification unique (SSO) est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test. 

* Vtiger CRM (SAML) prend en charge l’authentification unique lancée par le **fournisseur de services**.
* Vtiger CRM (SAML) prend en charge le provisionnement d’utilisateur **juste-à-temps**.

## <a name="add-vtiger-crm-saml-from-the-gallery"></a>Ajouter Vtiger CRM (SAML) à partir de la galerie

Pour configurer l’intégration de Vtiger CRM (SAML)à Azure AD, vous devez ajouter Vtiger CRM (SAML) à partir de la galerie à votre liste d’applications SaaS managées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Vtiger CRM (SAML)** dans la zone de recherche.
1. Sélectionnez **Vtiger CRM (SAML)** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-vtiger-crm-saml"></a>Configurer et tester l’authentification unique Azure AD pour Vtiger CRM (SAML)

Configurez et testez l’authentification unique Azure AD avec Vtiger CRM (SAML) pour un utilisateur de test nommé **B. Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Vtiger CRM (SAML) associé.

Pour configurer et tester l’authentification unique Azure AD avec Vtiger CRM (SAML), effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Vtiger CRM (SAML)](#configure-vtiger-crm-saml-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test Vtiger CRM (SAML)](#create-vtiger-crm-saml-test-user)** pour avoir un équivalent de B.Simon dans Vtiger CRM (SAML) associé à sa représentation dans Azure AD.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **Vtiger CRM (SAML)** , recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon pour **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la page **Configuration SAML de base**, effectuez les étapes suivantes :

   a. Dans la zone de texte **Identificateur (ID d’entité)** , saisissez une URL au format suivant : `https://<CUSTOMER_INSTANCE>.od1.vtiger.com/sso/saml?acs`

    b. Dans la zone de texte **URL de connexion**, tapez une URL en utilisant un des modèles suivants :

   | URL d’authentification |
   |---|
   |`https://<CUSTOMER_INSTANCE>.od1.vtiger.com`|
   |`https://<CUSTOMER_INSTANCE>.od2.vtiger.com`|
   |`https://<CUSTOMER_INSTANCE>.od1.vtiger.ws`|
   |

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Pour obtenir ces valeurs, contactez l’[équipe du support technique Vtiger CRM (SAML)](mailto:support@vtiger.com). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **Certificat (Base64)** , puis sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

   ![Lien Téléchargement de certificat](common/certificatebase64.png)

1. Dans la section **Configurer Vtiger CRM (SAML)**, copiez l’URL ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B. Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Vtiger CRM (SAML).

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Vtiger CRM (SAML)**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-vtiger-crm-saml-sso"></a>Configurer l’authentification unique Vtiger CRM (SAML)

Pour configurer l’authentification unique côté **Vtiger CRM (SAML)**, vous devez envoyer le **certificat (Base64)** téléchargé et les URL copiées appropriées du portail Azure à l’[équipe du support Vtiger CRM (SAML)](mailto:support@vtiger.com). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

### <a name="create-vtiger-crm-saml-test-user"></a>Créer un utilisateur de test Vtiger CRM (SAML)

Dans cette section, un utilisateur appelé Britta Simon est créé dans Vtiger CRM (SAML). Vtiger CRM (SAML) prend en charge l’attribution d’utilisateurs juste-à-temps, une option activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. Si l’utilisateur souhaité n’existe pas déjà dans Vtiger CRM (SAML), il est créé après l’authentification.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion Vtiger CRM (SAML), d’où vous pouvez lancer le flux de connexion. 

* Accédez directement à l’URL d’authentification Vtiger CRM (SAML) pour lancer le flux de connexion.

* Vous pouvez utiliser Mes applications de Microsoft. Un clic sur la vignette Vtiger CRM (SAML) dans Mes applications vous redirige vers l’URL de connexion à Vtiger CRM (SAML). Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré Vtiger CRM (SAML), vous pouvez appliquer le contrôle de session, qui protège en temps réel contre l’exfiltration et l’infiltration des données sensibles de votre organisation. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
