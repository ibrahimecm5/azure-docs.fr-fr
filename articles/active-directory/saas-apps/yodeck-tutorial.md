---
title: 'Tutoriel : Intégration d’Azure Active Directory à Yodeck | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Yodeck.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/02/2021
ms.author: jeedes
ms.openlocfilehash: 56f02ba6231570988257d24e6f8ee60aa1beedf5
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132319948"
---
# <a name="tutorial-azure-active-directory-integration-with-yodeck"></a>Tutoriel : Intégration d’Azure Active Directory à Yodeck

Dans ce tutoriel, vous allez apprendre à intégrer Yodeck à Azure Active Directory (Azure AD). Quand vous intégrez Yodeck à Azure AD, vous pouvez :

* Contrôler, dans Azure AD, qui a accès à Yodeck.
* Permettre à vos utilisateurs de se connecter automatiquement à Yodeck avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD avec Yodeck, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un [compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Yodeck pour lequel l’authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Yodeck prend en charge l’authentification unique lancée par le **fournisseur de services** et le **fournisseur d’identité**.

> [!NOTE]
> L’identificateur de cette application étant une valeur de chaîne fixe, une seule instance peut être configurée dans un locataire.

## <a name="add-yodeck-from-the-gallery"></a>Ajouter Yodeck à partir de la galerie

Pour configurer l’intégration de Yodeck avec Azure AD, vous devez ajouter Yodeck, à partir de la galerie, à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Yodeck** dans la zone de recherche.
1. Sélectionnez **Yodeck** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-yodeck"></a>Configurer et tester l’authentification unique Azure AD pour Yodeck

Configurez et testez l’authentification unique Azure AD avec Yodeck à l’aide d’un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir une liaison entre un utilisateur Azure AD et l’utilisateur associé dans Yodeck.

Pour configurer et tester l’authentification unique Azure AD avec Yodeck, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Yodeck](#configure-yodeck-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test Yodeck](#create-yodeck-test-user)** pour avoir, dans Yodeck, un équivalent de B.Simon lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Sur le portail Azure, dans la page d’intégration de l’application **Yodeck**, recherchez la section **Gérer**, puis sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, si vous souhaitez configurer l’application en mode lancé par le **fournisseur d’identité**, effectuez l’étape suivante :

    Dans la zone de texte **Identificateur**, tapez l’URL : `https://app.yodeck.com/api/v1/account/metadata/`

5. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    Dans la zone de texte **URL de connexion**, tapez l’URL : `https://app.yodeck.com/login`

6. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur le bouton Copier pour copier l’**URL des métadonnées de fédération d’application**, puis enregistrez-la sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/copy-metadataurl.png)

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Yodeck.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Yodeck**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-yodeck-sso"></a>Configurer l’authentification unique Yodeck

1. Pour automatiser la configuration dans **Yodeck**, vous devez installer l’**extension de navigateur de connexion sécurisée à Mes applications** en cliquant sur **Installer l’extension**.

    ![Capture d’écran montrant le bouton Installer l’extension.](./media/target-process-tutorial/install_extension.png)

1. Une fois l’extension ajoutée au navigateur, le fait de cliquer sur **Configurer Yodeck** vous redirige vers l’application Yodeck. À partir de là, indiquez les informations d’identification de l’administrateur pour vous connecter à Yodeck. Cette extension de navigateur configure automatiquement l’application et automatise les étapes 3 à 5.

    ![Configuration](common/setup-sso.png)

    **Si vous souhaitez configurer l'application manuellement, procédez comme suit :**

1. Dans une autre fenêtre de navigateur web, connectez-vous au site de votre entreprise Yodeck en tant qu’administrateur.

1. Cliquez sur **User Settings** dans l’angle supérieur droit de la page et sélectionnez **Account Settings**.

    ![Capture d’écran montrant l’élément Account Settings sélectionné pour l’utilisateur.](./media/yodeck-tutorial/account.png)

1. Sélectionnez **SAML** et effectuez les opérations suivantes :

    ![Capture d’écran montrant l’onglet SAML où vous pouvez effectuer ces étapes.](./media/yodeck-tutorial/configure.png)

    a. Sélectionnez **Import from URL**.

    b. Dans la zone de texte **URL**, collez la valeur **URL des métadonnées de fédération de l’application** que vous avez copiée dans le portail Azure, puis cliquez sur **Import**.
    
    c. Une fois l’**URL des métadonnées de fédération de l’application** importée, les champs restants sont renseignés automatiquement.

    d. Cliquez sur **Enregistrer**.

### <a name="create-yodeck-test-user"></a>Créer un utilisateur de test Yodeck

Pour se connecter à Yodeck, les utilisateurs d’Azure AD doivent être approvisionnés dans Yodeck. Dans le cas de Yodeck, le provisionnement est une tâche manuelle.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous à votre site d’entreprise Yodeck en tant qu’administrateur.

2. Cliquez sur **User Settings** dans l’angle supérieur droit de la page et sélectionnez **Users**.

    ![Capture d’écran montrant l’élément Users sélectionné pour l’utilisateur.](./media/yodeck-tutorial/user.png)

3. Cliquez sur **+User** pour ouvrir l’onglet **User Details**.

    ![Capture d’écran montrant le bouton Users.](./media/yodeck-tutorial/user-details.png)

4. Dans la page de boîte de dialogue **Détails de l'utilisateur**, procédez comme suit :

    ![Capture d’écran montrant l’onglet User Details où vous pouvez effectuer ces étapes.](./media/yodeck-tutorial/user-page.png)

    a. Dans la zone de texte **First Name**, entrez le prénom de l’utilisateur, par exemple **Britta**.

    b. Dans la zone de texte **Last Name**, tapez le nom de l’utilisateur, par exemple **Simon**.

    c. Dans la zone de texte **Email** (E-mail), tapez l’adresse e-mail d’un utilisateur, par exemple, brittasimon@contoso.com.

    d. Sélectionnez l’option **Account Permissions** conforme aux exigences de votre organisation.
    
    e. Cliquez sur **Enregistrer**.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

#### <a name="sp-initiated"></a>Lancée par le fournisseur de services :

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion à Yodeck à partir de laquelle vous pouvez lancer le flux de connexion.  

* Accédez directement à l’URL de connexion à Yodeck et lancez le flux de connexion.

#### <a name="idp-initiated"></a>Lancée par le fournisseur d’identité :

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors automatiquement connecté à l’instance de Yodeck pour laquelle vous avez configuré l’authentification unique. 

Vous pouvez aussi utiliser Mes applications de Microsoft pour tester l’application dans n’importe quel mode. Si, quand vous cliquez sur la vignette Yodeck dans Mes applications, le mode Fournisseur de services est configuré, vous êtes redirigé vers la page de connexion de l’application pour lancer le flux de connexion ; s’il s’agit du mode Fournisseur d’identité, vous êtes automatiquement connecté à l’instance de Yodeck pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré Yodeck, vous pouvez appliquer le contrôle de session, qui protège en temps réel contre l’exfiltration et l’infiltration des données sensibles de votre organisation. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
