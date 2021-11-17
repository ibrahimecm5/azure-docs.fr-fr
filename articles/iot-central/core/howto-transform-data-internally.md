---
title: Transformer des données dans Azure IoT Central | Microsoft Docs
description: Les appareils IoT envoient des données dans différents formats que vous devrez peut-être transformer. Cet article explique comment transformer des données dans IoT Central avant de les exporter.
author: dominicbetts
ms.author: dobett
ms.date: 10/28/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: c886fd71399f84f04e7ac83ff24059dd658f0a4b
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131579423"
---
# <a name="transform-data-inside-your-iot-central-application-for-export"></a>Transformer des données à l’intérieur de votre application IoT Central pour les exporter

:::image type="content" source="media/howto-transform-data-internally/transformations.png" alt-text="Diagramme montrant les options de transformation d’exportation de données." border="false":::

Les appareils IoT envoient des données dans différents formats. Pour utiliser les données d’un appareil dans votre solution IoT, il se peut que vous deviez transformer les données avant de les exporter vers d’autres services.

Cet article explique comment transformer des données d’appareil dans le cadre d’une définition d’exportation de données dans une application IoT Central.

Les transformations dans une définition d’exportation de données IoT Central vous permettent de manipuler le format et la structure des données d’un appareil avant leur exportation vers une destination. Vous pouvez spécifier une transformation pour chaque destination dans une définition d’exportation. Chaque message passe par la transformation afin de créer un enregistrement de sortie à exporter vers la destination.

Utilisez des transformations pour restructurer des charges utiles JSON, renommer des champs, filtrer des champs et exécuter des calculs simples sur des valeurs de télémétrie. Par exemple, utilisez une transformation pour convertir vos messages dans un format tabulaire correspondant au schéma d’une destination telle qu’une table Azure Data Explorer.

## <a name="add-a-transformation"></a>Ajouter une transformation

Pour ajouter une transformation pour une destination dans votre exportation de données, sélectionnez **+ Transformer** comme dans la capture d’écran suivante :

:::image type="content" source="media/howto-transform-data-internally/add-transformation.png" alt-text="Capture d’écran montrant comment ajouter une transformation à une destination.":::

Le panneau **Transformation des données** vous permet de spécifier la transformation. Dans la section **1. Ajouter votre message entrant**, vous pouvez entrer un échantillon de message à transformer. Vous pouvez également générer un échantillon de message en sélectionnant un modèle d’appareil. Dans la section **2. Créer une requête de transformation**, vous pouvez entrer la requête qui transforme le message entrant. La section **3. Voir un aperçu des messages sortants** affiche le résultat de la transformation :

:::image type="content" source="media/howto-transform-data-internally/transformation-editor.png" alt-text="Capture d’écran de l’éditeur de transformation dans IoT Central.":::

> [!TIP]
> Si vous ne connaissez pas le format de votre message entrant, utilisez `.` en tant que requête pour exporter le message tel quel vers une destination telle qu’un webhook. Collez ensuite le message reçu par le webhook dans ***1. Ajouter votre message entrant**. Générez ensuite une requête de transformation pour traiter ce message dans le format de sortie requis.

## <a name="build-a-transformation-query"></a>Créer une requête de transformation

