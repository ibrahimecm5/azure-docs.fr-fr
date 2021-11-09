---
title: Démarrage rapide pour envoyer des données de télémétrie d’appareils à Azure IoT Hub
description: Ce guide de démarrage rapide montre aux développeurs d’appareils comment connecter un appareil à Azure IoT Hub de manière sécurisée. Vous utilisez un Azure IoT device SDK pour C, C#, Python, Node.js ou Java afin de créer un client d’appareil pour Windows, Linux ou Raspberry Pi OS (Raspian). Enfin, vous connectez l’appareil à Azure IoT Hub et envoyez les données de télémétrie.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.topic: quickstart
ms.date: 08/03/2021
ms.collection: embedded-developer, application-developer
zone_pivot_groups: iot-develop-set1
ms.openlocfilehash: d079fd2380aed72910f5b78e2e3135c077ac59d1
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131077477"
---
# <a name="quickstart-send-telemetry-from-an-iot-plug-and-play-device-to-azure-iot-hub"></a>Démarrage rapide : Envoyer des données de télémétrie à partir d’un appareil IoT Plug-and-Play vers Azure IoT Hub

**S’applique à** : [Développeurs d’applications pour appareils](about-iot-develop.md#device-application-development)

:::zone pivot="programming-language-ansi-c"

[!INCLUDE [iot-develop-send-telemetry-iot-hub-c](../../includes/iot-develop-send-telemetry-iot-hub-c.md)]

:::zone-end

:::zone pivot="programming-language-csharp"

[!INCLUDE [iot-develop-send-telemetry-iot-hub-csharp](../../includes/iot-develop-send-telemetry-iot-hub-csharp.md)]

:::zone-end

:::zone pivot="programming-language-java"

[!INCLUDE [iot-develop-send-telemetry-iot-hub-java](../../includes/iot-develop-send-telemetry-iot-hub-java.md)]

:::zone-end

:::zone pivot="programming-language-nodejs"

[!INCLUDE [iot-develop-send-telemetry-iot-hub-node](../../includes/iot-develop-send-telemetry-iot-hub-node.md)]

:::zone-end

:::zone pivot="programming-language-python"

[!INCLUDE [iot-develop-send-telemetry-iot-hub-python](../../includes/iot-develop-send-telemetry-iot-hub-python.md)]

:::zone-end
    
## <a name="clean-up-resources"></a>Nettoyer les ressources
Si vous n’avez plus besoin des ressources Azure créées dans ce démarrage rapide, vous pouvez utiliser Azure CLI pour les supprimer.

> [!IMPORTANT]
> La suppression d’un groupe de ressources est irréversible. Le groupe de ressources et toutes les ressources qu’il contient sont supprimés définitivement. Veillez à ne pas supprimer accidentellement des ressources ou un groupe de ressources incorrects.

Pour supprimer un groupe de ressources par nom :
1. Exécutez la commande [az group delete](/cli/azure/group#az_group_delete). Cette commande supprime le groupe de ressources, le hub IoT et l’inscription de l’appareil que vous avez créés.

    ```azurecli-interactive
    az group delete --name MyResourceGroup
    ```
1. Exécutez la commande [az group list](/cli/azure/group#az_group_list) pour confirmer la suppression du groupe de ressources.  

    ```azurecli-interactive
    az group list
    ```

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez appris un workflow de base d’application Azure IoT pour connecter de façon sécurisée un appareil au cloud et envoyer des données de télémétrie appareil-à-cloud. Vous avez utilisé Azure CLI pour créer un hub Azure IoT et une instance d’appareil. Vous avez ensuite utilisé un Azure IoT device SDK pour créer un contrôleur de température, le connecter au hub et envoyer la télémétrie. Vous avez aussi utilisé Azure CLI pour superviser la télémétrie.

À présent, explorez les articles suivants pour en savoir plus sur la création de solutions d’appareils avec Azure IoT. 

> [!div class="nextstepaction"]
> [Contrôler un appareil connecté à un hub IoT](../iot-hub/quickstart-control-device.md)
> [!div class="nextstepaction"]
> [Envoyer des données de télémétrie à IoT Central](quickstart-send-telemetry-central.md)
> [!div class="nextstepaction"]
> [Connecter un DevKit MXCHIP AZ3166 à IoT Central](quickstart-devkit-mxchip-az3166.md)
