---
title: Détection d’assertion dans Analyse de texte pour l’intégrité
titleSuffix: Azure Cognitive Services
description: En savoir plus sur la détection d’assertion.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-health, ignite-fall-2021
ms.openlocfilehash: 0f9d24e43bd4525a703027cff6a5ee4bec7bd431
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131028996"
---
# <a name="assertion-detection"></a>Détection d’assertion

La signification du contenu médical est fortement affectée par les modificateurs, tels que la négation et les assertions conditionnelles, qui peuvent avoir une incidence critique en cas de mauvaise interprétation. L’analyse de texte pour la santé prend en charge trois catégories de détection d’assertion pour les entités dans le texte : 

* Certainty
* Logique conditionnelle
* Association

## <a name="assertion-output"></a>Sortie d’assertion

L’analyse de texte pour la santé retourne des modificateurs d’assertion, qui sont des attributs informatifs affectés à des concepts médicaux qui fournissent une compréhension plus approfondie du contexte des concepts dans le texte. Ces modificateurs sont divisés en trois catégories, chacune se concentrant sur un aspect différent et contenant un ensemble de valeurs s’excluant mutuellement. Une seule valeur par catégorie est attribuée à chaque entité. La valeur par défaut est la valeur la plus courante pour chaque catégorie. La réponse de sortie du service contient uniquement des modificateurs d’assertion qui sont différents de la valeur par défaut.

**CERTAINTY** : fournit des informations sur la présence (présent ou absent) du concept et du niveau de certitude du texte quant à sa présence (certain ou possible).
*   **Positive** [par défaut] : le concept existe ou est survenu.
* **Negative** : le concept n’existe pas maintenant ou n’a jamais eu lieu.
* **Positive_Possible** : le concept existe probablement, mais il y a une certaine incertitude.
* **Negative_Possible** : l’existence du concept est peu probable, mais il y a une certaine incertitude.
* **Neutral_Possible** : le concept peut exister ou non sans une tendance claire dans l’une ou l’autre direction.

**CONDITIONALITY** : fournit des informations indiquant si l’existence d’un concept dépend de certaines conditions. 
*   **None** [par défaut] : le concept est factuel et non hypothétique, et ne dépend pas de certaines conditions.
*   **Hypothetical** : le concept peut se développer ou se produire à l’avenir.
*   **Conditional** : le concept existe ou se produit uniquement sous certaines conditions.

**ASSOCIATION** : indique si le concept est associé à l’objet du texte ou à une autre personne.
*   **Subject** [par défaut] : le concept est associé à l’objet du texte, généralement le patient.
*   **Someone_Else**: le concept est associé à une personne qui n’est pas l’objet du texte.


La détection d’assertion représente des entités inversées comme une valeur négative pour la catégorie de certitude, par exemple :

```json
{
    "offset": 381,
    "length": 3,
    "text": "SOB",
    "category": "SymptomOrSign",
    "confidenceScore": 0.98,
    "assertion": {
        "certainty&quot;: &quot;negative"
    },
    "name": "Dyspnea",
    "links": [
        {
            "dataSource": "UMLS",
            "id&quot;: &quot;C0013404"
        },
        {
            "dataSource": "AOD",
            "id&quot;: &quot;0000005442"
        },
    ...
}
```

## <a name="next-steps"></a>Étapes suivantes

[Comment appeler l’Analyse de texte pour l’intégrité](../how-to/call-api.md)
