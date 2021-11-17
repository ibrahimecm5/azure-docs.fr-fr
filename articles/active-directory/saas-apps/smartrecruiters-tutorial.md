---
title: 'Didacticiel : Intégration d’Azure Active Directory à SmartRecruiters | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et SmartRecruiters.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/03/2021
ms.author: jeedes
ms.openlocfilehash: 7cd271c1c7105e8e3f8ab137ff58efb92b0de4bd
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132329188"
---
# <a name="tutorial-azure-active-directory-integration-with-smartrecruiters"></a>Didacticiel : Intégration d’Azure Active Directory à SmartRecruiters

Dans ce tutoriel, vous allez découvrir comment intégrer SmartRecruiters à Azure Active Directory (Azure AD). Quand vous intégrez SmartRecruiters à Azure AD, vous pouvez :

* Contrôler, dans Azure AD, qui a accès à SmartRecruiters.
* Permettre aux utilisateurs de se connecter automatiquement à SmartRecruiters avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement SmartRecruiters pour lequel l’authentification SSO (authentification unique) est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* SmartRecruiters prend en charge l’authentification SSO lancée par **le fournisseur de services (SP) et le fournisseur d’identité (IdP)** .

## <a name="add-smartrecruiters-from-the-gallery"></a>Ajouter SmartRecruiters à partir de la galerie

Pour configurer l’intégration de SmartRecruiters à Azure AD, vous devez ajouter SmartRecruiters à partir de la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, dans la zone de recherche, tapez **SmartRecruiters**.
1. Sélectionnez **SmartRecruiters** dans le panneau Résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-smartrecruiters"></a>Configurer et tester l’authentification SSO Azure AD pour SmartRecruiters

Configurez et testez l’authentification SSO Azure AD avec SmartRecruiters pour une utilisatrice de test appelée **B.Simon**. Pour que l’authentification SSO fonctionne, vous devez établir une relation entre un utilisateur Azure AD et l’utilisateur associé dans SmartRecruiters.

Pour configurer et tester l’authentification SSO Azure AD avec SmartRecruiters, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification SSO pour SmartRecruiters](#configure-smartrecruiters-sso)** afin de configurer les paramètres d’authentification unique côté application.
    1. **[Créer une utilisatrice de test pour SmartRecruiters](#create-smartrecruiters-test-user)** afin de disposer dans SmartRecruiters d’un équivalent de B.Simon lié à la représentation Azure AD de l’utilisatrice.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, dans la page d’intégration de l’application **SmartRecruiters**, recherchez la section **Gérer**, puis sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

4. À la section **Configuration SAML de base**, si vous souhaitez configurer l’application en mode initié par **IDP**, suivez les étapes ci-dessous :

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://www.smartrecruiters.com/web-sso/saml/<companyname>`

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://www.smartrecruiters.com/web-sso/saml/<companyname>/callback`

5. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://www.smartrecruiters.com/web-sso/saml/<companyname>/login`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL de réponse et l’URL de connexion réels. Pour obtenir ces valeurs, contactez [l’équipe de support technique SmartRecruiters](https://www.smartrecruiters.com/about-us/contact-us/). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

6. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (Base64)** en fonction des options définies par rapport à vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

7. Dans la section **Configurer SmartRecruiters**, copiez la ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez permettre à B.Simon d’utiliser l’authentification unique Azure en lui octroyant l’accès à SmartRecruiters.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **SmartRecruiters**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-smartrecruiters-sso"></a>Configurer l’authentification SSO pour SmartRecruiters

1. Dans une autre fenêtre de navigateur web, connectez-vous au site de votre entreprise SmartRecruiters en tant qu’administrateur.

1. Accédez à **Paramètres / Administrateur**.

    ![Capture d’écran montrant l’élément Settings / Admin sélectionné à partir d’un menu.](./media/smartrecruiters-tutorial/configure.png)

1. Dans la section **Configuration**, cliquez sur **Authentification unique web**.

    ![Capture d’écran montrant la sélection de Web SSO dans Configuration.](./media/smartrecruiters-tutorial/configuration-section.png)

1. Choisissez **Activer l’authentification unique web**.

    ![La capture d’écran montre le contrôle Enable Web SSO.](./media/smartrecruiters-tutorial/enable-web.png)

1. Dans la section **Configuration du fournisseur d’identité**, procédez comme suit :

    ![Capture d’écran montrant la section Identity Provider Configuration, dans laquelle vous pouvez indiquer les valeurs décrites.](./media/smartrecruiters-tutorial/identity-provider.png)

    a. Dans la zone de texte **URL du fournisseur d’identité**, collez la valeur de l’**URL de connexion** que vous avez copiée à partir du portail Azure.

    b. Ouvrez le **Certificat (en base64)** que vous avez téléchargé à partir du portail Azure dans le Bloc-notes, copiez son contenu et collez-le dans la zone de texte **Certificat du fournisseur d’identité**.

1. Cliquez sur **Enregistrer la configuration de l’authentification unique web**.

### <a name="create-smartrecruiters-test-user"></a>Créer un utilisateur test de SmartRecruiters

Dans cette section, vous créez un utilisateur appelé Britta Simon dans SmartRecruiters. Contactez [l’équipe de support technique de SmartRecruiters](https://www.smartrecruiters.com/about-us/contact-us/) pour ajouter les utilisateurs sur la plateforme SmartRecruiters. Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

#### <a name="sp-initiated"></a>Lancée par le fournisseur de services :

* Cliquez sur **Tester cette application** dans le portail Azure. Une redirection est effectuée vers l’URL de connexion à SmartRecruiters, où vous pouvez lancer le flux de connexion.  

* Accédez directement à l’URL de connexion à SmartRecruiters, puis lancez le flux de connexion à partir de cet emplacement.

#### <a name="idp-initiated"></a>Lancée par le fournisseur d’identité :

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors connecté automatiquement à l’instance de SmartRecruiters pour laquelle vous avez configuré l’authentification SSO. 

Vous pouvez aussi utiliser Mes applications de Microsoft pour tester l’application dans n’importe quel mode. Si, quand vous cliquez sur la vignette SmartRecruiters dans Mes applications, le mode Fournisseur de services est configuré, vous êtes redirigé vers la page de connexion de l’application pour lancer le flux de connexion ; s’il s’agit du mode Fournisseur d’identité, vous êtes automatiquement connecté à l’instance de SmartRecruiters pour laquelle vous avez configuré l’authentification SSO. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez configuré SmartRecruiters, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
