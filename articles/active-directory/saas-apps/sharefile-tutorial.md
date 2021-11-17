---
title: 'Tutoriel : Intégration de l’authentification unique Azure AD à Citrix ShareFile'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Citrix ShareFile.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/13/2021
ms.author: jeedes
ms.openlocfilehash: 1b59f3da3f2bd56a895a0e6b97d6052622e6b51d
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132333464"
---
# <a name="tutorial-azure-ad-sso-integration-with-citrix-sharefile"></a>Tutoriel : Intégration de l’authentification unique Azure AD à Citrix ShareFile

Dans ce tutoriel, vous allez apprendre à intégrer Citrix ShareFile à Azure Active Directory (Azure AD). Quand vous intégrez Citrix ShareFile à Azure AD, vous pouvez :

* Contrôler qui a accès à Citrix ShareFile.
* Permettre à vos utilisateurs de se connecter automatiquement à Citrix ShareFile avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Citrix ShareFile pour lequel l’authentification unique est activée.

> [!NOTE]
> Cette intégration peut également être utilisée à partir de l’environnement cloud US Government Azure AD. Cette application est disponible dans la Galerie d’applications cloud US Government Azure AD et peut être configurée de la même façon que dans le cloud public.

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Citrix ShareFile prend en charge l’authentification unique lancée par le **fournisseur de services**.

## <a name="add-citrix-sharefile-from-the-gallery"></a>Ajout de Citrix ShareFile à partir de la galerie

