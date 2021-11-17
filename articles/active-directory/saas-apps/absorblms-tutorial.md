---
title: 'Didacticiel : Intégration d’Azure Active Directory à Absorb LMS | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Absorb LMS.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/05/2021
ms.author: jeedes
ms.openlocfilehash: 39341f9ca95ec0206b795e02cebc9fb32ffc2767
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132305355"
---
# <a name="tutorial-azure-active-directory-integration-with-absorb-lms"></a>Didacticiel : Intégration d’Azure Active Directory avec Absorb LMS

Dans ce tutoriel, vous allez apprendre à intégrer Absorb LMS à Azure Active Directory (Azure AD). Quand vous intégrez Absorb LMS à Azure AD, vous pouvez :

* Contrôler qui dans Azure AD a accès à Absorb LMS.
* Permettre aux utilisateurs de se connecter automatiquement à Absorb LMS avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD avec Absorb LMS, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un [compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Absorb LMS pour lequel l’authentification unique est activée

> [!NOTE]
> Cette intégration peut également être utilisée à partir de l’environnement cloud US Government Azure AD. Cette application est disponible dans la Galerie d’applications cloud US Government Azure AD et peut être configurée de la même façon que dans le cloud public.

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Absorb LMS prend en charge l’authentification unique initiée par **IDP**

> [!NOTE]
> L’identificateur de cette application étant une valeur de chaîne fixe, une seule instance peut être configurée dans un locataire.

## <a name="add-absorb-lms-from-the-gallery"></a>Ajouter Absorb LMS à partir de la galerie

Pour configurer l’intégration d’Absorb LMS avec Azure AD, vous devez ajouter Absorb LMS à partir de la galerie dans votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Absorb LMS** dans la zone de recherche.
1. Sélectionnez **Absorb LMS** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-absorb-lms"></a>Configurer et tester l’authentification unique Azure AD pour Absorb LMS

Configurez et testez l’authentification unique Azure AD auprès d’Absorb LMS à l’aide d’un utilisateur de test nommé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Absorb LMS associé.

Pour configurer et tester l’authentification unique Azure AD auprès d’Absorb LMS, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Absorb LMS](#configure-absorb-lms-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur test Absorb LMS](#create-absorb-lms-test-user)** pour avoir, dans Absorb LMS, un équivalent de B.Simon lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **Absorb LMS**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Sur la page **Configurer l’authentification unique avec SAML**, cliquez sur le bouton **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    Avec **Absorb 5 - UI**, utilisez la configuration suivante :

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<SUBDOMAIN>.myabsorb.com/account/saml`

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://<SUBDOMAIN>.myabsorb.com/account/saml`

    Avec **Absorb 5 - New Learner Experience**, utilisez la configuration suivante :

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<SUBDOMAIN>.myabsorb.com/api/rest/v2/authentication/saml`

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://<SUBDOMAIN>.myabsorb.com/api/rest/v2/authentication/saml`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur et l’URL de réponse réels. Pour obtenir ces valeurs, contactez l’[équipe de support technique Absorb LMS](https://support.absorblms.com/hc/). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

5. La capture d’écran suivante montre la liste des attributs par défaut, où **nameidentifier** est mappé avec **user.userprincipalname**.

    ![image](common/edit-attribute.png)

6. Sur la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le fichier **XML de métadonnées de fédération** en fonction des options définies selon vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

7. Dans la section **Configurer Absorb LMS**, copiez la ou les URL appropriées, selon vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en accordant l’accès à Absorb LMS.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Absorb LMS**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-absorb-lms-sso"></a>Configurer l’authentification unique Absorb LMS

1. Dans une nouvelle fenêtre de navigateur web, connectez-vous au site de votre entreprise Absorb LMS en tant qu’administrateur.

2. Sélectionnez le bouton **Compte** en haut à droite.

    ![Bouton Compte](./media/absorblms-tutorial/account.png)

3. Dans le volet Compte, sélectionnez **Paramètres du portail**.

    ![Lien Paramètres du portail](./media/absorblms-tutorial/portal.png)

4. Sélectionnez l’onglet **Manage SSO Settings** (Gérer les paramètres d’authentification unique).

    ![L’onglet Utilisateurs](./media/absorblms-tutorial/sso.png)

5. Dans la page **Manage Single Sign-On Settings** (Gérer les paramètres d’authentification unique), procédez comme suit :

    ![Page Configuration d’authentification unique](./media/absorblms-tutorial/settings.png)

    a. Dans la zone de texte **Nom**, entrez un nom, par exemple Azure AD Marketplace SSO.

    b. Sélectionnez **SAML** comme **Méthode**.

    c. Dans le bloc-notes, ouvrez le certificat que vous avez téléchargé à partir du portail Azure. Supprimez les balises **-----BEGIN CERTIFICATE-----** et **-----END CERTIFICATE-----**. Puis, dans la zone **Clé**, collez le reste du contenu.

    d. Dans la zone **Mode**, sélectionnez **Identity Provider Initiated** (Initiée par le fournisseur d’identité).

    e. Dans la zone **Propriété ID** , sélectionnez l’attribut que vous avez configuré comme identificateur d’utilisateur dans Azure AD. Par exemple, si *nameidentifier* est sélectionné dans Azure AD, sélectionnez **Nom d’utilisateur**.

    f. Sélectionnez **Sha256** comme **Type de signature**.

    g. Dans la zone **Login URL** (URL de connexion), collez l’**URL d’accès utilisateur** indiquée dans la page **Propriétés** du portail Azure.

    h. Dans **Logout URL** (URL de déconnexion), collez la valeur de l’**URL de déconnexion** que vous avez copiée depuis la fenêtre **Configurer l’authentification** du portail Azure.

    i. Faites basculer **Automatically Redirect** (Redirection automatique) sur **Activé**.

6. Sélectionnez **Enregistrer**.

    ![Activer/désactiver Autoriser uniquement la connexion SSO](./media/absorblms-tutorial/save.png)

### <a name="create-absorb-lms-test-user"></a>Créer un utilisateur de test Absorb LMS

Les utilisateurs Azure AD désirant se connecter à Absorb LMS doivent être configurés dans Absorb LMS. Dans le cas d’Absorb LMS, l’approvisionnement est une tâche manuelle.

**Pour configurer l'approvisionnement des utilisateurs, procédez comme suit :**

1. Connectez-vous à votre site d’entreprise Absorb LMS en tant qu’administrateur.

2. Dans le volet **Utilisateurs**, sélectionnez **Utilisateurs**.

    ![Lien Utilisateurs](./media/absorblms-tutorial/users.png)

3. Sélectionnez l’onglet **Utilisateur**.

    ![Liste déroulante Ajouter un nouveau](./media/absorblms-tutorial/add.png)

4. Dans la page **Ajouter un utilisateur**, procédez comme suit :

    ![Page Ajouter un utilisateur](./media/absorblms-tutorial/user.png)

    a. Dans la zone **Prénom**, tapez le prénom, par exemple **Britta**.

    b. Dans la zone **Nom**, tapez le nom de famille, par exemple **Simon**.

    c. Dans la zone **Nom d’utilisateur**, tapez le nom complet, par exemple **Britta Simon**.

    d. Dans la zone **Mot de passe**, entrez le mot de passe utilisateur.

    e. Dans la zone **Confirmer le mot de passe**, saisissez de nouveau le mot de passe.

    f. Configurez le réglage **Est actif** sur **Actif**.

5. Sélectionnez **Enregistrer**.

    ![Activer/désactiver Autoriser uniquement la connexion SSO](./media/absorblms-tutorial/save.png)

    > [!NOTE]
    > Par défaut, l’approvisionnement d’utilisateurs n’est pas activé dans l’authentification unique. Si le client souhaite activer cette fonctionnalité, il doit la définir comme indiqué dans [cette](https://support.absorblms.com/hc/en-us/articles/360014083294-Incoming-SAML-2-0-SSO-Account-Provisioning) documentation. Notez également que l’approvisionnement d’utilisateurs est uniquement disponible avec **Absorb 5 - New Learner Experience** avec l’URL ACS-`https://company.myabsorb.com/api/rest/v2/authentication/saml`

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes.

* Cliquez sur Tester cette application dans le portail Azure ; vous devez être connecté automatiquement à l’instance d’Absorb LMS pour laquelle vous avez configuré l’authentification unique.

* Vous pouvez utiliser Mes applications de Microsoft. Le fait de cliquer sur la vignette Absorb LMS dans le panneau d’accès doit vous connecter automatiquement à l’application Absorb LMS pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré Absorb LMS, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
