---
title: 'Tutoriel : Intégration de l’authentification unique Azure Active Directory à Genesys Cloud | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Genesys Cloud for Azure.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/31/2021
ms.author: jeedes
ms.openlocfilehash: e3e5cc70f889eb24d50e67beb95a1e6324cecb02
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132341727"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-genesys-cloud-for-azure"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory à Genesys Cloud

Dans ce tutoriel, vous allez apprendre à intégrer Genesys Cloud for Azure à Azure Active Directory (Azure AD). Quand vous intégrez Genesys Cloud for Azure à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Genesys Cloud for Azure.
* Permettre à vos utilisateurs de se connecter automatiquement à Genesys Cloud for Azure avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous n’en avez pas, vous pouvez obtenir un [compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Genesys Cloud for Azure pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* Genesys Cloud for Azure prend en charge l’authentification unique lancée par **le fournisseur de services et par le fournisseur d’identité**.

* Genesys Cloud for Azure prend en charge l’[attribution automatisée d’utilisateurs](purecloud-by-genesys-provisioning-tutorial.md).

> [!NOTE]
> L’identificateur de cette application étant une valeur de chaîne fixe, une seule instance peut être configurée dans un locataire.

## <a name="add-genesys-cloud-for-azure-from-the-gallery"></a>Ajouter Genesys Cloud for Azure à partir de la galerie

Pour configurer l’intégration de Genesys Cloud for Azure dans Azure AD, vous devez ajouter Genesys Cloud for Azure à partir de la galerie à votre liste d’applications SaaS gérées. Pour ce faire, procédez comme suit :

1. Connectez-vous au portail Azure, soit avec un compte professionnel ou scolaire, soit avec un compte Microsoft personnel.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, saisissez **Genesys Cloud for Azure** dans la zone de recherche.
1. Sélectionnez **Genesys Cloud for Azure** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-genesys-cloud-for-azure"></a>Configurer et tester l’authentification unique Azure AD pour Genesys Cloud for Azure

Configurez et testez l’authentification unique Azure AD avec Genesys Cloud for Azure à l’aide d’un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur associé dans Genesys Cloud for Azure.

Pour configurer et tester l’authentification unique Azure AD avec Genesys Cloud for Azure, procédez comme suit :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique de Genesys Cloud for Azure](#configure-genesys-cloud-for-azure-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test Genesys Cloud for Azure](#create-genesys-cloud-for-azure-test-user)** pour avoir un équivalent de B.Simon dans Genesys Cloud for Azure lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Pour activer l’authentification unique Azure AD dans le portail Azure, effectuez les étapes suivantes :

1. Dans le portail Azure, accédez à la page d’intégration de l’application **Genesys Cloud for Azure**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, sélectionnez l’icône de crayon pour **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, si vous souhaitez configurer l’application en mode lancé par le **fournisseur d’identité**, procédez comme suit :

    a. Dans la zone **Identificateur**, entrez les URL qui correspondent à votre région :
    
    | URL d’identificateur |
    |---|
    | https://login.mypurecloud.com/saml |
    | https://login.mypurecloud.de/saml |
    | https://login.mypurecloud.jp/saml |
    | https://login.mypurecloud.ie/saml |
    | https://login.mypurecloud.com.au/saml |
    |

    b. Dans la zone **URL de réponse**, entrez les URL qui correspondent à votre région :

    | URL de réponse |
    |---|
    | https://login.mypurecloud.com/saml |
    | https://login.mypurecloud.de/saml |
    | https://login.mypurecloud.jp/saml |
    | https://login.mypurecloud.ie/saml |
    | https://login.mypurecloud.com.au/saml |
    |

1. Sélectionnez **Définir des URL supplémentaires** et effectuez l’étape suivante si vous souhaitez configurer l’application en mode lancé par le **fournisseur de services** :

    Dans la zone **URL de connexion**, entrez les URL qui correspondent à votre région :
    
    |URL de connexion |
    |---|
    | https://login.mypurecloud.com |
    | https://login.mypurecloud.de |
    | https://login.mypurecloud.jp |
    | https://login.mypurecloud.ie |
    | https://login.mypurecloud.com.au |
    |

1. L’application Genesys Cloud for Azure s’attend à recevoir les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à votre configuration des attributs de jetons SAML. La capture d’écran suivante montre la liste des attributs par défaut :

    ![image](common/default-attributes.png)

1. En outre, l’application Genesys Cloud for Azure s’attend à ce que quelques attributs supplémentaires (présentés dans le tableau suivant) soient repassés dans la réponse SAML. Ces attributs sont également préremplis, mais vous pouvez les examiner en fonction de vos besoins.

    | Nom | Attribut source|
    | ---------------| --------------- |
    | E-mail | user.userprincipalname |
    | OrganizationName | `Your organization name` |

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **Certificat (Base64)** , puis sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

1. Dans la section **Configurer Genesys Cloud for Azure**, copiez les URL appropriées en fonction de vos besoins.

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

Dans cette section, vous allez créer un utilisateur de test nommé B.Simon dans le portail Azure :

1. Dans le volet gauche du portail Azure, sélectionnez **Azure Active Directory**, **Utilisateurs**, puis **Tous les utilisateurs**.
1. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.
1. Dans les propriétés **Utilisateur**, effectuez les étapes suivantes :
   1. Dans le champ **Nom**, entrez `B.Simon`.  
   1. Dans le champ **Nom d’utilisateur**, entrez le nom d’utilisateur au format suivant : username@companydomain.extension. Par exemple : `B.Simon@contoso.com`.
   1. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.
   1. Sélectionnez **Create** (Créer).

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Genesys Cloud for Azure.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Genesys Cloud for Azure**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-genesys-cloud-for-azure-sso"></a>Configurer l’authentification unique de Genesys Cloud for Azure

1. Dans une autre fenêtre de navigateur web, connectez-vous à Genesys Cloud for Azure en tant qu’administrateur.

1. Sélectionnez **Admin** en haut, puis accédez à **Single Sign-on** (Authentification unique) sous **Integrations**.

    ![Capture d’écran montrant la fenêtre PureCloud Admin, où vous pouvez sélectionner Single Sign-on.](./media/purecloud-by-genesys-tutorial/configure-1.png)

1. Basculez vers l’onglet **ADFS/Azure AD(Premium)** , puis effectuez les étapes suivantes :

    ![Capture d’écran montrant la page Integrations dans laquelle vous pouvez indiquer les valeurs décrites.](./media/purecloud-by-genesys-tutorial/configure-2.png)

    a. Dans **ADFS Certificate** (Certificat ADFS), sélectionnez **Browse** (Parcourir) pour charger le certificat codé en base 64 que vous avez téléchargé du portail Azure.

    b. Dans la zone **ADFS Issuer URI** (URI de l’émetteur SAML), collez la valeur **Identificateur Azure AD** que vous avez copiée à partir du portail Azure.

    c. Dans la zone **Target URI** (URI cible), collez la valeur **URL de connexion** que vous avez copiée du portail Azure.

    d. Pour la valeur **Relying Party Identifier** (Identificateur de partie de confiance), accédez au portail Azure puis, dans la page d’intégration de l’application **Genesys Cloud for Azure**, sélectionnez l’onglet **Propriétés** et copiez la valeur **ID de l’application**. Collez ensuite cette valeur dans la zone **Relying Party Identifier**.

    ![Capture d’écran montrant le volet Propriétés dans lequel vous pouvez trouver la valeur de l’ID de l’application.](./media/purecloud-by-genesys-tutorial/configuration.png)

    e. Sélectionnez **Enregistrer**.

### <a name="create-genesys-cloud-for-azure-test-user"></a>Créer un utilisateur de test Genesys Cloud for Azure

Pour permettre aux utilisateurs d’Azure AD de se connecter à Genesys Cloud for Azure, vous devez les attribués dans Genesys Cloud for Azure. Dans Genesys Cloud for Azure, l’approvisionnement est une tâche manuelle.

**Pour configurer un compte d’utilisateur, effectuez ces étapes :**

1. Connectez-vous à Genesys Cloud for Azure en tant qu’administrateur.

1. Sélectionnez **Admin** en haut de la fenêtre, puis accédez à la section **People** (Personnes) sous **People & Permissions** (Personnes & Autorisations).

    ![Capture d’écran montrant la fenêtre PureCloud Admin, dans laquelle vous pouvez sélectionner People.](./media/purecloud-by-genesys-tutorial/configure-3.png)

1. Dans la page **People**, sélectionnez **Add Person** (Ajouter une personne).

    ![Capture d’écran affichant la page People dans laquelle vous pouvez ajouter une personne.](./media/purecloud-by-genesys-tutorial/configure-4.png)

1. Dans la boîte de dialogue **Add People to the Organization** (Ajouter des personnes à l’organisation), effectuez les étapes suivantes :

    ![Capture d’écran montrant la page dans laquelle vous pouvez indiquer les valeurs décrites.](./media/purecloud-by-genesys-tutorial/configure-5.png)

    a. Dans la zone **Full Name** (Nom complet), entrez le nom d’un utilisateur. Par exemple : **B.simon**.

    b. Dans la zone **Email**, entrez l’adresse e-mail de l’utilisateur. Par exemple : **b.simon\@contoso.com**.

    c. Sélectionnez **Create** (Créer).

> [!NOTE]
> Genesys Cloud for Azure prend également en charge l’attribution automatique d’utilisateurs. Des informations supplémentaires sur la configuration de cette fonctionnalité sont disponibles [ici](./purecloud-by-genesys-provisioning-tutorial.md).

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

#### <a name="sp-initiated"></a>Lancée par le fournisseur de services :

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion de Genesys Cloud for Azure, à partir de laquelle vous pouvez lancer le flux de connexion.  

* Accédez directement à l’URL de connexion de Genesys Cloud for Azure pour initier le flux de connexion.

#### <a name="idp-initiated"></a>Lancée par le fournisseur d’identité :

* Cliquez sur **Tester cette application** dans Portail Azure : vous devez être connecté automatiquement à l’instance de Genesys Cloud for Azure pour laquelle vous avez configuré l’authentification unique. 

Vous pouvez aussi utiliser Mes applications de Microsoft pour tester l’application dans n’importe quel mode. Quand vous cliquez sur la vignette Genesys Cloud for Azure dans Mes applications, si le mode Fournisseur de services est configuré, vous êtes redirigé vers la page de connexion de l’application pour lancer le flux de connexion ; si le mode Fournisseur d’identité est configuré, vous êtes automatiquement connecté à l’instance de Genesys Cloud for Azure pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez configuré Genesys Cloud for Azure, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
