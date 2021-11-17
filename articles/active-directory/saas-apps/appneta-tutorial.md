---
title: 'Tutoriel : Intégration de l’authentification unique Azure AD à AppNeta Performance Manager'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et AppNeta Performance Manager.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/12/2021
ms.author: jeedes
ms.openlocfilehash: c9edd0bc2b1abe9aa60ff9d4c6ef08e6894b14d2
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132288885"
---
# <a name="tutorial-azure-ad-sso-integration-with-appneta-performance-manager"></a>Tutoriel : Intégration de l’authentification unique Azure AD à AppNeta Performance Manager

Dans ce tutoriel, vous allez découvrir comment intégrer AppNeta Performance Manager à Azure Active Directory (Azure AD). Quand vous intégrez AppNeta Performance Manager à Azure AD, vous pouvez :

- Contrôler, dans Azure AD, qui a accès à AppNeta Performance Manager.
- Permettre aux utilisateurs de se connecter automatiquement à AppNeta Performance Manager avec leur compte Azure AD.
- Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

- Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
- Un abonnement à AppNeta Performance Manager pour lequel l’authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

- AppNeta Performance Manager prend en charge l’authentification unique lancée par le **fournisseur de services**.
- AppNeta Performance Manager prend en charge l’attribution d’utilisateurs **juste-à-temps**.

> [!NOTE]
> L’identificateur de cette application étant une valeur de chaîne fixe, une seule instance peut être configurée dans un locataire.

## <a name="adding-appneta-performance-manager-from-the-gallery"></a>Ajout d’AppNeta Performance Manager à partir de la galerie

Pour configurer l’intégration d’AppNeta Performance Manager à Azure AD, vous devez ajouter AppNeta Performance Manager, disponible dans la galerie, à votre liste d’applications SaaS managées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **AppNeta Performance Manager** dans la zone de recherche.
1. Sélectionnez **AppNeta Performance Manager** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-appneta-performance-manager"></a>Configurer et tester l’authentification unique Azure AD pour AppNeta Performance Manager

Configurez et testez l’authentification unique Azure AD avec AppNeta Performance Manager à l’aide d’un utilisateur de test nommé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir une relation entre un utilisateur Azure AD et l’utilisateur associé dans AppNeta Performance Manager.

