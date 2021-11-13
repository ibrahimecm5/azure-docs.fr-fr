---
title: Mise à l’échelle dans Azure Container Apps
description: Découvrez comment les applications effectuent un scale-out et un scale-in dans Azure Container Apps.
services: app-service
author: craigshoemaker
ms.service: app-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: cshoe
ms.custom: ignite-fall-2021
ms.openlocfilehash: b02d4d06b855229f41a0bae0b04d1729a29e4e49
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131579005"
---
# <a name="set-scaling-rules-in-azure-container-apps"></a>Définir des règles de mise à l’échelle dans Azure Container Apps

Azure Container Apps gère la mise à l’échelle horizontale automatique en utilisant un ensemble de règles de mise à l’échelle déclaratives. Quand une application conteneur effectue un scale-out, des instances de l’application conteneur sont créées à la demande. Ces instances sont appelées réplicas.

Les règles de mise à l’échelle sont définies dans la section `resources.properties.template.scale` de la [configuration](overview.md). Deux propriétés de mise à l’échelle s’appliquent à toutes les règles de votre application conteneur.

| Propriété de mise à l’échelle | Description | Valeur par défaut | Valeur min | Valeur max |
|---|---|---|---|---|
| `minReplicas` | Nombre minimal de réplicas en cours d’exécution pour votre application conteneur. | 0 | 1 | 25 |
| `maxReplicas` | Nombre maximal de réplicas en cours d’exécution pour votre application conteneur. | n/a | 1 | 25 |

- Si la mise à l’échelle de votre application conteneur est nulle, vous n’êtes pas facturé.
- Les règles de mise à l’échelle sont définies dans le tableau `rules`.
- Pour avoir toujours une instance de votre application en cours d’exécution, définissez `minReplicas` sur 1 ou plus.
- Les réplicas inactifs qui restent en mémoire sont facturés dans la catégorie « Frais d’inactivité ».
- Les changements effectués sur les règles de mise à l’échelle sont des changements [de niveau révision](overview.md).

> [!IMPORTANT]
> Les quantités de réplicas sont un chiffre cible et non une garantie. Même si vous définissez `maxReplicas` sur `1`, il n’y a aucune garantie de cohérence de thread.

## <a name="scale-triggers"></a>Déclencheurs de mise à l’échelle

