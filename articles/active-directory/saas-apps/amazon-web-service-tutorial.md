---
title: 'Tutoriel : Intégration de l’authentification unique Azure Active Directory à AWS Single-Account Access | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et AWS Single-Account Access.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/05/2021
ms.author: jeedes
ms.openlocfilehash: 5e3905c9d15d16641cad8a818719d9335cfcd916
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132324590"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-aws-single-account-access"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory à AWS Single-Account Access

Dans ce tutoriel, vous allez découvrir comment intégrer AWS Single-Account Access à Azure Active Directory (Azure AD). Quand vous intégrez AWS Single-Account Access à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à AWS Single-Account Access.
* Permettre à vos utilisateurs de se connecter automatiquement à AWS Single-Account Access avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="understanding-the-different-aws-applications-in-the-azure-ad-application-gallery"></a>Présentation des différentes applications AWS dans la galerie d’applications Azure AD
Utilisez les informations ci-dessous pour prendre une décision concernant l’utilisation des applications AWS Single Sign-On et AWS Single-Account Access dans la galerie d’applications Azure AD.

**AWS Single Sign-On**

[AWS Single Sign-On](./aws-single-sign-on-tutorial.md) a été ajoutée à la galerie d’applications Azure AD en février 2021. Elle facilite la gestion de l’accès de manière centralisée sur plusieurs comptes AWS et applications AWS, avec la connexion via Microsoft Azure AD. Fédérez une seule fois Microsoft Azure AD avec l’authentification unique AWS et utilisez l’authentification unique AWS pour gérer les autorisations sur l’ensemble de vos comptes AWS à partir d’un emplacement unique. L’authentification unique AWS provisionne automatiquement les autorisations et les maintient à jour lorsque vous mettez à jour des stratégies et des attributions d’accès. Les utilisateurs finaux peuvent s’authentifier à l’aide de leurs informations d’identification Azure AD pour accéder à la console AWS, à l’interface de ligne de commande et aux applications intégrées d’authentification unique AWS.

**AWS Single-Account Access**

L’application [AWS Single-Account Access]() a été utilisée par les clients au cours des dernières années et vous permet de fédérer Azure AD sur un seul compte AWS et d’utiliser Azure AD pour gérer l’accès aux rôles AWS IAM. Les administrateurs AWS IAM définissent des rôles et des stratégies dans chaque compte AWS. Pour chaque compte AWS, les administrateurs Azure AD se fédèrent à AWS IAM, attribuent des utilisateurs ou des groupes au compte et configurent Azure AD pour envoyer des assertions qui autorisent l’accès aux rôles.  

| Fonctionnalité | AWS Single Sign-On | AWS Single-Account Access |
|:--- |:---:|:---:|
|Accès conditionnel| Prend en charge une seule stratégie d’accès conditionnel pour tous les comptes AWS. | Prend en charge une seule stratégie d’accès conditionnel pour l’ensemble des comptes ou des stratégies personnalisées par compte|
| Accès à l’interface CLI | Prise en charge | Prise en charge|
| Privileged Identity Management | Pas encore pris en charge | Pas encore pris en charge |
| Centraliser la gestion des comptes | Centralisez la gestion des comptes dans AWS. | Centralisez la gestion des comptes dans Azure AD (nécessitera probablement une application d’entreprise Azure AD par compte). |
| Certificat SAML| Certificat unique| Certificats séparés par application/compte | 

## <a name="aws-single-account-access-architecture"></a>Architecture AWS Single-Account Access
![Diagramme de la relation entre Azure AD et AWS](./media/amazon-web-service-tutorial/tutorial_amazonwebservices_image.png)

Vous pouvez configurer plusieurs identificateurs pour plusieurs instances. Par exemple :

* `https://signin.aws.amazon.com/saml#1`

* `https://signin.aws.amazon.com/saml#2`

Avec ces valeurs, Azure AD supprime la valeur de **#** et envoie la valeur correcte `https://signin.aws.amazon.com/saml` en tant qu’URL d’audience dans le jeton SAML.

Nous vous recommandons cette approche pour les raisons suivantes :

