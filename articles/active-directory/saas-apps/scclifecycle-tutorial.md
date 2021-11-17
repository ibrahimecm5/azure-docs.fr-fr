---
title: 'Didacticiel : Intégration d’Azure Active Directory avec SCC LifeCycle | Microsoft Docs'
description: Découvrez comment configurer l'authentification unique entre Azure Active Directory et SCC LifeCycle.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/17/2021
ms.author: jeedes
ms.openlocfilehash: 3e0ce05f7fc51e7a8c8c7f420e88295b6f7167cd
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132308804"
---
# <a name="tutorial-azure-active-directory-integration-with-scc-lifecycle"></a>Didacticiel : Intégration d’Azure AD avec SCC LifeCycle

Dans ce tutoriel, vous allez apprendre à intégrer SCC LifeCycle à Azure Active Directory (Azure AD). Quand vous intégrez SCC LifeCycle à Azure AD, vous pouvez :

* dans Azure AD, contrôler qui a accès à SCC LifeCycle.
* permettre à vos utilisateurs de se connecter automatiquement à SCC LifeCycle avec leurs comptes Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD avec SCC LifeCycle, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un [compte gratuit](https://azure.microsoft.com/free/).
* Abonnement SCC LifeCycle pour lequel l’authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* SCC LifeCycle prend en charge l’authentification unique lancée par le **fournisseur de services**.

## <a name="add-scc-lifecycle-from-the-gallery"></a>Ajouter SCC LifeCycle à partir de la galerie

Pour configurer l’intégration de SCC LifeCycle à Azure AD, vous devez ajouter SCC LifeCycle à partir de la galerie à votre liste d’applications SaaS managées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **SCC LifeCycle** dans la zone de recherche.
1. Sélectionnez **SCC LifeCycle** dans le volet des résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-scc-lifecycle"></a>Configurer et tester l’authentification unique Azure AD pour SCC LifeCycle

Configurez et testez l’authentification unique Azure AD avec SCC LifeCycle pour un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur associé dans SCC LifeCycle.

Pour configurer et tester l’authentification unique Azure AD avec SCC LifeCycle, procédez comme suit :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique SCC LifeCycle](#configure-scc-lifecycle-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test SCC LifeCycle](#create-scc-lifecycle-test-user)** pour avoir un équivalent de B.Simon dans SCC LifeCycle lié à la représentation de l’utilisateur Azure AD.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le Portail Azure, accédez à la page d’intégration de l’application **SCC LifeCycle**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    a. Dans la zone de texte **Identificateur (ID d’entité)** , tapez une URL en utilisant un des modèles suivants :
    
    | **Identificateur** |
    |----------|
    | `https://bs1.scc.com/<entity>` |
    | `https://lifecycle.scc.com/<entity>` |

    b. Dans la zone de texte **URL de connexion**, saisissez une URL au format suivant : `https://<sub-domain>.scc.com/ic7/welcome/customer/PICTtest.aspx`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur et l’URL de connexion réels. Pour obtenir ces valeurs, contactez l’[équipe de support technique SCC LifeCycle](mailto:lifecycle.support@scc.com). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

5. Sur la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le fichier **XML de métadonnées de fédération** en fonction des options définies selon vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

6. Dans la section **Configurer SCC LifeCycle**, copiez la ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à SCC LifeCycle.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **SCC LifeCycle**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-scc-lifecycle-sso"></a>Configurer l’authentification unique SCC LifeCycle

Pour configurer l’authentification unique côté **SCC LifeCycle**, vous devez envoyer le fichier **XML des métadonnées** téléchargé et les URL appropriées, copiées à partir du portail Azure, à l’[équipe du support SCC LifeCycle](mailto:lifecycle.support@scc.com). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

   > [!NOTE]
   > L’authentification unique doit être activée par l’[équipe du support SCC LifeCycle](mailto:lifecycle.support@scc.com).

### <a name="create-scc-lifecycle-test-user"></a>Créer un utilisateur de test SCC LifeCycle

Pour se connecter à SCC LifeCycle, les utilisateurs d’Azure AD doivent être approvisionnés dans SCC LifeCycle. Vous n’avez rien à faire pour configurer l’approvisionnement des utilisateurs dans SCC LifeCycle.

Lorsqu’un utilisateur tente de se connecter à SCC LifeCycle, un compte SCC LifeCycle est, au besoin, automatiquement créé.

> [!NOTE]
> Le titulaire du compte Azure Active Directory reçoit un e-mail contenant un lien à suivre pour confirmer son compte et l’activer.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion à SCC LifeCycle, où vous pouvez lancer le flux de connexion. 

* Accédez directement à l’URL de connexion de SCC LifeCycle pour y initier le flux de connexion.

* Vous pouvez utiliser Mes applications de Microsoft. Quand vous cliquez sur la mosaïque SCC LifeCycle dans Mes applications, vous êtes redirigé vers l’URL de connexion de SCC LifeCycle. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez configuré SCC LifeCycle, vous pouvez appliquer le contrôle de session, qui protège l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
