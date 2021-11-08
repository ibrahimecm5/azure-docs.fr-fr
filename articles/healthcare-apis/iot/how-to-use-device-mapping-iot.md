---
title: modèle de mappage d’appareil dans IoT Connector-api de santé Azure
description: Cet article explique comment utiliser le modèle de mappage d’appareil dans le IoT Connector.
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: how-to
ms.date: 11/05/2021
ms.author: jasteppe
ms.openlocfilehash: f11770a05d2429c87647b65a828f6477f1b1b8cb
ms.sourcegitcommit: 5af89a2a7b38b266cc3adc389d3a9606420215a9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2021
ms.locfileid: "131988183"
---
# <a name="how-to-use-device-mappings"></a>Comment utiliser les mappages de périphérique

> [!IMPORTANT]
> Les API Azure Healthcare sont actuellement en version préliminaire. L’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) contient des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore en disponibilité générale.

Le connecteur IoT requiert deux types de mappages JSON. Le premier type, le **mappage d’appareils**, est chargé de mapper les charges utiles d’appareils envoyées au point de terminaison `devicedata` Azure Event Hub. Il extrait les types, les identificateurs d’appareil, les dates et heures de mesure, ainsi que la ou les valeur(s) de mesure. 

Le deuxième type de **mappage de destination FHIR (Fast Healthcare Interoperability Resources&#174;)** contrôle le mappage de la ressource FHIR. Il permet de configurer la durée de la période d’observation, le type de données FHIR utilisé pour stocker les valeurs et le ou les code(s) de terminologie. 

Les deux types de mappages sont composés dans un document JSON en fonction de leur type. Ces documents JSON sont ensuite ajoutés à votre connecteur IoT via le Portail Azure. Le document de mappage d’appareil est ajouté via la page **mappage d’appareil** et le document de mappage de destination FHIR via la page de **destination** .

> [!NOTE]
> Les mappages sont stockés dans un stockage d’objets BLOB sous-jacent et chargés à partir d’un objet BLOB par exécution de calcul. Une fois mis à jour, ils doivent prendre effet immédiatement. 

> [!TIP]
> Consultez l’outil [Mappeur de données du connecteur IoMT](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper) pour la modification, le test et la résolution des problèmes des mappages de destination FHIR et des appareils IOT Connector. Exportez les mappages de chargement vers le connecteur IoT dans le Portail Azure ou utilisez avec la [version open source](https://github.com/microsoft/iomt-fhir) du connecteur IOT.

## <a name="device-mappings-overview"></a>Vue d’ensemble des mappages d’appareils

Les mappages d’appareils fournissent des fonctionnalités permettant d’extraire le contenu d’un message de périphérique dans un format commun pour une évaluation supplémentaire. Chaque message de périphérique reçu est évalué par rapport à tous les modèles de mappage d’appareil. 

Un seul message d’appareil entrant peut être divisé en plusieurs messages sortants qui sont ensuite mappés à différentes observations dans le service FHIR. 

Le résultat est un objet de données normalisé représentant la valeur ou les valeurs analysées par les modèles. 

Le modèle de données normalisé contient quelques propriétés obligatoires qui doivent être recherchées et extraites :

|Propriété|Description|
|--------|-----------|
|**Type**|Nom/type pour classifier la mesure. Cette valeur est utilisée pour établir une liaison avec le mappage de destination FHIR requis. Plusieurs mappages peuvent être générés dans le même type, ce qui vous permet de mapper différentes représentations sur plusieurs appareils à une sortie commune unique.|
|**OccurenceTimeUtc**|Heure à laquelle la mesure a été effectuée.|
|**DeviceId**|Identificateur de l’appareil. Cette valeur doit correspondre à un identificateur sur la ressource de l’appareil qui existe sur le service FHIR de destination.|
|**Propriétés**|Extrayez au moins une propriété afin que la valeur puisse être enregistrée dans la ressource d’observation créée. Les propriétés sont un regroupement de paires de valeurs clés extraites pendant la normalisation.|

> [!IMPORTANT]
> Le modèle normalisé complet est défini par l’interface [IMeasurement](https://github.com/microsoft/iomt-fhir/blob/master/src/lib/Microsoft.Health.Fhir.Ingest.Schema/IMeasurement.cs) .

Vous trouverez ci-dessous des exemples conceptuels de ce qui se passe pendant la normalisation et le processus de transformation dans IoT Connector :

:::image type="content" source="media/iot-data-normalization-high-level.png" alt-text="Diagramme de normalisation des données IoT example1" lightbox="media/iot-data-normalization-high-level.png":::

:::image type="content" source="media/concepts-iot-mapping-templates/normalization-example.png" alt-text="Workflow de normalisation des données IoT example2" lightbox="media/concepts-iot-mapping-templates/normalization-example.png":::

La charge utile de contenu est un message Azure Event Hub, qui est composé de trois parties : Corps, Propriétés et SystemProperties. Le `Body` est un tableau d’octets qui représente une chaîne encodée en UTF-8. Pendant l’évaluation du modèle, le tableau d’octets est automatiquement converti en valeur de chaîne. `Properties` est un regroupement de valeurs clés à utiliser par le créateur du message. `SystemProperties` est également un regroupement de valeurs clés réservé par l’infrastructure Azure Event Hub avec des entrées automatiquement remplies par celles-ci.

```json
{
    "Body": {
        "content": "value"
    },
    "Properties": {
        "key1": "value1",
        "key2": "value2"
    },
    "SystemProperties": {
        "x-opt-sequence-number": 1,
        "x-opt-enqueued-time": "2021-02-01T22:46:01.8750000Z",
        "x-opt-offset": 1,
        "x-opt-partition-key": "1"
    }
}
```
## <a name="mapping-with-jsonpath"></a>Mappage avec JSONPath

Les cinq types de mappage de contenu d’appareil pris en charge aujourd’hui s’appuient sur les JSONPath pour correspondre au mappage et aux valeurs extraites requis. Vous trouverez plus d’informations sur JSONPath [ici](https://goessner.net/articles/JsonPath/). Les cinq types de modèles utilisent l' [implémentation JSON .net](https://www.newtonsoft.com/json/help/html/QueryJsonSelectTokenJsonPath.htm) pour la résolution des expressions JSONPath.

Vous pouvez définir un ou plusieurs modèles dans le modèle de mappage d’appareil. Chaque message de l’appareil Event Hub reçu est évalué par rapport à tous les modèles de mappage d’appareil. 

Un seul message d’appareil entrant peut être divisé en plusieurs messages sortants qui sont ensuite mappés à différentes observations dans le service FHIR. 

Différents types de modèles existent et peuvent être utilisés lors de la génération du fichier de mappage d’appareil.

|Nom                                                                     | Description                                                                   |  
|-------------------------------------------------------------------------|-------------------------------------------------------------------------------|
|[JsonPathContentTemplate](#jsonpathcontenttemplate)                      |Modèle qui prend en charge l’écriture d’expressions à l’aide de JsonPath                  
|[CollectionContentTemplate](#collectioncontenttemplate)                  |Modèle utilisé pour représenter une liste de modèles qui seront utilisés pendant la normalisation.                                                            |                                                           
|[CalculatedContentTemplate](#calculatedcontenttemplate)                  |Modèle qui prend en charge l’écriture d’expressions à l’aide de plusieurs langages d’expression. Prend en charge la transformation des données via l’utilisation de fonctions JmesPath.|
|[IotJsonPathContentTemplate](#iotjsonpathcontenttemplate)                |Modèle qui prend en charge les messages envoyés à partir d’Azure IOT Hub ou la fonctionnalité d’exportation de données héritée d’Azure IOT central.                                        |
|[IotCentralJsonPathContentTemplate](#iotcentraljsonpathcontenttemplate)  |Modèle qui prend en charge les messages envoyés via la fonctionnalité d’exportation de données d’Azure IOT central.|  

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

## <a name="collectioncontenttemplate"></a>CollectionContentTemplate

Le CollectionContentTemplate peut être utilisé pour représenter une liste de modèles qui seront utilisés pendant la normalisation.
                                                             
### <a name="example"></a>Exemple

```json
{
  "templateType": "CollectionContent",
  "template": [
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
    },
    {
      "templateType": "CalculatedContent",
      "template": {
        "typeName": "stepcount",
        "typeMatchExpression": "$..[?(@steps)]",
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
  ]
}
```

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

| Langage d’expression | Valeur        |
|---------------------|--------------|
| JSONPath            | **JsonPath** |
| JmesPath            | **JmesPath** |

>[!TIP]
>Pour plus d’informations sur JSONPath, consultez [JSONPath](https://goessner.net/articles/JsonPath/). [CalculatedContentTemplate](#calculatedcontenttemplate) utilise l' [implémentation JSON .net](https://www.newtonsoft.com/json/help/html/QueryJsonSelectTokenJsonPath.htm) pour la résolution des expressions JSONPath.
>
>Pour plus d’informations sur JmesPath, consultez [JmesPath](https://jmespath.org/specification.html). [CalculatedContentTemplate](#calculatedcontenttemplate) utilise l' [implémentation JmesPath .net](https://github.com/jdevillard/JmesPath.Net) pour la résolution d’expressions JmesPath.

### <a name="custom-functions"></a>Fonctions personnalisées

Un ensemble de fonctions personnalisées de connecteur IoT est également disponible. Ces fonctions personnalisées sont en dehors des fonctions fournies dans le cadre de la spécification JmesPath. Pour plus d’informations sur les fonctions personnalisées du connecteur IoT, consultez la page [fonctions client du connecteur IOT](./iot-connector-custom-functions.md).

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

**Project Plusieurs mesures à partir d’un seul message**

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

**Project Plusieurs mesures d’un tableau dans un message**

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

**Project Données du jeton et de l’événement d’origine correspondants**

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
>[Utilisation des mappages de destination FHIR](how-to-use-fhir-mapping-iot.md)

(FHIR&#174;) est une marque déposée de [HL7](https://hl7.org/fhir/) qui est utilisée avec l’autorisation de HL7.
