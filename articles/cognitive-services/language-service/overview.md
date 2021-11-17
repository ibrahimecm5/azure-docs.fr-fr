---
title: Qu’est-ce qu’Azure Cognitive Service for Language ?
titleSuffix: Azure Cognitive Services
description: Découvrez comment intégrer l’IA dans vos applications pour extraire des informations et comprendre la langue écrite.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: ignite-fall-2021
ms.openlocfilehash: b0eb1b3a1248e4bccf8f726724b57cacb352b88e
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132485359"
---
# <a name="what-is-azure-cognitive-service-for-language"></a>Qu’est-ce qu’Azure Cognitive Service for Language ?

Azure Cognitive Service for Language est un service cloud qui fournit des fonctionnalités de traitement en langage naturel (NLP) pour comprendre et analyser du texte. Utilisez ce service pour créer plus facilement des applications intelligentes à l’aide de Language Studio sur le web, d’API REST et de bibliothèques de client.  

Ce service Language réunit Analyse de texte, QnA Maker et LUIS tout en fournissant plusieurs nouvelles fonctionnalités. Ces fonctionnalités peuvent être :
* Préconfigurées, ce qui signifie que les modèles IA qu’utilise la fonctionnalité ne sont pas personnalisables. Vous envoyez juste vos données et utilisez la sortie de la fonctionnalité dans vos applications.
* Personnalisables, ce qui signifie que vous allez entraîner un modèle IA à l’aide de nos outils pour adapter vos données spécifiquement.

## <a name="migrate-from-text-analytics-qna-maker-or-language-understanding"></a>Migrer à partir d’Analyse de texte, de QnA Maker ou de Language Understanding ?

Azure Cognitive Services for Language unifie trois services linguistiques individuels de Cognitive Services : Analyse de texte, QnA Maker et Language Understanding (LUIS). Si vous utilisiez ces trois services, vous pouvez facilement migrer vers la nouvelle solution Azure Cognitive Services for Language. Pour obtenir des instructions, consultez [Migration vers Azure Cognitive Services for Language](concepts/migrate.md).  

## <a name="available-features"></a>Fonctionnalités disponibles

Azure Cognitive Service for Language offre les fonctionnalités suivantes :

> [!div class="mx-tdCol2BreakAll"]
> |Fonctionnalité  |Description  | Options de déploiement| 
> |---------|---------|---------|
> | [Reconnaissance d’entité nommée (NER)](named-entity-recognition/overview.md)     | Cette fonctionnalité préconfigurée identifie les entités dans le texte en fonction de plusieurs catégories prédéfinies.        | • [Language Studio](language-studio.md) <br> • [API REST et bibliothèque de client](named-entity-recognition/quickstart.md) |
> | [Détection d’informations d’identification personnelle](personally-identifiable-information/overview.md)     | Cette fonctionnalité préconfigurée identifie les entités dans le texte en fonction de plusieurs catégories prédéfinies d’informations sensibles, telles que les informations de compte.        | • [Language Studio](language-studio.md) <br> • [API REST et bibliothèque de client](named-entity-recognition/quickstart.md) |
> | [Extraction de phrases clés](key-phrase-extraction/overview.md)     | Cette fonctionnalité préconfigurée évalue le texte non structuré et, pour chaque document de saisie, retourne une liste de phrases clés et de points clés du texte. | • [Language Studio](language-studio.md) <br> • [API REST et bibliothèque de client](key-phrase-extraction/quickstart.md) <br> • [Conteneur Docker](key-phrase-extraction/how-to/use-containers.md)  |
> |[Liaison d’entités](entity-linking/overview.md)    | Cette fonctionnalité préconfigurée désambiguïse l’identité d’une entité trouvée dans le texte et fournit des liens vers l’entité sur Wikipédia.        | • [Language Studio](language-studio.md) <br> • [API REST et bibliothèque de client](entity-linking/quickstart.md) |
> | [Analyse de texte pour la santé](text-analytics-for-health/overview.md)    | Cette fonctionnalité préconfigurée extrait des informations de textes médicaux non structurés, tels que les notes cliniques et les notes de médecins.  | • [Language Studio](language-studio.md) <br> • [API REST et bibliothèque de client](text-analytics-for-health/quickstart.md) <br> • [Conteneur Docker](text-analytics-for-health/how-to/use-containers.md) |
> | [NER personnalisée](custom-named-entity-recognition/overview.md)    | Créez un modèle IA pour extraire des catégories d’entité personnalisées en utilisant du texte non structuré que vous fournissez. |  • [Language Studio](custom-named-entity-recognition/quickstart.md?pivots=language-studio) <br> • [API REST](custom-named-entity-recognition/quickstart.md?pivots=rest-api) |
> | [Analyser les sentiments et les opinions](sentiment-opinion-mining/overview.md)     | Cette fonctionnalité préconfigurée fournit des étiquettes de sentiment (comme « *négatif* », « *neutre* » et « *positif* ») pour les phrases et les documents. Cette fonctionnalité peut également fournir des informations précises sur les opinions associées à des mots qui apparaissent dans le texte, tels que les attributs de produits ou de services. |  • [Language Studio](language-studio.md) <br> • [API REST et bibliothèque de client](sentiment-opinion-mining/quickstart.md) <br> • [Conteneur Docker](sentiment-opinion-mining/how-to/use-containers.md)
> |[Détection de la langue](language-detection/overview.md)    | Cette fonctionnalité préconfigurée évalue le texte et détermine la langue dans laquelle il a été écrit. Elle retourne un identificateur de langue et un score qui indique la force de l’analyse.        | • [Language Studio](language-studio.md) <br> • [API REST et bibliothèque de client](language-detection/quickstart.md) <br> • [Conteneur Docker](language-detection/how-to/use-containers.md) |
> |[Classification de texte personnalisée (préversion)](custom-classification/overview.md)    | Créez un modèle IA pour classifier du texte non structuré dans des classes personnalisées que vous définissez.         | • [Language Studio](custom-classification/quickstart.md?pivots=language-studio)<br> • [API REST](language-detection/quickstart.md?pivots=rest-api) |
> | [Synthèse de texte (préversion)](text-summarization/overview.md)     | Cette fonctionnalité préconfigurée extrait les phrases clés qui, ensemble, véhiculent l’essence d’un document. | • [Language Studio](language-studio.md) <br> • [API REST et bibliothèque de client](text-summarization/quickstart.md) |
> | [Compréhension du langage courant (préversion)](conversational-language-understanding/overview.md)   | Créez un modèle IA pour pouvoir comprendre le langage naturel utilisé dans les applications, les bots et les appareils IoT. | • [Language Studio](conversational-language-understanding/quickstart.md)
> | [Réponses aux questions](question-answering/overview.md)     | Cette fonctionnalité préconfigurée fournit des réponses à des questions extraites de texte entré, à l’aide de contenu semi-structuré comme les FAQ, les manuels et les documents. | • [Language Studio](language-studio.md) <br> • [API REST et bibliothèque de client](question-answering/quickstart/sdk.md) |

