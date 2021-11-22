---
title: 'Azure Cosmos DB : API SQL .NET, SDK et ressources'
description: Tout savoir sur l’API SQL .NET et le Kit de développement logiciel (SDK), y compris les dates de sortie, les dates de déclassement et les modifications effectuées entre chaque version du Kit de développement logiciel (SDK) .NET Azure Cosmos DB.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 11/11/2021
ms.author: anfeldma
ms.custom: devx-track-dotnet
ms.openlocfilehash: a52d3e5690a043e3f886dd0a87db5ffba155528f
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132493359"
---
# <a name="azure-cosmos-db-net-sdk-v2-for-sql-api-download-and-release-notes-deprecated"></a>SDK .NET v2 Azure Cosmos DB pour l’API SQL : Téléchargement et notes de publication (déconseillé)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [Kit de développement logiciel (SDK) .NET v3](sql-api-sdk-dotnet-standard.md)
> * [SDK .NET v2](sql-api-sdk-dotnet.md)
> * [SDK .NET Core v2](sql-api-sdk-dotnet-core.md)
> * [SDK .NET Change Feed v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.JS](sql-api-sdk-node.md)
> * [Kit SDK Java v4](sql-api-sdk-java-v4.md)
> * [Kit SDK Java asynchrone v2](sql-api-sdk-async-java.md)
> * [SDK Java Sync v2](sql-api-sdk-java.md)
> * [Spring Data v2](sql-api-sdk-java-spring-v2.md)
> * [Spring Data v3](sql-api-sdk-java-spring-v3.md)
> * [Connecteur OLTP Spark 3](sql-api-sdk-java-spark-v3.md)
> * [Connecteur OLTP Spark 2](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [API REST Resource Provider](/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-query-getting-started.md)
> * [Exécuteur en bloc – .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Exécuteur en bloc – Java](sql-api-sdk-bulk-executor-java.md)

| | Liens |
|---|---|
|**Téléchargement du Kit de développement logiciel (SDK)**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/)|
|**Documentation de l’API**|[Documentation de référence sur l’API .NET](/dotnet/api/overview/azure/cosmosdb)|
|**Exemples**|[Exemples de code .NET](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples)|
|**Prise en main**|[Prise en main du Kit de développement logiciel (SDK) .NET Azure Cosmos DB](sql-api-get-started.md)|
|**Didacticiel d’application web**|[Développement d’applications web avec Azure Cosmos DB](sql-api-dotnet-application.md)|
|**Infrastructure actuellement prise en charge**|[Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)|

> [!IMPORTANT]
> Le 31 août 2024, le kit de développement logiciel (SDK) .NET Azure Cosmos DB v2.x sera supprimé. Le SDK et toutes les applications qui l’utilisent continueront à fonctionner. Azure Cosmos DB cessera simplement de fournir une maintenance et un support pour ce SDK. Nous vous recommandons de suivre ces instructions pour migrer vers la dernière version du kit de développement logiciel (SDK) .NET v3.
>

> [!NOTE]
> Si vous utilisez .NET Framework, consultez la dernière version 3.x du [SDK .NET](sql-api-sdk-dotnet-standard.md), qui cible .NET Standard.

## <a name="release-history"></a><a name="release-history"></a> Historique des mises en production

L’historique des versions est conservé dans le référentiel source du kit de développement logiciel (SDK) .NET Azure Cosmos DB. Pour obtenir une liste détaillée des mises en production de fonctionnalités et des bogues résolus dans chaque version, consultez la [Documentation du journal des modifications du SDK](https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/changelog.md)

Étant donné que la version 3 du Kit de développement logiciel (SDK) .NET Azure Cosmos DB inclut des fonctionnalités mises à jour et des performances améliorées, la version 2.x de ce SDK sera supprimée le 31 août 2024.Vous devez mettre à jour votre Kit de développement logiciel (SDK) vers la version 3 avant cette date. Nous vous recommandons de suivre les instructions pour migrer vers le Kit de développement logiciel (SDK) Java Azure Cosmos DB .NET version 3.

## <a name="recommended-version"></a><a name="recommended-version"> </a> Version recommandée

Différentes sous-versions des Kits de développement logiciel (SDK) .NET sont disponibles sous la version 2.x.x. **La version minimale recommandée est la 2.16.2**.

## <a name="known-issues"></a><a name="known-issues"></a> Problèmes connus

Vous trouverez ci-dessous une liste de tous les problèmes connus affectant la [version minimale recommandée](#recommended-version) :

| Problème | Impact | Limitation des risques | Lien de suivi |
| --- | --- | --- | --- |
| Lorsque vous utilisez le mode direct avec un compte avec plusieurs emplacements d’écriture, le Kit de développement logiciel (SDK) risque de ne pas détecter quand une région est ajoutée au compte. Le processus en arrière-plan qui [actualise les informations de compte](troubleshoot-sdk-availability.md#adding-a-region-to-an-account) ne peut pas démarrer. |Si une nouvelle région est ajoutée au compte qui fait partie des PreferredLocations dans un ordre supérieur à celui de la région actuelle, le Kit de développement logiciel (SDK) ne détecte pas la nouvelle région disponible. |Redémarrez-la. |https://github.com/Azure/azure-cosmos-dotnet-v2/issues/852 |

## <a name="faq"></a>Questions fréquentes (FAQ)

[!INCLUDE [cosmos-db-sdk-faq](../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Voir aussi

Pour en savoir plus sur Cosmos DB, consultez la page du service [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).
