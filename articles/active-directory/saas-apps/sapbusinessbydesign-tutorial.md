---
title: 'Didacticiel : intégration d’Azure Active Directory à SAP Business ByDesign | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et SAP Business ByDesign.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/22/2021
ms.author: jeedes
ms.openlocfilehash: 69fceff94faebb3acce3a4c512d811841d455ce5
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132341442"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-business-bydesign"></a>Didacticiel : Intégration d’Azure Active Directory à SAP Business ByDesign

Dans ce tutoriel, vous allez apprendre à intégrer SAP Business ByDesign à Azure Active Directory (Azure AD). Quand vous intégrez SAP Business ByDesign à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à SAP Business ByDesign.
* Permettre à vos utilisateurs de se connecter automatiquement à SAP Business ByDesign avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement SAP Business ByDesign pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* SAP Business ByDesign prend en charge l’authentification unique lancée par le **fournisseur de services**

## <a name="add-sap-business-bydesign-from-the-gallery"></a>Ajouter SAP Business ByDesign à partir de la galerie

Pour configurer l’intégration de SAP Business ByDesign à Azure AD, vous devez ajouter SAP Business ByDesign depuis la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **SAP Business ByDesign** dans la zone de recherche.
1. Sélectionnez **SAP Business ByDesign** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso"></a>Configurer et tester l’authentification unique Azure AD

Configurez et testez l’authentification unique (SSO) Azure AD avec SAP Business ByDesign à l’aide d’un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique (SSO) fonctionne, vous devez établir une relation entre un utilisateur Azure AD et l’utilisateur SAP Business ByDesign associé.

