---
title: Créer une machine virtuelle Azure avec un réseau double pile – Azure CLI
titleSuffix: Azure Virtual Network
description: Dans cet article, découvrez comment utiliser l’interface Azure CLI pour créer une machine virtuelle avec un réseau virtuel double pile dans Azure.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 11/11/2021
ms.custom: template-how-to
ms.openlocfilehash: ae8f7fef465b8ba42a058b43ca95fd2334f437c8
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132495407"
---
# <a name="create-an-azure-virtual-machine-with-a-dual-stack-network-using-the-azure-cli"></a>Créer une machine virtuelle Azure avec un réseau double pile à l’aide de l’interface Azure CLI

Dans cet article, vous allez créer une machine virtuelle dans Azure avec l’interface Azure CLI. La machine virtuelle est créée en même temps que le réseau double pile dans le cadre des procédures.  Une fois l’opération terminée, la machine virtuelle prend en charge les communications IPv4 et IPv6.  

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez-en un gratuitement](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Ce tutoriel nécessite l’interface Azure CLI version 2.0.28 ou ultérieure. Si vous utilisez Azure Cloud Shell, la version la plus récente est déjà installée.

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées.

Avec la commande [az group create](/cli/azure/group#az_group_create), créez un groupe de ressources nommé **myResourceGroup** dans l’emplacement **eastus2**.

```azurecli-interactive
  az group create \
    --name myResourceGroup \
    --location eastus2
```

## <a name="create-a-virtual-network"></a>Créez un réseau virtuel

Dans cette section, vous allez créer un réseau virtuel double pile pour la machine virtuelle.

Utilisez la commande [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) pour créer un réseau virtuel.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroup \
    --location eastus2 \
    --name myVNet \
    --address-prefixes 10.0.0.0/16 2404:f800:8000:122::/63 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.0.0.0/24 2404:f800:8000:122::/64
```

## <a name="create-public-ip-addresses"></a>Créer des adresses IP publiques

Vous allez créer deux adresses IP publiques dans cette section, IPv4 et IPv6. 

Utilisez la commande [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) pour créer l’adresse IP publique.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroup \
    --name myPublicIP-Ipv4 \
    --sku Standard \
    --version IPv4

  az network public-ip create \
    --resource-group myResourceGroup \
    --name myPublicIP-Ipv6 \
    --sku Standard \
    --version IPv6

```
## <a name="create-a-network-security-group"></a>Créer un groupe de sécurité réseau

Dans cette section, vous allez créer un groupe de sécurité réseau pour la machine virtuelle et le réseau virtuel.

Utilisez la commande [az network nsg create](/cli/azure/network/nsg#az_network_nsg_create) pour créer le groupe de sécurité réseau.

```azurecli-interactive
  az network nsg create \
    --resource-group myResourceGroup \
    --name myNSG
```

### <a name="create-network-security-group-rules"></a>Créer des règles pour le groupe de sécurité réseau

Vous allez créer une règle pour autoriser les connexions à la machine virtuelle sur le port 22 pour le protocole SSH. Une règle supplémentaire est créée pour autoriser tous les ports pour les connexions sortantes.

Utilisez la commande [az network nsg rule create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create) pour créer les règles de groupe de sécurité réseau.

```azurecli-interactive
  az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNSG \
    --name myNSGRuleSSH \
    --protocol '*' \
    --direction inbound \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 22 \
    --access allow \
    --priority 200

  az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNSG \
    --name myNSGRuleAllOUT \
    --protocol '*' \
    --direction outbound \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range '*' \
    --access allow \
    --priority 200
```

## <a name="create-virtual-machine"></a>Créer une machine virtuelle

Dans cette section, vous allez créer la machine virtuelle et ses ressources associées.

### <a name="create-network-interface"></a>Créer une interface réseau

Vous allez utiliser la commande [az network nic create](/cli/azure/network/nic#az_network_nic_create) pour créer l’interface réseau de la machine virtuelle. Les adresses IP publiques et le groupe de sécurité réseau créés précédemment sont associés à la carte d’interface réseau. L’interface réseau est jointe au réseau virtuel que vous avez créé précédemment.

```azurecli-interactive
  az network nic create \
    --resource-group myResourceGroup \
    --name myNIC1 \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG \
    --public-ip-address myPublicIP-IPv4
```

### <a name="create-ipv6-ip-configuration"></a>Créer une configuration IP IPv6

Utilisez la commande [az network nic ip-config create](/cli/azure/network/nic/ip-config#az_network_nic_ip_config_create) pour créer la configuration IPv6 pour la carte d’interface réseau.

```azurecli-interactive
  az network nic ip-config create \
    --resource-group myResourceGroup \
    --name myIPv6config \
    --nic-name myNIC1 \
    --private-ip-address-version IPv6 \
    --vnet-name myVNet \
    --subnet myBackendSubnet \
    --public-ip-address myPublicIP-IPv6
```

### <a name="create-vm"></a>Créer une machine virtuelle

Utilisez la commande [az vm create](/cli/azure/vm#az_vm_create) pour créer la machine virtuelle.

```azurecli-interactive
  az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --nics myNIC1 \
    --image UbuntuLTS \
    --admin-username azureuser \
    --authentication-type ssh \
    --generate-ssh-keys
```

## <a name="test-ssh-connection"></a>Tester la connexion SSH

Utilisez la commande [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show) pour afficher les adresses IP de la machine virtuelle.

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroup \
    --name myPublicIP-IPv4 \
    --query ipAddress \
    --output tsv
```

```bash
user@Azure:~$ az network public-ip show \
>     --resource-group myResourceGroup \
>     --name myPublicIP-IPv4 \
>     --query ipAddress \
>     --output tsv
20.119.201.208
```

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroup \
    --name myPublicIP-IPv6 \
    --query ipAddress \
    --output tsv
```

```bash
user@Azure:~$ az network public-ip show \
>     --resource-group myResourceGroup \
>     --name myPublicIP-IPv6 \
>     --query ipAddress \
>     --output tsv
2603:1030:408:6::9d
```

Ouvrez une connexion SSH à la machine virtuelle à l’aide de la commande suivante. Remplacez l’adresse IP par l’adresse IP de votre machine virtuelle.

```bash
  ssh azureuser@20.119.201.208
```

## <a name="clean-up-resources"></a>Nettoyer les ressources

Quand vous n’avez plus besoin du groupe de ressources, utilisez la commande [az group delete](/cli/azure/group#az_group_delete) pour supprimer ce groupe de ressources, la machine virtuelle, ainsi que toutes les ressources associées.

```azurecli-interactive
  az group delete \
    --name myResourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez découvert comment créer une machine virtuelle Azure avec un réseau double pile.

Pour plus d’informations sur le protocole IPv6 et les adresses IP dans Azure, consultez :

- [Vue d’ensemble du protocole IPv6 pour réseau virtuel Azure.](ipv6-overview.md)

- [Que sont les services IP de réseau virtuel Azure ?](ip-services-overview.md)


