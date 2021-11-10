---
title: Configurer une connexion à une source de données à l’aide d’une identité managée
titleSuffix: Azure Cognitive Search
description: Découvrez comment configurer une connexion d’indexeur à une source de données à l’aide d’une identité managée
author: gmndrg
ms.author: gimondra
manager: nitinme
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/02/2021
ms.openlocfilehash: 6c038b7bac8d0791aafcda0010adeae138489ceb
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2021
ms.locfileid: "131893782"
---
# <a name="set-up-an-indexer-connection-to-a-data-source-using-a-managed-identity"></a>Configurer une connexion d’indexeur à une source de données à l’aide d’une identité managée

> [!IMPORTANT] 
> La configuration d’une connexion à une source de données à l’aide d’une identité managée n’est pas prise en charge par le niveau Gratuit de Recherche cognitive Azure.

Un [indexeur](search-indexer-overview.md) dans Recherche cognitive Azure est un robot qui permet d’extraire des données de votre source de données vers Recherche cognitive Azure. Un indexeur obtient une connexion à la source de données à partir de l’objet source de données que vous créez. L’objet source de données comprend généralement les informations d’identification de la source de données cible. Par exemple, l’objet source de données peut inclure une clé de compte Stockage Azure si vous souhaitez indexer les données d’un conteneur de stockage blob.

Dans de nombreux cas, le fait de fournir des informations d’identification directement dans l’objet source de données n’est pas un problème, mais quelques difficultés peuvent se présenter :
* Comment conserver les informations d’identification en toute sécurité dans mon code qui crée l’objet source des données ?
* Si la clé ou le mot de passe de mon compte sont compromis et que je dois les modifier, je dois mettre à jour mes objets sources de données avec la nouvelle clé ou le nouveau mot de passe du compte afin que mon indexeur puisse de nouveau se connecter à la source de données.

Ces problèmes peuvent être résolus en configurant votre connexion à l’aide d’une identité managée.

## <a name="using-managed-identities"></a>Utilisation des identités managées

[Identités managées](../active-directory/managed-identities-azure-resources/overview.md) est une fonctionnalité qui fournit aux applications une identité gérée automatiquement dans le répertoire actif Azure (Azure AD). Vous pouvez utiliser cette fonctionnalité dans Recherche cognitive Azure pour créer un objet source de données doté d’une chaîne de connexion qui n’inclut aucune information d’identification. Au lieu de cela, votre service de recherche disposera de l’accès à la source de données via le contrôle d’accès en fonction du rôle Azure (Azure RBAC).

En configurant une source de données à l’aide d’une identité managée, vous pouvez modifier vos informations d’identification de source de données le cas échéant : vos indexeurs pourront toujours se connecter à la source de données. Vous pouvez également créer des objets sources de données dans votre code sans avoir à inclure de clé de compte ni à utiliser Key Vault pour récupérer une clé de compte.

Il existe deux types d’identités managées. La Recherche cognitive Azure prend en charge les identités managées affectées par le système et les identités managées affectées par l’utilisateur.

### <a name="system-assigned-managed-identity"></a>Identité managée affectée par le système

Une [Identité managée affectée par le système](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) peut être affectée à un service Azure unique. Vous pouvez affecter une identité managée affectée par le système à un service de Recherche cognitive Azure unique, laquelle est liée au cycle de vie de ce service de recherche.

### <a name="user-assigned-managed-identity-preview"></a>Identité managée affectée par l’utilisateur (préversion)

> [!IMPORTANT]
>Cette fonctionnalité est en préversion publique et soumise à des [conditions d’utilisation supplémentaires](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Les versions API REST 2021-04-30-Preview et [API REST Gestion 2021-04-01-Preview](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update) offrent cette fonctionnalité.

Une [identité managée affectée par l’utilisateur](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) est une ressource Azure autonome qui peut être affectée à un ou plusieurs services Azure. Un service de Recherche cognitive Azure unique peut avoir une ou plusieurs identités managées affectées par l’utilisateur. Une identité managée affectée par l’utilisateur unique peut être affectée à plusieurs services de recherche.

## <a name="limitations"></a>Limites

Les sources de données suivantes prennent en charge la configuration d’une connexion d’indexeur à l’aide d’identités managées. 

* [Stockage Blob Azure, Azure Data Lake Storage Gen2 (préversion), Stockage Table Azure](search-howto-managed-identities-storage.md)
* [Azure Cosmos DB](search-howto-managed-identities-cosmos-db.md)
* [Azure SQL Database](search-howto-managed-identities-sql.md)

Actuellement, les fonctionnalités suivantes ne prennent pas en charge l’utilisation des identités managées pour configurer la connexion :
* Base de connaissances
* Compétences personnalisées
 
## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur la configuration d’une connexion d’indexeur à l’aide d’identités managées :

* [Stockage Blob Azure, Azure Data Lake Storage Gen2 (préversion), Stockage Table Azure](search-howto-managed-identities-storage.md)
* [Azure Cosmos DB](search-howto-managed-identities-cosmos-db.md)
* [Azure SQL Database](search-howto-managed-identities-sql.md)