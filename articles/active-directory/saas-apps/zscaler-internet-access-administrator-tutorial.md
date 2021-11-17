---
title: "Tutoriel : Intégration d'Azure Active Directory à Zscaler Internet Access Administrator | Microsoft Docs"
description: Apprenez à configurer l'authentification unique entre Azure Active Directory et Zscaler Internet Access Administrator.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/25/2021
ms.author: jeedes
ms.openlocfilehash: 03ca5536ed373183f48769158e7cdf3686dc2722
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132302385"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-internet-access-administrator"></a>Tutoriel : Intégration d'Azure Active Directory à Zscaler Internet Access Administrator

Dans ce tutoriel, vous allez découvrir comment intégrer Zscaler Internet Access Administrator avec Azure Active Directory (Azure AD). Quand vous intégrez Zscaler Internet Access Administrator avec Azure AD, vous pouvez :

- Contrôler, dans Azure AD, qui a accès à Zscaler Internet Access Administrator.
- Permettre à vos utilisateurs de se connecter automatiquement à Zscaler Internet Access Administrator avec leur compte Azure AD.
- Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

- Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
- Un abonnement Zscaler Internet Access Administrator pour lequel l’authentification SSO (authentification unique) est activée.

> [!NOTE]
> Cette intégration peut également être utilisée à partir de l’environnement cloud US Government Azure AD. Cette application est disponible dans la Galerie d’applications cloud US Government Azure AD et peut être configurée de la même façon que dans le cloud public.

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

- Zscaler Internet Access Administrator prend en charge l’authentification SSO lancée par le fournisseur de services (**IdP**).

## <a name="add-zscaler-internet-access-administrator-from-the-gallery"></a>Ajouter Zscaler Internet Access Administrator à partir de la galerie

Pour configurer l'intégration de Zscaler Internet Access Administrator à Azure AD, vous devez ajouter Zscaler Internet Access Administrator à votre liste d'applications SaaS gérées à partir de la galerie.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, dans la zone de recherche, tapez **Zscaler Internet Access Administrator**.
1. Sélectionnez **Zscaler Internet Access Administrator** dans le panneau Résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-zscaler-internet-access-administrator"></a>Configurer et tester l’authentification SSO Azure AD pour Zscaler Internet Access Administrator

Configurez et testez l’authentification SSO Azure AD avec Zscaler Internet Access Administrator à l’aide d’une utilisatrice de test appelée **B.Simon**. Pour que l’authentification SSO fonctionne, vous devez établir une relation entre un utilisateur Azure AD et l’utilisateur associé dans Zscaler Internet Access Administrator.

Pour configurer et tester l’authentification SSO Azure AD avec Zscaler Internet Access Administrator, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
   1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
   1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
