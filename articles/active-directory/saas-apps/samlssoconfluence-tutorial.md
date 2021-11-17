---
title: 'Tutoriel : Intégration d’Azure Active Directory à SSO SAML pour Confluence de resolution GmbH | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et SSO SAML pour Confluence de resolution GmbH.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/04/2021
ms.author: jeedes
ms.openlocfilehash: b3e3a610b7bafe8670c3dbbb4b05c1fde1fff0e5
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132333691"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-confluence-by-resolution-gmbh"></a>Tutoriel : Intégration d’Azure Active Directory avec SSO SAML pour Confluence de resolution GmbH

Dans ce tutoriel, vous allez apprendre à intégrer SAML SSO for Confluence by resolution GmbH à Azure Active Directory (Azure AD). Quand vous intégrez SAML SSO for Confluence by resolution GmbH à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à SAML SSO for Confluence by resolution GmbH.
* Permettre à vos utilisateurs de se connecter automatiquement à SAML SSO for Confluence by resolution GmbH avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Abonnement SAML SSO for Confluence by resolution GmbH pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* SSO SAML pour Confluence de resolution GmbH prend en charge l’authentification unique initiée par le **fournisseur de services et le fournisseur d’identité**

## <a name="add-saml-sso-for-confluence-by-resolution-gmbh-from-the-gallery"></a>Ajouter SAML SSO for Confluence by resolution GmbH à partir de la galerie

Pour configurer l’intégration de SSO SAML pour Confluence de resolution GmbH dans Azure AD, vous devez ajouter SSO SAML pour Confluence de resolution GmbH à partir de la galerie dans votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **SAML SSO for Confluence by resolution GmbH** dans la zone de recherche.
1. Sélectionnez **SAML SSO for Confluence by resolution GmbH** dans le panneau de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-saml-sso-for-confluence-by-resolution-gmbh"></a>Configurer et tester l’authentification unique Azure AD pour SAML SSO for Confluence by resolution GmbH

Configurez et testez l’authentification unique Azure AD avec SAML SSO for Confluence by resolution GmbH pour un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur associé dans SAML SSO for Confluence by resolution GmbH.

