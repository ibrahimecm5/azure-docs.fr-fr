---
title: Conception de réseaux virtuels avec des ressources de passerelle NAT
titleSuffix: Azure Virtual Network NAT
description: Découvrez comment concevoir des réseaux virtuels avec des ressources de passerelle NAT.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: nat
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/11/2021
ms.author: allensu
ms.openlocfilehash: d0af0d38ef53e4ba90dcf0e7f4cbcc60c52b87a8
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132373134"
---
# <a name="designing-virtual-networks-with-nat-gateway-resources"></a>Conception de réseaux virtuels avec des ressources de passerelle NAT

Les ressources de passerelle NAT font partie du [service NAT de Réseau virtuel](nat-overview.md) et offrent une connectivité Internet sortante à un ou plusieurs sous-réseaux d’un réseau virtuel. Le sous-réseau du réseau virtuel indique quelle passerelle NAT est utilisée. Le service NAT fournit la traduction d’adresses réseau sources (SNAT) à un sous-réseau.  Les ressources de passerelle NAT spécifient les adresses IP statiques utilisées par les machines virtuelles lors de la création de flux sortants. Les adresses IP statiques proviennent de ressources d’adresses IP publiques, de ressources de préfixes d’adresses IP publiques ou des deux. Si une ressource de préfixe d’adresse IP publique est utilisée, toutes les adresses IP de l’ensemble de la ressource de préfixe d’adresse IP publique sont consommées par une ressource de passerelle NAT. Une ressource de passerelle NAT peut utiliser jusqu’à 16 adresses IP statiques.

<p align="center">
  <img src="media/nat-overview/flow-direction1.svg" alt="Figure depicts a NAT gateway resource that consumes all IP addresses for a public IP prefix and directs that traffic to and from two subnets of virtual machines and a virtual machine scale set." width="256" title="Service NAT de Réseau virtuel pour le trafic sortant vers Internet">
</p>

*Figure : Service NAT de Réseau virtuel pour le trafic sortant vers Internet*

## <a name="how-to-deploy-nat"></a>Comment déployer le service NAT

La configuration et l’utilisation de la passerelle NAT sont volontairement simples :  

