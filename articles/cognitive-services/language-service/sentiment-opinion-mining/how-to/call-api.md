---
title: Guide pratique pour utiliser l’analyse des sentiments et l’exploration des opinions
titleSuffix: Azure Cognitive Services
description: Cet article vous montre comment détecter les sentiments et explorer les opinions dans du texte.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: sample
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-sentiment-opinion-mining, ignite-fall-2021
ms.openlocfilehash: 32e67ba4c8462b48158a72273e297924867b73b2
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131434272"
---
# <a name="how-to-use-sentiment-analysis-and-opinion-mining"></a>Guide pratique pour utiliser l’analyse des sentiments et l’exploration des opinions 

L’analyse des sentiments et l’exploration des opinions sont deux moyens de détecter des sentiments positifs et négatifs. Avec l’analyse des sentiments, vous pouvez obtenir des étiquettes de sentiment (telles que « négatif », « neutre » et « positif ») et des scores de confiance au niveau de la phrase et du document. L’exploration des opinions fournit des informations précises sur les opinions associées à des mots (tels que les attributs de produits ou de services) dans le texte.

> [!TIP]
> Si vous souhaitez commencer à utiliser cette fonctionnalité, vous pouvez suivre l’[article de démarrage rapide](../quickstart.md). Vous pouvez également créer des exemples de demande à l’aide de [Language Studio](../../language-studio.md) sans avoir à écrire de code.


## <a name="sentiment-analysis"></a>Analyse des sentiments

L’analyse des sentiments applique des étiquettes de sentiment au texte, qui sont retournées au niveau de la phrase et du document avec un score de confiance pour chacune d’elles. 

Les étiquettes sont *positive* (positif), *negative* (négatif) et *neutral* (neutre). Au niveau du document, l’étiquette de sentiment *mixed* (mixte) peut aussi être retournée. Le sentiment du document est déterminé comme suit :

| Sentiment des phrases                                                                            | Étiquette de document retournée |
|-----------------------------------------------------------------------------------------------|-------------------------|
| Le document contient au moins une phrase `positive`. Le reste des phrases est de type `neutral`. | `positive`              |
| Le document contient au moins une phrase `negative`. Le reste des phrases est de type `neutral`. | `negative`              |
| Le document contient au moins une phrase `negative` et au moins une phrase `positive`.    | `mixed`                 |
| Toutes les phrases du document sont de type `neutral`.                                                  | `neutral`               |

Les scores de confiance sont compris entre 1 et 0. Plus les scores sont proches de 1, plus le niveau de confiance dans la classification de l’étiquette est élevé ; inversement, plus les scores sont faibles, plus le niveau de confiance est bas. Pour chaque document ou chaque phrase, les scores prédits associés aux étiquettes (positif, négatif et neutre) donnent la somme de 1. Pour plus d’informations, consultez la [note de transparence sur l’IA responsable](/legal/cognitive-services/text-analytics/transparency-note?context=/azure/cognitive-services/text-analytics/context/context). 

## <a name="opinion-mining"></a>Exploration des opinions

L’exploration des opinions est une fonctionnalité de l’analyse des sentiments. Également connu sous le nom d’Analyse des sentiments basée sur l’aspect dans le registre du traitement en langage naturel, cette fonctionnalité fournit des informations plus précises sur les opinions liées aux attributs de produits ou de services dans le texte. L’API fait apparaître les opinions en tant que cible (substantif ou verbe) ainsi qu’une évaluation (adjectif).

Par exemple, si un client laisse un commentaire sur un hôtel, comme « la chambre était géniale, mais le personnel peu sympathique », Exploration des opinions va repérer des cibles (aspects) dans le texte ainsi que les évaluations (opinions) et les sentiments associés. Analyse des sentiments peut signaler seulement un sentiment négatif.

:::image type="content" source="../media/opinion-mining.png" alt-text="Diagramme de l’exemple d’Exploration des opinions" lightbox="../media/opinion-mining.png":::

