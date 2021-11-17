---
title: 'Tutoriel : Intégration de l’authentification unique Azure AD à BenSelect'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et BenSelect.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/27/2021
ms.author: jeedes
ms.openlocfilehash: db5bf1678d64bb90466ba644c6ad70093bcc09e4
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132349202"
---
# <a name="tutorial-azure-ad-sso-integration-with-benselect"></a>Tutoriel : Intégration de l’authentification unique Azure AD à BenSelect

Dans ce tutoriel, vous allez apprendre à intégrer BenSelect à Azure Active Directory (Azure AD). Quand vous intégrez BenSelect à Azure AD, vous pouvez :

* Contrôler qui a accès à BenSelect
* Autoriser les utilisateurs à se connecter automatiquement à BenSelect avec leur compte Azure AD
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Abonnement BenSelect pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* BenSelect prend en charge l’authentification unique lancée par le **fournisseur d’identité**.

> [!NOTE]
> L’identificateur de cette application étant une valeur de chaîne fixe, une seule instance peut être configurée dans un locataire.

## <a name="add-benselect-from-the-gallery"></a>Ajouter BenSelect à partir de la galerie

Pour configurer l’intégration de BenSelect à Azure AD, vous devez ajouter BenSelect à partir de la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **BenSelect** dans la zone de recherche.
1. Sélectionnez **BenSelect** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-benselect"></a>Configurer et tester l’authentification unique Azure AD pour BenSelect

Configurez et testez l’authentification unique Azure AD avec BenSelect pour un utilisateur de test nommé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur BenSelect associé.

Pour configurer et tester l’authentification unique Azure AD avec BenSelect, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique BenSelect](#configure-benselect-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test BenSelect](#create-benselect-test-user)** pour avoir un équivalent de B.Simon dans BenSelect, lié à la représentation Azure AD associée.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Sur le portail Azure, dans la page d’intégration de l’application **BenSelect**, recherchez la section **Gérer**, puis sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, effectuez l’étape suivante :

    Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://www.benselect.com/enroll/login.aspx?Path=<tenant name>`

    > [!NOTE]
    > Cette valeur n’est pas la valeur réelle. Mettez à jour la valeur avec l’URL de réponse réelle. Pour obtenir la valeur, contactez [l’équipe de support de BenSelect](mailto:support@selerix.com). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. L’application BenSelect attend les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à la configuration des attributs du jeton SAML. La capture d’écran suivante montre la liste des attributs par défaut.

    ![Capture d’écran montrant User Attributes avec des attributs par défaut, comme givenname user.givenname et emailaddress user.mail.](common/edit-attribute.png)

1. Cliquez sur l’icône **Modifier** pour changer la **Valeur de nom d’identificateur**.

    ![Capture d’écran montrant le volet User Attributes & Claims avec l’icône Edit mise en évidence.](media/benselect-tutorial/mail-prefix1.png)

1. Dans la section **Gérer les revendications des utilisateurs** , effectuez les étapes suivantes :

    ![Capture d’écran montrant Manage user claims, où vous pouvez entrer les valeurs décrites dans cette étape.](media/benselect-tutorial/mail-prefix2.png)

    a. Sélectionnez **Transformation** dans le champ **Source**.

    b. Dans la liste déroulante **Transformation**, sélectionnez **ExtractMailPrefix()** .

    c. Dans la liste déroulante **Paramètre 1**, sélectionnez **user.userprincipalname**.

    d. Cliquez sur **Enregistrer**.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **Certificat (brut)** , puis sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificateraw.png)

1. Dans la section **Configurer BenSelect**, copiez l’URL ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en accordant l’accès à BenSelect.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **BenSelect**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-benselect-sso"></a>Configurer l’authentification unique BenSelect

Pour configurer l’authentification unique côté **BenSelect**, vous devez envoyer le **Certificat (brut)** téléchargé ainsi que les URL copiées à partir du portail Azure à l’[équipe du support technique BenSelect](mailto:support@selerix.com). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

> [!NOTE]
> Vous devez indiquer que cette intégration nécessite l’algorithme SHA256 (SHA1 n’est pas pris en charge) pour définir l’authentification unique sur le serveur approprié, comme app2101, etc.

### <a name="create-benselect-test-user"></a>Créer un utilisateur de test BenSelect

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans BenSelect. Pour ajouter des utilisateurs dans la plateforme BenSelect, contactez l’[équipe du support BenSelect](mailto:support@selerix.com). Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes.

* Cliquez sur Tester cette application dans le portail Azure. Vous êtes alors automatiquement connecté à l’instance BenSelect pour laquelle vous avez configuré l’authentification unique.

* Vous pouvez utiliser Mes applications de Microsoft. Le fait de cliquer sur la vignette BenSelect dans Mes applications vous connecte automatiquement à l’instance BenSelect pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré BenSelect, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