Container Apps prend en charge un grand nombre de déclencheurs de mise à l’échelle. Pour plus d’informations sur les déclencheurs de mise à l’échelle pris en charge, consultez [KEDA Scalers](https://keda.sh/docs/scalers/).

La documentation KEDA montre des exemples de code en YAML, tandis que le modèle ARM de Container Apps est en JSON. Quand vous transformez des exemples de KEDA pour les adapter à vos besoins, remplacez la casse [kebab](https://en.wikipedia.org/wiki/Naming_convention_(programming)#Delimiter-separated_words) des noms propriétés par une casse [mixte](https://en.wikipedia.org/wiki/Naming_convention_(programming)#Letter_case-separated_words).

## <a name="http"></a>HTTP

Les règles de mise à l’échelle HTTP vous permettent de contrôler le seuil qui détermine quand effectuer le scale-out.

| Propriété de mise à l’échelle | Description | Valeur par défaut | Valeur min | Valeur max |
|---|---|---|---|---|
| `concurrentRequests`| Dès que le nombre de demandes dépasse cette valeur, d’autres réplicas sont ajoutés, sans dépasser `maxReplicas`. | 50 | 1 | n/a |

```json
{
  ...
  "resources": {
    ...
    "properties": {
      ...
      "template": {
        ...
        "scale": {
          "minReplicas": 0,
          "maxReplicas": 5,
          "rules": [{
            "name": "http-rule",
            "http": {
              "metadata": {
                  "concurrentRequests": 100
              }
            }
          }]
        }
      }
    }
  }
}
```

Dans cet exemple, l’application conteneur effectue un scale-out jusqu’à cinq réplicas et peut effectuer un scale-down jusqu’à zéro instance. Le seuil de mise à l’échelle est défini sur 100 demandes simultanées par seconde.

## <a name="event-driven"></a>Basé sur les événements

Container Apps peut être mis à l’échelle en fonction d’une grande variété de types d’événement. Tout événement pris en charge par [KEDA](https://keda.sh/docs/scalers/) est pris en charge dans Container Apps.

Chaque type d’événement a différentes propriétés dans la section `metadata` de la définition KEDA. Utilisez ces propriétés pour définir une règle de mise à l’échelle dans Container Apps.

L’exemple suivant montre comment créer une règle de mise à l’échelle basée sur un déclencheur [Azure Service Bus](https://keda.sh/docs/scalers/azure-service-bus/).

```json
{
  ...
  "resources": {
    ...
    "properties": {
      "configuration": {
        "secrets": [{
          "name": "servicebusconnectionstring",
          "value": "<MY-CONNECTION-STRING-VALUE>"
        }],
      },
      "template": {
        ...
        "scale": {
          "minReplicas": "0",
          "maxReplicas": "10",
          "rules": [
          {
            "name": "queue-based-autoscaling",
            "custom": {
              "type": "azure-servicebus",
              "metadata": {
                "queueName": "myServiceBusQueue",
                "messageCount": "20"
              },
              "auth": [{
                "secretRef": "servicebusconnectionstring",
                "triggerParameter": "connection"
              }]
        }
    }]
}
```

Dans cet exemple, l’application conteneur est mise à l’échelle en fonction du comportement suivant :

- Quand le nombre de messages dans la file d’attente dépasse 20, des réplicas sont créés.
- La chaîne de connexion de la file d’attente est fournie comme paramètre au fichier de configuration et référencé avec la propriété `secretRef`.

## <a name="cpu"></a>UC

La mise à l’échelle du processeur permet à votre application d’effectuer un scale-in ou un scale-out en fonction de la quantité de processeur utilisée. La mise à l’échelle du processeur n’autorise pas de mise à l’échelle nulle de votre application conteneur. Pour plus d’informations sur ce déclencheur, consultez [KEDA CPU scale trigger](https://keda.sh/docs/scalers/cpu/).

L’exemple suivant montre comment créer une règle de mise à l’échelle de processeur.

```json
{
  ...
  "resources": {
    ...
    "properties": {
      ...
      "template": {
        ...
        "scale": {
          "minReplicas": "0",
          "maxReplicas": "10",
          "rules": [{
            "name": "cpuScalingRule",
            "custom": {
              "type": "cpu",
              "metadata": {
                "type": "Utilization",
                "value": "50"
              }
            }
          }]
        }
      }
    }
  }
}
```

- Dans cet exemple, l’application conteneur est mise à l’échelle quand l’utilisation du processeur dépasse 50 %.
- Au minimum, un seul réplica reste en mémoire pour les applications qui sont mises à l’échelle en fonction de l’utilisation du processeur.

## <a name="memory"></a>Mémoire

La mise à l’échelle de la mémoire permet à votre application d’effectuer un scale-in ou un scale-out en fonction de la quantité de mémoire utilisée. La mise à l’échelle de la mémoire n’autorise pas de mise à l’échelle nulle de votre application conteneur. Pour plus d’informations sur ce déclencheur de mise à l’échelle, consultez [KEDA Memory scaler](https://keda.sh/docs/scalers/memory/).

L’exemple suivant montre comment créer une règle de mise à l’échelle de mémoire.

```json
{
  ...
  "resources": {
    ...
    "properties": {
      ...
      "template": {
        ...
        "scale": {
          "minReplicas": "0",
          "maxReplicas": "10",
          "rules": [{
            "name": "memoryScalingRule",
            "custom": {
              "type": "memory",
              "metadata": {
                "type": "Utilization",
                "value": "50"
              }
            }
          }]
        }
      }
    }
  }
}
```

- Dans cet exemple, l’application conteneur est mise à l’échelle quand l’utilisation de la mémoire dépasse 50 %.
- Au minimum, un seul réplica reste en mémoire pour les applications qui sont mises à l’échelle en fonction de l’utilisation de la mémoire.


## <a name="considerations"></a>Considérations

- La mise à l’échelle verticale n’est pas prise en charge.
- Les quantités de réplicas sont un chiffre cible et non une garantie.
  - Même si vous définissez `maxReplicas` sur `1`, il n’y a aucune garantie de cohérence de thread.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Sécuriser votre application de conteneur](secure-app.md)
