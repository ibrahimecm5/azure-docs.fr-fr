---
title: Vêtements de personnes détectés dans une vidéo
description: Cette rubrique présente une vue d’ensemble de la fonctionnalité de détection d’une personne portant un vêtement spécifique dans une vidéo.
ms.topic: conceptual
ms.date: 11/15/2021
ms.author: juliako
ms.openlocfilehash: 4bc0c0fd21a68d80bf8c8803f1a64e6e0ea097c7
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132495438"
---
# <a name="peoples-detected-clothing-preview"></a>Vêtements de personnes détectés (préversion)

Video Analyzer for Media détecte les vêtements associés à la personne qui les porte dans la vidéo et fournit des informations, comme le type de vêtements détectés et l’horodateur (timestamp) de l’événement (début, fin). L’API retourne le niveau de confiance de la détection.
 
Voici deux exemples pour lesquels cette fonctionnalité peut être utile :
 
* Améliorez l’efficacité lors de la création de données brutes pour des créateurs de contenu, comme la publicité par vidéo, des actualités ou des rencontres sportives (par exemple, rechercher des personnes portant un maillot rouge dans une archive vidéo).
* Analyse post-événement : détectez et suivez l’action d’une personne pour mieux analyser un événement postérieur à un accident ou un crime (par exemple, une explosion, un cambriolage de banque, un incident).
 
La fonctionnalité de détection des vêtements récemment ajoutée est disponible quand vous indexez votre fichier en choisissant la présélection **Option avancée** -> **Vidéo avancée** ou **Vidéo + audio avancés** (sous Indexation vidéo + audio). L’indexation standard n’inclut pas ce nouveau modèle avancé.
 
:::image type="content" source="./media/detected-clothing/index-video.png" alt-text="Cette capture d’écran représente une option d’indexation de la vidéo":::  

Quand vous choisissez d’afficher les **Insights** de votre vidéo sur le site web [Video Analyzer for Media](https://www.videoindexer.ai/) (anciennement Video Indexer), les vêtements de personnes détectés s’affichent dans l’insight de suivi des **personnes détectées**. Quand vous choisissez une miniature d’une personne, les vêtements détectés deviennent disponibles.

:::image type="content" source="./media/detected-clothing/observed-people.png" alt-text="Capture d’écran de personnes observées":::  
 
Si vous souhaitez afficher les vêtements de personnes détectés dans la chronologie de votre vidéo sur le site web Video Analyzer for Media, accédez à **Afficher** -> **Afficher les insights** et sélectionnez l’option **Tout** ou **Afficher** -> **Vue personnalisée** et sélectionnez **Personnes observées**. 

:::image type="content" source="./media/detected-clothing/observed-person.png" alt-text="Capture d’écran d’une personne observée":::  
 
Pour rechercher un vêtement spécifique afin de renvoyer toutes les personnes observées qui portent ce vêtement, vous pouvez utiliser la barre de recherche soit pour **Insights**, soit pour **Chronologie** de votre vidéo sur le site web Azure Video Analyzer for Media.

La réponse JSON suivante illustre le résultat retourné par Azure Video Analyzer for Media lors du suivi des personnes détectées comme portant un vêtement associé :

```json
"observedPeople": [
    {
        "id": 1,
        "thumbnailId": "68bab0f2-f084-4c2b-859b-a951ed03c209",
        "clothing": [
            {
                "id": 1,
                "type": "sleeve",
                "properties": {
                    "length": "short"
                }
            },
            {
                "id": 2,
                "type": "pants",
                "properties": {
                    "length": "long"
                }
            }
        ],
        "instances": [
            {
                "adjustedStart": "0:00:05.5055",
                "adjustedEnd": "0:00:09.9766333",
                "start": "0:00:05.5055",
                "end": "0:00:09.9766333"
            }
        ]
    },
    {
        "id": 2,
        "thumbnailId": "449bf52d-06bf-43ab-9f6b-e438cde4f217",
        "clothing": [
            {
                "id": 1,
                "type": "sleeve",
                "properties": {
                    "length": "short"
                }
            },
            {
                "id": 2,
                "type": "pants",
                "properties": {
                    "length": "long"
                }
            }
        ],
        "instances": [
            {
                "adjustedStart": "0:00:07.2072",
                "adjustedEnd": "0:00:10.5105",
                "start": "0:00:07.2072",
                "end": "0:00:10.5105"
            }
        ]
    },
]
```

## <a name="limitations-and-assumptions"></a>Limitations et hypothèses

Il est important de prendre en compte les limites de la fonctionnalité de vêtement détecté pour éviter ou atténuer les effets des faux négatifs (détections manquées).
 
* Pour optimiser les résultats du détecteur, utilisez des enregistrements vidéo de caméras statiques (même si une caméra mobile ou un mélange de scènes peut aussi donner des résultats).
* En général, les personnes qui apparaissent en petit ne sont pas détectées (la hauteur minimale d’une personne est de 200 pixels).
* La taille d’image maximale est HD
* Les personnes ne sont pas détectées si elles ne sont ni debout ni en train de marcher.
* La mauvaise qualité d’une vidéo (par exemple en cas de faible luminosité) peut avoir un impact sur les résultats de la détection.
* La fréquence d’images recommandée est de 30 i/s minimum.
* Dans une entrée vidéo, une même image ne doit pas contenir plus de 10 personnes. La fonctionnalité peut gérer la présence d’un plus grand nombre de personnes dans une même image, mais la détection ne peut pas extraire plus de 10 personnes d’une image avec le niveau de confiance de détection le plus élevé.
* Les personnes portant des vêtements identiques (par exemple, des personnes en uniforme, des joueurs dans une rencontre sportive) peuvent être détectées comme étant une même personne avec un même numéro d’identification.
* Occultations : des erreurs peuvent se produire en cas d’occultations (scène/auto-occultation ou occultations causées par d’autres personnes).
* Pose : les pistes peuvent être scindées en cas de poses différentes (arrière/avant)

## <a name="next-steps"></a>Étapes suivantes 

[Tracer les personnes observées dans une vidéo](observed-people-tracing.md)
