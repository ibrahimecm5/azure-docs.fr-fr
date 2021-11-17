---
title: "Tutoriel : Intégration d'Azure Active Directory à Wdesk | Microsoft Docs"
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Wdesk.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/08/2021
ms.author: jeedes
ms.openlocfilehash: 84fffb80a95bb98b01f636924e7bc7c510e7b616
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132313372"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-wdesk"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory à Wdesk

Dans ce tutoriel, vous allez découvrir comment intégrer Wdesk à Azure Active Directory (Azure AD). Quand vous intégrez Wdesk à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Wdesk.
* Permettre aux utilisateurs de se connecter automatiquement à Wdesk avec leur compte Azure AD
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Wdesk pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Wdesk prend en charge l’authentification unique lancée par le **fournisseur de services** et par le **fournisseur d’identité**.

## <a name="add-wdesk-from-the-gallery"></a>Ajouter Wdesk à partir de la galerie

Pour configurer l’intégration de Wdesk avec Azure AD, vous devez ajouter Wdesk à partir de la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Wdesk** dans la zone de recherche.
1. Sélectionnez **Wdesk** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-wdesk"></a>Configurer et tester l’authentification unique Azure AD pour Wdesk

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD auprès de Wdesk, avec un utilisateur de test appelé **Britta Simon**.
Pour que l’authentification unique fonctionne, une relation entre l’utilisateur Azure AD et l’utilisateur Wdesk associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec Wdesk, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Wdesk](#configure-wdesk-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test Wdesk](#create-wdesk-test-user)** pour avoir un équivalent de B.Simon dans Wdesk lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans la page d’intégration de l’application **Wdesk** du portail Azure, recherchez la section **Gérer**, puis sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, si vous souhaitez configurer l’application en mode lancé par le **fournisseur d’identité**, effectuez les étapes suivantes :

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<subdomain>.wdesk.com/auth/saml/sp/metadata/<instancename>`

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://<subdomain>.wdesk.com/auth/saml/sp/consumer/<instancename>`

5. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<subdomain>.wdesk.com/auth/login/saml/<instancename>`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL de réponse et l’URL de connexion réels. Vous obtenez ces valeurs depuis le portail WDesk lorsque vous configurez l’authentification unique.

4. Sur la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le fichier **XML de métadonnées de fédération** en fonction des options définies selon vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

6. Dans la section **Configurer Wdesk**, copiez la ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Wdesk.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Wdesk**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-wdesk-sso"></a>Configurer l’authentification unique Wdesk

1. Ouvrez une autre fenêtre de navigateur web, puis connectez-vous à Wdesk en tant qu’administrateur de la sécurité.

1. En bas à gauche, cliquez sur **administrateur** et sélectionnez **Administrateur de compte** :
 
    ![Capture d’écran montrant l’élément Account Admin sélectionné à partir du menu Admin.](./media/wdesk-tutorial/account.png)

1. Dans Administrateur Wdesk, accédez à **Sécurité**, puis **SAML** > **Paramètres SAML** :

    ![Capture d’écran montrant l’élément SAML Settings sélectionné à partir de l’onglet SAML.](./media/wdesk-tutorial/settings.png)

1. Sous **SAML User ID Settings** (Paramètres d’ID utilisateur SAML), cochez **SAML User ID is Wdesk Username** (L’ID utilisateur SAML est le nom d’utilisateur Wdesk).

    ![Capture d’écran montrant SAML User ID Settings où vous pouvez sélectionner SAML User ID is Wdesk Username.](./media/wdesk-tutorial/wdesk-username.png)

4. Dans **Paramètres généraux**, cochez l’option **Activer l’authentification unique SAML** :

    ![Capture d’écran montrant Edit SAML Settings où vous pouvez sélectionner Enable SAML Single Sign-On.](./media/wdesk-tutorial/user-settings.png)

5. Dans **Détails sur le fournisseur de services**, procédez comme suit :

    ![Capture d’écran montrant Service Provider Details où vous pouvez entrer les valeurs décrites.](./media/wdesk-tutorial/service-provider.png)

    1. Copiez l’**URL de connexion** et collez-la dans la zone de texte **URL d’authentification** sur le portail Azure.

    1. Copiez l’**URL de métadonnée** et collez-la dans la zone de texte **Identificateur** sur le portail Azure.

    1. Copiez l’**URL de consommateur** et collez-la dans la zone de texte **URL de réponse** sur le portail Azure.

    1. Cliquez sur **Enregistrer** sur le portail Azure pour enregistrer les modifications.      

1. Cliquez sur **Configurer les paramètres du fournisseur d’identité** pour ouvrir la boîte de dialogue **Modifier les paramètres du fournisseur d’identité**. Cliquez sur **Choisir un fichier** pour trouver le fichier **Metadata.xml** que vous avez enregistré depuis le portail, puis chargez-le.
    
    ![Capture d’écran montrant Edit IdP Settings où vous pouvez charger des métadonnées.](./media/wdesk-tutorial/metadata.png)
  
1. Cliquez sur **Save changes**.

    ![Capture d’écran montrant le bouton Save changes.](./media/wdesk-tutorial/save.png)

### <a name="create-wdesk-test-user"></a>Créer un utilisateur de test Wdesk

Pour se connecter à Wdesk, les utilisateurs d’Azure AD doivent être provisionnés dans Wdesk. Dans Wdesk, l’approvisionnement est une tâche manuelle.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous à Wdesk en tant qu’administrateur de la sécurité.

2. Accédez à **Administrateur** > **Compte Administrateur**.

     ![Capture d’écran montrant l’élément Account Admin sélectionné à partir du menu Admin.](./media/wdesk-tutorial/account.png)

3. Dans **Personnes**, cliquez sur **Membres**.

4. Cliquez maintenant sur **Ajouter membre** pour ouvrir la boîte de dialogue **Ajouter membre**. 
   
    ![Capture d’écran montrant l’onglet Members où vous pouvez sélectionner Add Member.](./media/wdesk-tutorial/create-user-1.png)  

5. Dans la zone de texte **Utilisateur**, entrez un nom d’utilisateur, par exemple b.simon@contoso.com, puis cliquez sur le bouton **Continuer**.

    ![Capture d’écran montrant la boîte de dialogue Add Member où vous pouvez entrer un utilisateur.](./media/wdesk-tutorial/create-user-3.png)

6.  Renseignez les détails comme indiqué ci-dessous :
  
    ![Capture d’écran montrant la boîte de dialogue Add Member dans laquelle vous pouvez ajouter des informations de base pour un utilisateur.](./media/wdesk-tutorial/create-user-4.png)
 
    a. Dans la zone de texte **E-mail**, entrez une adresse e-mail d’utilisateur, par exemple b.simon@contoso.com.

    b. Dans la zone de texte **First Name** (Prénom), tapez le prénom de l’utilisateur, par exemple **B**.

    c. Dans la zone de texte **Nom**, entrez le nom de l’utilisateur, par exemple **Simon**.

7. Cliquez sur **Enregistrer membre**.  

    ![Capture d’écran montrant Send welcome email avec le bouton Save Member.](./media/wdesk-tutorial/create-user-5.png)

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

#### <a name="sp-initiated"></a>Lancée par le fournisseur de services :

* Cliquez sur **Tester cette application** dans le portail Azure. Cette opération redirige vers l’URL de connexion Wdesk, où vous pouvez lancer le flux de connexion.  

* Accédez directement à l’URL de connexion Wdesk pour lancer le flux de connexion.

#### <a name="idp-initiated"></a>Lancée par le fournisseur d’identité :

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors connecté automatiquement à l’instance Wdesk pour laquelle vous avez configuré l’authentification unique. 

Vous pouvez aussi utiliser Mes applications de Microsoft pour tester l’application dans n’importe quel mode. Si, quand vous cliquez sur la vignette Wdesk dans Mes applications, le mode Fournisseur de services est configuré, vous êtes redirigé vers la page de connexion de l’application pour lancer le flux de connexion ; s’il s’agit du mode Fournisseur d’identité, vous êtes automatiquement connecté à l’instance Wdesk pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré Wdesk, vous pouvez appliquer des contrôles de session, qui protège l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
