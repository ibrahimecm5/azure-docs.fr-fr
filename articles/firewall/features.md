---
title: Fonctionnalités du Pare-feu Azure Standard
description: Découvrir les fonctionnalités du service Pare-feu Azure
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 07/30/2021
ms.author: victorh
ms.openlocfilehash: 0c9b871197085a6a220482c3b9d1d35f25d5b427
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132136754"
---
# <a name="azure-firewall-standard-features"></a>Fonctionnalités du Pare-feu Azure Standard

Le [Pare-feu Azure](overview.md) Standard est un service de sécurité réseau cloud managé qui protège vos ressources de réseau virtuel Azure.

:::image type="content" source="media/features/firewall-standard.png" alt-text="Fonctionnalités du Pare-feu Azure Standard":::

Le pare-feu Azure inclut les fonctionnalités suivantes :

- Haute disponibilité intégrée
- Zones de disponibilité
- Extensibilité du cloud sans limites
- Règles de filtrage des noms de domaine complets de l’application
- Règles de filtrage du trafic réseau
- Balises FQDN
- Balises de service
- Informations sur les menaces
- Proxy DNS
- Système DNS personnalisé
- FQDN dans les règles de réseau
- Déploiement sans adresse IP publique en mode Tunnel forcé
- Prise en charge du mode SNAT sortant
- Prise en charge du trafic DNAT entrant
- Adresses IP publiques multiples
- Journalisation d’Azure Monitor
- Tunneling forcé
- Catégories web
- Certifications

## <a name="built-in-high-availability"></a>Haute disponibilité intégrée

Comme la haute disponibilité est intégrée, aucun équilibreur de charge supplémentaire n’est nécessaire, et vous n’avez rien à configurer.

## <a name="availability-zones"></a>Zones de disponibilité

