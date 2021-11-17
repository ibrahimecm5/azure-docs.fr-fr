---
title: 'Tutoriel : Intégration d’Azure Active Directory à Soloinsight-CloudGate SSO | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Soloinsight-CloudGate SSO.
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
ms.openlocfilehash: 0fabc06ae9656015296be8c6d96a0f3c3313bc3c
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132333330"
---
# <a name="tutorial-integrate-soloinsight-cloudgate-sso-with-azure-active-directory"></a>Tutoriel : Intégrer Soloinsight-CloudGate SSO à Azure Active Directory

Dans ce tutoriel, vous allez apprendre à intégrer Soloinsight-CloudGate SSO à Azure Active Directory (Azure AD). Lorsque vous intégrez Soloinsight-CloudGate SSO à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Soloinsight-CloudGate SSO.
* Permettre aux utilisateurs de se connecter automatiquement à Soloinsight-CloudGate SSO avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Soloinsight-CloudGate SSO pour lequel l’authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* Soloinsight-CloudGate SSO prend en charge l’authentification unique lancée par le **fournisseur de services**.
* Soloinsight-CloudGate SSO prend en charge l’[attribution automatisée d’utilisateurs](soloinsight-cloudgate-sso-provisioning-tutorial.md).

## <a name="add-soloinsight-cloudgate-sso-from-the-gallery"></a>Ajouter Soloinsight-CloudGate SSO à partir de la galerie

Pour configurer l’intégration de Soloinsight-CloudGate SSO à Azure AD, vous devez ajouter Soloinsight-CloudGate SSO à partir de la galerie à votre liste d’applications SaaS managées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Soloinsight-CloudGate SSO** dans la zone de recherche.
1. Sélectionnez **Soloinsight-CloudGate SSO** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-soloinsight-cloudgate-sso"></a>Configurer et tester Azure AD SSO pour Soloinsight-CloudGate SSO

Configurez et testez Azure AD SSO avec Soloinsight-CloudGate SSO pour un utilisateur de test nommé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Soloinsight-CloudGate SSO associé.

