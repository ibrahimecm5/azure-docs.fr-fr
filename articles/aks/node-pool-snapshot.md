---
title: Instantané des pools de nœuds Azure Kubernetes Service (AKS) (préversion)
description: Découvrez comment effectuer un instantané des pools de nœuds de cluster AKS, et comment créer des clusters et des pools de nœuds à partir d’un instantané.
ms.service: container-service
ms.topic: article
ms.date: 09/11/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: b547b65fc3f3086e90da073fb898de3980938147
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131858605"
---
# <a name="azure-kubernetes-service-aks-node-pool-snapshot-preview"></a>Instantané des pools de nœuds Azure Kubernetes Service (AKS) (préversion)

AKS publie une nouvelle image de nœud chaque semaine et chaque nouveau cluster, nouveau pool de nœuds ou cluster de mise à niveau reçoit toujours l’image la plus récente, ce qui peut rendre difficile la gestion de vos environnements et la possibilité d’avoir des environnements reproductibles.

Les instantanés de pool de nœuds vous permettent de prendre un instantané de la configuration de votre pool de nœuds, puis de créer des pools de nœuds ou de nouveaux clusters basés sur cet instantané tant que cette configuration et la version de Kubernetes sont prises en charge. Pour plus d’informations sur les fenêtres de prise en charge, consultez [Versions de Kubernetes prises en charge dans AKS][supported-versions].

L’instantané est une ressource Azure qui contient les informations de configuration du pool de nœuds source, comme la version de l’image de nœud, la version de Kubernetes, le type de système d’exploitation et la référence SKU. Vous pouvez ensuite référencer cette ressource d’instantané et les valeurs respectives de sa configuration pour créer un pool de nœuds ou un cluster basé sur celui-ci.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="before-you-begin"></a>Avant de commencer

Cet article suppose que vous avez un cluster AKS existant. Si vous avez besoin d’un cluster AKS, consultez le guide de démarrage rapide d’AKS [avec Azure CLI][aks-quickstart-cli]ou avec le [Portail Azure][aks-quickstart-portal].

### <a name="limitations"></a>Limites

- Tout pool de nœuds ou cluster créé à partir d’un instantané doit utiliser une machine virtuelle de la même famille de machines virtuelles que l’instantané. Par exemple, vous ne pouvez pas créer un nouveau pool de nœuds de série N basé sur un instantané capturé à partir d’un pool de nœuds de la série D, car les images de nœud dans ces cas sont structurellement différentes.
- Pendant la préversion, les instantanés doivent être créés et utilisés dans la même région que le pool de nœuds source.

### <a name="install-aks-preview-cli-extension"></a>Installer l’extension CLI de préversion d’aks

Vous devez également disposer de l’extension Azure CLI *aks-preview* version 0.5.40 ou ultérieure. Installez l’extension d’Azure CLI *aks-preview* à l’aide de la commande [az extension add][az-extension-add]. Ou installez toutes les mises à jour disponibles à l’aide de la commande [az extension update][az-extension-update].

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-the-snapshotpreview-preview-feature"></a>Inscrire la fonctionnalité d’évaluation `SnapshotPreview`

Pour utiliser la fonctionnalité, vous devez activer l’indicateur de fonctionnalité `SnapshotPreview` sur votre abonnement.

Inscrivez l’indicateur de fonctionnalité `SnapshotPreview` à l’aide de la commande [az feature register][az-feature-register], comme indiqué dans l’exemple suivant :

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "SnapshotPreview"
```

Quelques minutes sont nécessaires pour que l’état s’affiche *Registered* (Inscrit). Vérifiez l’état de l’inscription à l’aide de la commande [az feature list][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'microsoft.ContainerService/SnapshotPreview')].{Name:name,State:properties.state}"
```

