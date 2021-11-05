---
title: Utiliser l’API REST Azure Form Recognizer
description: Utilisez l’API REST Form Recognizer afin de créer une application de traitement des formulaires qui extrait des paires clé/valeur et des données de tableau de vos documents personnalisés.
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 11/02/2021
ms.author: lajanuar
ms.custom: ignite-fall-2021
ms.openlocfilehash: d74e7e84461eadc40cd4d2940ae083a411f12f57
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096455"
---
<!-- markdownlint-disable MD001 -->
<!-- markdownlint-disable MD024 -->
<!-- markdownlint-disable MD033 -->
<!-- markdownlint-disable MD034 -->

> [!NOTE]
>
> * Ce projet cible la version **2.1** de l’API Azure Form Recognizer et utilise cURL pour exécuter des appels d’API REST.

| [API REST Form Recognizer](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeWithCustomForm) | [Informations de référence de l’API REST Azure](/rest/api/azure/) |

## <a name="prerequisites"></a>Prérequis

* [cURL](https://curl.haxx.se/windows/) installé.
* [PowerShell version 6.0 +](/powershell/scripting/install/installing-powershell-core-on-windows) ou une application de ligne de commande similaire.
* Abonnement Azure - [En créer un gratuitement](https://azure.microsoft.com/free/cognitive-services/)
* Un blob Stockage Azure qui contient un jeu de données d’apprentissage. Consultez [Créer un jeu de données d’entraînement pour un modèle personnalisé](../../build-training-data-set.md) pour obtenir des conseils et des options pour constituer votre jeu de données d’entraînement. Vous pouvez utiliser les fichiers disponibles dans le dossier **Train** (Entraîner) de l’[exemple de jeu de données](https://go.microsoft.com/fwlink/?linkid=2090451) (téléchargez et extrayez *sample_data.zip*).
* Une fois que vous avez votre abonnement Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title="créez une ressource Form Recognizer"  target="_blank">créer une ressource Form Recognizer</a> sur le portail Azure pour obtenir votre clé et votre point de terminaison. À la fin du déploiement, sélectionnez **Accéder à la ressource**.
  * Vous aurez besoin de la clé et du point de terminaison de la ressource que vous créez pour connecter votre application à l’API Form Recognizer. Vous collerez votre clé et votre point de terminaison dans le code ci-dessous plus loin dans le projet.
  * Vous pouvez utiliser le niveau tarifaire Gratuit (`F0`) pour tester le service, puis passer par la suite à un niveau payant pour la production.
<<<<<<< HEAD:articles/applied-ai-services/form-recognizer/includes/how-to-guides/rest-api.md
* L’URL d’une **image de ticket de caisse**. Vous pouvez utiliser un [exemple d’image](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/contoso-allinone.jpg).
* L’URL d’une **image de carte de visite**. Vous pouvez utiliser un [exemple d’image](https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/business_cards/business-card-english.jpg).
* L’URL d’une **image de facture**. Vous pouvez utiliser un [exemple d’image](https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/forms/Invoice_1.pdf).
* L’URL d’une **image de document d’identité**. Vous pouvez utiliser un [exemple d’image](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/DriverLicense.png).
=======
* L’URL d’une **image de ticket de caisse**. Pour ce guide de démarrage rapide, vous pouvez utiliser un [exemple d’image](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/contoso-allinone.jpg).
* L’URL d’une **image de carte de visite**. Pour ce guide de démarrage rapide, vous pouvez utiliser un [exemple d’image](https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/business_cards/business-card-english.jpg).
* L’URL d’une **image de facture**. Vous pouvez utiliser un [exemple de document](https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/forms/Invoice_1.pdf) pour ce guide de démarrage rapide.
* L’URL d’une **image de document d’identité**. Vous pouvez utiliser un [exemple d’image](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/DriverLicense.png).
>>>>>>> 103f7cf9752d7b4e4c9bf3da2c3649ad27ebfd2f:articles/applied-ai-services/form-recognizer/includes/quickstarts/rest-api.md

## <a name="analyze-layout"></a>Analyser la disposition

Vous pouvez utiliser Form Recognizer pour analyser et extraire les tableaux, les marques de sélection, le texte et la structure dans les documents, sans avoir besoin d’entraîner un modèle. Pour plus d’informations sur l’extraction d’une disposition, consultez le [guide conceptuel des dispositions](../../concept-layout.md). Avant d’exécuter la commande, apportez les modifications suivantes :

1. Remplacez `{endpoint}` par le point de terminaison que vous avez obtenu avec votre abonnement Form Recognizer.
1. Remplacez `{subscription key}` par la clé d’abonnement que vous avez copiée à l’étape précédente.
1. Remplacez `\"{your-document-url}` par l’un des exemples d’URL.

#### <a name="request"></a>Requête

```bash
curl -v -i POST "https://{endpoint}/formrecognizer/v2.1/layout/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{'source': '{your-document-url}'}"
```

#### <a name="operation-location"></a>Operation-Location

Vous recevrez une réponse `202 (Success)` incluant un en-tête **Operation-Location**. La valeur de cet en-tête contient un ID de résultats qui vous permet d’interroger l’état de l’opération asynchrone et d’obtenir les résultats :

https://<span></span>cognitiveservice/formrecognizer/v2.1/layout/analyzeResults/ **{ID-résultats}** . 

Dans l’exemple suivant, la chaîne qui suit `analyzeResults/` dans l’URL correspond à l’ID des résultats.

```console
https://cognitiveservice/formrecognizer/v2/layout/analyzeResults/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

### <a name="get-layout-results"></a>Obtenir les résultats de la disposition

Après avoir appelé l’API **[Analyze Layout](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeLayoutAsync)** (Analyser la disposition), vous appelez l’API **[Get Analyze Layout Result](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/GetAnalyzeLayoutResult)** (Obtenir le résultat de l’analyse de la disposition) pour obtenir l’état de l’opération et les données extraites. Avant d’exécuter la commande, apportez les modifications suivantes :

1. Remplacez `{endpoint}` par le point de terminaison que vous avez obtenu avec votre abonnement Form Recognizer.
1. Remplacez `{subscription key}` par la clé d’abonnement que vous avez copiée à l’étape précédente.
1. Remplacez `{resultId}` par l’ID de résultats de l’étape précédente.
<!-- markdownlint-disable MD024 -->

#### <a name="request"></a>Requête

```bash
curl -v -X GET "https://{endpoint}/formrecognizer/v2.1/layout/analyzeResults/{resultId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="examine-the-results"></a>Examiner les résultats

Vous recevez une réponse `200 (success)` avec du contenu JSON.

Regardez l’image de facture suivante et sa sortie JSON correspondante.

* Le nœud `"readResults"` contient chaque ligne de texte avec sa position de cadre englobant respectif dans la page.
* Le nœud `selectionMarks` affiche chaque marque de sélection (case à cocher ou case d’option) et indique si son état est « sélectionné » ou « non sélectionné ».
* La section `"pageResults"` comprend les tableaux extraits. Pour chaque tableau, le texte, l’index de ligne et de colonne, l’étendue de ligne et de colonne, le cadre englobant, etc. sont extraits.

:::image type="content" source="../../media/contoso-invoice.png" alt-text="Document d’énoncé de projet Contoso avec un tableau.":::

#### <a name="response-body"></a>Response body

Cette sortie a été raccourcie à des fins de simplicité. Consultez l’[exemple de sortie complet sur GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/sample-layout-output.json).

```json
{
    "status": "succeeded",
    "createdDateTime": "2020-08-20T20:40:50Z",
    "lastUpdatedDateTime": "2020-08-20T20:40:55Z",
    "analyzeResult": {
        "version": "2.1.0",
        "readResults": [
            {
                "page": 1,
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
                "selectionMarks": [
                    {
                        "boundingBox": [
                            3.9737,
                            3.7475,
                            4.1693,
                            3.7475,
                            4.1693,
                            3.9428,
                            3.9737,
                            3.9428
                        ],
                        "confidence": 0.989,
                        "state": "selected"
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
                                    "#/readResults/0/lines/12/words/0",
                                    "#/readResults/0/lines/12/words/1"
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

Cette section montre comment analyser et extraire les champs communs de tickets de caisse émis aux États-Unis à l’aide d’un modèle de ticket préentraîné. Pour plus d’informations sur l’analyse des tickets de caisse, consultez le [guide conceptuel des tickets de caisse](../../concept-receipt.md). Pour commencer à analyser un reçu, appelez l’API **[Analyze Receipt](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeReceiptAsync)** (Analyser le reçu) en utilisant la commande cURL ci-dessous. Avant d’exécuter la commande, apportez les modifications suivantes :

1. Remplacez `{endpoint}` par le point de terminaison que vous avez obtenu avec votre abonnement Form Recognizer.
1. Remplacez `{your receipt URL}` par l’adresse URL d’une image de ticket de caisse.
1. Remplacez `{subscription key>` par la clé d’abonnement que vous avez copiée à l’étape précédente.

#### <a name="request"></a>Requête

```bash
curl -i -X POST "https://{endpoint}/formrecognizer/v2.1/prebuilt/receipt/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{your receipt URL}'}"
```

#### <a name="operation-location"></a>Operation-Location

Vous recevrez une réponse `202 (Success)` incluant un en-tête **Operation-Location**. La valeur de cet en-tête contient un ID de résultats qui vous permet d’interroger l’état de l’opération asynchrone et d’obtenir les résultats :

*https://<span></span>cognitiveservice/formrecognizer/v2.1/prebuilt/receipt/analyzeResults/**{ID-résultats}* **

Dans l’exemple suivant, la chaîne qui suit `operations/` correspond à l’ID des résultats :

```console
https://cognitiveservice/formrecognizer/v2.1/prebuilt/receipt/operations/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

### <a name="get-receipt-results"></a>Récupération des résultats de ticket de caisse

Après avoir appelé l’API **Analyze Receipt**, vous appelez l’API **[Get Analyze Receipt Result](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/GetAnalyzeReceiptResult)** pour obtenir l’état de l’opération et les données extraites. Avant d’exécuter la commande, apportez les modifications suivantes :

1. Remplacez `{endpoint}` par le point de terminaison que vous avez obtenu avec votre clé d’abonnement Form Recognizer. Vous la trouverez sous l’onglet **Vue d’ensemble** de la ressource Form Recognizer.
1. Remplacez `{resultId}` par l’ID de résultats de l’étape précédente.
1. Remplacez `{subscription key}` par votre clé d’abonnement.

#### <a name="request"></a>Requête

```bash
curl -X GET "https://{endpoint}/formrecognizer/v2.1/prebuilt/receipt/analyzeResults/{resultId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="examine-the-response"></a>Examiner la réponse

Vous recevez une réponse `200 (Success)` avec une sortie JSON. Le premier champ, `"status"`, indique l’état de l’opération. Si l’opération n’est pas terminée, la valeur de `"status"` sera `"running"` ou `"notStarted"`, et vous devez rappeler l’API, manuellement ou via un script. Nous vous recommandons d’attendre une seconde ou plus entre chaque appel.

Le nœud `"readResults"` contient tout le texte reconnu (si vous définissez le paramètre facultatif *includeTextDetails* sur `true`). Le texte est organisé par page, puis par ligne, puis par mots individuels. Le nœud `"documentResults"` contient les valeurs spécifiques du reçu découvertes par le modèle. C’est là que vous trouverez des paires clé/valeur utiles, telles que la taxe, le total et l’adresse du commerçant.

Regardez l’image de ticket suivante et sa sortie JSON correspondante.

![Ticket de caisse du magasin Contoso](../../media/contoso-allinone.jpg)

#### <a name="response-body"></a>Response body

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

## <a name="analyze-business-cards"></a>Analyser les cartes de visite

Cette section montre comment analyser et extraire les champs courants des cartes de visite en anglais en utilisant un modèle préentraîné. Pour plus d’informations sur l’analyse des cartes de visite, consultez le [guide conceptuel des cartes de visite](../../concept-business-card.md). Pour commencer à analyser une carte de visite, appelez l’API **[Analyze Business Card](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeBusinessCardAsync)** (Analyser la carte de visite) à l’aide de la commande cURL ci-dessous. Avant d’exécuter la commande, apportez les modifications suivantes :

1. Remplacez `{endpoint}` par le point de terminaison que vous avez obtenu avec votre abonnement Form Recognizer.
1. Remplacez `{your business card URL}` par l’adresse URL d’une image de ticket de caisse.
1. Remplacez `{subscription key}` par la clé d’abonnement que vous avez copiée à l’étape précédente.

#### <a name="request"></a>Requête

```bash
curl -i -X POST "https://{endpoint}/formrecognizer/v2.1/prebuilt/businessCard/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{your receipt URL}'}"
```

#### <a name="operation-location"></a>Operation-Location

Vous recevrez une réponse `202 (Success)` incluant un en-tête **Operation-Location**. La valeur de cet en-tête contient un ID de résultats qui vous permet d’interroger l’état de l’opération asynchrone et d’obtenir les résultats :

_https://<span></span>cognitiveservice/formrecognizer/v2.1/prebuilt/businessCard/analyzeResults/ **{ID-résultats}**_

Dans l’exemple suivant, la chaîne qui suit `analyzeResults/` dans l’URL correspond à l’ID des résultats.

```console
https://cognitiveservice/formrecognizer/v2.1/prebuilt/businessCard/analyzeResults/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

### <a name="get-business-card-results"></a>Obtenir les résultats de la carte de visite

Après avoir appelé l’API **Analyze Business Card** (Analyser la carte de visite), appelez l’API **[Get Analyze Business Card Result](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/GetAnalyzeBusinessCardResult)** (Obtenir le résultat de l’analyse de la carte de visite) pour obtenir l’état de l’opération et les données extraites. Avant d’exécuter la commande, apportez les modifications suivantes :

1. Remplacez `{endpoint}` par le point de terminaison que vous avez obtenu avec votre clé d’abonnement Form Recognizer. Vous la trouverez sous l’onglet **Vue d’ensemble** de la ressource Form Recognizer.
1. Remplacez `{resultId}` par l’ID de résultats de l’étape précédente.
1. Remplacez `{subscription key}` par votre clé d’abonnement.

```bash
curl -v -X GET https://{endpoint}/formrecognizer/v2.1/prebuilt/businessCard/analyzeResults/{resultId}"
-H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="examine-the-response"></a>Examiner la réponse

Vous recevez une réponse `200 (Success)` avec une sortie JSON.

Le nœud `"readResults"` contient tout le texte reconnu. Le texte est organisé par page, puis par ligne, puis par mots individuels. Le nœud `"documentResults"` contient les valeurs spécifiques de la carte de visite découvertes par le modèle. C’est là que vous trouverez des informations de contact utiles comme le nom de la société, le prénom, le nom, le numéro de téléphone, etc.

![Carte de visite de la société Contoso](../../media/business-card-english.jpg)

Cet exemple de sortie JSON a été raccourci pour une meilleure lisibilité. Consultez l’[exemple de sortie complet sur GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/business-card-result.json).

```json
{
    "status": "succeeded",
    "createdDateTime":"2021-02-09T18:14:05Z",
    "lastUpdatedDateTime":"2021-02-09T18:14:10Z",
    "analyzeResult": {
        "version": "2.1.0",
        "readResults": [
            {
             "page":1,
             "angle":-16.6836,
             "width":4032,
             "height":3024,
             "unit":"pixel"
          }
        ],
        "documentResults": [
            {
                "docType": "prebuilt:businesscard",
                "pageRange": [
                    1,
                    1
                ],
                "fields": {
                    "ContactNames": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "object",
                                "valueObject": {
                                    "FirstName": {
                                        "type": "string",
                                        "valueString": "Avery",
                                        "text": "Avery",
                                        "boundingBox": [
                                            703,
                                            1096,
                                            1134,
                                            989,
                                            1165,
                                            1109,
                                            733,
                                            1206
                                        ],
                                        "page": 1
                                },
                                "text": "Dr. Avery Smith",
                                "boundingBox": [
                                    419.3,
                                    1154.6,
                                    1589.6,
                                    877.9,
                                    1618.9,
                                    1001.7,
                                    448.6,
                                    1278.4
                                ],
                                "confidence": 0.993
                            }
                        ]
                    },
                    "Emails": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "avery.smith@contoso.com",
                                "text": "avery.smith@contoso.com",
                                "boundingBox": [
                                    2107,
                                    934,
                                    2917,
                                    696,
                                    2935,
                                    764,
                                    2126,
                                    995
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Websites": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "https://www.contoso.com/",
                                "text": "https://www.contoso.com/",
                                "boundingBox": [
                                    2121,
                                    1002,
                                    2992,
                                    755,
                                    3014,
                                    826,
                                    2143,
                                    1077
                                ],
                                "page": 1,
                                "confidence": 0.995
                            }
                        ]
                    }
                }
            }
        ]
    }
}
```

Le script imprime les réponses dans la console tant que l’opération **Analyze Business Card** n’est pas terminée.

## <a name="analyze-invoices"></a>Analyser les factures

Vous pouvez utiliser Form Recognizer pour extraire le texte des champs et les valeurs sémantiques d’une facture donnée.  Pour commencer l’analyse d’une facture, utilisez la commande cURL ci-dessous. Pour plus d’informations sur l’analyse des factures, consultez le [guide conceptuel des factures](../../concept-invoice.md). Pour commencer l’analyse d’une facture, appelez l’API **[Analyser une facture](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/5ed8c9843c2794cbb1a96291)** à l’aide de la commande cURL ci-dessous. Avant d’exécuter la commande, apportez les modifications suivantes :

1. Remplacez `{endpoint}` par le point de terminaison que vous avez obtenu avec votre abonnement Form Recognizer.
1. Remplacez `{your invoice URL}` par l’adresse URL d’un document de facture.
1. Remplacez `{subscription key}` par votre clé d’abonnement.

#### <a name="request"></a>Requête

```bash
curl -v -i POST https://{endpoint}/formrecognizer/v2.1/prebuilt/invoice/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key:  {subscription key}" --data-ascii "{'source': '{your invoice URL}'}"
```

#### <a name="operation-location"></a>Operation-Location

Vous recevrez une réponse `202 (Success)` incluant un en-tête **Operation-Location**. La valeur de cet en-tête contient un ID de résultats qui vous permet d’interroger l’état de l’opération asynchrone et d’obtenir les résultats :

 _https://<span></span>cognitiveservice/formrecognizer/v2.1/prebuilt/receipt/analyzeResults/ **{ID-résultats}**_

Dans l’exemple suivant, la chaîne qui suit `analyzeResults/` dans l’URL correspond à l’ID des résultats :

```console
https://cognitiveservice/formrecognizer/v2.1/prebuilt/invoice/analyzeResults/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

