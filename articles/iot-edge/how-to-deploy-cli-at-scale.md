---
title: Déployer des modules à grande échelle à l’aide d’Azure CLI – Azure IoT Edge
description: Utilisez l’extension IoT pour Azure CLI afin de créer des déploiements automatiques pour les groupes d’appareils IoT Edge.
keywords: ''
author: kgremban
ms.author: kgremban
ms.date: 10/13/2020
ms.topic: conceptual
ms.service: iot-edge
ms.custom: devx-track-azurecli
services: iot-edge
ms.openlocfilehash: 504ae03ecff532fff5a8343d02fd8bba21524cfd
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132397316"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-by-using-the-azure-cli"></a>Déployer et surveiller des modules IoT Edge à grande échelle à l’aide d’Azure CLI

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Créez un [déploiement automatique Azure IoT Edge](module-deployment-monitoring.md) à l’aide de l’interface de ligne de commande Azure pour gérer les déploiements en cours de plusieurs appareils à la fois. Les déploiements automatiques IoT Edge font partie de la fonctionnalité [Gestion automatique des appareils](../iot-hub/iot-hub-automatic-device-management.md) d’Azure IoT Hub. Les déploiements sont des processus dynamiques qui vous permettent de déployer plusieurs modules sur plusieurs appareils, de suivre l’état et l’intégrité des modules, et d’apporter des modifications si nécessaire.

Dans cet article, vous configurez Azure CLI et l’extension IoT. Vous apprendrez ensuite à déployer des modules sur un ensemble d’appareils IoT Edge et à superviser la progression à l’aide des commandes CLI disponibles.

## <a name="prerequisites"></a>Prérequis

* Un [hub IoT](../iot-hub/iot-hub-create-using-cli.md) dans votre abonnement Azure.
* Un ou plusieurs appareils IoT Edge.

  Si vous n’avez aucun appareil IoT Edge configuré, vous pouvez en créer un sur une machine virtuelle Azure. Suivez la procédure décrite dans l’un de ces articles de démarrage rapide : [Créer un appareil Linux virtuel](quickstart-linux.md) ou [Créer un appareil Windows virtuel](quickstart.md).