Lorsque vous êtes prêt, actualisez l’inscription du fournisseur de ressources *Microsoft.ContainerService* à l’aide de la commande [az provider register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="take-a-node-pool-snapshot"></a>Prendre un instantané d’un pool de nœuds

Pour prendre un instantané d’un pool de nœuds, vous avez d’abord besoin de l’ID de la ressource du pool de nœuds, que vous pouvez obtenir à partir de la commande ci-dessous :

```azurecli-interactive
NODEPOOL_ID=$(az aks nodepool show --name nodepool1 --cluster-name myAKSCluster --resource-group myResourceGroup --query id -o tsv)
```

> [!IMPORTANT]
> Votre pool de nœuds AKS doit être créé ou mis à niveau après le 10 novembre 2021 pour qu’un instantané puisse y être pris.

Pour prendre un instantané du pool de nœuds précédent, vous allez maintenant utiliser la commande de l’interface CLI `az aks snapshot`.

```azurecli-interactive
az aks snapshot create --name MySnapshot --resource-group MyResourceGroup --nodepool-id $NODEPOOL_ID --location eastus
```

## <a name="create-a-node-pool-from-a-snapshot"></a>Créer un pool de nœuds à partir d’un instantané

Vous avez tout d’abord besoin de l’ID de la ressource de l’instantané qui a été créé précédemment, que vous pouvez obtenir à partir de la commande ci-dessous :

```azurecli-interactive
SNAPSHOT_ID=$(az aks snapshot show --name MySnapshot --resource-group myResourceGroup --query id -o tsv)
```

Nous pouvons maintenant utiliser la commande ci-dessous pour ajouter un nouveau pool de nœuds basé sur cet instantané.

```azurecli-interactive
az aks nodepool add --name np2 --cluster-name myAKSCluster --resource-group myResourceGroup --snapshot-id $SNAPSHOT_ID
```

## <a name="upgrading-a-node-pool-to-a-snapshot"></a>Mise à niveau d’un pool de nœuds vers un instantané

Vous pouvez mettre à niveau un pool de nœuds vers une configuration d’instantané tant que la version de l’instantané Kubernetes et la version de l’image de nœud sont plus récentes que les versions du pool de nœuds actuel.

Vous avez tout d’abord besoin de l’ID de la ressource de l’instantané qui a été créé précédemment, que vous pouvez obtenir à partir de la commande ci-dessous :

```azurecli-interactive
SNAPSHOT_ID=$(az aks snapshot show --name MySnapshot --resource-group myResourceGroup --query id -o tsv)
```

Nous pouvons maintenant utiliser cette commande pour mettre à niveau ce pool de nœuds vers cette configuration de l’instantané.

```azurecli-interactive
az aks nodepool upgrade --name nodepool1 --cluster-name myAKSCluster --resource-group myResourceGroup --snapshot-id $SNAPSHOT_ID
```

> [!NOTE]
> La version de l’image de votre pool de nœuds est la même que celle contenue dans l’instantané et reste inchangée dans toutes les opérations de mise à l’échelle. Toutefois, si ce pool de nœuds est mis à niveau ou si une mise à niveau d’image de nœud est effectuée sans fournir d’ID d’instantané, l’image de nœud est mise à niveau vers la dernière version.

## <a name="create-a-cluster-from-a-snapshot"></a>Créer un cluster à partir d’un instantané

Quand vous créez un cluster à partir d’un instantané, le pool système d’origine du cluster est créé à partir de la configuration de l’instantané.

Vous avez tout d’abord besoin de l’ID de la ressource de l’instantané qui a été créé précédemment, que vous pouvez obtenir à partir de la commande ci-dessous :

```azurecli-interactive
SNAPSHOT_ID=$(az aks snapshot show --name MySnapshot --resource-group myResourceGroup --query id -o tsv)
```

Nous pouvons maintenant utiliser cette commande pour créer ce cluster à partir de la configuration de l’instantané.

```azurecli-interactive
az aks cluster create --name myAKSCluster2 --resource-group myResourceGroup --snapshot-id $SNAPSHOT_ID
```

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur les images de nœud les plus récentes, consultez les [notes de publication AKS](https://github.com/Azure/AKS/releases).
- Apprenez à mettre à niveau la version de Kubernetes avec la section [Mettre à niveau un cluster AKS][upgrade-cluster].
- Découvrez comment mettre à niveau votre version d’image de nœud avec [Mise à niveau d’image de nœud][node-image-upgrade]
- En savoir plus sur les pools de nœuds multiples et sur la mise à niveau des pools de nœuds avec la section [Créer et gérer les pools de nœuds multiples][use-multiple-node-pools].

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[supported-versions]: supported-kubernetes-versions.md
[upgrade-cluster]: upgrade-cluster.md
[node-image-upgrade]: node-image-upgrade.md
[github-schedule]: node-upgrade-github-actions.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[max-surge]: upgrade-cluster.md#customize-node-surge-upgrade
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-aks-install-cli]: /cli/azure/aks#az_aks_install_cli
[az-provider-register]: /cli/azure/provider#az_provider_register