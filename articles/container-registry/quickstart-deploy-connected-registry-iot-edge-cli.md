---
title: 'Démarrage rapide : Déployer un registre connecté sur un appareil IoT Edge'
description: Utilisez des commandes Azure CLI et le Portail Azure pour déployer un registre de conteneurs Azure connecté dans l’appareil Azure IoT Edge.
ms.topic: quickstart
ms.date: 10/21/2021
ms.author: memladen
author: toddysm
ms.custom: ignite-fall-2021
ms.openlocfilehash: e7d7ac92bda1aade5a446e6b0fe2a2b9966d5eb9
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096617"
---
# <a name="quickstart-deploy-a-connected-registry-to-an-iot-edge-device"></a>Démarrage rapide : Déployer un registre connecté sur un appareil IoT Edge

Dans ce guide de démarrage rapide, vous utilisez la commande Azure CLI pour déployer un [registre connecté](intro-connected-registry.md) en tant que module sur un appareil Azure IoT Edge. L’appareil IoT Edge peut accéder au registre de conteneurs Azure parent dans le cloud.

Pour obtenir une vue d’ensemble de l’utilisation d’un registre connecté avec IoT Edge, consultez [Utilisation d’un registre connecté avec Azure IoT Edge](overview-connected-registry-and-iot-edge.md). Ce scénario correspond à un appareil situé dans la [couche supérieure](overview-connected-registry-and-iot-edge.md#top-layer) d’une hiérarchie IoT Edge. 


[!INCLUDE [Prepare Azure CLI environment](../../includes/azure-cli-prepare-your-environment.md)]
* Appareil Azure IoT Hub et IoT Edge. Pour connaître les étapes de déploiement, consultez [Démarrage rapide : Déployer votre premier module IoT Edge sur un appareil Linux virtuel](../iot-edge/quickstart-linux.md).
  > [!IMPORTANT]
  > Pour accéder ultérieurement aux modules déployés sur l’appareil IoT Edge, veillez à ouvrir les ports 8000, 5671 et 8883 sur l’appareil. Pour plus d’informations, consultez [Guide d’ouverture de ports vers une machine virtuelle avec le portail Azure](../virtual-machines/windows/nsg-quickstart-portal.md). 

* Ressource de registre connecté dans Azure. Pour découvrir les étapes du déploiement, consultez les démarrages rapides à l’aide d’[Azure CLI][quickstart-connected-registry-cli] ou du [portail Azure][quickstart-connected-registry-portal]. 

    * Un registre connecté en mode `ReadWrite` ou `ReadOnly` peut être utilisé dans ce scénario. 
    * Dans les commandes décrites dans cet article, le nom du registre connecté est stocké dans la variable d’environnement *CONNECTED _REGISTRY_RW*.

[!INCLUDE [container-registry-connected-import-images](../../includes/container-registry-connected-import-images.md)]

## <a name="retrieve-connected-registry-configuration"></a>Récupérer la configuration de registre connecté

Avant de déployer le registre connecté sur l’appareil IoT Edge, vous devez récupérer les paramètres de configuration à partir de la ressource de registre connecté dans Azure.

Utilisez la commande [az acr connected-registry get-settings][az-acr-connected-registry-get-settings] pour obtenir les informations de paramètres nécessaires à l’installation d’un registre connecté. L’exemple suivant spécifie HTTPs comme protocole parent. Ce protocole est requis lorsque le registre parent est un registre cloud.

```azurecli
az acr connected-registry get-settings \
  --registry $REGISTRY_NAME \
  --name $CONNECTED_REGISTRY_RW \
  --parent-protocol https
```

Par défaut, les informations de paramètres n’incluent pas le mot de passe du [jeton de synchronisation](overview-connected-registry-access.md#sync-token) qui est également nécessaire pour déployer le registre connecté. Vous pouvez également générer l’un des mots de passe en passant le paramètre `--generate-password 1` ou `generate-password 2`. Enregistrez le mot de passe généré dans un emplacement sûr. Vous ne pourrez le récupérer une nouvelle fois.

> [!WARNING]
> La régénération d’un mot de passe déclenche une rotation des informations d’identification du jeton de synchronisation. Si vous avez configuré un appareil à l’aide du mot de passe précédent, vous devez mettre à jour la configuration.

[!INCLUDE [container-registry-connected-connection-configuration](../../includes/container-registry-connected-connection-configuration.md)]

## <a name="configure-a-deployment-manifest-for-iot-edge"></a>Configurer un manifeste de déploiement pour IoT Edge

Un manifeste de déploiement est un document JSON décrivant les modules à déployer sur un appareil IoT Edge. Pour plus d’informations, consultez [Comprendre comment les modules IoT Edge peuvent être utilisés, configurés et réutilisés](../iot-edge/module-composition.md).

Pour déployer le registre connecté et les modules de proxy d’API à l’aide de la commande Azure CLI, enregistrez le manifeste de déploiement suivant localement sous la forme d’un fichier `manifest.json`. Vous utiliserez le chemin du fichier dans la section suivante au moment d’exécuter la commande permettant d’appliquer la configuration à votre appareil.

[!INCLUDE [container-registry-connected-iot-edge-manifest](../../includes/container-registry-connected-iot-edge-manifest.md)]

## <a name="deploy-the-connected-registry-and-api-proxy-modules-on-iot-edge"></a>Déployer le registre connecté et les modules de proxy d’API sur IoT Edge

Utilisez la commande suivante pour déployer le registre connecté et les modules de proxy d’API sur l’appareil IoT Edge, à l’aide du manifeste de déploiement créé dans la section précédente. Indiquez l’ID du périphérique de couche supérieure IoT Edge et le nom de l’IoT Hub, le cas échéant.

```azurecli
# Set the IOT_EDGE_TOP_LAYER_DEVICE_ID and IOT_HUB_NAME environment variables for use in the following Azure CLI command
IOT_EDGE_TOP_LAYER_DEVICE_ID=<device-id>
IOT_HUB_NAME=<hub-name>

az iot edge set-modules \
  --device-id $IOT_EDGE_TOP_LAYER_DEVICE_ID \
  --hub-name $IOT_HUB_NAME \
  --content manifest.json
```

Pour plus d’informations, consultez [Déployer des modules Azure IoT Edge avec Azure CLI](../iot-edge/how-to-deploy-modules-cli.md).

Pour vérifier l’état du registre connecté, utilisez la commande [az acr connected-registry show][az-acr-connected-registry-show] suivante. Le nom du registre connecté est la valeur de *$CONNECTED_REGISTRY_RW*.

```azurecli
az acr connected-registry show \
  --registry $REGISTRY_NAME \
  --name $CONNECTED_REGISTRY_RW \
  --output table
```

Une fois le déploiement réussi, le registre connecté affiche l’état `Online`.

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez appris à déployer un registre connecté sur un appareil IoT Edge. Passez aux guides suivants pour apprendre à extraire des images du registre connecté nouvellement déployé ou à déployer le registre connecté sur des appareils IoT Edge imbriqués.


> [!div class="nextstepaction"]
> [Extraire des images à partir d’un registre connecté][pull-images-from-connected-registry]

> [!div class="nextstepaction"]
> [Didacticiel : Déployer un registre connecté sur des appareils IoT Edge imbriqués][tutorial-connected-registry-nested]

<!-- LINKS - internal -->
[az-acr-connected-registry-get-settings]: /cli/azure/acr/connected-registry/install#az_acr_connected_registry_get_settings
[az-acr-connected-registry-show]: /cli/azure/acr/connected-registr#az_acr_connected_registry_show
[az-acr-import]:/cli/azure/acr#az_acr_import
[az-acr-token-credential-generate]: /cli/azure/acr/token/credential?#az_acr_token_credential_generate
[container-registry-intro]: container-registry-intro.md
[pull-images-from-connected-registry]: pull-images-from-connected-registry.md
[quickstart-connected-registry-cli]: quickstart-connected-registry-cli.md
[quickstart-connected-registry-portal]: quickstart-connected-registry-portal.md
[tutorial-connected-registry-nested]: tutorial-deploy-connected-registry-nested-iot-edge-cli.md
