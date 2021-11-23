---
title: Cognitive Services dans Azure Synapse Analytics
description: Enrichissez vos données avec l’intelligence artificielle (IA) dans Azure Synapse Analytics à l’aide de modèles préformés à partir d’Azure Cognitive Services.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: overview
ms.reviewer: jrasnick, garye, negust, ruxu
ms.date: 06/30/2021
author: garyericson
ms.author: garye
ms.custom: ignite-fall-2021
ms.openlocfilehash: e69730f486303fa508fed4cd53f8df3ee76433ba
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132318462"
---
# <a name="cognitive-services-in-azure-synapse-analytics"></a>Cognitive Services dans Azure Synapse Analytics

À l’aide des modèles préformés d’Azure Cognitive Services, vous pouvez enrichir vos données avec l’intelligence artificielle (IA) dans Azure Synapse Analytics.

[Azure Cognitive Services](../../cognitive-services/what-are-cognitive-services.md) regroupent des services cloud qui ajoutent des informations cognitives à vos données, même si vous n’avez pas de compétences en matière de science des données ou d’intelligence artificielle. Vous pouvez utiliser ces services avec vos données dans Synapse Analytics de plusieurs façons :

- L’Assistant Cognitive Services de Synapse Analytics génère du code PySpark dans un notebook Synapse qui se connecte à un cognitive service à l’aide des données d’une table Spark. Ensuite, à l’aide de modèles de Machine Learning préformés, le service vous permet d’ajouter l’intelligence artificielle à vos données. Pour plus d’informations, consultez l’[Assistant d’analyse des sentiments](tutorial-cognitive-services-sentiment.md) et l’[Assistant de détection d’anomalie](tutorial-cognitive-services-anomaly.md).

