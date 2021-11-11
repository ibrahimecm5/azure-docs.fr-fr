---
title: Notes de publication d’Azure Video Analyzer - Azure
description: Cette rubrique fournit des notes de publication sur les versions, les améliorations, les correctifs de bogues et les problèmes connus d’Azure Video Analyzer.
ms.topic: conceptual
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 89ab651416237e06343f950d2a9202f082555e58
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131556442"
---
# <a name="azure-video-analyzer-release-notes"></a>Notes de publication d’Azure Video Analyzer

>Soyez notifié de la disponibilité des mises à jour sur cette page en faisant un copier-coller de cette URL : `https://docs.microsoft.com/api/search/rss?search=%22Azure+Video+Analyzer+on+IoT+Edge+release+notes%22&locale=en-us` dans votre lecteur de flux RSS.

Cet article vous fournit des informations sur :

* Versions les plus récentes
* Problèmes connus
* Résolution des bogues
* Fonctionnalités dépréciées

<hr width=100%>

## <a name="november-2-2021"></a>2 novembre 2021

Cette version est une mise à jour du module de périphérie Video Analyzer et du service Video Analyzer. La balise de version du module de périphérie est :

```
mcr.microsoft.com/media/video-analyzer:1.1.0
```

> [!NOTE]
> Dans les démarrages rapides et les tutoriels, les manifestes de déploiement utilisent une balise 1 (video-analyzer:1). Par conséquent, le simple redéploiement de tels manifestes doit mettre à jour le module sur vos appareils périphériques quand de nouvelles balises sont publiées.

La version d’API ARM du service Video Analyzer est :

```
2021-11-01-preview
```

### <a name="new-functionalities"></a>Nouvelles fonctionnalités

* Quand vous utilisez Video Analyzer avec le service IA [Vision par ordinateur pour l’analyse spatiale](edge/computer-vision-for-spatial-analysis.md) de Cognitive Services, vous pouvez générer et voir de nouveaux insights tels que la vitesse, l’orientation et le cheminement des personnes dans la vidéo en direct.
* Vous pouvez [découvrir les appareils ONVIF](edge/camera-discovery.md) dans le sous-réseau local de votre appareil de périphérie.
* Vous pouvez [capturer et enregistrer de la vidéo en direct directement dans le cloud](cloud/connect-cameras-to-cloud.md).
  * Vous pouvez utiliser le [streaming à faible latence](playback-recordings-how-to.md#low-latency-streaming) pour voir la vidéo en direct à partir de la caméra RTSP avec des latences de bout en bout d’environ 2 secondes.
  * Vous pouvez implémenter le [contrat Video Analyzer IoT PnP](cloud/connect-devices.md) sur votre caméra RTSP pour activer la capture vidéo à partir de votre appareil vers le service Video Analyzer.
* Vous pouvez [exporter la partie souhaitée de votre vidéo enregistrée](cloud/export-portion-of-video-as-mp4.md) vers un fichier MP4.
* Vous pouvez spécifier, pour toute vidéo enregistrée, une stratégie de conservation avec laquelle le service supprime régulièrement le contenu antérieur à la période spécifiée en nombre de jours.
* Les vidéos enregistrées à l’aide du module de périphérie Video Analyzer peuvent inclure des [images d’aperçu](edge/enable-video-preview-images.md) ou des miniatures de façon régulière, pour une meilleure expérience de navigation.

### <a name="known-issues"></a>Problèmes connus
* Lors de l’utilisation d’une diffusion en continu à faible latence, un seul client peut être connecté au service à la fois
* Lors de l’utilisation d’un module d’extension gRPC pour l’inférence avec la mémoire partagée, le module périphérique Video Analyzer et le module d’extension doivent tous deux être exécutés dans les mêmes [groupe et utilisateur](https://docs.docker.com/engine/reference/builder/#user)

## <a name="october-1-2021"></a>1er octobre 2021
Le service Video Analyzer est désormais disponible (en version préliminaire) dans la région Australie Est. Pour obtenir les informations les plus récentes sur la disponibilité, cliquez [ici](https://azure.microsoft.com/global-infrastructure/services/?products=video-analyzer&regions=all).

Un nouvel article a été ajouté pour décrire comment vous pouvez [incorporer le widget Video Analyzer dans Power BI](embed-player-in-power-bi.md).

## <a name="june-3-2021"></a>3 juin 2021

Cette balise de version pour l’actualisation de juillet 2021 du module est

```
     mcr.microsoft.com/media/video-analyzer:1.0.1
```
> [!NOTE]
> Dans les démarrages rapides et les tutoriels, les manifestes de déploiement utilisent une balise 1 (video-analyzer:1). Par conséquent, le simple redéploiement de tels manifestes doit mettre à jour le module sur vos appareils périphériques quand de nouvelles balises sont publiées.

### <a name="module-updates"></a>Mises à jour de module
* Prend en charge les caractères unicode dans les informations d’identification pour la connexion à une caméra RTSP
* Mises à jour pour activer les journaux détaillés en mode débogage

<hr width=100%>

## <a name="may-25-2021"></a>25 mai 2021

Cette version est la première préversion publique d’Azure Video Analyzer. La balise de version est

```
mcr.microsoft.com/media/video-analyzer:1.0.0
```

> [!NOTE]
> Dans les démarrages rapides et les tutoriels, les manifestes de déploiement utilisent une balise 1 (video-analyzer:1). Par conséquent, le simple redéploiement de tels manifestes doit mettre à jour le module sur vos appareils périphériques quand de nouvelles balises sont publiées.

### <a name="supported-functionalities"></a>Fonctionnalités prises en charge

* Analyser les flux vidéo en direct à l’aide des modules d’intelligence artificielle de votre choix et enregistrer éventuellement une vidéo sur le périphérique ou dans le cloud
* Enregistrer une vidéo avec des métadonnées d’inférence dans le cloud
* Déclencher des événements lorsque des objets franchissent une ligne à l’aide de votre propre modèle de détection d’objets
* Suivre les objets détectés avec votre propre modèle de détection 
* Utiliser les widgets du lecteur Video Analyzer (composants web) pour lire la vidéo et les métadonnées d’inférence enregistrées
* Déployer et configurer le module via le hub IoT à l’aide de Portail Azure ou Visual Studio Code
<!--REDIRECT* Manage [pipeline topologies](pipeline.md#pipeline-topologies) remotely or locally using [direct method](direct-methods.md) calls-->

## <a name="next-steps"></a>Étapes suivantes

[Vue d'ensemble](overview.md)
