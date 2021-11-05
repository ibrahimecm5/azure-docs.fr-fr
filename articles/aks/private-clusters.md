---
title: Créer un cluster Azure Kubernetes Service privé
description: Découvrez comment créer un cluster Azure Kubernetes Service (AKS) privé
services: container-service
ms.topic: article
ms.date: 8/30/2021
ms.openlocfilehash: 3cb83bd9b3aded2ab167afb39b024266a76163ae
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131049080"
---
# <a name="create-a-private-azure-kubernetes-service-cluster"></a>Créer un cluster Azure Kubernetes Service privé

Dans un cluster privé, le plan de contrôle ou le serveur d’API a des adresses IP internes qui sont définies dans le document [RFC1918 - Address Allocation for Private Internets](https://tools.ietf.org/html/rfc1918) (RFC1918 - Allocation d’adresses pour les réseaux Internet privés). En utilisant un cluster privé, vous pouvez vous assurer que le trafic réseau entre votre serveur d’API et vos pools de nœuds reste sur le réseau privé uniquement.

Le plan de contrôle ou le serveur d’API se trouve dans un abonnement Azure géré par Azure Kubernetes Service (AKS). Le cluster ou le pool de nœuds d’un client se trouve dans l’abonnement du client. Le serveur et le cluster ou le pool de nœuds peuvent communiquer entre eux par le biais du [service Azure Private Link][private-link-service] dans le réseau virtuel du serveur d’API et d’un point de terminaison privé exposé dans le sous-réseau du cluster AKS du client.

## <a name="region-availability"></a>Disponibilité des régions

Le cluster privé est disponible dans les régions public Azure Government et Azure China 21Vianet où [AKS est pris en charge](https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service).

> [!NOTE]
> Les sites Azure Government sont pris en charge. Cependant, US Gov Texas n’est pas pris en charge pour le moment, en raison de l’absence de prise en charge du service Liaison privée.

## <a name="prerequisites"></a>Prérequis

* Azure CLI version 2.2.0 ou ultérieure
* Le service Liaison privée est pris en charge uniquement sur Azure Load Balancer Standard. Azure Load Balancer De base n’est pas pris en charge.  
* Pour utiliser un serveur DNS personnalisé, ajoutez l’IP Azure DNS 168.63.129.16 en tant que serveur DNS en amont dans le serveur DNS personnalisé.

## <a name="create-a-private-aks-cluster"></a>Créer un cluster AKS privé

### <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources ou utilisez un groupe de ressources existant pour votre cluster AKS.

```azurecli-interactive
az group create -l westus -n MyResourceGroup
```

### <a name="default-basic-networking"></a>Mise en réseau de base par défaut 

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster  
```
Où `--enable-private-cluster` est un indicateur obligatoire pour un cluster privé. 

### <a name="advanced-networking"></a>Mise en réseau avancée  

```azurecli-interactive
az aks create \
    --resource-group <private-cluster-resource-group> \
    --name <private-cluster-name> \
    --load-balancer-sku standard \
    --enable-private-cluster \
    --network-plugin azure \
    --vnet-subnet-id <subnet-id> \
    --docker-bridge-address 172.17.0.1/16 \
    --dns-service-ip 10.2.0.10 \
    --service-cidr 10.2.0.0/24 
```
Où `--enable-private-cluster` est un indicateur obligatoire pour un cluster privé. 

> [!NOTE]
> Si le CIDR d’adresse de pont Docker (172.17.0.1/16) est en conflit avec le CIDR de sous-réseau, changez l’adresse de pont Docker en conséquence.

## <a name="configure-private-dns-zone"></a>Configurer une zone de DNS privé 

Les paramètres suivants peuvent être utilisés pour configurer une zone de DNS privé.

- « system », qui correspond aussi à la valeur par défaut. Si l’argument --private-dns-zone est omis, AKS crée une zone de DNS privé dans le groupe de ressources du nœud.
- « none », valeur par défaut pour le DNS public qui signifie que AKS ne crée pas de zone DNS privé.  
- « CUSTOM_PRIVATE_DNS_ZONE_RESOURCE_ID », qui vous oblige à créer une zone DNS privée au format suivant pour le cloud global Azure : `privatelink.<region>.azmk8s.io`. À partir de maintenant, vous allez avoir besoin de l’ID de ressource de la zone DNS privée.  De plus, vous aurez besoin d’une identité affectée par l’utilisateur ou d’un principal de service disposant au minimum des rôles `private dns zone contributor` et `vnet contributor`.
  - Si la zone DNS privé se trouve dans un autre abonnement que le cluster AKS, vous devez inscrire Microsoft.ContainerServices dans les deux abonnements.
  - « fqdn-subdomain » peut être utilisé avec « CUSTOM_PRIVATE_DNS_ZONE_RESOURCE_ID » uniquement pour fournir des fonctionnalités de sous-domaine à `privatelink.<region>.azmk8s.io`

### <a name="create-a-private-aks-cluster-with-private-dns-zone"></a>Créer un cluster AKS privé avec zone DNS privé

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster --enable-managed-identity --assign-identity <ResourceId> --private-dns-zone [system|none]
```
### <a name="create-a-private-aks-cluster-with-a-byo-private-dns-subzone-preview"></a>Créer un cluster AKS privé avec sous-zone DNS privée BYO (préversion)

Configuration requise :

* Azure CLI >= 2.29.0 ou Azure CLI avec l’extension aks-preview 0.5.34 ou version ultérieure.

### <a name="register-the-enableprivateclustersubzone-preview-feature"></a>Inscrire la fonctionnalité d’évaluation `EnablePrivateClusterSubZone`

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

Pour créer un cluster AKS capable d’utiliser le pilote CSI de magasin de secrets, vous devez activer l’indicateur de fonctionnalité `EnablePrivateClusterSubZone` sur votre abonnement.

Inscrivez l’indicateur de fonctionnalité `EnablePrivateClusterSubZone` à l’aide de la commande [az feature register][az-feature-register], comme indiqué dans l’exemple suivant :

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "EnablePrivateClusterSubZone"
```

Quelques minutes sont nécessaires pour que l’état s’affiche *Registered* (Inscrit). Vérifiez l’état de l’inscription à l’aide de la commande [az feature list][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnablePrivateClusterSubZone')].{Name:name,State:properties.state}"
```

Lorsque vous êtes prêt, actualisez l’inscription du fournisseur de ressources *Microsoft.ContainerService* à l’aide de la commande [az provider register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="install-the-aks-preview-cli-extension"></a>Installer l’extension CLI aks-preview

Vous devez également disposer de l’extension Azure CLI *aks-preview* version 0.5.34 ou ultérieure. Installez l’extension d’Azure CLI *aks-preview* à l’aide de la commande [az extension add][az-extension-add]. Si vous avez déjà installé l’extension, passez à la dernière version disponible avec la commande [az extension update][az-extension-update].

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="private-aks-cluster-with-byo-private-dns-subzone"></a>Cluster AKS privé avec sous-zone DNS privé BYOD

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster --enable-managed-identity --assign-identity <ResourceId> --private-dns-zone <BYO private dns zone ResourceId>
```

### <a name="create-a-private-aks-cluster-with-custom-private-dns-subzone"></a>Créer un cluster AKS privé avec sous-zone DNS privé personnalisée

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster --enable-managed-identity --assign-identity <ResourceId> --private-dns-zone <custom private dns zone ResourceId> --fqdn-subdomain <subdomain-name>
```

### <a name="create-a-private-aks-cluster-with-a-public-fqdn"></a>Créer un cluster AKS privé avec un nom de domaine complet public (FQDN) public

Configuration requise :

* Azure CLI >= 2.28.0 ou Azure CLI avec l’extension aks-preview 0.5.29 ou version ultérieure.
* Si vous utilisez ARM ou l’API REST, la version de l’API AKS doit être 2021-05-01 ou une version ultérieure.

L’option DNS public peut être utilisée pour simplifier les options de routage pour votre cluster privé.  

![DNS public](https://user-images.githubusercontent.com/50749048/124776520-82629600-df0d-11eb-8f6b-71c473b6bd01.png)

1. Lorsque vous approvisionnez un cluster AKS privé, AKS par défaut crée un nom de domaine complet public supplémentaire et un enregistrement A correspondant dans le DNS public Azure. Les nœuds d’agent utilisent encore un enregistrement A dans la zone DNS privé pour résoudre l’adresse IP privée du point de terminaison privé pour la communication avec le serveur d’API.

2. Si vous utilisez `--private-dns-zone none`, le cluster n’aura qu’un FQDN public. Quand vous utilisez cette option, aucune zone DNS privé n’est créée ni utilisée pour la résolution de noms du nom de domaine complet du serveur d’API. L’IP de l’API est toujours privé et ne peut pas être routé publiquement.

3. Si le FQDN public n’est pas souhaité, vous pouvez utiliser `--disable-public-fqdn` pour le désactiver (la zone DNS privée « none » n’est pas autorisée à désactiver le FQDN public).

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster --enable-managed-identity --assign-identity <ResourceId> --private-dns-zone <private-dns-zone-mode> --disable-public-fqdn
az aks update -n <private-cluster-name> -g <private-cluster-resource-group> --disable-public-fqdn
```

## <a name="options-for-connecting-to-the-private-cluster"></a>Options de connexion au cluster privé

Le point de terminaison du serveur d’API n’a pas d’adresse IP publique. Pour gérer le serveur d’API, vous devez utiliser une machine virtuelle qui a accès au réseau virtuel (VNet) Azure du cluster AKS. Il existe plusieurs options pour établir une connectivité réseau avec le cluster privé.

* Créez une machine virtuelle dans le même réseau virtuel Azure (VNet) que le cluster AKS.
* Utilisez une machine virtuelle dans un réseau distinct et configurez l'[appairage de réseaux virtuels][virtual-network-peering].  Pour plus d'informations sur cette option, consultez la section ci-dessous.
* Utilisez une [connexion ExpressRoute ou VPN][express-route-or-VPN].
* Utilisez la [fonctionnalité Run Command AKS](#aks-run-command).

La création d’une machine virtuelle dans le même réseau virtuel que le cluster AKS constitue l’option la plus simple.  ExpressRoute et les VPN présentent des coûts et une complexité de mise en réseau supplémentaires.  L’appairage de réseaux virtuels implique la planification de vos plages CIDR réseau pour veiller à ce qu'aucune plage ne se chevauche.

### <a name="aks-run-command"></a>Run Command AKS

Aujourd’hui, lorsque vous avez besoin d’accéder à un cluster privé, vous devez le faire au sein du réseau virtuel du cluster ou d’un réseau ou d’un ordinateur client homologué. En règle générale, votre machine doit être connectée via un VPN ou Express Route au réseau virtuel du cluster ou à un JumpBox à créer dans le réseau virtuel du cluster. La commande d’exécution AKS vous permet d’appeler des commandes à distance dans un cluster AKS par le biais de l’API AKS. Cette fonctionnalité fournit une API qui vous permet, par exemple, d’exécuter des commandes juste-à-temps à partir d’un ordinateur portable distant pour un cluster privé. Cela peut vous aider à obtenir un accès juste à temps rapide à un cluster privé lorsque la machine client ne se trouve pas sur le réseau privé du cluster tout en conservant et renforçant les mêmes contrôles RBAC et le même serveur d’API privé.

### <a name="prerequisites"></a>Prérequis

* Azure CLI version 2.24.0 ou ultérieure

### <a name="use-aks-run-command"></a>Utiliser Run Command AKS

Commande simple

```azurecli-interactive
az aks command invoke -g <resourceGroup> -n <clusterName> -c "kubectl get pods -n kube-system"
```

Déployer un manifeste en joignant le fichier spécifique

```azurecli-interactive
az aks command invoke -g <resourceGroup> -n <clusterName> -c "kubectl apply -f deployment.yaml -n default" -f deployment.yaml
```

Déployer un manifeste en joignant un dossier entier

```azurecli-interactive
az aks command invoke -g <resourceGroup> -n <clusterName> -c "kubectl apply -f deployment.yaml -n default" -f .
```

Effectuer une installation Helm et passer le manifeste des valeurs spécifiques

```azurecli-interactive
az aks command invoke -g <resourceGroup> -n <clusterName> -c "helm repo add bitnami https://charts.bitnami.com/bitnami && helm repo update && helm install my-release -f values.yaml bitnami/nginx" -f values.yaml
```
> [!NOTE]
> Sécurisez l’accès à Run Command AKS en créant un rôle personnalisé avec les autorisations « Microsoft.ContainerService/managedClusters/runcommand/action » et « Microsoft.ContainerService/managedclusters/commandResults/read » et attribuez-le à des utilisateurs ou des groupes spécifiques avec un accès juste-à-temps ou des stratégies d’accès conditionnel. 

## <a name="virtual-network-peering"></a>Peering de réseau virtuel

Comme indiqué, l’appairage de réseaux virtuels est un moyen d’accéder à votre cluster privé. Pour utiliser l’appairage de réseaux virtuels, vous devez configurer une liaison entre le réseau virtuel et la zone DNS privée.
    
1. Accédez au groupe de ressources du nœud dans le portail Azure.  
2. Sélectionnez la zone DNS privée.   
3. Dans le volet de gauche, sélectionnez le lien **Réseau virtuel**.  
4. Créez un lien permettant d’ajouter le réseau virtuel de la machine virtuelle à la zone DNS privée. Il faut quelques minutes pour que le lien de zone DNS soit disponible.  
5. Dans le portail Azure, accédez au groupe de ressources contenant le réseau virtuel de votre cluster.  
6. Dans le volet de droite, sélectionnez le réseau virtuel. Le nom du réseau virtuel se présente au format *aks-vnet-\** .  
7. Dans le volet de gauche, sélectionnez **Appairages**.  
8. Sélectionnez **Ajouter**, ajoutez le réseau virtuel de la machine virtuelle, puis créez l’appairage.  
9. Accédez au réseau virtuel sur lequel se trouve la machine virtuelle, sélectionnez **Appairages**, sélectionnez le réseau virtuel AKS, puis créez l’appairage. Si les plages d’adresses sur le réseau virtuel AKS et le réseau virtuel de la machine virtuelle sont en conflit, l’appairage échoue. Pour plus d’informations, consultez [Appairage de réseaux virtuels][virtual-network-peering].

## <a name="hub-and-spoke-with-custom-dns"></a>Hub-and-spoke avec DNS personnalisé

[Les architectures Hub-and-spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) sont couramment utilisées pour déployer des réseaux dans Azure. Dans la plupart de ces déploiements, les paramètres DNS des réseaux virtuels spoke sont configurés pour faire référence à un redirecteur DNS central afin d’autoriser la résolution DNS locale et basée sur Azure. Lors du déploiement d’un cluster AKS dans un environnement de mise en réseau de ce type, certaines considérations spéciales doivent être prises en compte.

![Hub-and-spoke de cluster privé](media/private-clusters/aks-private-hub-spoke.png)

1. Par défaut, lorsqu’un cluster privé est approvisionné, un point de terminaison privé (1) et une zone DNS privée (2) sont créés dans le groupe de ressources managé par le cluster. Le cluster utilise un enregistrement A dans la zone privée pour résoudre l’adresse IP du point de terminaison privé pour la communication avec le serveur d’API.

2. La zone DNS privée est liée uniquement au réseau virtuel auquel les nœuds de cluster sont attachés (3). Cela signifie que le point de terminaison privé peut uniquement être résolu par les hôtes de ce réseau virtuel lié. Dans les scénarios où aucun DNS personnalisé n’est configuré sur le réseau virtuel (par défaut), cela fonctionne sans problème, car les hôtes pointent vers l’adresse 168.63.129.16 pour le DNS qui peut résoudre les enregistrements dans la zone DNS privée en raison de la liaison.

3. Dans les scénarios où le réseau virtuel contenant votre cluster présente des paramètres DNS personnalisés (4), le déploiement du cluster échoue, sauf si la zone DNS privée est liée au réseau virtuel qui contient les programmes de résolution DNS personnalisés (5). Ce lien peut être créé manuellement après la création de la zone privée lors de l’approvisionnement du cluster ou via l’automatisation lors de la détection de la création de la zone à l’aide de mécanismes de déploiement basés sur les événements (par exemple, Azure Event Grid et Azure Functions).

> [!NOTE]
> Si vous utilisez [Apporter votre propre table de routage avec kubenet](./configure-kubenet.md#bring-your-own-subnet-and-route-table-with-kubenet) et Apporte votre propre DNS avec un cluster privé, la création du cluster échouera. Vous devez associer le [RouteTable](./configure-kubenet.md#bring-your-own-subnet-and-route-table-with-kubenet) dans le groupe de ressources du nœud au sous-réseau après l’échec de la création du cluster, afin que la création réussisse.

## <a name="limitations"></a>Limites 
* Les plages d’adresses IP autorisées ne peuvent pas être appliquées au point de terminaison du serveur d’API privé, elles sont uniquement applicables au serveur d’API public.
* Les [limitations du service Azure Private Link][private-link-service] s’appliquent aux clusters privés.
* Aucune prise en charge des agents hébergés par Microsoft Azure DevOps avec des clusters privés. Envisagez d’utiliser des [agents auto-hébergés](/azure/devops/pipelines/agents/agents?tabs=browser). 
* Pour les clients qui doivent activer Azure Container Registry afin d’utiliser des clusters AKS privés, le réseau virtuel Container Registry doit être appairé avec le réseau virtuel du cluster d’agent.
* Aucune prise en charge de la conversion de clusters AKS existants en clusters privés
* La suppression ou la modification du point de terminaison privé dans le sous-réseau du client entraîne l’arrêt du fonctionnement du cluster. 

<!-- LINKS - internal -->
[az-provider-register]: /cli/azure/provider#az_provider_register
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[private-link-service]: ../private-link/private-link-service-overview.md#limitations
[virtual-network-peering]: ../virtual-network/virtual-network-peering-overview.md
[azure-bastion]: ../bastion/tutorial-create-host-portal.md
[express-route-or-vpn]: ../expressroute/expressroute-about-virtual-network-gateways.md
[devops-agents]: /azure/devops/pipelines/agents/agents
[availability-zones]: availability-zones.md
