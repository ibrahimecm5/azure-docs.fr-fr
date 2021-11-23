---
title: mappages de IotJsonPathContentTemplate dans IoT Connector mappages d’appareils-api Azure Healthcare
description: cet article explique comment utiliser les mappages IotJsonPathContentTemplate avec IoT Connector modèles de mappages d’appareils.
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: how-to
ms.date: 11/22/2021
ms.author: jasteppe
ms.openlocfilehash: 56304f99e786a06abdfa67495bea061d21ebabb4
ms.sourcegitcommit: 3d04177023a3136832adb561da831ccc8e9910c7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132936503"
---
# <a name="how-to-use-iotjsonpathcontenttemplate-mappings"></a>Utilisation des mappages de IotJsonPathContentTemplate

> [!IMPORTANT]
> Les API Azure Healthcare sont actuellement en version préliminaire. L’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) contient des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore en disponibilité générale.

> [!TIP]
> Consultez l’outil [Mappeur de données du connecteur IoMT](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper) pour la modification, le test et la résolution des problèmes des mappages de destination FHIR et des appareils IOT Connector. Exportez les mappages de chargement vers le connecteur IoT dans le Portail Azure ou utilisez avec la [version open source](https://github.com/microsoft/iomt-fhir) du connecteur IOT.

Cet article explique comment utiliser les mappages IoTJsonPathContentTemplate avec les modèles de mappages d’appareils IoT Connector.

## <a name="iotjsonpathcontenttemplate"></a>IotJsonPathContentTemplate

Le IotJsonPathContentTemplate est semblable au JsonPathContentTemplate, sauf que `DeviceIdExpression` et `TimestampExpression` ne sont pas requis.

l’hypothèse, lors de l’utilisation de ce modèle, est que les messages en cours d’évaluation ont été envoyés à l’aide des kits de développement logiciel (sdk) d' [appareil Azure IoT Hub](../../iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-device-sdks) ou d' [exportation de données (héritée)](../../iot-central/core/howto-export-data-legacy.md) de [Azure IoT Central](../../iot-central/core/overview-iot-central.md). 

Lorsque vous utilisez ces kits de développement logiciel (SDK), l’identité de l’appareil et l’horodateur du message sont connus.

>[!IMPORTANT]
>assurez-vous que vous utilisez un identificateur d’appareil à partir d’Azure Iot Hub ou Azure IoT Central qui est inscrit comme identificateur pour une ressource d’appareil sur le service FHIR de destination.

si vous utilisez des kits de développement logiciel (sdk) d’appareils Azure IoT Hub, vous pouvez toujours utiliser le JsonPathContentTemplate, en supposant que vous utilisez des propriétés personnalisées dans le corps du message pour l’identité de l’appareil ou l’horodatage de mesure.

> [!NOTE]
> Lors de l’utilisation de `IotJsonPathContentTemplate` , le `TypeMatchExpression` doit résoudre l’intégralité du message en tant que JToken. Pour plus d’informations, consultez les exemples suivants :

### <a name="examples"></a>Exemples

**Fréquence cardiaque**

*Message*

```json
{
    "Body": {
        "heartRate": "78"        
    },
    "Properties": {
        "iothub-creation-time-utc" : "2021-02-01T22:46:01.8750000Z"
    },
    "SystemProperties": {
        "iothub-connection-device-id" : "device123"
    }
}
```

*Modèle*

```json

    "templateType": "JsonPathContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@Body.heartRate)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.Body.heartRate",
                "valueName": "hr"
            }
        ]
    }
}
```

**Tension artérielle**

*Message*

```json
{
    "Body": {
        "systolic": "123",
        "diastolic" : "87"
    },
    "Properties": {
        "iothub-creation-time-utc" : "2021-02-01T22:46:01.8750000Z"
    },
    "SystemProperties": {
        "iothub-connection-device-id" : "device123"
    }
}
```

*Modèle*

```json
{
    "typeName": "bloodpressure",
    "typeMatchExpression": "$..[?(@Body.systolic && @Body.diastolic)]",
    "values": [
        {
            "required": "true",
            "valueExpression": "$.Body.systolic",
            "valueName": "systolic"
        },
        {
            "required": "true",
            "valueExpression": "$.Body.diastolic",
            "valueName": "diastolic"
        }
    ]
}
```

> [!TIP]
> Pour plus d’informations sur les erreurs et les problèmes courants, consultez le [Guide de dépannage](./iot-troubleshoot-guide.md) du connecteur IOT.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à utiliser les mappages de périphérique. Pour savoir comment utiliser les mappages de destination FHIR, consultez

>[!div class="nextstepaction"]
>[Utilisation des mappages de destination FHIR](how-to-use-fhir-mappings.md)

(FHIR&#174;) est une marque déposée de [HL7](https://hl7.org/fhir/) qui est utilisée avec l’autorisation de HL7.
