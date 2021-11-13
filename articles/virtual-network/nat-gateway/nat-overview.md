---
title: Présentation du service NAT de Réseau virtuel Azure
titlesuffix: Azure Virtual Network
description: Vue d’ensemble des fonctionnalités, des ressources, de l’architecture et de l’implémentation du service NAT de Réseau virtuel. Découvrez le fonctionnement du service NAT de Réseau virtuel et comment utiliser des ressources de passerelle NAT dans le cloud.
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.subservice: nat
ms.topic: conceptual
ms.date: 10/20/2021
ms.author: allensu
ms.openlocfilehash: 5e2b9baeef03163e74ad83c97d8e8148d7f6cfaf
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130228315"
---
# <a name="what-is-virtual-network-nat"></a>Qu’est-ce que le service NAT de Réseau virtuel ?

Le NAT de réseau virtuel est un service de traduction d’adresses réseau entièrement managé et hautement résilient. Le NAT de réseau virtuel simplifie la connectivité Internet sortante pour les réseaux virtuels. Quand il est configuré sur un sous-réseau, toute la connectivité sortante utilise les adresses IP publiques statiques du NAT de réseau virtuel. 

:::image type="content" source="./media/nat-overview/flow-map.png" alt-text="La figure montre un NAT recevant du trafic à partir de sous-réseaux internes et le dirigeant vers une adresse IP publique (PIP) et un préfixe IP.":::

*Figure : Service NAT de Réseau virtuel*

## <a name="vnet-nat-benefits"></a>Avantages du NAT de réseau virtuel

### <a name="security"></a>Sécurité
Avec NAT, les machines virtuelles individuelles (ou d’autres ressources de calcul) n’ont pas besoin d’adresses IP publiques et peuvent rester entièrement privées. Ces ressources sans adresse IP publique peuvent néanmoins toujours atteindre des sources externes en dehors du réseau virtuel. Vous pouvez également associer un préfixe d’adresse IP publique pour garantir qu’un ensemble d’adresses IP contiguës sera utilisé pour le trafic sortant. Les règles de pare-feu de destination peuvent ensuite être configurées en fonction de cette liste d’adresses IP prévisibles.

### <a name="resiliency"></a>Résilience 
NAT est un service entièrement managé et distribué. Elle ne dépend d’aucune instance de calcul individuelle, comme des machines virtuelles ou un appareil de passerelle physique. Elle tire parti des réseaux à définition logicielle, ce qui la rend hautement résiliente. 

### <a name="scalability"></a>Extensibilité
NAT peut être associé à un sous-réseau et être utilisée par toutes les ressources de calcul de ce sous-réseau. En outre, tous les sous-réseaux d’un réseau virtuel peuvent tirer parti de la même ressource. Quand elle est associée à un préfixe d’adresse IP publique, elle est automatiquement mise à l’échelle en fonction du nombre d’adresses IP nécessaires pour le trafic sortant.

### <a name="performance"></a>Performances
NAT n’aura pas d’impact sur la bande passante réseau de vos ressources de calcul, car il s’agit d’un service réseau à définition logicielle. En savoir plus sur les [performances des passerelles NAT](nat-gateway-resource.md#performance).


## <a name="vnet-nat-basics"></a>Concepts de base du NAT de réseau virtuel

NAT peut être créé dans une zone de disponibilité spécifique et dispose d’une redondance intégrée dans la zone spécifiée. NAT est non zonal par défaut. Quand vous créez des scénarios de [zones de disponibilité](../../availability-zones/az-overview.md), NAT peut être isolé dans une zone spécifique. C’est ce qu’on appelle un déploiement zonal.

NAT a déjà fait l’objet d’un scale-out complet dès le départ. Aucune opération de montée en puissance ni de scale-out n’est nécessaire.  Azure gère le fonctionnement de NAT pour vous.  NAT a toujours plusieurs domaines d’erreur et peut supporter plusieurs défaillances sans interruption de service.

* Une connectivité sortante peut être définie pour chaque sous-réseau avec NAT.  Plusieurs sous-réseaux au sein du même réseau virtuel peuvent avoir différents services NAT. Un sous-réseau est configuré en spécifiant la ressource de passerelle NAT à utiliser.  Tout le trafic sortant du sous-réseau est traité par NAT automatiquement sans aucune configuration par le client.  Les routes définies par l’utilisateur ne sont pas nécessaires. NAT est prioritaire sur les autres scénarios de trafic sortant et remplace la destination Internet par défaut d’un sous-réseau.
* Il prend en charge seulement les protocoles TCP et UDP. ICMP n’est pas pris en charge.
* Une ressource de passerelle NAT peut utiliser ceci :

  * Adresse IP publique
  * Préfixe d’adresse IP publique
* NAT est compatible avec les ressources d’adresses IP publiques ou les ressources de préfixes d’adresses IP publiques de la référence SKU Standard, ou avec une combinaison des deux. Vous pouvez utiliser directement un préfixe d’adresse IP publique ou distribuer les adresses IP publiques du préfixe entre plusieurs ressources de passerelle NAT. NAT nettoie tout le trafic vers la plage d’adresses IP du préfixe. Les ressources de base, comme Équilibreur de charge de base ou Adresse IP publique de base ne sont pas compatibles avec NAT.  Les ressources de base doivent être placées sur un sous-réseau qui n’est pas associé à une passerelle NAT.
* NAT ne peut pas être associé à une adresse IP publique IPv6 ou à un préfixe IP public IPv6. Il peut cependant être associé à un sous-réseau à double pile.
* NAT autorise la création de flux depuis le réseau virtuel vers les services en dehors de votre réseau virtuel. Le trafic de retour provenant d’Internet est uniquement autorisé en réponse à un flux actif. Les services en dehors de votre réseau virtuel ne peuvent pas établir de connexion aux instances.
* NAT ne peut pas s’étendre sur plusieurs réseaux virtuels.
* NAT ne peut pas être déployé dans un [sous-réseau de passerelle](../../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsub)
* Le côté privé de NAT (instances de machine virtuelle ou autres ressources de calcul) envoie des paquets de réinitialisation TCP pour les tentatives de communication sur une connexion TCP qui n’existe pas. Par exemple, des connexions ont atteint le délai d’inactivité. Le paquet reçu suivant retourne une réinitialisation TCP à l’adresse IP privée pour signaler et forcer la fermeture de la connexion. Le côté public de NAT ne génère pas de paquets de réinitialisation TCP ni tout autre trafic.  Seul le trafic produit par le réseau virtuel du client est émis.
* Un délai d’inactivité TCP par défaut de 4 minutes est utilisé et peut être augmenté à 120 minutes maximum. Toute activité sur un flux peut également réinitialiser le délai d’inactivité, y compris les conservations de connexion active TCP.

## <a name="pricing-and-sla"></a>Tarifs et contrat SLA

Pour plus d'informations sur les tarifs, consultez [Tarification des réseaux virtuels](https://azure.microsoft.com/pricing/details/virtual-network). Le chemin des données NAT a une disponibilité d’au moins 99,9 %.

## <a name="next-steps"></a>Étapes suivantes

* Découvrez [comment obtenir une meilleure connectivité sortante à l’aide d’une passerelle NAT Azure](https://www.youtube.com/watch?v=2Ng_uM0ZaB4).
* Apprenez-en davantage sur la [ressource de passerelle NAT](./nat-gateway-resource.md).
* Pour en savoir plus, consultez [Métriques des passerelles NAT](./nat-metrics.md).
