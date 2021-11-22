---
title: Tracer les personnes observées dans une vidéo
description: Cette rubrique offre une vue d’ensemble du traçage des personnes observées dans une vidéo.
ms.topic: conceptual
ms.date: 04/30/2021
ms.author: juliako
ms.openlocfilehash: 6726c67cf5274ba252b86ace9165330dc296afb4
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132491940"
---
# <a name="trace-observed-people-in-a-video-preview"></a>Tracer les personnes observées dans une vidéo (préversion)

Video Indexer détecte les personnes observées dans les vidéos et fournit des informations comme l’emplacement d’une personne dans l’image vidéo et l’horodatage exact (début, fin) indiquant à quel moment elle apparaît. L’API retourne les coordonnées du cadre englobant (en pixels) pour chaque personne détectée avec la confiance de détection.  
 
Voici quelques exemples de scénarios où cette fonctionnalité peut être utile :

* Analyse post-événement : détectez et suivez l’action d’une personne pour mieux analyser un événement postérieur à un accident ou un crime (par exemple, une explosion, un cambriolage de banque, un incident).  
* Améliorez l’efficacité lors de la création de données brutes pour des créateurs de contenu, comme la publicité par vidéo, des actualités ou des rencontres sportives (par exemple, rechercher des personnes portant un maillot rouge dans une archive vidéo).
* Créez un résumé d’une longue vidéo, par exemple pour prouver dans le cadre d’une procédure judiciaire qu’une personne déterminée apparaît bien dans une vidéo, à partir de l’ID de la personne détectée.
* Découvrez et analysez des tendances dans le temps, par exemple comment les clients se déplacent dans les allées d’un centre commercial ou leur durée d’attente aux caisses.

Par exemple, si une vidéo contient une personne, l’opération de détection liste les apparitions de la personne avec les coordonnées dans les images vidéo. Vous pouvez utiliser cette fonctionnalité pour déterminer le parcours de la personne dans une vidéo. Elle vous permet aussi de déterminer s’il existe plusieurs occurrences de la même personne dans une vidéo.

Vous pouvez accéder à la fonctionnalité de **traçage des personnes observées** nouvellement ajoutée pendant que vous indexez votre fichier en choisissant la présélection **Option avancée** -> **Vidéo avancé** ou **Vidéo + audio avancé** (sous **Indexation vidéo + audio**). L’indexation standard n’inclut pas ce nouveau modèle avancé. 

:::image type="content" source="./media/observed-people-tracing/youtube-trailer.png" alt-text="Capture d’écran du traçage des personnes observées":::  
 
Quand vous choisissez d’afficher les **Insights** de votre vidéo sur le site web [Video Indexer](https://www.videoindexer.ai/account/login), le traçage des personnes observées s’affiche dans la page avec les miniatures de toutes les personnes détectées. Vous pouvez choisir la miniature d’une personne et voir où elle apparaît dans le lecteur vidéo. 

La réponse JSON suivante illustre les informations que retourne Video Indexer pendant le traçage des personnes observées : 

```json
    {
    ...
    "videos": [
        {
            ...
            "insights": {
                ...
                "observedPeople": [{
                    "id": 1,
                    "thumbnailId": "560f2cfb-90d0-4d6d-93cb-72bd1388e19d",
                    "instances": [
                        {
                            "adjustedStart": "0:00:01.5682333",
                            "adjustedEnd": "0:00:02.7027",
                            "start": "0:00:01.5682333",
                            "end": "0:00:02.7027"
                        }
                    ]
                },
                {
                    "id": 2,
                    "thumbnailId": "9c97ae13-558c-446b-9989-21ac27439da0",
                    "instances": [
                        {
                            "adjustedStart": "0:00:16.7167",
                            "adjustedEnd": "0:00:18.018",
                            "start": "0:00:16.7167",
                            "end": "0:00:18.018"
                        }
                    ]
                },]
            }
            ...
            }
    ]
}
```

## <a name="limitations-and-assumptions"></a>Limitations et hypothèses 

Il est important de prendre en compte les limites du traçage des personnes observées pour éviter ou atténuer les effets des faux négatifs (détections manquées) et des détails limités.

* Pour optimiser les résultats du détecteur, utilisez des enregistrements vidéo de caméras statiques (même si une caméra mobile ou un mélange de scènes peut aussi donner des résultats). 
* En général, les personnes qui apparaissent en petit ne sont pas détectées (la hauteur minimale de la personne est de 200 pixels).
* La taille d’image maximale est HD
* Les personnes qui ne sont pas debout ou qui ne marchent pas ne sont généralement pas détectées. 
* La mauvaise qualité d’une vidéo (par exemple, en cas de faible luminosité) peut avoir un impact sur les résultats de la détection. 
* La fréquence d’images recommandée est de 30 i/s, au minimum. 
* Dans une entrée vidéo, une même image ne doit pas contenir plus de 10 personnes. La fonctionnalité peut gérer la présence d’un plus grand nombre de personnes dans une même image, mais la détection ne peut pas extraire plus de 10 personnes d’une image avec le niveau de confiance de détection le plus élevé. 
* Les personnes portant des vêtements identiques (par exemple, des personnes en uniforme, des joueurs dans une rencontre sportive) peuvent être détectées comme étant une même personne avec un même numéro d’identification. 
* Occultations : des erreurs peuvent se produire en cas d’occultations (scène/auto-occultation ou occultations causées par d’autres personnes).
* Pose : les pistes peuvent être scindées en cas de poses différentes (arrière/avant)       

## <a name="next-steps"></a>Étapes suivantes

Consulter la [vue d’ensemble](video-indexer-overview.md)
