---
title: 'Tutoriel : Intégration de l’authentification unique Azure AD à Cloud Management Portal for Microsoft Azure'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Cloud Management Portal for Microsoft Azure.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/30/2021
ms.author: jeedes
ms.openlocfilehash: ebc9949cd1b6494d9641aecf989b6af5e09701e6
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132342221"
---
# <a name="tutorial-azure-ad-sso-integration-with-cloud-management-portal-for-microsoft-azure"></a>Tutoriel : Intégration de l’authentification unique Azure AD à Cloud Management Portal for Microsoft Azure

Dans ce tutoriel, vous allez apprendre à intégrer Cloud Management Portal for Microsoft Azure à Azure Active Directory (Azure AD). Quand vous intégrez Cloud Management Portal for Microsoft Azure à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Cloud Management Portal for Microsoft Azure.
* Permettre aux utilisateurs de se connecter automatiquement à Cloud Management Portal for Microsoft Azure avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Cloud Management Portal for Microsoft Azure pour lequel l’authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Cloud Management Portal for Microsoft Azure prend en charge l’authentification unique lancée par le **fournisseur de services**.

## <a name="add-cloud-management-portal-for-microsoft-azure-from-the-gallery"></a>Ajouter Cloud Management Portal for Microsoft Azure à partir de la galerie

Pour configurer l’intégration de Cloud Management Portal for Microsoft Azure à Azure AD, vous devez ajouter Cloud Management Portal for Microsoft Azure à partir de la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Cloud Management Portal for Microsoft Azure** dans la zone de recherche.
1. Sélectionnez **Cloud Management Portal for Microsoft Azure** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-cloud-management-portal-for-microsoft-azure"></a>Configurer et tester l’authentification unique Azure AD pour Cloud Management Portal for Microsoft Azure

Configurer et tester l’authentification unique Azure AD avec Cloud Management Portal for Microsoft Azure à l’aide d’un utilisateur test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Cloud Management Portal for Microsoft Azure associé.

Pour configurer et tester l’authentification unique Azure AD avec Cloud Management Portal for Microsoft Azure, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique de Cloud Management Portal for Microsoft Azure](#configure-cloud-management-portal-for-microsoft-azure-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur test Cloud Management Portal for Microsoft Azure](#create-cloud-management-portal-for-microsoft-azure-test-user)** pour avoir un équivalent de B.Simon dans Cloud Management Portal for Microsoft Azure lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **Cloud Management Portal for Microsoft Azure**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :
  
    a. Dans la zone **Identificateur**, tapez une URL en utilisant un des modèles suivants :

    | **Identificateur** |
    |------|
    | `https://<subdomain>.igcm.com` |
    | `https://<subdomain>.newsignature.com` |

    b. Dans la zone de texte **URL de réponse** , tapez une URL en utilisant un des modèles suivants :

    | **URL de réponse** |
    |----|
    | `https://<subdomain>.igcm.com/<instancename>` |
    | `https://<subdomain>.newsignature.com` |
    | `https://<subdomain>.newsignature.com/<instancename>` |
    
    c. Dans la zone de texte **URL de connexion**, tapez une URL en utilisant un des modèles suivants :

    | **URL d’authentification** |
    |------|
    | `https://portal.newsignature.com/<instancename>` |
    | `https://portal.igcm.com/<instancename>` |

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL d’authentification et l’URL de réponse réels. Pour obtenir ces valeurs, contactez [l’équipe du support client Cloud Management Portal for Microsoft Azure](mailto:jczernuszka@newsignature.com). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

5. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (Base64)** en fonction des options définies par rapport à vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

6. Dans la section **Configurer Cloud Management Portal for Microsoft Azure**, copiez la ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Cloud Management Portal for Microsoft Azure.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Cloud Management Portal for Microsoft Azure**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-cloud-management-portal-for-microsoft-azure-sso"></a>Configurer l’authentification unique de Cloud Management Portal for Microsoft Azure

Pour configurer l’authentification unique côté **Cloud Management Portal for Microsoft Azure**, vous devez envoyer le **Certificat (Base64)** téléchargé et les URL appropriées copiées à partir du portail Azure à l’[équipe du support technique Cloud Management Portal for Microsoft Azure](mailto:jczernuszka@newsignature.com). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

### <a name="create-cloud-management-portal-for-microsoft-azure-test-user"></a>Créer un utilisateur de test Cloud Management Portal for Microsoft Azure

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans Cloud Management Portal for Microsoft Azure. Rapprochez-vous de l’[équipe de support Cloud Management Portal for Microsoft Azure](mailto:jczernuszka@newsignature.com) pour ajouter les utilisateurs dans la plateforme Cloud Management Portal for Microsoft Azure. Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL d’authentification Cloud Management Portal for Microsoft Azure, à partir de laquelle vous pouvez lancer le processus de connexion. 

* Accédez directement à l’URL d’authentification Cloud Management Portal for Microsoft Azure pour lancer le processus de connexion.

* Vous pouvez utiliser Mes applications de Microsoft. Quand vous cliquez sur la vignette Cloud Management Portal for Microsoft Azure dans Mes applications, vous devriez être redirigé vers l’URL d’authentification Cloud Management Portal for Microsoft Azure. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré Cloud Management Portal for Microsoft Azure, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
