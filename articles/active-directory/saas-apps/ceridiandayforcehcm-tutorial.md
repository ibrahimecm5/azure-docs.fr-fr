---
title: 'Didacticiel : Intégration d’Azure Active Directory à Ceridian Dayforce HCM | Microsoft docs'
description: Découvrez comment configurer l'authentification unique entre Azure Active Directory et Ceridian Dayforce HCM.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/27/2021
ms.author: jeedes
ms.openlocfilehash: cc49e828bf10e56dc851e50ef460d8f62d99bc18
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132292088"
---
# <a name="tutorial-azure-active-directory-integration-with-ceridian-dayforce-hcm"></a>Didacticiel : Intégration d’Azure Active Directory à Ceridian Dayforce HCM

Dans ce tutoriel, vous allez découvrir comment intégrer Ceridian Dayforce HCM à Azure Active Directory (Azure AD). Lorsque vous intégrez Ceridian Dayforce HCM à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Ceridian Dayforce HCM.
* Permettre à vos utilisateurs de se connecter automatiquement à Ceridian Dayforce HCM avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Ceridian Dayforce HCM pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Ceridian Dayforce HCM prend en charge l’authentification unique initiée par le **fournisseur de services**

## <a name="add-ceridian-dayforce-hcm-from-the-gallery"></a>Ajouter Ceridian Dayforce HCM à partir de la galerie

Pour configurer l’intégration de Ceridian Dayforce HCM à Azure AD, vous devez ajouter Ceridian Dayforce HCM à partir de la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Ceridian Dayforce HCM** dans la zone de recherche.
1. Sélectionnez **Ceridian Dayforce HCM** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-ceridian-dayforce-hcm"></a>Configurer et tester l’authentification unique Azure AD pour Ceridian Dayforce HCM

Configurez et testez l’authentification unique Azure AD avec Ceridian Dayforce HCM à l’aide d’un utilisateur de test appelé **B. Simon**. Pour que l’authentification unique fonctionne, vous devez établir une relation entre un utilisateur Azure AD et l’utilisateur Ceridian Dayforce HCM associé.

Pour configurer et tester l’authentification unique Azure AD avec Ceridian Dayforce HCM, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Ceridian Dayforce HCM](#configure-ceridian-dayforce-hcm-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test Ceridian Dayforce HCM](#create-ceridian-dayforce-hcm-test-user)** pour avoir un équivalent de B.Simon dans Ceridian Dayforce HCM lié à la représentation Azure AD associée.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD 

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, dans la page d’intégration de l’application **Ceridian Dayforce HCM**, recherchez la section **Gérer**, puis sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL Ceridian Dayforce HCM](common/sp-identifier-reply.png)

    a. Dans la zone de texte **URL d’authentification** , tapez l’URL utilisée par vos utilisateurs pour se connecter à votre application Ceridian Dayforce HCM.

    | Environnement | URL |
    | :-- | :-- |
    | Production | `https://sso.dayforcehcm.com/<DayforcehcmNamespace>` |
    | Test | `https://ssotest.dayforcehcm.com/<DayforcehcmNamespace>` |

    b. Dans la zone de texte **Identificateur**, tapez l’URL au format suivant : 

    | Environnement | URL |
    | :-- | :-- |
    | Production | `https://ncpingfederate.dayforcehcm.com/sp` |
    | Test | `https://fs-test.dayforcehcm.com/sp` |

    c. Dans la zone de texte **URL de réponse**, tapez l’URL utilisée par Azure AD pour publier la réponse.

    | Environnement | URL |
    | :-- | :-- |
    | Production | `https://ncpingfederate.dayforcehcm.com/sp/ACS.saml2` |
    | Test | `https://fs-test.dayforcehcm.com/sp/ACS.saml2` |

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion, l’identificateur et l’URL de réponse réels. Pour obtenir ces valeurs, contactez [l’équipe du support Ceridian Dayforce HCM](https://www.ceridian.com/support). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

5. L’application Ceridian Dayforce HCM attend les assertions SAML dans un format spécifique. Configurez les revendications suivantes pour cette application. Vous pouvez gérer les valeurs de ces attributs à partir de la section **Attributs utilisateur** sur la page d’intégration des applications. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur le bouton **Modifier** pour ouvrir la boîte de dialogue **Attributs utilisateur**.

    ![Capture d’écran montrant User Attributes avec l’icône Edit sélectionnée.](common/edit-attribute.png)

6. Dans la section **Revendications des utilisateurs** de la boîte de dialogue **Attributs utilisateur**, configurez le jeton SAML comme sur l’image ci-dessus et procédez comme suit :

    | Name | Attribut source|
    | ---------| --------- |
    | name  | user.extensionattribute2 |

    a. Cliquez sur le bouton **Ajouter une nouvelle revendication** pour ouvrir la boîte de dialogue **Gérer les revendications des utilisateurs**.

    ![Capture d’écran montrant User claims avec l’option permettant d’ajouter une nouvelle revendication.](common/new-save-attribute.png)

    ![Capture d’écran montrant la boîte de dialogue Manage user claims où vous pouvez entrer les valeurs décrites.](common/new-attribute-details.png)

    b. Dans la zone de texte **Attribut**, indiquez le nom d’attribut pour cette ligne.

    c. Laissez le champ **Espace de noms** vide.

    d. Sélectionnez Source comme **Attribut**.

    e. Dans la liste **Attribut de la source**, sélectionnez l’attribut utilisateur que vous souhaitez utiliser pour votre implémentation. Par exemple, si vous souhaitez utiliser EmployeeID comme identificateur d’utilisateur unique et que vous avez stocké la valeur d’attribut dans ExtensionAttribute2, sélectionnez user.extensionattribute2.

    f. Cliquez sur **OK**

    g. Cliquez sur **Enregistrer**.

7. Sur la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le fichier **XML des métadonnées** en fonction des options définies, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

8. Dans la section **Configurer Ceridian Dayforce HCM**, copiez la ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Ceridian Dayforce HCM.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Ceridian Dayforce HCM**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

### <a name="configure-ceridian-dayforce-hcm-sso"></a>Configurer l’authentification unique Ceridian Dayforce HCM

Pour configurer l’authentification unique côté **Ceridian Dayforce HCM**, vous devez envoyer le **XML de métadonnée** téléchargé et les URL copiées correspondantes depuis le portail Azure à l’[équipe du support technique Ceridian Dayforce HCM](https://www.ceridian.com/support). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

### <a name="create-ceridian-dayforce-hcm-test-user"></a>Créer un utilisateur de test Ceridian Dayforce HCM

Dans cette section, vous allez créer un utilisateur nommé Britta Simon dans Ceridian Dayforce HCM. Pour ajouter les utilisateurs dans la plateforme Ceridian Dayforce HCM, contactez l’[équipe du support technique Ceridian Dayforce HCM](https://www.ceridian.com/support). Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique.

### <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion à Ceridian Dayforce HCM, d’où vous pouvez lancer le flux de connexion. 

* Accédez directement à l’URL de connexion à Ceridian Dayforce HCM pour lancer le flux de connexion.

* Vous pouvez utiliser Mes applications de Microsoft. Un clic sur la vignette Ceridian Dayforce HCM dans Mes applications vous redirige vers l’URL de connexion à Ceridian Dayforce HCM. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré Ceridian Dayforce HCM, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
