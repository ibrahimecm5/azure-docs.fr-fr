---
title: 'Tutoriel : Créer un équilibreur de charge de passerelle - Azure CLI'
titleSuffix: Azure Load Balancer
description: Utilisez ce tutoriel pour apprendre à créer un équilibreur de charge de passerelle à l’aide d’Azure CLI.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: tutorial
ms.date: 11/02/2021
ms.custom: template-tutorial, ignite-fall-2021
ms.openlocfilehash: 13435307985f3471800a6bfc3697c7bc0fc58ee1
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131097718"
---
# <a name="tutorial-create-a-gateway-load-balancer-using-the-azure-cli"></a>Tutoriel : Créer un équilibreur de charge de passerelle à l’aide d’Azure CLI

Azure Load Balancer se compose de SKUs Standard, Basic et Gateway. L'équilibreur de charge de passerelle est utilisé pour l'insertion transparente d'appareils virtuels de réseau (NVA)(appliance virtuelle réseau). Utilisez Load Balancer de passerelle pour les scénarios qui requièrent des performances élevées et une haute évolutivité de Appliances virtuelles réseau.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Inscrire la fonctionnalité de préversion
> * Créez un réseau virtuel.
> * Créer un groupe de sécurité réseau.
> * Créez un équilibreur de charge de passerelle.
> * Chaînez un serveur frontal de l’équilibreur de charge à l’équilibreur de charge de la passerelle.

> [!IMPORTANT]
> Azure Gateway Load Balancer est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ce tutoriel nécessite l’interface Azure CLI version 2.0.28 ou ultérieure. Si vous utilisez Azure Cloud Shell, la version la plus récente est déjà installée.

