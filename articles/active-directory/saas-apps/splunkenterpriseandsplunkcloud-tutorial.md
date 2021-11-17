---
title: 'Tutoriel : intégration d’Azure Active Directory avec l’authentification unique Azure AD pour Splunk Enterprise et Splunk Cloud | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et l’authentification unique Azure AD pour Splunk Enterprise et Splunk Cloud.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/16/2021
ms.author: jeedes
ms.openlocfilehash: e2eaaf36b75110562f847bf7c3cb795571cb778d
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132282839"
---
# <a name="tutorial-azure-active-directory-integration-with-azure-ad-sso-for-splunk-enterprise-and-splunk-cloud"></a>Tutoriel : intégration d’Azure Active Directory avec l’authentification unique Azure AD pour Splunk Enterprise et Splunk Cloud

Dans ce tutoriel, vous allez découvrir comment intégrer l’authentification unique Azure AD pour Splunk Enterprise et Splunk Cloud à Azure Active Directory (Azure AD). Quand vous intégrez l’authentification unique Azure AD pour Splunk Enterprise et Splunk Cloud à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à l’authentification unique Azure AD pour Splunk Enterprise et Splunk Cloud.
* Permettre à vos utilisateurs d’être connectés automatiquement à l’authentification unique Azure AD pour Splunk Enterprise et Splunk Cloud avec leur compte Azure AD.
* gérer vos comptes à un emplacement central : le portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement d’authentification unique Azure AD pour Splunk Enterprise et Splunk Cloud pour lequel l’authentification unique (SSO) est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* L’authentification unique Azure AD pour Splunk Enterprise et Splunk Cloud prend en charge l’authentification unique (SSO) initiée par le **fournisseur de services**

## <a name="add-azure-ad-sso-for-splunk-enterprise-and-splunk-cloud-from-the-gallery"></a>Ajouter l’authentification unique Azure AD Splunk Enterprise et Splunk Cloud à partir de la galerie

Pour configurer son intégration à Azure AD, vous devez ajouter l’authentification unique Azure AD pour Splunk Enterprise et Splunk Cloud à votre liste d’applications SaaS gérées, à partir de la galerie.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Authentification unique Azure AD pour Splunk Enterprise and Splunk Cloud** dans la zone de recherche.
1. Sélectionnez **Authentification unique Azure AD pour Splunk Enterprise et Splunk Cloud** dans le panneau de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-azure-ad-sso-for-splunk-enterprise-and-splunk-cloud"></a>Configurer et tester l’authentification unique Azure AD pour Splunk Enterprise et Splunk Cloud

Configurez et testez l’authentification unique Azure AD avec Splunk Enterprise et Splunk Cloud pour un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur associé dans l’authentification unique Azure AD pour Splunk Enterprise et Splunk Cloud.

Pour configurer et tester l’authentification unique Azure AD avec Splunk Enterprise et Splunk Cloud, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Azure AD pour Splunk Enterprise et Splunk Cloud](#configure-azure-ad-sso-for-splunk-enterprise-and-splunk-cloud-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test de l’authentification unique Azure AD pour Splunk Enterprise et Splunk Cloud](#create-azure-ad-sso-for-splunk-enterprise-and-splunk-cloud-test-user)** pour avoir un équivalent de B.Simon dans Splunk Enterprise et Splunk Cloud qui soit lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, dans la page d’intégration de l’application **Authentification unique Azure AD pour Splunk Enterprise et Splunk Cloud**, recherchez la section **Gérer**, puis sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<splunkserverUrl>/app/launcher/home`.

    b. Dans la zone de texte **Identificateur**, tapez une URL en utilisant le format suivant : `<splunkserverUrl>`

    c. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://<splunkserver>/saml/acs`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion, l’identificateur et l’URL de réponse réels. Pour obtenir ces valeurs, contactez l’[équipe de support technique de l’authentification unique Azure AD pour Splunk Enterprise et Splunk Cloud](https://www.splunk.com/en_us/about-splunk/contact-us.html). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

5. Sur la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le fichier **XML de métadonnées de fédération** en fonction des options définies selon vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Splunk Enterprise et Splunk Cloud.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Authentification unique Azure AD pour Splunk Enterprise et Splunk Cloud**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-azure-ad-sso-for-splunk-enterprise-and-splunk-cloud-sso"></a>Configurer l’authentification unique Azure AD pour Splunk Enterprise et Splunk Cloud

  Pour configurer l’authentification unique côté **Splunk Enterprise et Splunk Cloud**, vous devez envoyer le fichier **XML de métadonnées de fédération** téléchargé et les URL copiées appropriées à partir du portail Azure à [l’équipe de support technique Splunk Enterprise et Splunk Cloud](https://www.splunk.com/en_us/about-splunk/contact-us.html). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

### <a name="create-azure-ad-sso-for-splunk-enterprise-and-splunk-cloud-test-user"></a>Créer l’utilisateur test pour l’authentification unique Azure AD pour Splunk Enterprise et Splunk Cloud

Dans cette section, vous allez créer un utilisateur appelé Britta Simon pour l’authentification unique Azure AD de Splunk Enterprise et Splunk Cloud. Rapprochez-vous de l’[équipe de support Splunk Enterprise et Splunk Cloud](https://www.splunk.com/en_us/about-splunk/contact-us.html) pour ajouter les utilisateurs dans la plateforme Splunk Enterprise et Splunk Cloud. Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion à Splunk Enterprise et Splunk Cloud, d’où vous pouvez lancer le flux de connexion. 

* Accédez directement à l’URL de connexion à l’authentification unique Azure AD pour Splunk Enterprise et Splunk Cloud pour lancer le flux de connexion.

* Vous pouvez utiliser Mes applications de Microsoft. Quand vous cliquez sur la vignette Authentification unique Azure AD pour Splunk Enterprise et Splunk Cloud dans Mes applications, vous êtes redirigé vers l’URL de connexion à Splunk Enterprise et Splunk Cloud. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré l’authentification unique Azure AD pour Splunk Enterprise et Splunk Cloud, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
