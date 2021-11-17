---
title: 'Tutoriel : Intégration de l’authentification unique Azure AD à Benefitsolver'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Benefitsolver.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/30/2021
ms.author: jeedes
ms.openlocfilehash: 124efe77ad962e00604e700eec65cc305c10b51c
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132324212"
---
# <a name="tutorial-azure-ad-sso-integration-with-benefitsolver"></a>Tutoriel : Intégration de l’authentification unique Azure AD à Benefitsolver

Dans ce tutoriel, vous allez apprendre à intégrer Benefitsolver à Azure Active Directory (Azure AD). Quand vous intégrez Benefitsolver à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Benefitsolver.
* Permettre à vos utilisateurs de se connecter automatiquement à Benefitsolver avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Benefitsolver pour lequel l’authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Benefitsolver prend en charge l’authentification unique lancée par le **fournisseur de services**.

## <a name="add-benefitsolver-from-the-gallery"></a>Ajouter Benefitsolver à partir de la galerie

Pour configurer l’intégration de Benefitsolver avec Azure AD, vous devez ajouter Benefitsolver, disponible dans la galerie, à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Benefitsolver** dans la zone de recherche.
1. Sélectionnez **Benefitsolver** dans le volet des résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-benefitsolver"></a>Configurer et tester l’authentification unique Azure AD pour Benefitsolver

Configurez et testez l’authentification unique Azure AD avec Benefitsolver à l’aide d’un utilisateur test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Benefitsolver associé.

Pour configurer et tester l’authentification unique Azure AD avec Benefitsolver, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique de Benefitsolver](#configure-benefitsolver-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur test Benefitsolver](#create-benefitsolver-test-user)** pour avoir un équivalent de B.Simon dans Benefitsolver, lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **Benefitsolver**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    a. Dans la zone de texte **Identificateur**, tapez une URL en utilisant le format suivant : `https://<companyname>.benefitsolver.com/saml20`

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://www.benefitsolver.com/benefits/BenefitSolverView?page_name=single_signon_saml`

     c. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `http://<companyname>.benefitsolver.com`.

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL d’authentification et l’URL de réponse réels. Pour obtenir ces valeurs, contactez [l’équipe de support technique de Benefitsolver](https://www.businessolver.com/contact). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

5. L’application Benefitsolver s’attend à ce que les assertions SAML soient dans un format spécifique. Configurez les revendications suivantes pour cette application. Vous pouvez gérer les valeurs de ces attributs à partir de la section **Attributs utilisateur** sur la page d’intégration des applications. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur le bouton **Modifier** pour ouvrir la boîte de dialogue **Attributs utilisateur**.

    ![Capture d’écran montrant Attributs utilisateur avec le contrôle d’édition activé.](common/edit-attribute.png)

6. Dans la section **Revendications des utilisateurs** de la boîte de dialogue **Attributs utilisateur**, modifiez les revendications en utilisant l’icône **Modifier** ou ajoutez des revendications en utilisant l’option **Ajouter une nouvelle revendication** pour configurer l’attribut de jeton SAML comme sur l’image ci-dessus et procédez comme suit : 

    | Name |  Attribut source|
    |---------------|----------------|
    | ClientID | L’[équipe de support technique Benefitsolver](https://www.businessolver.com/contact) doit vous fournir cette valeur.|
    | ClientKey | L’[équipe de support technique Benefitsolver](https://www.businessolver.com/contact) doit vous fournir cette valeur.|
    | LogoutURL | L’[équipe de support technique Benefitsolver](https://www.businessolver.com/contact) doit vous fournir cette valeur.|
    | EmployeeID | L’[équipe de support technique Benefitsolver](https://www.businessolver.com/contact) doit vous fournir cette valeur.|
    | | |

    a. Cliquez sur le bouton **Ajouter une nouvelle revendication** pour ouvrir la boîte de dialogue **Gérer les revendications des utilisateurs**.

    ![Capture d’écran montrant Revendications des utilisateurs avec Ajouter une nouvelle revendication et Enregistrer activés.](common/new-save-attribute.png)

    ![Capture d’écran montrant Gérer les revendications des utilisateurs, où vous pouvez entrer les valeurs décrites à cette étape.](common/new-attribute-details.png)

    b. Dans la zone de texte **Attribut**, indiquez le nom d’attribut pour cette ligne.

    c. Laissez le champ **Espace de noms** vide.

    d. Sélectionnez Source comme **Attribut**.

    e. Dans la liste **Attribut de la source**, tapez la valeur d’attribut indiquée pour cette ligne.

    f. Cliquez sur **OK**

    g. Cliquez sur **Enregistrer**.

7. Sur la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le fichier **XML de métadonnées de fédération** en fonction des options définies selon vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

8. Dans la section **Configurer Benefitsolver**, copiez la ou les URL appropriées, selon vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Benefitsolver.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Benefitsolver**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-benefitsolver-sso"></a>Configurer l’authentification unique de Benefitsolver

Pour configurer l’authentification unique côté **Benefitsolver**, vous devez envoyer le **XML de métadonnée** téléchargé et les URL copiées dans le portail Azure à [l’équipe du support technique Benefitsolver](https://www.businessolver.com/contact). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

> [!NOTE]
> L’équipe de support technique Benefitsolver doit se charger de la configuration de l’authentification unique. Vous recevrez une notification dès que l’authentification unique aura été activée pour votre abonnement.

### <a name="create-benefitsolver-test-user"></a>Créer un utilisateur de test Benefitsolver

Dans cette section, vous allez créer un utilisateur nommé Britta Simon dans Benefitsolver. Collaborez avec l’[équipe du support technique Benefitsolver](https://www.businessolver.com/contact) pour ajouter des utilisateurs dans la plateforme Benefitsolver. Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL d’authentification Benefitsolver, à partir de laquelle vous pouvez lancer le processus de connexion. 

* Accédez directement à l’URL d’authentification Benefitsolver pour lancer le processus de connexion.

* Vous pouvez utiliser Mes applications de Microsoft. Le fait de cliquer sur la vignette Benefitsolver dans Mes applications vous redirige vers l’URL d’authentification Benefitsolver. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez configuré Benefitsolver, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
