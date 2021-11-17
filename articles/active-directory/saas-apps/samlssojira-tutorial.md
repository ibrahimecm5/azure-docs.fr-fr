---
title: 'Tutoriel : Intégration d’Azure Active Directory à SSO SAML pour Jira de Resolution GmbH | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et SSO SAML pour Jira de resolution GmbH.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/23/2021
ms.author: jeedes
ms.openlocfilehash: e5fae6e9f304364fecf9af9a55c235b5ae829828
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132299593"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-jira-by-resolution-gmbh"></a>Tutoriel : Intégration d’Azure Active Directory à SSO SAML pour Jira de resolution GmbH

Dans ce didacticiel, vous allez apprendre à intégrer SSO SAML pour Jira de resolution GmbH avec Azure Active Directory (Azure AD). Quand vous intégrez SSO SAML pour Jira de resolution GmbH à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à SSO SAML pour Jira de resolution GmbH.
* Permettre à vos utilisateurs de se connecter automatiquement à SSO SAML pour Jira de resolution GmbH avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Abonnement SSO SAML pour Jira de resolution GmbH (SSO) pour lequel l’authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* SSO SAML pour Jira de resolution GmbH prend en charge l’authentification unique lancée par le **fournisseur de services** et le **fournisseur d’identité**.

> [!NOTE]
> L’identificateur de cette application étant une valeur de chaîne fixe, une seule instance peut être configurée dans un locataire.

## <a name="add-saml-sso-for-jira-by-resolution-gmbh-from-the-gallery"></a>Ajouter SSO SAML pour Jira de resolution GmbH à partir de la galerie

Pour configurer l’intégration de SSO SAML pour Jira de resolution GmbH dans Azure AD, vous devez ajouter SSO SAML pour Jira de resolution GmbH à partir de la galerie dans votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **SSO SAML pour Jira de resolution GmbH** dans la zone de recherche.
1. Sélectionnez **SSO SAML pour Jira de resolution GmbH** dans le panneau de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-saml-sso-for-jira-by-resolution-gmbh"></a>Configurer et tester l’authentification unique Azure AD pour SSO SAML pour Jira de resolution GmbH

Configurez et testez l’authentification unique Azure AD avec SSO SAML pour Jira de resolution GmbH pour un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur associé dans SSO SAML pour Jira de resolution GmbH.

