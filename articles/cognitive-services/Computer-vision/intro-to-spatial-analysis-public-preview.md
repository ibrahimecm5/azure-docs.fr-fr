---
title: Qu'est-ce que l'Analyse spatiale ?
titleSuffix: Azure Cognitive Services
description: Ce document explique les concepts et fonctionnalités de base d’un conteneur Analyse spatiale Azure.
services: cognitive-services
author: nitinme
manager: nitinme
ms.author: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 10/06/2021
ms.custom: contperf-fy22q2
ms.openlocfilehash: fd160aa2ba4a626e12db638694cb6d971b19fbd2
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131469936"
---
# <a name="what-is-spatial-analysis"></a>Qu'est-ce que l'Analyse spatiale ?

Vous pouvez utiliser l’analyse spatiale de l’API Vision par ordinateur pour ingérer des flux vidéo provenant de caméras, en extraire des informations et générer des événements qui seront utilisés par d’autres systèmes. Ce service détecte la présence et les mouvements des personnes dans les vidéos. Il peut, par exemple, compter le nombre de personnes entrant dans un espace ou mesurer le respect des consignes de port du masque ou de distanciation sociale. En traitant les flux vidéo des espaces physiques, vous pouvez savoir de quelle manière les gens les utilisent et maximiser la valeur de l’espace pour votre organisation. 

<!--This documentation contains the following types of articles:
* The [quickstarts](./quickstarts-sdk/analyze-image-client-library.md) are step-by-step instructions that let you make calls to the service and get results in a short period of time. 
* The [how-to guides](./Vision-API-How-to-Topics/HowToCallVisionAPI.md) contain instructions for using the service in more specific or customized ways.
* The [conceptual articles](tbd) provide in-depth explanations of the service's functionality and features.
* The [tutorials](./tutorials/storage-lab-tutorial.md) are longer guides that show you how to use this service as a component in broader business solutions.-->

## <a name="what-it-does"></a>Résultat
L’analyse spatiale ingère la vidéo puis détecte les personnes dans la vidéo. Une fois les personnes détectées, le système les suit dans leurs déplacements au fil du temps, puis génère des événements lorsque les personnes interagissent avec des régions d’intérêt. Toutes ces opérations fournissent des insights à partir du champ de vision d’une seule caméra. 

### <a name="people-counting"></a>Comptage des personnes
Cette opération compte le nombre de personnes dans une zone spécifique dans le temps à l’aide de l’opération PersonCount. Elle génère un compte indépendant pour chaque image traitée sans tenter de suivre les personnes d’une image à l’autre. Cette opération peut être utilisée pour estimer le nombre de personnes dans un espace ou pour générer une alerte lorsqu’une personne apparaît.

![L’analyse spatiale compte le nombre de personnes dans le champ de vision des caméras.](https://user-images.githubusercontent.com/11428131/139924111-58637f2e-f2f6-42d8-8812-ab42fece92b4.gif)

### <a name="entrance-counting"></a>Comptage des entrées
Cette fonctionnalité permet de contrôler le temps passé par les personnes dans une zone ou lorsqu’elles entrent par une porte. Ce suivi peut être effectué à l’aide des opérations PersonCrossingPolygon ou PersonCrossingLine. Dans les scénarios de vente au détail, ces opérations peuvent être utilisées pour mesurer le temps d’attente pour une ligne de caisse ou l’engagement devant un présentoir. De même, ces opérations peuvent mesurer le trafic piétonnier dans un hall ou à un étage spécifique dans d’autres scénarios de bâtiments commerciaux.

![L’analyse spatiale mesure le temps d’attente dans la file d’attente aux caisses.](https://user-images.githubusercontent.com/11428131/137016574-0d180d9b-fb9a-42a9-94b7-fbc0dbc18560.gif)

### <a name="social-distancing-and-facemask-detection"></a>Détection de la distanciation sociale et du port du masque 
Cette fonctionnalité permet d’analyser dans quelle mesure les personnes respectent les exigences de distanciation sociale dans un espace. Grâce à l’opération PersonDistance, le système se calibre automatiquement lorsque les personnes se déplacent dans l’espace. Il identifie ensuite les personnes qui ne respectent pas un seuil de distance spécifique (par exemple, 2 m ou 3 m).

![L’analyse spatiale visualise les événements de violation de la distanciation sociale en montrant des lignes entre les personnes indiquant la distance.](https://user-images.githubusercontent.com/11428131/139924062-b5e10c0f-3cf8-4ff1-bb58-478571c022d7.gif)

L’analyse spatiale peut également être configurée pour détecter si une personne porte une protection faciale telle qu’un masque. Un classificateur de masque peut être activé pour les opérations PersonCount, PersonCrossingLine et PersonCrossingPolygon en configurant le paramètre `ENABLE_FACE_MASK_CLASSIFIER`.

![L’analyse spatiale permet de déterminer si les personnes portent un masque dans un ascenseur.](https://user-images.githubusercontent.com/11428131/137015842-ce524f52-3ac4-4e42-9067-25d19b395803.png)

## <a name="get-started"></a>Bien démarrer

Suivez le [guide de démarrage rapide](spatial-analysis-container.md) pour configurer le conteneur Analyse spatiale et commencer l’analyse vidéo.

## <a name="responsible-use-of-spatial-analysis-technology"></a>Utilisation responsable de la technologie d’analyse spatiale

Pour savoir comment utiliser la technologie d’analyse spatiale de façon responsable, consultez la [note de transparence](/legal/cognitive-services/computer-vision/transparency-note-spatial-analysis?context=%2fazure%2fcognitive-services%2fComputer-vision%2fcontext%2fcontext). Les notes de transparence de Microsoft vous aident à comprendre le fonctionnement de notre technologie d’IA et les choix que les propriétaires de systèmes peuvent faire pour influencer le niveau de performance et le comportement du système. Elles mettent l’accent sur l’importance de penser à l’ensemble du système, y compris la technologie, les personnes et l’environnement.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Démarrage rapide : Conteneur Analyse spatiale](spatial-analysis-container.md)
