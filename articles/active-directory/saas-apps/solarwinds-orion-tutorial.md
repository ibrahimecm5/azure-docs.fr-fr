---
title: 'Tutoriel : Intégration de l’authentification unique Azure Active Directory à SolarWinds Orion | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et SolarWinds Orion.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/29/2021
ms.author: jeedes
ms.openlocfilehash: 925ed8e4461fb7c97ba4d29819862fe84c27d82a
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132320654"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-solarwinds-orion"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory à SolarWinds Orion

Dans ce tutoriel, vous allez apprendre à intégrer SolarWinds Orion à Azure Active Directory (Azure AD). En intégrant SolarWinds Orion à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à SolarWinds Orion.
* Permettre aux utilisateurs de se connecter automatiquement à SolarWinds Orion avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement SolarWinds Orion pour lequel l’authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* SolarWinds Orion prend en charge l’authentification unique lancée par **le fournisseur de services et le fournisseur d’identité**.

## <a name="add-solarwinds-orion-from-the-gallery"></a>Ajouter SolarWinds Orion à partir de la galerie

Pour configurer l’intégration de SolarWinds Orion à Azure AD, vous devez ajouter SolarWinds Orion à votre liste d’applications SaaS gérées à partir de la galerie.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **SolarWinds Orion** dans la zone de recherche.
1. Sélectionnez **SolarWinds Orion** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.


## <a name="configure-and-test-azure-ad-sso-for-solarwinds-orion"></a>Configurer et tester l’authentification unique Azure AD pour SolarWinds Orion

Configurez et testez l’authentification unique Azure AD avec SolarWinds Orion pour un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur SolarWinds Orion associé.

