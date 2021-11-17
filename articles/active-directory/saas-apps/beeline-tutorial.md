---
title: 'Tutoriel : Intégration d’Azure Active Directory à Beeline | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Beeline.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/15/2021
ms.author: jeedes
ms.openlocfilehash: ff9611559467f24d1b01d4e628bdcf9642cca88a
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132336142"
---
# <a name="tutorial-azure-active-directory-integration-with-beeline"></a>Tutoriel : Intégration d’Azure Active Directory à Beeline

Dans ce tutoriel, vous allez apprendre à intégrer Beeline à Azure Active Directory (Azure AD). Quand vous intégrez Beeline à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Beeline.
* Permettre à vos utilisateurs de se connecter automatiquement à Beeline avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Beeline pour lequel l’authentification unique (SSO) est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Beeline prend en charge l’authentification unique lancée par le **fournisseur d’identité**.

## <a name="add-beeline-from-the-gallery"></a>Ajouter Beeline à partir de la galerie

Pour configurer l’intégration de Beeline avec Azure AD, vous devez ajouter Beeline disponible dans la galerie, à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Beeline** dans la zone de recherche.
1. Sélectionnez **Beeline** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-beeline"></a>Configurer et tester l’authentification unique Azure AD pour Beeline

Configurez et testez l’authentification unique Azure AD auprès de Beeline avec un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur associé dans Beeline.

Pour configurer et tester l’authentification unique Azure AD avec Beeline, vous devez suivre les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Beeline](#configure-beeline-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer l’utilisateur de test Beeline](#create-beeline-test-user)** pour avoir dans Beeline un équivalent de B.Simon lié à la représentation Azure AD associée.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **Beeline**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Sur la page **Configurer l’authentification unique avec SAML**, effectuez les étapes suivantes :

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://projects.beeline.com/<ProjInstance_Name>`

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://projects.beeline.com/<ProjInstance_Name>/SSO_External.ashx`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur et l’URL de réponse réels. Pour obtenir ces valeurs, contactez l’[équipe du support technique de Beeline](https://www.beeline.com/contact-support/). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

5. L’application Beeline attend les assertions SAML dans un format spécifique. Collaborez avec l’[équipe de support technique de Beeline](https://www.beeline.com/contact-support/) pour identifier tout d’abord l’identificateur d’utilisateur correct qui sera mappé à l’application. Suivez également les instructions de l’[équipe de support technique de Beeline](https://www.beeline.com/contact-support/) concernant l’attribut à utiliser pour ce mappage. Vous pouvez gérer la valeur de cet attribut à partir de l’onglet **Attributs utilisateur** de l’application. La capture d’écran suivante montre un exemple : Ici, nous avons mis en correspondance la revendication **Identificateur d’utilisateur** avec l’attribut **userprincipalname**, qui fournit l’ID utilisateur unique qui sera envoyé à l’application Beeline dans chaque réponse SAML correcte.

    ![image](common/edit-attribute.png)

6. Sur la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le fichier **XML de métadonnées de fédération** en fonction des options définies selon vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

7. Dans le [portail Azure](https://portal.azure.com/), dans la page d’intégration de l’application **Beeline**, sélectionnez **Propriétés** et copiez l’URL d’accès utilisateur.

    ![Copier l’URL de l’accès utilisateur](media/beeline-tutorial/client-access-url.png)

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Beeline.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Beeline**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-beeline-sso"></a>Configurer l’authentification unique Beeline

Pour configurer l’authentification unique côté **Beeline**, vous devez envoyer le **XML des métadonnées de fédération** téléchargé et l’URL d’accès utilisateur, copié à partir des propriétés dans le portail Azure, à l’[équipe du support technique de Beeline](https://www.beeline.com/contact-support/). Il lui faut les métadonnées et l’URL d’accès utilisateur afin que la connexion d’authentification unique SAML soit correctement configurée des deux côtés.

### <a name="create-beeline-test-user"></a>Créer l’utilisateur de test Beeline

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans Beeline. Tous les utilisateurs de Beeline doivent être provisionnés dans cette application avant de procéder à l’authentification unique. Vous devez donc collaborer avec l’[équipe de support technique de Beeline](https://www.beeline.com/contact-support/) pour provisionner ces utilisateurs dans l’application.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes.

* Dans le portail Azure, cliquez sur Tester cette application. Vous êtes alors automatiquement connecté à l’instance Beeline pour laquelle vous avez configuré l’authentification unique.

* Vous pouvez utiliser Mes applications de Microsoft. Le fait de cliquer sur la vignette Beeline dans Mes applications doit vous connecter automatiquement à l’application Beeline pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez configuré Beeline, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
