---
title: Créer une relation d’approbation entre une application et un fournisseur d’identité externe
titleSuffix: Microsoft identity platform
description: Configurez une relation d’approbation entre une application dans Azure AD et un fournisseur d’identité externe.  Cela permet à une charge de travail logicielle en dehors d’Azure d’accéder à des ressources protégées Azure AD sans utiliser de secrets ou de certificats.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 10/25/2021
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: keyam, udayh, vakarand
ms.openlocfilehash: 1d2c4631b663a57a8b1b2465c8cba6d6f81d803b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096948"
---
# <a name="configure-an-app-to-trust-an-external-identity-provider-preview"></a>Configurer une application pour approuver un fournisseur d’identité externe (préversion)

Cet article explique comment créer une relation d’approbation entre une application dans Azure Active Directory (Azure AD) et un fournisseur d’identité externe (IdP).  Vous pouvez alors configurer une charge de travail logicielle externe afin d’échanger un jeton délivré par le fournisseur d’identité externe contre un jeton d’accès délivré par la plateforme d’identités Microsoft. La charge de travail externe peut accéder aux ressources Azure AD protégées sans avoir à gérer de secrets (dans les scénarios pris en charge).  Pour en savoir plus sur le workflow d’échange de jetons, consultez l’article sur la [fédération des identités de charge de travail](workload-identity-federation.md).  Pour établir la relation d’approbation, configurez les informations d’identification de l’identité fédérée sur l’inscription de votre application à l’aide de Microsoft Graph.

Toute personne disposant des autorisations pour créer une inscription d’application et ajouter un secret ou un certificat peut ajouter des informations d’identification d’identité fédérée.  Toutefois, si le commutateur **Les utilisateurs peuvent inscrire des applications** dans le panneau [Paramètres utilisateur](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/UserSettings) a la valeur **Non**, vous ne pourrez pas créer d’inscription d’application ni configurer des informations d’identification d’identité fédérée.  Demandez à un administrateur de configurer les informations d’identification de l’identité fédérée en votre nom.  Toute personne disposant des rôles Administrateur d’application ou Propriétaire d’application peut le faire.

Une fois que vous avez configuré votre application pour qu’elle approuve un fournisseur d’identité externe, configurez votre charge de travail logicielle pour recevoir un jeton d’accès de la part du fournisseur d’identité Microsoft et accéder aux ressources protégées Azure AD.

## <a name="prerequisites"></a>Prérequis
[Créez une inscription d’application](quickstart-register-app.md) dans Azure AD.  Accordez à votre application l’accès aux ressources Azure ciblées par votre charge de travail logicielle externe.  

Recherchez l’ID d’objet de l’application (et non l’ID de l’application (client)) dont vous aurez besoin lors des étapes suivantes.  Vous trouverez l’ID d’objet de l’application dans le portail Azure.  Accédez à la liste des [applications inscrites](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) dans le portail Azure et sélectionnez votre inscription d’application.  Dans **Vue d’ensemble**->**Essentials**, recherchez l’**ID d’objet**.

Procurez-vous les informations pour votre fournisseur d’identité et votre charge de travail logicielle externe, dont vous aurez besoin lors des étapes suivantes.

## <a name="configure-a-federated-identity-credential-using-microsoft-graph"></a>Configurer des informations d’identification d’identité fédérée à l’aide de Microsoft Graph

