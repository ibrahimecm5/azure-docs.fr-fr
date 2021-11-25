---
title: Module azureiotsecurity de Defender pour IoT pour IoT Edge
description: Comprenez l’architecture et les capacités du module azureiotsecurity de Microsoft Defender pour IoT pour IoT Edge.
ms.topic: conceptual
ms.date: 11/09/2021
ms.openlocfilehash: 55681d731193e0f73a9fb21b415b03d4411ee6fe
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132318956"
---
# <a name="microsoft-defender-for-iot-edge-azureiotsecurity"></a>Module azureiotsecurity de Microsoft Defender pour IoT Edge

[Azure IoT Edge](../../iot-edge/index.yml) offre de puissantes fonctionnalités de gestion et d’exécution de flux de travail à la périphérie. Le rôle clé d’IoT Edge dans les environnements IoT le rend particulièrement séduisant pour les personnes malveillantes.

Le module azureiotsecurity de Defender pour IoT constitue une solution de sécurité complète pour vos appareils IoT Edge. Il collecte, agrège et analyse des données de sécurité brutes tirées du système d’exploitation et du système de conteneur pour produire des alertes et des suggestions de sécurité actionnables.

Tout comme les agents de sécurité Defender pour IoT destinés aux appareils IoT, le module Defender pour IoT Edge est hautement personnalisable grâce à son jumeau de module. Pour plus d’informations, voir [Configurer un agent](how-to-agent-configuration.md).

Le module azureiotsecurity de Defender pour IoT pour IoT Edge offre les fonctionnalités suivantes :

- Collecte d’événements de sécurité bruts auprès du système d’exploitation sous-jacent (Linux) et des systèmes de conteneurs IoT Edge.

  Pour en savoir plus sur les collecteurs de données de sécurité disponibles, consultez [Configuration de l'agent Defender pour IoT](how-to-agent-configuration.md).

- Analyse des manifestes de déploiement IoT Edge.

- Agrégation des événements de sécurité bruts dans des messages envoyés par un [hub IoT Edge](../../iot-edge/iot-edge-runtime.md#iot-edge-hub).

- Suppression de la configuration grâce au jumeau d’azureiotsecurity.

  Pour plus d'informations, consultez [Configurer un agent Defender pour IoT](how-to-agent-configuration.md).

Le module azureiotsecurity de Defender pour IoT pour IoT Edge s’exécute en mode privilégié sous IoT Edge. Ce mode est nécessaire pour l’autoriser à effectuer un monitoring du système d’exploitation et d’autres modules IoT Edge.

## <a name="module-supported-platforms"></a>Plateformes prises en charge du module

Le module azureiotsecurity de Defender pour IoT pour IoT Edge n’est actuellement disponible que pour Linux.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez découvert l’architecture et les capacités du module azureiotsecurity de Defender pour IoT pour IoT Edge.

Pour poursuivre votre prise en main du déploiement de Defender pour IoT, lisez les articles suivants :

- Déployer [azureiotsecurity pour IoT Edge](how-to-deploy-edge.md)
- Découvrez comment [configurer votre micro-agent Defender-IoT](how-to-agent-configuration.md)
- Apprenez à [activer le service Defender pour IoT dans votre hub IoT](quickstart-onboard-iot-hub.md)
- En savoir plus sur le service dans la [FAQ Defender pour IoT](resources-agent-frequently-asked-questions.md)
