---
title: Configurer un réseau Azure CNI dans AKS (Azure Kubernetes Service)
description: Découvrez comment configurer un réseau (avancé) Azure CNI dans AKS (Azure Kubernetes Service), notamment pour déployer un cluster AKS dans un réseau et un sous-réseau virtuels existants.
services: container-service
ms.topic: article
ms.date: 06/03/2019
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 3ab6ce9f955829cb3faf774c5b85c205f5841474
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132551346"
---
# <a name="configure-azure-cni-networking-in-azure-kubernetes-service-aks"></a>Configurer un réseau Azure CNI dans AKS (Azure Kubernetes Service)

Par défaut, les clusters AKS utilisent [kubenet][kubenet], et un réseau et un sous-réseau virtuels sont automatiquement créés. Avec *kubenet*, les nœuds obtiennent une adresse IP d’un sous-réseau du réseau virtuel. La traduction d’adresses réseau (NAT) est ensuite configurée sur les nœuds, et les pods reçoivent une adresse IP « cachée » derrière l’adresse IP du nœud. Cette approche réduit le nombre d’adresses IP que vous avez besoin de réserver dans votre espace réseau à l’usage des pods.

Avec l’interface [Azure Container Networking Interface (CNI)][cni-networking], chaque pod reçoit une adresse IP du sous-réseau et est accessible directement. Ces adresses IP doivent être uniques dans votre espace réseau et doivent être planifiées à l’avance. Chaque nœud possède un paramètre de configuration pour le nombre maximal de pods qu’il prend en charge. Le nombre équivalent d’adresses IP par nœud est alors réservé à l’avance pour ce nœud. Cette approche nécessite davantage de planification. De plus, elle conduit souvent à l’épuisement des adresses IP ou à la nécessité de regénérer les clusters dans un sous-réseau plus vaste à mesure que vos demandes d’applications augmentent.

Cet article vous montre comment utiliser les réseaux *Azure CNI* afin de créer et d’utiliser un sous-réseau de réseau virtuel pour un cluster AKS. Pour plus d’informations sur les options et considérations relatives aux réseaux, consultez [Concepts de réseau pour Kubernetes et AKS][aks-network-concepts].

## <a name="prerequisites"></a>Prérequis

* Le réseau virtuel du cluster AKS doit autoriser les connexions Internet sortantes.
* Les clusters AKS ne peuvent pas utiliser `169.254.0.0/16`, `172.30.0.0/16`, `172.31.0.0/16` ou `192.0.2.0/24` pour la plage d’adresses de service Kubernetes, la plage d’adresses de pod ou la plage d’adresses de réseau virtuel de cluster.
* L'identité de cluster utilisée par le cluster AKS doit au moins disposer des autorisations [Contributeur de réseau](../role-based-access-control/built-in-roles.md#network-contributor) sur le sous-réseau de votre réseau virtuel. Si vous souhaitez définir un [rôle personnalisé](../role-based-access-control/custom-roles.md) au lieu d’utiliser le rôle de contributeur de réseau intégré, les autorisations suivantes sont nécessaires :
  * `Microsoft.Network/virtualNetworks/subnets/join/action`
  * `Microsoft.Network/virtualNetworks/subnets/read`
* Le sous-réseau affecté au pool de nœuds AKS ne peut pas être un [sous-réseau délégué](../virtual-network/subnet-delegation-overview.md).
* AKS n’applique pas les groupes de sécurité réseau à son sous-réseau et ne modifie pas les groupes associés à ce sous-réseau. Si vous fournissez votre propre sous-réseau et ajoutez des groupes associés à ce sous-réseau, vous devez vous assurer que les règles de sécurité dans les groupes autorisent le trafic entre les plages CIDR du nœud et du pod. Pour plus d’informations, consultez [Groupes de sécurité réseau][aks-network-nsg].

## <a name="plan-ip-addressing-for-your-cluster"></a>Planifier l’adressage IP pour votre cluster

Les clusters configurés avec les réseaux Azure CNI nécessitent une planification supplémentaire. La taille de votre réseau virtuel et de son sous-réseau doit être suffisante pour prendre en charge le nombre de pods que vous envisagez d’exécuter, ainsi que le nombre de nœuds du cluster.

