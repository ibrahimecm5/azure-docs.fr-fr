---
title: 'Tutoriel : Intégration de l’authentification unique Azure AD à Projectplace'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Projectplace.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/13/2021
ms.author: jeedes
ms.openlocfilehash: ea2cfac86a601305cd63414582721b6b6df864bb
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132333881"
---
# <a name="tutorial-azure-ad-sso-integration-with-projectplace"></a>Tutoriel : Intégration de l’authentification unique Azure AD à Projectplace

Dans ce didacticiel, vous allez apprendre à intégrer Projectplace à Azure Active Directory (Azure AD). Lorsque vous intégrez Projectplace à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Projectplace.
* Autoriser les utilisateurs à se connecter automatiquement à Projectplace avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.
* Les utilisateurs peuvent être provisionnés automatiquement dans Projectplace.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Projectplace pour lequel l’authentification unique (SSO) est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* Projectplace prend en charge l’authentification unique lancée par **le fournisseur d’identité et le fournisseur de service** ainsi que le provisionnement d’utilisateurs **juste-à-temps**.

## <a name="add-projectplace-from-the-gallery"></a>Ajouter Projectplace à partir de la galerie

Pour configurer l’intégration de Projectplace avec Azure AD, vous devez ajouter Projectplace à votre liste d’applications SaaS gérées, à partir de la galerie.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Projectplace** dans la zone de recherche.
1. Sélectionnez **Projectplace** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-projectplace"></a>Configurer et tester l’authentification unique Azure AD pour Projectplace

Configurez et testez l’authentification unique Azure AD avec Projectplace à l’aide d’un utilisateur test appelé **B. Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Projectplace associé.

Pour configurer et tester l’authentification unique Azure AD avec Projectplace, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
   1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
   1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique de Projectplace](#configure-projectplace-sso)** pour configurer les paramètres de l’authentification unique côté application.
   1. **[Créer un utilisateur test Projectplace](#create-projectplace-test-user)** pour avoir dans Projectplace un équivalent de B. Simon lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **Projectplace**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon pour **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, si vous souhaitez configurer l’application en mode lancé par le **fournisseur de services**, l’application est préconfigurée et les URL nécessaires sont préremplies avec Azure. L’utilisateur doit enregistrer la configuration en cliquant sur le bouton **Enregistrer**.

1. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    Dans la zone de texte **URL de connexion**, tapez l’URL : `https://service.projectplace.com`

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur l’icône **Copier** pour copier l’**URL des métadonnées de fédération d’application** appropriée et enregistrez-la dans le Bloc-notes.

   ![Lien Téléchargement de certificat](common/copy-metadataurl.png)

1. Dans la section **Configurer Projectplace**, copiez la ou les URL appropriées, selon vos besoins.

   ![Copier les URL de configuration](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

Dans cette section, vous allez créer un utilisateur de test appelé B. Simon dans le portail Azure.

1. Dans le volet gauche du Portail Azure, sélectionnez **Azure Active Directory**, **Utilisateurs**, puis **Tous les utilisateurs**.
1. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.
1. Dans les propriétés **Utilisateur**, effectuez les étapes suivantes :
   1. Dans le champ **Nom**, entrez `B. Simon`.  
   1. Dans le champ **Nom de l’utilisateur**, entrez username@companydomain.extension. Par exemple : `BrittaSimon@contoso.com`.
   1. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.
   1. Cliquez sur **Créer**.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous autorisez B. Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Projectplace.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Projectplace**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-projectplace-sso"></a>Configurer l’authentification unique de Projectplace

Pour configurer l’authentification unique côté **Projectplace**, vous devez envoyer l’**URL des métadonnées de fédération d’application** copiée depuis le portail Azure à l’[équipe du support technique Projectplace](https://success.planview.com/Projectplace/Support). Celle-ci vérifiera que l’authentification unique SAML est configurée correctement des deux côtés.

>[!NOTE]
>La configuration de l’authentification unique doit être effectuée par [l’équipe du support technique Projectplace](https://success.planview.com/Projectplace/Support). Vous recevez une notification dès qu’elle est terminée. 

### <a name="create-projectplace-test-user"></a>Créer un utilisateur de test Projectplace

>[!NOTE]
>Vous pouvez ignorer cette étape si le provisionnement d’utilisateurs est activé dans Projectplace. Vous pouvez demander à l’[équipe du support technique Projectplace](https://success.planview.com/Projectplace/Support) d’activer le provisionnement. Après cela, les utilisateurs seront créés dans Projectplace au moment de la première connexion.

Pour autoriser les utilisateurs Azure AD à se connecter à Projectplace, vous devez les ajouter dans Projectplace. Vous devez les ajouter manuellement.

**Pour créer un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous au site d’entreprise **Projectplace** en tant qu’administrateur.

2. Accédez à **Contacts**, puis cliquez sur **Membres** :
   
    ![Accéder à People, puis sélectionner Members](./media/projectplace-tutorial/members.png "Personnes")

3. Sélectionnez **Ajouter un membre** :
   
    ![Sélectionner Add Member](./media/projectplace-tutorial/issues.png "Ajouter des membres")

4. Dans la section **Ajouter un membre**, procédez comme suit.
   
    ![Section Add Member](./media/projectplace-tutorial/account.png "New Members")
   
    1. Dans la zone **Nouveaux membres**, entrez l'adresse e-mail d'un compte Azure AD valide que vous souhaitez ajouter.
   
    1. Sélectionnez **Envoyer**.

   Un message électronique contenant un lien pour confirmer le compte avant qu’il ne soit activé est envoyé au titulaire du compte Azure AD.

>[!NOTE]
>Pour ajouter des comptes d’utilisateur Azure AD, vous pouvez utiliser n’importe quel autre outil ou API de création de compte d’utilisateur fourni par Projectplace.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

#### <a name="sp-initiated"></a>Lancée par le fournisseur de services :

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL d’authentification de Projectplace, à partir de laquelle vous pouvez lancer le processus de connexion.  

* Accédez directement à l’URL d’authentification de Projectplace pour lancer le processus de connexion.

#### <a name="idp-initiated"></a>Lancée par le fournisseur d’identité :

* Cliquez sur **Tester cette application** dans le portail Azure, ce qui devrait vous connecter automatiquement à l’application Projectplace pour laquelle vous avez configuré l’authentification unique. 

Vous pouvez aussi utiliser Mes applications de Microsoft pour tester l’application dans n’importe quel mode. Quand vous cliquez sur la vignette Projectplace dans Mes applications, si le mode Fournisseur de services est configuré, vous êtes redirigé vers la page d’authentification de l’application pour lancer le processus de connexion. Si le mode Fournisseur d’identité est configuré, vous êtes automatiquement connecté à l’application Projectplace pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré Projectplace, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
