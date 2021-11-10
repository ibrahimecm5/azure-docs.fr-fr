---
title: Indexer les données de l’API Gremlin (préversion)
titleSuffix: Azure Cognitive Search
description: Configurez un indexeur Azure Cosmos DB pour automatiser l’indexation du contenu d’API Gremlin dans le cadre de la recherche en texte intégral avec Recherche cognitive Azure.
author: gmndrg
ms.author: gimondra
manager: nitinme
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/11/2021
ms.openlocfilehash: a0876ef39d74403ad6ae5d88fbac12aeed543e27
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2021
ms.locfileid: "131893801"
---
# <a name="index-data-using-azure-cosmos-db-gremlin-api"></a>Indexer les données à l’aide de l’API Gremlin d’Azure Cosmos DB

> [!IMPORTANT]
> L’indexeur de l’API Gremlin de Cosmos DB est en préversion publique dans le cadre de [Conditions d’utilisation supplémentaires](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). [Demandez l’accès](https://aka.ms/azure-cognitive-search/indexer-preview) à cette fonctionnalité. Une fois l’accès activée, utilisez une [API REST en préversion (préversion du 30/06/2020 ou ultérieure)](search-api-preview.md) pour indexer votre contenu. La prise en charge du portail est actuellement limitée, et le Kit de développement logiciel (SDK) .NET n’est pas pris en charge.

Cet article vous montre comment configurer l’indexeur Azure Cosmos DB pour extraire le contenu et les rendre détectables dans Recherche cognitive Azure. Ce workflow crée un index de recherche dans Recherche cognitive Azure et le charge avec du contenu existant extrait à partir d’Azure Cosmos DB à l’aide de l’API Gremlin.

Étant donné que la terminologie peut prêter à confusion, il est important de souligner que l’[indexation Azure Cosmos DB](../cosmos-db/index-overview.md) et l’[indexation Recherche cognitive Azure](search-what-is-an-index.md) sont des opérations distinctes, propres à chaque service. Avant de commencer l’indexation Recherche cognitive Azure, votre base de données Azure Cosmos DB doit déjà exister et contenir des données.

## <a name="prerequisites"></a>Configuration requise

Pour que Recherche cognitive Azure puisse indexer les données dans Cosmos DB via l’API Gremlin, l’[indexation propre à Cosmos DB](../cosmos-db/index-overview.md) doit également être activée et définie sur [Cohérent](../cosmos-db/index-policy.md#indexing-mode). Il s’agit de la configuration par défaut de Cosmos DB. L’indexation de Recherche cognitive Azure ne fonctionnera pas si l’indexation de Cosmos DB n’est pas déjà activée.

## <a name="get-started"></a>Bien démarrer

Vous pouvez utiliser l’[API REST de préversion](/rest/api/searchservice/index-preview) pour indexer les données d’Azure Cosmos DB disponibles via l’API Gremlin en suivant un workflow en trois parties commun à tous les indexeurs dans Recherche cognitive Azure : créer une source de données, créer un index, créer un indexeur. Dans le processus ci-dessous, l’extraction des données à partir de Cosmos DB démarre lorsque vous soumettez la requête Créer un indexeur.

Par défaut, l’indexeur de l’API Gremlin Cosmos DB de Recherche cognitive Azure fait en sorte que chaque vertex de votre graphique soit un document dans l’index. Les arêtes seront ignorées. Vous pouvez également définir la requête pour qu’elle indexe uniquement les arêtes.

### <a name="step-1---assemble-inputs-for-the-request"></a>Étape 1 : Assembler des entrées pour la requête

Pour chaque requête, vous devez fournir le nom du service et la clé d’administration pour Recherche cognitive Azure (dans l’en-tête POST). Vous pouvez utiliser [Postman](./search-get-started-rest.md) ou tout autre client d’API REST pour envoyer des requêtes HTTPS à Recherche cognitive Azure.

Copiez et enregistrez les valeurs suivantes pour les utiliser dans votre requête :

+ Nom du service Recherche cognitive Azure
+ Clé d’administration de Recherche cognitive Azure
+ Chaîne de connexion de l’API Gremlin Cosmos DB

Vous pouvez trouver ces valeurs dans le portail Azure :

1. Dans les pages du portail pour Recherche cognitive Azure, copiez l’URL du service de recherche dans la page Vue d’ensemble.

2. Dans le volet de navigation gauche, cliquez sur **Clés**, puis copiez la clé primaire ou secondaire.

3. Basculez sur les pages du portail pour votre compte Cosmos DB. Dans le volet de navigation gauche, sous **Paramètres**, sélectionnez **Clés**. Cette page fournit un URI, deux ensembles de chaînes de connexion, et deux ensembles de clés. Copiez une des chaînes de connexion dans le bloc-notes.

### <a name="step-2---create-a-data-source"></a>Étape 2 : Créer une source de données

Une **source de données** spécifie les données à indexer, les informations d’identification et les stratégies pour identifier les modifications des données (par exemple, les documents modifiés ou supprimés dans votre collection). La source de données est définie en tant que ressource indépendante de manière à pouvoir être utilisée par plusieurs indexeurs.

Pour créer une source de données, formulez une requête POST :

```http
    POST https://[service name].search.windows.net/datasources?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [Search service admin key]
    
    {
        "name": "mycosmosdbgremlindatasource",
        "type": "cosmosdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myCosmosDbEndpoint.documents.azure.com;AccountKey=myCosmosDbAuthKey;ApiKind=Gremlin;Database=myCosmosDbDatabaseId"
        },
        "container": { "name": "myGraphId", "query": null }
    }
```

Le corps de la requête contient la définition de la source de données, qui doit inclure les champs suivants :

| Champ   | Description |
|---------|-------------|
| **name** | Obligatoire. Choisissez un nom pour représenter votre objet source de données. |
|**type**| Obligatoire. Doit être `cosmosdb`. |
|**credentials** | Obligatoire. La **connectionString** doit inclure un AccountEndpoint, AccountKey, ApiKind et Database. L’ApiKind est **Gremlin**.</br></br>Par exemple :<br/>`AccountEndpoint=https://<Cosmos DB account name>.documents.azure.com;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>;ApiKind=Gremlin`<br/><br/>L’AccountEndpoint doit utiliser le point de terminaison `*.documents.azure.com`.
| **container** | Contient les éléments suivants : <br/>**nom** : Obligatoire. Spécifiez l’ID du graphique.<br/>**query** : facultatif. Par défaut, il s’agit de `g.V()`. Pour indexer les arêtes, définissez la requête sur `g.E()`. |
| **dataChangeDetectionPolicy** | La progression incrémentielle est activée par défaut en utilisant `_ts` comme colonne de limite supérieure. |
|**dataDeletionDetectionPolicy** | facultatif. Consultez la section [Indexation des documents supprimés](#DataDeletionDetectionPolicy).|

### <a name="step-3---create-a-target-search-index"></a>Étape 3 : Créer un index de recherche cible

[Créez un index Recherche cognitive Azure cible](/rest/api/searchservice/create-index) si vous n’en avez pas. L’exemple suivant crée un index avec les champs « id », « label » et « description » :

```http
    POST https://[service name].search.windows.net/indexes?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [Search service admin key]

    {
       "name": "mysearchindex",
       "fields": [
        {
            "name": "rid",
            "type": "Edm.String",
            "facetable": false,
            "filterable": false,
            "key": true,
            "retrievable": true,
            "searchable": true,
            "sortable": false,
            "analyzer": "standard.lucene",
            "indexAnalyzer": null,
            "searchAnalyzer": null,
            "synonymMaps": [],
            "fields": []
        },{
            "name": "id",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "retrievable": true,
            "sortable": false,
            "facetable": false,
            "key": false,
            "indexAnalyzer": null,
            "searchAnalyzer": null,
            "analyzer": "standard.lucene",
            "synonymMaps": []
        }, {
            "name": "label",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "retrievable": true,
            "sortable": false,
            "facetable": false,
            "key": false,
            "indexAnalyzer": null,
            "searchAnalyzer": null,
            "analyzer": "standard.lucene",
            "synonymMaps": []
       }]
     }
```

Assurez-vous que le schéma de votre index cible est compatible avec votre graphique.

Pour les collections partitionnées, la clé de document par défaut est la propriété `_rid` d’Azure Cosmos DB, que Recherche cognitive Azure renomme automatiquement en `rid`, car les noms de champ ne peuvent pas commencer par un trait de soulignement. De même, les valeurs `_rid` d’Azure Cosmos DB contiennent des caractères non valides dans les clés de Recherche cognitive Azure. Pour cette raison, les valeurs `_rid` doivent être codées en base64 si vous souhaitez en faire votre clé de document.

### <a name="mapping-between-json-data-types-and-azure-cognitive-search-data-types"></a>Mappage entre les types de données JSON et les types de données Azure Search

| Type de données JSON | Types de champs d’index cible compatibles |
| --- | --- |
| Bool |Edm.Boolean, Edm.String |
| Nombres qui ressemblent à des nombres entiers |Edm.Int32, Edm.Int64, Edm.String |
| Nombres qui ressemblent à des nombres avec points flottants |Edm.Double, Edm.String |
| String |Edm.String |
| Tableaux de types primitifs, par exemple ["a", "b", "c"] |Collection(Edm.String) |
| Chaînes qui ressemblent à des dates |Edm.DateTimeOffset, Edm.String |
| Objets GeoJSON, par exemple { "type": "Point", "coordinates": [long, lat] } |Edm.GeographyPoint |
| Autres objets JSON |N/A |

### <a name="step-4---configure-and-run-the-indexer"></a>Étape 4 : Configurer et exécuter l’indexeur

Une fois l'index et la source de données créés, vous êtes prêt à créer l’indexeur :

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name": "mycosmosdbgremlinindexer",
      "description": "My Cosmos DB Gremlin API indexer",
      "dataSourceName": "mycosmosdbgremlindatasource",
      "targetIndexName": "mysearchindex"
    }
```

Cet indexeur commencera à s’exécuter après sa création et ne s’exécutera qu’une seule fois. Vous pouvez ajouter le paramètre facultatif de planification à la requête pour configurer votre indexeur de façon à ce qu’il s’exécute selon une planification. Pour plus d’informations sur la définition des planifications de l’indexeur, consultez [Guide pratique pour planifier les indexeurs pour Recherche cognitive Azure](search-howto-schedule-indexers.md).

Pour plus d’informations sur l’API Créer un indexeur, consultez [Créer un indexeur](/rest/api/searchservice/create-indexer).

<a name="DataDeletionDetectionPolicy"></a>

## <a name="indexing-deleted-documents"></a>Indexation des documents supprimés

Lorsque les données d’un graphique sont supprimées, vous pouvez également supprimer le document correspondant de l’index de recherche. L’objectif d’une stratégie de détection de la suppression des données est d’identifier efficacement les éléments de données supprimés et de supprimer le document complet de l’index. La stratégie de détection de la suppression des données n’est pas destinée à supprimer des documents partiels. La seule stratégie actuellement prise en charge est la stratégie `Soft Delete` (où la suppression est signalée par un indicateur quelconque), spécifiée comme suit :

```http
    {
        "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
        "softDeleteColumnName" : "the property that specifies whether a document was deleted",
        "softDeleteMarkerValue" : "the value that identifies a document as deleted"
    }
```

L'exemple suivant crée une source de données avec des conseils pour une stratégie de suppression en douceur :

```http
    POST https://[service name].search.windows.net/datasources?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [Search service admin key]
    
    {
        "name": "mycosmosdbgremlindatasource",
        "type": "cosmosdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myCosmosDbEndpoint.documents.azure.com;AccountKey=myCosmosDbAuthKey;ApiKind=Gremlin;Database=myCosmosDbDatabaseId"
        },
        "container": { "name": "myCollection" },
        "dataChangeDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "`_ts`"
        },
        "dataDeletionDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
            "softDeleteColumnName": "isDeleted",
            "softDeleteMarkerValue": "true"
        }
    }