Les adresses IP des pods et des nœuds de cluster sont affectées à partir du sous-réseau spécifié du réseau virtuel. Chaque nœud est configuré avec une adresse IP principale. Par défaut, 30 adresses IP supplémentaires sont préconfigurés par CNI Azure et affectées à des pods planifiés sur le nœud. Lorsque vous effectuez un scale-out de votre cluster, chaque nœud est configuré de manière similaire avec des adresses IP du sous-réseau. Vous pouvez également voir le nombre [maximal de pods par nœud](#maximum-pods-per-node).

> [!IMPORTANT]
> Le nombre d’adresses IP requises doit prendre en compte des considérations relatives aux opérations de mise à niveau et à l’échelle. Si vous définissez la plage d’adresses IP pour prendre en charge uniquement un nombre fixe de nœuds, vous ne pouvez pas mettre à niveau ou à l’échelle votre cluster.
>
> * Lorsque vous **mettez à niveau** votre cluster AKS, un nouveau nœud est déployé dans le cluster. Les services et charges de travail commencent à s’exécuter sur le nouveau nœud, et le nœud plus ancien est supprimé du cluster. Ce processus de mise à niveau propagée nécessite la disponibilité d’un minimum d’adresses IP ou de bloc supplémentaires. Le nombre de nœuds est alors `n + 1`.
>   * Cette considération est particulièrement importante lorsque vous utilisez des pools de nœuds Windows Server. Les nœuds Windows Server dans AKS n’appliquent pas automatiquement de mises à jour ; à la place, vous effectuez une mise à niveau sur le pool de nœuds. Cette mise à niveau déploie les nouveaux nœuds avec les derniers correctifs de sécurité et image de nœud de base Windows Server 2019. Pour plus d’informations sur la mise à niveau d’un pool de nœuds Windows Server, consultez [Upgrade a node pool][nodepool-upgrade] (Mettre à niveau un pool de nœuds).
>
> * Lorsque vous **mettez à l’échelle** un cluster AKS, un nouveau nœud est déployé dans le cluster. Les services et charges de travail commencent à s’exécuter sur le nouveau nœud. Votre plage d’adresses IP doit prendre en compte la manière dont vous voulez augmenter le nombre de nœuds et de pods que votre cluster prend en charge. Un nœud supplémentaire pour les opérations de mise à niveau doit également être inclus. Le nombre de nœuds est alors `n + number-of-additional-scaled-nodes-you-anticipate + 1`.

Si vous vous attendez à ce que vos nœuds exécutent le nombre maximal de pods, et détruisent et déploient régulièrement des pods, vous devez également prendre en compte des adresses IP supplémentaires par nœud. Ces adresses IP supplémentaires tiennent compte du fait que la suppression d’un service et la libération de l’adresse IP pour le déploiement et l’acquisition de l’adresse d’un nouveau service peuvent prendre quelques secondes.

Le plan d’adressage IP pour un cluster AKS se compose d’un réseau virtuel, d’au moins un sous-réseau pour les nœuds et les pods, et d’une plage d’adresses de service Kubernetes.

| Plage Azure/ressource Azure | Limites et tailles |
| --------- | ------------- |
| Réseau virtuel | Le réseau virtuel Azure peut être aussi volumineux que la valeur /8, mais est limité à 65 536 adresses IP configurées. Avant de configurer votre espace d’adressage, prenez en compte tous vos besoins en matière de mise en réseau, dont la communication avec des services dans d’autres réseaux virtuels. Par exemple, si vous configurez un espace d’adressage trop important, vous risquez de rencontrer des problèmes de chevauchement avec d’autres espaces d’adressage au sein de votre réseau.|
| Subnet | Doit pouvoir contenir les nœuds, les pods, ainsi que toutes les ressources Kubernetes et Azure qui peuvent être provisionnées dans votre cluster. Par exemple, si vous déployez un équilibreur de charge interne Azure, ses adresses IP frontend sont allouées à partir du sous-réseau du cluster, et non à partir des adresses IP non publiques. La taille du sous-réseau doit également prendre en compte les opérations de mise à niveau ou de futurs besoins de mise à l’échelle.<p />Pour calculer la taille de sous-réseau *minimale*, dont celle d’un nœud supplémentaire pour les opérations de mise à niveau : `(number of nodes + 1) + ((number of nodes + 1) * maximum pods per node that you configure)`<p/>Exemple pour un cluster à 50 nœuds : `(51) + (51  * 30 (default)) = 1,581` (/21 ou plus)<p/>Exemple pour un cluster de 50 nœuds incluant également un approvisionnement pour porter l’échelle à 10 nœuds supplémentaires : `(61) + (61 * 30 (default)) = 1,891` (/21 ou plus)<p>Si vous ne spécifiez pas de nombre maximal de pods par nœud lorsque vous créez votre cluster, le nombre maximal de pods par nœud est de *30*. Le nombre minimal d’adresses IP requises est basé sur cette valeur. Si vous calculez vos exigences d’adresse IP minimales sur une autre valeur maximale, consultez [comment configurer le nombre maximal de pods par nœud](#configure-maximum---new-clusters) pour définir cette valeur lorsque vous déployez votre cluster. |
| Plage d’adresses de service Kubernetes | Cette plage ne doit être utilisée par aucun élément réseau sur ce réseau virtuel ou connecté à celui-ci. Le CIDR d’adresse du service doit être inférieur à /12. Vous pouvez réutiliser cette plage sur différents clusters AKS. |
| Adresse IP du service DNS Kubernetes | Adresse IP dans la plage d'adresses de service Kubernetes, qui sera utilisée par la détection de service de cluster. N’utilisez pas la première adresse IP de votre plage d’adresses. La première adresse de votre plage de sous-réseaux est utilisée pour l’adresse *kubernetes.default.svc.cluster.local*. |
| Adresse de pont Docker | L'adresse réseau du pont Docker représente l'adresse réseau *docker0* par défaut présente dans toutes les installations Docker. Bien que le pont *docker0* ne soit pas utilisé par les clusters AKS ou les pods eux-mêmes, vous devez définir cette adresse pour continuer à prendre en charge des scénarios tels que *docker build* au sein du cluster AKS. Vous devez sélectionner un CIDR pour l’adresse réseau du pont Docker, sans quoi Docker choisira automatiquement un sous-réseau qui pourrait entrer en conflit avec d’autres CIDR. Vous devez choisir un espace d'adressage qui n'entre pas en collision avec le reste des CIDR de vos réseaux, y compris le CIDR du service du cluster et le CIDR du pod. Valeur par défaut : 172.17.0.1/16. Vous pouvez réutiliser cette plage sur différents clusters AKS. |

## <a name="maximum-pods-per-node"></a>Nombre maximal de pods par nœud

Le nombre maximal de pods par nœud dans un cluster AKS est de 250. Le nombre maximal *par défaut* de pods par nœud varie selon qu’il s’agit d’un réseau *kubenet* ou *Azure CNI*, mais aussi en fonction de la méthode de déploiement du cluster.

| Méthode de déploiement | Kubenet par défaut | Azure CNI par défaut | Configurable au moment du déploiement |
| -- | :--: | :--: | -- |
| Azure CLI | 110 | 30 | Oui (jusqu’à 250) |
| Modèle Resource Manager | 110 | 30 | Oui (jusqu’à 250) |
| Portail | 110 | 110 (configuré dans l’onglet Pools de nœuds) | Non |

### <a name="configure-maximum---new-clusters"></a>Configurer un maximum : nouveaux clusters

Vous pouvez configurer le nombre maximal de pods par nœud au moment du déploiement du cluster ou lorsque vous ajoutez de nouveaux pools de nœuds. Si vous procédez au déploiement avec Azure CLI ou avec un modèle Resource Manager, vous pouvez définir la valeur du nombre maximal de pods par nœud avec un maximum de 250.

Si vous ne spécifiez pas le paramètre maxPods lors de la création de pools de nœuds, vous recevez une valeur par défaut de 30 pour Azure CNI.

Une valeur minimale pour le nombre maximal de pods par nœud est appliquée afin de garantir l’espace des blocs système critiques pour l’intégrité du cluster. La valeur minimale qui peut être définie pour le nombre maximal de pods par nœud est égale à 10 si, et seulement si, la configuration de chaque pool de nœuds a un espace pour un minimum de 30 pods. Par exemple, la définition du nombre maximal de pods par nœud sur la valeur minimale de 10 nécessite que chaque pool de nœuds inclue un minimum de 3 nœuds. Cette exigence s’applique également à chaque pool de nœuds créé, de sorte que, si la valeur 10 est définie comme le nombre maximal de pods par nœud, chaque pool de nœuds ajouté doit inclure au moins 3 nœuds.

| Mise en réseau | Minimum | Maximale |
| -- | :--: | :--: |
| Azure CNI | 10 | 250 |
| Kubenet | 10 | 110 |

> [!NOTE]
> La valeur minimale dans le tableau ci-dessus est strictement appliquée par le service AKS. Vous ne pouvez pas définir une valeur maxPods inférieure à la valeur minimale, car cela peut empêcher le cluster de démarrer.

* **Azure CLI** : spécifiez l’argument `--max-pods` lorsque vous déployez un cluster avec la commande [az aks create][az-aks-create]. La valeur maximale est 250.
* **Modèle Resource Manager** : spécifiez la propriété `maxPods` dans l’objet [ManagedClusterAgentPoolProfile] lorsque vous déployez un cluster avec un modèle Resource Manager. La valeur maximale est 250.
* **Portail Azure**: vous ne pouvez pas modifier le nombre maximal de pods par nœud lorsque vous déployez un cluster avec le portail Azure. Les clusters de réseau Azure CNI sont limités à 110 pods par nœud lorsque vous effectuez le déploiement à partir du portail Azure.

### <a name="configure-maximum---existing-clusters"></a>Configurer un maximum : clusters existants

Le paramètre maxPod par nœud peut être défini lorsque vous créez un pool de nœuds. Si vous devez augmenter le paramètre maxPod par nœud sur un cluster existant, ajoutez un nouveau pool de nœuds avec le nouveau nombre de maxPod souhaité. Après la migration de vos pods vers le nouveau pool, supprimez le pool plus ancien. Pour supprimer un pool plus ancien dans un cluster, assurez-vous de définir les modes de pool de nœuds comme défini dans le [document de pools de nœuds système][system-node-pools].

## <a name="deployment-parameters"></a>Paramètres de déploiement

Quand vous créez un cluster AKS, les paramètres suivants sont configurables pour les réseaux Azure CNI :

**Réseau virtuel** : réseau virtuel dans lequel vous souhaitez déployer le cluster Kubernetes. Si vous souhaitez créer un réseau virtuel pour votre cluster, sélectionnez *Créer un nouveau*, puis suivez la procédure décrite dans la section *Créer un réseau virtuel*. Pour plus d’informations sur les limites et quotas d’un réseau virtuel Azure, voir [Abonnement Azure et limites, quotas et contraintes du service](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits).

**Sous-réseau** : sous-réseau du réseau virtuel dans lequel vous souhaitez déployer le cluster. Si vous souhaitez créer un nouveau sous-réseau dans le réseau virtuel pour votre cluster, sélectionnez *Créer un nouveau*, puis exécutez la procédure décrite dans la section *Créer un sous-réseau*. Pour une connectivité hybride, la plage d’adresses ne doit pas chevaucher d’autres réseaux virtuels dans votre environnement.

**Plug-in Réseau Azure** : quand le plug-in Réseau Azure est utilisé, le service d’équilibrage de charge interne avec « externalTrafficPolicy=Local » n’est pas accessible à partir de machines virtuelles avec une adresse IP dans clusterCIDR qui n’appartient pas au cluster AKS.

**Plage d’adresses du service Kubernetes** : ce paramètre est l’ensemble d’adresses IP virtuelles que Kubernetes attribue aux [services][services] internes dans votre cluster. Vous pouvez utiliser n’importe quelle plage d’adresses privées répondant aux exigences suivantes :

* ne peut pas figurer dans la plage d’adresses IP de réseau virtuel de votre cluster ;
* ne peut pas présenter de chevauchement avec un autre réseau virtuel avec lequel le réseau virtuel du cluster s’apparie ;
* ne doit avoir aucun élément en commun avec des adresses IP locales ;
* ne doit pas être dans les plages `169.254.0.0/16`, `172.30.0.0/16`, `172.31.0.0/16` ou `192.0.2.0/24`

Bien qu’il soit techniquement possible de spécifier une plage d’adresses de service dans le même réseau virtuel que votre cluster, cette manière de procéder n’est pas recommandée. Tout chevauchement entre des plages d’adresses IP est susceptible d’entraîner des comportements imprévisibles. Pour plus d’informations, consultez la section [FAQ](#frequently-asked-questions) de cet article. Pour plus d’informations sur les services Kubernetes, consultez[Services][services] dans la documentation de Kubernetes.

**Adresse IP du service DNS Kubernetes** :  adresse IP du service DNS du cluster. Cette adresse doit se situer dans la *plage d’adresses du service Kubernetes*. N’utilisez pas la première adresse IP de votre plage d’adresses. La première adresse de votre plage de sous-réseaux est utilisée pour l’adresse *kubernetes.default.svc.cluster.local*.

**Adresse du pont Docker** : L'adresse réseau du pont Docker représente l'adresse réseau *docker0* par défaut présente dans toutes les installations Docker. Bien que le pont *docker0* ne soit pas utilisé par les clusters AKS ou les pods eux-mêmes, vous devez définir cette adresse pour continuer à prendre en charge des scénarios tels que *docker build* au sein du cluster AKS. Vous devez sélectionner un CIDR pour l'adresse réseau du pont docker, sinon le docker choisira automatiquement un sous-réseau qui pourrait entrer en conflit avec d'autres CIDR. Vous devez choisir un espace d'adressage qui n'entre pas en collision avec le reste des CIDR de vos réseaux, y compris le CIDR du service du cluster et le CIDR du pod.

## <a name="configure-networking---cli"></a>Configurer la mise en réseau – Interface de ligne de commande

Lors de la création d’un cluster AKS avec Azure CLI, il est également possible de configurer un réseau Azure CNI. Utilisez les commandes suivantes pour créer un cluster AKS en activant un réseau Azure CNI.

Tout d’abord, récupérez l’ID de la ressource du sous-réseau auquel le cluster AKS sera joint :

```azurecli-interactive
$ az network vnet subnet list \
    --resource-group myVnet \
    --vnet-name myVnet \
    --query "[0].id" --output tsv

/subscriptions/<guid>/resourceGroups/myVnet/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/default
```

Utilisez la commande [az aks create][az-aks-create] avec l’argument `--network-plugin azure` pour créer un cluster avec mise en réseau avancée. Remplacez la valeur `--vnet-subnet-id` par l’ID du sous-réseau recueilli à l’étape précédente :

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --network-plugin azure \
    --vnet-subnet-id <subnet-id> \
    --docker-bridge-address 172.17.0.1/16 \
    --dns-service-ip 10.2.0.10 \
    --service-cidr 10.2.0.0/24 \
    --generate-ssh-keys
```

## <a name="configure-networking---portal"></a>Configurer la mise en réseau – Portail

La capture d’écran suivante de votre portail Azure représente un exemple de configuration de ces paramètres durant la création du cluster AKS :

![Configuration de mise en réseau avancée dans le portail Azure][portal-01-networking-advanced]

## <a name="dynamic-allocation-of-ips-and-enhanced-subnet-support-preview"></a>Allocation dynamique des adresses IP et prise en charge de sous-réseau améliorée (préversion)

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

Un inconvénient de la CNI (Container Networking Interface) traditionnelle est l’épuisement des adresses IP de pod à mesure que le cluster AKS croît, qui entraîne la nécessité de reconstruire l’ensemble du cluster dans un sous-réseau plus grand. La nouvelle fonctionnalité d’allocation d’adresses IP dynamique dans Azure CNI résout ce problème en allouant des adresses IP de pod à partir d’un sous-réseau séparé du sous-réseau hébergeant le cluster AKS. Elle offre les avantages suivants :

* **Meilleure utilisation des adresses IP** : les adresses IP sont allouées de façon dynamique aux pods du cluster à partir du sous-réseau de pod. Cela permet d’améliorer l’utilisation des adresses IP dans le cluster par rapport à la solution de CNI traditionnelle, qui effectue une allocation statique des adresses IP pour chaque nœud.  

* **Scalabilité et flexibilité** : les sous-réseaux de nœud et de pod peuvent être mis à l’échelle indépendamment. Un seul sous-réseau de pod peut être partagé entre plusieurs pools de nœuds d’un cluster ou plusieurs clusters AKS déployés dans le même réseau virtuel. Vous pouvez également configurer un sous-réseau de pod distinct pour un pool de nœuds.  

* **Hautes performances** : étant donné que des adresses IP de réseau virtuel sont affectées au pod, elles disposent d’une connectivité directe à d’autres pod et ressources de cluster dans le réseau virtuel. La solution prend en charge des clusters très volumineux sans dégradation des performances.

* **Stratégies de réseau virtuel distinctes pour les pods** : étant donné que les pods ont un sous-réseau distinct, vous pouvez configurer ceux-ci des stratégies de réseau virtuel distinctes, qui diffèrent des stratégies de nœud. Cela permet de nombreux scénarios utiles, tels que l’autorisation de la connectivité Internet uniquement pour les pods et pas pour les nœuds, la correction de l’adresse IP source pour un pod dans un pool de nœuds à l’aide d’une NAT de réseau virtuel, et l’utilisation de groupes de sécurité réseau pour filtrer le trafic entre les pools de nœuds.  

* **Stratégies réseau Kubernetes** : les stratégies réseau Azure et Calico fonctionnent avec cette nouvelle solution.  

### <a name="additional-prerequisites"></a>Autres composants requis

Les [composants requis][prerequisites] déjà répertoriés pour Azure CNI s’appliquent toujours, mais il existe quelques limitations supplémentaires :

* Seuls les clusters de nœuds et les pools de nœuds Linux sont pris en charge.
* Les clusters de moteur AKS et en libre service ne sont pas pris en charge.

### <a name="install-the-aks-preview-azure-cli"></a>Installez l’interface de ligne de commande Azure `aks-preview`

Vous aurez besoin de l’extension Azure CLI *aks-preview*. Installez l’extension d’Azure CLI *aks-preview* à l’aide de la commande [az extension add][az-extension-add]. Ou installez toutes les mises à jour disponibles à l’aide de la commande [az extension update][az-extension-update].

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-the-podsubnetpreview-preview-feature"></a>Inscrire la fonctionnalité d’évaluation `PodSubnetPreview`

Pour utiliser la fonctionnalité, vous devez activer l’indicateur de fonctionnalité `PodSubnetPreview` sur votre abonnement.

Inscrivez l’indicateur de fonctionnalité `PodSubnetPreview` à l’aide de la commande [az feature register][az-feature-register], comme indiqué dans l’exemple suivant :

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "PodSubnetPreview"
```

Quelques minutes sont nécessaires pour que l’état s’affiche *Registered* (Inscrit). Vérifiez l’état de l’inscription à l’aide de la commande [az feature list][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/PodSubnetPreview')].{Name:name,State:properties.state}"
```

Lorsque vous êtes prêt, actualisez l’inscription du fournisseur de ressources *Microsoft.ContainerService* à l’aide de la commande [az provider register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="planning-ip-addressing"></a>Planification de l’adressage IP

Lorsque vous utilisez cette fonctionnalité, la planification est bien plus simple. Étant donné que les nœuds et les pods se mettent à l’échelle indépendamment, leurs espaces d’adressage peuvent également être planifiés séparément. Étant donné que les sous-réseaux de pod peuvent être configurés pour la granularité d’un pool de nœuds, les clients peuvent toujours ajouter un sous-réseau quand ils ajoutent un pool de nœuds. Les pods système dans un cluster/pool de nœuds recevant également des adresses IP du sous-réseau de pod, ce comportement doit être pris en compte.

La planification des adresses IP pour les services Kubernetes et le pont Docker reste inchangée.

### <a name="maximum-pods-per-node-in-a-cluster-with-dynamic-allocation-of-ips-and-enhanced-subnet-support"></a>Nombre maximal de pods par nœud dans un cluster avec allocation dynamique d’adresses IP et prise en charge de sous-réseau améliorée

Les valeurs de pods par nœud lors de l’utilisation d’Azure CNI avec allocation dynamique d’adresses IP ont changé légèrement par rapport au comportement de la CNI traditionnelle :

|CNI|Méthode de déploiement|Default|Configurable au moment du déploiement|
|--|--| :--: |--|
|Azure CNI traditionnelle|Azure CLI|30|Oui (jusqu’à 250)|
|Azure CNI avec allocation dynamique d’adresses IP|Azure CLI|250|Oui (jusqu’à 250)|

Toutes les autres instructions relatives à la configuration du nombre maximal de nœuds par pod restent les mêmes.

### <a name="additional-deployment-parameters"></a>Paramètres de déploiement supplémentaires

Les paramètres de déploiement décrits ci-dessus sont toujours valides, à une exception près :

* Le paramètre **sous-réseau** fait désormais référence au sous-réseau associé aux nœuds du cluster.
* Un paramètre supplémentaire **sous-réseau de pod** est utilisé pour spécifier le sous-réseau dont les adresses IP seront allouées de façon dynamique aux pods.

### <a name="configure-networking---cli-with-dynamic-allocation-of-ips-and-enhanced-subnet-support"></a>Configurer la mise en réseau – CLI avec allocation dynamique d’adresses IP et prise en charge de sous-réseau améliorée

L’utilisation de l’allocation dynamique d’adresses IP et de la prise en charge de sous-réseau améliorée dans votre cluster est similaire à la méthode par défaut pour la configuration d’une Azure CNI de cluster. L’exemple suivant présente la création d’un réseau virtuel avec un sous-réseau pour les nœuds et un sous-réseau pour les pods, et la création d’un cluster qui utilise Azure CNI avec l’allocation dynamique d’adresses IP et la prise en charge de sous-réseau améliorée. Veillez à remplacer des variables telles que `$subscription` par vos propres valeurs.

Commencez par créer le réseau virtuel avec deux sous-réseaux :

```azurecli-interactive
resourceGroup="myResourceGroup"
vnet="myVirtualNetwork"
location="westcentralus"

# Create the resource group
az group create --name $resourceGroup --location $location

# Create our two subnet network 
az network vnet create -g $resourceGroup --location $location --name $vnet --address-prefixes 10.0.0.0/8 -o none 
az network vnet subnet create -g $resourceGroup --vnet-name $vnet --name nodesubnet --address-prefixes 10.240.0.0/16 -o none 
az network vnet subnet create -g $resourceGroup --vnet-name $vnet --name podsubnet --address-prefixes 10.241.0.0/16 -o none 
```

Ensuite, créez le cluster en référençant le sous-réseau de nœud à l’aide de `--vnet-subnet-id` et le sous-réseau de pod à l’aide de `--pod-subnet-id` :

```azurecli-interactive
clusterName="myAKSCluster"
subscription="aaaaaaa-aaaaa-aaaaaa-aaaa"

az aks create -n $clusterName -g $resourceGroup -l $location \
  --max-pods 250 \
  --node-count 2 \
  --network-plugin azure \
  --vnet-subnet-id /subscriptions/$subscription/resourceGroups/$resourceGroup/providers/Microsoft.Network/virtualNetworks/$vnet/subnets/nodesubnet \
  --pod-subnet-id /subscriptions/$subscription/resourceGroups/$resourceGroup/providers/Microsoft.Network/virtualNetworks/$vnet/subnets/podsubnet  
```

#### <a name="adding-node-pool"></a>Ajout d’un pool de nœuds

Lorsque vous ajoutez un pool de nœuds, référencez le sous-réseau de nœud à l’aide de `--vnet-subnet-id` et le sous-réseau de pod à l’aide de `--pod-subnet-id`. L’exemple suivant crée deux sous-réseaux qui sont ensuite référencés dans la création d’un nouveau pool de nœuds :

```azurecli-interactive
az network vnet subnet create -g $resourceGroup --vnet-name $vnet --name node2subnet --address-prefixes 10.242.0.0/16 -o none 
az network vnet subnet create -g $resourceGroup --vnet-name $vnet --name pod2subnet --address-prefixes 10.243.0.0/16 -o none 

az aks nodepool add --cluster-name $clusterName -g $resourceGroup  -n newnodepool \
  --max-pods 250 \
  --node-count 2 \
  --vnet-subnet-id /subscriptions/$subscription/resourceGroups/$resourceGroup/providers/Microsoft.Network/virtualNetworks/$vnet/subnets/node2subnet \
  --pod-subnet-id /subscriptions/$subscription/resourceGroups/$resourceGroup/providers/Microsoft.Network/virtualNetworks/$vnet/subnets/pod2subnet \
  --no-wait 
```

## <a name="frequently-asked-questions"></a>Forum aux questions

La série suivante de questions-réponses s’applique à la configuration de réseaux **Azure CNI**.

* *Puis-je déployer des machines virtuelles dans le sous-réseau de mon cluster ?*

  Oui.

* *Quelle adresse IP source les systèmes externes voient-ils pour le trafic en provenance d’un pod compatible avec Azure CNI ?*

  Les systèmes dans le même réseau virtuel que le cluster AKS voient l’adresse IP du pod comme l’adresse source pour tout trafic en provenance du pod. Les systèmes situés en dehors du réseau virtuel du cluster AKS voient l’adresse IP du nœud en tant qu’adresse source pour tout le trafic en provenance du pod.

* *Puis-je configurer des stratégies de réseau spécifiques aux pods ?*

  Oui, la stratégie de réseau Kubernetes est disponible dans AKS. Pour la prise en main, consultez [Sécuriser le trafic entre les pods avec des stratégies réseau dans Azure Kubernetes Service (AKS)][network-policy].

* *Le nombre maximal de pods pouvant être déployés sur un nœud peut-il être configuré ?*

  Oui, lorsque vous déployez un cluster avec l’interface CLI Azure ou un modèle Resource Manager. Consultez [Nombre maximal de pods par nœud](#maximum-pods-per-node).

  Vous ne pouvez pas modifier le nombre maximal de pods par nœud sur un cluster existant.

* *Comment configurer des propriétés supplémentaires pour le sous-réseau développé lors de la création du cluster AKS ? Par exemple, des points de terminaison du service.*

  La liste complète des propriétés pour le réseau virtuel et les sous- réseaux développés durant la création du cluster AKS peut être configurée dans la page de configuration du réseau virtuel standard du portail Azure.

* *Puis-je utiliser un autre sous-réseau dans le réseau virtuel de Mon cluster pour la* **plage d’adresses du service Kubernetes** ?

  Cette configuration, bien que possible, n’est pas recommandée. La plage d’adresses du service est un jeu d’adresses IP virtuelles que Kubernetes attribue aux services internes dans votre cluster. Azure Networking ne peut pas voir la plage d’adresses IP de service du cluster Kubernetes. En raison de ce manque de visibilité, il est possible de créer ultérieurement un sous-réseau dans le réseau virtuel du cluster, qui chevauche la plage d’adresses de service. Si un chevauchement de ce type se produit, Kubernetes peut affecter à un service une adresse IP déjà utilisée par une autre ressource dans le sous-réseau, ce qui provoque un comportement imprévisible ou des échecs. Utilisez une plage d’adresses en dehors du réseau virtuel du cluster pour éviter tout risque de chevauchement.

### <a name="dynamic-allocation-of-ip-addresses-and-enhanced-subnet-support-faqs"></a>FAQ sur l’allocation dynamique d’adresses IP et la prise en charge de sous-réseau améliorée

Les questions et réponses suivantes s’appliquent à la **Configuration réseau d’Azure CNI lors de l’utilisation de l’allocation dynamique d’adresses IP et de la prise en charge de sous-réseau améliorée**.

* *Puis-je attribuer plusieurs sous-réseaux de pod à un cluster/pool de nœuds ?*

  Vous ne pouvez attribuer qu’un seul sous-réseau à un cluster ou pool de nœuds. Toutefois, plusieurs clusters ou pools de nœuds peuvent partager un même sous-réseau.

* *Puis-je attribuer des sous-réseaux de pod entièrement à partir d’un autre réseau virtuel ?*

  Le sous-réseau de pod doit provenir du même réseau virtuel que le cluster.  

* *Certains pools de nœuds dans un cluster peuvent-ils utiliser la CNI traditionnelle, tandis que d’autres utilisent la nouvelle CNI ?*

  Le cluster tout entier ne doit utiliser qu’un seul type de CNI.

## <a name="aks-engine"></a>Moteur AKS

Le [moteur Azure Kubernetes Service (moteur AKS)][aks-engine] est un projet open source générant des modèles Azure Resource Manager que vous pouvez utiliser pour déployer des clusters Kubernetes sur Azure.

Les clusters Kubernetes créés avec le moteur AKS prennent en charge les plug-ins [kubenet][kubenet] et [Azure CNI][cni-networking]. En l’état, les deux scénarios de réseau sont pris en charge par le moteur AKS.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la mise en réseau dans AKS, consultez les articles suivants :

* [Utiliser une adresse IP statique avec l’équilibrage de charge d’Azure Kubernetes Service (AKS)](static-ip.md)
* [Utiliser un équilibreur de charge interne avec Azure Container Service (AKS)](internal-lb.md)

* [Créer un contrôleur d’entrée dans Azure Kubernetes Service (AKS)][aks-ingress-basic]
* [Routage d’applications HTTP][aks-http-app-routing]
* [Create an ingress controller to an internal virtual network in Azure Kubernetes Service (AKS)][aks-ingress-internal] (Créer un contrôleur d’entrée pour un réseau virtuel interne dans Azure Kubernetes Service (AKS))
* [Create an HTTPS ingress controller on Azure Kubernetes Service (AKS)][aks-ingress-tls] (Créer un contrôleur d’entrée HTTPS dans Azure Kubernetes Service (AKS))
* [Create an ingress controller with a static public IP address in Azure Kubernetes Service (AKS)][aks-ingress-static-tls] (Créer un contrôleur d’entrée avec une adresse IP publique statique dans Azure Kubernetes Service (AKS))
<!-- IMAGES -->
[advanced-networking-diagram-01]: ./media/networking-overview/advanced-networking-diagram-01.png [portal-01-networking-advanced]: ./media/networking-overview/portal-01-networking-advanced.png

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[services]: https://kubernetes.io/docs/concepts/services-networking/service/
[portal]: https://portal.azure.com
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet

<!-- LINKS - Internal -->
[az-aks-create]: /cli/azure/aks#az_aks_create
[aks-ssh]: ssh.md
[ManagedClusterAgentPoolProfile]: /azure/templates/microsoft.containerservice/managedclusters#managedclusteragentpoolprofile-object
[aks-network-concepts]: concepts-network.md
[aks-network-nsg]: concepts-network.md#network-security-groups
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-internal]: ingress-internal-ip.md
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[network-policy]: use-network-policies.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[network-comparisons]: concepts-network.md#compare-network-models
[system-node-pools]: use-system-pools.md
[prerequisites]: configure-azure-cni.md#prerequisites
