---
title: Présentation des fonctions de fenêtrage d’Azure Stream Analytics
description: Cet article décrit quatre fonctions de fenêtrage (bascules, récurrentes, glissantes, session) qui sont utilisées dans les travaux Azure Stream Analytics.
author: jseb225
ms.author: jeanb
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/16/2021
ms.openlocfilehash: 83c574ca578247389f8bc8c53c07847e1f01fb8b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131003627"
---
# <a name="introduction-to-stream-analytics-windowing-functions"></a>Présentation des fonctions de fenêtrage de Stream Analytics

Dans les scénarios de streaming en temps réel, il est courant d’effectuer des opérations sur les données contenues dans des fenêtres temporelles. Stream Analytics a une prise en charge native des fonctions de fenêtrage, permettant aux développeurs de créer des travaux de traitement de flux complexes avec un minimum d’effort.

Cinq types de fenêtres temporelles sont disponibles : [**bascule**](/stream-analytics-query/tumbling-window-azure-stream-analytics), [**récurrente**](/stream-analytics-query/hopping-window-azure-stream-analytics), [**glissante**](/stream-analytics-query/sliding-window-azure-stream-analytics), [**session**](/stream-analytics-query/session-window-azure-stream-analytics) et [**d’instantané**](/stream-analytics-query/snapshot-window-azure-stream-analytics).  Vous utilisez les fonctions de fenêtre dans la clause [**GROUP BY**](/stream-analytics-query/group-by-azure-stream-analytics) de la syntaxe de requête dans vos travaux Stream Analytics. Vous pouvez également agréger des événements sur plusieurs fenêtres avec la fonction [**Windows()**](/stream-analytics-query/windows-azure-stream-analytics).

Toutes les opérations de [fenêtrage](/stream-analytics-query/windowing-azure-stream-analytics) renvoient les résultats à la **fin** de la fenêtre. Notez que lorsque vous démarrez une tâche Stream Analytics, vous pouvez spécifier l’*heure de début de la sortie de la tâche* et le système extrait automatiquement les événements précédents dans les flux entrants pour générer la première fenêtre à l’heure spécifiée : par exemple, lorsque vous démarrez avec l’option *Maintenant*, elle commence immédiatement à émettre des données. La sortie de la fenêtre sera un événement unique qui dépendra de la fonction d’agrégation utilisée. L’événement de sortie aura l’horodatage de la fin de la fenêtre et toutes les fonctions de fenêtrage sont définies avec une longueur fixe. 

![Concepts des fonctions de fenêtrage de Stream Analytics](media/stream-analytics-window-functions/stream-analytics-window-functions-conceptual.png)

## <a name="tumbling-window"></a>Fenêtre bascule

Les fonctions de fenêtre [**bascule**](/stream-analytics-query/tumbling-window-azure-stream-analytics) sont utilisées pour segmenter un flux de données en segments temporels distincts et exécuter une fonction sur ces segments, comme dans l’exemple ci-dessous. Les principales caractéristiques des fenêtres bascules sont les suivantes : elles se répètent, elles ne se chevauchent pas et un événement ne peut pas appartenir à plusieurs fenêtres bascules.

![Fenêtre bascule Stream Analytics](media/stream-analytics-window-functions/stream-analytics-window-functions-tumbling-intro.png)

Avec les données d’entrée suivantes (illustrées ci-dessus) :

|Marqueur|CreatedAt|TimeZone|
|-|-|-|
|1|2021-10-26T10:15:01|PST|
|5|2021-10-26T10:15:03|PST|
|4|2021-10-26T10:15:06|PST|
|...|...|...|

Cette requête :

```SQL
SELECT System.Timestamp() as WindowEndTime, TimeZone, COUNT(*) AS Count
FROM TwitterStream TIMESTAMP BY CreatedAt
GROUP BY TimeZone, TumblingWindow(second,10)
```

Retourne :

|WindowEndTime|TimeZone|Count|
|-|-|-|
|2021-10-26T10:15:10|PST|5|
|2021-10-26T10:15:20|PST|2|
|2021-10-26T10:15:30|PST|4|