2. **[Configurer l’authentification SSO Zscaler Internet Access Administrator](#configure-zscaler-internet-access-administrator-sso)** pour configurer les paramètres d’authentification unique côté application.
   1. **[Créer un utilisateur de test Zscaler Internet Access Administrator](#create-zscaler-internet-access-administrator-test-user)** pour avoir dans Zscaler Internet Access Administrator un équivalent de Britta Simon lié à la représentation Azure AD associée.
3. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, dans la page d’intégration de l’application **Zscaler Internet Access Administrator**, recherchez la section **Gérer**, puis sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon pour **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, entrez les valeurs pour les champs suivants :

   a. Dans la zone de texte **Identificateur**, tapez l’une des URL suivantes selon vos besoins :

   | Identificateur                       |
   | -------------------------------- |
   | `https://admin.zscaler.net`      |
   | `https://admin.zscalerone.net`   |
   | `https://admin.zscalertwo.net`   |
   | `https://admin.zscalerthree.net` |
   | `https://admin.zscloud.net`      |
   | `https://admin.zscalerbeta.net`  |

   b. Dans la zone de texte **URL de réponse**, tapez l’une des URL suivantes selon vos besoins :

   | URL de réponse                                    |
   | -------------------------------------------- |
   | `https://admin.zscaler.net/adminsso.do`      |
   | `https://admin.zscalerone.net/adminsso.do`   |
   | `https://admin.zscalertwo.net/adminsso.do`   |
   | `https://admin.zscalerthree.net/adminsso.do` |
   | `https://admin.zscloud.net/adminsso.do`      |
   | `https://admin.zscalerbeta.net/adminsso.do`  |

1. L'application Zscaler Internet Access Administrator attend les assertions SAML dans un format spécifique. Configurez les revendications suivantes pour cette application. Vous pouvez gérer les valeurs de ces attributs à partir de la section **Attributs utilisateur et revendications** dans la page d’intégration des applications. Sur la page **Configurer l'authentification unique avec SAML**, cliquez sur le bouton **Modifier** pour ouvrir la boîte de dialogue **Attributs utilisateur et revendications**.

   ![Le lien Attribut](./media/zscaler-internet-access-administrator-tutorial/attributes.png)

1. Dans la section **Revendications des utilisateurs** de la boîte de dialogue **Attributs utilisateur**, configurez le jeton SAML comme sur l’image ci-dessus et procédez comme suit :

   | Name | Attribut source   |
   | ---- | ------------------ |
   | Role | user.assignedroles |

   a. Cliquez sur le bouton **Ajouter une nouvelle revendication** pour ouvrir la boîte de dialogue **Gérer les revendications des utilisateurs**.

   b. Dans la liste **Attribut de la source**, sélectionnez la valeur de l’attribut.

   c. Cliquez sur **OK**.

   d. Cliquez sur **Enregistrer**.

   > [!NOTE]
   > Cliquez [ici](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui) pour savoir comment configurer un rôle dans Azure AD.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (Base64)** en fonction des options définies par rapport à vos besoins, puis enregistrez-le sur votre ordinateur.

   ![Lien Téléchargement de certificat](common/certificatebase64.png)

1. Dans la section **Configurer Zscaler Internet Access Administrator**, copiez la ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez permettre à B.Simon d’utiliser l’authentification unique Azure en lui octroyant l’accès à Zscaler Internet Access Administrator.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Zscaler Internet Access Administrator**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous avez configuré les rôles comme indiqué ci-dessus, vous pouvez en sélectionner un dans la liste déroulante **Sélectionner un rôle**.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-zscaler-internet-access-administrator-sso"></a>Configurer l’authentification SSO pour Zscaler Internet Access Administrator

1. Dans une autre fenêtre de navigateur web, connectez-vous à votre UI d’administrateur Zscaler Internet Access.

2. Accédez à **Administration > Administrator Management**, effectuez les étapes suivantes, puis cliquez sur Save :

   ![Capture d’écran montrant Administrator Management avec les options permettant d’activer l’authentification SAML, de charger un certificat SSL et de spécifier un émetteur.](./media/zscaler-internet-access-administrator-tutorial/management.png "Administration")

   a. Cliquez sur **Enable SAML Authentication**.

   b. Cliquez sur **Charger** pour charger le certificat de signature SAML Azure que vous avez téléchargé depuis le portail Azure dans le **Certificat SSL public**.

   c. Si vous le souhaitez, pour renforcer la sécurité, ajoutez les détails de l’émetteur (dans le champ **Issuer**) afin de vérifier l’émetteur de la réponse SAML.

3. Dans l’UI de l’administrateur, effectuez les étapes suivantes :

   ![Capture d’écran montrant l’IU d’administration où vous pouvez effectuer les étapes.](./media/zscaler-internet-access-administrator-tutorial/activation.png)

   a. Passez la souris sur le menu **Activation** en bas à gauche.

   b. Cliquez sur **Activer**.

### <a name="create-zscaler-internet-access-administrator-test-user"></a>Créer un utilisateur de test Zscaler Internet Access Administrator

L'objectif de cette section est de créer un utilisateur appelé Britta Simon dans Zscaler Internet Access Administrator. Zscaler Internet Access ne prend pas en charge le provisionnement juste-à-temps pour l’authentification unique des administrateurs. Vous devez créer manuellement un compte d’administrateur.
Pour savoir comment créer un compte d’administrateur, reportez-vous à la documentation de Zscaler :

https://help.zscaler.com/zia/adding-admins

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes.

- Cliquez sur Tester cette application dans le portail Azure. Vous êtes alors connecté automatiquement à l’instance de Zscaler Internet Access Administrator pour laquelle vous avez configuré l’authentification SSO

- Vous pouvez utiliser Mes applications de Microsoft. Quand vous cliquez sur la vignette Zscaler Internet Access Administrator dans Mes applications, vous devez être automatiquement connecté à l’instance de Zscaler Internet Access Administrator pour laquelle vous avez configuré l’authentification SSO. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez configuré Zscaler Internet Access Administrator, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
