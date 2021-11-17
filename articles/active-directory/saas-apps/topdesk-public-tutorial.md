---
title: 'Didacticiel : Intégration d’Azure Active Directory avec TOPdesk - Public | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et TOPdesk - Public.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/08/2021
ms.author: jeedes
ms.openlocfilehash: 54afb9b36df429702ee9a17c30fbad43c53e3b91
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132282444"
---
# <a name="tutorial-azure-active-directory-integration-with-topdesk---public"></a>Didacticiel : Intégration d’Azure Active Directory à TOPdesk - Public

Dans ce tutoriel, vous allez découvrir comment intégrer TOPdesk - Public à Azure Active Directory (Azure AD). Quand vous intégrez TOPdesk - Public à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à TOPdesk - Public.
* Permettre à vos utilisateurs de se connecter automatiquement à TOPdesk - Public avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement à TOPdesk - Public pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* TOPdesk - Public prend en charge l’authentification unique lancée par le **fournisseur de services**.

## <a name="add-topdesk---public-from-the-gallery"></a>Ajouter TOPdesk - Public à partir de la galerie

Pour configurer l’intégration de TOPdesk - Public à Azure AD, vous devez ajouter TOPdesk - Public à partir de la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **TOPdesk - Public** dans la zone de recherche.
1. Sélectionnez **TOPdesk - Public** dans le panneau de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-topdesk---public"></a>Configurer et tester l’authentification unique Azure AD pour TOPdesk - Public

Configurez et testez l’authentification unique Azure AD avec TOPdesk - Public à l’aide d’un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur TOPdesk - Public associé.

