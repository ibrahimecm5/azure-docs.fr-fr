---
title: 'Tutoriel : Intégration d’Azure Active Directory à Amazon Web Services pour connecter plusieurs comptes | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure AD et Amazon Web Services (ancien tutoriel).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/24/2020
ms.author: jeedes
ms.openlocfilehash: 90890843c5377f22647634fad158fb7e26df17f5
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132288348"
---
# <a name="tutorial-azure-active-directory-integration-with-amazon-web-services"></a>Tutoriel : Intégration d’Azure Active Directory à Amazon Web Services

Dans ce tutoriel, vous découvrez comment intégrer Azure Active Directory (Azure AD) à Amazon Web Services (AWS) (ancien tutoriel).

Cette intégration offre les avantages suivants :

- Vous pouvez contrôler dans Azure AD qui a accès à AWS.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à AWS en utilisant l’authentification unique avec leurs comptes Azure AD.
- Vous pouvez centraliser la gestion de vos comptes à un seul emplacement : le Portail Azure.

![Diagramme de l’intégration d’Azure AD à AWS.](./media/aws-multi-accounts-tutorial/amazonwebservice.png)

> [!NOTE]
> Nous vous recommandons de _ne pas_ connecter une application AWS à tous vos comptes AWS. Au lieu de cela, nous vous recommandons d’utiliser [l’intégration de l’authentification unique Azure AD à AWS](./amazon-web-service-tutorial.md) pour configurer plusieurs instances de votre compte AWS à plusieurs instances d’applications AWS dans Azure AD.

Nous vous recommandons de _ne pas_ connecter une application AWS à tous vos comptes AWS pour les raisons suivantes :

- Utilisez cette approche seulement si vous n’avez qu’un petit nombre de comptes et de rôles AWS, car ce modèle n’est pas scalable au fil de l’augmentation du nombre de comptes AWS et de rôles qui y sont définis. L’approche n’utilise pas la fonctionnalité d’importation de rôles AWS avec le provisionnement d’utilisateurs Azure AD : vous devez donc ajouter, mettre à jour ou supprimer manuellement les rôles.

- Vous devez utiliser l’approche Microsoft Graph Explorer pour appliquer un correctif à tous les rôles à l’application. Nous vous déconseillons d’utiliser l’approche du fichier manifeste.

- Des clients ont signalé qu’après l’ajout d’environ 1 200 rôles d’application pour une même application AWS, chaque opération effectuée sur l’application commençait par lever les erreurs liées à la taille. Il existe une limite de taille inconditionnelle sur l’objet d’application.

- Vous devez mettre à jour manuellement les rôles quand ils sont ajoutés à un des comptes. Il s’agit malheureusement d’une approche _par remplacement_ et non pas d’une approche _par ajout_. En outre, si le nombre de vos comptes augmente, ceci devient une relation _n_ &times; _n_ avec les comptes et les rôles.

- Tous les comptes AWS utilisent le même fichier XML de métadonnées de fédération. Au moment de la substitution de certificat, la mise à jour du certificat sur tous les comptes AWS en même temps peut être une tâche énorme.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD avec AWS, vous avez besoin des éléments suivants :

- Un abonnement Azure AD Si vous n’avez pas d’abonnement Azure AD, vous pouvez obtenir un [essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).
- Un abonnement AWS pour lequel l’authentification unique est activée.

> [!NOTE]
> Nous ne vous recommandons pas de tester les étapes de ce tutoriel dans un environnement de production, sauf si c’est vraiment nécessaire.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

AWS prend en charge l’authentification unique lancée par le fournisseur de services et par le fournisseur d’identité.

## <a name="add-aws-from-the-gallery"></a>Ajouter AWS à partir de la galerie

Pour configurer l’intégration d’AWS à Azure AD, vous ajoutez AWS depuis la galerie à votre liste d’applications SaaS (Software as a Service) managées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire, ou avec un compte personnel Microsoft.
1. Dans le volet gauche, sélectionnez le service Azure AD avec lequel vous voulez travailler.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Amazon Web Services** dans la zone de recherche.
1. Dans la liste des résultats, sélectionnez **Amazon Web Services**, puis ajoutez l’application. Après quelques secondes, l’application est ajoutée à votre locataire.

