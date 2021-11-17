---
title: 'Tutoriel : Intégration de l’authentification unique Azure AD à VECOS Releezme Locker management system'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et VECOS Releezme Locker management system.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/20/2021
ms.author: jeedes
ms.openlocfilehash: 74d030d4bf5b8a7d4ba16a8d7cc0421af0110b02
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132340682"
---
# <a name="tutorial-azure-ad-sso-integration-with-vecos-releezme-locker-management-system"></a>Tutoriel : Intégration de l’authentification unique Azure AD à VECOS Releezme Locker management system

Dans ce tutoriel, vous allez apprendre à intégrer Azure Active Directory (Azure AD) à VECOS Releezme Locker management system. Lorsque vous intégrez Azure AD à VECOS Releezme Locker management system, vous pouvez :

* Contrôler dans Azure AD qui a accès à VECOS Releezme Locker management system. L’accès au système d’administration Locker de VECOS Releezme est uniquement nécessaire pour les utilisateurs qui ont besoin de gérer les lockers, par exemple, les gestionnaires d’installations, les employés du service d’assistance, etc.
* Permettre à vos utilisateurs de se connecter automatiquement à VECOS Releezme Locker management system avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement VECOS Releezme Locker management system pour lequel l’authentification unique (SSO) est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* VECOS Releezme Locker management system prend en charge l’authentification unique lancée par le **fournisseur de services**.

> [!NOTE]
> L’identificateur de cette application étant une valeur de chaîne fixe, une seule instance peut être configurée dans un locataire.

## <a name="add-vecos-releezme-locker-management-system-from-the-gallery"></a>Ajouter VECOS Releezme Locker management system à partir de la Galerie

Pour configurer l’intégration de VECOS Releezme Locker management system avec Azure AD, vous devez ajouter VECOS Releezme Locker management system à votre liste d’applications SaaS managées à partir de la galerie.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **VECOS Releezme Locker management system** dans la zone de recherche.
1. Sélectionnez **VECOS Releezme Locker management system** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-vecos-releezme-locker-management-system"></a>Configurer et tester l’authentification unique Azure AD pour VECOS Releezme Locker management system

Configurez et testez l’authentification unique Azure AD avec VECOS Releezme Locker management system à l’aide d’un utilisateur test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur associé dans VECOS Releezme Locker management system.

Pour configurer et tester l’authentification unique Azure AD avec VECOS Releezme Locker management system, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique de VECOS Releezme Locker management system](#configure-vecos-releezme-locker-management-system-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur test VECOS Releezme Locker management system](#create-vecos-releezme-locker-management-system-test-user)** pour avoir dans VECOS Releezme Locker management system un équivalent de B.Simon lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **VECOS Releezme Locker management system**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes : 

    a. Dans la zone de texte **Identificateur (ID d’entité)** , tapez une URL selon le modèle suivant : `https://<baseURL>/`

    b. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://<baseURL>/Saml2/Acs`
    
    c. Dans la zone de texte **URL de connexion**, saisissez une URL au format suivant :    
    `https://<baseURL>/sso` (si vous le souhaitez, ajoutez le paramètre de requête `?companycode=` avec la valeur de code d’entreprise donnée par VECOS.)

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL d’authentification et l’URL de réponse réels. Contactez l'[équipe de support technique du système d'administration Locker VECOS Releezme](mailto:servicedesk@vecos.com) pour connaître la région à laquelle vous vous connectez. En fonction de votre région, les URL ci-dessous seront différentes :

    | **Région** | **baseURL** |
    |-------|-------|
    | Europe| `https://www.releezme.net` |
    | Amérique du Nord | `https://na.releezme.net` |
    | Asie-Pacifique | `https://au.releezme.net` |

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur le bouton Copier pour copier l’**URL des métadonnées de fédération d’application**, puis enregistrez-la sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/copy-metadataurl.png)

## <a name="configure-vecos-releezme-locker-management-system-roles"></a>Configurer les rôles du système d'administration Locker de VECOS Releezme

1. Dans le Portail Azure, sélectionnez **Inscriptions d’applications**, puis sélectionnez **Toutes les applications**.
1. Dans la liste des inscriptions de l’application, sélectionnez **Système d'administration Locker VECOS Releezme**.
1. Dans l’inscription de l’application, ouvrez **Rôles de l’application**.
1. Dans la page des rôles de l’application, créez un nouveau rôle de l’application en cliquant sur **Créer un rôle de l’application**
1. Dans le champ **Nom complet**, entrez un nom pour le rôle, par ex. `VECOS Company Facility Manager`.
1. Sélectionnez **Utilisateurs/groupes**, comme valeur de **types de membres autorisés**.
1. Entrez le nom du rôle de système d'administration Locker de VECOS Releezme dans le champ **Valeur**. Consultez le tableau ci-dessous.
1. Cliquez sur **Appliquer**.

| Role | Valeur de rôle | Description |
| -- | --------- | ---------- |
| Service Desk | CompanyServiceDesk | Accès limité au service d’assistance. Accès en lecture seule principalement |
| Service Desk+ | CompanyServiceDeskPlus | Version avancée du service d’assistance avec un accès en lecture/écriture plus important |
| Gestionnaire des installations | CompanyFacilityManager | Gestionnaire des installations avec accès à la configuration de l’entreprise |
| Gestionnaire des installations+ | CompanyFacilityManagerPlus | Gestionnaire des installations avancé avec accès supplémentaire au sein de l’entreprise. |
| Administrateur | CompanyAdmin | Administrateur avec accès complet à l’entreprise |

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à VECOS Releezme Locker management system.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **VECOS Releezme Locker management system**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-vecos-releezme-locker-management-system-sso"></a>Configurer l’authentification unique de VECOS Releezme Locker management system

Pour configurer l’authentification unique côté **VECOS Releezme Locker management system**, vous devez envoyer l’**URL des métadonnées de fédération de l’application** à l’[équipe de support de VECOS Releezme Locker management system](mailto:servicedesk@vecos.com). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

### <a name="create-vecos-releezme-locker-management-system-test-user"></a>Créer un utilisateur test VECOS Releezme Locker management system

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans VECOS Releezme Locker management system. Collaborez avec l’[équipe de support de VECOS Releezme Locker management system](mailto:servicedesk@vecos.com) pour ajouter des utilisateurs dans la plateforme VECOS Releezme Locker management system. Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL d’authentification de VECOS Releezme Locker management system, d’où vous pouvez lancer le processus de connexion. 

* Accédez directement à l’URL d’authentification de VECOS Releezme Locker management system pour lancer le processus de connexion.

* Vous pouvez utiliser Mes applications de Microsoft. Quand vous cliquez sur la vignette VECOS Releezme Locker management system dans Mes applications, vous êtes redirigé vers l’URL d’authentification de VECOS Releezme Locker management system. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré VECOS Releezme Locker management system, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
