---
title: 'Tutoriel : Intégration d’Azure Active Directory à Infor CloudSuite | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Infor CloudSuite.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/05/2021
ms.author: jeedes
ms.openlocfilehash: d1f8dc1c22b9a2dc4c8761931421d324ebc06c44
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132326499"
---
# <a name="tutorial-azure-active-directory-integration-with-infor-cloudsuite"></a>Tutoriel : Intégration d’Azure Active Directory à Infor CloudSuite

Dans ce tutoriel, vous allez voir comment intégrer Infor CloudSuite à Azure Active Directory (Azure AD). Quand vous intégrez Infor CloudSuite à Azure AD, vous pouvez :

* Contrôler qui dans Azure AD a accès à Infor CloudSuite.
* Permettre à vos utilisateurs de se connecter automatiquement à Infor CloudSuite avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à Infor CloudSuite, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un [compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Infor CloudSuite pour lequel l’authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Infor CloudSuite prend en charge l’authentification unique initiée par **le fournisseur de services et le fournisseur d’identité**
* Infor CloudSuite prend en charge [**l’attribution d’utilisateurs et la suppression des privilèges d’accès** automatiques](infor-cloudsuite-provisioning-tutorial.md) (recommandé).
* Infor CloudSuite prend en charge l’attribution d’utilisateurs **Juste-à-temps**

## <a name="add-infor-cloudsuite-from-the-gallery"></a>Ajouter Infor CloudSuite à partir de la galerie

Pour configurer l’intégration d’Infor CloudSuite à Azure AD, vous devez ajouter Infor CloudSuite, disponible dans la galerie, à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Infor CloudSuite** dans la zone de recherche.
1. Sélectionnez **Infor CloudSuite** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-infor-cloudsuite"></a>Configurer et tester l’authentification unique Azure AD pour Infor CloudSuite

Configurez et testez l’authentification unique Azure AD avec Infor CloudSuite pour un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur associé dans Infor CloudSuite.

Pour configurer et tester l’authentification unique Azure AD avec Infor CloudSuite, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Infor CloudSuite](#configure-infor-cloudsuite-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test Infor CloudSuite](#create-infor-cloudsuite-test-user)** pour avoir un équivalent de B.Simon dans Infor CloudSuite qui soit lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **Infor CloudSuite**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, si vous souhaitez configurer l’application en mode lancé par le **fournisseur d’identité**, effectuez les étapes suivantes :

    a. Dans la zone de texte **Identificateur**, tapez l’URL en utilisant l’un des modèles suivants :

    ```http
    http://mingle-sso.inforcloudsuite.com
    http://mingle-sso.se1.inforcloudsuite.com
    http://mingle-sso.eu1.inforcloudsuite.com
    http://mingle-sso.se2.inforcloudsuite.com
    ```

    b. Dans la zone de texte **URL de réponse**, tapez l’URL en utilisant l’un des modèles suivants :

    ```http
    https://mingle-sso.inforcloudsuite.com:443/sp/ACS.saml2
    https://mingle-sso.se1.inforcloudsuite.com:443/sp/ACS.saml2
    https://mingle-sso.se2.inforcloudsuite.com:443/sp/ACS.saml2
    https://mingle-sso.eu1.inforcloudsuite.com:443/sp/ACS.saml2
    ```

5. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    Dans la zone de texte **URL de connexion**, tapez une URL en utilisant un des modèles suivants :

    ```http
    https://mingle-portal.inforcloudsuite.com/Tenant-Name/
    https://mingle-portal.eu1.inforcloudsuite.com/Tenant-Name/
    https://mingle-portal.se1.inforcloudsuite.com/Tenant-Name/
    https://mingle-portal.se2.inforcloudsuite.com/Tenant-Name/
    ```

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL de réponse et l’URL de connexion réels. Pour obtenir ces valeurs, contactez l’[équipe de support technique Infor CloudSuite](mailto:support@infor.com). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

6. Sur la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le fichier **XML de métadonnées de fédération** en fonction des options définies selon vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

7. Dans la section **Configurer Infor CloudSuite**, copiez la ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Infor CloudSuite.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Infor CloudSuite**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-infor-cloudsuite-sso"></a>Configurer l’authentification unique pour Infor CloudSuite

Pour configurer l’authentification unique côté **Infor CloudSuite**, vous devez envoyer le fichier **XML des métadonnées de fédération** téléchargé et les URL appropriées copiées depuis le Portail Azure à l’[équipe du support technique Infor CloudSuite](mailto:support@infor.com). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

### <a name="create-infor-cloudsuite-test-user"></a>Créer un utilisateur de test Infor CloudSuite

Dans cette section, un utilisateur appelé Britta Simon est créé dans Infor CloudSuite. Infor CloudSuite prend en charge l’attribution d’utilisateurs juste-à-temps, option activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas encore d’utilisateur dans Infor CloudSuite, il en est créé un après l’authentification. Si vous devez créer un utilisateur manuellement, contactez l’[équipe du support technique Infor CloudSuite](mailto:support@infor.com).

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

#### <a name="sp-initiated"></a>Lancée par le fournisseur de services :

* Cliquez sur **Tester cette application** dans le portail Azure. Cette opération redirige vers l’URL de connexion Infor CloudSuite, à partir de laquelle vous pouvez initier le flux de connexion.  

* Accédez directement à l’URL de connexion Infor CloudSuite pour y initier le flux de connexion.

#### <a name="idp-initiated"></a>Lancée par le fournisseur d’identité :

* Dans le portail Azure, cliquez sur **Tester cette application**. Vous êtes alors automatiquement connecté à l’instance Infor CloudSuite pour laquelle vous avez configuré l’authentification unique. 

Vous pouvez aussi utiliser Mes applications de Microsoft pour tester l’application dans n’importe quel mode. Si, quand vous cliquez sur la vignette Infor CloudSuite dans Mes applications, le mode Fournisseur de services est configuré, vous êtes redirigé vers la page de connexion de l’application pour initier le flux de connexion. S’il s’agit du mode Fournisseur d’identité, vous êtes automatiquement connecté à l’instance Infor CloudSuite pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez configuré Infor CloudSuite, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
