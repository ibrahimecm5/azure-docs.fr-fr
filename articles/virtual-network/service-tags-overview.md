---
title: Vue d’ensemble des balises de service Azure
titlesuffix: Azure Virtual Network
description: Découvrez les balises de service. Les balises de service permettent de réduire la complexité de la création des règles de sécurité.
services: virtual-network
documentationcenter: na
author: allegradomel
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/11/2021
ms.author: kumud
ms.reviewer: kumud
ms.openlocfilehash: 2affaf7b66c1a7872e1db2b92871c5d07e77cdd2
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132324628"
---
# <a name="virtual-network-service-tags"></a>Balises de service du réseau virtuel
<a name="network-service-tags"></a>

Une balise de service représente un groupe de préfixes d’adresses IP d’un service Azure donné. Microsoft gère les préfixes d’adresses englobés par l’étiquette de service et met à jour automatiquement l’étiquette de service quand les adresses changent, ce qui réduit la complexité des mises à jour fréquentes relatives aux règles de sécurité réseau.

Vous pouvez utiliser des étiquettes de service pour définir des contrôles d’accès réseau sur des [groupes de sécurité réseau](./network-security-groups-overview.md#security-rules) ou sur le [pare-feu Azure](../firewall/service-tags.md). Utilisez des étiquettes de service à la place des adresses IP spécifiques lors de la création de règles de sécurité. En spécifiant le nom de l’étiquette de service (par exemple, **ApiManagement**) dans le champ *Source* ou *Destination* d’une règle, vous pouvez autoriser ou refuser le trafic pour le service correspondant. 

> [!NOTE] 
> Depuis mars 2021, vous pouvez également utiliser des étiquettes de service à la place des plages d’adresses IP explicites dans les [routes définies par l’utilisateur](./virtual-networks-udr-overview.md). Cette fonctionnalité est actuellement en préversion publique. 

Vous pouvez utiliser des étiquettes de service pour isoler le réseau et protéger vos ressources Azure de l’accès Internet général tout en accédant aux services Azure qui ont des points de terminaison publics. Créez des règles de groupe de sécurité de réseau entrant/sortant pour refuser le trafic vers/depuis **Internet** et autoriser le trafic vers/depuis **AzureCloud** ou d’autres [balises de service disponibles](#available-service-tags) de services Azure spécifiques.

![Isolement réseau des services Azure à l’aide de balises de service](./media/service-tags-overview/service_tags.png)

## <a name="available-service-tags"></a>Balises de service disponibles
Le tableau suivant répertorie les balises de service disponibles à utiliser dans les règles [Groupes de sécurité réseau](./network-security-groups-overview.md#security-rules).

Les colonnes indiquent si la balise :

- Est adaptée aux règles couvrant le trafic entrant ou sortant.
- Prend en charge l’étendue [régionale](https://azure.microsoft.com/regions).
- Est utilisable dans les règles de [Pare-feu Azure](../firewall/service-tags.md).

Par défaut, les balises de service reflètent les plages pour l’ensemble du Cloud. Certaines balises de service permettent également d’obtenir un contrôle plus précis en limitant les plages d’adresses IP correspondantes à une région spécifiée. Par exemple, la balise de service **Storage** représente le Stockage Azure pour l’ensemble du cloud, alors que **Storage.WestUS** limite la sélection aux plages d’adresses IP de stockage de la région WestUS. Le tableau suivant indique si chaque balise de service prend en charge cette étendue régionale. Notez que la direction indiquée pour chaque étiquette est une recommandation. Par exemple, l’étiquette AzureCloud peut être utilisée pour autoriser le trafic entrant. Toutefois, nous vous déconseillons de le faire dans la plupart des scénarios, car cela revient à autoriser le trafic provenant de toutes les adresses IP Azure, y compris celles utilisées par d’autres clients Azure. 

| Tag | Objectif | Peut-elle utiliser le trafic entrant ou sortant ? | Peut-elle être étendue à une zone régionale ? | Peut-elle être utilisée avec le Pare-feu Azure ? |
| --- | -------- |:---:|:---:|:---:|
| **ActionGroup** | Groupe d’actions. | Trafic entrant | Non | Non |
| **ApiManagement** | Trafic de gestion pour les déploiements dédiés de Gestion des API. <br/><br/>**Remarque** : cette étiquette représente le point de terminaison du service Gestion des API Azure pour le plan de contrôle par région. Ceci permet aux clients d’effectuer des opérations de gestion sur les API, les opérations, les stratégies et les valeurs nommées configurées sur le service Gestion des API.  | Trafic entrant | Oui | Oui |
| **ApplicationInsightsAvailability** | Disponibilité d’Application Insights. | Trafic entrant | Non | Non |
| **AppConfiguration** | App Configuration. | Règle de trafic sortant | Non | Non |
| **AppService**    | Azure App Service Cette balise est recommandée pour les règles de sécurité sortantes vers les applications web et les applications de fonction.  | Règle de trafic sortant | Oui | Oui |
| **AppServiceManagement** | Trafic de gestion pour les déploiements dédiés vers App Service Environment. | Les deux | Non | Oui |
| **AzureActiveDirectory** | Azure Active Directory | Règle de trafic sortant | Non | Oui |
| **AzureActiveDirectoryDomainServices** | Trafic de gestion pour les déploiements dédiés vers Azure Active Directory Domain Services. | Les deux | Non | Oui |
| **AzureAdvancedThreatProtection** | Azure Advanced Threat Protection. | Règle de trafic sortant | Non | Non |
| **AzureArcInfrastructure** | Serveurs Azure Arc, Kubernetes avec Azure Arc et trafic Guest Configuration.<br/><br/>**Remarque** : cette étiquette a une dépendance vis-à-vis des étiquettes **AzureActiveDirectory**, **AzureTrafficManager** et **AzureResourceManager**. Cette étiquette n’est actuellement pas configurable sur le portail Azure.| Règle de trafic sortant | Non | Oui |
| **AzureAttestation** | Azure Attestation.<br/><br/>**Remarque** : cette étiquette n’est actuellement pas configurable sur le portail Azure. | Règle de trafic sortant | Non | Oui | 
| **AzureBackup** |Sauvegarde Azure.<br/><br/>**Remarque** : cette étiquette est dotée d’une dépendance par rapport aux étiquettes **Storage** et **AzureActiveDirectory**. | Règle de trafic sortant | Non | Oui |
| **AzureBotService** | Service Azure Bot. | Règle de trafic sortant | Non | Non |
| **AzureCloud** | Toutes les [adresses IP publiques du centre de données](https://www.microsoft.com/download/details.aspx?id=56519). | Règle de trafic sortant | Oui | Oui |
| **AzureCognitiveSearch** | Recherche cognitive Azure. <br/><br/>Cette balise ou les adresses IP qu’elle couvre permettent d’accorder aux indexeurs un accès sécurisé à des sources de données. Pour plus de détails, consultez la [documentation sur la connexion d’indexeur](../search/search-indexer-troubleshooting.md#connection-errors). <br/><br/> **Remarque** : L’adresse IP du service de recherche n’est pas incluse dans la liste des plages d’adresses IP pour cette balise de service et **doit également être ajoutée** au pare-feu IP des sources de données. | Trafic entrant | Non | Non |
| **AzureConnectors** | Cette étiquette représente les adresses IP utilisées pour les connecteurs managés qui effectuent des rappels de webhook entrants vers le service Azure Logic Apps et des appels sortants vers leurs services respectifs, par exemple, Stockage Azure ou Azure Event Hubs. | Trafic entrant/sortant | Oui | Oui |
| **AzureContainerRegistry** | Azure Container Registry. | Règle de trafic sortant | Oui | Oui |
| **AzureCosmosDB** | Azure Cosmos DB. | Règle de trafic sortant | Oui | Oui |
| **AzureDatabricks** | Azure Databricks. | Les deux | Non | Non |
| **AzureDataExplorerManagement** | Gestion d’Azure Data Explorer. | Trafic entrant | Non | Non |
| **AzureDataLake** | Azure Data Lake Storage Gen1. | Règle de trafic sortant | Non | Oui |
| **AzureDeviceUpdate** | Device Update pour IoT Hub. | Les deux | Non | Oui |
| **AzureDevSpaces** | Azure Dev Spaces. | Règle de trafic sortant | Non | Non |
| **AzureDevOps** | Azure Dev Ops.<br/><br/>**Remarque** : cette étiquette n’est actuellement pas configurable sur le portail Azure.| Trafic entrant | Non | Oui |
| **AzureDigitalTwins** | Azure Digital Twins.<br/><br/>**Remarque** : Cette étiquette et les adresses IP qu’elle couvre peuvent être utilisées pour restreindre l’accès aux points de terminaison configurés pour des routes d’événements. Cette étiquette n’est actuellement pas configurable sur le portail Azure. | Trafic entrant | Non | Oui |
| **AzureEventGrid** | Azure Event Grid. | Les deux | Non | Non |
| **AzureFrontDoor.Frontend** <br/> **AzureFrontDoor.Backend** <br/> **AzureFrontDoor.FirstParty**  | Azure Front Door. | Les deux | Non | Non |
| **AzureInformationProtection** | Azure Information Protection.<br/><br/>**Remarque** : cette étiquette est dotée d’une dépendance par rapport aux étiquettes **AzureActiveDirectory**, **AzureFrontDoor.Frontend** et **AzureFrontDoor.FirstParty**. | Règle de trafic sortant | Non | Non |
| **AzureIoTHub** | Azure IoT Hub. | Règle de trafic sortant | Oui | Non |
| **AzureKeyVault** | Azure Key Vault.<br/><br/>**Remarque** : cette étiquette est dotée d’une dépendance par rapport à l’étiquette **AzureActiveDirectory**. | Règle de trafic sortant | Oui | Oui |
| **AzureLoadBalancer** | Équilibrage de charge de l’infrastructure Azure. Elle est translatée vers l’[adresse IP virtuelle de l’hôte](./network-security-groups-overview.md#azure-platform-considerations) (168.63.129.16) d’où proviennent les sondes d’intégrité d’Azure. Cela comprend uniquement le trafic de sondes, et non le trafic réel vers votre ressource principale. Vous pouvez remplacer cette règle si vous n’utilisez pas l’équilibreur de charge Azure. | Les deux | Non | Non |
| **AzureMachineLearning** | Azure Machine Learning. | Les deux | Non | Oui |
| **AzureMonitor** | Métriques Log Analytics, Application Insights, AzMon et métriques personnalisées (points de terminaison GiG).<br/><br/>**Remarque :** pour Log Analytics, l’étiquette **Storage** est également nécessaire. Si des agents Linux sont utilisés, l’étiquette **GuestAndHybridManagement** est également nécessaire. | Règle de trafic sortant | Non | Oui |
| **AzureOpenDatasets** | Azure Open Datasets.<br/><br/>**Remarque** : cette étiquette est dotée d’une dépendance par rapport aux étiquettes **AzureFrontDoor.Frontend** et **Storage**. | Règle de trafic sortant | Non | Non |
| **AzurePlatformDNS** | Service DNS de l’infrastructure de base (par défaut).<br/><br>Vous pouvez utiliser cette balise pour désactiver le DNS par défaut. Utilisez cette balise avec prudence. Nous vous recommandons de lire les [considérations sur la plateforme Azure](./network-security-groups-overview.md#azure-platform-considerations). Nous vous recommandons également d’effectuer des tests avant d’utiliser cette balise. | Règle de trafic sortant | Non | Non |
| **AzurePlatformIMDS** | Azure Instance Metadata Service (IMDS), qui est un service d’infrastructure de base.<br/><br/>Vous pouvez utiliser cette balise pour désactiver le point de terminaison IMDS par défaut. Utilisez cette balise avec prudence. Nous vous recommandons de lire les [considérations sur la plateforme Azure](./network-security-groups-overview.md#azure-platform-considerations). Nous vous recommandons également d’effectuer des tests avant d’utiliser cette balise. | Règle de trafic sortant | Non | Non |
| **AzurePlatformLKM** | Gestionnaire de licences Windows ou service de gestion de clés.<br/><br/>Vous pouvez utiliser cette balise pour désactiver les paramètres par défaut des licences. Utilisez cette balise avec prudence. Nous vous recommandons de lire les [considérations sur la plateforme Azure](./network-security-groups-overview.md#azure-platform-considerations).  Nous vous recommandons également d’effectuer des tests avant d’utiliser cette balise. | Règle de trafic sortant | Non | Non |
| **AzureResourceManager** | Azure Resource Manager. | Règle de trafic sortant | Non | Non |
| **AzureSignalR** | Azure SignalR. | Règle de trafic sortant | Non | Non |
| **AzureSiteRecovery** | Azure Site Recovery.<br/><br/>**Remarque** : cette étiquette est dotée d’une dépendance par rapport aux étiquettes **AzureActiveDirectory**, **AzureKeyVault**, **EventHub**, **GuestAndHybridManagement** et **Storage**. | Règle de trafic sortant | Non | Non |
| **AzureSphere** | Cette étiquette et les adresses IP qu’elle couvre peuvent être utilisées pour restreindre l’accès aux services de sécurité Azure Sphere. </br> **Remarque** : cette étiquette n’est actuellement pas configurable sur le portail Azure. | Les deux | Non | Oui | 
| **AzureStack** | Services Azure Stack Bridge. </br> Cette étiquette représente le point de terminaison de service Azure Stack Bridge par région. </br> *Remarque : cette étiquette n’est actuellement pas configurable sur le portail Azure.* | Règle de trafic sortant | Non | Oui |
| **AzureTrafficManager** | Adresses IP de sonde Azure Traffic Manager.<br/><br/>Pour plus d’informations sur les adresses IP de sondage Traffic Manager, consultez les [Questions fréquentes (FAQ) sur Azure Traffic Manager](../traffic-manager/traffic-manager-faqs.md). | Trafic entrant | Non | Oui |  
| **AzureUpdateDelivery** | Pour accéder aux mises à jour de Windows. <br/><br/>**Remarque :** cette étiquette permet d’accéder aux services de métadonnées Windows Update. Pour télécharger les mises à jour avec succès, vous devez également activer la balise de service **AzureFrontDoor.FirstParty** et configurer les règles de sécurité de trafic sortant avec le protocole et le port définis, comme suit : <ul><li>AzureUpdateDelivery: TCP, port 443</li><li>AzureFrontDoor.FirstParty: TCP, port 80</li></ul>*Cette étiquette n’est actuellement pas configurable sur le portail Azure*.| Règle de trafic sortant | Non | Non |  
| **BatchNodeManagement** | Trafic de gestion pour les déploiements dédiés d’Azure Batch. | Les deux | Non | Oui |
| **CognitiveServicesManagement** | Plages d’adresses du trafic pour Azure Cognitive Services. | Les deux | Non | Non |
| **DataFactory**  | Azure Data Factory | Les deux | Non | Non |
| **DataFactoryManagement** | Trafic de gestion pour Azure Data Factory. | Règle de trafic sortant | Non | Non |
| **Dynamics365ForMarketingEmail** | Plages d’adresses pour le service de messagerie marketing de Dynamics 365. | Règle de trafic sortant | Oui | Non |
| **EOPExternalPublishedIPs** | Cette étiquette représente les adresses IP utilisées pour le Centre de sécurité et de conformité PowerShell. Pour plus d'informations, consultez [Se connecter au Centre de sécurité et de conformité PowerShell à l'aide du module EXO V2](/powershell/exchange/connect-to-scc-powershell). <br/><br/> **Remarque** : cette étiquette n’est actuellement pas configurable sur le portail Azure. | Les deux | Non | Oui |
| **EventHub** | Azure Event Hubs. | Règle de trafic sortant | Oui | Oui |
| **GatewayManager** | Trafic de gestion pour les déploiements dédiés à la passerelle VPN Azure et Application Gateway. | Trafic entrant | Non | Non |
| **GuestAndHybridManagement** | Azure Automation et Guest Configuration. | Règle de trafic sortant | Non | Oui |
| **HDInsight** | Azure HDInsight | Trafic entrant | Oui | Non |
| **Internet** | Espace d’adresse IP qui se trouve en dehors du réseau virtuel et est accessible sur les réseaux Internet publics.<br/><br/>La plage d’adresse inclut l’[espace de l’adresse IP public d’Azure](https://www.microsoft.com/download/details.aspx?id=41653). | Les deux | Non | Non |
| **LogicApps** | Logic Apps. | Les deux | Non | Non |
| **LogicAppsManagement** | Trafic de gestion pour Logic Apps. | Trafic entrant | Non | Non |
| **MicrosoftAzureFluidRelay** | Cette étiquette représente les adresses IP utilisées pour Azure Microsoft Fluid Relay Server. | Règle de trafic sortant | Non | Non |
| **MicrosoftCloudAppSecurity** | Applications Microsoft Defender pour le cloud. | Règle de trafic sortant | Non | Non |
| **MicrosoftContainerRegistry** | Registre de conteneurs pour les images de conteneur Microsoft. <br/><br/>**Remarque** : cette étiquette est dotée d’une dépendance par rapport à l’étiquette **AzureFrontDoor.FirstParty**. | Règle de trafic sortant | Oui | Oui |
| **PowerBI** | Power BI. **Remarque** : cette étiquette n’est actuellement pas configurable sur le portail Azure. | Les deux | Non | Non|
| **PowerPlatformInfra** | Cette étiquette représente les adresses IP utilisées par l’infrastructure pour héberger les services Power Platform. **Remarque** : cette étiquette n’est actuellement pas configurable sur le portail Azure. | Règle de trafic sortant | Non | Non |
| **PowerQueryOnline** | Power Query en ligne. | Les deux | Non | Non |
| **ServiceBus** | Trafic Azure Service Bus qui utilise le niveau de service Premium. | Règle de trafic sortant | Oui | Oui |
| **ServiceFabric** | Azure Service Fabric.<br/><br/>**Remarque** : cette étiquette représente le point de terminaison du service Service Fabric pour le plan de contrôle par région. Ceci permet aux clients d’effectuer des opérations de gestion pour leurs clusters Service Fabric à partir de leur réseau virtuel (point de terminaison, par exemple https:// westus.servicefabric.azure.com). | Les deux | Non | Non |
| **Sql** | Azure SQL Database, Azure Database pour MySQL, Azure Database pour PostgreSQL, Azure Database for MariaDB et Azure Synapse Analytics.<br/><br/>**Remarque** : cette étiquette représente le service, mais pas des instances spécifiques du service. Par exemple, la balise représente le service Azure SQL Database, mais pas une base de données ou un serveur SQL spécifique. Cette balise ne s’applique pas à une instance gérée SQL. | Règle de trafic sortant | Oui | Oui |
| **SqlManagement** | Trafic de gestion pour les déploiements dédiés de SQL. | Les deux | Non | Oui |
| **Stockage** | Stockage Azure. <br/><br/>**Remarque** : cette étiquette représente le service, mais pas des instances spécifiques du service. Par exemple, la balise représente le service Azure Storage, mais pas un compte Azure Storage spécifique. | Règle de trafic sortant | Oui | Oui |
| **StorageSyncService** | Service de synchronisation du stockage. | Les deux | Non | Non |
| **WindowsAdminCenter** | Autoriser le service principal du Centre d’administration Windows à communiquer avec l’installation des clients du Centre d’administration Windows. **Remarque** : cette étiquette n’est actuellement pas configurable sur le portail Azure. | Règle de trafic sortant | Non | Oui |
| **WindowsVirtualDesktop** | Windows Virtual Desktop. | Les deux | Non | Oui |
| **VirtualNetwork** | L’espace d’adressage du réseau virtuel (toutes les plages d’adresses IP définies pour le réseau virtuel), tous les espaces d’adressage locaux connectés, les réseaux virtuels [appairés](virtual-network-peering-overview.md) ou connectés à une [passerelle de réseau virtuel](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%3ftoc.json), l’[adresse IP virtuelle de l’hôte](./network-security-groups-overview.md#azure-platform-considerations) et les préfixes d’adresse utilisés sur les [routes définies par l’utilisateur](virtual-networks-udr-overview.md). Cette balise peut également contenir des itinéraires par défaut. | Les deux | Non | Non |

> [!NOTE]
>
> - Les balises des services Azure indiquent les préfixes d’adresse du cloud spécifique utilisé. Par exemple, les plages IP sous-jacentes qui correspondent à la valeur de balise **Sql** sur le cloud public Azure seront différentes des plages sous-jacentes sur le cloud Azure Chine.
>
> - Si vous implémentez un [point de terminaison de service de réseau virtuel](virtual-network-service-endpoints-overview.md) pour un service, par exemple Stockage Azure ou Azure SQL Database, Azure ajoute un [itinéraire](virtual-networks-udr-overview.md#optional-default-routes) vers un sous-réseau de réseau virtuel pour le service. Les préfixes d’adresse de l’itinéraire sont les mêmes préfixes d’adresse ou plages CIDR que pour la balise de service correspondante.


### <a name="tags-supported-in-the-classic-deployment-model"></a>Étiquettes prises en charge dans le modèle de déploiement classique

Le modèle de déploiement classique (antérieur à Azure Resource Manager) prend en charge un petit sous-ensemble des étiquettes répertoriées dans le tableau précédent. Les étiquettes dans le modèle de déploiement classique sont orthographiées différemment, comme indiqué dans le tableau suivant :

| Étiquette Resource Manager | Étiquette correspondante dans le modèle de déploiement classique |
|---|---|
| **AzureLoadBalancer** | AZURE_LOADBALANCER |
| **Internet** | INTERNET |
| **VirtualNetwork** | VIRTUAL_NETWORK |


## <a name="service-tags-on-premises"></a>Balises de service locales  
Vous pouvez obtenir la balise de service et les informations de plage actuelles à inclure dans le cadre de vos configurations du pare-feu local. Ces informations forment la liste actuelle des plages d’adresses IP qui correspondent à chaque balise de service. Vous pouvez obtenir les informations par programmation ou par le biais d’un téléchargement de fichier JSON, comme décrit dans les sections suivantes.

### <a name="use-the-service-tag-discovery-api"></a>Utiliser l’API Service Tag Discovery
Vous pouvez récupérer par programmation la liste actuelle des balises de service ainsi que les informations relatives aux plages d’adresses IP :

- [REST](/rest/api/virtualnetwork/servicetags/list)
- [Azure PowerShell](/powershell/module/az.network/Get-AzNetworkServiceTag)
- [Azure CLI](/cli/azure/network#az_network_list_service_tags)

Par exemple, pour récupérer tous les préfixes de l’étiquette de service Stockage, vous pouvez utiliser les applets de commande PowerShell suivantes : 

```azurepowershell-interactive
$serviceTags = Get-AzNetworkServiceTag -Location eastus2
$storage = $serviceTags.Values | Where-Object { $_.Name -eq "Storage" }
$storage.Properties.AddressPrefixes
```

> [!NOTE]
> 
> - La propagation des nouvelles données d’étiquette de service dans les résultats de l’API de toutes les régions Azure prend jusqu’à 4 semaines. 
> - Vous devez être authentifié et disposer d’un rôle avec des autorisations de lecture pour votre abonnement actuel. 
> - Les données d’API représentent les étiquettes qui peuvent être utilisées avec les règles de groupe de sécurité réseau, qui représente un sous-ensemble des étiquettes actuellement présentes dans le fichier JSON téléchargeable. 

### <a name="discover-service-tags-by-using-downloadable-json-files"></a>Détection de balises de service à l’aide de fichiers JSON téléchargeables 
Vous pouvez télécharger des fichiers JSON qui contiennent la liste actuelle des balises de service avec les informations relatives aux plages d’adresses IP. Ces listes sont mises à jour et publiées chaque semaine. Les emplacements de chaque Cloud sont :

- [Azure public](https://www.microsoft.com/download/details.aspx?id=56519)
- [Azure US Government](https://www.microsoft.com/download/details.aspx?id=57063)  
- [Azure Chine](https://www.microsoft.com/download/details.aspx?id=57062) 
- [Azure Allemagne](https://www.microsoft.com/download/details.aspx?id=57064)   

Les plages d’adresses IP de ces fichiers sont en notation CIDR. 

Les étiquettes AzureCloud suivantes n’ont pas de noms régionaux formatés selon le schéma normal : 
- AzureCloud.centralfrance (FranceCentral)
- AzureCloud.southfrance (FranceSouth)
- AzureCloud.germanywc (GermanyWestCentral)
- AzureCloud.germanyn (GermanyNorth)
- AzureCloud.norwaye (NorwayEast)
- AzureCloud.norwayw (NorwayWest)
- AzureCloud.switzerlandn (SwitzerlandNorth)
- AzureCloud.switzerlandw (SwitzerlandWest)
- AzureCloud.usstagee (EastUSSTG)
- AzureCloud.usstagec (SouthCentralUSSTG)


> [!NOTE]
> Un sous-ensemble de ces informations a été publié dans des fichiers XML pour [Azure public](https://www.microsoft.com/download/details.aspx?id=41653), [Azure Chine](https://www.microsoft.com/download/details.aspx?id=42064) et [Azure Allemagne](https://www.microsoft.com/download/details.aspx?id=54770). Ces téléchargements XML seront obsolètes à compter du 30 juin 2020 et ne seront plus disponibles après cette date. Vous devez effectuer une migration à l’aide de l’API Discovery ou des téléchargements de fichiers JSON, comme décrit dans les sections précédentes.

> [!TIP]
> 
> - Vous pouvez détecter les mises à jour d’une publication à l’autre en notant les valeurs *changeNumber* augmentées dans le fichier JSON. Chaque sous-section (par exemple, **Storage.WestUS**) a sa propre valeur *changeNumber* qui est incrémentée au fur et à mesure que des modifications sont effectuées. Le niveau supérieur de la valeur *changeNumber* du fichier est incrémenté lorsque l’une des sous-sections est modifiée.
>
> - Pour obtenir des exemples d’analyse des informations de balise de service (par exemple, obtenir toutes les plages d’adresses pour le stockage dans la région WestUS), reportez-vous à la documentation relative à l’[API Service Tag Discovery PowerShell](/powershell/module/az.network/Get-AzNetworkServiceTag).
>
> - Lorsque de nouvelles adresses IP sont ajoutées aux étiquettes de service, elles ne sont pas utilisées dans Azure pendant au moins une semaine. Cela vous donne le temps de mettre à jour les systèmes qui peuvent avoir besoin d’effectuer le suivi des adresses IP associées aux balises de service.


## <a name="next-steps"></a>Étapes suivantes
- Découvrez comment [créer des groupes de sécurité réseau](tutorial-filter-network-traffic.md).
