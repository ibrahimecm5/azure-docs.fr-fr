---
title: 'Tutoriel : Intégration de l’authentification unique (SSO) Azure Active Directory à Check Point Remote Secure Access VPN | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Check Point Remote Secure Access VPN.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/16/2021
ms.author: jeedes
ms.openlocfilehash: 33bcb007c1cfd4d09c25fb4abbf9c5fcfb15b58f
ms.sourcegitcommit: 5af89a2a7b38b266cc3adc389d3a9606420215a9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2021
ms.locfileid: "131988739"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-check-point-remote-secure-access-vpn"></a>Tutoriel : Intégration de l’authentification unique (SSO) Azure Active Directory à Check Point Remote Secure Access VPN

Dans ce tutoriel, vous allez découvrir comment intégrer Check Point Remote Secure Access VPN à Azure Active Directory (Azure AD). Quand vous intégrez Check Point Remote Secure Access VPN à Azure AD, vous pouvez :

* Contrôler, dans Azure AD, qui a accès à Check Point Remote Secure Access VPN.
* Permettre aux utilisateurs de se connecter automatiquement à Check Point Remote Secure Access VPN avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Check Point Remote Secure Access VPN pour lequel l’authentification SSO (authentification unique) est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* Check Point Remote Secure Access VPN prend en charge l’authentification SSO lancée par le **fournisseur de services**.

## <a name="adding-check-point-remote-secure-access-vpn-from-the-gallery"></a>Ajout de Check Point Remote Secure Access VPN à partir de la galerie

Pour configurer l’intégration de Check Point Remote Secure Access VPN à Azure AD, vous devez ajouter Check Point Remote Secure Access VPN à votre liste d’applications SaaS managées, à partir de la galerie.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Sous la section **Ajouter à partir de la galerie**, dans la zone de recherche, tapez **Check Point Remote Secure Access VPN**.
1. Sélectionnez **Check Point Remote Secure Access VPN** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-check-point-remote-secure-access-vpn"></a>Configurer et tester l’authentification SSO Azure AD pour Check Point Remote Secure Access VPN

Configurez et testez l’authentification SSO Azure AD avec Check Point Remote Secure Access VPN à l’aide d’une utilisatrice de test appelée **B.Simon**. Pour que l’authentification SSO fonctionne, vous devez établir une relation entre un utilisateur Azure AD et l’utilisateur associé dans Check Point Remote Secure Access VPN.

