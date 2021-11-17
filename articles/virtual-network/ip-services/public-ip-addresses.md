---
title: Adresses IP publiques dans Azure
titleSuffix: Azure Virtual Network
description: En savoir plus sur les adresses IP publiques dans Azure.
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: conceptual
ms.date: 04/29/2021
ms.author: allensu
ms.openlocfilehash: a57c4ea8d058f0f565b6dbec7fdb74b07ea7564e
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131471399"
---
# <a name="public-ip-addresses"></a>Adresses IP publiques

Les adresses IP publiques permettent aux ressources Internet de communiquer avec les ressources Azure (communication entrante). Les adresses IP publiques permettent aux ressources Azure de communiquer avec Internet et les services Azure publics. L’adresse est dédiée à la ressource jusqu’à ce que vous annuliez son attribution. Une ressource sans adresse IP publique attribuée peut établir une communication sortante. Azure attribue dynamiquement une adresse IP disponible qui n’est pas dédiée à la ressource. Pour plus d’informations sur les connexions sortantes dans Azure, consultez [Comprendre les connexions sortantes dans Azure](../../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Dans Azure Resource Manager, une [adresse IP publique](virtual-network-public-ip-address.md) est une ressource ayant ses propres propriétés. Voici quelques-unes des ressources auxquelles vous pouvez associer une ressource d’adresse IP publique :

* Interfaces réseau de machine virtuelle
* Équilibreurs de charge accessibles sur Internet
* Passerelles de réseau virtuel (VPN/ER)
* Passerelles NAT
* Passerelles d’application
* Pare-feu Azure
* Hôte Bastion

Pour Virtual Machine Scale Sets, utilisez [Préfixes d’adresses IP publiques](public-ip-address-prefix.md).

## <a name="at-a-glance"></a>Aperçu

Le tableau ci-dessous présente la propriété avec laquelle une adresse IP publique peut être associée à une ressource, ainsi que les méthodes d’allocation. Notez que la prise en charge du protocole IPv6 public n’est actuellement pas disponible pour tous les types de ressources.

| Ressources de niveau supérieur | Association d’adresse IP | IPv4 dynamique | IPv4 statique | IPv6 dynamique | IPv6 statique |
| --- | --- | --- | --- | --- | --- |
| Machine virtuelle |interface réseau |Oui | Oui | Oui | Oui |
| Équilibreur de charge accessible sur Internet |Configuration frontale |Oui | Oui | Oui |Oui |
| Passerelle de réseau virtuel (VPN) |Configuration IP de la passerelle |Oui (non-AZ uniquement) |Oui (AZ uniquement) | Non |Non |
| Passerelle de réseau virtuel (ER) |Configuration IP de la passerelle |Oui | Non | Oui (préversion) |No |
| Passerelle NAT |Configuration IP de la passerelle |Non |Oui | Non |Non |
| passerelle d’application |Configuration frontale |Oui (V1 uniquement) |Oui (V2 uniquement) | Non | Non |
| Pare-feu Azure | Configuration frontale | Non | Oui | Non | Non |
| Hôte Bastion | Configuration IP publique | Non | Oui | Non | Non |

## <a name="ip-address-version"></a>Version de l’adresse IP

Les adresses IP publiques peuvent être créées avec une adresse IPv4 et IPv6. Vous avez la possibilité de créer un déploiement à double pile avec une adresse IPv4 et IPv6.

## <a name="sku"></a>SKU

Les adresses IP publiques sont créées avec l’une des références SKU suivantes :

### <a name="standard"></a>Standard

Les adresses IP publiques de référence SKU standard :

- Utilisent toujours la méthode d’allocation statique.
- Dotées d’un délai d’inactivité du flux entrant réglable de 4 à 30 minutes, avec une valeur par défaut de 4 minutes et d’un délai d’inactivité du flux sortant fixe de 4 minutes.
- Conçu pour s’aligner avec le modèle « sécurisé par défaut » et être fermé dans le trafic entrant lorsqu’il est utilisé en tant que serveur frontal.  Il est nécessaire d’autoriser le trafic du plan de données avec le [groupe de sécurité réseau](../../virtual-network/network-security-groups-overview.md#network-security-groups) (NSG) (par exemple, sur la carte réseau d'une machine virtuelle à laquelle est attachée une adresse IP publique de SKU standard).
- Peut être redondant interzone (ce qui provient de l’ensemble des 3 zones), zonal (ce qui est garanti dans une zone de disponibilité présélectionnée spécifique) ou sans zone (ce qui n’est pas associé à une zone de disponibilité présélectionnée spécifique). Pour en savoir plus sur les zones de disponibilité, consultez [Vue d’ensemble des zones de disponibilité](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) et [Équilibreur de charge standard et zones de disponibilité](../../load-balancer/load-balancer-standard-availability-zones.md?toc=%2fazure%2fvirtual-network%2ftoc.json). **Les IP redondantes dans une zone peuvent être créées seulement dans des [régions où trois zones de disponibilité](../../availability-zones/az-region.md) sont actives.** Les IP créées avant que les zones soient actives ne sont pas redondantes dans une zone.
- Peuvent être utilisées avec la [préférence de routage](routing-preference-overview.md) pour permettre un contrôle plus granulaire de la façon dont le trafic est routé entre Azure et Internet.
- Peuvent être utilisées comme adresses IP frontales anycast pour [les équilibreurs de charge interrégions](../../load-balancer/cross-region-overview.md) (fonctionnalité d’évaluation).

> [!NOTE]
> Seules les adresses IP publiques avec la référence SKU De base sont disponibles lorsque vous utilisez [le service de métadonnées d’instance (IMDS, instance metadata service)](../../virtual-machines/windows/instance-metadata-service.md). La référence SKU Standard n’est pas prise en charge.

> [!NOTE]
> Les paramètres de diagnostic n’apparaissent pas dans le panneau de la ressource lors de l’utilisation d’une adresse IP publique de référence SKU standard. Pour activer la journalisation sur votre ressource d’adresse IP publique standard, accédez aux paramètres de diagnostic dans le panneau Azure Monitor et sélectionnez votre ressource d’adresse IP.

> [!NOTE]
> La communication entrante avec la ressource de référence SKU standard est possible uniquement si vous créez et associez un [groupe de sécurité réseau](../../virtual-network/network-security-groups-overview.md#network-security-groups) et que vous autorisez explicitement le trafic entrant prévu.

### <a name="basic"></a>De base

Les adresses de référence SKU de base :

- Pour IPv4 : peuvent être attribuées à l’aide de la méthode d’allocation dynamique ou statique.  Pour IPv6 : peuvent uniquement être attribuées à l’aide de la méthode d’allocation dynamique.
- Dotées d’un délai d’inactivité du flux entrant réglable de 4 à 30 minutes, avec une valeur par défaut de 4 minutes et d’un délai d’inactivité du flux sortant fixe de 4 minutes.
- Ouvertes par défaut.  Il est recommandé d’utiliser des groupes de sécurité réseau, mais cela est facultatif pour restreindre le trafic entrant ou sortant.
- Ne gèrent pas les scénarios de zone de disponibilité. Utilisent l’adresse IP publique de la référence SKU Standard pour les scénarios de zone de disponibilité dans les régions applicables. Pour en savoir plus sur les zones de disponibilité, consultez [Vue d’ensemble des zones de disponibilité](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) et [Équilibreur de charge standard et zones de disponibilité](../../load-balancer/load-balancer-standard-availability-zones.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Ne prennent pas en charge la fonctionnalité de [préférence de routage](routing-preference-overview.md) ou d’[équilibreurs de charge entre les régions](../../load-balancer/cross-region-overview.md).

> [!NOTE]
> Les adresses IPv4 de la référence SKU de base peuvent être mises à niveau après la création d’une référence SKU standard.  Pour en savoir plus sur la mise à niveau du SKU, consultez [Mise à niveau de l’adresse IP publique](public-ip-upgrade-portal.md).

>[!IMPORTANT]
> Les références SKU qui correspondent doivent être utilisées pour les ressources de Load Balancer et d’adresse IP publique. Vous ne pouvez pas avoir à la fois des ressources de référence SKU De base et de référence SKU Standard. Vous ne pouvez pas joindre des machines virtuelles autonomes, des machines virtuelles dans une ressource de groupe à haute disponibilité ou des ressources de groupe de machines virtuelles identiques aux deux références SKU simultanément.  De nouvelles conceptions doivent être envisagées à l’aide des ressources de référence SKU Standard.  Veuillez consulter [Load Balancer Standard](../../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) pour plus d’informations.

## <a name="ip-address-assignment"></a>Affectation d’adresses IP

Les adresses IPv4 publiques standard, IPv4 de base et IPv6 standard prennent toutes en charge l’attribution **static**.  Une adresse IP est attribuée à la ressource au moment de sa création. L’adresse IP est libérée lorsque la ressource est supprimée.  

> [!NOTE]
> Même lorsque vous définissez la méthode d’allocation sur **statique**, vous ne pouvez pas spécifier l’adresse IP réelle affectée à la ressource IP publique. Azure assigne l’adresse IP à partir d’un pool d’adresses IP disponibles dans l’emplacement Azure dans lequel la ressource est créée.
>

Des adresses IP publiques statiques sont fréquemment utilisées dans les cas suivants :

* Lorsque vous devez mettre à jour les règles de pare-feu pour communiquer avec vos ressources Azure.
* La résolution de noms DNS est telle qu’une modification de l’adresse IP nécessiterait une mise à jour des enregistrements A.
* Vos ressources Azure communiquent avec d’autres applications ou services qui utilisent un modèle de sécurité basé sur une adresse IP.
* Vous utilisez des certificats TLS/SSL liés à une adresse IP.

Les adresses IPv4 et IPv6 publiques de base prennent en charge une attribution **dynamique**.  Si vous sélectionnez la méthode dynamique, l’adresse IP **n’est pas** attribuée à la ressource au moment de sa création.  L’adresse IP est attribuée lorsque vous associez la ressource d’adresse IP publique à une ressource. L’adresse IP est libérée lorsque vous arrêtez (ou supprimez) la ressource.   Par exemple, une ressource IP publique est libérée à partir d’une ressource nommée **Ressource A**. **Ressource A** reçoit une adresse IP différente au démarrage si la ressource IP publique est réaffectée. Une adresse IP associée est libérée si la méthode d’allocation passe de **statique** à **dynamique**. Une adresse IP associée est inchangée si la méthode d’allocation passe de **dynamique** à **statique**. Définissez la méthode d’allocation **statique** pour vous assurer que l’adresse IP ne change pas.

> [!NOTE]
> Azure alloue des adresses IP publiques d’une plage unique à chaque région dans chaque Cloud Azure. Vous pouvez télécharger la liste des plages (préfixes) pour les clouds Azure [Public](https://www.microsoft.com/download/details.aspx?id=56519), [Gouvernement américain](https://www.microsoft.com/download/details.aspx?id=57063), [Chine](https://www.microsoft.com/download/details.aspx?id=57062), et [Allemagne](https://www.microsoft.com/download/details.aspx?id=57064).
>

## <a name="dns-name-label"></a>Étiquette du nom DNS

Sélectionnez cette option pour spécifier une étiquette DNS pour une ressource d’adresse IP publique. Cette fonctionnalité fonctionne pour les adresses IPv4 (enregistrements A 32 bits) et les adresses IPv6 (enregistrements AAAA de 128 bits).  Cette sélection crée un mappage pour **domainnamelabel**.**location**.cloudapp.azure.com sur l’adresse IP publique dans le DNS géré par Azure. 

Par exemple, la création d’une adresse IP publique avec :

* **contoso** comme **domainnamelabel**
* **West US** Azure **location**

Le nom de domaine complet (FQDN) **contoso.westus.cloudapp.azure.com** correspond à l’adresse IP publique de la ressource.

> [!IMPORTANT]
> Chaque étiquette de nom de domaine créée doit être unique dans son emplacement Azure.  

Si vous souhaitez utiliser un domaine personnalisé pour les services qui utilisent une adresse IP publique, vous pouvez utiliser [Azure DNS](../../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address) ou un fournisseur DNS externe pour votre enregistrement DNS.

## <a name="other-public-ip-address-features"></a>Autres fonctionnalités d’adresse IP publique

D’autres attributs peuvent être utilisés pour une adresse IP publique.  

* Le **niveau** global permet l’utilisation d’une adresse IP publique avec des équilibreurs de charge entre les régions. 
* L’option **Préférence de routage** Internet limite le temps que le trafic passe sur le réseau Microsoft, ce qui réduit le coût de transfert de données sortantes.

> [!NOTE]
> Actuellement, les fonctionnalités **Niveau** et **Préférence de routage** sont uniquement disponibles pour les adresses IPv4 de la référence SKU standard.  Elles ne peuvent pas non plus être utilisés simultanément sur la même adresse IP.
>

[!INCLUDE [ephemeral-ip-note.md](../../../includes/ephemeral-ip-note.md)]

## <a name="limits"></a>limites 

Les limites imposées pour l’adressage IP sont indiquées dans l’ensemble des [limites pour la mise en réseau](../../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) dans Azure. Ces limites sont exprimées par région et par abonnement. [Contactez le support](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour augmenter les limites par défaut en fonction de vos besoins métier.

## <a name="pricing"></a>Tarifs

Le coût des adresses IP publiques est modique. Pour en savoir plus sur la tarification des adresses IP dans Azure, lisez la page [Tarification des adresses IP](https://azure.microsoft.com/pricing/details/ip-addresses).

## <a name="limitations-for-ipv6"></a>Limitations pour IPv6

* Les passerelles VPN ne peuvent pas être utilisés dans un réseau virtuel où le protocole IPv6 est activé, que ce soit directement ou appairé avec « UseRemoteGateway ».
* Les adresses IPv6 publiques sont verrouillées sur un délai d’inactivité de 4 minutes.
* Azure ne prend pas en charge la communication IPv6 pour les conteneurs.
* L’utilisation de machines virtuelles ou de groupes identiques de machines virtuelles avec IPv6 uniquement n’est pas prise en charge. Chaque carte d’interface réseau doit inclure au moins une configuration IP IPv4 (double pile).
* Lorsque vous ajoutez IPv6 à des déploiements IPv4 existants, les plages IPv6 ne peuvent pas être ajoutées à un réseau virtuel avec des liens de navigation vers les ressources qui existent déjà.
* Le DNS direct pour IPv6 est actuellement pris en charge pour le DNS public Azure. Le DNS inversé n’est pas pris en charge.
* La préférence de routage et l’équilibrage de charge entre les régions ne sont pas pris en charge.

Pour plus d’informations sur IPv6 dans Azure, cliquez [ici](ipv6-overview.md).

## <a name="next-steps"></a>Étapes suivantes
* Découvrir les [adresses IP privées dans Azure](private-ip-addresses.md)
* [Déployer une machine virtuelle avec une adresse IP publique statique à l’aide du portail Azure](./virtual-network-deploy-static-pip-arm-portal.md)