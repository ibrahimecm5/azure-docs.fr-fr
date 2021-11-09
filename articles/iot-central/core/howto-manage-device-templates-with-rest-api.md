---
title: Utiliser l’API REST pour ajouter des modèles d’appareils dans Azure IoT Central
description: Comment utiliser l’API REST IoT Central pour ajouter des modèles d’appareils dans une application
author: v-krishnag
ms.author: v-krishnag
ms.date: 09/28/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: be2c84ed1b825fcc257dfce444c043a58a3efbc3
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131097977"
---
# <a name="how-to-use-the-iot-central-rest-api-to-manage-device-templates"></a>Comment utiliser l’API REST IoT Central pour gérer les modèles d’appareils

L’API REST IoT Central vous permet de développer des applications clientes qui s’intègrent aux applications IoT Central. Vous pouvez utiliser l’API REST pour gérer les [modèles d’appareils](concepts-device-templates.md) dans votre application IoT Central.

Chaque appel d’API REST IoT Central nécessite un en-tête d’autorisation. Pour plus d’informations, consultez [Comment authentifier et autoriser les appels d’API REST d’IoT Central](howto-authorize-rest-api.md).

Pour obtenir la documentation de référence sur l’API REST IoT Central, consultez [Informations de référence sur l’API REST d’Azure IoT Central](/rest/api/iotcentral/).

## <a name="device-templates"></a>Modèles d’appareil

Un modèle d’appareil contient un modèle d’appareil, des définitions de propriétés du cloud, des personnalisations et des définitions de vues. L’API REST vous permet de gérer le modèle d’appareil, les définitions de propriétés du cloud et les personnalisations. Utilisez l’interface utilisateur pour créer et gérer des vues.

La section sur le modèle d’appareil dans un modèle d’appareil spécifie les fonctionnalités d’un appareil que vous souhaitez connecter à votre application. Les capacités comprennent la télémétrie, les propriétés et les commandes. Le modèle est défini à l’aide du langage [DTDL](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md).

## <a name="device-templates-rest-api"></a>API REST des modèles d’appareils

L’API REST d’IoT Central vous permet de :

* Ajouter un modèle d’appareil à votre application
* Mettre à jour un modèle d’appareil dans votre application
* Obtenir la liste des modèles d’appareils dans l’application
* Obtenir un modèle d’appareil via son ID
* Supprimer un modèle d’appareil dans votre application

### <a name="add-a-device-template"></a>Ajouter un modèle d’appareil

Utilisez la demande suivante pour créer et publier un nouveau modèle d’appareil. Des vues par défaut sont générées automatiquement pour les modèles d’appareils créés de cette façon.

```http
PUT https://{subdomain}.{baseDomain}/api/deviceTemplates/{deviceTemplateId}?api-version=1.0
```

