---
title: 'Tutoriel : Intégration de l’authentification unique Azure AD à Cisco Webex'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Cisco Webex.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/01/2021
ms.author: jeedes
ms.openlocfilehash: 3647f3d8736d59aaeee4f73211dbf75c690f2446
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131474531"
---
# <a name="tutorial-azure-ad-sso-integration-with-cisco-webex"></a>Tutoriel : Intégration de l’authentification unique Azure AD à Cisco Webex

Ce didacticiel explique comment intégrer Cisco Webex avec Azure Active Directory (Azure AD). Quand vous intégrez Cisco Webex à Azure AD, vous pouvez :

* Dans Azure AD, contrôlez qui a accès à Cisco Webex.
* Permettre à vos utilisateurs de se connecter automatiquement à Cisco Webex avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Abonnement Cisco Webex pour lequel l’authentification unique est activée.
*  Fichier de métadonnées du fournisseur de services de Cisco Webex.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* Cisco Webex prend en charge l’authentification unique lancée par le **fournisseur de services**.
* Cisco Webex prend en charge [**l’attribution d’utilisateurs automatique**](./cisco-webex-provisioning-tutorial.md).

> [!NOTE]
> L’identificateur de cette application étant une valeur de chaîne fixe, une seule instance peut être configurée dans un locataire.

## <a name="adding-cisco-webex-from-the-gallery"></a>Ajout de Cisco Webex à partir de la galerie

Pour configurer l’intégration de Cisco Webex à Azure AD, vous devez ajouter Cisco Webex, disponible dans la galerie, à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Cisco Webex** dans la zone de recherche.
1. Sélectionnez **Cisco Webex** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-cisco-webex"></a>Configurer et tester l’authentification unique Azure AD pour Cisco Webex

Configurez et testez l’authentification unique Azure AD avec Cisco Webex pour un utilisateur de test nommé **B. Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Cisco Webex associé.

