---
title: Utiliser des méthodes directes dans Azure Video Analyzer – Azure
description: Le module de périphérie Azure Video Analyzer expose plusieurs méthodes directes qui sont décrites dans cette rubrique.
ms.topic: conceptual
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 39a1ddaeca42240e3f37f8e70679d5bd3d24957b
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131556461"
---
# <a name="azure-video-analyzer-direct-methods"></a>Méthodes directes d’Azure Video Analyzer

[!INCLUDE [header](includes/edge-env.md)]

Le module de périphérie Azure Video Analyzer `avaedge` expose plusieurs méthodes directes qui peuvent être appelées à partir d’IoT Hub. Les méthodes directes représentent une interaction de demande-réponse avec un appareil, similaire à un appel HTTP, dans la mesure où elles réussissent ou échouent immédiatement (après un délai d’attente spécifié par l’utilisateur). Cette approche est utile pour les scénarios où le plan d’action immédiate est différent selon que l’appareil a été en mesure ou non de répondre. Pour plus d’informations, consultez [Comprendre et appeler des méthodes directes à partir d’IoT Hub](../../../iot-hub/iot-hub-devguide-direct-methods.md).

Cette rubrique décrit ces méthodes, conventions et le schéma des méthodes.

## <a name="conventions"></a>Conventions

Les méthodes directes sont basées sur les conventions suivantes :

1. Les méthodes directes ont une version spécifiée au format MAJEURE.MINEURE (comme dans l’exemple suivant). Il s’agit du numéro « @apiVersion », où « 1 » indique la version MAJEURE et « 0 » la version MINEURE dans cet exemple :

    ```
    {
      "methodName": "pipelineTopologySet",
      "payload": {
          "@apiVersion": "1.1",
          "name": "{TopologyName}",
          "properties": {
              // Desired Topology properties
          }
      }
    }
    ```
