---
title: 'Tutoriel : Intégration de l’authentification unique Azure AD à Appaegis Isolation Access Cloud'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Appaegis Isolation Access Cloud.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/07/2021
ms.author: jeedes
ms.openlocfilehash: 358dcd9945751a20332063f14d408b12de151fed
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132318396"
---
# <a name="tutorial-azure-ad-sso-integration-with-appaegis-isolation-access-cloud"></a>Tutoriel : Intégration de l’authentification unique Azure AD à Appaegis Isolation Access Cloud

Dans ce tutoriel, vous allez découvrir comment intégrer Appaegis Isolation Access Cloud à Azure Active Directory (Azure AD). Quand vous intégrez Appaegis Isolation Access Cloud à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Appaegis Isolation Access Cloud.
* Permettre à vos utilisateurs de se connecter automatiquement à Appaegis Isolation Access Cloud avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Appaegis Isolation Access Cloud pour lequel l’authentification unique (SSO) est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* Appaegis Isolation Access Cloud prend en charge l’authentification unique lancée par **le fournisseur de services et par le fournisseur d’identité**.
* Appaegis Isolation Access Cloud prend en charge l’attribution d’utilisateurs **juste-à-temps**.

## <a name="adding-appaegis-isolation-access-cloud-from-the-gallery"></a>Ajout d’Appaegis Isolation Access Cloud à partir de la galerie

Pour configurer l’intégration d’Appaegis Isolation Access Cloud à Azure AD, vous devez ajouter Appaegis Isolation Access Cloud, disponible dans la galerie, à votre liste d’applications SaaS managées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Appaegis Isolation Access Cloud** dans la zone de recherche.
1. Sélectionnez **Appaegis Isolation Access Cloud** dans le panneau de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-appaegis-isolation-access-cloud"></a>Configurer et tester l’authentification unique Azure AD pour Appaegis Isolation Access Cloud

Configurez et testez l’authentification unique Azure AD avec Appaegis Isolation Access Cloud à l’aide d’un utilisateur test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir une relation entre un utilisateur Azure AD et l’utilisateur associé dans Appaegis Isolation Access Cloud.

Pour configurer et tester l’authentification unique Azure AD avec Appaegis Isolation Access Cloud, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Appaegis Isolation Access Cloud](#configure-appaegis-isolation-access-cloud-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur test Appaegis Isolation Access Cloud](#create-appaegis-isolation-access-cloud-test-user)** pour avoir, dans Appaegis Isolation Access Cloud, un équivalent de B.Simon lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, dans la page d’intégration de l’application **Appaegis Isolation Access Cloud**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, l’utilisateur n’a rien à faire, car l’application est déjà intégrée à Azure.

1. Cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes si vous souhaitez configurer l’application en mode lancé par le **fournisseur de services** :

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<SUBDOMAIN>.appaegis.net`.

    b. Dans la zone de texte **État de relais**, tapez une valeur en respectant le format suivant : `<RelayState>`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion et l’état de relais réels. Pour obtenir ces valeurs, contactez l’[équipe de support d’Appaegis Isolation Access Cloud](mailto:support@appaegis.com). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. L’application Appaegis Isolation Access Cloud s’attend à recevoir les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à votre configuration d’attributs de jeton SAML. La capture d’écran suivante montre la liste des attributs par défaut.

    ![image](common/default-attributes.png)

1. En plus de ce qui précède, l’application Appaegis Isolation Access Cloud s’attend à ce que quelques attributs supplémentaires (présentés ci-dessous) soient passés en retour dans la réponse SAML. Ces attributs sont également préremplis, mais vous pouvez les examiner pour voir s’ils répondent à vos besoins.
    
    | Nom | Attribut source |
    | ---------| --------- |
    | email | user.userprincipalname |

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **XML de métadonnées de fédération** et sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

1. Dans la section **Configurer Appaegis Isolation Access Cloud**, copiez la ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Appaegis Isolation Access Cloud.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Appaegis Isolation Access Cloud**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-appaegis-isolation-access-cloud-sso"></a>Configurer l’authentification unique Appaegis Isolation Access Cloud

1. Connectez-vous à votre site d’entreprise Appaegis Isolation Access Cloud en tant qu’administrateur.

1. Accédez à **Setting** (Paramètre) > **Customization** (Personnalisation), tapez une URL de domaine de locataire **(Tenant Domain URL)** dans la zone de texte, puis cliquez sur le bouton **+IdP**.

    ![Capture d’écran montrant la personnalisation d’un compte.](./media/appaegis-isolation-access-cloud-tutorial/account.png "Personnalisation de compte")

1. Dans la page **Identity Provider Settings** (Détails du fournisseur d’identité), effectuez les actions suivantes.

    ![Capture d’écran montrant les détails du fournisseur d’identité.](./media/appaegis-isolation-access-cloud-tutorial/details.png "Fournisseur d’identité") 

    1. Sélectionnez **Azure AD** dans la liste déroulante **Identity Provider** (Fournisseur d’identité).

    1. Copiez la valeur du champ **ACS URL** (URL ACS), puis collez-la dans la zone de texte **URL de réponse** de la section **Configuration SAML de base** du portail Azure.

    1. Copiez la valeur **Entity ID** (ID d’entité), puis collez-la dans la zone de texte **Identificateur** de la section **Configuration SAML de base** du portail Azure.

    1. Dans le Bloc-notes, ouvrez le fichier **Federation Metadata XML** (XML de métadonnées de fédération) téléchargé à partir du portail Azure, puis chargez le fichier avec l’option **SAML File Upload** (Chargement du fichier SAML).

    1. Cochez la case **Status** (État), puis cliquez sur **Save** (Enregistrer).

### <a name="create-appaegis-isolation-access-cloud-test-user"></a>Créer un utilisateur test Appaegis Isolation Access Cloud

Dans cette section, un utilisateur appelé Britta Simon est créé dans Appaegis Isolation Access Cloud. Appaegis Isolation Access Cloud prend en charge l’attribution d’utilisateurs juste-à-temps, option activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas encore d’utilisateur dans Appaegis Isolation Access Cloud, il en est créé un après l’authentification.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

#### <a name="sp-initiated"></a>Lancée par le fournisseur de services :

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion Appaegis Isolation Access Cloud, à partir de laquelle vous pouvez lancer le flux de connexion.  

* Accédez directement à l’URL de connexion Appaegis Isolation Access Cloud pour lancer le flux de connexion.

#### <a name="idp-initiated"></a>Lancée par le fournisseur d’identité :

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors automatiquement connecté à l’instance Appaegis Isolation Access Cloud pour laquelle vous avez configuré l’authentification unique. 

Vous pouvez aussi utiliser Mes applications de Microsoft pour tester l’application dans n’importe quel mode. Si, quand vous cliquez sur la vignette Appaegis Isolation Access Cloud dans Mes applications, le mode Fournisseur de services est configuré, vous êtes redirigé vers la page d’authentification de l’application pour lancer le processus de connexion. Si le mode Fournisseur d’identité est configuré, vous êtes automatiquement connecté à l’application Appaegis Isolation Access Cloud pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré Appaegis Isolation Access Cloud, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
