---
title: 'Tutoriel : Intégration de l’authentification unique Azure AD à Huddle'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Huddle.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/22/2021
ms.author: jeedes
ms.openlocfilehash: 9dfbccdc7c68a531d4d9d07bc80502c6da747fb1
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132314056"
---
# <a name="tutorial-azure-ad-sso-integration-with-huddle"></a>Tutoriel : Intégration de l’authentification unique Azure AD à Huddle

Dans ce tutoriel, vous allez découvrir comment intégrer Huddle à Azure Active Directory (Azure AD). Quand vous intégrez Huddle à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Huddle.
* Autoriser les utilisateurs à se connecter automatiquement à Huddle avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Huddle pour lequel l’authentification unique (SSO) est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Huddle prend en charge l’authentification unique lancée par **le fournisseur de services et le fournisseur d’identité**.

> [!NOTE]
> L’identificateur de cette application étant une valeur de chaîne fixe, une seule instance peut être configurée dans un locataire.

## <a name="add-huddle-from-the-gallery"></a>Ajouter Huddle à partir de la galerie

Pour configurer l’intégration de Huddle avec Azure AD, vous devez ajouter Huddle à votre liste d’applications SaaS gérées, à partir de la galerie.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Huddle** dans la zone de recherche.
1. Sélectionnez **Huddle** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-huddle"></a>Configurer et tester l’authentification unique Azure AD pour Huddle

Configurez et testez l’authentification unique Azure AD avec Huddle pour un utilisateur test appelé **B. Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur correspondant dans Huddle.

Pour configurer et tester l’authentification unique Azure AD avec Huddle, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Huddle](#configure-huddle-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur test Huddle](#create-huddle-test-user)** pour avoir, dans Huddle, un équivalent de B. Simon lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **Huddle**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon pour **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

4. À la section **Configuration SAML de base**, si vous souhaitez configurer l’application en mode initié par **IDP**, suivez les étapes ci-dessous :

    > [!NOTE]
    > Votre instance Huddle est automatiquement détectée à partir du domaine que vous entrez ci-dessous.

    a. Dans la zone de texte **Identificateur**, tapez l’une des URL suivantes :

    | **Identificateur** |
    |------|
    | `https://login.huddle.net` |
    | `https://login.huddle.com` |

    b. Dans la zone de texte **URL de réponse**, tapez l’une des URL suivantes :

    | **URL de réponse** |
    |----|
    | `https://login.huddle.net/saml/browser-sso` |
    | `https://login.huddle.com/saml/browser-sso` |
    | `https://login.huddle.com/saml/idp-initiated-sso` |

5. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    Dans la zone de texte **URL d’authentification**, tapez une URL au format suivant :

    | **URL d’authentification** |
    |----|
    | `https://<customsubdomain>.huddle.com` |
    | `https://us.huddle.com` |
      
    > [!NOTE]
    > La valeur de l’URL de connexion n’est pas réelle. Mettez à jour cette valeur avec l’URL d’authentification réelle. Contactez [l’équipe de support technique Huddle](https://huddle.zendesk.com) pour obtenir cette valeur.

6. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (Base64)** en fonction des options définies par rapport à vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

7. Dans la section **Configurer Huddle**, copiez la ou les URL appropriées en fonction de vos besoins.

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

Dans cette section, vous allez créer un utilisateur de test appelé B. Simon dans le portail Azure.

1. Dans le volet gauche du Portail Azure, sélectionnez **Azure Active Directory**, **Utilisateurs**, puis **Tous les utilisateurs**.
1. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.
1. Dans les propriétés **Utilisateur**, effectuez les étapes suivantes :
   1. Dans le champ **Nom**, entrez `B. Simon`.  
   1. Dans le champ **Nom de l’utilisateur**, entrez username@companydomain.extension. Par exemple : `B. Simon@contoso.com`.
   1. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.
   1. Cliquez sur **Créer**.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser B. Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Huddle.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Huddle**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-huddle-sso"></a>Configurer l’authentification unique Huddle

Pour configurer l’authentification unique côté **Huddle**, vous devez envoyer le **certificat (Base64)** téléchargé et les URL appropriées copiées depuis le portail Azure à l’[équipe du support technique Huddle](https://huddle.zendesk.com/). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

> [!NOTE]
> L’authentification unique doit être activée par l’équipe du support technique Huddle. Vous recevrez une notification une fois la configuration terminée.

### <a name="create-huddle-test-user"></a>Créer un utilisateur de test Huddle

Pour se connecter à Huddle, les utilisateurs d’Azure AD doivent être configurés dans Huddle. Dans le cas de Huddle, l’approvisionnement est une tâche manuelle.

**Pour configurer l'approvisionnement des utilisateurs, procédez comme suit :**

1. Connectez-vous à votre site d'entreprise **Huddle** en tant qu'administrateur.

2. Cliquez sur **Espace de travail**.

3. Cliquez sur **People \> Invite People**.

    ![Personnes](./media/huddle-tutorial/tasks.png "Personnes")

4. Dans la section **Créer une invitation**, procédez comme suit :
  
    ![Nouvelle invitation](./media/huddle-tutorial/team.png "Nouvelle invitation")
  
    a. Dans la liste **Choisir une équipe pour inviter des personnes à la rejoindre**, sélectionnez **Équipe**.

    b. Entrez l’**adresse e-mail** associée au compte Azure AD valide que vous souhaitez configurer dans la zone de texte **Entrer l’adresse e-mail des personnes que vous souhaitez inviter**.

    c. Cliquez sur **Invite**.

    > [!NOTE]
    > Le titulaire du compte Azure AD reçoit alors un message électronique contenant un lien pour confirmer le compte avant qu’il ne soit activé.

> [!NOTE]
> Vous pouvez utiliser n’importe quels autres outils ou API de création de compte d’utilisateur Huddle fourni par ce site pour configurer des comptes d’utilisateurs Azure AD.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

#### <a name="sp-initiated"></a>Lancée par le fournisseur de services :

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL d’authentification Huddle, à partir de laquelle vous pouvez lancer le processus de connexion.  

* Accédez directement à l’URL d’authentification Huddle pour lancer le processus de connexion.

#### <a name="idp-initiated"></a>Lancée par le fournisseur d’identité :

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors automatiquement connecté à l’application Huddle pour laquelle vous avez configuré l’authentification unique. 

Vous pouvez aussi utiliser Mes applications de Microsoft pour tester l’application dans n’importe quel mode. Quand vous cliquez sur la vignette Huddle dans Mes applications, si le mode Fournisseur de services est configuré, vous êtes redirigé vers la page d’authentification de l’application pour lancer le processus de connexion. Si le mode Fournisseur d’identité est configuré, vous devriez être automatiquement connecté à l’application Huddle pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez configuré Huddle, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
