---
title: 'Tutoriel : Intégration de l’authentification unique Azure Active Directory à Paylocity | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Paylocity.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/08/2021
ms.author: jeedes
ms.openlocfilehash: 83d8290555bfc13e987e019e62e635d11ca3e136
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132333995"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-paylocity"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory à Paylocity

Dans ce tutoriel, vous allez apprendre à intégrer Paylocity à Azure Active Directory (Azure AD). Quand vous intégrez Paylocity à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Paylocity.
* Permettre à vos utilisateurs de se connecter automatiquement à Paylocity avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Paylocity pour lequel l’authentification unique (SSO) est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* Paylocity prend en charge l’authentification unique lancée par **le fournisseur de services et le fournisseur d’identité**

## <a name="add-paylocity-from-the-gallery"></a>Ajouter Paylocity à partir de la galerie

Pour configurer l’intégration de Paylocity à Azure AD, vous devez ajouter Paylocity à partir de la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Paylocity** dans la zone de recherche.
1. Sélectionnez **Paylocity** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-paylocity"></a>Configurer et tester l’authentification unique Azure AD pour Paylocity

Configurez et testez l’authentification unique Azure AD avec Paylocity pour un utilisateur de test nommé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Paylocity associé.

Pour configurer et tester l’authentification unique Azure AD avec Paylocity, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    * **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    * **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Paylocity](#configure-paylocity-sso)** pour configurer les paramètres de l’authentification unique côté application.
    * **[Créer un utilisateur de test Paylocity](#create-paylocity-test-user)** pour avoir un équivalent de B.Simon dans Paylocity lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **Paylocity**, recherchez la section **Gérer**, puis sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, l’utilisateur n’a rien à faire, car l’application est déjà intégrée à Azure.

1. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    Dans la zone de texte **URL de connexion**, tapez l’URL : `https://access.paylocity.com/`

1. Cliquez sur **Enregistrer**.

1. Votre application Paylocity attend les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à la configuration des attributs du jeton SAML. La capture d’écran suivante montre la liste des attributs par défaut.

    ![image](common/default-attributes.png)

1. En plus de ce qui précède, l’application Paylocity s’attend à ce que quelques attributs supplémentaires soient repassés dans la réponse SAML. Ces attributs sont également préremplis, mais vous devez les mettre à jour avec les valeurs réelles.

    | Nom |  Attribut source|
    | ---------------| --------------- |
    | PartnerID | `P8000010` |
    | PaylocityUser | `user.mail`|
    | PaylocityEntity | < `PaylocityEntity` > |

    > [!NOTE]
    > PaylocityEntity est l’ID d’entreprise Paylocity.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **XML de métadonnées de fédération** et sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur l’**icône Modifier**.

    ![Capture d’écran montrant le « Certificat de signature SAML », avec l’action « Télécharger » sélectionnée pour « XML de métadonnées de fédération ».](./media/paylocity-tutorial/edit-samlassertion.png)

1. Sélectionnez **Option de signature** pour **Signer la réponse et l’assertion SAML**, puis cliquez sur **Enregistrer**.

    ![Modifier le certificat de signature SAML](./media/paylocity-tutorial/saml-assertion.png)

1. Dans la section **Configurer Paylocity**, copiez la ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Paylocity.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Paylocity**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-paylocity-sso"></a>Configurer l’authentification unique Paylocity

Pour configurer l’authentification unique côté **Paylocity** :

1. Téléchargez le **fichier XML des métadonnées de fédération**.
1. Dans Paylocity, accédez à **HR & Payroll** > **User Access** > **SSO Configuration**.
1. Sélectionnez **Add SSO Integration** sous **SSO Integrations**. Un nouveau tiroir s’ouvre.
1. Sélectionnez **Microsoft Azure** comme fournisseur d’authentification unique dans la liste déroulante.
1. Sélectionnez **Status** dans la liste déroulante.
1. Glissez-déplacez le fichier de métadonnées dans la zone de dépôt. Paylocity tente d’analyser l’émetteur, la redirection POST, les URL de liaison et le ou les certificats de sécurité.
1. Sélectionnez **Enregistrer** pour confirmer les modifications. L’intégration doit apparaître sous **SSO Integrations**.

### <a name="create-paylocity-test-user"></a>Créer un utilisateur de test Paylocity

Dans cette section, vous créez un utilisateur appelé B.Simon dans Paylocity. Collaborez avec l’[équipe du support technique Paylocity](mailto:service@paylocity.com) pour ajouter des utilisateurs dans la plateforme Paylocity. Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

#### <a name="sp-initiated"></a>Lancée par le fournisseur de services :

* Cliquez sur **Tester cette application** dans le portail Azure. Cette opération redirige vers l’URL d’authentification Paylocity, d’où vous pouvez lancer le processus de connexion.  

* Accédez directement à l’URL d’authentification Paylocity pour lancer le processus de connexion.

#### <a name="idp-initiated"></a>Lancée par le fournisseur d’identité :

* Sur le portail Azure, cliquez sur **Tester cette application**. Vous êtes alors automatiquement connecté à l’instance Paylocity pour laquelle vous avez configuré l’authentification unique. 

Vous pouvez aussi utiliser Mes applications de Microsoft pour tester l’application dans n’importe quel mode. Si, quand vous cliquez sur la vignette Paylocity dans Mes applications, le mode Fournisseur de services est configuré, vous êtes redirigé vers la page de connexion de l’application pour lancer le flux de connexion. S’il s’agit du mode Fournisseur d’identité, vous êtes automatiquement connecté à l’instance Paylocity pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez configuré Paylocity, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
