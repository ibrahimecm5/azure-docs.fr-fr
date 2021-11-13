---
title: Alertes de sécurité personnalisées pour IoT Hub
description: Découvrez les alertes de sécurité personnalisables et les actions de correction recommandées en utilisant les fonctionnalités et le service Defender pour IoT Hub.
ms.topic: conceptual
ms.date: 10/18/2021
ms.openlocfilehash: e91587df3dbfb50700f95eaefea3bf80c2e18e04
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130249720"
---
# <a name="defender-for-iot-hub-custom-security-alerts"></a>Alertes de sécurité personnalisées Defender pour IoT Hub

Defender pour IoT analyse continuellement votre solution IoT à l’aide d’analyses avancées et d’informations sur les menaces pour vous alerter en cas d’activités malveillantes.

Créez des alertes personnalisées en fonction de votre connaissance du comportement attendu de chaque appareil. Ces alertes jouent le rôle d’indicateurs les plus efficaces de toute modification potentielle du déploiement de votre organisation ou de votre environnement.

Vous pouvez définir les alertes Defender pour IoT suivantes selon le comportement attendu de votre hub IoT. Pour plus d’informations sur la personnalisation de chaque alerte, consultez [Créer des alertes personnalisées](quickstart-create-custom-alerts.md).

## <a name="built-in-custom-alerts-in-the-iot-hub"></a>Alertes intégrées et personnalisées dans IoT Hub

### <a name="low-severity"></a>Gravité faible

| Nom de l’alerte | severity | Source de données | Description | Type d’alerte |
|--|--|--|--|--|
| Alerte personnalisée : le nombre de messages cloud-à-appareil dans le protocole AMQP est en dehors de la plage autorisée | Faible | IoT Hub | Le nombre de messages cloud-à-appareil (protocole AMQP) dans une fenêtre de temps spécifique est en dehors de la plage autorisée actuellement configurée. | IoT_CA_AmqpC2DMessagesNotInAllowedRange |
| Alerte personnalisée : le nombre de messages cloud-à-appareil rejetés dans le protocole AMQP est en dehors de la plage autorisée | Faible | IoT Hub | Le nombre de messages cloud-à-appareil (protocole AMQP) rejetés par l’appareil dans une fenêtre de temps spécifique est en dehors de la plage autorisée actuellement configurée. | IoT_CA_AmqpC2DRejectedMessagesNotInAllowedRange |
| Alerte personnalisée : le nombre de messages appareil-à-cloud dans le protocole AMQP est en dehors de la plage autorisée | Faible | IoT Hub | Le nombre de messages appareil-à-cloud (protocole AMQP) dans une fenêtre de temps spécifique est en dehors de la plage autorisée actuellement configurée. | IoT_CA_AmqpD2CMessagesNotInAllowedRange |
| Alerte personnalisée : le nombre d’appels de méthode directe est en dehors de la plage autorisée | Faible | IoT Hub | Le nombre d’appels de méthode directe dans une fenêtre de temps spécifique est en dehors de la plage autorisée actuellement configurée. | IoT_CA_DirectMethodInvokesNotInAllowedRange |
| Alerte personnalisée : le nombre de chargements de fichiers est en dehors de la plage autorisée | Faible | IoT Hub | Le nombre de nombre de chargements de fichiers dans une fenêtre de temps spécifique est en dehors de la plage autorisée actuellement configurée. | IoT_CA_FileUploadsNotInAllowedRange |
| Alerte personnalisée : le nombre de messages cloud-à-appareil dans le protocole HTTP est en dehors de la plage autorisée | Faible | IoT Hub | La quantité de messages cloud-à-appareil (protocole HTTP) dans une fenêtre de temps n’est pas dans la plage autorisée configurée | IoT_CA_HttpC2DMessagesNotInAllowedRange |
| Alerte personnalisée : le nombre de messages cloud-à-appareil rejetés dans le protocole HTTP est en dehors de la plage autorisée | Faible | IoT Hub | Le nombre de messages cloud-à-appareil (protocole HTTP) dans une fenêtre de temps spécifique est en dehors de la plage autorisée actuellement configurée. | IoT_CA_HttpC2DRejectedMessagesNotInAllowedRange |
| Alerte personnalisée : le nombre de messages appareil-à-cloud dans le protocole HTTP est en dehors de la plage autorisée | Faible | IoT Hub | Le nombre de messages appareil-à-cloud (protocole HTTP) dans une fenêtre de temps spécifique est en dehors de la plage autorisée actuellement configurée. | IoT_CA_HttpD2CMessagesNotInAllowedRange |
| Alerte personnalisée : le nombre de messages cloud-à-appareil dans le protocole MQTT est en dehors de la plage autorisée | Faible | IoT Hub | Le nombre de messages cloud-à-appareil (protocole MQTT) dans une fenêtre de temps spécifique est en dehors de la plage autorisée actuellement configurée. | IoT_CA_MqttC2DMessagesNotInAllowedRange |
| Alerte personnalisée : le nombre de messages cloud-à-appareil rejetés dans le protocole MQTT est en dehors de la plage autorisée | Faible | IoT Hub | Le nombre de messages cloud-à-appareil (protocole MQTT) rejetés par l’appareil dans une fenêtre de temps spécifique est en dehors de la plage autorisée actuellement configurée. | IoT_CA_MqttC2DRejectedMessagesNotInAllowedRange |
| Alerte personnalisée : le nombre de messages appareil-à-cloud dans le protocole MQTT est en dehors de la plage autorisée | Faible | IoT Hub | Le nombre de messages appareil-à-cloud (protocole MQTT) dans une fenêtre de temps spécifique est en dehors de la plage autorisée actuellement configurée. | IoT_CA_MqttD2CMessagesNotInAllowedRange |
| Alerte personnalisée : le nombre de vidages de file d’attente de commandes est en dehors de la plage autorisée | Faible | IoT Hub | Le nombre de vidages de file d’attente de commandes dans une fenêtre de temps spécifique est en dehors de la plage autorisée actuellement configurée. | IoT_CA_QueuePurgesNotInAllowedRange |
| Alerte personnalisée : le nombre de mises à jour de jumeau de module est en dehors de la plage autorisée | Faible | IoT Hub | Le nombre de mises à jour de jumeau de module dans une fenêtre de temps spécifique est en dehors de la plage autorisée actuellement configurée. | IoT_CA_TwinUpdatesNotInAllowedRange |
| Alerte personnalisée : le nombre d’opérations non autorisées est en dehors de la plage autorisée | Faible | IoT Hub | Le nombre d’opérations non autorisées dans une fenêtre de temps spécifique est en dehors de la plage autorisée actuellement configurée. | IoT_CA_UnauthorizedOperationsNotInAllowedRange |

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [personnaliser une alerte](quickstart-create-custom-alerts.md)
- [Vue d’ensemble](overview.md) du service Defender pour IoT
