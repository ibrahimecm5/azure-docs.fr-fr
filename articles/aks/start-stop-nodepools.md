---
title: Démarrer et arrêter un pool de nœuds sur Azure Kubernetes Service (AKS) (préversion)
description: Découvrez comment démarrer ou arrêter un pool de nœuds sur Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 10/25/2021
author: qpetraroia
ms.author: qpetraroia
ms.openlocfilehash: 4bf2eeda7b13d520bca769e9698e4ea5abebd584
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131097558"
---
# <a name="start-and-stop-an-azure-kubernetes-service-aks-node-pool-preview"></a>Démarrer et arrêter un pool de nœuds Azure Kubernetes Service (AKS) (préversion)

Vos charges de travail AKS n’ont peut-être pas besoin de s’exécuter en continu. C’est le cas par exemple d’un cluster de développement qui contient des pools de nœuds exécutant des charges de travail spécifiques. Pour réaliser des économies sur les coûts de calcul, vous pouvez complètement désactiver (arrêter) vos pools de nœuds dans votre cluster AKS.

## <a name="before-you-begin"></a>Avant de commencer

Cet article suppose que vous avez un cluster AKS existant. Si vous avez besoin d’un cluster AKS, consultez le guide de démarrage rapide d’AKS [avec l’interface Azure CLI][aks-quickstart-cli], [avec Azure PowerShell][kubernetes-walkthrough-powershell] ou [avec le portail Azure][aks-quickstart-portal].

### <a name="install-aks-preview-cli-extension"></a>Installer l’extension CLI de préversion d’aks

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

Vous avez également besoin de l’extension Azure CLI *aks-preview*. Installez l’extension d’Azure CLI *aks-preview* à l’aide de la commande [az extension add][az-extension-add]. Ou installez toutes les mises à jour disponibles à l’aide de la commande [az extension update][az-extension-update].

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-the-previewstartstopagentpool-preview-feature"></a>Inscrire la fonctionnalité d’évaluation `PreviewStartStopAgentPool`

Pour utiliser la fonctionnalité, vous devez activer l’indicateur de fonctionnalité `PreviewStartStopAgentPool` sur votre abonnement.

Inscrivez l’indicateur de fonctionnalité `PreviewStartStopAgentPool` à l’aide de la commande [az feature register][az-feature-register], comme indiqué dans l’exemple suivant :

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "PreviewStartStopAgentPool"
```

Quelques minutes sont nécessaires pour que l’état s’affiche *Registered* (Inscrit). Vérifiez l’état de l’inscription à l’aide de la commande [az feature list][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/PreviewStartStopAgentPool')].{Name:name,State:properties.state}"
```

Lorsque vous êtes prêt, actualisez l’inscription du fournisseur de ressources *Microsoft.ContainerService* à l’aide de la commande [az provider register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="stop-an-aks-node-pool"></a>Arrêter un pool de nœuds AKS

> [!IMPORTANT]
> Le comportement de la fonctionnalité de démarrage et d’arrêt d’un pool de nœuds est le suivant :
>
> * Vous ne pouvez pas arrêter les pools système.
> * Les pools de nœuds spot sont pris en charge.
> * Les pools de nœuds arrêtés peuvent être mis à niveau.
> * Le cluster et le pool de nœuds doivent être en cours d’exécution.

Utilisez `az aks nodepool stop` pour arrêter un pool de nœuds AKS en cours d’exécution. L’exemple suivant arrête le pool de nœuds *testnodepool* :

```azurecli-interactive
az aks nodepool stop --nodepool-name testnodepool --resource-group myResourceGroup --cluster-name myAKSCluster
```

Vous pouvez vérifier à quel moment votre pool de nœuds est arrêté en utilisant la commande [az aks show][az-aks-show] et en confirmant que `powerState` indique `Stopped`, comme dans la sortie ci-dessous :

```json
{
[...]
 "osType": "Linux",
    "podSubnetId": null,
    "powerState": {
        "code&quot;: &quot;Stopped"
        },
    "provisioningState": "Succeeded",
    "proximityPlacementGroupId": null,
[...]
}
```

> [!NOTE]
> Si `provisioningState` indique `Stopping`, votre pool de nœuds n’a pas encore été complètement arrêté.

## <a name="start-a-stopped-aks-node-pool"></a>Démarrer un pool de nœuds AKS arrêté

Utilisez `az aks nodepool start` pour démarrer un pool de nœuds AKS arrêté. L’exemple suivant démarre le pool de nœuds arrêté nommé *testnodepool* :

```azurecli-interactive
az aks nodepool start --nodepool-name testnodepool --resource-group myResourceGroup --cluster-name myAKSCluster
```

Vous pouvez vérifier que votre pool de nœuds a démarré en utilisant [az aks show][az-aks-show] et en confirmant que `powerState` indique `Running`. Par exemple :

```json
{
[...]
 "osType": "Linux",
    "podSubnetId": null,
    "powerState": {
        "code&quot;: &quot;Running"
        },
    "provisioningState": "Succeeded",
    "proximityPlacementGroupId": null,
[...]
}
```

> [!NOTE]
> Si `provisioningState` indique `Starting`, votre pool de nœuds n’a pas encore complètement démarré.

---

## <a name="next-steps"></a>Étapes suivantes

- Pour savoir comment mettre à l’échelle les pools `User` à 0, consultez [Mettre à l’échelle les pools `User` à 0](scale-cluster.md#scale-user-node-pools-to-0).
- Pour savoir comment arrêter votre cluster, consultez [Démarrer et arrêter un cluster](start-stop-cluster.md).
- Pour savoir comment réduire les coûts à l’aide d’instances Spot, consultez [Ajouter un pool de nœuds spot à AKS](spot-node-pool.md).
- Pour en savoir plus sur les stratégies de prise en charge d’AKS, consultez [Stratégies de prise en charge d’AKS](support-policies.md).

<!-- LINKS - external -->

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[az-aks-show]: /cli/azure/aks#az_aks_show
[kubernetes-walkthrough-powershell]: kubernetes-walkthrough-powershell.md
[stop-azakscluster]: /powershell/module/az.aks/stop-azakscluster
[get-azakscluster]: /powershell/module/az.aks/get-azakscluster
[start-azakscluster]: /powershell/module/az.aks/start-azakscluster