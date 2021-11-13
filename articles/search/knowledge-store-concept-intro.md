---
title: Concepts de base de connaissances
titleSuffix: Azure Cognitive Search
description: Envoyez des documents enrichis à Stockage Azure afin de pouvoir les consulter, remodeler et utiliser des documents enrichis dans Recherche cognitive Azure et d’autres applications.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/02/2021
ms.openlocfilehash: 659e625aa98a92c2730e76bd847dfdddc3c955bf
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131577324"
---
# <a name="knowledge-store-in-azure-cognitive-search"></a>Base de connaissances dans Recherche cognitive Azure

La base de connaissances est un récepteur de données créé par un [pipeline d’enrichissement par IA](cognitive-search-concept-intro.md) de la Recherche cognitive qui stocke du contenu enrichi dans des tables et des conteneurs de blobs dans le Stockage Azure. Cette opération permet d’effectuer des analyses indépendantes ou un traitement en aval dans des scénarios autres que la recherche, comme l’exploration de connaissances.

Si vous avez utilisé des compétences cognitives dans le passé, vous savez déjà que l’*ensemble de compétences* déplace un document à travers une séquence d’enrichissements qui appellent des transformations atomiques, telles que la reconnaissance d’entités ou la traduction de texte. Le résultat peut être un index de recherche ou des projections dans une base de connaissances. Les deux sorties, index de recherche et base de connaissances, sont des produits du même pipeline. Elles sont dérivées des mêmes entrées mais elles ont pour résultat une sortie structurée, stockée et utilisée dans des applications distinctes.

:::image type="content" source="media/knowledge-store-concept-intro/knowledge-store-concept-intro.svg" alt-text="Pipeline avec ensemble de compétences" border="false":::

Physiquement, une base de connaissances représente un [Stockage Azure](../storage/common/storage-account-overview.md), soit le Stockage Table Azure, soit le Stockage Blob Azure, ou les deux. Tout outil ou processus pouvant se connecter au Stockage Azure peut utiliser le contenu d’un magasin de connaissances.

Vue à travers le navigateur de stockage, une base de connaissances ressemble à n’importe quelle collection de tables, d’objets ou de fichiers. L’exemple suivant montre une base de connaissances composée de trois tables avec des champs provenant de la source de données ou créés via des enrichissements (consultez « score de sentiments » et « translated_text »).

:::image type="content" source="media/knowledge-store-concept-intro/kstore-in-storage-explorer.png" alt-text="Lecture et écriture des compétences à partir de l’arborescence d’enrichissement" border="true":::

## <a name="benefits-of-knowledge-store"></a>Avantages de la base de connaissances

Les principaux avantages d’une base de connaissances sont deux fois plus nombreux : un accès flexible au contenu et la possibilité de mettre en forme les données.

Contrairement à un index de recherche accessible uniquement par le biais de requêtes dans la Recherche cognitive, une base de connaissances est accessible par tout outil, application ou processus qui prend en charge les connexions vers le Stockage Azure. Cette flexibilité ouvre de nouveaux scénarios pour la consommation du contenu analysé et enrichi produit par un pipeline d’enrichissement.

Les mêmes compétences qui enrichissent les données peuvent également être utilisées pour mettre en forme les données. Certains outils comme Power BI fonctionnent mieux avec les tables, alors qu’une charge de travail de science des données peut nécessiter une structure de données complexe dans un format blob. L’ajout d’une [compétence Modélisateur](cognitive-search-skill-shaper.md) à un ensemble de compétences vous permet de contrôler la forme de vos données. Vous pouvez ensuite passer ces formes à des projections, tables ou objets Blob, pour créer des structures de données physiques qui s’alignent sur l’utilisation prévue des données.

La vidéo suivante explique ces deux avantages, et bien plus encore.

