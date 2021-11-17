---
title: "Tutoriel : Intégration d'Azure Active Directory à SAP Cloud Platform Identity Authentication | Microsoft Docs"
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et SAP Cloud Platform Identity Authentication.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/01/2021
ms.author: jeedes
ms.openlocfilehash: 563da658ad5ddeb43ad7d8a39bafd15d9627b0b7
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132341556"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-cloud-platform-identity-authentication"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory à SAP Cloud Platform Identity Authentication

Dans ce tutoriel, vous allez apprendre à intégrer SAP Cloud Platform Identity Authentication à Azure Active Directory (Azure AD). Quand vous intégrez SAP Cloud Platform Identity Authentication à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à SAP Cloud Platform Identity Authentication.
* Permettre à vos utilisateurs de se connecter automatiquement à SAP Cloud Platform Identity Authentication avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement SAP Cloud Platform Identity Authentication pour lequel l’authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* SAP Cloud Platform Identity Authentication prend en charge l’authentification unique lancée par le **fournisseur de services** et par le **fournisseur d’identité**.
* SAP Cloud Platform Identity Authentication prend en charge l’[attribution automatisée d’utilisateurs](sap-cloud-platform-identity-authentication-provisioning-tutorial.md).

Avant de rentrer dans les détails techniques, il est essentiel de comprendre les concepts que vous allez voir. SAP Cloud Platform Identity Authentication et les services de fédération Active Directory (AD FS) vous permettent d’implémenter l’authentification unique entre les applications ou les services protégés par Azure AD (comme IdP) et les applications et services SAP protégés par SAP Cloud Platform Identity Authentication.

Actuellement, SAP Cloud Platform Identity Authentication remplit la fonction de fournisseur d’identité proxy pour les applications SAP. Azure Active Directory joue en retour le rôle de fournisseur d’identité principal dans cette configuration. 

Le schéma suivant illustre cette relation :

![Création d’un utilisateur de test Azure AD](./media/sap-hana-cloud-platform-identity-authentication-tutorial/architecture-01.png)

Avec cette configuration, votre client SAP Cloud Platform Identity Authentication est configuré en tant qu’application approuvée dans Azure Active Directory.

Toutes les applications et tous les services SAP que vous souhaitez protéger de cette façon sont ensuite configurés dans la console de gestion SAP Cloud Platform Identity Authentication.

Ainsi, l’autorisation d’accorder l’accès aux services et applications SAP doit avoir lieu dans SAP Cloud Platform Identity Authentication (par opposition à Azure Active Directory).

Quand vous configurez SAP Cloud Platform Identity Authentication en tant qu’application par le biais de la Place de Marché Azure Active Directory, vous n’avez pas besoin de configurer des revendications ou des assertions SAML.

> [!NOTE]
> Actuellement, seule l’authentification unique web a été testée par les deux parties. Les flux nécessaires à la communication application-API ou API-API devraient fonctionner mais n’ont pas encore été testés. Ils seront testés durant les activités à venir.

## <a name="adding-sap-cloud-platform-identity-authentication-from-the-gallery"></a>Ajout de SAP Cloud Platform Identity Authentication à partir de la galerie

Pour configurer l’intégration de SAP Cloud Platform Identity Authentication dans Azure AD, vous devez ajouter SAP Cloud Platform Identity Authentication à votre liste d’applications SaaS managées à partir de la galerie.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **SAP Cloud Platform Identity Authentication** dans la zone de recherche.
1. Sélectionnez **SAP Cloud Platform Identity Authentication** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.


## <a name="configure-and-test-azure-ad-sso-for-sap-cloud-platform-identity-authentication"></a>Configurer et tester l’authentification unique Azure AD pour SAP Cloud Platform Identity Authentication

Configurez et testez l’authentification unique Azure AD avec SAP Cloud Platform Identity Authentication à l’aide d’un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur SAP Cloud Platform Identity Authentication associé.

