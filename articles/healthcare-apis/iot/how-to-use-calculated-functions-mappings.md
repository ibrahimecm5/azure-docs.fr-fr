---
title: mappages de CalculatedContentTemplate dans IoT Connector mappages d’appareils-api Azure Healthcare
description: Cet article explique comment utiliser les mappages CalculatedContentTemplate avec les modèles de mappages d’appareils IoT Connector.
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: how-to
ms.date: 11/16/2021
ms.author: jasteppe
ms.openlocfilehash: 3e1ccf989bcd67e5e45d381ae681287730cad1d2
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132733417"
---
# <a name="how-to-use-calculatedcontenttemplate-mappings"></a>Utilisation des mappages de CalculatedContentTemplate

> [!IMPORTANT]
> Les API Azure Healthcare sont actuellement en version préliminaire. L’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) contient des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore en disponibilité générale.

> [!TIP]
> Consultez l’outil [Mappeur de données du connecteur IoMT](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper) pour la modification, le test et la résolution des problèmes des mappages de destination FHIR et des appareils IOT Connector. Exportez les mappages de chargement vers le connecteur IoT dans le Portail Azure ou utilisez avec la [version open source](https://github.com/microsoft/iomt-fhir) du connecteur IOT.

Cet article explique comment utiliser les mappages CalculatedContentTemplate avec les modèles de mappages d’appareils IoT Connector.

## <a name="calculatedcontenttemplate"></a>CalculatedContentTemplate

Le connecteur IoT fournit un modèle de contenu basé sur une expression qui correspond à la fois au modèle souhaité et à l’extraction des valeurs. Les **expressions** peuvent être utilisées par JSONPath ou JmesPath. Chaque expression contenue dans le modèle peut choisir son propre langage d’expression. 

> [!NOTE]
> Si un langage d’expression n’est pas défini, le langage d’expression par défaut configuré pour le modèle sera utilisé. La valeur par défaut est JSONPath, mais peut être remplacée si nécessaire.

Une expression est définie comme suit :

```json
<name of expression> : {
        "value" : <the expression>,
        "language": <the expression language>
    }
```

Dans l’exemple ci-dessous, *typeMatchExpression* est défini comme suit :

```json
"templateType": "CalculatedContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": {
            "value" : "$..[?(@heartRate)]",
            "language": "JsonPath"
        },
        ...
    }
```
> [!TIP]
> Le langage d’expression par défaut à utiliser pour un modèle de mappage d’appareil est JsonPath. Si vous souhaitez utiliser JsonPath, l’expression seule peut être fournie.

```json
"templateType": "CalculatedContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@heartRate)]",
        ...
    }
```

Le langage d’expression par défaut à utiliser pour un modèle peut être défini explicitement à l’aide du `defaultExpressionLanguage` paramètre :

```json
"templateType": "CalculatedContent",
    "template": {
        "typeName": "heartrate",
        "defaultExpressionLanguage": "JsonPath",
        "typeMatchExpression": "$..[?(@heartRate)]",
        ...
    }
```

Le CalculatedContentTemplate permet la correspondance et l’extraction des valeurs d’un message Azure Event Hub à l’aide d' **expressions** définies ci-dessous :

|Propriété|Description|Exemple|
|--------|-----------|-------|
|TypeName|Type à associer aux mesures qui correspondent au modèle|`heartrate`|
|TypeMatchExpression|Expression évaluée par rapport à la charge utile EventData. Si un JToken correspondant est trouvé, le modèle est considéré comme une correspondance. Toutes les expressions ultérieures sont évaluées par rapport aux JToken extraits correspondantes.|`$..[?(@heartRate)]`|
|TimestampExpression|Expression permettant d’extraire la valeur d’horodatage pour le OccurrenceTimeUtc de la mesure.|`$.matchedToken.endDate`|
|DeviceIdExpression|Expression permettant d’extraire l’identificateur de l’appareil.|`$.matchedToken.deviceId`|
|PatientIdExpression|*Obligatoire* lorsque IdentityResolution est en mode **création** et *facultatif* lorsque IdentityResolution est en mode de **recherche** . Expression permettant d’extraire l’identificateur du patient.|`$.matchedToken.patientId`|
|EncounterIdExpression|*Facultatif*: l’expression pour extraire l’identificateur de la rencontre.|`$.matchedToken.encounterId`|
|CorrelationIdExpression|*Facultatif*: l’expression pour extraire l’identificateur de corrélation. Cette sortie peut être utilisée pour regrouper des valeurs en une seule observation dans les mappages de destination FHIR.|`$.matchedToken.correlationId`|
|Values[].ValueName|Nom à associer à la valeur extraite par l’expression suivante. Utilisé pour lier la valeur/le composant souhaité dans le modèle de mappage de destination FHIR.|`hr`|
|Values[].ValueExpression|Expression permettant d’extraire la valeur souhaitée.|`$.matchedToken.heartRate`|
|Values[].Required|Nécessite que la valeur soit présente dans la charge utile. S’il est introuvable, une mesure n’est pas générée et une exception InvalidOperationException est créée.|`true`|

### <a name="expression-languages"></a>Langages des expressions

Lorsque vous spécifiez la langue à utiliser pour l’expression, les valeurs ci-dessous sont valides :

| Langage d’expression | Value        |
|---------------------|--------------|
| JSONPath            | **JsonPath** |
| JmesPath            | **JmesPath** |

>[!TIP]
>Pour plus d’informations sur JSONPath, consultez [JSONPath](https://goessner.net/articles/JsonPath/). [CalculatedContentTemplate](#calculatedcontenttemplate) utilise l' [implémentation JSON .net](https://www.newtonsoft.com/json/help/html/QueryJsonSelectTokenJsonPath.htm) pour la résolution des expressions JSONPath.
>
>Pour plus d’informations sur JmesPath, consultez [JmesPath](https://jmespath.org/specification.html). [CalculatedContentTemplate](#calculatedcontenttemplate) utilise l' [implémentation JmesPath .net](https://github.com/jdevillard/JmesPath.Net) pour la résolution d’expressions JmesPath.

### <a name="custom-functions"></a>Fonctions personnalisées

Un ensemble de fonctions personnalisées de connecteur IoT est également disponible. Ces fonctions personnalisées sont en dehors des fonctions fournies dans le cadre de la spécification JmesPath. Pour plus d’informations sur les fonctions personnalisées, consultez [fonctions personnalisées du connecteur IOT](./how-to-use-custom-functions.md).

### <a name="matched-token"></a>Jeton correspondant

Le **TypeMatchExpression** est évalué par rapport à la charge utile des EventData entrants. Si un JToken correspondant est trouvé, le modèle est considéré comme une correspondance. 

Toutes les expressions ultérieures sont évaluées par rapport à un nouveau JToken. Ce nouveau JToken contient à la fois la charge utile de EventData d’origine et le JToken extrait correspondant ici. 

De cette façon, la charge utile d’origine et l’objet correspondant sont disponibles pour chaque expression ultérieure. Le JToken extrait sera disponible en tant que propriété **matchedToken**.

Prenons l’exemple de message suivant :

*Message*

```json
{
  "Body": {
    "deviceId": "device123",
    "data": [
      {
        "systolic": "120", // Match
        "diastolic": "80", // Match 
        "date": "2021-07-13T17:29:01.061144Z"
      },
      {
        "systolic": "122", // Match
        "diastolic": "82", // Match
        "date": "2021-07-13T17:28:01.061122Z"
      }
    ]
  },
  "Properties": {},
  "SystemProperties": {}
}
```

*Modèle*

```json
{
  "templateType": "CollectionContent",
  "template": [
    {
      "templateType": "CalculatedContent",
      "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@systolic && @diastolic)]", // Expression
        "deviceIdExpression": "$.Body.deviceId", // This accesses the attribute 'deviceId' which belongs to the original event data
        "timestampExpression": "$.matchedToken.date", 
        "values": [
          {
            "required": "true",
            "valueExpression": "$.matchedToken.systolic",
            "valueName": "systolic"
          },
          {
            "required": "true",
            "valueExpression": "$.matchedToken.diastolic",
            "valueName": "diastolic"
          }
        ]
      }
    }
  ]
}
```

Deux correspondances sont extraites à l’aide de l’expression ci-dessus et utilisées pour créer JTokens. Les expressions ultérieures seront évaluées à l’aide de la JTokens suivante :

```json
{
  "Body": {
    "deviceId": "device123",
    "data": [
      {
        "systolic": "120", 
        "diastolic": "80",
        "date": "2021-07-13T17:29:01.061144Z"
      },
      {
        "systolic": "122",
        "diastolic": "82",
        "date": "2021-07-13T17:28:01.061122Z"
      }
    ]
  },
  "Properties": {},
  "SystemProperties": {},
  "matchedToken" : {
      "systolic": "120",
      "diastolic": "80",
      "date": "2021-07-13T17:29:01.061144Z"
  }
}
```

and

```json
{
  "Body": {
    "deviceId": "device123",
    "data": [
      {
        "systolic": "120",
        "diastolic": "80",
        "date": "2021-07-13T17:29:01.061144Z"
      },
      {
        "systolic": "122", 
        "diastolic": "82", 
        "date": "2021-07-13T17:28:01.061122Z"
      }
    ]
  },
  "Properties": {},
  "SystemProperties": {},
  "matchedToken" : {
      "systolic": "122",
      "diastolic": "82",
      "date": "2021-07-13T17:28:01.061122Z"
    }
  }
}
```

### <a name="examples"></a>Exemples

**Taux cardiaque**

*Message*

```json
{
  "Body": {
    "heartRate": "78",
    "endDate": "2019-02-01T22:46:01.8750000Z",
    "deviceId": "device123"
  },
  "Properties": {},
  "SystemProperties": {}
}
```

*Modèle*

```json
    {
      "templateType": "CalculatedContent",
      "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@heartRate)]",
        "deviceIdExpression": "$.matchedToken.deviceId",
        "timestampExpression": "$.matchedToken.endDate",
        "values": [
          {
            "required": "true",
            "valueExpression": "$.matchedToken.heartRate",
            "valueName": "hr"
          }
        ]
      }
    }
```

**Sollicitation du sang**

*Message*

```json
{
    "Body": {
        "systolic": "123", // Match
        "diastolic" : "87", // Match
        "endDate": "2019-02-01T22:46:01.8750000Z",
        "deviceId": "device123"
    },
    "Properties": {},
    "SystemProperties": {}
}
```

*Modèle*

```json
    {
      "templateType": "CalculatedContent",
      "template": {
        "typeName": "bloodpressure",
        "typeMatchExpression": "$..[?(@systolic && @diastolic)]", // Expression
        "deviceIdExpression": "$.matchedToken.deviceId",
        "timestampExpression": "$.matchedToken.endDate",
        "values": [
          {
            "required": "true",
            "valueExpression": "$.matchedToken.systolic",
            "valueName": "systolic"
          },
          {
            "required": "true",
            "valueExpression": "$.matchedToken.diastolic",
            "valueName": "diastolic"
          }
        ]
      }
    }
```

**Project plusieurs mesures à partir d’un seul Message**

*Message*

```json
{
    "Body": {
        "heartRate": "78", // Match (Template 1)
        "steps": "2", // Match (Template 2)
        "endDate": "2019-02-01T22:46:01.8750000Z",
        "deviceId": "device123"
    },
    "Properties": {},
    "SystemProperties": {}
}
```

*Modèle 1*

```json
    {
      "templateType": "CalculatedContent",
      "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@heartRate)]", // Expression
        "deviceIdExpression": "$.matchedToken.deviceId",
        "timestampExpression": "$.matchedToken.endDate",
        "values": [
          {
            "required": "true",
            "valueExpression": "$.matchedToken.heartRate",
            "valueName": "hr"
          }
        ]
      }
    },
```

*Modèle 2*

```json
    {
      "templateType": "CalculatedContent",
      "template": {
        "typeName": "stepcount",
        "typeMatchExpression": "$..[?(@steps)]", // Expression
        "deviceIdExpression": "$.matchedToken.deviceId",
        "timestampExpression": "$.matchedToken.endDate",
        "values": [
          {
            "required": "true",
            "valueExpression": "$.matchedToken.steps",
            "valueName": "steps"
          }
        ]
      }
    }
```

**Project plusieurs mesures à partir d’un tableau dans un Message**

*Message*

```json
{
  "Body": [
    {
      "heartRate": "78", // Match
      "endDate": "2019-02-01T20:46:01.8750000Z",
      "deviceId": "device123"
    },
    {
      "heartRate": "81", // Match
      "endDate": "2019-02-01T21:46:01.8750000Z",
      "deviceId": "device123"
    },
    {
      "heartRate": "72", // Match
      "endDate": "2019-02-01T22:46:01.8750000Z",
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
      "templateType": "CalculatedContent",
      "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@heartRate)]", // Expression
        "deviceIdExpression": "$.matchedToken.deviceId",
        "timestampExpression": "$.matchedToken.endDate",
        "values": [
          {
            "required": "true",
            "valueExpression": "$.matchedToken.heartRate",
            "valueName": "hr"
          }
        ]
      }
    }
```

**Project des données à partir d’un jeton et d’un événement d’origine correspondants**

*Message*

```json
{
  "Body": {
    "deviceId": "device123",
    "data": [
      {
        "systolic": "120", // Match
        "diastolic": "80", // Match 
        "date": "2021-07-13T17:29:01.061144Z"
      },
      {
        "systolic": "122", // Match
        "diastolic": "82", // Match
        "date": "2021-07-13T17:28:01.061122Z"
      }
    ]
  },
  "Properties": {},
  "SystemProperties": {}
}
```

*Modèle*

```json
    {
      "templateType": "CalculatedContent",
      "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@systolic && @diastolic)]", // Expression
        "deviceIdExpression": "$.Body.deviceId", // This accesses the attribute 'deviceId' which belongs to the original event data
        "timestampExpression": "$.matchedToken.date", 
        "values": [
          {
            "required": "true",
            "valueExpression": "$.matchedToken.systolic",
            "valueName": "systolic"
          },
          {
            "required": "true",
            "valueExpression": "$.matchedToken.diastolic",
            "valueName": "diastolic"
          }
        ]
      }
    }
```

**Sélectionner et transformer des données entrantes**

Dans l’exemple ci-dessous, les données de hauteur arrivent en pouces ou en mètres. Nous voulons que toutes les données de hauteur normalisées soient en mètres. Pour atteindre ce résultat, nous créons un modèle qui cible uniquement les données de hauteur en pouces et les transforme en mètres. Un autre modèle cible les données de hauteur en mètres et les stocke simplement en l’étant.

*Message*

```json
{
  "Body": [
    {
      "height": "78",
      "unit": "inches", // Match (Template 1)
      "endDate": "2019-02-01T22:46:01.8750000Z",
      "deviceId": "device123"
    },
    {
      "height": "1.9304",
      "unit": "meters", // Match (Template 2)
      "endDate": "2019-02-01T23:46:01.8750000Z",
      "deviceId": "device123"
    }
  ],
  "Properties": {},
  "SystemProperties": {}
}
```

*Modèle 1*

```json
    {
      "templateType": "CalculatedContent",
      "template": {
        "typeName": "heightInMeters",
        "typeMatchExpression": "$..[?(@unit == 'inches')]",
        "deviceIdExpression": "$.matchedToken.deviceId",
        "timestampExpression": "$.matchedToken.endDate",
        "values": [
          {
            "required": "true",
            "valueExpression": {
              "value": "multiply(to_number(matchedToken.height), `0.0254`)", // Convert inches to meters. Notice we utilize JmesPath as that gives us access to transformation functions
              "language": "JmesPath"
            },
            "valueName": "height"
          }
        ]
      }
    }
```

*Modèle 2*

```json
    {
      "templateType": "CalculatedContent",
      "template": {
        "typeName": "heightInMeters",
        "typeMatchExpression": "$..[?(@unit == 'meters')]",
        "deviceIdExpression": "$.matchedToken.deviceId",
        "timestampExpression": "$.matchedToken.endDate",
        "values": [
          {
            "required": "true",
            "valueExpression": "$.matchedToken.height", // Simply extract the height as it is already in meters
            "valueName": "height"
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
