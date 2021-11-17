---
title: 'Tutoriel : Intégration de l’authentification unique Azure AD à LogicMonitor'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et LogicMonitor.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/14/2021
ms.author: jeedes
ms.openlocfilehash: 18d29099445f3d0af52ea6e0fa02efa25410bbdd
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132290815"
---
# <a name="tutorial-azure-ad-sso-integration-with-logicmonitor"></a>Tutoriel : Intégration de l’authentification unique Azure AD à LogicMonitor

Dans ce tutoriel, vous allez découvrir comment intégrer LogicMonitor à Azure Active Directory (Azure AD). Quand vous intégrez LogicMonitor à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à LogicMonitor.
* Permettre à vos utilisateurs de se connecter automatiquement à LogicMonitor avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement LogicMonitor pour lequel l’authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* LogicMonitor prend en charge l’authentification unique lancée par le **fournisseur de services**.

## <a name="add-logicmonitor-from-the-gallery"></a>Ajouter LogicMonitor à partir de la galerie

Pour configurer l’intégration de LogicMonitor à Azure AD, vous devez ajouter LogicMonitor, disponible dans la galerie, à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **LogicMonitor** dans la zone de recherche.
1. Sélectionnez **LogicMonitor** dans le panneau de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-logicmonitor"></a>Configurer et tester l’authentification unique Azure AD pour LogicMonitor

Configurez et testez l’authentification unique Azure AD avec LogicMonitor pour un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur associé dans LogicMonitor.

Pour configurer et tester l’authentification unique Azure AD avec LogicMonitor, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique LogicMonitor](#configure-logicmonitor-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test LogicMonitor](#create-logicmonitor-test-user)** pour avoir un équivalent de B.Simon dans LogicMonitor qui soit lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, dans la page d’intégration de l’application **LogicMonitor**, recherchez la section **Gérer**, puis sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    a. Dans la zone de texte **Identificateur (ID d’entité)** , saisissez une URL au format suivant : `https://<companyname>.logicmonitor.com`
    
    b. Dans la zone de texte **URL de réponse (URL Assertion Consumer Service)** , saisissez l’URL : `https://companyname.logicmonitor.com/santaba/saml/SSO/` 
  
    c. Dans la zone de texte **URL de connexion**, saisissez une URL au format suivant : `https://<companyname>.logicmonitor.com`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur et l’URL de connexion réels. Pour obtenir ces valeurs, contactez [l’équipe du support client LogicMonitor](https://www.logicmonitor.com/contact/). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

5. Sur la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le fichier **XML de métadonnées de fédération** en fonction des options définies selon vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

6. Dans la section **Configurer LogicMonitor**, copiez l’URL ou les URL appropriées, en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à LogicMonitor.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **LogicMonitor**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-logicmonitor-sso"></a>Configurer l’authentification unique LogicMonitor

1. Connectez-vous à votre site d'entreprise **LogicMonitor** en tant qu'administrateur.

2. Dans le menu situé en haut, cliquez sur **Settings**.

    ![Paramètres](./media/logicmonitor-tutorial/menu.png "Paramètres")

3. Dans la barre de navigation située à gauche, cliquez sur **Single Sign On** (Authentification unique).

    ![Authentification unique](./media/logicmonitor-tutorial/roles.png "Authentification unique")

4. Dans la section **Single Sign-on (SSO) settings**, procédez comme suit :

    ![Paramètres d’authentification unique](./media/logicmonitor-tutorial/metadata.png "Paramètres d’authentification unique")

    a. Sélectionnez **Enable Single Sign-on**.

    b. Comme **Default Role Assignment**, sélectionnez **readonly**.

    c. Ouvrez le fichier de métadonnées téléchargé dans le Bloc-notes, puis collez le contenu du fichier dans la zone de texte **Identity Provider Metadata**.

    d. Cliquez sur **Enregistrer les modifications**.

### <a name="create-logicmonitor-test-user"></a>Créer l’utilisateur de test LogicMonitor

Pour que les utilisateurs d’Azure AD puissent se connecter, ils doivent être approvisionnés dans l’application LogicMonitor à l’aide de leurs noms d’utilisateur Azure Active Directory.

**Pour configurer l'approvisionnement des utilisateurs, procédez comme suit :**

1. Connectez-vous au site d’entreprise LogicMonitor en tant qu’administrateur.

2. Dans le menu du haut, cliquez sur **Settings**, puis sur **Roles and Users**.

    ![Roles and Users](./media/logicmonitor-tutorial/user.png "Roles and Users")

3. Cliquez sur **Add**.

4. Dans la section **Add an account**, procédez comme suit :

    ![Ajouter un compte](./media/logicmonitor-tutorial/details.png "Ajouter un compte")

    a. Entrez les valeurs appropriées dans les champs **Nom d’utilisateur**, **Adresse de messagerie**, **Mot de passe** et **Confirmer le mot de passe** pour l’utilisateur Azure Active Directory.

    b. Sélectionnez **Rôles**, **Afficher les autorisations** et **État**.

    c. Cliquez sur **Envoyer**.

> [!NOTE]
> Vous pouvez utiliser n’importe quel outil ou API de création de compte utilisateur, fourni par LogicMonitor, pour approvisionner des comptes utilisateur Azure Active Directory.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion à LogicMonitor, d’où vous pouvez lancer le flux de connexion. 

* Accédez directement à l’URL de connexion à LogicMonitor pour lancer le flux de connexion.

* Vous pouvez utiliser Mes applications de Microsoft. Quand vous cliquez sur la vignette LogicMonitor dans Mes applications, vous devez être connecté automatiquement à l’application LogicMonitor pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré LogicMonitor, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
