---
title: Connexion des caméras au service Azure Video Analyzer
description: Cet article explique comment connecter des caméras directement au service Azure Video Analyzer.
ms.service: azure-video-analyzer
ms.topic: conceptual
ms.date: 11/01/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 973db00ad3d8b7d0581d74b9de602322c20ac7b3
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096762"
---
# <a name="connect-cameras-to-the-cloud"></a>Connecter des caméras au cloud

[!INCLUDE [header](includes/cloud-env.md)]

Le service Azure Video Analyzer permet aux utilisateurs de connecter directement les caméras RTSP au cloud dans l’ordre de capture et d’enregistrement vidéo, à l’aide de [pipelines en direct](../pipeline.md). Cela permet de réduire la charge de calcul sur un appareil de périphérie ou d’éliminer complètement la nécessité d’un appareil de périmètre. Le service Video Analyzer prend actuellement en charge trois méthodes différentes pour connecter des caméras au cloud : la connexion via un adaptateur de périphérique distant, la connexion derrière un pare-feu à l’aide d’une commande IoT PnP et la connexion via Internet sans pare-feu.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/connect-cameras-to-cloud/connect-cameras-to-cloud.svg" alt-text="3 différentes méthodes pour connecter des caméras au cloud":::

## <a name="connect-via-a-remote-device-adapter"></a>Connecter via un adaptateur pour appareil mobile distant

Vous pouvez déployer le module de périphérie de Video Analyzer sur un appareil IoT Edge sur le même réseau (privé) que les caméras RTSP et connecter le périphérique à Internet. Le module de périphérie peut maintenant être configuré comme un *adaptateur* qui permet au service Video Analyzer de se connecter aux *appareils distants* (caméras). Le module de périphérie agit comme une [passerelle transparente](../../../iot-edge/iot-edge-as-gateway.md) pour le trafic vidéo entre les caméras RTSP et le service Video Analyzer. Cette approche est utile dans les scénarios suivants :

* Lorsque les caméras/appareils doivent être protégés contre l’exposition à Internet
* Lorsque les caméras/appareils ne disposent pas de la fonctionnalité permettant de se connecter à IoT Hub indépendamment
* Lorsque l’alimentation, l’espace ou d’autres considérations autorisent uniquement le déploiement d’un périphérique léger sur site

Le module périphérique Video Analyzer ne joue pas le rôle de passerelle transparente pour la messagerie et la télémétrie de la caméra à IoT Hub, mais uniquement de passerelle transparente pour la vidéo.

## <a name="connect-behind-a-firewall-using-an-iot-pnp-device-implementation"></a>Connecter derrière un pare-feu à l’aide d’une implémentation d’appareil PnP IoT

Cette méthode permet aux caméras ou appareils RTSP de se connecter à Video Analyzer derrière un pare-feu à l’aide de l’[interface de commande Plug-and-Play IoT](../../../iot-develop/overview-iot-plug-and-play.md) et élimine la nécessité d’un périphérique. Cette méthode requiert l’installation et l’exécution d’une implémentation d’appareil PnP IoT appropriée sur des caméras ou des appareils. Vous trouverez [ici](connect-devices.md) des informations sur la façon de connecter un appareil compatible de n’importe quel fabricant.

## <a name="connect-over-the-internet-no-firewall"></a>Connecter via internet (sans pare-feu)

Cette méthode ne doit être utilisée que pour les exercices de preuve de concept contrôlés, où il peut être permis d’autoriser le service Video Analyzer à accéder à l’appareil via Internet, sans pare-feu. 

Un cas d’usage associé se présente quand un module est déployé sur une machine virtuelle Azure pour simuler une caméra RTSP, comme décrit dans ce guide de [démarrage rapide](get-started-livepipelines-portal.md).


## <a name="next-steps"></a>Étapes suivantes

- Suivez [ce guide pratique](use-remote-device-adapter.md) pour connecter des caméras via un adaptateur pour appareil mobile à distance
- Suivez [ce guide pratique](connect-devices.md) pour plus d’informations sur la connexion des appareils à l’aide d’une implémentation IoT PnP
- Suivez [ce guide de démarrage rapide](get-started-livepipelines-portal.md) pour connecter des caméras à Internet
