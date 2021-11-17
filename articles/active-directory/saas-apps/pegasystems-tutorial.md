---
title: 'Tutoriel : Intégration de l’authentification unique Azure AD à Pega Systems'
description: Dans ce tutoriel, vous allez découvrir comment configurer l’authentification unique entre Azure Active Directory et Pega Systems.
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
ms.openlocfilehash: 464e05a715f5fa221659d37e35901e945ecc87d6
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132300117"
---
# <a name="tutorial-azure-ad-sso-integration-with-pega-systems"></a>Tutoriel : Intégration de l’authentification unique Azure AD à Pega Systems

Dans ce didacticiel, vous allez apprendre à intégrer Pega Systems à Azure Active Directory (Azure AD). Lorsque vous intégrez Pega Systems à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Pega Systems.
* Permettre à vos utilisateurs de se connecter automatiquement à Pega Systems avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Pega Systems pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* Pega Systems prend en charge l’authentification unique lancée par le fournisseur de services et le fournisseur d’identité.

## <a name="add-pega-systems-from-the-gallery"></a>Ajouter Pega Systems à partir de la galerie

Pour configurer l’intégration de Pega Systems à Azure AD, vous devez ajouter Pega Systems à partir de la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, entrez **Pega Systems** dans la zone de recherche.
1. Sélectionnez **Pega Systems** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-pega-systems"></a>Configurer et tester l’authentification unique Azure AD pour Pega Systems

Configurez et testez l’authentification unique Azure AD avec Pega Systems pour un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur associé dans Pega Systems.

