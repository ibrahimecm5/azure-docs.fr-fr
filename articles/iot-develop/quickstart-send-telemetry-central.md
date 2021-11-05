---
title: 'Démarrage rapide : Connecter un appareil et envoyer la télémétrie à Azure IoT Central'
description: Ce guide de démarrage rapide montre aux développeurs d’appareils comment connecter un appareil à Azure IoT Central de manière sécurisée. Vous utilisez Azure IoT device SDK pour C, C#, Python, Node.js ou Java afin d’exécuter une application cliente sur un appareil, puis vous vous connectez à IoT Central pour envoyer la télémétrie.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.topic: quickstart
ms.date: 04/27/2021
ms.collection: embedded-developer, application-developer
zone_pivot_groups: iot-develop-set1
ms.openlocfilehash: f6955c10aab9b21be43cc3b346e564cb9114979d
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131032737"
---
# <a name="quickstart-send-telemetry-from-a-device-to-azure-iot-central"></a>Démarrage rapide : Envoyer la télémétrie d’un appareil à Azure IoT Central

**S’applique à** : [Développeurs d’applications pour appareils](about-iot-develop.md#device-application-development)

:::zone pivot="programming-language-ansi-c"

[!INCLUDE [iot-develop-send-telemetry-central-c](../../includes/iot-develop-send-telemetry-central-c.md)]

:::zone-end

:::zone pivot="programming-language-csharp"

[!INCLUDE [iot-develop-send-telemetry-central-csharp](../../includes/iot-develop-send-telemetry-central-csharp.md)]

:::zone-end

:::zone pivot="programming-language-java"

[!INCLUDE [iot-develop-send-telemetry-central-java](../../includes/iot-develop-send-telemetry-central-java.md)]

:::zone-end

:::zone pivot="programming-language-nodejs"

[!INCLUDE [iot-develop-send-telemetry-central-nodejs](../../includes/iot-develop-send-telemetry-central-nodejs.md)]

:::zone-end

:::zone pivot="programming-language-python"

[!INCLUDE [iot-develop-send-telemetry-central-python](../../includes/iot-develop-send-telemetry-central-python.md)]

:::zone-end

## <a name="view-telemetry"></a>Afficher les données de télémétrie
Une fois que l’appareil est connecté à IoT Central, il commence à envoyer la télémétrie. Vous pouvez voir la télémétrie et d’autres informations sur les appareils connectés dans IoT Central. 

Dans IoT Central, sélectionnez **Appareils**, cliquez sur le nom de votre appareil, puis sélectionnez l’onglet **Vue d’ensemble**. Cette vue présente un graphe des températures indiquées par les deux thermostats.

:::image type="content" source="media/quickstart-send-telemetry-central/iot-central-telemetry-output-overview.png" alt-text="Vue d’ensemble de la télémétrie de l’appareil IoT Central":::

Sélectionnez l’onglet **Données brutes**. Cette vue présente la télémétrie chaque fois qu’un relevé de thermostat est envoyé.

:::image type="content" source="media/quickstart-send-telemetry-central/iot-central-telemetry-output-raw.png" alt-text="Sortie brute de la télémétrie de l’appareil IoT Central":::

Votre appareil est maintenant connecté en toute sécurité et envoie des données de télémétrie à Azure IoT.
    
## <a name="clean-up-resources"></a>Nettoyer les ressources
Si vous n’avez plus besoin des ressources IoT Central créées dans ce guide de démarrage rapide, vous pouvez les supprimer. En option, si vous envisagez de continuer à suivre la documentation de ce guide, vous pouvez conserver l’application que vous avez créée et la réutiliser dans d’autres exemples.

Pour supprimer l’exemple d’application Azure IoT Central et tous ses appareils et ressources :
1. Sélectionnez **Administration** > **Votre application**.
1. Sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez appris un workflow de base d’application Azure IoT pour connecter de façon sécurisée un appareil au cloud et envoyer des données de télémétrie appareil-à-cloud. Vous avez utilisé Azure IoT Central pour créer une application et une instance d’appareil. Vous avez ensuite utilisé Azure IoT device SDK pour créer un contrôleur de température, le connecter à IoT Central et envoyer la télémétrie. Vous avez également utilisé IoT Central pour surveiller les données de télémétrie.

À présent, explorez les articles suivants pour en savoir plus sur la création de solutions d’appareils avec Azure IoT. 

> [!div class="nextstepaction"]
> [Envoyer la télémétrie à Azure IoT Hub](./quickstart-send-telemetry-iot-hub.md)
> [!div class="nextstepaction"]
> [Créer une application IoT Central](../iot-central/core/quick-deploy-iot-central.md)