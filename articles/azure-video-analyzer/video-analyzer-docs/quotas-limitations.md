---
title: Quotas et limitations d’Azure Video Analyzer – Azure
description: Cet article décrit les quotas et les limitations d’Azure Video Analyzer.
ms.service: azure-video-analyzer
ms.topic: conceptual
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: cad4ed3ffac3d9b8617150070579bd9bee5ad6b3
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131563128"
---
# <a name="video-analyzer-quotas-and-limitations"></a>Quotas et limitations de Video Analyzer

Cet article décrit les quotas et les limitations d’Azure Video Analyzer.

## <a name="quotas-and-limitations---edge-module"></a>Quotas et limitations – Module Edge

Cette section énumère les quotas et limitations du module Edge Video Analyzer.

### <a name="maximum-period-of-disconnected-use"></a>Période maximale d’utilisation déconnectée

Le module de périphérie peut supporter une perte temporaire de la connectivité Internet. Si le module reste déconnecté pendant plus de 36 heures, il désactive tous les pipelines en direct en cours d’exécution. Tous les appels de méthode directe qui suivent sont bloqués.

Pour restaurer le module Edge dans un état opérationnel, vous devez rétablir la connectivité Internet de sorte que le module puisse communiquer correctement avec le compte Azure Video Analyzer.

### <a name="maximum-number-of-live-pipelines"></a>Nombre maximal de pipelines en direct

Au maximum 1 000 pipelines en direct par module (créés via `livePipelineSet`) sont pris en charge.

### <a name="maximum-number-of-pipeline-topologies"></a>Nombre maximal de topologies de pipeline

Au maximum 50 topologies de pipeline par module (créées via `pipelineTopologySet`) sont prises en charge.

### <a name="limitations-on-pipeline-topologies"></a>Limitations concernant les topologies de pipeline

Voici les limitations relatives à la façon dont différents nœuds peuvent être interconnectés dans une topologie de pipeline.

* Source RTSP
   * Une seule source RTSP est autorisée par topologie de pipeline.
* Processeur de détection de mouvement
   * Doit être immédiatement en aval de la source RTSP.
   * Ne peut pas être utilisé en aval d’un processeur d’extension HTTP ou gRPC.
* Processeur de porte de signal
   * Doit être immédiatement en aval de la source RTSP.
   * Ne peut pas être utilisé en amont d’un processeur d’extension HTTP ou gRPC.
* Processeur de suivi d’objet
   * Doit être immédiatement en aval d’un processeur d’extension HTTP ou gRPC. Le processeur d’extension ne doit pas appliquer un modèle IA à chaque image de la vidéo en direct.
* Processeur de franchissement de ligne
   * Doit être immédiatement en aval d’un processeur de suivi d’objet.
* Récepteur vidéo 
   * Doit être immédiatement en aval de la source RTSP ou du processeur de porte de signal.
* Récepteur de fichiers
   * Doit être immédiatement en aval du processeur de porte de signal.
   * Ne peut pas être immédiatement en aval d’un processeur d’extension HTTP ou gRPC, ou d’un processeur de détection de mouvement.
* Récepteur IoT Hub
   * Ne peut pas être immédiatement en aval d’une source IoT Hub.

