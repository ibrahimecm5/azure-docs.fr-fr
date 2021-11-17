---
title: 'Didacticiel : Intégration d’Azure Active Directory avec UltiPro | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et UltiPro.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/24/2020
ms.author: jeedes
ms.openlocfilehash: ab20365a728422caca5f734f460d8763b892e25a
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132332779"
---
# <a name="tutorial-azure-active-directory-integration-with-ultipro"></a>Didacticiel : Intégration d’Azure Active Directory à UltiPro

Dans ce tutoriel, vous allez apprendre à intégrer UltiPro à Azure Active Directory (Azure AD). Quand vous intégrez UltiPro à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à UltiPro.
* Permettre à vos utilisateurs de se connecter automatiquement à UltiPro avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.


## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement UltiPro pour lequel l’authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* UltiPro prend en charge l’authentification unique (SSO) lancée par le **fournisseur de services**.

## <a name="adding-ultipro-from-the-gallery"></a>Ajout d’UltiPro à partir de la galerie

Pour configurer l’intégration d’UltiPro à Azure AD, vous devez ajouter UltiPro à partir de la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **UltiPro** dans la zone de recherche.
1. Sélectionnez **UltiPro** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.


## <a name="configure-and-test-azure-ad-sso-for-ultipro"></a>Configurer et tester l’authentification unique Azure AD pour UltiPro

Configurez et testez l’authentification unique Azure AD avec UltiPro à l’aide d’un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir une relation entre un utilisateur Azure AD et l’utilisateur UltiPro associé.

Pour configurer et tester l’authentification unique Azure AD auprès d’UltiPro, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
2. **[Configurer l’authentification unique UltiPro](#configure-ultipro-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test UltiPro](#create-ultipro-test-user)** pour avoir un équivalent de B.Simon dans UltiPro, lié à la représentation Azure AD associée.
3. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **UltiPro**, recherchez la section **Gérer**, puis sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon pour **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    a. Dans la zone de texte **URL de connexion**, tapez une URL en respectant l’un des formats suivants :

    | URL de connexion |
    |-------------|
    | `https://<companyname>.ultipro.com/` |
    | `https://<companyname>.ultiproworkplace.com?cpi=AZUREADISSSUERURL` |
    | `https://<companyname>.ultipro.ca` |
    
    b. Dans la zone de texte **Identificateur**, tapez une URL en utilisant l’un des formats suivants :

    | Identificateur |
    |-------------|
    | `https://<companyname>.ultipro.com/adfs/services/trust` |
    | `https://<companyname>.ultiproworkplace.com/adfs/services/trust` |
    | `https://<companyname>.ultipro.ca/adfs/services/trust` |
    
    c. Dans la zone de texte **URL de réponse** , tapez une URL en respectant l’un des formats suivants :

    | URL de réponse |
    |-------------|
    | `https://<companyname>.ultipro.com/<instancename>` |
    | `https://<companyname>.ultiproworkplace.com/<instancename>` |
    | `https://<companyname>.ultipro.ca/<instancename>` |

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion, l’identificateur et l’URL de réponse réels. Pour obtenir ces valeurs, contactez l’[équipe de support technique d’UltiPro](https://www.ultimatesoftware.com/ContactUs). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

4. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (Base64)** en fonction des options définies par rapport à vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

6. Dans la section **Configurer UltiPro**, copiez la ou les URL appropriées correspondant à vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à UltiPro.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **UltiPro**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.


## <a name="configure-ultipro-sso"></a>Configurer l’authentification unique UltiPro

Pour configurer l’authentification unique côté **UltiPro**, vous devez envoyer le **Certificat (Base64)** téléchargé et les URL copiées correspondantes, depuis le Portail Azure à [l’équipe du support technique UltiPro](https://www.ultimatesoftware.com/ContactUs). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

### <a name="create-ultipro-test-user"></a>Créer un utilisateur de test UltiPro

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans UltiPro. Collaborez avec l’[équipe de support technique d’UltiPro](https://www.ultimatesoftware.com/ContactUs) pour ajouter les utilisateurs dans la plateforme UltiPro. Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion UltiPro, à partir de laquelle vous pouvez lancer le flux de connexion. 

* Accédez directement à l’URL de connexion UltiPro pour lancer le flux de connexion.

* Vous pouvez utiliser Mes applications de Microsoft. Le fait de cliquer sur la vignette UltiPro dans Mes applications vous redirige vers l’URL de connexion UltiPro. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré UltiPro, vous pouvez appliquer des contrôles de session qui protègent en temps réel contre l’exfiltration et l’infiltration des données sensibles de votre organisation. Les contrôles de session sont étendus à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
