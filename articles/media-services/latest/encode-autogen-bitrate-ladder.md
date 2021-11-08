---
title: Encoder des vidéos avec l’encodeur standard dans Azure Media Services
description: Cette rubrique montre comment utiliser l’encodeur standard dans Media Services pour encoder une vidéo d’entrée avec une échelle de débit générée automatiquement en fonction de la résolution d’entrée et du débit.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/21/2021
ms.author: inhenkel
ms.custom: seodec18
ms.openlocfilehash: 058d80b85a9a669612c64de6f0f705f6d605e419
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131065016"
---
#  <a name="encode-with-an-auto-generated-bitrate-ladder"></a>Encoder avec une échelle de débit générée automatiquement

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="overview"></a>Vue d’ensemble

Cet article explique comment utiliser l’encodeur standard dans Media Services pour encoder une vidéo d’entrée dans une échelle de débit générée automatiquement (paires débit-résolution) en fonction de la résolution d’entrée et du débit. Ce paramètre d’encodeur intégré, ou présélection, ne dépassera jamais la résolution d’entrée et le débit. Par exemple, si l’entrée est 720p à 3 Mbits/s, la sortie reste à 720p maximum et démarre à des vitesses inférieures à 3 Mbits/s.

### <a name="encoding-for-streaming"></a>Encodage pour la diffusion en continu

Lorsque vous utilisez le préréglage **AdaptiveStreaming** ou **H265AdaptiveStreaming** dans **Transform**, vous recevez une sortie appropriée pour la remise via des protocoles de diffusion en continu tels que TLS et DASH. Quand vous utilisez ce préréglage, l’encodeur détermine automatiquement le nombre de couches vidéo à générer, ainsi que le débit et la résolution. Le contenu de sortie contient des fichiers MP4 où les données audio encodées en AAC et soit une vidéo encodée en H. 264 (dans le cas de la présélection AdaptiveStreaming) ou en H. 265/HEVC (dans le cas de la présélection H265AdaptiveStreaming). Les fichiers MP4 de sortie ne sont pas entrelacés.

Pour voir un exemple d’utilisation de cette présélection, consultez [Diffuser un fichier](stream-files-dotnet-quickstart.md).

## <a name="output"></a>Output

Cette section présente trois exemples des couches vidéo de sortie produites par l’encodeur Media Services après encodage avec la présélection **AdaptiveStreaming** (H.264) ou la présélection **H265AdaptiveStreaming** (HEVC). Dans tous les cas, la sortie contient un fichier MP4 audio uniquement avec des données audio stéréo encodées à 128 Kbits/s.

### <a name="example-1"></a>Exemple 1
Une source avec une hauteur de « 1080 » et une fréquence d’images de « 29.970 » crée 6 couches vidéo :

|Couche|Hauteur|Largeur|Débit binaire (Kbits/s)|
|---|---|---|---|
|1|1080|1920|6780|
|2|720|1 280|3520|
|3|540|960|2210|
|4|360|640|1150|
|5|270|480|720|
|6|180|320|380|

### <a name="example-2"></a>Exemple 2
Une source avec une hauteur de « 720 » et une fréquence d’images de « 23.970 » crée 5 couches vidéo :

|Couche|Hauteur|Largeur|Débit binaire (Kbits/s)|
|---|---|---|---|
|1|720|1 280|2940|
|2|540|960|1850|
|3|360|640|960|
|4|270|480|600|
|5|180|320|320|

### <a name="example-3"></a>Exemple 3
Une source avec une hauteur de « 360 » et une fréquence d’images de « 29.970 » crée 3 couches vidéo :

|Couche|Hauteur|Largeur|Débit binaire (Kbits/s)|
|---|---|---|---|
|1|360|640|700|
|2|270|480|440|
|3|180|320|230|


## <a name="content-aware-encoding-comparison"></a>Comparaison du codage sensible au contenu

Les [préréglages d’encodage dépendant du contenu](./encode-content-aware-concept.md) offrent une meilleure solution sur les préréglages de diffusion en continu adaptative en analysant le contenu source avant de choisir le bon ensemble de vitesses de transmission et de résolution à utiliser dans l’échelle.
Il est recommandé de tester les [préréglages d’encodage prenant en charge le contenu](./encode-content-aware-concept.md) avant d’utiliser l’échelle statique et fixe fournie par les préréglages de diffusion en continu à débit adaptatif.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Diffuser un fichier en continu](stream-files-dotnet-quickstart.md)
>  [En utilisant les préréglages d’encodage dépendant du contenu](./encode-content-aware-concept.md)
> [Utilisation de l’encodage dépendant du contenu](./encode-content-aware-how-to.md)
