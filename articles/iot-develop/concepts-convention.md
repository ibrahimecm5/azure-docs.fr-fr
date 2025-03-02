---
title: Conventions IoT Plug-and-Play | Microsoft Docs
description: Description des conventions dont IoT Plug-and-Play attend des appareils qu’ils les utilisent lorsqu’ils envoient de la télémétrie et des propriétés et gèrent des commandes et des mises à jour des propriétés.
author: rido-min
ms.author: rmpablos
ms.date: 07/10/2020
ms.topic: conceptual
ms.service: iot-develop
services: iot-develop
ms.openlocfilehash: fc8992e8e602f4a92d870328b6da14dde06af087
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128670820"
---
# <a name="iot-plug-and-play-conventions"></a>Conventions IoT Plug-and-Play

Les appareils IoT Plug-and-Play doivent suivre un ensemble de conventions lorsqu’ils échangent des messages avec un hub IoT. Les appareils IoT Plug-and-Play utilisent le protocole MQTT pour communiquer avec IoT Hub.

Les appareils peuvent inclure des [modules](../iot-hub/iot-hub-devguide-module-twins.md) ou être implémentés dans un [module IoT Edge](../iot-edge/about-iot-edge.md) hébergé par le runtime IoT Edge.

Vous décrivez la télémétrie, les propriétés et les commandes qu’un appareil IoT Plug-and-Play implémente à l’aide d’un _modèle_ [Digital Twins Definition Language v2 (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl). Il existe deux types de modèles auxquels il est fait référence dans cet article :

- **Aucun composant** : modèle sans composants. Le modèle déclare la télémétrie, les propriétés et les commandes comme des propriétés de haut niveau dans la section Contenus de l’interface principale. Dans l’outil Explorateur Azure IoT, ce modèle apparaît comme un seul _composant par défaut_.
- **Plusieurs composants** : modèle composé d’au moins deux interfaces. Une interface principale, qui apparaît comme le _composant par défaut_, avec la télémétrie, les propriétés et les commandes. Une ou plusieurs interfaces déclarées comme des composants avec une télémétrie, des propriétés et des commandes supplémentaires.

Pour plus d’informations, consultez le [Guide de modélisation d’IoT Plug-and-Play](concepts-modeling-guide.md).

## <a name="identify-the-model"></a>identification du modèle

Pour annoncer le modèle qu’il implémente, un appareil ou module IoT Plug-and-Play inclut l’ID de modèle dans le paquet de connexion MQTT en ajoutant `model-id` au champ `USERNAME`.

Pour découvrir le modèle implémenté par un appareil ou un module, un service peut récupérer l’ID de modèle à partir :

- du champ `modelId` du jumeau d’appareil ;
- du champ `$metadata.$model` du jumeau numérique ;
- d’une notification de modification du jumeau numérique.

## <a name="telemetry"></a>Télémétrie

La télémétrie envoyée à partir d’un appareil sans composant ne nécessite aucune métadonnée supplémentaire. Le système ajoute la propriété `dt-dataschema`.

La télémétrie envoyée à partir d’un appareil à plusieurs composants doit ajouter `$.sub` comme propriété de message. Le système ajoute les propriétés `dt-subject` et `dt-dataschema`.

## <a name="read-only-properties"></a>Propriétés en lecture seule

### <a name="sample-no-component-read-only-property"></a>Exemple de propriété en lecture seule sans composant

Un appareil ou un module peut envoyer tout JSON valide qui respecte les règles DTDL v2.

DTDL :

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:example: Thermostat;1",
  "@type": "Interface",
  "contents": [
    {
      "@type": "Property",
      "name": "temperature",
      "schema": "double"
    }
  ]
}
```

Exemple de charge utile de propriété rapportée :

```json
"reported" :
{
  "temperature" : 21.3
}
```

### <a name="sample-multiple-components-read-only-property"></a>Exemple de propriété en lecture seule avec plusieurs composants

L’appareil ou le module doit ajouter le marqueur `{"__t": "c"}` pour indiquer que l’élément fait référence à un composant.

DTDL qui fait référence à un composant :

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:TemperatureController;1",
  "@type": "Interface",
  "displayName": "Temperature Controller",
  "contents": [
    {
      "@type" : "Component",
      "schema": "dtmi:com:example:Thermostat;1",
      "name": "thermostat1"
    }
  ]
}
```

