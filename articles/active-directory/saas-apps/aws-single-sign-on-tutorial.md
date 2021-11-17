---
title: 'Tutoriel : Intégration de l’authentification unique Azure AD à AWS Single Sign-on'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et AWS Single Sign-on.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/26/2021
ms.author: jeedes
ms.openlocfilehash: b6154d6542e0f61326c31aeca7abc15d671935ea
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132336370"
---
# <a name="tutorial-azure-ad-sso-integration-with-aws-single-sign-on"></a>Tutoriel : Intégration de l’authentification unique Azure AD à AWS Single Sign-on

Dans ce tutoriel, vous allez apprendre à intégrer AWS Single Sign-on à Azure Active Directory (Azure AD). Quand vous intégrez AWS Single Sign-on à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à AWS Single Sign-on.
* Permettre à vos utilisateurs de se connecter automatiquement à AWS Single Sign-on avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Abonnement AWS Single Sign-on pour lequel l’authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* AWS Single Sign-on prend en charge l’authentification unique lancée par le **fournisseur de services et le fournisseur d’identité**.

* AWS Single Sign-on prend en charge [**l’attribution automatique d’utilisateurs**](./aws-single-sign-on-provisioning-tutorial.md).

## <a name="adding-aws-single-sign-on-from-the-gallery"></a>Ajout d’AWS Single Sign-on à partir de la galerie

Pour configurer l’intégration d’AWS Single Sign-on à Azure AD, vous devez ajouter AWS Single Sign-on à votre liste d’applications SaaS gérées à partir de la galerie.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, saisissez **AWS Single Sign-on** dans la zone de recherche.
1. Sélectionnez **AWS Single Sign-on** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.


## <a name="configure-and-test-azure-ad-sso-for-aws-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD pour AWS Single Sign-on

Configurez et testez l’authentification unique Azure AD avec AWS Single Sign-on à l’aide d’un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur AWS Single Sign-on associé.