* [Azure CLI](/cli/azure/install-azure-cli) dans votre environnement. Votre version d’Azure CLI doit être la version 2.0.70 ou ultérieure. Utilisez `az --version` pour valider. Cette version prend en charge les commandes d’extension az et introduit l’infrastructure de la commande Knack.
* [Extension IoT pour Azure CLI](https://github.com/Azure/azure-iot-cli-extension).

## <a name="configure-a-deployment-manifest"></a>Configurer un manifeste de déploiement

Un manifeste de déploiement est un document JSON qui décrit les modules à déployer, le flux des données entre les modules et les propriétés souhaitées des jumeaux de module. Pour plus d’informations, consultez [Déployer des modules et établir des routes dans IoT Edge](module-composition.md).

Pour déployer des modules en utilisant Azure CLI, enregistrez le manifeste de déploiement localement sous forme de fichier .txt. Vous utiliserez le chemin du fichier dans la section suivante au moment d’exécuter la commande permettant d’appliquer la configuration à votre appareil.

Par exemple, voici un manifeste de déploiement de base comportant un seul module :

```json
{
  "content": {
    "modulesContent": {
      "$edgeAgent": {
        "properties.desired": {
          "schemaVersion": "1.1",
          "runtime": {
            "type": "docker",
            "settings": {
              "minDockerVersion": "v1.25",
              "loggingOptions": "",
              "registryCredentials": {}
            }
          },
          "systemModules": {
            "edgeAgent": {
              "type": "docker",
              "settings": {
                "image": "mcr.microsoft.com/azureiotedge-agent:1.1",
                "createOptions": "{}"
              }
            },
            "edgeHub": {
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/azureiotedge-hub:1.1",
                "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
              }
            }
          },
          "modules": {
            "SimulatedTemperatureSensor": {
              "version": "1.1",
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
                "createOptions": "{}"
              }
            }
          }
        }
      },
      "$edgeHub": {
        "properties.desired": {
          "schemaVersion": "1.0",
          "routes": {
            "upstream": "FROM /messages/* INTO $upstream"
          },
          "storeAndForwardConfiguration": {
            "timeToLiveSecs": 7200
          }
        }
      },
      "SimulatedTemperatureSensor": {
        "properties.desired": {
          "SendData": true,
          "SendInterval": 5
        }
      }
    }
  }
}
```

>[!NOTE]
>Cet exemple de manifeste de déploiement utilise la version de schéma 1.1 pour l’agent et le hub IoT Edge. La version 1.1 du schéma a été publiée avec la version 1.0.10 d’IoT Edge. Elle permet d’activer des fonctionnalités telles que l’ordre de démarrage des modules et la hiérarchisation des itinéraires.

## <a name="layered-deployment"></a>Déploiement en couches

Les déploiements en couches sont des déploiements automatiques qui peuvent être empilé les uns sur les autres. Pour plus d’informations sur les déploiements en couches, consultez [Comprendre les déploiements automatiques IoT Edge pour un seul ou de nombreux appareils](module-deployment-monitoring.md).

Les déploiements en couches peuvent être créés et gérés à l'aide d'Azure CLI comme n’importe quel déploiement automatique, à quelques différences près. Après la création d’un déploiement en couches, Azure CLI fonctionne pour les déploiements en couches de la même manière que pour tout autre déploiement. Pour créer un déploiement en couches, ajoutez la balise `--layered` à la commande create.

La deuxième différence réside dans la construction du manifeste de déploiement. Alors que les déploiements automatiques standard doivent contenir les modules d’exécution du système en plus des modules utilisateur, les déploiements en couches, quant à eux, peuvent uniquement contenir des modules utilisateur. Les déploiements en couches impliquent également un déploiement automatique standard sur un appareil afin de fournir les composants requis de chaque appareil IoT Edge, comme les modules d’exécution du système.

Par exemple, voici un manifeste de déploiement en couches de base comportant un seul module :

```json
{
  "content": {
    "modulesContent": {
      "$edgeAgent": {
        "properties.desired.modules.SimulatedTemperatureSensor": {
          "settings": {
            "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
              "createOptions": "{}"
          },
          "type": "docker",
          "status": "running",
          "restartPolicy": "always",
          "version": "1.0"
        }
      },
      "$edgeHub": {
        "properties.desired.routes.upstream": "FROM /messages/* INTO $upstream"
      },
      "SimulatedTemperatureSensor": {
        "properties.desired": {
          "SendData": true,
          "SendInterval": 5
        }
      }
    }
  }
}
```
>[!NOTE]
> Ce manifeste de déploiement en couches diffère légèrement d’un manifeste de déploiement standard. Les propriétés souhaitées des modules d’exécution sont réduites et utilisent la notation par points. Cette mise en forme est requise pour que le portail Azure reconnaisse un déploiement en couches. Par exemple :
>
> - `properties.desired.modules.<module_name>`
> - `properties.desired.routes.<route_name>`

L’exemple précédent illustrait le déploiement en couches définissant `properties.desired` pour un module. Si ce déploiement en couches venait à cibler un appareil sur lequel le même module est déjà appliqué, il remplacerait les propriétés souhaitées existantes. Pour mettre à jour les propriétés souhaitées plutôt que de les remplacer, vous pouvez définir une nouvelle sous-section. Par exemple :

```json
"SimulatedTemperatureSensor": {
  "properties.desired.layeredProperties": {
    "SendData": true,
    "SendInterval": 5
  }
}
```

Vous pouvez également exprimer cela comme suit :

```json
"SimulatedTemperatureSensor": {
  "properties.desired.layeredProperties.SendData" : true,
  "properties.desired.layeredProperties.SendInterval": 5
}
```

>[!NOTE]
>À l’heure actuelle, tous les déploiements en couches doivent inclure un objet `edgeAgent` pour être considéré comme valides. Même si un déploiement en couches met uniquement à jour les propriétés du module, incluez un objet vide. Par exemple : `"$edgeAgent":{}`. Un déploiement en couches avec un objet `edgeAgent` vide apparaît comme **ciblé** dans le jumeau de module `edgeAgent`, et non **appliqué**.

En résumé, pour créer un déploiement en couches :

- Ajoutez l’indicateur `--layered` à la commande create d’Azure CLI.
- N’incluez pas de modules système.
- Utilisez la notation par points complète sous `$edgeAgent` et `$edgeHub`.

Pour plus d’informations sur la configuration de jumeaux de module au sein des déploiements en couches, consultez [Déploiement en couches](module-deployment-monitoring.md#layered-deployment).

## <a name="identify-devices-by-using-tags"></a>Identifier les appareils à l’aide de balises

Avant de pouvoir créer un déploiement, vous devez être en mesure de spécifier les appareils concernés. Azure IoT Edge identifie les appareils en utilisant des *balises* dans le jumeau d’appareil. 

Chaque appareil peut avoir plusieurs balises que vous pouvez définir comme bon vous semble pour votre solution. Par exemple, si vous gérez un campus de bâtiments intelligents, vous pouvez ajouter les balises suivantes à un appareil :

```json
"tags":{
  "location":{
    "building": "20",
    "floor": "2"
  },
  "roomtype": "conference",
  "environment": "prod"
}
```

Pour plus d’informations sur les étiquettes et les jumeaux d’appareils, consultez [Comprendre et utiliser les jumeaux d’appareil IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md).

## <a name="create-a-deployment"></a>Créer un déploiement

Vous déployez des modules sur vos appareils cibles en créant un déploiement qui se compose du manifeste de déploiement et d’autres paramètres.

Utilisez la commande [az iot edge deployment create](/cli/azure/iot/edge/deployment) pour créer un déploiement :

```azurecli
az iot edge deployment create --deployment-id [deployment id] --hub-name [hub name] --content [file path] --labels "[labels]" --target-condition "[target query]" --priority [int]
```

Utilisez la même commande avec l’indicateur `--layered` pour créer un déploiement en couches.

La commande create pour le déploiement utilise les paramètres suivants :

* **--layered**. Indicateur facultatif permettant d’identifier le déploiement en tant que déploiement en couches.
* **--deployment-id**. Nom du déploiement à créer dans le hub IoT. Donnez à votre déploiement un nom unique comportant au plus 128 lettres minuscules. Évitez les espaces et les caractères non valides suivants : `& ^ [ ] { } \ | " < > /`. Ce paramètre est obligatoire.
* **--content**. Chemin d’accès au fichier JSON du manifeste de déploiement. Ce paramètre est obligatoire.
* **--hub-name**. Nom du hub IoT dans lequel le déploiement sera créé. Le hub doit être dans l’abonnement actuel. Pour modifier votre abonnement actuel, utilisez la commande `az account set -s [subscription name]`.
* **--labels**. Paires nom-valeur qui décrivent vos déploiements et vous aident à les suivre. Les étiquettes utilisent le format JSON pour les noms et les valeurs. Par exemple : `{"HostPlatform":"Linux", "Version:"3.0.1"}`.
* **--target-condition**. Condition qui détermine les appareils qui seront ciblés par ce déploiement. La condition est basée sur les balises du jumeau d’appareil ou sur les propriétés signalées par le jumeau d’appareil, et elle doit correspondre au format de l’expression. Par exemple : `tags.environment='test' and properties.reported.devicemodel='4000x'`.
* **--priority**. Entier positif. Si deux déploiements ou plus sont ciblés sur le même appareil, le déploiement ayant la valeur numérique la plus élevée pour priority s’applique.
* **--metrics**. Métriques qui interrogent les propriétés signalées par `edgeHub` afin de suivre l’état d’un déploiement. Les métriques utilisent une entrée JSON ou un chemin d’accès à un fichier. Par exemple : `'{"queries": {"mymetric": "SELECT deviceId FROM devices WHERE properties.reported.lastDesiredStatus.code = 200"}}'`.

Pour superviser un déploiement à l’aide d’Azure CLI, consultez [Superviser les déploiements IoT Edge](how-to-monitor-iot-edge-deployments.md#monitor-a-deployment-with-azure-cli).

## <a name="modify-a-deployment"></a>Modifier un déploiement

Quand vous modifiez un déploiement, les modifications sont répliquées immédiatement sur tous les appareils ciblés.

Si vous mettez à jour la condition cible, les mises à jour suivantes se produisent :

* Si un appareil ne remplissait pas l’ancienne condition cible, mais qu’il remplit la nouvelle condition cible et que ce déploiement a la priorité la plus élevée pour cet appareil, ce déploiement est appliqué à l’appareil.
* Si un appareil exécutant actuellement ce déploiement ne remplit plus la condition cible, il désinstalle ce déploiement et adopte le déploiement suivant dans l’ordre de priorité.
* Si un appareil exécutant actuellement ce déploiement ne remplit plus la condition cible et ne remplit la condition cible d’aucun autre déploiement, aucun changement ne se produit sur l’appareil. L’appareil continue d’exécuter ses modules actuels dans leur état actuel, mais il n’est plus géré dans le cadre de ce déploiement. Lorsque l’appareil remplit la condition cible d’un autre déploiement, il désinstalle ce déploiement et adopte le nouveau.

Vous ne pouvez pas mettre à jour le contenu d’un déploiement, qui comprend les modules et itinéraires définis dans le manifeste de déploiement. Si vous souhaitez mettre à jour le contenu d’un déploiement, créez un nouveau déploiement qui cible les mêmes appareils avec une priorité plus élevée. Vous pouvez modifier certaines propriétés d’un module existant, y compris la condition, les étiquettes, les métriques et la priorité de la cible.

Utilisez la commande [az iot edge deployment update](/cli/azure/iot/edge/deployment) pour mettre à jour un déploiement :

```azurecli
az iot edge deployment update --deployment-id [deployment id] --hub-name [hub name] --set [property1.property2='value']
```

La commande « deployment update » utilise les paramètres suivants :

* **--deployment-id**. Nom du déploiement qui existe dans le hub IoT.
* **--hub-name**. Nom du hub IoT dans lequel le déploiement existe. Le hub doit être dans l’abonnement actuel. Pour basculer sur l’abonnement souhaité, utilisez la commande `az account set -s [subscription name]`.
* **--set**. Mettez à jour une propriété dans le déploiement. Vous pouvez mettre à jour les propriétés suivantes :
  * `targetCondition` (par exemple, `targetCondition=tags.location.state='Oregon'`)
  * `labels`
  * `priority`
* **--add**. Ajoutez une nouvelle propriété au déploiement, y compris des conditions cibles ou des étiquettes.
* **--remove**. Supprimez une propriété existante, y compris des conditions cibles ou des étiquettes.

## <a name="delete-a-deployment"></a>Supprimer un déploiement

Lorsque vous supprimez un déploiement, tous les appareils adoptent leur déploiement suivant dans l’ordre de priorité. Si vos appareils ne remplissent la condition cible d’aucun autre déploiement, les modules ne sont pas supprimés lors de la suppression du déploiement.

Utilisez la commande [az iot edge deployment delete](/cli/azure/iot/edge/deployment) pour supprimer un déploiement :

```azurecli
az iot edge deployment delete --deployment-id [deployment id] --hub-name [hub name]
```

La commande `deployment delete` utilise les paramètres suivants :

* **--deployment-id**. Nom du déploiement qui existe dans le hub IoT.
* **--hub-name**. Nom du hub IoT dans lequel le déploiement existe. Le hub doit être dans l’abonnement actuel. Pour basculer sur l’abonnement souhaité, utilisez la commande `az account set -s [subscription name]`.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur le [déploiement des modules sur des appareils IoT Edge](module-deployment-monitoring.md).
