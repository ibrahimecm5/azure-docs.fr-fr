---
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: include
ms.date: 11/02/2021
ms.author: aahi
ms.custom: ignite-fall-2021
ms.openlocfilehash: ed3824491e043ea99637604e76d83612d1ad6764
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131521478"
---
## <a name="prerequisites"></a>Prérequis

* Abonnement Azure - [En créer un gratuitement](https://azure.microsoft.com/free/cognitive-services)

## <a name="create-a-new-azure-resource-and-azure-blob-storage-account"></a>Créer une ressource Azure et un compte Stockage Blob Azure

Avant de pouvoir utiliser un modèle de NER personnalisée, vous devez créer une ressource Azure Language, qui vous permettra de disposer des informations d’identification nécessaires pour créer un projet et commencer à entraîner un modèle. Vous aurez également besoin d’un compte Stockage Azure, où vous pourrez charger le jeu de données à utiliser pour générer votre modèle.

> [!IMPORTANT]
> Pour bien démarrer rapidement, nous vous recommandons de créer une ressource Azure Language en suivant les étapes ci-dessous. Ainsi, vous pourrez créer la ressource et configurer en même temps un compte de stockage, ce qui est plus facile à faire maintenant que plus tard.
>
> Si vous disposez d’une ressource préexistante à utiliser, vous devez la configurer séparément du compte de stockage. Pour plus d’informations, consultez [l’article sur la création d’un projet](../../how-to/create-project.md#using-a-pre-existing-azure-resource).

1. Accédez au [portail Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) pour créer une ressource Azure Language. Si vous êtes invité à sélectionner des fonctionnalités supplémentaires, sélectionnez **Classification de texte personnalisée et NER personnalisée**. Quand vous créez la ressource, vérifiez qu’elle comporte les paramètres suivants.

    |Paramètres nécessaires à la ressource Azure  |Valeur requise  |
    |---------|---------|
    |Location | « USA Ouest 2 » ou « Europe Ouest »         |
    |Niveau tarifaire     | Niveau tarifaire standard (**S**)        |

2. Dans la section **Reconnaissance d’entité nommée (NER) personnalisée et Classification personnalisée (préversion)** , sélectionnez un compte de stockage existant, ou sélectionnez **Créer un compte de stockage**. Notez que ces valeurs concernent ce guide de démarrage rapide, et pas nécessairement les [valeurs du compte de stockage](/azure/storage/common/storage-account-overview) à utiliser dans les environnements de production.

    |Valeur du compte de stockage  |Valeur recommandée  |
    |---------|---------|
    | Nom | Nom quelconque |
    | Performances | Standard |
    | Type de compte| Stockage (v1 universel) |
    | Réplication | Stockage localement redondant (LRS)
    |Location | N’importe quel lieu le plus proche de vous, pour une meilleure latence.        |


## <a name="upload-sample-data-to-blob-container"></a>Charger les exemples de données dans un conteneur d’objets blob

Une fois que vous avez créé un compte Stockage Azure et que vous l’avez lié à votre ressource Language, vous devez charger les exemples de fichiers de ce guide de démarrage rapide. Ces fichiers seront utilisés plus tard pour entraîner votre modèle.

1. [Téléchargez les exemples de données](https://go.microsoft.com/fwlink/?linkid=2175226) pour ce guide de démarrage rapide à partir de GitHub.

2. Accédez à votre compte Stockage Azure dans le [portail Azure](https://ms.portal.azure.com). Accédez à votre compte, puis chargez les exemples de données vers celui-ci.

L’exemple de jeu de données fourni contient 20 contrats de prêt. Chaque contrat comprend deux parties : un prêteur et un emprunteur. Vous pouvez utiliser l’exemple de fichier d’étiquettes fourni pour extraire les informations pertinentes relatives aux deux parties, la date du contrat, le montant du prêt et le taux d’intérêt.

### <a name="get-your-resource-keys-endpoint"></a>Obtenir vos clés de ressource et votre point de terminaison

* Accédez à la page de vue d’ensemble de votre ressource dans le [portail Azure](https://ms.portal.azure.com/#home)

* Dans le menu à gauche de l’écran, sélectionnez **Clés et point de terminaison**. Utilisez le point de terminaison pour les demandes d’API et la clé pour l’en-tête `Ocp-Apim-Subscription-Key`.
:::image type="content" source="../../../media/azure-portal-resource-credentials.png" alt-text="Capture d’écran montrant la clé et le point de terminaison d’une ressource Azure." lightbox="../../../media/azure-portal-resource-credentials.png":::

## <a name="create-a-custom-ner-project"></a>Créer un projet NER personnalisé

Une fois votre ressource et votre conteneur de stockage configurés, créez un projet de NER personnalisée. Un projet est une zone de travail dans laquelle vous créez vos modèles IA personnalisés à partir de vos données. Votre projet est uniquement accessible à vous-même et aux autres personnes disposant d’un accès de contributeur à la ressource Azure utilisée.

> [!NOTE]
> Le nom du projet respecte la casse pour toutes les opérations.

Créez une demande **POST** en utilisant l’URL, les en-têtes et le corps JSON suivants pour créer votre projet et importer le fichier d’étiquettes.

Utilisez l’URL suivante pour créer un projet et importer votre fichier d’étiquettes. Remplacez les valeurs d’espace réservé suivantes par vos valeurs : 

```rest
{YOUR-ENDPOINT}/language/analyze-text/projects/{projectName}/:import. 
```

|Espace réservé  |Valeur  | Exemple |
|---------|---------|---------|
|`{YOUR-ENDPOINT}`     | Point de terminaison pour l’authentification de votre demande d’API.   | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |

### <a name="parameters"></a>Paramètres

Passez le paramètre suivant avec votre demande. 

|Clé|Explication|Valeur|
|--|--|--|
|`api-version`| Version d’API utilisée.| `2021-11-01-preview` |

Pour passer le paramètre, ajoutez `?api-version=2021-11-01-preview` à la fin de l’URL de votre demande.

### <a name="headers"></a>headers

Utilisez l’en-tête suivant pour authentifier votre demande. 

|Clé|Valeur|
|--|--|
|`Ocp-Apim-Subscription-Key`| Clé de votre ressource. Utilisée pour authentifiée vos demandes d’API.|

### <a name="body"></a>body

Utilisez le code JSON suivant dans votre demande. Remplacez les valeurs d’espace réservé suivantes par vos valeurs : Utilisez le fichier d’étiquettes disponible sous l’onglet des [exemples de données](https://github.com/Azure-Samples/cognitive-services-sample-data-files)

```json
{
    "api-version": "2021-11-01-preview",
    "metadata": {
        "name": "MyProject",
        "multiLingual": true,
        "description": "Tryong out custom text classification",
        "modelType": "Extraction",
        "language": "string",
        "storageInputContainerName": "YOUR-CONTAINER-NAME",
        "settings": {}
    },
    "assets": {
        "extractors": [
        {
            "name": "Entity1"
        },
        {
            "name": "Entity2"
        }
    ],
    "documents": [
        {
            "location": "doc1.txt",
            "language": "en-us",
            "extractors": [
                {
                    "regionOffset": 0,
                    "regionLength": 500,
                    "labels": [
                        {
                            "extractorName": "Entity1",
                            "offset": 25,
                            "length": 10
                        },                    
                        {
                            "extractorName": "Entity2",
                            "offset": 120,
                            "length": 8
                        }
                    ]
                }
            ]
        },
        {
            "location": "doc2.txt",
            "language": "en-us",
            "extractors": [
                {
                    "regionOffset": 0,
                    "regionLength": 100,
                    "labels": [
                        {
                            "extractorName": "Entity2",
                            "offset": 20,
                            "length": 5
                        }
                    ]
                }
            ]
        }
    ]
    }
}
```
Pour la clé de métadonnées : 

|Clé  |Valeur  | Exemple |
|---------|---------|---------|
| `modelType  `    | Type de votre modèle. | Extraction |
|`storageInputContainerName`   | Nom de votre conteneur de stockage Blob Azure.   | `myContainer` |

Cette demande renvoie une erreur si :

* La ressource sélectionnée n’a pas les autorisations appropriées pour le compte de stockage. 

## <a name="start-training-your-model"></a>Commencer l’entraînement de votre modèle

Une fois votre projet créé, vous pouvez commencer l’entraînement d’un modèle NER personnalisé. Créez une demande **POST** en utilisant l’URL, les en-têtes et le corps JSON suivants pour commencer l’entraînement d’un modèle NER.
Créez une demande **POST** en utilisant l’URL, les en-têtes et le corps JSON suivants pour commencer l’entraînement d’un modèle de classification de texte.

### <a name="request-url"></a>URL de la demande

Utilisez l’URL suivante quand vous créez votre demande d’API. Remplacez les valeurs d’espace réservé suivantes par vos valeurs : 

```rest
{YOUR-ENDPOINT}/language/analyze-text/projects/{PROJECT-NAME}/:train
```

|Espace réservé  |Valeur  | Exemple |
|---------|---------|---------|
|`{YOUR-ENDPOINT}`     | Point de terminaison pour l’authentification de votre demande d’API.   | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |
|`{PROJECT-NAME}`     | Nom de votre projet. Cette valeur respecte la casse.  | `myProject` |

### <a name="parameters"></a>Paramètres

Passez le paramètre suivant avec votre demande.

|Clé|Explication|Valeur|
|--|--|--|
|`api-version`| Version d’API utilisée.| `2021-11-01-preview` |

Pour passer le paramètre, ajoutez `?api-version=2021-11-01-preview` à la fin de l’URL de votre demande.

### <a name="headers"></a>headers

Utilisez l’en-tête suivant pour authentifier votre demande. 

|Clé|Valeur|
|--|--|
|`Ocp-Apim-Subscription-Key`| Clé de votre ressource. Utilisée pour authentifiée vos demandes d’API.|

### <a name="request-body"></a>Corps de la demande

Utilisez le code JSON suivant dans votre demande. Le modèle est nommé `MyModel` une fois l’entraînement effectué.  

```json
{
  "modelLabel": "MyModel",
  "runValidation": true
}
```

|Clé  |Valeur  | Exemple |
|---------|---------|---------|
|`modelLabel  `    | Nom de votre modèle.   | MyModel |
|`runValidation`     | Valeur booléenne pour exécuter la validation sur le jeu de test.   | Vrai |

Une fois que vous avez envoyé votre demande d’API, vous recevez une réponse `202` indiquant la réussite. Dans les en-têtes de réponse, extrayez la valeur `location`. Elle est au format suivant : 

```rest
{YOUR-ENDPOINT}/language/analyze-text/projects/{YOUR-PROJECT-NAME}/train/jobs/{JOB-ID}?api-version=xxxx-xx-xx-xxxxxxx
``` 

`JOB-ID` est utilisé pour identifier votre demande, car cette opération est asynchrone. Vous utilisez cette URL à l’étape suivante pour obtenir l’état de l’entraînement. 

## <a name="get-training-status"></a>Obtenir l’état de l’entraînement

Utilisez la demande **GET** suivante pour interroger l’état du processus d’entraînement de votre modèle. Vous pouvez utiliser l’URL que vous avez reçue à l’étape précédente ou remplacer les valeurs d’espace réservé ci-dessous par vos propres valeurs. 

```rest
{YOUR-ENDPOINT}/language/analyze-text/projects/{YOUR-PROJECT-NAME}/train/jobs/{JOB-ID}
```

|Espace réservé  |Valeur  | Exemple |
|---------|---------|---------|
|`{YOUR-ENDPOINT}`     | Point de terminaison pour l’authentification de votre demande d’API.   | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |
|`{PROJECT-NAME}`     | Nom de votre projet. Cette valeur respecte la casse.  | `myProject` |
|`{JOB-ID}`     | ID de localisation de l’état d’entraînement de votre modèle. Il s’agit de la valeur d’en-tête `location` que vous avez reçue à l’étape précédente.  | `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx` |

### <a name="parameters"></a>Paramètres

Passez le paramètre suivant avec votre demande. 

|Clé|Explication|Valeur|
|--|--|--|
|`api-version`| Version d’API utilisée.| `2021-11-01-preview` |

Pour passer le paramètre, ajoutez `?api-version=2021-11-01-preview` à la fin de l’URL de votre demande.

### <a name="headers"></a>headers

Utilisez l’en-tête suivant pour authentifier votre demande. 

|Clé|Valeur|
|--|--|
|`Ocp-Apim-Subscription-Key`| Clé de votre ressource. Utilisée pour authentifiée vos demandes d’API.|

### <a name="response-body"></a>Corps de la réponse

Une fois que vous avez envoyé la demande, vous obtenez la réponse suivante. 

```json
{
  "jobs": [
    {
      "result": {
        "trainedModelLabel": "MyModel",
        "trainStatus": {
          "percentComplete": 0,
          "elapsedTime": "string"
        },
        "evaluationStatus": {
          "percentComplete": 0,
          "elapsedTime": "string"
        }
      },
      "jobId": "string",
      "createdDateTime": "2021-10-19T23:24:41.572Z",
      "lastUpdatedDateTime": "2021-10-19T23:24:41.572Z",
      "expirationDateTime": "2021-10-19T23:24:41.572Z",
      "status": "unknown",
      "errors": [
        {
          "code": "unknown",
          "message": "string"
        }
      ]
    }
  ],
  "nextLink": "string"
}
```
## <a name="deploy-your-model"></a>Déployer votre modèle

Créez une demande **PUT** en utilisant l’URL, les en-têtes et le corps JSON suivants pour commencer le déploiement d’un modèle NER personnalisé.

```rest
{YOUR-ENDPOINT}/language/analyze-text/projects/{PROJECT-NAME}/deployments/{DEPLOYMENT-NAME}
```

|Espace réservé  |Valeur  | Exemple |
|---------|---------|---------|
|`{YOUR-ENDPOINT}`     | Point de terminaison pour l’authentification de votre demande d’API.   | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |
|`{PROJECT-NAME}`     | Nom de votre projet. Cette valeur respecte la casse.  | `myProject` |
|`{DEPLOYMENT-NAME}`     | Nom de votre déploiement. Cette valeur respecte la casse.  | `prod` |

### <a name="parameters"></a>Paramètres

Passez le paramètre suivant avec votre demande. 

|Clé|Explication|Valeur|
|--|--|--|
|`api-version`| Version d’API utilisée.| `2021-11-01-preview` |

Pour passer le paramètre, ajoutez `?api-version=2021-11-01-preview` à la fin de l’URL de votre demande.

### <a name="headers"></a>headers

Utilisez l’en-tête suivant pour authentifier votre demande. 

|Clé|Valeur|
|--|--|
|`Ocp-Apim-Subscription-Key`| Clé de votre ressource. Utilisée pour authentifiée vos demandes d’API.|

### <a name="request-body"></a>Corps de la demande

Utilisez le code JSON suivant dans votre demande. Le modèle est nommé `MyModel` une fois l’entraînement effectué.  

```json
{
{
  "trainedModelLabel": "MyModel"
}
```

Une fois que vous avez envoyé votre demande d’API, vous recevez une réponse `202` indiquant la réussite. Dans les en-têtes de réponse, extrayez la valeur `location`. Elle est au format suivant : 

```rest
{YOUR-ENDPOINT}/language/analyze-text/projects/{YOUR-PROJECT-NAME}/deployments/{DEPLOYMENT-NAME}/jobs/{JOB-ID}?api-version=xxxx-xx-xx-xxxxxxx
``` 

`JOB-ID` est utilisé pour identifier votre demande, car cette opération est asynchrone. Vous utilisez cette URL à l’étape suivante pour obtenir l’état de publication.

## <a name="get-the-deployment-status"></a>Obtenir l’état du déploiement

Utilisez la demande **GET** suivante pour interroger l’état du processus de publication de votre modèle. Vous pouvez utiliser l’URL que vous avez reçue à l’étape précédente ou remplacer les valeurs d’espace réservé ci-dessous par vos propres valeurs. 

```rest
{YOUR-ENDPOINT}/language/analyze-text/projects/{YOUR-PROJECT-NAME}/deployments/{DEPLOYMENT-NAME}/jobs/{JOB-ID}
```

|Espace réservé  |Valeur  | Exemple |
|---------|---------|---------|
|`{YOUR-ENDPOINT}`     | Point de terminaison pour l’authentification de votre demande d’API.   | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |
|`{PROJECT-NAME}`     | Nom de votre projet. Cette valeur respecte la casse.  | `myProject` |
|`{DEPLOYMENT-NAME}`     | Nom de votre déploiement. Cette valeur respecte la casse.  | `prod` |
|`{JOB-ID}`     | ID de localisation de l’état d’entraînement de votre modèle. Il s’agit de la valeur d’en-tête `location` que vous avez reçue à l’étape précédente.  | `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx` |

### <a name="parameters"></a>Paramètres

Passez le paramètre suivant avec votre demande. 

|Clé|Explication|Valeur|
|--|--|--|
|`api-version`| Version d’API utilisée.| `2021-11-01-preview` |

Pour passer le paramètre, ajoutez `?api-version=2021-11-01-preview` à la fin de l’URL de votre demande.

### <a name="headers"></a>headers

Utilisez l’en-tête suivant pour authentifier votre demande. 

|Clé|Valeur|
|--|--|
|`Ocp-Apim-Subscription-Key`| Clé de votre ressource. Utilisée pour authentifiée vos demandes d’API.|


### <a name="submit-custom-ner-task"></a>Envoyer une tâche de NER personnalisée

Maintenant que votre modèle est déployé, vous pouvez commencer à lui envoyer des tâches de reconnaissance d’entité. 

> [!NOTE]
> Les noms de projet respectent la casse.

Utilisez cette demande **POST** pour démarrer une tâche d’extraction d’entité. Remplacez `{projectName}` par le nom du projet où se trouve le modèle à utiliser.

`{YOUR-ENDPOINT}/text/analytics/v3.2-preview.2/analyze`

#### <a name="headers"></a>headers

|Clé|Valeur|
|--|--|
|Ocp-Apim-Subscription-Key| Clé d’abonnement qui fournit l’accès à cette API.|

#### <a name="body"></a>body

```json
    {
    "displayName": "MyJobName",
    "analysisInput": {
        "documents": [
            {
                "id": "doc1", 
                "text": "Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nunc tempus, felis sed vehicula lobortis, lectus ligula facilisis quam, quis aliquet lectus diam id erat. Vivamus eu semper tellus. Integer placerat sem vel eros iaculis dictum. Sed vel congue urna."
            },
            {
                "id": "doc2",
                "text": "Mauris dui dui, ultricies vel ligula ultricies, elementum viverra odio. Donec tempor odio nunc, quis fermentum lorem egestas commodo. Class aptent taciti sociosqu ad litora torquent per conubia nostra, per inceptos himenaeos."
            }
        ]
    },
    "tasks": {
        "customEntityRecognitionTasks": [      
            {
                "parameters": {
                      "project-name": "MyProject",
                      "deployment-name": "MyDeploymentName"
                      "stringIndexType": "TextElements_v8"
                }
            }
        ]
    }
}
```

|Clé|Exemple de valeur|Description|
|--|--|--|
|displayName|« MyJobName »|Nom de votre travail|
|dans des documents|[{},{}]|Liste des documents sur lesquels exécuter des tâches|
|id|« doc1 »|identificateur de document sous forme de chaîne|
|text|« Lorem ipsum dolor sit amet »| Votre document sous forme de chaîne|
|« tasks »|[]| Liste des tâches à effectuer.|
|--|customEntityRecognitionTasks|Identificateur de la tâche à effectuer. |
|parameters|[]|Liste de paramètres à passer à la tâche|
|nom-projet| « MyProject »| Nom de votre projet. Le nom du projet respecte la casse.|
|deployment-name| « MyDeploymentName »| Nom de votre déploiement|

#### <a name="response"></a>response

Vous recevez une réponse 202 indiquant la réussite. Dans les **en-têtes** de réponse, extrayez `operation-location`.
`operation-location` est au format suivant :

 `{YOUR-ENDPOINT}/text/analytics/v3.2-preview.2/analyze/jobs/<jobId>`

Vous utilisez ce point de terminaison à l’étape suivante pour obtenir les résultats de la tâche de reconnaissance personnalisée.

### <a name="get-task-status-and-results"></a>Obtenir l’état et les résultats de la tâche

Utilisez la demande **GET** suivante pour interroger l’état/les résultats de la tâche de reconnaissance personnalisée. Vous pouvez utiliser le point de terminaison que vous avez reçu à l’étape précédente.

`{YOUR-ENDPOINT}/text/analytics/v3.2-preview.2/analyze/jobs/<jobId>`.

#### <a name="headers"></a>headers

|Clé|Valeur|
|--|--|
|Ocp-Apim-Subscription-Key| Clé d’abonnement qui fournit l’accès à cette API.|

### <a name="response-body"></a>Corps de la réponse

La réponse est un document JSON avec les paramètres suivants.

```json
{
    "createdDateTime": "2021-05-19T14:32:25.578Z",
    "displayName": "MyJobName",
    "expirationDateTime": "2021-05-19T14:32:25.578Z",
    "jobId": "3fa85f64-5717-4562-b3fc-2c963f66afa6",
    "lastUpdateDateTime": "2021-05-19T14:32:25.578Z",
    "status": "completed",
    "errors": [],
    "tasks": {
        "details": {
            "name": "MyJobName",
            "lastUpdateDateTime": "2021-03-29T19:50:23Z",
            "status": "completed"
        },
        "completed": 1,
        "failed": 0,
        "inProgress": 0,
        "total": 1,
        "tasks": {
    "customEntityRecognitionTasks": [
        {
            "lastUpdateDateTime": "2021-05-19T14:32:25.579Z",
            "name": "MyJobName",
            "status": "completed",
            "results": {
                "documents": [
                    {
                        "id": "doc1",
                        "entities": [
                            {
                                "text": "Government",
                                "category": "restaurant_name",
                                "offset": 23,
                                "length": 10,
                                "confidenceScore": 0.0551877357
                            }
                        ],
                        "warnings": []
                    },
                    {
                        "id": "doc2",
                        "entities": [
                            {
                                "text": "David Schmidt",
                                "category": "artist",
                                "offset": 0,
                                "length": 13,
                                "confidenceScore": 0.8022353
                            }
                        ],
                        "warnings": []
                    }
                ],
                "errors": [],
                "statistics": {
                    "documentsCount":0,
                    "validDocumentsCount":0,
                    "erroneousDocumentsCount":0,
                    "transactionsCount":0
                }
                    }
                }
            ]
        }
    }
```

## <a name="clean-up-resources"></a>Nettoyer les ressources

Quand vous n’avez plus besoin de votre projet, vous pouvez le supprimer avec la demande **DELETE** suivante. Remplacez les valeurs d’espace réservé par vos propres valeurs.   

```rest
{YOUR-ENDPOINT}/language/text/authoring/v1.0-preview.2/projects/{PROJECT-NAME}
```

|Espace réservé  |Valeur  | Exemple |
|---------|---------|---------|
|`{YOUR-ENDPOINT}`     | Point de terminaison pour l’authentification de votre demande d’API.   | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |
|`{PROJECT-NAME}`     | Nom de votre projet. Cette valeur respecte la casse.  | `myProject` |

### <a name="headers"></a>headers

Utilisez l’en-tête suivant pour authentifier votre demande. 

|Clé|Valeur|
|--|--|
|Ocp-Apim-Subscription-Key| Clé de votre ressource. Utilisée pour authentifiée vos demandes d’API.|
