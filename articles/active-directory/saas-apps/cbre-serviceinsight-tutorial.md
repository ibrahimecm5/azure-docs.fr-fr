---
title: 'Tutoriel : Intégration de l’authentification unique Azure AD à CBRE ServiceInsight'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et CBRE ServiceInsight.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/13/2021
ms.author: jeedes
ms.openlocfilehash: 5dba2d369be849cf9299aa758b5412bd2ee3b71f
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130217143"
---
# <a name="tutorial-azure-ad-sso-integration-with-cbre-serviceinsight"></a>Tutoriel : Intégration de l’authentification unique Azure AD à CBRE ServiceInsight

Dans ce tutoriel, vous allez apprendre à intégrer CBRE ServiceInsight à Azure AD (Azure Active Directory). Quand vous intégrez CBRE ServiceInsight à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à CBRE ServiceInsight.
* Permettre à vos utilisateurs de se connecter automatiquement à CBRE ServiceInsight avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement CBRE ServiceInsight pour lequel l’authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* CBRE ServiceInsight prend en charge l’authentification unique lancée par le **fournisseur de services**.
* CBRE ServiceInsight prend en charge l’attribution d’utilisateurs **juste-à-temps**.

> [!NOTE]
> L’identificateur de cette application étant une valeur de chaîne fixe, une seule instance peut être configurée dans un locataire.

## <a name="add-cbre-serviceinsight-from-the-gallery"></a>Ajouter CBRE ServiceInsight à partir de la galerie

Pour configurer l’intégration de CBRE ServiceInsight à Azure AD, vous devez ajouter CBRE ServiceInsight depuis la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **CBRE ServiceInsight** dans la zone de recherche.
1. Sélectionnez **CBRE ServiceInsight** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-cbre-serviceinsight"></a>Configurer et tester l’authentification unique Azure AD pour CBRE ServiceInsight

Configurez et testez l’authentification unique Azure AD avec CBRE ServiceInsight pour un utilisateur de test nommé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur CBRE ServiceInsight associé.

Pour configurer et tester l’authentification unique Azure AD avec CBRE ServiceInsight, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique CBRE ServiceInsight](#configure-cbre-serviceinsight-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test CBRE ServiceInsight](#create-cbre-serviceinsight-test-user)** pour obtenir un équivalent de B.Simon dans CBRE ServiceInsight lié à la représentation Azure AD associée.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **CBRE ServiceInsight**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, effectuez l’étape suivante :

    Dans la zone de texte **URL de connexion**, tapez l’URL : `https://adfs4.mainstreamsasp.com/adfs/ls/`

    > [!NOTE]
    > Cette valeur n’est pas la valeur réelle. Mettez à jour la valeur avec l’URL de connexion réelle. Pour obtenir cette valeur, contactez l’[équipe du support technique de CBRE ServiceInsight](mailto:SISupport@cbre.com). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur le bouton Copier pour copier l’**URL des métadonnées de fédération d’application**, puis enregistrez-la sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/copy-metadataurl.png)

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à CBRE ServiceInsight.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **CBRE ServiceInsight**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-cbre-serviceinsight-sso"></a>Configurer l’authentification unique CBRE ServiceInsight

Pour configurer l’authentification unique côté **CBRE ServiceInsight**, vous devez envoyer l’**URL des métadonnées de fédération de l’application** à [l’équipe du support technique CBRE ServiceInsight](mailto:SISupport@cbre.com). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

### <a name="create-cbre-serviceinsight-test-user"></a>Créer l’utilisateur de test CBRE ServiceInsight

Dans cette section, un utilisateur appelé Britta Simon est créé dans CBRE ServiceInsight. CBRE ServiceInsight prend en charge le provisionnement juste-à-temps, option activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas encore d’utilisateur dans CBRE ServiceInsight, il en est créé un quand vous tentez d’y accéder.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL d’authentification de CBRE ServiceInsight, d’où vous pouvez lancer le processus de connexion. 

* Accédez directement à l’URL d’authentification de CBRE ServiceInsight pour lancer le processus de connexion.

* Vous pouvez utiliser Mes applications de Microsoft. Lorsque vous cliquez sur la vignette CBRE ServiceInsight dans Mes applications, vous êtes redirigé vers l’URL d’authentification de CBRE ServiceInsight. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez configuré CBRE ServiceInsight, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).