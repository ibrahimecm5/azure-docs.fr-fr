---
title: Mise à jour d’un cluster Azure Kubernetes Service (AKS)
description: Découvrez comment mettre à niveau un cluster Azure Kubernetes service (AKS) pour obtenir les fonctionnalités et mises à jour de sécurité les plus récentes.
services: container-service
ms.topic: article
ms.date: 12/17/2020
ms.openlocfilehash: 8c5a395833cb19e4f5ce78f08ee37c2eb022169b
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132397335"
---
# <a name="upgrade-an-azure-kubernetes-service-aks-cluster"></a>Mise à jour d’un cluster Azure Kubernetes Service (AKS)

Une partie du cycle de vie du cluster AKS implique l’exécution de mises à niveau périodiques vers la dernière version de Kubernetes. Il est important d’appliquer les dernières publications de sécurité ou d’opérer une mise à niveau pour obtenir les dernières fonctionnalités. Cet article vous montre comment mettre à niveau les composants principaux ou un pool de nœud unique par défaut dans un cluster AKS.

Pour en savoir plus sur les clusters AKS utilisant plusieurs pools de nœuds ou des nœuds Windows Server, consultez [Mettre à niveau un pool de nœuds dans AKS][nodepool-upgrade].

## <a name="before-you-begin"></a>Avant de commencer

Pour les besoins de cet article, vous devez utiliser Azure CLI version 2.0.65 ou ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI][azure-cli-install].

> [!WARNING]
> Une mise à niveau de cluster AKS déclenche une isolation et un drainage de vos nœuds. Si vous n’avez qu’un quota de calcul faible, la mise à niveau peut échouer. Pour plus d’informations, consultez [Augmenter les quotas](../azure-portal/supportability/regional-quota-requests.md).

## <a name="check-for-available-aks-cluster-upgrades"></a>Recherchez les mises à niveau du cluster AKS disponibles

Pour rechercher les publications de Kubernetes disponibles pour votre cluster, utilisez la commande [az aks get-upgrades][az-aks-get-upgrades]. L’exemple suivant recherche les mises à niveau disponibles pour *myAKSCluster* dans *myResourceGroup* :

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

> [!NOTE]
> Lors de la mise à niveau d’un cluster AKS pris en charge, les versions mineures de Kubernetes ne peuvent pas être ignorées. Toutes les mises à niveau doivent être effectuées de façon séquentielle par le numéro de version principale. Par exemple, les mises à niveau *1.14.x* -> *1.15.x* et *1.15.x* -> *1.16.x* sont autorisées, mais pas *1.14.x* -> *1.16.x*. 
> > Vous pouvez ignorer plusieurs versions uniquement pour une mise à niveau d’une _version non prise en charge_ vers une _version prise en charge_. Par exemple, la mise à niveau d’une version *1.10.x* non prise en charge vers une version *1.15.x* prise en charge peut être effectuée.

L’exemple de sortie suivant montre que le cluster peut être mis à niveau vers les versions *1.19.1* et *1.19.3*:

```console
Name     ResourceGroup    MasterVersion    Upgrades
-------  ---------------  ---------------  --------------
default  myResourceGroup  1.18.10          1.19.1, 1.19.3
```

Si aucune mise à niveau n’est disponible, vous obtenez le message :

```console
ERROR: Table output unavailable. Use the --query option to specify an appropriate query. Use --debug for more info.
```

## <a name="customize-node-surge-upgrade"></a>Personnaliser l’augmentation du nombre de nœuds pendant une mise à niveau

> [!Important]
> Une augmentation du nombre de nœuds nécessite un quota d’abonnement pour l’augmentation maximale (max-surge) demandée pour chaque opération de mise à niveau. Par exemple, un cluster comprenant 5 pools de 4 nœuds a 20 nœuds en tout. Si chaque pool de nœuds a une valeur max-surge de 50 %, un quota de calcul et d’adresse IP supplémentaire de 10 nœuds (2 nœuds * 5 pools) est nécessaire pour effectuer la mise à niveau.
>
> Si vous utilisez Azure CNI, vérifiez que des adresses IP sont disponibles dans le sous-réseau et assurez-vous de [satisfaire aux exigences IP d’Azure CNI](configure-azure-cni.md).

