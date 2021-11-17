---
title: "Tutoriel : Intégration d'Azure Active Directory à Deputy | Microsoft Docs"
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Deputy.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/11/2021
ms.author: jeedes
ms.openlocfilehash: 258c7b6e88129367c512af4b21c794351f31f243
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132280438"
---
# <a name="tutorial-azure-active-directory-integration-with-deputy"></a>Tutoriel : Intégration d'Azure Active Directory à Deputy

Dans ce tutoriel, vous allez découvrir comment intégrer Deputy à Azure Active Directory (Azure AD). Quand vous intégrez Deputy à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Deputy.
* Permettre à vos utilisateurs de se connecter automatiquement à Deputy avec leurs comptes Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Deputy pour lequel l’authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* Deputy prend en charge l’authentification unique initiée par le **fournisseur de services et le fournisseur d’identité**.
* Deputy prend en charge le provisionnement d’utilisateurs **juste-à-temps**.

## <a name="add-deputy-from-the-gallery"></a>Ajouter Deputy à partir de la galerie

Pour configurer l’intégration de Deputy avec Azure AD, vous devez ajouter Deputy, à partir de la galerie, à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Deputy** dans la zone de recherche.
1. Sélectionnez **Deputy** dans le panneau des résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-deputy"></a>Configurer et tester l’authentification unique Azure AD pour Deputy

Configurez et testez l’authentification unique Azure AD auprès de Deputy pour un utilisateur de test nommé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur associé dans Deputy.

