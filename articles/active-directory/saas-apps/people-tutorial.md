---
title: 'Tutoriel : Intégration de l’authentification unique Azure Active Directory à People | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et People.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/27/2021
ms.author: jeedes
ms.openlocfilehash: d897457695009b7b7b9da0bb8f09c976aba66fe1
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132334033"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-people"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory à People

Dans ce tutoriel, vous allez apprendre à intégrer People à Azure Active Directory (Azure AD). Quand vous intégrez People à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à People
* Permettre à vos utilisateurs de se connecter automatiquement à People avec leur compte Azure AD
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Abonnement People pour lequel l’authentification unique (SSO) est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* People prend en charge l’authentification unique lancée par le **fournisseur de services**
* L’application mobile People peut désormais être configurée avec Azure AD pour activer l’authentification unique. Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

>[!NOTE]
>L’identificateur de cette application étant une valeur de chaîne fixe, une seule instance peut être configurée dans un locataire.

## <a name="add-people-from-the-gallery"></a>Ajouter People à partir de la galerie

Pour configurer l’intégration de People à Azure AD, vous devez ajouter People, disponible dans la galerie, à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **People** dans la zone de recherche.
1. Sélectionnez **People** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-people"></a>Configurer et tester l’authentification unique Azure AD pour People

Configurez et testez l’authentification unique Azure AD avec People à l’aide d’un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur People associé.

Pour configurer et tester l’authentification unique Azure AD avec People, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
2. **[Configurer l’authentification unique People](#configure-people-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test People](#create-people-test-user)** pour obtenir dans People un équivalent de B. Simon lié à la représentation Azure AD associée.
6. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, dans la page d’intégration de l’application **People**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon pour **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, entrez les valeurs pour les champs suivants :

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<company name>.peoplehr.net`.

    b. Dans la zone **Identificateur**, tapez l’URL : `https://www.peoplehr.com`

    c. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://<company name>.peoplehr.net/Pages/Saml/ConsumeAzureAD.aspx`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de réponse et l’URL de connexion réelles. Pour obtenir ces valeurs, contactez l’[équipe du support technique People](mailto:customerservices@peoplehr.com). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

4. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **XML de métadonnées de fédération** et sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

6. Dans la section **Configurer People**, copiez l’URL ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en accordant l’accès à People.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **People**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-people-sso"></a>Configurer l’authentification unique People

1. Pour automatiser la configuration dans People, vous devez installer l’**extension de navigateur My Apps Secure Sign-in** en cliquant sur **Installer l’extension**.

    ![Extension My apps](common/install-myappssecure-extension.png)

2. Après l’ajout de l’extension au navigateur, cliquez sur **Configurer People** pour être dirigé vers l’application People. À partir de là, indiquez les informations d’identification de l’administrateur pour vous connecter à People. Cette extension de navigateur configure automatiquement l’application et automatise les étapes 3 à 6.

    ![Configuration](common/setup-sso.png)

3. Si vous souhaitez configurer manuellement People, ouvrez une nouvelle fenêtre de navigateur web, connectez-vous à votre site d’entreprise People en tant qu’administrateur et effectuez les étapes suivantes :
   
4. Dans le menu sur le côté gauche, cliquez sur **Paramètres**.

    ![Capture d’écran montrant le menu de gauche avec l’élément « Settings » sélectionné.](./media/people-tutorial/settings.png)

5. Cliquez sur **Company**.

    ![Capture d’écran montrant l’élément « Company » sélectionné dans le menu « Settings ».](./media/people-tutorial/company.png)

6. Dans **Importer le fichier de métadonnées SAML « Authentification unique »** , cliquez sur **Parcourir** pour importer le fichier de métadonnées téléchargé.

    ![Configure Single Sign-On](./media/people-tutorial/xml.png)

### <a name="create-people-test-user"></a>Créer un utilisateur de test People

Dans cette section, vous créez un utilisateur appelé B.Simon dans People. Collaborez avec l’[équipe du support technique People](mailto:customerservices@peoplehr.com) pour ajouter des utilisateurs dans la plateforme People. Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Cette opération redirige vers l’URL de connexion à People, où vous pouvez lancer le flux de connexion. 

* Accédez directement à l’URL de connexion à People et lancez le flux de connexion.

* Vous pouvez utiliser Mes applications de Microsoft. Un clic sur la vignette People dans Mes applications vous redirige vers l’URL de connexion à People. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="test-sso-for-people-mobile"></a>Tester l’authentification unique pour People (Mobile)

1. Ouvrez l’application People Mobile. Dans la page de connexion, entrez **Email ID**, puis cliquez sur **Single Sign On** (Authentification unique).

    ![Connexion](./media/people-tutorial/test01.png)

2. Entrez **organization UserID**, puis cliquez sur **Next**.

    ![L’e-mail](./media/people-tutorial/test02.png)

3. Une fois la connexion réussie, la page d’accueil de l’application s’affiche comme illustrée ci-dessous :

    ![L’unique fois](./media/people-tutorial/test03.png)

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré People, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
