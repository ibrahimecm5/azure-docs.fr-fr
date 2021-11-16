---
title: 'Tutoriel : Intégration de l’authentification unique (SSO) Azure Active Directory avec cloudtamer.io | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et cloudtamer.io.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/26/2021
ms.author: jeedes
ms.openlocfilehash: 329121332449316f3582ee38c11ad843b25ab812
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132054122"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cloudtamerio"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory avec cloudtamer.io

Dans ce tutoriel, vous allez apprendre à intégrer cloudtamer.io à Azure Active Directory (Azure AD). Quand vous intégrez cloudtamer.io à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à cloudtamer.io.
* Permettre à vos utilisateurs de se connecter automatiquement à cloudtamer.io avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement cloudtamer.io pour lequel l’authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* cloudtamer.io prend en charge l'authentification unique lancée par **le fournisseur de services et le fournisseur d'identité**.
* cloudtamer.io prend en charge l'approvisionnement d'utilisateurs **juste-à-temps**.


## <a name="adding-cloudtamerio-from-the-gallery"></a>Ajout de cloudtamer.io à partir de la Galerie

Pour configurer l’intégration de cloudtamer.io à Azure AD, vous devez ajouter cloudtamer.io à partir de la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **cloudtamer.io** dans la zone de recherche.
1. Sélectionnez **cloudtamer.io** dans le panneau des résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.


## <a name="configure-and-test-azure-ad-sso-for-cloudtamerio"></a>Configurer et tester l’authentification unique Azure AD pour cloudtamer.io

Configurez et testez l’authentification unique Azure AD avec cloudtamer.io à l’aide d’un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur associé dans cloudtamer.io.