Le moteur de transformation utilise le processeur JSON [JQ](https://stedolan.github.io/jq/) open source pour restructurer et mettre en forme les charges utiles JSON. Pour spécifier une transformation, vous écrivez une requête JQ qui peut utiliser les filtres, fonctions et fonctionnalités intégrés de JQ. Pour obtenir des exemples de requêtes, consultez [Exemples de requêtes de transformation](#example-transformation-queries). Pour en savoir plus sur l’écriture de requêtes JQ, consultez le [Manuel JQ](https://stedolan.github.io/jq/manual/).

## <a name="pre-transformation-message-structure"></a>Structure de message avant transformation

Vous pouvez exporter les flux de données suivants à partir d’IoT Central : télémétrie, changements de propriétés, événements de connexion d’appareils, événements de cycle de vie d’appareils et événements de cycle de vie de modèles d’appareil. Chaque type de données a une structure spécifique incluant des informations telles que des valeurs de télémétrie, des informations sur l’application, des métadonnées d’appareil et des valeurs de propriété.

L’exemple suivant illustre la forme d’un message de télémétrie. Toutes ces données sont disponibles pour votre transformation. La structure du message est similaire pour d’autres types de messages, mais il existe des champs spécifiques du type. Vous pouvez utiliser la fonctionnalité **Ajouter votre message entrant** afin de générer un échantillon de message basé sur un modèle d’appareil dans votre application.

```json
{
  "applicationId": "93d68c98-9a22-4b28-94d1-06625d4c3d0f",
  "device": {
    "id": "31edabe6-e0b9-4c83-b0df-d12e95745b9f",
    "name": "Scripted Device - 31edabe6-e0b9-4c83-b0df-d12e95745b9f",
    "cloudProperties": [],
    "properties": {
      "reported": [
        {
          "id": "urn:smartKneeBrace:Smart_Vitals_Patch_wr:FirmwareVersion:1",
          "name": "FirmwareVersion",
          "value": 1.0
        }
      ]
    },
    "templateId": "urn:sbq3croo:modelDefinition:nf7st1wn3",
    "templateName": "Smart Knee Brace"
  },
  "telemetry": [
      {
        "id": "urn:continuousPatientMonitoringTemplate:Smart_Knee_Brace_6wm:Acceleration:1",
        "name": "Acceleration",
        "value": {
          "x": 19.212770659918583,
          "y": 20.596296675217335,
          "z": 54.04859440697045
        }
      },
      {
        "id": "urn:continuousPatientMonitoringTemplate:Smart_Knee_Brace_6wm:RangeOfMotion:1",
        "name": "RangeOfMotion",
        "value": 110
      }
  ],
  "enqueuedTime": "2021-03-23T19:55:56.971Z",
  "enrichments": {
      "your-enrichment-key": "enrichment-value"
  },
  "messageProperties": {
      "prop1": "prop-value"
  },
  "messageSource": "telemetry"
}
```

> [!TIP]
> Utilisez la fonctionnalité **Ajouter votre message entrant** dans l’interface utilisateur de l’application IOT Central afin de voir des exemples de structures de message pour d’autres types d’exportation de données, tels que des changements de propriétés.

## <a name="example-transformation-queries"></a>Exemples de requêtes de transformation

Les exemples de requêtes suivants utilisent le message de télémétrie présenté dans la section précédente.

**Exemple 1** : la requête JQ suivante génère chaque élément de télémétrie à partir du message d’entrée sous la forme d’un message sortant distinct :

```jq
.telemetry[]
```

Sortie JSON :

```json
{
  "id": "urn:continuousPatientMonitoringTemplate:Smart_Knee_Brace_6wm:Acceleration:1",
  "name": "Acceleration",
  "value": {
    "x": 19.212770659918583,
    "y": 20.596296675217335,
    "z": 54.04859440697045
  }
},
{
  "id": "urn:continuousPatientMonitoringTemplate:Smart_Knee_Brace_6wm:RangeOfMotion:1",
  "name": "RangeOfMotion",
  "value": 110
}
```

> [!TIP]
> Pour convertir la sortie en un seul message avec un tableau de types de télémétries, utilisez la requête `.telemetry`.

**Exemple 2** : la requête JQ suivante convertit le tableau de télémétrie entrante en un objet dans lequel les noms de télémétrie sont les clés :

```jq
.telemetry | map({ key: .name, value: .value }) | from_entries
```

Sortie JSON :

```json
{
  "Acceleration": {
    "x": 19.212770659918583,
    "y": 20.596296675217335,
    "z": 54.04859440697045
  },
  "RangeOfMotion": 110
}
```

**Exemple 3** : la requête JQ suivante trouve la valeur de télémétrie **RangeOfMotion** et la convertit de degrés en radians à l’aide de la formule `rad = degree * pi / 180`. Cette requête montre également comment importer et utiliser le module `iotc` :

```jq
import "iotc" as iotc;
{
  rangeOfMotion: (
    .telemetry
    | iotc::find(.name == "RangeOfMotion").value
    | . * 3.14159265358979323846 / 180
  )
}
```

Sortie JSON :

```json
{
  "rangeOfMotion": 1.9198621771937625
}
```

**Exemple 4** : pour manipuler le message entrant dans un format tabulaire, vous pouvez mapper chaque message exporté à une ou plusieurs *lignes*. La sortie de ligne est représentée logiquement sous la forme d’un objet JSON où le nom de colonne est la clé, et la valeur de colonne est la valeur :

```json
{
    "<column 1 name>": "<column 1 value>",
    "<column 2 name>": "<column 2 value>",
    ...
}
```

> [!TIP]
> Utilisez un format tabulaire lorsque vous exportez vers Azure Data Explorer.

La requête JQ suivante écrit des lignes dans une table qui stocke la télémétrie **rangeOfMotion** de différents appareils. La requête mappe l’ID d’appareil, l’heure de mise en file d’attente et la plage de déplacement dans une table avec les colonnes suivantes :

```jq
import "iotc" as iotc;
{
    deviceId: .deviceId,
    timestamp: .enqueuedTime,
    rangeOfMotion: .telemetry | iotc::find(.name == "RangeOfMotion").value
}
```

Sortie au format JSON :

```json
{
  "deviceId": "31edabe6-e0b9-4c83-b0df-d12e95745b9f",
  "timestamp": "2021-03-23T19:55:56.971Z",
  "rangeOfMotion": 110
}
```

## <a name="iot-central-module"></a>Module IoT Central

Un module JQ est une collection de fonctions personnalisées. Dans le cadre de votre requête de transformation, vous pouvez importer un module IoT Central spécifique intégré contenant des fonctions facilitant l’écriture de vos requêtes. Pour importer le module IoT Central, utilisez la directive suivante :

```jq
import "iotc" as iotc;
```

Le module IoT Central comprend les fonctions suivantes :

`find(expression)` : la fonction `find` vous permet de rechercher un élément de tableau spécifique, tel qu’une valeur de télémétrie ou une entrée de propriété dans votre charge utile. L’entrée de la fonction est un tableau et le paramètre définit un filtre JQ à exécuter sur chaque élément du tableau. La fonction retourne chaque élément de tableau où le filtre trouve la valeur true :

Par exemple, pour rechercher une valeur de télémétrie spécifique appelée `RangeOfMotion` :

```jq
.telemetry | iotc::find(.name == "RangeOfMotion")
```

## <a name="scenarios"></a>Scénarios

Les scénarios suivants utilisent la fonctionnalité de transformation afin de personnaliser le format de données de l’appareil pour une destination spécifique.

### <a name="scenario-1-export-device-data-to-azure-data-explorer"></a>Scénario 1 : exportation des données d’un appareil vers Azure Data Explorer

Dans ce scénario, vous transformez les données d’un appareil pour les faire correspondre au schéma fixe dans Azure Data Explorer, où chaque valeur de télémétrie apparaît sous la forme d’une colonne dans la table et chaque ligne représente un message unique. Par exemple :

| deviceId | Timestamp | T1 | T2 |T3 |
| :------------- | :---------- | :----------- | :---------- | :----------- |
| "31edabe6-e0b9-4c83-b0df-d12e95745b9f" |"2021-03-23T19:55:56.971Z | 1,18898 | 1,434709 | 2,97008 |

Pour exporter des données compatibles avec cette table, chaque message exporté doit ressembler à l’objet suivant. L’objet représente une ligne unique, où les clés sont les noms de colonnes et les valeurs sont celles à placer dans chaque colonne :

```json
{
    "Timestamp": <value-of-Timestamp>,
    "DeviceId": <value-of-deviceId>,
    "T1": <value-of-T1>,
    "T2": <value-of-T2>,
    "T3": <value-of-T3>,
}
```

Dans ce scénario, l’appareil envoie les valeurs de télémétrie `t1`, `t2` et `t3` dans un message entrant ressemblant à l’exemple suivant :

```json
{
  "applicationId": "c57fe8d9-d15d-4659-9814-d3cc38ca9e1b",
  "enqueuedTime": "1933-01-26T03:10:44.480001324Z",
  "messageSource": "telemetry",
  "telemetry": [
    {
      "id": "dtmi:temperaturesensor288:sensors1lr:t1;1",
      "name": "t1",
      "value": 1.1889838348731093e+308
    },
    {
      "id": "dtmi:temperaturesensor288:sensors1lr:t2;1",
      "name": "t2",
      "value": 1.4347093391531383e+308
    },
    {
      "id": "dtmi:temperaturesensor288:sensors1lr:t3;1",
      "name": "t3",
      "value": 2.9700885230380616e+307
    }
  ],
  "device": {
    "id": "oozrnl1zs857",
    "name": "haptic alarm",
    "templateId": "dtmi:modelDefinition:nhhbjotee:qytxnp8hi",
    "templateName": "hapticsensors",
    "properties": {
      "reported": []
    },
    "cloudProperties": [],
    "simulated": true,
    "approved": false,
    "blocked": false,
    "provisioned": true
  }
}
```

La requête JQ suivante génère les valeurs de télémétrie `T1`, `T2` et `T3`, ainsi que le `Timestamp` et le `deviceId` sous la forme d’un message avec des paires clé-valeur correspondant au schéma de table Azure Data Explorer :

```jq
import "iotc" as iotc;
{
  deviceId: .device.id,
  Timestamp: .enqueuedTime,
  T1: .telemetry | iotc::find(.name == "t1").value,
  T2: .telemetry | iotc::find(.name == "t2").value,
  T3: .telemetry | iotc::find(.name == "t3").value,
}
```

Sortie JSON :

```json
{
  "T1": 1.1889838348731093e+308,
  "T2": 1.4347093391531383e+308,
  "T3": 2.9700885230380616e+307,
  "Timestamp": "1933-01-26T03:10:44.480001324Z",
  "deviceId": "oozrnl1zs857"
}
```

Pour en savoir plus sur la façon d’ajouter un cluster Azure Data Explorer et une base de données en tant que destination d’exportation, consultez [Créer une destination Azure Data Explorer](howto-export-data.md#create-an-azure-data-explorer-destination).

### <a name="scenario-2-breaking-apart-a-telemetry-array"></a>Scénario 2 : décomposition d’un tableau de télémétrie

Dans ce scénario, l’appareil envoie le tableau de télémétrie suivant dans un message :

```json
{
  "applicationId": "570c2d7b-d72e-4ad1-aaf4-ad9b727daa47",
  "enqueuedTime": "1909-10-10T07:11:56.078161042Z",
  "messageSource": "telemetry",
  "telemetry": [
    {
      "id": "dtmi:sample1:data;1",
      "name": "data",
      "value": [
        {
          "id": "subdevice1",
          "values": {
              "running": true,
              "cycleCount": 2315
          }
        },
        {
          "id": "subdevice2",
          "values": {
              "running": false,
              "cycleCount": 824567
          }
        }
      ]
    },
    {
      "id": "dtmi:sample1:parentStatus;1",
      "name": "parentStatus",
      "value": "healthy"
    }
  ],
  "device": {
    "id": "9xwhr7khkfri",
    "name": "wireless port",
    "templateId": "dtmi:hpzy1kfcbt2:umua7dplmbd",
    "templateName": "Smart Vitals Patch",
    "properties": {
      "reported": [
        {
          "id": "dtmi:sample1:prop;1",
          "name": "Connectivity",
          "value": "Tenetur ut quasi minus ratione voluptatem."
        }
      ]
    },
    "cloudProperties": [],
    "simulated": true,
    "approved": true,
    "blocked": false,
    "provisioned": false
  }
}
```

Vous souhaitez transformer ces données d’appareil pour qu’elles correspondent au schéma de tableau suivant :

| cycleCount | deviceId | enqueuedTime | parentStatus | exécution en cours | subdeviceId |
| :--------- | :------- | :----------- | :----------- | :------ | :---------- |
| 2315   | "9xwhr7khkfri" | "1909-10-10T07:11:56.078161042Z" | "healthy" | true | "subdevice1" |
| 824567 USD | "9xwhr7khkfri" | "1909-10-10T07:11:56.078161042Z" | "healthy" | false | "subdevice2" |

La requête JQ suivante crée un message sortant distinct pour chaque entrée de sous-appareil dans le message, et inclut des informations communes du message de base et de l’appareil parent. Cette requête aplatit la sortie et sépare les divisions logiques dans vos données qui sont arrivées sous la forme d’un message unique :

```jq
import "iotc" as iotc;
{
    enqueuedTime: .enqueuedTime,
    deviceId: .device.id,
    parentStatus: .telemetry | iotc::find(.name == "parentStatus").value
} + (
    .telemetry
    | iotc::find(.name == "data").value[]
    | {
        subdeviceId: .id,
        running: .values.running,
        cycleCount: .values.cycleCount
    }
)
```

Sortie JSON :

```json
{
    "cycleCount": 2315,
    "deviceId": "9xwhr7khkfri",
    "enqueuedTime": "1909-10-10T07:11:56.078161042Z",
    "parentStatus": "healthy",
    "running": true,
    "subdeviceId": "subdevice1"
},
{
    "cycleCount": 824567,
    "deviceId": "9xwhr7khkfri",
    "enqueuedTime": "1909-10-10T07:11:56.078161042Z",
    "parentStatus": "healthy",
    "running": false,
    "subdeviceId": "subdevice2"
}
```

### <a name="scenario-3-power-bi-streaming"></a>Scénario 3 : diffusion en continu de Power BI

La fonctionnalité de diffusion en continu en temps réel de Power BI vous permet d’afficher les données d’un tableau de bord mis à jour en temps réel avec une faible latence. Pour plus d’informations, consultez [Diffusion en continu en temps réel dans Power BI](/power-bi/connect-data/service-real-time-streaming).

Pour utiliser IoT Central avec la diffusion en continu de Power BI, configurez une exportation de webhook qui envoie des corps de demande dans un format spécifique. Cet exemple est basé sur l’hypothèse que vous disposez d’un jeu de données de diffusion en continu Power BI dont le schéma est le suivant :

```json
[
  {
    "bloodPressureDiastolic": 161438124,
    "bloodPressureSystolic": -966387879,
    "deviceId": "9xwhr7khkfri",
    "deviceName": "wireless port",
    "heartRate": -633994413,
    "heartRateVariability": -37514094,
    "respiratoryRate": 1582211310,
    "timestamp": "1909-10-10T07:11:56.078161042Z"
  }
]
```

Pour créer la destination d’exportation du webhook, vous avez besoin du point de terminaison URL d’API REST pour votre jeu de données de diffusion en continu Power BI.

Dans ce scénario, l’appareil envoie des messages de télémétrie qui ressemblent à l’exemple suivant :

```json
{
  "applicationId": "570c2d7b-d72e-4ad1-aaf4-ad9b727daa47",
  "enqueuedTime": "1909-10-10T07:11:56.078161042Z",
  "messageSource": "telemetry",
  "telemetry": [
    {
      "id": "dtmi:smartVitalsPatch:Smart_Vitals_Patch_37p:HeartRate;1",
      "name": "HeartRate",
      "value": -633994413
    },
    {
      "id": "dtmi:smartVitalsPatch:Smart_Vitals_Patch_37p:RespiratoryRate;1",
      "name": "RespiratoryRate",
      "value": 1582211310
    },
    {
      "id": "dtmi:smartVitalsPatch:Smart_Vitals_Patch_37p:HeartRateVariability;1",
      "name": "HeartRateVariability",
      "value": -37514094
    },
    {
      "id": "dtmi:smartVitalsPatch:Smart_Vitals_Patch_37p:BodyTemperature;1",
      "name": "BodyTemperature",
      "value": 5.323322666478241e+307
    },
    {
      "id": "dtmi:smartVitalsPatch:Smart_Vitals_Patch_37p:FallDetection;1",
      "name": "FallDetection",
      "value": "Earum est nobis at voluptas id qui."
    },
    {
      "id": "dtmi:smartVitalsPatch:Smart_Vitals_Patch_37p:BloodPressure;1",
      "name": "BloodPressure",
      "value": {
        "Diastolic": 161438124,
        "Systolic": -966387879
      }
    }
  ],
  "device": {
    "id": "9xwhr7khkfri",
    "name": "wireless port",
    "templateId": "dtmi:hpzy1kfcbt2:umua7dplmbd",
    "templateName": "Smart Vitals Patch",
    "properties": {
      "reported": [
        {
          "id": "dtmi:smartVitalsPatch:Smart_Vitals_Patch_wr:DeviceStatus;1",
          "name": "DeviceStatus",
          "value": "Id optio iste vero et neque sit."
        }
      ]
    },
    "cloudProperties": [],
    "simulated": true,
    "approved": true,
    "blocked": false,
    "provisioned": false
  }
}
```

La requête JQ suivante transforme le message entrant dans un format adapté au webhook à envoyer au jeu de données de diffusion en continu Power BI. Cet exemple inclut une condition de filtre permettant de sortir des messages uniquement pour un modèle d’appareil spécifique. Vous pouvez utiliser la fonctionnalité de filtre d’exportation de données pour filtrer par modèle d’appareil :

```jq
import "iotc" as iotc;
if .device.templateId == "dtmi:hpzy1kfcbt2:umua7dplmbd" then 
    [{
        deviceId: .device.id,
        timestamp: .enqueuedTime,
        deviceName: .device.name,
        bloodPressureSystolic: .telemetry | iotc::find(.name == "BloodPressure").value.Systolic,
        bloodPressureDiastolic: .telemetry | iotc::find(.name == "BloodPressure").value.Diastolic,
        heartRate: .telemetry | iotc::find(.name == "HeartRate").value,
        heartRateVariability: .telemetry | iotc::find(.name == "HeartRateVariability").value,
        respiratoryRate: .telemetry | iotc::find(.name == "RespiratoryRate").value
    }]
else
    empty
end
```

Sortie JSON :

```json
{
  "bloodPressureDiastolic": 161438124,
  "bloodPressureSystolic": -966387879,
  "deviceId": "9xwhr7khkfri",
  "deviceName": "wireless port",
  "heartRate": -633994413,
  "heartRateVariability": -37514094,
  "respiratoryRate": 1582211310,
  "timestamp": "1909-10-10T07:11:56.078161042Z"
}
```

### <a name="scenario-4-export-data-to-azure-data-explorer-and-visualize-it-in-power-bi"></a>Scénario 4 : exportation de données vers Azure Data Explorer et visualisation dans Power BI

Dans ce scénario, vous exportez des données vers Azure Data Explorer, puis utilisez un connecteur pour visualiser les données dans Power BI. Pour en savoir plus sur la façon d’ajouter un cluster Azure Data Explorer et une base de données en tant que destination d’exportation, consultez [Créer une destination Azure Data Explorer](howto-export-data.md#create-an-azure-data-explorer-destination).

Ce scénario utilise une table Azure Data Explorer dont le schéma est le suivant :

``` kusto
.create table smartvitalspatch (
  EnqueuedTime:datetime,
  Message:string,
  Application:string,
  Device:string,
  Simulated:boolean,
  Template:string,
  Module:string,
  Component:string,
  Capability:string,
  Value:dynamic
)
```

Dans ce scénario, l’appareil envoie des messages de télémétrie qui ressemblent à l’exemple suivant :

```json
{
    "applicationId": "570c2d7b-d72e-4ad1-aaf4-ad9b727daa47",
    "enqueuedTime": "1909-10-10T07:11:56.078161042Z",
    "messageSource": "telemetry",
    "telemetry": [
        {
            "id": "dtmi:smartVitalsPatch:Smart_Vitals_Patch_37p:HeartRate;1",
            "name": "HeartRate",
            "value": -633994413
        },
        {
            "id": "dtmi:smartVitalsPatch:Smart_Vitals_Patch_37p:RespiratoryRate;1",
            "name": "RespiratoryRate",
            "value": 1582211310
        },
        {
            "id": "dtmi:smartVitalsPatch:Smart_Vitals_Patch_37p:HeartRateVariability;1",
            "name": "HeartRateVariability",
            "value": -37514094
        },
        {
            "id": "dtmi:smartVitalsPatch:Smart_Vitals_Patch_37p:BodyTemperature;1",
            "name": "BodyTemperature",
            "value": 5.323322666478241e+307
        },
        {
            "id": "dtmi:smartVitalsPatch:Smart_Vitals_Patch_37p:FallDetection;1",
            "name": "FallDetection",
            "value": "Earum est nobis at voluptas id qui."
        },
        {
            "id": "dtmi:smartVitalsPatch:Smart_Vitals_Patch_37p:BloodPressure;1",
            "name": "BloodPressure",
            "value": {
                "Diastolic": 161438124,
                "Systolic": -966387879
            }
        }
    ],
    "device": {
        "id": "9xwhr7khkfri",
        "name": "wireless port",
        "templateId": "dtmi:hpzy1kfcbt2:umua7dplmbd",
        "templateName": "Smart Vitals Patch",
        "properties": {
            "reported": [
                {
                    "id": "dtmi:smartVitalsPatch:Smart_Vitals_Patch_wr:DeviceStatus;1",
                    "name": "DeviceStatus",
                    "value": "Id optio iste vero et neque sit."
                }
            ]
        },
        "cloudProperties": [],
        "simulated": true,
        "approved": true,
        "blocked": false,
        "provisioned": false
    }
}
```

La requête JQ suivante transforme le message entrant en un message de sortie distinct pour chaque valeur de télémétrie. Cette transformation produit une sortie correspondant au schéma de table Azure Data Explorer. La transformation utilise un schéma entité-attribut-valeur où chaque ligne contient une valeur de télémétrie unique et où le nom de la télémétrie est une valeur figurant dans une colonne distincte de la même ligne :

```jq
. as $in | .telemetry[] | {
  EnqueuedTime: $in.enqueuedTime,
  Message: $in.messageId,
  Application: $in.applicationId,
  Device: $in.device.id,
  Simulated: $in.device.simulated,
  Template: ($in.device.templateName // ""),
  Module: ($in.module // ""),
  Component: ($in.component // ""),
  Capability: .name,
  Value: .value
}
```

Sortie JSON :

```json
{
  "Application": "570c2d7b-d72e-4ad1-aaf4-ad9b727daa47",
  "Capability": "HeartRate",
  "Component": "",
  "Device": "9xwhr7khkfri",
  "EnqueuedTime": "1909-10-10T07:11:56.078161042Z",
  "Message": null,
  "Module": "",
  "Simulated": true,
  "Template": "Smart Vitals Patch",
  "Value": -633994413
},
{
  "Application": "570c2d7b-d72e-4ad1-aaf4-ad9b727daa47",
  "Capability": "RespiratoryRate",
  "Component": "",
  "Device": "9xwhr7khkfri",
  "EnqueuedTime": "1909-10-10T07:11:56.078161042Z",
  "Message": null,
  "Module": "",
  "Simulated": true,
  "Template": "Smart Vitals Patch",
  "Value": 1582211310
},
{
  "Application": "570c2d7b-d72e-4ad1-aaf4-ad9b727daa47",
  "Capability": "HeartRateVariability",
  "Component": "",
  "Device": "9xwhr7khkfri",
  "EnqueuedTime": "1909-10-10T07:11:56.078161042Z",
  "Message": null,
  "Module": "",
  "Simulated": true,
  "Template": "Smart Vitals Patch",
  "Value": -37514094
},
{
  "Application": "570c2d7b-d72e-4ad1-aaf4-ad9b727daa47",
  "Capability": "BodyTemperature",
  "Component": "",
  "Device": "9xwhr7khkfri",
  "EnqueuedTime": "1909-10-10T07:11:56.078161042Z",
  "Message": null,
  "Module": "",
  "Simulated": true,
  "Template": "Smart Vitals Patch",
  "Value": 5.323322666478241e+307
},
{
  "Application": "570c2d7b-d72e-4ad1-aaf4-ad9b727daa47",
  "Capability": "FallDetection",
  "Component": "",
  "Device": "9xwhr7khkfri",
  "EnqueuedTime": "1909-10-10T07:11:56.078161042Z",
  "Message": null,
  "Module": "",
  "Simulated": true,
  "Template": "Smart Vitals Patch",
  "Value": "Earum est nobis at voluptas id qui."
},
{
  "Application": "570c2d7b-d72e-4ad1-aaf4-ad9b727daa47",
  "Capability": "BloodPressure",
  "Component": "",
  "Device": "9xwhr7khkfri",
  "EnqueuedTime": "1909-10-10T07:11:56.078161042Z",
  "Message": null,
  "Module": "",
  "Simulated": true,
  "Template": "Smart Vitals Patch",
  "Value": {
      "Diastolic": 161438124,
      "Systolic": -966387879
  }
}
```

Les données sortantes sont exportées vers votre cluster Azure Data Explorer. Pour visualiser les données exportées dans Power BI, procédez comme suit :

1. Installez l’application Power BI. Vous pouvez télécharger l’application Power BI de bureau à partir de [Convertir des données en insights actionnables avec Power BI Desktop](https://powerbi.microsoft.com/desktop/).
1. Téléchargez le fichier [IoT Central ADX Connector.pbit](https://github.com/Azure-Samples/iot-central-docs-samples/raw/master/azure-data-explorer-power-bi/IoT%20Central%20ADX%20Connector.pbit) de l’application Power BI Desktop à partir de GitHub.
1. Utilisez l’application Power BI Desktop pour ouvrir le fichier *IoT Central ADX Connector.pbit* que vous avez téléchargé à l’étape précédente. Lorsque vous y êtes invité, entrez le cluster, la base de données et les informations de table Power BI Desktop que vous avez notés précédemment.

Vous pouvez maintenant visualiser les données dans Power BI :

:::image type="content" source="media/howto-transform-data-internally/powerbi-report.png" alt-text="Capture d’écran d’un rapport Power BI affichant des données d’IoT Central." border="false":::

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous savez comment transformer des données dans IoT Central, nous vous suggérons de découvrir [Comment utiliser l’analytique dans IoT Central](./howto-create-analytics.md).
