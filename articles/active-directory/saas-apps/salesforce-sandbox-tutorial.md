---
title: "Tutoriel : Intégration d'Azure Active Directory à Salesforce Sandbox | Microsoft Docs"
description: Découvrez comment configurer une authentification unique entre Azure Active Directory et Salesforce Sandbox.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/28/2020
ms.author: jeedes
ms.openlocfilehash: 4d564bbde122a3fddbe4bc2bb2c911fea846f38f
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132299703"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-salesforce-sandbox"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory à Salesforce Sandbox

Dans ce tutoriel, vous allez apprendre à intégrer Salesforce Sandbox à Azure Active Directory (Azure AD). Quand vous intégrez Salesforce Sandbox à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Salesforce Sandbox.
* Permettre à vos utilisateurs de se connecter automatiquement à Salesforce Sandbox avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Salesforce Sandbox pour lequel l’authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Salesforce Sandbox prend en charge l’authentification unique initiée par le **fournisseur de services et le fournisseur d’identité**
* Salesforce Sandbox prend en charge l’attribution d’utilisateurs **juste-à-temps**
* Salesforce Sandbox prend en charge l’attribution d’utilisateurs [**automatique**](salesforce-sandbox-provisioning-tutorial.md)

## <a name="adding-salesforce-sandbox-from-the-gallery"></a>Ajout de Salesforce Sandbox à partir de la galerie

Pour configurer l’intégration de Salesforce Sandbox avec Azure AD, vous devez ajouter Salesforce Sandbox à partir de la galerie à votre liste d’applications SaaS managées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Salesforce Sandbox** dans la zone de recherche.
1. Sélectionnez **Salesforce Sandbox** dans le volet des résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.


## <a name="configure-and-test-azure-ad-sso-for-salesforce-sandbox"></a>Configurer et tester l’authentification unique Azure AD pour Salesforce Sandbox

Configurez et testez l’authentification unique Azure AD avec Salesforce Sandbox à l’aide d’un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Salesforce Sandbox associé.