Pour configurer et tester l'authentification SSO Azure AD auprès de cloudtamer.io, suivez les étapes ci-dessous :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique cloudtamer.io](#configure-cloudtamerio-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test cloudtamer.io](#create-cloudtamerio-test-user)** pour avoir, dans cloudtamer.io, un équivalent de B.Simon lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.
1. **[Assertions de groupe](#group-assertions)** pour définir des assertions de groupe pour Azure AD et cloudtamer.io.

### <a name="begin-cloudtamerio-sso-configuration"></a>Commencer la configuration de l’authentification unique cloudtamer.io

1. Connectez-vous à votre site web cloudtamer.io en tant qu’administrateur.

1. Cliquez sur l’icône plus **+** dans le coin supérieur droit et sélectionnez **IDMS**.

    ![Capture d’écran de la création d’IDMS.](./media/cloudtamer-io-tutorial/idms-creation.png)

1. Sélectionnez **SAML 2.0** comme type d’IDMS.

1. Laissez cet écran ouvert et copiez les valeurs de cet écran dans la configuration d’Azure AD.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **cloudtamer.io**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de modification/stylet de **Configuration SAML de base** pour modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, si vous souhaitez configurer l’application en mode Initié par le **fournisseur d’identité**, entrez les valeurs pour les champs suivants :

    a. Dans la zone de texte **Identificateur**, collez **ÉMETTEUR DU FOURNISSEUR DE SERVICES (ID D’ENTITÉ)** de cloudtamer.io dans cette zone.

    b. Dans la zone de texte **URL de réponse**, collez l'**URL ACS du fournisseur de services** de cloudtamer.io dans cette zone.

1. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    Dans la zone de texte **URL de connexion**, collez **l’URL ACS du fournisseur de services** de cloudtamer.io dans cette zone.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **XML de métadonnées de fédération** et sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

1. Dans la section **Configurer cloudtamer.io**, copiez l’URL ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à cloudtamer.io.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **cloudtamer.io**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-cloudtamerio-sso"></a>Configurer l’authentification unique cloudtamer.io

1. Procédez comme suit dans la page **Ajouter IDMS** :

    ![Capture d’écran de l’ajout d’IDMS.](./media/cloudtamer-io-tutorial/configuration.png)

    a. Dans le **Nom IDMS**, donnez un nom que les utilisateurs reconnaîtront sur l’écran de connexion.

    b. Dans la zone de texte **Émetteur de fournisseur d’identité (ID d’entité)** , collez la valeur d'**identificateur** que vous avez copiée à partir du portail Azure.

    c. Ouvrez le fichier **XML de métadonnées de Fédération** à partir du portail Azure dans le Bloc-notes et collez le contenu dans la zone de texte **Métadonnées du fournisseur d'identité**.

    d. Copiez la valeur du champ **Émetteur de fournisseur de service (ID d'entité)** et collez-la dans la zone de texte **Identificateur** de la section Configuration SAML de base sur le portail Azure.

    e. Copiez la valeur du champ **URL de redirection du fournisseur de services**, puis collez-la dans la zone de texte **URL de réponse** de la section Configuration SAML de base du portail Azure.

    f. Sous Mappage d’assertion, entrez les valeurs suivantes :

    | Champ | Valeur |
    |-----------|-------|
    | Prénom | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` |
    | Nom | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` |
    | E-mail | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` |
    |  Nom d’utilisateur | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` |
    |

1. Cliquez sur **Créer IDMS**.


### <a name="create-cloudtamerio-test-user"></a>Créer un utilisateur de test cloudtamer.io

Dans cette section, un utilisateur nommé Britta Simon est créé dans cloudtamer.io. cloudtamer.io prend en charge le provisionnement d’utilisateurs juste-à-temps, qui est activé par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas encore d’utilisateur dans cloudtamer.io, un utilisateur est créé après l’authentification.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

#### <a name="sp-initiated"></a>Lancée par le fournisseur de services :

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion à cloudtamer.io, à partir de laquelle vous pouvez lancer le flux de connexion.  

* Accédez directement à l’URL de connexion à cloudtamer.io pour lancer le flux de connexion.

#### <a name="idp-initiated"></a>Lancée par le fournisseur d’identité :

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors automatiquement connecté à l’instance decloudtamer.io pour laquelle vous avez configuré l’authentification unique 

Vous pouvez aussi utiliser Mes applications de Microsoft pour tester l’application dans n’importe quel mode. Si, lorsque vous cliquez sur la vignette cloudtamer.io dans Mes applications, le mode Fournisseur de services est configuré, vous êtes redirigé vers la page de connexion de l'application pour lancer le flux de connexion ; s'il s'agit du mode Fournisseur d'identité, vous êtes automatiquement connecté à l'instance de cloudtamer.io pour laquelle vous avez configuré l'authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="group-assertions"></a>Assertions de groupe

Pour gérer facilement les autorisations de l’utilisateur cloudtamer.io à l’aide de groupes Azure Active Directory existants, effectuez les étapes suivantes :

### <a name="azure-ad-configuration"></a>Configuration Azure AD

1. Dans le portail Azure, accédez à **Azure Active Directory** > **Applications d’entreprise**.
1. Dans la liste, sélectionnez l’application d’entreprise pour cloudtamer.io.
1. Dans **Vue d’ensemble**, dans le menu de gauche, sélectionnez **Authentification unique**.
1. Dans **Authentification unique**, sous **Attributs utilisateur et revendications**, sélectionnez **Modifier**.
1. Sélectionnez **Ajouter une revendication de groupe**. 
   > [!NOTE]
   > Vous ne pouvez avoir qu’une seule revendication de groupe. Si cette option est désactivée, vous avez peut-être déjà défini une revendication de groupe.
1. Dans **Revendications de groupe**, sélectionnez les groupes qui doivent être retournés dans la revendication :
   - Si tous les groupes que vous prévoyez d’utiliser dans cloudtamer.io seront toujours attribués à cette application d’entreprise, sélectionnez **Groupes attribués à l’application**.
   - Si vous souhaitez que tous les groupes s’affichent (cette sélection peut générer un grand nombre d’assertions de groupe et peut être soumise à des limites), sélectionnez **Groupes attribués à l’application**.
1. Pour **Attribut source**, conservez la sélection par défaut **ID de groupe**.
1. Cochez la case **Personnaliser le nom de la revendication de groupe**.
1. Pour **Nom**, entrez **memberOf**.
1. Sélectionnez **Enregistrer** pour terminer la modification avec Azure AD.

### <a name="cloudtamerio-configuration"></a>Configuration de cloudtamer.io

1. Dans cloudtamer.io, accédez à **Users** > **Identity Management Systems**.
1. Sélectionnez l’IDMS que vous avez créé pour Azure AD.
1. Dans la page de la vue d’ensemble, sélectionnez l’onglet **User Group Associations**.
1. Pour chaque mappage de groupe d’utilisateurs souhaité, effectuez ces étapes :
   1. Sélectionnez **Add** > **Add New**.
   1. Dans la boîte de dialogue qui s’affiche :
      1. Pour **Nom**, entrez **memberOf**.
      1. Pour **Regex**, entrez l’ID de l’objet (à partir d’Azure AD) du groupe que vous souhaitez faire correspondre.
      1. Pour **User Group**, sélectionnez le groupe interne cloudtamer.io que vous souhaitez mapper au groupe dans **Regex**.
      1. Cochez la case **Update on Login**.
   1. Sélectionnez **Add** pour ajouter l’association de groupe.

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré cloudtamer.io, vous pouvez appliquer le contrôle de session, qui protège de l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
