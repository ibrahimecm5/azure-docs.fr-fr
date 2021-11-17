---
title: Démarrage rapide - Contrôler un appareil depuis Azure IoT Hub | Microsoft Docs
description: Dans ce guide de démarrage rapide, vous exécutez deux exemples d’application. Une application est une application de service qui peut contrôler à distance des appareils connectés à votre hub. L’autre application simule un appareil connecté à votre concentrateur qui peut être contrôlé à distance.
author: eross-msft
ms.author: lizross
ms.service: iot-hub
services: iot-hub
ms.topic: quickstart
ms.custom:
- mvc
- mqtt
- 'Role: Cloud Development'
- devx-track-azurecli
ms.date: 07/26/2021
zone_pivot_groups: iot-hub-set1
ms.openlocfilehash: cae4b2c1b23acc56468d3c7ebe30cf8316842003
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132550246"
---
# <a name="quickstart-control-a-device-connected-to-an-iot-hub"></a>Démarrage rapide : Contrôler un appareil connecté à un hub IoT

Dans ce guide de démarrage rapide, vous utilisez une méthode directe pour contrôler un appareil simulé connecté à votre IoT Hub. IoT Hub est un service Azure qui vous permet de gérer vos appareils IoT à partir du cloud, et d’ingérer de gros volumes de télémétrie d’appareils dans le cloud pour les stocker ou les traiter. Vous pouvez utiliser des méthodes directes pour modifier à distance le comportement d’appareils connectés à votre hub IoT.

:::zone pivot="programming-language-csharp"

[!INCLUDE [quickstart-control-device-dotnet](../../includes/quickstart-control-device-dotnet.md)]

:::zone-end

:::zone pivot="programming-language-java"

[!INCLUDE [quickstart-control-device-java](../../includes/quickstart-control-device-java.md)]

:::zone-end

:::zone pivot="programming-language-nodejs"

[!INCLUDE [quickstart-control-device-node](../../includes/quickstart-control-device-node.md)]

:::zone-end

:::zone pivot="programming-language-python"

[!INCLUDE [quickstart-control-device-python](../../includes/quickstart-control-device-python.md)]

:::zone-end

## <a name="clean-up-resources"></a>Nettoyer les ressources

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez appelé une méthode directe sur un appareil à partir d’une application de service et répondu à l’appel de méthode directe dans une application d’appareil simulé.

Pour savoir comment acheminer les messages appareil-à-cloud vers différentes destinations dans le cloud, passez au tutoriel suivant.

> [!div class="nextstepaction"]
> [Tutoriel : acheminer les données de télémétrie vers différents points de terminaison pour traitement](tutorial-routing.md)