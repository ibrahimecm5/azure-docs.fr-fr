---
title: 'Tutoriel : Intégration d’Azure Active Directory à Sage Intacct | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Sage Intacct.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/15/2021
ms.author: jeedes
ms.openlocfilehash: d490177056ccc2ff09c41b1700887fc3e70e3a58
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132346607"
---
# <a name="tutorial-integrate-sage-intacct-with-azure-active-directory"></a>Tutoriel : Intégrer Sage Intacct à Azure Active Directory

Dans ce didacticiel, vous allez apprendre à intégrer Sage Intacct à Azure Active Directory (Azure AD). Quand vous intégrez Sage Intacct à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Sage Intacct.
* Permettre à vos utilisateurs de se connecter automatiquement à Sage Intacct avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Sage Intacct pour lequel l’authentification unique (SSO) est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* Sage Intacct prend en charge l’authentification unique lancée par le **fournisseur d’identité**

## <a name="adding-sage-intacct-from-the-gallery"></a>Ajout de Sage Intacct à partir de la galerie

Pour configurer l’intégration de Sage Intacct avec Azure AD, vous devez ajouter Sage Intacct à partir de la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Sage Intacct** dans la zone de recherche.
1. Sélectionnez **Sage Intacct** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-sage-intacct"></a>Configurer et tester l’authentification unique (SSO) Azure AD pour Sage Intacct

Configurez et testez l’authentification unique Azure AD avec Sage Intacct pour un utilisateur de test nommé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Sage Intacct associé.