### <a name="get-invoice-results"></a>Obtenir les résultats de la facture

Après avoir appelé l’API **Analyser une facture**, vous appelez l’API **[Obtenir le résultat de l’analyse de la facture](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/5ed8c9acb78c40a2533aee83)** pour obtenir l’état de l’opération et les données extraites. Avant d’exécuter la commande, apportez les modifications suivantes :

1. Remplacez `{endpoint}` par le point de terminaison que vous avez obtenu avec votre clé d’abonnement Form Recognizer. Vous la trouverez sous l’onglet **Vue d’ensemble** de la ressource Form Recognizer.
1. Remplacez `{resultId}` par l’ID de résultats de l’étape précédente.
1. Remplacez `{subscription key}` par votre clé d’abonnement.

#### <a name="request"></a>Requête

```bash
curl -v -X GET "https://{endpoint}/formrecognizer/v2.1/prebuilt/invoice/analyzeResults/{resultId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="examine-the-response"></a>Examiner la réponse

Vous recevez une réponse `200 (Success)` avec une sortie JSON.

* Le champ `"readResults"` contient chaque ligne de texte extraite de la facture.
* Le champ `"pageResults"` comprend les tableaux et les marques de sélection extraits de la facture.
* Le champ `"documentResults"` contient les informations relatives aux paires clé/valeur pour les parties les plus pertinentes de la facture.

Examinez la facture suivante et sa sortie JSON correspondante.

* [Exemple de facture](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/curl/form-recognizer/sample-invoice.pdf)

#### <a name="response-body"></a>Response body

Ce contenu JSON a été raccourci pour une meilleure lisibilité. Consultez l’[exemple de sortie complet sur GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/sample-invoice-output.json).

```json
{
    "status": "succeeded",
    "createdDateTime": "2020-11-06T23:32:11Z",
    "lastUpdatedDateTime": "2020-11-06T23:32:20Z",
    "analyzeResult": {
        "version": "2.1.0",
        "readResults": [{
            "page": 1,
            "angle": 0,
            "width": 8.5,
            "height": 11,
            "unit": "inch"
        }],
        "pageResults": [{
            "page": 1,
            "tables": [{
                "rows": 3,
                "columns": 4,
                "cells": [{
                    "rowIndex": 0,
                    "columnIndex": 0,
                    "text": "QUANTITY",
                    "boundingBox": [0.4953,
                    5.7306,
                    1.8097,
                    5.7306,
                    1.7942,
                    6.0122,
                    0.4953,
                    6.0122]
                },
                {
                    "rowIndex": 0,
                    "columnIndex": 1,
                    "text": "DESCRIPTION",
                    "boundingBox": [1.8097,
                    5.7306,
                    5.7529,
                    5.7306,
                    5.7452,
                    6.0122,
                    1.7942,
                    6.0122]
                },
                ...
                ],
                "boundingBox": [0.4794,
                5.7132,
                8.0158,
                5.714,
                8.0118,
                6.5627,
                0.4757,
                6.5619]
            },
            {
                "rows": 2,
                "columns": 6,
                "cells": [{
                    "rowIndex": 0,
                    "columnIndex": 0,
                    "text": "SALESPERSON",
                    "boundingBox": [0.4979,
                    4.963,
                    1.8051,
                    4.963,
                    1.7975,
                    5.2398,
                    0.5056,
                    5.2398]
                },
                {
                    "rowIndex": 0,
                    "columnIndex": 1,
                    "text": "P.O. NUMBER",
                    "boundingBox": [1.8051,
                    4.963,
                    3.3047,
                    4.963,
                    3.3124,
                    5.2398,
                    1.7975,
                    5.2398]
                },
                ...
                ],
                "boundingBox": [0.4976,
                4.961,
                7.9959,
                4.9606,
                7.9959,
                5.5204,
                0.4972,
                5.5209]
            }]
        }],
        "documentResults": [{
            "docType": "prebuilt:invoice",
            "pageRange": [1,
            1],
            "fields": {
                "AmountDue": {
                    "type": "number",
                    "valueNumber": 610,
                    "text": "$610.00",
                    "boundingBox": [7.3809,
                    7.8153,
                    7.9167,
                    7.8153,
                    7.9167,
                    7.9591,
                    7.3809,
                    7.9591],
                    "page": 1,
                    "confidence": 0.875
                },
                "BillingAddress": {
                    "type": "string",
                    "valueString": "123 Bill St, Redmond WA, 98052",
                    "text": "123 Bill St, Redmond WA, 98052",
                    "boundingBox": [0.594,
                    4.3724,
                    2.0125,
                    4.3724,
                    2.0125,
                    4.7125,
                    0.594,
                    4.7125],
                    "page": 1,
                    "confidence": 0.997
                },
                "BillingAddressRecipient": {
                    "type": "string",
                    "valueString": "Microsoft Finance",
                    "text": "Microsoft Finance",
                    "boundingBox": [0.594,
                    4.1684,
                    1.7907,
                    4.1684,
                    1.7907,
                    4.2837,
                    0.594,
                    4.2837],
                    "page": 1,
                    "confidence": 0.998
                },
                ...
            }
        }]
    }
}
```

## <a name="analyze-identity-id-documents"></a>Analyse de pièces d’identité

Pour commencer l’analyse d’un document d’identification, utilisez la commande cURL ci-dessous. Pour plus d’informations sur l’analyse des documents d’identité, consultez le [Guide conceptuel des documents d’identité](../../concept-id-document.md). Pour commencer l’analyse d’un document d’identité, appelez l’API **[Analyser un document d’identité](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/5f74a7738978e467c5fb8707)** avec la commande cURL ci-dessous. Avant d’exécuter la commande, apportez les modifications suivantes :

1. Remplacez `{endpoint}` par le point de terminaison que vous avez obtenu avec votre abonnement Form Recognizer.
1. Remplacez `{your ID document URL}` par l’adresse URL d’une image de ticket de caisse.
1. Remplacez `{subscription key}` par la clé d’abonnement que vous avez copiée à l’étape précédente.

#### <a name="request"></a>Requête

```bash
curl -i -X POST "https://{endpoint}/formrecognizer/v2.1/prebuilt/idDocument/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{your ID document URL}'}"
```

#### <a name="operation-location"></a>Operation-Location

Vous recevrez une réponse `202 (Success)` incluant un en-tête **Operation-Location**. La valeur de cet en-tête contient un ID de résultats qui vous permet d’interroger l’état de l’opération asynchrone et d’obtenir les résultats :

_https://<span></span>cognitiveservice/formrecognizer/v2.1/prebuilt/documentId/analyzeResults/ **{ID-résultats}**_

Dans l’exemple suivant, la chaîne qui suit `analyzeResults/` correspond à l’ID des résultats :

```console
https://westus.api.cognitive.microsoft.com/formrecognizer/v2.1/prebuilt/idDocument/analyzeResults/83d0137b-28e1-4051-98ce-42bd21f77ae0
```

### <a name="get-the-analyze-id-document-result"></a>Get the Analyze ID Document result

Après avoir appelé l’API **Analyze ID Document**, appelez l’API **[Get Analyze ID Document Result](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/GetAnalyzeBusinessCardResult)** pour récupérer l’état de l’opération et les données extraites.  Avant d’exécuter la commande, apportez les modifications suivantes :

1. Remplacez `{endpoint}` par le point de terminaison que vous avez obtenu avec votre clé d’abonnement Form Recognizer. Vous la trouverez sous l’onglet **Vue d’ensemble** de la ressource Form Recognizer.
1. Remplacez `{resultId}` par l’ID de résultats de l’étape précédente.
1. Remplacez `{subscription key}` par votre clé d’abonnement.

#### <a name="request"></a>Requête

```bash
curl -X GET "https://{endpoint}/formrecognizer/v2.1/prebuilt/businessCard/analyzeResults/{resultId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="examine-the-response"></a>Examiner la réponse

