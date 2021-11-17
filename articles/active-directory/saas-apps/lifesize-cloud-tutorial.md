---
title: 'Didacticiel : Intégration d’Azure Active Directory dans Lifesize Cloud | Documentation Microsoft'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Lifesize Cloud.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/24/2021
ms.author: jeedes
ms.openlocfilehash: 948a97d598710ad8cd10a3bf89dfd2cb7b512413
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132313458"
---
# <a name="tutorial-azure-active-directory-integration-with-lifesize-cloud"></a>Didacticiel : Intégration d’Azure Active Directory à Lifesize Cloud

Dans ce tutoriel, vous allez apprendre à intégrer Lifesize Cloud à Azure Active Directory (Azure AD). Lorsque vous intégrez Lifesize Cloud à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Lifesize Cloud.
* Permettre à vos utilisateurs de se connecter automatiquement à Lifesize Cloud avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Lifesize Cloud pour lequel l'authentification unique (SSO) est activée.

> [!NOTE]
> Cette intégration peut également être utilisée à partir de l’environnement cloud US Government Azure AD. Cette application est disponible dans la Galerie d’applications cloud US Government Azure AD et peut être configurée de la même façon que dans le cloud public.

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Lifesize Cloud prend en charge l'authentification unique lancée par le **fournisseur de services**.

* Lifesize Cloud prend en charge l'approvisionnement **automatique** d'utilisateurs.

## <a name="add-lifesize-cloud-from-the-gallery"></a>Ajouter Lifesize Cloud à partir de la galerie

Pour configurer l’intégration de Lifesize Cloud à Azure AD, vous devez ajouter Lifesize Cloud de la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, entrez **Lifesize Cloud** dans la zone de recherche.
1. Sélectionnez **Lifesize Cloud** dans le volet de résultats, puis ajoutez l'application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-lifesize-cloud"></a>Configurer et tester l'authentification unique Azure AD pour Lifesize Cloud

Configurez et testez l'authentification unique Azure AD auprès de Lifesize Cloud à l'aide d'un utilisateur de test appelé **B.Simon**. Pour que l'authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l'utilisateur associé dans Lifesize Cloud.

Pour configurer et tester l'authentification unique Azure AD auprès de Lifesize Cloud, suivez les étapes ci-dessous :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l'authentification unique Lifesize Cloud](#configure-lifesize-cloud-sso)** pour configurer les paramètres de l'authentification unique côté application.
    1. **[Créer un utilisateur de test Lifesize Cloud](#create-lifesize-cloud-test-user)** pour avoir, dans Lifesize Cloud, un équivalent de B.Simon qui est lié à la représentation d'un utilisateur Azure AD.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Sur le portail Azure, accédez à la page d'intégration de l'application **Lifesize Cloud**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://login.lifesizecloud.com/ls/?acs`.

    b. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://login.lifesizecloud.com/<COMPANY_NAME>`

    c. Cliquez sur **Définir des URL supplémentaires**.

    d. Dans la zone de texte **État de relais**, entrez une URL en utilisant le modèle suivant : `https://webapp.lifesizecloud.com/?ent=<IDENTIFIER>`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion, l’identificateur et l’état de relais réels. Pour obtenir les valeurs d’URL de connexion et d’identificateur, contactez l’[équipe du support technique Lifesize Cloud](https://legacy.lifesize.com/en/support). Vous pouvez également obtenir la valeur de l’état de relais à partir de la configuration SSO, expliquée plus loin dans ce tutoriel. Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

5. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (Base64)** en fonction des options définies par rapport à vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

6. Dans la section **Configurer Lifesize Cloud**, copiez la ou les URL appropriées correspondant à vos besoins.

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

Dans cette section, vous allez permettre à B.Simon d'utiliser l'authentification unique Azure en lui accordant l'accès à Lifesize Cloud.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Lifesize Cloud**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-lifesize-cloud-sso"></a>Configurer l'authentification unique pour Lifesize Cloud

1. Pour configurer SSO pour votre application, connectez-vous à l’application Lifesize Cloud avec les privilèges d’administrateur.

2. Dans le coin supérieur droit, cliquez sur votre nom, puis cliquez sur **Paramètres avancés**.

    ![Capture d’écran montrant l’élément de menu Advanced Settings.](./media/lifesize-cloud-tutorial/settings.png)

3. Dans les Paramètres avancés, cliquez maintenant sur le lien **Configuration de l’authentification unique**. La page Configuration SSO de l’instance s’ouvre.

    ![Capture d’écran montrant la zone des paramètres avancés dans laquelle vous pouvez sélectionner « SSO configuration ».](./media/lifesize-cloud-tutorial/configuration.png)

4. Maintenant, configurez les valeurs suivantes dans l’interface utilisateur de configuration de l’authentification unique.

    ![Capture d’écran montrant la page SSO Configuration dans laquelle vous pouvez indiquer les valeurs décrites.](./media/lifesize-cloud-tutorial/values.png)

    a. Dans la zone de texte **Identity Provider Issuer** (Émetteur du fournisseur d’identité), collez la valeur de **l’identificateur Azure AD** que vous avez copié à partir du portail Azure.

    b.  Dans la zone de texte **Login URL** (URL de connexion), collez la valeur **URL de connexion** que vous avez copiée dans le Portail Azure.

    c. Ouvrez dans le Bloc-notes votre certificat codé en base 64 téléchargé à partir du portail Azure, copiez son contenu dans le Presse-papiers, puis collez-le dans la zone de texte **Certificat X.509**.
  
    d. Dans la zone de texte First Name (Prénom) des mappages d’attribut SAML, entrez la valeur `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

    e. Dans la zone de texte **Last Name** (Nom) des mappages d’attribut SAML, entrez la valeur `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`

    f. Dans la zone de texte **Email** des mappages d’attribut SAML, entrez la valeur `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

5. Pour vérifier la configuration, vous pouvez cliquer sur le bouton **Test**.

    >[!NOTE]
    >Pour que les tests se déroulent correctement, vous devez exécuter l’assistant de configuration dans Azure AD et également fournir l’accès aux utilisateurs ou groupes autorisés à effectuer le test.

6. Activez l’authentification unique en appuyant sur le bouton **Activer l’authentification unique**.

7. Maintenant, cliquez sur le bouton **Mettre à jour** afin que tous les paramètres soient enregistrés. Cela génère la valeur RelayState. Copiez la valeur RelayState générée dans la zone de texte, puis collez-la dans la zone de texte **État de relais**, sous la section **Domaine et URL Lifesize Cloud**.

### <a name="create-lifesize-cloud-test-user"></a>Créer un utilisateur de test Lifesize Cloud

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans Lifesize Cloud. Lifesize Cloud ne prend pas en charge l’approvisionnement automatique des utilisateurs. Après une authentification réussie à Azure AD, l’utilisateur sera automatiquement approvisionné dans l’application.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l'URL de connexion à Lifesize Cloud, à partir de laquelle vous pouvez lancer le flux de connexion. 

* Accédez directement à l'URL de connexion à Lifesize Cloud pour lancer le flux de connexion.

* Vous pouvez utiliser Mes applications de Microsoft. Le fait de cliquer sur la vignette Lifesize Cloud dans Mes applications vous redirige vers l'URL de connexion à Lifesize Cloud. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré Lifesize Cloud, vous pouvez appliquer le contrôle de session, qui protège en temps réel contre l'exfiltration et l'infiltration des données sensibles de votre organisation. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
