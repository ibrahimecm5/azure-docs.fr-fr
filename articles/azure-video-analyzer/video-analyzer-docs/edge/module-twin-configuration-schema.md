---
title: Schéma JSON de jumeau de module Azure Video Analyzer
description: Cet article fournit une vue d’ensemble du schéma JSON de jumeau de module pour le module périphérique Azure Video Analyzer
ms.topic: how-to
ms.date: 10/01/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: f6dd3ae2e188e8179fde0cd3a684e647cbf92fe3
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096846"
---
# <a name="module-twin-configuration-schema"></a>Schéma de configuration de jumeau de module

[!INCLUDE [header](includes/edge-env.md)]

Les jumeaux d’appareil sont des documents JSON qui stockent des informations sur l’état des appareils (métadonnées, configurations et conditions). Azure IoT Hub conserve un jumeau d’appareil pour chaque appareil que vous y connectez. Pour une explication détaillée, consultez [Comprendre et utiliser les jumeaux de module dans IoT Hub](../../../iot-hub/iot-hub-devguide-module-twins.md).

Cette rubrique décrit le schéma JSON de jumeau du module périphérique Azure Video Analyzer.

## <a name="module-twin-properties"></a>Propriétés de jumeau de module

Le module périphérique Azure Video Analyzer expose les propriétés de jumeau de module suivantes.

| Propriété                    | Obligatoire | Dynamique | Description                                                  |
| :-------------------------- | :------- | :------ | :----------------------------------------------------------- |
| `ProvisioningToken`          | Oui      | Non      | Jeton d’authentification pour valider le module périphérique et provisionner les services cloud (notamment l’accès au compte Video Analyzer) |
| `ApplicationDataDirectory`    | Oui      | Non      | Chemin dans le système de fichiers du module, mappé à un volume monté pour une configuration persistante.       |
| `DiagnosticsEventsOutputName` | Non       | Oui     | Sortie hub pour les événements de diagnostic. (Vide signifie que les diagnostics ne sont pas publiés) |
| `OperationalEventsOutputName` | Non       | Oui     | Sortie hub pour les événements opérationnels. (Vide signifie que les événements opérationnels ne sont pas publiés) |
| `LogLevel`                    | Non       | Oui     | Une des valeurs suivantes : · Verbose · Information (par défaut) · Warning · Error · None |
| `LogCategories`               | Non       | Oui     | Liste des éléments suivants séparés par des virgules : Application, MediaPipeline, Events Par défaut : Application, Events |
| `AllowUnsecuredEndpoints`     | Non       | Oui     | Paramètre booléen autorisant la création de topologies avec des points de terminaison non sécurisés tels que `#Microsoft.VideoAnalyzer.UnsecuredEndpoint`. Valeur par défaut -true        |
| `TelemetryOptOut`             | Non       | Non     | Paramètre booléen permettant de refuser l’envoi de télémétries. Valeur par défaut -false       |
| `DebugLogsDirectory`          | Non       | Oui     | Répertoire pour les journaux de débogage. S’il est présent, les journaux sont générés, s’il n’est pas présent, les journaux de débogage sont désactivés.       |

Les propriétés dynamiques peuvent être mises à jour sans redémarrage du module. 

Pour plus d’informations sur le rôle des paramètres de diagnostic facultatifs, consultez l’article [Surveillance et journalisation](monitor-log-edge.md).

```json
{
    "properties.desired": {
        "ProvisioningToken": "$AVA_PROVISIONING_TOKEN",
        "ApplicationDataDirectory": "/var/lib/videoanalyzer",
        "DiagnosticsEventsOutputName": "diagnostics",
        "OperationalEventsOutputName": "operational",
        "LogLevel": "information",
        "LogCategories": "Application,Events",
        "DebugLogsDirectory": "/tmp/logs",
        "AllowUnsecuredEndpoints": true,
        "TelemetryOptOut": false    
     
    }
}
```

## <a name="next-steps"></a>Étapes suivantes

[Méthodes directes](direct-methods.md)