Pour configurer et tester l’authentification unique Azure AD avec SAP Business ByDesign, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique (SSO) SAP Business ByDesign](#configure-sap-business-bydesign-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test SAP Business ByDesign](#create-sap-business-bydesign-test-user)** pour avoir dans SAP Business ByDesign un équivalent de Britta Simon lié à la représentation Azure AD associée.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans la page d’intégration de l’application **SAP Business ByDesign** du portail Azure, recherchez la section **Gérer**, puis sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    a. Dans la zone de texte **URL de connexion**, saisissez une URL au format suivant : `https://<servername>.sapbydesign.com`

    b. Dans la zone de texte **Identificateur (ID d’entité)** , saisissez une URL au format suivant : `https://<servername>.sapbydesign.com`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Pour obtenir ces valeurs, contactez [l’équipe de support technique SAP Business ByDesign](https://www.sap.com/products/cloud-analytics.support.html). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

5. L’application SAP Business ByDesign attend les assertions SAML dans un format spécifique. Configurez les revendications suivantes pour cette application. Vous pouvez gérer les valeurs de ces attributs à partir de la section **Attributs utilisateur** sur la page d’intégration des applications. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur le bouton **Modifier** pour ouvrir la boîte de dialogue **Attributs utilisateur**.

    ![image1](common/edit-attribute.png)

6. Cliquez sur l’icône **Modifier** pour changer la **Valeur de nom d’identificateur**.

    ![image2](media/sapbusinessbydesign-tutorial/mail-prefix1.png)

7. Dans la section **Gérer les revendications des utilisateurs** , effectuez les étapes suivantes :

    ![image3](media/sapbusinessbydesign-tutorial/mail-prefix2.png)

    a. Sélectionnez **Transformation** dans le champ **Source**.

    b. Dans la liste déroulante **Transformation**, sélectionnez **ExtractMailPrefix()** .

    > [!NOTE]
    > Par défaut, ByD utilise le format NameID **non spécifié** pour le mappage d’utilisateur. ByD mappe le NameID des assertions SAML sur l’alias de l’utilisateur ByD. De plus, ByD prend en charge le format d’ID de nom **emailAddress**. Dans ce cas, ByD mappe le NameID de l’assertion SAM à l’adresse e-mail de l’utilisateur ByD des données de contact de l’employé ByD. Pour plus d’informations, vous pouvez vous reporter à [ce blog SAP](https://blogs.sap.com/2017/05/24/single-sign-on-sso-with-sap-business-bydesign/).

8. Sur la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le fichier **XML de métadonnées de fédération** en fonction des options définies selon vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

9. Dans la section **Configurer SAP Business ByDesign**, copiez l’URL ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à SAP Business ByDesign.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **SAP Business ByDesign**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.

1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-sap-business-bydesign-sso"></a>Configurer l’authentification unique SAP Business ByDesign

1. Connectez-vous à votre portail SAP Business ByDesign avec des droits d’administrateur.

2. Accédez à **Application and User Management Common Task (Tâche courante d’application et de gestion des utilisateurs)** et cliquez sur l’onglet **Identity Provider (Fournisseur d’identité)** .

3. Cliquez sur **New Identity Provider** (Nouveau fournisseur d’identité) et sélectionnez le fichier XML de métadonnées que vous avez téléchargé à partir du portail Azure. En important les métadonnées, le système charge automatiquement le certificat de signature ainsi que le certificat de chiffrement requis.

    ![Configurer l’authentification unique 1](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_54.png)

4. Pour inclure **l’URL Assertion Consumer Service** dans la requête SAML, sélectionnez **Include Assertion Consumer Service URL (Inclure l’URL Assertion Consumer Service)** .

5. Cliquez sur **Activate Single Sign-On**(Activer l’authentification unique).

6. Enregistrez vos modifications.

7. Cliquez sur l’onglet **My System** (Mon système).

    ![Configurer l’authentification unique 2](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_52.png)

8. Dans la zone de texte **Azure AD Sign On URL** (URL de connexion Azure AD), collez l’**URL de connexion** que vous avez copiée à partir du portail Azure.

    ![Configurer l’authentification unique 3](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_53.png)

9. Précisez si l’employé peut choisir manuellement entre l’authentification à l’aide d’un ID d’utilisateur/mot de passe ou l’authentification unique en cliquant sur **Manual Identity Provider Selection**(Sélection manuelle du fournisseur d’identité).

10. Dans la section **SSO URL** (URL SSO), spécifiez l’URL devant être utilisée par l’employé pour se connecter au système.
    Dans la liste déroulante URL Sent to Employee (URL envoyée à l’employé), vous pouvez choisir entre les options suivantes :

    **Non-SSO URL**

    Le système envoie uniquement l’URL du système normale à l’employé. L’employé ne peut pas se connecter à l’aide de l’authentification unique ; il doit utiliser un mot de passe ou un certificat à la place.

    **URL SSO**

    Le système envoie uniquement l’URL SSO à l’employé. L’employé peut se connecter à l’aide de l’authentification unique. La demande d’authentification est redirigée via l’IdP.

    **Sélection automatique**

    Si l’authentification unique n’est pas activée, le système envoie l’URL du système normale à l’employé. Si l’authentification unique est activée, le système vérifie si l’employé possède un mot de passe. Le cas échéant, les URL SSO et les URL non SSO sont envoyées à l’employé. Toutefois, si l’employé ne possède aucun mot de passe, seule l’URL SSO est envoyée à l’employé.

11. Enregistrez vos modifications.

### <a name="create-sap-business-bydesign-test-user"></a>Créer un utilisateur de test pour SAP Business ByDesign

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans SAP Business ByDesign. Veuillez contacter [l’équipe de prise en charge des clients de SAP Business ByDesign](https://www.sap.com/products/cloud-analytics.support.html) pour ajouter des utilisateurs sur la plateforme SAP Business ByDesign. 

> [!NOTE]
> Assurez-vous que la valeur NameID correspond au champ de nom d’utilisateur dans la plateforme SAP Business ByDesign.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

1. Cliquez sur **Tester cette application** dans le portail Azure. Cette opération redirige vers l’URL d’authentification SAP Business ByDesign, où vous pouvez lancer le processus de connexion. 

2. Accédez directement à l’URL d’authentification SAP Business ByDesign pour lancer le processus de connexion.

3. Vous pouvez utiliser Mes applications de Microsoft. Le fait de cliquer sur la vignette SAP Business ByDesign dans Mes applications vous redirige vers l’URL d’authentification SAP Business ByDesign. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

* Après avoir configuré SAP Business ByDesign, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Les contrôles de session sont étendus à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