Le Pare-feu Azure peut être configuré pendant le déploiement pour couvrir plusieurs zones de disponibilité afin de fournir une disponibilité supérieure. Avec les Zones de disponibilité Azure, votre disponibilité s’élève à 99,99 % du temps total. Pour plus d’informations, consultez [Contrat de niveau de service (SLA)](https://azure.microsoft.com/support/legal/sla/azure-firewall/v1_0/) du Pare-feu Azure. Un contrat de niveau de service de 99,99 % est offert quand deux Zones de disponibilité ou plus sont sélectionnées.

Vous pouvez également associer le Pare-feu Azure à une zone spécifique uniquement pour des raisons de proximité, en utilisant le contrat de niveau de service standard garantissant un taux de disponibilité de 99,95 %.

Il n’existe aucun coût supplémentaire pour un pare-feu déployé dans une Zone de disponibilité. Par contre, il existe des coûts supplémentaires pour les transferts de données entrants et sortants associés aux Zones de disponibilité Azure. Pour plus d’informations, consultez [Détails de la tarification de la bande passante](https://azure.microsoft.com/pricing/details/bandwidth/).

Les Zones de disponibilité de Pare-feu Azure sont disponibles dans les régions prenant en charge les Zones de disponibilité. Pour plus d’informations, consultez [Régions prenant en charge les zones de disponibilité dans Azure](../availability-zones/az-region.md).

> [!NOTE]
> Les Zones de disponibilité ne peuvent être configurées que pendant le déploiement. Vous ne pouvez pas configurer un pare-feu existant pour y inclure des Zones de disponibilité.

Pour plus d’informations sur les Zones de disponibilité, consultez [Régions et Zones de disponibilité dans Azure](../availability-zones/az-overview.md).

## <a name="unrestricted-cloud-scalability"></a>Extensibilité du cloud sans limites

Le service Pare-feu Azure peut évoluer en fonction de vos besoins pour prendre en charge les flux de trafic réseau variables. Vous n’avez donc pas besoin de budgétiser votre trafic de pointe.

## <a name="application-fqdn-filtering-rules"></a>Règles de filtrage des noms de domaine complets de l’application

Vous pouvez limiter le trafic HTTP/S sortant ou le trafic SQL Azure vers une liste spécifiée de noms de domaine complets (FQDN), y compris des caractères génériques. Cette fonctionnalité ne nécessite pas d’arrêt TLS.

## <a name="network-traffic-filtering-rules"></a>Règles de filtrage du trafic réseau

Vous pouvez créer de façon centralisée des règles de filtrage réseau *autoriser* ou *refuser* par protocole, port et adresse IP source et de destination. Le service Pare-feu Azure étant entièrement avec état, il peut distinguer les paquets légitimes pour différents types de connexions. Les règles sont appliquées et consignées entre plusieurs abonnements et réseaux virtuels.

Le Pare-feu Azure prend en charge le filtrage avec état des protocoles réseau de couche 3 et de couche 4. Les protocoles IP de couche 3 peuvent être filtrés en sélectionnant **n’importe quel** protocole dans la règle de réseau et en sélectionnant le caractère générique **\*** pour le port.

## <a name="fqdn-tags"></a>Balises FQDN

Les [balises FQDN](fqdn-tags.md) vous aident à autoriser le trafic réseau du service Azure connu via votre pare-feu. Par exemple, supposons que vous souhaitez autoriser le trafic réseau Windows Update via votre pare-feu. Vous créez une règle d’application et incluez la balise Windows Update. Le trafic réseau provenant de Windows Update peut désormais passer par votre pare-feu.

## <a name="service-tags"></a>Balises de service

Une [balise de service](service-tags.md) représente un groupe de préfixes d’adresses IP qui permet de simplifier la création de règles de sécurité. Vous ne pouvez pas créer votre propre balise de service, ni spécifier les adresses IP incluses dans une balise. Microsoft gère les préfixes d’adresse englobés par la balise de service et met à jour automatiquement la balise de service quand les adresses changent.

## <a name="threat-intelligence"></a>Informations sur les menaces

Le filtrage basé sur [Threat Intelligence](threat-intel.md) peut être activé pour votre pare-feu pour donner l’alerte et rejeter le trafic depuis ou vers des adresses IP et des domaines malveillants connus. Ces adresses IP et domaines proviennent du flux Microsoft Threat Intelligence.

## <a name="dns-proxy"></a>Proxy DNS

Si le proxy DNS est activé, le Pare-feu Azure peut traiter les requêtes DNS d'un ou plusieurs réseaux virtuels et les transférer vers le serveur DNS de votre choix. Cette fonctionnalité essentielle est nécessaire pour disposer d'un filtrage de FQDN fiable dans les règles de réseau. Vous pouvez activer le proxy DNS dans les paramètres du Pare-feu Azure et de la stratégie de pare-feu. Pour en savoir plus sur le proxy DNS, consultez [Paramètres DNS du Pare-feu Azure](dns-settings.md).

## <a name="custom-dns"></a>Système DNS personnalisé

Le système DNS personnalisé vous permet de configurer le Pare-feu Azure pour qu'il utilise votre propre serveur DNS, tout en veillant à ce que les dépendances sortantes du pare-feu soient toujours résolues avec Azure DNS. Vous pouvez configurer un serveur DNS individuel ou plusieurs serveurs dans les paramètres DNS du Pare-feu Azure et de la stratégie de pare-feu. Pour en savoir plus sur le système DNS personnalisé, consultez [Paramètres DNS du Pare-feu Azure](dns-settings.md).

Le Pare-feu Azure peut également résoudre les noms à l’aide du DNS privé Azure. Le réseau virtuel sur lequel réside le Pare-feu Azure doit être lié à la zone privée Azure. Pour en savoir plus, consultez [Utiliser le Pare-feu Azure comme redirecteur DNS avec Private Link](https://github.com/adstuart/azure-privatelink-dns-azurefirewall).

## <a name="fqdn-in-network-rules"></a>FQDN dans les règles de réseau

Vous pouvez utiliser des noms de domaine complets (FQDN) dans les règles de réseau basées sur la résolution DNS dans le Pare-feu Azure et la stratégie de pare-feu. 

Les FQDN spécifiés dans vos regroupements de règles sont traduits en adresses IP en fonction des paramètres DNS de votre pare-feu. Cette fonctionnalité vous permet de filtrer le trafic sortant en utilisant des FQDN avec n'importe quel protocole TCP/UDP (y compris NTP, SSH, RDP, etc.). Étant donné que cette fonctionnalité est basée sur la résolution DNS, il est fortement recommandé d’activer le proxy DNS pour garantir la cohérence de la résolution de noms avec vos machines virtuelles protégées et votre pare-feu.

## <a name="deploy-azure-firewall-without-public-ip-address-in-forced-tunnel-mode"></a>Déployer un Pare-feu Azure sans adresse IP publique en mode de Tunnel forcé

Le service Pare-feu Azure requiert une adresse IP publique à des fins opérationnelles. Bien qu'ils soient sécurisés, certains déploiements préfèrent ne pas exposer une adresse IP publique directement à Internet. 

Dans ce cas, vous pouvez déployer le Pare-feu Azure en mode Tunnel forcé. Cette configuration crée une carte réseau de gestion qui est utilisée par le Pare-feu Azure pour ses opérations. Le réseau Tenant Datapath peut être configuré sans adresse IP publique, et le trafic Internet peut être transféré en mode Tunnel forcé vers un autre pare-feu ou être complètement bloqué.

Le mode de Tunnel forcé ne peut pas être configuré au moment de l’exécution. Vous pouvez soit redéployer le Pare-feu, soit utiliser la fonctionnalité d'arrêt et de démarrage pour reconfigurer un Pare-feu Azure existant en mode Tunnel forcé. Les pare-feu déployés dans les hubs sécurisés sont toujours déployés en mode Tunnel forcé.

## <a name="outbound-snat-support"></a>Prise en charge du mode SNAT sortant

Toutes les adresses IP du trafic réseau virtuel sortant sont traduites en adresse IP publique de Pare-feu Azure (Source Network Address Translation). Vous pouvez identifier et autoriser le trafic entre votre réseau virtuel et des destinations Internet distantes. Le Pare-feu Azure ne traduit pas l’adresse réseau source (SNAT) quand l’adresse IP de destination correspond à une plage d’adresses IP privées selon le document [IANA RFC 1918](https://tools.ietf.org/html/rfc1918). 

Si votre organisation utilise une plage d’adresses IP publiques pour les réseaux privés, Pare-feu Azure traduit l’adresse réseau source du trafic en une des adresses IP privées du pare-feu dans AzureFirewallSubnet. Vous pouvez configurer Pare-feu Azure pour qu’il n’effectue **pas** une telle traduction. Pour plus d’informations, consultez [Plages d’adresses IP privées SNAT du Pare-feu Azure](snat-private-range.md).

Vous pouvez surveiller l’utilisation des ports SNAT dans les métriques du pare-feu Azure. Pour plus d’informations et voir notre recommandation sur l’utilisation des ports SNAT, consultez notre [documentation sur les métriques et les journaux de pare-feu](logs-and-metrics.md#metrics).

## <a name="inbound-dnat-support"></a>Prise en charge du trafic DNAT entrant

Le trafic Internet entrant vers votre adresse IP publique de pare-feu est traduit (Destination Network Address Translation ou DNAT) et filtré selon les adresses IP privées sur vos réseaux virtuels.

## <a name="multiple-public-ip-addresses"></a>Adresses IP publiques multiples

Vous pouvez associer [plusieurs adresses IP publiques](deploy-multi-public-ip-powershell.md) (jusqu’à 250) à votre pare-feu.

Cela donne accès aux scénarios suivants :

- **DNAT** -Vous pouvez traduire plusieurs instances de ports standard vers vos serveurs principaux. Par exemple, si vous avez deux adresses IP publiques, vous pouvez traduire le port TCP 3389 (RDP) pour ces deux adresses IP.
- **SNAT** - Des ports supplémentaires sont disponibles pour les connexions SNAT sortantes, réduisant ainsi le risque de pénurie de ports SNAT. À ce stade, Pare-feu Azure sélectionne aléatoirement l’adresse IP publique source à utiliser pour une connexion. Si votre réseau est doté d’un filtrage en aval, vous devez autoriser toutes les adresses IP publiques associées à votre pare-feu. Envisagez d’utiliser un [préfixe d’adresse IP publique](../virtual-network/ip-services/public-ip-address-prefix.md) pour simplifier cette configuration.

## <a name="azure-monitor-logging"></a>Journalisation d’Azure Monitor

Tous les événements sont intégrés à Azure Monitor, ce qui vous permet d’archiver les journaux d’activité dans un compte de stockage, de transmettre en continu des événements à votre hub d’événements ou de les envoyer à des journaux d’activité Azure Monitor. Pour obtenir des exemples de journaux Azure Monitor, consultez [Journaux Azure Monitor pour le Pare-feu Azure](./firewall-workbook.md).

Pour plus d’informations, consultez [Didacticiel : superviser les journaux du Pare-feu Azure et les métriques](./firewall-diagnostics.md). 

Le classeur Pare-feu Azure constitue un canevas flexible pour l’analyse de données du Pare-feu Azure. Il permet de créer des rapports visuels enrichis au sein du Portail Azure. Pour plus d’informations, consultez [Monitoring des journaux avec un classeur Pare-feu Azure](firewall-workbook.md).

## <a name="forced-tunneling"></a>Tunneling forcé

Vous pouvez configurer le pare-feu Azure pour router tout le trafic Internet vers un tronçon suivant désigné au lieu d’accéder directement à Internet. Par exemple, vous pouvez disposer d’un pare-feu de périphérie local ou d’une autre appliance virtuelle réseau (NVA) pour traiter le trafic réseau avant qu’il ne soit dirigé vers Internet. Pour plus d’informations, consultez la page [Tunneling forcé du Pare-feu Azure](forced-tunneling.md).

## <a name="web-categories"></a>Catégories web

Les catégories web permettent aux administrateurs d’autoriser ou de refuser aux utilisateurs l’accès aux catégories de sites web telles que les sites web de jeux d’argent, les sites web de réseaux sociaux, etc. Les catégories web sont incluses dans le Pare-feu Azure Standard, mais elles sont plus précises dans le Pare-feu Azure Premium. Contrairement à la fonctionnalité de catégories web de la référence SKU Standard qui correspond à la catégorie basée sur un nom de domaine complet, la référence SKU Premium correspond à la catégorie en fonction de l’URL complète pour le trafic HTTP et HTTPS. Pour plus d’informations sur le Pare-feu Azure Premium, consultez [Fonctionnalités du Pare-feu Azure Premium](premium-features.md).

Par exemple, si le Pare-feu Azure intercepte une demande HTTPS pour `www.google.com/news`, la catégorisation suivante est attendue : 

- Pare-feu Standard : seule la partie du nom de domaine complet étant examinée, `www.google.com` est classé en tant que *Moteur de recherche*. 

- Pare-feu Premium : l’URL complète étant examinée, `www.google.com/news` est classée en tant qu’*Actualités*.

Les catégories sont organisées en fonction de leur gravité sous **Responsabilité**, **Bande passante élevée**, **Utilisation métier**, **Perte de productivité**, **Navigation générale** et **Sans catégorie**.

### <a name="category-exceptions"></a>Exceptions de catégorie

Vous pouvez créer des exceptions à vos règles de catégorie web. Créez une collection de règles d’autorisation ou de refus distinct avec une priorité plus élevée au sein du groupe de collections de règles. Par exemple, vous pouvez configurer une collection de règles qui autorise `www.linkedin.com` avec la priorité 100, avec une collection de règles qui refuse **Réseaux sociaux** avec la priorité 200. Cette opération crée l’exception pour la catégorie web **Réseaux sociaux** prédéfinie.



## <a name="certifications"></a>Certifications

Le service Pare-feu Azure est conforme aux normes PCI (Payment Card Industry), SOC (Service Organization Controls), ISO (Organisation internationale de normalisation) et ICSA Labs. Pour plus d’informations, consultez [Certifications de conformité du pare-feu Azure](compliance-certifications.md).

## <a name="next-steps"></a>Étapes suivantes

- [Fonctionnalités du Pare-feu Azure Premium](premium-features.md)