- Synapse Machine Learning ([SynapseML](https://github.com/microsoft/SynapseML)) vous permet de créer des modèles analytiques et prédictifs puissants et hautement scalables à partir de diverses sources de données Spark. Synapse Spark fournit des bibliothèques SynapseML intégrées, notamment [Cognitive Services sur Spark](https://github.com/microsoft/SynapseML/blob/master/docs/cogsvc.md). Pour plus d’informations, consultez les [didacticiels](#tutorials).

- À partir du code PySpark généré par l’Assistant, ou de l’exemple de code SynapseML fourni dans le tutoriel, vous pouvez écrire votre propre code pour utiliser d’autres services cognitifs avec vos données. Pour plus d’informations sur les services disponibles, consultez la [Présentation d’Azure Cognitive Services](../../cognitive-services/what-are-cognitive-services.md).




## <a name="get-started"></a>Bien démarrer

Le tutoriel [Prérequis de l’utilisation de Cognitive Services dans Azure Synapse](tutorial-configure-cognitive-services-synapse.md) vous guide tout au long de la procédure à suivre avant d’utiliser Cognitive Services dans Synapse Analytics.

## <a name="tutorials"></a>Tutoriels

Les tutoriels suivants fournissent des exemples complets d’utilisation de Cognitive Services dans Synapse Analytics.

- [Analyse des sentiments avec Cognitive Services](tutorial-cognitive-services-sentiment.md) : à l’aide d’un exemple de jeu de données de commentaires de clients, vous générez une table Spark dotée d’une colonne indique le sentiment des commentaires dans chaque ligne.

- [Détection des anomalies avec Cognitive Services](tutorial-cognitive-services-anomaly.md) : à l’aide d’un exemple de jeu de données de série chronologique, vous générez une table Spark dotée d’une colonne indiquant si les données de chaque ligne constituent une anomalie.

- [Créer des applications Machine Learning avec Microsoft Machine Learning pour Apache Spark](tutorial-build-applications-use-mmlspark.md) : Ce tutoriel montre comment utiliser SynapseML pour accéder à plusieurs modèles à partir de Cognitive Services.

- Le module [Form Recognizer avec service IA appliqué](tutorial-form-recognizer-use-mmlspark.md) montre comment utiliser [Form Recognizer](../../applied-ai-services/form-recognizer/index.yml) pour analyser vos formulaires et vos documents, extrait du texte et des données sur Azure Synapse Analytics. 

- Le module [Analyse de texte avec Cognitive Services](tutorial-text-analytics-use-mmlspark.md) montre comment utiliser l’[Analyse de texte](../../cognitive-services/text-analytics/index.yml) pour analyser du texte non structuré sur Azure Synapse Analytics.

- Le module [Traducteur avec Cognitive Services](tutorial-translator-use-mmlspark.md) montre comment utiliser [Traducteur](../../cognitive-services/Translator/index.yml) pour créer des solutions intelligentes multilingues sur Azure Synapse Analytics

- Le module [Vision par ordinateur avec Cognitive Services](tutorial-computer-vision-use-mmlspark.md) montre comment utiliser la [Vision par ordinateur](../../cognitive-services/computer-vision/index.yml) pour analyser des images sur Azure Synapse Analytics.

## <a name="available-cognitive-services-apis"></a>API Cognitive Services disponibles
### <a name="bing-image-search"></a>Recherche d’images Bing

| Type d’API                                   | API SynapseML                  | API Cognitive Service (versions)                                                                                               | Prise en charge du réseau virtuel DEP |
| ------------------------------------------ | ------------------------------ | ------------------------------------------------------------------------------------------------------------------------------- | ---------------- |
|Recherche d’images Bing|BingImageSearch|Images : Recherche visuelle v7.0| Non pris en charge|

### <a name="anomaly-detector"></a>Détecteur d’anomalies

| Type d’API                                   | API SynapseML                  | API Cognitive Service (versions)                                                                                               | Prise en charge du réseau virtuel DEP |
| ------------------------------------------ | ------------------------------ | ------------------------------------------------------------------------------------------------------------------------------- | ---------------- |
| Détecter la dernière anomalie                        | DetectLastAnomaly              | Détecter le dernier point v1.0                                                                                                          | Prise en charge        |
| Détecter les anomalies                           | DetectAnomalies                | Détecter la série entière v1.0                                                                                                       | Prise en charge        |
| DetectAnomalies simple                     | SimpleDetectAnomalies          | Détecter la série entière v1.0                                                                                                       | Prise en charge        |

### <a name="computer-vision"></a>Vision par ordinateur

| Type d’API                                   | API SynapseML                  | API Cognitive Service (versions)                                                                                               | Prise en charge du réseau virtuel DEP |
| ------------------------------------------ | ------------------------------ | ------------------------------------------------------------------------------------------------------------------------------- | ---------------- |
| OCR                                        | OCR                            | Reconnaître le texte imprimé v2.0                                                                                                     | Prise en charge        |
| Reconnaître le texte                             | RecognizeText                  | Reconnaître le texte v2.0                                                                                                             | Prise en charge        |
| Lire l’image                                 | ReadImage                      | Lire v3.1                                                                                                                       | Prise en charge        |
| Générer des miniatures                        | GenerateThumbnails             | Générer une miniature v2.0                                                                                                         | Prise en charge        |
| Analyser l’image                              | AnalyzeImage                   | Analyse Image v2.0                                                                                                              | Prise en charge        |
| Reconnaître le contenu spécifique à un domaine          | RecognizeDomainSpecificContent | Analyser une image par domaine v2.0                                                                                                    | Prise en charge        |
| Image de balise                                  | TagImage                       | Image de balise v2.0                                                                                                                  | Prise en charge        |
| Décrire l’image                             | DescribeImage                  | Description d’image v2.0                                                                                                             | Prise en charge        |


### <a name="translator"></a>Traducteur

| Type d’API                                   | API SynapseML                  | API Cognitive Service (versions)                                                                                               | Prise en charge du réseau virtuel DEP |
| ------------------------------------------ | ------------------------------ | ------------------------------------------------------------------------------------------------------------------------------- | ---------------- |
| Traduire du texte                             | Translate                      | Traduire v3.0                                                                                                                  | Non pris en charge    |
| Translittérer du texte                         | Transliterate                  | Translittérer v3.0                                                                                                              | Non pris en charge    |
| Détecter la langue                            | Detect                         | Détection v3.0                                                                                                                     | Non pris en charge    |
| Arrêter la phrase                             | BreakSentence                  | Arrêter la phrase v3.0                                                                                                             | Non pris en charge    |
| Recherche dans le dictionnaire (autres traductions) | DictionaryLookup               | Recherche dans le dictionnaire v3.0                                                                                                          | Non pris en charge    |
| Traduction de documents                       | DocumentTranslator             | Traduction de documentation v1.0                                                                                                       | Non pris en charge    |


### <a name="face"></a>Visage

| Type d’API                                   | API SynapseML                  | API Cognitive Service (versions)                                                                                               | Prise en charge du réseau virtuel DEP |
| ------------------------------------------ | ------------------------------ | ------------------------------------------------------------------------------------------------------------------------------- | ---------------- |
| Détecter le visage                                | DetectFace                     | Détecter avec l’URL v1.0                                                                                                            | Prise en charge        |
| Rechercher des visages semblables                          | FindSimilarFace                | Rechercher des semblables v1.0                                                                                                               | Prise en charge        |
| Regrouper les visages                                | GroupFaces                     | Groupe v1.0                                                                                                                      | Prise en charge        |
| Identifier des visages                             | IdentifyFaces                  | Identifier v1.0                                                                                                                   | Prise en charge        |
| Vérifier les visages                               | VerifyFaces                    | Vérifier visage à visage v1.0                                                                                                        | Prise en charge        |

### <a name="form-recognizer"></a>Form Recognizer
| Type d’API                                   | API SynapseML                  | API Cognitive Service (versions)                                                                                               | Prise en charge du réseau virtuel DEP |
| ------------------------------------------ | ------------------------------ | ------------------------------------------------------------------------------------------------------------------------------- | ---------------- |
| Analyser la disposition                             | AnalyzeLayout                  | Analyser la disposition asynchrone v2.1                                                                                                       | Prise en charge        |
| Analyser les reçus                           | AnalyzeReceipts                | Analyser le reçu asynchrone v2.1                                                                                                      | Prise en charge        |
| Analyser les cartes de visite                     | AnalyzeBusinessCards           | Analyser la carte de visite asynchrone v2.1                                                                                                | Prise en charge        |
| Analyser les factures                           | AnalyzeInvoices                | Analyser la facture asynchrone v2.1                                                                                                      | Prise en charge        |
| Analyser les documents d’identité                       | AnalyzeIDDocuments             | identification (ID) du modèle de document v2.1                                                                                         | Prise en charge        |
| Liste de modèles personnalisés                         | ListCustomModels               | Liste de modèles personnalisés v2.1                                                                                                         | Prise en charge        |
| Obtenir un modèle personnalisé                           | GetCustomModel                 | Obtenir des modèles personnalisés v2.1                                                                                                          | Prise en charge        |
| Analyser un modèle personnalisé                       | AnalyzeCustomModel             | Analyser avec un modèle personnalisé v2.1                                                                                                  | Prise en charge        |

### <a name="speech-to-text"></a>Reconnaissance vocale
| Type d’API                                   | API SynapseML                  | API Cognitive Service (versions)                                                                                               | Prise en charge du réseau virtuel DEP |
| ------------------------------------------ | ------------------------------ | ------------------------------------------------------------------------------------------------------------------------------- | ---------------- |
| Reconnaissance vocale                             | SpeechToText                   | SpeechToText v1.0 |  Non pris en charge    |
| SDK Reconnaissance vocale                         | SpeechToTextSDK                | Utilisation du SDK Speech version 1.14.0                                                                                                 | Non pris en charge    |


### <a name="text-analytics"></a>Analyse de texte

| Type d’API                                   | API SynapseML                  | API Cognitive Service (versions)                                                                                               | Prise en charge du réseau virtuel DEP |
| ------------------------------------------ | ------------------------------ | ------------------------------------------------------------------------------------------------------------------------------- | ---------------- |
| Sentiment du texte v2                          | TextSentimentV2                | Sentiment v2.0                                                                                                                  | Prise en charge        |
| Détecteur de langage v2                       | LanguageDetectorV2             | Languages v2.0                                                                                                                  | Prise en charge        |
| Détecteur d’entité v2                         | EntityDetectorV2               | Lien d’entités v2.0                                                                                                           | Prise en charge        |
| NER v2                                     | NERV2                          | Reconnaissance générale d’entités v2.0                                                                                               | Prise en charge        |
| Extracteur de phrases clés v2                    | KeyPhraseExtractorV2           | Phrases clés v2.0                                                                                                                | Prise en charge        |
| Sentiment du texte                             | TextSentiment                  | Sentiment v3.1                                                                                                                  | Prise en charge        |
| Extracteur de phrases clés                       | KeyPhraseExtractor             | Phrases clés v3.1                                                                                                                | Prise en charge        |
| PII                                        | PII                            | Reconnaissance PII d’entités v3.1                                                                                                   | Prise en charge        |
| NER                                        | NER                            | Reconnaissance générale d’entités v3.1                                                                                               | Prise en charge        |
| Détecteur de langage                          | LanguageDetector               | Langages v3.1                                                                                                                  | Prise en charge        |
| Détecteur d’entité                            | EntityDetector                 | Lien d’entités v3.1                                                                                                           | Prise en charge        |


## <a name="next-steps"></a>Étapes suivantes

- [Fonctionnalités de Machine Learning dans Azure Synapse Analytics](what-is-machine-learning.md)
- [Présentation de Cognitive Services](../../cognitive-services/what-are-cognitive-services.md)
- [Utiliser un notebook échantillon de la galerie Synapse Analytics](quickstart-gallery-sample-notebook.md)
