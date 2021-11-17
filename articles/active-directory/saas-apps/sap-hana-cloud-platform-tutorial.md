---
title: 'Tutoriel : Intégration de l’authentification unique Azure AD à SAP Cloud Platform'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et SAP Cloud Platform.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/08/2021
ms.author: jeedes
ms.openlocfilehash: 4d7628b1431cebed1136aa2cb1b09d92e7fbb30b
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132320789"
---
# <a name="tutorial-azure-ad-sso-integration-with-sap-cloud-platform"></a>Tutoriel : Intégration de l’authentification unique Azure AD à SAP Cloud Platform

Dans ce tutoriel, vous allez apprendre à intégrer SAP Cloud Platform à Azure Active Directory (Azure AD). Quand vous intégrez SAP Cloud Platform à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à SAP Cloud Platform.
* Permettre à vos utilisateurs de se connecter automatiquement à SAP Cloud Platform avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement SAP Cloud Platform pour lequel l’authentification unique (SSO) est activée.

>[!IMPORTANT]
>Vous devez déployer votre propre application ou vous abonner à une application sur votre compte SAP Cloud Platform pour tester l’authentification unique. Dans ce didacticiel, une application est déployée dans le compte.
> 

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* SAP Cloud Platform prend en charge l’**authentification unique lancée par le fournisseur d’identité**.

## <a name="add-sap-cloud-platform-from-the-gallery"></a>Ajouter SAP Cloud Platform à partir de la galerie

Pour configurer l’intégration de SAP Cloud Platform à Azure AD, vous devez ajouter SAP Cloud Platform disponible dans la galerie, à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **SAP Cloud Platform** dans la zone de recherche.
1. Sélectionnez **SAP Cloud Platform** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-sap-cloud-platform"></a>Configurer et tester l’authentification unique Azure AD pour SAP Cloud Platform

Configurez et testez l’authentification unique Azure AD avec SAP Cloud Platform à l’aide d’un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur SAP Cloud Platform associé.

