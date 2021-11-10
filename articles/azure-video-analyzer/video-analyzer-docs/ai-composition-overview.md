---
title: Compositions d’intelligence artificielle dans Azure Video Analyzer
description: Cet article offre une vue d’ensemble de la prise en charge par Azure Video Analyzer de trois types de composition d’intelligence artificielle. Cette rubrique fournit également une explication de scénario pour chaque type de composition d’intelligence artificielle.
ms.topic: conceptual
ms.date: 11/04/2021
ms.openlocfilehash: 4195fd0dbcd44f0eeb273384e6d6cfad352aedb5
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131847106"
---
# <a name="ai-composition"></a>Composition d’intelligence artificielle

Cet article offre une vue d’ensemble de la prise en charge par Azure Video Analyzer de trois types de composition d’intelligence artificielle. 

* [Séquentiel](#sequential-ai-composition)
* [Parallel](#parallel-ai-composition)
* [Combiné](#combined-ai-composition)

## <a name="sequential-ai-composition"></a>Composition d’intelligence artificielle séquentielle

Les nœuds IA peuvent être composés de manière séquentielle. Ainsi, un nœud en aval peut augmenter les inférences générées par un nœud en amont.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/ai-composition/sequential.svg" alt-text="Composition d’intelligence artificielle séquentielle":::
 
### <a name="key-aspects"></a>Aspects clés

* Les extensions de pipeline jouent le rôle de nœuds de passage de média et peuvent être configurées pour que les serveurs IA externes reçoivent des trames de différents formats et résolutions à différentes vitesses. En outre, la configuration peut être spécifiée pour que les serveurs IA externes puissent recevoir toutes les trames ou uniquement les trames qui contiennent déjà des inférences.
* Les inférences sont ajoutées aux trames à mesure qu’elles passent par les différents nœuds d’extension. Un nombre illimité de ces nœuds peut être ajouté de manière séquentielle.
* D’autres scénarios tels que l’enregistrement continu vidéo ou l’enregistrement vidéo basé sur les événements peuvent être combinés à la composition d’intelligence artificielle séquentielle.

    
## <a name="parallel-ai-composition"></a>Composition d’intelligence artificielle parallèle

Les nœuds IA peuvent également être composés en parallèle et non en séquence. Ceci permet d’effectuer des inférences indépendantes sur le flux vidéo ingéré en économisant la bande passante d’ingestion en périphérie.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/ai-composition/parallel.svg" alt-text="Composition d’intelligence artificielle parallèle":::
 
### <a name="key-aspects"></a>Aspects clés

* La vidéo peut être divisée en un nombre quelconque de branches parallèles en tout point après les nœuds suivants.
    
    * Source RTSP
    * Motion Detector
    * Extension de pipeline

## <a name="combined-ai-composition"></a>Composition d’intelligence artificielle combinée

Les constructions de compositions séquentielles et parallèles peuvent être combinées en vue du développement de pipelines IA composables complexes. Cette combinaison est possible puisque les pipelines AVA permettent de combiner des nœuds d’extension de manière séquentielle et/ou avec une composition parallèle indéfiniment avec d’autres nœuds pris en charge.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/ai-composition/complex.svg" alt-text="Composition d’intelligence artificielle combinée":::
 


## <a name="next-steps"></a>Étapes suivantes

[Analyser des flux vidéo en direct avec plusieurs modèles IA à l’aide de la composition IA](analyze-ai-composition.md)
