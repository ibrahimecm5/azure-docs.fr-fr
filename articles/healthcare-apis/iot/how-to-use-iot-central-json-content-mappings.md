---
title: mappages de IotCentralJsonPathContentTemplate dans IoT Connector mappages d’appareils-api Azure Healthcare
description: cet article décrit le mappage IotCentralJsonPathContent avec les modèles de mappage de périphérique IoT Connector.
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: how-to
ms.date: 11/16/2021
ms.author: jasteppe
ms.openlocfilehash: 50c4d8bad51998f8077e2d5915748d0a19bd0a00
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132733458"
---
# <a name="how-to-use-iotcentraljsonpathcontenttemplate-mappings"></a>Utilisation des mappages de IotCentralJsonPathContentTemplate

> [!IMPORTANT]
> Les API Azure Healthcare sont actuellement en version préliminaire. L’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) contient des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore en disponibilité générale.

> [!TIP]
> Consultez l’outil [Mappeur de données du connecteur IoMT](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper) pour la modification, le test et la résolution des problèmes des mappages de destination FHIR et des appareils IOT Connector. Exportez les mappages de chargement vers le connecteur IoT dans le Portail Azure ou utilisez avec la [version open source](https://github.com/microsoft/iomt-fhir) du connecteur IOT.

Cet article explique comment utiliser les mappages IoTCentralJsonPathContentTemplate avec les mappages d’appareils IoT Connector.

## <a name="iotcentraljsonpathcontenttemplate"></a>IotCentralJsonPathContentTemplate

IotCentralJsonPathContentTemplate ne nécessite pas non plus DeviceIdExpression et TimestampExpression. elle est utilisée lorsque les messages en cours d’évaluation sont envoyés par le biais de la fonctionnalité [exporter des données](../../iot-central/core/howto-export-data.md) de [Azure IoT Central](../../iot-central/core/overview-iot-central.md). 

si vous utilisez la fonctionnalité d’exportation de données de Azure IoT Central et des propriétés personnalisées dans le corps du message pour l’identité de l’appareil ou l’horodatage de mesure, vous pouvez toujours utiliser le JsonPathContentTemplate.

> [!NOTE]
> Lors de l’utilisation de `IotCentralJsonPathContentTemplate` , `TypeMatchExpression` doit correspondre à l’intégralité du message en tant que JToken. Pour plus d’informations, consultez les exemples suivants :
 
### <a name="examples"></a>Exemples

**Fréquence cardiaque**

*Message*

```json
{
    "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
    "messageSource": "telemetry",
    "deviceId": "1vzb5ghlsg1",
    "schema": "default@v1",
    "templateId": "urn:qugj6vbw5:___qbj_27r",
    "enqueuedTime": "2020-08-05T22:26:55.455Z",
    "telemetry": {
        "Activity": "running",
        "BloodPressure": {
            "Diastolic": 7,
            "Systolic": 71
        },
        "BodyTemperature": 98.73447010562934,
        "HeartRate": 88,
        "HeartRateVariability": 17,
        "RespiratoryRate": 13
    },
    "enrichments": {
      "userSpecifiedKey": "sampleValue"
    },
    "messageProperties": {
      "messageProp": "value"
    }
}
```

*Modèle*

```json
{
    "templateType": "IotCentralJsonPathContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@telemetry.HeartRate)]",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.telemetry.HeartRate",
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
    "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
    "messageSource": "telemetry",
    "deviceId": "1vzb5ghlsg1",
    "schema": "default@v1",
    "templateId": "urn:qugj6vbw5:___qbj_27r",
    "enqueuedTime": "2020-08-05T22:26:55.455Z",
    "telemetry": {
        "Activity": "running",
        "BloodPressure": {
            "Diastolic": 7,
            "Systolic": 71
        },
        "BodyTemperature": 98.73447010562934,
        "HeartRate": 88,
        "HeartRateVariability": 17,
        "RespiratoryRate": 13
    },
    "enrichments": {
      "userSpecifiedKey": "sampleValue"
    },
    "messageProperties": {
      "messageProp": "value"
    }
}
```

*Modèle*

```json
{
    "templateType": "IotCentralJsonPathContent",
    "template": {
        "typeName": "bloodPressure",
        "typeMatchExpression": "$..[?(@telemetry.BloodPressure.Diastolic && @telemetry.BloodPressure.Systolic)]",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.telemetry.BloodPressure.Diastolic",
                "valueName": "bp_diastolic"
            },
            {
                "required": "true",
                "valueExpression": "$.telemetry.BloodPressure.Systolic",
                "valueName": "bp_systolic"
            }
        ]
    }
}
```

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à utiliser les mappages de périphérique. Pour savoir comment utiliser les mappages de destination FHIR, consultez

>[!div class="nextstepaction"]
>[Utilisation des mappages de destination FHIR](how-to-use-fhir-mappings.md)

(FHIR&#174;) est une marque déposée de [HL7](https://hl7.org/fhir/) qui est utilisée avec l’autorisation de HL7.
