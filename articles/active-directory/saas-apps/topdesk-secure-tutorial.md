---
title: 'Tutoriel : Intégration de l’authentification unique Azure AD à TOPdesk - Secure'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et TOPdesk - Secure.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/09/2021
ms.author: jeedes
ms.openlocfilehash: 01f70a4cb71f8f864e84dc27e200f2b8f020547a
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132340853"
---
# <a name="tutorial-azure-ad-sso-integration-with-topdesk---secure"></a>Tutoriel : Intégration de l’authentification unique Azure AD à TOPdesk - Secure

Dans ce tutoriel, vous allez découvrir comment intégrer TOPdesk - Secure à Azure Active Directory (Azure AD). Quand vous intégrez TOPdesk - Secure à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à TOPdesk - Secure.
* Permettre à vos utilisateurs de se connecter automatiquement à TOPdesk - Secure (par le biais de l’authentification unique) avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :
 
 * Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement TOPdesk - Secure pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* TOPdesk - Secure prend en charge l’authentification unique lancée par le **fournisseur de services**.

## <a name="add-topdesk---secure-from-the-gallery"></a>Ajouter TOPdesk - Secure à partir de la galerie

Pour configurer l’intégration de TOPdesk - Secure à Azure AD, vous devez ajouter TOPdesk - Secure à partir de la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **TOPdesk - Secure** dans la zone de recherche.
1. Sélectionnez **TOPdesk - Secure** dans le panneau de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-topdesk---secure"></a>Configurer et tester l’authentification unique Azure AD pour TOPdesk - Secure

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec TOPdesk - Secure avec un utilisateur de test appelé **Britta Simon**.
Pour que l’authentification unique fonctionne, une relation entre un utilisateur Azure AD et l’utilisateur TOPdesk - Secure associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec TOPdesk - Secure, vous devez effectuer les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
    2. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