- Chaque application vous fournit un certificat X509 unique. Chaque instance d’une instance de l’application AWS peut alors avoir une date d’expiration de certificat différente que vous pouvez gérer en fonction de chaque compte AWS. La substitution de certificat globale est plus aisée dans ce cas.

- Vous pouvez activer l’attribution d’utilisateurs avec une application AWS dans Azure AD, puis notre service extrait tous les rôles de ce compte AWS. Vous n’êtes pas obligé d’ajouter ni de mettre à jour manuellement les rôles AWS sur l’application.

- Vous pouvez attribuer le propriétaire de l’application individuellement pour l’application. Cette personne peut gérer l’application directement dans Azure AD.

> [!Note]
> Veillez à utiliser uniquement une application de la galerie.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Abonnement AWS pour lequel l’authentification unique (SSO) est activée

> [!Note]
> Les rôles ne doivent pas être modifiés manuellement dans Azure AD lors de l’importation des rôles.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* AWS Single-Account Access prend en charge l’authentification unique lancée par **le fournisseur de services et le fournisseur d’identité**.

> [!NOTE]
> L’identificateur de cette application étant une valeur de chaîne fixe, une seule instance peut être configurée dans un locataire.

## <a name="adding-aws-single-account-access-from-the-gallery"></a>Ajout d’AWS Single-Account Access à partir de la galerie

Pour configurer l’intégration d’AWS Single-Account Access à Azure AD, vous devez ajouter AWS Single-Account Access à partir de la galerie à votre liste d’applications SaaS managées.

1. Connectez-vous au portail Azure avec un compte professionnel, un compte scolaire ou un compte personnel Microsoft.
1. Dans le Portail Azure, recherchez et sélectionnez **Azure Active Directory**.
1. Dans le menu de vue d’ensemble d’Azure Active Directory, choisissez **Applications d’entreprise** > **Toutes les applications**.
1. Sélectionnez **Nouvelle application** pour ajouter une application.
1. Dans la section **Ajouter à partir de la galerie**, tapez **AWS Single-Account Access** dans la zone de recherche.
1. Sélectionnez **AWS Single-Account Access** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-aws-single-account-access"></a>Configurer et tester l’authentification unique Azure AD pour AWS Single-Account Access

Configurez et testez l’authentification unique Azure AD avec AWS Single-Account Access à l’aide d’un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur AWS Single-Account Access associé.

