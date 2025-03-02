---
title: Créer un hôte Bastion à l’aide d’Azure PowerShell | Microsoft Docs
description: Découvrez comment créer un hôte Azure Bastion à l’aide de PowerShell.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 09/22/2021
ms.author: cherylmc
ms.custom: ignite-fall-2021
ms.openlocfilehash: faa6fe1b7fdccf81f83b591c1f0258d27dc9fdd0
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131071518"
---
# <a name="create-an-azure-bastion-host-using-azure-powershell"></a>Créer un hôte Azure Bastion à l’aide d’Azure PowerShell

Dans cet article, découvrez comment créer un hôte Azure Bastion à l’aide de PowerShell. Une fois que vous avez provisionné le service Azure Bastion dans votre réseau virtuel, l’expérience fluide RDP/SSH est disponible pour toutes les machines virtuelles du même réseau virtuel. Le déploiement Azure Bastion est effectué par réseau virtuel et non par abonnement/compte ou machine virtuelle.

Vous pouvez éventuellement créer un hôte Azure Bastion en utilisant les méthodes suivantes :
* [Azure portal](./tutorial-create-host-portal.md)
* [Azure CLI](create-host-cli.md)

[!INCLUDE [About SKUs](../../includes/bastion-sku-note.md)]

## <a name="prerequisites"></a>Prérequis

Assurez-vous de disposer d’un abonnement Azure. Si vous ne disposez pas déjà d’un abonnement Azure, vous pouvez activer vos [avantages abonnés MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) ou créer un [compte gratuit](https://azure.microsoft.com/pricing/free-trial).

[!INCLUDE [PowerShell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

 > [!NOTE]
 > L'utilisation d'Azure Bastion avec Azure DNS Private Zones n'est pas prise en charge pour le moment. Avant de commencer, assurez-vous que le réseau virtuel sur lequel vous prévoyez de déployer votre ressource Bastion n'est pas lié à une zone DNS privée.
 >

## <a name="create-a-bastion-host"></a><a name="createhost"></a>Créer un hôte Bastion

Cette section vous permet de créer une ressource Azure Bastion à l’aide d’Azure PowerShell.

1. Créez un réseau virtuel et un sous-réseau Azure Bastion associé. Vous devez créer un sous-réseau Azure Bastion à l’aide de la valeur de nom **AzureBastionSubnet**. Cette valeur permet à Azure de savoir dans quel sous-réseau déployer les ressources Bastion. C’est différent d’un sous-réseau de passerelle VPN.

   [!INCLUDE [Note about BastionSubnet size.](../../includes/bastion-subnet-size.md)]

   ```azurepowershell-interactive
   $subnetName = "AzureBastionSubnet"
   $subnet = New-AzVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
   $vnet = New-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myBastionRG" -Location "westeurope" -AddressPrefix 10.0.0.0/16 -Subnet $subnet
   ```

2. Créez une adresse IP publique pour Azure Bastion. Adresse IP publique de la ressource Bastion où RDP/SSH est accessible (sur le port 443). L’adresse IP publique doit être située dans la même région que la ressource Bastion que vous créez.

   ```azurepowershell-interactive
   $publicip = New-AzPublicIpAddress -ResourceGroupName "myBastionRG" -name "myPublicIP" -location "westeurope" -AllocationMethod Static -Sku Standard
   ```

3. Créez une ressource Azure Bastion dans le sous-réseau Azure Bastion de votre réseau virtuel. Il faut environ 5 minutes pour que la ressource Bastion soit créée et déployée.

   ```azurepowershell-interactive
   $bastion = New-AzBastion -ResourceGroupName "myBastionRG" -Name "myBastion" -PublicIpAddress $publicip -VirtualNetwork $vnet
   ```
## <a name="disassociate-the-vm-public-ip-address"></a>Dissocier l’IP publique de la machine virtuelle

Azure Bastion n’utilise pas l’IP publique pour se connecter à la machine virtuelle cliente. Si vous n’avez pas besoin de l’IP publique de votre machine virtuelle, vous pouvez dissocier l’IP publique en suivant la procédure décrite dans cet article : [Dissocier une IP publique d’une machine virtuelle Azure](../virtual-network/ip-services/remove-public-ip-address-vm.md).

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations, lisez les [questions fréquentes (FAQ) sur Bastion](bastion-faq.md).
* Pour utiliser des groupes de sécurité réseau avec le sous-réseau Azure Bastion, consultez [Utiliser des groupes de sécurité réseau](bastion-nsg.md).
