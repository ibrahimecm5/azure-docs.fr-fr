---
title: 'Tutoriel : Intégration de l’authentification unique Azure AD à CrowdStrike Falcon Platform'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et CrowdStrike Falcon Platform.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/02/2021
ms.author: jeedes
ms.openlocfilehash: 1b44bc1ad77e6a92cda9b085434b756b6598c060
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132287986"
---
# <a name="tutorial-azure-ad-sso-integration-with-crowdstrike-falcon-platform"></a>Tutoriel : Intégration de l’authentification unique Azure AD à CrowdStrike Falcon Platform

Dans ce tutoriel, vous allez découvrir comment intégrer CrowdStrike Falcon Platform à Azure Active Directory (Azure AD). Quand vous intégrez CrowdStrike Falcon Platform à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à CrowdStrike Falcon Platform.
* Permettre à vos utilisateurs de se connecter automatiquement à CrowdStrike Falcon Platform avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement CrowdStrike Falcon valide.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* CrowdStrike Falcon Platform prend en charge l’authentification unique lancée par le **fournisseur de services et le fournisseur d’identité**.

## <a name="adding-crowdstrike-falcon-platform-from-the-gallery"></a>Ajout de CrowdStrike Falcon Platform à partir de la galerie

Pour configurer l’intégration de CrowdStrike Falcon Platform à Azure AD, vous devez ajouter CrowdStrike Falcon Platform à partir de la galerie à votre liste d’applications SaaS managées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **CrowdStrike Falcon Platform** dans la zone de recherche.
1. Sélectionnez **CrowdStrike Falcon Platform** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.


## <a name="configure-and-test-azure-ad-sso-for-crowdstrike-falcon-platform"></a>Configurer et tester l’authentification unique Azure AD pour CrowdStrike Falcon Platform

Configurez et testez l’authentification unique Azure AD avec CrowdStrike Falcon Platform pour un utilisateur test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur CrowdStrike Falcon Platform associé.

Pour configurer et tester l’authentification unique Azure AD avec CrowdStrike Falcon Platform, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique CrowdStrike Falcon Platform](#configure-crowdstrike-falcon-platform-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur test CrowdStrike Falcon Platform](#create-crowdstrike-falcon-platform-test-user)** pour avoir un équivalent de B.Simon dans CrowdStrike Falcon Platform lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **CrowdStrike Falcon Platform**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, si vous souhaitez configurer l’application en mode Initié par le **fournisseur d’identité**, entrez les valeurs pour les champs suivants :

    a. Dans la zone de texte **Identificateur**, tapez l’une des URL suivantes :

    | Identificateur |
    | -------------- |
    | `https://falcon.crowdstrike.com/saml/metadata` |
    | `https://falcon.us-2.crowdstrike.com/saml/metadata` |
    | `https://falcon.eu-1.crowdstrike.com/saml/metadata` |
    | `https://falcon.laggar.gcw.crowdstrike.com/saml/metadata` |
    |

    b. Dans la zone de texte **URL de réponse**, tapez l’une des URL suivantes :

    | URL de réponse |
    | -------------- |
    | `https://falcon.crowdstrike.com/saml/acs` |
    | `https://falcon.us-2.crowdstrike.com/saml/acs` |
    | `https://falcon.eu-1.crowdstrike.com/saml/acs` |
    | `https://falcon.laggar.gcw.crowdstrike.com/saml/acs` |
    |

1. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    Dans la zone de texte **URL d’authentification**, tapez l’une des URL suivantes :

    | URL de connexion |
    | -------------- |
    | `https://falcon.crowdstrike.com/login` |
    | `https://falcon.us-2.crowdstrike.com/login` |
    | `https://falcon.eu-1.crowdstrike.com/login` |
    | `https://falcon.laggar.gcw.crowdstrike.com/login` |
    |

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à CrowdStrike Falcon Platform.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **CrowdStrike Falcon Platform**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-crowdstrike-falcon-platform-sso"></a>Configurer l’authentification unique CrowdStrike Falcon Platform

Pour configurer l’authentification unique côté **CrowdStrike Falcon Platform**, vous devez envoyer l’**URL des métadonnées de fédération d’application** à l’[équipe du support de CrowdStrike Falcon Platform](mailto:support@crowdstrike.com). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

### <a name="create-crowdstrike-falcon-platform-test-user"></a>Créer un utilisateur test CrowdStrike Falcon Platform

Dans cette section, vous allez créer une utilisatrice appelée Britta Simon dans CrowdStrike Falcon Platform. Contactez l’[équipe du support de CrowdStrike Falcon Platform](mailto:support@crowdstrike.com) pour ajouter les utilisateurs à la plateforme CrowdStrike Falcon Platform. Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

#### <a name="sp-initiated"></a>Lancée par le fournisseur de services :

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL d’authentification CrowdStrike Falcon Platform, d’où vous pouvez lancer le processus de connexion.  

* Accédez directement à l’URL d’authentification CrowdStrike Falcon Platform pour lancer le processus de connexion.

#### <a name="idp-initiated"></a>Lancée par le fournisseur d’identité :

* Cliquez sur **Tester cette application** dans le portail Azure. Vous devriez alors être automatiquement connecté à l’application CrowdStrike Falcon Platform pour laquelle vous avez configuré l’authentification unique. 

Vous pouvez aussi utiliser Mes applications de Microsoft pour tester l’application dans n’importe quel mode. Si, quand vous cliquez sur la vignette CrowdStrike Falcon Platform dans le volet d’accès, le mode Fournisseur de services est configuré, vous êtes redirigé vers la page d’authentification de l’application pour lancer le processus de connexion. Si le mode Fournisseur d’identité est configuré, vous devriez être automatiquement connecté à l’application CrowdStrike Falcon Platform pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré CrowdStrike Falcon Platform, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
