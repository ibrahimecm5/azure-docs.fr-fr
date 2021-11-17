---
title: API d’opération de longue durée Azure Maps V2
description: Découvrez le traitement en arrière-plan asynchrone de longue durée V2 dans Azure Maps
author: anastasia-ms
ms.author: v-stharr
ms.date: 05/18/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
ms.custom: mvc
ms.openlocfilehash: ba9ec841d4a1118a342032d81cbb534212b26786
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132312768"
---
# <a name="creator-long-running-operation-api-v2"></a>API d’opération de longue durée du Créateur V2

Certaines API Azure Maps utilisent un [modèle de demande-réponse asynchrone](/azure/architecture/patterns/async-request-reply). Ce modèle permet à Azure Maps de fournir des services hautement disponibles et réactifs. Cet article explique l’implémentation spécifique d’Azure Map du traitement en arrière-plan asynchrone de longue durée.

## <a name="submit-a-request"></a>Soumettre une demande

Une application cliente démarre une opération de longue durée via un appel synchrone à une API HTTP. En général, cet appel présente la forme d’une requête HTTP POST. Quand une charge de travail asynchrone est correctement créée, l’API retourne un code d’état `202` HTTP, indiquant que la requête a été acceptée. Cette réponse contient un en-tête `Location` pointant vers un point de terminaison que le client peut sonder pour vérifier l’état de l’opération de longue durée.

### <a name="example-of-a-success-response"></a>Exemple de réponse de réussite

```HTTP
Status: 202 Accepted
Operation-Location: https://atlas.microsoft.com/service/operations/{operationId} 

```

Si l’appel n’est pas validé, l’API retourne plutôt une réponse HTTP `400` pour une demande incorrecte. Le corps de la réponse fournit au client des informations supplémentaires sur la raison pour laquelle la demande n’est pas valide.

### <a name="monitor-the-operation-status"></a>Surveiller l’état de l’opération

Le point de terminaison d’emplacement fourni dans les en-têtes de réponse acceptée peut être sondé pour vérifier l’état de l’opération de longue durée. Le corps de la réponse de la demande d’état de l’opération contient toujours les propriétés `status` et `created`. La propriété `status` indique l’état actuel de l’opération de longue durée. Les états possibles sont `"NotStarted"`, `"Running"`, `"Succeeded"` et `"Failed"`. La propriété `created` indique l’heure de la demande initiale de démarrage de l’opération de longue durée. Quand l’état est `"NotStarted"` ou `"Running"`, un en-tête `Retry-After` est également fourni avec la réponse. L’en-tête `Retry-After`, mesuré en secondes, permet de déterminer à quel moment le prochain appel de sondage à l’API d’état de l’opération doit être effectué.

### <a name="example-of-running-a-status-response"></a>Exemple d’exécution d’une réponse d’état

```HTTP
Status: 200 OK
Retry-After: 30
{
    "operationId": "c587574e-add9-4ef7-9788-1635bed9a87e",
    "created": "3/11/2020 8:45:13 PM +00:00",
    "status": "Running"
}
```

## <a name="handle-operation-completion"></a>Gérer la fin de l’opération

Une fois l’opération de longue durée terminée, l’état de la réponse est `"Succeeded"` ou `"Failed"`. Toutes les réponses renverront un code HTTP 200 OK. Lorsqu’une nouvelle ressource a été créée à partir d’une opération de longue durée, la réponse contient également un en-tête `Resource-Location` qui pointe vers les métadonnées relatives à la ressource. En cas d’échec, le corps de la réponse contient une propriété `error`. Les données d’erreur adhèrent à la spécification d’erreur OData.

### <a name="example-of-success-response"></a>Exemple de réponse de réussite

```HTTP
Status: 200 OK
Resource-Location: "https://atlas.microsoft.com/tileset/{tileset-id}"
 {
    "operationId": "c587574e-add9-4ef7-9788-1635bed9a87e",
    "created": "2021-05-06T07:55:19.5256829+00:00",
    "status": "Succeeded"
}
```

### <a name="example-of-failure-response"></a>Exemple de réponse d’échec

```HTTP
Status: 200 OK

{
    "operationId": "c587574e-add9-4ef7-9788-1635bed9a87e",
    "created": "3/11/2020 8:45:13 PM +00:00",
    "status": "Failed",
    "error": {
        "code": "InvalidFeature",
        "message": "The provided feature is invalid.",
        "details": {
            "code": "NoGeometry",
            "message": "No geometry was provided with the feature."
        }
    }
}
```
