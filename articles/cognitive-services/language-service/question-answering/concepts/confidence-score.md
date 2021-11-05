---
title: 'Score de confiance : réponses aux questions'
titleSuffix: Azure Cognitive Services
description: Quand une requête utilisateur est mise en correspondance avec une base de connaissances, la réponse aux questions renvoie des réponses pertinentes, ainsi qu’un score de confiance.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: language-service-question-answering, ignite-fall-2021
ms.openlocfilehash: 605f4ed4e4d146b6a03979496f171417330732ba
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131097747"
---
# <a name="confidence-score"></a>Score de confiance

Lorsqu’une requête utilisateur est mise en correspondance avec un projet (également appelé base de connaissances), la réponse aux questions renvoie des réponses pertinentes, ainsi qu’un score de confiance. Ce score indique la probabilité que la réponse corresponde à la requête de l’utilisateur.

Un score de confiance est une valeur comprise de 0 à 100. Un score de 100 est probablement une correspondance exacte, tandis qu’un score de 0 signifie qu’aucune réponse correspondante n’a été trouvée. Plus le score et élevé,  plus la réponse est fiable. Plusieurs réponses peuvent correspondre à une requête donnée. Dans ce cas, les réponses sont retournées dans l’ordre décroissant du score de confiance.

Le tableau suivant indique la confiance généralement associée à un score donné.

|Valeur du score|Signification du score|Exemple de requête|
|--|--|--|
|90 - 100|Correspondance presque exacte entre la requête de l’utilisateur et une question de la base de connaissances|
|> 70|Niveau de confiance élevé, généralement une bonne réponse correspondant complètement à la requête de l’utilisateur|
|50 - 70|Confiance moyenne, généralement une assez bonne réponse correspondant à l’intention principale de la requête de l’utilisateur|
|30 - 50|Confiance faible, généralement une réponse connexe correspondance partiellement l’intention de la requête de l’utilisateur|
|< 30|Confiance très faible, généralement une réponse ne correspondant pas à la requête de l’utilisateur, à l’exception de certains mots ou expressions |
|0|Aucune correspondance, donc aucune réponse retournée.|

## <a name="choose-a-score-threshold"></a>Choisir un seuil de score

Le tableau ci-dessus montre la plage de scores qui peuvent se produire lors de l’interrogation avec des réponses aux questions. Cependant, comme chaque projet est différent et qu’il comporte différents types de mots, d’intentions et d’objectifs, nous vous recommandons de tester et de choisir le seuil qui vous convient le mieux. Par défaut, le seuil est défini sur `0`, afin que toutes les réponses possibles soient retournées. Le seuil recommandé qui devrait fonctionner pour la plupart des projets est **50**.

Lorsque vous choisissez votre seuil, n’oubliez pas l’équilibre entre **exactitude** et **couverture**, et ajustez votre seuil à vos besoins.

- Si l’**exactitude** (ou précision) est primordiale pour votre scénario, augmentez votre seuil. Ainsi, chaque fois que vous retournerez une réponse, celle-ci sera beaucoup plus FIABLE et susceptible d’être celle qu’attendent les utilisateurs. Dans ce cas, vous risquez de laisser davantage de questions sans réponse. 

- Si la **couverture** (ou rappel) est primordiale et que vous souhaitez répondre au plus grand nombre possible de questions, même si la réponse n’a qu’une relation très vague ou lointaine avec la question, ABAISSEZ le seuil. Cela signifie qu’il pourrait y avoir davantage de cas où la réponse ne correspond pas à la requête réelle de l’utilisateur, mais fournit des informations vaguement associées à la question.

## <a name="set-threshold"></a>Définir le seuil

Définissez le score de seuil en tant que propriété du [corps JSON de l’API REST](../quickstart/sdk.md). Cela signifie que vous le définissez pour chaque appel à l’API REST.

## <a name="improve-confidence-scores"></a>Améliorer les scores de confiance

Pour améliorer le score de confiance d’une réponse spécifique à une question de l’utilisateur, vous pouvez ajouter la question de l’utilisateur à la base de connaissances en tant que question alternative liée à cette réponse. Vous pouvez également utiliser des [synonymes](../tutorials/adding-synonyms.md) (ignorant la casse) pour ajouter des synonymes aux mots clés dans votre projet.

## <a name="similar-confidence-scores"></a>Scores de confiance similaires

Lorsque plusieurs réponses ont un score de confiance similaire, il est probable que la question était trop générique et donc mise en correspondance à probabilité égale avec plusieurs réponses. Essayez de mieux structurer vos Questions et réponses afin que chaque entité QnA ait une intention distincte.

## <a name="confidence-score-differences-between-test-and-production"></a>Différences entre les scores de confiance entre le test et la production

Le score de confiance d’une réponse peut changer sensiblement entre le test et la version déployée du projet, même si le contenu est le même. En effet, le contenu de la base de connaissances de test et du projet déployé se trouvent dans des index Recherche cognitive Azure différents.

L’index de test contient toutes les paires de questions et réponses de votre projet. Lors de l’interrogation de l’index de test, la requête s’applique à l’ensemble de l’index, puis les résultats sont limités à la partition de ce projet spécifique. Si les résultats de la requête de test ont un impact négatif sur votre capacité à vérifier le projet, vous pouvez :
* Organisez votre projet à l’aide de l’un des éléments suivants :
    * Une ressource limitée à un projet : limitez votre ressource de langue unique (et l’index de test Azure Recherche cognitive résultant) à un projet/une base de connaissances.
    * Deux ressources, une pour le test, une pour la production : deux ressources de langue, en utilisant l’une pour le test (avec ses propres index de test et de production) et une pour la production (également avec ses propres index de test et de production)
* Utilisez toujours les mêmes paramètres lors de l’interrogation de vos projets de test et de production.

Lorsque vous déployez un projet, le contenu de la question et de la réponse de votre projet passe de l’index de test à un index de production dans Azure Search.

Si vous avez un projet dans des régions différentes, chacune d’elles utilise son propre index de Recherche cognitive Azure. Étant donné que différents index sont utilisés, les scores ne seront pas exactement les mêmes.

## <a name="no-match-found"></a>Aucune correspondance trouvée

Si aucune bonne correspondance n’est trouvée par la fonction de classement, le score de confiance de 0,0 ou « None » est retourné, et la réponse par défaut est renvoyée. Vous pouvez modifier la [réponse par défaut](../how-to/change-default-answer.md).

## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"]
> [Bonnes pratiques](./best-practices.md)