```

<a name="MappingGraphData"></a>

## <a name="mapping-graph-data-to-a-search-index"></a>Mappage des données du graphique à un index de recherche

L’indexeur de l’API Gremlin Cosmos DB mappe automatiquement quelques éléments de données de graphique pour vous :

1. L’indexeur mappera `_rid` à un champ `rid` dans l’index, s’il existe. Notez que si vous souhaitez utiliser la valeur `rid` comme clé dans votre index, vous devez encoder la clé en base64, car `_rid` peut contenir des caractères non valides dans les clés de document Recherche cognitive Azure.

1. L’indexeur mappera `_id` à un champ `id` dans l’index, s’il existe.

1. Lorsque vous interrogez votre base de données Cosmos DB à l’aide de l’API Gremlin, vous pouvez remarquer que la sortie JSON de chaque propriété comporte un `id` et une `value`. L’indexeur Cosmos DB de Recherche cognitive Azure mappera automatiquement la `value` des propriétés dans un champ de votre index de recherche portant le même nom que la propriété, le cas échéant. Dans l’exemple suivant, 450 est mappé à un champ `pages` dans l’index de recherche.

```http
    {
        "id": "Cookbook",
        "label": "book",
        "type": "vertex",
        "properties": {
          "pages": [
            {
              "id": "48cf6285-a145-42c8-a0aa-d39079277b71",
              "value": "450"
            }
          ]
        }
    }
