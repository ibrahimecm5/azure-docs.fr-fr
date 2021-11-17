---
title: "Tutoriel : Intégration d'Azure Active Directory à iLMS | Microsoft Docs"
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et iLMS.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/08/2021
ms.author: jeedes
ms.openlocfilehash: 3c50aa60a67fb7f4508f17f782cdd02bff35241b
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132317436"
---
# <a name="tutorial-integrate-ilms-with-azure-active-directory"></a>Tutoriel : Intégrer iLMS à Azure Active Directory

Dans ce tutoriel, vous allez apprendre à intégrer iLMS à Azure Active Directory (Azure AD). Quand vous intégrez iLMS à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à iLMS.
* Permettre à vos utilisateurs de se connecter automatiquement à iLMS avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement iLMS pour lequel l’authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* iLMS prend en charge l’authentification unique lancée par **le fournisseur de services et le fournisseur d’identité**.

> [!NOTE]
> L’identificateur de cette application étant une valeur de chaîne fixe, une seule instance peut être configurée dans un locataire.

## <a name="add-ilms-from-the-gallery"></a>Ajouter iLMS à partir de la galerie

Pour configurer l’intégration d’iLMS avec Azure AD, vous devez ajouter iLMS à partir de la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **iLMS** dans la zone de recherche.
1. Sélectionnez **iLMS** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-ilms"></a>Configurer et tester l’authentification unique Azure AD pour iLMS

Configurez et testez l’authentification unique Azure AD avec iLMS pour un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur iLMS associé.