DTDL qui définit le composant :

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "contents": [
    {
      "@type": "Property",
      "name": "temperature",
      "schema": "double"
    }
  ]
}
```

Exemple de charge utile de propriété rapportée :

```json
"reported": {
  "thermostat1": {
    "__t": "c",
    "temperature": 21.3
  }
}
```

## <a name="writable-properties"></a>Propriétés accessibles en écriture

L’appareil ou le module doit confirmer qu’il a reçu la propriété en envoyant une propriété rapportée. La propriété rapportée doit inclure :

- `value` : valeur réelle de la propriété (en général, la valeur reçue, mais l’appareil peut décider de signaler une autre valeur).
- `ac` : code d’accusé de réception qui utilise un code d’état HTTP.
- `av` : version d’accusé de réception qui fait référence à la `$version` de la propriété souhaitée. Vous pouvez trouver cette valeur dans la charge utile JSON de la propriété souhaitée.
- `ad` : description facultative de l’accusé de réception.

Quand un appareil démarre, il doit demander le jumeau de l’appareil et vérifier s’il existe des mises à jour de propriétés accessibles en écriture. Si la version d’une propriété accessible en écriture a augmenté pendant que l’appareil était hors connexion, l’appareil doit envoyer une réponse de propriété rapportée pour confirmer qu’il a reçu la mise à jour.

Quand un appareil démarre pour la première fois, il peut envoyer une valeur initiale pour une propriété rapportée s’il ne reçoit pas de propriété souhaitée initiale du hub. Dans ce cas, l’appareil doit définir `av` sur `1`. Par exemple :

```json
"reported": {
  "targetTemperature": {
    "value": 20.0,
    "ac": 200,
    "av": 1,
    "ad": "initialize"
  }
}
```

Un appareil peut utiliser la propriété rapportée pour fournir d’autres informations au hub. Par exemple, l’appareil peut répondre avec une série de messages en cours de traitement tels que :

```json
"reported": {
  "targetTemperature": {
    "value": 35.0,
    "ac": 202,
    "av": 3,
    "ad": "In-progress - reporting current temperature"
  }
}
```

Lorsque l’appareil atteint la température cible, il envoie le message suivant :

```json
"reported": {
  "targetTemperature": {
    "value": 20.0,
    "ac": 200,
    "av": 3,
    "ad": "Reached target temperature"
  }
}
```

Un appareil peut signaler une erreur telle que :

```json
"reported": {
  "targetTemperature": {
    "value": 120.0,
    "ac": 500,
    "av": 3,
    "ad": "Target temperature out of range. Valid range is 10 to 99."
  }
}
```

### <a name="object-type"></a>Type d’objet

Si une propriété accessible en écriture est définie en tant qu’objet, le service doit envoyer un objet complet à l’appareil. L’appareil doit accuser réception de la mise à jour en renvoyant suffisamment d’informations au service pour que celui-ci comprenne comment l’appareil a agi en réponse à la mise à jour. Cette réponse peut inclure ce qui suit :

- L’objet entier
- Uniquement les champs que l’appareil a mis à jour
- Un sous-ensemble des champs

Pour les objets volumineux, pensez à réduire la taille de l’objet que vous incluez dans l’accusé de réception.

L’exemple suivant montre une propriété accessible en écriture définie comme `Object` avec quatre champs :

DTDL :

```json
{
  "@type": "Property",
  "name": "samplingRange",
  "schema": {
    "@type": "Object",
    "fields": [
      {
        "name": "startTime",
        "schema": "dateTime"
      },
      {
        "name": "lastTime",
        "schema": "dateTime"
      },
      {
        "name": "count",
        "schema": "integer"
      },
      {
        "name": "errorCount",
        "schema": "integer"
      }
    ]
  },
  "displayName": "Sampling range"
  "writable": true
}
```

Pour mettre à jour cette propriété accessible en écriture, envoyez un objet complet à partir du service qui ressemble à ce qui suit :

```json
{
  "samplingRange": {
    "startTime": "2021-08-17T12:53:00.000Z",
    "lastTime": "2021-08-17T14:54:00.000Z",
    "count": 100,
    "errorCount": 5
  }
}
```

L’appareil répond avec un accusé de réception ressemblant à ce qui suit :

```json
{
  "samplingRange": {
    "ac": 200,
    "av": 5,
    "ad": "Weighing status updated",
    "value": {
      "startTime": "2021-08-17T12:53:00.000Z",
      "lastTime": "2021-08-17T14:54:00.000Z",
      "count": 100,
      "errorCount": 5
    }
  }
}
```

### <a name="sample-no-component-writable-property"></a>Exemple de propriété accessible en écriture sans composant

Lorsqu’un appareil reçoit plusieurs propriétés souhaitées dans une seule charge utile, il peut envoyer les réponses de propriétés rapportées dans plusieurs charges utiles ou combiner les réponses dans une charge utile unique.

Un appareil ou un module peut envoyer tout JSON valide qui respecte les règles DTDL v2 :

DTDL :

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:example: Thermostat;1",
  "@type": "Interface",
  "contents": [
    {
      "@type": "Property",
      "name": "targetTemperature",
      "schema": "double",
      "writable": true
    },
    {
      "@type": "Property",
      "name": "targetHumidity",
      "schema": "double",
      "writable": true
    }
  ]
}
```