Vous recevez une réponse `200 (Success)` avec une sortie JSON. Le premier champ, `"status"`, indique l’état de l’opération. Si l’opération n’est pas terminée, la valeur de `"status"` est `"running"` ou `"notStarted"`, et vous devez rappeler l’API, manuellement ou avec un script, jusqu’à ce que vous receviez la valeur `succeeded`. Nous vous recommandons d’attendre une seconde ou plus entre chaque appel.

* Le champ `"readResults"` contient chaque ligne de texte extraite du document d’identité.
* Le champ `"documentResults"` contient un tableau d’objets, chacun représentant un document d’ID détecté dans le document d’entrée.

Voici un exemple de document d’identité et sa sortie JSON correspondante

* :::image type="content" source="https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/DriverLicense.png" alt-text="exemple de permis de conduire":::

#### <a name="response-body"></a>Response body

```json
{
    "status": "succeeded",
    "createdDateTime": "2021-04-13T17:24:52Z",
    "lastUpdatedDateTime": "2021-04-13T17:24:55Z",
    "analyzeResult": {
      "version": "2.1.0",
      "readResults": [
        {
          "page": 1,
          "angle": -0.2823,
          "width": 450,
          "height": 294,
          "unit": "pixel"
        }
      ],
      "documentResults": [
        {
          "docType": "prebuilt:idDocument:driverLicense",
          "docTypeConfidence": 0.995,
          "pageRange": [
            1,
            1
          ],
          "fields": {
            "Address": {
              "type": "string",
              "valueString": "123 STREET ADDRESS YOUR CITY WA 99999-1234",
              "text": "123 STREET ADDRESS YOUR CITY WA 99999-1234",
              "boundingBox": [
                158,
                151,
                326,
                151,
                326,
                177,
                158,
                177
              ],
              "page": 1,
              "confidence": 0.965
            },
            "CountryRegion": {
              "type": "countryRegion",
              "valueCountryRegion": "USA",
              "confidence": 0.99
            },
            "DateOfBirth": {
              "type": "date",
              "valueDate": "1958-01-06",
              "text": "01/06/1958",
              "boundingBox": [
                187,
                133,
                272,
                132,
                272,
                148,
                187,
                149
              ],
              "page": 1,
              "confidence": 0.99
            },
            "DateOfExpiration": {
              "type": "date",
              "valueDate": "2020-08-12",
              "text": "08/12/2020",
              "boundingBox": [
                332,
                230,
                414,
                228,
                414,
                244,
                332,
                245
              ],
              "page": 1,
              "confidence": 0.99
            },
            "DocumentNumber": {
              "type": "string",
              "valueString": "LICWDLACD5DG",
              "text": "LIC#WDLABCD456DG",
              "boundingBox": [
                162,
                70,
                307,
                68,
                307,
                84,
                163,
                85
              ],
              "page": 1,
              "confidence": 0.99
            },
            "FirstName": {
              "type": "string",
              "valueString": "LIAM R.",
              "text": "LIAM R.",
              "boundingBox": [
                158,
                102,
                216,
                102,
                216,
                116,
                158,
                116
              ],
              "page": 1,
              "confidence": 0.985
            },
            "LastName": {
              "type": "string",
              "valueString": "TALBOT",
              "text": "TALBOT",
              "boundingBox": [
                160,
                86,
                213,
                85,
                213,
                99,
                160,
                100
              ],
              "page": 1,
              "confidence": 0.987
            },
            "Region": {
              "type": "string",
              "valueString": "Washington",
              "confidence": 0.99
            },
            "Sex": {
              "type": "string",
              "valueString": "M",
              "text": "M",
              "boundingBox": [
                226,
                190,
                232,
                190,
                233,
                201,
                226,
                201
              ],
              "page": 1,
              "confidence": 0.99
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

Pour entraîner un modèle Form Recognizer à l’aide des documents de votre conteneur d’objets blob Azure, appelez l’API **[Train Custom Model](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/TrainCustomModelAsync)** (Entraîner un modèle personnalisé) en exécutant la commande cURL ci-dessous. Avant d’exécuter la commande, apportez les modifications suivantes :

1. Remplacez `{endpoint}` par le point de terminaison que vous avez obtenu avec votre abonnement Form Recognizer.
1. Remplacez `{subscription key}` par la clé d’abonnement que vous avez copiée à l’étape précédente.
1. Remplacez `{SAS URL}` par l’URL de signature d’accès partagé (SAS) du conteur de stockage Azure Blob. [!INCLUDE [get SAS URL](../../includes/sas-instructions.md)]

   :::image type="content" source="../../media/quickstarts/get-sas-url.png" alt-text="Récupération d’URL SAS":::

#### <a name="request"></a>Requête

```bash
curl -i -X POST "https://{endpoint}/formrecognizer/v2.1/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{SAS URL}'}"
```

#### <a name="location"></a>Emplacement

Vous recevrez une réponse `201 (Success)` avec un en-tête **Location**. La valeur de cet en-tête contient l’ID du modèle entraîné, qui vous permet d’interroger l’état de l’opération asynchrone et d’obtenir les résultats :

_https://{point-de-terminaison}/formrecognizer/v2.1/custom/models/ **{ID-modèle}**_

Dans l’exemple suivant, la chaîne qui suit `models/` dans l’URL correspond à l’ID du modèle.

```console
https://westus.api.cognitive.microsoft.com/formrecognizer/v2.1/custom/models/77d8ecad-b8c1-427e-ac20-a3fe4af503e9
```

### <a name="train-a-model-with-labels"></a>Entraîner un modèle avec des étiquettes

Pour effectuer un entraînement avec des étiquettes, vous devez disposer de fichiers d’informations spéciaux sur les étiquettes (`\<filename\>.pdf.labels.json`) dans votre conteneur de stockage d’objets blob, en même temps que les documents d’entraînement. L’[outil d’étiquetage des exemples Form Recognizer](../../label-tool.md) propose une interface utilisateur qui facilite la création de ces fichiers d’étiquettes. Une fois que vous les avez, vous pouvez appeler l’API **[Entraîner un modèle personnalisé](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/TrainCustomModelAsync)** , avec le paramètre `"useLabelFile"` défini sur `true` dans le corps JSON.

Avant d’exécuter la commande, apportez les modifications suivantes :

1. Remplacez `{endpoint}` par le point de terminaison que vous avez obtenu avec votre abonnement Form Recognizer.
1. Remplacez `{subscription key}` par la clé d’abonnement que vous avez copiée à l’étape précédente.
1. Remplacez `{SAS URL}` par l’URL de signature d’accès partagé (SAS) du conteur de stockage Azure Blob. [!INCLUDE [get SAS URL](../../includes/sas-instructions.md)]

   :::image type="content" source="../../media/quickstarts/get-sas-url.png" alt-text="Récupération d’URL SAS":::

#### <a name="request"></a>Requête

```bash
curl -i -X POST "https://{endpoint}/formrecognizer/v2.1/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{SAS URL}', 'useLabelFile':true}"
```

#### <a name="location"></a>Emplacement

Vous recevrez une réponse `201 (Success)` avec un en-tête **Location**. La valeur de cet en-tête contient l’ID du modèle entraîné, qui vous permet d’interroger l’état de l’opération asynchrone et d’obtenir les résultats :

_https://{point-de-terminaison}/formrecognizer/v2.1/custom/models/ **{ID-modèle}**_

Dans l’exemple suivant, la chaîne qui suit `models/` dans l’URL correspond à l’ID du modèle.

```console
https://westus.api.cognitive.microsoft.com/formrecognizer/v2.1/custom/models/4da0bf8e-5325-467c-93bb-9ff13d5f72a2
```

### <a name="get-training-results"></a>Obtenir les résultats de l’entraînement

Une fois que vous avez commencé l’opération d’apprentissage, utilisez **[Get Custom Model](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/GetCustomModel)** pour vérifier l’état d’apprentissage. Transmettez l’ID du modèle dans la demande d’API :

1. Remplacez `{endpoint}` par le point de terminaison que vous avez obtenu avec votre clé d’abonnement Form Recognizer.
1. Remplacez `{subscription key}` par votre clé d’abonnement.
1. Remplacez `{model ID}` par l’ID de modèle que vous avez reçu à l’étape précédente.

#### <a name="request"></a>Requête

```bash
curl -X GET "https://{endpoint}/formrecognizer/v2.1/custom/models/{modelId}" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

