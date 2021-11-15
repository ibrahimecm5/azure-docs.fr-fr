---
title: SNAT (Source Network Address Translation) pour les connexions sortantes
titleSuffix: Azure Load Balancer
description: Découvrez comment Azure Load Balancer est utilisé pour la connectivité Internet sortante (SNAT).
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: conceptual
ms.custom: contperf-fy21q1
ms.date: 07/01/2021
ms.author: allensu
ms.openlocfilehash: 3e01660a7cb25a0d5df91908f033fdab59254692
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131576203"
---
# <a name="using-source-network-address-translation-snat-for-outbound-connections"></a>Utilisation de SNAT (Source Network Address Translation) pour les connexions sortantes

Certains scénarios requièrent des machines virtuelles ou des instances de calcul pour disposer d’une connectivité sortante à Internet. Les adresses IP front-end d’un équilibreur de charge public Azure peuvent être utilisées pour fournir une connectivité sortante à Internet pour des instances back-end. Cette configuration utilise la **traduction d’adresses réseau source (SNAT)** pour traduire l’adresse IP de la machine virtuelle en adresse IP publique d’équilibreur de charge. La SNAT mappe l’adresse IP du back-end sur l’adresse IP publique de votre équilibreur de charge. SNAT permet d’empêcher des sources externes d’avoir une adresse directe vers les instances principales.  

## <a name="azures-outbound-connectivity-methods"></a><a name="scenarios"></a>Méthodes de connectivité sortante d’Azure

La connectivité sortante à Internet peut être activée des manières suivantes :

| # | Méthode | Type d’allocation des ports | Qualité de production ? | Rating |
| ------------ | ------------ | ------ | ------------ | ------------ |
| 1 | Utilisation d’une ou plusieurs adresse(s) IP frontale(s) d’un équilibreur de charge pour le trafic sortant via des Règles de trafic sortant | Statique, explicite | Oui, mais pas à l’échelle | OK | 
| 2 | Association d’une passerelle NAT au sous-réseau | Statique, explicite | Oui | La meilleure | 
| 3 | Attribution d’une adresse IP publique à la machine virtuelle | Statique, explicite | Oui | OK | 
| 4 | Utilisation de la ou des adresse(s) IP frontale(s) d’un équilibreur de charge pour les messages sortants (et entrants) | Implicite | No | Deuxième pire |
| 5 | Utilisation de [l’accès sortant par défaut](../virtual-network/ip-services/default-outbound-access.md) | Implicite | Non | Pire |

## <a name="using-the-frontend-ip-address-of-a-load-balancer-for-outbound-via-outbound-rules"></a><a name="outboundrules"></a>Utilisation de l’adresse IP frontale d’un équilibreur de charge pour le trafic sortant via des règles de trafic sortant

Les règles de trafic sortant vous permettent de définir explicitement la traduction d’adresses réseau sources (SNAT) pour un équilibreur de charge standard public. 

Cette configuration vous permet d’utiliser la ou les adresse(s) IP publique(s) de votre équilibreur de charge pour la connectivité sortante des instances principales.

Cette configuration permet :

- L’usurpation d’adresse IP
- Simplification de vos listes d’autorisation
- La réduction du nombre de ressources IP publiques pour le déploiement

Avec les règles de trafic sortant, vous disposez d’un contrôle déclaratif complet sur la connectivité Internet sortante. Les règles de trafic sortant vous permettent de mettre à l’échelle et de régler cette capacité en fonction de vos besoins spécifiques.

Pour plus d’informations sur les règles de trafic sortant, consultez [Règles de trafic sortant](outbound-rules.md).

>[!Important]
> Lorsqu’un pool principal est configuré par adresse IP, il se comporte comme un équilibreur de charge de base avec l’option sortant par défaut activée. Pour une configuration et des applications sécurisées par défaut avec des besoins sortants exigeants, configurez le pool principal par carte réseau.

## <a name="associating-a-nat-gateway-to-the-subnet"></a>Association d’une passerelle NAT au sous-réseau

Le NAT de Réseau virtuel simplifie la connectivité Internet uniquement sortante pour les réseaux virtuels. Quand il est configuré sur un sous-réseau, toute la connectivité sortante utilise vos adresses IP publiques statiques spécifiées. Une connectivité sortante est possible sans équilibreur de charge ni adresses IP publiques directement attachées aux machines virtuelles. NAT est complètement managé et hautement résilient.

L’utilisation d’une passerelle NAT est la meilleure méthode pour la connectivité sortante. Une passerelle NAT est hautement extensible, fiable et n’a pas les mêmes préoccupations en matière d’épuisement des ports SNAT.

Pour plus d’informations sur le service NAT de réseau virtuel Azure, consultez [Qu’est-ce que le service NAT de réseau virtuel ?](../virtual-network/nat-gateway/nat-overview.md)

