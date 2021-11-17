---
title: 'Tutoriel : Intégration d’Azure Active Directory à Egnyte | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Egnyte.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/05/2021
ms.author: jeedes
ms.openlocfilehash: a2ac510b28a81ffc086daf9f97e4bbe86ffb69e4
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132314323"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-egnyte"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory à Egnyte

Dans ce tutoriel, vous allez découvrir comment intégrer Egnyte à Azure Active Directory (Azure AD). Quand vous intégrez Egnyte à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Egnyte.
* Permettre à vos utilisateurs de se connecter automatiquement à Egnyte avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Egnyte pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Egnyte prend en charge l’authentification unique lancée par le **fournisseur de services**.

> [!NOTE]
> L’identificateur de cette application étant une valeur de chaîne fixe, une seule instance peut être configurée dans un locataire.

## <a name="add-egnyte-from-the-gallery"></a>Ajouter Egnyte à partir de la galerie

Pour configurer l’intégration d’Egnyte avec Azure AD, vous devez ajouter Egnyte à votre liste d’applications SaaS gérées à partir de la galerie.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Egnyte** dans la zone de recherche.
1. Sélectionnez **Egnyte** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-egnyte"></a>Configurer et tester l’authentification unique Azure AD pour Egnyte

Configurez et testez l’authentification unique Azure AD avec Egnyte à l’aide d’un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Powerschool Performance Matters associé.

Pour configurer et tester l’authentification unique Azure AD avec Powerschool Performance Matters, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Egnyte](#configure-egnyte-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test Egnyte](#create-egnyte-test-user)** pour obtenir un équivalent de B.Simon dans Egnyte qui soit lié à la représentation Azure AD associée.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **Egnyte**, recherchez la section **Gérer**, puis sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<companyname>.egnyte.com`.

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://<companyname>.egnyte.com/samlconsumer/AzureAD`
    
    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour les valeurs avec l’URL de connexion et l’URL de réponse réelles. Pour obtenir cette valeur, contactez [l’équipe du support technique Egnyte](https://www.egnyte.com/corp/contact_egnyte.html). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

4. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (Base64)** en fonction des options définies par rapport à vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

5. Dans la section **Configurer Egnyte**, copiez l’URL appropriée en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Egnyte.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Egnyte**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-egnyte-sso"></a>Configurer l’authentification unique Egnyte

1. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Egnyte en tant qu’administrateur.

2. Cliquez sur **Settings**.
   
    ![Paramètre 1](./media/egnyte-tutorial/settings-tab.png "Paramètres")

3. Dans le menu, cliquez sur **Paramètres**.

    ![Menu 1](./media/egnyte-tutorial/menu-tab.png "Menu")

4. Cliquez sur l'onglet **Configuration**, puis cliquez sur **Sécurité**.

    ![Sécurité](./media/egnyte-tutorial/configuration.png "Sécurité")

5. Dans la section **Authentification unique**, procédez comme suit :

    ![Authentification unique](./media/egnyte-tutorial/authentication.png "Authentification unique")   
    
    1. Pour **Authentification unique**, sélectionnez **SAML 2.0**.

    1. Pour **Fournisseur d'identité**, sélectionnez **AzureAD**.

    1. Collez la valeur **URL de connexion** copiée à partir du portail Azure dans la zone de texte **Identity provider login URL** (URL de connexion du fournisseur d’identité).

    1. Collez la valeur **Identificateur Azure AD** copiée à partir du portail Azure dans la zone de texte **Identity provider entity ID** (ID d’entité du fournisseur d’identité).

    1. Ouvrez dans le Bloc-notes votre certificat codé en base 64 téléchargé à partir du portail Azure, copiez son contenu dans le Presse-papiers, puis collez-le dans la zone de texte **Certificat de fournisseur d’identité**.

    1. Pour **Mappage d'utilisateur par défaut**, sélectionnez **Adresse de messagerie**.

    1. Pour **Utiliser une valeur d'émetteur spécifique du domaine**, sélectionnez **Désactivé**.

    1. Cliquez sur **Enregistrer**.

### <a name="create-egnyte-test-user"></a>Créer un utilisateur de test Egnyte

Pour pouvoir se connecter à Egnyte, les utilisateurs d’Azure AD doivent être provisionnés dans Egnyte. Dans le cas de Egnyte, l’approvisionnement est une tâche manuelle.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous à votre site d’entreprise **Egnyte** en tant qu’administrateur.

2. Accédez à **Settings \> Users & Groups**.

3. Cliquez sur **Ajouter un nouvel utilisateur**, puis sélectionnez le type d'utilisateur à ajouter.
   
    ![Utilisateurs](./media/egnyte-tutorial/add-user.png "Utilisateurs")

4. Dans la section **New Power User** (Nouvel utilisateur avancé), effectuez les étapes suivantes :
    
    ![Nouvel utilisateur standard](./media/egnyte-tutorial/new-user.png "New Standard User")   

    a. Dans la zone de texte **E-mail**, tapez l’adresse e-mail de l’utilisateur, par exemple **brittasimon\@contoso.com**.

    b. Dans la zone de texte **Username** (Nom d’utilisateur), entrez le nom d’un utilisateur, par exemple **Brittasimon**.

    c. Pour **Single sign-on** (Authentification unique), sélectionnez **Authentication Type** (Type d’authentification).
   
    d. Cliquez sur **Enregistrer**.
    
    >[!NOTE]
    >Le titulaire du compte Azure Active Directory recevra une notification par courrier électronique.
    >

>[!NOTE]
>Vous pouvez utiliser tout autre outil ou API de création de compte d’utilisateur Egnyte fourni par Egnyte pour provisionner des comptes d’utilisateur Azure AD.
>

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion à Egnyte, d’où vous pouvez lancer le flux de connexion. 

* Accédez directement à l’URL de connexion à Egnyte et lancez le flux de connexion.

* Vous pouvez utiliser Mes applications de Microsoft. Quand vous cliquez sur la vignette Egnyte dans Mes applications, vous êtes redirigé vers l’URL de connexion à Egnyte. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré Egnyte, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