Pour configurer et tester l’authentification unique Azure AD avec Cisco Webex, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
2. **[Configurer l’authentification unique Cisco Webex](#configure-cisco-webex-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test Cisco Webex](#create-cisco-webex-test-user)** pour avoir un équivalent de B. Simon dans Cisco Webex, lié à la représentation Azure AD associée.
3. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **Cisco Webex**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon pour **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, chargez le **fichier de métadonnées du fournisseur de services** téléchargé et effectuez les étapes suivantes pour configurer l’application :

    >[!Note]
    >Vous obtiendrez le fichier de métadonnées de fournisseur de services dans la section **Configurer Cisco Webex** du tutoriel. 

    a. Cliquez sur **Charger un fichier de métadonnées**.

    b. Cliquez sur le **logo du dossier** pour sélectionner le fichier de métadonnées, puis cliquez sur **Charger**.

    c. Une fois le chargement du fichier de métadonnées du fournisseur de services terminé, les valeurs **Identificateur** et **URL de réponse** sont automatiquement remplies dans la section **Configuration SAML de base**, comme indiqué ci-dessous :

    d. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://web.ciscospark.com/idb/Consumer/metaAlias/<ID>/sp`
    
    > [!NOTE]
    > Cette valeur n’est pas la valeur réelle. Copiez la valeur de l’URL de réponse littérale et ajoutez cette valeur à `https://web.ciscospark.com/` pour formuler la valeur réelle de l’URL d’authentification.

1. L’application Cisco Webex s’attend à recevoir les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à la configuration de vos attributs de jeton SAML. La capture d’écran suivante montre la liste des attributs par défaut.

    ![image](common/default-attributes.png)

1. En plus de ce qui précède, l’application Cisco Webex s’attend à ce que quelques attributs supplémentaires (présentés ci-dessous) soient repassés dans la réponse SAML. Ces attributs sont également préremplis, mais vous pouvez les examiner pour voir s’ils répondent à vos besoins.
  
    | Nom |  Attribut source|
    | ---------------|--------- |
    | uid | user.userprincipalname |

    > [!NOTE]
    >  La valeur de l’attribut source est mappée par défaut à userprincipalname. Vous pouvez la remplacer par user.mail, user.onpremiseuserprincipalname ou toute autre valeur en fonction du paramètre de Webex.


1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **XML de métadonnées de fédération** et sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

   ![Lien Téléchargement de certificat](common/metadataxml.png)

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

Dans cette section, vous allez autoriser B. Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Cisco Webex.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Cisco Webex**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-cisco-webex-sso"></a>Configurer l’authentification unique Cisco Webex

1. Connectez-vous à Cisco Webex avec vos informations d’identification d’administrateur.

1. Sélectionnez **Organization Settings** (Paramètres de l’organisation), puis, dans la section **Authentication** (Authentification), cliquez sur **Modify** (Modifier).

    ![Capture d’écran montrant Authentication Settings où vous pouvez sélectionner Modify.](./media/cisco-spark-tutorial/organization-settings.png)
  
1. Sélectionnez **intégrer un fournisseur d’identité tiers. (Advanced)** et cliquez sur **Next** (Suivant).

    ![Capture d’écran illustrant la page Integrate a 3rd-party identity provider.](./media/cisco-spark-tutorial/enterprise-settings.png)

1. Cliquez sur **Download Metadata File** (Télécharger le fichier de métadonnées) pour télécharger le **fichier de métadonnées du fournisseur de services** et enregistrez-le sur votre ordinateur, puis cliquez sur **Next** (Suivant).

    ![Capture d’écran montrant le fichier de métadonnées du fournisseur de services.](./media/cisco-spark-tutorial/sp-metadata.png)

1. Cliquez sur l’option d’**exploration des fichiers** pour localiser et charger le fichier de métadonnées Azure AD. Ensuite, sélectionnez **Require certificate signed by a certificate authority in Metadata (more secure)** (Exiger un certificat signé par une autorité de certification dans les métadonnées (plus sûr)), puis cliquez sur **Next** (Suivant).

    ![Capture d’écran montant la page Import Idp Metadata.](./media/cisco-spark-tutorial/idp-metadata.png)

1. Sélectionnez **Test SSO Connection** (Tester la connexion SSO), puis, quand un nouvel onglet de navigateur s’ouvre, et authentifiez-vous auprès d’Azure AD en vous connectant.

1. Revenez à l’onglet du navigateur **Cisco Cloud Collaboration Management** (Gestion de la collaboration dans le cloud Cisco). Si le test a réussi, sélectionnez **Ce test a réussi. option Activer l’authentification unique** et cliquez sur **Suivant**.

1. Cliquez sur **Enregistrer**.

> [!NOTE]
> Pour en savoir plus sur la configuration de Cisco Webex, reportez-vous à [cette](https://help.webex.com/WBX000022701/How-Do-I-Configure-Microsoft-Azure-Active-Directory-Integration-with-Cisco-Webex-Through-Site-Administration#:~:text=In%20the%20Azure%20portal%2C%20select,in%20the%20Add%20Assignment%20dialog) page.

### <a name="create-cisco-webex-test-user"></a>Créer un utilisateur de test Cisco Webex

Dans cette section, un utilisateur appelé B.Simon est créé dans Cisco Webex. Cette application prend en charge l’attribution automatique d’utilisateurs, ce qui permet l’approvisionnement et la suppression des privilèges d’accès automatiques en fonction de vos règles d’entreprise.  Microsoft recommande d’utiliser l’approvisionnement automatique chaque fois que cela est possible. Voir comment activer l’approvisionnement automatique pour [Cisco Webex](./cisco-webex-provisioning-tutorial.md).

Si vous avez besoin de créer un utilisateur manuellement, procédez comme suit :

1. Connectez-vous à Cisco Webex avec vos informations d’identification d’administrateur.

2. Cliquez sur **Utilisateurs**, puis sur **Gérer les utilisateurs**.
   
    ![Capture d’écran montrant la page Users où vous pouvez gérer les utilisateurs.](./media/cisco-spark-tutorial/user-1.png) 

3. Dans la fenêtre **Manage Users** (Gérer les utilisateurs), sélectionnez **Manually Add or Modify Users** (Ajouter ou modifier manuellement des utilisateurs).

    ![La capture d’écran montre la page Users (Utilisateurs) où vous pouvez gérer les utilisateurs et sélectionner Manually Add or Modify Users.](./media/cisco-spark-tutorial/user-2.png)

4. Sélectionnez **Names and Email address** (Noms et adresse de messagerie). Ensuite, complétez la zone de texte comme suit :

    ![Capture d’écran montrant la boîte de dialogue Manage Users où vous pouvez ajouter ou modifier manuellement des utilisateurs.](./media/cisco-spark-tutorial/user-3.png) 

    a. Dans la zone de texte **First Name** (Prénom), tapez le prénom de l’utilisateur, par exemple **B**.

    b. Dans la zone de texte **Last Name** (Nom de famille), tapez le nom de l’utilisateur, par exemple **Simon**.

    c. Dans la zone de texte **Email address** (Adresse e-mail), tapez l’adresse e-mail d’un utilisateur, par exemple, b.simon@contoso.com.

5. Cliquez sur le signe plus pour ajouter B.Simon. Cliquez ensuite sur **Suivant**.

6. Dans la fenêtre **Add Services for Users** (Ajouter des services pour les utilisateurs), cliquez sur **Add Users** (Ajouter des utilisateurs), puis sur **Finish** (Terminer).

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion à Cisco Webex, d’où vous pouvez lancer le flux de connexion. 

* Accédez directement à l’URL de connexion à Cisco Webex pour lancer le flux de connexion.

* Vous pouvez utiliser Mes applications de Microsoft. Le fait de cliquer sur la vignette Cisco Webex dans Mes applications vous redirige vers l’URL de connexion à Cisco Webex. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).


## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré Cisco Webex, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).