Pour configurer et tester l’authentification unique Azure AD avec SAML SSO for Confluence by resolution GmbH, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
2. **[Configurer l’authentification unique SAML SSO for Confluence by resolution GmbH](#configure-saml-sso-for-confluence-by-resolution-gmbh-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test de SSO SAML pour Confluence de resolution GmbH](#create-saml-sso-for-confluence-by-resolution-gmbh-test-user)** : pour avoir un équivalent de Britta Simon dans SSO SAML pour Confluence de resolution GmbH qui soit lié à la représentation Azure AD de l’utilisateur.
6. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, dans la page d’intégration de l’application **SAML SSO for Confluence by resolution GmbH**, recherchez la section **Gérer**, puis sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, si vous souhaitez configurer l’application en mode Initié par le **fournisseur d’identité**, entrez les valeurs pour les champs suivants :

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<server-base-url>/plugins/servlet/samlsso`

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://<server-base-url>/plugins/servlet/samlsso`

1. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL de réponse et l’URL de connexion réels. Pour obtenir ces valeurs, contactez l’[équipe de support de SSO SAML pour Confluence de resolution GmbH](https://www.resolution.de/go/support). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

4. Sur la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le fichier **XML de métadonnées de fédération** en fonction des options définies selon vos besoins, puis enregistrez-le sur votre ordinateur.

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

Dans cette section, vous allez permettre à B.Simon d’utiliser l’authentification unique Azure en lui accordant l’accès à SAML SSO for Confluence by resolution GmbH.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **SSO SAML pour Confluence de resolution GmbH**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.


## <a name="configure-saml-sso-for-confluence-by-resolution-gmbh-sso"></a>Configurer l’authentification unique SAML SSO for Confluence by resolution GmbH

1. Dans une autre fenêtre de navigateur web, connectez-vous à votre **portail d’administration de SSO SAML pour Confluence de resolution GmbH** en tant qu’administrateur.

2. Pointez sur le roue dentée, puis cliquez sur **Modules complémentaires**.
    
    ![Capture d’écran montrant l’icône « Roue dentée » sélectionnée, et l’option « Add-ons » sélectionnée dans la liste déroulante.](./media/saml-sso-confluence-tutorial/add-on-1.png)

3. Vous êtes redirigé vers la page d’accès administrateur. Entrez le mot de passe, puis cliquez sur le bouton **Confirmer**.

    ![Capture d’écran montrant la page « Administrator Access » avec le bouton « Confirm » sélectionné](./media/saml-sso-confluence-tutorial/add-on-2.png)

4. Sous **ATLASSIAN MARKETPLACE**, cliquez sur **Find new add-ons** (Trouver de nouveaux modules complémentaires). 

    ![Capture d’écran montrant l’onglet « ATTLASSIAN MARKETPLACE » avec l’option « Find new add-ons » sélectionnée](./media/saml-sso-confluence-tutorial/add-on.png)

5. Recherchez **SAML Single Sign On (SSO) for Confluence**, puis cliquez sur le bouton **Install** pour installer le nouveau plug-in SAML.

    ![Capture d’écran montrant la page « Find new add-ons », avec « SAML Single Sign On (SSO) for Confluence » dans la zone de recherche et le bouton « Install » sélectionné](./media/saml-sso-confluence-tutorial/add-on-7.png)

6. L’installation du plug-in démarre. Cliquez sur **Fermer**.

    ![Capture d’écran montrant la boîte de dialogue « Installing »](./media/saml-sso-confluence-tutorial/add-on-8.png)

    ![Capture d’écran montrant la boîte de dialogue « Installed and ready to go! » avec l’action « Close » sélectionnée.](./media/saml-sso-confluence-tutorial/add-on-9.png)

7.  Cliquez sur **Gérer**.

    ![Capture d’écran montrant la page de l’application « SAML Single Sign On (SSO) for Confluence » avec le bouton « Manage » sélectionné](./media/saml-sso-confluence-tutorial/add-on-10.png)
    
8. Cliquez sur **Configurer** pour configurer le nouveau plug-in.

    ![Capture d’écran montrant la page « Manage » avec le bouton « Configure » sélectionné](./media/saml-sso-confluence-tutorial/add-on-11.png)

9. Ce nouveau plug-in figure également sous l’onglet **UTILISATEURS ET SÉCURITÉ**.

    ![Capture d’écran montrant l’onglet « USERS & SECURITY » avec « SAML SingleSignOn » sélectionné](./media/saml-sso-confluence-tutorial/add-on-3.png)
    
10. Dans la page **SAML SingleSignOn Plugin Configuration** (Configuration du plug-in d’authentification unique SAML), cliquez sur le bouton **Add new IdP** (Ajouter un nouveau fournisseur d’identité) pour configurer les paramètres du fournisseur d’identité.

    ![Capture d’écran montrant la page « SAML SingleSignOn Plugin Configuration », avec le bouton « Add new IdP » sélectionné](./media/saml-sso-confluence-tutorial/add-on-4.png)

11. Dans la page **Choose your SAML Identity Provider** (Choisissez votre fournisseur d’identité SAML), effectuez les actions suivantes :

    ![Capture d’écran montrant la page « Choose your SAML Identity Provider » avec les zones de texte « IdP Type », « Name » et « Description » mises en évidence](./media/saml-sso-confluence-tutorial/add-on-5-a.png)
 
    a. Définissez **Azure AD** comme type de fournisseurs d’identité.
    
    b. Ajoutez le **Nom** du fournisseur d’identité (p. ex. Azure AD).
    
    c. Ajoutez la **Description** du fournisseur d’identité (p. ex. Azure AD).
    
    d. Cliquez sur **Suivant**.
    
12. Dans la page **Identity provider configuration** (Configuration du fournisseur d’identité), cliquez sur le bouton **Next** (Suivant).

    ![Capture d’écran montrant la page « Identity provider configuration » avec le bouton « Next » sélectionné](./media/saml-sso-confluence-tutorial/add-on-5-b.png)

13. Dans la page **Import SAML IdP Metadata** (Importer les métadonnées du fournisseur d’identité SAML), effectuez les actions suivantes :

    ![Capture d’écran montrant la page « Import SAML IdP Metadata » avec les boutons « Import », « Load File » et « Next » sélectionnés](./media/saml-sso-confluence-tutorial/add-on-5-c.png)

    a. Cliquez sur le bouton **Load File** (Charger le fichier) et choisissez le fichier XML de métadonnées que vous avez téléchargé à l’étape 5.

    b. Cliquez sur le bouton **Import** (Importer).
    
    c. Patientez quelques instants jusqu’à la fin de l’importation.
    
    d. Cliquez sur le bouton **Next** (Suivant).
    
14. Dans la page **User ID attribute and transformation** (Attribut d’ID d’utilisateur et transformation), cliquez sur le bouton **Next** (Suivant).

    ![Capture d’écran montrant la page « User ID attribute and transformation » avec le bouton « Next » sélectionné](./media/saml-sso-confluence-tutorial/add-on-5-d.png)
    
15. Dans la page **User creation and update** (Création et mise à jour de l’utilisateur), cliquez sur **Save & Next** (Enregistrer et suivant) pour enregistrer les paramètres.   
    
    ![Capture d’écran montrant la page « User creation and update » avec le bouton « Save & Next » sélectionné](./media/saml-sso-confluence-tutorial/add-on-6-a.png)
    
16. Dans la page **Test your settings** (Tester vos paramètres), cliquez sur **Skip test & configure manually** (ignorer le test et configurer manuellement) afin d’ignorer le test de l’utilisateur pour l’instant. Cette opération, qui requiert certains paramètres dans le portail Azure, sera effectuée à la section suivante. 
    
    ![Capture d’écran montrant la page « Test your settings » avec le bouton « Skip test & configure manually » sélectionné](./media/saml-sso-confluence-tutorial/add-on-6-b.png)
    
17. Dans la boîte de dialogue **Skipping the test means...** (Ignorer le test signifie...), cliquez sur **OK**.
    
    ![Configure Single Sign-On](./media/saml-sso-confluence-tutorial/add-on-6-c.png)


### <a name="create-saml-sso-for-confluence-by-resolution-gmbh-test-user"></a>Créer un utilisateur de test pour SSO SAML pour Confluence de resolution GmbH

Pour permettre à des utilisateurs d’Azure AD de se connecter à SSO SAML pour Confluence de resolution GmbH, vous devez configurer ceux-ci dans SSO SAML pour Confluence de resolution GmbH.  
Dans SSO SAML pour Confluence de resolution GmbH, cette configuration est une tâche manuelle.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous à votre site d’entreprise SSO SAML pour Confluence de resolution GmbH en tant qu’administrateur.

2. Pointez sur la roue dentée, puis cliquez sur **Gestion des utilisateurs**.

    ![Capture d’écran montrant l’icône « Roue dentée » mise en évidence et l’option « User management » sélectionnée dans le menu](./media/saml-sso-confluence-tutorial/user-1.png) 

3. Dans la section Utilisateurs, cliquez sur l’onglet **Add users** (Ajouter des utilisateurs). Dans la page de boîte de dialogue **Add a User** (Ajouter un utilisateur), procédez comme suit :

    ![Ajouter un employé](./media/saml-sso-confluence-tutorial/user-2.png) 

    a. Dans la zone de texte **Username** (Nom d’utilisateur), tapez le nom d’un utilisateur, par exemple, Britta Simon.

    b. Dans la zone de texte **Full Name** (Nom complet), tapez le nom complet d’un utilisateur, par exemple, Britta Simon.

    c. Dans la zone de texte **Email** (E-mail), tapez l’adresse e-mail d’un utilisateur, par exemple, Brittasimon@contoso.com.

    d. Dans la zone de texte **Password** (Mot de passe), tapez le mot de passe de Britta Simon.

    e. Cliquez sur **Confirm Password** (Confirmer le mot de passe), puis entrez de nouveau le mot de passe.
    
    f. Cliquez sur le bouton **Ajouter**.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

#### <a name="sp-initiated"></a>Lancée par le fournisseur de services :

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion à SAML SSO for Confluence by resolution GmbH, d’où vous pouvez lancer le flux de connexion.  

* Accédez directement à l’URL de connexion à SAML SSO for Confluence by resolution GmbH pour lancer le flux de connexion.

#### <a name="idp-initiated"></a>Lancée par le fournisseur d’identité :

* Cliquez sur **Tester cette application** sur le portail Azure. Vous êtes alors automatiquement connecté à l’application SAML SSO for Confluence by resolution GmbH pour laquelle vous avez configuré l’authentification unique. 

Vous pouvez aussi utiliser Mes applications de Microsoft pour tester l’application dans n’importe quel mode. Si, quand vous cliquez sur la vignette SAML SSO for Confluence by resolution GmbH dans Mes applications, le mode Fournisseur de services est configuré, vous êtes redirigé vers la page de connexion de l’application pour lancer le flux de connexion ; s’il s’agit du mode Fournisseur d’identité, vous êtes automatiquement connecté à l’application SAML SSO for Confluence by resolution GmbH pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré SAML SSO for Confluence by resolution GmbH, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