Pour configurer et tester l’authentification SSO Azure AD avec Sage Intacct, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
2. **[Configurer l’authentification unique Sage Intacct](#configure-sage-intacct-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test Sage Intacct](#create-sage-intacct-test-user)** pour avoir un équivalent de l’utilisateur B.Simon dans Sage Intacct, qui soit lié à la représentation de l’utilisateur Azure AD.
6. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, dans la page d’intégration de l’application **Sage Intacct**, recherchez la section **Gérer**, puis sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon pour **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, entrez les valeurs pour les champs suivants :

    Dans la zone de texte **URL de réponse**, ajoutez les URL suivantes :  
    `https://www.intacct.com/ia/acct/sso_response.phtml` (Sélectionnez comme URL par défaut.)  
    `https://www.p-02.intacct.com/ia/acct/sso_response.phtml`  
    `https://www.p-03.intacct.com/ia/acct/sso_response.phtml`  
    `https://www.p-04.intacct.com/ia/acct/sso_response.phtml`  
    `https://www.p-05.intacct.com/ia/acct/sso_response.phtml`  

1. L’application Sage Intacct s’attend à recevoir les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à votre configuration des attributs du jeton SAML. La capture d’écran suivante montre la liste des attributs par défaut. Cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue Attributs de l’utilisateur.

    ![image](common/edit-attribute.png)

1. En plus de ce qui précède, l’application Sage Intacct s’attend à ce que quelques attributs supplémentaires soient repassés dans la réponse SAML. Dans la boîte de dialogue **Attributs utilisateur et revendications**, effectuez les étapes suivantes pour ajouter l’attribut de jeton SAML, comme indiqué dans le tableau ci-dessous :

    | Nom de l'attribut  |  Attribut source|
    | ---------------| --------------- |
    | Nom de l’entreprise | **ID de société Sage Intacct** |
    | name | La valeur doit être la même que l’**ID d’utilisateur** Sage Intacct que vous entrez dans la **section Créer un utilisateur de test Sage Intacct**, décrite plus loin dans ce didacticiel. |

    a. Cliquez sur le bouton **Ajouter une nouvelle revendication** pour ouvrir la boîte de dialogue **Gérer les revendications des utilisateurs**.

    b. Dans la zone de texte **Attribut**, indiquez le nom d’attribut pour cette ligne.

    c. Laissez le champ **Espace de noms** vide.

    d. Sélectionnez Source comme **Attribut**.

    e. Dans la liste **Attribut source**, tapez ou sélectionnez la valeur d’attribut affichée pour cette ligne.

    f. Cliquez sur **OK**

    g. Cliquez sur **Enregistrer**.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **Certificat (Base64)** , puis sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

1. Dans la section **Configurer Sage Intacct**, copiez l’URL ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Sage Intacct.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Sage Intacct**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-sage-intacct-sso"></a>Configurer l’authentification unique Sage Intacct

1. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Sage Intacct en tant qu’administrateur.

1. Cliquez sur l’onglet **Company**, puis sur **Company Info**.

    ![Société](./media/intacct-tutorial/ic790037.png "Company")

1. Cliquez sur l’onglet **Security**, puis sur **Edit**.

    ![Sécurité](./media/intacct-tutorial/ic790038.png "Sécurité")

1. Dans la section **Single sign on (SSO)** , procédez comme suit :

    ![Authentification unique](./media/intacct-tutorial/ic790039.png "Authentification unique")

    a. Sélectionnez **Enable Single Sign-On**.

    b. Pour **Identity provider type**, sélectionnez **SAML 2.0**.

    c. Dans la zone de texte **Issuer URL** (URL de l’émetteur), collez la valeur de l’**identifiant Azure AD** que vous avez copiée dans le portail Azure.

    d. Dans la zone de texte **Login URL** (URL de connexion), collez la valeur **URL de connexion**, que vous avez copiée dans le portail Azure.

    e. Ouvrez votre certificat codé en **base-64** dans le bloc-notes, copiez son contenu dans le presse-papiers, puis collez-le dans la zone **Certificat**.

    f. Cliquez sur **Enregistrer**.

### <a name="create-sage-intacct-test-user"></a>Créer un utilisateur de test Sage Intacct

Pour configurer les utilisateurs Azure AD de sorte qu’ils puissent se connecter à Sage Intacct, ils doivent être approvisionnés dans Sage Intacct. Pour Sage Intacct, l’approvisionnement est une tâche manuelle.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous à votre locataire **Sage Intacct**.

1. Cliquez sur l’onglet **Company**, puis sur **Users**.

    ![Utilisateurs](./media/intacct-tutorial/ic790041.png "Utilisateurs")

1. Cliquez sur l’onglet **Ajouter**.

    ![Ajouter](./media/intacct-tutorial/ic790042.png "Ajouter")

1. Dans la section **User Information** , procédez comme suit :

    ![Capture d’écran montrant la section User Information, où vous pouvez entrer les informations à cette étape](./media/intacct-tutorial/ic790043.png "User Information").

    a. Tapez **l’ID utilisateur**, le **nom**, le **prénom**, **l’adresse de messagerie**, le **titre** et le **numéro de téléphone** d’un compte Azure AD que vous souhaitez approvisionner dans la section **Informations utilisateur**.

    > [!NOTE]
    > Vérifiez que l’**ID d’utilisateur** dans la capture d’écran ci-dessus soit identique à la valeur **Attribut source** mappée avec l’attribut **nom** dans la section **Attributs de l’utilisateur** du portail Azure.

    b. Sélectionnez les **privilèges d’administrateur** d’un compte Azure AD que vous voulez approvisionner.

    c. Cliquez sur **Enregistrer**. 
    
    d. Le détenteur du compte Azure AD reçoit un message électronique et suit un lien pour confirmer le compte avant qu’il ne soit activé.

1. Cliquez sur l’onglet **Authentification unique** et vérifiez que l’**ID d’utilisateur de l’authentification unique fédérée** dans la capture d’écran ci-dessus soit identique à la valeur **Attribut source** mappée avec `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` dans la section **Attributs de l’utilisateur** du portail Azure.

    ![Capture d’écran montrant la section User Information, où vous pouvez entrer l’ID d’utilisateur de l’authentification unique fédérée.](./media/intacct-tutorial/ic790044.png "User Information")

> [!NOTE]
> Pour approvisionner des comptes d’utilisateurs Azure AD, vous pouvez utiliser d’autres outils de création de compte utilisateur Sage Intacct ou des API fournies par Sage Intacct.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes.

* Cliquez sur Tester cette application dans le portail Azure. Vous êtes alors connecté automatiquement à l’instance de Sage Intacct pour laquelle vous avez configuré l’authentification SSO

* Vous pouvez utiliser Mes applications de Microsoft. Quand vous cliquez sur la vignette Sage Intacct dans Mes applications, vous devez être automatiquement connecté à l’instance de Sage Intacct pour laquelle vous avez configuré l’authentification SSO. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).


## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez configuré Sage Intacct, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
