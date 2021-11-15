---
title: 'Tutoriel : Intégration de l’authentification unique Azure AD à Certain Admin SSO'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Certain Admin SSO.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/25/2021
ms.author: jeedes
ms.openlocfilehash: e34306e02b17e1e13e78a93934c0e658ac388c00
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131451461"
---
# <a name="tutorial-azure-active-directory-integration-with-certain-admin-sso"></a>Tutoriel : Intégration d’Azure Active Directory avec Certain Admin SSO

Dans ce tutoriel, vous allez apprendre à intégrer Certain Admin SSO à Azure Active Directory (Azure AD). Quand vous intégrez Certain Admin SSO à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Certain Admin SSO.
* Permettre à vos utilisateurs de se connecter automatiquement à Certain Admin SSO avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Certain Admin SSO pour lequel l’authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Certain Admin SSO prend en charge l’authentification unique lancée par le **fournisseur de services**.

## <a name="add-certain-admin-sso-from-the-gallery"></a>Ajouter Certain Admin SSO à partir de la galerie

Pour configurer l’intégration de Certain Admin SSO à Azure AD, vous devrez ajouter Certain Admin SSO à votre liste d’applications SaaS gérées à partir de la galerie.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, saisissez **Certain Admin SSO** dans la zone de recherche.
1. Sélectionnez **Certain Admin SSO** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-certain-admin-sso"></a>Configurer et tester l’authentification unique Azure AD pour Certain Admin SSO

Configurez et testez l’authentification unique Azure AD avec Certain Admin SSO à l’aide d’un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Certain Admin SSO associé.

Pour configurer et tester l’authentification unique Azure AD avec Certain Admin SSO, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Certain Admin SSO](#configure-certain-admin-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test Certain Admin SSO](#create-certain-admin-sso-test-user)** pour avoir un équivalent de B.Simon dans Certain Admin SSO lié à la représentation Azure AD associée.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **Certain Admin SSO**, recherchez la section **Gérer**, puis sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    a. Dans la zone de texte **Identificateur (ID d’entité)** , saisissez une URL au format suivant : `https://<SUBDOMAIN>.certain.com`

    b. Dans la zone de texte **URL de connexion**, saisissez une URL au format suivant : `https://<YOUR DOMAIN URL>/svcs/sso_admin_login/handleRequest/<ID>`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur et l’URL de connexion réels. Contactez [l’équipe de support client de Certain Admin SSO](mailto:integrations@certain.com) pour obtenir ces valeurs. Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

5. Dans la page **Configurer l’authentification unique avec SAML**, à la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (Raw)** en fonction des options définies, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificateraw.png)

6. Dans la section **Configurer Certain Admin SSO**, copiez les URL appropriées, selon vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Certain Admin SSO.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Certain Admin SSO**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-certain-admin-sso"></a>Configurer l’authentification unique Certain Admin SSO

Pour configurer l’authentification unique côté **Certain Admin SSO**, vous devez envoyer le **certificat (brut)** téléchargé et les URL copiées appropriées du Portail Azure à l’[équipe du support technique Certain Admin SSO](mailto:integrations@certain.com). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

### <a name="create-certain-admin-sso-test-user"></a>Créer un utilisateur de test Certain Admin SSO

Dans cette section, vous allez créer un utilisateur nommé Britta Simon dans Certain Admin SSO. Travaillez en collaboration avec [l’équipe de support de Certain Admin SSO](mailto:integrations@certain.com) pour ajouter des utilisateurs à la plateforme Certain Admin SSO. Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion de Certain Admin SSO, à partir de laquelle vous pouvez lancer le flux de connexion. 

* Accédez directement à l’URL de connexion de Certain Admin SSO pour lancer le flux de connexion.

* Vous pouvez utiliser Mes applications de Microsoft. Lorsque vous cliquez sur la vignette Certain Admin SSO dans Mes applications, vous êtes redirigé vers l’URL de connexion de Certain Admin SSO. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré Certain Admin SSO, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).