##  <a name="assigning-a-public-ip-to-the-virtual-machine"></a>Attribution d’une adresse IP publique à la machine virtuelle

 | Associations | Méthode | Protocoles IP |
 | ---------- | ------ | ------------ |
 | Adresse IP publique sur la carte réseau de la machine virtuelle | [SNAT (Source Network Address Translation, traduction d’adresses réseau sources)](#snat) </br> n’est pas utilisée. | Protocole TCP (Transmission Control Protocol) </br> Protocole UDP (User Datagram Protocol) </br> Protocole ICMP (Internet Control Message Protocol) </br> Protection ESP (Encapsulating Security Payload) |

 Tout le trafic est retourné au client demandeur à partir de l’adresse IP publique de la machine virtuelle (IP au niveau de l’instance).
 
 Azure utilise l’IP publique attribuée à la configuration IP de la carte d’interface réseau de l’instance pour tous les flux sortants. L’instance a tous les ports éphémères disponibles. Peu importe si la machine virtuelle est équilibrée en charge ou non. Ce scénario prend le pas sur les autres. 

 Une adresse IP publique affectée à une machine virtuelle constitue une relation 1:1 (non pas une relation 1-à-plusieurs) ; elle est implémentée comme un NAT 1:1 sans état.

## <a name="using-the-frontend-ip-address-of-a-load-balancer-for-outbound-and-inbound"></a><a name="snat"></a> Utilisation de l’adresse IP frontale d’un équilibreur de charge pour les messages sortants (et entrants)

>[!NOTE]
> Cette méthode n’est **PAS recommandée** pour les charges de travail de production, car elle augmente le risque d’épuisement des ports. Évitez d’utiliser cette méthode pour les charges de travail de production afin d’éviter les échecs de connexion potentiels dus à l’épuisement des ports SNAT. 

Une ressource dans le serveur principal d’un équilibreur de charge sans la configuration suivante crée des connexions sortantes via l’adresse IP frontale de l’équilibreur de charge. La ressource utilise la SNAT par défaut (également appelé accès sortant par défaut).

* Règles de trafic sortant
* Adresses IP publiques au niveau de l’instance
* La passerelle NAT est configurée

## <a name="default-outbound-access"></a>Accès sortant par défaut

Une ressource sans la configuration suivante crée des connexions sortantes par le biais de la SNAT par défaut. 

* Règles de trafic sortant
* Adresses IP publiques au niveau de l’instance
* La passerelle NAT est configurée
* Équilibrage de charge

Cet accès est connu sous le nom d’accès sortant par défaut. Un autre exemple de scénario utilisant la fonctionnalité SNAT par défaut est une machine virtuelle dans Azure (sans les associations mentionnées ci-dessus). Dans ce cas, la connectivité sortante est fournie par l’adresse IP d’accès sortant par défaut. Cette adresse IP est une adresse IP dynamique affectée par Azure que vous ne pouvez pas contrôler. La SNAT par défaut n’est pas recommandée pour les charges de travail de production.

### <a name="what-are-snat-ports"></a>Que sont les ports SNAT ?

Les ports sont utilisés pour générer des identificateurs uniques, eux-mêmes utilisés pour gérer des flux distincts. Internet utilise cinq tuples pour fournir cette distinction.

Si un port est utilisé pour des connexions entrantes, il a un **écouteur** pour les requêtes de connexions entrantes sur ce port. Ce port ne peut pas être utilisé pour les connexions sortantes. Pour établir une connexion sortante, utilisez un **port éphémère** pour fournir un port à la destination, afin de communiquer et de maintenir un flux de trafic distinct. Quand ces ports éphémères sont utilisés pour SNAT, ils sont appelés **ports SNAT**

Par définition, chaque adresse IP a 65 535 ports. Chaque port peut être utilisé pour les connexions entrantes ou sortantes pour les protocoles TCP (Transmission Control Protocol) et UDP (User Datagram Protocol). Quand une adresse IP publique est ajoutée en tant qu’IP frontale à un équilibreur de charge, 64 000 ports sont éligibles pour SNAT. Alors que toutes les IP publiques ajoutées en tant qu’adresses IP frontales peuvent être allouées, les adresses IP frontales sont consommées une par une. Par exemple, si 2 instances back-end se voient allouer 64 000 ports chacune, avec accès à 2 adresses IP frontales, les 2 instances back-end consommeront les ports de la première adresse IP frontale jusqu’à ce que les 64 000 ports soient épuisés.  

Un port utilisé pour un équilibrage de charge ou une règle NAT de trafic entrant utilise huit ports des ports 64 000. Cette utilisation permet de réduire le nombre de ports éligibles pour SNAT. Si une règle NAT de trafic entrant ou d’équilibrage de charge se trouve dans la même plage de huit qu’une autre règle, elle ne consomme pas de ports supplémentaires. 

### <a name="how-does-default-snat-work"></a>Comment fonctionne le SNAT par défaut ?

Quand la machine virtuelle crée un flux sortant, Azure traduit l’adresse IP source en adresse IP éphémère. Cette traduction s’effectue par le biais de la [SNAT](#snat). 

Si vous utilisez SNAT par défaut par le biais d’une règle d’équilibrage de charge, les ports SNAT sont préalloués comme décrit dans le [tableau d’allocation des ports SNAT par défaut](#snatporttable).
 
Lorsque vous utilisez un équilibreur de charge interne standard, il n’y a pas d’utilisation d’adresse IP éphémère pour SNAT. Cette fonctionnalité prend en charge la sécurité par défaut. Cette fonctionnalité garantit que toutes les adresses IP utilisées par les ressources sont configurables et peuvent être réservées. 

Pour obtenir une connectivité sortante à Internet lors de l’utilisation d’un équilibreur de charge interne standard, configurez :

- Une adresse IP publique au niveau de l’instance 
- Passerelle NAT
- Ajouter des instances principales à un équilibreur de charge public standard avec une règle de trafic sortant configurée.  

### <a name="what-is-the-ip-for-default-snat"></a>Quelle est l’adresse IP SNAT par défaut ?

Quand la machine virtuelle crée un flux sortant, Azure convertit l’adresse IP source en adresse IP source publique spécifiée dynamiquement. Cette adresse IP publique **n’est pas configurable** et ne peut pas être réservée. Cette adresse n’est pas comptabilisée dans la limite des ressources IP publiques de l’abonnement. 

L’adresse IP publique est publiée et une nouvelle adresse IP publique est demandée si vous redéployez ce qui suit : 

 * Machine virtuelle
 * Groupe à haute disponibilité
 * Jeu de mise à l’échelle de machine virtuelle 

>[!NOTE]
> Cette méthode n’est **PAS recommandée** pour les charges de travail de production, car elle augmente le risque d’épuisement des ports. N’hésitez pas à utiliser cette méthode pour les charges de travail de production afin d’éviter les échecs de connexion potentiels. 

| Type | Comportement sortant | 
| ------------ | ------ | 
| Équilibreur de charge public Standard | Utilisation des IP front-end de l’équilibreur de charge pour la SNAT |
| Équilibreur de charge interne Standard | Aucune connectivité Internet, sécurisé par défaut | 
| Équilibreur de charge public de base | Utilisation des IP front-end de l’équilibreur de charge pour la SNAT |
| Équilibreur de charge interne de base | SNAT avec adresse IP dynamique inconnue |

## <a name="exhausting-ports"></a> Épuisement des ports

Chaque connexion à une même adresse IP de destination et au même port de destination utilisera un port SNAT. Cette connexion gère un **flux de trafic** distinct depuis l’instance ou le **client** back-end vers un **serveur**. Ce processus fournit au serveur un port distinct vers lequel envoyer le trafic. Sans ce processus, l’ordinateur client ignore dans quel flux trouver tel ou tel paquet.

Imaginez que plusieurs navigateurs vont vers https://www.microsoft.com, à savoir :

* IP de destination = 23.53.254.142
* Port de destination = 443
* Protocole = TCP

Sans ports de destination différents pour le trafic de retour (le port SNAT utilisé pour établir la connexion), le client n’a aucun moyen de séparer le résultat d’une requête d’un autre.

Le nombre de connexions sortantes peut augmenter rapidement. L’instance back-end risque de se retrouver en manque de ports. Utilisez la fonctionnalité de **réutilisation des connexions** dans votre application. Sans la **réutilisation des connexions**, le risque d’**épuisement des ports** SNAT augmente. Pour plus d’informations sur le regroupement de connexions avec Azure App Service, consultez [Résolution des erreurs intermittentes de connexion sortante dans Azure App Service](../app-service/troubleshoot-intermittent-outbound-connection-errors.md#avoiding-the-problem).

Un épuisement des ports provoque l’échec des nouvelles connexions sortantes vers une adresse IP de destination. Les connexions sont correctement établies lorsqu’un port est disponible. Cet épuisement se produit lorsque les 64 000 ports à partir d’une adresse IP sont répartis sur de nombreuses instances back-end. Pour obtenir des conseils sur l’atténuation de l’épuisement des ports SNAT, consultez ce [guide de résolution des problèmes](./troubleshoot-outbound-connection.md).  

Pour les connexions TCP, l’équilibreur de charge utilise un seul port SNAT pour chaque adresse IP et port de destination. Ce multiusage permet plusieurs connexions à la même adresse IP de destination avec le même port SNAT. Ce multiusage est limité si la connexion ne dirige pas vers différents ports de destination.

Pour les connexions UDP, l’équilibreur de charge utilise un algorithme nommé « **port-restricted cone NAT** » (ou « NAT à cône restrictif sur les ports »), qui consomme un port SNAT par adresse IP de destination, quel que soit le port de destination. 

Cela permet de réutiliser un port pour un nombre illimité de connexions. Le port est réutilisé uniquement si l’adresse IP ou le port de destination est différent.

## <a name="default-port-allocation"></a><a name="preallocatedports"></a> Allocation des ports par défaut

Chaque adresse IP publique allouée en tant qu’adresse IP front-end de votre équilibreur de charge reçoit 64 000 ports SNAT pour ses membres de pool back-end. Les ports ne peuvent pas être partagés avec des membres du pool back-end. Une plage de ports SNAT ne peut être utilisée que par une seule instance back-end pour garantir le routage correct des paquets de retour. 

Si vous utilisez l’allocation automatique de port SNAT sortant par le biais d’une règle d’équilibrage de charge, la table d’allocation définira votre allocation de port pour chaque adresse IP.

Le tableau suivant <a name="snatporttable"></a>présente les préaffectations de ports SNAT pour les différents niveaux de tailles de pool back-end :

| Taille du pool (instances de machine virtuelle) | Ports de traduction d’adresses réseau sources par défaut par configuration IP |
| --- | --- |
| 1-50 | 1 024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |
| 401-800 | 64 |
| 801-1 000 | 32 | 

## <a name="manual-port-allocation"></a><a name="preallocatedports"></a> Allocation manuelle des ports

L’allocation manuelle du port SNAT en fonction de la taille du pool backend et du nombre de configurations d’adresses IP frontales peut permettre d’éviter l’épuisement des SNAT. 

Vous pouvez allouer manuellement les ports SNAT en fonction des « ports par instance » ou du « nombre maximal d’instances backend ». Si vous avez des machines virtuelles dans le backend, il est recommandé d’allouer des ports en fonction des « ports par instance » pour obtenir une utilisation maximale du port SNAT. 

Les ports par instance doivent être calculés comme indiqué ci-dessous : 

**Nombre d’adresses IP frontales * 64 000 / Nombre d’instances back-end** 

Si vous avez Virtual Machine Scale Sets dans le serveur principal, il est recommandé d’allouer des ports en fonction du « nombre maximal d’instances back-end ». Si le nombre de machines virtuelles ajoutées au serveur principal est supérieur aux ports SNAT restants autorisés, il est possible que l’opération de scale-up du groupe de machines virtuelles identiques soit bloquée ou que les nouvelles machines virtuelles ne reçoivent pas suffisamment de ports SNAT. 

## <a name="constraints"></a>Contraintes

*   Lorsqu’une connexion est inactive et qu’aucun nouveau paquet n’est envoyé, les ports sont libérés après un délai allant de 4 à 120 minutes.
  * Ce délai peut être configuré à l’aide des règles de trafic sortant.
*   Chaque adresse IP fournit 64 000 ports qui peuvent être utilisés pour la SNAT.
*   Chaque port peut être utilisé pour les connexions TCP et UDP à une adresse IP de destination
  * Un port UDP SNAT est nécessaire si le port de destination est unique ou non. Pour chaque connexion UDP à une adresse IP de destination, un port UDP SNAT est utilisé.
  * Un port TCP SNAT peut être utilisé pour plusieurs connexions à la même adresse IP de destination, à condition que les ports de destination soient différents.
*   L’épuisement des ports SNAT se produit lorsqu’une instance back-end n’a pas assez de ports SNAT. Un équilibreur de charge peut toujours avoir des ports SNAT inutilisés. Si une instance back-end utilise plus de ports SNAT que ceux qui lui sont alloués, elle ne pourra pas établir de nouvelles connexions sortantes.
*   Les paquets fragmentés seront supprimés, sauf si le trafic sortant se fait via une adresse IP publique au niveau de l’instance sur la carte réseau de la machine virtuelle.
*   Les configurations IP secondaires d’une interface réseau n’effectuent pas de communication sortante (sauf si une IP publique lui est associée) via Load Balancer.

## <a name="next-steps"></a>Étapes suivantes

*   [Résoudre les échecs de connexion sortante provoqués par l’épuisement des ports SNAT](./troubleshoot-outbound-connection.md)
*   [Passez en revue les métriques SNAT](./load-balancer-standard-diagnostics.md#how-do-i-check-my-snat-port-usage-and-allocation) et familiarisez-vous avec la méthode appropriée pour les filtrer, les fractionner et les afficher.