## <a name="analyze-forms-with-a-custom-model"></a>Analyser les formulaires avec un modèle personnalisé

À présent, vous allez utiliser le modèle nouvellement entraîné pour analyser un document et en extraire des tableaux et des champs. Appelez l’API **[Analyze Form](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeWithCustomForm)** (Analyser un formulaire) en exécutant la commande cURL suivante. Avant d’exécuter la commande, apportez les modifications suivantes :

1. Remplacez `{endpoint}` par le point de terminaison que vous avez obtenu avec votre clé d’abonnement Form Recognizer. Vous la trouverez sous l’onglet **Vue d’ensemble** de la ressource Form Recognizer.
1. Remplacez `{model ID}` par l’ID de modèle que vous avez reçu à la section précédente.
1. Remplacez `{SAS URL}` par une URL SAS vers votre fichier dans le stockage Azure. Suivez les étapes de la section Entraînement, mais au lieu d’obtenir une URL SAS pour tout le conteneur d’objets Blob, obtenez l’URL du fichier spécifique que vous souhaitez analyser.
1. Remplacez `{subscription key}` par votre clé d’abonnement.

#### <a name="request"></a>Requête

```bash
curl -v "https://{endpoint}/formrecognizer/v2.1/custom/models/{modelId}/analyze?includeTextDetails=true" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" -d "{ 'source': '{SAS URL}' } "
```

