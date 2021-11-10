---
title: Services Azure prenant en charge les zones de disponibilité
description: Découvrez quels services sont pris en charge par les zones de disponibilité et comprenez la résilience pour tous les services Azure.
author: prsandhu
ms.service: azure
ms.topic: conceptual
ms.date: 10/01/2021
ms.author: rarco
ms.reviewer: cynthn
ms.custom: references_regions
ms.openlocfilehash: 58d483c56f27dd346c672f2ec2013dce934993ee
ms.sourcegitcommit: 1a0fe16ad7befc51c6a8dc5ea1fe9987f33611a1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131866522"
---
# <a name="azure-services-that-support-availability-zones"></a>Services Azure prenant en charge les zones de disponibilité

Les [zones de disponibilité](az-overview.md) Azure sont des emplacements physiquement séparés au sein de chaque région Azure, qui sont tolérants aux défaillances de centre de données en raison de l’infrastructure redondante et de l’isolation logique des services Azure. 

Les zones de disponibilité ont toutes trait à la redondance du réseau. Les services Azure qui prennent en charge les zones de disponibilité sont conçus pour fournir les niveaux de résilience et de flexibilité appropriés, ainsi qu’une latence très faible. Avec les services Azure prenant en charge les zones de disponibilité, que vous architecturiez votre propre résilience ou que vous optiez pour la réplication et la distribution automatiques, l’avantage est le même. Vous bénéficiez d’une résilience supérieure des services hautement disponibles, quel qu’en soit le type. 

Azure s’efforce d’offrir une résilience élevée de l’ensemble des services et offres. L’exécution des services Azure qui prennent en charge les zones de disponibilité offre une résilience entièrement transparente et cohérente dans presque tous les scénarios, sans interruption.

## <a name="azure-regions-with-availability-zones"></a>Régions Azure avec des zones de disponibilité

Azure fournit l’empreinte mondiale la plus complète de tous les fournisseurs de cloud et ouvre rapidement de nouvelles régions et zones de disponibilité. Les régions prenant actuellement en charge les zones de disponibilité sont les suivantes.

| Amérique | Europe | Afrique | Asie-Pacifique |
|--------------------|----------------------|---------------------|----------------|
| Brésil Sud | France Centre | Afrique du Sud Nord | Australie Est |
| Centre du Canada | Allemagne Centre-Ouest | | Inde centrale\* |
| USA Centre | Europe Nord | | Japon Est |
| USA Est | Norvège Est | | Centre de la Corée |
| USA Est 2 | Sud du Royaume-Uni | | Asie Sud-Est |
| États-Unis - partie centrale méridionale | Europe Ouest | | Asie Est |
| Gouvernement américain - Virginie | Suède | | |
| USA Ouest 2 | | | |
| USA Ouest 3 | | | |

