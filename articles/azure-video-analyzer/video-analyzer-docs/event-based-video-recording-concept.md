---
title: Enregistrement vidéo basé sur les événements d’Azure Video Analyzer - Azure
description: Un enregistrement vidéo basé sur les événements (EVR) est un enregistrement vidéo qui a été déclenché par un événement. L’événement en question peut provenir du traitement du signal vidéo lui-même (par exemple, lorsqu’un mouvement est détecté) ou d’une source indépendante (par exemple, un capteur de porte signale que la porte a été ouverte). Certains cas d’usage relatifs à l’enregistrement EVR sont décrits dans cet article.
ms.topic: conceptual
ms.date: 11/01/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 4d6c7a45d9da0824578bd3640c53ee5d6b4ea23f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131039084"
---
# <a name="event-based-video-recording"></a>Enregistrement de vidéo basé sur les événements  

Un enregistrement vidéo basé sur les événements est un enregistrement vidéo qui a été déclenché par un événement. L’événement en question peut provenir du traitement du signal vidéo lui-même (par exemple, lorsqu’un mouvement est détecté) ou d’une source indépendante (par exemple, un capteur de porte signale que la porte a été ouverte). Certains cas d’usage relatifs à l’enregistrement EVR sont décrits dans cet article.

Les horodatages pour les enregistrements sont stockés au format UTC. La vidéo enregistrée peut être lue à l’aide des fonctionnalités de streaming de Video Analyzer. Pour plus d’informations, consultez [Lecture des enregistrements vidéo](playback-recordings-how-to.md).

## <a name="suggested-pre-reading"></a>Lecture préalable suggérée  

* [Concept de pipeline](pipeline.md)
* [Concept d’enregistrement vidéo](video-recording.md) 

## <a name="overview"></a>Vue d’ensemble 

Vous pouvez utiliser Video Analyzer pour exécuter EVR de deux manières :
* Enregistrer l’entrée d’une caméra IP prenant en charge RTSP dans une ressource vidéo donnée dans le cloud, où chaque nouvel événement s’ajouterait à l’enregistrement disponible dans cette ressource vidéo.
* Enregistrer dans des fichiers MP4 séparé du stockage local de l’appareil IoT Edge : chaque événement génère un nouveau fichier MP4.

Certains cas d’usage relatifs à l’enregistrement de vidéo basé sur les événements sont décrits dans cet article.

### <a name="video-recording-triggered-by-motion-detection"></a>Enregistrement vidéo déclenché par la détection de mouvement  

Dans ce cas d’usage, vous pouvez enregistrer des clips vidéo uniquement lorsqu’un mouvement est détecté dans la vidéo et être alerté quand un tel clip vidéo est généré. Cela pourrait être pertinent dans les scénarios de sécurité commerciale impliquant la protection des infrastructures critiques. EVR vous permet de réduire les coûts de stockage.

Le diagramme ci-dessous montre une représentation graphique d’un pipeline qui résout ce cas d’usage. La représentation JSON de la topologie de pipeline d’un tel pipeline est disponible [ici](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/evr-motion-video-sink/topology.json).

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording-concept/motion-detection.svg" alt-text="Enregistrement basé sur les événements de la vidéo en direct quand un mouvement est détecté.":::