Vous recevrez une réponse `202 (Success)` avec un en-tête **Operation-Location**. La valeur de cet en-tête contient un ID de résultats qui vous permet de suivre les résultats de l’opération d’analyse :

_https://<span></span>cognitiveservice/formrecognizer/v2.1/custom/models/{ID-modèle}/analyzeResults/ **{ID-résultats}**_

Dans l’exemple suivant, la chaîne qui suit `analyzeResults/` dans l’URL correspond à l’ID des résultats.

```console
https://cognitiveservice/formrecognizer/v2/layout/analyzeResults/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

Enregistrez cet ID de résultats pour l’étape suivante.

### <a name="get-the-analyze-results"></a>Obtenir les résultats de l’analyse

Appelez l’API **[Get Analyze Form Result](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/GetAnalyzeFormResult)** (Obtenir le résultat de l’analyse du formulaire) pour interroger les résultats de l’opération d’analyse.

1. Remplacez `{endpoint}` par le point de terminaison que vous avez obtenu avec votre clé d’abonnement Form Recognizer. Vous la trouverez sous l’onglet **Vue d’ensemble** de la ressource Form Recognizer.
1. Remplacez `{result ID}` par l’ID que vous avez reçu à la section précédente.
1. Remplacez `{subscription key}` par votre clé d’abonnement.

#### <a name="request"></a>Requête

```bash
curl -X GET "https://{endpoint}/formrecognizer/v2.1/custom/models/{modelId}/analyzeResults/{resultId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

