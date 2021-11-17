---
title: Gestion des appareils Azure IoT avec l’extension IoT pour Azure CLI | Microsoft Docs
description: Utilisez l’outil Extension IoT pour Azure CLI permettant la gestion des appareils Azure IoT Hub, avec les méthodes Direct et les options de gestion des propriétés souhaitées du jumeau.
author: eross-msft
manager: ''
keywords: gestion des appareils iot azure, gestion des appareils azure iot hub, gestion des appareils iot, gestion des appareils iot hub
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/16/2018
ms.author: lizross
ms.openlocfilehash: 765dd7f8dd179187c904c5b779609823cd3503af
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132555013"
---
# <a name="use-the-iot-extension-for-azure-cli-for-azure-iot-hub-device-management"></a>Utiliser l’extension IoT pour Azure CLI permettant la gestion des appareils Azure IoT Hub

![Diagramme de bout en bout](media/iot-hub-get-started-e2e-diagram/2.png)

Dans cet article, vous apprenez à utiliser l’extension IoT pour Azure CLI avec différentes options de gestion sur votre machine de développement. [L’extension IoT pour Azure CLI](https://github.com/Azure/azure-iot-cli-extension) est une extension IoT open source qui étend les fonctionnalités de [d’Azure CLI](/cli/azure/overview). L’interface Azure CLI comprend des commandes permettant d’interagir avec les points de terminaison de gestion et ceux d’Azure Resource Manager. Par exemple, vous pouvez utiliser Azure CLI pour créer une machine virtuelle Azure ou un hub IoT. Une extension de l’interface CLI permet à un service Azure d’enrichir l’interface CLI, donnant ainsi accès à des fonctionnalités supplémentaires propres au service. L’extension IoT offre aux développeurs IoT un accès en ligne de commande à toutes les fonctionnalités d’IoT Hub, d’IoT Edge et du service IoT Hub Device Provisioning.

| Option de gestion          | Tâche  |
|----------------------------|-----------|
| Méthodes directes             | Faites agir un appareil comme commençant/arrêtant d’envoyer des messages ou comme redémarrant l’appareil.                                        |
| Propriétés souhaitées pour la représentation    | Mettez un appareil dans certains états, par exemple en réglant un voyant sur le vert ou en définissant l’intervalle d’envoi de télémétrie sur 30 minutes.         |
| Propriétés signalées pour la représentation   | Obtenez l’état signalé d’un appareil. Par exemple, l’appareil signale que le voyant clignote maintenant.                                    |
| Balises de représentation                  | Stocker les métadonnées spécifiques à l’appareil dans le cloud, par exemple, l’emplacement de déploiement d’un distributeur automatique.                         |
| Requêtes de représentations d’appareil        | Interrogez tous les jumeaux d’appareil pour récupérer ceux qui présentent des conditions arbitraires, comme l’identification des appareils qui sont disponibles pour utilisation. |

Pour plus d’explications sur les différences et des conseils sur l’utilisation de ces options, consultez [l’aide sur la communication appareil-à-cloud](iot-hub-devguide-d2c-guidance.md) et [l’aide sur la communication cloud-à-appareil](iot-hub-devguide-c2d-guidance.md).

Les représentations d’appareil sont des documents JSON qui stockent des informations sur l’état des appareils (métadonnées, configurations et conditions). IoT Hub conserve une représentation d’appareil pour chaque appareil que vous y connectez. Pour plus d’informations sur les représentations d’appareil, consultez [Prise en main des représentations d’appareils](iot-hub-node-node-twin-getstarted.md).

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

## <a name="prerequisites"></a>Prérequis

* Suivre le tutoriel [Simulateur en ligne Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md) ou un des tutoriels de l’appareil. Par exemple, vous pouvez accéder à [Raspberry Pi avec Node.js](iot-hub-raspberry-pi-kit-node-get-started.md) ou à l’un des démarrages rapides [Envoyer des données de télémétrie](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-csharp). Ces articles demandent les éléments suivants :

  * Un abonnement Azure actif.
  * Une instance Azure IoT Hub associée à votre abonnement.
  * Une application cliente qui envoie des messages à votre instance Azure IoT Hub.

* Vérifiez que votre appareil exécute l’application cliente tout au long de ce didacticiel.

* [Python 2.7x ou Python 3.x](https://www.python.org/downloads/)

* l’interface de ligne de commande Azure. Si vous devez l’installer, consultez [Installer l’interface Azure CLI](/cli/azure/install-azure-cli). Vous devez utiliser Azure CLI version 2.0.70 ou ultérieure. Utilisez `az –version` pour valider.

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

* Installez l’extension IoT. Le plus simple consiste à exécuter `az extension add --name azure-iot`. Le [Lisez-moi de l’extension IoT](https://github.com/Azure/azure-iot-cli-extension/blob/master/README.md) décrit différentes manières d’installer l’extension.

## <a name="sign-in-to-your-azure-account"></a>Connexion à votre compte Azure

Connectez-vous à votre compte Azure en exécutant la commande suivante :

```azurecli
az login
```

## <a name="direct-methods"></a>Méthodes directes

```azurecli
az iot hub invoke-device-method --device-id <your device id> \
  --hub-name <your hub name> \
  --method-name <the method name> \
  --method-payload <the method payload>
```

## <a name="device-twin-desired-properties"></a>Propriétés souhaitées du jumeau d’appareil

Définissez un intervalle de propriété = 3000 en exécutant la commande suivante :

```azurecli
az iot hub device-twin update -n <your hub name> \
  -d <your device id> --set properties.desired.interval=3000
```

Cette propriété peut être lue sur votre appareil.

## <a name="device-twin-reported-properties"></a>Propriétés signalées du jumeau d’appareil

Obtenez les propriétés signalées de l’appareil en exécutant la commande suivante :

```azurecli
az iot hub device-twin show -n <your hub name> -d <your device id>
```

L’une des propriétés jumelles signalées est $metadata.$lastUpdated, qui indique la dernière fois où l’application d’appareil a mis à jour son ensemble de propriétés signalées.

## <a name="device-twin-tags"></a>Balises du jumeau d’appareil

Affichez les balises et les propriétés de votre appareil en exécutant la commande suivante :

```azurecli
az iot hub device-twin show --hub-name <your hub name> --device-id <your device id>
```

Ajoutez un champ role = temperature&humidity à l’appareil en exécutant la commande suivante :

```azurecli
az iot hub device-twin update \
  --hub-name <your hub name> \
  --device-id <your device id> \
  --set tags='{"role":"temperature&humidity"}'
```

## <a name="device-twin-queries"></a>Requêtes de représentations d’appareil

Interrogez les appareils avec une balise role = temperature&humidity en exécutant la commande suivante :

```azurecli
az iot hub query --hub-name <your hub name> \
  --query-command "SELECT * FROM devices WHERE tags.role = 'temperature&humidity'"
```

Interrogez les appareils, à l’exception de ceux qui ont une balise role = temperature&humidity en exécutant la commande suivante :

```azurecli
az iot hub query --hub-name <your hub name> \
  --query-command "SELECT * FROM devices WHERE tags.role != 'temperature&humidity'"
```

## <a name="next-steps"></a>Étapes suivantes

Vous avez appris à analyser des messages appareil vers cloud et à envoyer des messages cloud vers appareil entre votre appareil IoT et l’instance IoT Hub.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]