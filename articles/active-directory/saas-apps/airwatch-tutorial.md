---
title: 'Tutoriel : Intégration d’Azure Active Directory dans AirWatch | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et AirWatch.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/20/2021
ms.author: jeedes
ms.openlocfilehash: 8144f76478b8acca9bb6b3207bd249cbf96ba503
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132280694"
---
# <a name="tutorial-integrate-airwatch-with-azure-active-directory"></a>Tutoriel : Intégrer AirWatch à Azure Active Directory

Dans ce tutoriel, vous allez apprendre à intégrer AirWatch à Azure Active Directory (Azure AD). Quand vous intégrez AirWatch à Azure AD, vous pouvez :

* Contrôler qui a accès à AirWatch dans Azure AD.
* Permettre à vos utilisateurs de se connecter automatiquement à AirWatch avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :
 
* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement AirWatch pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test. 

* AirWatch prend en charge l’authentification unique lancée par le **fournisseur de services**

## <a name="add-airwatch-from-the-gallery"></a>Ajouter AirWatch à partir de la galerie

Pour configurer l’intégration d’AirWatch dans Azure AD, vous devez ajouter AirWatch à partir de la galerie dans votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **AirWatch** dans la zone de recherche.
1. Sélectionnez **AirWatch** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-airwatch"></a>Configurer et tester l’authentification unique Azure AD pour AirWatch

Configurez et testez l’authentification unique Azure AD avec AirWatch pour un utilisateur de test nommé **B. Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur AirWatch associé.

