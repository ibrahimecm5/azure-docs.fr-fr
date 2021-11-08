---
title: Utiliser l’API REST pour gérer l’exportation de données dans Azure IoT Central
description: Comment utiliser l’API REST IoT Central pour gérer l’exportation de données dans une application
author: v-krishnag
ms.author: v-krishnag
ms.date: 10/18/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: cb538b6c8777a9aeb1d02fe705a87831f7aaf5b9
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131433778"
---
# <a name="how-to-use-the-iot-central-rest-api-to-manage-data-exports"></a>Comment utiliser l’API REST IoT Central pour gérer les exportations de données

L’API REST IoT Central vous permet de développer des applications clientes qui s’intègrent aux applications IoT Central. Vous pouvez utiliser l’API REST pour créer et gérer les [exportations de données](howto-export-data.md) dans votre application IoT Central.

Chaque appel d’API REST IoT Central nécessite un en-tête d’autorisation. Pour plus d’informations, consultez [Comment authentifier et autoriser les appels d’API REST d’IoT Central](howto-authorize-rest-api.md).

Pour obtenir la documentation de référence sur l’API REST IoT Central, consultez [Informations de référence sur l’API REST d’Azure IoT Central](/rest/api/iotcentral/).

## <a name="data-export"></a>Exportation de données

Vous pouvez utiliser la fonctionnalité d’exportation de données IoT Central pour diffuser en continu les données de télémétrie, les modifications de propriété, les événements de connectivité des appareils, les événements de cycle de vie des appareils et les événements de cycle de vie du modèle d’appareil vers des destinations telles que Azure Event Hubs, Azure Service Bus, le Stockage Blob Azure et les points de terminaison webhook.

Chaque définition d’exportation de données peut envoyer des données à une ou plusieurs destinations. Créez les définitions de destination avant de créer la définition d’exportation.

### <a name="create-or-update-a-destination"></a>Créer ou mettre à jour une destination

Pour créer ou mettre à jour une définition de destination, utilisez la requête suivante :

```http
PUT https://{subdomain}.{baseDomain}/api/dataExport/destinations/{destinationId}?api-version=1.1-preview
```

* destinationId : ID unique de la destination.

L’exemple suivant montre un corps de demande qui crée une destination de stockage blob :

```json
{
  "displayName": "Blob Storage Destination",
  "type": "blobstorage@v1",
  "connectionString": "DefaultEndpointsProtocol=https;AccountName=yourAccountName;AccountKey=********;EndpointSuffix=core.windows.net",
  "containerName": "central-data"
}
```

Le corps de la demande contient des champs obligatoires :

* `displayName` : afficher le nom de la destination.
* `type` : type d’objet de destination qui peut être : `blobstorage@v1`, `dataexplorer@v1`, `eventhubs@v1`, `servicebusqueue@v1`, `servicebustopic@v1`, `webhook@v1`.
* `connectionString` : chaîne de connexion pour accéder à la ressource de destination.
* `containerName` : pour une destination de stockage blob, nom du conteneur dans lequel les données doivent être écrites.

La réponse à cette demande ressemble à l’exemple suivant : 

```json
{
    "id": "8dbcdb53-c6a7-498a-a976-a824b694c150",
    "displayName": "Blob Storage Destination",
    "type": "blobstorage@v1",
    "connectionString": "DefaultEndpointsProtocol=https;AccountName=yourAccountName;AccountKey=********;EndpointSuffix=core.windows.net",
    "containerName": "central-data",
    "status": "waiting"
}
```

### <a name="get-a-destination-by-id"></a>Obtenir une destination par ID

Utilisez la requête suivante pour récupérer les détails d’une destination à partir de votre application :

```http
GET https://{subdomain}.{baseDomain}/api/dataExport/destinations/{destinationId}?api-version=1.1-preview
```

La réponse à cette demande ressemble à l’exemple suivant :

```json
{
    "id": "8dbcdb53-c6a7-498a-a976-a824b694c150",
    "displayName": "Blob Storage Destination",
    "type": "blobstorage@v1",
    "connectionString": "DefaultEndpointsProtocol=https;AccountName=yourAccountName;AccountKey=********;EndpointSuffix=core.windows.net",
    "containerName": "central-data",
    "status": "waiting"
}
```

