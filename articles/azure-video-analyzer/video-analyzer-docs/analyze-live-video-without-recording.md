---
title: Analyse de vidéo en direct sans enregistrement – Azure
description: Vous pouvez utiliser une topologie de pipeline pour simplement extraire des analyses d’un flux vidéo en direct, sans avoir à les enregistrer en périphérie ou dans le cloud. Cet article aborde ce concept.
ms.topic: conceptual
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 6230c565e7f193264c0640a3a278b2812f20ad7d
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131847068"
---
# <a name="analyzing-live-videos-without-recording"></a>Analyse de vidéos en direct sans enregistrement


## <a name="suggested-pre-reading"></a>Lecture préalable suggérée 

* [Concept de pipeline](pipeline.md)
* [Concept d’extension de pipeline](pipeline-extension.md)
* [Concept d’enregistrement vidéo basé sur un événement](event-based-video-recording-concept.md)

## <a name="overview"></a>Vue d'ensemble  

Vous pouvez utiliser une topologie de pipeline pour analyser une vidéo en direct sans enregistrer de portions de la vidéo dans un fichier ou une ressource. Les topologies de pipeline ci-dessous sont similaires à celles décrites dans l’article [Enregistrement de vidéo basé sur un événement](event-based-video-recording-concept.md), mais sans nœud récepteur de vidéo ou de fichier.

> [!NOTE]
> L’analyse des vidéos en direct est actuellement disponible uniquement pour le module Edge et non pour le cloud.

### <a name="motion-detection"></a>Détection de mouvement

La topologie de pipeline ci-dessous se compose d’un nœud [source RTSP](pipeline.md#rtsp-source), d’un nœud [processeur de détection de mouvement](pipeline.md#motion-detection-processor) et d’un nœud [récepteur de messages IoT Hub](pipeline.md#iot-hub-message-sink). Vous pouvez voir les paramètres utilisés dans sa [représentation JSON](https://github.com/Azure/video-analyzer/blob/main/pipelines/live/topologies/motion-detection/topology.json). Cette topologie vous permet de détecter des mouvements dans les flux vidéo en direct entrants et de transmettre les événements à d’autres applications et services via le nœud récepteur de messages du IoT Hub. Les applications ou services externes peuvent déclencher une alerte ou envoyer une notification aux personnes appropriées.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/analyze-live-video-without-recording/motion-detection.svg" alt-text="Détection de mouvement dans une vidéo en direct":::

### <a name="analyzing-video-using-a-custom-vision-model"></a>Analyse de vidéos à l’aide d’un modèle Custom Vision 

La topologie de pipeline ci-dessous vous permet d’analyser un flux vidéo en direct à l’aide d’un modèle de vision personnalisé empaqueté dans un module distinct. Vous pouvez voir les paramètres utilisés dans sa [représentation JSON](https://github.com/Azure/video-analyzer/blob/main/pipelines/live/topologies/httpExtension/topology.json). D’autres [exemples](https://github.com/Azure/video-analyzer/tree/main/edge-modules/extensions) sont disponibles pour envelopper des modèles dans des modules IoT Edge qui s’exécutent en tant que service d’inférence.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/analyze-live-video-without-recording/motion-detected-frames.svg" alt-text="Analyse de vidéo en direct à l’aide d’un module de vision personnalisé":::

Dans cette topologie de pipeline, l’entrée vidéo de la source RTSP est envoyée à un nœud [processeur d’extension HTTP](pipeline.md#http-extension-processor) qui envoie des images (au format JPEG, BMP ou PNG) à un service d’inférence externe sur REST. Les résultats du service d’inférence externe sont récupérés par le nœud d’extension HTTP et relayés à IoT Edge Hub via le nœud de IoT Hub de réception des messages. Ce type de topologie de pipeline peut être utilisé pour créer des solutions pour divers scénarios, tels que la compréhension de la distribution de séries chronologiques de véhicules à une intersection, la compréhension du modèle de trafic des consommateurs dans un magasin de vente au détail, etc.

>[!TIP]
> Vous pouvez gérer la fréquence d’images dans le nœud de processeur d’extension HTTP à l’aide du champ `samplingOptions` avant de l’envoyer en aval.

Cet exemple peut être amélioré en utilisant un processeur de détecteur de mouvement avant le nœud de processeur d’extension HTTP. Cela réduira la charge sur le service d’inférence, car il est utilisé uniquement lorsqu’il y a une activité de mouvement dans la vidéo.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/analyze-live-video-without-recording/custom-model.svg" alt-text="Analyse de vidéo en direct à l’aide d’un module de vision personnalisé sur des images avec mouvement":::

## <a name="next-steps"></a>Étapes suivantes

[Démarrage rapide : Analyser une vidéo en direct avec votre propre modèle (HTTP)](analyze-live-video-use-your-model-http.md)
