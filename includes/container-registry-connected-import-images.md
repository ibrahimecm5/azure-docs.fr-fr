---
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 09/16/2021
ms.author: danlep
ms.openlocfilehash: a5bfee2b0348a68476855edc2b113338b779f20d
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131028968"
---
## <a name="import-images-to-your-cloud-registry"></a>Importer des images dans votre registre cloud

Importez les images conteneur suivantes dans votre registre cloud à l’aide de la commande [az acr import](/cli/azure/acr#az_acr_import). Si vous avez déjà importé ces images, ignorez cette étape.

### <a name="connected-registry-image"></a>Image de registre connecté

Pour prendre en charge des scénarios IoT Edge imbriqués, l’image de conteneur pour le runtime du registre connecté doit être disponible dans votre registre de conteneurs Azure privé. Utilisez la commande [az acr import](/cli/azure/acr#az_acr_import) pour importer l’image du registre connecté dans votre registre privé. 

```azurecli
# Use the REGISTRY_NAME variable in the following Azure CLI commands to identify the registry
REGISTRY_NAME=<container-registry-name>

az acr import \
  --name $REGISTRY_NAME \
  --source mcr.microsoft.com/acr/connected-registry:0.5.0
```

### <a name="iot-edge-and-api-proxy-images"></a>Images d’IoT Edge et de proxy d’API

Pour prendre en charge le registre connecté sur un IoT Edge imbriqué, vous devez déployer des modules pour l’IoT Edge et le proxy d’API. Importez ces images dans votre registre privé.

Le [module proxy d’API IoT Edge](../articles/iot-edge/how-to-configure-api-proxy-module.md) permet à un appareil IoT Edge d’exposer plusieurs services à l’aide du protocole HTTPS sur le même port, tel que 443.

```azurecli
az acr import \
  --name $REGISTRY_NAME \
  --source mcr.microsoft.com/azureiotedge-agent:1.2.4

az acr import \
  --name $REGISTRY_NAME \
  --source mcr.microsoft.com/azureiotedge-hub:1.2.4

az acr import \
  --name $REGISTRY_NAME \
  --source mcr.microsoft.com/azureiotedge-api-proxy:1.1.2

az acr import \
  --name $REGISTRY_NAME \
  --source mcr.microsoft.com/azureiotedge-diagnostics:1.2.4
```

### <a name="hello-world-image"></a>Image Hello-World

Pour tester le registre connecté, importez l’image `hello-world`. Ce référentiel sera synchronisé sur le registre connecté et extrait par les clients du registre connecté.

```azurecli
az acr import \
  --name $REGISTRY_NAME \
  --source mcr.microsoft.com/hello-world:1.1.2
```