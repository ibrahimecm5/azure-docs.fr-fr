---
title: 'Tutoriel : Intégration de l’authentification unique Azure AD à Zoho One'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Zoho One.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/14/2021
ms.author: jeedes
ms.openlocfilehash: 465719da319bb5b266cb6cdb9d2f33b626fd97da
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132303565"
---
# <a name="tutorial-azure-ad-sso-integration-with-zoho-one"></a>Tutoriel : Intégration de l’authentification unique Azure AD à Zoho One

Dans ce tutoriel, vous allez voir comment intégrer Zoho One à Azure Active Directory (Azure AD). Quand vous intégrez Zoho One à Azure AD, vous pouvez :

* Contrôler qui dans Azure AD a accès à Zoho One.
* Permettre à vos utilisateurs de se connecter automatiquement à Zoho One avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD avec Zoho One, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un [compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Zoho One pour lequel l’authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Zoho One prend en charge l’authentification unique lancée par le **fournisseur de services** et par le **fournisseur d’identité**.

> [!NOTE]
> L’identificateur de cette application étant une valeur de chaîne fixe, une seule instance peut être configurée dans un locataire.

## <a name="add-zoho-one-from-the-gallery"></a>Ajouter Zoho One à partir de la galerie

Pour configurer l’intégration de Zoho One avec Azure AD, vous devez ajouter Zoho One, disponible dans la galerie, à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Zoho One** dans la zone de recherche.
1. Sélectionnez **Zoho One** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-zoho-one"></a>Configurer et tester l’authentification unique (SSO) Azure AD pour Zoho One

Configurez et testez l’authentification unique Azure AD pour Zoho One à l’aide d’un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur associé dans Zoho One.

Pour configurer et tester l’authentification unique Azure AD avec Zoho One, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Zoho One](#configure-zoho-one-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test Zoho One](#create-zoho-one-test-user)** pour avoir dans Zoho One un équivalent de B.Simon qui soit associé à la représentation de l’utilisateur Azure AD.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Sur le portail Azure, accédez à la page d’intégration de l’application **Zoho One**, recherchez la section **Gérer**, puis sélectionnez **authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, si vous souhaitez configurer l’application en mode lancé par le **fournisseur d’identité**, effectuez les étapes suivantes :

    a. Dans la zone de texte **Identificateur**, tapez la valeur : `one.zoho.com`

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://accounts.zoho.com/samlresponse/<saml-identifier>`

    > [!NOTE]
    > La valeur **URL de réponse** ci-dessus n’est pas une valeur réelle. Vous obtenez la valeur `<saml-identifier>` de l’étape 4 de la section **Configurer l’authentification unique pour Zoho One**, dont la procédure est expliquée plus loin dans le tutoriel.

    c. Cliquez sur **Définir des URL supplémentaires**.

    d. Dans la zone de texte **État de relais**, tapez l’URL : `https://one.zoho.com`

5. Si vous souhaitez configurer l’application en mode initié par le **fournisseur de service**, procédez comme suit :

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://accounts.zoho.com/samlauthrequest/<domain_name>?serviceurl=https://one.zoho.com` 

    > [!NOTE] 
    > La valeur d’**URL de connexion** ci-dessus n’est pas une valeur réelle. Vous remplacerez cette valeur par l’URL de connexion de la section **Configurer l’authentification unique Zoho One**. La procédure est expliquée plus loin dans le tutoriel. 

6. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (Base64)** en fonction des options définies par rapport à vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

7. Dans la section **Configurer Zoho One**, copiez la ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant un accès à Zoho One.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Zoho One**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-zoho-one-sso"></a>Configurer l’authentification unique à Zoho One

1. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Zoho One en tant qu’administrateur.

2. Sous l’onglet **Organisation**, cliquez sur **Configurer** sous **Authentification SAML**.

    ![Organisation Zoho One](./media/zoho-one-tutorial/set-up.png)

3. Dans la page qui s’affiche, effectuez les étapes suivantes :

    ![Connexion à Zoho One](./media/zoho-one-tutorial/save.png)

    a. Dans la zone de texte **Sign-in URL** (URL de connexion), collez la valeur **Login URL** (URL de connexion) que vous avez copiée à partir du portail Azure.

    b. Dans la zone de texte **Sign-out URL** (URL de déconnexion), collez la valeur **Logout URL** (URL de déconnexion) que vous avez copiée à partir du portail Azure.

    c. Cliquez sur **Parcourir** pour charger le certificat (**Certificat (Base64)** ) que vous avez téléchargé à partir du portail Azure.

    d. Cliquez sur **Enregistrer**.

4. Après avoir enregistré la configuration de l’authentification SAML, copiez la valeur de **SAML-Identifier** (Identificateur SAML), puis ajoutez-le à l’**URL de réponse** située dans `<saml-identifier>` (au format `https://accounts.zoho.com/samlresponse/one.zoho.com`), puis collez la valeur générée dans la zone de texte **URL de réponse** sous la section **Configuration SAML de base**.

    ![SAML Zoho One](./media/zoho-one-tutorial/saml-identifier.png)

5. Accédez à l’onglet **Domains**, puis cliquez sur **Ajouter un domaine**.

    ![Domaine Zoho One](./media/zoho-one-tutorial/add-domain.png)

6. Dans la page **Ajouter un utilisateur**, effectuez les étapes suivantes :

    ![Ajouter un domaine Zoho One](./media/zoho-one-tutorial/add-domain-name.png)

    a. Dans la zone de texte **Nom de domaine**, tapez un domaine comme contoso.com.

    b. Cliquez sur **Ajouter**.

    >[!Note]
    >Après avoir ajouté le domaine, effectuez [ces](https://www.zoho.com/one/help/admin-guide/domain-verification.html) étapes pour vérifier votre domaine. Une fois le domaine vérifié, entrez le nom de domaine dans la zone de texte **URL de connexion** de la section **Configuration SAML de base** du portail Azure.

### <a name="create-zoho-one-test-user"></a>Créer un utilisateur de test Zoho One

Pour que les utilisateurs Azure AD puissent se connecter à Zoho One, ils doivent être attribués dans Zoho One. Dans Zoho One, l’attribution est une tâche manuelle.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous à Zoho One en tant qu’administrateur de la sécurité.

2. Sous l’onglet **Utilisateur**, cliquez sur **logo de l’utilisateur**.

    ![Utilisateur Zoho One](./media/zoho-one-tutorial/user.png)

3. Dans la page **Add user** (Ajouter un utilisateur), procédez comme suit :

    ![Ajouter un utilisateur Zoho One](./media/zoho-one-tutorial/add-user.png)
    
    a. Dans la zone de texte **Nom**, entrez le nom d’un utilisateur, par exemple **Britta Simon**.
    
    b. Dans la zone de texte **Adresse e-mail**, entrez l’adresse e-mail de l’utilisateur, par exemple brittasimon@contoso.com.

    >[!Note]
    >Sélectionnez votre domaine vérifié dans la liste des domaines.

    c. Cliquez sur **Add**.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

#### <a name="sp-initiated"></a>Lancée par le fournisseur de services :

* Cliquez sur **Tester cette application** dans le portail Azure. Cette opération effectue une redirection vers l’URL de connexion à Zoho One, d’où vous pouvez lancer le flux de connexion.  

* Accédez directement à l’URL de connexion à Zoho One, puis lancez le flux de connexion.

#### <a name="idp-initiated"></a>Lancée par le fournisseur d’identité :

* Dans le portail Azure, cliquez sur **Tester cette application**. Vous êtes alors automatiquement connecté à l’instance Zoho One pour laquelle vous avez configuré l’authentification unique. 

Vous pouvez aussi utiliser Mes applications de Microsoft pour tester l’application dans n’importe quel mode. Si, quand vous cliquez sur la vignette Zoho One dans Mes applications, le mode Fournisseur de services est configuré, vous êtes redirigé vers la page de connexion de l’application pour lancer le flux de connexion. S’il s’agit du mode Fournisseur d’identité, vous êtes automatiquement connecté à l’instance Zoho One pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez configuré Zoho One, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
