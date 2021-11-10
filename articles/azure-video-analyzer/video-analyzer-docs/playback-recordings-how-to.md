---
title: Affichage de vidéos – Azure Video Analyzer
description: Vous pouvez utiliser Azure Video Analyzer pour l’enregistrement vidéo continu qui vous permet d’enregistrer des vidéos dans le cloud pendant des semaines ou des mois. Vous pouvez également limiter votre enregistrement aux clips qui présentent un intérêt, par le biais de l’enregistrement basé sur les événements. En outre, lorsque vous utilisez le service Video Analyzer pour capturer des vidéos à partir de caméras, vous pouvez diffuser ces vidéos à mesure qu’elles sont capturées. Cet article explique comment afficher ces vidéos.
ms.service: azure-video-analyzer
ms.topic: how-to
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 552daea0a5a19c7c9e11aca9b47d61ea112a42b3
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131846973"
---
# <a name="viewing-of-videos"></a>Affichage de vidéos

## <a name="suggested-pre-reading"></a>Lecture préalable suggérée

* [Ressource vidéo Video Analyzer](terminology.md#video)
* [Enregistrement vidéo en continu](continuous-video-recording.md)
* [Enregistrement de vidéo basé sur les événements](event-based-video-recording-concept.md)

## <a name="background"></a>Arrière-plan  

Vous pouvez créer des [ressources vidéo](terminology.md#video) dans votre compte Azure Video Analyzer en effectuant un enregistrement à partir d’une caméra RTSP, ou en exportant une partie d’un tel enregistrement. Si vous créez des [machines virtuelles](terminology.md#vms) à l’aide d’API Video Analyzer, cet article vous aidera à comprendre comment afficher les vidéos. Après avoir lu cet article, nous vous conseillons de consulter l’article sur les [stratégies d’accès](access-policies.md) et celui sur le [widget de lecteur Video Analyzer](player-widget.md). 

Si vous évaluez les fonctionnalités de Video Analyzer, vous pouvez consulter l’un des [démarrages rapides](edge/detect-motion-record-video-clips-cloud.md) ou [didacticiels](edge/use-continuous-video-recording.md), et utiliser le portail Azure pour afficher les vidéos.
<!-- TODO - add a section here about 1P/3P SaaS and how to use widgets to allow end users to view videos without talking to ARM APIs -->

## <a name="creating-videos"></a>Création de vidéos

Voici quelques façons de créer des vidéos à l’aide du module de périphérie Video Analyzer :

* Enregistrer en [continu](continuous-video-recording.md) (CVR) à partir d’une caméra RTSP, pendant des semaines voire davantage.
* Enregistrer uniquement des séquences intéressantes via un [enregistrement de vidéo basé sur les événements](event-based-video-recording-concept.md) (EVR). 
 
Vous pouvez également utiliser le service Video Analyzer pour créer des vidéos à l’aide d’un enregistrement vidéo en continu. Vous pouvez aussi utiliser le service pour créer une vidéo en exportant une partie d’un enregistrement vidéo. Une telle vidéo contient un fichier téléchargeable (au format de fichier MP4).

## <a name="accessing-videos"></a>Accès aux vidéos

Vous pouvez interroger l’API ARM [`Videos`](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/videoanalyzer/resource-manager/Microsoft.Media/preview/2021-11-01-preview/Videos.json) pour afficher les propriétés de la ressource vidéo. Video Analyzer vous permet également de créer des applications dans lesquelles les utilisateurs finaux peuvent parcourir et afficher des vidéos sans passer par ARM. Comme indiqué dans l’article sur les [points de terminaison publics](access-public-endpoints-networking.md), vous avez accès à des API clientes via lesquelles vous pouvez interroger les propriétés des vidéos. Ces API sont exposées via un point de terminaison d’API client, que vous pouvez trouver dans la section Vue d’ensemble du panneau Video Analyzer dans le portail Azure, ou via l’API ARM [`VideoAnalyzers`](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/videoanalyzer/resource-manager/Microsoft.Media/preview/2021-11-01-preview/VideoAnalyzers.json). Vous pouvez contrôler l’accès à ce point de terminaison via des [stratégies d’accès](access-policies.md), et l’[article sur le widget de lecteur Video Analyzer](player-widget.md) explique comment procéder.

## <a name="determining-that-a-video-recording-is-ready-for-viewing"></a>Détermination du fait qu’un enregistrement vidéo est prêt pour l’affichage

Si votre ressource vidéo représente l’enregistrement à partir d’une caméra RTSP, vous pouvez [diffuser ce contenu](terminology.md#streaming) une fois l’enregistrement terminé, ou bien pendant l’enregistrement. Cela est signalé à l’aide de l’indicateur `canStream` qui sera défini sur `true` pour la ressource vidéo. Notez que ces vidéos auront la valeur `type` définie sur `archive`, et que l’URL de lecture ou de diffusion en continu est retournée dans `archiveBaseUrl`. 

Lorsque vous exportez une partie d’un enregistrement vidéo dans un fichier MP4, la ressource vidéo obtenue a la `type` valeur définie sur `file`, et est disponible en lecture ou téléchargement une fois le travail d’exportation de la vidéo terminé. L’URL de lecture ou de téléchargement de tels fichiers est retournée dans `downloadUrl`.
   > [!NOTE]
   > Les URL ci-dessus requièrent un [jeton du porteur](./access-policies.md#creating-a-token). Pour plus d’informations, consultez la documentation du [widget de lecteur Video Analyzer](player-widget.md).

## <a name="recording-and-playback-latencies"></a>Latences d’enregistrement et de lecture

Lorsque vous utilisez un module de périphérie Video Analyzer pour enregistrer sur une ressource vidéo, vous spécifiez une [propriété `segmentLength`](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/videoanalyzer/data-plane/VideoAnalyzer.Edge/preview/1.0.0/AzureVideoAnalyzer.json)dans votre topologie de pipeline, qui indique au module d’agréger une durée minimale de vidéo (en secondes) avant de l’écrite dans le cloud. Par exemple, si `segmentLength` est défini sur 300, le module accumule 5 minutes de vidéo avant de charger un « bloc » de 5 minutes, puis passe en mode accumulation pendant les 5 minutes suivantes avant de charger à nouveau. L’augmentation de la valeur de `segmentLength` présente l’avantage de réduire les coûts de transaction du service Stockage Azure, car le nombre de lectures et d’écritures n’est pas plus fréquent qu’une fois toutes les `segmentLength` secondes. Si vous utilisez le service Video Analyzer, la topologie de pipeline a la même [propriété `segmentLength`](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/videoanalyzer/resource-manager/Microsoft.Media/preview/2021-11-01-preview/PipelineTopologies.json).

Par conséquent, la diffusion en continu de la vidéo à partir de votre compte Video Analyzer sera retardée d’au moins autant de temps. 

Un autre facteur qui détermine la latence de bout en bout (retard entre le moment où un événement se produit devant la caméra et le moment où il est lu sur un périphérique de lecture) est la durée du groupe d’images [GOP](https://en.wikipedia.org/wiki/Group_of_pictures). Comme l’explique [la réduction du retard des streams en direct par l’utilisation de trois techniques simples](https://medium.com/vrt-digital-studio/reducing-the-delay-of-live-streams-by-using-3-simple-techniques-e8e028b0a641), plus la durée du GOP est longue, plus la latence est importante. Il est courant d’utiliser dans des scénarios de surveillance et de sécurité des caméras IP configurées pour utiliser des GOP de plus de 30 secondes. Cela a un impact important sur la latence globale.

## <a name="low-latency-streaming"></a>Diffusion en continu à faible latence

Lorsque vous utilisez le service Video Analyzer pour capturer et enregistrer des vidéos à partir de caméras RTSP, vous pouvez afficher la vidéo avec des latences d’environ 2 secondes en utilisant une [diffusion en continu à faible latence](terminology.md#low-latency-streaming). Le service met à disposition un tunnel WebSocket via lequel un lecteur basé sur RTSP, tel le [widget de lecteur Video Analyzer](player-widget.md), peut recevoir des vidéos à l’aide du [protocole RTSP](https://datatracker.ietf.org/doc/html/rfc7826.html). Notez que la latence globale dépend de la bande passante réseau entre la caméra et le cloud, ainsi qu’entre le cloud et l’appareil de lecture, et aussi de la puissance de traitement de l’appareil de lecture. L’URL de diffusion en continu à faible latence est retournée dans `rtspTunnelUrl`.

   > [!NOTE]
   > L’URL ci-dessus requiert un [jeton du porteur](./access-policies.md#creating-a-token). Pour plus d’informations, consultez la documentation du [widget de lecteur Video Analyzer](player-widget.md).

## <a name="video-analyzer-player-widget"></a>Widget du lecteur de Video Analyzer
Le service Video Analyzer offre les capacités nécessaires pour diffuser des flux via les protocoles HLS, MPEG-DASH ou RTSP à des appareils de lecture (clients). Vous pouvez utiliser le [widget de lecteur de Video Analyzer](player-widget.md) pour obtenir les URL et le jeton d’autorisation de contenu appropriés, et utiliser ceux-ci dans des applications clientes pour lire la vidéo et les métadonnées d’inférence.

Vous pouvez installer le widget de lecteur Video Analyzer pour afficher des vidéos. Vous pouvez installer le widget l’aide de `npm` ou de `yarn`, ce qui vous permet de l’inclure dans votre propre application côté client. Exécutez l’une des commandes suivantes pour inclure le widget dans votre propre application :

NPM :
```
npm install –-save @azure/video-analyzer-widgets
```
YARN :
```
yarn add @azure/video-analyzer-widgets 
```
Vous pouvez également incorporer un script prédéfini existant en ajoutant type="module" à l’élément de script référençant l’emplacement prédéfini en suivant l’exemple suivant :

```
<script async type="module" src="https://unpkg.com/@azure/video-analyzer-widgets"></script> 
``` 

## <a name="viewing-video-with-inference-results"></a>Affichage de vidéo avec résultats d’inférence
Lorsque vous enregistrez une vidéo à l’aide du module de périphérie Video Analyzer, si votre [pipeline](pipeline.md) utilise l’intelligence artificielle pour générer des résultats d’inférence, vous pouvez enregistrer ceux-ci en même temps que la vidéo. Lorsque vous affichez la vidéo, le widget de lecteur Video Analyzer peut superposer les résultats sur la vidéo. Consultez ce [didacticiel](edge/record-stream-inference-data-with-video.md) pour plus d’informations.

## <a name="next-steps"></a>Étapes suivantes

* [Comprendre les stratégies d’accès](access-policies.md)
* [Utiliser le widget de lecteur Video Analyzer](player-widget.md)
* [Enregistrement vidéo continu en périphérie](edge/use-continuous-video-recording.md)
* [Enregistrement vidéo continu dans le cloud](cloud/get-started-livepipelines-portal.md)
