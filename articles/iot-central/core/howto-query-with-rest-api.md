---
title: Utiliser l’API REST pour interroger des appareils dans Azure IoT Central
description: Comment utiliser l’API REST IoT Central pour interroger des appareils dans une application
author: dominicbetts
ms.author: dobett
ms.date: 10/12/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: b287bb463519d4b6ef76997dfb778ac4827014c2
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131097976"
---
# <a name="how-to-use-the-iot-central-rest-api-to-query-devices"></a>Comment utiliser l’API REST IoT Central pour interroger des appareils

L’API REST IoT Central vous permet de développer des applications clientes qui s’intègrent aux applications IoT Central. Vous pouvez utiliser l’API REST pour interroger des appareils dans votre application IoT Central. Voici des exemples de la façon dont vous pouvez utiliser l’API REST de requête :

- Obtenez les 10 dernières valeurs de télémétrie signalées par un appareil.
- Obtenez les dernières 24 heures de données des appareils qui se trouvent dans la même salle. La salle est une propriété de l’appareil ou du Cloud.
- Recherchez tous les appareils en état d’erreur et dont le microprogramme est obsolète.
- Tendances de télémétrie des appareils, en moyenne dans des fenêtres de 10 minutes.
- Obtenez la version actuelle du microprogramme de tous vos appareils à thermostat.

Cet article décrit comment utiliser l’API `/query` pour interroger des appareils.

Un appareil peut regrouper les propriétés, les données de télémétrie et les commandes qu’il prend en charge dans des _composants_ et _modules_.

Chaque appel d’API REST IoT Central nécessite un en-tête d’autorisation. Pour plus d’informations, consultez [Comment authentifier et autoriser les appels d’API REST d’IoT Central](howto-authorize-rest-api.md).

Pour obtenir la documentation de référence sur l’API REST IoT Central, consultez [Informations de référence sur l’API REST d’Azure IoT Central](/rest/api/iotcentral/).

## <a name="run-a-query"></a>Exécuter une requête

Utilisez la requête suivante pour exécuter une requête :

```http
POST https://{your app subdomain}.azureiotcentral.com/api/query?api-version=1.1-preview
```

La requête se trouve dans le corps de la requête et ressemble à ce qui suit :

```json
{
  "query": "SELECT $id, $ts, temperature, humidity FROM urn:modelDefinition:fupmoiu28b:ymju9efv9 WHERE WITHIN_WINDOW(P1D)"
}
```

La valeur `urn:modelDefinition:fupmoiu28b:ymju9efv9` dans la clause `FROM` est un *ID de modèle d’appareil*. Pour rechercher un ID de modèle d’appareil, accédez à la page **Périphériques** dans votre application IOT Central et pointez sur un appareil qui utilise le modèle. La carte comprend l’ID du modèle d’appareil :

:::image type="content" source="media/howto-query-with-rest-api/show-device-template-id.png" alt-text="Capture d’écran montrant comment rechercher l’ID de modèle d’appareil dans l’URL de la page.":::

La réponse à cette demande ressemble à l’exemple suivant :

```json
{
  "results": [
    {
      "$id": "sample-003",
      "$ts": "2021-09-10T12:59:52.015Z",
      "temperature": 47.632160152311016,
      "humidity": 49.726422005390816
    },
    {
      "$id": "sample-001",
      "$ts": "2021-09-10T13:01:34.286Z",
      "temperature": 58.898120617808495,
      "humidity": 44.66125772328022
    },
    {
      "$id": "sample-001",
      "$ts": "2021-09-10T13:04:04.96Z",
      "temperature": 52.79601469228174,
      "humidity": 71.5067230188416
    },
    {
      "$id": "sample-002",
      "$ts": "2021-09-10T13:04:36.877Z",
      "temperature": 49.610062789623264,
      "humidity": 52.78538601804491
    }
  ]
}
```

## <a name="syntax"></a>Syntax

La syntaxe de requête est semblable à la syntaxe SQL, et est constituée des clauses suivantes :

- `SELECT` est obligatoire et définit les données que vous souhaitez récupérer, telles que les valeurs de télémétrie de l’appareil.
- `FROM` est obligatoire et identifie le type de périphérique que vous interrogez. Cette clause spécifie l’ID du modèle d’appareil.
- `WHERE` est facultatif et vous permet de filtrer les résultats.
- `ORDER BY` est facultatif et vous permet de trier les résultats.
- `GROUP BY` est facultatif et vous permet d’agréger les résultats.

Les sections suivantes décrivent ces clauses de façon plus détaillée.

## <a name="select-clause"></a>Clause SELECT

La clause `SELECT` répertorie les valeurs de données à inclure dans le résultat de la requête et peut inclure les éléments suivants :

