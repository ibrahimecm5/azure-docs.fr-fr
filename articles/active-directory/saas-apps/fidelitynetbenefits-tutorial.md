---
title: 'Tutoriel : Intégration de l’authentification unique Azure AD à Fidelity NetBenefits'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Fidelity NetBenefits.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/25/2021
ms.author: jeedes
ms.openlocfilehash: 304bc6d4d3100478ba4fa542c76cd1d2e4ef4aca
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132285613"
---
# <a name="tutorial-azure-ad-sso-integration-with-fidelity-netbenefits"></a>Tutoriel : Intégration de l’authentification unique Azure AD à Fidelity NetBenefits

Dans ce tutoriel, vous allez apprendre à intégrer Fidelity NetBenefits à Azure Active Directory (Azure AD). Quand vous intégrez Fidelity NetBenefits à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Fidelity NetBenefits.
* Permettre à vos utilisateurs de se connecter automatiquement à Fidelity NetBenefits avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Fidelity NetBenefits pour lequel l’authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Fidelity NetBenefits prend en charge l’authentification unique lancée par le **fournisseur d’identité**.

* Fidelity NetBenefits prend en charge l’attribution d’utilisateurs **juste-à-temps**.

> [!NOTE]
> L’identificateur de cette application étant une valeur de chaîne fixe, une seule instance peut être configurée dans un locataire.

## <a name="add-fidelity-netbenefits-from-the-gallery"></a>Ajouter Fidelity NetBenefits à partir de la galerie

Pour configurer l’intégration de Fidelity NetBenefits à Azure AD, vous devez ajouter Fidelity NetBenefits à partir de la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, saisissez **Fidelity NetBenefits** dans la zone de recherche.
1. Sélectionnez **Fidelity NetBenefits** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-fidelity-netbenefits"></a>Configurer et tester l’authentification unique Azure AD pour Fidelity NetBenefits

Configurez et testez l’authentification unique Azure AD avec Fidelity NetBenefits à l’aide d’un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Fidelity NetBenefits associé.

Pour configurer et tester l’authentification unique Azure AD avec Fidelity NetBenefits, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Fidelity NetBenefits](#configure-fidelity-netbenefits-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test Fidelity NetBenefits](#create-fidelity-netbenefits-test-user)** pour avoir un équivalent de B.Simon dans Fidelity NetBenefits lié à la représentation Azure AD associée.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **Fidelity NetBenefits**, recherchez la section **Gérer**, puis sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    a. Dans la zone de texte **Identificateur**, saisissez l’une des valeurs suivantes :

    Pour l’environnement de test : `urn:sp:fidelity:geninbndnbparts20:uat:xq1`

    Pour l’environnement de production : `urn:sp:fidelity:geninbndnbparts20`

    b. Dans la zone de texte **URL de réponse**, entrez une URL fournie par Fidelity lors de l’implémentation ou contactez votre responsable de service client Fidelity assigné.

5. Votre application Fidelity NetBenefits attend les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à votre configuration des attributs du jeton SAML. La capture d’écran suivante montre la liste des attributs par défaut, où **nameidentifier** est mappé avec **user.userprincipalname**. Comme l’application Fidelity NetBenefits attend le mappage de **nameidentifier** avec **employeeid**, ou de toute autre revendication applicable à votre organisation en tant que **nameidentifier**, vous devez modifier le mappage d’attributs en cliquant sur l’icône **Modifier**, ce qui vous permet de changer le mappage d’attributs.

    ![image](common/edit-attribute.png)

    >[!Note]
    >Fidelity NetBenefits prend en charge la fédération statique et dynamique. Par statique, nous entendons que l’application n’utilisera pas l’attribution d’utilisateurs juste-à-temps d’après SAML. Par dynamique, nous entendons qu’elle prendra en charge l’attribution d’utilisateurs juste-à-temps. Pour l’utiliser, les clients doivent ajouter d’autres revendications dans Azure AD comme la date de naissance de l’utilisateur, etc. Ces détails sont fournis par votre **responsable de service client Fidelity** assigné et doivent activer la fédération dynamique pour votre instance.

6. Sur la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le fichier **XML de métadonnées de fédération** en fonction des options définies selon vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

7. Dans la section **Configurer Fidelity NetBenefits**, copiez la ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Fidelity NetBenefits.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Fidelity NetBenefits**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-fidelity-netbenefits-sso"></a>Configurer l’authentification unique Fidelity NetBenefits

Pour configurer l’authentification unique côté **Fidelity NetBenefits**, vous devez envoyer le **XML des métadonnées de fédération** téléchargé et les URL appropriées copiées à partir du portail Azure à l’[équipe du support technique de Fidelity NetBenefits](mailto:SSOMaintenance@fmr.com). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

### <a name="create-fidelity-netbenefits-test-user"></a>Créer l’utilisateur de test Fidelity NetBenefits

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans Fidelity NetBenefits. Dans le cadre d’une fédération statique, veuillez collaborer avec votre **responsable de service client Fidelity** assigné pour créer des utilisateurs dans la plateforme Fidelity NetBenefits. Ces utilisateurs doivent être créés et activés avant d’utiliser l’authentification unique.

Dans le cadre d’une fédération dynamique, les utilisateurs sont créés à l’aide de l’attribution juste-à-temps. Pour l’utiliser, les clients doivent ajouter d’autres revendications dans Azure AD comme la date de naissance de l’utilisateur, etc. Ces détails sont fournis par votre **responsable de service client Fidelity** assigné et doivent activer la fédération dynamique pour votre instance.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes.

* Cliquez sur Tester cette application dans le portail Azure. Vous êtes alors automatiquement connecté automatiquement à l’instance de Fidelity NetBenefits pour laquelle vous avez configuré l’authentification unique.

* Vous pouvez utiliser Mes applications de Microsoft. Quand vous cliquez sur la vignette Fidelity NetBenefits dans Mes applications, vous êtes automatiquement connecté à l’instance de Fidelity NetBenefits pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré Fidelity NetBenefits, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