Dans le diagramme, le nœud source RTSP capture le flux vidéo en direct à partir de la caméra et le remet à un nœud [processeur de détection de mouvement](pipeline.md#motion-detection-processor). Lors de la détection de mouvement dans la vidéo en direct, le nœud processeur de détection de mouvement génère des événements, qui accède au nœud [processeur de porte de signal](pipeline.md#signal-gate-processor), ainsi qu’au nœud récepteur de messages IoT Hub. Ce dernier envoie les événements au hub IoT Edge, à partir duquel ils peuvent être acheminés vers d’autres destinations pour déclencher des alertes. 

Les événements du nœud détecteur de mouvement déclenchent également le nœud processeur de porte de signal, en l’ouvrant et en laissant le flux vidéo en direct du nœud source RTSP passer au [nœud récepteur vidéo](pipeline.md#video-sink). En l’absence de tels événements de détection de mouvement ultérieurs, la porte se fermera après un laps de temps défini. Cela détermine la durée de la vidéo enregistrée qui est ajoutée à la ressource vidéo.

### <a name="video-recording-based-on-events-from-other-sources"></a>Enregistrement de vidéo basé sur les événements provenant d’autres sources  

Dans ce cas d’usage, les signaux d’un autre capteur IoT peuvent être utilisés pour déclencher l’enregistrement de la vidéo. Le diagramme ci-dessous montre une représentation graphique d’un pipeline qui résout ce cas d’usage. La représentation JSON de la topologie de pipeline d’un tel pipeline [est disponible ici](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/evr-hubMessage-file-sink/topology.json).

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording-concept/other-sources.svg" alt-text="Enregistrement basé sur les événements de la vidéo en direct lorsqu’elle est signalée par une source externe.":::

Dans le diagramme, le capteur externe envoie des événements au hub IoT Edge. Les événements sont ensuite acheminés vers le nœud processeur de porte de signal via le nœud [source de messages IoT Hub](pipeline.md#iot-hub-message-source). Le comportement du nœud processeur de porte de signal est le même que dans le cas d’usage précédent : lorsqu’il est déclenché par l’événement externe, il s’ouvre et laisse le flux vidéo en direct passer du nœud source RTSP au nœud récepteur de fichiers. Un nouveau fichier MP4 est écrit dans le stockage local de l’appareil IoT Edge à chaque ouverture de la porte.

### <a name="video-recording-based-on-an-external-inferencing-module"></a>Enregistrement de vidéo basé sur un module d’inférence externe 

Dans ce cas d’usage, vous pouvez enregistrer de la vidéo basée sur le signal d’un système logique externe. Un exemple de ce cas d’usage peut être l’enregistrement de vidéo dans le cloud uniquement lorsqu’un camion est détecté dans le flux vidéo du trafic sur une autoroute. Le diagramme ci-dessous montre une représentation graphique d’un pipeline qui résout ce cas d’usage. La représentation JSON de la topologie de pipeline d’un tel pipeline [est disponible ici](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/evr-hubMessage-video-sink/topology.json).

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording-concept/external-inferencing-module.svg" alt-text="Enregistrement basé sur les événements de la vidéo en direct lorsqu’elle est signalée par un module d’inférence externe.":::

Dans le diagramme, le nœud source RTSP capture le flux vidéo en direct à partir de la caméra et le remet à deux branches : l’une possède un nœud [processeur de porte de signal](pipeline.md#signal-gate-processor), et l’autre utilise un nœud [d’extension HTTP](pipeline.md#http-extension-processor) pour envoyer des données à un module logique externe. Le nœud d’extension HTTP permet au pipeline d’envoyer des trames d’image (au format JPEG, BMP ou PNG) à un service d’inférence externe via REST. Généralement, ce chemin de signal ne peut prendre en charge que des fréquences d’images faibles (< 5 fps). Vous pouvez utiliser le nœud de processeur d’extension HTTP pour réduire la fréquence d’images de la vidéo allant vers le modèle d’inférence externe.

Les résultats du service d’inférence externe sont récupérés par le nœud d’extension HTTP et relayés au hub IoT Edge via le nœud récepteur de messages IoT Hub, où ils peuvent être traités par le module logique externe. Si le service d’inférence est capable de détecter des véhicules, par exemple, le module logique peut rechercher un véhicule spécifique, tel qu’un camion. Lorsque le module logique détecte l’objet d’intérêt, il peut déclencher le nœud processeur de porte de signal en envoyant un événement via le hub IoT Edge au nœud source des messages IoT Hub dans le pipeline. La sortie de la porte de signal est affichée pour atteindre un nœud récepteur de fichiers ou un nœud récepteur de ressources. Chaque fois qu’un camion est détecté, la vidéo est enregistrée dans le cloud (ajouté à la ressource vidéo).

Cet exemple peut être amélioré à l’aide d’un processeur de détecteur de mouvement avant le nœud de processeur d’extension HTTP. Cela permettra de réduire la charge du service d’inférence, par exemple la nuit lorsqu’il peut y avoir de longues périodes sans aucun véhicule sur la route.

## <a name="resiliency"></a>Résilience
Consultez la [Remarque sur l’enregistrement résilient](continuous-video-recording.md#resilient-recording), qui s’applique également aux enregistrements EVR.

## <a name="next-steps"></a>Étapes suivantes

* [Didacticiel : enregistrement de vidéo basé sur les événements](record-event-based-live-video.md)
* [Lecture du contenu enregistré](playback-recordings-how-to.md)
