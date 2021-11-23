---
title: Versions de la plateforme Azure Industrial IoT
description: Cet article fournit une vue d’ensemble des versions existantes de la plateforme Industrial IoT et de leur support.
author: monikavar
ms.author: movarshn
ms.service: industrial-iot
ms.topic: overview
ms.date: 11/10/2021
ms.openlocfilehash: 248cf06093ae206f68553cd3a6c81dff8fbb8e75
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132494388"
---
# <a name="azure-industrial-iot-platform-release-281"></a>Plateforme Azure Industrial IoT version 2.8.1
Nous avons le plaisir d’annoncer la sortie de la version 2.8.1 de nos composants de la plateforme Industrial IoT. Il s’agit de la première mise à jour corrective de la version 2.8 LTS (Long-Term Support). Elle contient des mises à jour de sécurité, des correctifs de bogues et des optimisations de performances importants.

## <a name="azure-industrial-iot-platform-release-28"></a>Plateforme Azure Industrial IoT version 2.8

Nous avons le plaisir d’annoncer que la version 2.8 est disponible avec un support LTS (Long-Term Support). Tout en continuant à développer et à publier les mises à jour de nos projets en cours sur GitHub, nous proposons désormais une version qui ne recevra que les correctifs de bogues et les correctifs de sécurité critiques à partir de juillet 2021. Les clients peuvent compter sur un cycle de vie de support à plus long terme pour ces builds LTS, offrant stabilité et assurance pour la planification sur des horizons de temps plus longs dont nos clients ont besoin. La version avec LTS offre aux clients la garantie qu’ils bénéficieront de tous les correctifs de sécurité ou de bogues critiques nécessaires avec un impact minimal sur leurs déploiements et leurs interactions avec les modules.  Dans le même temps, les clients peuvent accéder aux dernières mises à jour de la [version principale](https://github.com/Azure/Industrial-IoT) afin de rester en phase avec les derniers développements et le cycle le plus rapide pour les mises à jour de produits. 

## <a name="version-history"></a>Historique des versions 

|Version      |Type                   |Date         |Points forts                             |
|-------------|-----------------------|-------------|---------------------------------------|
|2.5.4        |Stable                 |Mars 2020   |Interface de méthode directe IoT Hub, contrôle depuis le cloud sans microservices supplémentaires (mode autonome), interface de serveur d’agent utilisateur OPC, utilise la pile OPC d’OPC Foundation. [Notes de publication](https://github.com/Azure/Industrial-IoT/releases/tag/2.5.4)|
|[2.7.206](https://github.com/Azure/Industrial-IoT/tree/release/2.7.206)      |Stable                 |Janvier 2021 |Configuration via l’API REST (mode orchestré), prend en charge le format de télémétrie Samples ainsi que PubSub. [Notes de publication](https://github.com/Azure/Industrial-IoT/releases/tag/2.7.206)|
|[2.8](https://github.com/Azure/Industrial-IoT/tree/2.8.0)        |Prise en charge à long terme (LTS)|Juillet 2021    |Mise à jour d’IoT Edge vers 1.1 LTS, journalisation et traçage de la pile OPC pour un meilleur diagnostic d’OPC Publisher, correctifs de sécurité - [Notes de publication](https://github.com/Azure/Industrial-IoT/releases/tag/2.8.0)|
|[2.8.1](https://github.com/Azure/Industrial-IoT/tree/2.8.1)        |Version corrective pour LTS 2.8|Novembre 2021    |Correctifs de bogues critiques, mises à jour de sécurité, optimisations des performances pour LTS v2.8|

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Qu’est-ce que l’IoT industriel ?](overview-what-is-industrial-iot.md)
