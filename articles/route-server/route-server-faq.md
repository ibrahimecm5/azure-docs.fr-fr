---
title: Forum aux questions sur Azure Route Server
description: Trouvez des réponses aux questions fréquemment posées sur Azure Route Server.
services: route-server
author: duongau
ms.service: route-server
ms.topic: article
ms.date: 11/02/2021
ms.author: duau
ms.openlocfilehash: 47584994586e735647be4116fb49de610e5f97f5
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131453266"
---
# <a name="azure-route-server-faq"></a>Forum aux questions sur Serveur de routes Azure

## <a name="what-is-azure-route-server"></a>Qu’est-ce qu’Azure Route Server ?

Azure Route Server est un service complètement managé qui vous permet de gérer facilement le routage entre votre appliance virtuelle réseau (NVA) et votre réseau virtuel.

### <a name="is-azure-route-server-just-a-vm"></a>Azure Route Server est-il juste une machine virtuelle ?

Non. Azure Route Server est un service conçu avec une haute disponibilité. S’il est déployé sur une région Azure qui prend en charge les [zones de disponibilité](../availability-zones/az-overview.md), il offre une redondance au niveau de la zone.

### <a name="how-many-route-servers-can-i-create-in-a-virtual-network"></a>Combien de serveurs de routes est-ce que je peux créer dans un réseau virtuel ?

Vous ne pouvez créer qu’un seul serveur de routes dans un réseau virtuel. Il doit être déployé dans un sous-réseau dédié appelé *RouteServerSubnet*.

### <a name="does-azure-route-server-support-virtual-network-peering"></a>Serveur de routes Azure prend-il en charge l’appairage de réseaux virtuels ?

Oui, si vous appairez un réseau virtuel hébergeant Serveur de routes Azure à un autre réseau virtuel et que vous activez l’utilisation de la passerelle distante sur le second réseau virtuel, Serveur de routes Azure apprend les espaces d’adressage de ce réseau virtuel et les envoie à toutes les appliances virtuelles réseau appairées. Il programme également les routes à partir des appliances virtuelles réseau dans la table de routage des machines virtuelles du réseau virtuel appairé. 


### <a name="what-routing-protocols-does-azure-route-server-support"></a><a name = "protocol"></a>Quels sont les protocoles de routage pris en charge par Azure Route Server ?

