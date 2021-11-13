---
title: Liaisons Azure Cosmos DB pour Functions versions 2.x et ultérieures
description: Découvrez comment utiliser des déclencheurs et liaisons Azure Cosmos DB dans Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 09/01/2021
ms.author: cshoe
ms.openlocfilehash: 87318f887078a07783ec68af4fb6819f227eb38c
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130248523"
---
# <a name="azure-cosmos-db-trigger-and-bindings-for-azure-functions-2x-and-higher-overview"></a>Présentation du déclencheur et des liaisons Azure Cosmos DB pour Azure Functions versions 2.x et ultérieures

> [!div class="op_single_selector" title1="Sélectionnez la version du runtime Azure Functions que vous utilisez : "]
> * [Version 1](functions-bindings-cosmosdb.md)
> * [Versions 2 et ultérieures](functions-bindings-cosmosdb-v2.md)

Cet ensemble d’articles explique comment utiliser des liaisons [Azure Cosmos DB](../cosmos-db/serverless-computing-database.md) dans Azure Functions versions 2.x et ultérieures. Azure Functions prend en charge les liaisons de déclencheur, d’entrée et de sortie pour Azure Cosmos DB.

| Action | Type |
|---------|---------|
| Exécuter une fonction lors de la création ou de la modification d’un document Azure Cosmos DB | [Déclencheur](./functions-bindings-cosmosdb-v2-trigger.md) |
| Lire un document Azure Cosmos DB | [Liaison d’entrée](./functions-bindings-cosmosdb-v2-input.md) |
| Enregistrer les modifications apportées à un document Azure Cosmos DB  |[Liaison de sortie](./functions-bindings-cosmosdb-v2-output.md) |

> [!NOTE]
> Cette référence concerne [Azure Functions versions 2.x et ultérieures](functions-versions.md).  Pour plus d’informations sur l’utilisation de ces liaisons dans Functions 1.x, consultez [Liaisons Azure Cosmos DB pour Azure Functions 1.x](functions-bindings-cosmosdb.md).
>
> Cette liaison était nommée à l’origine DocumentDB. Dans Azure Functions version 2.x, le déclencheur, les liaisons et le package sont tous nommés Cosmos DB.

## <a name="supported-apis"></a>API prises en charge

[!INCLUDE [SQL API support only](../../includes/functions-cosmosdb-sqlapi-note.md)]

## <a name="add-to-your-functions-app"></a>Ajouter à votre application de fonction

### <a name="functions-2x-and-higher"></a>Functions 2.x et versions ultérieures

Pour utiliser le déclencheur et les liaisons, vous devez référencer le package approprié. Le package NuGet est utilisé pour les bibliothèques de classes .NET, tandis que le bundle d’extensions est utilisé pour tous les autres types d’applications.

| Langage                                        | Ajouter via...                                   | Notes 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | L’installation du [package NuGet], version 3.x | |
| Script C#, Java, JavaScript, Python, PowerShell | L’inscription du [bundle d’extensions]          | Il est recommandé d’utiliser l’[extension Azure Tools] avec Visual Studio Code. |
| Script C# (en ligne uniquement dans le portail Azure)         | Ajout d’une liaison                            | Pour mettre à jour des extensions de liaison existantes sans avoir à republier votre application de fonction, voir [Mettre à jour vos extensions]. |

[Package NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB
[core tools]: ./functions-run-local.md
[Bundle d’extensions]: ./functions-bindings-register.md#extension-bundles
[Mettre à jour vos extensions]: ./functions-bindings-register.md
[Extension Azure Tools]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="cosmos-db-extension-4x-and-higher"></a>Extension Cosmos DB 4.x et versions ultérieures

Une nouvelle version de l’extension de liaisons Cosmos DB est disponible en préversion. Elle introduit la possibilité de [se connecter à l’aide d’une identité plutôt que d’un secret](./functions-reference.md#configure-an-identity-based-connection). Pour obtenir un tutoriel sur la configuration de vos applications de fonction avec des identités managées, consultez le [tutoriel sur la création d’une application de fonction avec des connexions basées sur l’identité](./functions-identity-based-connections-tutorial.md). Pour les applications .NET, la nouvelle version de l’extension change également les types avec lesquels vous pouvez établir une liaison, en remplaçant les types du SDK v2 `Microsoft.Azure.DocumentDB` par des types plus récents du SDK v3 [Microsoft.Azure.Cosmos](../cosmos-db/sql/sql-api-sdk-dotnet-standard.md). En savoir plus sur la façon dont ces nouveaux types sont différents et sur la manière de les migrer à partir du [Guide de migration SDK](../cosmos-db/sql/migrate-dotnet-v3.md), du [déclencheur](./functions-bindings-cosmosdb-v2-trigger.md), de la [liaison d’entrée](./functions-bindings-cosmosdb-v2-input.md)et des exemples de [liaison de sortie](./functions-bindings-cosmosdb-v2-output.md) .

Cette version de l’extension est disponible sous la forme d’un [package NuGet en préversion]. Pour en savoir plus, consultez [Mettre à jour vos extensions].

[Package NuGet en préversion]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB/4.0.0-preview2

> [!NOTE]
> Actuellement, l’authentification avec une identité plutôt qu’une clé secrète à l’aide de l’extension 4. x préversion est uniquement disponible pour les plans Elastic Premium. 

### <a name="functions-1x"></a>Functions 1.x

Les applications Functions 1.x ont automatiquement une référence au package NuGet [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs), version 2.x.

## <a name="exceptions-and-return-codes"></a>Exceptions et codes de retour

| Liaison | Informations de référence |
|---|---|
| CosmosDB | [Codes d’erreur CosmosDB](/rest/api/cosmos-db/http-status-codes-for-cosmosdb) |

<a name="host-json"></a>

## <a name="hostjson-settings"></a>Paramètres host.json

[!INCLUDE [functions-host-json-section-intro](../../includes/functions-host-json-section-intro.md)]

```json
{
    "version": "2.0",
    "extensions": {
        "cosmosDB": {
            "connectionMode": "Gateway",
            "protocol": "Https",
            "leaseOptions": {
                "leasePrefix": "prefix1"
            }
        }
    }
}
```

|Propriété  |Default |Description |
|----------|--------|------------|
|GatewayMode|Passerelle|Le mode de connexion utilisé par la fonction lors de la connexion au service Azure Cosmos DB. Les options sont `Direct` et `Gateway`.|
|Protocol|Https|Le protocole de connexion utilisé par la fonction lors de la connexion au service Azure Cosmos DB. Lisez [ceci pour obtenir des explications sur les deux modes](../cosmos-db/performance-tips.md#networking). <br><br> Ce paramètre n’est pas disponible dans la [version 4.x de l’extension](#cosmos-db-extension-4x-and-higher). |
|leasePrefix|n/a|Préfixe de bail à utiliser dans toutes les fonctions d’une application. <br><br> Ce paramètre n’est pas disponible dans la [version 4.x de l’extension](#cosmos-db-extension-4x-and-higher).|

## <a name="next-steps"></a>Étapes suivantes

- [Exécuter une fonction lors de la création ou de la modification d’un document Azure Cosmos DB (Déclencheur)](./functions-bindings-cosmosdb-v2-trigger.md)
- [Lire un document Azure Cosmos DB (liaison d’entrée)](./functions-bindings-cosmosdb-v2-input.md)
- [Enregistrer les modifications apportées à un document Azure Cosmos DB (liaison de sortie)](./functions-bindings-cosmosdb-v2-output.md)