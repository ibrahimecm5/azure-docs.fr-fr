---
title: API de placement de circuit Azure ExpressRoute CrossConnnections
description: Cet article fournit aux partenaires ExpressRoute une vue d’ensemble détaillée sur l’API de placement de circuit ExpressRoute CrossConnections.
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/19/2021
ms.author: mialdrid
ms.openlocfilehash: 3b464e0d9bb5c770109899f7aba8c9e0934d2064
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131097957"
---
# <a name="expressroute-circuit-placement-api"></a>API de placement de circuit ExpressRoute

L’API de placement de circuit du partenaire ExpressRoute permet aux partenaires ExpressRoute d’approvisionner la connectivité du circuit sur une paire de ports spécifique. Plus précisément, si un partenaire ExpressRoute gère plusieurs paires de ports à un emplacement d’appairage, il peut utiliser cette API pour sélectionner la paire de ports qui facilitera le circuit ExpressRoute.

Cette API utilise le type de ressource expressRouteCrossConnection. Pour plus d’informations, consultez [Développement et intégration de l’API ExpressRoute CrossConnection](cross-connections-api-development.md).

## <a name="workflow"></a>Workflow

1. Les clients ExpressRoute partagent la clé de service du circuit ExpressRoute cible.

1. Le partenaire ExpressRoute exécute une instruction GET à l’aide de l’API expressRouteProviderPorts pour identifier la **PortPairDescription** de la paire de ports cible. Le partenaire ExpressRoute peut interroger une liste de PortPairDescriptions sur toutes les paires de ports de l’abonnement ou étendre la requête à un emplacement d’appairage spécifique.

1. Une fois la PortPairDescription cible identifiée, le partenaire ExpressRoute exécute une instruction GET/PUT expressRouteCrossConnection pour déplacer le circuit ExpressRoute vers la paire de ports cible.

Les partenaires ExpressRoute gèrent la configuration des couches 2 et 3 en émettant des opérations REST sur la ressource expressRouteCrossConnections.

## <a name="api-development-and-integration-steps"></a>Étapes de développement et d’intégration d’API

### <a name="get-using-the-expressrouteproviderports-api-to-list-port-pairs"></a>Exécuter une instruction GET à l’aide de l’API expressRouteProviderPorts pour répertorier les paires de ports

Le partenaire ExpressRoute peut répertorier toutes les paires de ports dans l’abonnement du fournisseur cible ou répertorier les paires de ports au sein d’un emplacement d’appairage spécifique.

### <a name="to-get-a-list-of-all-port-pairs-for-a-provider"></a>Pour obtenir la liste de toutes les paires de ports pour un fournisseur

https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Network/expressRouteProviderPorts 

#### <a name="get-operation"></a>Opération GET

```rest
{
    "parameters": {
      "api-version": "2020-03-01",
      "subscriptionId": "subid"
    },
    "responses": {
        "200": {
          "body": {
            "value": [
              {
                "portPairDescriptor": "bvtazureixpportpair1",
                "id": "/subscriptions/subid/providers/Microsoft.Network/ExpressRouteProviderPort/bvtazureixpportpair1",
                "type": "Microsoft.Network/expressRouteProviderPort",
                "location": "uswest",
                "etag": "W/\"c0e6477e-8150-4d4f-9bf6-bb10e6acb63a\"",
                "properties": {
                    "portPairDescriptor": "bvtazureixpportpair",
                    "primaryAzurePort": "bvtazureixp01a",
                    "secondaryAzurePort": "bvtazureixp01b",
                    "peeringLocation": "SiliconValley",
                    "overprovisionFactor": 4,
                    "portBandwidthInMbps": 4000,
                    "usedBandwidthInMbps": 2500,
                    "remainingBandwidthInMbps": 1500
                }
              },
              {
                "portPairDescriptor": "bvtazureixpportpair2",
                "id": "/subscriptions/subid/providers/Microsoft.Network/ ExpressRouteProviderPort/bvtazureixpportpair2",
                "type": "Microsoft.Network/expressRouteProviderPort",
                "location": "uswest",
                "etag": "W/\"c0e6477e-8150-4d4f-9bf6-bb10e6acb63a\"",
                "properties": {
                    "portPairDescriptor": "bvtazureixpportpair2",
                    "primaryAzurePort": "bvtazureixp02a",
                    "secondaryAzurePort": "bvtazureixp02b",
                    "peeringLocation": "seattle",
                    "overprovisionFactor": 4,
                    "portBandwidthInMbps": 4000,
                    "usedBandwidthInMbps": 1200,
                    "remainingBandwidthInMbps": 1800
                }
              }
            ]
          }
        }
      }
    }
  }
}
```

**Code d’état de la réponse**

* 200 (OK) La demande est réussie.Elle récupère la liste des ports.
* 4XX (Bad Request) Échec de l’une des validations, par exemple : le subid du fournisseur n’est pas valide.

### <a name="list-of-all-port-for-a-provider-for-a-particular-peering-location"></a>Liste de tous les ports d’un fournisseur pour un emplacement d’appairage particulier

#### <a name="get"></a>GET

https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Network/expressRouteProviderPorts?location={locationName}

#### <a name="get-operation"></a>Opération GET