### <a name="list-destinations"></a>Destinations des listes

Utilisez la requête suivante pour récupérer une liste de destinations à partir de votre application :

```http
GET https://{subdomain}.{baseDomain}/api/dataExport/destinations?api-version=1.1-preview
```

La réponse à cette demande ressemble à l’exemple suivant : 

```json
{
    "value": [
        {
            "id": "8dbcdb53-c6a7-498a-a976-a824b694c150",
            "displayName": "Blob Storage Destination",
            "type": "blobstorage@v1",
            "authorization": {
                "type": "connectionString",
                "connectionString": DefaultEndpointsProtocol=https;AccountName=yourAccountName;AccountKey=********;EndpointSuffix=core.windows.net",
                "containerName": "central-data"
            },
            "status": "waiting"
        },
        {
            "id": "9742a8d9-c3ca-4d8d-8bc7-357bdc7f39d9",
            "displayName": "Webhook destination",
            "type": "webhook@v1",
            "url": "http://requestbin.net/r/f7x2i1ug",
            "headerCustomizations": {},
            "status": "error",
        }
        }
    ]
}
```

### <a name="patch-a-destination"></a>Corriger une destination

```http
PATCH https://{subdomain}.{baseDomain}/api/dataExport/destinations/{destinationId}?api-version=1.1-preview
```

Vous pouvez l’utiliser pour effectuer une mise à jour incrémentielle d’une exportation. L’exemple de corps de la demande ressemble à l’exemple suivant, qui met à jour le `displayName` vers une destination :

```json
{
  "displayName": "Blob Storage",
  "type": "blobstorage@v1",
  "connectionString": "DefaultEndpointsProtocol=https;AccountName=yourAccountName;AccountKey=********;EndpointSuffix=core.windows.net",
  "containerName": "central-data"
}
```

La réponse à cette demande ressemble à l’exemple suivant :

```json
{
    "id": "8dbcdb53-c6a7-498a-a976-a824b694c150",
    "displayName": "Blob Storage",
    "type": "blobstorage@v1",
    "connectionString": "DefaultEndpointsProtocol=https;AccountName=yourAccountName;AccountKey=********;EndpointSuffix=core.windows.net",
    "containerName": "central-data",
    "status": "waiting"
}   
```

### <a name="delete-a-destination"></a>Supprimer une destination

Pour supprimer une destination, utilisez la requête suivante :

```http
DELETE https://{subdomain}.{baseDomain}/api/dataExport/destinations/{destinationId}?api-version=1.1-preview
```

### <a name="create-or-update-an-export-definition"></a>Créer ou mettre à jour une définition d’exportation

Pour créer ou mettre à jour une définition d’exportation de données, utilisez la requête suivante :

```http
PUT https://{subdomain}.{baseDomain}/api/dataExport/exports/{exportId}?api-version=1.1-preview
```

L’exemple suivant montre un corps de la demande qui crée une définition d’exportation pour la télémétrie de l’appareil :

```json
{
    "displayName": "Enriched Export",
    "enabled": true,
    "source": "telemetry",
    "enrichments": {
        "Custom data": {
            "value": "My value"
        }
    },
    "destinations": [
        {
            "id": "8dbcdb53-c6a7-498a-a976-a824b694c150"
        }
    ]
}
```

Le corps de la demande contient des champs obligatoires :

* `displayName` : nom d’affichage de l’exportation.
* `enabled` : basculer vers démarrer/arrêter une exportation à partir de l’envoi de données.
* `source` : type de données à exporter.
* `destinations` : liste des destinations auxquelles l’exportation doit envoyer des données. Les ID de destination doivent déjà exister dans l’application.

Il existe des champs facultatifs que vous pouvez utiliser pour ajouter plus de détails à l’exportation.

* `enrichments` : informations supplémentaires à inclure avec chaque message envoyé. Les données sont représentées sous la forme d’un ensemble de paires clé/valeur, où la clé est le nom de l’enrichissement qui apparaîtra dans le message de sortie et la valeur identifie les données à envoyer.
* `filter` : requête définissant les événements de la source qui doivent être exportés.

La réponse à cette demande ressemble à l’exemple suivant : 

