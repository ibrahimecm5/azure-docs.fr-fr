---
title: API du micro-agent Defender-IoT pour Azure RTOS
description: API de référence pour le micro-agent Defender-IoT pour Azure RTOS.
ms.topic: reference
ms.date: 11/09/2021
ms.author: mlottner
ms.openlocfilehash: ad79ec853f3ee625c9b8c3c4bb4063aee87481f1
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132286637"
---
# <a name="defender-iot-micro-agent-for-azure-rtos-api-preview"></a>Micro-agent Defender-IoT pour Azure RTOS (préversion)

Les API Defender pour IoT sont régies par les [conditions d’utilisation et la licence d’API Microsoft](/legal/microsoft-apis/terms-of-use).

Cette API est conçue pour être utilisée uniquement avec le micro-agent Defender-IoT pour Azure RTOS. Pour accéder à des ressources supplémentaires, consultez la [ressource GitHub pour le micro-agent Defender-IoT pour Azure RTOS](https://github.com/azure-rtos/azure-iot-preview/releases).

## <a name="enable-defender-iot-micro-agent-for-azure-rtos"></a>Activer le micro-agent Defender-IoT pour Azure RTOS

**nx_azure_iot_security_module_enable**

### <a name="prototype"></a>Prototype

```c
UINT nx_azure_iot_security_module_enable(NX_AZURE_IOT *nx_azure_iot_ptr);
```

### <a name="description"></a>Description

Cette routine active le sous-système du micro-agent Defender-IoT Azure IoT. Une machine à états interne gère la collecte d’événements de sécurité et les transmet à Azure IoT Hub. Une seule instance de NX_AZURE_IOT_SECURITY_MODULE est requise pour gérer la collecte de données.

### <a name="parameters"></a>Paramètres

| Nom | Description |
|---------|---------|
| nx_azure_iot_ptr  [in]    | Pointeur vers un `NX_AZURE_IOT`.  |

### <a name="return-values"></a>Valeurs retournées

|Valeurs retournées  |Description |
|---------|---------|
|NX_AZURE_IOT_SUCCESS|   Le module de sécurité Azure IoT a été correctement activé.     |
|NX_AZURE_IOT_FAILURE   |  L’activation du module de sécurité Azure IoT a échoué, en raison d’une erreur interne.    |
|NX_AZURE_IOT_INVALID_PARAMETER   |  Le module de sécurité requiert une instance de #NX_AZURE_IOT valide.      |

### <a name="allowed-from"></a>Autorisé à partir de

Threads

## <a name="disable-azure-iot-defender-iot-micro-agent"></a>Désactiver le micro-agent Defender-IoT Azure IoT

**nx_azure_iot_security_module_disable**

### <a name="prototype"></a>Prototype

```c
UINT nx_azure_iot_security_module_disable(NX_AZURE_IOT *nx_azure_iot_ptr);
```

### <a name="description"></a>Description

Cette routine désactive le sous-système du micro-agent Defender-IoT Azure IoT.

### <a name="parameters"></a>Paramètres

| Nom | Description |
|---------|---------|
| nx_azure_iot_ptr  [in]    | Pointeur vers `NX_AZURE_IOT`. Si la valeur est NULL, l’instance singleton est désactivée. |

### <a name="return-values"></a>Valeurs retournées

|Valeurs retournées  |Description |
|---------|---------|
|NX_AZURE_IOT_SUCCESS     |   Le module de sécurité Azure IoT est correctement désactivé.      |
|NX_AZURE_IOT_INVALID_PARAMETER   |  L’instance de IoT Hub Azure est différente de l’instance composite singleton.       |
|NX_AZURE_IOT_FAILURE    |  La désactivation du module de sécurité Azure IoT a échoué, en raison d’une erreur interne.       |

### <a name="allowed-from"></a>Autorisé à partir de

Threads

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur la prise en main du micro-agent Defender-IoT pour Azure RTOS, consultez les articles suivants :

- Consultez la [présentation](iot-security-azure-rtos.md) du micro-agent Defender-IoT pour Azure RTOS d’Azure Defender pour IoT.
