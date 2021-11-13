---
title: 'Azure ExpressRoute : ajouter la prise en charge d’IPv6 à l’aide d’Azure CLI'
description: Découvrez comment ajouter la prise en charge d’IPv6 pour se connecter à des déploiements Azure à l’aide d’Azure CLI.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 09/27/2021
ms.author: duau
ms.openlocfilehash: 9341d61229ed5b28eed838271fd0108c1a45d8b5
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130228948"
---
# <a name="add-ipv6-support-for-private-peering-using-azure-cli"></a>Ajouter la prise en charge d’IPv6 pour le Peering privé à l’aide d’Azure CLI

Cet article explique comment ajouter la prise en charge d’IPv6 pour se connecter via ExpressRoute à vos ressources dans Azure à l’aide d’Azure CLI.

## <a name="prerequisites"></a>Prérequis

* Examinez les [conditions préalables](expressroute-prerequisites.md) et les [flux de travail](expressroute-workflows.md) avant de commencer la configuration.
* Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Installez la dernière version des commandes CLI (version 2.0 ou ultérieure). Pour plus d’informations sur l’installation des commandes CLI, consultez [Installer l’interface de ligne de commande Microsoft Azure](/cli/azure/install-azure-cli) et [Prise en main d’Azure CLI](/cli/azure/get-started-with-azure-cli).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="add-ipv6-private-peering-to-your-expressroute-circuit"></a>Ajouter le peering privé IPv6 à votre circuit ExpressRoute

1. [Créez un circuit ExpressRoute](howto-circuit-cli.md) ou utilisez un circuit existant. Affichez les détails du circuit en exécutant la commande suivante :

    ```azurecli-interactive
    az network express-route show --resource-group "<ExpressRouteResourceGroup>" --name "<MyCircuit>"
    ```

2. Affichez la configuration de Peering privé pour le circuit en exécutant la commande suivante :

    ```azurecli-interactive
    az network express-route peering show -g "<ExpressRouteResourceGroup>" --circuit-name "<MyCircuit>" --name AzurePrivatePeering
    ```

3. Ajoutez un Peering privé IPv6 à votre configuration de Peering privé IPv4. Indiquez une paire de sous-réseaux IPv6 /126 dont vous êtes propriétaire pour votre lien principal et vos liens secondaires. À partir de chacun de ces sous-réseaux, vous allez attribuer la première adresse IP utilisable à votre routeur. Microsoft utilise la deuxième IP utilisable pour son routeur.

    ```azurecli-interactive
    az network express-route peering update -g "<ExpressRouteResourceGroup>" --circuit-name "<MyCircuit>" --name AzurePrivatePeering --ip-version ipv6 --primary-peer-subnet "<X:X:X:X/126>" --secondary-peer-subnet "<Y:Y:Y:Y/126>"
    ```

## <a name="update-your-connection-to-an-existing-virtual-network"></a>Mettre à jour votre connexion à un réseau virtuel existant

Suivez les étapes ci-dessous si vous disposez d’un environnement de ressources Azure existant que vous souhaitez utiliser avec votre Peering privé IPv6.

1. Ajoutez un espace d’adressage IPv6 au réseau virtuel auquel votre circuit ExpressRoute est connecté.

    ```azurecli-interactive
    az network vnet update -g "<MyResourceGroup>" -n "<MyVNet>" --address-prefixes "X:X:X:X::/64"
    ```

3. Ajoutez un espace d’adressage IPv6 à votre sous-réseau de passerelle. Le sous-réseau IPv6 de passerelle doit être /64 ou plus.

    ```azurecli-interactive
    az network vnet subnet update -g "<MyResourceGroup>" -n "<MySubnet>" -vnet-name "<MyVNet>" --address-prefixes "10.0.0.0/26", "X:X:X:X::/64"
    ```

4. Si vous disposez d’une passerelle existante redondante dans une zone, exécutez la commande suivante pour activer la connectivité IPv6. (Notez que les changements peuvent nécessiter 1 heure pour être appliqués.) Sinon, [créez la passerelle de réseau virtuel](expressroute-howto-add-gateway-resource-manager.md) à l’aide d’une référence SKU. Si vous envisagez d’utiliser FastPath, utilisez UltraPerformance ou ErGw3AZ (notez que celle-ci n’est disponible que pour des circuits utilisant ExpressRoute Direct).

    ```azurecli-interactive
    az network vnet-gateway update --name "<GatewayName>" --resource-group "<MyResourceGroup>"
    ```
>[!NOTE]
> Si vous disposez déjà d’une passerelle qui n’est pas redondante interzone (c’est-à-dire qu’il s’agit d’un niveau tarifaire Standard, Haute performance ou Ultra-performance) et utilise une adresse IP publique de référence (SKU) De base, vous devez supprimer et [recréer la passerelle](expressroute-howto-add-gateway-resource-manager.md#add-a-gateway) avec n’importe quelle référence (SKU) et une adresse IP publique statique standard.

## <a name="create-a-connection-to-a-new-virtual-network"></a>Créer une connexion à un nouveau réseau virtuel

Suivez les étapes ci-dessous si vous envisagez de vous connecter à un nouvel ensemble de ressources Azure à l’aide de votre Peering privé IPv6.

1. Créez un réseau virtuel à double pile avec l’espace d’adressage IPv4 et IPv6. Pour plus d’informations, consultez [Créer un réseau virtuel](../virtual-network/quick-create-cli.md).

2. [Créez le sous-réseau de passerelle à double pile](expressroute-howto-add-gateway-resource-manager.md#add-a-gateway).

3. [Créez la passerelle de réseau virtuel](expressroute-howto-add-gateway-resource-manager.md#add-a-gateway) à l’aide d’une référence SKU. Si vous envisagez d’utiliser FastPath, utilisez la référence UltraPerformance ou ErGw3AZ (notez que celle-ci n’est disponible que pour des circuits utilisant ExpressRoute Direct).

4. [Liez votre réseau virtuel à votre circuit ExpressRoute](howto-linkvnet-cli.md).

## <a name="limitations"></a>Limites
Bien que la prise en charge d’IPV6 soit disponible pour les connexions à des déploiements dans des régions Azure globales, elle ne prend pas en charge les cas d’utilisation suivants :

* Connexions à des passerelles ExpressRoute *existantes* qui ne sont pas redondantes dans une zone. Notez que les passerelles ExpressRoute *nouvellement* créées de n’importe quel niveau tarifaire (redondant interzone ou non) à l’aide d’une adresse IP statique standard peuvent être utilisées pour les connexions ExpressRoute à double pile
* Connexions Global Reach entre des circuits ExpressRoute
* Utiliser ExpressRoute avec un WAN virtuel
* FastPath avec circuits non ExpressRoute Direct
* FastPath avec circuits dans les emplacements de Peering suivants : Dubaï
* Coexistence avec une passerelle VPN

## <a name="next-steps"></a>Étapes suivantes

Pour résoudre des problèmes liés à ExpressRoute, consultez les articles suivants :

* [Vérification de la connectivité ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
* [Résolution des problèmes de performances réseau](expressroute-troubleshooting-network-performance.md)