Vous recevrez une réponse `200 (Success)` avec un corps JSON sous la forme suivante. La sortie a été raccourcie à des fins de simplicité. Notez le champ `"status"` vers le bas. La valeur est `"succeeded"` quand l’opération d’analyse est terminée. Si l’opération d’analyse n’est pas terminée, vous devez réinterroger le service en réexécutant la commande. Nous vous recommandons d’attendre une seconde ou plus entre chaque appel.

Dans les modèles personnalisés entraînés sans étiquette, les tables et les associations de paires clé/valeur se trouvent dans le nœud `"pageResults"` de la sortie JSON. Dans les modèles personnalisés entraînés avec des étiquettes, les associations de paires clé/valeur se trouvent dans le nœud `"documentResults"`. Si vous avez également spécifié l’extraction de texte brut par le biais du paramètre d’URL *includeTextDetails*, le nœud `"readResults"` affiche le contenu et les positions de tout le texte dans le document.

Cet exemple de sortie JSON a été raccourci pour simplifier. Consultez l’[exemple de sortie complet sur GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/analyze-result-invoice-6.pdf.json).

#### <a name="response-body"></a>Response body

```JSON
{
  "status": "succeeded",
  "createdDateTime": "2020-08-21T01:13:28Z",
  "lastUpdatedDateTime": "2020-08-21T01:13:42Z",
  "analyzeResult": {
    "version": "2.1.0",
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
              5.0444,
              0.3613,
              8.0917,
              0.3613,
              8.0917,
              0.6718,
              5.0444,
              0.6718
            ],
            "words": [
              {
                "text": "Project",
                "boundingBox": [
                  5.0444,
                  0.3587,
                  6.2264,
                  0.3587,
                  6.2264,
                  0.708,
                  5.0444,
                  0.708
                ]
              },
              {
                "text": "Statement",
                "boundingBox": [
                  6.3361,
                  0.3635,
                  8.0917,
                  0.3635,
                  8.0917,
                  0.6396,
                  6.3361,
                  0.6396
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
                6.9833,
                1.0615,
                7.3333,
                1.0615,
                7.3333,
                1.1649,
                6.9833,
                1.1649
              ],
              "elements": [
                "#/readResults/0/lines/2/words/0"
              ]
            },
            "value": {
              "text": "9/10/2020",
              "boundingBox": [
                7.3833,
                1.0802,
                7.925,
                1.0802,
                7.925,
                1.174,
                7.3833,
                1.174
              ],
              "elements": [
                "#/readResults/0/lines/3/words/0"
              ]
            },
            "confidence": 1
          },
          ...
        ],
        "tables": [
          {
            "rows": 5,
            "columns": 5,
            "cells": [
              {
                "text": "Training Date",
                "rowIndex": 0,
                "columnIndex": 0,
                "boundingBox": [
                  0.6944,
                  4.2779,
                  1.5625,
                  4.2779,
                  1.5625,
                  4.4005,
                  0.6944,
                  4.4005
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

[!INCLUDE [improve results](../../includes/improve-results-unlabeled.md)]

## <a name="manage-custom-models"></a>Gérer des modèles personnalisés

### <a name="get-a-list-of-custom-models"></a>Obtenir une liste de modèles personnalisés

Utilisez l’API **[List Custom Models](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/GetCustomModels)** (Lister les modèles personnalisés) dans la commande suivante pour retourner la liste de tous les modèles personnalisés qui appartiennent à votre abonnement.

1. Remplacez `{endpoint}` par le point de terminaison que vous avez obtenu avec votre abonnement Form Recognizer.
1. Remplacez `{subscription key}` par la clé d’abonnement que vous avez copiée à l’étape précédente.

#### <a name="request"></a>Requête

```bash
curl -v -X GET "https://{endpoint}/formrecognizer/v2.1/custom/models?op=full"
-H "Ocp-Apim-Subscription-Key: {subscription key}"
```

#### <a name="response-body"></a>Response body

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

Pour récupérer des informations détaillées sur un modèle personnalisé spécifique, utilisez l’API **[Get Custom Model](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/GetCustomModel)** (Obtenir un modèle personnalisé) dans la commande suivante.

1. Remplacez `{endpoint}` par le point de terminaison que vous avez obtenu avec votre abonnement Form Recognizer.
1. Remplacez `{subscription key}` par la clé d’abonnement que vous avez copiée à l’étape précédente.
1. Remplacez `{modelId}` par l’ID du modèle personnalisé que vous souhaitez rechercher.

### <a name="request"></a>Requête

```bash
curl -v -X GET "https://{endpoint}/formrecognizer/v2.1/custom/models/{modelId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

