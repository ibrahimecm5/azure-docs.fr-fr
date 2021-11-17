---
title: 'Tutoriel : Intégration de l’authentification unique Azure AD avec Kantega SSO for Confluence'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Kantega SSO pour Confluence.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/13/2021
ms.author: jeedes
ms.openlocfilehash: 024687e693b82ed3f8053fcdceefe876a5cad9e1
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132313784"
---
# <a name="tutorial-azure-ad-sso-integration-with-kantega-sso-for-confluence"></a>Tutoriel : Intégration de l’authentification unique Azure AD avec Kantega SSO for Confluence

Dans ce tutoriel, vous allez apprendre à intégrer Azure Active Directory (Azure AD) à Kantega SSO for Confluence. Quand vous intégrez Azure AD à Kantega SSO for Confluence, vous pouvez :

* Contrôler dans Azure AD qui a accès à Kantega SSO for Confluence.
* Permettre aux utilisateurs de se connecter automatiquement à Kantega SSO for Confluence avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD avec Kantega SSO pour Confluence, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un [compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Kantega SSO for Confluence pour lequel l’authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Kantega SSO for Confluence prend en charge l’authentification unique lancée par le **fournisseur de services et le fournisseur d’identité**.

## <a name="add-kantega-sso-for-confluence-from-the-gallery"></a>Ajouter Kantega SSO for Confluence à partir de la galerie

Pour configurer l’intégration de Kantega SSO pour Confluence dans Azure AD, vous devez ajouter Kantega SSO pour Confluence à partir de la galerie à la liste des applications SaaS managées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Kantega SSO for Confluence** dans la zone de recherche.
1. Sélectionnez **Kantega SSO for Confluence** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-kantega-sso-for-confluence"></a>Configurer et tester l’authentification unique Azure AD pour Kantega SSO for Confluence

Configurez et testez l’authentification unique Azure AD avec Kantega SSO for Confluence à l’aide d’un utilisateur test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Kantega SSO for Confluence associé.

Pour configurer et tester l’authentification unique Azure AD avec Kantega SSO for Confluence, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique de Kantega SSO for Confluence](#configure-kantega-sso-for-confluence-sso)** pour configurer les paramètres d’authentification unique côté application.
    1. **[Créer un utilisateur test Kantega SSO for Confluence](#create-kantega-sso-for-confluence-test-user)** pour avoir un équivalent de B.Simon dans Kantega SSO for Confluence, lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **Kantega SSO for Confluence**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, si vous souhaitez configurer l’application en mode lancé par le **fournisseur d’identité**, effectuez les étapes suivantes :

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

5. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL de réponse et l’URL de connexion réels. Ces valeurs sont reçues durant la configuration du plug-in Confluence qui est décrite plus loin dans le didacticiel.

6. Sur la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le fichier **XML de métadonnées de fédération** en fonction des options définies selon vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

7. Dans la section **Configurer Kantega SSO pour Confluence**, copiez la ou les URL appropriée(s) en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Kantega SSO for Confluence.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Kantega SSO pour Confluence**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-kantega-sso-for-confluence-sso"></a>Configurer l’authentification unique de Kantega SSO for Confluence

1. Dans une autre fenêtre de navigateur web, connectez-vous à votre **portail d’administration Confluence** en tant qu’administrateur.

1. Pointez sur le roue dentée, puis cliquez sur **Modules complémentaires**.

    ![Capture d’écran montrant l’icône de menu « Roue dentée » et l’option « Add-ons » sélectionnée.](./media/kantegassoforconfluence-tutorial/settings.png)

1. Sous **ATLASSIAN MARKETPLACE**, cliquez sur **Find new add-ons** (Trouver de nouveaux modules complémentaires).

    ![Capture d’écran montrant l’onglet « ATTLASSIAN MARKETPLACE » avec l’option « Find new add-ons » sélectionnée.](./media/kantegassoforconfluence-tutorial/admin.png)

1. Recherchez **Kerberos SAML Kantega SSO pour Confluence**, puis cliquez sur le bouton **Installer** pour installer le nouveau plug-in SAML.

    ![Capture d’écran montrant la page « Find new add-ons », avec « Kantega SSO for Confluence SAML Kerberos » dans la zone de recherche et le bouton « Install » sélectionné.](./media/kantegassoforconfluence-tutorial/install-button.png)

1. L’installation du plug-in démarre.

    ![Capture d’écran montrant la fenêtre « Installing » du plug-in.](./media/kantegassoforconfluence-tutorial/plugin.png)

1. Une fois l’installation terminée. Cliquez sur **Fermer**.

    ![Capture d’écran montrant l’écran « Installed and ready to go » avec l’action « Close » sélectionnée.](./media/kantegassoforconfluence-tutorial/installation.png)

1. Cliquez sur **Gérer**.

    ![Capture d’écran montrant le plug-in « Kantega Single Sign-on with Kerberos and SAML » avec le bouton « Manage » sélectionné.](./media/kantegassoforconfluence-tutorial/integration.png)

1. Cliquez sur **Configurer** pour configurer le nouveau plug-in.

    ![Capture d’écran montrant la page « Kantega Single Sign-on with Kerberos and SAML » avec le bouton « Configure » sélectionné.](./media/kantegassoforconfluence-tutorial/configuration.png)

1. Ce nouveau plug-in figure également sous l’onglet **UTILISATEURS ET SÉCURITÉ**.

    ![Capture d’écran montrant l’onglet « USERS & SECURITY » avec l’action « Kantega Single Sign-on » sélectionnée.](./media/kantegassoforconfluence-tutorial/security.png)

1. Dans la section **SAML**. Dans le menu déroulant **Ajouter le fournisseur d’identité**, sélectionnez **Azure Active Directory (Azure AD)** .

    ![Capture d’écran montrant la section « SAML » avec les éléments « Add Identity provider » et « Azure Active Directory (Azure AD) » sélectionnés.](./media/kantegassoforconfluence-tutorial/azure.png)

1. Sélectionnez le niveau d’abonnement **De base**.

    ![Capture d’écran montrant la page « Preparing Azure AD » avec l’option « Basic » sélectionnée.](./media/kantegassoforconfluence-tutorial/subscription.png)

1. Dans la section **Propriétés de l’application**, procédez comme suit :

    ![Capture d’écran montrant la section « App properties » avec le champ « App ID URL » et le bouton « Copy » mis en évidence, et le bouton « Next » sélectionné.](./media/kantegassoforconfluence-tutorial/properties.png)

    a. Copiez la valeur de **URI ID d’application** et utilisez-la en tant que **Identificateur, URL de réponse et URL de connexion** dans la section **Configuration SAML de base** du portail Azure.

    b. Cliquez sur **Suivant**.

1. Dans la section **Metadata import** (Importation des métadonnées), procédez comme suit : 

    ![Capture d’écran montrant la section « Metadata import » avec l’option « Metadata file on my computer » sélectionnée.](./media/kantegassoforconfluence-tutorial/metadata.png)

    a. Sélectionnez **Metadata file on my computer** (Fichier de métadonnées sur mon ordinateur), puis chargez le fichier de métadonnées que vous avez téléchargé à partir du portail Azure.

    b. Cliquez sur **Suivant**.

1. Dans la section **Name and SSO location** (Nom et emplacement de l’authentification unique), procédez comme suit :

    ![Capture d’écran montrant la section « Name and SSO location », avec la zone de texte « Identity provider name » mise en évidence et le bouton « Next » sélectionné.](./media/kantegassoforconfluence-tutorial/location.png)

    a. Ajoutez le nom du fournisseur d’identité dans la zone de texte **Identity provider name** (Nom du fournisseur d’identité) (par exemple, Azure AD).

    b. Cliquez sur **Suivant**.

1. Vérifiez le certificat de signature, puis cliquez sur **Suivant**.

    ![Capture d’écran montrant la section « Signature verification » avec le bouton « Next » sélectionné.](./media/kantegassoforconfluence-tutorial/certificate.png)

1. Dans la section **Confluence user accounts** (Comptes d’utilisateur Confluence), procédez comme suit :

    ![Capture d’écran montrant la section « Confluence user accounts » avec l’option « Create users in Confluence’s Internal Directory if needed » et le bouton « Next » sélectionnés.](./media/kantegassoforconfluence-tutorial/accounts.png)

    a. Sélectionnez **Create users in Confluence’s internal Directory if needed** (Créer des utilisateurs dans l’annuaire interne de Confluence si nécessaire) et entrez le nom de groupe approprié pour les utilisateurs (il peut s’agir de plusieurs groupes séparés par des virgules).

    b. Cliquez sur **Suivant**.

1. Cliquez sur **Terminer**.

    ![Capture d’écran de la page « Summary » avec le bouton « Finish » sélectionné.](./media/kantegassoforconfluence-tutorial/summary.png)

1. Dans la section **Known domains for Azure AD** (Domaines connus pour Azure AD), procédez comme suit : 

    ![Capture d’écran montrant la page « Known domains for Azure AD » avec la zone de texte « Known domains » mise en évidence et le bouton « Save » sélectionné.](./media/kantegassoforconfluence-tutorial/domain.png)

    a. Sélectionnez **Known domains** (Domaines connus) dans le volet gauche de la page.

    b. Entrez le nom de domaine dans la zone de texte **Known domains** (Domaines connus).

    c. Cliquez sur **Enregistrer**.

### <a name="create-kantega-sso-for-confluence-test-user"></a>Création d’un utilisateur de test Kantega SSO pour Confluence

Pour permettre aux utilisateurs Azure AD de se connecter à Confluence, vous devez les configurer dans Confluence. Dans le cas de Kantega SSO pour Confluence, cet approvisionnement est une tâche manuelle.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous à votre site d’entreprise Kantega SSO pour Confluence en tant qu’administrateur.

1. Pointez sur la roue dentée, puis cliquez sur **Gestion des utilisateurs**.

    ![Capture d’écran montrant l’icône « Roue dentée » et l’option « User management » sélectionnées.](./media/kantegassoforconfluence-tutorial/user-management.png)

1. Dans la section Utilisateurs, cliquez sur l’onglet **Add users** (Ajouter des utilisateurs). Dans la page de boîte de dialogue **Add a User** (Ajouter un utilisateur), procédez comme suit :

    ![Ajouter un employé](./media/kantegassoforconfluence-tutorial/create-user.png)

    a. Dans la zone de texte **Username** (Nom d’utilisateur), tapez l’e-mail d’un utilisateur, par exemple, Brittasimon@contoso.com.

    b. Dans la zone de texte **Full Name** (Nom complet), tapez le nom complet d’un utilisateur, par exemple, Britta Simon.

    c. Dans la zone de texte **Email** (E-mail), tapez l’adresse e-mail d’un utilisateur, par exemple, Brittasimon@contoso.com.

    d. Dans la zone de texte **Password** (Mot de passe), tapez le mot de passe de l’utilisateur.

    e. Cliquez sur **Confirm Password** (Confirmer le mot de passe), puis entrez de nouveau le mot de passe.

    f. Cliquez sur le bouton **Ajouter**.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

#### <a name="sp-initiated"></a>Lancée par le fournisseur de services :

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL d’authentification de Kantega SSO for Confluence, à partir de laquelle vous pouvez lancer le processus de connexion.  

* Accédez directement à l’URL d’authentification de Kantega SSO for Confluence pour lancer le processus de connexion.

#### <a name="idp-initiated"></a>Lancée par le fournisseur d’identité :

* Cliquez sur **Tester cette application** dans le portail Azure, ce qui devrait vous connecter automatiquement à l’application Kantega SSO for Confluence pour laquelle vous avez configuré l’authentification unique. 

Vous pouvez aussi utiliser Mes applications de Microsoft pour tester l’application dans n’importe quel mode. Quand vous cliquez sur la vignette Kantega SSO for Confluence dans Mes applications, si le mode Fournisseur de services est configuré, vous êtes redirigé vers la page d’authentification de l’application pour lancer le processus de connexion. Si le mode Fournisseur d’identité est configuré, vous devriez être automatiquement connecté à l’application Kantega SSO for Confluence pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré Kantega SSO for Confluence, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