Pour configurer et tester l’authentification unique Azure AD avec TOPdesk - Public, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique TOPdesk - Public](#configure-topdesk---public-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test TOPdesk - Public](#create-topdesk---public-test-user)** pour avoir un équivalent de B.Simon dans TOPdesk - Public lié à la représentation Azure AD associée.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **TOPdesk - Public**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

4.  Dans la section **Configuration SAML de base**, si vous disposez d’un **fichier de métadonnées du fournisseur de services**, suivez les étapes ci-dessous :

    >[!NOTE]
    >Vous obtiendrez le **fichier de métadonnées de fournisseur de services** dans la section **Configurer l’authentification unique TOPdesk - Public** du tutoriel.

    a. Cliquez sur **Charger un fichier de métadonnées**.
    
    ![Charger le fichier de métadonnées](common/upload-metadata.png)

    b. Cliquez sur le **logo du dossier** pour sélectionner le fichier de métadonnées, puis cliquez sur **Charger**.

    ![choisir le fichier de métadonnées](common/browse-upload-metadata.png)

    c. Une fois le fichier de métadonnées chargé, les valeurs **Identificateur** et **URL de réponse** sont automatiquement renseignées dans la section Configuration SAML de base.

    d. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<companyname>.topdesk.net`.

    e. Dans la zone de texte **URL d’identificateur**, renseignez l’URL des métadonnées TOPdesk que vous pouvez récupérer à partir de la configuration TOPdesk. Elle doit utiliser le modèle suivant : `https://<companyname>.topdesk.net/saml-metadata/<identifier>`
    
    f. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://<companyname>.topdesk.net/tas/public/login/verify`
    
    > [!NOTE] 
    > Si les valeurs **Identificateur** et **URL de réponse** ne sont pas renseignées automatiquement, vous devez les entrer manuellement. Pour Identificateur, suivez le modèle comme indiqué ci-dessus, et vous obtenez la valeur URL de réponse à partir de la section **Configurer l’authentification unique TOPdesk - Public** plus loin dans le tutoriel. La valeur **URL de connexion** n’est pas réelle. Vous devez donc la mettre à jour avec l’URL de connexion réelle. Contactez l’[équipe de support technique TOPdesk - Public](https://my.topdesk.com/) pour obtenir la valeur. Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

5. Sur la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le fichier **XML de métadonnées de fédération** en fonction des options définies selon vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

6. Dans la section **Configurer TOPdesk - Public**, copiez la ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à TOPdesk - Public.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **TOPdesk - Public**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-topdesk---public-sso"></a>Configurer l’authentification unique TOPdesk - Public

1. Connectez-vous à votre site d'entreprise **TOPdesk - Public** en tant qu'administrateur.

2. Dans le menu **TOPdesk**, cliquez sur **Settings**.
   
    ![Paramètres](./media/topdesk-public-tutorial/menu.png "Paramètres")

3. Cliquez sur **Login Settings**.
   
    ![Paramètres de connexion](./media/topdesk-public-tutorial/login.png "Login Settings")

4. Développez le menu **Login Settings**, puis cliquez sur **General**.
   
    ![Paramètres généraux](./media/topdesk-public-tutorial/general.png "Paramètres généraux")

5. Dans la section **Public** de la section de configuration **SAML login**, procédez comme suit :
   
    ![Paramètres techniques](./media/topdesk-public-tutorial/public.png "Technical Settings")
   
    a. Cliquez sur **Download** pour télécharger le fichier de métadonnées public et l'enregistrer localement sur votre ordinateur.
   
    b. Ouvrez le fichier de métadonnées téléchargé et recherchez le nœud **AssertionConsumerService**.

    ![AssertionConsumerService](./media/topdesk-public-tutorial/service.png "AssertionConsumerService")
   
    c. Copiez la valeur **AssertionConsumerService** et collez-la dans la zone de texte **URL de réponse** dans la section **Configuration SAML de base**.      
   
6. Pour créer un fichier de certificat, procédez comme suit :
    
    ![Certificate](./media/topdesk-public-tutorial/certificate-file.png "Certificat")
    
    a. Ouvrez le fichier de métadonnées téléchargé à partir du portail Azure.
    
    b. Développez le nœud **RoleDescriptor** qui a un **xsi:type** égal à **fed:ApplicationServiceType**.
    
    c. Copiez la valeur du nœud **X509Certificate**.
    
    d. Enregistrez la valeur **X509Certificate** copiée sur votre ordinateur dans un fichier.

7. Dans la section **Public**, cliquez sur **Add**.
    
    ![Connexion SAML](./media/topdesk-public-tutorial/add.png "SAML login")

8. Dans la page de boîte de dialogue **SAML configuration assistant**, procédez comme suit :
    
    ![Assistant de configuration SAML](./media/topdesk-public-tutorial/configuration.png "SAML configuration assistant")
    
    a. Pour charger votre fichier de métadonnées téléchargé à partir du portail Azure, dans **Métadonnées de fédération**, cliquez sur **Parcourir**.

    b. Pour charger le fichier de certificat, sous **Certificate (RSA)**, cliquez sur **Browse**.

    c. Pour charger le fichier de logo que vous a fourni l'équipe de support technique TOPdesk, sous **Logo icon**, cliquez sur **Browse**.

    d. Dans la zone de texte **User name attribute** (Attribut de nom d’utilisateur), entrez `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    e. Dans la zone de texte **Display name**, tapez un nom pour votre configuration.

    f. Cliquez sur **Enregistrer**.

### <a name="create-topdesk---public-test-user"></a>Créer un utilisateur de test TOPdesk - Public

Pour pouvoir se connecter à TOPdesk - Public, les utilisateurs d’Azure AD doivent être provisionnés dans TOPdesk - Public. Dans le cas de TOPdesk - Public, l’approvisionnement est une tâche manuelle.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Pour configurer l'approvisionnement des utilisateurs, procédez comme suit :

1. Connectez-vous à votre site d’entreprise **TOPdesk - Public** en tant qu’administrateur.

2. Dans le menu en haut, cliquez sur **TOPdesk \> New \> Support Files \> Person**.
   
    ![Person](./media/topdesk-public-tutorial/files.png "Personne")

3. Dans la boîte de dialogue New User, procédez comme suit :
   
    ![New Person](./media/topdesk-public-tutorial/new.png "New Person")
   
    a. Cliquez sur l’onglet General.

    b. Dans la zone de texte **Surname**, tapez le nom de l’utilisateur, par exemple Simon.
 
    c. Sélectionnez un **Site** pour le compte.
 
    d. Cliquez sur **Enregistrer**.

> [!NOTE]
> Vous pouvez utiliser n’importe quel outil ou API de création de compte d’utilisateur, fourni par TOPdesk - Public, pour approvisionner des comptes d’utilisateur Azure AD.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion à TOPdesk - Public, d’où vous pouvez lancer le flux de connexion. 

* Accédez directement à l’URL de connexion à TOPdesk - Public pour lancer le flux de connexion.

* Vous pouvez utiliser Mes applications de Microsoft. Un clic sur la vignette TOPdesk - Public dans Mes applications vous redirige vers l’URL de connexion à TOPdesk - Public. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré TOPdesk - Public, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