```

Il se peut que vous deviez utiliser des [mappages de champs de sortie](cognitive-search-output-field-mapping.md) afin de mapper la sortie de votre requête aux champs de votre index. Vous souhaiterez probablement utiliser des mappages de champs de sortie plutôt que des [mappages de champs](search-indexer-field-mappings.md), car la requête personnalisée contiendra probablement des données complexes.

Par exemple, supposons que votre requête génère la sortie suivante :

```json
    [
      {
        "vertex": {
          "id": "Cookbook",
          "label": "book",
          "type": "vertex",
          "properties": {
            "pages": [
              {
                "id": "48cf6085-a211-42d8-a8ea-d38642987a71",
                "value": "450"
              }
            ],
          }
        },
        "written_by": [
          {
            "yearStarted": "2017"
          }
        ]
      }
    ]
```

Si vous souhaitez mapper la valeur de `pages` dans le JSON ci-dessus à un champ `totalpages` de votre index, vous pouvez ajouter le [mappage de champs de sortie](cognitive-search-output-field-mapping.md) suivant à votre définition d’indexeur :

```json
    ... // rest of indexer definition 
    "outputFieldMappings": [
        {
          "sourceFieldName": "/document/vertex/pages",
          "targetFieldName": "totalpages"
        }
    ]
```

Notez que le mappage de champs de sortie commence par `/document` et n’inclut pas de référence à la clé de propriété dans le JSON. Cela s’explique par le fait que l’indexeur place chaque document sous le nœud `/document` lors de l’ingestion des données du graphique. L’indexeur vous permet également de référencer automatiquement la valeur de `pages` en référençant simplement `pages` au lieu de devoir référencer le premier objet dans le tableau de `pages`.

## <a name="next-steps"></a>Étapes suivantes

+ Pour en savoir plus sur l’API Gremlin Azure Cosmos DB, consultez [Présentation de la base de données Azure Cosmos : API Gremlin]()../cosmos-db/graph-introduction.md).

+ Pour plus d’informations sur les scénarios et les tarifs de la Recherche cognitive Azure, consultez la [page du service Search sur azure.microsoft.com](https://azure.microsoft.com/services/search/).