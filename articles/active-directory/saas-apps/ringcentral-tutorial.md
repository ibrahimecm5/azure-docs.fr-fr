---
title: 'Tutoriel : Intégration d’Azure Active Directory à RingCentral | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et RingCentral.
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
ms.openlocfilehash: 69fe78302fa44bf22b56048b8fc9278a02843df0
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132341601"
---
# <a name="tutorial-integrate-ringcentral-with-azure-active-directory"></a>Tutoriel : Intégrer RingCentral à Azure Active Directory

Dans ce tutoriel, vous allez découvrir comment intégrer RingCentral à Azure AD (Azure Active Directory). Quand vous intégrez RingCentral à Azure AD, vous pouvez :

* Contrôler qui a accès à RingCentral dans Azure AD.
* Permettre aux utilisateurs de se connecter automatiquement à RingCentral avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement RingCentral pour lequel l’authentification unique est activée

> [!NOTE]
> Cette intégration peut également être utilisée à partir de l’environnement cloud US Government Azure AD. Cette application est disponible dans la Galerie d’applications cloud US Government Azure AD et peut être configurée de la même façon que dans le cloud public.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* RingCentral prend en charge l’authentification unique initiée par **un fournisseur d’identité**.

* RingCentral prend en charge l’[attribution automatique d’utilisateurs](ringcentral-provisioning-tutorial.md).

## <a name="add-ringcentral-from-the-gallery"></a>Ajouter RingCentral à partir de la galerie

Pour configurer l’intégration de RingCentral à Azure AD, vous devez ajouter RingCentral à partir de la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **RingCentral** dans la zone de recherche.
1. Sélectionnez **RingCentral** dans le panneau de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-ringcentral"></a>Configurer et tester l’authentification unique Azure AD pour RingCentral

Configurez et testez l’authentification unique Azure AD avec RingCentral à l’aide d’un utilisateur de test appelé **Britta Simon**. Pour que l’authentification unique fonctionne, vous devez établir une relation entre un utilisateur Azure AD et l’utilisateur RingCentral associé.

