---
title: Démarrer et arrêter Azure Kubernetes Service (AKS)
description: Découvrez comment arrêter ou démarrer un cluster Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 08/09/2021
author: palma21
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 8a19cc69d14e32a20819618efcf60594aef34be9
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132371977"
---
# <a name="stop-and-start-an-azure-kubernetes-service-aks-cluster"></a>Arrêter et démarrer un cluster Azure Kubernetes Service (AKS)

Il se peut que vos charges de travail AKS n’aient pas besoin de s’exécuter en continu, par exemple un cluster de développement utilisé uniquement pendant les heures de bureau. Cela se traduit par des heures où votre cluster Azure Kubernetes Service (AKS) peut être inactif et n’exécute plus que les composants système. Vous pouvez réduire l’encombrement du cluster en [mettant à l’échelle tous les pools de nœud `User` sur 0](scale-cluster.md#scale-user-node-pools-to-0), mais votre [pool `System`](use-system-pools.md) est toujours requis pour exécuter les composants système pendant que le cluster est en cours d’exécution.
Pour optimiser davantage vos coûts pendant ces périodes, vous pouvez complètement désactiver (arrêter) votre cluster. Cette action arrête complètement le plan de contrôle et les nœuds de l’agent, ce qui vous permet d’économiser tous les coûts de calcul, tout en conservant l’ensemble de vos objets (à l’exception des pods autonomes) et votre état de cluster pour le redémarrage. Vous pouvez ensuite reprendre là où vous aviez laissé les choses après un week-end, ou faire en sorte que votre cluster s’exécute uniquement lorsque vous exécutez vos programmes de traitement par lots.

## <a name="before-you-begin"></a>Avant de commencer

Cet article suppose que vous avez un cluster AKS existant. Si vous avez besoin d’un cluster AKS, consultez le guide de démarrage rapide d’AKS [avec l’interface Azure CLI][aks-quickstart-cli], [avec Azure PowerShell][kubernetes-walkthrough-powershell] ou [avec le portail Azure][aks-quickstart-portal].

### <a name="limitations"></a>Limites

Lorsque vous utilisez la fonctionnalité de démarrage/arrêt du cluster, les restrictions suivantes s’appliquent :

- Cette fonctionnalité est uniquement prise en charge pour les clusters supportés par des groupes de machines virtuelles identiques.
- L’état du cluster d’un cluster AKS arrêté est conservé pendant 12 mois au maximum. Si votre cluster est arrêté pendant plus de 12 mois, l’état du cluster ne peut pas être récupéré. Pour plus d’informations, consultez les [Stratégies de support pour AKS](support-policies.md).
- Vous pouvez uniquement démarrer ou supprimer un cluster AKS arrêté. Pour effectuer une opération telle qu’une mise à l’échelle ou une mise à niveau, commencez par démarrer votre cluster.
- Les PrivateEndpoints provisionnés par le client qui sont liés à un cluster privé doivent être supprimés et recréés quand vous démarrez un cluster AKS arrêté.
- Étant donné que le processus d’arrêt draine tous les nœuds, tous les pod autonomes (c’est-à-dire les pods non gérés par un Deployment, StatefulSet, DaemonSet, Job, etc.) seront supprimés.

## <a name="stop-an-aks-cluster"></a>Arrêter un cluster AKS

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Vous pouvez utiliser la commande `az aks stop` pour arrêter l’exécution des nœuds et du plan de contrôle d’un cluster AKS. L’exemple suivant arrête un cluster nommé *myAKSCluster* :

```azurecli-interactive
az aks stop --name myAKSCluster --resource-group myResourceGroup
```

Vous pouvez vérifier à quel moment votre cluster est arrêté à l’aide de la commande[az aks show][az-aks-show] et en confirmant que `powerState` indique `Stopped` comme dans la sortie ci-dessous :

```json
{
[...]
  "nodeResourceGroup": "MC_myResourceGroup_myAKSCluster_westus2",
  "powerState":{
    "code":"Stopped"
  },
  "privateFqdn": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
[...]
}
```

Si `provisioningState` affiche `Stopping`, cela signifie que votre cluster n’a pas encore été complètement arrêté.

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Vous pouvez utiliser l’applet de commande [Stop-AzAksCluster][stop-azakscluster] pour arrêter les nœuds et le plan de contrôle d’un cluster AKS en cours d’exécution. L’exemple suivant arrête un cluster nommé *myAKSCluster* :

```azurepowershell-interactive
Stop-AzAksCluster -Name myAKSCluster -ResourceGroupName myResourceGroup
```

Vous pouvez vérifier que votre cluster est arrêté à l’aide de l’applet de commande[Get-AzAksCluster][get-azakscluster] et en confirmant que `ProvisioningState` a pour valeur `Stopped` comme dans la sortie ci-dessous :

```Output
ProvisioningState       : Stopped
MaxAgentPools           : 100
KubernetesVersion       : 1.20.7
...
```

Si `ProvisioningState` affiche `Stopping`, cela signifie que votre cluster n’a pas encore été complètement arrêté.

---

> [!IMPORTANT]
> Si vous utilisez des [budgets d’interruption de pod](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/), l’opération d’arrêt peut prendre plus de temps, car le processus de drainage prendra plus de temps.

## <a name="start-an-aks-cluster"></a>Démarrer un cluster AKS

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Vous pouvez utiliser la commande `az aks start` pour démarrer les nœuds et le plan de contrôle d’un cluster AKS arrêté. Le cluster est redémarré avec l’état du plan de contrôle et le nombre de nœuds d’agent précédents.
L’exemple suivant démarre un cluster nommé *myAKSCluster* :

```azurecli-interactive
az aks start --name myAKSCluster --resource-group myResourceGroup
```

Vous pouvez vérifier à quel moment votre cluster a démarré à l’aide de la commande [az aks show][az-aks-show] et en confirmant que `powerState` indique `Running` comme dans la sortie ci-dessous :

```json
{
[...]
  "nodeResourceGroup": "MC_myResourceGroup_myAKSCluster_westus2",
  "powerState":{
    "code":"Running"
  },
  "privateFqdn": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
[...]
}
```

Si `provisioningState` affiche `Starting`, cela signifie que votre cluster n’a pas encore été complètement démarré.

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Vous pouvez utiliser l’applet de commande [Start-AzAksCluster][start-azakscluster] pour démarrer les nœuds et le plan de contrôle d’un cluster AKS arrêté. Le cluster est redémarré avec l’état du plan de contrôle et le nombre de nœuds d’agent précédents.
L’exemple suivant démarre un cluster nommé *myAKSCluster* :

```azurepowershell-interactive
Start-AzAksCluster -Name myAKSCluster -ResourceGroupName myResourceGroup
```

Vous pouvez vérifier à quel moment votre cluster a démarré à l’aide de l’applet de commande [Get-AzAksCluster][get-azakscluster] et en confirmant que `ProvisioningState` a pour valeur `Succeeded` comme dans la sortie suivante :

```Output
ProvisioningState       : Succeeded
MaxAgentPools           : 100
KubernetesVersion       : 1.20.7
...
```

Si `ProvisioningState` affiche `Starting`, cela signifie que votre cluster n’a pas encore été complètement démarré.

---

> [!NOTE]
> Lorsque vous démarrez la sauvegarde de votre cluster, le comportement suivant est attendu :
>
> * L’adresse IP de votre serveur d’API peut changer.
> * Si vous utilisez le programme de mise à l’échelle automatique de cluster, quand vous redémarrez votre cluster, le nombre de nœuds actuel peut ne pas être compris entre les valeurs de plage minimale et maximale que vous avez définies. Le cluster démarre avec le nombre de nœuds dont il a besoin pour exécuter ses charges de travail, et ce nombre n’est pas impacté par les paramètres de votre programme de mise à l’échelle automatique. Lorsque votre cluster effectue des opérations de mise à l’échelle, les valeurs minimale et maximale ont un impact sur votre nombre de nœuds actuel, et le cluster finit par atteindre et rester dans la plage souhaitée, jusqu’à ce que vous l’arrêtiez.

## <a name="next-steps"></a>Étapes suivantes

- Pour savoir comment mettre à l’échelle les pools `User` à 0, consultez [Mettre à l’échelle les pools `User` à 0](scale-cluster.md#scale-user-node-pools-to-0).
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
