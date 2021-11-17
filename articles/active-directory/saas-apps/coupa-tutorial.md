---
title: 'Didacticiel : Intégration d’Azure Active Directory à Coupa | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Coupa.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/05/2021
ms.author: jeedes
ms.openlocfilehash: 682a49c23f3d3c9e567955b892772a6b07f22a9d
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132338627"
---
# <a name="tutorial-azure-active-directory-integration-with-coupa"></a>Didacticiel : Intégration d’Azure Active Directory à Coupa

Dans ce tutoriel, vous allez découvrir comment intégrer Coupa à Azure Active Directory (Azure AD). Quand vous intégrez Coupa à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Coupa.
* Permettre à vos utilisateurs de se connecter automatiquement à Coupa avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Coupa pour lequel l’authentification unique (SSO) est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Coupa prend en charge l’authentification unique lancée par le **fournisseur de services**

## <a name="add-coupa-from-the-gallery"></a>Ajouter Coupa à partir de la galerie

Pour configurer l'intégration de Coupa avec Azure AD, vous devez ajouter Coupa disponible dans la galerie à votre liste d'applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Coupa** dans la zone de recherche.
1. Sélectionnez **Coupa** dans le panneau de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-coupa"></a>Configurer et tester l’authentification unique Azure AD pour Coupa

Configurez et testez l’authentification unique Azure AD avec Coupa avec un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur associé dans Coupa.

Pour configurer et tester l’authentification unique Azure AD avec Coupa, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Coupa](#configure-coupa-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test Coupa](#create-coupa-test-user)** pour avoir un équivalent de B.Simon dans Coupa lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, dans la page d’intégration de l’application **Coupa**, recherchez la section **Gérer**, puis sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<companyname>.coupahost.com`.

    > [!NOTE]
    > La valeur de l’URL de connexion n’est pas réelle. Mettez à jour cette valeur avec l’URL d’authentification réelle. Pour obtenir cette valeur, contactez l’[équipe de support technique de Coupa](https://success.coupa.com/Support/Contact_Us?).

    b. Dans la zone **Identificateur**, tapez l’URL : 

    | Environnement  | URL |
    |:-------------|----|
    | Bac à sable | `sso-stg1.coupahost.com`|
    | Production | `sso-prd1.coupahost.com`|
    | | |

    c. Dans la zone **URL de réponse**, tapez l’URL : 

    | Environnement | URL |
    |------------- |----|
    | Bac à sable | `https://sso-stg1.coupahost.com/sp/ACS.saml2`|
    | Production | `https://sso-prd1.coupahost.com/sp/ACS.saml2`|
    | | |

4. Sur la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le fichier **XML de métadonnées de fédération** en fonction des options définies selon vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

6. Dans la section **Configurer Coupa**, copiez la ou les URL appropriées, en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Coupa.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Coupa**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-coupa-sso"></a>Configurer l’authentification unique Coupa

1. Connectez-vous à votre site d’entreprise Coupa en tant qu’administrateur.

2. Accédez à **Setup \> Security controls**.

    ![Contrôles de sécurité](./media/coupa-tutorial/setup.png "Security Controls")

3. Dans la section **Log in using Coupa credentials**, procédez comme suit :

    ![Métadonnées SP Coupa](./media/coupa-tutorial/login.png "Métadonnées SP Coupa")

    a. Sélectionnez **Log in using SAML**.

    b. Cliquez sur **Parcourir** pour charger les métadonnées téléchargées à partir du portail Azure.

    c. Cliquez sur **Enregistrer**.

### <a name="create-coupa-test-user"></a>Créer un utilisateur de test Coupa

Pour se connecter à Coupa, les utilisateurs d’Azure AD doivent être approvisionnés dans Coupa.  

* Dans le cas de Coupa, l’approvisionnement est une tâche manuelle.

**Pour configurer l'approvisionnement des utilisateurs, procédez comme suit :**

1. Connectez-vous à votre site d'entreprise **Coupa** en tant qu'administrateur.

2. Dans le menu en haut, cliquez sur **Setup**, puis sur **Users**.

    ![Utilisateurs](./media/coupa-tutorial/user.png "Utilisateurs")

3. Cliquez sur **Créer**.

    ![Créer des utilisateurs](./media/coupa-tutorial/create.png "Créer des utilisateurs")

4. Dans la section **User Create**, procédez comme suit :

    ![Détails de l’utilisateur](./media/coupa-tutorial/details.png "User Details")

    a. Dans les zones de texte **Login**, **First name**, **Last Name**, **Single Sign-On ID** et **Email**, entrez les attributs d'un compte Azure Active Directory valide que vous voulez configurer.

    b. Cliquez sur **Créer**.

    >[!NOTE]
    >Le titulaire du compte Azure Active Directory reçoit un message électronique contenant un lien pour confirmer le compte et l’activer.
    >

>[!NOTE]
>Vous pouvez utiliser tout autre outil ou n’importe quelle API de création de compte d’utilisateur fournis par Coupa pour provisionner des comptes d’utilisateurs Azure AD.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion à Coupa, d’où vous pouvez lancer le flux de connexion. 

* Accédez directement à l’URL de connexion à Coupa pour lancer le flux de connexion.

* Vous pouvez utiliser Mes applications de Microsoft. Quand vous cliquez sur la vignette Coupa dans Mes applications, vous devez être connecté automatiquement à l’application Coupa pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré Coupa, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