## <a name="hopping-window"></a>Fenêtre récurrente

Les fonctions de [**fenêtre récurrente**](/stream-analytics-query/hopping-window-azure-stream-analytics) font des bonds d’une durée fixe dans le temps. Il peut être facile de les considérer comme des fenêtres bascule qui peuvent se chevaucher et être émises plus souvent que la taille de la fenêtre. Les événements peuvent appartenir à plusieurs jeux de résultats de fenêtres récurrentes. Pour créer une fenêtre récurrente identique à une fenêtre bascule, spécifiez une taille de bond égale à la taille de la fenêtre. 

![Fenêtre récurrente Stream Analytics](media/stream-analytics-window-functions/stream-analytics-window-functions-hopping-intro.png)

Avec les données d’entrée suivantes (illustrées ci-dessus) :

|Marqueur|CreatedAt|Rubrique|
|-|-|-|
|1|2021-10-26T10:15:01|Diffusion en continu|
|5|2021-10-26T10:15:03|Diffusion en continu|
|4|2021-10-26T10:15:06|Diffusion en continu|
|...|...|...|

Cette requête :

```SQL
SELECT System.Timestamp() as WindowEndTime, Topic, COUNT(*) AS Count
FROM TwitterStream TIMESTAMP BY CreatedAt
GROUP BY Topic, HoppingWindow(second,10,5)
```

Retourne :

|WindowEndTime|Rubrique|Count|
|-|-|-|
|2021-10-26T10:15:10|Diffusion en continu|5|
|2021-10-26T10:15:15|Diffusion en continu|3|
|2021-10-26T10:15:20|Diffusion en continu|2|
|2021-10-26T10:15:25|Diffusion en continu|4|
|2021-10-26T10:15:30|Diffusion en continu|4|


## <a name="sliding-window"></a>Fenêtre glissante

Les [**fenêtres glissantes**](/stream-analytics-query/sliding-window-azure-stream-analytics), à la différence des fenêtres bascules ou récurrentes, génèrent des événements uniquement pour les points temporels où le contenu de la fenêtre change réellement. En d’autres termes, lorsqu’un événement entre dans la fenêtre ou la quitte. Par conséquent, chaque fenêtre affiche au moins un événement. Comme pour les fenêtres récurrentes, les événements peuvent appartenir à plusieurs fenêtres glissantes.

![Fenêtre défilante Stream Analytics 10 secondes](media/stream-analytics-window-functions/sliding-window-updated.png)

Avec les données d’entrée suivantes (illustrées ci-dessus) :

|Marqueur|CreatedAt|Rubrique|
|-|-|-|
|1|2021-10-26T10:15:10|Diffusion en continu|
|5|2021-10-26T10:15:12|Diffusion en continu|
|9|2021-10-26T10:15:15|Diffusion en continu|
|7|2021-10-26T10:15:15|Diffusion en continu|
|8|2021-10-26T10:15:27|Diffusion en continu|

Cette requête :

```SQL
SELECT System.Timestamp() as WindowEndTime, Topic, COUNT(*) AS Count
FROM TwitterStream TIMESTAMP BY CreatedAt
GROUP BY Topic, SlidingWindow(second,10)
HAVING COUNT(*) >=3
```

Retourne :

|WindowEndTime|Rubrique|Count|
|-|-|-|
|2021-10-26T10:15:15|Diffusion en continu|4|
|2021-10-26T10:15:20|Diffusion en continu|3|

## <a name="session-window"></a>Fenêtre session

Les fonctions de fenêtre [**session**](/stream-analytics-query/session-window-azure-stream-analytics) regroupent les événements qui arrivent au même moment, en filtrant les périodes de temps où il n’existe aucune donnée. Il existe trois paramètres principaux : délai d’expiration, durée maximale et clé de partitionnement (facultative).

![Fenêtre session Stream Analytics](media/stream-analytics-window-functions/stream-analytics-window-functions-session-intro.png)