Pour configurer et tester l’authentification unique Azure AD avec Salesforce Sandbox, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Salesforce Sandbox](#configure-salesforce-sandbox-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test Salesforce Sandbox](#create-salesforce-sandbox-test-user)** pour avoir un équivalent de B.Simon dans Salesforce Sandbox lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **Salesforce Sandbox**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, si vous avez un **fichier de métadonnées de fournisseur de services** et voulez une configuration en mode initié par le **fournisseur d’identité** (IDP), effectuez les étapes suivantes :

    a. Cliquez sur **Charger un fichier de métadonnées**.

    ![Charger le fichier de métadonnées](common/upload-metadata.png)

    b. Cliquez sur le **logo du dossier** pour sélectionner le fichier de métadonnées, puis cliquez sur **Charger**.

    ![choisir le fichier de métadonnées](common/browse-upload-metadata.png)

    > [!NOTE]
    > Vous obtenez le fichier de métadonnées du fournisseur de services à partir du portail d’administration Salesforce Sandbox, décrit plus loin dans le tutoriel.

    c. Une fois que le fichier de métadonnées est correctement chargé, la valeur **URL de réponse** est renseignée automatiquement dans la zone de texte **URL de réponse**.

    ![image](common/both-replyurl.png)

    > [!Note]
    > Si la valeur **URL de réponse** n’est pas automatiquement renseignée, renseignez-la manuellement en fonction de vos besoins.

5. Sur la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le fichier **XML des métadonnées** en fonction des options définies, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

6. Dans la section **Configurer Salesforce Sandbox**, copiez la ou les URL appropriées, selon vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Salesforce Sandbox.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Salesforce Sandbox**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-salesforce-sandbox-sso"></a>Configurer l’authentification unique Salesforce Sandbox

1. Ouvrez un nouvel onglet dans votre navigateur et connectez-vous à votre compte d’administrateur Salesforce Sandbox.

2. Cliquez sur **Setup** sous **l’icône de paramètres** en haut à droite de la page.

    ![Capture d’écran mettant en évidence l’icône « Paramètres » en haut à droite et l’option « Configurer » dans le menu déroulant.](./media/salesforce-sandbox-tutorial/configure1.png)

3. Dans le volet de navigation de gauche, accédez à **SETTINGS** (PARAMÈTRES), puis cliquez sur **Identity** (Identité) pour développer la section associée. Puis cliquez sur **Paramètres de l’authentification unique**.

    ![Capture d’écran montrant le menu « Paramètres » dans le volet gauche, avec « Paramètres de l’authentification unique » sélectionné dans le menu « Identité ».](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

4. Sur la page **Paramètres de l’authentification unique**, cliquez sur le bouton **Modifier**.

    ![Capture d’écran montrant la page « Paramètres de l’authentification unique » avec le bouton « Modifier » sélectionné.](./media/salesforce-sandbox-tutorial/configure3.png)

5. Sélectionnez **SAML activé**, puis cliquez sur **Enregistrer**.

    ![Capture d’écran montrant la page « Paramètres de l’authentification unique » avec la case « SAML activé » cochée et le bouton « Enregistrer » sélectionné.](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

6. Pour configurer vos paramètres d’authentification unique SAML, cliquez sur **Nouveau à partir d’un fichier de métadonnées**.

    ![Capture d’écran montrant la page « Paramètres de l’authentification unique » avec le bouton « Nouveau à partir d’un fichier de métadonnées » sélectionné.](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

7. Cliquez sur **Sélectionner le fichier** pour charger le fichier XML de métadonnées que vous avez téléchargé à partir du Portail Azure, puis cliquez sur **Créer**.

    ![Capture d’écran montrant la page « Paramètres de l’authentification unique » avec les boutons « Sélectionner le fichier » et « Créer » sélectionnés.](./media/salesforce-sandbox-tutorial/xmlchoose.png)

8. Sur la page **Paramètres d’authentification unique SAML**, les champs se renseignent automatiquement et cliquez sur Enregistrer.

    ![Capture d’écran montrant la page « Paramètres de l’authentification unique » avec les champs remplis et le bouton « Enregistrer » sélectionné.](./media/salesforce-sandbox-tutorial/salesforcexml.png)

9. Dans la page **Paramètres de l’authentification unique**, cliquez sur le bouton **Télécharger les métadonnées** pour télécharger le fichier de métadonnées du fournisseur de services. Utilisez ce fichier dans la section **Configuration SAML de base** dans le portail Azure pour configurer les URL nécessaires comme expliqué ci-dessus.

    ![Capture d’écran montrant la page « Paramètres de l’authentification unique » avec le bouton « Télécharger les métadonnée » sélectionné.](./media/salesforce-sandbox-tutorial/configure4.png)

10. Si vous souhaitez configurer l’application en mode initié par le **fournisseur de service**, vous devez respecter les prérequis suivants :

    a. Vous devez disposer d’un domaine vérifié.

    b. Vous devez configurer et activer votre domaine sur Salesforce Sandbox. La procédure à suivre est décrite dans la suite de ce didacticiel.

    c. Dans le portail Azure, dans la section **Configuration SAML de base**, cliquez sur **Définir des URL supplémentaires** et effectuez l’étape suivante :
  
    ![Informations d’authentification unique dans Domaine et URL Salesforce Sandbox](common/both-signonurl.png)

    Dans la zone de texte **URL de connexion**, tapez la valeur au format suivant : `https://<instancename>--Sandbox.<entityid>.my.salesforce.com`

    > [!NOTE]
    > Cette valeur doit être copiée à partir du portail Salesforce Sandbox une fois que vous avez activé le domaine.

11. Dans la section **Certificat de signature SAML**, cliquez sur **XML de métadonnées de fédération**, puis enregistrez le fichier xml sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

12. Ouvrez un nouvel onglet dans votre navigateur et connectez-vous à votre compte d’administrateur Salesforce Sandbox.

13. Cliquez sur **Setup** sous **l’icône de paramètres** en haut à droite de la page.

    ![Capture d’écran mettant en évidence l’icône « Paramètres » en haut à droite et l’option « Configurer » dans le menu déroulant.](./media/salesforce-sandbox-tutorial/configure1.png)

14. Dans le volet de navigation de gauche, accédez à **SETTINGS** (PARAMÈTRES), puis cliquez sur **Identity** (Identité) pour développer la section associée. Puis cliquez sur **Paramètres de l’authentification unique**.

    ![Capture d’écran montrant le menu « Paramètres » dans le volet de navigation gauche, avec « Paramètres de l’authentification unique » sélectionné dans le menu « Identité ».](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

15. Sur la page **Paramètres de l’authentification unique**, cliquez sur le bouton **Modifier**.

    ![Capture d’écran montrant la page « Paramètres de l’authentification unique » avec le bouton « Modifier » sélectionné.](./media/salesforce-sandbox-tutorial/configure3.png)

16. Sélectionnez **SAML activé**, puis cliquez sur **Enregistrer**.

    ![Capture d’écran montrant la page « Paramètres de l’authentification unique » avec la case « SAML activé » cochée et le bouton « Enregistrer » sélectionné.](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

17. Pour configurer vos paramètres d’authentification unique SAML, cliquez sur **Nouveau à partir d’un fichier de métadonnées**.

    ![Capture d’écran montrant la page « Paramètres de l’authentification unique » et le bouton « Nouveau à partir d’un fichier de métadonnées » sélectionné.](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

18. Cliquez sur **Choisir un fichier** pour charger le fichier XML de métadonnées, puis sur **Créer**.

    ![Capture d’écran montrant la page « Paramètres de l’authentification unique » avec les boutons « Sélectionner le fichier » et « Créer » sélectionnés.](./media/salesforce-sandbox-tutorial/xmlchoose.png)

19. Sur la page **Paramètres d'authentification unique SAML**, les champs sont automatiquement renseignés. Entrez le nom de la configuration (par exemple : *SPSSOWAAD_Test*) dans la zone de texte **Nom** et cliquez sur Enregistrer.

    ![Capture d’écran montrant la page « Paramètres de l’authentification unique » avec les champs remplis, un exemple de nom dans la zone de texte « Nom » et le bouton « Enregistrer » sélectionné.](./media/salesforce-sandbox-tutorial/sf-saml-config.png)

20. Pour activer votre domaine sur Salesforce Sandbox, procédez comme suit :

    > [!NOTE]
    > Avant d’activer le domaine, vous devez créer le même sur Salesforce Sandbox. Pour plus d’informations, voir [Définition de votre nom de domaine](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_define.htm&language=en_US). Une fois le domaine créé, assurez-vous qu’il est configuré correctement.

21. Dans le volet de navigation de gauche de Salesforce Sandbox, cliquez sur **Company Settings** (Paramètres de l’entreprise) pour développer la section associée, puis cliquez sur **My Domain** (Mon domaine).

    ![Capture d’écran montrant « Paramètres de l’entreprise » et « Mon domaine » sélectionnés dans le volet de navigation gauche.](./media/salesforce-sandbox-tutorial/sf-my-domain.png)

22. Dans la section **Authentication Configuration** (Configuration de l’authentification), cliquez sur **Authentication Policy** (Stratégie d’authentification).

    ![Capture d’écran montrant la section « Configuration de l’authentification », avec le bouton « Modifier » sélectionné.](./media/salesforce-sandbox-tutorial/sf-edit-auth-config.png)

23. Dans la section **Authentication Configuration** (Configuration de l’authentification), pour le champ **Authentication Service** (Service d’authentification), sélectionnez le nom du paramètre d’authentification unique SAML que vous avez défini lors de la configuration SSO dans Salesforce Sandbox, puis cliquez sur **Save** (Enregistrer).

    ![Configure Single Sign-On](./media/salesforce-sandbox-tutorial/configure2.png)

### <a name="create-salesforce-sandbox-test-user"></a>Créer un utilisateur de test Salesforce Sandbox

Dans cette section, un utilisateur nommé Britta Simon est créé dans Salesforce Sandbox. Salesforce Sandbox prend en charge l’approvisionnement juste-à-temps, option activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. Si un utilisateur n’existe pas dans Salesforce Sandbox, un nouveau est créé lorsque vous tentez d’accéder à Salesforce Sandbox. Salesforce Sandbox prend également en charge l’attribution automatique d’utilisateurs, vous trouverez [ici](salesforce-sandbox-provisioning-tutorial.md) plus d’informations sur la façon de configurer l’attribution automatique d’utilisateurs.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

#### <a name="sp-initiated"></a>Lancée par le fournisseur de services :

* Cliquez sur **Tester cette application** dans le portail Azure. Cette opération redirige vers l’URL de connexion Salesforce Sandbox, où vous pouvez lancer le processus de connexion.  

* Accédez directement à l’URL de connexion Salesforce Sandbox pour lancer le processus de connexion.

#### <a name="idp-initiated"></a>Lancée par le fournisseur d’identité :

* Cliquez sur **Tester cette application** dans le portail Azure : vous devez être connecté automatiquement à l’instance de Salesforce Sandbox pour laquelle vous avez configuré l’authentification unique. 

Vous pouvez aussi utiliser Mes applications de Microsoft pour tester l’application dans n’importe quel mode. Si, quand vous cliquez sur la vignette Salesforce Sandbox dans Mes applications, le mode Fournisseur de services est configuré, vous êtes redirigé vers la page de connexion de l’application pour lancer le flux de connexion ; s’il s’agit du mode Fournisseur d’identité, vous êtes automatiquement connecté à l’instance de Salesforce Sandbox pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré Salesforce Sandbox, vous pouvez appliquer des contrôles de session, qui protègent contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Les contrôles de session sont étendus à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