Pour configurer et tester l’authentification unique Azure AD avec Pega Systems, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Pega Systems](#configure-pega-systems-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test Pega Systems](#create-pega-systems-test-user)** pour avoir un équivalent de B.Simon dans Pega Systems lié à la représentation Azure AD associée.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, dans la page d’intégration de l’application **Pega Systems**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la boîte de dialogue **Configuration SAML de base**, si vous souhaitez configurer l’application en mode lancé par le fournisseur d’identité, procédez comme suit.

    1. Dans la zone de texte **Identificateur**, tapez une URL au format suivant :

       `https://<customername>.pegacloud.io:443/prweb/sp/<instanceID>`

    1. Dans la zone **URL de réponse**, tapez une URL au format suivant : 

       `https://<customername>.pegacloud.io:443/prweb/PRRestService/WebSSO/SAML/AssertionConsumerService`

5. Si vous souhaitez configurer l’application en mode lancé par le fournisseur de services, sélectionnez **Définir des URL supplémentaires**, puis procédez comme suit.

    1. Dans la zone **URL de connexion**, entrez la valeur de l’URL de connexion.

    1. Dans la zone **État de relais**, entrez une URL au format suivant : `https://<customername>.pegacloud.io/prweb/sso`

    > [!NOTE]
    > Les valeurs fournies ici sont des espaces réservés. Vous devez utiliser l’identificateur, l’URL de réponse, l’URL de connexion et l’URL d’état de relais exacts. Vous pouvez obtenir les valeurs de l’identificateur et de l’URL de réponse dans une application Pega, comme expliqué plus loin dans ce tutoriel. Pour obtenir la valeur d’état de relais, contactez le [équipe du support technique Pega Systems](https://www.pega.com/contact-us). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

6. L’application Pega Systems requiert les assertions SAML dans un format spécifique. Pour les obtenir au format qui convient, vous devez ajouter des mappages d’attributs personnalisés à la configuration d’attributs de jeton SAML. La capture d’écran suivante montre les attributs par défaut. Sélectionnez l’icône **Modifier** pour ouvrir la boîte de dialogue **Attributs d’utilisateur** :

    ![Attributs utilisateur](common/edit-attribute.png)

7. En plus des attribués affichés dans la capture d’écran précédente, l’application Pega Systems requiert quelques attributs supplémentaires à repasser dans la réponse SAML. Dans la section **Revendications des utilisateurs** de la boîte de dialogue **Attributs utilisateur**, procédez comme suit pour ajouter ces attributs de jeton SAML :
    
   - `uid`
   - `cn`
   - `mail`
   - `accessgroup`  
   - `organization`  
   - `orgdivision`
   - `orgunit`
   - `workgroup`  
   - `Phone`

    > [!NOTE]
    > Ces valeurs sont spécifiques à votre organisation. Fournissez les valeurs appropriées.

    1. Sélectionnez **Ajouter une nouvelle revendication** pour ouvrir la boîte de dialogue **Gérer les revendications des utilisateurs** :

    ![Sélectionner Ajouter une nouvelle revendication](common/new-save-attribute.png)

    ![Boîte de dialogue Gérer les revendications des utilisateurs](common/new-attribute-details.png)

    1. Dans la zone de texte **Nom**, entrez le nom d’attribut affiché pour cette ligne.

    1. Laissez la zone de texte **Espace de noms** vide.

    1. Pour la **Source**, sélectionnez **Attribut**.

    1. Dans la liste **Attribut de la source**, sélectionnez la valeur d’attribut indiquée pour cette ligne.

    1. Sélectionnez **OK**.

    1. Sélectionnez **Enregistrer**.

8. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, sélectionnez le lien **Télécharger** en regard de **XML de métadonnées de fédération**, selon vos besoins, puis enregistrez le certificat sur votre ordinateur :

    ![Lien de téléchargement du certificat](common/metadataxml.png)

9. Dans la section **Configurer Pega Systems**, copiez la ou les URL appropriées, selon vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Pega Systems.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Pega Systems**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-pega-systems-sso"></a>Configurer l’authentification unique Pega Systems

1. Pour configurer l’authentification unique du côté de **Pega Systems**, connectez-vous au portail Pega avec un compte administrateur dans une autre fenêtre de navigateur.

1. Sélectionnez **Créer** > **SysAdmin** > **Service d’authentification** :

    ![Sélectionner Service d’authentification](./media/pegasystems-tutorial/admin.png)
    
1. Effectuez les étapes suivantes sur l’écran **Créer le service d’authentification**.

    ![Écran Créer le service d'authentification](./media/pegasystems-tutorial/service.png)

    1. Dans la liste **Type**, sélectionnez **SAML 2.0**.

    1. Dans la zone **Nom**, entrez un nom (par exemple **Authentification unique Azure AD**).

    1. Dans la zone **Brève description**, entrez une description.  

    1. Sélectionnez **Créer et ouvrir**.
    
1. Dans la section **Informations sur le fournisseur d’identité (IdP)** , cliquez sur **Importer les métadonnées IdP** et explorez le fichier de métadonnées que vous avez téléchargé depuis le portail Azure. Cliquez sur **Envoyer** pour charger les métadonnées :

    ![Section d'informations sur le fournisseur d'identité (IdP)](./media/pegasystems-tutorial/metadata.png)
    
    Vous renseignerez ainsi les données IdP comme illustré ici :

    ![Données IdP importées](./media/pegasystems-tutorial/data.png)
    
1. Dans la section **Paramètres du fournisseur de services**, effectuez les étapes suivantes.

    ![Paramètres du fournisseur de services](./media/pegasystems-tutorial/settings.png)

    1. Copiez la valeur **Entity Identification**, puis collez-la dans la zone **Identificateur** de la section **Configuration SAML de base** du portail Azure.

    1. Copiez la valeur **Emplacement de l'URL Assertion Consumer Service (ACS)** , puis collez-la dans la zone **URL de réponse** de la section **Configuration SAML de base** du portail Azure.

    1. Sélectionnez **Disable request signing** (Désactiver la signature de requête).

1. Sélectionnez **Enregistrer**.

### <a name="create-pega-systems-test-user"></a>Créer un utilisateur de test Pega Systems

Ensuite, vous devez créer un utilisateur nommé Britta Simon dans Pega Systems. Collaborez avec l'[équipe du support technique Pega Systems](https://www.pega.com/contact-us) pour créer des utilisateurs.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

#### <a name="sp-initiated"></a>Lancée par le fournisseur de services :

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion à Pega Systems, à partir de laquelle vous pouvez lancer le flux de connexion.  

* Accédez directement à l’URL de connexion à Pega Systems pour lancer le flux de connexion.

#### <a name="idp-initiated"></a>Lancée par le fournisseur d’identité :

* Dans le portail Azure, cliquez sur **Tester cette application**. Vous êtes alors automatiquement connecté à l’instance de Pega Systems pour laquelle vous avez configuré l’authentification unique. 

Vous pouvez aussi utiliser Mes applications de Microsoft pour tester l’application dans n’importe quel mode. Si, quand vous cliquez sur la vignette Pega Systems dans Mes applications, le mode Fournisseur de services est configuré, vous êtes redirigé vers la page de connexion de l’application pour lancer le flux de connexion ; s’il s’agit du mode Fournisseur d’identité, vous êtes automatiquement connecté à l’instance de Pega Systems pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré Pega Systems, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