Pour configurer et tester l’authentification unique Azure AD avec AWS Single Sign-on, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique AWS Single Sign-on](#configure-aws-single-sign-on-sso)** pour configurer les paramètres d’authentification unique côté application.
    1. **[Créer un utilisateur de test AWS Single Sign-on](#create-aws-single-sign-on-test-user)** pour avoir un équivalent de B.Simon dans AWS Single Sign-on qui soit lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **AWS Single Sign-on**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Si vous disposez d’un **fichier de métadonnées du fournisseur de services**, dans la section **Configuration SAML de base**, suivez les étapes ci-dessous :

    a. Cliquez sur **Charger un fichier de métadonnées**.

    b. Cliquez sur le **logo du dossier** pour sélectionner le fichier de métadonnées dont le téléchargement est expliqué dans la section **[Configurer l’authentification unique AWS Single Sign-on](#configure-aws-single-sign-on-sso)** , puis cliquez sur **Ajouter**.

    ![image2](common/browse-upload-metadata.png)

    c. Une fois le fichier de métadonnées chargé, les valeurs **Identificateur** et **URL de réponse** sont automatiquement renseignées dans la section Configuration SAML de base :

    ![image3](common/idp-intiated.png)

    > [!Note]
    > Si les valeurs **Identificateur** et **URL de réponse** ne sont pas automatiquement renseignées, renseignez-les manuellement en fonction de vos besoins.

1. Si vous ne disposez pas d’un **fichier de métadonnées du fournisseur de services**, effectuez les étapes ci-dessous dans la section **Configuration SAML de base**. Si vous souhaitez configurer l’application en mode Initié par le **fournisseur d’identité**, entrez les valeurs pour les champs suivants :

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<REGION>.signin.aws.amazon.com/platform/saml/<ID>`

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://<REGION>.signin.aws.amazon.com/platform/saml/acs/<ID>`

1. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://portal.sso.<REGION>.amazonaws.com/saml/assertion/<ID>`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL de réponse et l’URL de connexion réels. Pour obtenir ces valeurs, contactez l’[équipe du support client d’AWS Single Sign-on](mailto:aws-sso-partners@amazon.com). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. L’application AWS Single Sign-on s’attend à recevoir les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à votre configuration des attributs de jeton SAML. La capture d’écran suivante montre la liste des attributs par défaut.

    ![image](common/edit-attribute.png)


    > [!NOTE]
    > Si ABAC est activé dans l’authentification unique AWS, des attributs supplémentaires peuvent être transmis en tant qu’étiquettes de session directement aux comptes AWS.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **Certificat (Base64)** et sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

1. Dans la section **Configurer AWS Single Sign-on**, copiez les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à AWS Single Sign-on.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **AWS Single Sign-on**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-aws-single-sign-on-sso"></a>Configurer l’authentification unique AWS Single Sign-on

1. Pour automatiser la configuration dans AWS Single Sign-on, vous devez installer l’**extension de navigateur de connexion sécurisée à Mes applications** en cliquant sur **Installer l’extension**.

    ![Extension My apps](common/install-myappssecure-extension.png)

2. Après l’ajout de l’extension au navigateur, cliquez sur **Configurer AWS Single Sign-on** pour être dirigé vers l’application AWS Single Sign-on. À partir de là, fournissez les informations d’identification d’administrateur pour vous connecter à AWS Single Sign-on. Cette extension de navigateur configure automatiquement l’application et automatise les étapes 3 à 10.

    ![Configuration](common/setup-sso.png)

3. Si vous voulez configurer AWS Single Sign-on manuellement, dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise AWS Single Sign-on en tant qu’administrateur.

1. Accédez à **Services -> Security, Identity, & Compliance (Services -> Sécurité, identité et conformité) -> AWS Single Sign-On**.
2. Dans le volet de navigation de gauche, sélectionnez **Settings** (Paramètres).
3. Dans la page **Settings** (Paramètres), recherchez **Identity source** (Source d’identité), puis cliquez sur **Change** (Modifier).

    ![Capture d’écran du service de changement de la source d’identité](./media/aws-single-sign-on-tutorial/settings.png)

4. Dans la page Change identity source (Changer la source d’identité), choisissez **External identity provider** (Fournisseur d’identité externe).

    
    ![Capture d’écran de la section de sélection d’un fournisseur d’identité externe](./media/aws-single-sign-on-tutorial/external-identity-provider.png)


1. Effectuez les étapes ci-dessous dans la section **Configure external identity provider** (Configurer un fournisseur d’identité externe) :

    ![Capture d’écran de la section de téléchargement et de chargement des métadonnées](./media/aws-single-sign-on-tutorial/upload-metadata.png)

    a. Dans la section **Service provider metadata** (Métadonnées du fournisseur de services), recherchez **AWS SSO SAML metadata** (Métadonnées SAML de l’authentification unique AWS), puis sélectionnez **Download metadata file** (Télécharger le fichier de métadonnées) afin de télécharger le fichier de métadonnées, de l’enregistrer sur votre ordinateur, puis de l’utiliser pour effectuer le chargement sur le portail Azure.

    b. Copiez la valeur du champ **AWS SSO Sign-in URL** (URL de connexion avec authentification unique AWS), puis collez-la dans la zone de texte **URL de connexion** de la section **Configuration SAML de base** du portail Azure.

    c. Dans la section **Identity provider metadata** (Métadonnées du fournisseur d’identité), choisissez **Browse** (Parcourir) pour charger le fichier de métadonnées que vous avez téléchargé sur le portail Azure.

    d. Choisissez **Next: Review** (Suivant : Vérifier).

8. Dans la zone de texte, tapez **ACCEPT** (ACCEPTER) pour changer la source d’identité.

    ![Capture d’écran pour la confirmation de la configuration](./media/aws-single-sign-on-tutorial/accept.png)

9. Cliquez sur **Change identity source** (Changer la source d’identité).

### <a name="create-aws-single-sign-on-test-user"></a>Créer un utilisateur de test AWS Single Sign-on

1. Ouvrez la **console d’authentification unique AWS**.

2. Dans le volet de navigation de gauche, choisissez **Users** (Utilisateurs).

3. Dans la page Users, choisissez **Add user** (Ajouter un utilisateur).

4. Dans la page Add user, procédez comme suit :

    a. Dans le champ **Username** (Nom d’utilisateur), entrez B.Simon.

    b. Dans le champ **Email address** (Adresse e-mail), entrez `username@companydomain.extension`. Par exemple : `B.Simon@contoso.com`.

    c. Dans le champ **Confirm email address** (Confirmer l’adresse e-mail), entrez à nouveau l’adresse e-mail de l’étape précédente.

    d. Dans le champ First name (Prénom), entrez `Jane`.

    e. Dans le champ Last name (Nom), entrez `Doe`.

    f. Dans le champ Display name (Nom d’affichage), saisissez `Jane Doe`.

    g. Choisissez **Next: Groups** (Suivant : Groupes).

    > [!NOTE]
    > Assurez-vous que le nom d’utilisateur entré dans l’authentification unique AWS correspond au nom de connexion Azure AD de l’utilisateur. Cela vous permettra d’éviter tout problème d’authentification.

5. Choisissez **Ajouter un utilisateur**.
6. Vous allez ensuite attribuer l’utilisateur à votre compte AWS. Pour ce faire, dans le volet de navigation de gauche de la console d’authentification unique AWS, choisissez **AWS accounts** (Comptes AWS).
7. Dans la page AWS Accounts, sélectionnez l’onglet AWS organization (Organisation AWS), cochez la case à côté du compte AWS que vous souhaitez attribuer à l’utilisateur. Choisissez ensuite **Assign users** (Attribuer des utilisateurs).
8. Dans la page Assign Users, recherchez l’utilisateur B.Simon et cochez la case qui lui correspond. Ensuite, choisissez **Next: Permission sets** (Suivant : Jeux d’autorisations).
9. Sous la section Select permission sets (Sélectionner des jeux d’autorisations), cochez la case à côté du jeu d’autorisations que vous souhaitez attribuer à l’utilisateur B.Simon. Si aucun jeu d’autorisations n’existe, choisissez **Create new permission set** (Créer un nouveau jeu d’autorisations).

    > [!NOTE]
    > Les jeux d’autorisations définissent le niveau d’accès dont disposent les utilisateurs et les groupes sur un compte AWS. Pour en savoir plus sur les jeux d’autorisations, consultez la page **Permission Sets** de l’authentification unique AWS.
10. Cliquez sur **Terminer**.

> [!NOTE]
> AWS Single Sign-on prend également en charge l’attribution automatique d’utilisateurs. Vous trouverez plus d’informations [ici](./aws-single-sign-on-provisioning-tutorial.md) sur la façon de configurer l’attribution automatique d’utilisateurs.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

#### <a name="sp-initiated"></a>Lancée par le fournisseur de services :

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion à AWS Single Sign-on, à partir de laquelle vous pouvez lancer le flux de connexion.  

* Accédez directement à l’URL de connexion à AWS Single Sign-on pour lancer le flux de connexion.

#### <a name="idp-initiated"></a>Lancée par le fournisseur d’identité :

* Dans le portail Azure, cliquez sur **Tester cette application**. Vous êtes alors automatiquement connecté à l’instance d’AWS Single Sign-on pour laquelle vous avez configuré l’authentification unique. 

Vous pouvez aussi utiliser Mes applications de Microsoft pour tester l’application dans n’importe quel mode. Si, quand vous cliquez sur la vignette AWS Single Sign-on dans Mes applications, le mode Fournisseur de services est configuré, vous êtes redirigé vers la page de connexion de l’application pour lancer le flux de connexion. S’il s’agit du mode Fournisseur d’identité, vous êtes automatiquement connecté à l’instance d’AWS Single Sign-on pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).


## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré AWS Single Sign-on, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
