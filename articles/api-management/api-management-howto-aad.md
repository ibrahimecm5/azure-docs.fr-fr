---
title: Autoriser des comptes de développeurs avec Azure Active Directory
titleSuffix: Azure API Management
description: Découvrez comment autoriser des utilisateurs à l’aide d’Azure Active Directory dans Gestion des API.
services: api-management
documentationcenter: API Management
author: dlepow
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/20/2021
ms.author: danlep
ms.openlocfilehash: fd45ad3a77ddfe9bea46c9c816b67be0c2cf1fac
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131579157"
---
# <a name="authorize-developer-accounts-by-using-azure-active-directory-in-azure-api-management"></a>Autoriser des comptes de développeurs à l’aide d’Azure Active Directory dans Gestion des API Azure

Dans cet article, vous allez apprendre à :
> [!div class="checklist"]
> * Activer l’accès au portail des développeurs pour les utilisateurs à partir d’Azure Active Directory (Azure AD).
> * Gérer des groupes d’utilisateurs Azure AD en ajoutant des groupes externes qui contiennent les utilisateurs.

## <a name="prerequisites"></a>Prérequis

- Suivez le guide de démarrage rapide [Créer une instance du service Gestion des API Azure](get-started-create-service-instance.md).
- [Importez et publiez](import-and-publish.md) une instance Gestion des API Azure.

[!INCLUDE [premium-dev-standard.md](../../includes/api-management-availability-premium-dev-standard.md)]

## <a name="authorize-developer-accounts-by-using-azure-ad"></a>Autoriser des comptes de développeurs à l’aide d’Azure AD