Lancez [Azure Cloud Shell](https://portal.azure.com/#cloudshell/) et connectez-vous à votre locataire.

### <a name="create-a-federated-identity-credential"></a>Créer des informations d’identification d’identité fédérée

Exécutez la commande suivante pour [créer des informations d’identification d’identité fédérée](/graph/api/application-post-federatedidentitycredentials?view=graph-rest-beta&preserve-view=true) sur votre application (spécifiée par l’ID d’objet de l’application).  

*issuer* et *subject* sont les éléments clés nécessaires à la configuration de la relation d’approbation. *issuer* est l’URL du fournisseur d’identité externe, qui doit correspondre à la revendication `issuer` du jeton externe échangé.  *subject* est l’identificateur de la charge de travail logicielle externe, qui doit correspondre à la revendication `sub` (`subject`) du jeton externe échangé. *subject* n’a pas de format fixe, car chaque fournisseur d’identité utilise son propre sujet (parfois un GUID, parfois un identificateur délimité par des points-virgules, parfois des chaînes arbitraires). La combinaison de `issuer` et `subject` doit être unique sur l’application.  Lorsque la charge de travail logicielle externe demande à la plateforme d’identités Microsoft d’échanger le jeton externe contre un jeton d’accès, les valeurs *issuer* et *subject* des informations d’identification de l’identité fédérée sont vérifiées par rapport aux revendications `issuer` et `subject` fournies dans le jeton externe. Si ce contrôle de validation réussit, la plateforme d’identités Microsoft délivre un jeton d’accès à la charge de travail logicielle externe.

*audiences* liste les audiences qui peuvent apparaître dans le jeton externe.  Ce champ est obligatoire, et sa valeur par défaut est « api://AzureADTokenExchange ». Il indique ce que la plateforme d’identités Microsoft doit accepter dans la revendication `aud` dans le jeton entrant.  Cette valeur représente Azure AD dans votre fournisseur d’identité externe, et n’a aucune valeur fixe parmi les fournisseurs d’identité. Vous devrez peut-être créer une nouvelle inscription d’application dans votre IdP pour servir d’audience de ce jeton.

*name* est l’identificateur unique des informations d’identification de l’identité fédérée, qui a une limite de 120 caractères et doit être compatible avec les URL. Il est immuable une fois créé.

*description* est la description non validée, fournie par l’utilisateur, des informations d’identification de l’identité fédérée. 

Exécutez la commande suivante pour [créer des informations d’identification d’identité fédérée](/graph/api/application-post-federatedidentitycredentials?view=graph-rest-beta&preserve-view=true) sur votre application (spécifiée par l’ID d’objet de l’application).  *issuer* identifie GitHub comme émetteur de jeton externe.  *subject* identifie l’organisation, le dépôt et l’environnement GitHub pour votre workflow GitHub Actions.  Quand le workflow GitHub Actions demande à la plateforme d’identités Microsoft d’échanger un jeton GitHub contre un jeton d’accès, les valeurs des informations d’identification de l’identité fédérée sont comparées au jeton GitHub fourni.

```azurecli
az rest --method POST --uri 'https://graph.microsoft.com/beta/applications/f6475511-fd81-4965-a00e-41e7792b7b9c/federatedIdentityCredentials' --body '{"name":"Testing","issuer":"https://token.actions.githubusercontent.com/","subject":"repo:octo-org/octo-repo:environment:Production","description":"Testing","audiences":["api://AzureADTokenExchange"]}' 
```

Et vous recevez la réponse :
```azurecli
{
  "@odata.context": "https://graph.microsoft.com/beta/$metadata#applications('f6475511-fd81-4965-a00e-41e7792b7b9c')/federatedIdentityCredentials/$entity",
  "audiences": [
    "api://AzureADTokenExchange"
  ],
  "description": "Testing",
  "id": "1aa3e6a7-464c-4cd2-88d3-90db98132755",
  "issuer": "https://token.actions.githubusercontent.com/",
  "name": "Testing",
  "subject": "repo:octo-org/octo-repo:environment:Production"
}
```

> [!IMPORTANT]
> Si vous ajoutez accidentellement des informations de charge de travail externe incorrectes dans le paramètre *subject*, les informations d’identification de l’identité fédérée sont correctement créées sans erreur.  L’erreur ne se manifeste que lorsque l’échange de jetons échoue.

### <a name="list-federated-identity-credentials-on-an-app"></a>Lister les informations d’identification d’identité fédérée sur une application

Exécutez la commande suivante pour [lister les informations d’identification d’identité fédérée](/graph/api/application-list-federatedidentitycredentials?view=graph-rest-beta&preserve-view=true) pour une application (spécifiée par l’ID d’objet de l’application) :

```azurecli
az rest -m GET -u 'https://graph.microsoft.com/beta/applications/f6475511-fd81-4965-a00e-41e7792b7b9c/federatedIdentityCredentials' 
```

Et vous recevez une réponse semblable à la suivante :

```azurecli
{
  "@odata.context": "https://graph.microsoft.com/beta/$metadata#applications('f6475511-fd81-4965-a00e-41e7792b7b9c')/federatedIdentityCredentials",
  "value": [
    {
      "audiences": [
        "api://AzureADTokenExchange"
      ],
      "description": "Testing",
      "id": "1aa3e6a7-464c-4cd2-88d3-90db98132755",
      "issuer": "https://token.actions.githubusercontent.com/",
      "name": "Testing",
      "subject": "repo:octo-org/octo-repo:environment:Production"
    }
  ]
}
```

### <a name="delete-a-federated-identity-credential"></a>Supprimer des informations d’identification d’identité fédérée

Exécutez la commande suivante pour [supprimer des informations d’identification d’identité fédérée](/graph/api/application-list-federatedidentitycredentials?view=graph-rest-beta&preserve-view=true) d’une application (spécifiée par l’ID d’objet de l’application) :

```azurecli
az rest -m DELETE  -u 'https://graph.microsoft.com/beta/applications/f6475511-fd81-4965-a00e-41e7792b7b9c/federatedIdentityCredentials/51ecf9c3-35fc-4519-a28a-8c27c6178bca' 

```

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations, découvrez comment Azure AD utilise l’[octroi d’informations d’identification du client OAuth 2.0](v2-oauth2-client-creds-grant-flow.md#third-case-access-token-request-with-a-federated-credential) et une assertion du client émise par un autre fournisseur d’identité pour obtenir un jeton.