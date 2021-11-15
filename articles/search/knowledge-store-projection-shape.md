---
title: Mise en forme des données pour le magasin de connaissances
titleSuffix: Azure Cognitive Search
description: Définissez les structures de données dans un magasin de connaissances en créant des formes de données et en les passant à une projection.
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/15/2021
ms.openlocfilehash: f0558d7e3cffd744ec4effa1d4f81f359e909e97
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131503168"
---
# <a name="shaping-data-for-projection-into-a-knowledge-store"></a>Mise en forme des données pour la projection dans un magasin de connaissances

Dans Recherche cognitive Azure, la « mise en forme des données » décrit une étape du [flux de travail du magasin de connaissances](knowledge-store-concept-intro.md) qui crée une représentation des données du contenu que vous souhaitez projeter dans des tables, des objets et des fichiers dans Stockage Azure.

À mesure que les compétences s’exécutent, les sorties sont écrites dans une arborescence d’enrichissements dans une hiérarchie de nœuds, et bien que vous souhaitiez afficher et consommer l’arborescence d’enrichissements dans son intégralité, il est plus probable que vous souhaitiez plus de granularité en créant des sous-ensembles de nœuds pour différents scénarios, en plaçant par exemple les nœuds associés à du texte traduit ou à des entités extraites dans des tables spécifiques.

En soi, l’arborescence d’enrichissements n’inclut pas de logique qui indiquerait comment son contenu est représenté dans un magasin de connaissances. Les formes de données comblent cette lacune en fournissant le schéma de ce qui est placé dans chaque table, objet et projection de fichier. Vous pouvez considérer une forme de données comme une définition ou une vue personnalisée des données enrichies. Vous pouvez créer autant de formes que nécessaire, puis les affecter à des [projections](knowledge-store-projection-overview.md) dans une définition de magasin de connaissances. 

## <a name="approaches-for-creating-shapes"></a>Approches permettant la création de formes

Deux méthodes pour mettre en forme du contenu enrichi afin qu’il puisse être projeté dans un magasin de connaissances sont possibles :

+ Utilisez la [compétence Modéliseur](cognitive-search-skill-shaper.md) pour créer des nœuds dans une arborescence d’enrichissements, expressément utilisés pour la projection. La plupart des compétences créent un nouveau contenu. En revanche, une compétence Modéliseur utilise des nœuds existants, généralement pour consolider plusieurs nœuds dans un seul objet complexe. Cela est utile pour les tables, où vous souhaitez que la sortie de plusieurs nœuds soit exprimée physiquement sous forme de colonnes dans la table. 

+ Utilisez une mise en forme incluse dans la définition de la projection.

La compétence Modéliseur externalise la forme pour pouvoir l’utiliser par plusieurs projections, voire d’autres compétences. Elle garantit également que toutes les mutations de l’arborescence d’enrichissements sont contenues dans la compétence, et que la sortie est un objet qui peut être réutilisé. Par contre, la mise en forme incluse vous permet de créer la forme dont vous avez besoin, mais elle constitue un objet anonyme qui est utilisable uniquement dans la projection pour laquelle elle est définie.

Les approches peuvent s’utiliser ensemble ou séparément. Cet article présente les deux : une compétence Modéliseur pour les projections de table et une mise en forme incluse avec la projection de table d’expressions clés.

## <a name="use-a-shaper-skill"></a>Utiliser une compétence Modéliseur

