---
title: Résolution de l’erreur Azure IoT Hub 403006 DeviceMaximumActiveFileUploadLimitExceeded
description: Comprendre comment corriger l’erreur 403006 DeviceMaximumActiveFileUploadLimitExceeded
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 5b90593c44fbab0342def8b85a1a71e8ef15d6ad
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131011065"
---
# <a name="403006-devicemaximumactivefileuploadlimitexceeded"></a>403006 DeviceMaximumActiveFileUploadLimitExceeded

Cet article décrit les causes et solutions des erreurs **403006 DeviceMaximumActiveFileUploadLimitExceeded**.

## <a name="symptoms"></a>Symptômes

Votre demande de chargement de fichier échoue en générant le code d’erreur **403006** et le message « Le nombre de demandes de chargement de fichier actives ne peut pas être supérieur à 10 ».

## <a name="cause"></a>Cause

Notez que chaque client d’appareil est limité à [10 chargements de fichiers simultanés](./iot-hub-devguide-quotas-throttling.md#other-limits). 

Vous pouvez facilement dépasser la limite si votre appareil ne signale pas à IoT Hub l’achèvement des chargements de fichiers. Ce problème est généralement dû à un réseau non fiable côté appareil.

## <a name="solution"></a>Solution

Assurez-vous que l’appareil peut rapidement [signaler à IoT Hub l’achèvement des chargements de fichiers](./iot-hub-devguide-file-upload.md#device-notify-iot-hub-of-a-completed-file-upload). Ensuite, essayez de [réduire la durée de vie du jeton SAP pour la configuration du chargement de fichiers](iot-hub-configure-file-upload.md).

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les chargements de fichiers, voir [Chargement de fichiers avec IoT Hub](./iot-hub-devguide-file-upload.md) et [Configurer les chargements de fichiers IoT Hub à l’aide du portail Azure](./iot-hub-configure-file-upload.md).