Pour configurer et tester l’authentification unique Azure AD avec SolarWinds Orion, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique SolarWinds Orion](#configure-solarwinds-orion-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test SolarWinds Orion](#create-solarwinds-orion-test-user)** pour avoir un équivalent de B.Simon dans SolarWinds Orion lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **SolarWinds Orion**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, si vous souhaitez configurer l’application en mode Initié par le **fournisseur d’identité**, entrez les valeurs pour les champs suivants :

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<ORION-HOSTNAME-OR-EXTERNAL-URL>`

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://<ORION-HOSTNAME-OR-EXTERNAL-URL>/Orion/SAMLLogin.aspx`

1. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<ORION-HOSTNAME-OR-EXTERNAL-URL>/Orion/Login.aspx`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL de réponse et l’URL de connexion réels. Pour obtenir ces valeurs, contactez l’[équipe de support SolarWinds Orion](mailto:technicalsupport@solarwinds.com). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. L’application SolarWinds Orion attend les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à la configuration des attributs du jeton SAML. La capture d’écran suivante montre la liste des attributs par défaut.

    ![image](common/default-attributes.png)

1. En plus de ce qui précède, l’application SolarWinds Orion s’attend à ce que quelques attributs supplémentaires (présentés ci-dessous) soient repassés dans la réponse SAML. Ces attributs sont également préremplis, mais vous pouvez les examiner pour voir s’ils répondent à vos besoins.
    
    | Nom |  Attribut source|
    | ----------- | --------- |
    | FirstName | user.givenname |
    | LastName | user.surname |
    | E-mail |user.mail |

1. Dans la section **Attributs utilisateur et revendications**, cliquez sur l’icône en forme de crayon pour modifier, puis cliquez sur **Ajouter une revendication de groupe**.

    ![Capture d’écran d’Attributs utilisateur et revendications.](./media/solarwinds-orion-tutorial/group-claim.png)

1. Choisissez **Groupes de sécurité**.
1. Si Azure AD est synchronisé avec votre instance locale d’AD, remplacez **Attribut source** par **sAMAccountName**. Dans le cas contraire, laissez-le comme ID de groupe.

1. Dans les **options avancées**, cochez la case **Personnaliser le nom de la revendication de groupe** et donnez-lui le nom OrionGroups.

1. Cliquez sur **Enregistrer**.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **Certificat (en base64)** , puis sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

1. Dans la section **Configurer SolarWinds Orion**, copiez l’URL ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Evergreen.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Evergreen**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-solarwinds-orion-sso"></a>Configurer l’authentification unique SolarWinds Orion

1. Connectez-vous à SolarWinds Orion et accédez à **Settings (Paramètres)**  -> **All Settings (Tous les paramètres)** .

    ![Capture d’écran affichant l’élément All Settings sélectionné dans Settings.](./media/solarwinds-orion-tutorial/settings.png)

1. Dans la section **USER ACCOUNTS** (Comptes d’utilisateurs), sélectionnez **SAML Configuration** (Configuration de SAML).

    ![Capture d’écran montrant la sélection de SAML Configuration à partir de User Accounts.](./media/solarwinds-orion-tutorial/configure-user-accounts.png)

1. Cliquez sur **ADD IDENTITY PROVIDER** (Ajouter un fournisseur d’identité).

    ![Capture d’écran montrant la section de la configuration SAML dans laquelle vous pouvez sélectionner ADD IDENTITY PROVIDER.](./media/solarwinds-orion-tutorial/configure-add-identity-provider.png)

1. Dans la page **Add Identity Provider**, effectuez les étapes suivantes :

    ![Capture d’écran montrant la page Add Identity Provider, dans laquelle vous pouvez indiquer les valeurs décrites.](./media/solarwinds-orion-tutorial/configure-solarwinds.png)

    a. Accédez à l’onglet **Configure** (Configurer).

    b. Dans la zone de texte **Identity Provider Name** (Nom du fournisseur d’identité), attribuez un nom de fournisseur valide comme `My SSO service`.

    c. Dans la zone de texte **SSO Target URL** (URL cible SSO), collez la valeur **URL de connexion** que vous avez copiée sur le portail Azure.

    d.  Dans la zone de texte **Issuer URL** (URL de l’émetteur), collez l’**Identificateur Azure AD** que vous avez copié à partir du portail Azure.

    e. Ouvrez le **Certificat (en base64)** téléchargé à partir du portail Azure dans le Bloc-notes, puis collez le contenu dans la zone de texte **X.509 Signing Certificate** (Certificat de signature X.509).

    f. Cliquez sur **Save**(Enregistrer).

### <a name="create-solarwinds-orion-test-user"></a>Créer un utilisateur de test SolarWinds Orion

1. Connectez-vous au site web SolarWinds Orion et accédez à **Settings (Paramètres)**  -> **All Settings (Tous les paramètres)** .

    ![Capture d’écran affichant l’élément All Settings sélectionné dans Settings.](./media/solarwinds-orion-tutorial/settings.png)

1. Dans la section **USER ACCOUNTS** (Comptes d’utilisateurs), sélectionnez **Manage Accounts** (Gérer les comptes).

    ![Capture d’écran montrant la sélection de SAML Configuration.](./media/solarwinds-orion-tutorial/user-accounts.png)

1. Sous l’onglet **INDIVIDUAL ACCOUNTS** (Comptes individuels), cliquez sur **ADD NEW ACCOUNT** (Ajouter un nouveau compte).

    ![Capture d’écran montrant l’option ADD NEW ACCOUNT sélectionnée dans Manage Accounts.](./media/solarwinds-orion-tutorial/create-user.png)

1. Sélectionnez le type de compte dont vous avez besoin pour créer des utilisateurs individuels ou des groupes SAML.

    ![Capture d’écran montrant l’ajout d’un nouveau compte, où vous pouvez sélectionner le type de compte.](./media/solarwinds-orion-tutorial/create-user-new-account.png)

1.  Dans la zone de texte **NAME ID** (ID de nom), entrez le nom qui doit correspondre exactement au nom d’utilisateur ou au nom de groupe figurant dans Azure AD.

1.  Cliquez sur **Next** (Suivant), puis envoyez la page.

    ![Capture d’écran montrant l’ajout d’un nouveau compte, où vous pouvez indiquer la valeur de Name ID à partir d’Azure AD.](./media/solarwinds-orion-tutorial/create-user-name-id.png)

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

#### <a name="sp-initiated"></a>Lancée par le fournisseur de services :

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion à SolarWinds Orion, d’où vous pouvez lancer le flux de connexion.  

* Accédez directement à l’URL de connexion à SolarWinds Orion et lancez le flux de connexion.

#### <a name="idp-initiated"></a>Lancée par le fournisseur d’identité :

* Dans le portail Azure, cliquez sur **Tester cette application**. Vous êtes alors automatiquement connecté à l’instance de SolarWinds Orion pour laquelle vous avez configuré l’authentification unique. 

Vous pouvez aussi utiliser Mes applications de Microsoft pour tester l’application dans n’importe quel mode. Quand vous cliquez sur la vignette SolarWinds Orion dans Mes applications, si le mode Fournisseur de services est configuré, vous êtes redirigé vers la page de connexion de l’application pour lancer le flux de connexion ; si le mode Fournisseur d’identité est configuré, vous êtes automatiquement connecté à l’instance de SolarWinds Orion pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré SolarWinds Orion, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
