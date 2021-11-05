---
title: À propos des paramètres de configuration d’Azure Bastion
description: En savoir plus sur les paramètres de configuration disponibles pour Azure Bastion.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 10/12/2021
ms.author: cherylmc
ms.custom: ignite-fall-2021
ms.openlocfilehash: d1cf4c18c4722c327d9d74569e7709d11068dc7c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131087061"
---
# <a name="about-bastion-configuration-settings"></a>À propos des paramètres de configuration de Bastion

Les sections de cet article décrivent les ressources et les paramètres pour Azure Bastion.

## <a name="skus"></a><a name="skus"></a>Références SKU

Une référence SKU est également appelée niveau. Azure Bastion prend en charge deux types de SKU : De base et Standard. La référence SKU est configurée dans le portail Azure pendant le workflow lorsque vous configurez Bastion. Vous pouvez [mettre à niveau un SKU De base vers un SKU Standard](#upgradesku).

* Le **SKU De base** fournit une fonctionnalité de base, permettant à Azure Bastion de gérer la connectivité RDP/SSH aux machines virtuelles sans exposer les adresses IP publiques sur les machines virtuelles de l’application cible.
* La référence SKU Standard active les fonctionnalités Premium qui permettent à Azure Bastion de gérer la connectivité à distance à une plus grande échelle.

La table suivante présente les fonctionnalités et les références SKU correspondantes. 

[!INCLUDE [Azure Bastion SKUs](../../includes/bastion-sku.md)]

### <a name="configuration-methods"></a>Méthodes de configuration

Actuellement, vous devez utiliser le portail Azure si vous souhaitez spécifier la référence SKU Standard. Si vous utilisez Azure CLI ou Azure PowerShell pour configurer Bastion, la référence SKU ne peut pas être spécifiée et le SKU De base est défini par défaut.

| Méthode | Valeur | Liens |
| --- | --- | --- |
| Portail Azure | Niveau : De base ou <br>Standard | [Démarrage rapide : Configurer Bastion à partir des paramètres de la machine virtuelle](quickstart-host-portal.md)<br>[Tutoriel - Configurer Bastion](tutorial-create-host-portal.md) |
| Azure PowerShell | De base uniquement - aucun paramètre |[Configurer Bastion - PowerShell](bastion-create-host-powershell.md) |
| Azure CLI |  De base uniquement - aucun paramètre | [Configurer Bastion - CLI](create-host-cli.md) |

### <a name="upgrade-a-sku"></a><a name="upgradesku"></a>Mettre à niveau un SKU

Azure Bastion prend en charge la mise à niveau d’une référence SKU De base vers Standard.

> [!NOTE]
> La rétrogradation d’une référence SKU Standard vers De base n’est pas prise en charge. Pour rétrograder, vous devez supprimer et recréer l’instance Azure Bastion.
>

#### <a name="configuration-methods"></a>Méthodes de configuration

Vous pouvez configurer ce paramètre à l’aide de la méthode suivante :

| Méthode | Valeur | Liens |
| --- | --- | --- |
| Portail Azure |Niveau  | [Mettre à niveau un SKU](upgrade-sku.md)|

## <a name="instances-and-host-scaling"></a><a name="instance"></a>Instances et mise à l’échelle de l’hôte

Une instance est une machine virtuelle Azure optimisée qui est créée lorsque vous configurez Azure Bastion. Elle est entièrement gérée par Azure et exécute tous les processus nécessaires pour Azure Bastion. Une instance est également appelée unité d’échelle. Vous vous connectez aux machines virtuelles clientes via une instance Azure Bastion. Quand vous configurez Azure Bastion à l’aide de la référence SKU De base, deux instances sont créées. Si vous utilisez la référence SKU Standard, vous pouvez spécifier le nombre d’instances. Cela s’appelle la **mise à l’échelle de l’hôte**. 

Chaque instance peut prendre en charge 10-12 connexions RDP/SSH simultanées. Le nombre de connexions par instance dépend des actions que vous prenez quand vous vous connectez à la machine virtuelle cliente. Par exemple, si vous effectuez un traitement intensif en données, cela crée une charge plus importante que l’instance doit traiter. Une fois les sessions simultanées dépassées, une unité d’échelle (instance) supplémentaire est requise. 

Les instances sont créées dans AzureBastionSubnet. Pour la mise à l’échelle de l’hôte, AzureBastionSubnet doit être /26 ou plus grand. L’utilisation d’un sous-réseau plus petit limite le nombre d’instances que vous pouvez créer. Pour plus d’informations sur AzureBastionSubnet, voir la section [Sous-réseaux](#subnet) de cet article.

### <a name="configuration-methods"></a>Méthodes de configuration

Vous pouvez configurer ce paramètre à l’aide de la méthode suivante :

| Méthode | Valeur | Liens |
| --- | --- | --- |
| Portail Azure |Nombre d’instances  | [Configurer la mise à l’échelle de l’hôte](configure-host-scaling.md)|


## <a name="azure-bastion-subnet"></a><a name="subnet"></a>Sous-réseau d’Azure Bastion

Azure Bastion nécessite un sous-réseau dédié : **AzureBastionSubnet**. Ce sous-réseau doit être créé dans le réseau virtuel sur lequel Azure Bastion est déployé. Le sous-réseau doit avoir la configuration suivante :

* Le nom du sous-réseau doit être *AzureBastionSubnet*.
* La taille du sous-réseau doit être /27 ou supérieure (/26,/25, etc.).
* Pour la mise à l’échelle de l’hôte, un sous-réseau /26 ou plus grand est recommandé. L’utilisation d’un espace de sous-réseau plus petit limite le nombre d’unités d’échelle. Pour plus d’informations, consultez la section [Mise à l’échelle de l’hôte](#instance) de cet article.
* Le sous-réseau doit se trouver dans le même réseau virtuel et le même groupe de ressources que l’hôte Bastion.
* Le sous-réseau ne peut pas contenir des ressources supplémentaires.

### <a name="configuration-methods"></a>Méthodes de configuration

Vous pouvez configurer ce paramètre à l’aide des méthodes suivantes :

| Méthode | Valeur | Liens |
| --- | --- |--- |
| Portail Azure | Subnet  |[Démarrage rapide : Configurer Bastion à partir des paramètres de la machine virtuelle](quickstart-host-portal.md)<br>[Tutoriel - Configurer Bastion](tutorial-create-host-portal.md)|
| Azure PowerShell | -subnetName|[cmdlet](/powershell/module/az.network/new-azbastion#parameters) |
| Azure CLI |  --subnet-name | [command](/cli/azure/network/vnet#az_network_vnet_create) |

## <a name="public-ip-address"></a><a name="public-ip"></a>Adresse IP publique

Azure Bastion nécessite une adresse IP publique. L’adresse IP publique doit avoir la configuration suivante :

* La référence SKU d’adresse IP publique doit être **Standard**.
* La méthode d’attribution/allocation d’adresse IP publique doit être **statique**.
* Le nom de l’adresse IP publique est le nom de la ressource par lequel vous souhaitez faire référence à cette adresse IP publique.
* Vous pouvez choisir d’utiliser une adresse IP publique que vous avez déjà créée, à condition qu’elle réponde aux critères requis par Azure Bastion et qu’elle ne soit pas déjà utilisée.

### <a name="configuration-methods"></a>Méthodes de configuration

Vous pouvez configurer ce paramètre à l’aide des méthodes suivantes :

| Méthode | Valeur | Liens |
| --- | --- |--- |
| Portail Azure | Adresse IP publique |[Azure portal](https://portal.azure.com)|
| Azure PowerShell | -PublicIpAddress| [cmdlet](/powershell/module/az.network/new-azbastion#parameters)  |
| Azure CLI | --public-ip create |[command](/cli/azure/network/public-ip)
|

## <a name="next-steps"></a>Étapes suivantes

Pour accéder aux questions fréquentes (FAQ), consultez la [FAQ Azure Bastion](bastion-faq.md).
