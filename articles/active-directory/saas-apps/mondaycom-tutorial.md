---
title: 'Tutoriel : Intégration de l’authentification unique (SSO) Azure Active Directory avec monday.com | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et monday.com.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/08/2021
ms.author: jeedes
ms.openlocfilehash: df7406bcce758a21bd1f001a0309cd07e51d5373
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132344633"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-mondaycom"></a>Tutoriel : Intégration de l’authentification unique (SSO) Azure Active Directory avec monday.com

Dans ce tutoriel, vous allez apprendre à intégrer monday.com avec Azure Active Directory (Azure AD). Quand vous intégrez monday.com à Azure AD, vous pouvez effectuer les opérations suivantes :

* Dans Azure AD, contrôler qui a accès à monday.com.
* Permettre à vos utilisateurs de se connecter automatiquement à monday.com avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Abonnement monday.com pour lequel l’authentification unique (SSO) est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* monday.com prend en charge l’authentification unique initiée par le **fournisseur de services (SP) et le fournisseur d’identité (IDP)**
* monday.com prend en charge [**l’attribution d’utilisateurs et la suppression des privilèges d’accès** automatiques](mondaycom-provisioning-tutorial.md) (recommandé).
* monday.com prend en charge l’attribution d’utilisateurs **juste-à-temps**

## <a name="add-mondaycom-from-the-gallery"></a>Ajouter monday.com à partir de la galerie

Pour configurer l’intégration de monday.com dans Azure AD, vous devez ajouter monday.com à votre liste d’applications SaaS gérées à partir de la galerie.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **monday.com** dans la zone de recherche.
1. Sélectionnez **monday.com** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-mondaycom"></a>Configurer et tester l’authentification unique Azure AD pour monday.com

Configurez et testez l’authentification unique Azure AD avec MyVR à l’aide d’un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur monday.com associé.

Pour configurer et tester l’authentification unique Azure AD avec monday.com, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique monday.com](#configure-mondaycom-sso)** – pour configurer les paramètres d’authentification unique côté application.
    1. **[Créer un utilisateur de test monday.com](#create-mondaycom-test-user)** – pour avoir un équivalent de B.Simon dans monday.com qui soit lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **monday.com**, recherchez la section **Gérer**, puis sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, si vous avez un **fichier de métadonnées de fournisseur de services** et voulez une configuration en mode initié par le **fournisseur d’identité** (IDP), effectuez les étapes suivantes :

    a. Cliquez sur **Charger un fichier de métadonnées**.

    ![Charger le fichier de métadonnées](common/upload-metadata.png)

    b. Cliquez sur le **logo du dossier** pour sélectionner le fichier de métadonnées, puis cliquez sur **Charger**.

    ![choisir le fichier de métadonnées](common/browse-upload-metadata.png)

    c. Une fois le fichier de métadonnées chargé, les valeurs **Identificateur** et **URL de réponse** sont automatiquement renseignées dans la section Configuration SAML de base.

    > [!Note]
    > Si les valeurs **Identificateur** et **URL de réponse** ne sont pas automatiquement renseignées, renseignez-les manuellement. L’**Identificateur** et l’**URL de réponse** sont identiques et la valeur figure dans le modèle suivant : `https://<your-domain>.monday.com/saml/saml_callback`

1. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<YOUR_DOMAIN>.monday.com`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL de réponse et l’URL de connexion réels. Pour obtenir ces valeurs, contactez l’[équipe de support client monday.com](https://monday.com/contact-us/). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. L’application monday.com attend les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à votre configuration des attributs de jeton SAML. La capture d’écran suivante montre la liste des attributs par défaut.

    ![Capture d’écran montrant Attributs utilisateur et revendications avec des valeurs par défaut telles que Givenname user.givenname et Emailaddress User.mail.](common/default-attributes.png)

1. En plus de ce qui précède, l’application monday.com s’attend à ce que quelques attributs supplémentaires, indiqués ci-dessous, soient repassés dans la réponse SAML. Ces attributs sont également préremplis, mais vous pouvez les examiner pour voir s’ils répondent à vos besoins.

    | Nom | Attribut source |
    |--|--|
    | E-mail | user.mail |
    | FirstName | user.givenname |
    | LastName | user.surname |

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **Certificat (en base64)** , puis sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

1. Dans la section **Configurer monday.com**, copiez la ou les URL appropriées, selon vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à monday.com.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **monday.com**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-mondaycom-sso"></a>Configurer l’authentification unique monday.com

1. Pour automatiser la configuration dans monday.com, vous devez installer l’**extension de navigateur My Apps Secure Sign-in** en cliquant sur **Installer l’extension**.

    ![Extension My apps](common/install-myappssecure-extension.png)

1. Après l’ajout de l’extension au navigateur, cliquez sur **Configurer monday.com** pour être dirigé vers l’application monday.com. À partir de là, indiquez les informations d’identification de l’administrateur pour vous connecter à monday.com. Cette extension de navigateur configure automatiquement l’application et automatise les étapes 3 à 6.

    ![Configuration](common/setup-sso.png)

1. Si vous souhaitez configurer manuellement monday.com, ouvrez une nouvelle fenêtre de navigateur web, connectez-vous à votre site d’entreprise monday.com en tant qu’administrateur et effectuez les étapes suivantes :

1. Accédez à **Profile** en haut à droite de la page et cliquez sur **Admin**.

    ![Capture d’écran montrant le profil d’administrateur sélectionné.](./media/mondaycom-tutorial/configuration-1.png)

1. Sélectionnez **Sécurité** et cliquez sur **Ouvrir** en regard de SAML.

    ![Capture d’écran montrant l’onglet Security avec l’option permettant d’ouvrir qui est située en regard de SAML.](./media/mondaycom-tutorial/configuration-2.png)

1. Remplissez les détails ci-dessous à partir de votre fournisseur d’identité.

    ![Capture d’écran montrant la zone du fournisseur SAML dans laquelle vous pouvez entrer des informations à partir de votre fournisseur d’identité.](./media/mondaycom-tutorial/configuration-3.png)

    > [!NOTE]
    > Pour plus d’informations, consultez [cet](https://support.monday.com/hc/articles/360000460605-SAML-Single-Sign-on?abcb=34642) article.

### <a name="create-mondaycom-test-user"></a>Créer utilisateur de test monday.com

Dans cette section, un utilisateur appelé B.Simon est créé dans monday.com. monday.com prend en charge l’approvisionnement juste-à-temps, qui est activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas d’utilisateur dans monday.com, un nouvel utilisateur est créé lorsque vous tentez d’y accéder.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

#### <a name="sp-initiated"></a>Lancée par le fournisseur de services :

* Cliquez sur **Tester cette application** dans le portail Azure. Cette opération redirige vers l’URL de connexion monday.com, d’où vous pouvez lancer le processus de connexion.  

* Accédez directement à l’URL de connexion monday.com pour lancer le processus de connexion.

#### <a name="idp-initiated"></a>Lancée par le fournisseur d’identité :

* Dans le portail Azure, cliquez sur **Tester cette application**. Vous êtes alors automatiquement connecté à l’instance monday.com pour laquelle vous avez configuré l’authentification unique. 

Vous pouvez aussi utiliser Mes applications de Microsoft pour tester l’application dans n’importe quel mode. Si, quand vous cliquez sur la vignette monday.com dans Mes applications, le mode Fournisseur de services est configuré, vous êtes redirigé vers la page de connexion de l’application pour lancer le flux de connexion. S’il s’agit du mode Fournisseur d’identité, vous êtes automatiquement connecté à l’instance monday.com pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez configuré monday.com, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