Pour configurer et tester l’authentification unique Azure AD avec AWS Single-Account Access, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique AWS Single-Account Access](#configure-aws-single-account-access-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test AWS Single-Account Access](#create-aws-single-account-access-test-user)** pour avoir un équivalent de B.Simon dans AWS Single-Account Access associé à la représentation Azure AD de l’utilisateur.
    1. **[Comment configurer le provisionnement des rôles dans AWS Single-Account Access](#how-to-configure-role-provisioning-in-aws-single-account-access)**
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **AWS Single-Account Access**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de modification/stylet de **Configuration SAML de base** pour modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, mettez à jour les champs **Identificateur (ID d’entité)** et **URL de réponse** avec la même valeur par défaut : `https://signin.aws.amazon.com/saml`. Vous devez sélectionner **Enregistrer** pour enregistrer les changements de configuration.

1. Quand vous configurez plusieurs instances, indiquez la valeur d’identificateur. À partir de la deuxième instance, utilisez le format suivant, y compris un signe **#** pour spécifier une valeur SPN unique.

    `https://signin.aws.amazon.com/saml#2`

1. L’application AWS s’attend à recevoir les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à votre configuration des attributs de jetons SAML. La capture d’écran suivante montre la liste des attributs par défaut.

    ![image](common/default-attributes.png)

1. En plus de ce qui précède, l’application AWS s’attend à ce que quelques attributs supplémentaires (présentés ci-dessous) soient repassés dans la réponse SAML. Ces attributs sont également préremplis, mais vous pouvez les examiner pour voir s’ils répondent à vos besoins.
    
    | Nom  | Attribut source  | Espace de noms |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | `https://aws.amazon.com/SAML/Attributes` |
    | Role | user.assignedroles |  `https://aws.amazon.com/SAML/Attributes` |
    | SessionDuration | [Indiquez une valeur comprise entre 900 secondes (15 minutes) et 43 200 secondes (12 heures)] |  `https://aws.amazon.com/SAML/Attributes` |

    > [!NOTE]
    > AWS attend des rôles pour les utilisateurs affectés à l’application. Veuillez configurer ces rôles dans Azure AD pour pouvoir affecter les rôles appropriés aux utilisateurs. Pour comprendre comment configurer des rôles dans Azure AD, consultez [cette page](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui)

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML** (Étape 3), sélectionnez **Ajouter un certificat**.

    ![Créer un certificat SAML](common/add-saml-certificate.png)

1. Générez un nouveau certificat de signature SAML, puis sélectionnez **Nouveau certificat**. Entrez une adresse e-mail pour les notifications de certificat.
   
    ![Nouveau certificat SAML](common/new-saml-certificate.png) 

1. Dans la section **Certificat de signature SAML**, recherchez **XML de métadonnées de fédération**, puis sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/amazon-web-service-tutorial/certificate.png)

1. Dans la section **Configurer AWS Single-Account Access**, copiez la ou les URL appropriées en fonction de vos besoins.

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

Dans cette section, vous allez créer un utilisateur de test appelé B. Simon dans le portail Azure.

1. Dans le Portail Azure, recherchez et sélectionnez **Azure Active Directory**.
1. Dans le menu de vue d’ensemble d’Azure Active Directory, choisissez **Utilisateurs** > **Tous les utilisateurs**.
1. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.
1. Dans les propriétés **Utilisateur**, effectuez les étapes suivantes :
   1. Dans le champ **Nom**, entrez `B.Simon`.  
   1. Dans le champ **Nom de l’utilisateur**, entrez username@companydomain.extension. Par exemple : `B.Simon@contoso.com`.
   1. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.
   1. Cliquez sur **Créer**.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à AWS Single-Account Access.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **AWS Single-Account Access**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-aws-single-account-access-sso"></a>Configurer l’authentification unique AWS Single-Account Access

1. Dans une autre fenêtre de navigateur, connectez-vous à votre site d’entreprise AWS en tant qu’administrateur.

2. Sélectionnez l’**icône d’accueil d’AWS**.

    ![Capture d’écran du site d’entreprise AWS, avec mise en évidence de l’icône d’accueil d’AWS][11]

3. Sélectionnez **Gestion des identités et des accès**.

    ![Capture d’écran de la page des services AWS, avec mise en évidence de l’option IAM][12]

4. Sélectionnez **Identity Providers** (Fournisseurs d’identité) > **Create Provider** (Créer un fournisseur).

    ![Capture d’écran de la page IAM, avec mise en évidence des éléments Identity Providers et Create Provider][13]

5. Dans la page **Configure Provider** (Configurer le fournisseur), effectuez les étapes suivantes :

    ![Capture d’écran de Configure Provider][14]

    a. Pour **Type de fournisseur**, sélectionnez **SAML**.

    b. Dans **Provider Name** (Nom de fournisseur), tapez un nom de fournisseur (par exemple : *WAAD*).

    c. Pour charger le **fichier de métadonnées** téléchargé à partir du portail Azure, sélectionnez **Choisir un fichier**.

    d. Sélectionnez **Étape suivante**.

6. Dans la page **Verify Provider Information** (Vérifier les informations du fournisseur), sélectionnez **Create**.

    ![Capture d’écran de Verify Provider Information, avec mise en évidence de l’option Créer][15]

7. Sélectionnez **Roles** > **Create role**.

    ![Capture d’écran de la page Roles][16]

8. Dans la page **Create role** (Créer un rôle), effectuez les étapes suivantes :  

    ![Capture d’écran de la page Create role][19]

    a. Dans **Select type of trusted entity** (Sélectionnez un type d’entité approuvé), Sélectionnez **SAML 2.0 federation** (Fédération SAML 2.0).

    b. Dans **Choose a SAML 2.0 Provider** (Choisir un fournisseur SAML 2.0), sélectionnez le **fournisseur SAML** que vous avez déjà créé (par exemple : *WAAD*).

    c. Sélectionnez **Allow programmatic and AWS Management Console access** (Autoriser l’accès par programme à AWS Management Console).
  
    d. Sélectionnez **Suivant : Autorisations**.

9. Dans la boîte de dialogue **Attach Permissions Policies** (Attacher des stratégies d’autorisation), attachez la stratégie appropriée pour votre organisation. Ensuite, sélectionnez **Next: Vérification**).  

    ![Capture d’écran de la boîte de dialogue Attach Permissions Policies][33]

