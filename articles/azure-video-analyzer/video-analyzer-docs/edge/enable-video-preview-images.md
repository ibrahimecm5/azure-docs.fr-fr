---
title: Activer des images d’aperçu
description: Cet article explique comment activer des images d’aperçu et y accéder lors de l’enregistrement de vidéos à l’aide d’Azure Video Analyzer
ms.topic: how-to
ms.date: 11/01/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: ed3ebeffb70b592c1a81e8ec7871a662f7b856bd
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096444"
---
# <a name="enable-preview-images-when-recording-video"></a>Activer des images d’aperçu lors de l’enregistrement de vidéos

Vous pouvez utiliser Azure Video Analyzer pour [capturer et enregistrer des vidéos](../video-recording.md) à partir d’une caméra RTSP. Vous créez une topologie de pipeline qui comprend un nœud de récepteur vidéo, comme illustré dans ce [guide de démarrage rapide](detect-motion-record-video-clips-cloud.md) ou ce [tutoriel](use-continuous-video-recording.md). 

Si vous enregistrez une vidéo à l’aide du module de périphérie Video Analyzer, vous pouvez activer le nœud de récepteur vidéo pour générer régulièrement un ensemble d’images d’aperçu de tailles différentes. Ces images peuvent ensuite être récupérées à partir de la [ressource vidéo](../terminology.md#video) dans votre compte Video Analyzer. Par exemple, si votre caméra génère une vidéo qui a une résolution de 1920 x 1080, les images d’aperçu auront les tailles suivantes :

  * 320 x 180 : petite
  * 640 x 360 : moyenne
  * 1280 x 720 : grande

> [!NOTE]
> Les images d’aperçu conservent les proportions de la vidéo de la caméra.

Les images d’aperçu sont générées régulièrement, la fréquence étant déterminée par [`segmentLength`](../playback-recordings-how-to.md#recording-and-playback-latencies). Si vous utilisez l’[enregistrement basé sur les événements](record-event-based-live-video.md), vous devez noter que les images sont générées uniquement lorsque le pipeline en direct est actif et que la vidéo est en cours d’enregistrement. Chaque fois qu’un ensemble d’images d’aperçu est généré, elles remplacent l’ensemble précédent.

> [!NOTE]
> Cette fonctionnalité est actuellement disponible uniquement avec le module de périphérie Video Analyzer. En outre, cette activation a un impact sur les coûts de stockage Azure, calculés en fonction des transactions fréquentes pour l’écriture ou l’affichage des images ainsi que de la taille des images.

## <a name="enable-preview-images-in-the-video-sink-node"></a>Activer des images d’aperçu dans le nœud de récepteur vidéo
Pour activer les images d’aperçu, vous devez définir l’indicateur approprié dans le nœud de récepteur vidéo de la topologie de pipeline. Sous **videoPublishingOptions**, affectez la valeur **true** à **enableVideoPreviewImage**  

Exemple :
```
        "sinks": [
        {
          "@type": "#Microsoft.VideoAnalyzer.VideoSink",
          "name": "videoSink",
          "videoName": "{$parameter-for-specifying-unique-videoName-for-each-pipeline}",
          "inputs": [
            {
              "nodeName": "rtspSource",
              "outputSelectors": [
                {
                  "property": "mediaType",
                  "operator": "is",
                  "value": "video"
                }
              ]
            }
          ],
          "videoPublishingOptions": {
            "enableVideoPreviewImage": true
          },
          "videoCreationProperties": {
            "title": "{$parameter-for-specifying-unique-title-for-each-pipeline}",
            "description": "{$parameter-for-specifying-unique-description-for-each-pipeline}k",
            "segmentLength": "PT30S"
          },
          "localMediaCachePath": "/var/lib/videoanalyzer/tmp/",
          "localMediaCacheMaximumSizeMiB": "2048"
        }
      ]
``` 

## <a name="accessing-preview-images"></a>Accès aux images d’aperçu

Pour obtenir les URL statiques vers les images d’aperçu disponibles, une demande GET doit être appelée sur la ressource vidéo avec un [jeton de porteur autorisé](../playback-recordings-how-to.md#accessing-videos). Vous verrez les URL répertoriées sous **contentUrls** dans la réponse, comme indiqué ci-dessous.

```
      "contentUrls": {
        ...
        "previewImageUrls": {
          "small": "XXXX",
          "medium": "XXXX",
          "large": "XXXX"
         }
       },
    
```

## <a name="next-steps"></a>Étapes suivantes

Essayez d’activer les images d’aperçu vidéo dans la topologie dans ce [guide de démarrage rapide](detect-motion-record-video-clips-cloud.md) ou [tutoriel](use-continuous-video-recording.md). 
