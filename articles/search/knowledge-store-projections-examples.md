---
title: Définir des projections
titleSuffix: Azure Cognitive Search
description: Découvrez comment définir des projections de tables, d’objets et de fichiers dans une base de connaissances en examinant la syntaxe et des exemples.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/15/2021
ms.openlocfilehash: 0aa980d2268f6d055f3aa05a8e30d57712c022df
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131563622"
---
# <a name="define-projections-in-a-knowledge-store"></a>Définir des projections dans une base de connaissances

Une [projection](knowledge-store-projection-overview.md) est un composant de la définition de la [base de connaissances](knowledge-store-concept-intro.md) qui détermine la façon dont le contenu enrichi par l’intelligence artificielle est stocké dans Stockage Azure. Les projections déterminent le type, la quantité et la composition des structures de données contenant votre contenu.

Dans cet article, vous allez découvrir la syntaxe de chaque type de projection :

+ [Projections de table](#define-a-table-projection)
+ [Projections d’objet](#define-an-object-projection)
+ [Projections de fichier](#define-a-file-projection)

Les projections sont spécifiées sous la propriété « knowledgeStore » d’une propriété d’un ensemble de compétences.

```json
"knowledgeStore" : {
    "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
    "projections": [
      {
        "tables": [ ],
        "objects": [ ],
        "files": [ ]
      }
    ]
```

Si vous avez besoin de plus d’informations avant de commencer, consultez [cette liste de contrôle](knowledge-store-projection-overview.md#checklist-for-getting-started) pour obtenir des conseils et découvrir un flux de travail.

> [!TIP]
> Lorsque vous développez des projections, [activez la mise en cache d’enrichissement (préversion)](search-howto-incremental-index.md) afin de pouvoir réutiliser les enrichissements existants tout en modifiant les définitions de projection.  Il s’agit d’une fonctionnalité en préversion. Veillez donc à utiliser l’API REST en préversion (api-version=2020-06-30-preview ou version ultérieure) sur la demande de l’indexeur. Sans mise en cache, les modifications simples apportées à une projection entraînent un retraitement complet du contenu enrichi. En mettant en cache les enrichissements, vous pouvez effectuer une itération sur les projections sans frais de traitement de l’ensemble de compétences.

## <a name="requirements"></a>Spécifications

Toutes les projections ont des propriétés de source et de destination. La source est toujours le contenu d’une arborescence d’enrichissement créée pendant l’exécution de l’ensemble de compétences. La destination est le nom et le type de l’objet qui sera créé et chargé dans le Stockage Azure.

À l’exception des projections de fichiers qui acceptent uniquement des images binaires, la source doit être :

+ JSON valide
+ Un chemin d’accès à un nœud dans l’arborescence d’enrichissement (par exemple `"source": "/document/objectprojection"`)

Si un nœud peut être résolu en un champ unique, une représentation plus courante est une référence à une forme complexe. Les formes complexes sont créées à l’aide d’une méthodologie de mise en forme, soit une [compétence Modélisateur](cognitive-search-skill-shaper.md) soit une [définition de mise en forme en ligne](knowledge-store-projection-shape.md#inline-shape), en général une compétence Modélisateur.

Les compétences Modélisateur sont à privilégier, car la plupart des compétences ne génèrent pas elles-mêmes de code JSON valide. Dans de nombreux cas, la même forme de données créée par une compétence Modélisateur peut être utilisée de la même façon par les projections de table et d’objet.

En fonction des exigences d’entrée de la source, le fait de savoir comment [mettre en forme les données](knowledge-store-projection-shape.md) devient une exigence pratique pour la définition de la projection, en particulier si vous travaillez avec des tables.

## <a name="define-a-table-projection"></a>Définition d’une projection de table

Les projections de table sont recommandées pour les scénarios qui nécessitent une exploration des données, comme l’analyse avec Power BI ou les charges de travail qui consomment des trames de données. La section des tables d’un tableau projections est la liste de tables que vous souhaitez projeter.

Pour définir une projection de table, utilisez le tableau `tables` dans la propriété projections. Une projection de table a trois propriétés obligatoires :

| Propriété | Description |
|----------|-------------|
| tableName | Détermine le nom d’une table créée dans Stockage Table Azure.  |
| generatedKeyName | Nom de colonne de la clé qui identifie de façon unique chaque ligne. La valeur est générée par le système. Si vous omettez cette propriété, une colonne est créée automatiquement avec pour convention de nommage l’association du nom de la table et de la « clé ». |
| source | Chemin d’un nœud dans une arborescence d’enrichissement. Le nœud doit être une référence à une forme complexe qui détermine quelles sont les colonnes qui sont créées dans la table.|

Dans les projections de table, « source » est généralement la sortie d’une [compétence Modélisateur](cognitive-search-skill-shaper.md) qui définit la forme de la table. Les tables comportent des lignes et des colonnes, et la modélisation est le mécanisme par lequel les lignes et les colonnes sont spécifiées. Vous pouvez utiliser une [compétence Modélisateur ou des modélisations inlined](knowledge-store-projection-shape.md). La compétence Modélisateur produit un JSON valide, mais la source peut être la sortie de n’importe quelle compétence, tant que le JSON est valide.

> [!NOTE]
> Les projections de table sont soumises aux [limites de stockage](/rest/api/storageservices/understanding-the-table-service-data-model) imposées par le Stockage Azure. La taille maximale respective d’une entité et d’une propriété est de 1 Mo et 64 Ko. Ces contraintes font des tables une bonne solution pour stocker un grand nombre de petites entités.

### <a name="single-table-example"></a>Exemple de table unique

Le schéma de la table est spécifié en partie par la projection (nom et clé de la table) mais également par la source, qui fournit la modélisation de la table (colonnes). Cet exemple présente une seule table pour vous permettre de vous concentrer sur les détails de la définition.

```json
"projections" : [
  {
    "tables": [
      { "tableName": "Hotels", "generatedKeyName": "HotelId", "source": "/document/tableprojection" }
    ]
  }
]
```

Les colonnes sont dérivées de la « source ». La forme de données suivante contenant HotelId, HotelName, Category et Description entraîne la création de ces colonnes dans la table.

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "name": "#3",
    "description": null,
    "context": "/document",
    "inputs": [
    {
        "name": "HotelId",
        "source": "/document/HotelId"
    },
    {
        "name": "HotelName",
        "source": "/document/HotelName"
    },
    {
        "name": "Category",
        "source": "/document/Category"
    },
    {
        "name": "Description",
        "source": "/document/Description"
    },
    ],
    "outputs": [
    {
        "name": "output",
        "targetName": "tableprojection"
    }
    ]
}
```

### <a name="multiple-table-slicing-example"></a>Exemple de table multiple (découpage)

Un modèle commun pour les projections de tables consiste à avoir plusieurs tables associées, où les colonnes partitionKey et rowKey générées par le système sont créées pour prendre en charge les relations entre tables pour toutes les tables sous le même groupe de projection. 

La création de plusieurs tables peut être utile si vous souhaitez contrôler la façon dont les données associées sont agrégées. Si le contenu enrichi comporte des composants non liés ou indépendants, par exemple si les mots clés extraits d’un document peuvent ne pas être liés aux entités reconnues dans le même document, vous pouvez fractionner ces champs en tables adjacentes.

Lors de la projection dans plusieurs tables, la forme complète est projetée dans chaque table, sauf si un nœud enfant est la source d’une autre table du même groupe. Si vous ajoutez une projection dont le chemin source est un nœud enfant d’une projection existante, le nœud enfant est divisé à partir du nœud parent et projeté dans la nouvelle table associée. Cette technique vous permet de définir un nœud unique dans une compétence Modélisateur qui peut être la source de toutes vos projections.

Le modèle des tables mutiples se compose des éléments suivants :

+ Une table en tant que table parente ou table principale
+ Tables supplémentaires pour contenir des secteurs du contenu enrichi

Par exemple, supposons qu’une compétence Modélisateur génère un « EnrichedShape » qui contient des informations sur les hôtels, ainsi que du contenu enrichi comme des expressions clés, des emplacements et des organisations. La table principale contient des champs qui décrivent l’hôtel (ID, nom, description, adresse, catégorie). Les expressions clés obtiennent la colonne d’expression clé. Les entités obtiennent les colonnes d’entité.

```json
"projections" : [
  {
    "tables": [
    { "tableName": "MainTable", "generatedKeyName": "HotelId", "source": "/document/EnrichedShape" },
    { "tableName": "KeyPhrases", "generatedKeyName": "KeyPhraseId", "source": "/document/EnrichedShape/*/KeyPhrases/*" },
    { "tableName": "Entities", "generatedKeyName": "EntityId", "source": "/document/EnrichedShape/*/Entities/*" }
    ]
  }
]
```

### <a name="naming-relationships"></a>Nommage des relations

Les propriétés `generatedKeyName` et `referenceKeyName` sont utilisées pour lier des données entre des tables ou même entre des types de projection. Chaque ligne de la table enfant a une propriété qui pointe vers le parent. Le nom de la colonne ou de la propriété dans l’enfant est le `referenceKeyName` du parent. Quand le `referenceKeyName` n’est pas fourni, le service le définit par défaut sur le `generatedKeyName` du parent. 

Power BI utilise ces clés générées pour découvrir les relations dans les tables. S’il est nécessaire que la colonne de la table enfant soit nommée différemment, définissez la propriété `referenceKeyName` sur la table . Par exemple, vous pouvez définir `generatedKeyName` en tant qu’ID pour la table tblDocument et `referenceKeyName` en tant que DocumentID. Ainsi, la colonne des tables tblEntities et tblKeyPhrases contenant l’ID de document se nomme DocumentID.

## <a name="define-an-object-projection"></a>Définir une projection d’objet

Les projections d’objet sont des représentations JSON de l’arborescence d’enrichissement pouvant provenir de n’importe quel nœud. En comparaison avec les projections de table, les projections d’objets sont plus simples à définir et elles sont utilisées lors de la projection de documents entiers. Les projections d’objet sont limitées à une seule projection dans un conteneur et elles ne peuvent pas être découpées.

Pour définir une projection d’objet, utilisez le tableau `objects` dans la propriété projections. Une projection d’objet a trois propriétés obligatoires :

| Propriété | Description |
|----------|-------------|
| storageContainer | Détermine le nom d’un conteneur créé dans le Stockage Azure.  |
| generatedKeyName | Nom de colonne de la clé qui identifie de façon unique chaque ligne. La valeur est générée par le système. Si vous omettez cette propriété, une colonne est créée automatiquement avec pour convention de nommage l’association du nom de la table et de la « clé ». |
| source | Chemin d’accès à un nœud de l’arborescence d’enrichissement qui est la racine de la projection. Le nœud est en général une référence à une forme de données complexe qui détermine la structure des objets BLOB.|

L’exemple suivant projette des documents d’hôtel individuels, un document d’hôtel par objet blob, dans un conteneur appelé `hotels`.

```json
"knowledgeStore": {
  "storageConnectionString": "an Azure storage connection string",
  "projections" : [
    {
      "tables": [ ]
    },
    {
      "objects": [
        {
        "storageContainer": "hotels",
        "source": "/document/objectprojection",
        }
      ]
    },
    {
        "files": [ ]
    }
  ]
}
```

La source est la sortie d’une compétence Modélisateur appelée « objectprojection ». Chaque objet blob aura une représentation JSON de chaque entrée de champ.

```json
    {
      "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
      "name": "#3",
      "description": null,
      "context": "/document",
      "inputs": [
        {
          "name": "HotelId",
          "source": "/document/HotelId"
        },
        {
          "name": "HotelName",
          "source": "/document/HotelName"
        },
        {
          "name": "Category",
          "source": "/document/Category"
        },
        {
          "name": "keyPhrases",
          "source": "/document/HotelId/keyphrases/*"
        },
      ],
      "outputs": [
        {
          "name": "output",
          "targetName": "objectprojection"
        }
      ]
    }
```

## <a name="define-a-file-projection"></a>Définir une projection de fichiers

Les projections de fichiers sont toujours des images normalisées binaires, où la normalisation fait référence au redimensionnement et à la rotation potentiels pour leur utilisation dans l’exécution des ensembles de compétences. Les projections de fichiers, de façon similaire aux projections d’objet, sont créées sous forme d’objets blob dans Stockage Azure et contiennent des données binaires (et non JSON).

Pour définir une projection de fichiers, utilisez le tableau `files` dans la propriété projections. Une projection de fichiers a trois propriétés obligatoires :

| Propriété | Description |
|----------|-------------|
| storageContainer | Détermine le nom d’un conteneur créé dans le Stockage Azure.  |
| generatedKeyName | Nom de colonne de la clé qui identifie de façon unique chaque ligne. La valeur est générée par le système. Si vous omettez cette propriété, une colonne est créée automatiquement avec pour convention de nommage l’association du nom de la table et de la « clé ». |
| source | Chemin d’accès à un nœud de l’arborescence d’enrichissement qui est la racine de la projection. Pour les fichiers images, la source est toujours `/document/normalized_images/*`.  Les projections de fichier agissent seulement sur la collection `normalized_images`. Ni les indexeurs, ni un ensemble de compétences ne vont traverser l’image non normalisée d’origine.|

La destination est toujours un conteneur d’objets blob, avec un préfixe de dossier correspondant à la valeur encodée en base64 de l’ID de document. S’il y a plusieurs images, elles seront placées dans le même dossier. Les projections de fichiers ne peuvent pas partager le même conteneur que les projections d’objets. Elles doivent être projetées vers un conteneur distinct. 

L’exemple suivant projette toutes les images normalisées extraites du nœud de document d’un document enrichi dans un conteneur appelé `myImages`.

```json
"projections": [
    {
        "tables": [ ],
        "objects": [ ],
        "files": [
            {
                "storageContainer": "myImages",
                "source": "/document/normalized_images/*"
            }
        ]
    }
]
```

## <a name="test-projections"></a>Projections de test

Vous pouvez traiter des projections de test en procédant comme suit :

1. Affectez à la propriété `storageConnectionString` de la base de connaissances une chaîne de connexion de compte de stockage universel V2 valide.  

1. [Mettez à jour les ensembles de compétences](/rest/api/searchservice/update-skillset) en émettant une demande PUT avec votre définition de projection dans le corps des compétences.

1. [Exécutez l’indexeur](/rest/api/searchservice/run-indexer) pour mettre l’ensemble de compétences en exécution. 

1. [Surveillez l’exécution de l’indexeur](search-howto-monitor-indexers.md) pour vérifier la progression et intercepter les erreurs.

1. [Utilisez le navigateur de stockage](knowledge-store-view-storage-explorer.md) pour vérifier la création des objets dans le Stockage Azure.

1. Si vous projetez des tables, [importez-les dans Power BI](knowledge-store-connect-power-bi.md) pour la manipulation et la consultation. Dans la plupart des cas, Power BI découvrira automatiquement les relations entre les tables.

## <a name="common-issues"></a>Problèmes courants

L’omission de l’une des étapes suivantes peut aboutir à des résultats inattendus. Si votre sortie ne s’affiche pas correctement, vérifiez les conditions suivantes.

+ Les enrichissements de chaîne ne sont pas mis en forme dans un JSON valide. Quand des chaînes sont enrichies, par exemple la chaîne `merged_content` enrichie d’expressions clés, la propriété enrichie est représentée comme enfant de `merged_content` dans l’arborescence des enrichissements. La représentation par défaut n’est pas un JSON correctement mis en forme. Au moment de la projection, vous devez donc transformer l’enrichissement en un objet JSON valide avec un nom et une valeur. L’utilisation d’une compétence Modélisateur ou la définition de formes en ligne vous aidera à résoudre ce problème.

+ Omission de `/*` à la fin d’un chemin source. Si la source d’une projection est `/document/projectionShape/keyPhrases`, le tableau d’expressions clés est projeté sous la forme d’un objet ou d’une ligne unique. À la place, définissez le chemin source sur `/document/projectionShape/keyPhrases/*` afin d’obtenir une seule ligne ou un seul objet pour chacune des expressions clés.

+ Erreurs dans la syntaxe du chemin. Les [sélecteurs de chemin](cognitive-search-concept-annotations-syntax.md) respectent la casse et peuvent entraîner des avertissements d’entrée manquante si vous n’utilisez pas la casse exacte du sélecteur. 

## <a name="next-steps"></a>Étapes suivantes

L’étape suivante vous guide tout au long de la mise en forme et de la projection de la sortie à partir d’un ensemble de compétences riche. Si votre ensemble de compétences est complexe, l’article suivant fournit des exemples de formes et de projections.

> [!div class="nextstepaction"]
> [Exemple détaillé de formes et de projections](knowledge-store-projection-example-long.md)
