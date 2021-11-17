---
title: 'Tutoriel : Intégration de l’authentification unique Azure Active Directory à Zenya | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Zenya.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/01/2021
ms.author: jeedes
ms.openlocfilehash: 25440815a219b353ebd44ba6ce60e504cb1862b4
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132346531"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-zenya"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory à Zenya

Dans ce tutoriel, vous allez apprendre à intégrer Zenya à Azure Active Directory (Azure AD). Quand vous intégrez Zenya à Azure AD, vous pouvez :

* Contrôler, dans Azure AD, qui a accès à Zenya.
* Permettre à vos utilisateurs de se connecter automatiquement à Zenya avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Zenya pour lequel l’authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* Zenya prend en charge l’authentification unique lancée par le **fournisseur de services**
* Zenya prend en charge l’[attribution automatique d’utilisateurs](iprova-provisioning-tutorial.md).

## <a name="add-zenya-from-the-gallery"></a>Ajouter Zenya à partir de la galerie

Pour configurer l’intégration de Zenya à Azure AD, vous devez ajouter Zenya à partir de la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Zenya** dans la zone de recherche.
1. Sélectionnez **Zenya** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-zenya"></a>Configurer et tester l’authentification unique Azure AD pour Zenya

Configurez et testez l’authentification unique Azure AD avec Zenya pour une utilisatrice de test appelée **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur associé dans Zenya.

Pour configurer et tester l’authentification unique Azure AD avec Zenya, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Zenya](#configure-zenya-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test Zenya](#create-zenya-test-user)** pour avoir un équivalent de B.Simon dans Zenya lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="retrieve-configuration-information-from-zenya"></a>Récupérer des informations de configuration à partir de Zenya

Dans cette section, vous allez récupérer des informations à partir de Zenya pour configurer l’authentification unique Azure AD.

1. Ouvrez un navigateur web et accédez à la page **SAML2 info** de Zenya, en utilisant les modèles d’URL suivants :
    
     `https://<SUBDOMAIN>.zenya.work/saml2info`   
     `https://<SUBDOMAIN>.iprova.nl/saml2info`  
     `https://<SUBDOMAIN>.iprova.be/saml2info`  
     `https://<SUBDOMAIN>.iprova.eu/saml2info` 

    ![Afficher la page d’informations de SAML2 Zenya](media/iprova-tutorial/information.png)

1. Laissez l’onglet du navigateur ouvert pendant que vous effectuez les étapes qui suivent dans un autre onglet de navigateur.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans la page d’intégration de l’application **Zenya** du Portail Azure, recherchez la section **Gérer**, puis sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    a. Renseignez le champ **URL de connexion** avec la valeur affichée derrière l’étiquette **Sign-on URL** dans la page **Zenya SAML2 info**. Cette page est toujours ouverte dans votre autre onglet de navigateur.

    b. Renseignez le champ **Identificateur** avec la valeur affichée derrière l’étiquette **EntityID** dans la page **Zenya SAML2 info**. Cette page est toujours ouverte dans votre autre onglet de navigateur.

    c. Renseignez le champ **URL de réponse** avec la valeur affichée derrière l’étiquette **Reply URL** dans la page **Zenya SAML2 info**. Cette page est toujours ouverte dans votre autre onglet de navigateur.

1. L’application Zenya s’attend à recevoir les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à votre configuration des attributs de jetons SAML. La capture d’écran suivante montre la liste des attributs par défaut.

    ![image](common/default-attributes.png)

1. En plus de ce qui précède, l’application Zenya s’attend à ce que quelques attributs supplémentaires, indiqués ci-après, soient passés dans la réponse SAML. Ces attributs sont également préremplis, mais vous pouvez les examiner pour voir s’ils répondent à vos besoins.

    | Nom | Attribut source| Espace de noms  |
    | ---------------| -------- | -----|
    | `samaccountname` | `user.onpremisessamaccountname`| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur le bouton Copier pour copier l’**URL des métadonnées de fédération d’application**, puis enregistrez-la sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/copy-metadataurl.png)

## <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

Dans cette section, vous allez créer un utilisateur de test appelé B. Simon dans le portail Azure.

