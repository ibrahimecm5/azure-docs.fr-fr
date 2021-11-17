---
title: 'Tutoriel : Intégration de l’authentification unique (SSO) Azure Active Directory à SuccessFactors | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et SuccessFactors.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/26/2020
ms.author: jeedes
ms.openlocfilehash: fb2e3eb93e1d9dbb01419d381c02c0a2717d9933
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132316885"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-successfactors"></a>Tutoriel : Intégration de l’authentification unique (SSO) Azure Active Directory à SuccessFactors

L’objectif de ce tutoriel est de vous apprendre à intégrer SuccessFactors à Azure Active Directory (Azure AD). Quand vous intégrez SuccessFactors à Azure AD, vous pouvez :

* Contrôler qui a accès à SuccessFactors
* Permettre à vos utilisateurs de se connecter automatiquement à SuccessFactors avec leur compte Azure AD
* Gérer vos comptes à un emplacement central : le Portail Azure.


## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Abonnement SuccessFactors pour lequel l’authentification unique (SSO) est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* SuccessFactors prend en charge l’authentification unique lancée par le **fournisseur de services**.

## <a name="adding-successfactors-from-the-gallery"></a>Ajout de SuccessFactors à partir de la galerie

Pour configurer l’intégration de SuccessFactors à Azure AD, vous devez ajouter SuccessFactors à partir de la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **SuccessFactors** dans la zone de recherche.
1. Sélectionnez **SuccessFactors** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.


## <a name="configure-and-test-azure-ad-sso-for-successfactors"></a>Configurer et tester l’authentification unique Azure AD pour SuccessFactors

Configurez et testez l’authentification unique Azure AD avec SuccessFactors pour un utilisateur de test nommé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur SuccessFactors associé.

