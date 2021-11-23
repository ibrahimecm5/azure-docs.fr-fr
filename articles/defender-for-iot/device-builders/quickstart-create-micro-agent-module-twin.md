---
title: 'Démarrage rapide : Créer un jumeau de module pour le micro-agent Defender pour le cloud pour IoT (préversion)'
description: Suivez ce guide de démarrage rapide afin d'apprendre à créer des jumeaux de module DefenderIotMicroAgent individuels pour les nouveaux appareils.
ms.date: 11/09/2021
ms.topic: quickstart
ms.openlocfilehash: 35480236f6a1fc7457e5af5e8a64e2967d8eaa3e
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132331525"
---
# <a name="quickstart-create-a-defender-for-cloud-iot-micro-agent-module-twin-preview"></a>Démarrage rapide : Créer un jumeau de module pour le micro-agent Defender pour le cloud pour IoT (préversion)

Vous pouvez créer des jumeaux de module  **DefenderIotMicroAgent** individuels pour les nouveaux appareils. Vous pouvez également créer des jumeaux de module par lot pour tous les appareils d’un hub IoT. 

## <a name="prerequisites"></a>Prérequis

Aucun

## <a name="device-twins"></a>Jumeaux d’appareil 

Pour les solutions IoT intégrées à Azure, les jumeaux d’appareil jouent un rôle essentiel dans la gestion d’appareils et l’automatisation des processus. 

Defender pour IoT a la possibilité d’être entièrement intégré à votre plateforme de gestion d’appareils IoT existante. L’intégration complète vous permet de gérer l’état de sécurité de votre appareil et d’utiliser toutes les fonctionnalités de contrôle d’appareil existantes. L’intégration est obtenue par l’utilisation du mécanisme de jumelage IoT Hub. 

Pour en savoir plus sur le concept, consultez  [Comprendre et utiliser les jumeaux d’appareil dans IoT Hub](../../iot-hub/iot-hub-devguide-device-twins.md). 

## <a name="defender-iot-micro-agent-twins"></a>Jumeaux du micro-agent Defender IoT 

Defender pour IoT utilise un jumeau de micro-agent Defender-IoT pour chaque appareil. Le jumeau du micro-agent Defender IoT contient toutes les informations relatives à la sécurité des appareils pour chaque appareil spécifique de votre solution. Les propriétés de sécurité des appareils sont configurées par le biais d’un jumeau du micro-agent Defender IoT dédié pour une communication plus sécurisée, ce qui permet les mises à jour et une maintenance nécessitant moins de ressources. 

## <a name="understanding-defenderiotmicroagent-module-twins"></a>Présentation des jumeaux de module DefenderIotMicroAgent 

Les jumeaux d’appareil jouent un rôle clé dans la gestion des appareils et l’automatisation des processus pour les solutions IoT qui sont intégrées à Azure.

Defender pour IoT offre la possibilité d’intégrer totalement votre plateforme de gestion d’appareils IoT existante, ce qui vous permet de gérer l’état de sécurité de vos appareils et de tirer parti des fonctionnalités de contrôle d’appareils existantes. Vous pouvez intégrer votre instance Defender pour IoT à l’aide du mécanisme de jumelage IoT Hub.  

Pour en savoir plus sur le concept général des jumeaux de module dans Azure IoT Hub, consultez  [Comprendre et utiliser les jumeaux de module dans IoT Hub](../../iot-hub/iot-hub-devguide-module-twins.md).

Defender pour IoT utilise le mécanisme de jumelage de module et gère le jumeau du micro-agent Defender IoT nommé `DefenderIotMicroAgent` pour chacun de vos appareils. 

Pour tirer pleinement parti de toutes les fonctionnalités de Defender pour IoT, vous devez créer, configurer et utiliser les jumeaux du micro-agent Defender IoT pour chaque appareil du service. 

## <a name="create-defenderiotmicroagent-module-twin"></a>Créer un jumeau de module DefenderIotMicroAgent 

Les jumeaux de module **DefenderIotMicroAgent** peuvent être créées en modifiant manuellement chaque jumeau de module afin d’inclure des configurations spécifiques pour chaque appareil. 

Pour créer manuellement un jumeau de module  **DefenderIotMicroAgent** destiné à un appareil : 

1. Dans votre hub IoT, recherchez et sélectionnez l’appareil sur lequel créer un jumeau de micro-agent Defender-IoT. 

1. Sélectionnez **Ajouter l’identité de module**. 

1. Dans le champ **Nom de l’identité de module**, entrez `DefenderIotMicroAgent`. 

1. Sélectionnez  **Enregistrer**. 

## <a name="verify-the-creation-of-a-module-twin"></a>Vérifier la création d’un jumeau de module 

Pour vérifier l’existence d’un jumeau de micro-agent Defender-IoT pour un appareil particulier : 

1. Dans votre hub Azure IoT, sélectionnez **Appareils IoT** dans le menu **Explorateurs**.  

1. Entrez l’ID de l’appareil, ou sélectionnez une option dans le champ **d’interrogation des appareils**, puis sélectionnez **Interroger les appareils**.  

    :::image type="content" source="media/quickstart-create-micro-agent-module-twin/iot-devices.png" alt-text="Sélectionnez Interroger les appareils pour obtenir la liste de vos appareils.":::

1. Sélectionnez l’appareil ou ouvrez la page **Détails de l’appareil**. 

1. Sélectionnez le menu **Identités de module**, puis vérifiez l’existence du module **DefenderIotMicroAgent** dans la liste des identités de module associées à l’appareil.  

    :::image type="content" source="media/quickstart-create-micro-agent-module-twin/device-details-module.png" alt-text="Sélectionnez Identités de module sous l’onglet.":::

## <a name="next-steps"></a>Étapes suivantes 

> [!div class="nextstepaction"]
> [Examiner les recommandations de sécurité](quickstart-investigate-security-recommendations.md)