Exemple de charge utile de propriété souhaitée :

```json
"desired" :
{
  "targetTemperature" : 21.3,
  "targetHumidity" : 80,
  "$version" : 3
}
```

Exemple de première charge utile de propriété rapportée :

```json
"reported": {
  "targetTemperature": {
    "value": 21.3,
    "ac": 200,
    "av": 3,
    "ad": "complete"
  }
}
```

Exemple de deuxième charge utile de propriété rapportée :

```json
"reported": {
  "targetHumidity": {
    "value": 80,
    "ac": 200,
    "av": 3,
    "ad": "complete"
  }
}
```

> [!NOTE]
> Vous pouvez choisir de combiner ces deux charges utiles de propriétés rapportées dans une charge utile unique.

### <a name="sample-multiple-components-writable-property"></a>Exemple de propriété accessible en écriture avec plusieurs composants

L’appareil ou le module doit ajouter le marqueur `{"__t": "c"}` pour indiquer que l’élément fait référence à un composant.

Le marqueur est envoyé uniquement pour les mises à jour des propriétés définies dans un composant. Les mises à jour des propriétés définies dans le composant par défaut n’incluent pas le marqueur. Voir [Exemple de propriété accessible en écriture sans composant](#sample-no-component-writable-property).

Lorsqu’un appareil reçoit plusieurs propriétés rapportées dans une seule charge utile, il peut envoyer les réponses de propriétés rapportées dans plusieurs charges utiles ou combiner les réponses dans une charge utile unique.

L’appareil ou le module doit confirmer qu’il a reçu les propriétés en envoyant des propriétés rapportées :

DTDL qui fait référence à un composant :

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:TemperatureController;1",
  "@type": "Interface",
  "displayName": "Temperature Controller",
  "contents": [
    {
      "@type" : "Component",
      "schema": "dtmi:com:example:Thermostat;1",
      "name": "thermostat1"
    }
  ]
}
```

DTDL qui définit le composant :

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "contents": [
    {
      "@type": "Property",
      "name": "targetTemperature",
      "schema": "double",
      "writable": true
    }
  ]
}
```

Exemple de charge utile de propriété souhaitée :

```json
"desired": {
  "thermostat1": {
    "__t": "c",
    "targetTemperature": 21.3,
    "targetHumidity": 80,
    "$version" : 3
  }
}
```

Exemple de première charge utile de propriété rapportée :

```json
"reported": {
  "thermostat1": {
    "__t": "c",
    "targetTemperature": {
      "value": 23,
      "ac": 200,
      "av": 3,
      "ad": "complete"
    }
  }
}
```

Exemple de deuxième charge utile de propriété rapportée :

```json
"reported": {
  "thermostat1": {
    "__t": "c",
    "targetHumidity": {
      "value": 80,
      "ac": 200,
      "av": 3,
      "ad": "complete"
    }
  }
}
```

> [!NOTE]
> Vous pouvez choisir de combiner ces deux charges utiles de propriétés rapportées dans une charge utile unique.

## <a name="commands"></a>Commandes

Les interfaces sans composant utilisent le nom de commande sans préfixe.

Sur un appareil ou un module, les interfaces avec plusieurs composants utilisent des noms de commande au format suivant : `componentName*commandName`.

## <a name="next-steps"></a>Étapes suivantes

À présent que vous connaissez les conventions IoT Plug-and-Play, voici quelques ressources supplémentaires :

- [Langage DTDL (Digital Twins Definition Language)](https://github.com/Azure/opendigitaltwins-dtdl)
- [SDK d’appareils C](/azure/iot-hub/iot-c-sdk-ref/)
- [API REST IoT](/rest/api/iothub/device)
- [Guide de modélisation d’IoT Plug-and-Play](concepts-modeling-guide.md)