Pour configurer et tester Azure AD SSO avec Soloinsight-CloudGate SSO, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer Soloinsight-CloudGate SSO](#configure-soloinsight-cloudgate-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test Soloinsight-CloudGate SSO](#create-soloinsight-cloudgate-sso-test-user)** pour avoir un équivalent de B.Simon dans Soloinsight-CloudGate SSO lié à la représentation Azure AD associée.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **Soloinsight-CloudGate SSO**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon pour **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la page **Configuration SAML de base**, effectuez les étapes suivantes :

    1. Dans la zone de texte **URL de connexion**, saisissez une URL au format suivant : `https://<SUBDOMAIN>.sigateway.com/login`

    1. Dans la zone de texte **Identificateur (ID d’entité)** , saisissez une URL au format suivant : `https://<SUBDOMAIN>.sigateway.com/process/sso`

   > [!NOTE]
   > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur et l’URL de connexion réels, tel qu’expliqué dans la section **Configurer l’authentification unique Soloinsight-CloudGate SSO** de ce tutoriel.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **Certificat (Base64)** , puis sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

   ![Lien Téléchargement de certificat](common/certificatebase64.png)

1. Dans la section **Configurer Soloinsight-CloudGate SSO**, copiez la ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Soloinsight-CloudGate SSO.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Soloinsight-CloudGate SSO**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-soloinsight-cloudgate-sso"></a>Configurer Soloinsight-CloudGate SSO

1. Pour automatiser la configuration dans Soloinsight-CloudGate SSO, vous devez installer l’**extension de navigateur My Apps Secure Sign-in** en cliquant sur **Installer l’extension**.

    ![Extension My apps](common/install-myappssecure-extension.png)

2. Après l’ajout de l’extension au navigateur, cliquez sur **Configurer Soloinsight-CloudGate SSO** pour être dirigé vers l’application Soloinsight-CloudGate SSO. À partir de là, indiquez les informations d’identification de l’administrateur pour vous connecter à Soloinsight-CloudGate SSO. Cette extension de navigateur configure automatiquement l’application et automatise les étapes 3 à 8.

    ![Configuration](common/setup-sso.png)

3. Si vous souhaitez configurer manuellement Soloinsight-CloudGate SSO, ouvrez une nouvelle fenêtre de navigateur web, connectez-vous à votre site d’entreprise Soloinsight-CloudGate SSO en tant qu’administrateur, puis effectuez les étapes suivantes :

4. Pour obtenir les valeurs qui doivent être collées dans le portail Azure lors de la configuration SAML de base, connectez-vous au portail web CloudGate à l’aide de vos informations d’identification, puis accédez aux paramètres d’authentification unique à l’emplacement suivant : **Home>Administration>System settings>General**.

    ![Paramètres CloudGate SSO](./media/soloinsight-cloudgate-sso-tutorial/main-settings.png)

5. **URL de consommateur SAML**

    * Copiez les liens indiqués dans les champs **Saml Consumer URL** (URL de consommateur SAML) et **Redirect URL** (URL de redirection), et collez-les dans le Portail Azure dans la section **Configuration SAML de base**, respectivement dans les champs **Identificateur (ID d’entité)** et **URL de réponse**.

        ![SAMLIdentifier](./media/soloinsight-cloudgate-sso-tutorial/identifier.png)

6. **Certificat de signature SAML**

    * Accédez à la source du fichier de certificat (Base64) téléchargé à partir des listes de certificats de signature SAML sur le Portail Azure, et cliquez dessus avec le bouton droit. Choisissez l’option **Edit with Notepad++** (Modifier avec Notepad++) dans la liste. 

        ![SAMLcertificate](./media/soloinsight-cloudgate-sso-tutorial/certificate-file.png)

    * Copiez le contenu dans le fichier de certificat (Base64) Notepad++.

        ![Copie du certificat](./media/soloinsight-cloudgate-sso-tutorial/certificate-copy.png)

    * Collez le contenu dans le champ **Certificate** des paramètres d’authentification unique dans le portail web CloudGate et cliquez sur le bouton Save (Enregistrer).

        ![Portail de certificat](./media/soloinsight-cloudgate-sso-tutorial/certificate-portal.png)

7. **Groupe par défaut**

    * Sélectionnez **Business Admin** (Administrateur d’entreprise) dans la liste déroulante de l’option **Default Group** (Groupe par défaut) dans le portail web CloudGate.

        ![Groupe par défaut](./media/soloinsight-cloudgate-sso-tutorial/default-group.png)

8. **Identificateur AD et URL de connexion**

    * L’**URL de connexion** copiée à partir des paramètres **Configurer l’authentification unique Soloinsight-CloudGate** dans le Portail Azure doit être entrée dans la section de paramètres d’authentification unique du portail web CloudGate.

    * Collez le lien **URL de connexion** du Portail Azure dans le champ **AD Login URL** (URL de connexion AD) du portail web CloudGate.

    * Collez le lien **Identificateur Azure AD** du Portail Azure dans le champ **AD Identifier** (Identificateur AD) du portail web CloudGate.

        ![Connexion AD](./media/soloinsight-cloudgate-sso-tutorial/ad-login.png)

### <a name="create-soloinsight-cloudgate-sso-test-user"></a>Créer utilisateur de test Soloinsight-CloudGate SSO

Pour créer un utilisateur de test, sélectionnez **Employees** dans le menu principal du portail web CloudGate et renseignez le formulaire Add New employee (Ajouter un nouvel employé). Le niveau d’autorité qui doit être affecté à l’utilisateur de test est **Business Admin** (Administrateur d’entreprise). Cliquez sur **Create** (Créer) une fois que vous avez renseigné tous les champs obligatoires.

![Test de l’employé](./media/soloinsight-cloudgate-sso-tutorial/employee-test.png)

> [!NOTE]
> Soloinsight-CloudGate SSO prend également en charge l’attribution automatique d’utilisateurs. Vous trouverez plus d’informations sur la configuration de cette fonctionnalité [ici](./soloinsight-cloudgate-sso-provisioning-tutorial.md).

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes redirigé vers l’URL de connexion Soloinsight-CloudGate - SSO à partir de laquelle vous pouvez lancer le flux de connexion. 

* Accédez directement à l’URL de connexion Soloinsight-CloudGate - SSO pour y lancer le flux de connexion.

* Vous pouvez utiliser Mes applications de Microsoft. Lorsque vous cliquez sur la tuile Soloinsight-CloudGate SSO dans Mes applications, vous serez redirigé vers l’URL de connexion Soloinsight-CloudGate SSO. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré Soloinsight-CloudGate SSO, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
