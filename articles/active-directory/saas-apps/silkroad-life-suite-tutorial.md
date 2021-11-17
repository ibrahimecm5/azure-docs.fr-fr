---
title: 'Didacticiel : Intégration d’Azure Active Directory à SilkRoad Life Suite | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et SilkRoad Life Suite.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/09/2021
ms.author: jeedes
ms.openlocfilehash: 8bef3229ca8ca814e55d9dba34c79646367bbd7c
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132304683"
---
# <a name="tutorial-azure-active-directory-integration-with-silkroad-life-suite"></a>Didacticiel : Intégration d’Azure Active Directory à SilkRoad Life Suite

Dans ce tutoriel, vous allez apprendre à intégrer SilkRoad Life Suite à Azure Active Directory (Azure AD). Quand vous intégrez SilkRoad Life Suite à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à SilkRoad Life Suite.
* Permettre à vos utilisateurs de se connecter automatiquement à SilkRoad Life Suite avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD avec SilkRoad Life Suite, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un [compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement SilkRoad Life Suite pour lequel l’authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* SilkRoad Life Suite prend en charge l’authentification unique lancée par le **fournisseur de services**.

## <a name="add-silkroad-life-suite-from-the-gallery"></a>Ajouter SilkRoad Life Suite à partir de la galerie

Pour configurer l’intégration de SilkRoad Life Suite dans Azure AD, vous devez ajouter SilkRoad Life Suite, disponible dans la galerie, à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **SilkRoad Life Suite** dans la zone de recherche.
1. Sélectionnez **SilkRoad Life Suite** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-silkroad-life-suite"></a>Configurer et tester l’authentification unique (SSO) Azure AD pour SilkRoad Life Suite

Configurez et testez l’authentification unique Azure AD avec SilkRoad Life Suite à l’aide d’un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur SilkRoad Life Suite associé.

Pour configurer et tester l’authentification unique Azure AD avec SilkRoad Life Suite, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique SilkRoad Life Suite](#configure-silkroad-life-suite-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test SilkRoad Life Suite](#create-silkroad-life-suite-test-user)** pour avoir un équivalent de B.Simon dans SilkRoad Life Suite qui soit lié à la représentation de l’utilisateur dans Azure AD.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **SilkRoad Life Suite**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, si vous disposez d’un **fichier de métadonnées du fournisseur de services**, suivez les étapes ci-dessous :

    > [!NOTE]
    > Le **fichier de métadonnées du fournisseur de services** est expliqué plus loin dans ce tutoriel.

    a. Cliquez sur **Charger un fichier de métadonnées**.

    ![Capture d’écran montrant la Configuration SAML de base avec le lien Charger un fichier de métadonnées.](common/upload-metadata.png)

    b. Cliquez sur le **logo du dossier** pour sélectionner le fichier de métadonnées, puis cliquez sur **Charger**.

    ![Capture d’écran montrant une boîte de dialogue dans laquelle vous pouvez sélectionner et charger un fichier.](common/browse-upload-metadata.png)

    c. Une fois le fichier de métadonnées chargé, les valeurs **Identificateur** et **URL de réponse** sont automatiquement renseignées dans la section Configuration SAML de base.

    > [!Note]
    > Si les valeurs **Identificateur** et **URL de réponse** ne sont pas automatiquement renseignées, renseignez-les manuellement en fonction de vos besoins.

    d. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<SUBDOMAIN>.silkroad-eng.com/Authentication/`.

5. Dans la section **Configuration SAML de base**, si vous ne disposez pas d’un **fichier de métadonnées du fournisseur de services**, effectuez les étapes suivantes :

    a. Dans la zone **Identificateur**, tapez une URL en utilisant un des modèles suivants :

    | URL d’identificateur |
    |-----|
    |`https://<SUBDOMAIN>.silkroad-eng.com/Authentication/SP`|
    |`https://<SUBDOMAIN>.silkroad.com/Authentication/SP`|
    

    b. Dans la zone de texte **URL de réponse** , tapez une URL en utilisant un des modèles suivants :

    | URL de réponse |
    |-----|
    |`https://<SUBDOMAIN>.silkroad-eng.com/Authentication/`|
    |`https://<SUBDOMAIN>.silkroad.com/Authentication/`|

    c. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<SUBDOMAIN>.silkroad-eng.com/Authentication/`.

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL de réponse et l’URL de connexion réels. Pour obtenir ces valeurs, contactez [l’équipe de support technique SilkRoad Life Suite](https://www.silkroad.com/locations/). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

6. Sur la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le fichier **XML de métadonnées de fédération** en fonction des options définies selon vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

7. Dans la section **Configurer SilkRoad Life Suite**, copiez la ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à SilkRoad Life Suite.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **SilkRoad Life Suite**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-silkroad-life-suite-sso"></a>Configurer l’authentification unique SilkRoad Life Suite

1. Connectez-vous à votre site d’entreprise SilkRoad en tant qu’administrateur.

    > [!NOTE]
    > Pour obtenir l’accès à l’application d’authentification SilkRoad Life Suite pour configurer la fédération avec Microsoft Azure AD, contactez le support technique SilkRoad ou votre représentant SilkRoad Services.

1. Accédez à **Service Provider** (Fournisseur de services), puis cliquez sur **Federation Details** (Informations sur la fédération).

    ![Capture d’écran affichant l’élément Federation Details sélectionné dans Service Provider.](./media/silkroad-life-suite-tutorial/details.png)

1. Cliquez sur **Download Federation Metadata**(Télécharger les métadonnées de fédération), puis enregistrez le fichier de métadonnées sur votre ordinateur. Dans la section **Configuration SAML de base** du portail Azure, utilisez les métadonnées de fédération téléchargées pour le **fichier de métadonnées de fournisseur de services**.

    ![Capture d’écran affichant le lien de téléchargement des métadonnées de fédération.](./media/silkroad-life-suite-tutorial/metadata.png)

1. Dans votre application **SilkRoad**, cliquez sur **Authentication Sources** (Sources d’authentification).

    ![Capture d’écran montrant l’élément Authentication Sources sélectionné.](./media/silkroad-life-suite-tutorial/sources.png) 

1. Cliquez sur **Add Authentication Source**(Ajouter une source d’authentification).

    ![Capture d’écran montrant le lien d’ajout d’une source d’authentification.](./media/silkroad-life-suite-tutorial/add-source.png)

1. Dans la section **Add Authentication Source** (Ajouter une source d’authentification), procédez comme suit :

    ![Capture d’écran montrant la section Add Authentication Source, avec la sélection du bouton Create Identity Provider using File Data.](./media/silkroad-life-suite-tutorial/metadata-file.png)
  
    a. Sous **Option 2 - Metadata File** (Option 2 - Fichier de métadonnées), cliquez sur **Browse** (Parcourir) pour charger le fichier de métadonnées que vous avez téléchargé à partir du portail Azure.
  
    b. Cliquez sur **Create Identity Provider using File Data**.

1. Dans la section **Authentication Sources** (Sources d’authentification), cliquez sur **Edit** (Modifier).

    ![Capture d’écran affichant les sources d’authentification avec l’option Edit sélectionnée.](./media/silkroad-life-suite-tutorial/edit-source.png)

1. Dans la boîte de dialogue **Edit Authentication Source** (Modifier la source d’authentification), procédez comme suit :

    ![Capture d’écran montrant la boîte de dialogue Edit Authentication Source, dans laquelle vous pouvez entrer les valeurs décrites.](./media/silkroad-life-suite-tutorial/authentication.png)

    a. Pour l’option **Enabled**, sélectionnez **Yes**.

    b. Dans la zone de texte **EntityId**, collez la valeur de l’**identificateur Azure AD** que vous avez copiée dans le portail Azure.

    c. Dans la zone de texte **IdP Description** (Description IdP), entrez une description de votre configuration (par exemple : **Authentification unique Azure AD**).

    d. Dans la zone de texte **Metadata File** (Fichier de métadonnées), chargez le fichier de **métadonnées** que vous avez téléchargé à partir du portail Azure.
  
    e. Dans la zone de texte **IdP Name** (Nom IdP), entrez un nom spécifique de votre configuration (par exemple : *Azure SP*).
  
    f. Dans la zone de texte **Logout Service URL** (URL de service de déconnexion), collez l’**URL de déconnexion** que vous avez copiée dans le portail Azure.

    g. Dans la zone de texte **Sign-on service URL** (URL du service de connexion), collez l’**URL de connexion** que vous avez copiée à partir du portail Azure.

    h. Cliquez sur **Enregistrer**.

1. Désactivez toutes les autres sources d’authentification.

    ![Capture d’écran affichant la section Authentication Sources où vous pouvez désactiver d’autres sources. ](./media/silkroad-life-suite-tutorial/manage-source.png)

### <a name="create-silkroad-life-suite-test-user"></a>Créer un utilisateur de test SilkRoad Life Suite

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans SilkRoad Life Suite. Collaborez avec [l’équipe de support technique SilkRoad Life Suite](https://www.silkroad.com/locations/) pour ajouter des utilisateurs dans la plateforme SilkRoad Life Suite. Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Cette opération redirige vers l’URL de connexion SilkRoad Life Suite où vous pouvez lancer le processus de connexion. 

* Accédez directement à l’URL d’authentification unique de SilkRoad Life Suite pour lancer le flux de connexion.

* Vous pouvez utiliser Mes applications de Microsoft. Quand vous cliquez sur la vignette SilkRoad Life Suite dans Mes applications, vous êtes redirigé vers l’URL de connexion à SilkRoad Life Suite. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré SilkRoad Life Suite, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