10. Dans la boîte de dialogue **Review** (Vérification), effectuez les étapes suivantes :

    ![Capture d’écran de la boîte de dialogue Review][34]

    a. Dans **Role name** (Nom du rôle), entrez votre nom de rôle.

    b. Dans **Role description** (Description du rôle), entrez la description.

    c. Sélectionnez **Create role**.

    d. Créez autant de rôles que nécessaire et mappez-les vers le fournisseur d’identité.

11. Utilisez les informations d’identification du compte de service AWS pour récupérer les rôles depuis le compte AWS dans la section d’attribution d’utilisateurs d’Azure AD. Pour ce faire, ouvrez la page d’accueil de la console AWS.

12. Sélectionnez **Services**. Sous **Security, Identity & Compliance** (Sécurité, identité et conformité), sélectionnez **IAM**.

    ![Capture d’écran de la page d’accueil de la console AWS, avec mise en évidence de Services et IAM](./media/amazon-web-service-tutorial/fetchingrole1.png)

13. Dans la section IAM, sélectionnez **Policies** (Stratégies).

    ![Capture d’écran de la section IAM, avec mise en évidence de Policies](./media/amazon-web-service-tutorial/fetchingrole2.png)

14. Créez une stratégie en sélectionnant **Create policy** (Créer une stratégie) pour récupérer les rôles du compte AWS dans la section d’attribution d’utilisateurs Azure AD.

    ![Capture d’écran de la page Create role, avec mise en évidence de Create policy](./media/amazon-web-service-tutorial/fetchingrole3.png)

15. Créez votre propre stratégie pour extraire tous les rôles à partir de comptes AWS.

    ![Capture d’écran de la page Create policy, avec mise en évidence de JSON](./media/amazon-web-service-tutorial/policy1.png)

    a. Dans **Create policy**, sélectionnez l’onglet **JSON**.

    b. Dans le document de stratégie, ajoutez le code JSON suivant :

    ```json
    {
        "Version": "2012-10-17",
        "Statement": [
            {
                "Effect": "Allow",
                "Action": [
                "iam:ListRoles"
                ],
                "Resource": "*"
            }
        ]
    }
    ```

    c. Sélectionnez **Review policy** (Vérifier la stratégie) pour valider la stratégie.

    ![Capture d’écran de la page Create policy](./media/amazon-web-service-tutorial/policy5.png)

16. Définissez la nouvelle stratégie.

    ![Capture d’écran de la page Create policy, avec mise en évidence des champs Name et Description](./media/amazon-web-service-tutorial/policy2.png)

    a. Pour **Name**, entrez **AzureAD_SSOUserRole_Policy**.

    b. Pour **Description**, entrez **This policy will allow to fetch the roles from AWS accounts** (Cette stratégie permettra de récupérer les rôles à partir de comptes AWS).

    c. Sélectionnez **Créer une stratégie**.

17. Créez un compte d’utilisateur dans le service IAM d’AWS.

    a. Dans la console IAM d’AWS, sélectionnez **Users** (Utilisateurs).

    ![Capture d’écran de la console IAM d’AWS, avec mise en évidence de Users](./media/amazon-web-service-tutorial/policy3.png)

    b. Pour créer un utilisateur, sélectionnez **Add user** (Ajouter un utilisateur).

    ![Capture d’écran du bouton Add user](./media/amazon-web-service-tutorial/policy4.png)

    c. Dans la section **Add user** :

    ![Capture d’écran de la page Add user avec mise en évidence de User name et Access](./media/amazon-web-service-tutorial/adduser1.png)

    * Entrez **AzureADRoleManager** (GestionnaireDeRôlesAzureAD) comme nom d’utilisateur.

    * Pour le type d’accès, sélectionnez **Programmatic access** (Accès par programmation). Ainsi, l’utilisateur peut appeler les API et extraire les rôles du compte AWS.

    * Sélectionnez **Next: Permissions** (Suivant : Autorisations).