- Données de télémétrie. Utilisez les noms de télémétrie du modèle d’appareil.
- Propriétés signalées (Reported) . Utilisez les noms de propriété du modèle d’appareil.
- Propriétés Cloud. Utilisez les noms de propriété Cloud du modèle d’appareil.
- `$id`. L’ID de l’appareil.
- `$provisioned`. Valeur booléenne qui indique si l’appareil est déjà approvisionné.
- `$simulated`. Valeur booléenne qui indique si l’appareil est un appareil simulé.
- `$ts`. Timestamp associé à une valeur de télémétrie.

Si votre modèle d’appareil utilise des composants tels que le composant **Informations sur l’appareil**, référencez les données de télémétrie ou les propriétés définies dans le composant comme suit :

```json
{
  "query": "SELECT deviceInformation.model, deviceInformation.swVersion FROM urn:modelDefinition:fupmoiu28b:ymju9efv9"
}
```

Le nom du composant se trouve dans le modèle d’appareil :

:::image type="content" source="media/howto-query-with-rest-api/show-component-name.png" alt-text="Capture d’écran montrant comment rechercher le nom du composant.":::

Les limites suivantes s’appliquent à la clause `SELECT` :

- Il n’y a pas d’opérateur générique.
- Vous ne pouvez pas avoir plus de 15 éléments dans la liste de sélection.
- Dans une requête unique, sélectionnez les données de télémétrie ou les propriétés, mais pas les deux. Une requête de propriété peut inclure à la fois des propriétés signalées et des propriétés Cloud.

### <a name="aliases"></a>Alias

Utilisez le mot clé `AS` afin de définir un alias pour un élément dans la clause `SELECT`. L’alias est utilisé dans le résultat de la requête. Vous pouvez également l’utiliser ailleurs dans la requête. Par exemple :

```json
{
  "query": "SELECT $id as ID, $ts as timestamp, temperature as t, pressure as p FROM urn:modelDefinition:fupmoiu28b:ymju9efv9 WHERE WITHIN_WINDOW(P1D) AND t > 0 AND p > 50"
}
```

> [!TIP]
> Vous ne pouvez pas utiliser un autre élément de la liste de sélection comme alias. Par exemple, l’élément suivant n’est pas autorisé `SELECT id, temp AS id...`.

Le résultat ressemble à la sortie suivante :

```json
{
  "results": [
    {
      "ID": "sample-002",
      "timestamp": "2021-09-10T11:40:29.188Z",
      "t": 40.20355053736378,
      "p": 79.26806508746755
    },
    {
      "ID": "sample-001",
      "timestamp": "2021-09-10T11:43:42.61Z",
      "t": 68.03536237975348,
      "p": 58.33517075380311
    }
  ]
}
```

### <a name="top"></a>Haut de la page

Utilisez `TOP` pour limiter le nombre de résultats retournées par la requête. Par exemple, la requête suivante renvoie les 10 premiers résultats :

```json
{
    "query": "SELECT TOP 10 $id as ID, $ts as timestamp, temperature, humidity FROM urn:modelDefinition:fupmoiu28b:ymju9efv9"
}
```

Si vous n’utilisez pas `TOP`, la requête retourne un maximum de 10 000 résultats.

