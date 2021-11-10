---
title: Gérer une stratégie de rétention avec Azure Video Analyzer
description: Cette rubrique explique comment gérer une stratégie de rétention avec Azure Video Analyzer.
ms.service: azure-video-analyzer
ms.topic: how-to
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 0bb6ad0f3b9cb5ddea876969da3b6429e2f2ba32
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131849384"
---
# <a name="manage-retention-policy-with-video-analyzer"></a>Gérer une stratégie de rétention avec Video Analyzer

Vous pouvez utiliser Azure Video Analyzer pour archiver du contenu pendant de longues périodes, de quelques secondes à plusieurs années de contenu vidéo provenant d’une source unique. Vous pouvez contrôler explicitement la stratégie de rétention de votre contenu, ce qui garantit que le contenu plus ancien est supprimé régulièrement. Vous pouvez appliquer différentes stratégies à différentes archives. Par exemple, vous pouvez conserver les 3 derniers jours d’enregistrements d’une caméra de surveillance de parking, et les 30 derniers jours d’enregistrements d’une caméra de surveillance située derrière le bureau d’un caissier.

## <a name="retention-period"></a>Période de rétention

Pour chaque [ressource vidéo](terminology.md#video) dans votre compte Video Analyzer, vous pouvez appliquer une stratégie de rétention. Lorsque vous spécifiez une période de rétention (comme décrit ci-dessous), le service supprime régulièrement le contenu antérieur à cette période. Si vous ne spécifiez pas de stratégie, le contenu est stocké indéfiniment.

La période de rétention est généralement définie dans les propriétés d’un nœud de récepteur vidéo lors de la création d’une topologie de pipeline. La propriété `retentionPeriod` de Microsoft dans la section `videoCreationProperties`. Comme son nom l’indique, la propriété est utilisée quand un pipeline actif est activé à l’aide de cette topologie et une nouvelle ressource vidéo créée. Ces propriétés de création ne sont pas utilisées si la ressource vidéo existe déjà (par exemple, lors de la réactivation d’un pipeline). Vous trouverez ci-dessous un exemple de la façon dont une stratégie de rétention de 3 jours peut être appliquée à toutes les caméras d’un parking.

```
{
  "@type": "#Microsoft.VideoAnalyzer.VideoSink",
  "name": "{nodeName}",         
  "videoName": "{nameForVideoResource}",
  "videoCreationProperties":
  {
    "title": "{titleForVideo}",
    "description": "{descriptionForVideo}",
    "segmentLength": "PT30S",
    "retentionPeriod": "P3D"
  },
}
```

Vous pouvez également définir ou mettre à jour la propriété `retentionPeriod` d’une ressource vidéo à l’aide du portail Azure ou via l’[API REST](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/videoanalyzer/resource-manager/Microsoft.Media/preview/2021-11-01-preview/Videos.json). Vous trouverez ci-dessous un exemple de définition d’une stratégie de rétention de 3 jours.

```
"archival":
{
   "retentionPeriod": "P3D",
},
```

## <a name="rules-and-limitations"></a>Règles et limitations

* Jusqu’à 24 heures peuvent s’écouler avant qu’une stratégie de rétention prenne effet
* Format : la propriété `retentionPeriod` prend la valeur de durée ISO 8601, qui doit être de plusieurs jours. Exemples de valeurs acceptables : P1D, P20D, P1M, P365D, P1Y, P5Y
    * La valeur minimale est P1D et la valeur maximale P5Y
    * Lorsque la valeur est Null, la stratégie de rétention est supprimée, et le contenu vidéo est stocké indéfiniment
* L’utilisation d’une stratégie de rétention occasionne des coûts de transaction de stockage Azure supplémentaires à mesure que le service localise et supprime des blobs

## <a name="next-steps"></a>Étapes suivantes

[Lecture des enregistrements](playback-recordings-how-to.md)
