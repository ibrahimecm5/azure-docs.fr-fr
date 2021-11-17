---
title: Concepts de projection
titleSuffix: Azure Cognitive Search
description: Présente les concepts de projection et les meilleures pratiques. Si vous créez un magasin de connaissances dans Recherche cognitive, les projections déterminent le type, la quantité et la composition des objets dans stockage Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/15/2021
ms.openlocfilehash: 1b698ae5b03a7d622e4fccb717789be8cb330057
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131561287"
---
# <a name="knowledge-store-projections-in-azure-cognitive-search"></a>« Projections » de base de connaissances dans Recherche cognitive Azure

Les projections sont les tables, les objets et les fichiers physiques dans une [**base de connaissances**](knowledge-store-concept-intro.md) qui acceptent du contenu à partir d’un pipeline d’enrichissement d’intelligence artificielle de Recherche cognitive. Si vous créez une base de connaissances, la définition et la mise en forme de projections constituent la majeure partie du travail.

Cet article présente les concepts de projection et le flux de travail afin que vous disposiez d’un arrière-plan avant de commencer le codage.

Les projections sont définies dans l’ensemble de compétences de Recherche cognitive, mais les résultats finaux sont les projections de tables, d’objets et de fichiers image dans Stockage Azure.

:::image type="content" source="media/knowledge-store-concept-intro/kstore-in-storage-explorer.png" alt-text="Projections exprimées dans Stockage Azure" border="true":::

## <a name="types-of-projections-and-usage"></a>Types de projections et d’utilisation

Une base de connaissances est une construction logique exprimée physiquement sous la forme d’un regroupement relâché de tables, d’objets JSON ou de fichiers image binaires dans Stockage Azure.

