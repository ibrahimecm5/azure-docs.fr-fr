---
title: 'Tutoriel : Intégration d’Azure Active Directory à xMatters OnDemand | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et xMatters OnDemand.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/07/2021
ms.author: jeedes
ms.openlocfilehash: 82e0e8543f51d6bb8a6965e19712a079502564e2
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132303667"
---
# <a name="tutorial-azure-active-directory-integration-with-xmatters-ondemand"></a>Tutoriel : Intégration d’Azure Active Directory à xMatters OnDemand

Dans ce tutoriel, vous allez apprendre à intégrer xMatters OnDemand à Azure Active Directory (Azure AD). Quand vous intégrez xMatters OnDemand à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à xMatters OnDemand.
* Permettre à vos utilisateurs de se connecter automatiquement à xMatters OnDemand avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à xMatters OnDemand, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un [compte gratuit](https://azure.microsoft.com/free/).
* Abonnement xMatters OnDemand pour lequel l’authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* xMatters OnDemand prend en charge l’authentification unique lancée par le **fournisseur d’identité**.

## <a name="add-xmatters-ondemand-from-the-gallery"></a>Ajouter xMatters OnDemand à partir de la galerie

Pour configurer l’intégration de xMatters OnDemand à Azure AD, vous devez ajouter xMatters OnDemand disponible dans la galerie, à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **xMatters OnDemand** dans la zone de recherche.
1. Sélectionnez **xMatters OnDemand** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-xmatters-ondemand"></a>Configurer et tester l’authentification unique Azure AD pour xMatters OnDemand

Configurez et testez l’authentification unique Azure AD avec xMatters OnDemand à l’aide d’un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur xMatters OnDemand associé.

Pour configurer et tester l’authentification unique Azure AD avec xMatters OnDemand, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
    2. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
2. **[Configurer l’authentification unique xMatters OnDemand](#configure-xmatters-ondemand-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test xMatters OnDemand](#create-xmatters-ondemand-test-user)** pour avoir un équivalent de Britta Simon dans xMatters OnDemand lié à la représentation Azure AD de l’utilisateur.
3. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **xMatters OnDemand**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    a. Dans la zone de texte **Identificateur**, tapez une URL en utilisant un des modèles suivants :

    | Identificateur |
    | ---------- |
    | `https://<COMPANY_NAME>.au1.xmatters.com.au/` |
    | `https://<COMPANY_NAME>.cs1.xmatters.com/` |
    | `https://<COMPANY_NAME>.xmatters.com/` |
    | `https://www.xmatters.com` |
    | `https://<COMPANY_NAME>.xmatters.com.au/` |

    b. Dans la zone de texte **URL de réponse** , tapez une URL en utilisant un des modèles suivants :

    | URL de réponse |
    | ---------- |
    | `https://<COMPANY_NAME>.au1.xmatters.com.au` |
    | `https://<COMPANY_NAME>.xmatters.com/sp/<INSTANCE_NAME>` |
    | `https://<COMPANY_NAME>.cs1.xmatters.com/sp/<INSTANCE_NAME>` |
    | `https://<COMPANY_NAME>.au1.xmatters.com.au/<INSTANCE_NAME>` |

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur et l’URL de réponse réels. Pour obtenir ces valeurs, contactez l’[l’équipe de support technique de xMatters OnDemand](https://www.xmatters.com/company/contact-us/). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

5. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (Base64)** en fonction des options définies par rapport à vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

    > [!IMPORTANT]
    > Vous devez transférer le certificat à l’[équipe de support technique de xMatters OnDemand](https://www.xmatters.com/company/contact-us/). L’équipe du support technique xMatters doit charger le certificat avant que vous ne puissiez finaliser la configuration de l’authentification unique.

6. Dans la section **Configurer xMatters OnDemand**, copiez la ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à xMatters OnDemand.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **xMatters OnDemand**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-xmatters-ondemand-sso"></a>Configurer l’authentification unique xMatters OnDemand

1. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise xMatters OnDemand en tant qu’administrateur.

2. Cliquez sur **Admin**, puis sur **Company Details** (Détails sur l’entreprise).

    ![Page d’administration](./media/xmatters-ondemand-tutorial/admin.png "Page d’administration")

3. Dans la page **SAML Configuration** , procédez comme suit :

    ![Section Configuration SAML ](./media/xmatters-ondemand-tutorial/saml-configuration.png "Section Configuration SAML ")

    a. Sélectionnez **Enable SAML**.

    b. Dans la zone de texte **ID du fournisseur d’identité**, collez la valeur de l’**identificateur Azure AD** que vous avez copiée à partir du Portail Azure.

    c. Dans la zone de texte **Single Sign On URL** (URL d’authentification unique), collez l’**URL de connexion** que vous avez copiée sur le Portail Azure.

    d. Dans la zone de texte **Logout URL Redirect** (Redirection d’URL de déconnexion), collez l’**URL de déconnexion** que vous avez copiée sur le portail Azure.

    e. Cliquez sur **Choisir un fichier** pour charger le **certificat (Base64)** que vous avez téléchargé à partir du portail Azure. 

    f. Dans la page des détails de la société, en haut, cliquez sur **Enregistrer les modifications**.

    ![Détails de la société](./media/xmatters-ondemand-tutorial/save-button.png "Détails de la société")

### <a name="create-xmatters-ondemand-test-user"></a>Créer un utilisateur de test xMatters OnDemand

1. Connectez-vous à votre locataire **xMatters OnDemand**.

2. Accédez à l’**icône utilisateurs** > **Users** (Utilisateurs) puis cliquez sur **Add Users** (Ajouter des utilisateurs).

    ![Utilisateurs](./media/xmatters-ondemand-tutorial/add-user.png "Utilisateurs")

3. Dans la section **Add Users**, renseignez les champs requis, puis cliquez sur le bouton **Add User**.

    ![Add a User](./media/xmatters-ondemand-tutorial/add-user-2.png "Ajouter un utilisateur") (Ajouter un utilisateur)

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes.

* Cliquez sur Tester cette application dans le portail Azure. Vous devez alors être automatiquement connecté à l’instance de xMatters OnDemand pour laquelle vous avez configuré l’authentification unique.

* Vous pouvez utiliser Mes applications de Microsoft. Le fait de cliquer sur la vignette xMatters OnDemand dans Mes applications doit vous connecter automatiquement à l’application xMatters OnDemand pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré xMatters OnDemand, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
