---
title: Exemples de projection
titleSuffix: Azure Cognitive Search
description: Explorez un exemple détaillé qui projette la sortie d’un ensemble de compétences riche en formes complexes qui informent la structure et la composition du contenu dans une base de connaissances.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/20/2021
ms.openlocfilehash: a820101897ae20dbdeac029187d4ace356ae08af
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132491105"
---
# <a name="detailed-example-of-shapes-and-projections-in-a-knowledge-store"></a>Exemple détaillé de formes et de projections dans une base de connaissances

Cet article fournit un exemple détaillé qui complète les [concepts généraux](knowledge-store-projection-overview.md) et les [articles consacrés à la syntaxe](knowledge-store-projections-examples.md) en vous guidant à travers les étapes de mise en forme et de projection nécessaires pour exprimer entièrement la sortie d’un ensemble de compétences riche dans une [base de connaissances](knowledge-store-concept-intro.md).

Si les spécifications de votre application nécessitent plusieurs compétences et projections, cet exemple peut vous donner une meilleure idée de la façon dont les formes et les projections se recoupent.

## <a name="download-sample-definitions"></a>Télécharger les exemples de définitions

Cet exemple utilise l’[application de bureau Postman](https://www.postman.com/downloads/) et les [API REST de recherche](/rest/api/searchservice/).

Clonez ou téléchargez [azure-search-postman-samples](https://github.com/Azure-Samples/azure-search-postman-samples) sur GitHub et importez la [**Collection de projections**](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/projections) pour parcourir cet exemple vous-même.

## <a name="set-up-sample-data"></a>Configurer les exemples de données

Des exemples de documents ne sont pas spécifiquement inclus avec la collection Projections, mais les [fichiers de données de démonstration d’enrichissement par IA](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/ai-enrichment-mixed-media) du [référentiel azure-search-sample-data](https://github.com/Azure-Samples/azure-search-sample-data) contiennent du texte et des images, et fonctionneront avec les projections décrites dans cet exemple.

Créez un conteneur d’objets blob dans Stockage Azure et chargez les 14 éléments.

Dans Stockage Azure, copiez une chaîne de connexion afin de pouvoir la spécifier dans la collection Postman.

## <a name="example-skillset"></a>Exemple d’ensemble de compétences

Pour comprendre la dépendance entre les formes et les projections, passez en revue les ensembles de compétences suivants qui créent du contenu enrichi. Cet ensemble de compétences traite à la fois des images brutes et du texte, ce qui produit des sorties qui sont référencées dans des modélisations et des projections.

Portez une attention particulière aux sorties des compétences (targetNames). Les sorties écrites dans l’arborescence de documents enrichie sont référencées dans les projections et dans les formes (via les compétences Modélisateur).

```json
{
    "name": "projections-demo-ss",
    "description": "Skillset that enriches blob data found in "merged_content". The enrichment granularity is a document.",
    "skills": [
        {
            "@odata.type": "#Microsoft.Skills.Text.V3.EntityRecognitionSkill",
            "name": "#1",
            "description": null,
            "context": "/document/merged_content",
            "categories": [
                "Person",
                "Quantity",
                "Organization",
                "URL",
                "Email",
                "Location",
                "DateTime"
            ],
            "defaultLanguageCode": "en",
            "minimumPrecision": null,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/merged_content"
                },
                {
                    "name": "languageCode",
                    "source": "/document/language"
                }
            ],
            "outputs": [
                {
                    "name": "persons",
                    "targetName": "people"
                },
                {
                    "name": "organizations",
                    "targetName": "organizations"
                },
                {
                    "name": "locations",
                    "targetName": "locations"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
            "name": "#2",
            "description": null,
            "context": "/document/merged_content",
            "defaultLanguageCode": "en",
            "maxKeyPhraseCount": null,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/merged_content"
                },
                {
                    "name": "languageCode",
                    "source": "/document/language"
                }
            ],
            "outputs": [
                {
                    "name": "keyPhrases",
                    "targetName": "keyphrases"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
            "name": "#3",
            "description": null,
            "context": "/document",
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/merged_content"
                }
            ],
            "outputs": [
                {
                    "name": "languageCode",
                    "targetName": "language"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.MergeSkill",
            "name": "#4",
            "description": null,
            "context": "/document",
            "insertPreTag": " ",
            "insertPostTag": " ",
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/content"
                },
                {
                    "name": "itemsToInsert",
                    "source": "/document/normalized_images/*/text"
                },
                {
                    "name": "offsets",
                    "source": "/document/normalized_images/*/contentOffset"
                }
            ],
            "outputs": [
                {
                    "name": "mergedText",
                    "targetName": "merged_content"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Vision.OcrSkill",
            "name": "#5",
            "description": null,
            "context": "/document/normalized_images/*",
            "textExtractionAlgorithm": "printed",
            "lineEnding": "Space",
            "defaultLanguageCode": "en",
            "detectOrientation": true,
            "inputs": [
                {
                    "name": "image",
                    "source": "/document/normalized_images/*"
                }
            ],
            "outputs": [
                {
                    "name": "text",
                    "targetName": "text"
                },
                {
                    "name": "layoutText",
                    "targetName": "layoutText"
                }
            ]
        }
    ],
    "cognitiveServices": {
        "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
        "description": "A Cognitive Services resource in the same region as Search.",
        "key": "<COGNITIVE SERVICES All-in-ONE KEY>"
    },
    "knowledgeStore": null
}
```

## <a name="example-shaper-skill"></a>Exemple de compétence Modélisateur

Une [compétence Modélisateur](cognitive-search-skill-shaper.md) est un utilitaire qui permet de travailler avec du contenu enrichi au lieu de créer du nouveau contenu enrichi. L’ajout d’un Modélisateur à un ensemble de compétences vous permet de créer une forme personnalisée que vous pouvez projeter dans Stockage Table ou Stockage Blob. Sans modélisation personnalisée, les projections se limitent au référencement d’un seul nœud (une seule projection par sortie), ce qui ne convient pas pour les tables. La création d’une forme personnalisée permet de regrouper différents éléments en un nouvel ensemble logique qui peut être projeté sous la forme d’une table unique, ou découpé et distribué dans une collection de tables. 

Dans cet exemple, la modélisation personnalisée combine les métadonnées d’objet blob ainsi que les entités et phrases clés identifiées. La modélisation personnalisée s’appelle `projectionShape` et est apparenté à `/document`. 

L’une des finalités de la mise en forme est de s’assurer que tous les nœuds d’enrichissement sont exprimés dans un format JSON correct, ce qui est obligatoire pour la projection dans une base de connaissances. Cela est particulièrement vrai quand une arborescence d’enrichissement contient des nœuds qui n’ont pas le bon format JSON (par exemple, si un enrichissement est apparenté à une primitive comme une chaîne).

Notez que les deux derniers nœuds, `KeyPhrases` et `Entities`, sont wrappés dans un objet JSON valide avec le `sourceContext`. Cela est nécessaire, car `keyphrases` et `entities` sont des enrichissements sur les primitives et ils doivent être convertis en JSON valide avant de pouvoir être projetés.

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "name": "ShaperForTables",
    "description": null,
    "context": "/document",
    "inputs": [
        {
            "name": "metadata_storage_content_type",
            "source": "/document/metadata_storage_content_type",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "metadata_storage_name",
            "source": "/document/metadata_storage_name",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "metadata_storage_path",
            "source": "/document/metadata_storage_path",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "metadata_content_type",
            "source": "/document/metadata_content_type",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "keyPhrases",
            "source": null,
            "sourceContext": "/document/merged_content/keyphrases/*",
            "inputs": [
                {
                    "name": "KeyPhrases",
                    "source": "/document/merged_content/keyphrases/*"
                }

            ]
        },
        {
            "name": "Entities",
            "source": null,
            "sourceContext": "/document/merged_content/entities/*",
            "inputs": [
                {
                    "name": "Entities",
                    "source": "/document/merged_content/entities/*/name"
                }

            ]
        }
    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "projectionShape"
        }
    ]
}
```

### <a name="add-shapers-to-a-skillset"></a>Ajouter des Modélisateurs à un ensemble de compétences

L’exemple d’ensemble de compétences introduit au début de cet article n’incluait pas la compétence Modélisateur, mais les compétences Modélisateur appartiennent à un ensemble de compétences et sont souvent placées vers la fin.

Au sein d’un ensemble de compétences, une compétence Modélisateur peuvent ressembler à ceci :

```json
    "name": "projections-demo-ss",
    "skills": [
        {
            <Shaper skill goes here>
            }
        ],
    "cognitiveServices":  "A key goes here",
    "knowledgeStore": []
}  
```

## <a name="projecting-to-tables"></a>Projection dans des tables

À partir des exemples ci-dessus, nous pouvons voir qu’il existe une quantité connue d’enrichissements et de modélisations de données qui peuvent être référencés dans les projections de tables. Dans la projection de tables ci-dessous, trois tables sont définies à l’aide des propriétés `tableName`, `source` et `generatedKeyName`.

Ces trois tables sont liées via des clés générées et par le parent partagé `/document/projectionShape`.

```json
"knowledgeStore" : {
    "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
    "projections": [
        {
            "tables": [
                {
                    "tableName": "tblDocument",
                    "generatedKeyName": "Documentid",
                    "source": "/document/projectionShape"
                },
                {
                    "tableName": "tblKeyPhrases",
                    "generatedKeyName": "KeyPhraseid",
                    "source": "/document/projectionShape/keyPhrases/*"
                },
                {
                    "tableName": "tblEntities",
                    "generatedKeyName": "Entityid",
                    "source": "/document/projectionShape/Entities/*"
                }
            ],
            "objects": [],
            "files": []
        }
    ]
}
```

### <a name="test-your-work"></a>Tester votre travail

Vous pouvez vérifier les définitions de la projection en effectuant ces étapes :

1. Affectez à la propriété `storageConnectionString` de la base de connaissances une chaîne de connexion de compte de stockage universel V2 valide.  

1. Mettez à jour l’ensemble de compétences en envoyant la requête PUT.

1. Après la mise à jour de l’ensemble de compétences, exécutez l’indexeur. 

Vous disposez maintenant d’une projection de travail avec trois tables. [Quand vous importez ces tables dans Power BI](knowledge-store-connect-power-bi.md), ce dernier doit pouvoir détecter les relations automatiquement.

Avant de passer à l’exemple suivant, revoyons les aspects de la projection de table pour comprendre les mécanismes de découpage et des données associées.

### <a name="slicing-a-table-into-multiple-child-tables"></a>Découpage d’une table en plusieurs tables enfants

Le découpage est une technique qui divise une forme regroupée entière en plusieurs parties constitutives. Vous obtenez des tables distinctes mais liées entre elles, que vous pouvez utiliser individuellement.

Dans l’exemple, `projectionShape` est la forme regroupée (ou nœud d’enrichissement). Dans la définition de la projection, `projectionShape` est découpé en tables supplémentaires, ce qui vous permet d’extraire des parties de la forme, `keyPhrases` et `Entities`. Dans Power BI, cette opération est utile lorsque plusieurs entités et expressions clés sont associées à chaque document : vous obtenez des insights plus complets si vous pouvez visualiser les entités et les expressions clés sous forme de données classifiées par catégorie.

Le découpage génère implicitement une relation entre les tables parent et enfant, en utilisant le `generatedKeyName` dans la table parent pour créer une colonne du même nom dans la table enfant. 

### <a name="naming-relationships"></a>Nommage des relations

Les propriétés `generatedKeyName` et `referenceKeyName` sont utilisées pour lier des données entre des tables ou même entre des types de projection. Chaque ligne de la table enfant a une propriété qui pointe vers le parent. Le nom de la colonne ou de la propriété dans l’enfant est le `referenceKeyName` du parent. Quand le `referenceKeyName` n’est pas fourni, le service le définit par défaut sur le `generatedKeyName` du parent. 

Power BI utilise ces clés générées pour découvrir les relations dans les tables. S’il est nécessaire que la colonne de la table enfant soit nommée différemment, définissez la propriété `referenceKeyName` sur la table . Par exemple, vous pouvez définir `generatedKeyName` en tant qu’ID pour la table tblDocument et `referenceKeyName` en tant que DocumentID. Ainsi, la colonne des tables tblEntities et tblKeyPhrases contenant l’ID de document se nomme DocumentID.

## <a name="projecting-blob-documents"></a>Projection de documents blob

Les projections d’objet sont des représentations JSON de l’arborescence d’enrichissement pouvant provenir de n’importe quel nœud. En comparaison avec les projections de table, les projections d’objets sont plus simples à définir et elles sont utilisées lors de la projection de documents entiers. Les projections d’objet sont limitées à une seule projection dans un conteneur et elles ne peuvent pas être découpées.

Pour définir une projection d’objet, utilisez le tableau `objects` dans la propriété projections.

La source est le chemin vers un nœud de l’arborescence d’enrichissement qui est la racine de la projection. Bien que cela ne soit pas obligatoire, le chemin du nœud correspond généralement à la sortie d’une compétence Modélisateur. Ceci est dû au fait que la plupart des compétences ne produisent pas par elles-mêmes des objets JSON valides, ce qui signifie que de la mise en forme est nécessaire. Dans de nombreux cas, la même compétence de modélisateur que celle qui crée une projection de table peut être utilisée pour générer une projection d’objet. Sinon, la source peut également être définie sur un nœud avec [une mise en forme inline](knowledge-store-projection-shape.md#inline-shape) pour fournir la structure.

La destination est toujours un conteneur d’objets blob.

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

## <a name="projecting-an-image-file"></a>Projection d’un fichier image

Les projections de fichiers sont toujours des images normalisées binaires, où la normalisation fait référence au redimensionnement et à la rotation potentiels pour leur utilisation dans l’exécution des ensembles de compétences. Les projections de fichier, de façon similaire aux projections d’objet, sont créées sous forme d’objets blob dans Stockage Azure et contiennent l’image.

Pour définir une projection de fichier, utilisez le tableau `files` dans la propriété projections.

La source est toujours `/document/normalized_images/*`. Les projections de fichier agissent seulement sur la collection `normalized_images`. Ni les indexeurs, ni un ensemble de compétences ne vont traverser l’image non normalisée d’origine.

La destination est toujours un conteneur d’objets blob, avec un préfixe de dossier correspondant à la valeur encodée en base64 de l’ID de document. Les projections de fichiers ne peuvent pas partager le même conteneur que les projections d’objets. Elles doivent être projetées vers un conteneur distinct. 

L’exemple suivant projette toutes les images normalisées extraites du nœud de document d’un document enrichi dans un conteneur appelé `myImages`.

```json
"knowledgeStore" : {
    "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
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
}
```

## <a name="projecting-to-multiple-types"></a>Projection dans plusieurs types

Un scénario plus complexe peut vous obliger à projeter du contenu dans différents types de projection. Par exemple, projeter les phrases clés et les entités vers des tables, enregistrer les résultats de l’OCR du texte et du texte de disposition en tant qu’objets, puis projeter les images en tant que fichiers. 

Étapes pour plusieurs types de projection :

1. Créer une table avec une ligne pour chaque document.
1. Créer une table liée à la table de documents avec chaque expression clé identifiée en tant que ligne dans cette table.
1. Créer une table liée à la table de documents avec chaque entité identifiée en tant que ligne dans cette table.
1. Créer une projection d’objet avec le texte de disposition pour chaque image.
1. Créez une projection de fichier, en projetant chaque image extraite.
1. Créer une table de référence croisée qui contient des références à la table de document, à la projection d’objet avec le texte de disposition et à la projection de fichier.

### <a name="shape-data-for-cross-projection"></a>Mettre en forme les données pour la projection croisée

Pour obtenir les formes nécessaires pour ces projections, commencez par ajouter une nouvelle compétence Modélisateur qui crée un objet mis en forme appelé `crossProjection`. 

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "name": "ShaperForCrossProjection",
    "description": null,
    "context": "/document",
    "inputs": [
        {
            "name": "metadata_storage_name",
            "source": "/document/metadata_storage_name",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "keyPhrases",
            "source": null,
            "sourceContext": "/document/merged_content/keyphrases/*",
            "inputs": [
                {
                    "name": "KeyPhrases",
                    "source": "/document/merged_content/keyphrases/*"
                }

            ]
        },
        {
            "name": "entities",
            "source": null,
            "sourceContext": "/document/merged_content/entities/*",
            "inputs": [
                {
                    "name": "Entities",
                    "source": "/document/merged_content/entities/*/name"
                }

            ]
        },
        {
            "name": "images",
            "source": null,
            "sourceContext": "/document/normalized_images/*",
            "inputs": [
                {
                    "name": "image",
                    "source": "/document/normalized_images/*"
                },
                {
                    "name": "layoutText",
                    "source": "/document/normalized_images/*/layoutText"
                },
                {
                    "name": "ocrText",
                    "source": "/document/normalized_images/*/text"
                }
                ]
        }
 
    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "crossProjection"
        }
    ]
}
```

### <a name="define-table-object-and-file-projections"></a>Définir des projections de table, d’objet et de fichier

À partir de l’objet crossProjection regroupé, découpez l’objet en plusieurs tables, capturez la sortie OCR sous forme d’objets blob, puis enregistrez l’image en tant que fichiers (également dans le Stockage Blob).

```json
"knowledgeStore" : {
    "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
    "projections": [
            {
            "tables": [
                {
                    "tableName": "crossDocument",
                    "generatedKeyName": "Id",
                    "source": "/document/crossProjection"
                },
                {
                    "tableName": "crossEntities",
                    "generatedKeyName": "EntityId",
                    "source": "/document/crossProjection/entities/*"
                },
                {
                    "tableName": "crossKeyPhrases",
                    "generatedKeyName": "KeyPhraseId",
                    "source": "/document/crossProjection/keyPhrases/*"
                },
                {
                    "tableName": "crossReference",
                    "generatedKeyName": "CrossId",
                    "source": "/document/crossProjection/images/*"
                }
                    
            ],
            "objects": [
                {
                    "storageContainer": "crossobject",
                    "generatedKeyName": "crosslayout",
                    "source": null,
                    "sourceContext": "/document/crossProjection/images/*/layoutText",
                    "inputs": [
                        {
                            "name": "OcrLayoutText",
                            "source": "/document/crossProjection/images/*/layoutText"
                        }
                    ]
                }
            ],
            "files": [
                {
                    "storageContainer": "crossimages",
                    "generatedKeyName": "crossimages",
                    "source": "/document/crossProjection/images/*/image"
                }
            ]
        }
    ]
}
```

Les projections d’objets nécessitent un nom de conteneur pour chaque projection. Les projections d’objets et les projections de fichiers ne peuvent pas partager de conteneur. 

### <a name="relationships-among-table-object-and-file-projections"></a>Relations entre les projections de table, d’objet et de fichier

Cet exemple met également en lumière une autre fonctionnalité des projections. En définissant plusieurs types de projections dans le même objet de projection, il existe une relation exprimée dans et entre les différents types (tables, objets, fichiers). Cela vous permet de commencer avec une ligne de tableau pour un document et de rechercher tout le texte OCR pour les images contenues dans ce document dans la projection de l’objet. 

Si vous ne souhaitez pas que les données soient liées, définissez les projections dans des groupes de projections distincts. Par exemple, l’extrait de code suivant entraîne la liaison des tables, sans qu’aucune relation ne soit toutefois établie entre les tables et les projections d’objet (texte OCR). 

Les groupes de projection sont utiles quand vous souhaitez projeter les mêmes données dans différentes formes en fonction de besoins différents. Par exemple, un groupe de projections pour le tableau de bord Power BI et un autre groupe de projections pour la capture des données utilisées pour entraîner un modèle Machine Learning wrappé dans une compétence personnalisée.

Lors de la création de projections de types différents, les projections de fichier et d’objet sont générées en premier, et les chemins sont ajoutés aux tables.

```json
"knowledgeStore" : {
    "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
    "projections": [
        {
            "tables": [
                {
                    "tableName": "unrelatedDocument",
                    "generatedKeyName": "Documentid",
                    "source": "/document/projectionShape"
                },
                {
                    "tableName": "unrelatedKeyPhrases",
                    "generatedKeyName": "KeyPhraseid",
                    "source": "/document/projectionShape/keyPhrases"
                }
            ],
            "objects": [
                
            ],
            "files": []
        }, 
        {
            "tables": [],
            "objects": [
                {
                    "storageContainer": "unrelatedocrtext",
                    "source": null,
                    "sourceContext": "/document/normalized_images/*/text",
                    "inputs": [
                        {
                            "name": "ocrText",
                            "source": "/document/normalized_images/*/text"
                        }
                    ]
                },
                {
                    "storageContainer": "unrelatedocrlayout",
                    "source": null,
                    "sourceContext": "/document/normalized_images/*/layoutText",
                    "inputs": [
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

L’exemple de cet article montre des modèles courants pour créer des projections. Maintenant que vous avez bien compris les concepts, vous êtes mieux armé pour créer des projections adaptées à votre scénario particulier.

> [!div class="nextstepaction"]
> [Configurer la mise en cache pour l’enrichissement incrémentiel](search-howto-incremental-index.md)