Pour trier les résultats avant que `TOP` ne limite le nombre de résultats, utilisez l’option [TRIER PAR](#order-by-clause).

## <a name="from-clause"></a>Clause FROM

La clause `FROM` doit contenir un ID de modèle d’appareil. La clause `FROM` spécifie le type d’appareil que vous interrogez.

Pour rechercher un ID de modèle d’appareil, accédez à la page **Périphériques** dans votre application IOT Central et pointez sur un appareil qui utilise le modèle. La carte comprend l’ID du modèle d’appareil :

:::image type="content" source="media/howto-query-with-rest-api/show-device-template-id.png" alt-text="Capture d’écran montrant comment rechercher l’ID de modèle d’appareil dans l’URL de la page.":::

Vous pouvez également utiliser l’appel d’API REST [Appareils - Get](/rest/api/iotcentral/1.1-previewdataplane/devices/get) pour obtenir l’ID de modèle d’appareil d’un appareil.

## <a name="where-clause"></a>WHERE, clause

La clause `WHERE` vous permet d’utiliser des valeurs et des fenêtres de temps pour filtrer les résultats :

### <a name="time-windows"></a>Fenêtres de temps

Pour obtenir les données de télémétrie reçues par votre application dans une fenêtre de temps spécifiée, utilisez `WITHIN_WINDOW` dans le cadre de la clause `WHERE`. Par exemple, pour récupérer les données de télémétrie concernant la température et l’humidité du dernier jour, utilisez la requête suivante :

```json
{
  "query": "SELECT $id, $ts, temperature, humidity FROM urn:modelDefinition:fupmoiu28b:ymju9efv9 WHERE WITHIN_WINDOW(P1D)"
}
```

La valeur de la fenêtre de temps utilise le [format de durée ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Durations). Le tableau suivant présente quelques exemples :

| Exemple | Description                |
| ------- | -------------------------- |
| PT10M   | 10 dernières minutes           |
| P1D     | Dernier jour                   |
| P2DT12H | 2 derniers jours et 12 heures |
| P1W     | Semaine dernière                  |
| PT5H    | Cinq dernières heures            |
| '2021-06-13T13:00:00Z/2021-06-13T15:30:00Z' | Intervalle de temps spécifique |

> [!NOTE]
> Vous pouvez utiliser des fenêtres de temps uniquement lorsque vous interrogez des données de télémétrie.

### <a name="value-comparisons"></a>Comparaisons de valeurs

Vous pouvez récupérer des données de télémétrie ou des valeurs de propriété en fonction de valeurs spécifiques. Par exemple, la requête suivante retourne tous les messages dans lesquels la température est supérieure à zéro, la pression supérieure à 50 et l’ID d’appareil correspond à **sample-002** ou **sample-003** :

```json
{
  "query": "SELECT $id, $ts, temperature AS t, pressure AS p FROM urn:modelDefinition:fupmoiu28b:ymju9efv9 WHERE WITHIN_WINDOW(P1D) AND t > 0 AND p > 50 AND $id IN ['sample-002', 'sample-003']"
}
```

Les opérateurs suivants sont pris en charge :

- Opérateurs logiques `AND` et `OR`.
- Opérateurs de comparaison : `=`, `!=`, `>`, `<`, `>=`, `<=`, `<>` et `IN`.

> [!NOTE]
> L’opérateur `IN` fonctionne uniquement avec les données de télémétrie et `$id`.

Les limites suivantes s’appliquent à la clause `WHERE` :

- Vous pouvez utiliser un maximum de 10 opérateurs dans une même requête.
- Dans une requête de télémétrie, la clause `WHERE` ne peut contenir que des filtres de données de télémétrie et de métadonnées d’appareil.
- Dans une requête de propriété, la clause `WHERE` peut contenir uniquement des propriétés signalées, des propriétés Cloud et des filtres de métadonnées d’appareil.

## <a name="aggregations-and-group-by-clause"></a>Agrégations et clause TRIER PAR

Les fonctions d’agrégation vous permettent de calculer des valeurs telles que la moyenne, la valeur maximale et la valeur minimale des données de télémétrie dans une fenêtre de temps. Par exemple, la requête suivante calcule la température et l’humidité moyennes à partir de l’appareil `sample-001` dans une fenêtre de 10 minutes :

```json
{
  "query": "SELECT AVG(temperature), AVG(pressure) FROM urn:modelDefinition:fupmoiu28b:ymju9efv9 WHERE WITHIN_WINDOW(P1D) AND $id='{{DEVICE_ID}}' GROUP BY WINDOW(PT10M)"
}
```

Les résultats sont semblables à la sortie suivante :

```json
{
    "results": [
        {
            "$ts": "2021-09-14T11:40:00Z",
            "avg_temperature": 49.212146114456104,
            "avg_pressure": 48.590304135023764
        },
        {
            "$ts": "2021-09-14T11:30:00Z",
            "avg_temperature": 52.44844454703927,
            "avg_pressure": 52.25973211022142
        },
        {
            "$ts": "2021-09-14T11:20:00Z",
            "avg_temperature": 50.14626272506926,
            "avg_pressure": 48.98400386898757
        }
    ]
}
```

Les fonctions d’agrégation suivantes sont prises en charge : `SUM`, `MAX`, `MIN`, `COUNT`, `AVG`, `FIRST` et `LAST`.

Utilisez `GROUP BY WINDOW` pour spécifier la taille de la fenêtre. Si vous n’utilisez pas `GROUP BY WINDOW`, la requête agrège les données de télémétrie des 30 derniers jours.

> [!NOTE]
> Vous ne pouvez agréger que des valeurs de télémétrie.

## <a name="order-by-clause"></a>Clause ORDER BY

La clause `ORDER BY` vous permet de trier les résultats de la requête à l’aide d’une valeur de télémétrie, d’un timestamp ou de l’ID de l’appareil. Vous pouvez trier par ordre croissant ou décroissant. Par exemple, la requête suivante renvoie les résultats les plus récents en premier :

```json
{
  "query": "SELECT $id as ID, $ts as timestamp, temperature, humidity FROM urn:modelDefinition:fupmoiu28b:ymju9efv9 ORDER BY timestamp DESC"
}
```

> [!TIP]
> Combinez `ORDER BY` et `TOP` pour limiter le nombre de résultats retournées par la requête une fois le tri terminé.

## <a name="limits"></a>limites

Les limites actuelles pour les requêtes sont les suivantes :

- Pas plus de 15 éléments dans la liste des clauses `SELECT`.
- Pas plus de 10 opérations logiques dans la clause `WHERE`.
- Les requêtes retournent un maximum de 10 000 enregistrements.
- La longueur maximale de la chaîne de requête est de 350 caractères.
- Vous ne pouvez pas utiliser le caractère générique (`*`) dans la liste des clauses `SELECT`.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous savez comment interroger les appareils avec l’API REST, nous vous suggérons d’apprendre à [utiliser l’API REST IoT Central pour gérer les utilisateurs et les rôles](howto-manage-users-roles-with-rest-api.md).