Pour configurer et tester l’authentification unique Azure AD avec SAP Cloud Platform, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
2. **[Configurer l’authentification unique SAP Cloud Platform](#configure-sap-cloud-platform-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur test SAP Cloud Platform](#create-sap-cloud-platform-test-user)** pour avoir un équivalent de Britta Simon dans SAP Cloud Platform lié à la représentation Azure AD associée.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **SAP Cloud Platform**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, entrez les valeurs pour les champs suivants :

    a. Dans la zone de texte **Identificateur**, vous indiquez le type de votre SAP Cloud Platform. Tapez une URL selon l’un des modèles suivants : 

    | **Identificateur** |
    |-----------|
    | `https://hanatrial.ondemand.com/<instancename>` |
    | `https://hana.ondemand.com/<instancename>` |
    | `https://us1.hana.ondemand.com/<instancename>` |
    | `https://ap1.hana.ondemand.com/<instancename>` |

    b. Dans la zone de texte **URL de réponse** , tapez une URL en respectant l’un des formats suivants :

    | **URL de réponse** |
    |----------|
    | `https://<subdomain>.hanatrial.ondemand.com/<instancename>` |
    | `https://<subdomain>.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.us1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.dispatcher.us1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.ap1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.dispatcher.ap1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.dispatcher.hana.ondemand.com/<instancename>` |

    c. Dans la zone de texte **URL d’authentification**, entrez l’URL utilisée par vos utilisateurs pour se connecter à votre application **SAP Cloud Platform**. Il s’agit de l’URL spécifique au compte d’une ressource protégée de votre application SAP Cloud Platform. L’URL est basée sur le modèle suivant : `https://<applicationName><accountName>.<landscape host>.ondemand.com/<path_to_protected_resource>`
      
    >[!NOTE]
    >Il s’agit de l’URL de votre application SAP Cloud Platform sur laquelle l’utilisateur doit s’authentifier.
    > 

    | **URL de connexion** |
    |------|
    | `https://<subdomain>.hanatrial.ondemand.com/<instancename>` |
    | `https://<subdomain>.hana.ondemand.com/<instancename>` |

    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL de réponse et l’URL de connexion réels. Contactez l’[équipe de support client SAP Cloud Platform](https://help.sap.com/viewer/65de2977205c403bbc107264b8eccf4b/Cloud/5dd739823b824b539eee47b7860a00be.html) pour obtenir l’URL de connexion et l’identificateur. URL de réponse que vous obtenez dans la section Gestion des approbations qui est expliquée plus loin dans le didacticiel.
    > 
4. Sur la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le fichier **XML de métadonnées de fédération** en fonction des options définies selon vos besoins, puis enregistrez-le sur votre ordinateur.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à SAP Cloud Platform.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **SAP Cloud Platform**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-sap-cloud-platform-sso"></a>Configurer l’authentification unique SAP Cloud Platform

1. Dans une autre fenêtre de navigateur web, connectez-vous à SAP Cloud Platform Cockpit à l’adresse `https://account.<landscape host>.ondemand.com/cockpit`(par exemple : https://account.hanatrial.ondemand.com/cockpit).

2. Cliquez sur l’onglet **Trust** .
   
    ![Approbation](./media/sap-hana-cloud-platform-tutorial/account.png "Trust")

3. Dans la section Gestion des approbations, sous **Fournisseur de services local**, procédez comme suit :

    ![Capture d’écran montrant la section « Trust Management » avec l’onglet « Local Service Provider » sélectionné et toutes les zones de texte mises en évidence.](./media/sap-hana-cloud-platform-tutorial/service.png "Gestion des approbations")
   
    a. Cliquez sur **Modifier**.

    b. Comme **Configuration Type**, sélectionnez **Custom**.

    c. Pour **Local Provider Name**, laissez la valeur par défaut. Copiez cette valeur et collez-la dans le champ **Identificateur** dans la configuration d’Azure AD pour SAP Cloud Platform.

    d. Pour générer une paire de clés **Signature Key** et **Signing Certificate**, cliquez sur **Generate Key Pair**.

    e. Pour **Principal Propagation**, sélectionnez **Disabled**.

    f. Pour **Force Authentication**, sélectionnez **Disabled**.

    g. Cliquez sur **Enregistrer**.

4. Une fois les paramètres **Fournisseur de services local** enregistrés, procédez comme suit pour obtenir l’URL de réponse :
   
    ![Obtenir les métadonnées](./media/sap-hana-cloud-platform-tutorial/certificate.png "Get Metadata")

    a. Téléchargez le fichier de métadonnées SAP Cloud Platform en cliquant sur **Obtenir les métadonnées**.

    b. Ouvrez le fichier XML de métadonnées SAP Cloud Platform téléchargé et recherchez la balise **ns3:AssertionConsumerService** .
 
    c. Copiez la valeur de l’attribut **Emplacement** et collez-la dans le champ **URL de réponse** dans la configuration d’Azure AD pour SAP Cloud Platform.

5. Cliquez sur l’onglet **Trusted Identity Provider**, puis sur **Add Trusted Identity Provider**.
   
    ![Capture d’écran montrant la page « Trust Management » avec l’onglet « Trusted Identity Provider » sélectionné.](./media/sap-hana-cloud-platform-tutorial/add-service.png "Gestion des approbations")
   
    >[!NOTE]
    >Pour gérer la liste de fournisseurs d’identité approuvés, vous devrez avoir choisi le type de configuration Custom dans la section Local Service Provider. Pour le type de configuration Default, vous disposez d’une approbation non modifiable et implicite au SAP ID Service. Pour None, vous n’avez aucun paramètre d’approbation.
    > 
    > 

6. Cliquez sur l’onglet **General**, puis sur **Browse** pour charger le fichier de métadonnées que vous avez téléchargé.
    
    ![Gestion des approbations](./media/sap-hana-cloud-platform-tutorial/general.png "Gestion des approbations")
    
    >[!NOTE]
    >Après avoir téléchargé le fichier de métadonnées, les valeurs de **Authentification unique**, **URL de déconnexion unique** et de **Certificat de signature** sont remplies automatiquement.
    > 
     
7. Cliquez sur onglet **Attributes** .

8. Sous l’onglet **Attributes**, procédez comme suit :
    
    ![Attributs](./media/sap-hana-cloud-platform-tutorial/principal-attribute.png "Attributs") 

    a. Cliquez sur **Add Assertion-Based Attribute**, puis ajoutez les attributs basés sur une assertion suivants :
       
    | Assertion Attribute | Principal Attribute |
    | --- | --- |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` |firstname |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` |lastname |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` |email |
   
    >[!NOTE]
    >La configuration des attributs dépend de la façon dont sont développées les applications sur SCP, en l’occurrence des attributs qu’elles attendent dans la réponse SAML et avec quel nom (Principal Attribute) elles accèdent à cet attribut dans le code.
    > 
    
    b. L’attribut **Default Attribute** de la capture d’écran ne sert qu’à des fins d’illustration. Il n’est pas nécessaire de faire fonctionner le scénario.  
 
    c. Les noms et valeurs de **Principal Attribute** dans la capture d’écran dépendent de la façon dont l’application est développée. Il est possible que votre application exige des mappages différents.

### <a name="assertion-based-groups"></a>Groupes basés sur une assertion

Une étape facultative est de configurer des groupes basés sur une assertion pour votre fournisseur d’identité Azure Active Directory.

L’utilisation de SAP Cloud Platform vous permet d’attribuer de manière dynamique un ou plusieurs utilisateurs à un ou plusieurs rôles dans vos applications SAP Cloud Platform, en fonction des valeurs des attributs de l’assertion SAML 2.0. 

Par exemple, si l’assertion contient l’attribut « *contract=temporaire* », vous souhaiterez peut-être que tous les utilisateurs affectés soient ajoutés au groupe « *TEMPORAIRE* ». Le groupe « *TEMPORAIRE* » peut contenir un ou plusieurs rôles d’une ou plusieurs applications déployées dans votre compte SAP Cloud Platform.
 
Utilisez des groupes basés sur une assertion quand vous voulez affecter simultanément plusieurs utilisateurs à un ou plusieurs rôles d’applications dans votre compte SAP Cloud Platform. Si vous ne voulez affecter qu’un seul utilisateur ou un petit nombre d’utilisateurs à des rôles spécifiques, nous vous recommandons de les affecter directement dans l’onglet « **Autorisations** » de SAP Cloud Platform Cockpit.

### <a name="create-sap-cloud-platform-test-user"></a>Créer un utilisateur test SAP Cloud Platform

Pour permettre aux utilisateurs d’Azure AD de se connecter à SAP Cloud Platform, vous devez leur attribuer des rôles dans SAP Cloud Platform.

**Pour affecter un rôle à un utilisateur, procédez comme suit :**

1. Connectez-vous à votre cockpit **SAP Cloud Platform**.

2. Procédez comme suit :
   
    ![Autorisations](./media/sap-hana-cloud-platform-tutorial/roles.png "Autorisations")
   
    a. Cliquez sur **Authorization**.

    b. Cliquez sur l’onglet **Users** .

    c. Dans la zone de texte **User** , tapez l’adresse de messagerie de l’utilisateur.

    d. Cliquez sur **Assign** pour attribuer l’utilisateur à un rôle.

    e. Cliquez sur **Enregistrer**.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Cette opération redirige vers l’URL de connexion SAP Cloud Platform où vous pouvez lancer le flux de connexion. 

* Accédez directement à l’URL de connexion SAP Cloud Platform pour lancer le flux de connexion à partir de là.

* Vous pouvez utiliser Mes applications de Microsoft. Quand vous cliquez sur la vignette SAP Cloud Platform dans Mes applications, vous devez être connecté automatiquement à l’application SAP Cloud Platform pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré SAP Cloud Platform, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