2. **[Configurer l’authentification unique TOPdesk - Secure](#configure-topdesk---secure-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test TOPdesk - Secure](#create-topdesk---secure-test-user)** pour avoir un équivalent de Britta Simon dans TOPdesk - Secure lié à la représentation de l’utilisateur dans Azure AD.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure.

Pour configurer l’authentification unique Azure AD avec TOPdesk - Secure, effectuez les étapes suivantes :

1. Dans le portail Azure, dans la page d’intégration de l’application **TOPdesk - Secure**, cliquez sur **Authentification unique**.

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, sélectionnez le mode **SAML/WS-Fed** afin d’activer l’authentification unique.

3. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône Crayon pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    a. Dans la zone **URL d’identificateur**, renseignez l’URL des métadonnées TOPdesk que vous pouvez récupérer à partir de la configuration TOPdesk. Elle doit utiliser le modèle suivant : `https://<companyname>.topdesk.net/saml-metadata/<identifier>`

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://<companyname>.topdesk.net/tas/secure/login/verify`

    c. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<companyname>.topdesk.net`.

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL de réponse et l’URL de connexion réels. Contactez l’[équipe de support technique TOPdesk - Secure](https://www.topdesk.com/en/services/support/) pour obtenir ces valeurs. Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

5. Sur la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le fichier **XML de métadonnées de fédération** en fonction des options définies selon vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

6. Dans la section **Configurer TOPdesk - Secure**, copiez la ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à TOPdesk - Secure.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **TOPdesk - Secure**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-topdesk---secure-sso"></a>Configurer l’authentification unique TOPdesk - Secure

1. Connectez-vous à votre site d'entreprise **TOPdesk - Secure** en tant qu'administrateur.

2. Dans le menu **TOPdesk**, cliquez sur **Settings**.

    ![Paramètres](./media/topdesk-secure-tutorial/menu.png "Paramètres")

3. Cliquez sur **Login Settings**.

    ![Paramètres de connexion](./media/topdesk-secure-tutorial/overview.png "Login Settings")

4. Développez le menu **Login Settings**, puis cliquez sur **General**.

    ![Généralités](./media/topdesk-secure-tutorial/navigator.png "Général")

5. Dans la section **Secure** de la section de configuration **SAML login**, procédez comme suit :

    ![Paramètres techniques](./media/topdesk-secure-tutorial/configuration.png "Technical Settings")

    a. Cliquez sur **Download** pour télécharger le fichier de métadonnées public et l'enregistrer localement sur votre ordinateur.

    b. Ouvrez le fichier de métadonnées et recherchez le nœud **AssertionConsumerService**.

    ![Assertion Consumer Service](./media/topdesk-secure-tutorial/service.png "Assertion Consumer Service")

    c. Copiez la valeur **AssertionConsumerService**, collez-la dans la zone de texte URL de réponse dans la section **Domaine et URL TOPdesk - Secure**.

6. Pour créer un fichier de certificat, procédez comme suit :

    ![Certificate](./media/topdesk-secure-tutorial/file.png "Certificat")

    a. Ouvrez le fichier de métadonnées téléchargé à partir du portail Azure.

    b. Développez le nœud **RoleDescriptor** qui a un **xsi:type** égal à **fed:ApplicationServiceType**.

    c. Copiez la valeur du nœud **X509Certificate**.

    d. Enregistrez la valeur **X509Certificate** copiée sur votre ordinateur dans un fichier.

7. Dans la section **Public**, cliquez sur **Add**.

    ![Ajouter](./media/topdesk-secure-tutorial/secure.png "Ajouter")

8. Dans la page de boîte de dialogue **SAML configuration assistant**, procédez comme suit :

    ![Assistant de configuration SAML](./media/topdesk-secure-tutorial/metadata.png "SAML configuration assistant")

    a. Pour charger votre fichier de métadonnées téléchargé à partir du portail Azure, dans **Métadonnées de fédération**, cliquez sur **Parcourir**.

    b. Pour charger le fichier de certificat, sous **Certificate (RSA)**, cliquez sur **Browse**.

    c. Pour **Private key(RSA, PKCS8, DER)**, vous pouvez charger votre propre clé privée ou contacter [l’équipe de support technique TOPdesk](https://www.topdesk.com/en/services/support/) pour obtenir la clé privée.

    d. Pour charger le fichier de logo que vous a fourni l'équipe de support technique TOPdesk, sous **Logo icon**, cliquez sur **Browse**.

    e. Dans la zone de texte **User name attribute** (Attribut de nom d’utilisateur), entrez `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    f. Dans la zone de texte **Display name**, tapez un nom pour votre configuration.

    g. Cliquez sur **Enregistrer**.

### <a name="create-topdesk---secure-test-user"></a>Créer un utilisateur de test TOPdesk - Secure

Pour se connecter à TOPdesk - Secure, les utilisateurs d’Azure AD doivent être approvisionnés dans TOPdesk - Secure.  
Dans le cas de TOPdesk - Secure, l’approvisionnement est une tâche manuelle.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Pour configurer l'approvisionnement des utilisateurs, procédez comme suit :

1. Connectez-vous à votre site d’entreprise **TOPdesk - Secure** en tant qu’administrateur.

2. Dans le menu en haut, cliquez sur **TOPdesk \> New \> Support Files \> Operator**.

    ![Opérateur](./media/topdesk-secure-tutorial/support-files.png "Opérateur")

3. Dans la boîte de dialogue **New Operator**, procédez comme suit :

    ![Nouvel opérateur](./media/topdesk-secure-tutorial/details.png "New, opérateur")

    a. Cliquez sur l’onglet **General** (Général).

    b. Dans la zone de texte **Surname** (Nom), tapez le nom de l’utilisateur, par exemple **Simon**.

    c. Sélectionnez un **Site** pour le compte dans la section **Location**.

    d. Dans la zone de texte **Login Name** de la section **TOPdesk Login**, tapez un nom de connexion pour votre utilisateur.

    e. Cliquez sur **Enregistrer**.

> [!NOTE]
> Vous pouvez utiliser tout autre outil ou API de création de compte d’utilisateur TOPdesk - Secure fourni par TOPdesk - Secure pour provisionner des comptes d’utilisateur Azure AD.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion à TOPdesk - Secure, d’où vous pouvez lancer le flux de connexion. 

* Accédez directement à l’URL de connexion à TOPdesk - Secure pour lancer le flux de connexion.

* Vous pouvez utiliser Mes applications de Microsoft. Quand vous cliquez sur la vignette TOPdesk - Secure dans Mes applications, vous devez être connecté automatiquement à l’application TOPdesk - Secure pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré TOPdesk - Secure, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
