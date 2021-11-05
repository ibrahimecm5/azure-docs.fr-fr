---
title: 'Démarrage rapide : Créer un registre connecté à l’aide de l’interface CLI'
description: Utilisez les commandes Azure CLI pour créer une ressource de registre de conteneurs Azure connectée qui peut synchroniser des images et d’autres artefacts avec le registre cloud.
ms.topic: quickstart
ms.date: 10/21/2021
ms.author: memladen
author: toddysm
ms.custom: ignite-fall-2021
ms.openlocfilehash: 926d9ca087f509a1aa76dcec235fd7996bce4a78
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131098162"
---
# <a name="quickstart-create-a-connected-registry-using-the-azure-cli"></a>Démarrage rapide : Créer un registre connecté à l’aide d’Azure CLI

Dans ce guide de démarrage rapide, vous allez utiliser Azure CLI pour créer une ressource de [registre connecté](intro-connected-registry.md) dans Azure. La fonctionnalité de registre connecté d’Azure Container Registry vous permet de déployer un registre à distance ou localement, et de synchroniser des images et d’autres artefacts avec le registre cloud. 

Ici, vous créez deux ressources de registre connecté pour un registre cloud : un registre connecté autorise les fonctionnalités en lecture et écriture (pull et push d’artefacts) et l’autre autorise les fonctionnalités en lecture seule. 

Après avoir créé un registre connecté, vous pouvez suivre d’autres guides pour le déployer et l’utiliser sur votre infrastructure locale ou distante.

[!INCLUDE [Prepare Azure CLI environment](../../includes/azure-cli-prepare-your-environment.md)]

