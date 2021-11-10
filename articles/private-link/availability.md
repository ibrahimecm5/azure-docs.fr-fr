---
title: Disponibilité d’Azure Private Link
description: Dans cet article, découvrez quels services Azure prennent en charge Private Link.
author: asudbring
ms.author: allensu
ms.service: private-link
ms.topic: conceptual
ms.date: 3/15/2021
ms.custom: template-concept,references_regions
ms.openlocfilehash: ff4b23ffaf366ac9c1583b6fb9eb87f87b7db0e0
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131428935"
---
# <a name="azure-private-link-availability"></a>Disponibilité d’Azure Private Link

Azure Private Link vous permet d’accéder aux services Azure PaaS (par exemple Stockage Azure et SQL Database) ainsi qu’aux services de partenaires ou de clients hébergés par Azure sur un [point de terminaison privé](private-endpoint-overview.md) dans votre réseau virtuel. 

> [!IMPORTANT]
> Azure Private Link est maintenant en disponibilité générale. Le point de terminaison privé et le service Private Link (service derrière l’équilibreur de charge standard) sont tous les deux en disponibilité générale. Différentes versions d’Azure PaaS seront intégrées à Azure Private Link à différentes échéances. Pour en savoir plus sur les limitations connues, consultez [Point de terminaison privé](private-endpoint-overview.md#limitations) et [Service Liaison privée](private-link-service-overview.md#limitations). 

## <a name="service-availability"></a>Disponibilité du service

Les tableaux suivants listent les services Private Link et les régions où ils sont disponibles. 

### <a name="ai--machine-learning"></a>IA + Machine Learning

|Services pris en charge  |Régions disponibles | Autres considérations | État  |
|:-------------------|:-----------------|:----------------|:--------|
|Azure Machine Learning | Toutes les régions publiques    |  | GA   <br/> [Découvrez comment créer un point de terminaison privé pour Azure Machine Learning.](../machine-learning/how-to-configure-private-link.md)   |

### <a name="analytics"></a>Analytics

|Services pris en charge  |Régions disponibles | Autres considérations | État  |
|:-------------------|:-----------------|:----------------|:--------|
|Azure Synapse Analytics| Toutes les régions publiques <br/> Toutes les régions administratives |  Pris en charge pour la [stratégie de connexion](../azure-sql/database/connectivity-architecture.md#connection-policy) Proxy |GA <br/> [Découvrez comment créer un point de terminaison privé pour Azure Synapse Analytics.](../azure-sql/database/private-endpoint-overview.md)|
|Azure Event Hub | Toutes les régions publiques<br/>Toutes les régions administratives      |   | GA   <br/> [Découvrez comment créer un point de terminaison privé pour Azure Event Hub.](../event-hubs/private-link-service.md)  |
| Azure Monitor <br/>(Log Analytics & Application Insights) | Toutes les régions publiques      |  | GA   <br/> [Découvrez comment créer un point de terminaison privé pour Azure Monitor.](../azure-monitor/logs/private-link-security.md)   |
|Azure Data Factory | Toutes les régions publiques<br/> Toutes les régions administratives<br/>Toutes les régions de Chine    | Les informations d’identification doivent être stockées dans un coffre de clés Azure| GA   <br/> [Découvrez comment créer un point de terminaison privé pour Azure Data Factory.](../data-factory/data-factory-private-link.md)   |
|Azure HDInsight | Toutes les régions publiques<br/>Toutes les régions administratives      |   | GA   <br/> [Découvrez comment créer un point de terminaison privé pour Azure HDInsight.](../hdinsight/hdinsight-private-link.md)  |

### <a name="compute"></a>Compute

|Services pris en charge  |Régions disponibles | Autres considérations | État  |
|:-------------------|:-----------------|:----------------|:--------|
|Azure App Configuration | Toutes les régions publiques      |  | GA  </br> [Découvrez comment créer un point de terminaison privé pour Azure App Configuration](../azure-app-configuration/concept-private-endpoint.md) |
|Disques managés Azure | Toutes les régions publiques<br/> Toutes les régions administratives<br/>Toutes les régions de Chine    | [Sélectionner pour connaître les limitations connues](../virtual-machines/disks-enable-private-links-for-import-export-portal.md#limitations) | GA   <br/> [Découvrez comment créer un point de terminaison privé pour Disques managés Azure.](../virtual-machines/disks-enable-private-links-for-import-export-portal.md)   |

### <a name="containers"></a>Conteneurs

|Services pris en charge  |Régions disponibles | Autres considérations | État  |
|:-------------------|:-----------------|:----------------|:--------|
|Azure Container Registry | Toutes les régions publiques<br/> Toutes les régions administratives    | Pris en charge avec le niveau Premium du registre de conteneurs. [Sélectionner des niveaux](../container-registry/container-registry-skus.md)| GA   <br/> [Découvrez comment créer un point de terminaison privé pour Azure Container Registry.](../container-registry/container-registry-private-link.md)   |
|Azure Kubernetes Service - API Kubernetes | Toutes les régions publiques      |  | GA   <br/> [Découvrez comment créer un point de terminaison privé pour Azure Kubernetes Service.](../aks/private-clusters.md)   |

### <a name="databases"></a>Bases de données

|Services pris en charge  |Régions disponibles | Autres considérations | État  |
|:-------------------|:-----------------|:----------------|:--------|
|  Azure SQL Database         | Toutes les régions publiques <br/> Toutes les régions administratives<br/>Toutes les régions de Chine      |  Pris en charge pour la [stratégie de connexion](../azure-sql/database/connectivity-architecture.md#connection-policy) Proxy | GA <br/> [Découvrez comment créer un point de terminaison privé pour Azure SQL](./tutorial-private-endpoint-sql-portal.md)      |
|Azure Cosmos DB|  Toutes les régions publiques<br/> Toutes les régions administratives</br> Toutes les régions de Chine | |GA <br/> [Découvrez comment créer un point de terminaison privé pour Cosmos DB.](./tutorial-private-endpoint-cosmosdb-portal.md)|
|  Azure Database pour PostgreSQL - Serveur unique         | Toutes les régions publiques <br/> Toutes les régions administratives<br/>Toutes les régions de Chine     | Prise en charge des niveaux tarifaires Usage général et À mémoire optimisée | GA <br/> [Découvrez comment créer un point de terminaison privé pour Azure Database pour PostgreSQL.](../postgresql/concepts-data-access-and-security-private-link.md)      |
|  Azure Database pour MySQL         | Toutes les régions publiques<br/> Toutes les régions administratives<br/>Toutes les régions de Chine      |  | GA <br/> [Découvrez comment créer un point de terminaison privé pour Azure Database pour MySQL.](../mysql/concepts-data-access-security-private-link.md)     |
|  Azure Database for MariaDB         | Toutes les régions publiques<br/> Toutes les régions administratives<br/>Toutes les régions de Chine     |  | GA <br/> [Découvrez comment créer un point de terminaison privé pour Azure Database for MariaDB.](../mariadb/concepts-data-access-security-private-link.md)      |

### <a name="integration"></a>Intégration

|Services pris en charge  |Régions disponibles | Autres considérations | État  |
|:-------------------|:-----------------|:----------------|:--------|
|Azure Event Grid| Toutes les régions publiques<br/> Toutes les régions administratives       |  | GA   <br/> [Découvrez comment créer un point de terminaison privé pour Azure Event Grid.](../event-grid/network-security.md) |
|Azure Service Bus | Toutes les régions publiques<br/>Toutes les régions administratives  | Pris en charge avec le niveau Premium d’Azure Service Bus. [Sélectionner des niveaux](../service-bus-messaging/service-bus-premium-messaging.md) | GA   <br/> [Découvrez comment créer un point de terminaison privé pour Azure Service Bus.](../service-bus-messaging/private-link-service.md)    |

### <a name="internet-of-things-iot"></a>Internet des objets (IoT)

|Services pris en charge  |Régions disponibles | Autres considérations | État  |
|:-------------------|:-----------------|:----------------|:--------|
| Azure IoT Hub | Toutes les régions publiques    |  | GA   <br/> [Découvrez comment créer un point de terminaison privé pour Azure IoT Hub.](../iot-hub/virtual-network-support.md) |
|  Azure Digital Twins         | Toutes les régions publiques prises en charge par Azure Digital Twins     |  | Préversion <br/> [Découvrez comment créer un point de terminaison privé pour Azure Digital Twins.](../digital-twins/how-to-enable-private-link-portal.md)      |

### <a name="management-and-governance"></a>Gestion et gouvernance

| Services pris en charge | Régions disponibles | Autres considérations | État  |
| ------------ | ----------------| ------------| ----------------|
| Azure Automation  | Toutes les régions publiques<br/> Toutes les régions administratives |  | GA </br> [Découvrez comment créer un point de terminaison privé pour Azure Automation.](../automation/how-to/private-link-security.md)|
|Sauvegarde Azure | Toutes les régions publiques<br/> Toutes les régions administratives   |  | GA <br/> [Découvrez comment créer un point de terminaison privé pour Sauvegarde Azure.](../backup/private-endpoints.md)   |
|Azure Purview | Asie Sud-Est, Australie Est, Brésil Sud, Europe Nord, Europe Ouest, Canada Centre, USA Est, USA Est 2, USA Est 2 EUAP, USA Centre Sud, USA Centre-Ouest, USA Ouest 2, Inde Centre, Royaume-Uni Sud   | [Sélectionner pour connaître les limitations connues](../purview/catalog-private-link-troubleshoot.md#known-limitations) | GA <br/> [Découvrez comment créer un point de terminaison privé pour Azure Purview.](../purview/catalog-private-link.md)   |

### <a name="security"></a>Sécurité

|Services pris en charge  |Régions disponibles | Autres considérations | État  |
|:-------------------|:-----------------|:----------------|:--------|
|  Azure Key Vault         | Toutes les régions publiques<br/> Toutes les régions administratives      |  | GA   <br/> [Découvrez comment créer un point de terminaison privé pour Azure Key Vault.](../key-vault/general/private-link-service.md)   |

### <a name="storage"></a>Stockage
|Services pris en charge  |Régions disponibles | Autres considérations | État  |
|:-------------------|:-----------------|:----------------|:--------|
| Stockage Blob Azure (Data Lake Storage Gen2 inclus)       |  Toutes les régions publiques<br/> Toutes les régions administratives       |  Pris en charge uniquement avec le type de compte V2 Usage général | GA <br/> [Découvrez comment créer un point de terminaison privé pour Stockage Blob.](tutorial-private-endpoint-storage-portal.md)  |
| Azure Files | Toutes les régions publiques<br/> Toutes les régions administratives      | |   GA <br/> [Découvrez comment créer des points de terminaison réseau Azure Files.](../storage/files/storage-files-networking-endpoints.md)   |
| Azure File Sync | Toutes les régions publiques      | |   GA <br/> [Découvrez comment créer des points de terminaison réseau Azure Files.](../storage/file-sync/file-sync-networking-endpoints.md)   |
| Stockage File d’attente Azure       |  Toutes les régions publiques<br/> Toutes les régions administratives       |  Pris en charge uniquement avec le type de compte V2 Usage général | GA <br/> [Découvrez comment créer un point de terminaison privé pour Stockage File d’attente.](tutorial-private-endpoint-storage-portal.md) |
| Stockage Table Azure       |  Toutes les régions publiques<br/> Toutes les régions administratives       |  Pris en charge uniquement avec le type de compte V2 Usage général | GA <br/> [Découvrez comment créer un point de terminaison privé pour Stockage Table.](tutorial-private-endpoint-storage-portal.md)  |
| Azure Batch | Toutes les régions publiques sauf : Allemagne CENTRE, Allemagne NORD-EST <br/> Toutes les régions administratives  | | GA <br/> [Découvrez comment créer un point de terminaison privé pour Azure Batch.](../batch/private-connectivity.md) |

### <a name="web"></a>Web
|Services pris en charge  |Régions disponibles | Autres considérations | État  |
|:-------------------|:-----------------|:----------------|:--------|
| Azure SignalR | USA EST, USA CENTRE SUD,<br/>USA OUEST 2, Toutes les régions de Chine      |  | PRÉVERSION   <br/> [Découvrez comment créer un point de terminaison privé pour Azure SignalR.](../azure-signalr/howto-private-endpoints.md)   |
|Azure Web Apps | Toutes les régions publiques<br/> Chine Nord 2 et Est 2    | Pris en charge avec le plan PremiumV2, PremiumV3 ou Function Premium  | GA   <br/> [Découvrez comment créer un point de terminaison privé pour Azure Web Apps.](./tutorial-private-endpoint-webapp-portal.md)   |
|Recherche Azure | Toutes les régions publiques <br/> Toutes les régions administratives | Pris en charge avec le service en mode privé | GA   <br/> [Découvrez comment créer un point de terminaison privé pour Recherche Azure.](../search/service-create-private-endpoint.md)    |
|Azure Relay | Toutes les régions publiques      |  | PRÉVERSION <br/> [Découvrez comment créer un point de terminaison privé pour Azure Relay.](../azure-relay/private-link-service.md)  |

### <a name="private-link-service-with-a-standard-load-balancer"></a>Service Private Link avec un équilibreur de charge Standard

|Services pris en charge  |Régions disponibles | Autres considérations | Statut  |
|:-------------------|:-----------------|:----------------|:--------|
|Services Private Link derrière l’instance Azure Load Balancer standard | Toutes les régions publiques<br/> Toutes les régions administratives<br/>Toutes les régions de Chine  | Pris en charge avec Standard Load Balancer | GA <br/> [Découvrez comment créer un service Private Link.](create-private-link-service-portal.md) |

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur le service Azure Private Link :
- [Qu’est-ce que Liaison privée Azure ?](private-link-overview.md)
- [Créer un point de terminaison privé en utilisant le portail Azure](create-private-endpoint-portal.md)