Pour configurer et tester l’authentification unique (SSO) Azure AD auprès de Deputy, procédez comme suit :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Deputy](#configure-deputy-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test Deputy](#create-deputy-test-user)** pour avoir un équivalent de B.Simon dans Deputy lié à la représentation Azure AD associée.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **Deputy**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

4. À la section **Configuration SAML de base**, si vous souhaitez configurer l’application en mode initié par **IDP**, suivez les étapes ci-dessous :

    a. Dans la zone de texte **Identificateur**, tapez une URL en utilisant un des modèles suivants :

    ```http
    https://<subdomain>.<region>.au.deputy.com
    https://<subdomain>.<region>.ent-au.deputy.com
    https://<subdomain>.<region>.na.deputy.com
    https://<subdomain>.<region>.ent-na.deputy.com
    https://<subdomain>.<region>.eu.deputy.com
    https://<subdomain>.<region>.ent-eu.deputy.com
    https://<subdomain>.<region>.as.deputy.com
    https://<subdomain>.<region>.ent-as.deputy.com
    https://<subdomain>.<region>.la.deputy.com
    https://<subdomain>.<region>.ent-la.deputy.com
    https://<subdomain>.<region>.af.deputy.com
    https://<subdomain>.<region>.ent-af.deputy.com
    https://<subdomain>.<region>.an.deputy.com
    https://<subdomain>.<region>.ent-an.deputy.com
    https://<subdomain>.<region>.deputy.com
    ```

    b. Dans la zone de texte **URL de réponse** , tapez une URL en utilisant un des modèles suivants :
    
    ```http
    https://<subdomain>.<region>.au.deputy.com/exec/devapp/samlacs
    https://<subdomain>.<region>.ent-au.deputy.com/exec/devapp/samlacs
    https://<subdomain>.<region>.na.deputy.com/exec/devapp/samlacs
    https://<subdomain>.<region>.ent-na.deputy.com/exec/devapp/samlacs
    https://<subdomain>.<region>.eu.deputy.com/exec/devapp/samlacs
    https://<subdomain>.<region>.ent-eu.deputy.com/exec/devapp/samlacs
    https://<subdomain>.<region>.as.deputy.com/exec/devapp/samlacs.
    https://<subdomain>.<region>.ent-as.deputy.com/exec/devapp/samlacs
    https://<subdomain>.<region>.la.deputy.com/exec/devapp/samlacs
    https://<subdomain>.<region>.ent-la.deputy.com/exec/devapp/samlacs
    https://<subdomain>.<region>.af.deputy.com/exec/devapp/samlacs
    https://<subdomain>.<region>.ent-af.deputy.com/exec/devapp/samlacs
    https://<subdomain>.<region>.an.deputy.com/exec/devapp/samlacs
    https://<subdomain>.<region>.ent-an.deputy.com/exec/devapp/samlacs
    https://<subdomain>.<region>.deputy.com/exec/devapp/samlacs
    ```

5. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<your-subdomain>.<region>.deputy.com`

    >[!NOTE]
    > Le suffixe de région Deputy est facultatif ou il doit utiliser l’une des valeurs suivantes : au | na | eu |as |la |af |an |ent-au |ent-na |ent-eu |ent-as | ent-la | ent-af | ent-an

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL de réponse et l’URL de connexion réels. Pour obtenir ces valeurs, contactez l’[équipe du support technique Deputy](https://www.deputy.com/call-centers-customer-support-scheduling-software). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. L’application Deputy s’attend à recevoir les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à votre configuration des attributs de jeton SAML. La capture d’écran suivante montre la liste des attributs par défaut.

    ![image](common/default-attributes.png)

1. En plus des éléments cités précédemment, l’application Deputy s’attend à ce que quelques attributs supplémentaires (présentés ci-dessous) soient retransmis dans la réponse SAML. Ces attributs sont également préremplis, mais vous pouvez les examiner pour voir s’ils répondent à vos besoins.
    
    | Nom |  Attribut source|
    | -------------- | --------- |
    | Prénom | user.givenname|
    | Nom | user.surname |

6. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (Base64)** en fonction des options définies par rapport à vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

7. Dans la section **Configurer Deputy**, copiez l’URL ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Deputy.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Deputy**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-deputy-sso"></a>Configurer l’authentification unique Deputy

1. Connectez-vous à votre compte Deputy en tant qu’administrateur.

1. En haut à droite, cliquez sur votre compte, puis sélectionnez **Paramètres d’entreprise**.

    ![Capture d’écran des paramètres d’entreprise](./media/deputy-tutorial/business-settings.png)

1. Ensuite, sous l’onglet **Général**, cliquez sur **Paramètres d’authentification unique**.

    ![Capture d’écran des paramètres d’authentification unique](./media/deputy-tutorial/general.png)

2. Dans cette page des **Paramètres d’authentification unique**, procédez comme suit.

    ![Configure Single Sign-On](./media/deputy-tutorial/configuration.png)
    
    a. Cliquez sur **Activer l’authentification unique**.
   
    b. Dans la zone de texte **URL de connexion du fournisseur d’identité**, collez **l’URL de connexion** que vous avez copiée à partir du portail Azure.

    c. Dans la zone de texte **Émetteur du fournisseur d’identité**, collez **l’identifiant (ID d’entité)** que vous avez copié à partir du portail Azure.

    d. Ouvrez le **Certificat (en base64)** téléchargé à partir du portail Azure dans le Bloc-notes, puis collez le contenu dans la zone de texte **certificat X.509**.

    e. Activez la **connexion d’authentification unique requise** si vous souhaitez vous connecter avec l’authentification unique.
   
    f. Activez **le provisionnement d’utilisateurs juste-à-temps**. Dans les champs **Prénom** et **Nom**, attribuez les noms des attributs que vous avez configurés dans la section **Attributs utilisateur et revendications**, comme `First name` et `Last name`.

    g. Cliquez sur **Appliquer les modifications**.

### <a name="create-deputy-test-user"></a>Créer un utilisateur de test Deputy

Dans cette section, un utilisateur nommé Britta Simon est créé dans Deputy. Deputy prend en charge le provisionnement d’utilisateurs juste-à-temps qui est activé par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe aucun utilisateur dans Deputy, un utilisateur est créé après l’authentification.

#### <a name="to-add-the-user-manually-perform-the-following-steps"></a>Pour ajouter l’utilisateur manuellement, procédez comme suit :

1. Connectez-vous à votre site d’entreprise Deputy en tant qu’administrateur.

2. Dans le volet de navigation situé en haut, cliquez sur **People**(Personnes).

3. Cliquez sur le bouton **Ajouter des personnes**, puis cliquez sur **Ajouter une seule personne**.
   
    ![Ajouter des personnes](./media/deputy-tutorial/create-user-1.png "Ajouter des personnes")

4. Pour ajouter un utilisateur, procédez comme suit dans l’onglet **Général**.
   
    ![Nouvel utilisateur](./media/deputy-tutorial/create-user-2.png "Nouvel utilisateur")

    a. Dans les zones de texte **Prénom** et **Nom**, renseignez les champs comme **Britta** et **Simon**.
   
    b. Dans la zone de texte **Travaille chez**, tapez le nom de l’entreprise.
   
    c. Cliquez sur le bouton **Enregistrer** .

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

#### <a name="sp-initiated"></a>Lancée par le fournisseur de services :

* Cliquez sur **Tester cette application** dans le portail Azure. Cette opération redirige vers l’URL de connexion TeamzSkill, où vous pouvez lancer le processus de connexion.  

* Accédez directement à l’URL de connexion TeamzSkill pour lancer le processus de connexion.

#### <a name="idp-initiated"></a>Lancée par le fournisseur d’identité :

* Cliquez sur **Tester cette application** dans le portail Azure : vous devez être connecté automatiquement à l’instance de TeamzSkill pour laquelle vous avez configuré l’authentification unique. 

Vous pouvez aussi utiliser Mes applications de Microsoft pour tester l’application dans n’importe quel mode. Quand vous cliquez sur la vignette TeamzSkill dans Mes applications, si le mode Fournisseur de services est configuré, vous êtes redirigé vers la page de connexion de l’application pour initier le flux de connexion. Si le mode Fournisseur d’identité est configuré, vous êtes automatiquement connecté à l’instance de TeamzSkill pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).


## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez configuré Deputy, vous pouvez appliquer le contrôle de session qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
