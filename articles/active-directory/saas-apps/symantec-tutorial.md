---
title: 'Tutoriel : Intégration de l’authentification unique Azure AD à Symantec Web Security Service (WSS)'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Symantec Web Security Service (WSS).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/28/2021
ms.author: jeedes
ms.openlocfilehash: 90abed6597f123b291a5af281f109f07df990be5
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131471209"
---
# <a name="tutorial-azure-ad-sso-integration-with-symantec-web-security-service-wss"></a>Tutoriel : Intégration de l’authentification unique Azure AD à Symantec Web Security Service (WSS)

Dans ce didacticiel, vous allez apprendre à intégrer votre compte Symantec Web Security Service (WSS) à votre compte Azure Active Directory (Azure AD) afin que WSS puisse authentifier un utilisateur final approvisionné dans Azure AD à l’aide de l’authentification SAML et appliquer les règles de stratégie au niveau de l’utilisateur ou du groupe.

L’intégration de Symantec Web Security Service (WSS) à Azure AD vous fait bénéficier des avantages suivants :

* Gérer tous les groupes et utilisateurs finaux utilisés par votre compte WSS à partir de votre portail Azure AD.

* Autoriser les utilisateurs finaux à s’authentifier sur WSS à l’aide de leurs informations d’identification Azure AD.

* Activer l’application des règles de stratégie au niveau de l’utilisateur et du groupe définies dans votre compte WSS.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement à Symantec Web Security Service (WSS) pour lequel l’authentification unique est activée

> [!NOTE]
> Cette intégration peut également être utilisée à partir de l’environnement cloud US Government Azure AD. Cette application est disponible dans la Galerie d’applications cloud US Government Azure AD et peut être configurée de la même façon que dans le cloud public.

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Symantec Web Security Service (WSS) prend en charge l’authentification unique lancée par le **fournisseur d’identité**.

> [!NOTE]
> L’identificateur de cette application étant une valeur de chaîne fixe, une seule instance peut être configurée dans un locataire.

## <a name="add-symantec-web-security-service-wss-from-the-gallery"></a>Ajout de Symantec Web Security Service (WSS) à partir de la galerie

Pour configurer l’intégration de Symantec Web Security Service (WSS) à Azure AD, vous devez ajouter Symantec Web Security Service (WSS) à partir de la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Symantec Web Security Service (WSS)** dans la zone de recherche.
1. Sélectionnez **Symantec Web Security Service (WSS)** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-symantec-web-security-service-wss"></a>Configurer et tester l’authentification unique Azure AD pour Symantec Web Security Service (WSS)

Configurez et testez l’authentification unique Azure AD avec Symantec Web Security Service (WSS) pour un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur associé dans Symantec Web Security Service (WSS).

Pour configurer et tester l’authentification unique Azure AD avec Symantec Web Security Service (WSS), effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Symantec Web Security Service (WSS)](#configure-symantec-web-security-service-wss-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test Symantec Web Security Service (WSS)](#create-symantec-web-security-service-wss-test-user)** pour avoir dans Symantec Web Security Service (WSS) un équivalent de B.Simon lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, dans la page d’intégration de l’application **Symantec Web Security Service (WSS)** , recherchez la section **Gérer**, puis sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la boîte de dialogue **Configuration SAML de base**, effectuez les étapes suivantes :

    a. Dans la zone de texte **Identificateur**, tapez une l’URL : `https://saml.threatpulse.net:8443/saml/saml_realm`

    b. Dans la zone **URL de réponse**, tapez l’URL : `https://saml.threatpulse.net:8443/saml/saml_realm/bcsamlpost`

    > [!NOTE]
    > Contactez l’[équipe de support technique de Symantec Web Security Service (WSS)](https://www.symantec.com/contact-us) si les valeurs **Identificateur** et **URL de réponse** ne fonctionnent pas pour une raison quelconque. Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

5. Sur la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le fichier **XML de métadonnées de fédération** en fonction des options définies selon vos besoins, puis enregistrez-le sur votre ordinateur.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Symantec Web Security Service (WSS).

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Symantec Web Security Service (WSS)**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-symantec-web-security-service-wss-sso"></a>Configurer l’authentification unique Symantec Web Security Service (WSS)

Pour configurer l’authentification unique côté Symantec Web Security Service (WSS), consultez la documentation en ligne de WSS. Le **XML de métadonnées de fédération** devra être importé dans le portail WSS. Contactez l’[équipe de support Symantec Web Security Service (WSS)](https://www.symantec.com/contact-us) si vous avez besoin d’aide pour configurer le portail WSS.

### <a name="create-symantec-web-security-service-wss-test-user"></a>Créer un utilisateur de test Symantec Web Security Service (WSS)

Dans cette section, vous créez un utilisateur appelé Britta Simon dans Symantec Web Security Service (WSS). Le nom d’utilisateur final correspondante peut être créé manuellement dans le portail WSS ou vous pouvez attendre que les utilisateurs/groupes approvisionnés dans Azure AD se synchronisent dans le portail WSS après quelques minutes (environ 15 minutes). Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique. L’adresse IP publique de l’ordinateur de l’utilisateur final, qui sera utilisée pour parcourir les sites web, doit également être configurée dans le portail Symantec Web Security Service (WSS).

> [!NOTE]
> [Cliquez ici](https://www.bing.com/search?q=my+ip+address&qs=AS&pq=my+ip+a&sc=8-7&cvid=29A720C95C78488CA3F9A6BA0B3F98C5&FORM=QBLH&sp=1) pour obtenir l’adresse IP publique de votre ordinateur.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes.

* Cliquez sur Tester cette application dans le portail Azure. Vous êtes alors automatiquement connecté à l’instance de Symantec Web Security Service (WSS) pour laquelle vous avez configuré l’authentification unique.

* Vous pouvez utiliser Mes applications de Microsoft. Quand vous cliquez sur la vignette Symantec Web Security Service (WSS) dans Mes applications, vous devez être connecté automatiquement à l’instance de Symantec Web Security Service (WSS) pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré Symantec Web Security Service (WSS), vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).