Pour configurer et tester l’authentification unique Azure AD avec AirWatch, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique AirWatch](#configure-airwatch-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test AirWatch](#create-airwatch-test-user)** pour avoir un équivalent de B.Simon dans AirWatch lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, dans la page d’intégration de l’application **AirWatch**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon pour **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la boîte de dialogue **Configuration SAML de base**, entrez les valeurs pour les champs suivants :

    1. Dans la zone de texte **URL de connexion**, saisissez une URL au format suivant : `https://<subdomain>.awmdm.com/AirWatch/Login?gid=companycode`

    1. Dans la zone de texte **Identificateur (ID d’entité)** , saisissez la valeur comme suit : `AirWatch`

    > [!NOTE]
    > Cette valeur n’est pas la valeur réelle. Mettez à jour la valeur avec l’URL de connexion réelle. Contactez l’[équipe de support technique AirWatch](https://www.air-watch.com/company/contact-us/) pour obtenir cette valeur. Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. L’application AirWatch s’attend à ce que les assertions SAML soient dans un format spécifique. Configurez les revendications suivantes pour cette application. Vous pouvez gérer les valeurs de ces attributs à partir de la section **Attributs utilisateur** sur la page d’intégration des applications. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur le bouton **Modifier** pour ouvrir la boîte de dialogue **Attributs utilisateur**.

    ![image](common/edit-attribute.png)

1. Dans la section **Revendications des utilisateurs** de la boîte de dialogue **Attributs utilisateur**, modifiez les revendications en utilisant l’icône **Modifier** ou ajoutez des revendications en utilisant l’option **Ajouter une nouvelle revendication** pour configurer l’attribut de jeton SAML comme sur l’image ci-dessus et procédez comme suit :

    | Name |  Attribut source|
    |---------------|----------------|
    | Identificateur d’utilisateur | user.userprincipalname |
    | | |

    a. Cliquez sur le bouton **Ajouter une nouvelle revendication** pour ouvrir la boîte de dialogue **Gérer les revendications des utilisateurs**.

    b. Dans la zone de texte **Attribut**, indiquez le nom d’attribut pour cette ligne.

    c. Laissez le champ **Espace de noms** vide.

    d. Sélectionnez Source comme **Attribut**.

    e. Dans la liste **Attribut de la source**, tapez la valeur d’attribut indiquée pour cette ligne.

    f. Cliquez sur **OK**

    g. Cliquez sur **Enregistrer**.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **XML de métadonnées de fédération** et sélectionnez **Télécharger** pour télécharger le fichier XML de métadonnées et l’enregistrer sur votre ordinateur.

   ![Lien Téléchargement de certificat](common/metadataxml.png)

1. Dans la section **Configurer AirWatch**, copiez l’URL ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B. Simon à utiliser l’authentification unique Azure en lui accordant l’accès à AirWatch.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **AirWatch**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

### <a name="configure-airwatch-sso"></a>Configurer l’authentification unique AirWatch

1. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise AirWatch en tant qu’administrateur.

1. Dans la page de paramètres : Sélectionnez **Settings > Enterprise Integration > Directory Services** (Paramètres > Intégration d’entreprise > Services d’annuaire).

   ![Paramètres](./media/airwatch-tutorial/services.png "Paramètres")

1. Cliquez sur l’onglet **User** (Utilisateur) puis, dans la zone de texte **Base DN** (Nom unique de base), tapez votre nom de domaine et cliquez sur **Save** (Enregistrer).

   ![Capture d’écran mettant en évidence la zone de texte Base DN.](./media/airwatch-tutorial/user.png "Utilisateur")

1. Cliquez sur l’onglet **Server** .

   ![Serveur](./media/airwatch-tutorial/directory.png "Serveur")

1. Dans la section **LDAP**, effectuez les étapes suivantes :

    ![Capture d’écran montrant les modifications que vous devez apporter à la section LDAP.](./media/airwatch-tutorial/ldap.png "LDAP")   

    a. Pour **Directory Type**, sélectionnez **None**.

    b. Sélectionnez **Use SAML For Authentication**.

1. Dans la section **SAML 2.0**, pour charger le certificat téléchargé, cliquez sur **Upload**.

    ![Charger](./media/airwatch-tutorial/uploads.png "Télécharger")

1. Dans la section **Request** , procédez comme suit :

    ![Section Request](./media/airwatch-tutorial/request.png "Requête")  

    a. Pour **Request Binding Type**, sélectionnez **POST**.

    b. Dans la boîte de dialogue **Configurer l’authentification unique sur AirWatch** du portail Azure, copiez la valeur **URL de connexion**, puis collez-la dans la zone de texte **Identity Provider Single Sign On URL** (URL d’authentification unique du fournisseur d’identité).

    c. Pour **NameID Format**, sélectionnez **Email Address**.

    d. Pour **Authentication Request Security**, sélectionnez **None**.

    e. Cliquez sur **Enregistrer**.

1. Cliquez à nouveau sur l’onglet **User** .

    ![Utilisateur](./media/airwatch-tutorial/users.png "Utilisateur")

1. Dans la section **Attribute** , procédez comme suit :

    ![Attribut](./media/airwatch-tutorial/attributes.png "Attribut")

    a. Dans la zone de texte **Object Identifier** (Identificateur de l’objet), tapez `http://schemas.microsoft.com/identity/claims/objectidentifier`.

    b. Dans la zone de texte **Username** (Nom d’utilisateur), tapez `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    c. Dans la zone de texte **Display Name** (Nom d’affichage), tapez `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.

    d. Dans la zone de texte **First Name** (Prénom), tapez `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.

    e. Dans la zone de texte **Last Name** (Nom), tapez `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`.

    f. Dans la zone de texte **Email** (E-mail), tapez `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    g. Cliquez sur **Enregistrer**.

### <a name="create-airwatch-test-user"></a>Créer un utilisateur de test AirWatch

Pour pouvoir se connecter à AirWatch, les utilisateurs d’Azure AD doivent être provisionnés dans AirWatch. Dans le cas d’AirWatch, cet approvisionnement est une tâche manuelle.

**Pour configurer l'approvisionnement des utilisateurs, procédez comme suit :**

1. Connectez-vous à votre site d’entreprise **AirWatch** en tant qu’administrateur.

2. Dans le volet de navigation situé sur le côté gauche, cliquez sur **Accounts**, puis sur **Users**.
  
   ![Utilisateurs](./media/airwatch-tutorial/accounts.png "Utilisateurs")

3. Dans le menu **Users**, cliquez sur **List View**, puis sur **Add > Add User**.
  
   ![Capture d’écran mettant en évidence les boutons Add et Add User.](./media/airwatch-tutorial/add.png "Ajouter un utilisateur")

4. Dans la boîte de dialogue **Add / Edit User** , procédez comme suit :

   ![Ajouter un utilisateur](./media/airwatch-tutorial/save.png "Ajouter un utilisateur")

   a. Tapez le nom d’utilisateur, le mot de passe, la confirmation du mot de passe, le prénom, le nom et l’adresse e-mail du compte Azure Active Directory valide que vous souhaitez approvisionner dans les champs correspondants, à savoir **Username**, **Password**, **Confirm Password**, **First Name**, **Last Name** et **Email Address**.

   b. Cliquez sur **Enregistrer**.

> [!NOTE]
> Vous pouvez utiliser tout autre outil ou n’importe quelle API de création de compte d’utilisateur fournis par AirWatch pour provisionner des comptes d’utilisateurs Azure AD.

### <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion à AirWatch, où vous pouvez lancer le flux de connexion. 

* Accédez directement à l’URL de connexion à AirWatch et lancez le flux de connexion.

* Vous pouvez utiliser Mes applications de Microsoft. Quand vous cliquez sur la vignette AirWatch dans Mes applications, vous êtes redirigé vers l’URL de connexion à AirWatch. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez configuré AirWatch, vous pouvez appliquer le contrôle de session, qui protège l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
