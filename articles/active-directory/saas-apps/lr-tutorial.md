---
title: 'Didacticiel : Intégration d’Azure Active Directory avec LoginRadius | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et LoginRadius.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/27/2021
ms.author: jeedes
ms.openlocfilehash: 1774e5f7099d130fe1692911fbcf1f7794fa7e20
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132285111"
---
# <a name="tutorial-azure-active-directory-integration-with-loginradius"></a>Didacticiel : Intégration d’Azure Active Directory avec LoginRadius

Dans ce tutoriel, vous allez apprendre à intégrer LoginRadius à Azure Active Directory (Azure AD). Quand vous intégrez LoginRadius à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à LoginRadius.
* Permettre à vos utilisateurs de se connecter automatiquement à LoginRadius avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD avec LoginRadius, vous aurez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un [compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement LoginRadius pour lequel l’authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* LoginRadius prend en charge l’authentification unique lancée par le **fournisseur de services**.

> [!NOTE]
> L’identificateur de cette application étant une valeur de chaîne fixe, une seule instance peut être configurée dans un locataire.

## <a name="add-loginradius-from-the-gallery"></a>Ajouter LoginRadius à partir de la galerie

Pour configurer l’intégration de LoginRadius à Azure AD, vous devez ajouter LoginRadius à votre liste d’applications SaaS gérées à partir de la galerie.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **LoginRadius** dans la zone de recherche.
1. Sélectionnez **LoginRadius** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-loginradius"></a>Configurer et tester l’authentification unique Azure AD pour LoginRadius

Configurez et testez l’authentification unique Azure AD avec LoginRadius pour un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur associé dans LoginRadius.

Pour configurer et tester l’authentification unique Azure AD avec LoginRadius, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique LoginRadius](#configure-loginradius-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test LoginRadius](#create-loginradius-test-user)** pour avoir un équivalent de B.Simon dans LoginRadius associé à sa représentation dans Azure AD.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **LoginRadius**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

   1. Dans la zone de texte **Identificateur (ID d’entité)** , entrez l’URL `https://lr.hub.loginradius.com/`

   1. Dans la zone de texte **URL de réponse (URL Assertion Consumer Service)** , entrez l’URL ACS LoginRadius `https://lr.hub.loginradius.com/saml/serviceprovider/AdfsACS.aspx` 

   1. Dans la zone de texte **URL de connexion**, entrez l’URL `https://secure.loginradius.com/login`

5. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, sélectionnez **Télécharger** pour télécharger le fichier **XML de métadonnées de fédération** en fonction des options que vous avez définies par rapport à vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

6. Dans la section **Configurer LoginRadius**, copiez la ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à LoginRadius.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **LoginRadius**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-loginradius-sso"></a>Configurer l’authentification unique LoginRadius

Dans cette section, vous allez activer l’authentification unique Azure AD dans la console d’administration LoginRadius.

1. Connectez-vous à votre compte LoginRadius [Admin Console](https://adminconsole.loginradius.com/login).

2. Accédez à la section **Team Management** (Gestion d’équipe) de [LoginRadius Admin Console](https://secure.loginradius.com/account/team).

3. Sélectionnez l’onglet **Single Sign-On** (Authentification unique), puis **Azure AD** :

   ![Capture d’écran montrant le menu d’authentification unique dans la console de gestion d’équipe LoginRadius](./media/loginradius-tutorial/azure-ad.png)

4. Dans la page de configuration Azure AD, effectuez les étapes suivantes :

   ![Capture d’écran montrant la configuration d’Azure Active Directory dans la console de gestion d’équipe LoginRadius](./media/loginradius-tutorial/single-sign-on.png)

    1. Dans **ID Provider Location** (Emplacement du fournisseur d’ID), entrez le point de terminaison de connexion, que vous obtenez dans votre compte Azure AD.

    1. Dans **ID Provider Logout URL** (URL de déconnexion du fournisseur d’ID), entrez le point de terminaison de déconnexion, que vous obtenez dans votre compte Azure AD.
 
    1. Dans **ID Provider Certificate** (Certificat du fournisseur d’ID), entrez le certificat Azure AD, que vous obtenez dans votre compte Azure AD. Entrez la valeur du certificat avec l’en-tête et le pied de page. Exemple : `-----BEGIN CERTIFICATE-----<certifciate value>-----END CERTIFICATE-----`

    1. Dans **Service Provider Certificate** (Certificat du fournisseur de services) et **Server Provider Certificate Key** (Clé du certificat du fournisseur de services), entrez votre certificat et votre clé. 

       Vous pouvez créer un certificat auto-signé en exécutant les commandes suivantes sur la ligne de commande (Linux/Mac) :

       - Commande pour obtenir la clé de certificat pour le fournisseur de services : `openssl genrsa -out lr.hub.loginradius.com.key 2048`

       - Commande pour obtenir le certificat pour le fournisseur de services : `openssl req -new -x509 -key lr.hub.loginradius.com.key -out lr.hub.loginradius.com.cert -days 3650 -subj /CN=lr.hub.loginradius.com`

       > [!NOTE]
       > Veillez à entrer les valeurs de certificat et de clé de certificat avec l’en-tête et le pied de page :
       > - Exemple de format de valeur de certificat : `-----BEGIN CERTIFICATE-----<certifciate value>-----END CERTIFICATE-----`
       > - Exemple de format de valeur de clé de certificat : `-----BEGIN RSA PRIVATE KEY-----<certifciate key value>-----END RSA PRIVATE KEY-----`

5. Dans la section **Data Mapping** (Mappage de données), sélectionnez les champs (champs de fournisseur de services) et entrez les champs Azure AD correspondants (champs de fournisseur d’identité).

    Voici une liste de quelques noms de champs pour Azure AD.

    | Champs    | Clé de profil                                                          |
    | --------- | -------------------------------------------------------------------- |
    | E-mail     | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` |
    | FirstName | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`    |
    | LastName  | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`      |

    > [!NOTE]
    > Le champ **Email** doit être mappé. Le mappage des champs **FirstName** et **LastName** est facultatif.

### <a name="create-loginradius-test-user"></a>Créer un utilisateur de test LoginRadius

1. Connectez-vous à votre compte LoginRadius [Admin Console](https://adminconsole.loginradius.com/login).

2. Accédez à la section de gestion de votre équipe dans la console d’administration LoginRadius.

   ![Capture d’écran montrant la console d’administration LoginRadius](./media/loginradius-tutorial/team-management.png)

3. Sélectionnez **Add Team Member** (Ajouter un membre à l’équipe) dans le menu latéral pour ouvrir le formulaire. 

4. Dans le formulaire **Add Team Member** (Ajouter un membre à l’équipe), créez un utilisateur nommé Britta Simon sur votre site LoginRadius en fournissant les détails de l’utilisateur et en lui attribuant les autorisations souhaitées. Pour en savoir plus sur les autorisations en fonction des rôles, consultez la section [Role Access Permissions](https://www.loginradius.com/docs/api/v2/admin-console/team-management/manage-team-members#roleaccesspermissions0) du document LoginRadius relatif à la [gestion des membres d’équipe](https://www.loginradius.com/docs/api/v2/admin-console/team-management/manage-team-members#roleaccesspermissions0). Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide de Mes applications.

1. Dans un navigateur, accédez à https://accounts.loginradius.com/auth.aspx, puis sélectionnez **Fed SSO log in**.
2. Entrez le nom de votre application LoginRadius, puis sélectionnez **Login** (Connexion).
3. Une fenêtre contextuelle s’ouvre alors pour vous inviter à vous connecter à votre compte Azure AD.
4. Une fois l’authentification effectuée, la fenêtre contextuelle se ferme et vous êtes connecté à la console d’administration LoginRadius.

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré LoginRadius, vous pouvez appliquer le contrôle de session, qui protège en temps réel contre l’exfiltration et l’infiltration des données sensibles de votre organisation. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