Ressource de passerelle NAT :
- Créer une ressource de passerelle NAT régionale ou zonale,
- Affectez des adresses IP.
- Si nécessaire, modifiez le délai d’inactivité TCP (facultatif).  Vérifiez les [minuteurs](#timers) <ins>avant</ins> de changer la valeur par défaut.

Réseau virtuel :
- Configurez un sous-réseau de réseau virtuel pour utiliser une passerelle NAT.

Les routes définies par l’utilisateur ne sont pas nécessaires.


## <a name="design-guidance"></a>Conseils pour la conception

Passez en revue cette section pour vous familiariser avec les considérations relatives à la conception des réseaux virtuels avec le service NAT.  

### <a name="connecting-to-azure-services"></a>Connexion aux services Azure

Lors de la connexion aux services Azure, l’approche recommandée consiste à utiliser une [liaison privée](../../private-link/private-link-overview.md). 

La liaison privée lie les ressources de service Azure à votre réseau virtuel et contrôle l’accès à vos ressources de service Azure. Par exemple, lorsque vous accédez au Stockage Azure, utilisez un point de terminaison privé pour le stockage pour vous assurer que votre connexion est entièrement privée.

### <a name="connecting-to-the-internet"></a>Connexion à Internet

NAT est recommandé pour les scénarios sortants pour toutes les charges de travail de production où vous devez vous connecter à un point de terminaison public. Les scénarios suivants sont des exemples de la manière de garantir la coexistence de la passerelle entrante avec la passerelle NAT pour le trafic sortant.

#### <a name="nat-and-vm-with-instance-level-public-ip"></a>Service NAT et machine virtuelle avec une adresse IP publique au niveau de l’instance

<p align="center">
  <img src="media/nat-overview/flow-direction2.svg" alt="Figure depicts a NAT gateway that supports outbound traffic to the internet from a virtual network and inbound traffic with an instance-level public IP." width="300" title="Service NAT de Réseau virtuel et machine virtuelle avec une adresse IP publique au niveau de l’instance">
</p>

*Figure : Service NAT de Réseau virtuel et machine virtuelle avec une adresse IP publique au niveau de l’instance*

| Sens | Ressource |
|:---:|:---:|
| Trafic entrant | Machine virtuelle avec une adresse IP publique au niveau de l’instance |
| Règle de trafic sortant | Passerelle NAT |

La machine virtuelle utilise la passerelle NAT pour le trafic sortant.  Le trafic entrant n’est pas affecté.

#### <a name="nat-and-vm-with-standard-public-load-balancer"></a>NAT et machine virtuelle avec équilibreur de charge public Standard

<p align="center">
  <img src="media/nat-overview/flow-direction3.svg" alt="Figure depicts a NAT gateway that supports outbound traffic to the internet from a virtual network and inbound traffic with a public load balancer." width="350" title="Service NAT de Réseau virtuel et machine virtuelle avec équilibreur de charge public">
</p>

*Figure : Service NAT de Réseau virtuel et machine virtuelle avec équilibreur de charge public*

| Sens | Ressource |
|:---:|:---:|
| Trafic entrant | Équilibreur de charge public |
| Règle de trafic sortant | Passerelle NAT |

Toute configuration de trafic sortant à partir d’une règle d’équilibrage de charge ou de règles de trafic sortant est remplacée par la passerelle NAT.  Le trafic entrant n’est pas affecté.

#### <a name="nat-and-vm-with-instance-level-public-ip-and-standard-public-load-balancer"></a>Service NAT et machine virtuelle avec une adresse IP publique au niveau de l’instance et un équilibreur de charge public Standard

<p align="center">
  <img src="media/nat-overview/flow-direction4.svg" alt="Figure depicts a NAT gateway that supports outbound traffic to the internet from a virtual network and inbound traffic with an instance-level public IP and a public load balancer." width="425" title="Service NAT de Réseau virtuel et machine virtuelle avec une adresse IP publique au niveau de l’instance et un équilibreur de charge public">
</p>

*Figure : Service NAT de Réseau virtuel et machine virtuelle avec une adresse IP publique au niveau de l’instance et un équilibreur de charge public*

| Sens | Ressource |
|:---:|:---:|
| Trafic entrant | Machine virtuelle avec une adresse IP publique au niveau de l’instance et un équilibreur de charge public |
| Règle de trafic sortant | Passerelle NAT |

Toute configuration de trafic sortant à partir d’une règle d’équilibrage de charge ou de règles de trafic sortant est remplacée par la passerelle NAT.  La machine virtuelle utilise également la passerelle NAT pour le trafic sortant.  Le trafic entrant n’est pas affecté.

## <a name="performance"></a>Performances

Chaque ressource de passerelle NAT peut fournir un débit maximal de 50 Gbits/s. Vous pouvez diviser vos déploiements entre plusieurs sous-réseaux et affecter à chaque sous-réseau ou groupe de sous-réseaux une passerelle NAT pour un scale-out.

Chaque passerelle NAT peut prendre en charge 64 000 flux respectivement pour TCP et UDP par adresse IP sortante attribuée.  Consultez la section suivante sur la traduction d’adresses réseau sources (SNAT, Source Network Address Translation) pour plus de détails, ainsi que l’[article dédié à la résolution des problèmes](./troubleshoot-nat.md) pour obtenir des conseils de résolution des problèmes spécifiques.

## <a name="source-network-address-translation"></a>Traduction d’adresses réseau sources

La traduction d’adresses réseau sources (SNAT) réécrit la source d’un flux pour qu’il provienne d’une autre adresse IP.  Les ressources de passerelle NAT utilisent une variante de SNAT communément appelée traduction d’adresses de port (PAT). PAT réécrit l’adresse source et le port source. Avec SNAT, il n’existe aucune relation fixe entre le nombre d’adresses privées et leurs adresses publiques traduites.  

### <a name="fundamentals"></a>Notions de base

Examinons un exemple de quatre flux pour expliquer le concept de base.  La passerelle NAT utilise la ressource d’adresse IP publique 65.52.1.1 et la machine virtuelle établit des connexions à l’adresse 65.52.0.1.

| Flux | Tuple source | Tuple de destination |
|:---:|:---:|:---:|
| 1 | 192.168.0.16:4283 | 65.52.0.1:80 |
| 2 | 192.168.0.16:4284 | 65.52.0.1:80 |
| 3 | 192.168.0.17.5768 | 65.52.0.1:80 |

Ces flux peuvent ressembler à ceci après l’application de PAT :

| Flux | Tuple source | Tuple source après SNAT | Tuple de destination | 
|:---:|:---:|:---:|:---:|
| 1 | 192.168.0.16:4283 | **65.52.1.1:1234** | 65.52.0.1:80 |
| 2 | 192.168.0.16:4284 | **65.52.1.1:1235** | 65.52.0.1:80 |
| 3 | 192.168.0.17.5768 | **65.52.1.1:1236** | 65.52.0.1:80 |

La destination voit que la source du flux est 65.52.0.1 (Tuple source SNAT) avec le port attribué indiqué.  PAT, comme indiqué dans le tableau précédent, est également appelé SNAT de déguisement de port.  Plusieurs sources privées se font passer pour une adresse IP et un port.  

#### <a name="source-snat-port-reuse"></a>réutilisation des ports source (SNAT)

Les passerelles NAT réutilisent de façon opportuniste les ports sources (SNAT).  L’exemple suivant illustre ce concept comme un flux supplémentaire pour l’ensemble de flux précédent.  La machine virtuelle dans l’exemple est un flux vers l’adresse 65.52.0.2.

| Flux | Tuple source | Tuple de destination |
|:---:|:---:|:---:|
| 4 | 192.168.0.16:4285 | 65.52.0.2:80 |

Une passerelle NAT translatera probablement le flux 4 vers un port également utilisable pour d’autres destinations.  Pour plus d’informations sur le dimensionnement correct de l’approvisionnement de votre adresse IP, consultez [Mise à l’échelle](#scaling).

| Flux | Tuple source | Tuple source après SNAT | Tuple de destination | 
|:---:|:---:|:---:|:---:|
| 4 | 192.168.0.16:4285 | 65.52.1.1:**1234** | 65.52.0.2:80 |

Ne prenez pas de dépendance vis-à-vis de la façon dont les ports sources sont affectés dans l’exemple ci-dessus.  L’illustration précédente montre uniquement le concept de base.

La traduction d’adresses réseau sources (SNAT) fournie par le service NAT diffèrent de [Load Balancer](../../load-balancer/load-balancer-outbound-connections.md) sous plusieurs aspects.

### <a name="on-demand"></a>À la demande

NAT fournit des ports SNAT à la demande pour les nouveaux flux de trafic sortant. Tous les ports SNAT disponibles dans l’inventaire sont utilisés par toute machine virtuelle sur les sous-réseaux configurés avec NAT. 

<p align="center">
  <img src="media/nat-overview/lb-vnnat-chart.svg" alt="Figure depicts inventory of all available SNAT ports used by any virtual machine on subnets configured with N A T." width="550" title="SNAT de trafic sortant à la demande du service NAT de Réseau virtuel">
</p>

*Figure : SNAT de trafic sortant à la demande du service NAT de Réseau virtuel*

Toute configuration IP d’une machine virtuelle peut créer des flux sortants à la demande si besoin.  Aucune préallocation, par planification d’instance incluant le surprovisionnement du pire cas par instance, n’est nécessaire.  

<p align="center">
  <img src="media/nat-overview/exhaustion-threshold.svg" alt="Figure depicts inventory of all available SNAT ports used by any virtual machine on subnets configured with N A T with exhaustion threshold." width="550" title="Différences dans les scénarios d’épuisement">
</p>

*Figure : Différences dans les scénarios d’épuisement*

Une fois qu’un port SNAT est libéré, il devient disponible pour être utilisé par toute machine virtuelle sur des sous-réseaux configurés avec NAT.  L’allocation à la demande permet aux charges de travail dynamiques et divergentes sur des sous-réseaux d’utiliser des ports SNAT selon leurs besoins.  Tant qu’un inventaire des ports SNAT est disponible, les flux SNAT aboutissent. Les zones réactives de port SNAT bénéficient plutôt de l’inventaire plus grand. Les ports SNAT ne sont pas laissés inutilisés pour les machines virtuelles qui n’en ont pas activement besoin.

### <a name="scaling"></a>Mise à l'échelle

La mise à l’échelle NAT est principalement une fonction de gestion de l’inventaire des ports SNAT partagés et disponibles. NAT a besoin d’un inventaire de ports SNAT suffisant pour traiter les flux sortant maximaux attendus pour tous les sous-réseaux attachés à une ressource de passerelle NAT.  Vous pouvez utiliser des ressources d’adresses publiques IP, des ressources de préfixes d’adresses IP publiques, ou des deux, pour créer un inventaire des ports SNAT.  

>[!NOTE]
>Si vous attribuez une ressource de préfixe d’adresse IP publique, l’ensemble du préfixe d’adresse IP publique entier est utilisé.  Vous ne pouvez pas attribuer une ressource de préfixe d’adresse IP publique puis séparer des adresses IP individuelles pour les attribuer à d’autres ressources.  Si vous voulez attribuer des adresses IP individuelles à partir d’un préfixe d’adresse IP publique à plusieurs ressources, vous devez créer des adresses IP publiques individuelles à partir de la ressource de préfixe d’adresse IP publique, puis les attribuer en fonction des besoins plutôt que d’attribuer la ressource de préfixe d’adresse IP publique elle-même.

SNAT mappe des adresses privées à une ou plusieurs adresses IP publiques, en réécrivant l’adresse source et le port source dans les processus. Une ressource de passerelle NAT utilise 64 000 ports (ports SNAT) par adresse IP publique configurée pour cette traduction. Les ressources de passerelle NAT peuvent comprendre jusqu’à 16 adresses IP et 1 million de ports SNAT. Si une ressource de préfixe d’adresse IP publique est fournie, chaque adresse IP présente dans le préfixe fournit un inventaire des ports SNAT. L’ajout d’autres adresses IP publiques augmente les ports SNAT disponibles dans l’inventaire. Les protocoles TCP et UDP sont des inventaires de ports SNAT distincts qui ne sont pas liés.

Les ressources de passerelle NAT réutilisent de façon opportuniste les ports sources (SNAT). Au titre de conseil de conception à des fins de mise à l’échelle, vous devez partir du principe que chaque flux nécessite un nouveau port SNAT et mettre à l’échelle le nombre total d’adresses IP disponibles pour le trafic sortant.  Vous devez réfléchir soigneusement à la mise à l’échelle que vous concevez, et approvisionner les quantités d’adresses IP en conséquence.

Les ports SNAT vers différentes destinations sont les plus susceptibles d’être réutilisés quand c’est possible. Et lorsque l’épuisement des ports SNAT approche, les flux peuvent échouer.  

Pour un exemple, consultez [Notions de base de SNAT](#source-network-address-translation).


### <a name="protocols"></a>Protocoles

Les ressources de passerelle NAT interagissent avec les en-têtes IP et de transport IP des flux UDP et TCP. Elles sont indépendantes des charges utiles de la couche Application.  Les autres protocoles IP ne sont pas pris en charge.

### <a name="timers"></a>Minuteurs

>[!IMPORTANT]
>Le minuteur spécifiant une longue période d’inactivité peut augmenter inutilement la probabilité d’épuisement des ressources SNAT. Plus la période que vous spécifiez pour le minuteur est longue, plus la durée pendant laquelle la traduction d’adresses réseau (NAT) accapare les ports SNAT est longue, jusqu’à ce que le délai d’inactivité finisse par être atteint. Si le délai d’inactivité de vos flux est dépassé, ces derniers finissent par échouer quand même et consomment inutilement l’inventaire des ports SNAT.  Les flux qui échouent au bout de 2 heures auraient également échoué au bout de 4 minutes (valeur par défaut). L’augmentation du délai d’inactivité est une option de dernier recours qui doit être utilisée avec modération. Si un flux ne devient jamais inactif, il n’est pas affecté par le minuteur d’inactivité.

Le délai d’inactivité TCP peut varier de 4 minutes (valeur par défaut) à 120 minutes (2 heures) pour tous les flux.  De plus, vous pouvez réinitialiser le minuteur d’inactivité avec du trafic en cours de flux.  Un modèle recommandé pour l’actualisation des connexions inactives longues et la détection d’activité sur les points de terminaison consiste à utiliser des conservations de connexion active TCP.  Les conservations de connexion active TCP apparaissent comme des accusés de réception en double sur les points de terminaison, leur surcharge est faible et elles sont invisibles pour la couche Application.

Les minuteurs suivants sont utilisés pour la libération des ports SNAT :

| Minuterie | Valeur |
|---|---|
| TCP FIN | 60 secondes |
| TCP RST | 10 secondes |
| TCP demi-ouvert | 30 secondes |

Un port SNAT peut être réutilisé pour la même adresse IP de destination et le même port de destination au bout de 5 secondes.

>[!NOTE] 
>Ces paramètres de minuteur sont susceptibles d’être modifiés. Les valeurs sont fournies pour faciliter la résolution des problèmes et vous ne devez pas dépendre de minuteurs spécifiques pour le moment.

## <a name="limitations"></a>Limites

- L’équilibreur de charge De base et les adresses IP publiques De base sont incompatibles avec NAT. Utilisez à la place des équilibreurs de charge de référence SKU Standard et des adresses IP publiques.
- La fragmentation d’IP n’est pas prise en charge via la passerelle NAT.

## <a name="next-steps"></a>Étapes suivantes

* Découvrez le [service NAT de réseau virtuel](nat-overview.md).
* Découvrez les [métriques et les alertes pour les ressources de passerelle NAT](nat-metrics.md).
* Découvrez la [résolution des problèmes liés aux ressources de passerelle NAT](troubleshoot-nat.md).
