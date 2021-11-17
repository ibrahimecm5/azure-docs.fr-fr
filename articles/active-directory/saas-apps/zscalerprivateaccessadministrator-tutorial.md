---
title: 'Didacticiel : Intégration d’Azure Active Directory à Zscaler Private Access Administrator | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Zscaler Private Access Administrator.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/31/2021
ms.author: jeedes
ms.openlocfilehash: 95cec5299405a8e5960eba46b4ee19b37383b829
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132302289"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-private-access-administrator"></a>Didacticiel : Intégration d’Azure Active Directory à Zscaler Private Access Administrator

Dans ce tutoriel, vous allez apprendre à intégrer Zscaler Private Access Administrator à Azure Active Directory (Azure AD). Quand vous intégrez Zscaler Private Access Administrator avec Azure AD, vous pouvez :

* Contrôler qui a accès à Zscaler Private Access Administrator dans Azure AD.
* Permettre à vos utilisateurs de se connecter automatiquement à Zscaler Private Access Administrator avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à Zscaler Private Access Administrator, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un [compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Zscaler Private Access Administrator pour lequel l’authentification unique est activée.

> [!NOTE]
> Cette intégration peut également être utilisée à partir de l’environnement cloud US Government Azure AD. Cette application est disponible dans la Galerie d’applications cloud US Government Azure AD et peut être configurée de la même façon que dans le cloud public.

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Zscaler Private Access Administrator prend en charge l’authentification unique lancée par le **fournisseur de services** et le **fournisseur d’identité**.

## <a name="add-zscaler-private-access-administrator-from-the-gallery"></a>Ajout de Zscaler Private Access Administrator à partir de la galerie

Pour configurer l’intégration de Zscaler Private Access Administrator à Azure AD, vous devez ajouter Zscaler Private Access Administrator à partir de la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, dans la zone de recherche, tapez **Zscaler Private Access Administrator**.
1. Sélectionnez **Zscaler Private Access Administrator** dans le panneau Résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-zscaler-private-access-administrator"></a>Configurer et tester Azure AD SSO pour Zscaler Private Access Administrator

Configurez et testez Azure AD SSO avec Zscaler Private Access Administrator pour un utilisateur de test nommé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur associé dans Zscaler Private Access Administrator.

Pour configurer et tester Azure AD SSO avec Zscaler Private Access Administrator, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification SSO Zscaler Private Access Administrator](#configure-zscaler-private-access-administrator-sso)** pour configurer les paramètres d’authentification unique côté application.
    1. **[Créer un utilisateur de test Zscaler Private Access Administrator](#create-zscaler-private-access-administrator-test-user)** pour avoir dans Zscaler Private Access Administrator un équivalent de B.Simon lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **Zscaler Private Access Administrator**, recherchez la section **Gérer**, puis sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, si vous souhaitez configurer l’application en mode lancé par le **fournisseur d’identité**, effectuez les étapes suivantes :

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<SUBDOMAIN>.private.zscaler.com/auth/metadata`

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://<SUBDOMAIN>.private.zscaler.com/auth/sso`

    c. Cliquez sur **Définir des URL supplémentaires**.

    d. Dans la zone de texte **État de relais**, tapez une valeur : `idpadminsso`

5.  Si vous souhaitez configurer l’application en mode initié par le **fournisseur de service**, procédez comme suit :

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<SUBDOMAIN>.private.zscaler.com/auth/sso`.   

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL de réponse et l’URL de connexion réels. Contactez l’[équipe de support Zscaler Private Access Administrator](https://help.zscaler.com/zpa-submit-ticket) pour obtenir ces valeurs. Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

6. Sur la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le fichier **XML de métadonnées de fédération** en fonction des options définies selon vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

7. Dans la section **Configurer Zscaler Private Access Administrator**, copiez la ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Zscaler Private Access Administrator.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Zscaler Private Access Administrator**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-zscaler-private-access-administrator-sso"></a>Configurer l’authentification unique Zscaler Private Access Administrator

1. Dans une autre fenêtre de navigateur web, connectez-vous à Zscaler Private Access Administrator en tant qu’administrateur.

2. En haut, cliquez sur **Administration** et allez à la section **AUTHENTIFICATION** puis cliquez sur **Configuration IdP**.

    ![Administrateur Zscaler Private Access Administrator](./media/zscalerprivateaccessadministrator-tutorial/admin.png)

3. Dans le coin supérieur droit, cliquez sur **Ajouter une configuration IdP**. 

    ![Ajouter IdP Zscaler Private Access Administrator](./media/zscalerprivateaccessadministrator-tutorial/add-configuration.png)

4. Sur la **page Ajouter une configuration IdP**, procédez comme suit :
 
    ![Zscaler Private Access Administrator idpselect](./media/zscalerprivateaccessadministrator-tutorial/select-file.png)

    a. Cliquez sur **Sélectionner fichier** pour charger le fichier de métadonnées téléchargé depuis Azure AD dans le champ **Charger fichier de métadonnée IdP**.

    b. Les **métadonnées IdP** sont lu à partir d’Azure AD et tous les champs sont renseignés, comme montré ci-dessous.

    ![Zscaler Private Access Administrator idpconfig](./media/zscalerprivateaccessadministrator-tutorial/metadata.png)

    c. Sélectionnez **Authentification unique** comme **Administrateur**.

    d. Sélectionnez votre domaine dans le champ **Domaines**.
    
    e. Cliquez sur **Enregistrer**.

### <a name="create-zscaler-private-access-administrator-test-user"></a>Créer un utilisateur de test Zscaler Private Access Administrator

Pour pouvoir se connecter à Zscaler Private Access Administrator, les utilisateurs Azure AD doivent être provisionnés dans Zscaler Private Access Administrator. Dans le cas de Zscaler Private Access Administrator, la configuration est une tâche manuelle.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous à votre site d’entreprise Zscaler Private Access Administrator en tant qu’administrateur.

2. En haut, cliquez sur **Administration** et allez à la section **AUTHENTIFICATION** puis cliquez sur **Configuration IdP**.

    ![Administrateur Zscaler Private Access Administrator](./media/zscalerprivateaccessadministrator-tutorial/admin.png)

3. Cliquez sur **Administrateurs** à gauche du menu.

    ![Administrateur Zscaler Private Access Administrator](./media/zscalerprivateaccessadministrator-tutorial/administrator.png)

4. Dans le coin supérieur droit, cliquez sur **Ajouter un administrateur** :

    ![Ajouter un administrateur Zscaler Private Access Administrator](./media/zscalerprivateaccessadministrator-tutorial/add-administrator.png)

5. Dans la page **Ajouter un administrateur**, procédez comme suit :

    ![Administrateur d’utilisateurs Zscaler Private Access Administrator](./media/zscalerprivateaccessadministrator-tutorial/user-admin.png)

    a. Dans la zone de texte **Nom d’utilisateur**, entrez l’adresse e-mail de l’utilisateur, par exemple BrittaSimon@contoso.com.

    b. Dans la zone de texte **Mot de passe**, tapez le mot de passe.

    c. Dans la zone de texte **Confirmer le mot de passe**, entrez le mot de passe.

    d. Sélectionnez **Rôle** pour **Zscaler Private Access Administrator**.

    e. Dans la zone de texte **E-mail**, entrez l’adresse e-mail de l’utilisateur, par exemple BrittaSimon@contoso.com.

    f. Dans la zone de texte **Téléphone**, tapez le numéro de téléphone.

    g. Dans la zone de texte **Fuseau horaire**, sélectionnez le fuseau horaire.

    h. Cliquez sur **Enregistrer**.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

#### <a name="sp-initiated"></a>Lancée par le fournisseur de services :

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion à Zscaler Private Access Administrator, d’où vous pouvez lancer le flux de connexion.  

* Accédez directement à l’URL de connexion à Zscaler Private Access Administrator pour lancer le flux de connexion.

#### <a name="idp-initiated"></a>Lancée par le fournisseur d’identité :

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors connecté automatiquement à l’instance de Zscaler Private Access Administrator pour laquelle vous avez configuré l’authentification SSO. 

Vous pouvez aussi utiliser Mes applications de Microsoft pour tester l’application dans n’importe quel mode. Si, quand vous cliquez sur la vignette Zscaler Private Access Administrator dans Mes applications, le mode Fournisseur de services est configuré, vous êtes redirigé vers la page de connexion de l’application pour lancer le flux de connexion ; s’il s’agit du mode Fournisseur d’identité, vous êtes automatiquement connecté à l’instance de Zscaler Private Access Administrator pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez configuré Zscaler Private Access Administrator, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
