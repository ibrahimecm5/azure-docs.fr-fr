---
title: Guide pratique pour envoyer des tâches de classification personnalisée
titleSuffix: Azure Cognitive Services
description: Apprenez-en davantage sur l’envoi d’une demande de classification de texte personnalisée.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-classification, ignite-fall-2021
ms.openlocfilehash: 9ba640d5d0177c812ad25a76353bd89f2e696d81
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132054711"
---
# <a name="deploy-a-model-and-classify-text-using-the-runtime-api"></a>Déployer un modèle et classifier du texte à l’aide de l’API de runtime

Une fois que vous êtes satisfait de votre modèle et que vous avez apporté les améliorations nécessaires, vous pouvez le déployer et commencer à classifier le texte. Le déploiement d’un modèle le rend disponible pour une utilisation par le biais de l’API de runtime.

## <a name="prerequisites"></a>Prérequis

* [Un projet de classification personnalisée](create-project.md) avec un compte Stockage Blob Azure configuré 
* Des données texte qui [ont été chargées](create-project.md#prepare-training-data) sur votre compte de stockage
* Des [données étiquetées](tag-data.md) et un [modèle correctement entraîné](train-model.md)
* Avoir passé en revue les [détails de l’évaluation du modèle](view-model-evaluation.md) pour déterminer les performances de votre modèle
* (facultatif) [Avoir apporté des améliorations](improve-model.md) à votre modèle si ses performances ne sont pas satisfaisantes. 

Pour plus d’informations, consultez le [cycle de vie du développement d’applications](../overview.md#project-development-lifecycle).

## <a name="deploy-your-model"></a>Déployer votre modèle

1. Accédez à votre projet dans [Langage Studio](https://aka.ms/custom-classification)

2. Dans le menu de gauche, sélectionnez **Deploy model**.

3. Sélectionnez le modèle à déployer, puis **Déployer le modèle**.

> [!TIP]
> Vous pouvez tester votre modèle dans Language Studio en envoyant des exemples de texte à classer. 
> 1. Sélectionnez **Tester le modèle** dans le menu de gauche de votre projet dans Language Studio.
> 2. Sélectionnez le modèle à tester.
> 3. Ajoutez votre texte à la zone de texte, vous pouvez également charger un fichier `.txt`. 
> 4. Cliquez sur **Run the test**.
> 5. Sous l’onglet **Result**, vous pouvez voir les classes prédites pour votre texte. Vous pouvez également voir la réponse JSON sous l’onglet **JSON**.

## <a name="send-a-text-classification-request-to-your-model"></a>Envoyer une demande de classification de texte à votre modèle

# <a name="using-language-studio"></a>[Utilisation de Language Studio](#tab/language-studio)

### <a name="using-language-studio"></a>Utilisation de Language Studio

1. Une fois le déploiement effectué, sélectionnez le modèle à utiliser et, dans le menu supérieur, cliquez sur **Obtenir l’URL de prédiction**, et copiez l’URL et le corps.

    :::image type="content" source="../media/get-prediction-url-1.png" alt-text="run-inference" lightbox="../media/get-prediction-url-1.png":::

2. Dans la fenêtre qui s’affiche, sous le pivot **Envoyer**, copiez l’exemple de demande dans la ligne de commande

3. Remplacez `<YOUR_DOCUMENT_HERE>` par le texte à classer.

    :::image type="content" source="../media/get-prediction-url-2.png" alt-text="run-inference-2" lightbox="../media/get-prediction-url-2.png":::

4. Envoyez la demande.

5. Dans l’en-tête de réponse que vous recevez, extrayez `jobId` à partir de `operation-location`, au format : `{YOUR-ENDPOINT}/text/analytics/v3.2-preview.2/analyze/jobs/<jobId}>`

6. Copiez la requête de récupération et remplacez `<OPERATION-ID>` par le `jobId` obtenu à l’étape précédente, puis envoyez la requête.

    :::image type="content" source="../media/get-prediction-url-3.png" alt-text="run-inference-3" lightbox="../media/get-prediction-url-3.png":::

 Plus d’informations sur les résultats sont disponibles dans la section suivante.

# <a name="using-the-api"></a>[Utilisation de l’API](#tab/api)

### <a name="using-the-api"></a>Utilisation de l’API

Tout d’abord, vous devez disposer de votre point de terminaison et de votre clé de ressource

1. Accédez à la page de vue d’ensemble de votre ressource dans le [portail Azure](https://ms.portal.azure.com/#home)

2. Dans le menu de gauche, sélectionnez **Clés et point de terminaison**. Utilisez le point de terminaison pour les demandes d’API et la clé pour l’en-tête `Ocp-Apim-Subscription-Key`.

    :::image type="content" source="../media/get-endpoint-azure.png" alt-text="Obtenir le point de terminaison Azure" lightbox="../media/get-endpoint-azure.png":::

### <a name="submit-text-classification-task"></a>Envoyer la tâche de classification de texte

> [!NOTE]
> Les noms de projet respectent la casse.

Utilisez cette requête **POST** pour démarrer une tâche d’extraction d’entité.

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
        "customMultiClassificationTasks": [      
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
|--|customMultiClassificationTasks|Identificateur de la tâche à effectuer. Utilisez `customClassificationTasks` pour les tâches de classification avec une seule étiquette et `customMultiClassificationTasks` pour les tâches de classification avec plusieurs étiquettes. |
|parameters|[]|Liste de paramètres à passer à la tâche|
|nom-projet| « MyProject »| Nom de votre projet. Le nom du projet respecte la casse.|
|deployment-name| « MyDeploymentName »| Nom de votre déploiement|

#### <a name="response"></a>response

Vous recevez une réponse 202 indiquant la réussite. Dans les **en-têtes** de réponse, extrayez `operation-location`.
`operation-location` est au format suivant :

 `{YOUR-ENDPOINT}/text/analytics/v3.2-preview.2/analyze/jobs/<jobId>`

Vous utiliserez ce point de terminaison à l’étape suivante pour obtenir les résultats de la tâche de classification personnalisée.

### <a name="get-the-classification-task-status-and-results"></a>Obtenir l’état et les résultats de la tâche de classification

Utilisez la requête **GET** suivante pour interroger l’état/les résultats de la tâche de classification personnalisée. Vous pouvez utiliser le point de terminaison que vous avez reçu à l’étape précédente.

`{YOUR-ENDPOINT}/text/analytics/v3.2-preview.2/analyze/jobs/<jobId>`.

#### <a name="headers"></a>headers

|Clé|Valeur|
|--|--|
|Ocp-Apim-Subscription-Key| Clé d’abonnement qui fournit l’accès à cette API.|

Plus d’informations sur les résultats sont disponibles dans la section suivante.

---


#### <a name="text-classification-task-results"></a>Résultats de la tâche de classification de texte

La réponse renvoyée par l’appel de résultat de GET est un document JSON avec les paramètres suivants :

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
    "customMultiClassificationTasks": [
        {
            "lastUpdateDateTime": "2021-05-19T14:32:25.579Z",
            "name": "MyJobName",
            "status": "completed",
            "results": {
                "documents": [
                    {
                        "id": "doc1",
                        "classes": [
                            {
                                "category": "Class_1",
                                "confidenceScore": 0.0551877357
                            }
                        ],
                        "warnings": []
                    },
                    {
                        "id": "doc2",
                        "classes": [
                            {
                                "category": "Class_1",
                                "confidenceScore": 0.0551877357
                            },
                                                        {
                                "category": "Class_2",
                                "confidenceScore": 0.0551877357
                            }
                        ],
                        "warnings": []
                    }
                ],
                "errors": [],
                "statistics": {
                    "documentsCount":0,
                    "erroneousDocumentsCount":0,
                    "transactionsCount":0
                }
                    }
                }
            ]
        }
    }
```