Pour configurer et tester l’authentification unique Azure AD avec SSO SAML pour Jira de resolution GmbH, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique SSO SAML pour Jira de resolution GmbH](#configure-saml-sso-for-jira-by-resolution-gmbh-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test de SSO SAML pour Jira de resolution GmbH](#create-saml-sso-for-jira-by-resolution-gmbh-test-user)** pour avoir un équivalent de B. Simon dans SSO SAML pour Jira de resolution GmbH qui soit lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Sur le portail Azure, dans la page d’intégration de l’application **SSO SAML pour Jira de resolution GmbH**, recherchez la section **Gérer**, puis sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, si vous souhaitez configurer l’application en mode lancé par le **fournisseur d’identité**, effectuez ensuite les étapes suivantes :

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<server-base-url>/plugins/servlet/samlsso`

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://<server-base-url>/plugins/servlet/samlsso`

    c. Cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes si vous souhaitez configurer l’application en mode lancé par le **fournisseur de services** :

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > Dans l’Identificateur, l’URL de réponse et l’URL de connexion, remplacez **\<server-base-url>** par l’URL de base de votre instance Jira. Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure. Si vous rencontrez un problème, contactez le [support technique de SSO SAML pour Jira de resolution GmbH](https://www.resolution.de/go/support).

4. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, téléchargez le fichier **XML de métadonnées de fédération** et enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

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

Dans cette section, vous activez B. Simon pour utiliser l’authentification unique Azure en accordant l’accès à SSO SAML pour Jira de resolution GmbH.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **SSO SAML pour Jira de resolution GmbH**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-saml-sso-for-jira-by-resolution-gmbh-sso"></a>Configurer l’authentification unique SSO SAML pour Jira de resolution GmbH 

1. Dans une autre fenêtre de navigateur web, connectez-vous à votre instance Jira comme administrateur.

2. Pointez sur la roue dentée à droite et cliquez sur **Gérer les applications**.
    
    ![Capture d’écran montrant une flèche pointant sur l’icône en forme de roue dentée et l’option « Gérer les applications » sélectionnée dans la liste déroulante](./media/samlssojira-tutorial/add-on-1.png)

3. Si vous êtes redirigé vers la page d’accès administrateur, entrez le **mot de passe**, puis cliquez sur le bouton **Confirmer**.

    ![Capture d’écran montrant la page d’accès administrateur](./media/samlssojira-tutorial/add-on-2.png)

4. En principe, Jira vous redirige vers la Place de marché Atlassian. Si ce n’est pas le cas, cliquez sur **Find new apps** (Rechercher les nouvelles applications) dans le panneau de gauche. Recherchez **SAML Single Sign On (SSO) for JIRA** (SSO SAML pour Jira), puis cliquez sur le bouton **Install** (Installer) pour installer le plug-in SAML.

    ![Capture d’écran montrant la page « Atlassian Marketplace for JIRA » avec une flèche pointant sur le bouton « Install » pour l’application « SAML Single Sign On (SSO) JIRA, SAML/SSO »](./media/samlssojira-tutorial/store.png)

5. L’installation du plug-in démarre. À la fin de l’installation, cliquez sur le bouton **Fermer**.

    ![Capture d’écran montrant la boîte de dialogue « Installing »](./media/samlssojira-tutorial/store-2.png)

    ![Capture d’écran montrant la boîte de dialogue « Installed and ready to go! » avec le bouton « Close » sélectionné](./media/samlssojira-tutorial/store-3.png)

6. Cliquez ensuite sur **Gérer**.

    ![Capture d’écran montrant l’application « SAML Single Sign On (SSO) Jira, SAML/SSO » avec le bouton « Manage » sélectionné](./media/samlssojira-tutorial/store-4.png)
    
7. Cliquez maintenant sur **Configurer** pour configurer le nouveau plug-in installé.

    ![Capture d’écran montrant la page « Manage apps », avec le bouton « Configure » sélectionné pour l’application « SAML SingleSignOn for Jira »](./media/samlssojira-tutorial/store-5.png)

8. Dans l’Assistant **SAML SingleSignOn Plugin Configuration** (Configuration du plug-in d’authentification unique SAML), cliquez sur **Add new IdP** (Ajouter un nouveau fournisseur d’identité) pour configurer Azure AD comme nouveau fournisseur d’identité.

    ![Capture d’écran montrant la page « Welcome », avec le bouton « Add new IdP » sélectionné](./media/samlssojira-tutorial/add-on-4.png) 

9. Dans la page **Choose your SAML Identity Provider** (Choisissez votre fournisseur d’identité SAML), effectuez les étapes suivantes :

    ![Capture d’écran montrant la page « Choose your SAML Identity Provider » avec les zones de texte « IdP Type » et « Name » mises en évidence et le bouton « Next » sélectionné](./media/samlssojira-tutorial/identity-provider.png)
 
    a. Définissez **Azure AD** comme type de fournisseurs d’identité.
    
    b. Entrez le **Nom** du fournisseur d’identité (par exemple, Azure AD).
    
    c. Ajoutez une **Description** facultative du fournisseur d’identité (par exemple, Azure AD).
    
    d. Cliquez sur **Suivant**.
    
10. Sur la page **Configuration du fournisseur d’identité**, cliquez sur **Suivant**.
 
    ![Capture d’écran montrant la page « Identity provider configuration »](./media/samlssojira-tutorial/configuration.png)

11. Dans la page **Import SAML IdP Metadata** (Importer les métadonnées du fournisseur d’identité SAML), effectuez les actions suivantes :

    ![Capture d’écran montrant la page « Import SAML IdP Metadata » avec l’action « Select Metadata XML File » sélectionnée](./media/samlssojira-tutorial/metadata.png)

    a. Cliquez sur le bouton **Select Metadata XML File** (Sélectionner le fichier XML de métadonnées), puis sélectionnez le fichier **XML de métadonnées de fédération** que vous avez téléchargé précédemment.

    b. Cliquez sur le bouton **Import**.
     
    c. Patientez quelques instants jusqu’à la fin de l’importation.  
     
    d. Cliquez sur le bouton **Suivant**.
    
12. Dans la page **User ID attribute and transformation** (Attribut d’ID d’utilisateur et transformation), cliquez sur le bouton **Next** (Suivant).

    ![Capture d’écran montrant la page « User ID attribute and transformation » avec le bouton « Next » sélectionné](./media/samlssojira-tutorial/transformation.png)
    
13. Dans la page **User creation and update** (Création et mise à jour de l’utilisateur), cliquez sur **Save & Next** (Enregistrer et suivant) pour enregistrer les paramètres.
    
    ![Capture d’écran montrant la page « User creation and update » avec le bouton « Save & Next » sélectionné](./media/samlssojira-tutorial/update.png)
    
14. Dans la page **Test your settings** (Tester vos paramètres), cliquez sur **Skip test & configure manually** (ignorer le test et configurer manuellement) afin d’ignorer le test de l’utilisateur pour l’instant. Cette opération, qui requiert certains paramètres dans le portail Azure, sera effectuée à la section suivante.
    
    ![Capture d’écran montrant la page « Test your settings » avec le bouton « Skip test & configure manually » sélectionné](./media/samlssojira-tutorial/test.png)
    
15. Cliquez sur **OK** pour ignorer l’avertissement.
    
    ![Capture d’écran montrant la boîte de dialogue d’avertissement avec le bouton « OK » sélectionné](./media/samlssojira-tutorial/warning.png)

### <a name="create-saml-sso-for-jira-by-resolution-gmbh-test-user"></a>Créer un utilisateur de test pour SSO SAML pour Jira de resolution GmbH

Pour permettre à des utilisateurs Azure AD de se connecter à SSO SAML pour Jira de resolution GmbH, vous devez les provisionner dans SSO SAML pour Jira de resolution GmbH. Dans le cadre de ce tutoriel, vous devez effectuer le provisionnement manuellement. Toutefois, il existe d’autres modèles de provisionnement disponibles pour le plug-in SSO SAML de resolution, comme le provisionnement **Just In Time** (Juste-à-temps). Consultez la documentation de resolution sur [SAML SSO by resolution GmbH](https://wiki.resolution.de/doc/saml-sso/latest/all). Si vous avez une question à ce sujet, contactez le [support technique de resolution](https://www.resolution.de/go/support).

**Pour provisionner manuellement un compte d’utilisateur, effectuez les étapes suivantes :**

1. Connectez-vous à l’instance Jira comme administrateur.

2. Pointez sur la roue dentée et sélectionnez **User management** (Gestion des utilisateurs).

   ![Capture d’écran montrant une flèche pointant sur l’icône en forme de roue dentée avec l’option « User management » sélectionnée dans la liste déroulante](./media/samlssojira-tutorial/user-1.png)

3. Si vous êtes redirigé vers la page d’accès administrateur, entrez le **mot de passe**, puis cliquez sur le bouton **Confirmer**.

    ![Capture d’écran montrant la page « accès administrateur » avec la zone de texte « mot de passe » mise en surbrillance.](./media/samlssojira-tutorial/user-2.png) 

4. Sous l’onglet **User management** (Gestion des utilisateurs), cliquez sur **Create user** (Créer un utilisateur).

    ![Capture d’écran montrant l’onglet « User management » avec le bouton « Create user » sélectionné.](./media/samlssojira-tutorial/user-3-new.png) 

5. Dans la boîte de dialogue **Create New User** (Créer un utilisateur), effectuez les étapes suivantes. L’utilisateur que vous créez doit être strictement identique à son équivalent dans Azure AD :

    ![Ajouter un employé](./media/samlssojira-tutorial/user-4-new.png) 

    a. Dans la zone de texte **Email Address**, entrez l’adresse e-mail de l’utilisateur : <b>BrittaSimon@contoso.com</b>.

    b. Dans la zone de texte **Full Name** (Nom complet), tapez le nom complet de l’utilisateur : **Britta Simon**.

    c. Dans la zone de texte **Username** (Nom d’utilisateur), tapez l’adresse e-mail de l’utilisateur : <b>BrittaSimon@contoso.com</b>. 

    d. Dans la zone de texte **Password** (Mot de passe), tapez le mot de passe de l’utilisateur.

    e. Cliquez sur **Create user** (Créer l’utilisateur) pour terminer la création de l’utilisateur.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

#### <a name="sp-initiated"></a>Lancée par le fournisseur de services :

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion à SSO SAML pour Jira de resolution GmbH, d’où vous pouvez lancer le flux de connexion.  

* Accédez directement à l’URL de connexion à SSO SAML pour Jira de resolution GmbH pour lancer le flux de connexion.

#### <a name="idp-initiated"></a>Lancée par le fournisseur d’identité :

* Cliquez sur **Tester cette application** sur le portail Azure. Vous êtes alors automatiquement connecté à l’application SSO SAML pour Jira de resolution GmbH pour laquelle vous avez configuré l’authentification unique. 

Vous pouvez aussi utiliser Mes applications de Microsoft pour tester l’application dans n’importe quel mode. Si, quand vous cliquez sur la vignette SSO SAML pour Jira de resolution GmbH dans Mes applications, le mode Fournisseur de services est configuré, vous êtes redirigé vers la page de connexion de l’application pour lancer le flux de connexion ; s’il s’agit du mode Fournisseur d’identité, vous êtes automatiquement connecté à l’application SSO SAML pour Jira de resolution GmbH pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="enable-sso-redirection-for-jira"></a>Activer la redirection SSO pour Jira

Comme indiqué dans la section précédente, il existe actuellement deux façons de déclencher l’authentification unique : soit à partir du **portail Azure**, soit par le biais d’un **lien spécifique vers votre instance Jira**. Le plug-in SSO SAML de resolution GmbH vous permet aussi de déclencher l’authentification unique simplement **en accédant à une URL pointant vers votre instance Jira**.

En fait, tous les utilisateurs qui accèdent à Jira sont redirigés vers l’authentification unique après l’activation d’une option dans le plug-in.

Pour activer la redirection vers l’authentification unique, effectuez les étapes suivantes dans **votre instance Jira** :

1. Accédez à la page de configuration du plug-in SSO SAML dans Jira.
1. Cliquez sur **Redirection** dans le panneau de gauche.

   ![Capture d’écran partielle de la page de configuration du plug-in Jira SAML SingleSignOn mettant en évidence le lien de redirection dans le volet de navigation gauche.](./media/samlssojira-tutorial/configure-1.png)

1. Cochez **Enable SSO Redirect** (Activer la redirection vers l’authentification unique).

   ![Capture d’écran partielle de la page de configuration du plug-in JIRA SAML SingleSignOn mettant en évidence la case « Enable SSO Redirect » (Activer la redirection de l’authentification unique) cochée.](./media/samlssojira-tutorial/configure-2.png) 

1. Choisissez le bouton **Save Settings** (Enregistrer les paramètres) dans l’angle supérieur droit.

Après avoir activé l’option, vous pouvez toujours accéder à l’invite du nom d’utilisateur/mot de passe, si l’option **Enable nosso** (Activer nosso) est cochée, à partir de `https://<server-base-url>/login.jsp?nosso`. Comme toujours, remplacez **\<server-base-url>** par votre URL de base.

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré SSO SAML pour Jira de resolution GmbH, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