### <a name="supported-cameras"></a>Caméras prises en charge
Vous ne pouvez utiliser que des caméras IP qui prennent en charge le protocole RTSP. Vous trouverez les caméras IP qui prennent RTSP en charge dans la page des [produits conformes ONVIF](https://www.onvif.org/conformant-products). Recherchez les appareils conformes aux profils G, S ou T.

Vous devez configurer ces caméras pour utiliser la vidéo H.264 et l’audio AAC. Les autres codecs ne sont actuellement pas pris en charge.

Video Analyzer prend en charge RTSP uniquement avec des [flux RTP entrelacés](https://datatracker.ietf.org/doc/html/rfc2326#section-10.12). Dans ce mode, le trafic RTP est traité par tunnel par le biais de la connexion TCP RTSP. Le trafic RTP sur UDP n’est pas pris en charge. 

### <a name="support-for-video-ai"></a>Prise en charge de l’intelligence artificielle vidéo
Les processeurs d’extension HTTP ou gRPC prennent uniquement en charge l’envoi de données d’image ou de vidéo avec un module d’IA externe. Ainsi, l’exécution de l’inférence sur des données audio n’est pas prise en charge. Par conséquent, les nœuds de processeur dans les topologies de pipeline qui ont un nœud source RTSP parmi leur `inputs` utilisent également une propriété `outputSelectors` pour s’assurer que seules des vidéos sont transmises au processeur. Pour un exemple, regardez cette [topologie](https://github.com/Azure/video-analyzer/blob/main/pipelines/live/topologies/evr-grpcExtension-video-sink/topology.json).

## <a name="quotas-and-limitations---live-and-batch-pipeline"></a>Quotas et limitations - pipeline en direct et par lot

Cette section énumère les quotas et limitations des pipelines cloud Video Analyzer. 

### <a name="maximum-number-of-pipeline-topologies"></a>Nombre maximal de topologies de pipeline 

Au plus 5 topologies de pipeline par compte Video Analyzer sont prises en charge. 

### <a name="limits-on-concurrent-activation-of-live-pipelines"></a>Limites concernant l’activation simultanée des pipelines en direct 

Au plus 10 pipelines en direct peuvent être activés sur une fenêtre de temps de 5 minutes et au maximum 10 de ces pipelines peuvent être dans un état `Activating` à tout moment.

Ces limites ne s’appliquent pas aux travaux de pipeline.  

### <a name="maximum-live-pipelines-per-topology"></a>Nombre maximal de pipelines en direct par topologie  

Au plus 50 pipelines en direct par topologie sont pris en charge.  

### <a name="concurrent-low-latency-streaming-sessions"></a>Sessions simultanées de streaming à faible latence  

Pour chaque pipeline en direct actif, il peut y avoir au plus une application cliente affichant le [flux à faible latence](playback-recordings-how-to.md#low-latency-streaming). Si un autre client tente de se connecter, la demande est refusée.  

### <a name="limitations-on-designing-pipeline-topologies"></a>Limitations concernant la conception de topologies de pipeline 

Voici les différents nœuds qui peuvent être connectés les uns aux autres dans une topologie de pipeline et leurs limitations : 

* Source RTSP
   * Une seule source RTSP est autorisée par topologie de pipeline.
* Source vidéo
   * Une seule source vidéo est autorisée par topologie de pipeline.
   * Peut uniquement être utilisée dans les topologies de pipeline du [genre lot](pipeline.md#batch-pipeline). Elle peut uniquement accepter une ressource vidéo de type `archive`. 
* Processeur d’encodeur 
   * Un seul processeur d’encodeur est autorisé par topologie de pipeline.
   * Peut uniquement être utilisé dans les topologies de pipeline du [genre lot](pipeline.md#batch-pipeline), et il doit être immédiatement en amont du nœud du récepteur vidéo dans ces topologies.
   * Il prend uniquement en charge l’encodage vidéo avec le codec H.264 et audio avec le codec AAC.
   * Permet à l’utilisateur de spécifier des propriétés d’encodage lors de la conversion de la vidéo enregistrée dans le format souhaité pour le traitement en aval. Deux types : (a) Valeur système prédéfinie (b) Valeur prédéfinie personnalisée. Les configurations autorisées pour chaque présélection sont répertoriées dans le tableau ci-dessous : 
     
     | Configuration       | Présélection du système        | Valeur prédéfinie personnalisée |
     | -----------         | -----------          |----------- |
     | Vitesse de transmission en kbits/s de l’encodeur vidéo      | identique à la source      | 200 à 16 000 kbits/s |
     | Fréquence d’images       | identique à la source      | 0 à 300 |
     | Hauteur    | identique à la source        | 1 à 4 320 |
     | Largeur    | identique à la source       | 1 à 8 192 |
     | Mode   | Remplir        | Pad, PreserveAspectRatio, Stretch |     
     | Vitesse de transmission en kbits/s de l’encodeur audio  | identique à la source        | Valeurs autorisées : 96, 112, 128, 160, 192, 224, 256 |
     
* Récepteur vidéo 
   *  Un seul récepteur vidéo est autorisé par topologie de pipeline.
   *  Doit être immédiatement en aval de la source RTSP ou du nœud de processeur d’encodeur. 
   *  Lorsqu’il est utilisé dans une topologie de pipeline du genre lot, il génère un fichier MP4 comme sortie.

### <a name="support-for-batch-video-export"></a>Prise en charge de l’exportation de vidéos par lot 

* Segmenter la sélection 
   * La séquence de temps, qui correspond à l’horodatage de début et de fin de la partie de la vidéo archivée à exporter, doit être spécifiée en heure UTC. 
   * L’étendue maximale de la séquence de temps (horodatage de fin - horodatage de début) doit être inférieure ou égale à 24 heures. 

### <a name="supported-cameras"></a>Caméras prises en charge

Vous ne pouvez utiliser que des caméras IP qui prennent en charge le protocole RTSP. Vous trouverez les caméras IP qui prennent RTSP en charge dans la page des [produits conformes ONVIF](https://www.onvif.org/conformant-products). Recherchez les appareils conformes aux profils G, S ou T. 

Vous devez configurer ces caméras pour utiliser la vidéo H.264 et l’audio AAC. Les autres codecs ne sont actuellement pas pris en charge. La vitesse de transmission de l’encodage vidéo doit être comprise entre 500 et 3 000 kbit/s.Si la vitesse de transmission d’ingestion exacte est supérieure à ce seuil, l’ingestion est déconnectée et reconnectée avec un backoff exponentiel.

Video Analyzer prend en charge RTSP uniquement avec des [flux RTP entrelacés](https://datatracker.ietf.org/doc/html/rfc2326#section-10.12). Dans ce mode, le trafic RTP est traité par tunnel par le biais de la connexion TCP RTSP. Le trafic RTP sur UDP n’est pas pris en charge. 

### <a name="support-for-video-ai"></a>Prise en charge de l’intelligence artificielle vidéo 

L’analyse de la vidéo en direct ou enregistrée n’est pas prise en charge actuellement.

## <a name="quotas-and-limitations---service"></a>Quotas et limitations – Service

Cette section énumère les quotas et limitations du compte Video Analyzer.

### <a name="limitations-on-service-operations-at-preview"></a>Limitations relatives aux opérations de service en préversion

Au moment de mettre en production la préversion, le service Video Analyzer ne prend pas en charge les fonctionnalités suivantes :

* possibilité de migrer le compte Media Services d’un abonnement à un autre sans interruption ;
* possibilité d’utiliser plusieurs comptes de stockage avec le compte ;
* possibilité d’utiliser un compte de stockage dans une région différente de celle du compte Video Analyzer.

### <a name="limits-on-video-resources"></a>Limites des ressources vidéo
Au moment de mettre en production la préversion, chaque compte Video Analyzer peut avoir au maximum 10 000 ressources vidéo. Si vous avez besoin d’une limite supérieure, ouvrez un ticket de support dans le portail Azure.

### <a name="limits-on-access-policies"></a>Limites des stratégies d’accès
Au moment de mettre en production la préversion, chaque compte Video Analyzer peut avoir au maximum 20 stratégies d’accès.

### <a name="limits-on-registered-edge-modules"></a>Limites des modules Edge inscrits
Au moment de mettre en production la préversion, chaque compte Video Analyzer peut avoir au maximum 1 000 modules Edge inscrits. Si vous avez besoin d’une limite supérieure, ouvrez un ticket de support dans le portail Azure.

### <a name="limits-on-client-api-calls"></a>Limites des appels d’API client
Les limites suivantes s’appliquent aux API client au moment de mettre en production la préversion :

* Jusqu’à 50 demandes en 10 secondes
* Jusqu’à 600 demandes par heure

## <a name="limitations---video-analyzer-player-widgets"></a>Limitations – Widgets de lecteur Video Analyzer

Au moment de mettre en production la préversion, la lecture sur des appareils iOS n’est pas prise en charge.

## <a name="next-steps"></a>Étapes suivantes

[Vue d'ensemble](overview.md)