1. Connectez-vous au [portail Azure](https://portal.azure.com). 
1. Sélectionnez ![Icône flèche](./media/api-management-howto-aad/arrow.png).
1. Recherchez et sélectionnez **Services Gestion des API**.
1. Sélectionnez votre instance de service Gestion des API.
1. Sous **Portail des développeurs**, sélectionnez **Identités**.
1. Sélectionnez **+ Ajouter** en haut pour ouvrir le volet **Ajouter un fournisseur d’identité** à droite.
1. Sous **Type**, sélectionnez **Azure Active Directory** dans le menu déroulant.
    * Une fois cette option sélectionnée, vous pouvez entrer d’autres informations nécessaires. 
    * Les informations incluent l’**ID client** et la **clé secrète client**. 
    * Voir plus d’informations sur ces contrôles plus loin dans l’article.
1. Enregistrez l’**URL de redirection** pour plus tard.
    
    :::image type="content" source="media/api-management-howto-aad/api-management-with-aad001.png" alt-text="Ajouter un fournisseur d’identité dans Portail Azure":::

    > [!NOTE]
    > Il existe deux URL de redirection :<br/>
    > * L’**URL de redirection** pointe vers le portail des développeurs le plus récent de Gestion des API.
    > * L’**URL de redirection (portail déconseillé)** pointe vers le portail des développeurs déconseillé de Gestion des API.
    >
    > Nous vous recommandons d’utiliser l’URL de redirection du portail des développeurs le plus récent.
   
1. Dans votre navigateur, ouvrez le portail Azure dans un nouvel onglet. 
1. Accédez à [Inscriptions d’applications](https://go.microsoft.com/fwlink/?linkid=2083908) pour inscrire une application dans Active Directory.
1. Sélectionnez **Nouvelle inscription**. Sur la page **Inscrire une application**, définissez les valeurs comme suit :
    
    * Choisissez un nom explicite pour le champ **Nom**. Exemple : *developer-portal*
    * Définissez **Types de comptes pris en charge** sur **Comptes dans cet annuaire organisationnel**. 
    * Définissez l’**URI de redirection** sur la valeur que vous avez enregistrée à l’étape 9. 
    * Sélectionnez **Inscription**. 

1.  Après avoir inscrit l’application, copiez l’**ID (client) d’application** à partir de la page **Vue d’ensemble**. 
1. Basculez sur l’onglet de navigateur avec votre instance Gestion des API. 
1. Dans la fenêtre **Ajouter le fournisseur d’identité**, collez la valeur de **l’ID (client) d’application** dans la zone **ID client**.
1. Basculez sur l’onglet du navigateur avec l’inscription d’application.
1. Sélectionnez l’inscription d’application appropriée.
1. Sous la section **Gérer** du menu latéral, sélectionnez **Certificats et secrets**. 
1. Dans la page **Certificats et secrets**, sélectionnez le bouton **Nouvelle clé secrète client** sous **Clés secrètes client**. 
    * Entrez une **Description**.
    * Sélectionnez une option pour **Date d’expiration**.
    * Choisissez **Ajouter**. 
1. Copiez la **valeur du secret** du client avant de quitter la page. Vous en aurez besoin ultérieurement. 
1. Sous **Gérer** dans le menu latéral, sélectionnez **Authentification**.
1. Sous les sections **Flux d’octroi implicite et flux hybride**, cochez la case **Jetons d’ID**.
1. Basculez sur l’onglet de navigateur avec votre instance Gestion des API. 
1. Collez le secret dans le champ **Clé secrète client** dans le volet **Ajouter un fournisseur d’identité**.

    > [!IMPORTANT]
    > Mettez à jour la **clé secrète client** avant l’expiration de la clé. 

1. Dans le champ **Locataires autorisés** du volet **Ajouter un fournisseur d’identité**, indiquez les domaines des instances Azure AD auxquels vous souhaitez accorder l’accès aux API de l’instance de service Gestion des API. 
    * Vous pouvez séparer plusieurs domaines par des sauts de ligne, des espaces ou des virgules.

    > [!NOTE]
    > Vous pouvez spécifier plusieurs domaines dans la section **Locataires autorisés**. Un administrateur général doit accorder à l’application l’accès aux données du répertoire avant que les utilisateurs puissent se connecter à partir d’un domaine différent du domaine d’inscription d’origine de l’application. Pour accorder l’autorisation, l’administrateur général doit :
    > 1. Accédez à `https://<URL of your developer portal>/aadadminconsent` (par exemple, `https://contoso.portal.azure-api.net/aadadminconsent`).
    > 1. Entrer le nom de domaine du locataire Azure AD auquel il souhaite accorder l’accès.
    > 1. Sélectionnez **Envoyer**. 

1.  Après avoir spécifié la configuration désirée, sélectionnez **Ajouter**.

Une fois les modifications enregistrées, les utilisateurs de l’instance Azure AD spécifiée peuvent [se connecter au portail des développeurs en utilisant un compte Azure AD](#log_in_to_dev_portal).

## <a name="add-an-external-azure-ad-group"></a>Ajouter un groupe Azure AD externe

Maintenant que vous avez activé l’accès pour les utilisateurs d’un locataire Azure AD, vous pouvez :
* Ajouter des groupes Azure AD dans Gestion des API. 
* Contrôler la visibilité des produits à l’aide de groupes Azure AD.

Suivez ces étapes pour accorder :
* L’autorisation d’application `Directory.Read.All` pour l’API Microsoft Graph et l’API Graph Azure Active Directory.
* L’autorisation déléguée `User.Read` pour l’API Microsoft Graph. 

1. Mettez à jour les 3 premières lignes du script PowerShell suivant pour qu’elles correspondent à votre environnement et exécutez-le. 
   ```powershell
   $subId = "Your Azure subscription ID" #e.g. "1fb8fadf-03a3-4253-8993-65391f432d3a"
   $tenantId = "Your Azure AD Tenant or Organization ID" #e.g. 0e054eb4-e5d0-43b8-ba1e-d7b5156f6da8"
   $appObjectID = "Application Object ID that has been registered in AAD" #e.g. "2215b54a-df84-453f-b4db-ae079c0d2619"
   #Login and Set the Subscription
   az login
   az account set --subscription $subId
   #Assign the following permissions: Microsoft Graph Delegated Permission: User.Read, Microsoft Graph Application Permission: Directory.ReadAll,  Azure Active Directory Graph Application Permission: Directory.ReadAll (legacy)
   az rest --method PATCH --uri "https://graph.microsoft.com/v1.0/$($tenantId)/applications/$($appObjectID)" --body "{'requiredResourceAccess':[{'resourceAccess': [{'id': 'e1fe6dd8-ba31-4d61-89e7-88639da4683d','type': 'Scope'},{'id': '7ab1d382-f21e-4acd-a863-ba3e13f7da61','type': 'Role'}],'resourceAppId': '00000003-0000-0000-c000-000000000000'},{'resourceAccess': [{'id': '5778995a-e1bf-45b8-affa-663a9f3f4d04','type': 'Role'}], 'resourceAppId': '00000002-0000-0000-c000-000000000000'}]}"
   ```
2. Déconnectez-vous et reconnectez-vous au portail Azure.
3. Accédez à la page d’inscription d’application pour l’application que vous avez inscrite dans [la section précédente](#authorize-developer-accounts-by-using-azure-ad). 
4. Cliquez sur **Autorisations d’API**. Vous devez voir les autorisations accordées par le script PowerShell à l’étape 1. 
5. Sélectionnez **Accorder un consentement administrateur pour {tenantname}** afin d’accorder l’accès à tous les utilisateurs de ce répertoire. 

Vous pouvez maintenant ajouter des groupes Azure AD externes à partir de l’onglet **Groupes** de votre instance Gestion des API.

1. Sous **Portail des développeurs** dans le menu latéral, sélectionnez **Groupes**.
2. Sélectionnez le bouton **Ajouter un groupe Azure AD**.

   ![Bouton « Ajouter groupe AAD »](./media/api-management-howto-aad/api-management-with-aad008.png)
1. Sélectionnez le **locataire** dans la liste déroulante. 
2. Recherchez et sélectionnez le groupe que vous voulez ajouter.
3. Appuyez sur le bouton **Sélectionner**.

Une fois que vous avez ajouté un groupe Azure AD externe, vous pouvez passer en revue ses propriétés et les configurer : 
1. Sélectionnez le nom du groupe sous l’onglet **Groupes**. 
2. Modifiez les informations **Nom** et **Description** du groupe.
 
Les utilisateurs de l’instance Azure AD configurée peuvent désormais :
* Se connecter au portail des développeurs. 
* Voir tous les groupes pour lesquels ils disposent d’une visibilité et s’y abonner.

> [!NOTE]
> Pour en savoir plus sur la différence entre les types d’**autorisations déléguées** et de **permissions d’application**, consultez l’article [Autorisations et consentement dans la plateforme d’identités Microsoft](../active-directory/develop/v2-permissions-and-consent.md#permission-types).

## <a name="developer-portal-add-azure-ad-account-authentication"></a><a id="log_in_to_dev_portal"></a> Portail des développeurs : Ajouter l’authentification de compte Azure AD

Dans le portail des développeurs, vous pouvez vous connecter avec Azure AD à l’aide du widget **Bouton de connexion : OAuth** inclus sur la page de connexion du contenu par défaut du portail des développeurs.

Bien qu’un nouveau compte soit automatiquement créé lorsqu’un nouvel utilisateur se connecte avec Azure AD, vous pouvez envisager d’ajouter le même widget à la page d’inscription. Le widget **Formulaire d’inscription : OAuth** représente un formulaire utilisé pour s’inscrire à l’aide d’OAuth.

> [!IMPORTANT]
> Vous devez [republier le portail](api-management-howto-developer-portal-customize.md#publish) pour que les modifications apportées à Azure AD prennent effet.

## <a name="legacy-developer-portal-how-to-sign-in-with-azure-ad"></a>Portail des développeurs hérité : Comment se connecter avec Azure AD

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

Pour vous connecter au portail des développeurs à l’aide d’un compte Azure AD que vous avez configuré dans les sections précédentes :

1. Ouvrez une nouvelle fenêtre de navigateur à l’aide de l’URL de connexion provenant de la configuration de l’application Active Directory. 
2. Sélectionnez **Azure Active Directory**.

   ![page de connexion][api-management-dev-portal-signin]

1. Entrez les informations d’identification de l’un des utilisateurs dans Azure AD.
2. Sélectionnez **Connexion**.

   ![Connexion avec un nom d’utilisateur et un mot de passe][api-management-aad-signin]

1. Si un formulaire d’inscription vous est proposé, renseignez les informations supplémentaires requises. 
2. Sélectionnez **S’inscrire**.

   ![Bouton S’inscrire sur le formulaire d’inscription][api-management-complete-registration]

Votre utilisateur est maintenant inscrit auprès du portail des développeurs pour votre instance de service Gestion des API.

![Portail des développeurs une fois l’inscription terminée][api-management-registration-complete]

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [protéger votre serveur principal d’API web dans Gestion des API en utilisant l’autorisation OAuth 2.0 avec Azure AD](./api-management-howto-protect-backend-with-aad.md).
- En savoir plus sur [Azure Active Directory et OAuth2.0](../active-directory/develop/authentication-vs-authorization.md).
- Découvrez plus de [vidéos](https://azure.microsoft.com/documentation/videos/index/?services=api-management) sur Gestion des API.
- Pour les autres méthodes permettant de sécuriser votre service principal, consultez [Authentification mutuelle des certificats](./api-management-howto-mutual-certificates.md).
- [Création d’une instance du service Gestion des API](./get-started-create-service-instance.md).
- [Gérer votre première API](./import-and-publish.md).

[api-management-dev-portal-signin]: ./media/api-management-howto-aad/api-management-dev-portal-signin.png
[api-management-aad-signin]: ./media/api-management-howto-aad/api-management-aad-signin.png
[api-management-complete-registration]: ./media/api-management-howto-aad/api-management-complete-registration.png
[api-management-registration-complete]: ./media/api-management-howto-aad/api-management-registration-complete.png

[How to add operations to an API]: ./mock-api-responses.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: ./api-management-policies.md
[Caching policies]: ./api-management-policies.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[https://oauth.net/2/]: https://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Sign in to the developer portal by using an Azure AD account]: #Sign-in-to-the-developer-portal-by-using-an-Azure-AD-account
