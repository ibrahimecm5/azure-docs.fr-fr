---
title: 'Tutoriel : Intégration de l’authentification unique Azure Active Directory à AWS ClientVPN | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et AWS ClientVPN.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/17/2021
ms.author: jeedes
ms.openlocfilehash: 954142d1bfa83bca606f22c1c6b8fb5013319a98
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132317973"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-aws-clientvpn"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory à AWS ClientVPN

Dans ce tutoriel, vous allez apprendre à intégrer AWS ClientVPN à Azure Active Directory (Azure AD). Quand vous intégrez AWS ClientVPN à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à AWS ClientVPN
* Permettre à vos utilisateurs de se connecter automatiquement à AWS ClientVPN avec leur compte Azure AD
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement AWS ClientVPN pour lequel l’authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* AWS ClientVPN prend en charge l’authentification unique lancée par le **fournisseur de services**.

* AWS ClientVPN prend en charge l’attribution d’utilisateurs **juste-à-temps**.

> [!NOTE]
> L’identificateur de cette application étant une valeur de chaîne fixe, une seule instance peut être configurée dans un locataire.

## <a name="add-aws-clientvpn-from-the-gallery"></a>Ajout d’AWS ClientVPN à partir de la galerie

Pour configurer l’intégration d’AWS ClientVPN à Azure AD, vous devez ajouter AWS ClientVPN à partir de la galerie à votre liste d’applications SaaS managées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **AWS ClientVPN** dans la zone de recherche.
1. Sélectionnez **AWS ClientVPN** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-aws-clientvpn"></a>Configurer et tester l’authentification unique Azure AD pour AWS ClientVPN

Configurez et testez l’authentification unique Azure AD avec AWS ClientVPN à l’aide d’un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur AWS ClientVPN associé.

Pour configurer et tester l’authentification unique Azure AD avec AWS ClientVPN, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique AWS ClientVPN](#configure-aws-clientvpn-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test AWS ClientVPN](#create-aws-clientvpn-test-user)** pour avoir un équivalent de B.Simon dans AWS ClientVPN associé à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **AWS ClientVPN**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    a. Dans la zone de texte **URL de connexion**, saisissez une URL au format suivant : `https://<LOCALHOST>`

    b. Dans la zone de texte **URL de réponse** , tapez une URL en utilisant un des modèles suivants :

    | URL de réponse |
    |------------|
    | `http://<LOCALHOST>` |
    | `https://self-service.clientvpn.amazonaws.com/api/auth/sso/saml` |
    |

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles.  Mettez à jour ces valeurs avec l’URL de réponse et l’URL de connexion réelles.  L’URL de connexion et l’URL de réponse peuvent avoir la même valeur (http://127.0.0.1:35001).  Pour plus d’informations, consultez la [documentation AWS ClientVPN](https://docs.aws.amazon.com/vpn/latest/clientvpn-admin/client-authentication.html#ad).   Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure. Contactez l’[équipe de support AWS ClientVPN](https://aws.amazon.com/contact-us/) pour tout problème de configuration. 

1. Dans le service Azure Active Directory, accédez à **Inscriptions d’applications**, puis sélectionnez **Toutes les applications**.

1. Tapez **AWS ClientVPN** dans la zone de recherche et sélectionnez **AWS ClientVPN** dans le volet de recherche.

1. Cliquez sur **Manifeste** ; conservez l’URL de réponse sous la forme **http** au lieu de **https** pour que l’intégration fonctionne. Cliquez sur **Enregistrer**.

    ![Page de manifeste](./media/aws-clientvpn-tutorial/reply-url.png)

1. L’application AWS ClientVPN s’attend à recevoir les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à la configuration d’attributs du jeton SAML. La capture d’écran suivante montre la liste des attributs par défaut.

    ![image](common/default-attributes.png)

1. En plus de ce qui précède, l’application AWS ClientVPN s’attend à ce que quelques attributs supplémentaires (présentés ci-dessous) soient repassés dans la réponse SAML. Ces attributs sont également préremplis, mais vous pouvez les examiner pour voir s’ils répondent à vos besoins.
    
    | Nom |  Attribut source|
    | -------------- | --------- |
    | memberOf | user.groups |

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **XML de métadonnées de fédération** et sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

1. Dans la section **Configurer AWS ClientVPN**, copiez l’URL ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à AWS ClientVPN.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **AWS ClientVPN**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-aws-clientvpn-sso"></a>Configurer l’authentification unique AWS ClientVPN

Suivez ce [lien](https://docs.aws.amazon.com/vpn/latest/clientvpn-admin/client-authentication.html#federated-authentication) pour obtenir des instructions afin de configurer l’authentification unique côté AWS ClientVPN.

### <a name="create-aws-clientvpn-test-user"></a>Créer un utilisateur de test AWS ClientVPN

Dans cette section, un utilisateur appelé Britta Simon est créé dans AWS ClientVPN. AWS ClientVPN prend en charge l’attribution d’utilisateurs juste-à-temps, qui est activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas encore d’utilisateur dans AWS ClientVPN, il en est créé un après l’authentification.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion AWS ClientVPN, à partir de laquelle vous pouvez lancer le flux de connexion. 

* Accédez directement à l’URL de connexion AWS ClientVPN pour lancer le flux de connexion.

* Vous pouvez utiliser Mes applications de Microsoft. Le fait de cliquer sur la vignette AWS ClientVPN dans Mes applications vous redirige vers l’URL de connexion AWS ClientVPN. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).


## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré AWS ClientVPN, vous pouvez appliquer le contrôle de session, qui protège en temps réel contre l’exfiltration et l’infiltration des données sensibles de votre organisation. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
