---
title: Activité d’exécution du pipeline
titleSuffix: Azure Data Factory & Azure Synapse
description: Découvrez comment vous pouvez utiliser l’activité Execute Pipeline pour appeler un pipeline à partir d’un autre pipeline dans Azure Data Factory ou Synapse Analytics.
author: chez-charlie
ms.author: chez
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: orchestration
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/09/2021
ms.openlocfilehash: 047548a39c16c5f6b6ee3f7d359a8664c87e7062
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128566593"
---
# <a name="execute-pipeline-activity-in-azure-data-factory-and-synapse-analytics"></a>Activité Execute Pipeline dans Azure Data Factory et Synapse Analytics

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

L’activité Execute Pipeline permet à un pipeline Data Factory ou Synapse d’appeler un autre pipeline.

## <a name="syntax"></a>Syntaxe

```json
{
    "name": "MyPipeline",
    "properties": {
        "activities": [
            {
                "name": "ExecutePipelineActivity",
                "type": "ExecutePipeline",
                "typeProperties": {
                    "parameters": {                        
                        "mySourceDatasetFolderPath": {
                            "value": "@pipeline().parameters.mySourceDatasetFolderPath",
                            "type": "Expression"
                        }
                    },
                    "pipeline": {
                        "referenceName": "<InvokedPipelineName>",
                        "type": "PipelineReference"
                    },
                    "waitOnCompletion": true
                 }
            }
        ],
        "parameters": [
            {
                "mySourceDatasetFolderPath": {
                    "type": "String"
                }
            }
        ]
    }
}
```

## <a name="type-properties"></a>Propriétés type

Propriété | Description | Valeurs autorisées | Obligatoire
-------- | ----------- | -------------- | --------
name | Nom de l’activité d’exécution du pipeline. | String | Oui
type | Doit avoir la valeur : **ExecutePipeline**. | String | Oui
pipeline | Référence de pipeline au pipeline dépendant que pipeline appelle. Un objet de référence de pipeline comporte deux propriétés : **referenceName** et **type**. La propriété referenceName spécifie le nom du pipeline de référence. La propriété de type doit être définie sur PipelineReference. | PipelineReference | Oui
parameters | Paramètres à passer au pipeline appelé | Objet JSON qui mappe des noms de paramètres à des valeurs d’arguments | Non
waitOnCompletion | Définit si l’exécution de l’activité attend l’exécution du pipeline dépendant. La valeur par défaut est true. | Boolean | Non

## <a name="sample"></a>Exemple
Ce scénario comporte deux pipelines :

- **Pipeline master** : ce pipeline comporte une activité d’exécution du pipeline qui appelle le pipeline. Le pipeline master accepte deux paramètres : `masterSourceBlobContainer`, `masterSinkBlobContainer`.
- **Pipeline appelé** : ce pipeline comporte une activité de copie qui copie les données d’une source d’objets blob Azure dans le récepteur d’objets blob Azure. Le pipeline appelé accepte deux paramètres : `sourceBlobContainer`, `sinkBlobContainer`.

### <a name="master-pipeline-definition"></a>Définition du pipeline master

```json
{
  "name": "masterPipeline",
  "properties": {
    "activities": [
      {
        "type": "ExecutePipeline",
        "typeProperties": {
          "pipeline": {
            "referenceName": "invokedPipeline",
            "type": "PipelineReference"
          },
          "parameters": {
            "sourceBlobContainer": {
              "value": "@pipeline().parameters.masterSourceBlobContainer",
              "type": "Expression"
            },
            "sinkBlobContainer": {
              "value": "@pipeline().parameters.masterSinkBlobContainer",
              "type": "Expression"
            }
          },
          "waitOnCompletion": true
        },
        "name": "MyExecutePipelineActivity"
      }
    ],
    "parameters": {
      "masterSourceBlobContainer": {
        "type": "String"
      },
      "masterSinkBlobContainer": {
        "type": "String"
      }
    }
  }
}

```

### <a name="invoked-pipeline-definition"></a>Définition du pipeline appelé

```json
{
  "name": "invokedPipeline",
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "BlobSink"
          }
        },
        "name": "CopyBlobtoBlob",
        "inputs": [
          {
            "referenceName": "SourceBlobDataset",
            "type": "DatasetReference"
          }
        ],
        "outputs": [
          {
            "referenceName": "sinkBlobDataset",
            "type": "DatasetReference"
          }
        ]
      }
    ],
    "parameters": {
      "sourceBlobContainer": {
        "type": "String"
      },
      "sinkBlobContainer": {
        "type": "String"
      }
    }
  }
}

```

**Service lié**

```json
{
    "name": "BlobStorageLinkedService",
    "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=*****;AccountKey=*****"
    }
  }
}
```

**Jeu de données source**
```json
{
    "name": "SourceBlobDataset",
    "properties": {
    "type": "AzureBlob",
    "typeProperties": {
      "folderPath": {
        "value": "@pipeline().parameters.sourceBlobContainer",
        "type": "Expression"
      },
      "fileName": "salesforce.txt"
    },
    "linkedServiceName": {
      "referenceName": "BlobStorageLinkedService",
      "type": "LinkedServiceReference"
    }
  }
}
```

**Jeu de données récepteur**
```json
{
    "name": "sinkBlobDataset",
    "properties": {
    "type": "AzureBlob",
    "typeProperties": {
      "folderPath": {
        "value": "@pipeline().parameters.sinkBlobContainer",
        "type": "Expression"
      }
    },
    "linkedServiceName": {
      "referenceName": "BlobStorageLinkedService",
      "type": "LinkedServiceReference"
    }
  }
}
```

### <a name="running-the-pipeline"></a>Exécution du pipeline

Pour exécuter le pipeline master dans cet exemple, les valeurs suivantes sont transmises aux paramètres masterSourceBlobContainer et masterSinkBlobContainer : 

```json
{
  "masterSourceBlobContainer": "executetest",
  "masterSinkBlobContainer": "executesink"
}
```

Le pipeline master transfère ces valeurs au pipeline appelé comme indiqué dans l’exemple suivant : 

```json
{
    "type": "ExecutePipeline",
    "typeProperties": {
      "pipeline": {
        "referenceName": "invokedPipeline",
        "type": "PipelineReference"
      },
      "parameters": {
        "sourceBlobContainer": {
          "value": "@pipeline().parameters.masterSourceBlobContainer",
          "type": "Expression"
        },
        "sinkBlobContainer": {
          "value": "@pipeline().parameters.masterSinkBlobContainer",
          "type": "Expression"
        }
      },

      ....
}

```
## <a name="next-steps"></a>Étapes suivantes
Consultez d’autres activités de flux de contrôle prises en charge : 

- [Pour chaque activité](control-flow-for-each-activity.md)
- [Activité d’obtention des métadonnées](control-flow-get-metadata-activity.md)
- [Activité de recherche](control-flow-lookup-activity.md)
- [Activité Web](control-flow-web-activity.md)
