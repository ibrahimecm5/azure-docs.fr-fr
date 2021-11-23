---
title: Mappages JsonPathContentTemplate dans les mappages d’appareils de connecteur IoT-API de santé Azure
description: Cet article explique comment utiliser les mappages JsonPathContentTemplate avec les modèles de mappages d’appareils IoT Connector.
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: how-to
ms.date: 11/22/2021
ms.author: jasteppe
ms.openlocfilehash: 9c8ce02be3301bdfaf8d151ef84e1475edaa6b03
ms.sourcegitcommit: 3d04177023a3136832adb561da831ccc8e9910c7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132937257"
---
# <a name="how-to-use-jsonpathcontenttemplate-mappings"></a>Utilisation des mappages de JsonPathContentTemplate

> [!IMPORTANT]
> Les API Azure Healthcare sont actuellement en version préliminaire. L’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) contient des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore en disponibilité générale.

> [!TIP]
> Consultez l’outil [Mappeur de données du connecteur IoMT](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper) pour la modification, le test et la résolution des problèmes des mappages de destination FHIR et des appareils IOT Connector. Exportez les mappages de chargement vers le connecteur IoT dans le Portail Azure ou utilisez avec la [version open source](https://github.com/microsoft/iomt-fhir) du connecteur IOT.

Cet article explique comment utiliser les mappages JsonPathContentTemplate avec les modèles de mappages d’appareils IoT Connector.

## <a name="jsonpathcontenttemplate"></a>JsonPathContentTemplate

Le JsonPathContentTemplate permet la correspondance et l’extraction des valeurs d’un message Azure Event Hub à l’aide de JSONPath.

|Propriété|Description|Exemple|
|--------|-----------|-------|
|TypeName|Type à associer aux mesures qui correspondent au modèle|`heartrate`|
|TypeMatchExpression|Expression JSONPath qui est évaluée par rapport à la charge utile EventData. Si un JToken correspondant est trouvé, le modèle est considéré comme une correspondance. Toutes les expressions ultérieures sont évaluées par rapport aux JToken extraits correspondantes.|`$..[?(@heartRate)]`|
|TimestampExpression|Expression JSONPath pour extraire la valeur d’horodatage pour le OccurrenceTimeUtc de la mesure.|`$.matchedToken.endDate`|
|DeviceIdExpression|Expression JSONPath pour extraire l’identificateur de l’appareil.|`$.matchedToken.deviceId`|
|PatientIdExpression|*Obligatoire* lorsque IdentityResolution est en mode **création** et *facultatif* lorsque IdentityResolution est en mode de **recherche** . Expression permettant d’extraire l’identificateur du patient.|`$.matchedToken.patientId`|
|EncounterIdExpression|*Facultatif*: l’expression pour extraire l’identificateur de la rencontre.|`$.matchedToken.encounterId`|
|CorrelationIdExpression|*Facultatif*: l’expression pour extraire l’identificateur de corrélation. Cette sortie peut être utilisée pour regrouper des valeurs en une seule observation dans les mappages de destination FHIR.|`$.matchedToken.correlationId`|
|Values[].ValueName|Nom à associer à la valeur extraite par l’expression suivante. Utilisé pour lier la valeur/le composant souhaité dans le modèle de mappage de destination FHIR.|`hr`|
|Values[].ValueExpression|Expression JSONPath pour extraire la valeur souhaitée.|`$.matchedToken.heartRate`|
|Values[].Required|Nécessite que la valeur soit présente dans la charge utile. S’il est introuvable, une mesure n’est pas générée et une exception InvalidOperationException est créée.|`true`|

### <a name="examples"></a>Exemples

**Fréquence cardiaque**

*Message*

```json
{
    "Body": {
        "heartRate": "78",
        "endDate": "2021-02-01T22:46:01.8750000Z",
        "deviceId": "device123"
    },
    "Properties": {},
    "SystemProperties": {}
}
```

*Modèle*

```json
{
    "templateType": "JsonPathContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@heartRate)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.heartRate",
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
        "diastolic" : "87",
        "endDate": "2021-02-01T22:46:01.8750000Z",
        "deviceId": "device123"
    },
    "Properties": {},
    "SystemProperties": {}
}
```

*Modèle*

```json
{
    "typeName": "bloodpressure",
    "typeMatchExpression": "$..[?(@systolic && @diastolic)]",
    "deviceIdExpression": "$.deviceId",
    "timestampExpression": "$.endDate",
    "values": [
        {
            "required": "true",
            "valueExpression": "$.systolic",
            "valueName": "systolic"
        },
        {
            "required": "true",
            "valueExpression": "$.diastolic",
            "valueName": "diastolic"
        }
    ]
}
```
**Projeter plusieurs mesures à partir d’un seul message**

*Message*

```json
{
    "Body": {
        "heartRate": "78",
        "steps": "2",
        "endDate": "2021-02-01T22:46:01.8750000Z",
        "deviceId": "device123"
    },
    "Properties": {},
    "SystemProperties": {}
}
```

*Modèle 1*

```json
{
    "templateType": "JsonPathContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@heartRate)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.heartRate",
                "valueName": "hr"
            }
        ]
    }
}
```

*Modèle 2*

```json
{
    "templateType": "JsonPathContent",
    "template": {
        "typeName": "stepcount",
        "typeMatchExpression": "$..[?(@steps)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.steps",
                "valueName": "steps"
            }
        ]
    }
}
```

**Projeter plusieurs mesures à partir d’un tableau dans un message**

*Message*

```json
{
    "Body": [
        {
            "heartRate": "78",
            "endDate": "2021-02-01T22:46:01.8750000Z",
            "deviceId": "device123"
        },
        {
            "heartRate": "81",
            "endDate": "2021-02-01T23:46:01.8750000Z",
            "deviceId": "device123"
        },
        {
            "heartRate": "72",
            "endDate": "2021-02-01T24:46:01.8750000Z",
            "deviceId": "device123"
        }
    ],
    "Properties": {},
    "SystemProperties": {}
}
```
*Modèle*

```json
{
    "templateType": "JsonPathContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@heartRate)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.heartRate",
                "valueName": "hr"
            }
        ]
    }
}
```

> [!TIP]
> Pour plus d’informations sur les erreurs et les problèmes courants, consultez le [Guide de dépannage](./iot-troubleshoot-guide.md) du connecteur IOT.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à utiliser les mappages de périphérique. Pour savoir comment utiliser les mappages de destination FHIR, consultez

>[!div class="nextstepaction"]
>[Utilisation des mappages de destination FHIR](how-to-use-fhir-mappings.md)

(FHIR&#174;) est une marque déposée de [HL7](https://hl7.org/fhir/) qui est utilisée avec l’autorisation de HL7.
