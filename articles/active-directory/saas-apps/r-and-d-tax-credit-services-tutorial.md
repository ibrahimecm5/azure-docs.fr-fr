---
title: 'Tutoriel : Intégration de l’authentification unique Azure AD à R and D Tax Credit Services : 10-wk Implementation'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et R and D Tax Credit Services.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/14/2021
ms.author: jeedes
ms.openlocfilehash: f7f6e25b1c276fdda310f394a9b939ea7f860cfa
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2021
ms.locfileid: "132000623"
---
# <a name="tutorial-azure-ad-sso-integration-with-r-and-d-tax-credit-services--10-wk-implementation"></a>Tutoriel : Intégration de l’authentification unique Azure AD à R and D Tax Credit Services : 10-wk Implementation

Dans ce tutoriel, vous allez apprendre à intégrer R and D Tax Credit Services à Azure Active Directory (Azure AD). Quand vous intégrez Azure AD à R and D Tax Credit Services, vous pouvez :

* Contrôler dans Azure AD qui a accès à R and D Tax Credit Services.
* Permettre aux utilisateurs de se connecter automatiquement à R and D Tax Credit Services avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement R and D Tax Credit Services pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* R and D Tax Credit Services prend en charge l’authentification unique lancée par le **fournisseur de services**.

> [!NOTE]
> L’identificateur de cette application étant une valeur de chaîne fixe, une seule instance peut être configurée dans un locataire.

## <a name="add-r-and-d-tax-credit-services-from-the-gallery"></a>Ajouter R and D Tax Credit Services à partir de la galerie

Pour configurer l’intégration de R and D Tax Credit Services avec Azure AD, vous devez ajouter R and D Tax Credit Services à votre liste d’applications SaaS managées à partir de la galerie.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **R and D Tax Credit Services** dans la zone de recherche.
1. Sélectionnez **R and D Tax Credit Services** dans le panneau des résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-r-and-d-tax-credit-services"></a>Configurer et tester l’authentification unique Azure AD pour R and D Tax Credit Services

Configurez et testez l’authentification unique Azure AD avec R and D Tax Credit Services pour un utilisateur de test nommé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur R and D Tax Credit Services associé.

Pour configurer et tester l’authentification unique Azure AD avec R and D Tax Credit Services, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique R and D Tax Credit Services](#configure-r-and-d-tax-credit-services-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur test R and D Tax Credit Services](#create-r-and-d-tax-credit-services-test-user)** pour avoir un équivalent de B.Simon dans R and D Tax Credit Services, lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **R and D Tax Credit Services**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    a. Dans la zone de texte **Identificateur (ID d’entité)**, tapez la valeur : `urn:pwcid:saml:sp:p`

    b. Dans la zone de texte **URL de réponse**, tapez l’URL : `https://login.pwc.com/openam/PWCIAuthConsumer/metaAlias/pwc/sp3`

    c. Dans la zone de texte **URL de connexion**, tapez l’URL : `https://researchcreditsolution.pwc.com/`

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **XML de métadonnées de fédération** et sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

1. Dans la section **R and D Tax Credit Services**, copiez la ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à R and D Tax Credit Services.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **R and D Tax Credit Services**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-r-and-d-tax-credit-services-sso"></a>Configurer l’authentification unique R and D Tax Credit Services

Pour configurer l’authentification unique côté **R and D Tax Credit Services**, vous devez envoyer le fichier **XML des métadonnées de fédération** téléchargé et les URL appropriées copiées à partir du portail Azure à l’[équipe du support technique de R and D Tax Credit Services](https://www.pwc.com/us/en/services/tax/specialized-tax/research-development-credit.html). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

### <a name="create-r-and-d-tax-credit-services-test-user"></a>Créer un utilisateur de test R and D Tax Credit Services

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans R and D Tax Credit Services. Rapprochez-vous de l’[équipe du support technique de R and D Tax Credit Services](https://www.pwc.com/us/en/services/tax/specialized-tax/research-development-credit.html) pour ajouter les utilisateurs à la plateforme R and D Tax Credit Services. Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Cette opération redirige vers l’URL de connexion à R and D Tax Credit Services, d’où vous pouvez lancer le flux de connexion. 

* Accédez directement à l’URL de connexion à R and D Tax Credit Services, puis lancez le flux de connexion à partir de là.

* Vous pouvez utiliser Mes applications de Microsoft. Lorsque vous cliquez sur la vignette R and D Tax Credit Services dans le dossier Mes applications, vous êtes redirigé vers l’URL de connexion à R and D Tax Credit Services. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez configuré R and D Tax Credit Services, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).