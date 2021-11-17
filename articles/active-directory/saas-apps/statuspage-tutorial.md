---
title: 'Tutoriel : Intégration de l’authentification unique Azure Active Directory à StatusPage | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et StatusPage.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/31/2021
ms.author: jeedes
ms.openlocfilehash: 24ea84daca5c6f613a336984dbd91202d664812d
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132320653"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-statuspage"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory à StatusPage

Dans ce tutoriel, vous allez apprendre à intégrer StatusPage à Azure Active Directory (Azure AD). En intégrant StatusPage à Azure AD, vous pouvez :

* Utiliser Azure AD pour contrôler qui a accès à StatusPage.
* Permettre à vos utilisateurs de se connecter automatiquement à StatusPage avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD avec StatusPage, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un [compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement StatusPage pour lequel l'authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* StatusPage prend en charge l'authentification unique initiée par le **fournisseur d'identité**.

## <a name="add-statuspage-from-the-gallery"></a>Ajouter StatusPage à partir de la galerie

Pour configurer l’intégration de StatusPage avec Azure AD, vous devez ajouter StatusPage à partir de la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **StatusPage** dans la zone de recherche.
1. Sélectionnez **StatusPage** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-statuspage"></a>Configurer et tester l’authentification unique Azure AD pour StatusPage

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec StatusPage à l’aide d’un utilisateur de test appelé **Britta Simon**.
Pour que l’authentification unique fonctionne, une relation entre l’utilisateur Azure AD et l’utilisateur StatusPage associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec StatusPage, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique StatusPage](#configure-statuspage-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test StatusPage](#create-statuspage-test-user)** pour avoir un équivalent de Britta Simon dans StatusPage lié à la représentation Azure AD associée.
6. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **StatusPage**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Sur la page **Configurer l’authentification unique avec SAML**, effectuez les étapes suivantes :

    a. Dans la zone de texte **Identificateur**, tapez une URL en utilisant un des modèles suivants :

    | Identificateur |
    |--------------|
    | `https://<subdomain>.statuspagestaging.com/` |
    | `https://<subdomain>.statuspage.io/` |
    |

    b. Dans la zone de texte **URL de réponse** , tapez une URL en utilisant un des modèles suivants :

     | URL de réponse |
    |--------------|
    | `https://<subdomain>.statuspagestaging.com/sso/saml/consume` |
    | `https://<subdomain>.statuspage.io/sso/saml/consume` |
    |

    > [!NOTE]
    > Contactez l’équipe de support StatusPage à l’adresse [SupportTeam@statuspage.io](mailto:SupportTeam@statuspage.io)pour demander les métadonnées nécessaires à la configuration de l’authentification unique. 
    >
    > a. À partir des métadonnées, copiez la valeur de l’émetteur et collez-la dans la zone de texte **Identificateur** .
    >
    > b. À partir des métadonnées, copiez l’URL de réponse et collez-la dans la zone de texte **URL de réponse** .

5. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (Base64)** en fonction des options définies par rapport à vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

6. Dans la section **Configurer StatusPage**, copiez la ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à StatusPage.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, **Toutes les applications**, puis **StatusPage**.

2. Dans la liste des applications, sélectionnez **StatusPage**.

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

4. Cliquez sur le bouton **Ajouter un utilisateur**, puis sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

6. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.

7. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-statuspage-sso"></a>Configurer l’authentification unique StatusPage

1. Pour automatiser la configuration dans StatusPage, vous devez installer l’**extension de navigateur My Apps Secure Sign-in** en cliquant sur **Installer l’extension**.

    ![Extension My apps](common/install-myappssecure-extension.png)

2. Une fois l’extension ajoutée au navigateur, cliquez sur **Configurer StatusPage** pour être dirigé vers l’application StatusPage. À partir de là, indiquez les informations d’identification de l’administrateur pour vous connecter à StatusPage. Cette extension de navigateur configure automatiquement l’application et automatise les étapes 3 à 6.

    ![Configuration](common/setup-sso.png)

3. Si vous souhaitez configurer StatusPage manuellement, dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise StatusPage en tant qu’administrateur.

1. Dans la barre d’outils principale, cliquez sur **Manage Account**(Gérer le compte).

    ![Capture d’écran montrant la sélection de Manage Account sur le site d’entreprise StatusPage.](./media/statuspage-tutorial/account.png)

1. Cliquez sur l’onglet **Single Sign-on** (Authentification unique).

    ![Capture d’écran montrant l’onglet Single Sign-On.](./media/statuspage-tutorial/tab.png)

1. Sur la page de configuration de l’authentification unique, procédez comme suit :

    ![Capture d’écran montrant la page SSO Setup dans laquelle vous pouvez indiquer les valeurs décrites.](./media/statuspage-tutorial/setup.png)

    ![Capture d’écran montrant le bouton Save Configuration.](./media/statuspage-tutorial/configuration.png)

    a. Dans la zone de texte **SSO Target URL** (URL cible d’authentification unique), collez la valeur **URL de connexion**, que vous avez copiée à partir du portail Azure.

    b. Ouvrez le certificat que vous avez téléchargé dans le Bloc-notes, copiez son contenu, puis collez-le dans la zone de texte **Certificat** .

    c. Cliquez sur **ENREGISTRER LA CONFIGURATION**.

### <a name="create-statuspage-test-user"></a>Créer un utilisateur de test StatusPage

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans StatusPage.

StatusPage prend en charge l’approvisionnement juste-à-temps. Vous l’avez déjà activé dans [Configurer l’authentification unique Azure AD](#configure-azure-ad-sso).

**Pour créer un utilisateur appelé Britta Simon dans StatusPage, procédez comme suit :**

1. Connectez-vous à votre site d’entreprise StatusPage en tant qu’administrateur.

1. Dans le menu situé en haut, cliquez sur **Manage Account**.

    ![Capture d’écran montrant la sélection de Manage Account sur le site d’entreprise StatusPage.](./media/statuspage-tutorial/account.png)

1. Cliquez sur l’onglet **Team Members** (Membres de l’équipe).
  
    ![Capture d’écran affichant l’onglet Team Members.](./media/statuspage-tutorial/sandbox.png) 

1. Cliquez sur l’onglet **ADD TEAM MEMBER** (Ajouter un membre d’équipe).
  
    ![Capture d’écran affichant le bouton Add Team Member.](./media/statuspage-tutorial/team.png) 

1. Entrez l'adresse e-mail, le prénom et le nom de l'utilisateur valide que vous souhaitez approvisionner dans les zones de texte correspondantes, à savoir, **Email Address** (Adresse e-mail), **First Name** (Prénom) et **Surname** (Nom). 

    ![Capture d’écran montrant la boîte de dialogue Add a User dans laquelle vous pouvez entrer les valeurs décrites.](./media/statuspage-tutorial/user.png) 

1. Pour **Role**, choisissez **Client Administrator**.

1. Cliquez sur **CREATE ACCOUNT** (Créer un compte).

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes.

* Cliquez sur Tester cette application dans le portail Azure ; vous êtes alors connecté automatiquement à l’instance de StatusPage pour laquelle vous avez configuré l’authentification unique.

* Vous pouvez utiliser Mes applications de Microsoft. Un clic sur la vignette StatusPage dans Mes applications doit vous connecter automatiquement à l’application StatusPage pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez configuré StatusPage, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
