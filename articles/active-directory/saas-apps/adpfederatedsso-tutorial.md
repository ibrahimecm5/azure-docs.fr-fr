---
title: 'Tutoriel : Intégration de l’authentification unique Azure AD à ADP'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et ADP.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/30/2021
ms.author: jeedes
ms.openlocfilehash: c008122f0354b77505df61bdade90adb3960782f
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132280796"
---
# <a name="tutorial-azure-ad-sso-integration-with-adp"></a>Tutoriel : Intégration de l’authentification unique Azure AD à ADP

Dans ce tutoriel, vous allez apprendre à intégrer ADP dans Azure Active Directory (Azure AD). Quand vous intégrez ADP dans Azure AD, vous pouvez :

* contrôler dans Azure AD qui a accès à ADP ;
* permettre à vos utilisateurs de se connecter automatiquement à ADP avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement ADP pour lequel l’authentification unique (SSO) est activée.

> [!NOTE]
> Cette intégration peut également être utilisée à partir de l’environnement cloud US Government Azure AD. Cette application est disponible dans la Galerie d’applications cloud US Government Azure AD et peut être configurée de la même façon que dans le cloud public.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* ADP prend en charge l’authentification unique lancée par le **fournisseur d’identité**.

> [!NOTE]
> L’identificateur de cette application étant une valeur de chaîne fixe, une seule instance peut être configurée dans un locataire.

## <a name="add-adp-from-the-gallery"></a>Ajouter ADP depuis la galerie

Pour configurer l’intégration d’ADP à Azure AD, vous devez ajouter ADP disponible dans la galerie, à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **ADP** dans la zone de recherche.
1. Sélectionnez **ADP** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-adp"></a>Configurer et tester l’authentification unique Azure AD pour ADP

Configurez et testez l’authentification unique Azure AD avec ADP à l’aide d’un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur ADP associé.

Pour configurer et tester l’authentification unique Azure AD avec ADP, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
2. **[Configurer l’authentification unique ADP](#configure-adp-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test ADP](#create-adp-test-user)** pour avoir un équivalent de B.Simon dans ADP, associé à sa représentation dans Azure AD.
3. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, dans la page d’intégration de l’application **ADP**, cliquez sur **l’onglet Propriétés** et effectuez les étapes suivantes : 

    ![Propriétés de l’authentification unique](./media/adpfederatedsso-tutorial/properties.png)

    a. Définissez le champ **Activé pour que les utilisateurs se connectent** sur **Oui**.

    b. Copiez **l’URL d’accès utilisateur** et collez-la dans la **section Configurer l’URL d’authentification**, comme expliqué plus loin dans le didacticiel.

    c. Définissez le champs **Affectation de l’utilisateur obligatoire** sur **Oui**.

    d. Définissez le champ **Visible par les utilisateurs** sur **Non**.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **ADP**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon pour **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    Dans la zone de texte **Identificateur (ID d’entité)** , tapez l’URL : `https://fed.adp.com`

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **XML de métadonnées de fédération** et sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

1. Dans la section **Configurer ADP**, copiez l’URL ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à ADP.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **ADP**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-adp-sso"></a>Configurer l’authentification unique ADP

Pour configurer l’authentification unique côté **ADP**, vous devez charger le **XML de métadonnées** téléchargé sur le [site web ADP](https://adpfedsso.adp.com/public/login/index.fcc).

> [!NOTE]  
> Ce processus peut prendre quelques jours.

### <a name="configure-your-adp-services-for-federated-access"></a>Configurer vos services ADP pour un accès fédéré

>[!Important]
> Vous devez assigner à l’application de service ADP les employés qui ont besoin d’un accès fédéré à vos services ADP, puis réassigner les utilisateurs à un service ADP spécifique.
Une fois la confirmation reçue de votre représentant ADP, configurez vos services ADP et assignez/gérez les utilisateurs pour contrôler l’accès utilisateur à un service ADP spécifique.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **ADP** dans la zone de recherche.
1. Sélectionnez **ADP** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.
1. Dans le portail Azure, dans la page d’intégration de votre application **ADP**, cliquez sur **l’onglet Propriétés** et effectuez les étapes suivantes :  

    ![Onglet Propriétés de l’authentification unique liée](./media/adpfederatedsso-tutorial/application.png)

    1. Définissez le champ **Activé pour que les utilisateurs se connectent** sur **Oui**.

    1. Définissez le champs **Affectation de l’utilisateur obligatoire** sur **Oui**.

    1. Définissez le champ **Visible par les utilisateurs** sur **Oui**.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **ADP**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.

1. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, sélectionnez le **Mode****Lié**. pour lier votre application à **ADP**.

    ![Authentification unique liée](./media/adpfederatedsso-tutorial/linked.png)

1. Accédez à la section **Configurer l’URL d’authentification**, puis effectuez les étapes suivantes :

    ![Configurer l’authentification unique](./media/adpfederatedsso-tutorial/users.png)

    1. Collez l’**URL d’accès utilisateur**, que vous avez copiée à partir de l’**onglet Propriétés** plus haut (à partir de l’application ADP principale).

    1. Voici les 5 applications qui prennent en charge différentes **URL d’état de relais**. Vous devez ajouter la valeur **d’URL d’état de relais** appropriée pour une application particulière manuellement à **l’URL d’accès utilisateur**.

        * **ADP Workforce Now**

            `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?WFN`

        * **ADP Workforce Now Enhanced Time**

            `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?EETDC2`

        * **ADP Vantage HCM**

            `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?ADPVANTAGE`

        * **ADP Enterprise HR**

            `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?PORTAL`

        * **MyADP**

            `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?REDBOX`

1. **Enregistrez** les changements apportés.

1. Une fois la confirmation reçue de votre représentant ADP, commencez à effectuer un test avec un ou deux utilisateurs.

    1. Assignez quelques utilisateurs à l’application de service ADP pour tester l’accès fédéré.

    1. Le test est réussi quand les utilisateurs accèdent à l’application de service ADP dans la galerie et qu’ils peuvent accéder à leur service ADP.

1. Une fois confirmée la réussite d’un test, assignez le service ADP fédéré à des utilisateurs ou à des groupes d’utilisateurs, comme expliqué plus loin dans le didacticiel, et déployez-le pour vos employés.

### <a name="create-adp-test-user"></a>Créer un utilisateur de test ADP

L’objectif de cette section est de créer un utilisateur appelé B. Simon dans ADP. Pour ajouter les utilisateurs au compte ADP, contactez l’[équipe du support ADP](https://www.adp.com/contact-us/overview.aspx). 

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes.

* Cliquez sur Tester cette application dans le portail Azure, ce qui devrait vous connecter automatiquement à l’application ADP pour laquelle vous avez configuré l’authentification unique.

* Vous pouvez utiliser Mes applications de Microsoft. Quand vous cliquez sur la vignette ADP dans Mes applications, vous devez vous connecter automatiquement à l’application ADP pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez configuré ADP, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