| Projection | Stockage | Utilisation |
|------------|---------|-------|
| [Tables](knowledge-store-projections-examples.md#define-a-table-projection) | Stockage de table Azure | Utilisé pour les données qui sont le mieux représentées sous la forme de lignes et de colonnes, ou chaque fois que vous avez besoin de représentations précises de vos données (par exemple, en tant que trames de données). Les projections de table vous permettent de définir une forme schématisée, à l’aide d’une [Compétence Modélisateur ou de la mise en forme incluse](knowledge-store-projection-shape.md) pour spécifier des colonnes et des lignes. Vous pouvez organiser le contenu en plusieurs tables selon des principes de normalisation familiers. Les tables qui se trouvent dans le même groupe sont automatiquement liées. |
| [Objets](knowledge-store-projections-examples.md#define-an-object-projection) | Stockage Blob Azure | Utilisé lorsque vous avez besoin de la représentation JSON complète de vos données et de vos enrichissements dans un document JSON. Comme pour les projections de table, seuls les objets JSON valides peuvent être projetés en tant qu’objets, et la modélisation peut vous aider à le faire. |
| [Fichiers](knowledge-store-projections-examples.md#define-a-file-projection) | Stockage Blob Azure | Utilisé quand vous devez enregistrer des fichiers image binaires normalisés. |

## <a name="projection-definition"></a>Définition de la projection

Les projections sont spécifiées sous la propriété « knowledgeStore » d’une propriété d’un [ensemble de compétences](/rest/api/searchservice/create-skillset). Les définitions de projection sont utilisées lors de l’appel de l’indexeur pour créer et charger des objets dans Stockage Azure avec un contenu enrichi. Si vous ne connaissez pas ces concepts, commencez par [Enrichissement par IA](cognitive-search-concept-intro.md) pour profiter d’une présentation.

L’exemple suivant illustre l’emplacement des projections sous knowledgeStore et la construction de base. Le nom, le type et la source de contenu constituent une définition de projection.

```json
"knowledgeStore" : {
    "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
    "projections": [
      {
        "tables": [
          { "tableName": "ks-museums-main", "generatedKeyName": "ID", "source": "/document/tableprojection" },
          { "tableName": "ks-museumEntities", "generatedKeyName": "ID","source": "/document/tableprojection/Entities/*" }
        ],
        "objects": [
          { "storageContainer": "ks-museums", "generatedKeyName": "ID", "source": "/document/objectprojection" }
        ],
        "files": [ ]
      }
    ]
```

## <a name="projection-groups"></a>Groupes de projections

Les projections sont un tableau de collections complexes, ce qui signifie que vous pouvez spécifier plusieurs ensembles de chaque type. Il est courant d’utiliser un seul groupe de projection, mais vous pouvez en utiliser plusieurs si les besoins en matière de stockage incluent la prise en charge de différents outils et scénarios. À titre d’exemple, vous pouvez utiliser un groupe pour la conception et le débogage d’un ensemble de compétences, tandis qu’un deuxième ensemble collecte la sortie utilisée pour une application en ligne et qu’un troisième est utilisé pour les charges de travail de science des données.

La sortie des mêmes compétences est utilisée pour remplir tous les groupes sous projections. L’exemple suivant en montre deux.

```json
"knowledgeStore" : {
    "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
    "projections": [
        {
            "tables": [],
            "objects": [],
            "files": []
        }, 
        {
            "tables": [],
            "objects": [],
            "files": []
        }
    ]
}
```

Les groupes de projection disposent des principales caractéristiques d’exclusivité mutuelle et de parenté suivantes. 

| Principe | Description |
|-----------|-------------|
| Exclusivité mutuelle | Chaque groupe est totalement isolé des autres groupes afin de prendre en charge différents scénarios de mise en forme des données. Par exemple, si vous testez différentes structures et combinaisons de tables, vous devez placer chaque ensemble dans un groupe de projection différent pour le test AB. Chaque groupe obtient des données de la même source (arborescence d’enrichissement), mais il est totalement isolé de la combinaison table-objet-fichier de tout groupe de projection appairé.|
| Parenté | Au sein d’un groupe de projection, le contenu des tables, des objets et des fichiers est lié. La base de connaissances utilise des clés générées comme points de référence pour un nœud parent commun. Par exemple, imaginez un scénario dans lequel vous avez un document contenant des images et du texte. Vous pouvez projeter le texte vers des tables, et les images vers des fichiers binaires, et les objets disposeront d’une colonne/propriété contenant l’URL du fichier.|

## <a name="projection-source"></a>« Source » de projection

Le paramètre source est le troisième composant d’une définition de projection. Étant donné que les projections stockent les données d’un pipeline d’enrichissement de l’IA, la source d’une projection est toujours le résultat d’une compétence. Par conséquent, la sortie peut être un champ unique (par exemple, un champ de texte traduit), mais il s’agit souvent d’une référence à une forme de données.

Les formes de données proviennent de vos compétences. Parmi toutes les compétences intégrées fournies dans Recherche cognitive, il existe une compétence d’utilitaire appelée [**Compétence de modélisateur**](cognitive-search-skill-shaper.md), utilisée pour créer des formes de données. Vous pouvez inclure des compétences de modélisateur (autant que nécessaire) pour prendre en charge les projections dans la base de connaissances.

Les formes sont fréquemment utilisées avec des projections de table, où la forme spécifie non seulement les lignes qui sont placées dans la table, mais également les colonnes qui sont créées (vous pouvez également transformer une forme en une projection d’objet).

Les formes peuvent être complexes et il est impossible de les aborder en détail ici, mais l’exemple suivant illustre brièvement une forme de base. La sortie de la compétence de modélisateur est spécifiée comme source d’une projection de table. Dans la projection de table elle-même se trouve les colonnes « metadata-storage_path », « reviews_text », « reviews_title », et ainsi de suite, tel que spécifié dans la forme.

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "name": "ShaperForTables",
    "description": null,
    "context": "/document",
    "inputs": [
        {
            "name": "metadata_storage_path",
            "source": "/document/metadata_storage_path",
            "sourceContext": null,
            "inputs": []
        },
        {
          "name": "reviews_text",
          "source": "/document/reviews_text"
        }, 
        {
          "name": "reviews_title",
          "source": "/document/reviews_title"
        },
        {
          "name": "reviews_username",
          "source": "/document/reviews_username"
        },
    ],
    "outputs": [
      {
        "name": "output",
        "targetName": "mytableprojection"
      }
    ]
}
```

## <a name="projection-lifecycle"></a>Cycle de vie de projection

Les projections ont un cycle de vie qui est lié aux données sources de votre source de données. Lorsque les données sources sont mises à jour et réindexées, les projections sont mises à jour avec les résultats des enrichissements, ce qui garantit que vos projections sont cohérentes avec les données de votre source de données. Toutefois, les projections sont également stockées de manière indépendante dans Stockage Azure. Elles ne seront pas supprimées lorsque l’indexeur ou le service de recherche lui-même sera supprimé. 

## <a name="consume-in-apps"></a>Consommer dans les applications

Après l’exécution de l’indexeur, connectez-vous aux projections et consommez les données dans d’autres applications et charges de travail.

+ Utilisez le [navigateur de stockage](knowledge-store-view-storage-explorer.md) pour vérifier la création des objets et le contenu.

+ Utilisez [Power BI pour l’exploration des données](knowledge-store-connect-power-bi.md). Cet outil fonctionne mieux lorsque les données se trouvent dans le Stockage Table Azure. Dans Power BI, vous pouvez manipuler des données dans de nouvelles tables qui sont plus faciles à interroger et à analyser.

+ Utilisez des données enrichies dans un conteneur d’objets BLOB dans un pipeline de science des données. Par exemple, vous pouvez [charger les données à partir d’objets BLOB dans un DataFrame Pandas](/azure/architecture/data-science-process/explore-data-blob).

+ Enfin, si vous devez exporter vos données de la base de connaissances, Azure Data Factory comprend des connecteurs pour exporter les données et les placer dans la base de données de votre choix.

## <a name="checklist-for-getting-started"></a>Liste de contrôle pour la prise en main

Rappelez-vous que les projections s’appliquent exclusivement aux bases de connaissances et ne sont pas utilisées pour structurer un index de recherche.

1. Dans Stockage Azure, obtenez une chaîne de connexion à partir des **Clés d’accès** et vérifiez que le compte est StorageV2 (usage général V2).

1. Dans Stockage Azure, familiarisez-vous avec le contenu existant dans les conteneurs et les tables afin de choisir des noms non conflictuels pour les projections. Une base de connaissances est une collection libre de tables et de conteneurs. Envisagez d’adopter une convention d’affectation de noms pour effectuer le suivi des objets connexes.

1. Dans Recherche cognitive, [activez le mise en cache d’enrichissement (préversion)](search-howto-incremental-index.md) dans l’indexeur, puis [exécutez l’indexeur](search-howto-run-reset-indexers.md) pour exécuter l’ensemble de compétences et remplir le cache. Il s’agit d’une fonctionnalité en préversion. Veillez donc à utiliser l’API REST en préversion (api-version=2020-06-30-preview ou version ultérieure) sur la demande de l’indexeur. Une fois le cache rempli, vous pouvez modifier gratuitement les définitions de projection dans une base de connaissances (tant que les compétences elles-mêmes ne sont pas modifiées).

1. Dans votre code, toutes les projections sont définies uniquement dans une ensemble de compétences. Il n’existe aucune propriété d’indexeur (comme les mappages de champs ou les mappages de champs de sortie) qui s’appliquent aux projections. Dans une définition d’ensemble de compétences, vous allez vous concentrer sur deux domaines : la propriété knowledgeStore et le tableau des compétences.

   1. Sous knowledgeStore, spécifiez les projections de table, d’objet et de fichier dans la section `projections`. Le type d’objet, le nom d’objet et la quantité (selon le nombre de projections que vous définissez) sont déterminés dans cette section.

   1. À partir du tableau de compétences, déterminez les résultats de compétences qui seront référencées dans le `source` de chaque projection. Toutes les projections ont une source. La source peut être la sortie d’une compétence en amont, mais il s’agit souvent de la sortie d’une compétence de modélisateur. La composition de votre projection est déterminée à l’aide de formes. 

1. Si vous ajoutez des projections à un ensemble de compétences existant, [mettez à jour l’ensemble de compétences](/rest/api/searchservice/update-skillset) et [exécutez l’indexeur](/rest/api/searchservice/run-indexer).

1. Vérifiez vos résultats dans stockage Azure. Lors des exécutions suivantes, évitez de nommer des collisions en supprimant des objets dans Stockage Azure ou en modifiant les noms de projet dans les compétences.

## <a name="next-steps"></a>Étapes suivantes

Passez en revue la syntaxe et les exemples pour chaque type de projection.

> [!div class="nextstepaction"]
> [Définir des projections dans une base de connaissances](knowledge-store-projections-examples.md)