Si vous utilisez l’API REST, pour avoir l’exploration des opinions dans vos résultats, vous devez inclure l’indicateur `opinionMining=true` dans une demande d’analyse de sentiments. Les résultats de l’Exploration des opinions sont inclus dans la réponse de l’Analyse des sentiments. L’exploration des opinions est une extension de l’Analyse des sentiments qui est incluse dans votre [niveau tarifaire](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/) actuel.

## <a name="determine-how-to-process-the-data-optional"></a>Déterminer le mode de traitement des données (facultatif)

### <a name="specify-the-sentiment-analysis-model"></a>Spécifier le modèle d’analyse des sentiments

Par défaut, l’analyse des sentiments utilise le dernier modèle IA disponible sur votre texte. Vous pouvez également configurer vos demandes d’API pour utiliser une version de modèle spécifique. Le modèle que vous spécifiez sera utilisé pour effectuer des opérations d’analyse des sentiments.

| Versions prises en charge | dernière version Généralement disponible | dernière préversion |
|--|--|--|
| `2019-10-01`, `2020-04-01`, `2021-10-01-preview` | `2020-04-01`   | `2021-10-01-preview`   |

### <a name="using-a-preview-model-version"></a>Utilisation d’une préversion du modèle

Pour utiliser une préversion de modèle dans vos appels d’API, vous devez spécifier la version du modèle à l’aide du paramètre de version de modèle. Par exemple, si vous envoyez une requête en utilisant Python :

```python
result = text_analytics_client.analyze_sentiment(documents, show_opinion_mining=True, model_version="2021-10-01-preview")
```

ou si vous utilisiez l’API REST :

```rest
https://your-resource-name.cognitiveservices.azure.com/text/analytics/v3.1/sentiment?opinionMining=true&model-version=2021-10-01-preview
```

