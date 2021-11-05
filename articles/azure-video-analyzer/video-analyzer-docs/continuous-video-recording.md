---
title: Enregistrement vidéo continu à partir de la périphérie -Azure Video Analyzer
description: L’enregistrement vidéo continu (CVR) fait référence au processus d’enregistrement vidéo continu qui est effectué à partir d’une source vidéo en direct. Cette rubrique explique ce qu’est l’enregistrement vidéo continu et comment l’utiliser avec Azure Video Analyzer.
ms.service: azure-video-analyzer
ms.topic: conceptual
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 7d737536cca3e014b0412a00a99ce034f6d28e36
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131556556"
---
# <a name="continuous-video-recording"></a>Enregistrement de vidéo continu    

L’enregistrement de vidéo continu (CVR) fait référence au processus d’enregistrement vidéo continu à partir d’une source vidéo. Azure Video Analyzer prend en charge l’enregistrement vidéo continu, 24 h sur 24 et 7 jours sur 7, à partir d’une caméra CCTV via une [topologie de pipeline](pipeline.md) constituée d’un nœud source RTSP et d’un nœud de récepteur vidéo. Le diagramme ci-dessous montre une représentation graphique d’un tel pipeline. La représentation JSON de la topologie est disponible dans [ce document](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/cvr-video-sink/topology.json). Vous pouvez utiliser une telle topologie pour créer des enregistrements relativement longs (années de contenu). Les horodatages pour les enregistrements sont stockés au format UTC.  

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/continuous-video-recording/continuous-video-recording-overview.svg" alt-text="Enregistrement vidéo en continu":::

Une instance de la topologie de pipeline décrite ci-dessus peut être exécutée sur un appareil de périphérie dans le service Video Analyzer, avec le récepteur vidéo qui enregistre dans une [ressource vidéo](terminology.md#video). La vidéo sera enregistrée aussi longtemps que le pipeline sera activé. La vidéo enregistrée peut être lue à l’aide des fonctionnalités de streaming de Video Analyzer. Pour plus d’informations, consultez [Lecture des enregistrements vidéo](playback-recordings-how-to.md).

## <a name="suggested-pre-reading"></a>Lecture préalable suggérée  

Il est recommandé de lire les articles suivants avant de continuer.

* [Concept de topologie de pipeline](pipeline.md)
* [Concept d’enregistrement vidéo](video-recording.md) 
 
## <a name="resilient-recording"></a>Enregistrement résilient

Le module de périphérie Video Analyzer prend en charge le fonctionnement dans des conditions où l’appareil de périphérie peut parfois perdre la connectivité avec le cloud ou subir une baisse de la bande passante disponible. Pour tenir compte de cela, la vidéo de la source est enregistrée localement dans un cache et elle est automatiquement synchronisée avec la ressource vidéo de manière périodique. Si vous examinez la [topologie de pipeline](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/cvr-video-sink/topology.json), vous verrez que les propriétés suivantes sont définies :

```
"segmentLength": "PT30S",
"localMediaCacheMaximumSizeMiB": "2048",
"localMediaCachePath": "/var/lib/videoanalyzer/tmp/",
```

Les deux dernières propriétés sont pertinentes pour l’enregistrement résilient (toutes deux sont également des propriétés nécessaires pour un nœud de récepteur vidéo). La propriété `localMediaCachePath` indique au récepteur vidéo d’utiliser ce chemin de dossier pour mettre en cache les données multimédia avant de les charger dans le cloud. Vous pouvez consulter [cet article](../../iot-edge/how-to-access-host-storage-from-module.md) pour comprendre comment le module Edge peut utiliser le stockage local de votre appareil. La propriété `localMediaCacheMaximumSizeMiB` définit la quantité d’espace disque que le récepteur vidéo peut utiliser en tant que cache (1 Mio = 1 024 * 1024 octets). 

Si votre module de périphérie perd la connectivité pendant une longue période et que le contenu stocké dans le dossier cache atteint la valeur `localMediaCacheMaximumSizeMiB`, le récepteur vidéo commencera à supprimer les données du cache, en commençant par les données les plus anciennes. Par exemple, si l’appareil a perdu la connexion à 10 h et que le cache atteint la limite maximale à 18 h, le récepteur vidéo commence à supprimer les données enregistrées à 10 h. 

Lorsque la connectivité réseau est restaurée, le récepteur vidéo commence à charger à partir du cache, en commençant une fois encore par les données les plus anciennes. Dans l’exemple ci-dessus, supposons que 5 minutes de vidéo doivent être supprimées du cache au moment où la connectivité est rétablie (disons à 18 h 02), alors le récepteur vidéo commencera à charger à partir de 10 h 05.

Des écarts entre enregistrements peuvent également se produire, par exemple, si vous redémarrez des pipelines pour une raison quelconque. Vous pouvez également arrêter un pipeline et le redémarrer ultérieurement. Tant que les paramètres de la caméra ne changent pas, vous pouvez continuer à enregistrer dans la même ressource vidéo Video Analyzer.

## <a name="segmented-recording"></a>Enregistrement segmenté  

La propriété `segmentLength`, indiquée dans la section ci-dessus, vous permet de contrôler le coût des transactions qui sont associées à l’écriture des données dans le compte de stockage où la ressource vidéo est enregistrée. Par exemple, si vous augmentez la valeur de 30 secondes à 5 minutes, le nombre de transactions de stockage diminuera d’un facteur de 10 (5*60/30).

La propriété `segmentLength` garantit que la vidéo est écrite dans le compte de stockage au plus une fois toutes les `segmentLength` secondes. Cette propriété a une valeur minimale de 30 secondes (il s’agit également de la valeur par défaut) et peut être augmentée par incréments de 30 secondes jusqu’à un maximum de 5 minutes.

Cette propriété s’applique au module de périphérie Video Analyzer et au service Video Analyzer. Consultez l’article [Lecture des enregistrements vidéo](playback-recordings-how-to.md) pour connaître l’effet de `segmentLength` sur la lecture.

## <a name="see-also"></a>Voir aussi

* [Enregistrement de vidéo basé sur les événements](event-based-video-recording-concept.md) 
* [Lecture des enregistrements vidéo](playback-recordings-how-to.md) 

## <a name="next-steps"></a>Étapes suivantes

[Didacticiel : enregistrement de vidéo en continu](edge/use-continuous-video-recording.md) 