- Un compte Azure avec un abonnement actif : [créez gratuitement un compte](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- Une référence SKU standard publique existante Azure Load Balancer. Pour plus d’informations sur la création d’un équilibreur de charge, consultez **[Créer un équilibreur de charge public à l’aide d’Azure CLI](quickstart-load-balancer-standard-public-cli.md)** .
    - Dans le cadre de ce tutoriel, l’équilibreur de charge existant dans les exemples est nommé **myLoadBalancer**.

## <a name="register-preview-feature"></a>Inscrire la fonctionnalité d’évaluation

Dans le cadre de la préversion publique de l’équilibreur de charge de la passerelle, le fournisseur doit être inscrit dans votre abonnement Azure.

Utilisez [az feature register](/cli/azure/feature#az_feature_register) pour enregistrer la fonctionnalité du fournisseur **AllowGatewayLoadBalancer** :

```azurecli-interactive
  az feature register \
    --name AllowGatewayLoadBalancer \
    --namespace Microsoft.Network
```

Utilisez [Register-AzResourceProvider](/cli/azure/provider#az_provider_register) pour enregistrer le fournisseur de ressources **Microsoft.Network** :

```azurecli-interactive
  az provider register \
    --namespace Microsoft.Network
```

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées.

Créez un groupe de ressources avec la commande [az group create](/cli/azure/group#az_group_create) :

```azurecli-interactive
  az group create \
    --name TutorGwLB-rg \
    --location eastus

```

## <a name="configure-virtual-network"></a>Configurer un réseau virtuel

Un réseau virtuel est nécessaire pour les ressources qui se trouvent dans le pool backend de l'équilibreur de charge de la passerelle.  

### <a name="create-virtual-network"></a>Création d’un réseau virtuel

Utilisez la commande [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) pour créer le réseau virtuel.

```azurecli-interactive
  az network vnet create \
    --resource-group TutorGwLB-rg \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```

### <a name="create-bastion-public-ip-address"></a>Créer une adresse IP publique Bastion

Utilisez [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) pour créer une IP publique pour l’hôte Azure Bastion.

```azurecli-interactive
az network public-ip create \
    --resource-group TutorGwLB-rg \
    --name myBastionIP \
    --sku Standard \
    --zone 1 2 3
```

### <a name="create-bastion-subnet"></a>Créer un sous-réseau Bastion

Utilisez [az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) pour créer le sous-réseau Bastion.

```azurecli-interactive
az network vnet subnet create \
    --resource-group TutorGwLB-rg \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/27
```

### <a name="create-bastion-host"></a>Créer un hôte bastion

Utilisez [az network bastion create](/cli/azure/network/bastion#az_network_bastion_create) pour déployer un hôte bastion pour une gestion sécurisée des ressources dans le réseau virtuel.

```azurecli-interactive
az network bastion create \
    --resource-group TutorGwLB-rg \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

Le déploiement de l’hôte Azure Bastion peut prendre quelques minutes.

## <a name="configure-nsg"></a>Configurer le groupe de sécurité réseau

L’exemple suivant permet de créer un groupe de sécurité réseau nommé : Vous allez configurer les règles NSG nécessaires pour le trafic réseau dans le réseau virtuel créé précédemment.

### <a name="create-nsg"></a>Création d’un groupe de sécurité réseau

Utilisez [az network nsg create](/cli/azure/network/nsg#az_network_nsg_create) pour créer le groupe de sécurité réseau.

```azurecli-interactive
  az network nsg create \
    --resource-group TutorGwLB-rg \
    --name myNSG
```

### <a name="create-nsg-rules"></a>Créer les règles du groupe de sécurité réseau

Utilisez [az network nsg rule create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create) pour créer des règles pour le groupe de sécurité réseau.

```azurecli-interactive
  az network nsg rule create \
    --resource-group TutorGwLB-rg \
    --nsg-name myNSG \
    --name myNSGRule-AllowAll \
    --protocol '*' \
    --direction inbound \
    --source-address-prefix '0.0.0.0/0' \
    --source-port-range '*' \
    --destination-address-prefix '0.0.0.0/0' \
    --destination-port-range '*' \
    --access allow \
    --priority 100

  az network nsg rule create \
    --resource-group TutorGwLB-rg \
    --nsg-name myNSG \
    --name myNSGRule-AllowAll-TCP-Out \
    --protocol 'TCP' \
    --direction outbound \
    --source-address-prefix '0.0.0.0/0' \
    --source-port-range '*' \
    --destination-address-prefix '0.0.0.0/0' \
    --destination-port-range '*' \
    --access allow \
    --priority 100
```

## <a name="configure-gateway-load-balancer"></a>Configurer un équilibreur de charge de passerelle

Dans cette section, vous allez créer la configuration et déployer l’équilibrage de charge de la passerelle.  

### <a name="create-gateway-load-balancer"></a>Créez un équilibreur de charge de passerelle.

Pour créer l’équilibreur de charge, utilisez la commande [az network lb create](/cli/azure/network/lb#az_network_lb_create).

```azurecli-interactive
  az network lb create \
    --resource-group TutorGwLB-rg \
    --name myLoadBalancer-gw \
    --sku Gateway \
    --vnet-name myVNet \
    --subnet myBackendSubnet \
    --backend-pool-name myBackendPool \
    --frontend-ip-name myFrontEnd
```

### <a name="create-tunnel-interface"></a>Créer une interface de tunnel

Une interface interne est créée automatiquement avec Azure CLI avec **`--identifier`** défini sur **900** et **`--port`** défini sur **10800**.

Vous allez utiliser la commande [az network lb address-pool tunnel-interface add](/cli/azure/network/lb/address-pool/tunnel-interface#az_network_lb_address_pool_tunnel_interface_add) pour créer une interface de tunnel externe pour l’équilibreur de charge. 

```azurecli-interactive
  az network lb address-pool tunnel-interface add \
    --address-pool myBackEndPool \
    --identifier '901' \
    --lb-name myLoadBalancer-gw \
    --protocol VXLAN \
    --resource-group TutorGwLB-rg \
    --type External \
    --port '10801'
```

### <a name="create-health-probe"></a>Créer une sonde d’intégrité
Une sonde d’intégrité est nécessaire pour surveiller l’intégrité des instances de serveur principal dans l’équilibreur de charge. Utilisez la commande [az network lb probe create](/cli/azure/network/lb/probe#az_network_lb_probe_create) pour créer la sonde d’intégrité.

```azurecli-interactive
  az network lb probe create \
    --resource-group TutorGwLB-rg \
    --lb-name myLoadBalancer-gw \
    --name myHealthProbe \
    --protocol http \
    --port 80 \
    --path '/' \
    --interval '5' \
    --threshold '2'
    
```

### <a name="create-load-balancing-rule"></a>Créer une règle d’équilibrage de charge

Le trafic destiné aux instances principales est routé avec une règle d’équilibrage de charge. Utilisez [az network lb rule create](/cli/azure/network/lb/probe#az_network_lb_rule_create) pour créer la règle d’équilibrage de charge.

```azurecli-interactive
  az network lb rule create \
    --resource-group TutorGwLB-rg \
    --lb-name myLoadBalancer-gw \
    --name myLBRule \
    --protocol All \
    --frontend-port 0 \
    --backend-port 0 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe
```

## <a name="add-network-virtual-appliances-to-the-gateway-load-balancer-backend-pool"></a>Ajouter des appliances virtuelles réseau à la passerelle Load Balancer pool backend
Déployez Appliances virtuelles réseau via la place de marché Azure. Une fois le déploiement effectué, ajoutez les machines virtuelles au pool de back-ends avec la commande [az network nic ip-config address-pool add](/cli/azure/network/nic/ip-config/address-pool#az_network_nic_ip_config_address_pool_add).

## <a name="chain-load-balancer-frontend-to-gateway-load-balancer"></a>Chaîner un serveur frontal de l’équilibreur de charge à l’équilibreur de charge de passerelle

Dans cet exemple, vous allez chaîner le serveur frontal d’un équilibreur de charge standard à l’équilibreur de charge de la passerelle. 

Vous allez ajouter le serveur frontal à l’adresse IP frontale d’un équilibreur de charge existant dans votre abonnement.

Utilisez [az network lb frontend-ip show](/cli/azure/network/lb/frontend-ip#az_az_network_lb_frontend_ip_show) pour placer l’ID de ressource de votre serveur frontal de l’équilibreur de charge de passerelle dans une variable.

Utilisez [az network lb frontend-ip update](/cli/azure/network/lb/frontend-ip#az_network_lb_frontend_ip_update) pour chaîner le serveur frontal de l’équilibreur de charge de la passerelle à votre équilibreur de charge existant.

```azurecli-interactive
  feid=$(az network lb frontend-ip show \
    --resource-group TutorGwLB-rg \
    --lb-name myLoadBalancer-gw \
    --name myFrontend \
    --query id \
    --output tsv)

  az network lb frontend-ip update \
    --resource-group CreatePubLBQS-rg \
    --name myFrontendIP \
    --lb-name myLoadBalancer \
    --public-ip-address myPublicIP \
    --gateway-lb $feid

```

## <a name="clean-up-resources"></a>Nettoyer les ressources

Lorsque vous n’en avez plus besoin, vous pouvez utiliser la commande [az group delete](/cli/azure/group#az_group_delete) pour supprimer le groupe de ressources, l’équilibreur de charge et les ressources restantes.

```azurecli-interactive
  az group delete \
    --name TutorGwLB-rg
```

## <a name="next-steps"></a>Étapes suivantes

Créer des appliances virtuelles réseau dans Azure. 

Lorsque vous créez le Appliances virtuelles réseau, choisissez les ressources créées dans ce tutoriel :

* Réseau virtuel

* Subnet

* Groupe de sécurité réseau

* Équilibreur de charge de passerelle

Passez à l’article suivant pour découvrir comment créer un équilibreur de charge Azure interrégional :
> [!div class="nextstepaction"]
> [Équilibreur de charge inter-région](tutorial-cross-region-powershell.md)
