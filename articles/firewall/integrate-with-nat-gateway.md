---
title: Mettre à l’échelle les ports SNAT avec Azure NAT Gateway
description: Vous pouvez intégrer Pare-feu Azure à NAT Gateway pour augmenter le nombre de ports SNAT.
services: firewall
author: KumudD
ms.service: firewall
ms.topic: how-to
ms.date: 04/23/2021
ms.author: kumud
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 30d1da573bec205212658fa6aff5ac3b71ad2e50
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132399007"
---
# <a name="scale-snat-ports-with-azure-nat-gateway"></a>Mettre à l’échelle les ports SNAT avec Azure NAT Gateway

Pare-feu Azure fournit 2 048 ports SNAT par IP publique configurée, et vous pouvez y associer jusqu’à [250 IP publiques](./deploy-multi-public-ip-powershell.md). Selon votre architecture et vos modèles de trafic, vous pouvez avoir besoin de plus de 512 000 ports SNAT disponibles avec cette configuration. Par exemple, lorsque vous les utilisez pour protéger des [déploiements de Windows Virtual Desktop](./protect-azure-virtual-desktop.md) volumineux qui s’intègrent à Microsoft 365 Apps.

L’utilisation d’un grand nombre d’IP publiques pose un autre problème lorsqu’il existe des exigences de filtrage des adresses IP en aval. Pare-feu Azure sélectionne de manière aléatoire l’IP publique source à utiliser pour une connexion. Vous devez donc autoriser toutes les IP publiques qui lui sont associées. Même si vous utilisez des [préfixes d’IP publiques](../virtual-network/ip-services/public-ip-address-prefix.md) et que vous devez associer 250 IP publiques pour répondre à vos exigences en matière de ports SNAT sortants, vous devez toujours créer et autoriser 16 préfixes d’IP publiques.

Une meilleure option pour mettre à l’échelle les ports SNAT sortants consiste à utiliser la [ressource de passerelle NAT](../virtual-network/nat-gateway/nat-overview.md). Celle-ci fournit 64 000 ports SNAT par IP publique et prend en charge jusqu’à 16 IP publiques, fournissant ainsi jusqu’à 1 024 000 ports SNAT sortants.

Lorsqu’une ressource de passerelle NAT est associée à un sous-réseau de Pare-feu Azure, tout le trafic Internet sortant utilise automatiquement l’IP publique de la passerelle NAT. Il n’est pas nécessaire de configurer des [itinéraires définis par l’utilisateur](../virtual-network/tutorial-create-route-table-portal.md). Le trafic de réponse utilise l’IP publique de Pare-feu Azure pour maintenir la symétrie des flux. Si plusieurs adresses IP sont associées à la passerelle NAT, l’adresse IP est sélectionnée de manière aléatoire. Il n’est pas possible de spécifier l’adresse à utiliser.

Il n’existe pas de double NAT avec cette architecture. Les instances de Pare-feu Azure envoient le trafic à la passerelle NAT à l’aide de leur adresse IP privée plutôt que de l’IP publique de Pare-feu Azure.

> [!NOTE]
> L’utilisation d’Azure NAT Gateway est actuellement incompatible avec le Pare-feu Azure si vous avez déployé votre [Pare-feu Azure sur plusieurs zones de disponibilité](deploy-availability-zone-powershell.md).

## <a name="associate-nat-gateway-with-azure-firewall-subnet---azure-powershell"></a>Associer une passerelle NAT à un sous-réseau de Pare-feu Azure (Azure PowerShell)

L’exemple suivant crée et attache une passerelle NAT à un sous-réseau de Pare-feu Azure en utilisant Azure PowerShell.

```azurepowershell-interactive
# Create public IP addresses
New-AzPublicIpAddress -Name public-ip-1 -ResourceGroupName nat-rg -Sku Standard -AllocationMethod Static -Location 'South Central US'
New-AzPublicIpAddress -Name public-ip-2 -ResourceGroupName nat-rg -Sku Standard -AllocationMethod Static -Location 'South Central US'

# Create NAT gateway
$PublicIPAddress1 = Get-AzPublicIpAddress -Name public-ip-1 -ResourceGroupName nat-rg
$PublicIPAddress2 = Get-AzPublicIpAddress -Name public-ip-2 -ResourceGroupName nat-rg
New-AzNatGateway -Name firewall-nat -ResourceGroupName nat-rg -PublicIpAddress $PublicIPAddress1,$PublicIPAddress2 -Location 'South Central US' -Sku Standard

# Associate NAT gateway to subnet
$virtualNetwork = Get-AzVirtualNetwork -Name nat-vnet -ResourceGroupName nat-rg
$natGateway = Get-AzNatGateway -Name firewall-nat -ResourceGroupName nat-rg
$firewallSubnet = $virtualNetwork.subnets | Where-Object -Property Name -eq AzureFirewallSubnet
$firewallSubnet.NatGateway = $natGateway
$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="associate-nat-gateway-with-azure-firewall-subnet---azure-cli"></a>Associer une passerelle NAT à un sous-réseau de Pare-feu Azure (Azure CLI)

L’exemple suivant crée et attache une passerelle NAT à un sous-réseau de Pare-feu Azure en utilisant Azure CLI.

```azurecli-interactive
# Create public IP addresses
az network public-ip create --name public-ip-1 --resource-group nat-rg --sku standard
az network public-ip create --name public-ip-2 --resource-group nat-rg --sku standard

# Create NAT gateway
az network nat gateway create --name firewall-nat --resource-group nat-rg --public-ip-addresses public-ip-1 public-ip-2

# Associate NAT gateway to subnet
az network vnet subnet update --name AzureFirewallSubnet --vnet-name nat-vnet --resource-group nat-rg --nat-gateway firewall-nat
```

## <a name="next-steps"></a>Étapes suivantes

- [Conception de réseaux virtuels avec des ressources de passerelle NAT](../virtual-network/nat-gateway/nat-gateway-resource.md)