>[!NOTE]
>Les identifiants des modèles d’appareils suivent la convention d’affectation de noms [DTDL](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#digital-twin-model-identifier), par exemple : `dtmi:contoso:mythermostattemplate;1`.

L’exemple suivant montre le corps d’une demande qui ajoute un modèle d’appareil pour un thermostat. Le `capabilityModel` comprend la télémétrie de température, deux propriétés et une commande. Le modèle d’appareil définit la propriété de cloud `CustomerName` et personnalise la propriété `targetTemperature` avec `decimalPlaces`, `displayUnit`, `maxValue` et `minValue`. La valeur du modèle d’appareil `@id` doit correspondre à la valeur `deviceTemplateId` de l’URL. La valeur du modèle d’appareil `@id` n’est pas la même que la valeur `@id` de `capabilityModel`.

```json
{
    "displayName": "Thermostat",

    "@id": "dtmi:contoso:mythermostattemplate;1",
    "@type": [
        "ModelDefinition",
        "DeviceModel"
    ],
    "@context": [
        "dtmi:iotcentral:context;2",
        "dtmi:dtdl:context;2"
    ],
    "capabilityModel": {
        "@id": "dtmi:contoso:Thermostat;1",
        "@type": "Interface",
        "contents": [
            {
                "@type": [
                    "Telemetry",
                    "Temperature"
                ],
                "description": "Temperature in degrees Celsius.",
                "displayName": "Temperature",
                "name": "temperature",
                "schema": "double",
                "unit": "degreeCelsius"
            },
            {
                "@type": [
                    "Property",
                    "Temperature"
                ],
                "description": "Allows to remotely specify the desired target temperature.",
                "displayName": "Target Temperature",
                "name": "targetTemperature",
                "schema": "double",
                "unit": "degreeCelsius",
                "writable": true,
                "decimalPlaces": 1,
                "displayUnit": "C",
                "maxValue": 80,
                "minValue": 50
            },
            {
                "@type": [
                    "Property",
                    "Temperature"
                ],
                "description": "Returns the max temperature since last device reboot.",
                "displayName": "Max temperature since last reboot.",
                "name": "maxTempSinceLastReboot",
                "schema": "double",
                "unit": "degreeCelsius"
            },
            {
                "@type": "Command",
                "description": "This command returns the max, min and average temperature from the specified time to the current time.",
                "displayName": "Get report",
                "name": "getMaxMinReport",
                "request": {
                    "@type": "CommandPayload",
                    "description": "Period to return the max-min report.",
                    "displayName": "Since",
                    "name": "since",
                    "schema": "dateTime"
                },
                "response": {
                    "@type": "CommandPayload",
                    "displayName": "Temperature Report",
                    "name": "tempReport",
                    "schema": {
                        "@type": "Object",
                        "fields": [
                            {
                                "displayName": "Max temperature",
                                "name": "maxTemp",
                                "schema": "double"
                            },
                            {
                                "displayName": "Min temperature",
                                "name": "minTemp",
                                "schema": "double"
                            },
                            {
                                "displayName": "Average Temperature",
                                "name": "avgTemp",
                                "schema": "double"
                            },
                            {
                                "displayName": "Start Time",
                                "name": "startTime",
                                "schema": "dateTime"
                            },
                            {
                                "displayName": "End Time",
                                "name": "endTime",
                                "schema": "dateTime"
                            }
                        ]
                    }
                }
            },
            {
                "@type": [
                    "Property",
                    "Cloud",
                    "StringValue"
                ],
                "displayName": "Customer Name",
                "name": "CustomerName",
                "schema": "string"
            }
        ],
        "description": "Reports current temperature and provides desired temperature control.",
        "displayName": "Thermostat"
    }
}
```

Le corps de la demande contient des champs obligatoires :

* `@id` : ID unique sous la forme d’un nom de ressource uniforme simple.
* `@type` : déclare que cet objet est une interface.
* `@context` : spécifie la version DTDL utilisée pour l’interface.
* `contents` : liste les propriétés, données de télémétrie et commandes qui composent votre appareil. Les fonctionnalités peuvent être définies dans plusieurs interfaces.
* `capabilityModel` : chaque modèle d’appareil comprend un modèle de capacité. Une relation est établie entre chaque modèle de capacité de module et un modèle d’appareil. Un modèle de capacité implémente une ou plusieurs interfaces de module.

Il existe des champs facultatifs que vous pouvez utiliser pour ajouter des détails au modèle de capacité, tels que le nom d’affichage et la description.

Chaque entrée de la liste d’interfaces dans la section implements comprend :

* `name` : nom de programmation de l’interface.
* `schema` : interface implémentée par le modèle de capacité.

La réponse à cette demande ressemble à l’exemple suivant : 

```json
{
    "etag": "\"~F27cqSo0ON3bfOzwgZxAl89/JVvM80+dds6y8+mZh5M=\"",
    "displayName": "Thermostat",
    "capabilityModel": {
        "@id": "dtmi:contoso:Thermostat;1",
        "@type": "Interface",
        "contents": [
            {
                "@type": [
                    "Telemetry",
                    "Temperature"
                ],
                "description": "Temperature in degrees Celsius.",
                "displayName": "Temperature",
                "name": "temperature",
                "schema": "double",
                "unit": "degreeCelsius"
            },
            {
                "@type": [
                    "Property",
                    "Temperature",
                    "NumberValue"
                ],
                "description": "Allows to remotely specify the desired target temperature.",
                "displayName": "Target Temperature",
                "name": "targetTemperature",
                "schema": "double",
                "unit": "degreeCelsius",
                "writable": true,
                "decimalPlaces": 1,
                "displayUnit": "C",
                "maxValue": 80,
                "minValue": 50
            },
            {
                "@type": [
                    "Property",
                    "Temperature"
                ],
                "description": "Returns the max temperature since last device reboot.",
                "displayName": "Max temperature since last reboot.",
                "name": "maxTempSinceLastReboot",
                "schema": "double",
                "unit": "degreeCelsius"
            },
            {
                "@type": "Command",
                "description": "This command returns the max, min and average temperature from the specified time to the current time.",
                "displayName": "Get report",
                "name": "getMaxMinReport",
                "request": {
                    "@type": "CommandPayload",
                    "description": "Period to return the max-min report.",
                    "displayName": "Since",
                    "name": "since",
                    "schema": "dateTime"
                },
                "response": {
                    "@type": "CommandPayload",
                    "displayName": "Temperature Report",
                    "name": "tempReport",
                    "schema": {
                        "@type": "Object",
                        "fields": [
                            {
                                "displayName": "Max temperature",
                                "name": "maxTemp",
                                "schema": "double"
                            },
                            {
                                "displayName": "Min temperature",
                                "name": "minTemp",
                                "schema": "double"
                            },
                            {
                                "displayName": "Average Temperature",
                                "name": "avgTemp",
                                "schema": "double"
                            },
                            {
                                "displayName": "Start Time",
                                "name": "startTime",
                                "schema": "dateTime"
                            },
                            {
                                "displayName": "End Time",
                                "name": "endTime",
                                "schema": "dateTime"
                            }
                        ]
                    }
                }
            },
            {
                "@type": [
                    "Property",
                    "Cloud",
                    "StringValue"
                ],
                "displayName": "Customer Name",
                "name": "CustomerName",
                "schema": "string"
            }
        ],
        "description": "Reports current temperature and provides desired temperature control.",
        "displayName": "Thermostat"
    },
    "@id": "dtmi:modelDefinition:spzeut3n:n2lteu39u6",
    "@type": [
        "ModelDefinition",
        "DeviceModel"
    ],
    "@context": [
        "dtmi:iotcentral:context;2",
        "dtmi:dtdl:context;2"
    ]
}

```

### <a name="get-a-device-template"></a>Obtenir un modèle d’appareil

Utilisez la demande suivante pour récupérer les détails d’un modèle d’appareil à partir de votre application :

```http
GET https://{subdomain}.{baseDomain}/api/deviceTemplates/{deviceTemplateId}?api-version=1.0
```

>[!NOTE]
> Vous pouvez obtenir le `deviceTemplateId` à partir de l’interface utilisateur de l’application IoT Central en plaçant le curseur de la souris sur un appareil.

La réponse à cette demande ressemble à l’exemple suivant :

```json
{
    "etag": "\"~F27cqSo0ON3bfOzwgZxAl89/JVvM80+dds6y8+mZh5M=\"",
    "displayName": "Thermostat",
    "capabilityModel": {
        "@id": "dtmi:contoso:Thermostat;1",
        "@type": "Interface",
        "contents": [
            {
                "@type": [
                    "Telemetry",
                    "Temperature"
                ],
                "description": "Temperature in degrees Celsius.",
                "displayName": "Temperature",
                "name": "temperature",
                "schema": "double",
                "unit": "degreeCelsius"
            },
            {
                "@type": [
                    "Property",
                    "Temperature",
                    "NumberValue"
                ],
                "description": "Allows to remotely specify the desired target temperature.",
                "displayName": "Target Temperature",
                "name": "targetTemperature",
                "schema": "double",
                "unit": "degreeCelsius",
                "writable": true,
                "decimalPlaces": 1,
                "displayUnit": "C",
                "maxValue": 80,
                "minValue": 50
            },
            {
                "@type": [
                    "Property",
                    "Temperature"
                ],
                "description": "Returns the max temperature since last device reboot.",
                "displayName": "Max temperature since last reboot.",
                "name": "maxTempSinceLastReboot",
                "schema": "double",
                "unit": "degreeCelsius"
            },
            {
                "@type": "Command",
                "description": "This command returns the max, min and average temperature from the specified time to the current time.",
                "displayName": "Get report",
                "name": "getMaxMinReport",
                "request": {
                    "@type": "CommandPayload",
                    "description": "Period to return the max-min report.",
                    "displayName": "Since",
                    "name": "since",
                    "schema": "dateTime"
                },
                "response": {
                    "@type": "CommandPayload",
                    "displayName": "Temperature Report",
                    "name": "tempReport",
                    "schema": {
                        "@type": "Object",
                        "fields": [
                            {
                                "displayName": "Max temperature",
                                "name": "maxTemp",
                                "schema": "double"
                            },
                            {
                                "displayName": "Min temperature",
                                "name": "minTemp",
                                "schema": "double"
                            },
                            {
                                "displayName": "Average Temperature",
                                "name": "avgTemp",
                                "schema": "double"
                            },
                            {
                                "displayName": "Start Time",
                                "name": "startTime",
                                "schema": "dateTime"
                            },
                            {
                                "displayName": "End Time",
                                "name": "endTime",
                                "schema": "dateTime"
                            }
                        ]
                    }
                }
            },
            {
                "@type": [
                    "Property",
                    "Cloud",
                    "StringValue"
                ],
                "displayName": "Customer Name",
                "name": "CustomerName",
                "schema": "string"
            }
        ],
        "description": "Reports current temperature and provides desired temperature control.",
        "displayName": "Thermostat"
    },
    "@id": "dtmi:modelDefinition:spzeut3n:n2lteu39u6",
    "@type": [
        "ModelDefinition",
        "DeviceModel"
    ],
    "@context": [
        "dtmi:iotcentral:context;2",
        "dtmi:dtdl:context;2"
    ]
}
```

### <a name="list-device-templates"></a>Répertorier les modèles d’appareils

Utilisez la demande suivante pour récupérer une liste de modèles d’appareils à partir de votre application :

```http
GET https://{subdomain}.{baseDomain}/api/deviceTemplates?api-version=1.0
```

La réponse à cette demande ressemble à l’exemple suivant : 

```json
{
    "value": [
        {
            "etag": "\"~F27cqSo0ON3bfOzwgZxAl89/JVvM80+dds6y8+mZh5M=\"",
            "displayName": "Thermostat",
            "capabilityModel": {
                "@id": "dtmi:contoso:Thermostat;1",
                "@type": "Interface",
                "contents": [
                    {
                        "@type": [
                            "Telemetry",
                            "Temperature"
                        ],
                        "description": "Temperature in degrees Celsius.",
                        "displayName": "Temperature",
                        "name": "temperature",
                        "schema": "double",
                        "unit": "degreeCelsius"
                    },
                    {
                        "@type": [
                            "Property",
                            "Temperature",
                            "NumberValue"
                        ],
                        "description": "Allows to remotely specify the desired target temperature.",
                        "displayName": "Target Temperature",
                        "name": "targetTemperature",
                        "schema": "double",
                        "unit": "degreeCelsius",
                        "writable": true,
                        "decimalPlaces": 1,
                        "displayUnit": "C",
                        "maxValue": 80,
                        "minValue": 50
                    },
                    {
                        "@type": [
                            "Property",
                            "Temperature"
                        ],
                        "description": "Returns the max temperature since last device reboot.",
                        "displayName": "Max temperature since last reboot.",
                        "name": "maxTempSinceLastReboot",
                        "schema": "double",
                        "unit": "degreeCelsius"
                    },
                    {
                        "@type": "Command",
                        "description": "This command returns the max, min and average temperature from the specified time to the current time.",
                        "displayName": "Get report",
                        "name": "getMaxMinReport",
                        "request": {
                            "@type": "CommandPayload",
                            "description": "Period to return the max-min report.",
                            "displayName": "Since",
                            "name": "since",
                            "schema": "dateTime"
                        },
                        "response": {
                            "@type": "CommandPayload",
                            "displayName": "Temperature Report",
                            "name": "tempReport",
                            "schema": {
                                "@type": "Object",
                                "fields": [
                                    {
                                        "displayName": "Max temperature",
                                        "name": "maxTemp",
                                        "schema": "double"
                                    },
                                    {
                                        "displayName": "Min temperature",
                                        "name": "minTemp",
                                        "schema": "double"
                                    },
                                    {
                                        "displayName": "Average Temperature",
                                        "name": "avgTemp",
                                        "schema": "double"
                                    },
                                    {
                                        "displayName": "Start Time",
                                        "name": "startTime",
                                        "schema": "dateTime"
                                    },
                                    {
                                        "displayName": "End Time",
                                        "name": "endTime",
                                        "schema": "dateTime"
                                    }
                                ]
                            }
                        }
                    },
                    {
                        "@type": [
                            "Property",
                            "Cloud",
                            "StringValue"
                        ],
                        "displayName": "Customer Name",
                        "name": "CustomerName",
                        "schema": "string"
                    }
                ],
                "description": "Reports current temperature and provides desired temperature control.",
                "displayName": "Thermostat"
            },
            "@id": "dtmi:modelDefinition:spzeut3n:n2lteu39u6",
            "@type": [
                "ModelDefinition",
                "DeviceModel"
            ],
            "@context": [
                "dtmi:iotcentral:context;2",
                "dtmi:dtdl:context;2"
            ]
        },
        {
            "etag": "\"~XS5GovPNzJqFIwkkV/vyWW5U/6if2NwC/NqUlDxExAY=\"",
            "displayName": "Thermostat2",
            "capabilityModel": {
                "@id": "dtmi:contoso:Thermostat2;1",
                "@type": "Interface",
                "contents": [
                    {
                        "@type": [
                            "Telemetry",
                            "Temperature"
                        ],
                        "description": "Temperature in degrees Celsius.",
                        "displayName": "Temperature",
                        "name": "temperature",
                        "schema": "double",
                        "unit": "degreeCelsius"
                    },
                    {
                        "@type": [
                            "Property",
                            "Temperature",
                            "NumberValue"
                        ],
                        "description": "Allows to remotely specify the desired target temperature.",
                        "displayName": "Target Temperature",
                        "name": "targetTemperature",
                        "schema": "double",
                        "unit": "degreeCelsius",
                        "writable": true,
                        "decimalPlaces": 1,
                        "displayUnit": "C",
                        "maxValue": 80,
                        "minValue": 50
                    },
                    {
                        "@type": [
                            "Property",
                            "Temperature"
                        ],
                        "description": "Returns the max temperature since last device reboot.",
                        "displayName": "Max temperature since last reboot.",
                        "name": "maxTempSinceLastReboot",
                        "schema": "double",
                        "unit": "degreeCelsius"
                    },
                    {
                        "@type": "Command",
                        "description": "This command returns the max, min and average temperature from the specified time to the current time.",
                        "displayName": "Get report",
                        "name": "getMaxMinReport",
                        "request": {
                            "@type": "CommandPayload",
                            "description": "Period to return the max-min report.",
                            "displayName": "Since",
                            "name": "since",
                            "schema": "dateTime"
                        },
                        "response": {
                            "@type": "CommandPayload",
                            "displayName": "Temperature Report",
                            "name": "tempReport",
                            "schema": {
                                "@type": "Object",
                                "fields": [
                                    {
                                        "displayName": "Max temperature",
                                        "name": "maxTemp",
                                        "schema": "double"
                                    },
                                    {
                                        "displayName": "Min temperature",
                                        "name": "minTemp",
                                        "schema": "double"
                                    },
                                    {
                                        "displayName": "Average Temperature",
                                        "name": "avgTemp",
                                        "schema": "double"
                                    },
                                    {
                                        "displayName": "Start Time",
                                        "name": "startTime",
                                        "schema": "dateTime"
                                    },
                                    {
                                        "displayName": "End Time",
                                        "name": "endTime",
                                        "schema": "dateTime"
                                    }
                                ]
                            }
                        }
                    },
                    {
                        "@type": [
                            "Property",
                            "Cloud",
                            "StringValue"
                        ],
                        "displayName": "Customer Name",
                        "name": "CustomerName",
                        "schema": "string"
                    }
                ],
                "description": "Reports current temperature and provides desired temperature control.",
                "displayName": "Thermostat"
            },
            "@id": "dtmi:modelDefinition:spzeut3n:n2lteu39u67",
            "@type": [
                "ModelDefinition",
                "DeviceModel"
            ],
            "@context": [
                "dtmi:iotcentral:context;2",
                "dtmi:dtdl:context;2"
            ]
        }
    ]
}
```

### <a name="update-a-device-template"></a>Mettre à jour un modèle d’appareil

```http
PATCH https://{subdomain}.{baseDomain}/api/deviceTemplates/{deviceTemplateId}?api-version=1.0
```

>[!NOTE] 
>`{deviceTemplateId}` doit être identique à `@id` dans la charge utile.

L’exemple de corps de demande ressemble à l’exemple suivant, qui ajoute une propriété de cloud `LastMaintenanceDate` au modèle d’appareil :

```json
{
    "displayName": "Thermostat",

    "@id": "dtmi:contoso:mythermostattemplate",
    "@type": [
        "ModelDefinition",
        "DeviceModel"
    ],
    "@context": [
        "dtmi:iotcentral:context;2",
        "dtmi:dtdl:context;2"
    ],
    "capabilityModel": {
        "@id": "dtmi:contoso:Thermostat;1",
        "@type": "Interface",
        "contents": [
            {
                "@type": [
                    "Telemetry",
                    "Temperature"
                ],
                "description": "Temperature in degrees Celsius.",
                "displayName": "Temperature",
                "name": "temperature",
                "schema": "double",
                "unit": "degreeCelsius"
            },
            {
                "@type": [
                    "Property",
                    "Temperature"
                ],
                "description": "Allows to remotely specify the desired target temperature.",
                "displayName": "Target Temperature",
                "name": "targetTemperature",
                "schema": "double",
                "unit": "degreeCelsius",
                "writable": true,
                "decimalPlaces": 1,
                "displayUnit": "C",
                "maxValue": 80.0,
                "minValue": 50.0
            },
            {
                "@type": [
                    "Property",
                    "Temperature"
                ],
                "description": "Returns the max temperature since last device reboot.",
                "displayName": "Max temperature since last reboot.",
                "name": "maxTempSinceLastReboot",
                "schema": "double",
                "unit": "degreeCelsius"
            },
            {
                "@type": "Command",
                "description": "This command returns the max, min and average temperature from the specified time to the current time.",
                "displayName": "Get report",
                "name": "getMaxMinReport",
                "request": {
                    "@type": "CommandPayload",
                    "description": "Period to return the max-min report.",
                    "displayName": "Since",
                    "name": "since",
                    "schema": "dateTime"
                },
                "response": {
                    "@type": "CommandPayload",
                    "displayName": "Temperature Report",
                    "name": "tempReport",
                    "schema": {
                        "@type": "Object",
                        "fields": [
                            {
                                "displayName": "Max temperature",
                                "name": "maxTemp",
                                "schema": "double"
                            },
                            {
                                "displayName": "Min temperature",
                                "name": "minTemp",
                                "schema": "double"
                            },
                            {
                                "displayName": "Average Temperature",
                                "name": "avgTemp",
                                "schema": "double"
                            },
                            {
                                "displayName": "Start Time",
                                "name": "startTime",
                                "schema": "dateTime"
                            },
                            {
                                "displayName": "End Time",
                                "name": "endTime",
                                "schema": "dateTime"
                            }
                        ]
                    }
                }
            },
            {
                "@type": [
                    "Property",
                    "Cloud",
                    "StringValue"
                ],
                "displayName": "Customer Name",
                "name": "CustomerName",
                "schema": "string"
            },
            {
                "@type": [
                    "Property",
                    "Cloud",
                    "StringValue"
                ],
                "displayName": "Last Maintenance Date",
                "name": "LastMaintenanceDate",
                "schema": "dateTime"
            }
        ],
        "description": "Reports current temperature and provides desired temperature control.",
        "displayName": "Thermostat"
    }
}

```

La réponse à cette demande ressemble à l’exemple suivant :

```json
{
    "etag": "\"~6Ku691rHAgw/yw8u+ygZJGAKjSN4P4q/KxCU2xskrmk=\"",
    "displayName": "Thermostat",
    "capabilityModel": {
        "@id": "dtmi:contoso:Thermostat;1",
        "@type": "Interface",
        "contents": [
            {
                "@type": [
                    "Telemetry",
                    "Temperature"
                ],
                "description": "Temperature in degrees Celsius.",
                "displayName": "Temperature",
                "name": "temperature",
                "schema": "double",
                "unit": "degreeCelsius"
            },
            {
                "@type": [
                    "Property",
                    "Temperature"
                ],
                "description": "Allows to remotely specify the desired target temperature.",
                "displayName": "Target Temperature",
                "name": "targetTemperature",
                "schema": "double",
                "unit": "degreeCelsius",
                "writable": true,
                "decimalPlaces": 1,
                "displayUnit": "C",
                "maxValue": 80,
                "minValue": 50
            },
            {
                "@type": [
                    "Property",
                    "Temperature"
                ],
                "description": "Returns the max temperature since last device reboot.",
                "displayName": "Max temperature since last reboot.",
                "name": "maxTempSinceLastReboot",
                "schema": "double",
                "unit": "degreeCelsius"
            },
            {
                "@type": "Command",
                "description": "This command returns the max, min and average temperature from the specified time to the current time.",
                "displayName": "Get report",
                "name": "getMaxMinReport",
                "request": {
                    "@type": "CommandPayload",
                    "description": "Period to return the max-min report.",
                    "displayName": "Since",
                    "name": "since",
                    "schema": "dateTime"
                },
                "response": {
                    "@type": "CommandPayload",
                    "displayName": "Temperature Report",
                    "name": "tempReport",
                    "schema": {
                        "@type": "Object",
                        "fields": [
                            {
                                "displayName": "Max temperature",
                                "name": "maxTemp",
                                "schema": "double"
                            },
                            {
                                "displayName": "Min temperature",
                                "name": "minTemp",
                                "schema": "double"
                            },
                            {
                                "displayName": "Average Temperature",
                                "name": "avgTemp",
                                "schema": "double"
                            },
                            {
                                "displayName": "Start Time",
                                "name": "startTime",
                                "schema": "dateTime"
                            },
                            {
                                "displayName": "End Time",
                                "name": "endTime",
                                "schema": "dateTime"
                            }
                        ]
                    }
                }
            },
            {
                "@type": [
                    "Property",
                    "Cloud",
                    "StringValue"
                ],
                "displayName": "Customer Name",
                "name": "CustomerName",
                "schema": "string"
            },
            {
                "@type": [
                    "Property",
                    "Cloud",
                    "DateTimeValue"
                ],
                "displayName": "Last Maintenance Date",
                "name": "LastMaintenanceDate",
                "schema": "dateTime"
            }
        ],
        "description": "Reports current temperature and provides desired temperature control.",
        "displayName": "Thermostat"
    },
    "@id": "dtmi:modelDefinition:spzeut3n:n2lteu39u6",
    "@type": [
        "ModelDefinition",
        "DeviceModel"
    ],
    "@context": [
        "dtmi:iotcentral:context;2",
        "dtmi:dtdl:context;2"
    ]
}
```

### <a name="delete-a-device-template"></a>Supprimer un modèle d’appareil

Pour supprimer un modèle d’appareil, utilisez la demande suivante :

```http
DELETE https://{subdomain}.{baseDomain}/api/deviceTemplates/{deviceTemplateId}?api-version=1.0
```

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous savez comment gérer des modèles d’appareils avec l’API REST, nous vous suggérons de passer à l’étape suivante : [Comment créer des modèles d’appareils à partir de l’interface graphique utilisateur d’IoT Central](howto-set-up-template.md#create-a-device-template).