Les compétences Modéliseur sont généralement placées à la fin d’un ensemble de compétences, créant ainsi une vue des données que vous souhaitez passer à une projection. Cet exemple crée une forme appelée « tableprojection » qui contient les nœuds suivants : « reviews_text », « reviews_title », « AzureSearch_DocumentKey », ainsi que les scores de sentiment et les expressions clés des révisions paginées. 

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "name": "#5",
    "description": null,
    "context": "/document",
    "inputs": [        
        {
            "name": "reviews_text",
            "source": "/document/reviews_text",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "reviews_title",
            "source": "/document/reviews_title",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "AzureSearch_DocumentKey",
            "source": "/document/AzureSearch_DocumentKey",
            "sourceContext": null,
            "inputs": []
        },  
        {
            "name": "pages",
            "source": null,
            "sourceContext": "/document/reviews_text/pages/*",
            "inputs": [
                {
                    "name": "Sentiment",
                    "source": "/document/reviews_text/pages/*/Sentiment",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "LanguageCode",
                    "source": "/document/Language",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "Page",
                    "source": "/document/reviews_text/pages/*",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "keyphrase",
                    "sourceContext": "/document/reviews_text/pages/*/Keyphrases/*",
                    "inputs": [
                        {
                            "source": "/document/reviews_text/pages/*/Keyphrases/*",
                            "name": "Keyphrases"
                        }
                    ]
                }
            ]
        }
    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "tableprojection"
        }
    ]
}
```

### <a name="sourcecontext-property"></a>Propriété SourceContext

Dans une compétence Modéliseur, une entrée peut avoir un élément `sourceContext`. Cette même propriété peut également être utilisée dans des formes incluses dans des projections. 

`sourceContext` est utilisé pour construire des objets imbriqués à plusieurs niveaux dans un pipeline d’enrichissement. Si l’entrée est dans un contexte *différent* de celui de la compétence, utilisez *sourceContext*. La propriété *sourceContext* implique que vous définissiez une entrée imbriquée avec l’élément spécifique traité comme source. 

Dans l’exemple ci-dessus, l’analyse des sentiments et l’extraction des expressions clés ont été effectuées sur du texte qui a été fractionné en pages pour une analyse plus efficace. En supposant que vous souhaitiez que les scores et les expressions soient projetés dans une table, vous devez maintenant définir le contexte sur l’entrée imbriquée qui fournit le score et l’expression.

### <a name="projecting-a-shape-into-multiple-tables"></a>Projection d’une forme dans plusieurs tables

Avec le nœud `tableprojection` défini dans la section `outputs` ci-dessus, vous pouvez diviser des parties du nœud `tableprojection` en tables individuelles associées :

```json
"projections": [
    {
        "tables": [
            {
                "tableName": "hotelReviewsDocument",
                "generatedKeyName": "Documentid",
                "source": "/document/tableprojection"
            },
            {
                "tableName": "hotelReviewsPages",
                "generatedKeyName": "Pagesid",
                "source": "/document/tableprojection/pages/*"
            },
            {
                "tableName": "hotelReviewsKeyPhrases",
                "generatedKeyName": "KeyPhrasesid",
                "source": "/document/tableprojection/pages/*/keyphrase/*"
            }
        ]
    }
]
```

## <a name="inline-shape-for-table-projections"></a>Forme incluse pour les projections de table

La mise en forme incluse permet de former de nouvelles formes dans la définition de projection. La mise en forme incluse a les caractéristiques suivantes :

+ La forme ne peut être utilisée que par la projection qui la contient.
+ La forme peut être identique à ce qu’une compétence Modéliseur peut produire.

Une forme incluse est créée à l’aide de `sourceContext` et de `inputs`.

| Propriété | Description |
|----------|-------------|
| sourceContext | Définit la racine de la projection.  |
| inputs | Chaque entrée est une colonne de la table. Nom correspond au nom de la colonne. La source correspond au nœud d’enrichissement qui fournit la valeur. |

Pour projeter les mêmes données que l’exemple précédent, nous utilisons une option de projection incluse similaire à ceci :

```json
"projections": [
    {
        "tables": [
            {
                "tableName": "hotelReviewsInlineDocument",
                "generatedKeyName": "Documentid",
                "sourceContext": "/document",     
                "inputs": [
                    {
                        "name": "reviews_text",
                        "source": "/document/reviews_text"
                    },
                    {
                        "name": "reviews_title",
                        "source": "/document/reviews_title"
                    },
                    {
                        "name": "AzureSearch_DocumentKey",
                        "source": "/document/AzureSearch_DocumentKey"
                    }                             
                ]
            },
            {
                "tableName": "hotelReviewsInlinePages",
                "generatedKeyName": "Pagesid",
                "sourceContext": "/document/reviews_text/pages/*",
                "inputs": [
                        {
                    "name": "Sentiment",
                    "source": "/document/reviews_text/pages/*/Sentiment"
                    },
                    {
                        "name": "LanguageCode",
                        "source": "/document/Language"
                    },
                    {
                        "name": "Page",
                        "source": "/document/reviews_text/pages/*"
                    }
                ]
            },
            {
                "tableName": "hotelReviewsInlineKeyPhrases",
                "generatedKeyName": "KeyPhraseId",
                "sourceContext": "/document/reviews_text/pages/*/Keyphrases/*",
                "inputs": [
                    {
                        "name": "Keyphrases",
                        "source": "/document/reviews_text/pages/*/Keyphrases/*"
                    }
                ]
            }
        ]
    }
]
```
  
Dans les deux approches, nous pouvons observer comment les valeurs de « Keyphrases » sont projetées à l’aide de « sourceContext ». Le nœud « Keyphrases », qui contient une collection de chaînes, est lui-même un enfant du texte de la page. Toutefois, étant donné que les projections requièrent un objet JSON et que la page est de type primitif (chaîne), « sourceContext » est utilisé pour inclure l’expression clé dans un objet avec une propriété nommée. Cette technique permet aussi de projeter des primitives de manière indépendante.

<a name="inline-shape"></a>

## <a name="inline-shape-for-object-projections"></a>Forme incluse pour les projections d’objets

Vous pouvez générer une nouvelle forme à l’aide de la compétence Modélisateur ou utiliser la mise en forme inline de la projection d’objet. Tandis que l’exemple de tables illustre l’approche de la création d’une forme et du découpage, cet exemple illustre l’utilisation de la mise en forme inline. 

La mise en forme inline permet de créer une forme dans la définition des entrées d’une projection. La modélisation inline crée un objet anonyme identique à ce que produirait une compétence Modélisateur (dans le cas présent, `projectionShape`). La mise en forme inline est utile si vous définissez une forme que vous n’envisagez pas de réutiliser.

La propriété projections est un tableau. Cet exemple comprend une nouvelle instance de projection au tableau, où la définition knowledgeStore contient des projections inline. Quand vous utilisez des projections inline, vous pouvez omettre la compétence Modélisateur.

```json
"knowledgeStore" : {
    "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
    "projections": [
            {
            "tables": [ ],
            "objects": [
                {
                    "storageContainer": "sampleobject",
                    "source": null,
                    "generatedKeyName": "myobject",
                    "sourceContext": "/document",
                    "inputs": [
                        {
                            "name": "metadata_storage_name",
                            "source": "/document/metadata_storage_name"
                        },
                        {
                            "name": "metadata_storage_path",
                            "source": "/document/metadata_storage_path"
                        },
                        {
                            "name": "content",
                            "source": "/document/content"
                        },
                        {
                            "name": "keyPhrases",
                            "source": "/document/merged_content/keyphrases/*"
                        },
                        {
                            "name": "entities",
                            "source": "/document/merged_content/entities/*/name"
                        },
                        {
                            "name": "ocrText",
                            "source": "/document/normalized_images/*/text"
                        },
                        {
                            "name": "ocrLayoutText",
                            "source": "/document/normalized_images/*/layoutText"
                        }
                    ]

                }
            ],
            "files": []
        }
    ]
}
```

## <a name="next-steps"></a>Étapes suivantes

Cet article décrit les concepts et principes des formes de projection. À l’étape suivante, découvrez comment elles sont appliquées dans des modèles pour les projections de tables, d’objets et de fichiers.

> [!div class="nextstepaction"]
> [Définir des projections dans une base de connaissances](knowledge-store-projections-examples.md)