* Registre de conteneurs Azure : si vous n’avez pas encore de registre de conteneurs, [créez-en un](container-registry-get-started-azure-cli.md) (niveau Premium requis) dans une [région](intro-connected-registry.md#available-regions) qui prend en charge les registres connectés. 

## <a name="enable-the-dedicated-data-endpoint-for-the-cloud-registry"></a>Activer le point de terminaison de données dédié pour le registre cloud

Activez le point de terminaison de données dédié pour le registre de conteneurs Azure dans le cloud à l’aide de la commande [az acr update][az-acr-update]. Cette étape est nécessaire pour qu’un registre connecté communique avec le registre cloud.

```azurecli
# Set the REGISTRY_NAME environment variable to identify the existing cloud registry
REGISTRY_NAME=<container-registry-name>

az acr update --name $REGISTRY_NAME \
  --data-endpoint-enabled
```

[!INCLUDE [container-registry-connected-import-images](../../includes/container-registry-connected-import-images.md)]

## <a name="create-a-connected-registry-resource-for-read-and-write-functionality"></a>Créer une ressource de registre connecté pour les fonctionnalités en lecture et écriture

Créez un registre connecté à l’aide de la commande [az acr connected-registry create][az-acr-connected-registry-create]. Le nom du registre connecté doit commencer par une lettre et contenir uniquement des caractères alphanumériques. Il doit comprendre de 5 à 40 caractères, et être unique dans la hiérarchie de ce registre de conteneurs Azure.

```azurecli
# Set the CONNECTED_REGISTRY_RW environment variable to provide a name for the connected registry with read/write functionality
CONNECTED_REGISTRY_RW=<connnected-registry-name>

az acr connected-registry create --registry $REGISTRY_NAME \
  --name $CONNECTED_REGISTRY_RW \
  --repository "hello-world" "acr/connected-registry" "azureiotedge-agent" "azureiotedge-hub" "azureiotedge-api-proxy"
```

Cette commande crée une ressource de registre connecté dont le nom est la valeur de *$CONNECTED_REGISTRY_RW* et la lie au registre cloud dont le nom est la valeur de *$REGISTRY_NAME*. Dans les guides de démarrage rapide ultérieurs, vous en apprendrez davantage sur les options de déploiement du registre connecté. 
* Les référentiels spécifiés seront synchronisés entre le registre cloud et le registre connecté une fois celui-ci déployé. 
* Comme aucune option `--mode` n’est spécifiée pour le registre connecté, elle est créée dans le [mode ReadWrite](intro-connected-registry.md#modes) par défaut. 
* Étant donné qu’aucune planification de synchronisation n’est définie pour ce registre connecté, les référentiels sont synchronisés sans interruption entre le registre cloud et le registre connecté.

  > [!IMPORTANT]
  > Pour prendre en charge des scénarios imbriqués où des couches inférieures n’ont pas accès à Internet, vous devez toujours autoriser la synchronisation du référentiel `acr/connected-registry`. Ce référentiel contient l’image du runtime du registre connecté.

## <a name="create-a-connected-registry-resource-for-read-only-functionality"></a>Créer une ressource de registre connecté pour les fonctionnalités en lecture seule

Vous pouvez également utiliser la commande [az acr connected-registry create][az-acr-connected-registry-create] pour créer un registre connecté fonctionnant en lecture seule. 

```azurecli
# Set the CONNECTED_REGISTRY_READ environment variable to provide a name for the connected registry with read-only functionality
CONNECTED_REGISTRY_RO=<connnected-registry-name>
az acr connected-registry create --registry $REGISTRY_NAME \
  --parent $CONNECTED_REGISTRY_RW \
  --name $CONNECTED_REGISTRY_RO \
  --repository "hello-world" "acr/connected-registry" "azureiotedge-agent" "azureiotedge-hub" "azureiotedge-api-proxy" \
  --mode ReadOnly
```

Cette commande crée une ressource de registre connecté dont le nom est la valeur de *$CONNECTED_REGISTRY_RO* et la lie au registre cloud dont le nom est la valeur de *$REGISTRY_NAME*. 
* Les référentiels spécifiés seront synchronisés entre le registre parent nommé avec la valeur de *$CONNECTED _REGISTRY_RW* et le registre connecté une fois déployé.
* Cette ressource est créée en [mode lecture seule](intro-connected-registry.md#modes), ce qui permet d’activer la fonctionnalité de lecture seule (pull d’artefact) une fois déployée. 
* Étant donné qu’aucune planification de synchronisation n’est définie pour ce registre connecté, les référentiels sont synchronisés sans interruption entre le registre parent et le registre connecté.

## <a name="verify-that-the-resources-are-created"></a>Vérifier que les ressources ont été créées

Vous pouvez utiliser la commande [az acr connected-registry list][az-acr-connected-registry-list] du registre connecté pour vérifier que les ressources sont créées. 

```azurecli
az acr connected-registry list \
  --registry $REGISTRY_NAME \
  --output table
```

Vous devez voir la réponse suivante. Étant donné que les registres connectés ne sont pas encore déployés, l’état de connexion « Hors connexion » indique qu’ils sont actuellement déconnectés du cloud.

```
NAME                 MODE        CONNECTION STATE    PARENT               LOGIN SERVER    LAST SYNC (UTC)
-------------------  --------    ------------------  -------------------  --------------  -----------------
myconnectedregrw    ReadWrite    Offline
myconnectedregro    ReadOnly     Offline             myconnectedregrw
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez utilisé Azure CLI pour créer deux ressources de registre connecté dans Azure. Ces nouvelles ressources de registre connecté sont liées à votre registre cloud et permettent la synchronisation d’artefacts avec le registre cloud.

Passez aux guides de déploiement de registre connecté pour savoir comment déployer et utiliser un registre connecté sur votre infrastructure de IoT Edge.

> [!div class="nextstepaction"]
> [Démarrage rapide : Déployer un registre connecté sur IoT Edge][quickstart-deploy-connected-registry-iot-edge-cli]

<!-- LINKS - internal -->
[az-acr-connected-registry-create]: /cli/azure/acr/connected-registry#az_acr_connected_registry_create
[az-acr-connected-registry-list]: /cli/azure/acr/connected-registry#az_acr_connected_registry_list
[az-acr-create]: /cli/azure/acr#az_acr_create
[az-acr-update]: /cli/azure/acr#az_acr_update
[az-acr-import]: /cli/azure/acr#az_acr_import
[az-group-create]: /cli/azure/group#az_group_create
[container-registry-intro]: container-registry-intro.md
[container-registry-skus]: container-registry-skus.md
[quickstart-deploy-connected-registry-iot-edge-cli]: quickstart-deploy-connected-registry-iot-edge-cli.md