Une fenêtre de session commence lorsque le premier événement se produit. Si un autre événement se produit dans le délai d’expiration spécifié à partir du dernier événement réceptionné, alors la fenêtre s’étend pour inclure le nouvel événement. Dans le cas contraire, si aucun événement ne se produit dans le délai imparti, la fenêtre est fermée une fois le délai expiré.

Si des événements continuent à se produire durant le délai d’expiration spécifié, la fenêtre de session continuera à s’étendre jusqu'à ce que la durée maximale soit atteinte. Les intervalles de vérification de la durée maximale sont définis pour être de la même taille que la durée maximale spécifiée. Par exemple, si la durée maximale est égale à 10, alors les vérifications pour voir si la fenêtre dépasse la durée maximale se produisent à t = 0, 10, 20, 30, etc.

Lorsqu’une clé de partition est fournie, les événements sont regroupés par la clé et la fenêtre de session est appliquée indépendamment à chaque groupe. Ce partitionnement est utile lorsque vous avez besoin de fenêtres de session différentes pour différents utilisateurs ou appareils.

Avec les données d’entrée suivantes (illustrées ci-dessus) :

|Marqueur|CreatedAt|Rubrique|
|-|-|-|
|1|2021-10-26T10:15:01|Diffusion en continu|
|2|2021-10-26T10:15:04|Diffusion en continu|
|3|2021-10-26T10:15:13|Diffusion en continu|
|...|...|...|

Cette requête :

```SQL
SELECT System.Timestamp() as WindowEndTime, Topic, COUNT(*) AS Count
FROM TwitterStream TIMESTAMP BY CreatedAt
GROUP BY Topic, SessionWindow(second,5,10)
```

Retourne :

|WindowEndTime|Rubrique|Count|
|-|-|-|
|2021-10-26T10:15:09|Diffusion en continu|2|
|2021-10-26T10:15:24|Diffusion en continu|4|
|2021-10-26T10:15:31|Diffusion en continu|2|
|2021-10-26T10:15:39|Diffusion en continu|1|

## <a name="snapshot-window"></a>Fenêtre d’instantané

Les fenêtres d’[**instantanés**](/stream-analytics-query/snapshot-window-azure-stream-analytics) regroupent les événements qui ont le même horodatage. Contrairement à d’autres types de fenêtrage, qui requièrent une fonction de fenêtre spécifique [par exemple, [SessionWindow()](/stream-analytics-query/session-window-azure-stream-analytics)], vous pouvez appliquer une fenêtre d’instantané en ajoutant System.Timestamp() à la clause GROUP BY.

![Fenêtre d’instantané Stream Analytics](media/stream-analytics-window-functions/stream-analytics-window-functions-snapshot-intro.png)

Avec les données d’entrée suivantes (illustrées ci-dessus) :

|Marqueur|CreatedAt|Rubrique|
|-|-|-|
|1|2021-10-26T10:15:04|Diffusion en continu|
|2|2021-10-26T10:15:04|Diffusion en continu|
|3|2021-10-26T10:15:04|Diffusion en continu|
|...|...|...|

Cette requête :

```SQL
SELECT System.Timestamp() as WindowEndTime, Topic, COUNT(*) AS Count
FROM TwitterStream TIMESTAMP BY CreatedAt
GROUP BY Topic, System.Timestamp()
```

Retourne :

|WindowEndTime|Rubrique|Count|
|-|-|-|
|2021-10-26T10:15:04|Diffusion en continu|4|
|2021-10-26T10:15:10|Diffusion en continu|2|
|2021-10-26T10:15:13|Diffusion en continu|1|
|2021-10-26T10:15:22|Diffusion en continu|2|

## <a name="next-steps"></a>Étapes suivantes
* [Présentation d’Azure Stream Analytics](stream-analytics-introduction.md)
* [Prise en main d'Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Mise à l’échelle des travaux Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Références sur le langage des requêtes d'Azure Stream Analytics](/stream-analytics-query/stream-analytics-query-language-reference)
* [Références sur l’API REST de gestion d’Azure Stream Analytics](/rest/api/streamanalytics/)