1. Une version donnée du module Video Analyzer prend en charge toutes les versions mineures d’un appel de méthode directe jusqu’à sa version actuelle. La prise en charge des versions majeures n’est pas garantie.
1. Toutes les méthodes directes sont synchrones.
1. Les résultats d’erreur sont basés sur le [schéma d’erreur OData](http://docs.oasis-open.org/odata/odata-json-format/v4.01/odata-json-format-v4.01.html#sec_ErrorResponse).
1. Les noms doivent respecter les contraintes suivantes :
    * Uniquement des caractères alphanumériques et des tirets, à condition qu’ils ne commencent pas ni ne se terminent par un tiret
    * Pas d’espaces
    * 32 caractères maximum.

### <a name="example-of-response-from-a-direct-method"></a>Exemple de réponse d’une méthode directe

```
-----------------------  Request: livePipelineList  --------------------------------------------------

{
  "@apiVersion": "1.1"
}

---------------  Response: livePipelineList - Status: 200  ---------------

{
  "value": []
}

--------------------------------------------------------------------------
```    
### <a name="error-codes"></a>Codes d’erreur
Comme le montre l’exemple ci-dessous, lorsque vous recevez une réponse d’erreur d’une méthode directe, il existe un code d’erreur de niveau supérieur et des informations supplémentaires sont fournies sous `details`.


```json
{
  "status": 400,
  "payload": {
    "error": {
      "code": "BadRequest",
      "message": "The request is invalid",
      "details": [
        {
          "code": "ApiVersionNotSupported",
          "message": "The API version '1.4' is not supported. Supported version(s): 1.0, 1.1"
        }
      ]
    }
  }
}
```

Voici les codes d’erreur utilisés au niveau supérieur.

|Statut |Code   |Message|
|---|---|---|
|400|   BadRequest| La demande n’est pas valide.|
|400|   InvalidResource|    La ressource n’est pas valide.|
|400|   InvalidVersion| La version de l’API n’est pas valide.|
|402|   ConnectivityRequired    |Le module Edge requiert une connectivité cloud pour fonctionner correctement.|
|404|   NotFound    |Ressource introuvable|
|409|   Conflit|   Conflit d’opérations.|
|500|   InternalServerError|    Erreur interne du serveur|
|503|   ServerBusy| Serveur occupé.|

Voici quelques-uns des codes d’erreur utilisés au niveau de détail.

|Statut|    Code détaillé   |Description|
|---|---|---|
|400|   PipelineValidationError|    Erreurs de pipeline générales, par exemple, de cycles ou de partitionnement, etc.|
|400|   ModuleValidationError|  Erreurs de validation spécifiques au module.|
|409|   PipelineTopologyInUse|  La topologie de pipeline est toujours référencée par un ou plusieurs pipelines en direct.|
|409|   OperationNotAllowedInState| L’opération demandée ne peut pas être effectuée à l’état actuel.|
|409|   ResourceValidationError|    La ressource référencée (par exemple, ressource vidéo) n’est pas dans un état valide.|

## <a name="supported-direct-methods"></a>Méthodes directes prises en charge  
Voici les méthodes directes exposées par le module Edge Video Analyzer. Le schéma des méthodes directes est disponible [ici](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/videoanalyzer/data-plane/VideoAnalyzer.Edge/preview/1.1.0/AzureVideoAnalyzerSdkDefinitions.json).

### <a name="pipelinetopologylist"></a>pipelineTopologyList

Cette méthode directe répertorie toutes les topologies de pipeline créées.

#### <a name="request"></a>Requête

```
{
  "@apiVersion": "1.1"
}
```
#### <a name="response"></a>response

```
{
  "status": 200,
  "value": [
    {
      "systemData": {
        "createdAt": "2021-05-05T14:19:22.16Z",
        "lastModifiedAt": "2021-05-05T16:20:41.505Z"
      },
      
      // first pipeline topology payload
      
    },
      "systemData": {
        "createdAt": "2021-05-06T14:19:22.16Z",
        "lastModifiedAt": "2021-05-06T16:20:41.505Z"
      },
      
      // next pipeline topology payload
      
    }    
  ]
}
```

#### <a name="status-codes"></a>Codes d’état

| Condition | Code d’état |
|--|--|
| Entité trouvée | 200 |
| Erreurs utilisateur générales | Plage 400 |
| Entité introuvable | 404 |
| Erreurs de serveur générales | Plage 500 |

### <a name="pipelinetopologyset"></a>pipelineTopologySet

Crée une topologie de pipeline du nom donné si une telle topologie n’existe pas, ou renomme une topologie existante à l’aide de ce nom.

Aspects clés :

* Une topologie de pipeline peut être mise à jour librement si aucun pipeline en direct n’y fait référence.
* Une topologie de pipeline peut être mise à jour librement si tous les pipelines en direct qui y font référence sont désactivés à la condition que :

    * Les paramètres récemment ajoutés ont des valeurs par défaut.
    * Les paramètres supprimés ne sont référencés par aucun pipeline.
* Seules certaines mises à jour de topologie de pipeline sont autorisées quand un pipeline en direct est actif.

#### <a name="request"></a>Requête

```
  {
    "methodName": "pipelineTopologySet",
    "payload": {
        "@apiVersion": "1.1",
        "name": "{TopologyName}",
        "properties": {
            // Desired pipeline topology properties
        }
    }
  }
```

#### <a name="response"></a>response

```
  {
    "status": 201,
    "payload": {
        "systemData": {
           "createdAt": "2021-05-11T18:16:46.491Z",
           "lastModifiedAt": "2021-05-11T18:16:46.491Z"
        },
        "name": "{TopologyName}",
        "properties": {
            // Complete pipeline topology
        }
    }
  }
```
#### <a name="status-codes"></a>Codes d’état

|Condition  |Code d’état    |Code d’erreur détaillé|
|---|---|---|
Entité existante mise à jour |200|   N/A|
Nouvelle entité créée  |201|   N/A|
Erreurs utilisateur générales |Plage 400  ||
Erreurs de validation de pipeline  |400    |PipelineValidationError|
Erreurs de validation de module|   400 |ModuleValidationError|
Erreurs de serveur générales   |Plage 500  ||

### <a name="pipelinetopologyget"></a>pipelineTopologyGet

Récupère une topologie de pipeline du nom spécifié si elle existe.

#### <a name="request"></a>Requête

```
  {
        "@apiVersion": "1.1",
        "name": "{TopologyName}"       
  }
```
#### <a name="response"></a>response

```
{
  "status": 200,
  "payload": {
    "value": [
      {
        "systemData": {
          "createdAt": "2021-05-06T10:28:04.560Z",
          "lastModifiedAt": "2021-05-06T10:28:04.560Z"
        },
        "name": "{TopologyName}",
        // Complete pipeline topology
      }
    ]
  }
}
```

#### <a name="status-codes"></a>Codes d’état

| Condition | Code d’état |
|--|--|
| Succès | 200 |
| Erreurs utilisateur générales | Plage 400 |
| Erreurs de serveur générales | Plage 500 |

### <a name="pipelinetopologydelete"></a>pipelineTopologyDelete

Supprime une topologie de pipeline.

* Notez qu’il ne peut pas y avoir de pipelines en direct [référençant une topologie de pipeline en cours de suppression](../pipeline.md#pipeline-states). S’il existe de tels pipelines en direct, vous recevrez une erreur `TopologyInUse`.

#### <a name="request"></a>Requête

```
  {
    "methodName": "pipelineTopologyDelete",
    "payload": {
        "@apiVersion": "1.1",
        "name": "{TopologyName}"
    }
  }
```
#### <a name="response"></a>response

```
{
    "status": 200,
    "payload": null
}
```

#### <a name="status-codes"></a>Codes d’état

| Condition | Code d’état | Code d’erreur détaillé |
|--|--|--|
| Entité supprimée | 200 | N/A |
| Entité introuvable | 204 | N/A |
| Erreurs utilisateur générales | Plage 400 |  |
| La topologie de pipeline est référencée par un ou plusieurs pipelines. | 409 | PipelineTopologyInUse |
| Erreurs de serveur générales | Plage 500 |  |

### <a name="livepipelinelist"></a>livePipelineList

Répertorie tous les pipelines en direct.

#### <a name="request"></a>Requête

```
  {
        "@apiVersion": "1.1"
  }
```
#### <a name="response"></a>response

```
{
  "status": 200,
  "value": [
    {
      "systemData": {
        "createdAt": "2021-05-05T14:19:22.16Z",
        "lastModifiedAt": "2021-05-05T16:20:41.505Z"
      },      
      // first live pipeline payload  
    },
      "systemData": {
        "createdAt": "2021-05-06T14:19:22.16Z",
        "lastModifiedAt": "2021-05-06T16:20:41.505Z"
      },
      // next live pipeline payload
    }    
  ]
}
```
#### <a name="status-codes"></a>Codes d’état

| Condition | Code d’état |
|--|--|
| Entité trouvée | 200 |
| Erreurs utilisateur générales | Plage 400 |
| Entité introuvable | 404 |
| Erreurs de serveur générales | Plage 500 |

### <a name="livepipelineset"></a>livePipelineSet

Crée un pipeline en direct du nom indiqué s’il n’en existe pas, ou met à jour un pipeline en direct existant de ce nom.

Aspects clés :

* Un pipeline en direct à l’état « Désactivé » peut être mis à jour librement. Dans d’autres états, seules certaines mises à jour sont autorisées.
* Un pipeline en direct est revalidé à chaque mise à jour.

#### <a name="request"></a>Requête

```
  {
        "@apiVersion": "1.1",
        "name": "{livePipelineName}",
        "properties": {
            // Desired live pipeline properties
        }
  }
```
#### <a name="response"></a>response

````
  {
    "status": 201,
    "payload": {
        "systemData": {
           "createdAt": "2021-05-11T18:16:46.491Z",
           "lastModifiedAt": "2021-05-11T18:16:46.491Z"
        },
        "name": "{livePipelineName}",
        "properties": {
            // Complete live pipeline
        }
    }
  }
````
#### <a name="status-codes"></a>Codes d’état

| Condition | Code d’état | Code d’erreur détaillé |
|--|--|--|
| Entité existante mise à jour | 200 | N/A |
| Nouvelle entité créée | 201 | N/A |
| Erreurs utilisateur générales | Plage 400 | N/A |
| Erreurs de validation de pipeline en direct | 400 | PipelineValidationError |
| Erreurs de validation du module | 400 | ModuleValidationError |
| Erreurs de validation des ressources | 409 | ResourceValidationError |
| Erreurs de serveur générales | Plage 500 | N/A |

### <a name="livepipelinedelete"></a>livePipelineDelete

Supprime un pipeline en direct.

Aspects clés :

* Seuls des pipelines en direct désactivés peuvent être supprimés.

#### <a name="request"></a>Requête

```
  {
        "@apiVersion": "1.1",
        "name": "{livePipelineName}"
  }
```
#### <a name="response"></a>response

```
  {
    "status": 200,
    "payload": null
  }
```
#### <a name="status-codes"></a>Codes d’état

| Condition | Code d’état | Code d’erreur détaillé |
|--|--|--|
| Pipeline en direct correctement supprimé | 200 | N/A |
| Pipeline en direct introuvable | 204 | N/A |
| Erreurs utilisateur générales | Plage 400 |  |
| Le pipeline n’est pas dans l’état « Désactivé » | 409 | OperationNotAllowedInState |
| Erreurs de serveur générales | Plage 500 |  |

### <a name="livepipelineget"></a>livePipelineGet

Cela est similaire à livePipelineTopologyGet. Récupère un pipeline en direct du nom spécifié s’il existe.

#### <a name="request"></a>Requête

```
  {
        "@apiVersion": "1.1",
        "name": "{livePipelineName}"       
  }
```
#### <a name="response"></a>response

```
{
  "status": 200,
  "payload": {
    "value": [
      {
        "systemData": {
          "createdAt": "2021-05-06T10:28:04.560Z",
          "lastModifiedAt": "2021-05-06T10:28:04.560Z"
        },
        "name": "{livePipelineName}",
        // Complete live pipeline
      }
    ]
  }
}
```

#### <a name="status-codes"></a>Codes d’état

| Condition | Code d’état |
|--|--|
| Succès | 200 |
| Erreurs utilisateur générales | Plage 400 |
| Erreurs de serveur générales | Plage 500 |

### <a name="livepipelineactivate"></a>livePipelineActivate

Active un pipeline en direct. 

Aspects clés

* La méthode retourne quand le pipeline en direct est dans l’état « Activation » ou « Activé ». 
* Une opération de List/Set sur le pipeline en direct retourne l’état actuel.
* Cette méthode peut être appelée tant que le pipeline en direct n’est pas dans l’état « Désactivation » (temporaire).
* Idempotence :
    * L’appel de `livePipelineActivate` sur un pipeline en direct qui est déjà dans l’état « Activation » se comporte de la même façon que si le pipeline était désactivé.
    * L’activation d’un pipeline dans l’état « Actif » retourne immédiatement un code de réussite.

#### <a name="request"></a>Requête

```
  {
        "@apiVersion": "1.1",
        "name": "{livePipelineName}"
  }
```
#### <a name="response"></a>response

```
{
    "status": 200,
    "payload": null
}
```
#### <a name="status-codes"></a>Codes d’état

| Condition | Code d’état | Code d’erreur détaillé |
|--|--|--|
| Pipeline activé avec succès | 200 | N/A |
| Nouvelle entité créée | 201 | N/A |
| Erreurs utilisateur générales | Plage 400 |  |
| Erreurs de validation du module | 400 | ModuleValidationError |
| Erreurs de validation des ressources | 409 | ResourceValidationError |
| Le pipeline en direct est dans un état de désactivation | 409 | OperationNotAllowedInState |
| Erreurs de serveur générales | Plage 500 |  |

### <a name="livepipelinedeactivate"></a>livePipelineDeactivate

Désactive un pipeline en direct. La désactivation d’un pipeline désactive normalement le traitement vidéo et garantit que l’ensemble des événements et des vidéos mis en cache en périphérie sont validés dans le cloud, le cas échéant.

Aspects clés :

* La méthode retourne des résultats uniquement quand le pipeline est désactivé.
* Cette méthode peut être appelée tant que le pipeline en direct n’est pas dans l’état « Activation » (temporaire).
* Le pipeline passe à l’état « Désactivation » lors de sa désactivation.
    * Une opération de List/Set sur le pipeline en direct retourne l’état actuel.
    * La désactivation se termine une fois tous les médias chargés dans le cloud (si le pipeline a un nœud [récepteur vidéo](../pipeline.md#video-sink)).
* Idempotence :
    * L’appel de `livePipelineDeactivate` sur un pipeline en direct qui est déjà dans l’état « Désactivation » se comporte de la même façon que si le pipeline était dans l’état « Actif ».
    * La désactivation d’un pipeline dans l’état « Inactif » retourne immédiatement un code de réussite.

#### <a name="request"></a>Requête

```
  {
    "@apiVersion": "1.1",
    "name": "{livePipelineName}"
  }
```
#### <a name="response"></a>response

```
{
 "status": 200,
 "payload": null
}
```
| Condition | Code d’état | Code d’erreur détaillé |
|--|--|--|
| Pipeline activé avec succès | 200 | N/A |
| Nouvelle entité créée | 201 | N/A |
| Erreurs utilisateur générales | Plage 400 |  |
| Le pipeline est dans l’état Activation | 409 | OperationNotAllowedInState |
| Erreurs de serveur générales | Plage 500 |  |

### <a name="remotedeviceadapterlist"></a>remoteDeviceAdapterList

Répertorie tous les adaptateurs d’appareil à distance. Le module de périphérie Video Analyzer peut agir en tant que passerelle transparente pour la vidéo, ce qui permet aux appareils IoT d’envoyer des vidéos vers le cloud à partir d’un pare-feu. Un adaptateur d’appareil distant doit être créé pour chaque appareil IoT. La communication entre le cloud et l’appareil IoT est ensuite transmis via le module de périphérie Video Analyzer.

#### <a name="request"></a>Requête

```
  {
        "@apiVersion": "1.1"
  }
```
#### <a name="response"></a>response

```
{
  "status": 200,
  "value": [
    {
      "systemData": {
        "createdAt": "2021-10-05T14:19:22.16Z",
        "lastModifiedAt": "2021-10-05T16:20:41.505Z"
      },      
      // first remote device adapter payload
    },
      "systemData": {
        "createdAt": "2021-10-06T14:19:22.16Z",
        "lastModifiedAt": "2021-10-06T16:20:41.505Z"
      },
      // next remote device adapter  payload
    }    
  ]
}
```
#### <a name="status-codes"></a>Codes d’état

| Condition | Code d’état |
|--|--|
| Entité trouvée | 200 |
| Erreurs utilisateur générales | Plage 400 |
| Entité introuvable | 404 |
| Erreurs de serveur générales | Plage 500 |

### <a name="remotedeviceadapterset"></a>remoteDeviceAdapterSet

Crée un adaptateur d’appareil distant portant le nom donné s’il n’existe pas d’adaptateur ou met à jour un adaptateur existant portant ce nom. Dans la charge utile de réponse, et dans les appels `remoteDeviceAdapterList` ou `remoteDeviceAdapterGet`, les informations d’identification ou les secrets sont omis.


#### <a name="request"></a>Requête

```
  {
        "@apiVersion": "1.1",
        "name": "{remoteDeviceAdapterName}",
        "properties": {
            // Desired remote device adapter properties
        }
  }
```
#### <a name="response"></a>response

````
  {
    "status": 201,
    "payload": {
        "systemData": {
           "createdAt": "2021-10-11T18:16:46.491Z",
           "lastModifiedAt": "2021-10-11T18:16:46.491Z"
        },
        "name": "{remoteDeviceAdapterName}",
        "properties": {
            // Remote device adapter properties, except the credentials
        }
    }
  }
````
#### <a name="status-codes"></a>Codes d’état

| Condition | Code d’état | Code d’erreur détaillé |
|--|--|--|
| Entité existante mise à jour | 200 | N/A |
| Nouvelle entité créée | 201 | N/A |
| Erreurs utilisateur générales | Plage 400 | N/A |
| Erreurs de validation de l’adaptateur d’appareil distant | 400 | RemoteDeviceAdapterValidationError |
| Erreurs de validation du module | 400 | ModuleValidationError |
| Erreurs de validation des ressources | 409 | ResourceValidationError |
| Erreurs de serveur générales | Plage 500 | N/A |

### <a name="remotedeviceadapterget"></a>remoteDeviceAdapterGet

Cette valeur est similaire à `livePipelineTopologyGet`. Elle récupère un adaptateur d’appareil distant portant le nom spécifié, s’il existe (les informations d’identification ou les secrets sont omis).

#### <a name="request"></a>Requête

```
  {
        "@apiVersion": "1.1",
        "name": "{remoteDeviceAdapterName}"       
  }
```
#### <a name="response"></a>response

```
{
  "status": 200,
  "payload": {
    "value": [
      {
        "systemData": {
          "createdAt": "2021-10-06T10:28:04.560Z",
          "lastModifiedAt": "2021-10-06T10:28:04.560Z"
        },
        "name": "{remoteDeviceAdapterName}",
        // Remote device adapter properties, except the credentials
      }
    ]
  }
}
```

#### <a name="status-codes"></a>Codes d’état

| Condition | Code d’état |
|--|--|
| Succès | 200 |
| Erreurs utilisateur générales | Plage 400 |
| Erreurs de serveur générales | Plage 500 |

### <a name="remotedeviceadapterdelete"></a>remoteDeviceAdapterDelete

Supprime un seul adaptateur d’appareil distant, s’il existe.

#### <a name="request"></a>Requête

```
  {
        "@apiVersion": "1.1",
        "name": "{remoteDeviceAdapterName}"
  }
```
#### <a name="response"></a>response

```
  {
    "status": 200,
    "payload": null
  }
```
#### <a name="status-codes"></a>Codes d’état

| Condition | Code d’état | Code d’erreur détaillé |
|--|--|--|
| Adaptateur d’appareil distant correctement supprimé | 200 | N/A |
| Adaptateur d’appareil distant introuvable | 204 | N/A |
| Erreurs utilisateur générales | Plage 400 |  |
| Erreurs de serveur générales | Plage 500 |  |

### <a name="onvifdevicediscover-and-onvifdeviceget"></a>onvifDeviceDiscover et onvifDeviceGet
Les détails de ces appels sont fournis dans l’article sur la [découverte des appareils ONVIF](camera-discovery.md).

## <a name="next-steps"></a>Étapes suivantes

[Schéma de configuration de jumeau de module](module-twin-configuration-schema.md)
