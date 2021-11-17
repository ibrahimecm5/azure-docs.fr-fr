---
title: 'Tutoriel : Intégration de l’authentification unique Azure Active Directory à Mapbox | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Mapbox.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/16/2020
ms.author: jeedes
ms.openlocfilehash: c3e78f1b74c835fde35777a8a38339afdf102f3e
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132290829"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-mapbox"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory à Mapbox

Dans ce tutoriel, vous allez apprendre à intégrer Mapbox à Azure Active Directory (Azure AD). Quand vous intégrez Mapbox à Azure AD, vous pouvez :

* Contrôler, dans Azure AD, qui a accès à Mapbox.
* Permettre aux utilisateurs de se connecter automatiquement à Mapbox avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.


## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Mapbox pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* Mapbox prend en charge l’authentification unique lancée par le **fournisseur d’identité**

> [!NOTE]
> L’identificateur de cette application étant une valeur de chaîne fixe, une seule instance peut être configurée dans un locataire.

## <a name="adding-mapbox-from-the-gallery"></a>Ajout de Mapbox à partir de la galerie

Pour configurer l’intégration de Mapbox à Azure AD, vous devez ajouter Mapbox à partir de la galerie à votre liste d’applications SaaS managées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Mapbox** dans la zone de recherche.
1. Sélectionnez **Mapbox** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-mapbox"></a>Configurer et tester l’authentification unique Azure AD pour Mapbox

Configurez et testez l’authentification unique Azure AD avec Mapbox pour un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Mapbox associé.

Pour configurer et tester l’authentification unique Azure AD auprès de Mapbox, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Mapbox](#configure-mapbox-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test Mapbox](#create-mapbox-test-user)** pour avoir un équivalent de B.Simon dans Mapbox lié à la représentation Azure AD associée.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **Mapbox**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, l’application est préconfigurée et les URL nécessaires sont déjà préremplies avec Azure. L’utilisateur doit enregistrer la configuration en cliquant sur le bouton **Enregistrer**.

1. L’application Mapbox s’attend à recevoir les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à votre configuration d’attributs de jetons SAML. La capture d’écran suivante montre la liste des attributs par défaut.

    ![image](common/default-attributes.png)

1. En plus de ce qui précède, l’application Mapbox s’attend à ce que quelques attributs supplémentaires (présentés ci-dessous) soient repassés dans la réponse SAML. Ces attributs sont également préremplis, mais vous pouvez les examiner pour voir s’ils répondent à vos besoins.

    | Nom   |  Attribut source|
    | -----|--------- |
    | rôle | user.assignedroles |
    | | |

    > [!NOTE]
    > Pour comprendre comment configurer des rôles dans Azure AD, consultez [cette page](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui).

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **Certificat (brut)** , puis sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificateraw.png)

1. Dans la section **Configurer Mapbox**, copiez la ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Mapbox.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Mapbox**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous avez configuré les rôles comme indiqué ci-dessus, vous pouvez en sélectionner un dans la liste déroulante **Sélectionner un rôle**.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-mapbox-sso"></a>Configurer l’authentification unique Mapbox

1. Dans une autre fenêtre du navigateur web, connectez-vous à Mapbox en tant qu’administrateur.

1. Cliquez sur l’onglet **Settings** (Paramètres).

    ![Mapbox – Onglet des paramètres](./media/mapbox-tutorial/configure1.png)

1. Cliquez sur l’onglet **Security** (Sécurité) dans le volet de navigation gauche.

    ![Mapbox – Onglet de la sécurité](./media/mapbox-tutorial/configure2.png)

1. Cliquez sur **Edit single sign-on** (Modifier l’authentification unique).

    ![Mapbox – Modifier l’authentification unique](./media/mapbox-tutorial/configure3.png)

1. Faites défiler jusqu’à **Step 3: Setup SAML single sign-on for Mapbox** (Configurer l’authentification unique SAML pour Mapbox) et effectuez les étapes suivantes :

    ![Configuration de Mapbox](./media/mapbox-tutorial/configure4.png)

    1. Dans la zone de texte **Idp Sign-on URL** (URL de connexion du fournisseur d’identité), collez l’**URL de connexion** que vous avez copiée à partir du portail Azure.

    1. Dans la zone de texte **Issuer ID** (ID de l’émetteur), collez l’**Identificateur Azure AD** que vous avez copié à partir du portail Azure.

    1. Dans le Bloc-notes, ouvrez le fichier de certificat brut (**Certificate (Raw)** ) téléchargé à partir du portail Azure, copiez le contenu du certificat, puis collez-le dans la zone de texte **X.509 certificate**.

    1. Cliquez sur **Save single sign-on settings** (Enregistrer les paramètres d’authentification unique).

### <a name="create-mapbox-test-user"></a>Créer un utilisateur de test Mapbox

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans Mapbox. Collaborez avec l’[équipe du support technique Mapbox](mailto:help@mapbox.com) pour ajouter les utilisateurs à la plateforme Mapbox. Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes.

* Cliquez sur Tester cette application dans le portail Azure : vous devez être connecté automatiquement à l’instance de Mapbox pour laquelle vous avez configuré l’authentification unique.

* Vous pouvez utiliser Mes applications de Microsoft. Quand vous cliquez sur la vignette Mapbox dans Mes applications, vous devez être connecté automatiquement à l’application Mapbox pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).


## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré Mapbox, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