Pour configurer et tester l’authentification unique Azure AD auprès de SuccessFactors, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
2. **[Configurer l’authentification unique SuccessFactors](#configure-successfactors-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test SuccessFactors](#create-successfactors-test-user)** pour avoir un équivalent de B.Simon dans SuccessFactors lié à la représentation Azure AD de l’utilisateur.
3. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **SuccessFactors**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon pour **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    a. Dans la zone de texte **URL de connexion**, tapez une URL en respectant l’un des formats suivants :

    - `https://<companyname>.successfactors.com/<companyname>`
    - `https://<companyname>.sapsf.com/<companyname>`
    - `https://<companyname>.successfactors.eu/<companyname>`
    - `https://<companyname>.sapsf.eu`

    b. Dans la zone de texte **Identificateur**, tapez une URL en utilisant l’un des formats suivants :

    - `https://www.successfactors.com/<companyname>`
    - `https://www.successfactors.com`
    - `https://<companyname>.successfactors.eu`
    - `https://www.successfactors.eu/<companyname>`
    - `https://<companyname>.sapsf.com`
    - `https://hcm4preview.sapsf.com/<companyname>`
    - `https://<companyname>.sapsf.eu`
    - `https://www.successfactors.cn`
    - `https://www.successfactors.cn/<companyname>`

    c. Dans la zone de texte **URL de réponse** , tapez une URL en respectant l’un des formats suivants :

    - `https://<companyname>.successfactors.com/<companyname>`
    - `https://<companyname>.successfactors.com`
    - `https://<companyname>.sapsf.com/<companyname>`
    - `https://<companyname>.sapsf.com`
    - `https://<companyname>.successfactors.eu/<companyname>`
    - `https://<companyname>.successfactors.eu`
    - `https://<companyname>.sapsf.eu`
    - `https://<companyname>.sapsf.eu/<companyname>`
    - `https://<companyname>.sapsf.cn`
    - `https://<companyname>.sapsf.cn/<companyname>`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de réponse, l’identificateur et l’URL de connexion réels. Contactez [l’équipe de support client de SuccessFactors](https://www.sap.com/support.html) pour obtenir ces valeurs.

4. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **Certificat (Base64)** , puis sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

6. Dans la section **Configurer SuccessFactors**, copiez la ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à SuccessFactors.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **SuccessFactors**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-successfactors-sso"></a>Configurer l’authentification unique SuccessFactors

1. Dans une autre fenêtre de navigateur web, connectez-vous à votre **Portail d’administration SuccessFactors** en tant qu’administrateur.

2. Dans **Application Security** (Sécurité des applications), accédez à la **fonctionnalité d’authentification unique**.

3. Indiquez une valeur dans le champ **Reset Token** (Réinitialiser le jeton), puis cliquez sur **Save Token** (Enregistrer le jeton) pour activer l’authentification unique SAML.

    ![Capture d’écran montrant l’onglet Application Security avec Single Sign On Features mis en évidence où vous pouvez entrer un jeton.][11]

    > [!NOTE]
    > Cette valeur est utilisée comme commutateur activé/désactivé. Si une valeur est enregistrée, l’authentification unique SAML est activée. Si aucune valeur n’est enregistrée, l’authentification unique SAML est désactivée.

4. Accédez à la capture d’écran ci-dessous et effectuez les actions suivantes :

    ![Capture d’écran montrant le volet For SAML-based SSO où vous pouvez entrer les valeurs décrites.][12]
  
    a. Sélectionnez la case d’option **SAML v2 SSO** .
  
    b. Définissez le **Nom de la partie de confiance SAML** (par exemple, émetteur SAML + nom de l’entreprise).

    c. Dans la zone de texte **URL de l’émetteur**, collez l’**Identificateur Azure AD** que vous avez copié sur le portail Azure.

    d. Sélectionnez **Assertion** pour **Require Mandatory Signature** (Exiger une signature obligatoire).

    e. Sélectionnez **Enabled** (Activé) dans le champ **Enable SAML Flag** (Activer l’indicateur SAML).

    f. Sélectionnez **No** (Non) dans le champ **Login Request Signature(SF Generated/SP/RP)** (Signature de la demande de connexion (générée par SF/fournisseur de services/fournisseur de ressources)).

    g. Sélectionnez **Browser/Post Profile** (Navigateur/Post-profilage) en tant que **profil SAML**.

    h. Sélectionnez **No** (Non) dans le champ **Enforce Certificate Valid Period** (Appliquer la période valide du certificat).

    i. Copiez le contenu du fichier de certificat téléchargé sur le Portail Azure, puis collez-le dans la zone de texte **Certificat de vérification SAML**.

    > [!NOTE] 
    > Le contenu du certificat doit comporter des balises de début et de fin de certificat.

5. Accédez à SAML V2, puis procédez comme suit :

    ![Capture d’écran montrant le volet SAML v2: SP-initiated logout où vous pouvez entrer les valeurs décrites.][13]

    a. Sélectionnez **Yes** (Oui) dans **Support SP-initiated Global Logout** (Prendre en charge la déconnexion globale initiée par le fournisseur de services).

    b. Dans la zone de texte **URL du service de déconnexion global (destination LogoutRequest)** , collez la valeur **URL de déconnexion** que vous avez copiée sur le Portail Azure.

    c. Sélectionnez **No** (Non) dans **Require sp must encrypt all NameID element** (Exiger que le fournisseur de services chiffre tous les éléments NameID).

    d. Sélectionnez **unspecified** (non spécifié) dans **NameID Format** (Format NameID).

    e. Sélectionnez **Yes** (Oui) dans **Enable sp initiated login (AuthnRequest)** (Activer la connexion initiée par le fournisseur de services (AuthnRequest)).

    f. Dan la zone de texte **Send request as Company-Wide issuer** (Envoyer la requête à l’émetteur couvrant l’ensemble de l’entreprise), collez la valeur **URL de connexion** que vous avez copiée sur le portail Azure.

6. Suivez ces étapes si vous souhaitez que les noms d’utilisateur de connexion ne respectent pas la casse.

    ![Configure Single Sign-On][29]

    a. Accédez à **Company Settings** (Paramètres de l’entreprise) (en bas).

    b. Cochez la case près de **Enable Non-Case-Sensitive Username**(Activer le non-respect de la casse pour les noms d’utilisateur).

    c. Cliquez sur **Enregistrer**.

    > [!NOTE]
    > Si vous essayez d’activer cette option, le système vérifie si cela crée un nom de connexion SAML en double. Par exemple, si le client possède les noms d’utilisateur User1 et user1. Si vous désactivez le respect de la casse, ces noms deviennent des doublons. Le système vous transmet un message d’erreur et n’active pas la fonctionnalité. Le client devra modifier l’orthographe d’un des noms d’utilisateur.

### <a name="create-successfactors-test-user"></a>Créer un utilisateur de test SuccessFactors

Pour pouvoir se connecter à SuccessFactors, les utilisateurs d’Azure AD doivent être attribués dans SuccessFactors. Dans le cas de SuccessFactors, l’approvisionnement est une tâche manuelle.

Pour créer des utilisateurs dans SuccessFactors, vous devez contacter [l’équipe de support technique SuccessFactors](https://www.sap.com/support.html).

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Cette opération redirige vers l’URL de connexion SuccessFactors, à partir de laquelle vous pouvez lancer le processus de connexion. 

* Accédez directement à l’URL de connexion SuccessFactors pour lancer le processus de connexion.

* Vous pouvez utiliser Mes applications de Microsoft. Un clic sur la vignette SuccessFactors dans Mes applications vous redirige vers l’URL de connexion SuccessFactors. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré SuccessFactors, vous pouvez appliquer des contrôles de session, ce qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Les contrôles de session sont étendus à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).

<!--Image references-->

[11]: ./media/successfactors-tutorial/tutorial_successfactors_07.png
[12]: ./media/successfactors-tutorial/tutorial_successfactors_08.png
[13]: ./media/successfactors-tutorial/tutorial_successfactors_09.png
[29]: ./media/successfactors-tutorial/tutorial_successfactors_10.png
