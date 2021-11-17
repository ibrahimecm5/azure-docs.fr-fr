---
title: 'Didacticiel : Intégration d’Azure Active Directory avec Kantega SSO pour JIRA | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Kantega SSO pour JIRA.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/27/2021
ms.author: jeedes
ms.openlocfilehash: cb972e9ac5b04f4f27c3717fc7b99a4c2d3eff8e
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132313749"
---
# <a name="tutorial-azure-active-directory-integration-with-kantega-sso-for-jira"></a>Didacticiel : Intégration d’Azure Active Directory avec Kantega SSO pour JIRA

Dans ce tutoriel, vous allez apprendre à intégrer Kantega SSO pour JIRA avec Azure Active Directory (Azure AD). Quand vous intégrez Kantega SSO pour JIRA à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Kantega SSO pour JIRA.
* Permettre aux utilisateurs de se connecter automatiquement à Kantega SSO pour JIRA avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD avec Kantega SSO pour JIRA, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un [compte gratuit](https://azure.microsoft.com/free/).

* Abonnement Kantega SSO pour JIRA pour lequel l’authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Kantega SSO pour JIRA prend en charge l’authentification unique lancée par le **fournisseur de services et le fournisseur d’identité**.

## <a name="add-kantega-sso-for-jira-from-the-gallery"></a>Ajouter Kantega SSO pour JIRA à partir de la galerie

Pour configurer l’intégration de Kantega SSO pour JIRA dans Azure AD, vous devez ajouter Kantega SSO pour JIRA à partir de la galerie à la liste des applications SaaS managées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Kantega SSO pour JIRA** dans la zone de recherche.
1. Sélectionnez **Kantega SSO pour JIRA** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-kantega-sso-for-jira"></a>Configurer et tester l’authentification unique Azure AD pour Kantega SSO pour JIRA

Configurez et testez l’authentification unique Azure AD avec Kantega SSO pour JIRA pour un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Kantega SSO pour JIRA associé.

Pour configurer et tester l’authentification unique Azure AD avec Kantega SSO pour JIRA, procédez comme suit :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Kantega SSO pour JIRA](#configure-kantega-sso-for-jira-sso)** pour configurer les paramètres d’authentification unique côté application.
    1. **[Créer un utilisateur de test Kantega SSO pour JIRA](#create-kantega-sso-for-jira-test-user)** pour avoir un équivalent de B.Simon dans Kantega SSO pour JIRA qui soit lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **Kantega SSO pour JIRA**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, si vous souhaitez configurer l’application en mode lancé par le **fournisseur d’identité**, effectuez les étapes suivantes :

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<UNIQUE_ID>/login`

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<UNIQUE_ID>/login`

5. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<UNIQUE_ID>/login`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL de réponse et l’URL de connexion réels. Ces valeurs sont reçues durant la configuration du plug-in JIRA qui est décrite plus loin dans le didacticiel.

6. Sur la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le fichier **XML de métadonnées de fédération** en fonction des options définies selon vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

7. Dans la section **Configurer Kantega SSO pour JIRA**, copiez la ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Kantega SSO pour JIRA.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Kantega SSO pour JIRA**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-kantega-sso-for-jira-sso"></a>Configurer l’authentification unique Kantega SSO pour JIRA

1. Dans une autre fenêtre du navigateur web, connectez-vous à votre serveur local JIRA en tant qu’administrateur.

1. Pointez sur la roue dentée, puis cliquez sur **Add-ons** (Modules complémentaires).

    ![Capture d’écran montrant l’icône « Roue dentée » sélectionnée, et l’option « Add-ons » sélectionnée dans la liste déroulante.](./media/kantegassoforjira-tutorial/settings.png)

1. Sous l’onglet Modules complémentaires, cliquez sur **Find new add-ons** (Trouver de nouveaux modules complémentaires). Recherchez **Kantega SSO for JIRA (SAML & Kerberos)** , puis cliquez sur le bouton **Installer** pour installer le nouveau plug-in SAML.

    ![Capture d’écran montrant la section « Find new Add-ons », avec « Kantego SSO for JIRA (SAML & Kerberos) » dans la zone de recherche et le bouton « Install » sélectionné.](./media/kantegassoforjira-tutorial/install-tab.png)

1. L’installation du plug-in démarre.

    ![Capture d’écran montrant la boîte de dialogue « Installing » du plug-in.](./media/kantegassoforjira-tutorial/installation.png)

1. Une fois l’installation terminée. Cliquez sur **Fermer**.

    ![Capture d’écran montrant la boîte de dialogue « Installed and ready to go! » avec l’action « Close » sélectionnée.](./media/kantegassoforjira-tutorial/close-tab.png)

1.  Cliquez sur **Gérer**.

    ![Capture d’écran affichant la page de l’application « Kantega SSO » avec le bouton « Manage » sélectionné.](./media/kantegassoforjira-tutorial/manage-tab.png)
    
1. Le nouveau plug-in est répertorié sous **INTÉGRATIONS**. Cliquez sur **Configurer** pour configurer le nouveau plug-in.

    ![Capture d’écran montrant l’élément « INTEGRATIONS » mis en évidence dans le menu de navigation de gauche, et le bouton « Configure » sélectionné dans la section « Manage add-ons ».](./media/kantegassoforjira-tutorial/integration.png)

1. Dans la section **SAML**. Dans le menu déroulant **Ajouter le fournisseur d’identité**, sélectionnez **Azure Active Directory (Azure AD)**.

    ![Capture d’écran montrant la liste déroulante « Add identity provider » avec l’option « Azure Active Directory (Azure AD) » sélectionnée.](./media/kantegassoforjira-tutorial/identity-provider.png)

1. Sélectionnez le niveau d’abonnement **De base**.

    ![Capture d’écran montrant la section « Preparing Azure AD » avec l’option « Basic » sélectionnée.](./media/kantegassoforjira-tutorial/basic-tab.png)

1. Dans la section **Propriétés de l’application**, procédez comme suit : 

    ![Capture d’écran montrant la section « App properties » avec la zone de texte « App ID URL » et l’icône de copie mises en évidence ; le bouton « Next » est sélectionné.](./media/kantegassoforjira-tutorial/properties.png)

    1. Copiez la valeur de **URI ID d’application** et utilisez-la en tant que **Identificateur, URL de réponse et URL de connexion** dans la section **Configuration SAML de base** du portail Azure.

    1. Cliquez sur **Suivant**.

1. Dans la section **Metadata import** (Importation des métadonnées), procédez comme suit : 

    ![Capture d’écran montrant la section « Metadata import » avec l’option « Metadata file on my computer » sélectionnée.](./media/kantegassoforjira-tutorial/metadata.png)

    1. Sélectionnez **Metadata file on my computer** (Fichier de métadonnées sur mon ordinateur), puis chargez le fichier de métadonnées que vous avez téléchargé à partir du portail Azure.

    1. Cliquez sur **Suivant**.

1. Dans la section **Name and SSO location** (Nom et emplacement de l’authentification unique), procédez comme suit :

    ![Capture d’écran montrant la section « Name and SSO location », avec la zone de texte « Identity provider name » mise en évidence et le bouton « Next » sélectionné.](./media/kantegassoforjira-tutorial/location.png)

    1. Ajoutez le nom du fournisseur d’identité dans la zone de texte **Identity provider name** (Nom du fournisseur d’identité) (par exemple, Azure AD).

    1. Cliquez sur **Suivant**.

1. Vérifiez le certificat de signature, puis cliquez sur **Suivant**.

    ![Capture d’écran montrant la section « Signature verification » avec le bouton « Next » sélectionné.](./media/kantegassoforjira-tutorial/certificate.png)

1. Dans la section **JIRA user accounts** (Comptes d’utilisateur JIRA), procédez comme suit :

    ![Capture d’écran montrant la section « JIRA user accounts », avec l’option « Create users in JIRA’s Internal Directory if needed » mise en évidence et le bouton « Next » sélectionné.](./media/kantegassoforjira-tutorial/accounts.png)

    1. Sélectionnez **Create users in JIRA’s internal Directory if needed** (Créer des utilisateurs dans l’annuaire interne de JIRA si nécessaire) et saisissez le nom de groupe approprié pour les utilisateurs (il peut s’agir de plusieurs groupes séparés par des virgules).

    1. Cliquez sur **Suivant**.

1. Cliquez sur **Terminer**.

    ![Capture d’écran montrant la section « Summary » avec le bouton « Finish » sélectionné.](./media/kantegassoforjira-tutorial/finish-tab.png)

1. Dans la section **Known domains for Azure AD** (Domaines connus pour Azure AD), procédez comme suit :

    ![Configure Single Sign-On](./media/kantegassoforjira-tutorial/save-tab.png)

    1. Sélectionnez **Known domains** (Domaines connus) dans le volet gauche de la page.

    2. Entrez le nom de domaine dans la zone de texte **Known domains** (Domaines connus).

    3. Cliquez sur **Enregistrer**.

### <a name="create-kantega-sso-for-jira-test-user"></a>Créer un utilisateur de test Kantega SSO pour JIRA

Pour se connecter à JIRA, les utilisateurs Azure AD doivent être provisionnés dans JIRA. Dans Kantega SSO pour JIRA, cet approvisionnement est une tâche manuelle.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous à votre serveur local JIRA en tant qu’administrateur.

1. Pointez sur la roue dentée, puis cliquez sur **Gestion des utilisateurs**.

    ![Capture d’écran montrant l’icône « Roue dentée » mise en évidence et l’option « User management » sélectionnée dans la liste déroulante.](./media/kantegassoforjira-tutorial/user.png) 

1. Sous l’onglet **User management** (Gestion des utilisateurs), cliquez sur **Create user** (Créer un utilisateur).

    ![Capture d’écran montrant la section « User management » avec le bouton « Create User » sélectionné.](./media/kantegassoforjira-tutorial/create-user.png) 

1. Dans la page de boîte de dialogue **Create New User** (Créer un utilisateur), procédez comme suit :

    ![Ajouter un employé](./media/kantegassoforjira-tutorial/new-user.png) 

    1. Dans la zone de texte **Email address** (Adresse e-mail), tapez l’adresse e-mail d’un utilisateur, par exemple, Brittasimon@contoso.com.

    2. Dans la zone de texte **Full Name** (Nom complet), tapez le nom complet d’un utilisateur, par exemple, Britta Simon.

    3. Dans la zone de texte **Username** (Nom d’utilisateur), tapez l’e-mail d’un utilisateur, par exemple, Brittasimon@contoso.com.

    4. Dans la zone de texte **Password** (Mot de passe), tapez le mot de passe de l’utilisateur.

    5. Cliquez sur **Create User** (Créer un utilisateur).

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

#### <a name="sp-initiated"></a>Lancée par le fournisseur de services :

* Cliquez sur **Tester cette application** dans le portail Azure. Une redirection est effectuée vers l’URL de connexion à Kantega SSO pour JIRA, où vous pouvez lancer le flux de connexion.  

* Accédez directement à l’URL de connexion à Kantega SSO pour JIRA pour lancer le flux de connexion.

#### <a name="idp-initiated"></a>Lancée par le fournisseur d’identité :

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors automatiquement connecté à l’instance Kantega SSO pour JIRA pour laquelle vous avez configuré l’authentification unique. 

Vous pouvez aussi utiliser Mes applications de Microsoft pour tester l’application dans n’importe quel mode. Si, quand vous cliquez sur la vignette Kantega SSO pour JIRA dans Mes applications, le mode Fournisseur de services est configuré, vous êtes redirigé vers la page de connexion de l’application pour lancer le flux de connexion ; s’il s’agit du mode Fournisseur d’identité, vous êtes automatiquement connecté à l’instance Kantega SSO pour JIRA pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré Kantega SSO pour JIRA, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
