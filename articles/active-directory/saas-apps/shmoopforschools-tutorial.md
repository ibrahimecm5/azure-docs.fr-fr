---
title: 'Tutoriel : Intégration d’Azure Active Directory à Shmoop For Schools | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Shmoop For Schools.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/18/2020
ms.author: jeedes
ms.openlocfilehash: 83c08d603ab69cb06e3b82a11dd15daaf0410d52
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132304843"
---
# <a name="tutorial-integrate-shmoop-for-schools-with-azure-active-directory"></a>Tutoriel : Intégrer Shmoop For Schools à Azure Active Directory

Dans ce tutoriel, vous allez apprendre à intégrer Shmoop For Schools à Azure Active Directory (Azure AD). Quand vous Intégrez Shmoop For Schools à Azure Active Directory, vous pouvez :

- Contrôler qui a accès à Shmoop For Schools
- Autoriser les utilisateurs à se connecter automatiquement à Shmoop For Schools avec leur compte Azure AD
- Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

- Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
- Abonnement Shmoop For Schools avec authentification unique (SSO)

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

- Shmoop For Schools prend en charge l’authentification unique initiée par le **fournisseur de services**
- Shmoop For Schools prend en charge l’attribution d’utilisateurs **Juste-à-temps**

## <a name="adding-shmoop-for-schools-from-the-gallery"></a>Ajout de Shmoop For Schools à partir de la galerie

Pour configurer l’intégration de Shmoop For Schools à Azure AD, vous devez ajouter Shmoop For Schools dans votre liste d’applications SaaS managées à partir de la galerie.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Shmoop For Schools** dans la zone de recherche.
1. Sélectionnez **Shmoop For Schools** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-shmoop-for-schools"></a>Configurer et tester l’authentification unique Azure AD pour Shmoop For Schools

Configurez et testez l’authentification unique Azure AD avec Shmoop For Schools pour un utilisateur de test nommé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Shmoop For Schools associé.

Pour configurer et tester l’authentification unique Azure AD auprès de Shmoop For Schools, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
   1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
   1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
2. **[Configurer l’authentification unique Shmoop For Schools](#configure-shmoop-for-schools-sso)** pour configurer les paramètres de l’authentification unique côté application.
   1. **[Créer un utilisateur de test Shmoop For Schools](#create-shmoop-for-schools-test-user)** pour avoir un équivalent de B.Simon dans Shmoop For Schools qui soit associé à la représentation de l’utilisateur Azure AD.
3. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, dans la page d’intégration de l’application **Shmoop For Schools**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon pour **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

   a. Dans la zone de texte **URL de connexion**, saisissez une URL au format suivant : `https://schools.shmoop.com/public-api/saml2/start/<uniqueid>`

   b. Dans la zone de texte **Identificateur (ID d’entité)** , saisissez une URL au format suivant : `https://schools.shmoop.com/<uniqueid>`

   > [!NOTE]
   > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Pour obtenir ces valeurs, contactez [l’équipe de support du client Shmoop For Schools](mailto:support@shmoop.com). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. Votre application Shmoop For Schools attend les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à la configuration des attributs du jeton SAML. La capture d’écran suivante montre la liste des attributs par défaut.

   ![image](common/default-attributes.png)

1. En plus de ce qui précède, l’application Shmoop For Schools s’attend à ce que quelques attributs supplémentaires (présentés ci-dessous) soient repassés dans la réponse SAML. Ces attributs sont également préremplis, mais vous pouvez les examiner pour voir s’ils répondent à vos besoins.

   | Nom | Attribut source   |
   | ---- | ------------------ |
   | rôle | user.assignedroles |

   > [!NOTE]
   > Shmoop For Schools prend en charge deux rôles pour les utilisateurs : **Teacher** et **Student**. Configurez ces rôles dans Azure AD pour pouvoir affecter les rôles appropriés aux utilisateurs. Pour comprendre comment configurer des rôles dans Azure AD, consultez [cette page](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui).

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en accordant l’accès à Shmoop For Schools.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Shmoop For Schools**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous avez configuré les rôles comme indiqué ci-dessus, vous pouvez en sélectionner un dans la liste déroulante **Sélectionner un rôle**.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-shmoop-for-schools-sso"></a>Configurer l’authentification unique Shmoop For Schools

Pour configurer l’authentification unique côté **Shmoop For Schools**, vous devez envoyer **l’URL des métadonnées de fédération de l’application** à [l’équipe du support technique Shmoop For Schools](mailto:support@shmoop.com). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

### <a name="create-shmoop-for-schools-test-user"></a>Créer un utilisateur de test Shmoop For Schools

Dans cette section, un utilisateur appelé B.Simon est créé dans Shmoop For Schools. Shmoop For Schools prend en charge l’approvisionnement d'utilisateurs juste-à-temps, option activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas encore d’utilisateur dans Shmoop For Schools, il en est créé un après l’authentification.

> [!NOTE]
> Si vous devez créer un utilisateur manuellement, contactez [l’équipe de support de Shmoop For Schools](mailto:support@shmoop.com).

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes.

- Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion à Shmoop For Schools, où vous pouvez lancer le flux de connexion.

- Accédez directement à l’URL de connexion à Shmoop For Schools pour y lancer le flux de connexion.

- Vous pouvez utiliser Mes applications de Microsoft. Lorsque vous cliquez sur la vignette Shmoop For Schools dans Mes applications, vous êtes redirigé vers l’URL de connexion à Shmoop For Schools. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré Shmoop For Schools, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