Azure Route Server prend uniquement en charge BGP (Border Gateway Protocol). Votre NVA doit prendre en charge le protocole BGP externe multitronçon, car vous devez déployer Azure Route Server sur un sous-réseau dédié de votre réseau virtuel. Le numéro [ASN](https://en.wikipedia.org/wiki/Autonomous_system_(Internet)) que vous choisissez doit être différent de celui utilisé par Azure Route Server quand vous configurez le protocole BGP sur votre NVA.

### <a name="does-azure-route-server-route-data-traffic-between-my-nva-and-my-vms"></a>Azure Route Server route-t-il le trafic de données entre ma NVA et mes machines virtuelles ?

Non. Azure Route Server échange des routes BGP uniquement avec votre NVA. Le trafic de données passe directement de l’appliance virtuelle réseau à la machine virtuelle de destination et directement de la machine virtuelle à l’appliance virtuelle réseau.

### <a name="does-azure-route-server-store-customer-data"></a>Serveur de routes Azure stocke-t-il les données des clients ?
Non. Serveur de routes Azure échange uniquement les itinéraires BGP avec votre appliance virtuelle réseau, puis les propage vers votre réseau virtuel.

### <a name="why-does-azure-route-server-require-a-public-ip-address"></a>Pourquoi Serveur de routes Azure nécessite-t-il une adresse IP publique ?

Serveur de routes Azure doit garantir la connectivité au service back-end qui gère la configuration de Serveur de routes, car une telle adresse IP publique est nécessaire. 

### <a name="does-azure-route-server-support-ipv6"></a>Le serveur Azure Route prend-il en charge l'IPv6 ?

Non. Nous ajouterons le support IPv6 dans le futur. 

### <a name="if-azure-route-server-receives-the-same-route-from-more-than-one-nva-how-does-it-handle-them"></a>Si le serveur de routes Azure reçoit la même route de plusieurs appliances virtuelles réseau, comment la gère-t-il ?

Si la route a à la même longueur de chemin AS, le serveur de routes Azure va programmer plusieurs copies de la route, chacune avec un tronçon suivant différent, vers les machines virtuelles du réseau virtuel. Quand les machines virtuelles envoient le trafic vers la destination de cette route, les hôtes de machines virtuelles effectuent un routage ECMP (Equal-Cost Multi-Path). Cependant, si une appliance virtuelle réseau envoie la route avec une longueur de chemin AS plus courte que celle d’autres appliances virtuelles réseau, le serveur de routes Azure programme seulement la route dont le tronçon suivant est défini sur cette appliance virtuelle réseau pour les machines virtuelles du réseau virtuel.

### <a name="does-azure-route-server-preserve-the-bgp-communities-of-the-route-it-receives"></a>Le serveur de routes Azure conserve-t-il les communautés BGP de la route qu’il reçoit ?

Oui, le serveur de routes Azure propage la route telle quelle avec les communautés BGP.

### <a name="what-is-the-bgp-timer-setting-of-azure-route-server"></a>Quel est le paramètre de temporisation BGP du serveur Azure Route ?
Le délai d'attente est fixé à 60 secondes et le délai d'attente à 180 secondes.

### <a name="what-autonomous-system-numbers-asns-can-i-use"></a>Est-ce que je peux utiliser des numéros ASN (Autonomous System Numbers) ?

Vous pouvez utiliser vos propres numéros ASN publics ou privés dans votre appliance virtuelle réseau. Vous ne pouvez pas utiliser les plages réservées par Azure ou l’IANA.
Les ASN suivants sont réservés par Azure ou l’IANA :

* Numéros ASN réservés par Azure :
    * ASN publics : 8074, 8075, 12076
    * ASN privés : 65515, 65517, 65518, 65519, 65520
* ASN [réservés par l’IANA](http://www.iana.org/assignments/iana-as-numbers-special-registry/iana-as-numbers-special-registry.xhtml) :
    * 23456, 64496-64511, 65535-65551

### <a name="can-i-use-32-bit-4-byte-asns"></a>Est-ce que je peux utiliser des numéros ASN 32 bits (4 octets) ?

Non, Azure Route Server ne prend en charge que les numéros ASN 16 bits (2 octets).

### <a name="can-i-associate-a-user-defined-route-udr-to-the-routeserversubnet"></a>Puis-je associer une route définie par l'utilisateur (UDR) au RouteServerSubnet ?

Non, Azure Route Server ne prend pas en charge la configuration d'un UDR sur le RouteServerSubnet.

### <a name="can-i-associate-a-network-security-group-nsg-to-the-routeserversubnet"></a>Puis-je associer un groupe de sécurité réseau (NSG) au RouteServerSubnet ?

Non, Azure route Server ne prend pas en charge l’Association NSG à RouteServerSubnet.

### <a name="can-i-peer-two-route-servers-in-two-peered-virtual-networks-and-enable-the-nvas-connected-to-the-route-servers-to-talk-to-each-other"></a>Puis-je homologuer deux serveurs de routage dans deux réseaux virtuels homologués et autoriser les appliances virtuelles réseau connectés aux serveurs de routage à communiquer entre eux ? 

***Topologie : NVA1-> RouteServer1-> (via VNet Peering)-> RouteServer2-> NVA2***

Non, Azure Route Server ne transfère pas le trafic de données. Pour activer la connectivité de transit via l’appliance virtuelle réseau, configurez une connexion directe (par exemple, un tunnel IPsec) entre les appliances virtuelles réseau et utilisez les serveurs de routage pour la propagation de routes dynamiques. 

### <a name="can-i-use-azure-route-server-to-direct-traffic-between-subnets-in-the-same-virtual-network-to-flow-inter-subnet-traffic-through-the-nva"></a>Puis-je utiliser le Serveur de routes Azure pour diriger le trafic entre les sous-réseaux dans le même réseau virtuel afin de transmettre le trafic entre sous-réseaux via l’appliance virtuelle réseau ?

Non. Les itinéraires système pour le trafic lié au réseau virtuel, aux peerings de réseaux virtuels ou aux points de terminaison de service de réseau virtuel, sont les itinéraires sélectionnés par défaut, même si les itinéraires BGP sont plus spécifiques. Comme le Serveur de routes utilise BGP pour publier des itinéraires, ce n’est pas encore pris en charge par la conception. Vous devez continuer à utiliser UDR pour forcer le remplacement des itinéraires et vous ne pouvez pas utiliser BGP pour basculer rapidement à travers ces itinéraires. Vous devez continuer à utiliser une solution tierce pour mettre à jour les UDR via l’API dans une situation de basculement, ou utiliser un Azure Load Balancer avec le mode ports HA pour diriger le trafic.

Vous pouvez toujours utiliser le Serveur de routes pour diriger le trafic entre les sous-réseaux dans différents réseaux virtuels vers le flux à l’aide de l’appliance virtuelle réseau. La seule conception possible qui peut fonctionner est un sous-réseau par réseau virtuel « Spoke » et tous les réseaux virtuels sont homologués à un réseau virtuel « Hub », mais cela est très limité et doit prendre en compte la mise à l’échelle et les limites maximales d’Azure sur les réseaux virtuels et les sous-réseaux.

## <a name="route-server-limits"></a><a name = "limitations"></a>Limites de Route Server

Azure Route Server présente les limites suivantes (par déploiement).

| Ressource | Limite |
|----------|-------|
| Nombre de pairs BGP pris en charge | 8 |
| Nombre de routes que chaque pair BGP peut publier sur Azure Route Server | 1 000 |
| Nombre de routes qu’Azure Route Server peut publier sur ExpressRoute ou la passerelle VPN | 200 |
| Nombre de machines virtuelles du réseau virtuel (réseaux virtuels appairés compris) que Serveur de routes Azure peut prendre en charge | 2000 |

Le nombre de machines virtuelles qu'Azure Route Server peut prendre en charge n'est pas une limite stricte. Cela dépend de la manière dont l'infrastructure Route Server est déployée dans une région Azure.

Si votre appliance virtuelle réseau publie plus de routes que la limite, la session BGP est abandonnée. Dans le cas où la session BGP d’événement est abandonnée entre la passerelle et Serveur de routes Azure, vous perdez la connectivité entre votre réseau local et Azure. Pour plus d’informations, consultez [Diagnostiquer un problème de routage sur une machine virtuelle Azure](../virtual-network/diagnose-network-routing-problem.md).


## <a name="next-steps"></a>Étapes suivantes

Découvrez comment [configurer Azure Route Server](quickstart-configure-route-server-powershell.md).