18. Créez une stratégie pour cet utilisateur.

    ![Capture d’écran montrant la page Ajouter un utilisateur, où vous pouvez créer une stratégie pour l’utilisateur.](./media/amazon-web-service-tutorial/adduser2.png)

    a. Sélectionnez **Attach existing policies directly** (Attacher directement les stratégies existantes).

    b. Recherchez la stratégie nouvellement créée dans la section du filtre **AzureAD_SSOUserRole_Policy** (Stratégie_RôleUtilisateurAuthentificationUnique_AzureAD).

    c. Sélectionnez la stratégie, puis sélectionnez **Next: Vérification**).

19. Passez en revue la stratégie liée à l’utilisateur attaché.

    ![Capture d’écran de la page Add user, avec mise en évidence de l’élément Create user](./media/amazon-web-service-tutorial/adduser3.png)

    a. Vérifiez le nom d’utilisateur, le type d’accès et la stratégie mappée à l’utilisateur.

    b. Sélectionnez **Create user** (Créer un utilisateur).

20. Téléchargez les informations d’identification d’un utilisateur.

    ![Capture d’écran montrant la page Add user avec un bouton Download csv pour obtenir les informations d’identification de l’utilisateur.](./media/amazon-web-service-tutorial/adduser4.png)

    a. Copiez les **Access key ID** (ID de la clé d’accès) et **Secret access key** (Clé d’accès secrète) de l’utilisateur.

    b. Dans la section d’attribution d’utilisateurs d’Azure AD, entrez ces informations d’identification pour extraire les rôles à partir de la console AWS.

    c. Sélectionnez **Fermer**.

### <a name="how-to-configure-role-provisioning-in-aws-single-account-access"></a>Comment configurer le provisionnement des rôles dans AWS Single-Account Access

1. Dans le portail de gestion Azure AD, dans l’application AWS, accédez à **Provisioning** (Provisionnement).

    ![Capture d’écran de l’application AWS, avec mise en évidence de Provisioning](./media/amazon-web-service-tutorial/provisioning.png)

2. Entrez la clé d’accès et le secret dans les champs **clientsecret** (secret client) et **Secret Token** (Jeton secret) respectivement.

    ![Capture d’écran de la boîte de dialogue Admin Credentials (Informations d’identification d’administrateur)](./media/amazon-web-service-tutorial/provisioning1.png)

    a. Entrez la clé d’accès utilisateur AWS dans le champ **clientsecret**.

    b. Entrez le secret d’utilisateur AWS dans le champ **Jeton secret**.

    c. Sélectionnez **Test Connection** (Tester la connexion).

    d. Enregistrez le paramètre en sélectionnant **Save** (Enregistrer).

3. Dans la section **Settings** (Paramètres), pour **Provisioning Status** (État du provisionnement), sélectionnez **On** (Activé). Ensuite, sélectionnez **Enregistrer**.

    ![Capture d’écran de la section Settings, avec mise en évidence de l’option On](./media/amazon-web-service-tutorial/provisioning2.png)

> [!NOTE]
> Le service de provisionnement importe les rôles uniquement d’AWS vers Azure AD. Ce service ne provisionne pas les utilisateurs et les groupes d’Azure AD dans AWS.

> [!NOTE]
> Après avoir enregistré les informations d’identification de provisionnement, vous devez attendre que le cycle de synchronisation initial s’exécute. La synchronisation prend environ 40 minutes. Vous pouvez voir le statut en bas de la page **Mise en service**, sous **Statut actuel**.

### <a name="create-aws-single-account-access-test-user"></a>Créer un utilisateur de test AWS Single-Account Access

L’objectif de cette section est de créer un utilisateur appelé B.Simon dans AWS Single-Account Access. AWS Single-Account Access ne nécessitant pas de création d’utilisateur dans son système pour l’authentification unique, vous n’avez aucune action à accomplir ici.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

#### <a name="sp-initiated"></a>Lancée par le fournisseur de services :

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion à AWS Single-Account Access, où vous pouvez lancer le flux de connexion.  

* Accédez directement à l’URL de connexion à AWS Single-Account Access pour lancer le flux de connexion.

