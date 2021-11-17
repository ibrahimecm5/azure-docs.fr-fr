---
title: 'Didacticiel : Intégration d’Azure Active Directory à Hosted Graphite | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Hosted Graphite.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/11/2021
ms.author: jeedes
ms.openlocfilehash: 185838b45f656e1b28b25749b215ab992d039131
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132291362"
---
# <a name="tutorial-azure-active-directory-integration-with-hosted-graphite"></a>Didacticiel : Intégration d’Azure Active Directory à Hosted Graphite

Dans ce didacticiel, vous allez apprendre à intégrer Hosted Graphite à Azure Active Directory (Azure AD). Quand vous intégrez Hosted Graphite à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Hosted Graphite.
* Permettre à vos utilisateurs de se connecter automatiquement à Hosted Graphite avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Hosted Graphite pour lequel l’authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Hosted Graphite prend en charge l’authentification unique lancée par **le fournisseur de services et le fournisseur d’identité**.
* Hosted Graphite prend en charge le provisionnement d’utilisateurs **juste-à-temps**.

## <a name="add-hosted-graphite-from-the-gallery"></a>Ajouter Hosted Graphite à partir de la galerie

Pour configurer l’intégration de Hosted Graphite à Azure AD, vous devez ajouter Hosted Graphite à partir de la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Hosted Graphite** dans la zone de recherche.
1. Sélectionnez **Hosted Graphite** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-hosted-graphite"></a>Configurer et tester l’authentification unique Azure AD pour Hosted Graphite

Configurez et testez l’authentification unique Azure AD avec Hosted Graphite pou un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur associé dans Hosted Graphite.

Pour configurer et tester l’authentification unique Azure AD avec Hosted Graphite, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Hosted Graphite](#configure-hosted-graphite-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test Hosted Graphite](#create-hosted-graphite-test-user)** pour avoir un équivalent de B.Simon dans Hosted Graphite associé à sa représentation dans Azure AD.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **Hosted Graphite**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

4. À la section **Configuration SAML de base**, si vous souhaitez configurer l’application en mode initié par **IDP**, suivez les étapes ci-dessous :

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://www.hostedgraphite.com/metadata/<USER_ID>`

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://www.hostedgraphite.com/complete/saml/<USER_ID>`

5. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://www.hostedgraphite.com/login/saml/<USER_ID>/`

    > [!NOTE]
    > Notez qu’il ne s’agit pas des valeurs réelles. Vous devez mettre à jour ces valeurs avec l’identificateur, l’URL de réponse et l’URL de connexion réels. Pour obtenir ces valeurs, vous pouvez accéder à Accéder->Configuration de SAML ou contactez [l’équipe de support d’Hosted Graphite](mailto:help@hostedgraphite.com).

6. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (Base64)** en fonction des options définies par rapport à vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

7. Dans la section **Configurer Hosted Graphite**, copiez la ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Hosted Graphite.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Hosted Graphite**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-hosted-graphite-sso"></a>Configurer l’authentification unique Hosted Graphite

1. Connectez-vous à votre client Hosted Graphite en tant qu’administrateur.

2. Accédez à la **page de configuration de SAML** dans la barre latérale (**Accéder -> Configuration SAML**).

    ![Capture d’écran montrant le menu Access avec SAML Setup sélectionné.](./media/hostedgraphite-tutorial/setup.png)

3. Confirmez que ces URL correspondent à votre configuration dans la section **Configuration SAML de base** du portail Azure.

    ![Capture d’écran montrant Basic SAML Configuration.](./media/hostedgraphite-tutorial/configuration.png)

4. Dans les zones de texte **ID d’entité ou d’émetteur** et **URL de connexion à authentification unique**, collez les valeurs **Identificateur Azure AD** et **URL de connexion**  que vous avez copiées à partir du portail Azure.

    ![Capture d’écran montrant des entrées pour Identity Provider.](./media/hostedgraphite-tutorial/integration.png)

5. Sélectionnez **Lecture seule** comme **Rôle d’utilisateur par défaut**.

    ![Capture d’écran montrant Default User Role, qui est Read-only.](./media/hostedgraphite-tutorial/role.png)

6. Ouvrez dans le Bloc-notes votre certificat codé en base 64 téléchargé à partir du portail Azure, copiez son contenu dans le Presse-papiers, puis collez-le dans la zone de texte **Certificat X.509**.

    ![Capture d’écran montrant un certificat x.509.](./media/hostedgraphite-tutorial/certificate.png)

7. Cliquez sur le bouton **Enregistrer** .

### <a name="create-hosted-graphite-test-user"></a>Créer un utilisateur de test Hosted Graphite

Dans cette section, un utilisateur appelé Britta Simon est créé dans Hosted Graphite. Hosted Graphite prend en charge l’attribution d’utilisateurs juste-à-temps, qui est une option activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas encore d’utilisateur dans Hosted Graphite, il en est créé un après l’authentification.

> [!NOTE]
> Si vous devez créer un utilisateur manuellement, contactez l’[équipe du support technique de Hosted Graphite](mailto:help@hostedgraphite.com).

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

#### <a name="sp-initiated"></a>Lancée par le fournisseur de services :

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion à Hosted Graphite, d’où vous pouvez lancer le flux de connexion.  

* Accédez directement à l’URL de connexion à Hosted Graphite pour lancer le flux de connexion.

#### <a name="idp-initiated"></a>Lancée par le fournisseur d’identité :

* Cliquez sur **Tester cette application** dans le portail Azure. Vous devez alors être automatiquement connecté à l’instance de Hosted Graphite pour laquelle vous avez configuré l’authentification unique. 

Vous pouvez aussi utiliser Mes applications de Microsoft pour tester l’application dans n’importe quel mode. Si, quand vous cliquez sur la vignette Hosted Graphite dans Mes applications, le mode Fournisseur de services est configuré, vous êtes redirigé vers la page de connexion de l’application pour lancer le flux de connexion ; s’il s’agit du mode Fournisseur d’identité, vous êtes automatiquement connecté à l’instance de Hosted Graphite pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré Hosted Graphite, vous pouvez appliquer le contrôle de session, qui protège en temps réel contre l’exfiltration et l’infiltration des données sensibles de votre organisation. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
