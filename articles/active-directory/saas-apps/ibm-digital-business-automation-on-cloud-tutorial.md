---
title: 'Tutoriel : Intégration de l’authentification unique Azure Active Directory avec IBM Digital Business Automation on Cloud | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et IBM Digital Business Automation on Cloud.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/12/2021
ms.author: jeedes
ms.openlocfilehash: 25e61f30318df4200d797c7d78753ea2d5ca84bb
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132321219"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ibm-digital-business-automation-on-cloud"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory avec IBM Digital Business Automation on Cloud

Dans ce tutoriel, vous allez apprendre à intégrer IBM Digital Business Automation on Cloud avec Azure AD (Azure Active Directory). Quand vous intégrez IBM Digital Business Automation on Cloud avec Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à IBM Digital Business Automation on Cloud.
* Permettre aux utilisateurs de se connecter automatiquement à IBM Digital Business Automation on Cloud avec leurs comptes Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Abonnement IBM Digital Business Automation on Cloud pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* IBM Digital Business Automation on Cloud prend en charge l'authentification unique lancée par **le fournisseur d'identité et le fournisseur de service**.

## <a name="add-ibm-digital-business-automation-on-cloud-from-the-gallery"></a>Ajouter IBM Digital Business Automation on Cloud à partir de la galerie

Pour configurer l’intégration d’IBM Digital Business Automation on Cloud avec Azure AD, vous devez ajouter IBM Digital Business Automation on Cloud à votre liste d’applications SaaS managées à partir de la galerie.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, dans la zone de recherche, tapez **IBM Digital Business Automation on Cloud**.
1. Sélectionnez **IBM Digital Business Automation on Cloud** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-ibm-digital-business-automation-on-cloud"></a>Configurer et tester l'authentification unique Azure AD pour IBM Digital Business Automation on Cloud

Configurez et testez l’authentification unique Azure AD avec IBM Digital Business Automation on Cloud à l’aide d’un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir une relation entre un utilisateur Azure AD et l’utilisateur IBM Digital Business Automation on Cloud associé.

Pour configurer et tester l'authentification unique Azure AD auprès d'IBM Digital Business Automation on Cloud, suivez les étapes ci-dessous :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique IBM Digital Business Automation on Cloud](#configure-ibm-digital-business-automation-on-cloud-sso)** pour configurer les paramètres d’authentification unique côté application.
    1. **[Créer un utilisateur de test IBM Digital Business Automation on Cloud](#create-ibm-digital-business-automation-on-cloud-test-user)** pour avoir un équivalent de B.Simon dans IBM Digital Business Automation on Cloud qui soit lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Sur le portail Azure, accédez à la page d'intégration de l'application **IBM Digital Business Automation on Cloud**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, si vous disposez d’un **fichier de métadonnées du fournisseur de services**, suivez les étapes ci-dessous :
    
    a. Cliquez sur **Charger un fichier de métadonnées**.

    b. Cliquez sur le **logo du dossier** pour sélectionner le fichier de métadonnées, puis cliquez sur **Charger**.

    c. Une fois le fichier de métadonnées chargé, les valeurs **Identificateur** et **URL de réponse** sont automatiquement renseignées dans la zone de texte de la section IBM Digital Business Automation on Cloud :

    > [!Note]
    > Si les valeurs **Identificateur** et **URL de réponse** ne sont pas automatiquement renseignées, renseignez-les manuellement en fonction de vos besoins.

    > [!Note]
    > Les clients peuvent obtenir le fichier de métadonnées de leur abonnement cloud auprès de l’[équipe de support technique IBM Digital Business Automation on Cloud](mailto:supportbpmoncloud@us.ibm.com).

1. En l'absence de **fichier de métadonnées du fournisseur de services**, dans la section **Configuration SAML de base**, si vous souhaitez configurer l'application en mode Initié par le **fournisseur d'identité**, procédez comme suit :

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://www.automationcloud.ibm.com/isam/sps/<TENANT>/saml20`

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://www.automationcloud.ibm.com/isam/sps/<TENANT>/saml20/login`

1. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://www.automationcloud.ibm.com/isam/sps/<TENANT>/login`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL de réponse et l’URL de connexion réels. Pour obtenir ces valeurs, contactez l’[équipe de support client d’IBM Digital Business Automation on Cloud](mailto:supportbpmoncloud@us.ibm.com). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **XML de métadonnées de fédération** et sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

1. Dans la section **Configurer IBM Digital Business Automation on Cloud**, copiez la ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez permettre à B.Simon d’utiliser l’authentification unique Azure en lui octroyant l’accès à IBM Digital Business Automation on Cloud.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **IBM Digital Business Automation on Cloud**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-ibm-digital-business-automation-on-cloud-sso"></a>Configurer l’authentification unique IBM Digital Business Automation on Cloud

Pour configurer l’authentification unique côté **IBM Digital Business Automation on Cloud**, vous devez envoyer le **XML des métadonnées de fédération** téléchargé et les URL appropriées copiées à partir du portail Azure à l’[équipe de support d’IBM Digital Business Automation on Cloud](mailto:supportbpmoncloud@us.ibm.com). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

### <a name="create-ibm-digital-business-automation-on-cloud-test-user"></a>Créer un utilisateur de test IBM Digital Business Automation on Cloud

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans IBM Digital Business Automation on Cloud. Collaborez avec l’[équipe de support d’IBM Digital Business Automation on Cloud](mailto:supportbpmoncloud@us.ibm.com) pour ajouter les utilisateurs à la plateforme IBM Digital Business Automation on Cloud. Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

#### <a name="sp-initiated"></a>Lancée par le fournisseur de services :

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l'URL de connexion à IBM Digital Business Automation on Cloud, à partir de laquelle vous pouvez lancer le flux de connexion.  

* Accédez directement à l'URL de connexion à IBM Digital Business Automation on Cloud pour lancer le flux de connexion.

#### <a name="idp-initiated"></a>Lancée par le fournisseur d’identité :

* Cliquez sur **Tester cette application** sur le portail Azure. Vous êtes alors automatiquement connecté à l'instance d'IBM Digital Business Automation on Cloud pour laquelle vous avez configuré l'authentification unique. 

Vous pouvez aussi utiliser Mes applications de Microsoft pour tester l’application dans n’importe quel mode. Si, lorsque vous cliquez sur la vignette IBM Digital Business Automation on Cloud dans Mes applications, le mode Fournisseur de services est configuré, vous êtes redirigé vers la page de connexion de l'application pour lancer le flux de connexion ; s'il s'agit du mode Fournisseur d'identité, vous êtes automatiquement connecté à l'instance d'IBM Digital Business Automation on Cloud pour laquelle vous avez configuré l'authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré IBM Digital Business Automation on Cloud, vous pouvez appliquer le contrôle de session, qui protège en temps réel contre l'exfiltration et l'infiltration des données sensibles de votre organisation. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
