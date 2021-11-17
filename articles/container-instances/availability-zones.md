---
title: Déployer un groupe de conteneurs zonal dans Azure Container Instances (ACI)
description: Découvrez comment déployer un groupe de conteneurs dans une zone de disponibilité.
ms.topic: article
ms.date: 10/13/2021
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: a2cce289dc95ecc876ba308ed9406fffb37b9e18
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131858649"
---
# <a name="deploy-an-azure-container-instances-aci-container-group-in-an-availability-zone-preview"></a>Déployer un groupe de conteneurs Azure Container Instances (ACI) dans une zone de disponibilité (préversion)

Une [zone de disponibilité][availability-zone-overview] est une zone physiquement séparée dans une région Azure. Vous pouvez utiliser des zones de disponibilité pour protéger vos applications conteneurisées contre des défaillances ou pertes improbables d’un centre de données entier. Azure Container Instances (ACI) prend en charge les déploiements de groupes de conteneurs zonaux, ce qui signifie que l’instance est épinglée à une zone de disponibilité spécifique sélectionnée automatiquement. La zone de disponibilité est spécifiée au niveau du groupe de conteneurs. Les conteneurs au sein d’un groupe de conteneurs ne peuvent pas avoir de zones de disponibilité uniques. Pour modifier la zone de disponibilité de votre groupe de conteneurs, vous devez supprimer le groupe de conteneurs et en créer un autre avec la nouvelle zone de disponibilité.

> [!IMPORTANT]
> Actuellement, cette fonctionnalité est uniquement disponible en tant que version préliminaire. Les préversions sont à votre disposition, à condition que vous acceptiez les conditions d’utilisation supplémentaires.

> [!IMPORTANT]
> Les déploiements de groupes de conteneurs zonaux sont pris en charge dans la plupart des régions où ACI est disponible pour les groupes de conteneurs Linux et Windows Server 2019. Pour plus d’informations, consultez [Régions et disponibilité des ressources][container-regions].

> [!NOTE]
> Les exemples présentés dans cet article sont mis en forme pour l’interpréteur de commandes Bash. Si vous préférez un autre interpréteur de commandes, ajustez les caractères de continuation de ligne en conséquence.

## <a name="limitations"></a>Limites

> [!IMPORTANT]
> Cette fonctionnalité n’est actuellement pas disponible pour le portail Azure.

* Les groupes de conteneurs avec des ressources GPU ne prennent pas en charge les zones de disponibilité pour l’instant.
* Les groupes de conteneurs injectés de réseau virtuel ne prennent pas en charge les zones de disponibilité pour l’instant.
* Les groupes de conteneurs Windows Server 2016 ne prennent pas en charge les zones de disponibilité pour l’instant.

### <a name="version-requirements"></a>Configuration requise pour la version

* Si vous utilisez Azure CLI, assurez-vous que la version `2.30.0` ou une version ultérieure est installée.
* Si vous utilisez PowerShell, assurez-vous que la version `2.1.1-preview` ou une version ultérieure est installée.
* Si vous utilisez le Kit de développement logiciel (SDK) Java, assurez-vous que la version `2.9.0` ou une version ultérieure est installée.
* La prise en charge des zones de disponibilité n’est disponible uniquement sur l’API ACI version `09-01-2021` ou ultérieure.

## <a name="deploy-a-container-group-using-an-azure-resource-manager-arm-template"></a>Déployer un groupe de conteneurs à l’aide d’un modèle Azure Resource Manager (ARM)

### <a name="create-the-arm-template"></a>Créer le modèle ARM

Commencez par copier le JSON suivant dans un nouveau fichier nommé `azuredeploy.json`. Cet exemple de modèle déploie un groupe de conteneurs avec un conteneur unique dans la zone de disponibilité 1 au sein de la région USA Est.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "metadata": {
        "_generator": {
            "name": "bicep",
            "version": "0.4.1.14562",
            "templateHash": "12367894147709986470"
        }
    },
    "parameters": {
        "name": {
            "type": "string",
            "defaultValue": "acilinuxpublicipcontainergroup",
            "metadata": {
                "description": "Name for the container group"
            }
        },
        "image": {
            "type": "string",
            "defaultValue": "mcr.microsoft.com/azuredocs/aci-helloworld",
            "metadata": {
                "description": "Container image to deploy. Should be of the form repoName/imagename:tag for images stored in public Docker Hub, or a fully qualified URI for other registries. Images from private registries require additional registry credentials."
            }
        },
        "port": {
            "type": "int",
            "defaultValue": 80,
            "metadata": {
                "description": "Port to open on the container and the public IP address."
            }
        },
        "cpuCores": {
            "type": "int",
            "defaultValue": 1,
            "metadata": {
                "description": "The number of CPU cores to allocate to the container."
            }
        },
        "memoryInGb": {
            "type": "int",
            "defaultValue": 2,
            "metadata": {
                "description": "The amount of memory to allocate to the container in gigabytes."
            }
        },
        "restartPolicy": {
            "type": "string",
            "defaultValue": "Always",
            "allowedValues": [
                "Always",
                "Never",
                "OnFailure"
            ],
            "metadata": {
                "description": "The behavior of Azure runtime if container has stopped."
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "eastus",
            "metadata": {
                "description": "Location for all resources."
            }
        }
    },
    "functions": [],
    "resources": [
        {
            "type": "Microsoft.ContainerInstance/containerGroups",
            "apiVersion": "2021-09-01",
            "zones": [
                "1"
            ],
            "name": "[parameters('name')]",
            "location": "[parameters('location')]",
            "properties": {
                "containers": [
                    {
                        "name": "[parameters('name')]",
                        "properties": {
                            "image": "[parameters('image')]",
                            "ports": [
                                {
                                    "port": "[parameters('port')]",
                                    "protocol": "TCP"
                                }
                            ],
                            "resources": {
                                "requests": {
                                    "cpu": "[parameters('cpuCores')]",
                                    "memoryInGB": "[parameters('memoryInGb')]"
                                }
                            }
                        }
                    }
                ],
                "osType": "Linux",
                "restartPolicy": "[parameters('restartPolicy')]",
                "ipAddress": {
                    "type": "Public",
                    "ports": [
                        {
                            "port": "[parameters('port')]",
                            "protocol": "TCP"
                        }
                    ]
                }
            }
        }
    ],
    "outputs": {
        "containerIPv4Address": {
            "type": "string",
            "value": "[reference(resourceId('Microsoft.ContainerInstance/containerGroups', parameters('name'))).ipAddress.ip]"
        }
    }
}
```

### <a name="deploy-the-arm-template"></a>Déployer le modèle ARM

Créez un groupe de ressources avec la commande [az group create][az-group-create] :

```azurecli
az group create --name myResourceGroup --location eastus
```

Déployez le modèle avec la commande [az deployment group create][az-deployment-group-create] :

```azurecli
az deployment group create \
  --resource-group myResourceGroup \
  --template-file azuredeploy.json
```

## <a name="get-container-group-details"></a>Obtenir les détails d’un groupe de conteneurs

Pour vérifier que le groupe de conteneurs a été déployé correctement dans une zone de disponibilité, affichez les détails du groupe de conteneurs à l’aide de la commande [az container show][az-container-show] :

```azurecli
az containershow --name acilinuxcontainergroup --resource-group myResourceGroup
```

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az_container_create
[container-regions]: container-instances-region-availability.md
[az-container-show]: /cli/azure/container#az_container_show
[az-group-create]: /cli/azure/group#az_group_create
[az-deployment-group-create]: /cli/azure/deployment#az_deployment_group_create
[availability-zone-overview]: /availability-zones/az-overview.md