Pour plus d’informations, consultez la documentation de référence.
* [REST API](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1/operations/Sentiment)
* [.NET](https://docs.microsoft.com/dotnet/api/azure.ai.textanalytics.analyzesentimentaction?view=azure-dotnet#properties)
* [Python](https://docs.microsoft.com/python/api/azure-ai-textanalytics/azure.ai.textanalytics.textanalyticsclient?view=azure-python#analyze-sentiment-documents----kwargs-)
* [Java](https://docs.microsoft.com/java/api/com.azure.ai.textanalytics.models.analyzesentimentoptions.setmodelversion?view=azure-java-stable#com_azure_ai_textanalytics_models_AnalyzeSentimentOptions_setModelVersion_java_lang_String_)
* [JavaScript](https://docs.microsoft.com/javascript/api/@azure/ai-text-analytics/analyzesentimentoptions?view=azure-node-latest)

### <a name="input-languages"></a>Langues de saisie

Lorsque vous envoyez des documents à traiter par l’analyse des sentiments, vous pouvez spécifier les [langues prises en charge](../language-support.md) dans lesquelles ils sont écrits. Si vous ne spécifiez pas de langue, l’analyse des sentiments prend Anglais par défaut. L’API peut retourner des décalages dans la réponse pour prendre en charge différents [encodages multilingues et d’émoji](../../concepts/multilingual-emoji-support.md). 

## <a name="submitting-data"></a>Envoi de données

La qualité des résultats de l’analyse des sentiments et de l’exploration des opinions est meilleure quand vous leur donnez de plus petites quantités de texte à traiter. C’est l’inverse pour certaines fonctionnalités comme l’extraction de phrases clés qui sont plus performantes sur de plus grands blocs de texte. 

Pour envoyer une demande d’API, vous aurez besoin du point de terminaison et de la clé de votre ressource Language.

> [!NOTE]
> Vous trouverez la clé et le point de terminaison de votre ressource Language dans le portail Azure. Ils sont dans la page **Clé et point de terminaison** de la ressource, sous **Gestion des ressources**. 

L’analyse est effectuée à la réception de la demande. Pour connaître la taille et le nombre de demandes que vous pouvez envoyer par minute et par seconde, consultez la section sur les limites des données ci-dessous.

L’utilisation synchrone des fonctionnalités d’analyse des sentiments et d’exploration des opinions est sans état. Aucune donnée n’est stockée dans votre compte, et les résultats sont retournés immédiatement dans la réponse.

Lors de l’utilisation asynchrone de ces fonctionnalités, les résultats de l’API sont disponibles pendant 24 heures après l’ingestion de la demande et sont indiqués dans la réponse. Après cette période, les résultats sont purgés et ne sont plus disponibles pour récupération.

## <a name="getting-sentiment-analysis-and-opinion-mining-results"></a>Obtention des résultats de l’analyse des sentiments et de l’exploration des opinions

Lorsque vous recevez des résultats de l’API, l’ordre des phrases clés retournées est déterminé en interne par le modèle. Vous pouvez streamer les résultats vers une application ou enregistrer la sortie dans un fichier sur le système local.

L’analyse des sentiments retourne une étiquette de sentiment et un score de confiance pour l’ensemble du document et chaque phrase qu’il contient. Plus les scores sont proches de 1, plus le niveau de confiance dans la classification de l’étiquette est élevé ; inversement, plus les scores sont faibles, plus le niveau de confiance est bas. Un document peut contenir plusieurs phrases, et les scores de confiance dans chaque document ou phrase s’additionnent pour arriver à 1.

L’Exploration des opinions localisera les cibles (substantifs ou verbes) dans le texte et leur évaluation associée (adjectif). Par exemple, la phrase *The restaurant had great food and our server was friendly* (Les plats du restaurant étaient excellents et le serveur sympa) présente deux cibles : *food* (plats) et *server* (serveur). Chaque cible a une évaluation. Par exemple, l’évaluation pour *food* (plat) serait *great* (excellent), tandis que l’évaluation pour *server* (serveur) serait *friendly* (sympa).

L’API retourne des avis en tant que cible (substantif ou verbe) ainsi qu’une évaluation (adjectif).


## <a name="data-limits"></a>Limites de données

> [!NOTE]
> * Si vous avez besoin d’analyser des documents plus volumineux que la limite autorisée, vous pouvez scinder le texte en blocs de texte plus petits avant de les envoyer à l’API. 
> * Un document est une chaîne unique de caractères de texte.  

| Limite | Valeur |
|------------------------|---------------|
| Taille maximale d’un document (synchrone) | 5 120 caractères mesurés par [StringInfo.LengthInTextElements](/dotnet/api/system.globalization.stringinfo.lengthintextelements).  |
| Nombre maximal de caractères par demande (asynchrone) | 125 000 caractères pour tous les documents envoyés, tels que mesurés par [StringInfo.LengthInTextElements](/dotnet/api/system.globalization.stringinfo.lengthintextelements).  |
| Taille maximale d’une demande | 1 Mo. |
| Nombre maximal de documents par demande | 10 |

Si un document dépasse la limite de caractères, l’API se comporte différemment suivant la fonctionnalité que vous utilisez :

* Asynchrones :
  * L’API rejette l’intégralité de la demande et retourne une erreur `400 bad request` si un document qu’elle contient dépasse la taille maximale.
* Synchrone :  
  * L’API ne traite pas un document qui dépasse la taille maximale et retourne une erreur de document non valide. Si une demande d’API a plusieurs documents, l’API continue de les traiter s’ils sont dans la limite de caractères.

Si vous dépassez le nombre maximal de documents que vous pouvez envoyer dans une seule demande, un code d’erreur HTTP 400 est généré.

### <a name="rate-limits"></a>Limites du taux de transfert

Votre limite de débit varie en fonction de votre [niveau tarifaire](https://aka.ms/unifiedLanguagePricing).

| Niveau          | Demandes par seconde | Requêtes par minute |
|---------------|---------------------|---------------------|
| S / Multiservice | 1 000                | 1 000                |
| S0 / F0         | 100                 | 300                 |

## <a name="see-also"></a>Voir aussi

* [Vue d’ensemble de l’analyse des sentiments et de l’exploration des opinions](../overview.md)
