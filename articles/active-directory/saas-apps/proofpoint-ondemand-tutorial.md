---
title: 'Tutoriel : Intégration de l’authentification unique Azure AD à Proofpoint on Demand'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Proofpoint on Demand.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/09/2021
ms.author: jeedes
ms.openlocfilehash: 38eb3525245e081d9a3c17ed75104becbffa1e78
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132310725"
---
# <a name="tutorial-azure-ad-sso-integration-with-proofpoint-on-demand"></a>Tutoriel : Intégration de l’authentification unique Azure AD à Proofpoint on Demand

Dans ce tutoriel, vous allez découvrir comment intégrer Proofpoint on Demand avec Azure Active Directory (Azure AD). Quand vous intégrez Proofpoint on Demand avec Azure AD, vous pouvez :

* Contrôler, dans Azure AD, qui a accès à Proofpoint on Demand.
* Permettre à vos utilisateurs de se connecter automatiquement à Proofpoint on Demand avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Proofpoint on Demand pour lequel l’authentification SSO (authentification unique) est activée.

> [!NOTE]
> Si vous utilisez une authentification multifacteur ou sans mot de passe avec Azure AD, désactivez la valeur AuthnContext dans la demande SAML. Autrement, Azure AD lève l’erreur en cas d’incompatibilité d’ AuthnContext et ne renvoie pas le jeton à l’application.

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Proofpoint on Demand prend en charge l’authentification unique lancée par le **fournisseur de services**.

## <a name="add-proofpoint-on-demand-from-the-gallery"></a>Ajouter Proofpoint on Demand à partir de la galerie

Pour configurer l’intégration de Proofpoint on Demand à Azure AD, vous devez ajouter Proofpoint on Demand à partir de la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, dans la zone de recherche, tapez **Proofpoint on Demand**.
1. Sélectionnez **Proofpoint on Demand** dans le panneau Résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-proofpoint-on-demand"></a>Configurer et tester l’authentification SSO Azure AD pour Proofpoint on Demand

Configurez et testez l’authentification SSO Azure AD avec Proofpoint on Demand à l’aide d’une utilisatrice de test appelée **B.Simon**. Pour que l’authentification SSO fonctionne, vous devez établir une relation entre un utilisateur Azure AD et l’utilisateur associé dans Proofpoint on Demand.

Pour configurer et tester l’authentification SSO Azure AD avec Proofpoint on Demand, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification SSO pour Proofpoint on Demand](#configure-proofpoint-on-demand-sso)** afin de configurer les paramètres d’authentification unique côté application.
    1. **[Créer une utilisatrice de test pour Proofpoint on Demand](#create-proofpoint-on-demand-test-user)** afin de disposer dans Proofpoint on Demand d’un équivalent de B.Simon lié à la représentation Azure AD de l’utilisatrice.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, dans la page d’intégration de l’application **Proofpoint on Demand**, recherchez la section **Gérer**, puis sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    a. Dans la zone de texte **Identificateur**, tapez une URL en utilisant le format suivant : `https://<hostname>.pphosted.com/ppssamlsp`

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://<hostname>.pphosted.com:portnumber/v1/samlauth/samlconsumer`

    c. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<hostname>.pphosted.com/ppssamlsp_hostname`.

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL d’authentification et l’URL de réponse réels. Pour obtenir ces valeurs, contactez l’[équipe de support technique Proofpoint on Demand](https://www.proofpoint.com/us/support-services). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

5. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (Base64)** en fonction des options définies par rapport à vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

6. Dans la section **Configurer Proofpoint on Demand**, copiez la ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez permettre à B.Simon d’utiliser l’authentification unique Azure en lui octroyant l’accès à Proofpoint on Demand.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Proofpoint on Demand**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-proofpoint-on-demand-sso"></a>Configurer l’authentification SSO pour Proofpoint on Demand

Pour configurer l’authentification unique côté **Proofpoint on Demand**, vous devez envoyer le **Certificat (Base64)** téléchargé et les URL correspondantes copiées depuis le portail Azure à [l’équipe du support technique Proofpoint on Demand](https://www.proofpoint.com/us/support-services). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

### <a name="create-proofpoint-on-demand-test-user"></a>Créer un utilisateur de test Proofpoint on Demand

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans Proofpoint on Demand. Collaborez avec l’[équipe de support aux clients Proofpoint on Demand](https://www.proofpoint.com/us/support-services) pour ajouter des utilisateurs sur la plateforme Proofpoint on Demand.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Cette opération effectue une redirection vers l’URL de connexion à Proofpoint on Demand, où vous pouvez lancer le flux de connexion. 

* Accédez directement à l’URL de connexion à Proofpoint on Demand, puis lancez le flux de connexion à partir de cet emplacement.

* Vous pouvez utiliser Mes applications de Microsoft. Quand vous cliquez sur la vignette Proofpoint on Demand dans Mes applications, vous êtes redirigé vers l’URL d’authentification Proofpoint on Demand. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez configuré Proofpoint on Demand, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
