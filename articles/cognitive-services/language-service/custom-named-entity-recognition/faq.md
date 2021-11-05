---
title: FAQ sur la reconnaissance d’entité nommée (NER) personnalisée
titleSuffix: Azure Cognitive Services
description: En savoir plus sur les questions fréquemment posées lors de l’utilisation de la reconnaissance d’entité nommée personnalisée.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-ner, ignite-fall-2021
ms.openlocfilehash: c413e03e9106a3063e9cfdf26a1ab4b7fd7d5f25
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131097926"
---
# <a name="frequently-asked-questions-for-custom-named-entity-recognition"></a>FAQ sur la reconnaissance d’entités nommées personnalisées

Trouvez des réponses aux questions les plus fréquentes sur les concepts et les scénarios liés à la classification de NER dans Azure Cognitive Service for Language.

## <a name="how-many-tagged-files-are-needed"></a>Combien de fichiers étiquetés sont nécessaires ?

En général, des [données étiquetées](how-to/tag-data.md) diverses et représentatives permettent d’obtenir de meilleurs résultats, à condition que l’étiquetage soit effectué de manière précise, cohérente et complète. Il n’existe aucun nombre défini d’entités étiquetées qui permettront à tous les modèles d’offrir de bonnes performances. Les performances dépendent fortement de vos entités et de son ambiguïté. Les types d’entités ambigus nécessitent davantage de balises. Les performances dépendent également de la qualité de votre étiquetage. Le nombre recommandé d’instances étiquetées par entité est 200. 

## <a name="what-are-the-service-limits"></a>Quelles sont les limites du service ?

Pour plus d’informations, consultez l’article sur les [limites du service](service-limits.md).

## <a name="what-to-do-if-my-model-scores-poorly"></a>Que faire si les scores de mon modèle sont faibles ?

L’évaluation du modèle peut ne pas toujours être complète, surtout si une classe spécifique est manquante ou sous-représentée dans votre jeu de test. Ajoutez des données étiquetées supplémentaires à votre modèle pour améliorer les performances et avoir un jeu de test plus représentatif.

## <a name="how-do-i-improve-model-performance"></a>Comment faire pour améliorer les performances du modèle ?

Affichez la [matrice de confusion](how-to/view-model-evaluation.md) pour identifier l’ambiguïté de schéma. Ensuite, [examinez votre jeu de test](how-to/improve-model.md) pour voir les entités prédites et étiquetées côte à côte afin d’obtenir une meilleure idée des performances de votre modèle et de déterminer si des modifications sont nécessaires dans le schéma ou les étiquettes.  

## <a name="i-trained-my-model-but-i-cant-test-it"></a>J’ai entraîné mon modèle, mais je ne peux pas le tester

Vous devez [déployer votre modèle](quickstart.md#deploy-your-model) avant de pouvoir le tester. 

## <a name="how-do-i-use-the-analyze-api"></a>Comment faire pour utiliser l’API d’analyse ?

Après avoir déployé votre modèle, vous [appelez l’API du runtime](how-to/call-api.md). Pour plus d’informations, consultez [Référence sur l’API Analyse](https://aka.ms/ct-runtime-swagger).

## <a name="data-privacy-and-security"></a>Sécurité et confidentialité des données

Vos données sont stockées uniquement dans votre compte Stockage Azure. La NER personnalisée n’a qu’un accès en lecture au cours de l’entraînement et de l’évaluation. 

<!-- ## How to clone my project?

To clone your project you need to [export]() project assests and then [import]() them into a new project. -->

## <a name="next-steps"></a>Étapes suivantes

[Vue d’ensemble de NER personnalisée](overview.md)
