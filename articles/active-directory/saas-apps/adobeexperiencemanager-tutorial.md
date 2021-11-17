---
title: 'Didacticiel : Intégration d’Azure Active Directory à Adobe Experience Manager | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Adobe Experience Manager.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/17/2021
ms.author: jeedes
ms.openlocfilehash: 6eb0bc2f41d202c5406b3f021c02da4494a76d04
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132321844"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-experience-manager"></a>Didacticiel : Intégration d’Azure Active Directory à Adobe Experience Manager

L’objectif de ce tutoriel est de vous apprendre à intégrer Adobe Experience Manager à Azure Active Directory (Azure AD). Quand vous intégrez Adobe Experience Manager à Azure AD, vous pouvez :

* Contrôler qui dans Azure AD a accès à Adobe Experience Manager.
* Permettre aux utilisateurs de se connecter automatiquement à Adobe Experience Manager avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement où l’authentification unique à Adobe Experience Manager est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* Adobe Experience Manager prend en charge l’authentification unique initiée par **le fournisseur de services et le fournisseur d’identité**

* Adobe Experience Manager prend en charge l’attribution d’utilisateurs **juste-à-temps**.

## <a name="adding-adobe-experience-manager-from-the-gallery"></a>Ajout d’Adobe Experience Manager à partir de la galerie

Pour configurer l’intégration d’Adobe Experience Manager à Azure AD, vous devez ajouter Adobe Experience Manager à votre liste d’applications SaaS gérées, à partir de la galerie.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Adobe Experience Manager** dans la zone de recherche.
1. Sélectionnez **Adobe Experience Manager** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.


## <a name="configure-and-test-azure-ad-sso-for-adobe-experience-manager"></a>Configurer et tester l’authentification unique Azure AD pour Adobe Experience Manager

Configurez et testez l’authentification unique (SSO) Azure AD pour Adobe Experience Manager à l’aide d’un utilisateur de test nommé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Adobe Experience Manager associé.

