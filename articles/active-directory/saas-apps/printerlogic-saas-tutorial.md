---
title: 'Tutoriel : Intégration de l’authentification unique (SSO) Azure Active Directory à PrinterLogic | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et PrinterLogic.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/31/2021
ms.author: jeedes
ms.openlocfilehash: ca4d3b6d19d429e4838f1c003ffdce8c283ef44f
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132299818"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-printerlogic"></a>Tutoriel : Intégration de l’authentification unique (SSO) Azure Active Directory à PrinterLogic

Ce tutoriel explique comment intégrer PrinterLogic à Azure AD (Azure Active Directory). Quand vous intégrez PrinterLogic à Azure AD, vous pouvez :

* Contrôler, dans Azure AD, qui a accès à PrinterLogic.
* Permettre à vos utilisateurs de se connecter automatiquement à PrinterLogic avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement PrinterLogic pour lequel l’authentification unique (SSO) est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* PrinterLogic prend en charge l’authentification SSO lancée par **le fournisseur de services (SP) et le fournisseur d’identité (IdP)** .
* PrinterLogic prend en charge le provisionnement d’utilisateurs **juste-à-temps**.

* PrinterLogic prend en charge le [provisionnement d’utilisateurs automatisé](printer-logic-saas-provisioning-tutorial.md).

## <a name="add-printerlogic-from-the-gallery"></a>Ajouter PrinterLogic à partir de la galerie

Pour configurer l’intégration de PrinterLogic à Azure AD, vous devez ajouter PrinterLogic à partir de la galerie dans votre liste d’applications SaaS managées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **PrinterLogic** dans la zone de recherche.
1. Sélectionnez **PrinterLogic** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-printerlogic"></a>Configurer et tester l’authentification unique Azure AD pour PrinterLogic

Configurez et testez l’authentification unique Azure AD avec PrinterLogic à l’aide d’un utilisateur de test nommé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur PrinterLogic associé.

Pour configurer et tester l’authentification unique Azure AD auprès de PrinterLogic, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
   1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
   1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique PrinterLogic](#configure-printerlogic-sso)** – pour configurer les paramètres d’authentification unique côté application.
   1. **[Créer un utilisateur de test PrinterLogic](#create-printerlogic-test-user)** – pour avoir un équivalent de B.Simon dans PrinterLogic qui soit lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **PrinterLogic**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, si vous souhaitez configurer l’application en mode Initié par le **fournisseur d’identité**, entrez les valeurs pour les champs suivants :

   a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://gw.app.printercloud.com/<my_instance>/authn/idp/azuread/saml2/metadata`

   b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://gw.app.printercloud.com/<my_instance>/authn/idp/azuread/saml2/acs`

1. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

   Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://www.<my_instance>printercloud.com`.

   > [!NOTE]
   > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL de réponse et l’URL de connexion réels. Contactez [l’équipe du support technique client PrinterLogic](mailto:support@printerlogic.com) pour obtenir ces valeurs. Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. L’application PrinterLogic attend les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à votre configuration des attributs de jeton SAML. La capture d’écran suivante montre la liste des attributs par défaut.

   ![image](common/edit-attribute.png)

1. En plus de ce qui précède, l’application PrinterLogic s’attend à ce que quelques attributs supplémentaires (présentés ci-dessous) soient repassés dans la réponse SAML. Ces attributs sont également préremplis, mais vous pouvez les examiner pour voir s’ils répondent à vos besoins.

   | Nom | Attribut source   |
   | ---- | ------------------ |
   | Role | user.assignedroles |

   > [!NOTE]
   > Cliquez [ici](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui) pour savoir comment configurer un rôle dans Azure AD.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **Certificat (Base64)** , puis sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

   ![Lien Téléchargement de certificat](common/certificatebase64.png)

1. Dans la section **Configurer PrinterLogic**, copiez l’URL ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à PrinterLogic.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **PrinterLogic**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous avez configuré les rôles comme indiqué ci-dessus, vous pouvez en sélectionner un dans la liste déroulante **Sélectionner un rôle**.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-printerlogic-sso"></a>Configurer l’authentification unique PrinterLogic

Pour configurer l’authentification unique côté **PrinterLogic**, vous devez envoyer le **certificat (Base64)** téléchargé et les URL appropriées copiées à partir du portail Azure à [l’équipe du support technique PrinterLogic](mailto:support@printerlogic.com). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

### <a name="create-printerlogic-test-user"></a>Créer un utilisateur de test PrinterLogic

Dans cette section, un utilisateur appelé Britta Simon est créé dans PrinterLogic. PrinterLogic prend en charge le provisionnement d’utilisateurs juste-à-temps, qui est activé par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas encore d’utilisateur dans PrinterLogic, un utilisateur est créé après l’authentification.

PrinterLogic prend aussi en charge le provisionnement automatique d’utilisateurs ; vous trouverez plus d’informations [ici](./printer-logic-saas-provisioning-tutorial.md) sur la manière de configurer ce dernier.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes.

#### <a name="sp-initiated"></a>Lancée par le fournisseur de services :

* Cliquez sur **Tester cette application** dans le portail Azure. Une redirection est effectuée vers l’URL de connexion à PrinterLogic, où vous pouvez lancer le flux de connexion.

* Accédez directement à l’URL de connexion à PrinterLogic, puis lancez le flux de connexion à partir de cet emplacement.

#### <a name="idp-initiated"></a>Lancée par le fournisseur d’identité :

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors connecté automatiquement à l’instance de PrinterLogic pour laquelle vous avez configuré l’authentification SSO.

* Vous pouvez aussi utiliser Mes applications de Microsoft pour tester l’application dans n’importe quel mode. Si, quand vous cliquez sur la vignette PrinterLogic dans Mes applications, le mode Fournisseur de services est configuré, vous êtes redirigé vers la page de connexion de l’application pour lancer le flux de connexion ; s’il s’agit du mode Fournisseur d’identité, vous êtes automatiquement connecté à l’instance de PrinterLogic pour laquelle vous avez configuré l’authentification SSO. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré PrinterLogic, vous pouvez appliquer le contrôle de session qui protège en temps réel contre l’exfiltration et l’infiltration des données sensibles de votre organisation. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
