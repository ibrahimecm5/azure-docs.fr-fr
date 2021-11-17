---
title: 'Tutoriel : Intégration d’Azure Active Directory à Brightidea | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Brightidea.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/19/2021
ms.author: jeedes
ms.openlocfilehash: b2263122e9b009fe7b771f915614540fc7f60346
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132338836"
---
# <a name="tutorial-azure-active-directory-integration-with-brightidea"></a>Tutoriel : Intégration d’Azure Active Directory à Brightidea

Dans ce tutoriel, vous allez apprendre à intégrer Brightidea à Azure Active Directory (Azure AD). Lorsque vous intégrez Brightidea à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Brightidea.
* Permettre à vos utilisateurs de se connecter automatiquement à Brightidea avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement à Brightidea pour lequel l'authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Brightidea prend en charge l'authentification unique lancée par **le fournisseur de services et le fournisseur d'identité**.
* Brightidea prend en charge l'approvisionnement d'utilisateurs **juste-à-temps**.

> [!NOTE]
> L’identificateur de cette application étant une valeur de chaîne fixe, une seule instance peut être configurée dans un locataire.

## <a name="add-brightidea-from-the-gallery"></a>Ajouter Brightidea à partir de la galerie

Pour configurer l’intégration de Brightidea dans Azure AD, vous devez ajouter Brightidea depuis la galerie à votre liste d’applications SaaS managées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, entrez **Brightidea** dans la zone de recherche.
1. Sélectionnez **Brightidea** dans le volet de résultats, puis ajoutez l'application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-brightidea"></a>Configurer et tester l'authentification unique Azure AD pour Brightidea

Configurez et testez l'authentification unique Azure AD auprès de Brightidea à l'aide d'un utilisateur de test appelé **B.Simon**. Pour que l'authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l'utilisateur associé dans Brightidea.

Pour configurer et tester l'authentification unique Azure AD auprès de Brightidea, suivez les étapes ci-dessous :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l'authentification unique Brightidea](#configure-brightidea-sso)** pour configurer les paramètres de l'authentification unique côté application.
    1. **[Créer un utilisateur de test Brightidea](#create-brightidea-test-user)** pour avoir, dans Brightidea, un équivalent de B.Simon associé à sa représentation dans Azure AD.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Sur la page d’intégration de l'application **Brightidea** du portail Azure, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, si vous avez un **fichier de métadonnées de fournisseur de services** et voulez une configuration en mode initié par le **fournisseur d’identité** (IDP), effectuez les étapes suivantes :

    a. Cliquez sur **Charger un fichier de métadonnées**.

    ![Charger le fichier de métadonnées](common/upload-metadata.png)

    b. Cliquez sur le **logo du dossier** pour sélectionner le fichier de métadonnées, puis cliquez sur **Charger**.

    ![choisir le fichier de métadonnées](common/browse-upload-metadata.png)

    c. Une fois le fichier de métadonnées correctement chargé, les valeurs **Identificateur** et **URL de réponse** se renseignent automatiquement dans la zone de texte de la section Brightidea :

    > [!Note]
    > Si les valeurs **Identificateur** et **URL de réponse** ne sont pas automatiquement renseignées, renseignez-les manuellement en fonction de vos besoins.

5. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<SUBDOMAIN>.brightidea.com`

4. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le fichier **XML de métadonnées de fédération** en fonction des options définies selon vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

6. Dans la section **Configurer Brightidea**, copiez la ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez permettre à B.Simon d'utiliser l'authentification unique Azure en lui accordant l'accès à Brightidea.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Brightidea**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-brightidea-sso"></a>Configurer l'authentification unique pour Brightidea

1. Dans une autre fenêtre de navigateur web, connectez-vous à Brightidea en utilisant les informations d’identification d’administrateur.

2. Pour accéder à la fonctionnalité d’authentification unique dans votre système Brightidea, accédez à **Enterprise Setup** -> **Authentication Tab**. Vous voyez alors deux sous-onglets : Auth Selection et SAML Profiles.

    ![Capture d’écran montrant le site Brightidea avec l’onglet Authentication sélectionné.](./media/brightidea-tutorial/authentication.png)

3. Sélectionnez **Auth Selection**. Par défaut, seules deux méthodes standard apparaissent : Brightidea Login et Registration. Quand une méthode d’authentification unique est ajoutée, elle figure dans la liste.

    ![Capture d’écran montrant l’onglet Brightidea Authentication avec Auth Selection sélectionné.](./media/brightidea-tutorial/selection.png)

4. Sélectionnez **SAML Profiles** et effectuez les étapes suivantes :

    ![Capture d’écran montrant l’onglet Brightidea Authentication avec SAML Profiles sélectionné, qui fournit des options pour Download Metadata et Add New.](./media/brightidea-tutorial/profile.png)

    a. Cliquez sur **Télécharger les métadonnées** et chargez-les dans la section **Configuration SAML de base** du portail Azure.

    b. Cliquez sur le bouton **Ajouter nouveau** sous le **paramètre de fournisseur d’identité** et effectuez les étapes suivantes :

    ![Capture d’écran montrant Brightidea Identity Provider Setting, où vous entrez des informations.](./media/brightidea-tutorial/metadata.png)

   * Entrez le **nom du profil SAML**, comme `Azure Ad SSO`

   * Pour **charger les métadonnées**, cliquez pour choisir le fichier de métadonnées téléchargé à partir du portail Azure.

     > [!NOTE]
     > Après avoir chargé le fichier de métadonnées, les champs restants, **Service d’authentification unique, Émetteur du fournisseur d’identité, Télécharger la clé publique**, se renseignent automatiquement.

   * Dans la zone de texte **E-mail**, entrez la valeur `mail`.

   * Dans la zone de texte **Nom d’écran**, entrez la valeur `givenName`.

   * Cliquez sur **Enregistrer les modifications**. 

### <a name="create-brightidea-test-user"></a>Créer un utilisateur de test Brightidea

Dans cette section, un utilisateur appelé Britta Simon est créé dans Brightidea. Brightidea prend en charge le provisionnement d’utilisateurs juste-à-temps, option activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas encore d’utilisateur dans Brightidea, il en est créé un après l’authentification.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

#### <a name="sp-initiated"></a>Lancée par le fournisseur de services :

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l'URL de connexion à Brightidea, à partir de laquelle vous pouvez lancer le flux de connexion.  

* Accédez directement à l'URL de connexion à Brightidea pour lancer le flux de connexion.

#### <a name="idp-initiated"></a>Lancée par le fournisseur d’identité :

* Cliquez sur **Tester cette application** sur le portail Azure. Vous êtes alors automatiquement connecté à l'instance de Brightidea pour laquelle vous avez configuré l'authentification unique. 

Vous pouvez aussi utiliser Mes applications de Microsoft pour tester l’application dans n’importe quel mode. Si, lorsque vous cliquez sur la vignette Brightidea dans Mes applications, le mode Fournisseur de services est configuré, vous êtes redirigé vers la page de connexion de l'application pour lancer le flux de connexion ; s'il s'agit du mode Fournisseur d'identité, vous êtes automatiquement connecté à l'instance de Brightidea pour laquelle vous avez configuré l'authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré Brightidea, vous pouvez appliquer le contrôle de session, qui protège en temps réel contre l'exfiltration et l'infiltration des données sensibles de votre organisation. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
