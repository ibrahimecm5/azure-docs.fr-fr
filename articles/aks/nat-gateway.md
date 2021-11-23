---
title: NAT Gateway managé (préversion)
description: Découvrez comment créer un cluster AKS avec l’intégration NAT managée
services: container-service
ms.topic: article
ms.date: 10/26/2021
ms.author: juda
ms.openlocfilehash: 87edce54391661fe986365ad45d72bfeee41c761
ms.sourcegitcommit: c434baa76153142256d17c3c51f04d902e29a92e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132179609"
---
# <a name="managed-nat-gateway-preview"></a>NAT Gateway managé (préversion)

Même si les clients AKS sont en mesure d’acheminer le trafic de sortie via un équilibreur de charge Azure Load Balancer, il existe des limites quant à la quantité de flux de trafic sortants possible. 

La passerelle Azure NAT Gateway autorise jusqu’à 64 000 flux de trafic UDP et TCP sortants par adresse IP, avec un maximum de 16 adresses IP.

Cet article vous montre comment créer un cluster AKS avec une passerelle NAT Gateway managée pour le trafic sortant.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="before-you-begin"></a>Avant de commencer

Pour utiliser la passerelle NAT managée, vous devez disposer des éléments suivants :

* La version la plus récente d’Azure CLI
* L’extension `aks-preview` version 0.5.31 ou ultérieure
* Kubernetes version 1.20.x ou ultérieure


### <a name="register-the-aks-natgatewaypreview-feature-flag"></a>Inscrire l’indicateur de fonctionnalité `AKS-NATGatewayPreview`

Pour utiliser la fonctionnalité de NAT Gateway, vous devez activer l’indicateur de fonctionnalité `AKS-NATGatewayPreview` sur votre abonnement. 

```azurecli
az feature register --namespace "Microsoft.ContainerService" --name "AKS-NATGatewayPreview"
```
Vous pouvez vérifier l’état de l’enregistrement à l’aide de la commande [az feature list][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-NATGatewayPreview')].{Name:name,State:properties.state}"
```

Lorsque vous êtes prêt, actualisez l’inscription du fournisseur de ressources *Microsoft.ContainerService* à l’aide de la commande [az provider register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```


## <a name="create-an-aks-cluster-with-a-managed-nat-gateway"></a>Créer un cluster AKS avec une passerelle NAT Gateway managée
Pour créer un cluster AKS avec une nouvelle passerelle NAT Gateway managée, utilisez `--outbound-type managedNATGateway`, ainsi que `--nat-gateway-managed-outbound-ip-count` et `--nat-gateway-idle-timeout`, lors de l’exécution de `az aks create`. L’exemple suivant crée un groupe de ressources *myresourcegroup*, puis crée un cluster *natcluster* AKS dans *myresourcegroup* avec une passerelle NAT Gateway managée, deux adresses IP sortantes et un délai d’inactivité de 30 secondes.


```azurecli-interactive
az group create --name myresourcegroup --location southcentralus
```

```azurecli-interactive
az aks create \
    --resource-group myresourcegroup \
    --name natcluster \
    --node-count 3 \
    --outbound-type managedNATGateway \ 
    --nat-gateway-managed-outbound-ip-count 2 \
    --nat-gateway-idle-timeout 30
```

> [!IMPORTANT]
> Si aucune valeur n’est spécifiée pour l’adresse IP sortante, la valeur par défaut est 1.

### <a name="update-the-number-of-outbound-ip-addresses"></a>Mettre à jour le nombre d’adresses IP sortantes
Pour mettre à jour l’adresse IP sortante ou le délai d’inactivité, utilisez `--nat-gateway-managed-outbound-ip-count` ou `--nat-gateway-idle-timeout` lors de l’exécution de `az aks update`. Par exemple :

```azurecli-interactive
az aks update \ 
    --resource-group myresourcegroup \
    --name natcluster\
    --nat-gateway-managed-outbound-ip-count 5
```

## <a name="create-an-aks-cluster-with-a-user-assigned-nat-gateway"></a>Créer un cluster AKS avec une passerelle NAT affectée par l’utilisateur
Pour créer un cluster AKS avec une passerelle NAT affectée par l’utilisateur, utilisez `--outbound-type userAssignedNATGateway` lors de l’exécution de `az aks create`. Cette configuration nécessite une mise en réseau personnalisée (par le biais de [Kubenet][byo-vnet-kubenet] ou d’[Azure CNI][byo-vnet-azure-cni]) et que la passerelle NAT soit préconfigurée sur le sous-réseau. Les commandes suivantes créent les ressources requises pour ce scénario. Veillez à les exécuter toutes dans la même session afin que les valeurs stockées dans les variables soient toujours disponibles pour la commande `az aks create`.

1. Créez le groupe de ressources :
    ```azurecli-interactive
    az group create --name myresourcegroup \
        --location southcentralus
    ```

2. Créez une identité managée pour les autorisations réseau et stockez l’ID dans `$IDENTITY_ID` pour une utilisation ultérieure :
    ```azurecli-interactive
    IDENTITY_ID=$(az identity create \
        --resource-group myresourcegroup \
        --name natclusterid \
        --location southcentralus \
        --query id \
        --output tsv)
    ```

3. Créez une adresse IP publique pour la passerelle NAT :
    ```azurecli-interactive
    az network public-ip create \
        --resource-group myresourcegroup \
        --name mynatgatewaypip \
        --location southcentralus \
        --sku standard
    ```

4. Créer la passerelle NAT :
    ```azurecli-interactive
    az network nat gateway create \
        --resource-group myresourcegroup \
        --name mynatgateway \
        --location southcentralus \
        --public-ip-addresses mynatgatewaypip
    ```

5. Créez un réseau virtuel :
    ```azurecli-interactive
    az network vnet create \
        --resource-group myresourcegroup \
        --name myvnet \
        --location southcentralus \
        --address-prefixes 172.16.0.0/20 
    ```

6. Créez un sous-réseau dans le réseau virtuel à l’aide de la passerelle NAT et stockez l’ID dans `$SUBNET_ID` pour une utilisation ultérieure :
    ```azurecli-interactive
    SUBNET_ID=$(az network vnet subnet create \
        --resource-group myresourcegroup \
        --vnet-name myvnet \
        --name natcluster \
        --address-prefixes 172.16.0.0/22 \
        --nat-gateway mynatgateway \
        --query id \
        --output tsv)
    ```

7. Créez un cluster AKS à l’aide du sous-réseau avec la passerelle NAT et l’identité managée :
    ```azurecli-interactive
    az aks create \
        --resource-group myresourcegroup \
        --name natcluster \
        --location southcentralus \
        --network-plugin azure \
        --vnet-subnet-id $SUBNET_ID \
        --outbound-type userAssignedNATGateway \
        --enable-managed-identity \
        --assign-identity $IDENTITY_ID
    ```

## <a name="next-steps"></a>Étapes suivantes
- Pour plus d’informations sur la passerelle NAT Gateway, consultez la documentation relative à [Azure NAT Gateway][nat-docs].

<!-- LINKS - internal -->


<!-- LINKS - external-->
[nat-docs]: ../virtual-network/nat-gateway/nat-overview.md
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[byo-vnet-azure-cni]: configure-azure-cni.md
[byo-vnet-kubenet]: configure-kubenet.md