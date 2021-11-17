---
title: 'Tutoriel : Intégration de l’authentification unique Azure AD à ITRP'
description: Dans ce tutoriel, vous allez découvrir comment configurer l’authentification unique entre Azure Active Directory et ITRP.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/27/2021
ms.author: jeedes
ms.openlocfilehash: b972c3c6779d3ca288685d1a8921bceac7d001dd
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132346379"
---
# <a name="tutorial-azure-ad-sso-integration-with-itrp"></a>Tutoriel : Intégration de l’authentification unique Azure AD à ITRP

Dans ce tutoriel, vous allez apprendre à intégrer ITRP à Azure Active Directory (Azure AD). Quand vous intégrez ITRP à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à ITRP.
* Permettre à vos utilisateurs de se connecter automatiquement à ITRP avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à ITRP, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un [compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement ITRP pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* ITRP prend en charge l’authentification unique lancée par le fournisseur de services.

## <a name="add-itrp-from-the-gallery"></a>Ajouter ITRP à partir de la galerie

Pour configurer l’intégration d’ITRP à Azure AD, vous devez ajouter ITRP à votre liste d’applications SaaS managées, à partir de la galerie.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, saisissez **ITRP** dans la zone de recherche.
1. Sélectionnez **ITRP** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-itrp"></a>Configurer et tester l’authentification unique Azure AD pour ITRP

Configurez et testez l’authentification unique Azure AD avec ITRP à l’aide d’un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur ITRP associé.

Pour configurer et tester l’authentification unique Azure AD avec ITRP, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique ITRP](#configure-itrp-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test ITRP](#create-an-itrp-test-user)** pour avoir un équivalent de B.Simon dans ITRP lié à la représentation Azure AD associée.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **ITRP**, recherchez la section **Gérer**, puis sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la boîte de dialogue **Configuration SAML de base**, effectuez les étapes suivantes.

    1. Dans la zone de texte **Identificateur (ID d’entité)**, entrez une URL au format suivant :

       `https://<tenant-name>.itrp.com`

    1. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant :
    
       `https://<tenant-name>.itrp.com`   

    > [!NOTE]
    > Ces valeurs sont des espaces réservés. Vous devez les remplacer par les valeurs réelles de l’identificateur et de l’URL de connexion. Pour obtenir ces valeurs, contactez l’[équipe du support technique ITRP](https://www.4me.com/support/). Vous pouvez aussi vous référer aux modèles figurant dans la boîte de dialogue **Configuration SAML de base** dans le portail Azure.

5. Dans la section **Certificat de signature SAML**, sélectionnez le bouton **Modifier** pour ouvrir la boîte de dialogue **Certificat de signature SAML** :

    ![Capture d’écran montrant la page Certificat de signature SAML avec l’icône Modifier sélectionnée.](common/edit-certificate.png)

6. Dans la boîte de dialogue **Certificat de signature SAML**, copiez la valeur **Thumbprint** du certificat et enregistrez-la :

    ![Copiez la valeur Thumbprint](common/copy-thumbprint.png)

7. Dans la section **Configurer ITRP**, copiez la ou les URL appropriées, en fonction de vos besoins :

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à ITRP.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **ITRP**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-itrp-sso"></a>Configurer l’authentification unique ITRP

1. Dans une nouvelle fenêtre de navigateur web, connectez-vous à votre site d’entreprise ITRP en tant qu’administrateur.

1. En haut de la fenêtre, sélectionnez l’icône **Settings** (Paramètres ) :

    ![Icône Paramètres](./media/itrp-tutorial/profile.png "Icône Paramètres")

1. Dans le volet gauche, sélectionnez **Single Sign-On** (Authentification unique) :

    ![Sélectionner l’authentification unique](./media/itrp-tutorial/setting.png "Sélectionner l’authentification unique")

1. Dans la section de configuration **Single Sign-On**, effectuez les étapes suivantes.

    ![Capture d’écran montrant la section Single Sign-On avec l’option Enabled sélectionnée.](./media/itrp-tutorial/configuration.png "Section Single Sign-On")

    ![Capture d’écran montrant la section Single Sign-On dans laquelle vous pouvez ajouter les informations décrites à cette étape](./media/itrp-tutorial/certificate.png "Section Single Sign-On").

    1. Sélectionnez **Enabled**.

    1. Dans la zone **URL de déconnexion distante**, collez la valeur de l’**URL de déconnexion** que vous avez copiée à partir du portail Azure.

    1. Dans la zone **URL SSO SAML**, collez la valeur de l’**URL de connexion** que vous avez copiée à partir du portail Azure.

    1. Dans la zone **Empreinte du certificat**, collez la valeur du certificat **Empreinte** que vous avez copiée à partir du portail Azure.

    1. Sélectionnez **Enregistrer**.

### <a name="create-an-itrp-test-user"></a>Créer un utilisateur de test ITRP

Pour autoriser les utilisateurs Azure AD à se connecter à ITRP, vous devez les ajouter dans ITRP. Vous devez les ajouter manuellement.

Pour créer un compte d’utilisateur, procédez comme suit :

1. Connectez-vous à votre locataire ITRP.

1. En haut de la fenêtre, sélectionnez l’icône **Enregistrements** :

    ![Icône Enregistrements](./media/itrp-tutorial/account.png "Icône Enregistrements")

1. Dans le menu, sélectionnez **Personnes** :

    ![Sélectionner des personnes](./media/itrp-tutorial/user.png "Sélectionner des personnes")

1. Sélectionnez le signe plus ( **+** ) pour ajouter une nouvelle personne :

    ![Sélectionner le signe plus](./media/itrp-tutorial/people.png "Sélectionner le signe plus")

1. Dans la boîte de dialogue **Ajouter une nouvelle personne**, effectuez les étapes suivantes.

    ![Boîte de dialogue Add New Person](./media/itrp-tutorial/details.png "Boîte de dialogue Add New Person")

    1. Entrez l’adresse e-mail d’un compte Azure AD valide que vous souhaitez ajouter.

    1. Sélectionnez **Enregistrer**.

> [!NOTE]
> Vous pouvez utiliser n’importe quel API ou outil de création de compte d’utilisateur fourni par ITRP pour provisionner des comptes d’utilisateurs Azure AD.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion d’ITRP, à partir de laquelle vous pouvez lancer le flux de connexion. 

* Accédez directement à l’URL de connexion d’ITRP pour lancer le flux de connexion.

* Vous pouvez utiliser Mes applications de Microsoft. Lorsque vous cliquez sur la vignette ITRP dans Mes applications, vous êtes redirigé vers l’URL de connexion d’ITRP. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré ITRP, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