1. Dans le volet gauche du Portail Azure, sélectionnez **Azure Active Directory**, **Utilisateurs**, puis **Tous les utilisateurs**.
1. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.
1. Dans les propriétés **Utilisateur**, effectuez les étapes suivantes :
   1. Dans le champ **Nom**, entrez `B.Simon`.  
   1. Dans le champ **Nom de l’utilisateur**, entrez username@companydomain.extension. Par exemple : `B.Simon@contoso.com`.
   1. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.
   1. Cliquez sur **Créer**.

## <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Zenya.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Zenya**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-zenya-sso"></a>Configurer l’authentification unique Zenya

1. Connectez-vous à Zenya avec le compte **Administrateur**.

2. Ouvrez le menu **Atteindre**.

3. Sélectionnez **Application management** (Gestion des applications).

4. Sélectionnez **General** (Général) dans le panneau **System settings** (Paramètres système).

5. Sélectionnez **Modifier**.

6. Faites défiler jusqu’à **Contrôle d’accès**.

    ![Paramètres de contrôle d’accès Zenya](media/iprova-tutorial/access-control.png)

7. Recherchez le paramètre **Users are automatically logged on with their network accounts** (Les utilisateurs sont automatiquement connectés avec leur compte réseau) et remplacez-le par **Yes, authentication via SAML** (Oui, authentification via SAML). Des options supplémentaires s’affichent maintenant.

8. Sélectionnez **Configurer**.

9. Sélectionnez **Suivant**.

10. Zenya vous demande si vous souhaitez télécharger des données de fédération à partir d’une URL ou les charger à partir d’un fichier. Sélectionnez l’option **From URL** (À partir d’une URL).

    ![Télécharger des métadonnées Azure AD](media/iprova-tutorial/metadata.png)

11. Collez l’URL de métadonnées que vous avez enregistrée lors de la dernière étape de la section « Configurer l’authentification unique Azure AD ».

12. Sélectionnez le bouton en forme de flèche pour télécharger les métadonnées à partir d’Azure AD.

13. Une fois le téléchargement terminé, le message de confirmation **Valid Federation Data file downloaded** (Fichier de données de fédération valide téléchargé) s’affiche.

14. Sélectionnez **Suivant**.

15. Ignorez l’option **Test login** (Tester la connexion) pour l’instant et sélectionnez **Suivant**.

16. Dans la zone de liste déroulante **Claim to use** (Revendication à utiliser), sélectionnez **windowsaccountname**.

17. Sélectionnez **Terminer**.

18. Vous revenez à présent à l’écran **Edit general settings** (Modifier les paramètres généraux). Faites défiler jusqu’en bas de la page et sélectionnez **OK** pour enregistrer votre configuration.

## <a name="create-zenya-test-user"></a>Créer un utilisateur de test Zenya

1. Connectez-vous à Zenya avec le compte **Administrateur**.

2. Ouvrez le menu **Atteindre**.

3. Sélectionnez **Application management** (Gestion des applications).

4. Sélectionnez **Users** (Utilisateurs) dans le panneau **Users and user groups** (Utilisateurs et groupes d’utilisateurs).

5. Sélectionnez **Ajouter**.

6. Dans la zone de texte **Username** (Nom d’utilisateur), entrez le nom d’un utilisateur, par exemple `B.Simon@contoso.com`.

7. Dans la zone **Full name** (Nom complet), entrez un nom complet d’utilisateur, comme **B.Simon**.

8. Sélectionnez l’option **No password (use single sign-on)** (Aucun mot de passe (utiliser l’authentification unique)).

9. Dans la zone de texte **Email address** (Adresse e-mail), entrez l’adresse e-mail de l’utilisateur, par exemple `B.Simon@contoso.com`.

10. Faites défiler jusqu’à la fin de la page, puis sélectionnez **Finish** (Terminer).

> [!NOTE]
> Zenya prend aussi en charge l’attribution automatique d’utilisateurs. Vous trouverez [ici](./iprova-provisioning-tutorial.md) plus d’informations sur la façon de configurer l’attribution automatique d’utilisateurs.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion à Zenya, d’où vous pouvez lancer le flux de connexion. 

* Accédez directement à l’URL de connexion à Zenya pour lancer le flux de connexion.

* Vous pouvez utiliser Mes applications de Microsoft. Un clic sur la vignette Zenya dans Mes applications vous redirige vers l’URL de connexion à Zenya. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez configuré Zenya, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