```json
{
    "id": "6e93df53-1ce5-45e4-ad61-3eb0d684b3a5",
    "displayName": "Enriched Export",
    "enabled": true,
    "source": "telemetry",
    "enrichments": {
        "Custom data": {
            "value": "My value"
        }
    },
    "destinations": [
        {
            "id": "9742a8d9-c3ca-4d8d-8bc7-357bdc7f39d9"
        }
    ],
    "status": "starting"
}
```

### <a name="get-an-export-by-id"></a>Récupérer une exportation par ID

Utilisez la requête suivante pour récupérer les détails d’une définition d’exportation à partir de votre application :

```http
GET https://{subdomain}.{baseDomain}/api/dataExport/exports/{exportId}?api-version=1.1-preview
```

La réponse à cette demande ressemble à l’exemple suivant :

```json
{
    "id": "8dbcdb53-c6a7-498a-a976-a824b694c150",
    "displayName": "Blob Storage Destination",
    "type": "blobstorage@v1",
    "connectionString": "DefaultEndpointsProtocol=https;AccountName=yourAccountName;AccountKey=********;EndpointSuffix=core.windows.net",
    "containerName": "central-data",
    "status": "waiting"
}
```

### <a name="list-export-definitions"></a>Répertorier les définitions d’exportation

Utilisez la requête suivante pour récupérer une liste de définitions d’exportation à partir de votre application :

```http
GET https://{subdomain}.{baseDomain}/api/dataExport/exports?api-version=1.1-preview
```

La réponse à cette demande ressemble à l’exemple suivant : 

```json
{
  "value": [
    {
      "id": "6e93df53-1ce5-45e4-ad61-3eb0d684b3a5",
      "displayName": "Enriched Export",
      "enabled": true,
      "source": "telemetry",
      "enrichments": {
        "Custom data": {
          "value": "My value"
        }
      },
      "destinations": [
        {
          "id": "9742a8d9-c3ca-4d8d-8bc7-357bdc7f39d9"
        }
      ],
      "status": "starting"
    },
    {
      "id": "802894c4-33bc-4f1e-ad64-e886f315cece",
      "displayName": "Enriched Export",
      "enabled": true,
      "source": "telemetry",
      "enrichments": {
        "Custom data": {
          "value": "My value"
        }
      },
      "destinations": [
        {
          "id": "9742a8d9-c3ca-4d8d-8bc7-357bdc7f39d9"
        }
      ],
      "status": "healthy"
    }
  ]
}
```

### <a name="patch-an-export-definition"></a>Corriger une définition d’exportation

```http
PATCH https://{subdomain}.{baseDomain}/dataExport/exports/{exportId}?api-version=1.1-preview
```

Vous pouvez l’utiliser pour effectuer une mise à jour incrémentielle d’une exportation. L’exemple de corps de la demande ressemble à l’exemple suivant, qui met à jour le `enrichments` vers une exportation :

```json
{
    "displayName": "Enriched Export",
    "enabled": true,
    "source": "telemetry",
    "enrichments": {
        "Custom data": {
            "value": "My value 2"
        }
    },
    "destinations": [
        {
            "id": "9742a8d9-c3ca-4d8d-8bc7-357bdc7f39d9"
        }
    ]
}
```

La réponse à cette demande ressemble à l’exemple suivant :

```json
{
    "id": "6e93df53-1ce5-45e4-ad61-3eb0d684b3a5",
    "displayName": "Enriched Export",
    "enabled": true,
    "source": "telemetry",
    "enrichments": {
        "Custom data": {
            "value": "My"
        }
    },
    "destinations": [
        {
            "id": "9742a8d9-c3ca-4d8d-8bc7-357bdc7f39d9"
        }
    ],
    "status": "healthy"
}
```

### <a name="delete-an-export-definition"></a>Supprimer une définition d’exportation

Pour supprimer une définition d’exportation, utilisez la requête suivante :

```http
DELETE https://{subdomain}.{baseDomain}/api/dataExport/destinations/{destinationId}?api-version=1.1-preview
```

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous savez comment gérer l’exportation des données dans l’API REST, nous vous suggérons d’apprendre [comment utiliser l’API REST IoT Central pour gérer les modèles d’appareils](howto-manage-device-templates-with-rest-api.md).
