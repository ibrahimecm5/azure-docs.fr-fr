---
title: Vue d’ensemble du micro-agent autonome (préversion)
description: Les agents de sécurité Microsoft Defender pour IoT vous permettent d’intégrer la sécurité directement dans vos nouveaux appareils IoT et projets Azure IoT.
ms.date: 11/09/2021
ms.topic: article
ms.openlocfilehash: 9a8600f4a75bb9a4294ab44842fbae22d8c3e6ea
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132335667"
---
# <a name="standalone-micro-agent-overview-preview"></a>Vue d’ensemble du micro-agent autonome (préversion)

La sécurité est une préoccupation quasi-universelle pour les responsables de l’implémentation de l’IoT. Les appareils IoT ont des besoins uniques en matière de surveillance des points de terminaison, de gestion de la posture de sécurité et de détection des menaces, le tout avec des exigences de performances très spécifiques.

L’agent de sécurité Microsoft Defender pour IoT vous permet d’intégrer la sécurité directement dans vos nouveaux appareils IoT et projets Azure IoT. Le micro-agent a des options de déploiement flexibles, y compris la possibilité de déployer en tant que package binaire ou de modifier le code source, et il est disponible pour les systèmes d’exploitation IoT standard tels que Linux et les RTO Azure.

Le micro-agent Microsoft Defender pour IoT fournit une visibilité sur les points de terminaison pour la gestion de la posture de sécurité, la détection des menaces et l’intégration aux autres outils de sécurité de Microsoft pour une gestion unifiée de la sécurité.

## <a name="security-posture-management"></a>Gestion de la posture de sécurité

Surveillez de manière proactive la posture de sécurité de vos appareils IoT. Microsoft Defender pour IoT fournit des recommandations relatives à la posture de sécurité en fonction du point de référence CIS, ainsi que des recommandations propres aux appareils. Obtenez une visibilité sur la sécurité du système d’exploitation, notamment la configuration du système d’exploitation, la configuration du pare-feu et les autorisations.

## <a name="endpoint-iot-and-ot-threat-detection"></a>Détection des menaces IoT et OT sur les points de terminaison

Détectez les menaces telles que les botnets, les tentatives par force brute, le minage de cryptomonnaie et les activités suspectes sur le réseau. Créez des alertes personnalisées pour cibler les menaces les plus importantes dans votre organisation.

## <a name="flexible-distribution-and-deployment-models"></a>Modèles de distribution et de déploiement flexibles

Le micro-agent Microsoft Defender pour IoT inclut le code source, ce qui vous permet d’incorporer le micro-agent dans le microprogramme ou de le personnaliser pour inclure uniquement ce dont vous avez besoin. Le micro-agent est également disponible sous forme de package binaire ou intégré directement à d’autres solutions Azure IoT.

## <a name="meets-the-needs-of-your-iot-devices-with-minimal-impact"></a>Répond aux besoins de vos appareils IoT, avec un impact minimal

Le micro-agent Microsofy Defender pour IoT est facile à déployer et a peu d’impact sur les performances du point de terminaison. Grâce au micro-agent Defender pour IoT, vous pouvez :

- **Optimiser les performances**: Le micro-agent Microsoft Defender pour IoT affiche un faible encombrement et une faible consommation du processeur.

- **Plug-and-Play**: il n’existe aucune dépendance de noyau du système d’exploitation spécifique ou de prise en charge nécessaire pour tous les principaux systèmes d’exploitation IOT. Le micro-agent Microsoft Defender pour IoT se connecte à vos appareils, où qu’ils se trouvent.

- **Déploiement flexible**: En tant qu’agent autonome, le micro-agent Microsoft Defender pour IoT prend en charge différents modèles de distribution et un déploiement flexible.

## <a name="data-processing-and-residency"></a>Traitement et résidence des données

> [!NOTE]
> Microsoft Defender pour le traitement des données IoT et la résidence peuvent avoir lieu dans une région différente de la région du Hub IoT. Defender pour IoT utilise une représentation d’appareil, une adresse IP non masquée et des données de configuration supplémentaires dans le cadre de sa logique de détection de sécurité.

Defender pour le traitement des données IoT et la résidence peut se produire dans des régions qui sont différentes de la région du Hub IoT. Le mappage entre le Hub IoT et Defender pour les régions IoT est le suivant :

- Pour un hub situé en Europe, les données sont stockées dans la région *Europe Ouest*.

- Pour un hub situé hors Europe, les données sont stockées dans la région *USA Est*.

Defender pour IoT utilise la représentation d’appareil, les adresses IP non masquées et les données de configuration supplémentaires dans le cadre de sa logique de détection de sécurité par défaut. Pour désactiver le jumeau d’appareil et afficher le regroupement d’adresses IP, accédez à la page de paramètres de la collection de données.

:::image type="content" source="media/data-collection-settings.png" alt-text="Capture d’écran de la page de paramètres des collections de données.":::

Pour plus d’informations, consultez [Personnaliser votre solution Defender pour IoT](concept-micro-agent-configuration.md).

## <a name="next-steps"></a>Étapes suivantes

Consultez vos [méthodes d'authentification auprès du micro-agent (préversion)](concept-security-agent-authentication.md).
