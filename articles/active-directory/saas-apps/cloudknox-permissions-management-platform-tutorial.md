---
title: 'Tutoriel : Intégration de l’authentification unique Azure Active Directory à CloudKnox Permissions Management Platform | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et CloudKnox Permissions Management Platform.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/27/2021
ms.author: jeedes
ms.openlocfilehash: 2c958f965daadee63a8b2bdaf7e7ec8655e395f2
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132334907"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cloudknox-permissions-management-platform"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory à CloudKnox Permissions Management Platform

Dans ce tutoriel, vous allez apprendre à intégrer CloudKnox Permissions Management Platform à Azure AD (Azure Active Directory). Quand vous intégrez CloudKnox Permissions Management Platform à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à CloudKnox Permissions Management Platform.
* Permettre à vos utilisateurs de se connecter automatiquement à CloudKnox Permissions Management Platform avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement CloudKnox Permissions Management Platform pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* CloudKnox Permissions Management Platform prend en charge l’authentification unique lancée par le **fournisseur d’identité**

> [!NOTE]
> L’identificateur de cette application étant une valeur de chaîne fixe, une seule instance peut être configurée dans un locataire.

## <a name="adding-cloudknox-permissions-management-platform-from-the-gallery"></a>Ajout de CloudKnox Permissions Management Platform à partir de la galerie

Pour configurer l’intégration de CloudKnox Permissions Management Platform avec Azure AD, vous devez ajouter CloudKnox Permissions Management Platform, à partir de la galerie, à votre liste d’applications SaaS managées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **CloudKnox Permissions Management Platform** dans la zone de recherche.
1. Sélectionnez **CloudKnox Permissions Management Platform** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.


## <a name="configure-and-test-azure-ad-sso-for-cloudknox-permissions-management-platform"></a>Configurer et tester l’authentification unique Azure AD pour CloudKnox Permissions Management Platform

Configurez et testez l’authentification unique Azure AD avec CloudKnox Permissions Management Platform pour un utilisateur de test nommé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur CloudKnox Permissions Management Platform associé.

Pour configurer et tester l’authentification unique Azure AD avec CloudKnox Permissions Management Platform, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique CloudKnox Permissions Management Platform](#configure-cloudknox-permissions-management-platform-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test CloudKnox Permissions Management Platform](#create-cloudknox-permissions-management-platform-test-user)** pour avoir dans CloudKnox Permissions Management Platform un équivalent de B.Simon lié à la représentation Azure AD associée.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **CloudKnox Permissions Management Platform**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la page **Configurer l’authentification unique avec SAML**, entrez les valeurs pour les champs suivants :

    Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://app.cloudknox.io/saml/<ID>`

    > [!NOTE]
    > La valeur de l’URL de réponse n’est pas réelle. Mettez à jour la valeur avec l’URL de réponse réelle. Pour obtenir ces valeurs, contactez l’[équipe de support technique de CloudKnox Permissions Management Platform](mailto:support@cloudknox.io). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. L’application CloudKnox Permissions Management Platform s’attend à recevoir les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à votre configuration d’attributs de jetons SAML. La capture d’écran suivante montre la liste des attributs par défaut.

    ![image](common/default-attributes.png)

1. En plus de ce qui précède, l’application CloudKnox Permissions Management Platform s’attend à ce que quelques attributs supplémentaires soient repassés dans la réponse SAML. Ces attributs sont également préremplis, mais vous pouvez les examiner pour voir s’ils répondent à vos besoins.
    
    | Nom |  Attribut source|
    | --------------- | --------- |
    | First_Name | user.givenname |
    | Groupes | user.groups |
    | Last_Name | user.surname |
    | Email_Address | user.mail |

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **XML de métadonnées de fédération** et sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

1. Dans la section **Configurer CloudKnox Permissions Management Platform**, copiez la ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à CloudKnox Permissions Management Platform.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **CloudKnox Permissions Management Platform**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-cloudknox-permissions-management-platform-sso"></a>Configurer l’authentification unique CloudKnox Permissions Management Platform

Pour configurer l’authentification unique côté **CloudKnox Permissions Management Platform**, vous devez envoyer le **XML des métadonnées de fédération** téléchargé et les URL appropriées copiées à partir du portail Azure à l’[équipe du support technique de CloudKnox Permissions Management Platform](mailto:support@cloudknox.io). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

### <a name="create-cloudknox-permissions-management-platform-test-user"></a>Créer un utilisateur de test CloudKnox Permissions Management Platform

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans CloudKnox Permissions Management Platform. Rapprochez-vous de l’[équipe de support CloudKnox Permissions Management Platform](mailto:support@cloudknox.io) pour ajouter les utilisateurs à la plateforme CloudKnox Permissions Management Platform. Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes.

* Cliquez sur Tester cette application dans le portail Azure. Vous êtes alors connecté automatiquement à l’instance de CloudKnox Permissions Management Platform pour laquelle vous avez configuré l’authentification unique.

* Vous pouvez utiliser Mes applications de Microsoft. Quand vous cliquez sur la vignette CloudKnox Permissions Management Platform dans Mes applications, vous devez être automatiquement connecté à l’application CloudKnox Permissions Management Platform pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).


## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré CloudKnox Permissions Management Platform, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