Par défaut, AKS configure les mises à niveau avec un nœud supplémentaire. La valeur par défaut 1 pour les paramètres max-surge permet à AKS de réduire les interruptions de la charge de travail en créant un nœud supplémentaire avant l’isolation et le drainage des applications existantes pour remplacer un nœud de version antérieure. La valeur max-surge peut être personnalisée par pool de nœuds pour obtenir un compromis entre la vitesse de mise à niveau et l’interruption causée par la mise à niveau. En augmentant la valeur max-surge, le processus de mise à niveau se termine plus rapidement, bien que celui-ci puisse faire l’objet d’interruptions. 

Par exemple, une valeur max-surge de 100 % offre le processus de mise à niveau le plus rapide possible (doublant le nombre de nœuds), mais entraîne également la vidange simultanée de tous les nœuds du pool de nœuds. Il est donc préférable d’utiliser des valeurs élevées telles que celle-ci pour les environnements de test. Pour les pools de nœuds de production, nous vous recommandons d’utiliser un paramètre max-surge de 33 %.

AKS accepte à la fois des valeurs entières et des valeurs en pourcentage pour max-surge. Ainsi, l’entier « 5 » indique une augmentation de cinq nœuds. La valeur « 50 % » indique une augmentation égale à la moitié du nombre de nœuds actuel dans le pool. Les valeurs en pourcentage de max-surge vont de 1 % (minimum) à 100 % (maximum). Si vous utilisez une valeur en pourcentage, le nombre de nœuds est arrondi à la valeur entière la plus proche. Si la valeur max-surge est inférieure au nombre de nœuds actuel au moment de la mise à niveau, le nombre de nœuds actuel est utilisé comme valeur max-surge.

Pendant une mise à niveau, la valeur max-surge peut être égale au minimum à 1 et au maximum au nombre de nœuds dans votre pool de nœuds. Vous pouvez définir des valeurs plus élevées, mais le nombre maximal de nœuds utilisés pour max-surge ne sera pas supérieur au nombre de nœuds dans le pool au moment de la mise à niveau.

> [!Important]
> Le paramètre max-surge sur un pool de nœuds est persistant.  Les mises à niveau ultérieures de Kubernetes ou les mises à niveau des versions de nœud utiliseront ce paramètre. Vous pouvez modifier la valeur de max-surge pour vos pools de nœuds à tout moment. Pour les pools de nœuds de production, nous vous recommandons de définir un paramètre max-surge de 33 %.

Utilisez les commandes suivantes pour définir des valeurs max-surge pour des pools de nœuds nouveaux ou existants.

```azurecli-interactive
# Set max surge for a new node pool
az aks nodepool add -n mynodepool -g MyResourceGroup --cluster-name MyManagedCluster --max-surge 33%
```

```azurecli-interactive
# Update max surge for an existing node pool 
az aks nodepool update -n mynodepool -g MyResourceGroup --cluster-name MyManagedCluster --max-surge 5
```

## <a name="upgrade-an-aks-cluster"></a>Mettre à niveau un cluster AKS

