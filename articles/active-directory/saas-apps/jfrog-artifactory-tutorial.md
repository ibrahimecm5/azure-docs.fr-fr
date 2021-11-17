---
title: 'Tutoriel : Intégration d’Azure Active Directory avec JFrog Artifactory | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et JFrog Artifactory.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/09/2021
ms.author: jeedes
ms.openlocfilehash: 551ec61a167f81be638c72604d982e1903b33b91
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132295386"
---
# <a name="tutorial-integrate-jfrog-artifactory-with-azure-active-directory"></a>Tutoriel : Intégrer JFrog Artifactory à Azure Active Directory

Dans ce tutoriel, vous allez découvrir comment intégrer JFrog Artifactory à Azure Active Directory (Azure AD). Quand vous intégrez JFrog Artifactory à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à JFrog Artifactory.
* Permettre à vos utilisateurs de se connecter automatiquement à JFrog Artifactory avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement JFrog Artifactory pour lequel l’authentification unique (SSO) est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* JFrog Artifactory prend en charge l’authentification unique lancée par **le fournisseur de services et le fournisseur d’identité**.
* JFrog Artifactory prend en charge le provisionnement d’utilisateurs **Juste-à-temps**.

## <a name="add-jfrog-artifactory-from-the-gallery"></a>Ajouter JFrog Artifactory à partir de la galerie

Pour configurer l’intégration de JFrog Artifactory à Azure AD, vous devez ajouter JFrog Artifactory à partir de la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **JFrog Artifactory** dans la zone de recherche.
1. Sélectionnez **JFrog Artifactory** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-jfrog-artifactory"></a>Configurer et tester l’authentification unique Azure AD pour JFrog Artifactory

Configurez et testez l’authentification unique Azure AD avec JFrog Artifactory pour un utilisateur de test nommé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur JFrog Artifactory associé.

Pour configurer et tester l’authentification unique Azure AD auprès de JFrog Artifactory, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique JFrog Artifactory](#configure-jfrog-artifactory-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test JFrog Artifactory](#create-jfrog-artifactory-test-user)** pour avoir un équivalent de B.Simon dans JFrog Artifactory lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **JFrog Artifactory**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon pour **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, si vous souhaitez configurer l’application en mode Initié par le **fournisseur d’identité**, entrez les valeurs pour les champs suivants :

    a. Dans la zone de texte **Identificateur**, entrez une URL au format de l’URL d’Artifactory.

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant :
    
    - Pour l’auto-hébergement d’Artifactory : `https://<servername>.jfrog.io/artifactory/webapp/saml/loginResponse`
    - Pour le SaaS Artifactory : `https://<servername>.jfrog.io/<servername>/webapp/saml/loginResponse`

1. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    Dans la zone de texte **URL d’authentification**, tapez une URL au format suivant :
    - Pour l’auto-hébergement d’Artifactory : `https://<servername>.jfrog.io/<servername>/webapp/`
    - Pour le SaaS Artifactory : `https://<servername>.jfrog.io/ui/login`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL de réponse et l’URL de connexion réels. Pour obtenir ces valeurs, contactez l’[équipe de support technique JFrog Artifactory](https://support.jfrog.com). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. Votre application JFrog Artifactory s’attend à recevoir les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à votre configuration des attributs de jetons SAML. La capture d’écran suivante montre la liste des attributs par défaut. Cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue des attributs et des revendications utilisateur.

    ![Capture d’écran montrant Attributs utilisateur avec le contrôle d’édition activé.](common/edit-attribute.png)

1. En plus de ce qui précède, l’application JFrog Artifactory s’attend à ce que quelques attributs supplémentaires soient repassés dans la réponse SAML. Dans la section **Attributs et revendications de l’utilisateur**, cliquez sur **Ajouter une revendications de groupe**, effectuez les étapes suivantes :

    a. Cliquez sur le **stylo** en regard de **Groupes renvoyés dans la revendication**.

    ![Capture d’écran montrant User Attributes & Claims avec l’icône Edit sélectionnée.](./media/jfrog-artifactory-tutorial/configuration-4.png)

    ![Capture d’écran montrant la section Revendications de groupe avec l’option Tous les groupes sélectionnée.](./media/jfrog-artifactory-tutorial/configuration-5.png)

    b. Sélectionnez **Tous les groupes** dans la liste d’options.

    c. Cliquez sur **Enregistrer**.

4. Dans la page **Configurer l’authentification unique avec SAML**, à la section **Certificat de signature SAML**, recherchez **Certificat (Base64)** et sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/jfrog-artifactory-tutorial/certificate-base.png)

6. Configurez (Nom du fournisseur de services SAML) Artifactory avec le champ « Identificateur » (voir l’étape 4). Dans la section **Configurer JFrog Artifactory**, copiez la ou les URL appropriées en fonction de vos besoins.

   - Pour l’auto-hébergement d’Artifactory : `https://<servername>.jfrog.io/artifactory/webapp/saml/loginResponse` 
   - Pour le SaaS Artifactory : `https://<servername>.jfrog.io/<servername>/webapp/saml/loginResponse`

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à JFrog Artifactory.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **JFrog Artifactory**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-jfrog-artifactory-sso"></a>Configurer l’authentification unique JFrog Artifactory

Pour configurer l’authentification unique côté **JFrog Artifactory**, vous devez envoyer le **Certificat (brut)** téléchargé et les URL copiées appropriées du portail Azure à l’[équipe du support technique JFrog Artifactory](https://support.jfrog.com). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

### <a name="create-jfrog-artifactory-test-user"></a>Créer un utilisateur de test JFrog Artifactory

Dans cette section, un utilisateur appelé B.Simon est créé dans JFrog Artifactory. JFrog Artifactory prend en charge le provisionnement d’utilisateurs juste-à-temps, une option activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. Si l’utilisateur souhaité n’existe pas déjà dans JFrog Artifactory, il est créé après l’authentification.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

#### <a name="sp-initiated"></a>Lancée par le fournisseur de services :

* Cliquez sur **Tester cette application** dans le portail Azure. Cette opération redirige vers l’URL de connexion à JFrog Artifactory, d’où vous pouvez lancer le processus de connexion.  

* Accédez directement à l’URL de connexion JFrog Artifactory et lancez le processus de connexion.

#### <a name="idp-initiated"></a>Lancée par le fournisseur d’identité :

* Un clic sur **Tester cette application** dans le portail Azure doit vous connecter automatiquement à l’instance de JFrog Artifactory pour laquelle vous avez configuré l’authentification unique. 

Vous pouvez aussi utiliser Mes applications de Microsoft pour tester l’application dans n’importe quel mode. Si, quand vous cliquez sur la vignette JFrog Artifactory dans Mes applications, le mode Fournisseur de services est configuré, vous êtes redirigé vers la page de connexion de l’application pour lancer le flux de connexion. S’il s’agit du mode Fournisseur d’identité, vous êtes automatiquement connecté à l’instance JFrog Artifactory pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré JFrog Artifactory, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
