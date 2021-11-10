---
title: Terminologie d’Azure Video Analyzer
description: Cette article fournit une vue d’ensemble de la terminologie d’Azure Video Analyzer.
ms.service: azure-video-analyzer
ms.topic: conceptual
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 9010ae81606bcd84d37debc4c702a0bc53e328dd
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131846954"
---
# <a name="azure-video-analyzer-terminology"></a>Terminologie d’Azure Video Analyzer

Cet article fournit une vue d’ensemble de la terminologie relative à [Azure Video Analyzer](overview.md).

## <a name="pipeline-topology"></a>Topologie de pipeline

Une [topologie de pipeline](pipeline.md) vous permet de définir l’emplacement à partir duquel les médias doivent être capturés, la manière dont ils doivent être traités et où les résultats doivent être livrés. Il vous permet de définir un pipeline constitué de sources, de processeurs et de nœuds récepteurs grâce auxquels vous pouvez créer des applications d’analytique vidéo en direct. 

## <a name="rtsp"></a>RTSP

[RTSP](https://tools.ietf.org/html/rfc2326) (Real-Time Streaming Protocol) fait référence au protocole de streaming en temps réel. Il s’agit d’un protocole au niveau de l’application pour contrôler la remise des données avec des propriétés en temps réel. Le protocole RTSP fournit un cadre extensible qui permet une remise contrôlée à la demande de données en temps réel, comme des données audio et vidéo. Video Analyzer [prend en charge](pipeline.md#rtsp-source) la capture, l’analyse et l’enregistrement de vidéos à partir de caméras IP prenant en charge le protocole RTSP.

## <a name="vms"></a>VMS

[VMS](https://en.wikipedia.org/wiki/Video_management_system) (Video Management System) fait référence à un système de gestion vidéo. De tels systèmes sont utilisés pour configurer et contrôler des caméras de vidéosurveillance, ainsi que pour capturer et enregistrer des vidéos à partir de ces caméras. Ces systèmes fournissent également des applications clientes pour lire les vidéos enregistrées.

## <a name="recording"></a>Enregistrement

Dans le contexte d’un système de gestion vidéo pour caméras de sécurité, l’enregistrement vidéo fait référence au processus de capture de vidéos à partir des caméras, et à leur stockage afin de les regarder ultérieurement via des applications mobiles et de navigateur. L’enregistrement vidéo peut être classé dans la catégorie de l’[enregistrement vidéo en continu](continuous-video-recording.md) et l’[enregistrement vidéo basé sur des événements](event-based-video-recording-concept.md). Ces catégories sont expliquées plus en détail dans la page relative au concept d’[enregistrement vidéo](video-recording.md).

## <a name="streaming"></a>Diffusion en continu

Vous pouvez utiliser le service Video Analyzer pour diffuser en continu des enregistrements vidéo à des clients, en utilisant des protocoles de diffusion multimédia en continu basés sur HTTP, comme [HTTP Live Streaming (TLS)](https://developer.apple.com/streaming/) et [MPEG-DASH](https://dashif.org/about/). Vous pouvez utiliser les [widgets du lecteur Azure Video Analyzer](https://github.com/Azure/video-analyzer/blob/main/widgets/readme.md) (composants web) pour lire des ressources vidéo. Par ailleurs, HLS est pris en charge par des lecteurs web comme [JW Player](https://www.jwplayer.com/), [hls.js](https://github.com/video-dev/hls.js/), [VideoJS](https://videojs.com/), [Shaka Player de Google](https://github.com/google/shaka-player), et vous pouvez effectuer un rendu en mode natif dans des applications mobiles avec [Exoplayer](https://github.com/google/ExoPlayer) d’Android et [AV Foundation](https://developer.apple.com/av-foundation/) d’iOS. MPEG-DASH est également pris en charge par [une série de clients mentionnés sur cette page](https://dashif.org/tools/clients/).

## <a name="exporting"></a>Exportation

Dans le contexte d’une machine virtuelle pour les caméras de sécurité, l’exportation vidéo se réfère au processus qui consiste à prendre une ou plusieurs parties sélectionnées d’un enregistrement vidéo et à créer un fichier distinct, en général un fichier [MP4](https://en.wikipedia.org/wiki/MPEG-4_Part_14), qui peut ensuite être partagé en externe. Par exemple, l’enregistrement vidéo peut contenir une séquence dans laquelle l’action rapide du personnel a empêché un incident de sécurité. Cette partie de l’enregistrement peut être exportée vers un fichier MP4 pour être utilisée dans les sessions de formation ultérieures.

## <a name="video"></a>Vidéo

Les vidéos sont des ressources de votre compte Video Analyzer qui activent des fonctionnalités de machines virtuelles telles que l’enregistrement, la lecture, la diffusion en continu et l’exportation. Les vidéos peuvent être enregistrées à partir de caméras RTSP ou créées en exportant des parties d’une vidéo enregistrée existante. Les vidéos enregistrées peuvent être diffusées en continu et affichées à l’aide du widget de lecteur Video Analyzer ou d’autres lecteurs compatibles. Les vidéos exportées peuvent être téléchargées sous forme de fichiers MP4.

Lorsque vous utilisez Video Analyzer pour l’enregistrement à partir d’une caméra RTSP, vous devez associer cette ressource vidéo à cette caméra. Vous pouvez enregistrer en continu la vidéo à partir de la caméra vers la ressource vidéo, ou vous pouvez enregistrer sporadiquement en fonction des événements : Video Analyzer prend en charge l’ajout de données à une ressource vidéo existante. Il faut que les propriétés de la caméra RTSP (résolution, fréquence d’images, etc.) restent inchangées. Si vous avez besoin de modifier les paramètres de la caméra, vous devez passer à l’enregistrement dans une nouvelle ressource vidéo.

Lorsque vous créez un compte Video Analyzer, vous devez y associer un compte de stockage Azure. Les vidéos enregistrées et exportées sont stockées en tant qu’objets BLOB dans un conteneur du compte de stockage. Tout le contenu associé à de telles ressources vidéo est stocké sous la forme d’objets blob dans les conteneurs correspondants, tandis que Video Analyzer contient les métadonnées (telles qu’un nom, une description, une heure de création).

## <a name="grpc"></a>gRPC

[gRPC](https://grpc.io/docs/guides/) est un Framework d’appel de procédure distante (RPC) indépendant du langage et de haute performance. Il utilise des schémas structurés basés sur une session via [Protocol Buffers 3](https://developers.google.com/protocol-buffers/docs/proto3) en tant que format d’échange de messages sous-jacent pour la communication.

## <a name="low-latency-streaming"></a>Diffusion en continu à faible latence

La diffusion vidéo en continu à faible latence est une fonctionnalité utile d’un système de machines virtuelles. Video Analyzer peut diffuser des vidéos en direct avec un délai d’environ 2 secondes. La latence signifie le délai entre le moment où un événement se produit avant l’appareil photo et le moment où cet événement se produit sur un périphérique de lecture.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez les [pipelines](pipeline.md).
