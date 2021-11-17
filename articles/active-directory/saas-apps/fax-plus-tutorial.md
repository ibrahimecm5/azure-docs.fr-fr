---
title: 'Tutoriel : Intégration de l’authentification unique Azure Active Directory à FAX.PLUS | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et FAX.PLUS.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/19/2021
ms.author: jeedes
ms.openlocfilehash: 880df37b09fd36d22f9176839bbe534ebcb116a0
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132338019"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-faxplus"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory à FAX.PLUS

Dans ce tutoriel, vous allez apprendre à intégrer FAX.PLUS à Azure Active Directory (Azure AD). Quand vous intégrez FAX.PLUS à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à FAX.PLUS.
* Permettre à vos utilisateurs de se connecter automatiquement à FAX.PLUS avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement FAX.PLUS pour lequel l’authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* FAX.PLUS prend en charge l’authentification unique initiée par **un fournisseur de services et un fournisseur d’identité**.
* FAX.PLUS prend en charge l’attribution d’utilisateurs **juste-à-temps**.

> [!NOTE]
> L’identificateur de cette application étant une valeur de chaîne fixe, une seule instance peut être configurée dans un locataire.

## <a name="add-faxplus-from-the-gallery"></a>Ajouter FAX.PLUS à partir de la galerie

Pour configurer l’intégration de FAX.PLUS à Azure AD, vous devez ajouter FAX.PLUS à votre liste d’applications SaaS gérées à partir de la galerie.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, saisissez **FAX.PLUS** dans la zone de recherche.
1. Sélectionnez **FAX.PLUS** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.


## <a name="configure-and-test-azure-ad-sso-for-faxplus"></a>Configurer et tester l’authentification unique Azure AD pour FAX.PLUS

Configurez et testez l’authentification unique Azure AD avec FAX.PLUS à l’aide d’un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur associé dans FAX.PLUS.

Pour configurer et tester l’authentification unique Azure AD avec FAX.PLUS, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique FAX.PLUS](#configure-faxplus-sso)** pour configurer les paramètres d’authentification unique côté application.
    1. **[Créer un utilisateur de test FAX.PLUS](#create-faxplus-test-user)** pour avoir dans FAX.PLUS un équivalent de B.Simon qui soit lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **FAX.PLUS**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, l’utilisateur n’a rien à faire, car l’application est déjà intégrée à Azure.

1. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    Dans la zone de texte **URL de connexion**, tapez l’URL : `https://www.fax.plus/login`

1. Cliquez sur **Enregistrer**.

1. L’application FAX.PLUS s’attend à recevoir les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à votre configuration des attributs de jeton SAML. La capture d’écran suivante montre la liste des attributs par défaut.

    ![image](common/default-attributes.png)

1. En plus de ce qui précède, l’application FAX.PLUS s’attend à ce que quelques attributs supplémentaires (présentés ci-dessous) soient retransmis dans la réponse SAML. Ces attributs sont également préremplis, mais vous pouvez les examiner pour voir s’ils répondent à vos besoins.
    
    | Nom | Attribut source|
    | --------------- | --------- |
    | firstname  | user.givenname |
    | lastname   | user.surname   |

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **Certificat (en base64)** , puis sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

1. Dans la section **Configurer FAX.PLUS**, copiez les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à FAX.PLUS.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **FAX.PLUS**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-faxplus-sso"></a>Configurer l’authentification unique FAX.PLUS

1. Pour automatiser la configuration dans FAX.PLUS, vous devez installer l’**extension de navigateur Connexion sécurisée à Mes applications** en cliquant sur **Installer l’extension**.

    ![Extension My apps](common/install-myappssecure-extension.png)

2. Une fois l’extension ajoutée au navigateur, un clic sur **Configurer FAX.PLUS** vous redirige vers l’application FAX.PLUS. À partir de là, indiquez les informations d’identification de l’administrateur pour vous connecter à FAX.PLUS. Cette extension de navigateur configure automatiquement l’application et automatise les étapes 3 à 5.

    ![Configuration](common/setup-sso.png)

3. Si vous voulez configurer FAX.PLUS manuellement, dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise FAX.PLUS en tant qu’administrateur.

2. Accédez à la section **Sécurité** dans votre profil d’administrateur et faites défiler la page jusqu’à **Avancé**.

3. Dans le panneau **Configuration**, cliquez sur le bouton **Activer l’authentification unique** et procédez comme suit.
    
    ![Compte](./media/fax.plus-tutorial/configuration.png "Compte") 

    a. Dans la zone de texte **ID d’entité**, collez la valeur **Identificateur Azure AD** que vous avez copiée dans le portail Azure.

    b. Dans la zone de texte **URL d’authentification unique**, collez l’**URL de connexion** que vous avez copiée à partir du portail Azure.

    c. Ouvrez le **Certificat (en base64)** téléchargé à partir du portail Azure dans le Bloc-notes, puis collez le contenu dans la zone de texte **X.509 Certificate**.

    d. Si vous souhaitez vous connecter par le biais de l’authentification unique, cochez la case **Autoriser uniquement la connexion par authentification unique pour l’utilisateur administrateur**. 

    e. Cliquez sur **Confirmer**.

### <a name="create-faxplus-test-user"></a>Créer un utilisateur de test FAX.PLUS

Dans cette section, un utilisateur appelé Britta Simon est créé dans FAX.PLUS. FAX.PLUS prend en charge l’attribution d’utilisateurs juste-à-temps, option qui est activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas encore d’utilisateur dans FAX.PLUS, un nouvel utilisateur est créé après l’authentification.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

#### <a name="sp-initiated"></a>Lancée par le fournisseur de services :

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion à FAX.PLUS, d’où vous pouvez lancer le flux de connexion.  

* Accédez directement à l’URL de connexion à FAX.PLUS pour lancer le processus de connexion.

#### <a name="idp-initiated"></a>Lancée par le fournisseur d’identité :

* Dans le portail Azure, cliquez sur **Tester cette application**. Vous êtes alors automatiquement connecté à l’instance de FAX.PLUS pour laquelle vous avez configuré l’authentification unique. 

Vous pouvez aussi utiliser Mes applications de Microsoft pour tester l’application dans n’importe quel mode. Si, quand vous cliquez sur la vignette FAX.PLUS dans Mes applications, le mode Fournisseur de services est configuré, vous êtes redirigé vers la page de connexion de l’application pour lancer le flux de connexion. S’il s’agit du mode Fournisseur d’identité, vous êtes automatiquement connecté à l’instance de FAX.PLUS pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré FAX.PLUS, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