```rest
{
  "parameters": {
    "api-version": "2020-03-01",
    "locationName": "SiliconValley",
    "subscriptionId": "subid"
  },
  "responses": {
    "200": {
      "body": {
        "value": [
          {
            "portPairDescriptor": "bvtazureixpportpair1",
            "id": "/subscriptions/subid/providers/Microsoft.Network/ ExpressRouteProviderPort /bvtazureixpportpair1",
            "type": "Microsoft.Network/expressRouteProviderPort",
            "location": "uswest",
            "etag": "W/\"c0e6477e-8150-4d4f-9bf6-bb10e6acb63a\"",
            "properties": {
              "portPairDescriptor": "bvtazureixpportpair",
              "primaryAzurePort": "bvtazureixp01a",
              "secondaryAzurePort": "bvtazureixp01b",
              "peeringLocation": "SiliconValley",
              "overprovisionFactor": 4,
              "portBandwidthInMbps": 4000,
              "usedBandwidthInMbps": 2500,
              "remainingBandwidthInMbps": 1500
            }
          }
        ]
      }
    }
  }
}
```

**Code d’état de la réponse**

* 200 (OK) La demande est réussie. Elle récupère la liste des ports.
* 4XX (Bad Request) Échec de l’une des validations, par exemple : le subid du fournisseur n’est pas valide ou l’emplacement n’est pas valide.

Pour obtenir les détails d’un port particulier à l’aide de l’ID du descripteur de paire de ports

#### <a name="get"></a>GET

https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Network/expressRouteProviderPorts/{portPairDescriptor}

#### <a name="get-operation"></a>Opération GET

```rest
{
  "parameters": {
    "api-version": "2020-03-01",
    "portPairDescriptor": " bvtazureixpportpair1",
    "subscriptionId": "subid"
  },
  "responses": {
    "200": {
      "body": {
        "value": [
          {
            "portPairDescriptor": "bvtazureixpportpair1",
            "id": "/subscriptions/subid/providers/Microsoft.Network/ExpressRouteProviderPort/bvtazureixpportpair1",
            "type": "Microsoft.Network/expressRouteProviderPort",
            "location": "uswest",
            "etag": "W/\"c0e6477e-8150-4d4f-9bf6-bb10e6acb63a\"",
            "properties": {
              "portPairDescriptor": "bvtazureixpportpair",
              "primaryAzurePort": "bvtazureixp01a",
              "secondaryAzurePort": "bvtazureixp01b",
              "peeringLocation": "SiliconValley",
              "overprovisionFactor": 4,
              "portBandwidthInMbps": 4000,
              "usedBandwidthInMbps": 2500,
              "remainingBandwidthInMbps": 15
            }
          }
        ]
      }
    }
  }
}
```

**Description du code d’état**

* 200 (OK) La demande est réussie. Elle récupère les détails du port.
* 204 La paire de ports avec l’ID du descripteur mentionné n’est pas disponible.
* 4XX (Bad Request) Échec de l’une des validations, par exemple : le subid du fournisseur n’est pas valide.

### <a name="put-expressroutecrossconnection-api-to-move-a-circuit-to-a-specific-port-pair"></a>Exécuter une instruction PUT à l’aide de l’API expressRouteCrossConnection pour déplacer un circuit vers une paire de ports spécifique

Une fois que le portPairDescriptor de la paire de ports cible est identifié, le partenaire ExpressRoute peut utiliser l’[API ExpressRouteCrossConnection](/rest/api/expressroute/express-route-cross-connections/create-or-update) pour déplacer le circuit ExpressRoute vers une paire de ports spécifique.

Actuellement, cette API est utilisée par les fournisseurs pour mettre à jour l’état d’approvisionnement du circuit. Cette même API sera utilisée par les fournisseurs pour mettre à jour la paire de ports du circuit.

Actuellement, les propriétés primaryAzurePort et secondaryAzurePort sont en lecture seule. Nous avons maintenant désactivé les propriétés en lecture seule pour ces ports.

#### <a name="put"></a>PUT

https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/expressRouteCrossConnections/{crossConnectionName}?api-version=2021-02-01

#### <a name="put-operation"></a>Opération PUT

```rest
{
"parameters": {
    "api-version": "2020-03-01",
    "crossConnectionName": "The name of the cross connection",
    "subscriptionId": "subid"
  }
},
{
Request   "body": {
    " primaryAzurePort ": " bvtazureixp03a"
     "secondaryAzurePort": "bvtazureixp03b",
  }
}
Response:
{
  "name": "<circuitServiceKey>",
  "id": "/subscriptions/subid/resourceGroups/CrossConnectionSiliconValley/providers/Microsoft.Network/expressRouteCrossConnections/<circuitServiceKey>",
  "type": "Microsoft.Network/expressRouteCrossConnections",
  "location": "brazilsouth",
  "properties": {
    "provisioningState": "Enabled",
    "expressRouteCircuit": {
      "id": "/subscriptions/subid/resourceGroups/ertest/providers/Microsoft.Network/expressRouteCircuits/er1"
    },
    "peerings": [],
    "peeringLocation": "SiliconValley",
    "bandwidthInMbps": 1000,
    "primaryAzurePort": "bvtazureixp03a",
    "secondaryAzurePort": "bvtazureixp03b",
    "sTag": 2,
    "serviceProviderProvisioningState": "NotProvisioned"
  }
}
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur toutes les API REST ExpressRoute, consultez [API REST ExpressRoute](/rest/api/expressroute/).