Pour configurer et tester l’authentification SSO Azure AD avec Check Point Remote Secure Access VPN, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification SSO de Check Point Remote Secure Access VPN](#configure-check-point-remote-secure-access-vpn-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.

    1. **[Créer une utilisatrice de test pour Check Point Remote Secure Access VPN](#create-check-point-remote-secure-access-vpn-test-user)** afin de disposer dans Check Point Remote Secure Access VPN d’un équivalent de B.Simon lié à la représentation Azure AD de l’utilisatrice.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **Check Point Remote Secure Access VPN**, recherchez la section **Gérer**, puis sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, entrez les valeurs pour les champs suivants :

    1. Dans la zone de texte **Identificateur (ID d’entité)** , saisissez une URL au format suivant : `https://<GATEWAY_IP>/saml-vpn/spPortal/ACS/ID/<IDENTIFIER_UID>`

    1. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://<GATEWAY_IP>/saml-vpn/spPortal/ACS/Login/<IDENTIFIER_UID>`

    1. Dans la zone de texte **URL de connexion**, saisissez une URL au format suivant : `https://<GATEWAY_IP>/saml-vpn/`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL d’authentification et l’URL de réponse réels. Pour obtenir ces valeurs, contactez l’[équipe du support technique de Check Point Remote Secure Access VPN](mailto:support@checkpoint.com). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **XML de métadonnées de fédération** et sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

1. Dans la section **Configurer Check Point Remote Secure Access VPN**, copiez l’URL ou les URL appropriées selon vos besoins.

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

Dans cette section, vous allez permettre à B.Simon d’utiliser l’authentification unique Azure en lui octroyant l’accès à Check Point Remote Secure Access VPN.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Check Point Remote Secure Access VPN**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-check-point-remote-secure-access-vpn-sso"></a>Configurer l’authentification SSO pour Check Point Remote Secure Access VPN

### <a name="configure-an-external-user-profile-object"></a>Configurer un objet de profil utilisateur externe

> [!NOTE]
> Cette section est nécessaire uniquement si vous ne souhaitez pas utiliser d’annuaire Active Directory local (LDAP).

**Configurer un profil utilisateur générique dans le SmartDashboard hérité** :

1. Dans SmartConsole, accédez à **Manage & Settings > Blades** (Gestion et paramètres > Blades).

1. Dans la section **Mobile Access** (Accès mobile), cliquez sur **Configure in SmartDashboard** (Configurer dans SmartDashboard). Le SmartDashboard hérité s’ouvre.

1. Dans le volet **Network Objects** (Objets réseau), cliquez sur **Utilisateurs**.

1. Cliquez avec le bouton droit à un emplacement vide, puis sélectionnez **New > External User Profile > Match all users** (Nouveau > Profil utilisateur externe > Faire correspondre tous les utilisateurs).

1. Configurez les propriétés de **External User Profile** :

    1. Dans la page **General Properties** (Propriétés générales) :
        * Dans le champ de nom correspondant à **External User Profile**, conservez le nom par défaut `generic`*
        * Dans le champ **Date d’expiration**, définissez la date applicable

    1. Dans la page **Authentification** :
        * Dans la liste déroulante **Authentication Scheme** (Schéma d’authentification), sélectionnez `undefined`
    1. Dans les pages **Location** (Emplacement), **Time** (Heure) et **Encryption** (Chiffrement) :
        * Configurez les autres paramètres applicables
    1. Cliquez sur **OK**.

1. Dans la barre d’outils supérieure, cliquez sur **Update** (Mettre à jour), ou appuyez sur Ctrl+S.

1. Fermez SmartDashboard.

1. Dans SmartConsole, installez la stratégie de contrôle d’accès.

### <a name="configure-remote-access-vpn"></a>Configurer Remote Access VPN

1. Ouvrez l’objet de la passerelle de sécurité applicable.

1. Dans la page General Properties, activez la blade logicielle **IPsec VPN** (VPN IPsec).

1. Dans l’arborescence de gauche, cliquez dans la page **IPsec VPN**.

1. Dans la section **This Security Gateway participates in the following VPN communities** (Cette passerelle de sécurité fait partie des communautés VPN suivantes), cliquez sur **Add** (Ajouter), puis sélectionnez **Remote Access Community** (Communauté d’accès à distance).

1. Dans l’arborescence de gauche, cliquez sur **VPN clients > Remote Access** (Client VPN > Accès à distance).

1. Activez **Support Visitor Mode** (Prendre en charge le mode Visiteur).

1. Dans l’arborescence de gauche, cliquez sur **VPN clients > Office Mode** (Clients VPN > Mode Bureau).

1. Sélectionnez **Allow Office Mode** (Autoriser le mode Bureau), puis sélectionnez la méthode du mode Bureau applicable.

1. Dans l’arborescence de gauche, cliquez sur **VPN Clients > SAML Portal Settings** (Clients VPN > Paramètres du portail SAML).

1. Vérifiez que l’URL principale contient le nom de domaine complet de la passerelle.
Ce nom de domaine doit finir par un suffixe DNS inscrit par votre organisation.
Par exemple : `https://gateway1.company.com/saml-vpn`

1. Vérifiez que le certificat est approuvé par le navigateur des utilisateurs finaux.

1. Cliquez sur **OK**.

### <a name="configure-an-identity-provider-object"></a>Configurer un objet de fournisseur d’identité

1. Effectuez les étapes suivantes pour chaque passerelle de sécurité qui participe à Remote Access VPN.

1. Dans SmartConsole > affichage **Gateways & Servers**, cliquez sur **New > More > User/Identity > Identity Provider**.

    ![capture d’écran du nouveau fournisseur d’identité.](./media/check-point-remote-access-vpn-tutorial/identity-provider.png)

1. Dans la page **New Identity Provider**, effectuez les étapes suivantes.

    ![capture d’écran de la section Identity Provider.](./media/check-point-remote-access-vpn-tutorial/new-identity-provider.png)

    a. Dans le champ **Gateway**, sélectionnez la passerelle de sécurité qui doit effectuer l’authentification SAML.

    b. Dans le champ **Service**, dans la liste déroulante, sélectionnez **Remote Access VPN**.

    c. Copiez la valeur du champ **Identifier(Entity ID)** , puis collez-la dans la zone de texte **Identificateur** de la section **Configuration SAML de base** du portail Azure.

    d. Copiez la valeur du champ **Reply URL**, puis collez-la dans la zone de texte **URL de réponse** de la section **Configuration SAML de base** du portail Azure.

    e. Sélectionnez **Importer un fichier de métadonnées** pour charger le **XML de métadonnées de fédération** téléchargé à partir du portail Azure.

    > [!NOTE]
    > Vous pouvez également sélectionner **Insert Manually** pour coller manuellement les valeurs **ID d’entité** et **URL de connexion** dans les champs correspondants, et pour charger le **Fichier de certificat** à partir du portail Azure.

    f. Cliquez sur **OK**.

### <a name="configure-the-identity-provider-as-an-authentication-method"></a>Configurer le fournisseur d’identité en tant que méthode d’authentification

1. Ouvrez l’objet de la passerelle de sécurité applicable.

1. Dans la page **VPN Clients > Authentication** (Client VPN > Authentification) :

    a. Décochez la case **Allow older clients to connect to this gateway** (Autoriser les clients plus anciens à se connecter à cette passerelle).

    b. Ajoutez un nouvel objet ou modifiez un domaine existant.

    ![capture d’écran de l’ajout d’un nouvel objet.](./media/check-point-remote-access-vpn-tutorial/add-new-object.png)

1. Entrez un nom et un nom d’affichage, puis ajoutez/modifiez une méthode d’authentification. Au cas où l’option de connexion serait utilisée sur les passerelles qui participent à MEP, le nom doit commencer par le préfixe « SAMLVPN_ » pour rendre l’expérience utilisateur plus fluide.

    ![capture d’écran de l’option de connexion.](./media/check-point-remote-access-vpn-tutorial/login-option.png)

1. Sélectionnez l’option **Identity Provider** (Fournisseur d’identité), cliquez sur le bouton `+` vert, puis sélectionnez l’objet de fournisseur d’identité applicable.

    ![capture d’écran montrant la sélection de l’objet de fournisseur d’identité applicable.](./media/check-point-remote-access-vpn-tutorial/green-button.png)

1. Dans la fenêtre Multiple Login Options (Options de connexion multiples), dans le volet gauche, cliquez sur **User Directories** (Annuaires des utilisateurs), puis sélectionnez **Manual configuration** (Configuration manuelle).
Nous avons deux options :
    1. Si vous ne souhaitez pas utiliser d’annuaire Active Directory local (LDAP), sélectionnez uniquement External User Profiles, puis cliquez sur OK.
    2. Si vous souhaitez utiliser un annuaire Active Directory local (LDAP), sélectionnez uniquement LDAP users (Utilisateurs LDAP), puis dans LDAP Lookup Type (Type de recherche LDAP), sélectionnez email (e-mail). Puis cliquez sur OK.

    ![Capture d’écran de la configuration manuelle.](./media/check-point-remote-access-vpn-tutorial/manual-configuration.png)

1. Configurez les paramètres nécessaires dans la base de données de gestion :

    1. Fermez SmartConsole.

    2. Connectez-vous avec l’outil GuiDBEdit au serveur d’administration (consultez [sk13009](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails&solutionid=sk13009)).

    3. Dans le volet supérieur gauche, accédez à **Edit > Network Objects** (Edition > Objets réseau).

    4. Dans le volet supérieur droit, sélectionnez l’**objet Security Gateway** (Passerelle de sécurité).

    5. Dans le volet inférieur, accédez à **realms_for_blades > vpn**.

    6. Si vous ne souhaitez pas utiliser d’annuaire Active Directory local (LDAP), affectez à **do_ldap_fetch** la valeur **false**, et à **do_generic_fetch** la valeur **true**. Cliquez ensuite sur **OK**. Si vous souhaitez utiliser un annuaire Active Directory local (LDAP), affectez à **do_ldap_fetch** la valeur **true**, et à **do_generic_fetch** la valeur **false**. Cliquez ensuite sur **OK**.

    7. Répétez les étapes 4 à 6 pour toutes les passerelles de sécurité applicables.

    8. Enregistrez tous les changements apportés en sélectionnant **Fichier** > **Enregistrer tout**.

1. Fermez l’outil GuiDBEdit.

1. Chaque passerelle de sécurité et chaque blade logicielle a des paramètres distincts. Vérifiez les paramètres de chaque passerelle de sécurité et de chaque blade logicielle qui utilise l’authentification (VPN, Mobile Access et Identity Awareness).

    * Veillez à sélectionner l’option **LDAP users** uniquement pour les blades logicielles qui utilisent LDAP.

    * Veillez à sélectionner l’option **External User Profiles** uniquement pour les blades logicielles qui n’utilisent pas LDAP.

1. Installez la stratégie de contrôle d’accès sur chaque passerelle de sécurité.

### <a name="vpn-ra-client-installation-and-configuration"></a>Installation et configuration du client VPN RA

1. Installez le client VPN.

1. Définissez le mode d’exploration du fournisseur d’identité (facultatif).

    Par défaut, le client Windows utilise son navigateur intégré, et le client macOS utilise Safari pour s’authentifier sur le portail du fournisseur d’identité. Pour les clients Windows, modifiez ce comportement de façon à utiliser Internet Explorer à la place :

    1. Sur la machine cliente, ouvrez un éditeur de texte brut en tant qu’administrateur.

    2. Ouvrez le fichier `trac.defaults` dans l’éditeur de texte.

       * Sur les éditions 32 bits de Windows : 

         `%ProgramFiles%\CheckPoint\Endpoint Connect\trac.defaults`

       * Sur les éditions 64 bits de Windows : 

         `%ProgramFiles(x86)%\CheckPoint\Endpoint Connect\trac.defaults`

    3. Remplacez la valeur `embedded` de l’attribut `idp_browser_mode` par `IE`.

    4. Enregistrez le fichier .

    5. Redémarrez le service du client Check Point Endpoint Security VPN.

   Ouvrez l’invite de commandes Windows en tant qu’administrateur, puis exécutez les commandes suivantes :

   `# net stop TracSrvWrapper`

   `# net start TracSrvWrapper`

1. Démarrez l’authentification avec le navigateur s’exécutant en arrière-plan :

    1. Sur la machine cliente, ouvrez un éditeur de texte brut en tant qu’administrateur.

    2. Ouvrez le fichier `trac.defaults` dans l’éditeur de texte.

        * Sur les éditions 32 bits de Windows : 

          `%ProgramFiles%\CheckPoint\Endpoint Connect\trac.defaults`

        * Sur les éditions 64 bits de Windows : 

          `%ProgramFiles(x86)%\CheckPoint\Endpoint Connect\trac.defaults`

        * Sur macOS :

          `/Library/Application Support/Checkpoint/Endpoint Security/Endpoint Connect/trac.defaults`

    3. Remplacez la valeur de `idp_show_browser_primary_auth_flow` par `false`.

    4. Enregistrez le fichier .

    5. Redémarrez le service du client Check Point Endpoint Security VPN.

       * Sur les clients Windows, ouvrez l’invite de commandes Windows en tant qu’administrateur, puis exécutez les commandes suivantes :

         `# net stop TracSrvWrapper`

         `# net start TracSrvWrapper`

       * Sur les clients macOS, exécutez :

         `sudo launchctl stop com.checkpoint.epc.service`

         `sudo launchctl start com.checkpoint.epc.service`

### <a name="create-check-point-remote-secure-access-vpn-test-user"></a>Créer l’utilisateur de test Check Point Remote Secure Access VPN

Dans cette section, vous allez créer une utilisatrice appelée Britta Simon dans Check Point Remote Secure Access VPN. Rapprochez-vous de l’[équipe du support technique de Check Point Remote Secure Access VPN](mailto:support@checkpoint.com) pour ajouter les utilisateurs à la plateforme Check Point Remote Secure Access VPN. Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

1. Ouvrez le client VPN, puis cliquez sur **Connect to...** (Se connecter à).

    ![capture d’écran de l’option Connect to.](./media/check-point-remote-access-vpn-tutorial/connect.png)

1. Dans la liste déroulante, sélectionnez **Site**, puis cliquez sur **Se connecter**.

    ![capture d’écran de sélection du site.](./media/check-point-remote-access-vpn-tutorial/site.png)

1. Dans la fenêtre indépendante de connexion Azure AD, connectez-vous à l’aide des informations d’identification Azure AD créées dans la section **Créer un utilisateur de test Azure AD**.

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez configuré Check Point Remote Secure Access VPN, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
