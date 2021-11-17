---
title: 'Didacticiel : Intégration d’Azure Active Directory à ZIVVER | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et ZIVVER.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/17/2021
ms.author: jeedes
ms.openlocfilehash: e007f6026d402fd144e880238cd69df356977f1d
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132332494"
---
# <a name="tutorial-azure-active-directory-integration-with-zivver"></a>Didacticiel : Intégration d’Azure Active Directory à ZIVVER

Dans ce tutoriel, vous allez apprendre à intégrer ZIVVER à Azure Active Directory (Azure AD). Lorsque vous intégrez ZIVVER à Azure AD, vous pouvez :

* dans Azure AD, contrôler qui a accès à ZIVVER.
* permettre à vos utilisateurs de se connecter automatiquement à ZIVVER avec leurs comptes Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à ZIVVER, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un [compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement ZIVVER pour lequel l’authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* ZIVVER prend en charge l’authentification unique lancée par le **fournisseur d’identité**.

> [!NOTE]
> L’identificateur de cette application étant une valeur de chaîne fixe, une seule instance peut être configurée dans un locataire.

## <a name="add-zivver-from-the-gallery"></a>Ajouter ZIVVER depuis la galerie

Pour configurer l’intégration de ZIVVER avec Azure AD, vous devez ajouter ZIVVER, disponible dans la galerie, à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **ZIVVER** dans la zone de recherche.
1. Sélectionnez **ZIVVER** dans le volet des résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-zivver"></a>Configurer et tester l’authentification unique Azure AD pour ZIVVER

Configurez et testez l’authentification unique Azure AD avec ZIVVER à l’aide d’un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur associé dans ZIVVER.

Pour configurer et tester l’authentification unique Azure AD avec ZIVVER, procédez comme suit :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique ZIVVER](#configure-zivver-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test ZIVVER](#create-zivver-test-user)** pour avoir un équivalent de B.Simon dans ZIVVER lié à la représentation d’utilisateur Azure AD.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le Portail Azure, accédez à la page d’intégration de l’application **ZIVVER**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, effectuez l’étape suivante :

    Dans la zone de texte **Identificateur**, tapez l’URL : `https://app.zivver.com/SAML/Zivver`

5. L’application ZIVVER s’attend à recevoir les assertions SAML dans un certain format, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à la configuration des attributs de jetons SAML. La capture d’écran suivante montre la liste des attributs par défaut, où **nameidentifier** est mappé avec **user.userprincipalname**. L’application ZIVVER s’attend à ce que **nameidentifier** soit mappé sur **user.mail**. Vous devez donc modifier le mappage d’attribut en cliquant sur l’icône **Modifier**.

    ![Capture d’écran montrant User Attributes avec l’icône Edit sélectionnée.](common/edit-attribute.png)

6. En plus de ce qui précède, l’application ZIVVER s’attend à ce que quelques attributs supplémentaires soient repassés dans la réponse SAML. Dans la section **Revendications des utilisateurs** de la boîte de dialogue **Attributs utilisateur**, effectuez les étapes suivantes pour ajouter le jeton SAML comme indiqué dans le tableau ci-dessous :

    | Name | Espace de noms | Attribut source |
    | ---------------|---------| ---------- |
    | ZivverAccountKey | https:\//zivver.com/SAML/Attributes | user.objectid |

    >[!NOTE]
    >Si vous utilisez une configuration hybride avec Active Directory local et l’outil Azure AD Connect, VALUE doit être défini sur `user.objectGUID`.

    a. Cliquez sur le bouton **Ajouter une nouvelle revendication** pour ouvrir la boîte de dialogue **Gérer les revendications des utilisateurs**.

    ![Capture d’écran montrant User claims avec l’option permettant d’ajouter une nouvelle revendication.](common/new-save-attribute.png)

    ![Capture d’écran montrant la boîte de dialogue Manage user claims où vous pouvez entrer les valeurs décrites.](common/new-attribute-details.png)

    b. Dans la zone de texte **Attribut**, indiquez le nom d’attribut pour cette ligne.

    c. Laissez le champ **Espace de noms** vide.

    d. Sélectionnez Source comme **Attribut**.

    e. Dans la liste **Attribut de la source**, tapez la valeur d’attribut indiquée pour cette ligne.

    f. Cliquez sur **Enregistrer**.

7. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **XML de métadonnées de fédération** et cliquez sur l’icône **Copier** pour copier l’**URL des métadonnées de fédération d’application** en fonction des options définies par rapport à vos besoins, puis enregistrez-les sur votre ordinateur.

    ![Lien de téléchargement d’URL de certificat](./media/zivver-tutorial/metadataxmlurl.png)

8. Dans la section **Configurer ZIVVER**, copiez les URL appropriées, en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à ZIVVER.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **ZIVVER**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-zivver-sso"></a>Configurer l’authentification unique ZIVVER

1. Dans une autre fenêtre de navigateur web, connectez-vous à votre [site](https://app.zivver.com/login) d’entreprise ZIVVER en tant qu’administrateur.

2. Cliquez sur l’icône **Paramètres de l’organisation** en bas à gauche de la fenêtre du navigateur.

3. Accédez à **Single sign-on** (Authentification unique).

4. Ouvrez le fichier XML de métadonnées de fédération que vous avez téléchargé à partir du portail Azure.

5. Dans la zone de texte **Identity Provider metadata URL** (URL des métadonnées de fournisseur d’identité), collez l’**URL des métadonnées de fédération d’application** que vous avez enregistrée précédemment à partir du portail Azure.

6. Cochez la case **Turn on SSO** (Activer l’authentification unique).

7. Cliquez sur **ENREGISTRER**.

### <a name="create-zivver-test-user"></a>Créer un utilisateur de test ZIVVER

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans ZIVVER. Collaborez avec l’[équipe du support technique ZIVVER](https://support.zivver.com/) pour ajouter des utilisateurs dans la plate-forme ZIVVER. Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes.

* Cliquez sur Tester cette application dans le Portail Azure : vous devez être connecté automatiquement à l’application ZIVVER pour laquelle vous avez configuré l’authentification unique.

* Vous pouvez utiliser Mes applications de Microsoft. Quand vous cliquez sur la mosaïque ZIVVER dans Mes applications, vous devez être connecté automatiquement à l’application ZIVVER pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez configuré ZIVVER, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