Pour configurer et tester l’authentification unique Azure AD avec RingCentral, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    * **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    * **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique RingCentral](#configure-ringcentral-sso)** pour configurer les paramètres de l’authentification unique côté application.
    * **[Créer un utilisateur de test RingCentral](#create-ringcentral-test-user)** pour avoir un équivalent de B.Simon dans RingCentral, lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **RingCentral**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon pour **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, si vous disposez d’un **fichier de métadonnées du fournisseur de services**, suivez les étapes ci-dessous :

    1. Cliquez sur **Charger un fichier de métadonnées**.
    1. Cliquez sur le **logo du dossier** pour sélectionner le fichier de métadonnées, puis cliquez sur **Charger**.
    1. Une fois le fichier de métadonnées chargé, les valeurs **Identificateur** et **URL de réponse** sont automatiquement renseignées dans la section **Configuration SAML de base**.

    > [!Note]
    > Sur la page de configuration de l’authentification unique de RingCentral, vous obtenez le **fichier de métadonnées du fournisseur de services**, qui sera expliqué plus tard dans le didacticiel.

1. Si vous n’avez pas de **fichier de métadonnées du fournisseur de services**, entrez les valeurs pour les champs suivants :

    a. Dans la zone de texte **Identificateur**, saisissez l’une des URL :
  
    | Identificateur |
    |--|
    |  `https://sso.ringcentral.com` |
    | `https://ssoeuro.ringcentral.com` |

    b. Dans la zone de texte **URL de réponse**, saisissez l’une des URL :

    | URL de réponse |
    |--|
    | `https://sso.ringcentral.com/sp/ACS.saml2` |
    | `https://ssoeuro.ringcentral.com/sp/ACS.saml2` |

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur le bouton Copier pour copier l’**URL des métadonnées de fédération d’application**, puis enregistrez-la sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

Dans cette section, vous allez créer un utilisateur de test appelé Britta Simon dans le Portail Azure.

1. Dans le volet gauche du Portail Azure, sélectionnez **Azure Active Directory**, **Utilisateurs**, puis **Tous les utilisateurs**.
1. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.
1. Dans les propriétés **Utilisateur**, effectuez les étapes suivantes :
   1. Dans le champ **Nom**, entrez `Britta Simon`.  
   1. Dans le champ **Nom de l’utilisateur**, entrez username@companydomain.extension. Par exemple : `BrittaSimon@contoso.com`.
   1. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.
   1. Cliquez sur **Créer**.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à RingCentral.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **RingCentral**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-ringcentral-sso"></a>Configurer l’authentification unique RingCentral

1. Pour automatiser la configuration dans RingCentral, vous devez installer l’**extension de navigateur My Apps Secure Sign-in** en cliquant sur **Installer l’extension**.

    ![Extension My apps](common/install-myappssecure-extension.png)

1. Après avoir ajouté l’extension au navigateur, cliquez sur **Configurer RingCentral** pour être dirigé vers l’application RingCentral. À partir de là, indiquez les informations d’identification de l’administrateur pour vous connecter à RingCentral. Cette extension de navigateur configure automatiquement l’application pour vous et automatise les étapes 3 à 7.

    ![Configuration](common/setup-sso.png)

1. Si vous souhaitez configurer manuellement RingCentral, ouvrez une nouvelle fenêtre de navigateur web, connectez-vous à votre site d’entreprise RingCentral en tant qu’administrateur et effectuez les étapes suivantes :

1. En haut de la page, cliquez sur **Outils**.

    ![Capture d’écran montrant les outils sélectionnés sur le site d’entreprise RingCentral.](./media/ringcentral-tutorial/ringcentral-1.png)

1. Accédez à **Authentification unique**.

    ![Capture d’écran montrant l’élément Single Sign-on sélectionné dans le menu Tools.](./media/ringcentral-tutorial/ringcentral-2.png)

1. Sur la page **Authentification unique**, dans la section **Configuration SSO**, cliquez sur **Modifier** dans l’**étape 1**, puis procédez comme suit :

    ![Capture d’écran montrant la page de configuration de l’authentification unique dans laquelle vous pouvez sélectionner Edit.](./media/ringcentral-tutorial/ringcentral-3.png)

1. Sur la page **Configurer l’authentification unique**, effectuez les étapes suivantes :

    ![Capture d’écran montrant la page Set up Single Sign-On, dans laquelle vous pouvez charger les métadonnées du fournisseur d’identité.](./media/ringcentral-tutorial/ringcentral-4.png)

    a. Cliquez sur **Parcourir** pour charger le fichier de métadonnées que vous avez téléchargé à partir du portail Azure.

    b. Après avoir chargé les métadonnées, les valeurs sont automatiquement renseignées dans la section **SSO General Information** (Informations générales sur l’authentification unique).

    c. Dans la section **Mappage d’attributs**, définissez **Map Email Attribute to** (Mapper l’attribut d’e-mail à) sur `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    d. Cliquez sur **Enregistrer**.

    e. Dans l’**étape 2**, cliquez sur **Télécharger** pour télécharger le **fichier de métadonnées du fournisseur de services**. Ensuite, chargez-le dans la section **Configuration SAML de base** afin de renseigner automatiquement les valeurs **Identificateur** et **URL de réponse** dans le portail Azure.

    ![Capture d’écran montrant la page de configuration de l’authentification unique, dans laquelle vous pouvez sélectionner Download.](./media/ringcentral-tutorial/ringcentral-6.png) 

    f. Sur la même page, accédez à la section **Activer l’authentification unique** et procédez comme suit :

    ![Capture d’écran présentant la section d’activation de l’authentification unique, dans laquelle vous pouvez terminer la configuration.](./media/ringcentral-tutorial/ringcentral-5.png)

    * Sélectionnez **Activer le service d’authentification unique**.

    * Sélectionnez **Allow users to log in with SSO or RingCentral credential** (Autoriser les utilisateurs à se connecter avec des informations d’identification de l’authentification unique ou RingCentral).

    * Cliquez sur **Enregistrer**.

### <a name="create-ringcentral-test-user"></a>Créer un utilisateur de test RingCentral

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans RingCentral. Collaborez avec l’[équipe de support technique RingCentral](https://success.ringcentral.com/RCContactSupp) pour ajouter les utilisateurs dans la plateforme RingCentral. Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique.

RingCentral prend aussi en charge l’attribution automatique d’utilisateurs. Vous trouverez [ici](./ringcentral-provisioning-tutorial.md) plus d’informations sur la façon de configurer l’attribution automatique d’utilisateurs.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes.

* Dans le portail Azure, cliquez sur Tester cette application. Vous êtes alors automatiquement connecté à l’instance de RingCentral pour laquelle vous avez configuré l’authentification unique.

* Vous pouvez utiliser Mes applications de Microsoft. Quand vous cliquez sur la vignette RingCentral dans Mes applications, vous êtes connecté automatiquement à l’instance de RingCentral pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré RingCentral, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