1. Accédez au volet **Propriétés**, puis copiez la valeur qui est affichée dans la zone **ID d’objet**.

   ![Capture d’écran de la zone ID d’objet dans le volet Propriétés.](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-properties.png)

## <a name="configure-and-test-azure-ad-sso"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous configurez et vous testez l’authentification unique Azure AD avec AWS, avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir quel est l’utilisateur AWS correspondant à l’utilisateur Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et le même utilisateur dans AWS doit être établie.

Dans AWS, affectez la valeur du **nom d’utilisateur** d’Azure AD comme valeur de **Username** (Nom d’utilisateur) d’AWS pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec AWS, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
1. **[Configurez l’authentification unique AWS](#configure-aws-sso)** pour configurer les paramètres de l’authentification unique côté application.
1. **[Tester l’authentification SSO](#test-sso)** , pour vérifier que la configuration fonctionne.

### <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure et vous la configurez dans votre application AWS en procédant comme suit :

1. Dans le portail Azure, dans le volet gauche de la page d’intégration de l’application **Amazon Web Services (AWS)** , sélectionnez **Authentification unique**.

   ![Capture d’écran de la commande « Authentification unique ».](common/select-sso.png)

1. Dans le volet **Sélectionner une méthode d’authentification unique**, sélectionnez le mode **SAML/WS-Fed** pour activer l’authentification unique.

   ![Capture d’écran du volet « Sélectionner une méthode d’authentification unique ».](common/select-saml-option.png)

1. Dans le volet **Configurer l’authentification unique avec SAML**, sélectionnez le bouton **Modifier** (icône de crayon).

   ![Capture d’écran du bouton Modifier dans le volet « Configurer l’authentification unique avec SAML ».](common/edit-urls.png)

1. Le volet **Configuration SAML de base** s’ouvre. Ignorez cette section, cat l’application est préintégrée à Azure. Sélectionnez **Enregistrer**.

   L’application AWS attend les assertions SAML dans un format spécifique. Vous pouvez gérer les valeurs de ces attributs à partir de la section **Attributs utilisateur et revendications** dans la page **Intégration de l’application**.

1. Dans le volet **Configurer l’authentification unique avec SAML**, sélectionnez le bouton **Modifier**.

   ![Capture d’écran du bouton Modifier dans le volet « Attributs utilisateur ».](common/edit-attribute.png)

1. Dans la section **Revendications de l’utilisateur** du volet **Attributs utilisateur**, configurez les attributs du jeton SAML en utilisant les valeurs du tableau suivant :

   | Nom            | Attribut source                                                            | Espace de noms                                |
   | --------------- | --------------------------------------------------------------------------- | ---------------------------------------- |
   | RoleSessionName | user.userprincipalname                                                      | `https://aws.amazon.com/SAML/Attributes` |
   | Role            | user.assignedroles                                                          | `https://aws.amazon.com/SAML/Attributes` |
   | SessionDuration | « Indiquez une valeur comprise entre 900 secondes (15 minutes) et 43 200 secondes (12 heures) » | `https://aws.amazon.com/SAML/Attributes` |

   a. Sélectionnez **Ajouter une nouvelle revendication** puis, dans le volet **Gérer les revendications des utilisateurs**, effectuez les opérations suivantes :

   ![Capture d’écran des boutons « Ajouter une nouvelle revendication » et « Gérer les revendications des utilisateurs »dans le volet « Revendications de l’utilisateur ».](common/new-save-attribute.png)

   ![Capture d’écran du volet « Gérer les revendications des utilisateurs ».](common/new-attribute-details.png)

   b. Dans la zone de texte **Nom**, entrez le nom de l’attribut.

   c. Dans la zone de texte **Espace de noms**, entrez une valeur d’espace de noms.

   d. Pour la **Source**, sélectionnez **Attribut**.

   e. Dans la liste déroulante **Attribut de la source**, sélectionnez l’attribut.

   f. Sélectionnez **OK**, puis **Enregistrer**.

   > [!NOTE]
   > Pour plus d’informations sur les rôles dans Azure AD, consultez [Ajouter des rôles d’application à votre application et les recevoir dans le jeton](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui).

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, sélectionnez **Télécharger** pour télécharger le fichier XML de métadonnées de fédération, puis enregistrez-le sur votre ordinateur.

   ![Capture d’écran du lien de téléchargement « XML de métadonnées de fédération » dans le volet « Certificat de signature SAML ».](common/metadataxml.png)

### <a name="configure-aws-sso"></a>Configurer l’authentification unique AWS

1. Dans une nouvelle fenêtre de navigateur, connectez-vous à votre site d’entreprise AWS en tant qu’administrateur.

1. Sélectionnez l’icône **Accueil AWS**.

   ![Capture d’écran de l’icône « Accueil AWS ».][11]

1. Dans le volet **AWS services** (Services AWS), sous **Security, Identity & Compliance** (Sécurité, identité et conformité), sélectionnez **IAM (Identity & Access Management)** (Gestion des identités et des accès).

   ![Capture d’écran du lien « Identity and Access Management » (Gestion des identités et des accès) dans le volet « AWS Services ».][12]

1. Dans le volet gauche, sélectionnez **Identity Providers** (Fournisseurs d’identités), puis sélectionnez **Create Provider** (Créer un fournisseur).

   ![Capture d’écran du bouton « Create Provider ».][13]

1. Dans le volet **Configure Provider** (Configurer le fournisseur), effectuez les étapes suivantes :

   ![Capture d’écran du volet Configure Provider.][14]

   a. Dans la liste déroulante **Provider Type** (Type de fournisseur), sélectionnez **SAML**.

   b. Dans la zone **Provider Name**, entrez un nom de fournisseur (par exemple _WAAD_).

   c. En regard de la zone **Metadata Document** (Document de métadonnées), sélectionnez **Choose File** (Choisir un fichier) pour charger votre fichier XML de métadonnées de fédération sur le portail Azure.

   d. Sélectionnez **Étape suivante**.

1. Dans la page **Verify Provider Information** (Vérifier les informations du fournisseur), sélectionnez **Create** (Créer).

   ![Capture d’écran du volet « Verify Provider Information ».][15]

1. Dans le volet gauche, sélectionnez **Roles** (Rôles), puis sélectionnez **Create role** (Créer un rôle).

   ![Capture d’écran du bouton « Create role » dans le volet Roles.][16]

   > [!NOTE]
   > La longueur combinée de l’ARN (Amazon Resource Name) du rôle et de l’ARN du fournisseur SAML pour un rôle importé doit avoir au plus 240 caractères.

1. Dans la page **Create role**, effectuez les étapes suivantes :

   ![Capture d’écran du bouton de l’entité approuvée « SAML 2.0 federation » (Fédération SAML 2.0) dans la page « Create role ».][19]

   a. Dans **Select type of trusted entity** (Sélectionnez un type d’entité approuvé), Sélectionnez **SAML 2.0 federation** (Fédération SAML 2.0).

   b. Sous **Choose a SAML 2.0 Provider** (Choisir un fournisseur SAML 2.0), sélectionnez le fournisseur SAML que vous avez créé précédemment (par exemple _WAAD_)

   c. Sélectionnez **Allow programmatic and AWS Management Console access** (Autoriser l’accès par programme à AWS Management Console).

   d. Sélectionnez **Suivant : Autorisations**.

1. Dans la zone de recherche, entrez **Administrator Access** (Accès administrateur), cochez la case **AdministratorAccess**, puis sélectionnez **Suivant : Balises**.

   ![Capture d’écran de la liste « Policy name » (Nom de la stratégie) avec la stratégie AdministratorAccess sélectionnée.](./media/aws-multi-accounts-tutorial/administrator-access.png)

1. Dans le volet **Add tags (optional)** (Ajouter des balises (facultatif)), effectuez les étapes suivantes :

   ![Capture d’écran du volet « Add tags (optional) ».](./media/aws-multi-accounts-tutorial/config2.png)

   a. Dans la zone **Key** (Clé), entrez le nom de la clé (par exemple _Azureadtest_).

   b. Dans la zone **Value (optional)** (Valeur (facultatif)), entrez la valeur de la clé en utilisant le format suivant : `<accountname-aws-admin>`. Le nom du compte doit être entièrement en lettres minuscules.

   c. Sélectionnez **Suivant : Vérification**).

1. Dans le volet **Review** (Passer en revue), procédez comme suit :

   ![Capture d’écran du volet Review, avec les zones « Role name » (Nom du rôle) et « Role description » (Description du rôle) mises en évidence.][34]

   a. Dans la zone de texte **Role name**, entrez la valeur au format suivant : `<accountname-aws-admin>`.

   b. Dans la zone **Role description**, entrez la valeur que vous avez utilisée pour le nom du rôle.

   c. Sélectionnez **Create role**.

   d. Créez autant de rôles que nécessaire, puis mappez-les au fournisseur d’identité.

   > [!NOTE]
   > De même, vous pouvez créer d’autres rôles, comme _accountname-finance-admin_, _accountname-read-only-user_, _accountname-devops-user_ ou _accountname-tpm-user_ en y attachant une stratégie différente. Vous pouvez changer ces stratégies de rôles ultérieurement, selon les spécifications propres à chaque compte AWS. Il est judicieux de conserver les mêmes stratégies pour chaque rôle entre les comptes AWS.

1. Veillez à prendre note de l’ID de compte pour le compte AWS dans les propriétés du volet Amazon Elastic Compute Cloud (Amazon EC2) ou dans le tableau de bord IAM, comme illustré dans la capture d’écran suivante :

   ![Capture d’écran montrant l’endroit où l’ID de compte est affiché dans le volet « Identity and Access Management ».](./media/aws-multi-accounts-tutorial/aws-accountid.png)

1. Connectez-vous au portail Azure et accédez à **Groupes**.

1. Créez des groupes portant le même nom que ceux des rôles IAM créés précédemment, puis prenez note de la valeur de la zone **ID d’objet** de chacun de ces nouveaux groupes.

   ![Capture d’écran des détails du compte pour un nouveau groupe.](./media/aws-multi-accounts-tutorial/copy-objectids.png)

1. Déconnectez-vous du compte AWS actif, puis connectez-vous avec un autre compte pour lequel vous voulez configurer l’authentification unique avec Azure AD.

1. Une fois que vous avez créé tous les rôles dans les comptes, ils apparaissent dans la liste **Roles** pour ces comptes.

   ![Capture d’écran de la liste des rôles, montrant le nom, la description et les entités approuvées de chaque rôle.](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-listofroles.png)

Vous devez ensuite capturer tous les ARN de rôle et les entités approuvées pour tous les rôles de tous les comptes. Vous devez les mapper manuellement à l’application Azure AD. Pour ce faire :

1. Sélectionnez chaque rôle pour copier les valeurs de son ARN de rôle et ses entités approuvées. Ces valeurs sont nécessaires pour tous les rôles que vous allez créer dans Azure AD.

   ![Capture d’écran du volet Summary (Récapitulatif) pour les ARN et les entités approuvées d’un rôle.](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-role-summary.png)

1. Répétez l’étape précédente pour tous les rôles de tous les comptes, puis stockez-les dans un fichier texte au format suivant : `<Role ARN>,<Trusted entities>`.

1. Ouvrez l’[Explorateur Microsoft Graph](https://developer.microsoft.com/graph/graph-explorer), puis effectuez les étapes suivantes :

   a. Connectez-vous au site Explorateur Microsoft Graph avec les informations d’identification d’administrateur global ou de coadministrateur pour votre locataire.

   b. Vous devez disposer des autorisations suffisantes pour créer les rôles. Sélectionnez **Modifier les autorisations**.

   ![Capture d’écran du lien « Modifier les autorisations » dans le volet d’authentification de Microsoft Graph Explorer.](./media/aws-multi-accounts-tutorial/graph-explorer-new9.png)

   c. Dans la liste des autorisations, si vous n’avez pas déjà les autorisations montrées dans la capture d’écran suivante, sélectionnez chacune d’elle, puis sélectionnez **Modifier les autorisations**.

   ![Capture d’écran de la liste des autorisations de Microsoft Graph Explorer, avec les autorisations appropriées mises en évidence.](./media/aws-multi-accounts-tutorial/graph-explorer-new10.png)

   d. Reconnectez-vous à l’Explorateur Graph, et acceptez les conditions d’utilisation du site.

   e. En haut du volet, sélectionnez **GET** pour la méthode, sélectionnez **bêta** pour la version puis, dans la zone de requête, entrez un des éléments suivants :

   - Pour extraire tous les principaux de service de votre locataire, utilisez `https://graph.microsoft.com/beta/servicePrincipals`.
   - Si vous utilisez plusieurs annuaires, utilisez `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`, qui contient votre domaine principal.

   ![Capture d’écran du volet « Corps de la requête » de la requête de l’Explorateur Microsoft Graph.](./media/aws-multi-accounts-tutorial/graph-explorer-new1.png)

   f. Dans la liste des principaux de service, obtenez celui que vous devez modifier.

   Vous pouvez également utiliser les touches Ctrl+F pour rechercher tous les principaux de service listés dans l’application. Pour obtenir un principal de service spécifique, incluez dans la requête l’ID d’objet du principal de service que vous avez copié précédemment depuis le volet Propriétés d’Azure AD, comme illustré ici :

   `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.

   ![Capture d’écran montrant une requête de principal de service qui inclut l’ID d’objet.](./media/aws-multi-accounts-tutorial/graph-explorer-new2.png)

   g. Extrayez la propriété appRoles à partir de l’objet du principal du service.

   ![Capture d’écran du code pour extraire la propriété appRoles de l’objet du principal de service.](./media/aws-multi-accounts-tutorial/graph-explorer-new3.png)

   h. À présent, vous devez générer de nouveaux rôles pour votre application.

   i. Le code JSON suivant est un exemple d’objet appRoles. Créez un objet similaire pour ajouter les rôles que vous voulez pour votre application.

   ```
   {
   "appRoles": [
       {
           "allowedMemberTypes": [
               "User"
           ],
           "description": "msiam_access",
           "displayName": "msiam_access",
           "id": "7dfd756e-8c27-4472-b2b7-38c17fc5de5e",
           "isEnabled": true,
           "origin": "Application",
           "value": null
       },
       {
           "allowedMemberTypes": [
               "User"
           ],
           "description": "Admin,WAAD",
           "displayName": "Admin,WAAD",
           "id": "4aacf5a4-f38b-4861-b909-bae023e88dde",
           "isEnabled": true,
           "origin": "ServicePrincipal",
           "value": "arn:aws:iam::12345:role/Admin,arn:aws:iam::12345:saml-provider/WAAD"
       },
       {
           "allowedMemberTypes": [
               "User"
           ],
           "description": "Auditors,WAAD",
           "displayName": "Auditors,WAAD",
           "id": "bcad6926-67ec-445a-80f8-578032504c09",
           "isEnabled": true,
           "origin": "ServicePrincipal",
           "value": "arn:aws:iam::12345:role/Auditors,arn:aws:iam::12345:saml-provider/WAAD"
       }    ]
   }
   ```

   > [!Note]
   > Vous pouvez ajouter de nouveaux rôles seulement une fois que vous avez ajouté _msiam_access_ pour l’opération de correction. Vous pouvez aussi ajouter autant de rôles que vous le souhaitez selon les besoins de votre organisation. Azure AD envoie la _valeur_ de ces rôles comme valeur de revendication dans la réponse SAML.

   j. Dans l’Explorateur Microsoft Graph, changez la méthode de **GET** en **PATCH**. Corrigez l’objet du principal de service avec les rôles souhaités en mettant à jour la propriété appRoles comme celle qui est montrée dans l’exemple précédent. Sélectionnez **Exécuter la requête** pour exécuter l’opération de correction. Un message de réussite confirme la création du rôle pour votre application AWS.

   ![Capture d’écran du volet Explorateur Microsoft Graph, avec la méthode changée en PATCH.](./media/aws-multi-accounts-tutorial/graph-explorer-new11.png)

1. Une fois le principal de service corrigé avec d’autres rôles, vous pouvez affecter des utilisateurs/groupes à leurs rôles respectifs. Vous faites cela dans le portail Azure en accédant à l’application AWS, puis en sélectionnant l’onglet **Utilisateurs et groupes** en haut.

1. Nous vous recommandons de créer un groupe pour chaque rôle AWS afin de pouvoir attribuer ce rôle particulier dans le groupe. Ce mappage un-à-un signifie qu’un groupe est affecté à un rôle. Vous pouvez ensuite ajouter des membres qui appartiennent à ce groupe.

1. Une fois que vous avez créé les groupes, sélectionnez le groupe et affectez-le à l’application.

   ![Capture d’écran du volet « Utilisateurs et groupes ».](./media/aws-multi-accounts-tutorial/graph-explorer-new5.png)

   > [!Note]
   > Les groupes imbriqués ne sont pas pris en charge quand vous affectez des groupes.

1. Pour attribuer le rôle au groupe, sélectionnez le rôle, puis sélectionnez **Attribuer**.

   ![Capture d’écran du volet « Ajouter une attribution ».](./media/aws-multi-accounts-tutorial/graph-explorer-new6.png)

   > [!Note]
   > Une fois que vous avez attribué les rôles, vous pouvez les visualiser en actualisant votre session du portail Azure.

### <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous testez la configuration de l’authentification unique Azure AD en utilisant Microsoft Mes applications.

Quand vous sélectionnez la vignette **AWS** dans Mes applications, la page de l’application AWS s’ouvre avec une option pour sélectionner le rôle.

![Capture d’écran de la page AWS pour le test d’authentification unique.](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-test-screen.png)

Vous pouvez également vérifier la réponse SAML pour afficher les rôles transmis en tant que revendications.

![Capture d’écran de la réponse SAML.](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-test-saml.png)

Pour plus d’informations sur Mes applications, consultez [Se connecter et démarrer des applications à partir du portail Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré AWS, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. Pour plus d’informations, consultez [Découvrir comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).

<!--Image references-->

[11]: ./media/aws-multi-accounts-tutorial/ic795031.png
[12]: ./media/aws-multi-accounts-tutorial/ic795032.png
[13]: ./media/aws-multi-accounts-tutorial/ic795033.png
[14]: ./media/aws-multi-accounts-tutorial/ic795034.png
[15]: ./media/aws-multi-accounts-tutorial/ic795035.png
[16]: ./media/aws-multi-accounts-tutorial/ic795022.png
[17]: ./media/aws-multi-accounts-tutorial/ic795023.png
[18]: ./media/aws-multi-accounts-tutorial/ic795024.png
[19]: ./media/aws-multi-accounts-tutorial/ic795025.png
[32]: ./media/aws-multi-accounts-tutorial/ic7950251.png
[33]: ./media/aws-multi-accounts-tutorial/ic7950252.png
[35]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-provisioning.png
[34]: ./media/aws-multi-accounts-tutorial/config3.png
[36]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-securitycredentials.png
[37]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-securitycredentials-continue.png
[38]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-createnewaccesskey.png
[39]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-provisioning-automatic.png
[40]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-provisioning-testconnection.png
[41]: ./media/aws-multi-accounts-tutorial/
