---
title: Créer, changer ou supprimer un préfixe d’adresse IP publique Azure
titlesuffix: Azure Virtual Network
description: En savoir plus sur les préfixes d’IP publiques et sur la manière de les créer, de les modifier ou de les supprimer.
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: allensu
ms.openlocfilehash: 824d89126252a0690b93a2129f47d8e02b728694
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131560109"
---
# <a name="create-change-or-delete-a-public-ip-address-prefix"></a>Créer, changer ou supprimer un préfixe d’adresse IP publique

Découvrez la notion de préfixe d’adresse IP publique. Un préfixe d’adresse IP publique est une plage contiguë d’adresses IP publiques à référence SKU standard.  Lorsque vous créez une ressource d’adresse IP publique, vous pouvez affecter une IP publique statique à partir du préfixe, et associer l’adresse aux ressources Azure. Pour plus d’informations, consultez [Vue d’ensemble du préfixe d’adresse IP publique](public-ip-address-prefix.md).

## <a name="create-a-public-ip-address-prefix"></a>Créer un préfixe d’adresse IP publique

La section suivante détaille les paramètres au moment de la création d’un préfixe d’IP publique.

   |Paramètre|Requis ?|Détails|
   |---|---|---|
   |Abonnement|Oui|Doit exister dans le même [abonnement](../../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) que la ressource à laquelle vous voulez associer l’adresse IP publique.|
   |Resource group|Oui|Peut exister dans un [groupe de ressources](../../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) identique ou différent de celui de la ressource à laquelle vous voulez associer l’adresse IP publique.|
   |Nom|Oui|Le nom doit être unique au sein du groupe de ressources que vous avez sélectionné.|
   |Région|Oui|Il doit exister dans la même [région](https://azure.microsoft.com/regions) que les adresses IP publiques auxquelles vous attribuez des adresses de la plage.|
   |Version de l’adresse IP|Yes| Version IP du préfixe (v4 ou v6).
   |Taille de préfixe|Oui| Taille du préfixe dont vous avez besoin. Une plage de 16 adresses IP (/28 pour v4 ou /124 pour v6) est la plage par défaut.

Vous pouvez également utiliser les commandes CLI ou PowerShell ci-dessous pour créer un préfixe d’adresse IP publique.

**Commandes**

|Outil|Commande|
|---|---|
|Interface de ligne de commande|[az network public-ip prefix create](/cli/azure/network/public-ip/prefix#az_network_public_ip_prefix_create)|
|PowerShell|[New-AzPublicIpPrefix](/powershell/module/az.network/new-azpublicipprefix)|

>[!NOTE]
>Dans les régions avec des zones de disponibilité, vous pouvez utiliser des commandes PowerShell ou CLI pour créer un préfixe d’adresse IP publique comme suit : non zonal, associé à une zone spécifique ou pour utiliser la redondance de zone.  Pour l’API version 2020-08-01 ou ultérieure, si un paramètre de zone n’est pas fourni, un préfixe d’adresse IP publique non zonal est créé. Pour les versions d’API antérieures à 2020-08-01, un préfixe d’adresse IP publique redondant interzone est créé. 

## <a name="create-a-static-public-ip-address-from-a-prefix"></a>Créer une adresse IP publique statique à partir d’un préfixe

La section suivante détaille les paramètres nécessaires à la création d’une adresse IP publique statique à partir d’un préfixe.

   |Paramètre|Requis ?|Détails|
   |---|---|---|
   |Nom|Oui|Le nom de l’adresse IP publique doit être unique au sein du groupe de ressources sélectionné.|
   |Délai d’inactivité (minutes)|Non|Durée (en minutes) de maintien d’une connexion TCP ou HTTP ouverte sans utiliser les clients pour envoyer des messages keep-alive. |
   |Étiquette du nom DNS|Non|Elle doit être unique dans la région Azure où vous créez le nom (pour tous les abonnements et tous les clients). Azure inscrit automatiquement le nom et l’adresse IP dans son DNS pour que vous puissiez vous connecter à une ressource avec le nom. Azure ajoute un sous-réseau par défaut, *location.cloudapp.azure.com*, au nom que vous indiquez pour créer le nom DNS complet. Pour plus d’informations, consultez [Utiliser Azure DNS avec une adresse IP publique Azure](../../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address).|

Vous pouvez également utiliser les commandes CLI ou PowerShell ci-dessous avec les paramètres **--public-ip-prefix (CLI)** et **-PublicIpPrefix (PowerShell)** , pour créer une ressource d’adresse IP publique à partir d’un préfixe. 

|Outil|Commande|
|---|---|
|Interface de ligne de commande|[az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create)|
|PowerShell|[New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)|

>[!NOTE]
>Lors de la demande d’une adresse IP publique à partir d’un préfixe d’adresse IP publique, l’allocation n’est pas déterministe ou séquentielle. Si une demande d’adresse IP publique spécifique à partir d’un préfixe d’adresse IP publique est requise, les commandes PowerShell ou CLI le permettent.  Pour PowerShell, utilisez le paramètre `IpAddress` (suivi de l’adresse IP souhaitée). Pour CLI, utilisez le paramètre `ip-address` (suivi de l’adresse IP souhaitée).

>[!NOTE]
>Seules les adresses IP publiques créées avec la référence (SKU) standard peuvent être attribuées à partir de la plage du préfixe. Pour en savoir plus sur les références (SKU) d’adresses IP publiques, consultez [Adresse IP publique](public-ip-addresses.md#public-ip-addresses).

## <a name="view-or-delete-a-prefix"></a>Voir ou supprimer un préfixe

Pour afficher ou supprimer un préfixe, les commandes suivantes peuvent être utilisées dans l’interface Azure CLI et dans Azure PowerShell.

**Commandes**

|Outil|Commande|
|---|---|
|Interface de ligne de commande|[az network public-ip prefix list](/cli/azure/network/public-ip/prefix#az_network_public_ip_prefix_list) pour lister les adresses IP publiques<br>[az network public-ip prefix show](/cli/azure/network/public-ip/prefix#az_network_public_ip_prefix_show) pour afficher les paramètres<br> [az network public-ip prefix update](/cli/azure/network/public-ip/prefix#az_network_public_ip_prefix_update) pour mettre à jour<br>[az network public-ip prefix delete](/cli/azure/network/public-ip/prefix#az_network_public_ip_prefix_delete) pour supprimer|
|PowerShell|[Get-AzPublicIpPrefix](/powershell/module/az.network/get-azpublicipprefix) pour récupérer un objet d’adresse IP publique et afficher ses paramètres<br>[Set-AzPublicIpPrefix](/powershell/module/az.network/set-azpublicipprefix) pour mettre à jour les paramètres<br> [Remove-AzPublicIpPrefix](/powershell/module/az.network/remove-azpublicipprefix) pour supprimer|

## <a name="permissions"></a>Autorisations

Afin que les autorisations gèrent les préfixes d’adresses IP publiques, votre compte doit être affecté au rôle de [contributeur de réseaux](../../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ou à un rôle [personnalisé](../../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 

| Action                                                            | Nom                                                           |
| ---------                                                         | -------------                                                  |
| Microsoft.Network/publicIPPrefixes/read                           | Lire un préfixe d’adresse IP publique                                |
| Microsoft.Network/publicIPPrefixes/write                          | Créer ou mettre à jour un préfixe d’adresse IP publique                    |
| Microsoft.Network/publicIPPrefixes/delete                         | Supprimer un préfixe d’adresse IP publique                              |
|Microsoft.Network/publicIPPrefixes/join/action                     | Créer une adresse IP publique à partir d’un préfixe |

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur les scénarios et les avantages avec un [préfixe d’adresse IP publique](public-ip-address-prefix.md)