## <a name="tutorials"></a>Tutoriels

Une fois que vous avez suivi le guide de démarrage rapide du service Language, essayez nos tutoriels qui vous montrent comment résoudre différents scénarios.

* [Extraire les phrases clés de texte stocké dans Power BI](key-phrase-extraction/tutorials/integrate-power-bi.md)
* [Utiliser Power Automate pour trier les informations dans Microsoft Excel](named-entity-recognition/tutorials/extract-excel-information.md) 
* [Utiliser Flask pour la traduction de texte, l’analyse de sentiments et la synthèse vocale](/azure/cognitive-services/translator/tutorial-build-flask-app-translation-synthesis?context=/azure/cognitive-services/language-service/context/context)
* [Utiliser Cognitive Services dans des applications de canevas](/powerapps/maker/canvas-apps/cognitive-services-api?context=/azure/cognitive-services/language-service/context/context)
* [Créer un bot FAQ](question-answering/tutorials/bot-service.md)

## <a name="additional-code-samples"></a>Exemples de code supplémentaires

Vous trouverez d’autres exemples de code sur GitHub pour les langages suivants :

* [C#](https://github.com/Azure/azure-sdk-for-net/tree/main/sdk/textanalytics/Azure.AI.TextAnalytics/samples)
* [Java](https://github.com/Azure/azure-sdk-for-java/tree/main/sdk/textanalytics/azure-ai-textanalytics/src/samples)
* [JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/textanalytics/ai-text-analytics/samples)
* [Python](https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/textanalytics/azure-ai-textanalytics/samples)

## <a name="deploy-on-premises-using-docker-containers"></a>Déployer localement en utilisant des conteneurs Docker 
Utilisez les conteneurs du service Language pour déployer localement des fonctionnalités d’API. Ces conteneurs Docker vous permettent de rapprocher le service plus près de vos données pour des raisons de conformité, de sécurité ou opérationnelles. Le service Language offre les conteneurs suivants :

* [Analyse des sentiments](sentiment-opinion-mining/how-to/use-containers.md)
* [Détection de la langue](language-detection/how-to/use-containers.md)
* [Extraction de phrases clés](key-phrase-extraction/how-to/use-containers.md) 
* [Analyse de texte pour la santé](text-analytics-for-health/how-to/use-containers.md)


## <a name="responsible-ai"></a>Intelligence artificielle responsable 

Un système IA comprend non seulement la technologie, mais aussi les personnes qui l’utiliseront, les personnes qui seront affectées par lui et l’environnement dans lequel il est déployé. Lisez les articles suivants pour en savoir plus sur l’utilisation et le déploiement de l’IA responsable dans vos systèmes :

* [Note de transparence pour le service Language](/legal/cognitive-services/text-analytics/transparency-note)
* [Intégration et utilisation responsable](/legal/cognitive-services/text-analytics/guidance-integration-responsible-use)
* [Données, confidentialité et sécurité](/legal/cognitive-services/text-analytics/data-privacy)
