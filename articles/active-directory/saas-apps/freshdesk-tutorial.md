---
title: 'Didacticiel : Intégration d’Azure Active Directory avec FreshDesk | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et FreshDesk.
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
ms.openlocfilehash: 7678efc256e0c533ac82f8eee4abf86b76328b55
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132338000"
---
# <a name="tutorial-azure-active-directory-integration-with-freshdesk"></a>Didacticiel : Intégration d’Azure Active Directory à FreshDesk

Dans ce tutoriel, vous allez découvrir comment intégrer FreshDesk à Azure Active Directory (Azure AD). Quand vous intégrez FreshDesk à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à FreshDesk.
* Permettre à vos utilisateurs de se connecter automatiquement à FreshDesk avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :
 
* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement FreshDesk pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* FreshDesk prend en charge l’authentification unique initiée par le **fournisseur de services**

## <a name="add-freshdesk-from-the-gallery"></a>Ajouter FreshDesk à partir de la galerie

Pour configurer l’intégration de FreshDesk dans Azure AD, vous devez ajouter FreshDesk depuis la galerie dans votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **FreshDesk** dans la zone de recherche.
1. Sélectionnez **FreshDesk** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-freshdesk"></a>Configurer et tester l’authentification unique Azure AD pour FreshDesk

Configurez et testez l’authentification unique Azure AD avec FreshDesk pour un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur FreshDesk associé.

Pour configurer et tester l’authentification unique Azure AD avec FreshDesk, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique FreshDesk](#configure-freshdesk-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test FreshDesk](#create-freshdesk-test-user)** pour avoir un équivalent de Britta Simon dans FreshDesk, lié à la représentation Azure AD associée.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

1. Sur le portail Azure, dans la page d’intégration de l’application **FreshDesk**, recherchez la section **Gérer**, puis sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon pour **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, entrez les valeurs pour les champs suivants :

    1. Dans la zone de texte **URL de connexion**, tapez une URL en utilisant le modèle `https://<tenant-name>.freshdesk.com` ou toute autre valeur suggérée par FreshDesk.

    1. Dans la zone de texte **Identificateur (ID d’entité)** , tapez une URL en utilisant le modèle `https://<tenant-name>.freshdesk.com`, ou toute autre valeur suggérée par FreshDesk.
     
    1. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://<tenant-name>.freshdesk.com/login/saml`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion, l’identificateur et l’URL de réponse réels. Contactez [l’équipe de support FreshDesk](https://freshdesk.com/helpdesk-software?utm_source=Google-AdWords&utm_medium=Search-IND-Brand&utm_campaign=Search-IND-Brand&utm_term=freshdesk&device=c&gclid=COSH2_LH7NICFVUDvAodBPgBZg) pour obtenir ces valeurs. Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. L’application FreshDesk s’attend à recevoir les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à la configuration des attributs de votre jeton SAML. La capture d’écran suivante montre la liste des attributs par défaut. **Unique User Identifier**  (Identificateur d’utilisateur unique) est mappé à **user.userPrincipalName**, mais FreshDesk s’attend à ce que cette revendication soit mappée à **user. mail**. Vous devez donc modifier le mappage d’attributs en cliquant sur l’icône de modification et changer le mappage d’attributs.

    ![image](common/edit-attribute.png)

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (Base64)** en fonction des options définies par rapport à vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

1. Dans la section **Configurer FreshDesk**, copiez la ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à FreshDesk.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **FreshDesk**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-freshdesk-sso"></a>Configurer l’authentification unique FreshDesk

1. Dans une autre fenêtre de navigateur web, connectez-vous au site de votre entreprise Freshdesk en tant qu’administrateur.

2. Sélectionnez l’icône **Settings** (Paramètres) puis, dans la section **Security** (Sécurité), effectuez les étapes suivantes :

    ![Authentification unique](./media/freshdesk-tutorial/configure-1.png "Authentification unique")
  
    1. Pour **Single Sign On** (Authentification unique), sélectionnez **On** (Activé).

    1. Dans **Login Method** (Méthode de connexion), sélectionnez **SAML SSO**.

    1. Dans la zone de texte **Entity ID provided by the IdP** (ID d’entité fourni par le fournisseur d’identité), collez la valeur **ID d’entité** que vous avez copiée sur le portail Azure.

    1. Dans la zone de texte **URL d’authentification unique SAML**, collez la valeur d’**URL de connexion** que vous avez copiée à partir du portail Azure.

    1. Dans **Signing Options** (Options de signature), sélectionnez **Only Signed Assertions** (Uniquement les assertions signées) dans la liste déroulante.

    1. Dans la zone de texte **Logout URL** (URL de déconnexion), collez l’**URL de déconnexion** que vous avez copiée sur le portail Azure.

    1. Dans la zone de texte **Security Certificate** (Certificat de sécurité), collez la valeur de **Certificat (en base64)** que vous avez obtenue précédemment.
  
    1. Cliquez sur **Enregistrer**.

## <a name="create-freshdesk-test-user"></a>Créer un utilisateur de test FreshDesk

Pour permettre aux utilisateurs Azure AD de se connecter à FreshDesk, vous devez les approvisionner dans FreshDesk.  
Dans le cas de FreshDesk, l’approvisionnement est une tâche manuelle.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous à votre locataire **Freshdesk** .

1. Dans le menu de gauche, cliquez sur **Admin** puis, sous l’onglet **General Settings** (Paramètres généraux), cliquez sur **Agents**.
  
    ![Agents](./media/freshdesk-tutorial/create-user-1.png "Agents")

1. Cliquez sur **New Agent**.

    ![Nouvel agent](./media/freshdesk-tutorial/create-user-2.png "New Agent")

1. Dans la boîte de dialogue Agent Information (Informations sur l’agent), complétez les champs obligatoires, puis cliquez sur **Create agent** (Créer un agent).

    ![Informations sur l’agent](./media/freshdesk-tutorial/create-user-3.png "Informations sur l’agent")

    >[!NOTE]
    >Le titulaire du compte Azure AD reçoit alors un message électronique qui contient un lien pour confirmer le compte avant qu’il soit activé.
    >
    >[!NOTE]
    >Vous pouvez utiliser tout autre outil ou n’importe quelle API de création de compte d’utilisateur fournis par Freshdesk pour provisionner des comptes d’utilisateurs Azure AD à Freshdesk.

### <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion à FreshDesk, d’où vous pouvez lancer le flux de connexion. 

* Accédez directement à l’URL de connexion à FreshDesk pour lancer le processus de connexion.

* Vous pouvez utiliser Mes applications de Microsoft. Quand vous cliquez sur la vignette FreshDesk dans Mes applications, vous devez être connecté automatiquement à l’application FreshDesk pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez configuré FreshDesk, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