> [!VIDEO https://www.youtube.com/embed/XWzLBP8iWqg?version=3]

## <a name="knowledge-store-definition"></a>Définition de la base de connaissances

Une base de connaissances est définie à l’intérieur d’une définition de compétences et possède deux composants : 

+ Une chaîne de connexion au service Stockage Azure

+ des [**Projections**](knowledge-store-projection-overview.md) qui déterminent si la base de connaissances comprend des tables, des objets ou des fichiers. 

L’élément de projections est un tableau. Vous pouvez créer plusieurs ensembles de combinaisons table-objet-fichier au sein d’une base de connaissances.

```json
"knowledgeStore": {
   "storageConnectionString":"<YOUR-AZURE-STORAGE-ACCOUNT-CONNECTION-STRING>",
   "projections":[
      {
         "tables":[ ],
         "objects":[ ],
         "files":[ ]
      }
   }
```

Le type de projection que vous spécifiez dans cette structure détermine le type de stockage utilisé par la base de connaissances.

+ `tables` permet de projeter du contenu enrichi dans le service Stockage Table. Définissez une projection de table lorsque vous avez besoin de structures de rapports tabulaires pour des entrées dans des outils analytiques, ou les exporter en tant que trames de données dans d’autres magasins de données. Vous pouvez spécifier plusieurs `tables` dans le même groupe de projections pour obtenir un sous-ensemble ou une coupe transversale des documents enrichis. Dans le même groupe de projection, les relations entre tables sont conservées afin que vous puissiez les utiliser toutes.

  Le contenu projeté n’est pas agrégé ou normalisé. La capture d’écran suivante montre un tableau, trié par phrases clés, avec le document parent indiqué dans la colonne adjacente. Contrairement à l’ingestion des données durant l’indexation, il n’existe aucune analyse linguistique ou agrégation de contenu. Les formes plurielles et les différences de casse sont considérées comme des instances uniques.

  :::image type="content" source="media/kstore-keyphrases-per-document.png" alt-text="Capture d’écran de phrases clés et de documents dans un tableau" border="true":::

+ `objects` permet de projeter un document JSON dans le service Stockage Blob. La représentation physique d’un `object` est une structure JSON hiérarchique représentant un document enrichi.

+ `files` permet de projeter des fichiers image dans le service Stockage Blob. Un `file` est une image extraite d’un document, transférée intacte vers un stockage Blob. Bien qu’il soit nommé « fichiers », il s’affiche dans le Stockage Blob et non dans le stockage de fichiers.

## <a name="create-a-knowledge-store"></a>Créer une base de connaissances

Pour créer une base de connaissances, utilisez le portail ou une API. Vous aurez besoin de [Stockage Azure](../storage/index.yml), d’un [ensemble de compétences](cognitive-search-working-with-skillsets.md) et d’un [indexeur](search-indexer-overview.md). Dans la mesure où les indexeurs nécessitent un index de recherche, vous devez également fournir une définition d’index.

Utilisez l’approche du portail pour obtenir le chemin le plus rapide vers une base de connaissances terminée. Vous pouvez aussi choisir l’API REST pour une compréhension plus approfondie de la façon dont les objets sont définis et associés.

### <a name="azure-portal"></a>[**Portail Azure**](#tab/kstore-portal)

[**Créez votre première base de connaissances en quatre étapes**](knowledge-store-create-portal.md) à l’aide de l’Assistant **Importation des données**.

1. Définissez votre schéma de données

1. Définissez votre ensemble de compétences et spécifiez une base de connaissances.

1. Créez un schéma d’index. L’Assistant l’exige et peut en inférer un pour vous.

1. Exécutez l’Assistant. L’extraction, l’enrichissement et le stockage se produisent dans cette dernière étape.

L’Assistant automatise les tâches que vous devriez normalement gérer manuellement. La modélisation et les projections (définitions des structures de données physiques dans le service de Stockage Azure) sont créées automatiquement. 

### <a name="rest"></a>[**REST**](#tab/kstore-rest)

[**Créer votre première base de connaissances à l’aide de Postman**](knowledge-store-create-rest.md) est un didacticiel qui vous guide tout au long des objets et des demandes appartenant à cette [collection de la base de connaissances](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/knowledge-store).

Vous pouvez utiliser l’API REST version `2020-06-30` pour créer une base de connaissances via des ajouts à un ensemble de compétences.

+ [Créer un ensemble de compétences (api-version=2020-06-30)](/rest/api/searchservice/create-skillset)
+ [Mettre à jour un ensemble de compétences (api-version=2020-06-30)](/rest/api/searchservice/update-skillset)

Dans les compétences suivantes :

+ Spécifier les projections à intégrer au service Stockage Azure (tables, objets, fichiers)
+ Incluez une compétence Modélisateur dans votre ensemble de compétences pour déterminer le schéma et le contenu de la projection
+ Affecter la modélisation nommée à une projection

### <a name="net-sdk"></a>[**Kit de développement logiciel (SDK) .NET**](#tab/kstore-dotnet)

Pour les développeurs .NET, utilisez la [classe KnowledgeStore](/dotnet/api/azure.search.documents.indexes.models.knowledgestore) de la bibliothèque de client Azure.Search.Documents.

---

<a name="tools-and-apps"></a>

## <a name="connect-with-apps"></a>Se connecter avec des applications

Une fois les enrichissements disponibles dans le stockage, n'importe quel outil ou technologie capable de se connecter à Stockage Blob ou Stockage Table peut être utilisé pour explorer, analyser ou utiliser le contenu. La liste suivante est un début :

+ [Navigateur de stockage](knowledge-store-view-storage-explorer.md) pour voir la structure et le contenu du document enrichi. Considérez-le comme votre outil de référence pour afficher le contenu de la base de connaissances.

+ [Power BI](knowledge-store-connect-power-bi.md) pour la création de rapports et l’analyse. 

+ [Azure Data Factory](../data-factory/index.yml) permet d'effectuer d'autres manipulations.

## <a name="content-lifecycle"></a>Cycle de vie du contenu

Chaque fois que vous exécutez l’indexeur et l’ensemble de compétences, la base de connaissances est mise à jour si l’ensemble de compétences ou les données sources sous-jacentes ont changé. Les changements détectés par l’indexeur sont propagés via le processus d’enrichissement aux projections dans la base de connaissances, ce qui permet de garantir que les données projetées sont une représentation actualisée du contenu de la source de données d’origine. 

> [!Note]
> Bien que vous puissiez modifier les données dans les projections, les mises à jour sont remplacées au prochain appel du pipeline, en supposant que le document présent dans les données sources soit mis à jour. 

### <a name="changes-in-source-data"></a>Changements dans les données sources

Pour les sources de données qui prennent en charge le suivi des modifications, un indexeur traite les nouveaux documents ainsi que ceux auxquels des changements ont été apportés, tout en ignorant les documents existants déjà traités. Les informations d’horodatage varient en fonction de la source de données. Toutefois, dans un conteneur d’objets blob, l’indexeur examine la date `lastmodified` pour déterminer quels sont les objets blob à ingérer.

### <a name="changes-to-a-skillset"></a>Changements apportés à un ensemble de compétences

Si vous apportez des changements à un ensemble de compétences, vous devez [activer la mise en cache des documents enrichis](cognitive-search-incremental-indexing-conceptual.md) pour réutiliser les enrichissements existants dans la mesure du possible.

En l’absence de mise en cache incrémentielle, l’indexeur traite toujours les documents dans l’ordre de la limite supérieure, sans revenir en arrière. L’indexeur traite les objets blob triés par `lastModified`, quels que soient les changements apportés aux paramètres de l’indexeur ou à l’ensemble de compétences. Si vous changez un ensemble de compétences, les documents traités ne sont pas mis à jour pour refléter le nouvel ensemble de compétences. Les documents traités après l’ajout de changements à l’ensemble de compétences utilisent le nouvel ensemble de compétences. Ainsi, les documents d’index sont un mélange d’anciens et de nouveaux ensembles de compétences.

Avec la mise en cache incrémentielle et après la mise à jour d’un ensemble de compétences, l’indexeur réutilise les enrichissements non affectés par les changements apportés à l’ensemble de compétences. Les enrichissements en amont sont extraits du cache, tout comme les enrichissements indépendants et isolés de la compétence à laquelle des changements ont été apportés.

### <a name="deletions"></a>Suppressions

Bien qu’un indexeur crée et mette à jour des structures et du contenu dans le service Stockage Azure, il ne les supprime pas. Les projections continuent d’exister même quand l’indexeur ou l’ensemble de compétences est supprimé. En tant que propriétaire du compte de stockage, vous devez supprimer une projection si elle n’est plus nécessaire. 

## <a name="next-steps"></a>Étapes suivantes

La base de connaissances offre la persistance de documents enrichis, utile lors de la conception d’un ensemble de compétences, ou la création de structures et de contenu pour une consommation par les applications clientes qui peuvent accéder à un compte de stockage Azure.

L’approche la plus simple pour créer des documents enrichis consiste à [utiliser le portail](knowledge-store-create-portal.md). Toutefois, vous pouvez également utiliser Postman et l’API REST, ce qui est plus utile si vous souhaitez obtenir des insights sur la façon dont les objets sont créés et référencés par programmation.

> [!div class="nextstepaction"]
> [Créer une base de connaissances à l’aide de Postman et de REST](knowledge-store-create-rest.md)