Pour configurer et tester l’authentification unique d’Azure AD avec SAP Cloud Platform Identity Authentication, suivez les étapes ci-dessous :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique SAP Cloud Platform Identity Authentication](#configure-sap-cloud-platform-identity-authentication-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test SAP Cloud Platform Identity Authentication](#create-sap-cloud-platform-identity-authentication-test-user)** pour avoir un équivalent de B.Simon dans SAP Cloud Platform Identity Authentication lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **SAP Cloud Platform Identity Authentication**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, si vous souhaitez configurer l’application en mode lancé par le **fournisseur d’identité**, effectuez les étapes suivantes :

    a. Dans la zone de texte **Identificateur**, tapez une valeur au format suivant : `<IAS-tenant-id>.accounts.ondemand.com`

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://<IAS-tenant-id>.accounts.ondemand.com/saml2/idp/acs/<IAS-tenant-id>.accounts.ondemand.com`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur et l’URL de réponse réels. Pour obtenir ces valeurs, contactez [l’équipe du support technique de SAP Cloud Platform Identity Authentication](https://cloudplatform.sap.com/capabilities/security/trustcenter.html). Si vous ne comprenez pas la valeur d’identificateur, lisez la documentation SAP Cloud Platform Identity Authentication relative à la [configuration du locataire SAML 2.0](https://help.hana.ondemand.com/cloud_identity/frameset.htm?e81a19b0067f4646982d7200a8dab3ca.html).

5. Cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes si vous souhaitez configurer l’application en mode lancé par le **fournisseur de services** :

    ![Informations d’authentification unique pour Domaine et URL SAP Cloud Platform Identity Authentication](common/metadata-upload-additional-signon.png)

    Dans la zone de texte **URL de connexion**, tapez une valeur au format suivant : `{YOUR BUSINESS APPLICATION URL}`

    > [!NOTE]
    > Cette valeur n’est pas la valeur réelle. Mettez-la à jour avec l’URL de connexion réelle. Utilisez l’URL de connexion spécifique de votre application métier. En cas de doute, contactez [l’équipe du support technique de SAP Cloud Platform Identity Authentication](https://cloudplatform.sap.com/capabilities/security/trustcenter.html).

1. L’application SAP Cloud Platform Identity Authentication attend les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à votre configuration d’attributs de jeton SAML. La capture d’écran suivante montre la liste des attributs par défaut.

    ![image](common/default-attributes.png)

1. En plus de ce qui précède, l’application SAP Cloud Platform Identity Authentication s’attend à ce que quelques attributs supplémentaires (présentés ci-dessus) soient repassés dans la réponse SAML. Ces attributs sont également préremplis, mais vous pouvez les examiner pour voir s’ils répondent à vos besoins.

    | Nom | Attribut source|
    | ---------------| --------------- |
    | firstName | user.givenname |

8. Sur la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le fichier **XML des métadonnées** en fonction des options définies, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

9. Dans la section **Configurer SAP Cloud Platform Identity Authentication**, copiez la ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à SAP Cloud Platform Identity Authentication.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **SAP Cloud Platform Identity Authentication**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.

1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.

1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-sap-cloud-platform-identity-authentication-sso"></a>Configurer l’authentification unique SAP Cloud Platform Identity Authentication

1. Pour automatiser la configuration dans SAP Cloud Platform Identity Authentication, vous devez installer l’**extension de navigateur My Apps Secure Sign-in** en cliquant sur **Installer l’extension**.

    ![Extension My apps](common/install-myappssecure-extension.png)

2. Une fois l’extension ajoutée au navigateur, un clic sur **Configurer SAP Cloud Platform Identity Authentication** vous dirigera vers l’application SAP Cloud Platform Identity Authentication. À partir de là, fournissez les informations d’identification d’administrateur pour vous connecter à SAP Cloud Platform Identity Authentication. Cette extension de navigateur configure automatiquement l’application pour vous et automatise les étapes 3 à 7.

    ![Configuration](common/setup-sso.png)

3. Si vous souhaitez configurer manuellement SAP Cloud Platform Identity Authentication, dans une autre fenêtre de navigateur web, accédez à la console d’administration de SAP Cloud Platform Identity Authentication. L’URL suit le modèle suivant : `https://<tenant-id>.accounts.ondemand.com/admin`. Lisez ensuite la documentation relative à SAP Cloud Platform Identity Authentication à l’adresse [Integration with Microsoft Azure AD (Intégration à Microsoft Azure AD)](https://developers.sap.com/tutorials/cp-ias-azure-ad.html).

2. Dans le portail Azure, sélectionnez le bouton **Enregistrer**.

3. Effectuez les étapes suivantes uniquement si vous souhaitez ajouter et activer l’authentification unique pour une autre application SAP. Répétez les étapes décrites dans la section **Ajout de SAP Cloud Platform Identity Authentication à partir de la galerie**.

4. Dans le portail Azure, dans la page d’intégration des applications **SAP Cloud Platform Identity Authentication**, sélectionnez **Authentification liée**.

    ![Configurer l’authentification liée](./media/sap-hana-cloud-platform-identity-authentication-tutorial/linked-sign-on.png)

5. Enregistrez la configuration.

> [!NOTE]
> La nouvelle application utilise la configuration de l’authentification unique de l’application SAP précédente. Veillez à utiliser les mêmes fournisseurs d’identité d’entreprise dans la console d’administration de SAP Cloud Platform Identity Authentication.

### <a name="create-sap-cloud-platform-identity-authentication-test-user"></a>Créer un utilisateur de test SAP Cloud Platform Identity Authentication

Vous n’avez pas besoin de créer un utilisateur dans SAP Cloud Platform Identity Authentication. Les utilisateurs qui se trouvent dans le magasin d’utilisateurs Azure AD peuvent utiliser la fonctionnalité d’authentification unique (SSO).

SAP Cloud Platform Identity Authentication prend en charge l’option de fédération des identités. Cette option permet à l’application de vérifier si les utilisateurs authentifiés par le fournisseur d’identité d’entreprise sont présents dans le magasin d’utilisateurs de SAP Cloud Platform Identity Authentication.

L’option de fédération des identités est désactivée par défaut. Si la fédération des identités est activée, seuls les utilisateurs importés dans SAP Cloud Platform Identity Authentication peuvent accéder à l’application.

Pour savoir comment activer ou désactiver la fédération des identités avec SAP Cloud Platform Identity Authentication, consultez « Activer la fédération des identités avec SAP Cloud Platform Identity Authentication » dans [Configure Identity Federation with the User Store of SAP Cloud Platform Identity Authentication (Configurer la fédération des identités avec le magasin d’utilisateurs de SAP Cloud Platform Identity Authentication)](https://help.sap.com/viewer/6d6d63354d1242d185ab4830fc04feb1/Cloud/c029bbbaefbf4350af15115396ba14e2.html).

> [!NOTE]
> SAP Cloud Platform Identity Authentication prend également en charge l’attribution automatique d’utilisateurs. Des informations supplémentaires sur la configuration de cette fonctionnalité sont disponibles [ici](./sap-cloud-platform-identity-authentication-provisioning-tutorial.md).

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes.

#### <a name="sp-initiated"></a>Lancée par le fournisseur de services :

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion SAP Cloud Platform Identity Authentication, à partir de laquelle vous pouvez lancer le flux de connexion.

* Accédez directement à l’URL de connexion SAP Cloud Platform Identity Authentication pour y lancer le processus de connexion.

#### <a name="idp-initiated"></a>Lancée par le fournisseur d’identité :

* Dans le portail Azure, cliquez sur **Tester cette application**. Vous êtes alors automatiquement connecté à l’application SAP Cloud Platform Identity Authentication pour laquelle vous avez configuré l’authentification unique

Vous pouvez aussi utiliser Mes applications de Microsoft pour tester l’application dans n’importe quel mode. Si, quand vous cliquez sur la vignette SAP Cloud Platform Identity Authentication dans Mes applications, le mode Fournisseur de services est configuré, vous êtes redirigé vers la page de connexion de l’application pour lancer le flux de connexion ; s’il s’agit du mode Fournisseur d’identité, vous êtes automatiquement connecté à l’instance de SAP Cloud Platform Identity Authentication pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré SAP Cloud Platform Identity Authentication, vous pouvez appliquer des contrôles de session, qui protègent l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Les contrôles de session sont étendus à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
