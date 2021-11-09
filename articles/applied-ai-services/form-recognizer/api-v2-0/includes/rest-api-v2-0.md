---
title: 'Informations de référence : API REST Form Recognizer 2.0'
description: Utilisez l’API REST Form Recognizer afin de créer une application de traitement des formulaires qui extrait des paires clé/valeur et des données de tableau de vos documents personnalisés.
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 05/25/2021
ms.author: lajanuar
ms.custom: ignite-fall-2021
ms.openlocfilehash: 645ef6947afb52e1a76d859d72649b89e5db0614
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131253247"
---
<!-- markdownlint-disable MD001 -->
<!-- markdownlint-disable MD024 -->
<!-- markdownlint-disable MD033 -->
<!-- markdownlint-disable MD034 -->
> [!NOTE]
> Ce guide utilise cURL pour exécuter des appels d’API REST.

|[API REST Form Recognizer](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm)|[Informations de référence de l’API REST Azure](/rest/api/azure/)|

## <a name="prerequisites"></a>Prérequis

* [cURL](https://curl.haxx.se/windows/) installé.
* [PowerShell version 6.0 +](/powershell/scripting/install/installing-powershell-core-on-windows) ou une application de ligne de commande similaire.
* Abonnement Azure - [En créer un gratuitement](https://azure.microsoft.com/free/cognitive-services/)
* Un blob Stockage Azure qui contient un jeu de données d’apprentissage. Consultez [Créer un jeu de données d’entraînement pour un modèle personnalisé](../../build-training-data-set.md) pour obtenir des conseils et des options pour constituer votre jeu de données d’entraînement. Dans le cadre de ce guide de démarrage rapide, vous pouvez utiliser les fichiers disponibles dans le dossier **Train** de l’[exemple de jeu de données](https://go.microsoft.com/fwlink/?linkid=2090451) (téléchargez et extrayez *sample_data.zip*).
* Une fois que vous avez votre abonnement Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title="créez une ressource Form Recognizer"  target="_blank">créer une ressource Form Recognizer</a> sur le portail Azure pour obtenir votre clé et votre point de terminaison. À la fin du déploiement, sélectionnez **Accéder à la ressource**.
  * Vous aurez besoin de la clé et du point de terminaison de la ressource que vous créez pour connecter votre application à l’API Form Recognizer. Vous collerez votre clé et votre point de terminaison dans le code ci-dessous plus loin dans le guide de démarrage rapide.
  * Vous pouvez utiliser le niveau tarifaire Gratuit (`F0`) pour tester le service, puis passer par la suite à un niveau payant pour la production.
* Une URL pour une image de ticket de caisse. Pour ce guide de démarrage rapide, vous pouvez utiliser un [exemple d’image](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/contoso-allinone.jpg).
* URL pour une image de carte de visite. Pour ce guide de démarrage rapide, vous pouvez utiliser un [exemple d’image](https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/business_cards/business-card-english.jpg).
* URL pour une image de facture. Vous pouvez utiliser un [exemple de document](https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/forms/Invoice_1.pdf) pour ce guide de démarrage rapide.

## <a name="code-examples"></a>Exemples de code

Ces extraits de code vous montrent comment effectuer les tâches suivantes avec l’API REST Form Recognizer :
<!-- markdownlint-disable MD001 -->

* [Analyser la disposition](#analyze-layout)
* [Analyser les reçus](#analyze-receipts)
* [Entraîner un modèle personnalisé](#train-a-custom-model)
* [Analyser les formulaires avec un modèle personnalisé](#analyze-forms-with-a-custom-model)
* [Gérer des modèles personnalisés](#manage-custom-models)



## <a name="analyze-layout"></a>Analyser la disposition

Vous pouvez utiliser Form Recognizer pour analyser et extraire les tableaux, les marques de sélection, le texte et la structure dans les documents, sans avoir besoin d’entraîner un modèle. Pour plus d’informations sur l’extraction d’une disposition, consultez le [guide conceptuel des dispositions](../../concept-layout.md). Avant d’exécuter la commande, apportez les modifications suivantes :

1. Remplacez `{Endpoint}` par le point de terminaison que vous avez obtenu avec votre abonnement Form Recognizer.
1. Remplacez `{subscription key}` par la clé d’abonnement que vous avez copiée à l’étape précédente.
1. Remplacez `\"{your-document-url}` par l’un des exemples d’URL.

```bash
curl -v -i POST "https://{Endpoint}/formrecognizer/v2.0/layout/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{'source': '{your-document-url}'}"
```

Vous recevrez une réponse `202 (Success)` incluant un en-tête **Operation-Location**. La valeur de cet en-tête contient un ID d’opération que vous pouvez utiliser pour interroger l’état de l’opération asynchrone et obtenir les résultats. Dans l’exemple suivant, la chaîne après `analyzeResults/` est l’ID d’opération.

```console
https://cognitiveservice/formrecognizer/v2/layout/analyzeResults/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

### <a name="get-layout-results"></a>Obtenir les résultats de la disposition

Après avoir appelé l’API **[https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeLayoutAsync)** , vous appelez l’API **[https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/GetAnalyzeLayoutResult)** pour obtenir l’état de l’opération et les données extraites. Avant d’exécuter la commande, apportez les modifications suivantes :

1. Remplacez `{Endpoint}` par le point de terminaison que vous avez obtenu avec votre abonnement Form Recognizer.
1. Remplacez `{subscription key}` par la clé d’abonnement que vous avez copiée à l’étape précédente.
1. Remplacez `{resultId}` par l’ID d’opération obtenu à l’étape précédente.
<!-- markdownlint-disable MD024 -->

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.0/layout/analyzeResults/{resultId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="examine-the-results"></a>Examiner les résultats

Vous recevez une réponse `200 (success)` avec du contenu JSON.

Regardez l’image de facture suivante et sa sortie JSON correspondante.

* Le nœud `"readResults"` contient chaque ligne de texte avec sa position de cadre englobant respectif dans la page.
* La section `"pageResults"` comprend les tableaux extraits. Pour chaque tableau, le texte, l’index de ligne et de colonne, l’étendue de ligne et de colonne, le cadre englobant, etc. sont extraits.

:::image type="content" source="../../media/contoso-invoice.png" alt-text="Document d’énoncé de projet Contoso avec un tableau.":::

Cette sortie a été raccourcie à des fins de simplicité. Consultez l’[exemple de sortie complet sur GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/sample-layout-output.json).

```json
{
    "status": "succeeded",
    "createdDateTime": "2020-08-20T20:36:52Z",
    "lastUpdatedDateTime": "2020-08-20T20:36:58Z",
    "analyzeResult": {
        "version": "2.0.0",
        "readResults": [
            {
                "page": 1,
                "language": "en",
                "angle": 0,
                "width": 8.5,
                "height": 11,
                "unit": "inch",
                "lines": [
                    {
                        "boundingBox": [
                            0.5826,
                            0.4411,
                            2.3387,
                            0.4411,
                            2.3387,
                            0.7969,
                            0.5826,
                            0.7969
                        ],
                        "text": "Contoso, Ltd.",
                        "words": [
                            {
                                "boundingBox": [
                                    0.5826,
                                    0.4411,
                                    1.744,
                                    0.4411,
                                    1.744,
                                    0.7969,
                                    0.5826,
                                    0.7969
                                ],
                                "text": "Contoso,",
                                "confidence": 1
                            },
                            {
                                "boundingBox": [
                                    1.8448,
                                    0.4446,
                                    2.3387,
                                    0.4446,
                                    2.3387,
                                    0.7631,
                                    1.8448,
                                    0.7631
                                ],
                                "text": "Ltd.",
                                "confidence": 1
                            }
                        ]
                    },
                    ...
                ]
            }
        ],
        "pageResults": [
            {
                "page": 1,
                "tables": [
                    {
                        "rows": 5,
                        "columns": 5,
                        "cells": [
                            {
                                "rowIndex": 0,
                                "columnIndex": 0,
                                "text": "Training Date",
                                "boundingBox": [
                                    0.5133,
                                    4.2167,
                                    1.7567,
                                    4.2167,
                                    1.7567,
                                    4.4492,
                                    0.5133,
                                    4.4492
                                ],
                                "elements": [
                                    "#/readResults/0/lines/14/words/0",
                                    "#/readResults/0/lines/14/words/1"
                                ]
                            },
                            ...
                        ]
                    },
                    ...
                ]
            }
        ]
    }
}
```

## <a name="analyze-receipts"></a>Analyser les reçus

Cette section montre comment analyser et extraire les champs communs de tickets de caisse émis aux États-Unis à l’aide d’un modèle de ticket préentraîné. Pour plus d’informations sur l’analyse des tickets de caisse, consultez le [guide conceptuel des tickets de caisse](../../concept-receipt.md). Pour commencer à analyser un reçu, appelez l’API **[Analyze Receipt](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeReceiptAsync)** (Analyser le reçu) en utilisant la commande cURL ci-dessous. Avant d’exécuter la commande, apportez les modifications suivantes :

1. Remplacez `{Endpoint}` par le point de terminaison que vous avez obtenu avec votre abonnement Form Recognizer.
1. Remplacez `{your receipt URL}` par l’adresse URL d’une image de ticket de caisse.
1. Remplacez `{subscription key>` par la clé d’abonnement que vous avez copiée à l’étape précédente.

```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.0/prebuilt/receipt/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{your receipt URL}'}"
```

Vous recevrez une réponse `202 (Success)` incluant un en-tête **Operation-Location**. La valeur de cet en-tête contient un ID d’opération que vous pouvez utiliser pour interroger l’état de l’opération asynchrone et obtenir les résultats. Dans l’exemple suivant, la chaîne après `operations/` est l’ID d’opération.

```console
https://cognitiveservice/formrecognizer/v2.0/prebuilt/receipt/operations/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

### <a name="get-the-receipt-results"></a>Obtenir les résultats du ticket de caisse

Après avoir appelé l’API **Analyze Receipt**, vous appelez l’API **[Get Analyze Receipt Result](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/GetAnalyzeReceiptResult)** pour obtenir l’état de l’opération et les données extraites. Avant d’exécuter la commande, apportez les modifications suivantes :

1. Remplacez `{Endpoint}` par le point de terminaison que vous avez obtenu avec votre clé d’abonnement Form Recognizer. Vous la trouverez sous l’onglet **Vue d’ensemble** de la ressource Form Recognizer.
1. Remplacez `{operationId}` par l’ID d’opération obtenu à l’étape précédente.
1. Remplacez `{subscription key}` par votre clé d’abonnement.

```bash
curl -X GET "https://{Endpoint}/formrecognizer/v2.0/prebuilt/receipt/analyzeResults/{operationId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="examine-the-response"></a>Examiner la réponse

Vous recevez une réponse `200 (Success)` avec une sortie JSON. Le premier champ, `"status"`, indique l’état de l’opération. Si l’opération n’est pas terminée, la valeur de `"status"` sera `"running"` ou `"notStarted"`, et vous devez rappeler l’API, manuellement ou via un script. Nous vous recommandons d’attendre une seconde ou plus entre chaque appel.

Le nœud `"readResults"` contient tout le texte reconnu (si vous définissez le paramètre facultatif *includeTextDetails* sur `true`). Le texte est organisé par page, puis par ligne, puis par mots individuels. Le nœud `"documentResults"` contient les valeurs spécifiques du reçu découvertes par le modèle. C’est là que vous trouverez des paires clé/valeur utiles, telles que la taxe, le total et l’adresse du commerçant.

Regardez l’image de ticket suivante et sa sortie JSON correspondante.

![Ticket de caisse du magasin Contoso](../../media/contoso-allinone.jpg)

Cette sortie a été raccourcie pour une meilleure lisibilité. Consultez l’[exemple de sortie complet sur GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/receipt-result.json).

```json
{
  "status":"succeeded",
  "createdDateTime":"2019-12-17T04:11:24Z",
  "lastUpdatedDateTime":"2019-12-17T04:11:32Z",
  "analyzeResult":{
    "version":"2.1.0",
    "readResults":[
      {
        "page":1,
        "angle":0.6893,
        "width":1688,
        "height":3000,
        "unit":"pixel",
        "language":"en",
        "lines":[
          {
            "text":"Contoso",
            "boundingBox":[
              635,
              510,
              1086,
              461,
              1098,
              558,
              643,
              604
            ],
            "words":[
              {
                "text":"Contoso",
                "boundingBox":[
                  639,
                  510,
                  1087,
                  461,
                  1098,
                  551,
                  646,
                  604
                ],
                "confidence":0.955
              }
            ]
          },
          ...
        ]
      }
    ],
    "documentResults":[
      {
        "docType":"prebuilt:receipt",
        "pageRange":[
          1,
          1
        ],
        "fields":{
          "ReceiptType":{
            "type":"string",
            "valueString":"Itemized",
            "confidence":0.692
          },
          "MerchantName":{
            "type":"string",
            "valueString":"Contoso Contoso",
            "text":"Contoso Contoso",
            "boundingBox":[
              378.2,
              292.4,
              1117.7,
              468.3,
              1035.7,
              812.7,
              296.3,
              636.8
            ],
            "page":1,
            "confidence":0.613,
            "elements":[
              "#/readResults/0/lines/0/words/0",
              "#/readResults/0/lines/1/words/0"
            ]
          },
          "MerchantAddress":{
            "type":"string",
            "valueString":"123 Main Street Redmond, WA 98052",
            "text":"123 Main Street Redmond, WA 98052",
            "boundingBox":[
              302,
              675.8,
              848.1,
              793.7,
              809.9,
              970.4,
              263.9,
              852.5
            ],
            "page":1,
            "confidence":0.99,
            "elements":[
              "#/readResults/0/lines/2/words/0",
              "#/readResults/0/lines/2/words/1",
              "#/readResults/0/lines/2/words/2",
              "#/readResults/0/lines/3/words/0",
              "#/readResults/0/lines/3/words/1",
              "#/readResults/0/lines/3/words/2"
            ]
          },
          "MerchantPhoneNumber":{
            "type":"phoneNumber",
            "valuePhoneNumber":"+19876543210",
            "text":"987-654-3210",
            "boundingBox":[
              278,
              1004,
              656.3,
              1054.7,
              646.8,
              1125.3,
              268.5,
              1074.7
            ],
            "page":1,
            "confidence":0.99,
            "elements":[
              "#/readResults/0/lines/4/words/0"
            ]
          },
          "TransactionDate":{
            "type":"date",
            "valueDate":"2019-06-10",
            "text":"6/10/2019",
            "boundingBox":[
              265.1,
              1228.4,
              525,
              1247,
              518.9,
              1332.1,
              259,
              1313.5
            ],
            "page":1,
            "confidence":0.99,
            "elements":[
              "#/readResults/0/lines/5/words/0"
            ]
          },
          "TransactionTime":{
            "type":"time",
            "valueTime":"13:59:00",
            "text":"13:59",
            "boundingBox":[
              541,
              1248,
              677.3,
              1261.5,
              668.9,
              1346.5,
              532.6,
              1333
            ],
            "page":1,
            "confidence":0.977,
            "elements":[
              "#/readResults/0/lines/5/words/1"
            ]
          },
          "Items":{
            "type":"array",
            "valueArray":[
              {
                "type":"object",
                "valueObject":{
                  "Quantity":{
                    "type":"number",
                    "text":"1",
                    "boundingBox":[
                      245.1,
                      1581.5,
                      300.9,
                      1585.1,
                      295,
                      1676,
                      239.2,
                      1672.4
                    ],
                    "page":1,
                    "confidence":0.92,
                    "elements":[
                      "#/readResults/0/lines/7/words/0"
                    ]
                  },
                  "Name":{
                    "type":"string",
                    "valueString":"Cappuccino",
                    "text":"Cappuccino",
                    "boundingBox":[
                      322,
                      1586,
                      654.2,
                      1601.1,
                      650,
                      1693,
                      317.8,
                      1678
                    ],
                    "page":1,
                    "confidence":0.923,
                    "elements":[
                      "#/readResults/0/lines/7/words/1"
                    ]
                  },
                  "TotalPrice":{
                    "type":"number",
                    "valueNumber":2.2,
                    "text":"$2.20",
                    "boundingBox":[
                      1107.7,
                      1584,
                      1263,
                      1574,
                      1268.3,
                      1656,
                      1113,
                      1666
                    ],
                    "page":1,
                    "confidence":0.918,
                    "elements":[
                      "#/readResults/0/lines/8/words/0"
                    ]
                  }
                }
              },
              ...
            ]
          },
          "Subtotal":{
            "type":"number",
            "valueNumber":11.7,
            "text":"11.70",
            "boundingBox":[
              1146,
              2221,
              1297.3,
              2223,
              1296,
              2319,
              1144.7,
              2317
            ],
            "page":1,
            "confidence":0.955,
            "elements":[
              "#/readResults/0/lines/13/words/1"
            ]
          },
          "Tax":{
            "type":"number",
            "valueNumber":1.17,
            "text":"1.17",
            "boundingBox":[
              1190,
              2359,
              1304,
              2359,
              1304,
              2456,
              1190,
              2456
            ],
            "page":1,
            "confidence":0.979,
            "elements":[
              "#/readResults/0/lines/15/words/1"
            ]
          },
          "Tip":{
            "type":"number",
            "valueNumber":1.63,
            "text":"1.63",
            "boundingBox":[
              1094,
              2479,
              1267.7,
              2485,
              1264,
              2591,
              1090.3,
              2585
            ],
            "page":1,
            "confidence":0.941,
            "elements":[
              "#/readResults/0/lines/17/words/1"
            ]
          },
          "Total":{
            "type":"number",
            "valueNumber":14.5,
            "text":"$14.50",
            "boundingBox":[
              1034.2,
              2617,
              1387.5,
              2638.2,
              1380,
              2763,
              1026.7,
              2741.8
            ],
            "page":1,
            "confidence":0.985,
            "elements":[
              "#/readResults/0/lines/19/words/0"
            ]
          }
        }
      }
    ]
  }
}
```

## <a name="train-a-custom-model"></a>Entraîner un modèle personnalisé

Pour entraîner un modèle personnalisé, vous avez besoin d’un jeu de données d’entraînement dans un objet blob Stockage Azure. Vous avez besoin d’au minimum cinq formulaires remplis (documents PDF et/ou images) d’un type ou d’une structure identique. Consultez [Créer un jeu de données d’apprentissage pour un modèle personnalisé](../../build-training-data-set.md) pour obtenir des conseils et des options pour constituer vos données d’apprentissage.

L’entraînement sans les données étiquetées est l’opération par défaut et est plus simple. Vous pouvez également étiqueter manuellement une partie ou la totalité de vos données d’entraînement. Il s’agit d’un processus plus complexe, mais le modèle entraîné qui en résulte est de meilleure qualité.

> [!NOTE]
> Vous pouvez aussi entraîner des modèles à l’aide d’une interface graphique utilisateur telle que l’[outil d’étiquetage des exemples Form Recognizer](../../label-tool.md).

### <a name="train-a-model-without-labels"></a>Entraîner un modèle sans étiquettes

Pour entraîner un modèle Form Recognizer avec les documents présents dans votre conteneur d’objets blob Azure, appelez l’API **[Entraîner un modèle personnalisé]https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/TrainCustomModelAsync)** en exécutant la commande cURL suivante. Avant d’exécuter la commande, apportez les modifications suivantes :

1. Remplacez `{Endpoint}` par le point de terminaison que vous avez obtenu avec votre abonnement Form Recognizer.
1. Remplacez `{subscription key}` par la clé d’abonnement que vous avez copiée à l’étape précédente.
1. Remplacez `{SAS URL}` par l’URL de signature d’accès partagé (SAS) du conteur de stockage Azure Blob. 

[!INCLUDE [get SAS URL](sas-instruction.md)]

   :::image type="content" source="../../media/quickstarts/get-sas-url.png" alt-text="Récupération d’URL SAS":::

```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.0/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{SAS URL}'}"
```

Vous recevrez une réponse `201 (Success)` avec un en-tête **Location**. La valeur de cet en-tête est l’ID du nouveau modèle en cours d’entraînement.

### <a name="train-a-model-with-labels"></a>Entraîner un modèle avec des étiquettes

Pour effectuer un entraînement avec des étiquettes, vous devez disposer de fichiers d’informations spéciaux sur les étiquettes (`\<filename\>.pdf.labels.json`) dans votre conteneur de stockage d’objets blob, en même temps que les documents d’entraînement. L’[outil d’étiquetage des exemples Form Recognizer](../../label-tool.md) propose une interface utilisateur qui facilite la création de ces fichiers d’étiquettes. Une fois que vous les avez, vous pouvez appeler l’API **[Entraîner un modèle personnalisé](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/TrainCustomModelAsync)** , avec le paramètre `"useLabelFile"` défini sur `true` dans le corps JSON.

Avant d’exécuter la commande, apportez les modifications suivantes :

1. Remplacez `{Endpoint}` par le point de terminaison que vous avez obtenu avec votre abonnement Form Recognizer.
1. Remplacez `{subscription key}` par la clé d’abonnement que vous avez copiée à l’étape précédente.
1. Remplacez `{SAS URL}` par l’URL de signature d’accès partagé (SAS) du conteur de stockage Azure Blob. [!INCLUDE [get SAS URL](sas-instruction.md)]

   :::image type="content" source="../../media/quickstarts/get-sas-url.png" alt-text="Récupération d’URL SAS":::

```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.0/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{SAS URL}', 'useLabelFile':true }"
```

Vous recevrez une réponse `201 (Success)` avec un en-tête **Location**. La valeur de cet en-tête est l’ID du nouveau modèle en cours d’entraînement.

### <a name="get-training-results"></a>Obtenir les résultats de l’entraînement

Une fois que vous avez démarré l’opération d’entraînement, vous utilisez une nouvelle opération, **[Obtenir un modèle personnalisé](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/GetCustomModel)** , pour vérifier l’état de l’entraînement. Transmettez l’ID de modèle dans cet appel d’API pour vérifier l’état de l’entraînement :

1. Remplacez `{Endpoint}` par le point de terminaison que vous avez obtenu avec votre clé d’abonnement Form Recognizer.
1. Remplacez `{subscription key}` par votre clé d’abonnement.
1. Remplacez `{model ID}` par l’ID de modèle que vous avez reçu à l’étape précédente.

```bash
curl -X GET "https://{Endpoint}/formrecognizer/v2.0/custom/models/{model ID}" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

Vous recevrez une réponse `200 (Success)` avec un corps JSON sous la forme suivante. Notez le champ `"status"`. Il aura pour valeur `"ready"` une fois l’entraînement terminé. Si l’entraînement du modèle n’est pas terminé, vous devez réinterroger le service en réexécutant la commande. Nous vous recommandons d’attendre une seconde ou plus entre chaque appel.

Le champ `"modelId"` contient l’ID du modèle que vous êtes en train d’entraîner. Vous en aurez besoin à l’étape suivante.

```json
{
  "modelInfo":{
    "status":"ready",
    "createdDateTime":"2019-10-08T10:20:31.957784",
    "lastUpdatedDateTime":"2019-10-08T14:20:41+00:00",
    "modelId":"1cfb372bab404ba3aa59481ab2c63da5"
  },
  "trainResult":{
    "trainingDocuments":[
      {
        "documentName":"invoices\\Invoice_1.pdf",
        "pages":1,
        "errors":[

        ],
        "status":"succeeded"
      },
      {
        "documentName":"invoices\\Invoice_2.pdf",
        "pages":1,
        "errors":[

        ],
        "status":"succeeded"
      },
      {
        "documentName":"invoices\\Invoice_3.pdf",
        "pages":1,
        "errors":[

        ],
        "status":"succeeded"
      },
      {
        "documentName":"invoices\\Invoice_4.pdf",
        "pages":1,
        "errors":[

        ],
        "status":"succeeded"
      },
      {
        "documentName":"invoices\\Invoice_5.pdf",
        "pages":1,
        "errors":[

        ],
        "status":"succeeded"
      }
    ],
    "errors":[

    ]
  },
  "keys":{
    "0":[
      "Address:",
      "Invoice For:",
      "Microsoft",
      "Page"
    ]
  }
}
```

## <a name="analyze-forms-with-a-custom-model"></a>Analyser les formulaires avec un modèle personnalisé

À présent, vous allez utiliser le modèle nouvellement entraîné pour analyser un document et en extraire des tableaux et des paires clé-valeur. Appelez l’API **[Analyze Form](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm)** (Analyser un formulaire) en exécutant la commande cURL suivante. Avant d’exécuter la commande, apportez les modifications suivantes :

1. Remplacez `{Endpoint}` par le point de terminaison que vous avez obtenu avec votre clé d’abonnement Form Recognizer. Vous la trouverez sous l’onglet **Vue d’ensemble** de la ressource Form Recognizer.
1. Remplacez `{model ID}` par l’ID de modèle que vous avez reçu à la section précédente.
1. Remplacez `{SAS URL}` par une URL SAS vers votre fichier dans le stockage Azure. Suivez les étapes de la section Entraînement, mais au lieu d’obtenir une URL SAS pour tout le conteneur d’objets Blob, obtenez l’URL du fichier spécifique que vous souhaitez analyser.
1. Remplacez `{subscription key}` par votre clé d’abonnement.

```bash
curl -v "https://{Endpoint}/formrecognizer/v2.0/custom/models/{model ID}/analyze?includeTextDetails=true" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" -d "{ 'source': '{SAS URL}' } "
```

Vous recevrez une réponse `202 (Success)` avec un en-tête **Operation-Location**. La valeur de cet en-tête inclut un ID de résultats à utiliser pour suivre les résultats de l’opération d’analyse. Enregistrez cet ID de résultats pour l’étape suivante.

### <a name="get-the-analyze-results"></a>Obtenir les résultats de l’analyse

Appelez l’API **[Get Analyze Form Result](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/GetAnalyzeFormResult)** (Obtenir le résultat de l’analyse du formulaire) pour interroger les résultats de l’opération d’analyse.

1. Remplacez `{Endpoint}` par le point de terminaison que vous avez obtenu avec votre clé d’abonnement Form Recognizer. Vous la trouverez sous l’onglet **Vue d’ensemble** de la ressource Form Recognizer.
1. Remplacez `{result ID}` par l’ID que vous avez reçu à la section précédente.
1. Remplacez `{subscription key}` par votre clé d’abonnement.

```bash
curl -X GET "https://{Endpoint}/formrecognizer/v2.0/custom/models/{model ID}/analyzeResults/{result ID}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

Vous recevrez une réponse `200 (Success)` avec un corps JSON sous la forme suivante. La sortie a été raccourcie à des fins de simplicité. Notez le champ `"status"` vers le bas. La valeur est `"succeeded"` quand l’opération d’analyse est terminée. Si l’opération d’analyse n’est pas terminée, vous devez réinterroger le service en réexécutant la commande. Nous vous recommandons d’attendre une seconde ou plus entre chaque appel.

Dans les modèles personnalisés entraînés sans étiquette, les tables et les associations de paires clé/valeur se trouvent dans le nœud `"pageResults"` de la sortie JSON. Dans les modèles personnalisés entraînés avec des étiquettes, les associations de paires clé/valeur se trouvent dans le nœud `"documentResults"`. Si vous avez également spécifié l’extraction de texte brut par le biais du paramètre d’URL *includeTextDetails*, le nœud `"readResults"` affiche le contenu et les positions de tout le texte dans le document.

Cet exemple de sortie JSON a été raccourci pour simplifier. Consultez l’[exemple de sortie complet sur GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/analyze-result-invoice-6.pdf.json).

```JSON
{
  "status": "succeeded",
  "createdDateTime": "2020-08-21T00:46:25Z",
  "lastUpdatedDateTime": "2020-08-21T00:46:32Z",
  "analyzeResult": {
    "version": "2.0.0",
    "readResults": [
      {
        "page": 1,
        "angle": 0,
        "width": 8.5,
        "height": 11,
        "unit": "inch",
        "lines": [
          {
            "text": "Project Statement",
            "boundingBox": [
              5.0153,
              0.275,
              8.0944,
              0.275,
              8.0944,
              0.7125,
              5.0153,
              0.7125
            ],
            "words": [
              {
                "text": "Project",
                "boundingBox": [
                  5.0153,
                  0.275,
                  6.2278,
                  0.275,
                  6.2278,
                  0.7125,
                  5.0153,
                  0.7125
                ]
              },
              {
                "text": "Statement",
                "boundingBox": [
                  6.3292,
                  0.275,
                  8.0944,
                  0.275,
                  8.0944,
                  0.7125,
                  6.3292,
                  0.7125
                ]
              }
            ]
          },
        ...
        ]
      }
    ],
    "pageResults": [
      {
        "page": 1,
        "keyValuePairs": [
          {
            "key": {
              "text": "Date:",
              "boundingBox": [
                6.9722,
                1.0264,
                7.3417,
                1.0264,
                7.3417,
                1.1931,
                6.9722,
                1.1931
              ],
              "elements": [
                "#/readResults/0/lines/2/words/0"
              ]
            },
            "confidence": 1
          },
         ...
        ],
        "tables": [
          {
            "rows": 4,
            "columns": 5,
            "cells": [
              {
                "text": "Training Date",
                "rowIndex": 0,
                "columnIndex": 0,
                "boundingBox": [
                  0.6931,
                  4.2444,
                  1.5681,
                  4.2444,
                  1.5681,
                  4.4125,
                  0.6931,
                  4.4125
                ],
                "confidence": 1,
                "rowSpan": 1,
                "columnSpan": 1,
                "elements": [
                  "#/readResults/0/lines/15/words/0",
                  "#/readResults/0/lines/15/words/1"
                ],
                "isHeader": true,
                "isFooter": false
              },
              ...
            ]
          }
        ],
        "clusterId": 0
      }
    ],
    "documentResults": [],
    "errors": []
  }
}
```

### <a name="improve-results"></a>Améliorer les résultats

[!INCLUDE [improve results](improve-result-unlabeled.md)]

## <a name="manage-custom-models"></a>Gérer des modèles personnalisés

### <a name="get-a-list-of-custom-models"></a>Obtenir une liste de modèles personnalisés

Utilisez l’API **[List Custom Models](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/GetCustomModels)** (Lister les modèles personnalisés) dans la commande suivante pour retourner la liste de tous les modèles personnalisés qui appartiennent à votre abonnement.

1. Remplacez `{Endpoint}` par le point de terminaison que vous avez obtenu avec votre abonnement Form Recognizer.
1. Remplacez `{subscription key}` par la clé d’abonnement que vous avez copiée à l’étape précédente.

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.0/custom/models?op=full"
-H "Ocp-Apim-Subscription-Key: {subscription key}"
```

Vous recevrez une réponse de réussite `200`, avec des données JSON telles que les suivantes. L’élément `"modelList"` contient tous les modèles créés et leurs informations.

```json
{
  "summary": {
    "count": 0,
    "limit": 0,
    "lastUpdatedDateTime": "string"
  },
  "modelList": [
    {
      "modelId": "string",
      "status": "creating",
      "createdDateTime": "string",
      "lastUpdatedDateTime": "string"
    }
  ],
  "nextLink": "string"
}
```

### <a name="get-a-specific-model"></a>Obtenir un modèle spécifique

Pour récupérer des informations détaillées sur un modèle personnalisé spécifique, utilisez l’API **[Get Custom Model](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/GetCustomModel)** (Obtenir un modèle personnalisé) dans la commande suivante.

1. Remplacez `{Endpoint}` par le point de terminaison que vous avez obtenu avec votre abonnement Form Recognizer.
1. Remplacez `{subscription key}` par la clé d’abonnement que vous avez copiée à l’étape précédente.
1. Remplacez `{modelId}` par l’ID du modèle personnalisé que vous souhaitez rechercher.

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.0/custom/models/{modelId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

Vous recevrez une réponse de réussite `200`, avec des données JSON telles que les suivantes.

```json
{
  "modelInfo": {
    "modelId": "string",
    "status": "creating",
    "createdDateTime": "string",
    "lastUpdatedDateTime": "string"
  },
  "keys": {
    "clusters": {}
  },
  "trainResult": {
    "trainingDocuments": [
      {
        "documentName": "string",
        "pages": 0,
        "errors": [
          "string"
        ],
        "status": "succeeded"
      }
    ],
    "fields": [
      {
        "fieldName": "string",
        "accuracy": 0.0
      }
    ],
    "averageModelAccuracy": 0.0,
    "errors": [
      {
        "message": "string"
      }
    ]
  }
}
```

### <a name="delete-a-model-from-the-resource-account"></a>Supprimer un modèle du compte de ressource

Vous pouvez aussi supprimer un modèle de votre compte en référençant son ID. Cette commande appelle l’API **[Delete Custom Model](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/DeleteCustomModel)** (Supprimer un modèle personnalisé) pour supprimer le modèle utilisé dans la section précédente.

1. Remplacez `{Endpoint}` par le point de terminaison que vous avez obtenu avec votre abonnement Form Recognizer.
1. Remplacez `{subscription key}` par la clé d’abonnement que vous avez copiée à l’étape précédente.
1. Remplacez `{modelId}` par l’ID du modèle personnalisé que vous souhaitez rechercher.

```bash
curl -v -X DELETE "https://{Endpoint}/formrecognizer/v2.0/custom/models/{modelId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

Vous recevrez une réponse de réussite `204`, indiquant que votre modèle est marqué pour suppression. Les artefacts de modèle seront supprimés dans les 48 heures.

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez utilisé l’API REST Form Recognizer pour entraîner des modèles et analyser des formulaires de différentes manières. Consultez à présent la documentation de référence pour explorer l’API Form Recognizer plus en détail.

> [!div class="nextstepaction"]
> [Documentation de référence sur l’API REST](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm)