Pour configurer et tester l’authentification unique Azure AD avec AppNeta Performance Manager, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
   1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
   1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique AppNeta Performance Manager](#configure-appneta-performance-manager-sso)** pour configurer les paramètres de l’authentification unique côté application.
   1. **[Créer un utilisateur de test AppNeta Performance Manager](#create-appneta-performance-manager-test-user)** pour avoir, dans AppNeta Performance Manager, un équivalent de B.Simon lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Sur le portail Azure, dans la page d’intégration de l’application **AppNeta Performance Manager**, recherchez la section **Gérer**, puis sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](./media/appneta-tutorial/edit-urls.png)

1. Dans la section **Configuration SAML de base**, entrez les valeurs pour les champs suivants :

   a. Dans la zone de texte **URL de connexion**, saisissez une URL au format suivant : `https://<subdomain>.pm.appneta.com`

   b. Dans le champ URL de réponse (URL Assertion Consumer Service), entrez : `https://sso.connect.pingidentity.com/sso/sp/ACS.saml2`

   > [!NOTE]
   > La valeur de l’URL d’authentification ci-dessus est un exemple. Mettez à jour cette valeur avec l’URL d’authentification réelle. Pour obtenir cette valeur, contactez l’[équipe du support d’AppNeta Performance Manager](mailto:support@appneta.com). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. L’application AppNeta Performance Manager s’attend à recevoir les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à votre configuration des attributs de jetons SAML. La capture d’écran suivante montre la liste des attributs par défaut :

   ![Capture d’écran montrant les attributs par défaut pour un jeton SAML.](./media/appneta-tutorial/edit-attribute.png)

1. En plus de ce qui précède, l’application AppNeta Performance Manager s’attend à ce que quelques attributs supplémentaires (présentés ci-dessous) soient repassés dans la réponse SAML. Ces attributs sont également préremplis, mais vous pouvez les examiner pour voir s’ils répondent à vos besoins.

   | Nom      | Attribut source       |
   | --------- | ---------------------- |
   | firstName | user.givenname         |
   | lastName  | user.surname           |
   | email     | user.userprincipalname |
   | name      | user.userprincipalname |
   | groups    | user.assignedroles     |
   | phone     | user.telephonenumber   |
   | title     | user.jobtitle          |
   |           |                        |

1. Pour passer correctement les assertions SAML « groups », vous devez configurer des rôles d’application et définir leur valeur pour qu’elle corresponde aux mappages de rôles définis dans AppNeta Performance Manager. Sous **Azure Active Directory** > **Inscriptions des applications** >  **Toutes les applications**, sélectionnez **Appneta Performance Manager**.

   ![Capture d’écran montrant les inscriptions des applications avec Appneta Performance Manager en bas. ](./media/appneta-tutorial/app-registrations.png)

1. Dans le volet de gauche, cliquez sur **Rôles d’application**. L’écran suivant apparaît :

   ![Capture d’écran montrant les rôles d’application avec Appneta Performance Manager en bas. ](./media/appneta-tutorial/app-roles.png)

1. Cliquez sur **Créer un rôle d’application**.
1. Dans l’écran **Créer un rôle d’application**, suivez ces étapes :
   1. Dans le champ **Nom complet**, entrez un nom pour le rôle.
   1. Dans le champ **Types de membres autorisés**, sélectionnez **Utilisateurs/groupes**.
   1. Dans le champ **Valeur**, entrez la valeur du groupe de sécurité défini dans vos mappages de rôles AppNeta Performance Manager.
   1. Dans le champ **Description**, entrez une description pour le rôle.
   1. Cliquez sur **Appliquer**.

   ![Capture d’écran de la boîte de dialogue Créer un rôle d’application avec les champs remplis comme décrit. ](./media/appneta-tutorial/create-app-role.png)

1. Après avoir créé les rôles, vous devez les mapper à vos utilisateurs/groupes. Accédez à **Azure Active Directory** > **Applications d’entreprise** > **Appneta Performance Manager** > **Utilisateurs et groupes**.
1. Sélectionnez un utilisateur/groupe, puis attribuez le rôle d’application souhaité (créé à l’étape précédente).
1. Une fois que vous avez mappé les rôles d’application, accédez à **Azure Active Directory** > **Applications d’entreprise** > **Appneta Performance Manager** > **Authentification unique**.
1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **XML de métadonnées de fédération** et sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

   ![Lien Téléchargement de certificat](common/metadataxml.png)

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à AppNeta Performance Manager.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **AppNeta Performance Manager**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous avez configuré les rôles comme indiqué ci-dessus, vous pouvez en sélectionner un dans la liste déroulante **Sélectionner un rôle**.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

   > [!NOTE]
   > En pratique, vous ajouterez des groupes à l’application plutôt que des utilisateurs individuels.

## <a name="configure-appneta-performance-manager-sso"></a>Configurer l’authentification unique AppNeta Performance Manager

Pour configurer l’authentification unique côté **AppNeta Performance Manager**, vous devez envoyer le **XML des métadonnées de fédération** téléchargé à l’[équipe du support d’AppNeta Performance Manager](mailto:support@appneta.com). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

### <a name="create-appneta-performance-manager-test-user"></a>Créer un utilisateur de test AppNeta Performance Manager

Dans cette section, un utilisateur appelé B.Simon est créé dans AppNeta Performance Manager. AppNeta Performance Manager prend en charge l’attribution d’utilisateurs juste-à-temps, option activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas encore d’utilisateur dans AppNeta Performance Manager, il en est créé un après l’authentification.

> [!Note]
> Si vous devez créer un utilisateur manuellement, contactez l’[équipe du support technique AppNeta Performance Manager](mailto:support@appneta.com).

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes.

- Dans le portail Azure, sélectionnez **Tester cette application**. Vous êtes alors redirigé vers l’URL d’authentification AppNeta Performance Manager, d’où vous pouvez lancer le processus de connexion.

- Accédez directement à l’URL de connexion à AppNeta Performance Manager pour y lancer le flux de connexion.

- Vous pouvez utiliser Mes applications de Microsoft. Quand vous cliquez sur la vignette AppNeta Performance Manager dans le portail Mes applications, vous êtes redirigé vers l’URL d’authentification AppNeta Performance Manager. Pour plus d’informations sur le portail Mes applications, consultez [Introduction à Mes applications](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré AppNeta Performance Manager, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