Avec une liste des versions disponibles pour votre cluster AKS, utilisez la commande [az aks upgrade][az-aks-upgrade] pour opérer la mise à niveau. Au cours du processus de mise à niveau, AKS effectuera les opérations suivantes : 
- Il ajoute un nouveau nœud de tampon (ou autant de nœuds que ceux configurés dans [max-surge](#customize-node-surge-upgrade)) au cluster qui exécute la version de Kubernetes spécifiée. 
- Il effectue [l’isolation et le drainage][kubernetes-drain] d’un des anciens nœuds pour minimiser l’interruption aux applications en cours d’exécution (si vous utilisez max-surge, l’[isolation et le drainage][kubernetes-drain] portent sur autant de nœuds que le nombre de nœuds de tampon spécifiés). 
- Lorsque l’ancien nœud est entièrement drainé, il est réinitialisé pour recevoir la nouvelle version et devient le nœud de mémoire tampon pour le nœud suivant à mettre à niveau. 
- Ce processus se répète jusqu’à ce que tous les nœuds du cluster soient mis à niveau. 
- À la fin du processus, le dernier nœud tampon est supprimé, ce qui a pour effet de maintenir le nombre de nœuds d’agent existants et l’équilibre de la zone.

```azurecli-interactive
az aks upgrade \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --kubernetes-version KUBERNETES_VERSION
```

Quelques minutes suffisent pour mettre à niveau le cluster. Le temps nécessaire varie en fonction du nombre de nœuds.

> [!IMPORTANT]
> Assurez-vous que les `PodDisruptionBudgets` (PDB) permettent de déplacer au moins un réplica de pod à la fois, sinon l’opération de drainage/d’exclusion échouera.
> Si l’opération de drainage échoue, l’opération de mise à niveau échouera de par sa conception afin de garantir que les applications ne soient pas interrompues. Corrigez ce qui a provoqué l’arrêt de l’opération (PDB incorrects, manque de quota, etc.), puis recommencez l’opération.

Pour vérifier si la mise à niveau a réussi, utilisez la commande [az aks show][az-aks-show] :

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

L’exemple de sortie suivant montre que le cluster exécute à présent la version *1.18.10* :

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ----------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.18.10              Succeeded            myakscluster-dns-379cbbb9.hcp.eastus.azmk8s.io
```

## <a name="set-auto-upgrade-channel"></a>Définir un canal de mise à niveau automatique

En plus de mettre à niveau manuellement un cluster, vous pouvez définir un canal de mise à niveau automatique sur votre cluster. Les canaux de mise à niveau suivants sont disponibles :

|Canal| Action | Exemple
|---|---|---|
| `none`| désactive les mises à niveau automatiques et conserve le cluster dans sa version actuelle de Kubernetes| Paramètre par défaut si vous ne modifiez pas la valeur|
| `patch`| met à niveau automatiquement le cluster vers la dernière version du correctif prise en charge lorsqu’elle devient disponible, tout en conservant la version mineure| Par exemple, si un cluster exécute la version *1.17.7* et que les versions *1.17.9*, *1.18.4*, *1.18.6* et *1.19.1* sont disponibles, votre cluster est mis à niveau vers la version *1.17.9*.|
| `stable`| met à niveau automatiquement le cluster vers la dernière version du correctif prise en charge sur la version mineure *N-1*, où *N* désigne la dernière version mineure prise en charge.| Par exemple, si un cluster exécute la version *1.17.7* et que les versions *1.17.9*, *1.18.4*, *1.18.6* et *1.19.1* sont disponibles, votre cluster est mis à niveau vers la version *1.18.6*.
| `rapid`| met à niveau automatiquement le cluster vers la dernière version du correctif prise en charge sur la dernière version mineure prise en charge.| Dans les cas où le cluster a une version de Kubernetes qui est une version mineure *N-2* où *N* est la dernière version mineure prise en charge, le cluster se met d’abord à niveau vers la dernière version de patch prise en charge sur la version mineure *N-1*. Par exemple, si un cluster exécute la version *1.17.7* et que les versions *1.17.9*, *1.18.4*, *1.18.6* et *1.19.1* sont disponibles, votre cluster se met d’abord à niveau vers la version *1.18.6*, puis se met à niveau vers la version *1.19.1*.
| `node-image`| Mettez automatiquement à niveau l’image de nœud vers la dernière version disponible.| Microsoft fournit régulièrement des correctifs et de nouvelles images pour les nœuds d’image (généralement une fois par semaine), mais vos nœuds en cours d’exécution n’obtiendront pas les nouvelles images, sauf si vous effectuez une mise à niveau de l’image de nœud. L’activation du canal node-image met automatiquement à jour vos images de nœud quand une nouvelle version est disponible. |

> [!NOTE]
> La mise à jour automatique du cluster ne concerne que les versions GA de Kubernetes et non les versions en préversion.

La mise à niveau automatique d’un cluster suit le même processus que la mise à niveau manuelle d’un cluster. Pour plus d’informations, consultez [Mettre à niveau un cluster AKS][upgrade-cluster].

Pour définir le canal de mise à niveau automatique lors de la création d’un cluster, utilisez le paramètre *auto-upgrade-channel*, comme dans l’exemple suivant.

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myAKSCluster --auto-upgrade-channel stable --generate-ssh-keys
```

Pour définir le canal de mise à niveau automatique sur un cluster existant, mettez à jour le paramètre *auto-upgrade-channel*, comme dans l’exemple suivant.

```azurecli-interactive
az aks update --resource-group myResourceGroup --name myAKSCluster --auto-upgrade-channel stable
```

## <a name="using-cluster-auto-upgrade-with-planned-maintenance"></a>Utilisation de la mise à niveau automatique de cluster avec la maintenance planifiée

Si vous utilisez la maintenance planifiée et la mise à niveau automatique, votre mise à niveau démarre pendant la fenêtre de maintenance spécifiée. Pour plus d’informations sur la maintenance planifiée, consultez [Utiliser la maintenance planifiée pour planifier les fenêtres de maintenance de votre cluster Azure Kubernetes service (AKS) (préversion)][planned-maintenance].

## <a name="special-considerations-for-node-pools-that-span-multiple-availability-zones"></a>Considérations spéciales pour les pools de nœuds qui s’étendent sur plusieurs zones de disponibilité

AKS utilise l’équilibrage de zone best-effort dans les groupes de nœuds. Pendant une surtension de mise à niveau, les zones des nœuds de surtension dans les groupes de machines virtuelles identiques sont inconnues à l’avance. Cela peut entraîner temporairement une configuration de zone déséquilibrée pendant une mise à niveau. Toutefois, AKS supprime les nœuds de surtension une fois la mise à niveau terminée et préserve l’équilibre d’origine des zones. Si vous souhaitez que vos zones restent équilibrées pendant la mise à niveau, augmentez le nombre de nœuds à un multiple de 3. Les groupes de machines virtuelles identiques équilibreront ensuite vos nœuds entre les zones de disponibilité avec un équilibrage de zone best-effort.

Si des PVC sont soutenues par des disques LRS Azure, elles seront liées à une zone particulière et risquent de ne pas être récupérées immédiatement si le nœud de surtension ne correspond pas à la zone de la PVC. Cela peut entraîner un temps d’arrêt sur votre application lorsque l’opération de mise à niveau continue à drainer les nœuds, mais que les PV sont liés à une zone. Pour gérer ce cas et maintenir la haute disponibilité, configurez un [budget de perturbation des pods](https://kubernetes.io/docs/tasks/run-application/configure-pdb/) sur votre application. Cela permet à Kubernetes de respecter vos exigences en matière de disponibilité pendant l’opération de drainage de la mise à niveau. 


## <a name="next-steps"></a>Étapes suivantes

Cet article vous a montré comment mettre à niveau un cluster AKS existant. Pour en savoir plus sur le déploiement et la gestion des clusters d’AKS, reportez-vous aux didacticiels.

> [!div class="nextstepaction"]
> [Didacticiels AKS][aks-tutorial-prepare-app]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-upgrades]: /cli/azure/aks#az_aks_get_upgrades
[az-aks-upgrade]: /cli/azure/aks#az_aks_upgrade
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-provider-register]: /cli/azure/provider#az_provider_register
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[upgrade-cluster]:  #upgrade-an-aks-cluster
[planned-maintenance]: planned-maintenance.md