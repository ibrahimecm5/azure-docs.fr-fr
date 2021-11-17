---
title: 'Didacticiel : Intégration d’Azure Active Directory à Rackspace SSO | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Rackspace SSO.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/14/2021
ms.author: jeedes
ms.openlocfilehash: 5fffea39509166bcf600537372f0f37f1534308c
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132306906"
---
# <a name="tutorial-azure-active-directory-integration-with-rackspace-sso"></a>Didacticiel : Intégration d’Azure Active Directory à Rackspace SSO

Dans ce tutoriel, vous allez apprendre à intégrer Rackspace SSO à Azure Active Directory (Azure AD). Lorsque vous intégrez Rackspace SSO à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Rackspace SSO.
* Permettre aux utilisateurs de se connecter automatiquement à Rackspace SSO avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à Rackspace SSO, vous devez disposer des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un [compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Rackspace SSO pour lequel l'authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Rackspace SSO prend en charge l'authentification unique lancée par le **fournisseur de services**.

> [!NOTE]
> L’identificateur de cette application étant une valeur de chaîne fixe, une seule instance peut être configurée dans un locataire.

## <a name="add-rackspace-sso-from-the-gallery"></a>Ajouter Rackspace SSO à partir de la galerie

Pour configurer l’intégration de Rackspace SSO à Azure AD, vous devez ajouter Rackspace SSO à partir de la galerie à votre liste d’applications SaaS managées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, entrez **Rackspace SSO** dans la zone de recherche.
1. Sélectionnez **Rackspace SSO** dans le volet de résultats, puis ajoutez l'application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-rackspace-sso"></a>Configurer et tester l'authentification unique Azure AD pour Rackspace SSO

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Rackspace SSO grâce à un utilisateur de test appelé **Britta Simon**.
Quand vous utilisez l’authentification unique avec Rackspace, les utilisateurs Rackspace sont automatiquement créés la première fois qu’ils se connectent au portail Rackspace. 

Pour configurer et tester l'authentification unique Azure AD auprès de Rackspace SSO, vous devez suivre les étapes ci-dessous :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
2. **[Configurer l’authentification unique Rackspace SSO](#configure-rackspace-sso-single-sign-on)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Configurer le mappage d’attributs dans le panneau de configuration de Rackspace](#set-up-attribute-mapping-in-the-rackspace-control-panel)** pour attribuer des rôles Rackspace aux utilisateurs Azure AD.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Sur le portail Azure, accédez à la page d'intégration de l'application **Rackspace SSO**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, chargez le **fichier de métadonnées du fournisseur de services** que vous pouvez télécharger depuis l’[URL](https://login.rackspace.com/federate/sp.xml) et effectuez les étapes suivantes :

    a. Cliquez sur **Charger un fichier de métadonnées**.

    ![Capture d’écran montrant la Configuration SAML de base avec le lien Charger un fichier de métadonnées.](common/upload-metadata.png)

    b. Cliquez sur le **logo du dossier** pour sélectionner le fichier de métadonnées, puis cliquez sur **Charger**.

    ![Capture d’écran montrant une boîte de dialogue dans laquelle vous pouvez sélectionner et charger un fichier.](common/browse-upload-metadata.png)

    c. Une fois que le fichier de métadonnées est chargé, les URL nécessaires sont renseignées automatiquement.

    d. Dans la zone de texte **URL de connexion**, tapez l’URL : `https://login.rackspace.com/federate/`

5. Sur la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le fichier **XML de métadonnées de fédération** en fonction des options définies selon vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

Ce fichier sera chargé sur Rackspace pour renseigner les paramètres de configuration de la fédération des identités nécessaires.

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

Dans cette section, vous allez permettre à B.Simon d'utiliser l'authentification unique Azure en lui accordant l'accès à Rackspace SSO.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Rackspace SSO**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-rackspace-sso-single-sign-on"></a>Configurer l’authentification unique Rackspace SSO

Pour configurer l’authentification unique du côté **Rackspace SSO** :

1. Consultez la documentation de [Ajouter un fournisseur d’identité au panneau de configuration](https://developer.rackspace.com/docs/rackspace-federation/gettingstarted/add-idp-cp/)
1. Elle vous guide à travers les étapes permettant de :
    1. Créer un fournisseur d’identité
    1. Spécifiez un domaine de messagerie que les utilisateurs utiliseront pour identifier votre entreprise lors de la connexion.
    1. Chargez le fichier **XML des métadonnées de fédération** précédemment téléchargé à partir du panneau de configuration d’Azure.

Ceci configure correctement les paramètres de l’authentification unique de base nécessaires pour qu’Azure et Rackspace se connectent.

### <a name="set-up-attribute-mapping-in-the-rackspace-control-panel"></a>Configurer le mappage d’attributs dans le panneau de configuration de Rackspace

Rackspace utilise une **stratégie de mappage d’attributs** pour affecter des rôles et des groupes Rackspace à vos utilisateurs avec authentification unique. La **stratégie de mappage d’attributs** traduit les revendications SAML Azure AD en champs de configuration des utilisateurs nécessaires à Rackspace. Vous trouverez plus d’informations dans la [documentation sur les concepts de base du mappage d’attributs](https://developer.rackspace.com/docs/rackspace-federation/appendix/map/) de Rackspace. Voici quelques considérations à prendre en compte :

* Si vous voulez affecter différents niveaux d’accès Rackspace avec des groupes Azure AD, vous devez activer la revendication des groupes dans les paramètres d’authentification unique **Rackspace SSO** d’Azure. La **stratégie de mappage d’attributs** est alors utilisée pour mettre en correspondance ces groupes avec les rôles et les groupes Rackspace souhaités :

    ![Les paramètres de revendication des groupes](common/sso-groups-claim.png)

* Par défaut, Azure AD envoie l’UID des groupes Azure AD dans la revendication SAML, et non pas leur nom. Cependant, si vous synchronisez votre annuaire Active Directory avec Azure AD, vous avez la possibilité d’envoyer les noms réels des groupes :

    ![Les paramètres des noms de revendication des groupes](common/sso-groups-claims-names.png)

L’exemple suivant de **stratégie de mappage d’attributs** montre :
1. Définition du nom de l’utilisateur Rackspace sur la revendication SAML `user.name`. Vous pouvez utiliser n’importe quelle revendication, mais il est plus courant de définir ceci sur un champ contenant l’adresse e-mail de l’utilisateur.
1. Définition des rôles Rackspace `admin` et `billing:admin` sur un utilisateur en mettant en correspondance un groupe Azure AD, sur la base du nom de groupe ou de l’UID de groupe. Une *substitution* de `"{0}"` dans le champ `roles` est utilisée, et elle sera remplacée par les résultats des expressions de règle `remote`.
1. Utilisation de la `"{D}"` *substitution par défaut* pour permettre à Rackspace de récupérer des champs SAML supplémentaires en recherchant des revendications SAML standard et bien connues dans l’échange SAML.

```yaml
---
mapping:
    rules:
    - local:
        user:
          domain: "{D}"
          name: "{At(http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name)}"
          email: "{D}"
          roles:
              - "{0}"
          expire: "{D}"
      remote:
          - path: |
              (
                if (mapping:get-attributes('http://schemas.microsoft.com/ws/2008/06/identity/claims/groups')='7269f9a2-aabb-9393-8e6d-282e0f945985') then ('admin', 'billing:admin') else (),
                if (mapping:get-attributes('http://schemas.microsoft.com/ws/2008/06/identity/claims/groups')='MyAzureGroup') then ('admin', 'billing:admin') else ()
              )
            multiValue: true
  version: RAX-1
```
> [!TIP]
> Veillez à utiliser un éditeur de texte qui valide la syntaxe YAML lors de la modification de votre fichier de stratégie.

Pour obtenir plus d’exemples, consultez la [documentation des concepts de base du mappage d’attributs](https://developer.rackspace.com/docs/rackspace-federation/appendix/map/) de Rackspace.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l'URL de connexion à Rackspace SSO, à partir de laquelle vous pouvez lancer le flux de connexion. 

* Accédez directement à l'URL de connexion à Rackspace SSO et lancez le flux de connexion.

* Vous pouvez utiliser Mes applications de Microsoft. Le fait de cliquer sur la vignette Rackspace SSO dans Mes applications vous redirige vers l'URL de connexion à Rackspace SSO. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

Vous pouvez également utiliser le bouton **Valider** dans les paramètres d’authentification unique de **Rackspace SSO** :

   ![Bouton de validation de SSO](common/sso-validate-sign-on.png)

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré Rackspace SSO, vous pouvez appliquer le contrôle de session, qui protège en temps réel contre l'exfiltration et l'infiltration des données sensibles de votre organisation. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