Pour configurer l’intégration de Citrix ShareFile avec Azure AD, vous devez ajouter Citrix ShareFile, disponible dans la galerie, à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Citrix ShareFile** dans la zone de recherche.
1. Sélectionnez **Citrix ShareFile** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-citrix-sharefile"></a>Configurer et tester l’authentification unique Azure AD pour Citrix ShareFile

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Citrix ShareFile avec un utilisateur de test appelé **Britta Simon**.
Pour que l’authentification unique fonctionne, une relation entre l’utilisateur Azure AD et l’utilisateur Citrix ShareFile associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec Citrix ShareFile, procédez comme suit :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
2. **[Configurer l’authentification unique Citrix ShareFile](#configure-citrix-sharefile-sso)** pour configurer les paramètres d’authentification unique côté application.
    1. **[Créer un utilisateur de test Citrix ShareFile](#create-citrix-sharefile-test-user)** pour avoir un équivalent de Britta Simon dans Citrix ShareFile, lié à la représentation Azure AD associée.
3. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **Citrix ShareFile**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes : 

    a. Dans la zone de texte **Identificateur (ID d’entité)** , tapez une URL en utilisant l’un des modèles suivants :

    | **Identificateur** |
    |--------|
    | `https://<tenant-name>.sharefile.com` |
    | `https://<tenant-name>.sharefile.com/saml/info` |
    | `https://<tenant-name>.sharefile1.com/saml/info` |
    | `https://<tenant-name>.sharefile1.eu/saml/info` |
    | `https://<tenant-name>.sharefile.eu/saml/info` |

    b. Dans la zone de texte **URL de réponse** , tapez une URL en respectant l’un des formats suivants :
    
    | **URL de réponse** |
    |-------|
    | `https://<tenant-name>.sharefile.com/saml/acs` |
    | `https://<tenant-name>.sharefile.eu/saml/<URL path>` |
    | `https://<tenant-name>.sharefile.com/saml/<URL path>` |

    c. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<tenant-name>.sharefile.com/saml/login`.

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL de réponse et l’URL de connexion réels. Pour obtenir ces valeurs, contactez [l’équipe du support Citrix ShareFile](https://www.citrix.co.in/products/citrix-content-collaboration/support.html). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

4. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (Base64)** en fonction des options définies par rapport à vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

6. Dans la section **Configurer Citrix ShareFile**, copiez la ou les URL appropriées correspondant à vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Citrix ShareFile.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Citrix ShareFile**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-citrix-sharefile-sso"></a>Configurer l’authentification unique Citrix ShareFile

1. Pour automatiser la configuration dans **Citrix ShareFile**, vous devez installer l’**extension de navigateur My Apps Secure Sign-in** en cliquant sur **Installer l’extension**.

    ![Extension My apps](common/install-myappssecure-extension.png)

2. Une fois l’extension ajoutée au navigateur, cliquez sur **Configurer Citrix ShareFile** pour être dirigé vers l’application Citrix ShareFile. À partir de là, indiquez les informations d’identification de l’administrateur pour vous connecter à Citrix ShareFile. Cette extension de navigateur configure automatiquement l’application pour vous et automatise les étapes 3 à 7.

    ![Configuration](common/setup-sso.png)

3. Si vous souhaitez configurer Citrix ShareFile manuellement, dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Citrix ShareFile en tant qu’administrateur.

1. Dans le tableau de bord (**Dashboard**), cliquez sur **Settings** (Paramètres), puis sélectionnez **Admin Settings** (Paramètres d’administration).

    ![Administration](./media/sharefile-tutorial/settings.png)

1. Dans les paramètres d’administration, accédez à **Security** -> **Login & Security Policy** (Stratégie de connexion et de sécurité).
   
    ![Administration des comptes](./media/sharefile-tutorial/settings-security.png "Administration des comptes")

1. Dans la page de boîte de dialogue **Authentification unique/Configuration de SAML 2.0** sous **Paramètres de base**, procédez comme suit :
   
    ![Authentification unique](./media/sharefile-tutorial/saml-configuration.png "Authentification unique")
   
    a. Sélectionnez **OUI** dans **Activer SAML**.

    b. Copiez la valeur **Émetteur / ID d’entité ShareFile** et collez-la dans la zone **URL d’identificateur** de la boîte de dialogue **Configuration SAML de base** du portail Azure.
    
    c. Dans la zone de texte **Your IDP Issuer/Entity ID** (Votre émetteur IDP/identificateur d’entité), collez **l’Identificateur Azure AD** que vous avez copié sur le Portail Azure.

    d. Cliquez sur **Change** (Modifier) en regard du champ **X.509 Certificate** (Certificat X.509), puis chargez le certificat que vous avez téléchargé à partir du portail Azure.
    
    e. Dans la zone de texte **Login URL** (URL de connexion), collez la valeur **URL de connexion** que vous avez copiée dans le Portail Azure.
    
    f. Dans la zone de texte **Logout URL** (URL de déconnexion), collez la valeur **URL de déconnexion** que vous avez copiée dans le Portail Azure.

    g. Sous **Optional Settings** (Paramètres facultatifs), dans **SP-Initiated Auth Context** (Contexte d’authentification initié par le fournisseur de services), choisissez **User Name and Password** (Nom d’utilisateur et mot de passe) et **Exact**.

5. Cliquez sur **Enregistrer**.

## <a name="create-citrix-sharefile-test-user"></a>Créer un utilisateur de test Citrix ShareFile

1. Connectez-vous à votre client **Citrix ShareFile**.

2. Cliquez sur **People** -> **Manage Users Home** -> **Create New Users** -> **Create Employee**.
   
    ![Créer un employé](./media/sharefile-tutorial/create-user.png "Créer un employé")

3. Dans la section **Basic Information**, procédez comme suit :
   
    ![Informations de base](./media/sharefile-tutorial/user-form.png "Informations de base")
   
    a. Dans la zone de texte **First Name**, entrez le **prénom** de l’utilisateur. Ici, il s’agit de **Britta**.
   
    b.  Dans la zone de texte **Last Name**, entrez le **nom** de l’utilisateur. Ici, il s’agit de **Simon**.
   
    c. Dans la zone de texte **Email Address** (Adresse e-mail), tapez l’adresse e-mail du compte de Britta Simon au format suivant : **brittasimon\@contoso.com**.

4. Cliquez sur **Add User**.
  
    >[!NOTE]
    >Le titulaire du compte Azure AD va recevoir un e-mail contenant un lien lui permettant de confirmer son compte avant que celui-ci ne soit activé. Vous pouvez utiliser n’importe quel autre outil de création de compte d’utilisateur Citrix ShareFile ou toute autre API fournie par Citrix ShareFile pour approvisionner des comptes d’utilisateur Azure AD.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes.

* Cliquez sur **Tester cette application** dans le portail Azure. Cette opération redirige vers l’URL de connexion Citrix ShareFile, où vous pouvez lancer le processus de connexion.

* Accédez directement à l’URL de connexion Citrix ShareFile pour lancer le processus de connexion.

* Vous pouvez utiliser Mes applications de Microsoft. Un clic sur la vignette Citrix ShareFile dans Mes applications vous redirige vers l’URL de connexion Citrix ShareFile. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré Citrix ShareFile, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