Pour configurer l’authentification unique Azure AD avec Adobe Experience Manager, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
2. **[Configurer l’authentification unique Adobe Experience Manager](#configure-adobe-experience-manager-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test Adobe Experience Manager](#create-adobe-experience-manager-test-user)** pour avoir un équivalent de Britta Simon dans Adobe Experience Manager, lié à la représentation Azure AD associée.
6. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **Adobe Experience Manager**, recherchez la section **Gérer**, puis sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, si vous souhaitez configurer l’application en mode Initié par le **fournisseur d’identité**, entrez les valeurs pour les champs suivants :

    a. Dans la zone de texte **Identificateur**, tapez une valeur unique que vous définissez sur votre serveur AEM également.

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://<AEM Server Url>/saml_login`

    > [!NOTE]
    > La valeur de l’URL de réponse n’est pas réelle. Mettez à jour la valeur avec l’URL de réponse réelle. Pour obtenir cette valeur, contactez l’[équipe du support technique Adobe Experience Manager](https://helpx.adobe.com/support/experience-manager.html). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

5. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    Dans la zone de texte **URL de connexion**, tapez l’URL de votre serveur Adobe Experience Manager.

6. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (Base64)** en fonction des options définies par rapport à vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

7. Dans la section **Configurer Adobe Experience Manager**, copiez la ou les URL appropriées correspondant à vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Adobe Experience Manager.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Adobe Experience Manager**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-adobe-experience-manager-sso"></a>Configurer l’authentification unique à Adobe Experience Manager

1. Ouvrez le portail d’administration **Adobe Experience Manager** dans une autre fenêtre de navigateur.

2. Sélectionnez **Paramètres** > **Sécurité** > **Utilisateurs**.

    ![Capture d’écran montrant la vignette Users dans Adobe Experience Manager.](./media/adobe-experience-manager-tutorial/user-1.png)

3. Sélectionnez **Administrateur** ou tout autre utilisateur approprié.

    ![Capture d’écran mettant en évidence l’utilisateur Administrator.](./media/adobe-experience-manager-tutorial/tutorial-admin-6.png)

4. Sélectionnez **Paramètres du compte** > **Gérer TrustStore**.

    ![Capture d’écran montrant Manage TrustStore sous Account settings.](./media/adobe-experience-manager-tutorial/manage-trust.png)

5. Sous **Ajouter un certificat à partir d’un fichier CER**, cliquez sur **Sélectionner un fichier de certificat**. Accédez au fichier de certificat que vous avez téléchargé à partir du portail Azure et sélectionnez-le.

    ![Capture d’écran mettant en évidence le bouton Select Certificate File.](./media/adobe-experience-manager-tutorial/user-2.png)

6. Le certificat est ajouté au TrustStore. Notez l’alias du certificat.

    ![Capture d’écran montrant que le certificat est ajouté au TrustStore.](./media/adobe-experience-manager-tutorial/tutorial-admin-7.png)

7. Sur la page **Utilisateurs**, sélectionnez **authentication-service**.

    ![Capture d’écran mettant en évidence authentication-service sur l’écran.](./media/adobe-experience-manager-tutorial/tutorial-admin-8.png)

8. Sélectionnez **Paramètres du compte** > **Create/Manage KeyStore** (Créer/gérer KeyStore). Créez KeyStore en fournissant un mot de passe.

    ![Capture d’écran mettant en évidence Manage KeyStore.](./media/adobe-experience-manager-tutorial/tutorial-admin-9.png)

9. Revenez à l’écran d’administrateur. Puis sélectionnez **Paramètres** > **Opérations** > **Console Web**.

    ![Capture d’écran mettant Web Console sous Operations dans la section Settings.](./media/adobe-experience-manager-tutorial/tutorial-admin-1.png)

    La page de configuration s’ouvre.

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/adobe-experience-manager-tutorial/tutorial-admin-2.png)

10. Rechercher **Adobe Granite SAML 2.0 Authentication Handler**. Puis cliquez sur l’icône **Ajouter**.

    ![Capture d’écran mettant en évidence Adobe Granite SAML 2.0 Authentication Handler.](./media/adobe-experience-manager-tutorial/tutorial-admin-3.png)

11. Sur cette page, effectuez les actions suivantes.

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/adobe-experience-manager-tutorial/tutorial-admin-4.png)

    a. Dans la zone **Chemin d’accès**, entrez **/** .

    b. Dans la zone **IDP URL** (URL IDP), entrez la valeur **URL de connexion** copiée à partir du portail Azure.

    c. Dans la zone **Alias du certificat IDP**, entrez la valeur **Alias du certificat** que vous avez ajoutée dans TrustStore.

    d. Dans la zone **Security Provided Entity ID** (ID d’entité fourni par la sécurité), entrez la valeur d’**Identificateur Azure AD** unique que vous avez configurée dans le portail Azure.

    e. Dans la zone **URL Assertion Consumer Service**, entrez la valeur **URL de réponse** que vous avez configurée dans le portail Azure.

    f. Dans la zone **Mot de passe de Key Store**, entrez le **mot de passe** que vous avez défini dans KeyStore.

    g. Dans la zone **ID d’attribut utilisateur**, entrez **l’ID de nom** ou un autre ID d’utilisateur pertinent.

    h. Sélectionnez **Créer automatiquement des utilisateurs CRX**.

    i. Dans la zone **Logout URL** (URL de déconnexion), entre la valeur unique **URL de déconnexion** obtenue à partir du portail Azure.

    j. Sélectionnez **Enregistrer**.

### <a name="create-adobe-experience-manager-test-user"></a>Créer un utilisateur de test Adobe Experience Manager

Dans cette section, vous créez un utilisateur appelé Britta Simon dans Adobe Experience Manager. Si vous avez sélectionné l’option **Autocreate CRX Users** (Créer automatiquement des utilisateurs CRX), les utilisateurs sont créés automatiquement après une authentification réussie.

Si vous souhaitez créer manuellement des utilisateurs, collaborez avec [l’équipe de support technique Adobe Experience Manager](https://helpx.adobe.com/support/experience-manager.html) pour ajouter les utilisateurs dans la plateforme Adobe Experience Manager.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

#### <a name="sp-initiated"></a>Lancée par le fournisseur de services :

* Cliquez sur **Tester cette application** dans le portail Azure. Cette opération effectue une redirection vers l’URL de connexion à Adobe Experience Manager, d’où vous pouvez lancer le flux de connexion.  

* Accédez directement à l’URL de connexion à Adobe Experience Manager, puis lancez le flux de connexion.

#### <a name="idp-initiated"></a>Lancée par le fournisseur d’identité :

* Cliquez sur **Tester cette application** dans le portail Azure : vous devez être connecté automatiquement à l’instance Adobe Experience Manager pour laquelle vous avez configuré l’authentification unique. 

Vous pouvez aussi utiliser Mes applications de Microsoft pour tester l’application dans n’importe quel mode. Si, quand vous cliquez sur la vignette Adobe Experience Manager dans Mes applications, le mode Fournisseur de services est configuré, vous êtes redirigé vers la page de connexion de l’application pour lancer le flux de connexion. S’il s’agit du mode Fournisseur d’identité, vous êtes automatiquement connecté à l’instance Adobe Experience Manager pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).


## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez configuré Adobe Experience Manager, vous pouvez appliquer le contrôle de session qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