\* Pour en savoir plus sur les zones de disponibilité et sur la prise en charge des services disponibles dans ces régions, contactez votre représentant ou délégué commercial Microsoft. Pour savoir quelles sont les prochaines régions qui prendront en charge les zones de disponibilité, consultez [Zones géographiques Azure](https://azure.microsoft.com/global-infrastructure/geographies/).

Pour obtenir la liste des services Azure qui prennent en charge les Zones de disponibilité par région Azure, consultez la [documentation sur les zones de disponibilité](az-overview.md).

## <a name="highly-available-services"></a>Services hautement disponibles

Les services Azure qui prennent en charge les zones de disponibilité sont de trois types : *zonaux*, *redondants interzone* et *toujours disponibles*. Vous pouvez combiner ces trois approches d’architecture quand vous concevez votre stratégie de résilience.

- **Services zonaux** : une ressource peut être déployée dans une zone de disponibilité spécifique auto-sélectionnée pour répondre à des exigences plus rigoureuses en matière de latence ou de performances. La résilience est auto-architecturée en répliquant des applications et des données dans une ou plusieurs zones à l’intérieur de la région. Les ressources peuvent être affectées à une zone en particulier. Vous pouvez, par exemple, épingler des machines virtuelles, des disques managés ou des adresses IP standard à une zone spécifique, afin d’obtenir une résilience accrue en faisant en sorte qu’une ou plusieurs instances de ressources soient réparties entre les zones.
- **Services redondants interzone** : les ressources sont répliquées ou distribuées automatiquement entre les zones. Par exemple, les services redondants interzone répliquent les données dans trois zones afin qu’une défaillance dans une zone n’affecte pas la haute disponibilité des données. 
- **Services toujours disponibles** : toujours disponibles dans toutes les zones géographiques Azure, ils résistent aux pannes à l’échelle d’une zone et d’une région. Pour obtenir la liste complète des services toujours disponibles, également appelés services non régionaux, dans Azure, consultez l’article [Produits disponibles par région](https://azure.microsoft.com/global-infrastructure/services/).

Comme mentionné plus haut dans cet article, tous les services Azure ne sont pas universellement disponibles dans toutes les régions. Les offres sont regroupées en trois catégories reflétant leur disponibilité _régionale_, en services *de base*, *standard* et *stratégiques*. La stratégie générale d’Azure pour le déploiement de services dans une région donnée est principalement pilotée par le type de région, la catégories de service et la demande des clients. Pour plus d’informations, consultez [Services Azure](region-types-service-categories-azure.md).

- **Services de base** : disponibles dans toutes les régions recommandées et alternatives quand une région est généralement disponible, ou dans les 90 jours suivant la disponibilité générale d’un nouveau service de base.
- **Services standard** : disponibles dans toutes les régions recommandées dans les 90 jours suivant la disponibilité générale d’une région. Les services standard sont basés sur la demande dans les régions alternatives, et beaucoup sont déjà déployés dans un grand sous-ensemble de régions alternatives.
- **Services stratégiques** : offres de services ciblés, souvent axés sur un secteur ou adossés à du matériel personnalisé. La disponibilité des services stratégiques est basée sur la demande dans les régions, et beaucoup sont déjà déployés dans un grand sous-ensemble de régions recommandées.

Les services Azure qui prennent en charge les zones de disponibilité, y compris les offres zonales et redondantes interzone, sont en constante évolution.

Pour plus d’informations sur les machines virtuelles de génération plus ancienne, consultez [Tailles de machines virtuelles des générations précédentes](../virtual-machines/sizes-previous-gen.md).

Les tableaux suivants fournissent un résumé de l’offre actuelle de services Azure zonaux, redondants interzone et toujours disponibles. Ils répertorient les offres Azure en fonction de la disponibilité régionale de chacune d’elles.

##### <a name="legend"></a>Légende
![Légende contenant des icônes et la signification de chacune d’elles en ce qui concerne la catégorie de service et la disponibilité régionale de chaque service figurant dans le tableau.](media/legend.png) 

Dans le catalogue de produits, les services toujours disponibles sont répertoriés en tant que services « non régionaux ».

### <a name="an-icon-that-signifies-this-service-is-foundational-foundational-services"></a>![Icône indiquant que ce service est de base.](media/icon-foundational.svg) Services fondamentaux 

| **Produits**   | **Résilience**   |
| --- | --- |
| [Azure Application Gateway (V2)](../application-gateway/application-gateway-autoscaling-zone-redundant.md)  | ![Icône indiquant que ce service est redondant interzone.](media/icon-zone-redundant.svg) ![Icône indiquant que ce service est zonal.](media/icon-zonal.svg)  |
| [Azure Backup](../backup/backup-create-rs-vault.md)  | ![Icône indiquant que ce service est zonal.](media/icon-zonal.svg)  |
| [Azure Cosmos DB](../cosmos-db/high-availability.md#availability-zone-support)  | ![Icône indiquant que ce service est redondant interzone.](media/icon-zone-redundant.svg)   |
| [Azure Data Lake Storage Gen 2](../storage/blobs/data-lake-storage-introduction.md)  | ![Icône indiquant que ce service est redondant interzone.](media/icon-zone-redundant.svg)  |
| [Azure DNS : Azure DNS Private Zones](../dns/private-dns-getstarted-portal.md)  | ![Icône indiquant que ce service est redondant interzone.](media/icon-zone-redundant.svg)  |
| [Azure ExpressRoute](../expressroute/designing-for-high-availability-with-expressroute.md)  | ![Icône indiquant que ce service est redondant interzone.](media/icon-zone-redundant.svg)  |
| [Adresse IP publique Azure](../virtual-network/ip-services/public-ip-addresses.md)  | ![Icône indiquant que ce service est redondant interzone.](media/icon-zone-redundant.svg) ![Icône indiquant que ce service est zonal.](media/icon-zonal.svg)  |
| Azure SQL Database ([niveau Usage général](../azure-sql/database/high-availability-sla.md))  | ![Icône indiquant que ce service est redondant interzone.](media/icon-zone-redundant.svg)  |
| Azure SQL Database ([niveaux Premium &amp; Critique pour l’entreprise](../azure-sql/database/high-availability-sla.md))  | ![Icône indiquant que ce service est redondant interzone.](media/icon-zone-redundant.svg)  |
| [Stockage sur disque Azure](../storage/common/storage-redundancy.md)  | ![Icône indiquant que ce service est redondant interzone.](media/icon-zone-redundant.svg)  |
| [Azure Event Hubs](../event-hubs/event-hubs-geo-dr.md#availability-zones)  | ![Icône indiquant que ce service est redondant interzone.](media/icon-zone-redundant.svg)  |
| [Azure Key Vault](../key-vault/general/disaster-recovery-guidance.md)  | ![Icône indiquant que ce service est redondant interzone.](media/icon-zone-redundant.svg)  |
| [Équilibrage de charge Azure](../load-balancer/load-balancer-standard-availability-zones.md)  | ![Icône indiquant que ce service est redondant interzone.](media/icon-zone-redundant.svg) ![Icône indiquant que ce service est zonal.](media/icon-zonal.svg)  |
| [Azure Service Bus](../service-bus-messaging/service-bus-geo-dr.md#availability-zones)  | ![Icône indiquant que ce service est redondant interzone.](media/icon-zone-redundant.svg)  |
| [Azure Service Fabric](../service-fabric/service-fabric-cross-availability-zones.md)  | ![Icône indiquant que ce service est redondant interzone.](media/icon-zone-redundant.svg) ![Icône indiquant que ce service est zonal.](media/icon-zonal.svg)  |
| [compte Stockage Azure](../storage/common/storage-redundancy.md)  | ![Icône indiquant que ce service est redondant interzone.](media/icon-zone-redundant.svg)  |
| Stockage :  [niveaux de Stockage Blob Azure chaud/froid](../storage/common/storage-redundancy.md)  | ![Icône indiquant que ce service est redondant interzone.](media/icon-zone-redundant.svg)  |
| Stockage :  [disques managés](../virtual-machines/managed-disks-overview.md)  | ![Icône indiquant que ce service est redondant interzone.](media/icon-zone-redundant.svg) ![Icône indiquant que ce service est zonal.](media/icon-zonal.svg)  |
| [Groupes de machines virtuelles identiques Azure](../virtual-machine-scale-sets/scripts/cli-sample-zone-redundant-scale-set.md)  | ![Icône indiquant que ce service est redondant interzone.](media/icon-zone-redundant.svg) ![Icône indiquant que ce service est zonal.](media/icon-zonal.svg)  |
| [Machines virtuelles Azure](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![Icône indiquant que ce service est zonal.](media/icon-zonal.svg)  |
| Machines virtuelles :  [série Av2](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![Icône indiquant que ce service est zonal.](media/icon-zonal.svg)  |
| Machines virtuelles :  [série Bs](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![Icône indiquant que ce service est zonal.](media/icon-zonal.svg)  |
| Machines virtuelles :  [série DSv2](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![Icône indiquant que ce service est zonal.](media/icon-zonal.svg)  |
| Machines virtuelles :  [série DSv3](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![Icône indiquant que ce service est zonal.](media/icon-zonal.svg)  |
| Machines virtuelles :  [série Dv2](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![Icône indiquant que ce service est zonal.](media/icon-zonal.svg)  |
| Machines virtuelles :  [série Dv3](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![Icône indiquant que ce service est zonal.](media/icon-zonal.svg)  |
| Machines virtuelles :  [série ESv3](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![Icône indiquant que ce service est zonal.](media/icon-zonal.svg)  |
| Machines virtuelles :  [série Ev3](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![Icône indiquant que ce service est zonal.](media/icon-zonal.svg)  |
| Machines virtuelles :  [série F](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![Icône indiquant que ce service est zonal.](media/icon-zonal.svg)  |
| Machines virtuelles :  [série FS](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![Icône indiquant que ce service est zonal.](media/icon-zonal.svg)  |
| Machines virtuelles :  [Shared Image Gallery](../virtual-machines/shared-image-galleries.md#make-your-images-highly-available)  | ![Icône indiquant que ce service est redondant interzone.](media/icon-zone-redundant.svg)  |
| [Réseau virtuel Azure](../vpn-gateway/create-zone-redundant-vnet-gateway.md)  | ![Icône indiquant que ce service est redondant interzone.](media/icon-zone-redundant.svg)  |
| [Passerelle VPN Azure](../vpn-gateway/about-zone-redundant-vnet-gateways.md)  | ![Icône indiquant que ce service est redondant interzone.](media/icon-zone-redundant.svg)  |
### <a name="an-icon-that-signifies-this-service-is-mainstream-mainstream-services"></a>![Icône indiquant que ce service est standard.](media/icon-mainstream.svg) Services standard

| **Produits**   | **Résilience**   |
| --- | --- |
| [App Service](../app-service/how-to-zone-redundancy.md)  | ![Icône indiquant que ce service est redondant interzone.](media/icon-zone-redundant.svg) ![Icône indiquant que ce service est zonal.](media/icon-zonal.svg)  |
| [Environnements Azure App Service](../app-service/environment/zone-redundancy.md)  | ![Icône indiquant que ce service est redondant interzone.](media/icon-zone-redundant.svg) ![Icône indiquant que ce service est zonal.](media/icon-zonal.svg)  |
| [Services de domaine Azure Active Directory](../active-directory-domain-services/overview.md)  | ![Icône indiquant que ce service est redondant interzone.](media/icon-zone-redundant.svg)  |
| [Gestion des API Azure](../api-management/zone-redundancy.md)  | ![Icône indiquant que ce service est zonal.](media/icon-zonal.svg)  |
| [Azure App Configuration](../azure-app-configuration/faq.yml#how-does-app-configuration-ensure-high-data-availability)  | ![Icône indiquant que ce service est redondant interzone.](media/icon-zone-redundant.svg)  |
| [Azure Bastion](../bastion/bastion-overview.md)  | ![Icône indiquant que ce service est redondant interzone.](media/icon-zone-redundant.svg)  |
| [Azure Batch](../batch/create-pool-availability-zones.md)  | ![Icône indiquant que ce service est redondant interzone.](media/icon-zone-redundant.svg)  |
| [Cache Azure pour Redis](../azure-cache-for-redis/cache-high-availability.md)  | ![Icône indiquant que ce service est redondant interzone.](media/icon-zone-redundant.svg) ![Icône indiquant que ce service est zonal.](media/icon-zonal.svg)  |
| [Recherche cognitive Azure](../search/search-performance-optimization.md#availability-zones)  | ![Icône indiquant que ce service est redondant interzone.](media/icon-zone-redundant.svg)  |
| Azure Cognitive Services :  [Analyse de texte](../cognitive-services/text-analytics/index.yml)  | ![Icône indiquant que ce service est redondant interzone.](media/icon-zone-redundant.svg)  |
| [Explorateur de données Azure](/azure/data-explorer/create-cluster-database-portal)  | ![Icône indiquant que ce service est redondant interzone.](media/icon-zone-redundant.svg)  |
| [Azure Data Factory](../data-factory/index.yml).  | ![Icône indiquant que ce service est redondant interzone.](media/icon-zone-redundant.svg)  |
| Azure Database pour MySQL –  [Serveur flexible](../mysql/flexible-server/concepts-high-availability.md)  | ![Icône indiquant que ce service est redondant interzone.](media/icon-zone-redundant.svg)  |
| Azure Database pour PostgreSQL –  [Serveur flexible](../postgresql/flexible-server/overview.md)  | ![Icône indiquant que ce service est redondant interzone.](media/icon-zone-redundant.svg)  |
| [Azure DDoS Protection](../ddos-protection/ddos-faq.yml)  | ![Icône indiquant que ce service est redondant interzone.](media/icon-zone-redundant.svg) |
| [Azure Disk Encryption](../virtual-machines/disks-redundancy.md)  | ![Icône indiquant que ce service est redondant interzone.](media/icon-zone-redundant.svg) |
| [Pare-feu Azure](../firewall/deploy-availability-zone-powershell.md)  | ![Icône indiquant que ce service est redondant interzone.](media/icon-zone-redundant.svg)  |
| [Azure Firewall Manager](../firewall-manager/quick-firewall-policy.md)  | ![Icône indiquant que ce service est redondant interzone.](media/icon-zone-redundant.svg)  |
| [Azure Functions](https://azure.github.io/AppService/2021/08/25/App-service-support-for-availability-zones.html)  | ![Icône indiquant que ce service est redondant interzone.](media/icon-zone-redundant.svg)  |
| [Azure Kubernetes Service (AKS)](../aks/availability-zones.md)  | ![Icône indiquant que ce service est redondant interzone.](media/icon-zone-redundant.svg) ![Icône indiquant que ce service est zonal.](media/icon-zonal.svg)  |
| [Azure Monitor](../azure-monitor/logs/availability-zones.md)  | ![Icône indiquant que ce service est redondant interzone.](media/icon-zone-redundant.svg)  |
| [Azure Monitor : Application Insights](../azure-monitor/logs/availability-zones.md)  | ![Icône indiquant que ce service est redondant interzone.](media/icon-zone-redundant.svg)  |
| [Azure Monitor : Log Analytics](../azure-monitor/logs/availability-zones.md)  | ![Icône indiquant que ce service est redondant interzone.](media/icon-zone-redundant.svg)  |
| [Azure Private Link](../private-link/private-link-overview.md)  | ![Icône indiquant que ce service est redondant interzone.](media/icon-zone-redundant.svg)  |
| [Azure Site Recovery](../site-recovery/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery.md)  | ![Icône indiquant que ce service est zonal.](media/icon-zonal.svg)  |
| Azure SQL :  [machine virtuelle](../azure-sql/database/high-availability-sla.md)  | ![Icône indiquant que ce service est redondant interzone.](media/icon-zone-redundant.svg) |
| [Pare-feu d’applications web Azure](../firewall/deploy-availability-zone-powershell.md)  | ![Icône indiquant que ce service est redondant interzone.](media/icon-zone-redundant.svg)  |
| [Azure Container Registry](../container-registry/zone-redundancy.md)  | ![Icône indiquant que ce service est redondant interzone.](media/icon-zone-redundant.svg)  |
| [Azure Event Grid](../event-grid/overview.md)  | ![Icône indiquant que ce service est redondant interzone.](media/icon-zone-redundant.svg)  |
| [Azure HDInsight](../hdinsight/hdinsight-use-availability-zones.md)  | ![Icône indiquant que ce service est zonal.](media/icon-zonal.svg)  |
| [Azure Network Watcher](../network-watcher/frequently-asked-questions.yml)  | ![Icône indiquant que ce service est redondant interzone.](media/icon-zone-redundant.svg)  |
| Network Watcher :  [Traffic Analytics](../network-watcher/frequently-asked-questions.yml)  | ![Icône indiquant que ce service est redondant interzone.](media/icon-zone-redundant.svg)  |
| [Power BI Embedded](/power-bi/admin/service-admin-failover#what-does-high-availability)  | ![Icône indiquant que ce service est redondant interzone.](media/icon-zone-redundant.svg)  |
| [Stockage Blob Premium](../storage/common/storage-account-overview.md)  | ![Icône indiquant que ce service est redondant interzone.](media/icon-zone-redundant.svg)  |
| Stockage :  [fichiers Azure Premium](../storage/files/storage-files-planning.md)  | ![Icône indiquant que ce service est redondant interzone.](media/icon-zone-redundant.svg)  |
| Machines virtuelles :  [Azure Dedicated Host](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![Icône indiquant que ce service est zonal.](media/icon-zonal.svg)  |
| Machines virtuelles :  [série Ddsv4](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![Icône indiquant que ce service est zonal.](media/icon-zonal.svg)  |
| Machines virtuelles :  [série Ddv4](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![Icône indiquant que ce service est zonal.](media/icon-zonal.svg)  |
| Machines virtuelles :  [série Dsv4](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![Icône indiquant que ce service est zonal.](media/icon-zonal.svg)  |
| Machines virtuelles :  [série Dv4](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![Icône indiquant que ce service est zonal.](media/icon-zonal.svg)  |
| Machines virtuelles :  [série Edsv4](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![Icône indiquant que ce service est zonal.](media/icon-zonal.svg)  |
| Machines virtuelles :  [série Edv4](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![Icône indiquant que ce service est zonal.](media/icon-zonal.svg)  |
| Machines virtuelles :  [série Esv4](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![Icône indiquant que ce service est zonal.](media/icon-zonal.svg)  |
| Machines virtuelles :  [série Ev4](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![Icône indiquant que ce service est zonal.](media/icon-zonal.svg)  |
| Machines virtuelles :  [série Fsv2](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![Icône indiquant que ce service est zonal.](media/icon-zonal.svg)  |
| Machines virtuelles :  [série M](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![Icône indiquant que ce service est zonal.](media/icon-zonal.svg)  |
| [Azure Virtual WAN](../virtual-wan/virtual-wan-faq.md#how-are-availability-zones-and-resiliency-handled-in-virtual-wan)  | ![Icône indiquant que ce service est redondant interzone.](media/icon-zone-redundant.svg)  |
| Virtual WAN :  [Azure ExpressRoute](../virtual-wan/virtual-wan-faq.md#how-are-availability-zones-and-resiliency-handled-in-virtual-wan)  | ![Icône indiquant que ce service est redondant interzone.](media/icon-zone-redundant.svg)  |
| Virtual WAN :  [passerelle VPN point à site](../vpn-gateway/about-zone-redundant-vnet-gateways.md)  | ![Icône indiquant que ce service est redondant interzone.](media/icon-zone-redundant.svg)  |
| Virtual WAN :  [passerelle VPN site à site](../vpn-gateway/about-zone-redundant-vnet-gateways.md)  | ![Icône indiquant que ce service est redondant interzone.](media/icon-zone-redundant.svg)  |

### <a name="an-icon-that-signifies-this-service-is-strategic-strategic-services"></a>![Icône indiquant que ce service est stratégique.](media/icon-strategic.svg) Services stratégiques

| **Produits**   | **Résilience**   |
| --- | --- |
| Azure Red Hat OpenShift  | ![Icône indiquant que ce service est redondant interzone.](media/icon-zone-redundant.svg) ![Icône indiquant que ce service est zonal.](media/icon-zonal.svg)  |
| Azure Cognitive Services : Le détecteur d’anomalies  | ![Icône indiquant que ce service est redondant interzone.](media/icon-zone-redundant.svg)  |
| Azure Cognitive Services : Form Recognizer  | ![Icône indiquant que ce service est redondant interzone.](media/icon-zone-redundant.svg)  |
| Stockage Azure : Disque Ultra  | ![Icône indiquant que ce service est zonal.](media/icon-zonal.svg)  |

### <a name="an-icon-that-signifies-this-service-is-non-regional-non-regional-services-always-available-services"></a>![Une icône indiquant que ce service est non régional.](media/icon-always-available.svg) Services non régionaux (services toujours disponibles)

| **Produits**   | **Résilience**   |
| --- | --- |
| Azure DNS  | ![Icône indiquant que ce service est toujours disponible.](media/icon-always-available.svg) |
| Azure Active Directory  | ![Icône indiquant que ce service est toujours disponible.](media/icon-always-available.svg) |
| Azure Advanced Threat Protection  | ![Icône indiquant que ce service est toujours disponible.](media/icon-always-available.svg) |
| Azure Advisor  | ![Icône indiquant que ce service est toujours disponible.](media/icon-always-available.svg) |
| Azure Blueprints  | ![Icône indiquant que ce service est toujours disponible.](media/icon-always-available.svg) |
| Azure Bot Service  | ![Icône indiquant que ce service est toujours disponible.](media/icon-always-available.svg) |
| Azure Front Door  | ![Icône indiquant que ce service est toujours disponible.](media/icon-always-available.svg) |
| Azure Defender pour IoT  | ![Icône indiquant que ce service est toujours disponible.](media/icon-always-available.svg) |
| Azure Front Door  | ![Icône indiquant que ce service est toujours disponible.](media/icon-always-available.svg) |
| Azure Information Protection  | ![Icône indiquant que ce service est toujours disponible.](media/icon-always-available.svg) |
| Azure Lighthouse  | ![Icône indiquant que ce service est toujours disponible.](media/icon-always-available.svg) |
| Applications managées Azure  | ![Icône indiquant que ce service est toujours disponible.](media/icon-always-available.svg) |
| Azure Maps  | ![Icône indiquant que ce service est toujours disponible.](media/icon-always-available.svg) |
| Azure Performance Diagnostics  | ![Icône indiquant que ce service est toujours disponible.](media/icon-always-available.svg) |
| Azure Policy  | ![Icône indiquant que ce service est toujours disponible.](media/icon-always-available.svg) |
| Azure Resource Graph  | ![Icône indiquant que ce service est toujours disponible.](media/icon-always-available.svg) |
| Azure Sentinel  | ![Icône indiquant que ce service est toujours disponible.](media/icon-always-available.svg) |
| Azure Stack  | ![Icône indiquant que ce service est toujours disponible.](media/icon-always-available.svg) |
| Azure Stack Edge  | ![Icône indiquant que ce service est toujours disponible.](media/icon-always-available.svg) |
| Azure Cloud Shell  | ![Icône indiquant que ce service est toujours disponible.](media/icon-always-available.svg) |
| Azure Content Delivery Network  | ![Icône indiquant que ce service est toujours disponible.](media/icon-always-available.svg) |
| Azure Cost Management et Facturation | ![Icône indiquant que ce service est toujours disponible.](media/icon-always-available.svg) |
| Customer Lockbox pour Microsoft Azure  | ![Icône indiquant que ce service est toujours disponible.](media/icon-always-available.svg) |
| Microsoft Intune  | ![Icône indiquant que ce service est toujours disponible.](media/icon-always-available.svg) |
| Azure Peering Service  | ![Icône indiquant que ce service est toujours disponible.](media/icon-always-available.svg) |
| Portail Azure  | ![Icône indiquant que ce service est toujours disponible.](media/icon-always-available.svg) |
| Microsoft Cloud App Security  | ![Icône indiquant que ce service est toujours disponible.](media/icon-always-available.svg) |
| Microsoft Graph  | ![Icône indiquant que ce service est toujours disponible.](media/icon-always-available.svg) |
| Azure Security Center  | ![Icône indiquant que ce service est toujours disponible.](media/icon-always-available.svg) |
| Azure Traffic Manager  | ![Icône indiquant que ce service est toujours disponible.](media/icon-always-available.svg) |

Pour obtenir la liste des services Azure qui prennent en charge les Zones de disponibilité par région Azure, consultez la [documentation sur les zones de disponibilité](az-overview.md).

## <a name="pricing-for-virtual-machines-in-availability-zones"></a>Tarification des machines virtuelles dans les zones de disponibilité

Vous pouvez accéder aux zones de disponibilité Azure à l’aide de votre abonnement Azure. Pour plus d’informations, consultez [Tarification de la bande passante](https://azure.microsoft.com/pricing/details/bandwidth/).

## <a name="next-steps"></a>Étapes suivantes

- [Création de solutions pour la haute disponibilité à l’aide de zones de disponibilité](/azure/architecture/high-availability/building-solutions-for-high-availability)
- [Haute disponibilité avec les services Azure](/azure/architecture/framework/resiliency/overview)
- [Concevoir des modèles pour la haute disponibilité](/azure/architecture/framework/resiliency/app-design)
