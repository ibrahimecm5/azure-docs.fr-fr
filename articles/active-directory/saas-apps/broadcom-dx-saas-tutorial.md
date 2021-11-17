---
title: 'Tutoriel : Intégration de l’authentification unique Azure Active Directory à Broadcom DX SaaS | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Broadcom DX SaaS.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/30/2021
ms.author: jeedes
ms.openlocfilehash: 5f12bc03896e7c5abd1da4f559b56e53c277749b
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132335097"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-broadcom-dx-saas"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory à Broadcom DX SaaS

Dans ce tutoriel, vous allez apprendre à intégrer Broadcom DX SaaS à Azure Active Directory (Azure AD). Quand vous intégrez Broadcom DX SaaS à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Broadcom DX SaaS.
* Permettre à vos utilisateurs de se connecter automatiquement à Broadcom DX SaaS avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Broadcom DX SaaS pour lequel l’authentification unique est activée.

> [!NOTE]
> Cette intégration peut également être utilisée à partir de l’environnement cloud US Government Azure AD. Cette application est disponible dans la Galerie d’applications cloud US Government Azure AD et peut être configurée de la même façon que dans le cloud public.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* Broadcom DX SaaS prend en charge l’authentification unique initiée par **un fournisseur d’identité**.

* Broadcom DX SaaS prend en charge l’attribution d’utilisateurs **juste-à-temps**.

> [!NOTE]
> L’identificateur de cette application étant une valeur de chaîne fixe, une seule instance peut être configurée dans un locataire.

## <a name="add-broadcom-dx-saas-from-the-gallery"></a>Ajouter Broadcom DX SaaS à partir de la Galerie

Pour configurer l’intégration de Broadcom DX SaaS à Azure AD, vous devez ajouter Broadcom DX SaaS à votre liste d’applications SaaS gérées à partir de la galerie.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, saisissez **Broadcom DX SaaS** dans la zone de recherche.
1. Sélectionnez **Broadcom DX SaaS** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.


## <a name="configure-and-test-azure-ad-sso-for-broadcom-dx-saas"></a>Configurer et tester l’authentification unique Azure AD pour Broadcom DX SaaS

Configurez et testez l’authentification unique Azure AD avec Broadcom DX SaaS à l’aide d’un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur associé dans Broadcom DX SaaS.

Pour configurer et tester l’authentification unique Azure AD avec Broadcom DX SaaS, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Broadcom DX SaaS](#configure-broadcom-dx-saas-sso)** pour configurer les paramètres d’authentification unique côté application.
    1. **[Créer un utilisateur de test Broadcom DX SaaS](#create-broadcom-dx-saas-test-user)** pour avoir dans Broadcom DX SaaS un équivalent de B.Simon qui soit lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **Broadcom DX SaaS**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la page **Configurer l’authentification unique avec SAML**, effectuez les étapes suivantes :

    a. Dans la zone de texte **Identificateur**, tapez une valeur au format suivant : `DXI_<TENANT_NAME>`

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://axa.dxi-na1.saas.broadcom.com/ess/authn/<TENANT_NAME>`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur et l’URL de réponse réels. Pour obtenir ces valeurs, contactez l’[équipe du support client de Broadcom DX SaaS](mailto:dxi-na1@saas.broadcom.com). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. L’application Broadcom DX SaaS s’attend à recevoir les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à votre configuration des attributs de jeton SAML. La capture d’écran suivante montre la liste des attributs par défaut.

    ![image](common/default-attributes.png)

1. En plus de ce qui précède, l’application Broadcom DX SaaS s’attend à ce que quelques attributs supplémentaires (présentés ci-dessous) soient repassés dans la réponse SAML. Ces attributs sont également préremplis, mais vous pouvez les examiner pour voir s’ils répondent à vos besoins.
    
    | Nom |  Attribut source|
    | --------------- | --------- |
    | Groupe | user.groups |

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **Certificat (en base64)** , puis sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

1. Dans la section **Configurer Broadcom DX SaaS**, copiez les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Broadcom DX SaaS.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Broadcom DX SaaS**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-broadcom-dx-saas-sso"></a>Configurer l’authentification unique Broadcom DX SaaS

1. Connectez-vous à votre site d’entreprise Broadcom DX SaaS en tant qu’administrateur.

2. Allez dans **Settings** (Paramètres) et cliquez sur l’onglet **Users** (Utilisateurs).

    ![Utilisateurs](./media/broadcom-dx-saas-tutorial/settings.png "Utilisateurs")

3. Dans la section **User management** (Gestion des utilisateurs), cliquez sur le bouton de sélection, puis sélectionnez **SAML**.

    ![Gestion des utilisateurs](./media/broadcom-dx-saas-tutorial/local.png "User Management")

4. Dans la section **Identify SAML account** (Identifier un compte SAML), procédez comme suit :
   
    ![Compte](./media/broadcom-dx-saas-tutorial/broadcom-1.png "Compte") 

    a. Dans la zone de texte **Issuer** (Émetteur), collez la valeur **Identificateur Azure AD** que vous avez copiée à partir du portail Azure.

    b. Dans la zone de texte **Identity Provider (IDP) Login URL** (URL de connexion du fournisseur d’identité), collez la valeur de l’**URL de connexion** que vous avez copiée à partir du portail Azure.

    c. Dans la zone de texte **Identity Provider (IDP) Logout URL** (URL de déconnexion du fournisseur d’identité), collez la valeur de l’**URL de déconnexion** que vous avez copiée à partir du portail Azure.

    d. Ouvrez le **certificat (en base64)** téléchargé à partir du portail Azure dans Bloc-notes et collez le contenu dans la zone de texte **Identity provider certificate** (Certificat du fournisseur d’identité).

    e. Cliquez sur **NEXT**.

5. Dans la section **Map attributes** (Mapper les attributs), remplissez les attributs SAML requis dans la page suivante et cliquez sur **NEXT** (Suivant).

    ![Attributs](./media/broadcom-dx-saas-tutorial/broadcom-2.png "Attributs") 


6. Dans la section **Identify user group** (Identifier le groupe d’utilisateurs), entrez l’ID d’objet de ce groupe, puis cliquez sur **NEXT** (Suivant).

    ![Ajouter un groupe](./media/broadcom-dx-saas-tutorial/broadcom-3.png "Ajouter un groupe") 

7. Dans la section **Populate SAML Account** (Remplir le compte SAML), vérifiez les valeurs remplies, puis cliquez sur **SAVE** (Enregistrer).

    ![Compte SAML](./media/broadcom-dx-saas-tutorial/broadcom-4.png "Compte SAML") 

### <a name="create-broadcom-dx-saas-test-user"></a>Créer un utilisateur de test Broadcom DX SaaS

Dans cette section, un utilisateur appelé Britta Simon est créé dans Broadcom DX SaaS. Broadcom DX SaaS prend en charge l’attribution d’utilisateurs juste-à-temps, option qui est activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas encore d’utilisateur dans Broadcom DX SaaS, un nouvel utilisateur est créé après l’authentification.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes.

* Dans le portail Azure, cliquez sur Tester cette application. Vous êtes alors automatiquement connecté à l’instance de Broadcom DX SaaS pour laquelle vous avez configuré l’authentification unique.

* Vous pouvez utiliser Mes applications de Microsoft. Quand vous cliquez sur la vignette Broadcom DX SaaS dans Mes applications, vous devez être connecté automatiquement à l’instance de Broadcom DX SaaS pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré Broadcom DX SaaS, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
