---
title: 'Démarrage rapide : Intégrer Defender pour IoT à une solution basée sur un agent'
description: Suivez ce guide de démarrage rapide pour apprendre à intégrer et activer le service de sécurité Defender pour IoT dans votre instance d'Azure IoT Hub.
ms.topic: quickstart
ms.date: 11/09/2021
ms.openlocfilehash: f22b319a409b481fdeb26a68cdd4b58809b8d015
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132281851"
---
# <a name="quickstart-onboard-defender-for-iot-to-an-agent-based-solution"></a>Démarrage rapide : Intégrer Defender pour IoT à une solution basée sur un agent

Cet article explique comment activer le service Defender pour IoT sur votre hub IoT existant. Si vous n’avez pas de hub IoT, consultez [Créer un hub IoT à l’aide du portail Azure](../../iot-hub/iot-hub-create-through-portal.md) pour bien démarrer.

Vous pouvez gérer votre sécurité IoT par le biais du hub IoT dans Defender pour IoT. Le portail de gestion situé dans le hub IoT vous permet d’effectuer les opérations suivantes : 

- Gérer la sécurité IoT Hub.

- Gestion de base de la sécurité d’un appareil IoT sans installation d’agent basée sur les données de télémétrie IoT Hub. 

- Gestion avancée pour la sécurité d’un appareil IoT basée sur le micro-agent.

> [!NOTE]
> Actuellement, Defender pour IoT prend uniquement en charge les hubs IoT de niveau standard.

## <a name="prerequisites"></a>Prérequis

Aucun

## <a name="onboard-defender-for-iot-to-an-iot-hub"></a>Intégrer Defender pour IoT à un hub IoT

Pour tous les nouveaux hubs IoT, Defender pour IoT est défini sur **Activé** par défaut. Vous pouvez vérifier que Defender pour IoT a la valeur **Activé** pendant le processus de création d’un hub IoT.

Pour vérifier que le bouton bascule est défini sur **Activé** :

1. Accédez au portail Azure.

1. Sélectionnez **IoT Hub** dans la liste des services Azure.

1. Sélectionnez **Create** (Créer).

    :::image type="content" source="media/quickstart-onboard-iot-hub/create-iot-hub.png" alt-text="Sélectionnez le bouton Créer dans la barre d’outils supérieure." lightbox="media/quickstart-onboard-iot-hub/create-iot-hub-expanded.png":::

1. Sélectionnez l’onglet **Gestion**, puis vérifiez que le bouton bascule **Defender pour IoT** est défini sur **Activé**.

    :::image type="content" source="media/quickstart-onboard-iot-hub/management-tab.png" alt-text="Vérifiez que le bouton bascule Defender pour IoT est défini sur Activé.":::

## <a name="onboard-defender-for-iot-to-an-existing-iot-hub"></a>Intégration de Defender pour IoT à un hub IoT existant

Vous pouvez intégrer Defender pour IoT à un hub IoT existant, où vous pouvez ensuite superviser la gestion des identités des appareils, et les modèles de communication appareil-à-cloud et cloud-à-appareil.

Pour intégrer Defender pour IoT à un hub IoT existant :

1. Accédez au IoT Hub. 

1. Sélectionnez le hub IoT à intégrer.

1. Sélectionnez n’importe quelle option sous **Sécurité**.

1. Cliquez sur  **Sécuriser votre solution IoT**, puis complétez le formulaire d’intégration. 

    :::image type="content" source="media/quickstart-onboard-iot-hub/secure-your-iot-solution.png" alt-text="Sélectionnez le bouton Sécuriser votre solution IoT pour sécuriser votre solution.":::

Le bouton **Sécuriser votre solution IoT** apparaît seulement si le hub IoT n’a pas déjà été intégré ou si, lors de l’intégration, vous laissez le bouton bascule de Defender pour IoT sur **Désactivé**.

:::image type="content" source="media/quickstart-onboard-iot-hub/toggle-is-off.png" alt-text="Si votre bouton bascule a été défini sur Désactivé lors de l’intégration.":::

## <a name="next-steps"></a>Étapes suivantes

Passez à l’article suivant pour configurer votre solution...

> [!div class="nextstepaction"]
> [Créer un jumeau de module pour le micro-agent Defender pour le cloud pour IoT (préversion)](quickstart-create-micro-agent-module-twin.md)
