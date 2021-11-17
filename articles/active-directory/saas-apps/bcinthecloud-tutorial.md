---
title: 'Tutoriel : Intégration de l’authentification unique Azure AD à BC in the Cloud'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et BC in the Cloud.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/20/2021
ms.author: jeedes
ms.openlocfilehash: 17bd44f54206af69ed2291e05d5df1d5618d2744
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132324342"
---
# <a name="tutorial-azure-ad-sso-integration-with-bc-in-the-cloud"></a>Tutoriel : Intégration de l’authentification unique Azure AD à BC in the Cloud

Dans ce tutoriel, vous allez découvrir comment intégrer BC in the Cloud à Azure Active Directory (Azure AD). Quand vous intégrez BC in the Cloud à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à BC in the Cloud.
* Autoriser les utilisateurs à se connecter automatiquement à BC in the Cloud avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement BC in the Cloud pour lequel l’authentification unique (SSO) est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* BC in the Cloud prend en charge l’authentification unique lancée par le **fournisseur de services**.

> [!NOTE]
> L’identificateur de cette application étant une valeur de chaîne fixe, une seule instance peut être configurée dans un locataire.

## <a name="add-bc-in-the-cloud-from-the-gallery"></a>Ajouter BC in the Cloud à partir de la galerie

Pour configurer l’intégration de BC in the Cloud à Azure AD, vous devez ajouter BC in the Cloud de la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, entrez **BC in the Cloud** dans la zone de recherche.
1. Sélectionnez **BC in the Cloud** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-bc-in-the-cloud"></a>Configurer et tester l’authentification unique Azure AD pour BC in the Cloud

Configurez et testez l’authentification unique Azure AD avec BC in the Cloud pour un utilisateur test nommé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur correspondant dans BC in the Cloud.

Pour configurer et tester l’authentification unique Azure AD avec BC in the Cloud, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique BC in the Cloud](#configure-bc-in-the-cloud-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur test BC in the Cloud](#create-bc-in-the-cloud-test-user)** pour avoir, dans BC in the Cloud, un équivalent de B.Simon lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, dans la page d’intégration de l’application **BC in the Cloud**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    a. Dans la zone de texte **Identificateur (ID d’entité)** , tapez l’URL suivante : `https://app.bcinthecloud.com`

    b. Dans la zone de texte **URL de connexion**, saisissez une URL au format suivant : `https://app.bcinthecloud.com/router/loginSaml/<customerid>`

    > [!NOTE]
    > Cette valeur n’est pas la valeur réelle. Mettez à jour cette valeur avec l’URL d’authentification réelle. Pour obtenir cette valeur, contactez [l’équipe du support BC in the Cloud](https://www.bcinthecloud.com/supportcenter/).

5. Sur la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le fichier **XML de métadonnées de fédération** en fonction des options définies selon vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

6. Dans la section **Configurer BC in the Cloud**, copiez la ou les URL appropriées correspondant à vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à BC in the Cloud.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **BC in the Cloud**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-bc-in-the-cloud-sso"></a>Configurer l’authentification unique BC in the Cloud

Pour configurer l’authentification unique côté **BC in the Cloud**, vous devez envoyer le **XML des métadonnées de fédération** téléchargé et les URL copiées dans le portail Azure à l’[équipe du support technique BC in the Cloud](https://www.bcinthecloud.com/supportcenter/). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

### <a name="create-bc-in-the-cloud-test-user"></a>Créer un utilisateur de test BC in the Cloud

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans BC in the Cloud. Pour ajouter des utilisateurs dans la plateforme BC in the Cloud, contactez l’[équipe du support technique BC in the Cloud](https://www.bcinthecloud.com/supportcenter/). Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL d’authentification BC in the Cloud, d’où vous pouvez lancer le processus de connexion. 

* Accédez directement à l’URL d’authentification BC in the Cloud pour lancer le processus de connexion.

* Vous pouvez utiliser Mes applications de Microsoft. Quand vous cliquez sur la vignette BC in the Cloud dans Mes applications, vous êtes redirigé vers l’URL d’authentification BC in the Cloud. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré BC in the Cloud, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
