---
title: 'Didacticiel : Intégration d’Azure Active Directory à Autotask Workplace | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Autotask Workplace.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/02/2021
ms.author: jeedes
ms.openlocfilehash: 75e76d224ac70398471a56c76168f41088074103
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132296596"
---
# <a name="tutorial-azure-active-directory-integration-with-autotask-workplace"></a>Didacticiel : Intégration d’Azure Active Directory à Autotask Workplace

Dans ce tutoriel, vous allez découvrir comment intégrer Autotask Workplace à Azure Active Directory (Azure AD). Quand vous intégrez Autotask Workplace à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Autotask Workplace.
* Permettre aux utilisateurs de se connecter automatiquement à Autotask Workplace avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Autotask Workplace pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Autotask Workplace prend en charge l’authentification unique lancée par le **fournisseur de services et le fournisseur d’identité**

## <a name="add-autotask-workplace-from-the-gallery"></a>Ajout der Autotask Workplace à partir de la galerie

Pour configurer l’intégration d’Autotask Workplace à Azure AD, vous devez ajouter Autotask Workplace à partir de la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Autotask Workplace** dans la zone de recherche.
1. Sélectionnez **Autotask Workplace** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-autotask-workplace"></a>Configurer et tester l’authentification unique Azure AD pour Autotask Workplace

Configurez et testez l’authentification unique Azure AD avec Autotask Workplace à l’aide d’un utilisateur de test nommé **B. Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Autotask Workplace associé.

Pour configurer et tester l’authentification unique Azure AD avec Autotask Workplace, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Autotask Workplace](#configure-autotask-workplace-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test Autotask Workplace](#create-autotask-workplace-test-user)** pour avoir un équivalent de B.Simon dans Autotask Workplace, associé à sa représentation dans Azure AD.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, dans la page d’intégration de l’application **Autotask Workplace**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

4. À la section **Configuration SAML de base**, si vous souhaitez configurer l’application en mode initié par **IDP**, suivez les étapes ci-dessous :

    ![Capture d’écran montrant Basic SAML Configuration, où vous pouvez entrer Identifier, et Reply URL, et sélectionner Save.](common/idp-intiated.png)

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<subdomain>.awp.autotask.net/singlesignon/saml/metadata`

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://<subdomain>.awp.autotask.net/singlesignon/saml/SSO`

5. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    ![Capture d’écran montrant Set additional URLs, où vous pouvez entrer une URL de connexion.](common/metadata-upload-additional-signon.png)

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<subdomain>.awp.autotask.net/loginsso`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL de réponse et l’URL de connexion réels. Pour obtenir ces valeurs, contactez [l’équipe du support Autotask Workplace](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

6. Sur la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le fichier **XML de métadonnées de fédération** en fonction des options définies selon vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

7. Dans la section **Configurer Autotask Workplace**, copiez la ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Autotask Workplace.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Autotask Workplace**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-autotask-workplace-sso"></a>Configurer l’authentification unique Autotask Workplace

1. Dans une autre fenêtre de navigateur web, connectez-vous à Workplace Online à l’aide des informations d’identification administrateur.

    > [!Note]
    > Lorsque vous configurez le fournisseur d’identité, vous devez indiquer un sous-domaine. Pour confirmer le sous-domaine, connectez-vous à Workplace Online. Une fois connecté, notez le sous-domaine dans l’URL. Le sous-domaine est la partie entre le « https:// » et «.awp.autotask.net/ » et doit être us, eu, ca ou au.

2. Accédez à **Configuration** > **Authentification unique** et procédez comme suit :

    ![Configuration de l’authentification unique Autotask](./media/autotaskworkplace-tutorial/configuration-1.png)

    a. Sélectionnez l’option **Fichier XML de métadonnées** et chargez le fichier **XML de métadonnées de fédération** téléchargé à partir du portail Azure.

    b. Cliquez sur **ACTIVER SSO**.

    ![Configuration de l’approbation d’authentification unique Autotask](./media/autotaskworkplace-tutorial/configuration-2.png)

    c. Cochez la case **I confirm this information is correct and I trust this IdP** (Je confirme l’exactitude de ces informations et je fais confiance à ce fournisseur d’identité).

    d. Cliquez sur **APPROUVER**.

> [!Note]
> Si vous avez besoin d’aide lors de la configuration d’Autotask Workplace, consultez [cette page](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm) pour obtenir de l’aide avec votre compte Workplace.

### <a name="create-autotask-workplace-test-user"></a>Créer un utilisateur de test Autotask Workplace

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans Autotask Workplace. Pour ajouter des utilisateurs dans la plateforme Autotask Workplace, contactez [l’équipe du support Autotask Workplace](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm).

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

#### <a name="sp-initiated"></a>Lancée par le fournisseur de services :

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion à Autotask Workplace, où vous pouvez lancer le flux de connexion.  

* Accédez directement à l’URL de connexion à Autotask Workplace pour lancer le flux de connexion.

#### <a name="idp-initiated"></a>Lancée par le fournisseur d’identité :

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors connecté automatiquement à l’instance d’Autotask Workplace pour laquelle vous avez configuré l’authentification unique. 

Vous pouvez aussi utiliser Mes applications de Microsoft pour tester l’application dans n’importe quel mode. Si, quand vous cliquez sur la vignette Autotask Workplace dans Mes applications, le mode Fournisseur de services est configuré, vous êtes redirigé vers la page de connexion de l’application pour lancer le flux de connexion ; s’il s’agit du mode Fournisseur d’identité, vous êtes automatiquement connecté à l’instance d’Autotask Workplace pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré Autotask Workplace, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