#### <a name="request-body"></a>Corps de la demande

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

Vous pouvez aussi supprimer un modèle de votre compte en référençant son ID. Cette commande appelle l’API **[Delete Custom Model](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/DeleteCustomModel)** (Supprimer un modèle personnalisé) pour supprimer le modèle utilisé dans la section précédente.

1. Remplacez `{endpoint}` par le point de terminaison que vous avez obtenu avec votre abonnement Form Recognizer.
1. Remplacez `{subscription key}` par la clé d’abonnement que vous avez copiée à l’étape précédente.
1. Remplacez `{modelId}` par l’ID du modèle personnalisé que vous souhaitez rechercher.

### <a name="request"></a>Requête

```bash
curl -v -X DELETE "https://{endpoint}/formrecognizer/v2.1/custom/models/{modelId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

Vous recevrez une réponse de réussite `204`, indiquant que votre modèle est marqué pour suppression. Les artefacts de modèle seront supprimés dans les 48 heures.

## <a name="next-steps"></a>Étapes suivantes

Pour ce projet, vous avez utilisé l’API REST Form Recognizer pour analyser des formulaires de différentes manières. Explorez à présent la documentation de référence pour découvrir plus en détail l’API Form Recognizer.

> [!div class="nextstepaction"]
> [Documentation de référence sur l’API REST](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeWithCustomForm)