Pour configurer et tester l’authentification unique Azure AD avec iLMS, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique iLMS](#configure-ilms-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test iLMS](#create-ilms-test-user)** pour avoir un équivalent de B.Simon dans iLMS associé à sa représentation dans Azure AD.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **iLMS**, recherchez la section **Gérer**, puis sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon pour **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la page **Configuration SAML de base**, si vous souhaitez configurer l’application en mode lancé par le **fournisseur d’identité**, effectuez les étapes suivantes :

    a. Dans la zone de texte **Identificateur**, collez la valeur de **Identifier** (Identificateur) que vous copiez à partir de la section **Service Provider** (Fournisseur de services) des paramètres SAML dans le portail d’administration iLMS.

    b. Dans la zone de texte **URL de réponse**, collez la valeur **Point de terminaison (URL)** que vous copiez à partir de la section **Fournisseur de services** des paramètres SAML dans le portail d’administration iLMS, avec le modèle suivant : `https://www.inspiredlms.com/Login/<INSTANCE_NAME>/consumer.aspx`.

1. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    Dans la zone de texte **URL de connexion**, collez la valeur **Point de terminaison (URL)** que vous copiez à partir de la section **Fournisseur de services** des paramètres SAML dans le portail d’administration iLMS en tant que `https://www.inspiredlms.com/Login/<INSTANCE_NAME>/consumer.aspx`.

1. Pour permettre le provisionnement JIT, votre application iLMS attend les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à la configuration des attributs du jeton SAML. La capture d’écran suivante montre la liste des attributs par défaut. Cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue Attributs d’utilisateur.

    > [!NOTE]
    > Vous devez activer **Créer un compte utilisateur non reconnu** dans iLMS pour mapper ces attributs. Suivez les instructions [ici](https://support.inspiredelearning.com/help/adding-updating-and-managing-users#just-in-time-provisioning-with-saml-single-signon) pour avoir une idée sur la configuration des attributs.

1. En plus de ce qui précède, l’application iLMS s’attend à ce que quelques attributs supplémentaires soient repassés dans la réponse SAML. Dans la section **Revendications des utilisateurs** de la boîte de dialogue **Attributs utilisateur**, effectuez les étapes suivantes pour ajouter le jeton SAML comme indiqué dans le tableau ci-dessous :

    | Name | Attribut source|
    | --------|------------- |
    | division | user.department |
    | region | user.state |
    | department | user.jobtitle |

    a. Cliquez sur le bouton **Ajouter une nouvelle revendication** pour ouvrir la boîte de dialogue **Gérer les revendications des utilisateurs**.

    b. Dans la zone de texte **Attribut**, indiquez le nom d’attribut pour cette ligne.

    c. Laissez le champ **Espace de noms** vide.

    d. Sélectionnez Source comme **Attribut**.

    e. Dans la liste **Attribut de la source**, tapez la valeur d’attribut indiquée pour cette ligne.

    f. Cliquez sur **OK**

    g. Cliquez sur **Enregistrer**.

1. Sur la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le fichier **XML de métadonnées de fédération** en fonction des options définies selon vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

1. Dans la section **Configurer iLMS**, copiez l’URL ou les URL appropriées, en fonction de vos besoins.

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

Dans cette section, vous allez créer un utilisateur de test appelé Britta Simon dans le Portail Azure.

1. Dans le volet gauche du Portail Azure, sélectionnez **Azure Active Directory**, **Utilisateurs**, puis **Tous les utilisateurs**.
1. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.
1. Dans les propriétés **Utilisateur**, effectuez les étapes suivantes :
   1. Dans le champ **Nom**, entrez `Britta Simon`.  
   1. Dans le champ **Nom de l’utilisateur**, entrez username@companydomain.extension. Par exemple : `BrittaSimon@contoso.com`.
   1. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.
   1. Cliquez sur **Créer**.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à iLMS.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **iLMS**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-ilms-sso"></a>Configurer l’authentification unique iLMS

1. Dans une autre fenêtre de navigateur web, connectez-vous à votre **portail d’administration iLMS** en tant qu’administrateur.

2. Cliquez sur **SSO:SAML** sous l’onglet **Paramètres** pour ouvrir les paramètres SAML et effectuer les opérations suivantes :

    ![Capture d’écran montrant l’onglet de paramètres ILMS où vous pouvez sélectionner SSO: SAML.](./media/ilms-tutorial/settings.png)

3. Développez la section **Fournisseur de services** et copiez les valeurs **Identificateur** et **URL du point de terminaison**.

    ![Capture d’écran montrant les paramètres SAML où vous pouvez obtenir les valeurs.](./media/ilms-tutorial/values.png) 

4. Sous la section **Fournisseur d’identité**, cliquez sur **importer les métadonnées**.

5. Sélectionnez le fichier **Métadonnées de fédération** téléchargé à partir du portail Azure, à partir de la section **Certificat de signature SAML**.

    ![Capture d’écran montrant les paramètres SAML où vous pouvez sélectionner le fichier de métadonnées.](./media/ilms-tutorial/certificate.png)

6. Si vous souhaitez activer JIT pour approvisionner des comptes iLMS pour l’annulation de reconnaissance d’utilisateurs, procédez comme suit :

    a. Cochez **Créer un compte utilisateur non reconnu**.

    ![Capture d’écran montrant l’option Create Un-recognized User Account.](./media/ilms-tutorial/accounts.png)

    b. Mappez les attributs dans Azure AD avec les attributs dans iLMS. Dans la colonne d’attribut, spécifiez le nom des attributs ou la valeur par défaut.

    c. Accédez à l’onglet **Règles d’entreprise** et effectuez les opérations suivantes :

    ![Capture d’écran montrant les paramètres Business Rules, où vous pouvez entrer les informations à cette étape.](./media/ilms-tutorial/rules.png)

    d. Cochez **Créer des régions, divisions et départements non reconnus** pour créer des régions, divisions et départements qui n’existent pas encore au moment de l’authentification unique.

    e. Cochez **Mettre à jour le profil utilisateur lors de la connexion** pour spécifier si le profil utilisateur est mis à jour à chaque ouverture de session unique.

    f. Si l’option **Mettre à jour les valeurs vides pour les champs non obligatoires dans le profil utilisateur** est cochée, les champs facultatifs qui sont vides lors de la connexion provoquent également la présence de valeurs vides pour ces champs dans le profil iLMS de l’utilisateur.

    g. Cochez **Envoyer un e-mail de notification d’erreur** et entrez l’adresse e-mail de l’utilisateur pour lequel vous souhaitez recevoir l’e-mail de notification d’erreur.

7. Cliquez sur **Enregistrer** pour enregistrer les paramètres.

    ![Capture d’écran montrant le bouton Save.](./media/ilms-tutorial/save.png)

### <a name="create-ilms-test-user"></a>Créer l’utilisateur de test iLMS

L’application prend en charge la configuration d’utilisateur juste-à-temps, et après authentification, les utilisateurs sont créés automatiquement dans l’application. JIT fonctionnera si vous avez coché la case **Créer un compte utilisateur non reconnu** lors de la configuration de SAML sur le portail d’administration iLMS.

Si vous avez besoin de créer un utilisateur manuellement, suivez les étapes ci-dessous :

1. Connectez-vous à votre site d’entreprise iLMS en tant qu’administrateur.

2. Cliquez sur **Register User** (Inscrire un utilisateur) sous l’onglet **Users** (Utilisateurs) pour ouvrir la page **Register User** (Inscrire un utilisateur).

   ![Capture d’écran montrant l’onglet ILMS settings, où vous pouvez sélectionner Register User.](./media/ilms-tutorial/user.png)

3. Dans la page **Register User** (Inscrire un utilisateur), effectuez les étapes suivantes.

    ![Capture d’écran montrant la page Register User, où vous entrez les informations spécifiées.](./media/ilms-tutorial/add-user.png)

    a. Dans la zone de texte **First Name** (Prénom), tapez le prénom, par exemple Britta.

    b. Dans la zone de texte **Last Name** (Nom), tapez le nom, par exemple Simon.

    c. Dans la zone de texte **Email ID** (Identificateur e-mail), entrez l’adresse e-mail de l’utilisateur, par exemple BrittaSimon@contoso.com.

    d. Dans la liste déroulante **Région**, sélectionnez la valeur pour la région.

    e. Dans la liste déroulante **Division**, sélectionnez la valeur pour la division.

    f. Dans la liste déroulante **Département**, sélectionnez une valeur pour le département.

    g. Cliquez sur **Enregistrer**.

    > [!NOTE]
    > Vous pouvez envoyer un courrier d’inscription à l’utilisateur en cochant la case **Send Registration Mail** (Envoyer un e-mail d’inscription).

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

#### <a name="sp-initiated"></a>Lancée par le fournisseur de services :

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion à iLMS, d’où vous pouvez lancer le flux de connexion.  

* Accédez directement à l’URL de connexion à iLMS pour lancer le flux de connexion.

#### <a name="idp-initiated"></a>Lancée par le fournisseur d’identité :

* Cliquez sur **Tester cette application** dans le portail Azure. Vous devez alors être automatiquement connecté à l’instance d’iLMS pour laquelle vous avez configuré l’authentification unique. 

Vous pouvez aussi utiliser Mes applications de Microsoft pour tester l’application dans n’importe quel mode. Si, quand vous cliquez sur la vignette iLMS dans Mes applications, le mode Fournisseur de services est configuré, vous êtes redirigé vers la page de connexion de l’application pour lancer le flux de connexion ; s’il s’agit du mode Fournisseur d’identité, vous êtes automatiquement connecté à l’instance d’iLMS pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré iLMS, vous pouvez appliquer le contrôle de session, qui protège en temps réel contre l’exfiltration et l’infiltration des données sensibles de votre organisation. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