#### <a name="idp-initiated"></a>Lancée par le fournisseur d’identité :

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors automatiquement connecté à l’instance d’AWS Single-Account Access pour laquelle vous avez configuré l’authentification unique. 

Vous pouvez aussi utiliser Mes applications de Microsoft pour tester l’application dans n’importe quel mode. Si, quand vous cliquez sur la vignette AWS Single-Account Access dans Mes applications, le mode Fournisseur de services est configuré, vous êtes redirigé vers la page de connexion de l’application pour lancer le flux de connexion ; s’il s’agit du mode Fournisseur d’identité, vous êtes automatiquement connecté à l’instance d’AWS Single-Account Access pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).


## <a name="known-issues"></a>Problèmes connus

* L’intégration du provisionnement AWS Single-Account Access ne peut être utilisée que pour se connecter à des points de terminaison cloud publics AWS. L’intégration du provisionnement AWS Single-Account Access ne peut pas être utilisée pour accéder à des environnements gouvernementaux AWS.
 
* Dans la section **Provisionnement**, la sous-section **Mappages** affiche un message « Chargement en cours... » et n’affiche jamais les mappages d’attributs. Le seul workflow de provisionnement pris en charge aujourd’hui est l’importation des rôles à partir d’AWS dans Azure AD pour la sélection lors de l’affectation d’un groupe ou utilisateur. Pour cela, les mappages d’attributs sont prédéterminés et ne sont pas configurables.

* La section **Provisionnement** prend uniquement en charge l’entrée d’un ensemble d’informations d’identification pour un locataire AWS à la fois. Tous les rôles importés sont écrits dans la propriété `appRoles` de l’[objet `servicePrincipal`](/graph/api/resources/serviceprincipal) d’Azure AD pour le locataire AWS.

  Plusieurs locataires AWS (représentés par `servicePrincipals`) peuvent être ajoutés à Azure AD à partir de la galerie à des fins de provisionnement. Toutefois, il existe un problème connu, lié à l’impossibilité d’écrire automatiquement tous les rôles importés à partir des différents `servicePrincipals` AWS utilisés pour le provisionnement dans le `servicePrincipal` unique utilisé pour l’authentification unique.

  En guise de solution de contournement, vous pouvez utiliser l’[API Microsoft Graph](/graph/api/resources/serviceprincipal) pour extraire tous les `appRoles` importés dans chaque `servicePrincipal` AWS où le provisionnement est configuré. Vous pouvez ensuite ajouter ces chaînes de rôle au `servicePrincipal` AWS où l’authentification unique est configurée.

* Les rôles doivent satisfaire aux exigences suivantes pour pouvoir être importés à partir d’AWS dans Azure AD :

  * Les rôles doivent avoir un seul fournisseur SAML défini dans AWS
  * La longueur combinée du Nom de ressource Amazon (ARN) du rôle et de l’ARN du fournisseur SAML associé doit être inférieure à 240 caractères.

## <a name="change-log"></a>Journal des modifications

* 12/01/2020 : la taille limite des rôles a été portée à 239 caractères (contre 119 auparavant).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré AWS Single-Account Access, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).

[11]: ./media/amazon-web-service-tutorial/ic795031.png
[12]: ./media/amazon-web-service-tutorial/ic795032.png
[13]: ./media/amazon-web-service-tutorial/ic795033.png
[14]: ./media/amazon-web-service-tutorial/ic795034.png
[15]: ./media/amazon-web-service-tutorial/ic795035.png
[16]: ./media/amazon-web-service-tutorial/ic795022.png
[17]: ./media/amazon-web-service-tutorial/ic795023.png
[18]: ./media/amazon-web-service-tutorial/ic795024.png
[19]: ./media/amazon-web-service-tutorial/ic795025.png
[32]: ./media/amazon-web-service-tutorial/ic7950251.png
[33]: ./media/amazon-web-service-tutorial/ic7950252.png
[35]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning.png
[34]: ./media/amazon-web-service-tutorial/ic7950253.png
[36]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials.png
[37]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials_continue.png
[38]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_createnewaccesskey.png
[39]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_automatic.png
[40]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_testconnection.png
[41]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_on.png
