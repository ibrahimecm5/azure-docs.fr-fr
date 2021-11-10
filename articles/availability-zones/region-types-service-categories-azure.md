---
title: Services Azure
description: En savoir plus sur les types de régions et les catégories de services dans Azure.
author: mamccrea
ms.service: azure
ms.topic: conceptual
ms.date: 10/01/2021
ms.author: mamccrea
ms.reviewer: cynthn
ms.custom: references_regions
ms.openlocfilehash: 7a8159a10749e877c6502ef775071af6cc420f46
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132059511"
---
# <a name="azure-services"></a>Services Azure

La disponibilité des services dans les régions Azure dépend du type de région. Il existe deux types de régions dans Azure : *recommandée* et *alternative*.

- **Recommandée** : ces régions fournissent le plus grand nombre de fonctionnalités de service et prennent actuellement en charge les zones de disponibilité. Elles sont désignées dans le portail Azure avec le libellé **Recommandée**.
- **Alternative** : ces régions étendent l’empreinte d’Azure au sein d’une limite de résidence des données où une région recommandée existe déjà. Les autres régions vous aident à optimiser la latence et à fournir une deuxième région pour les besoins de récupération d’urgence, mais elles ne prennent pas en charge les zones de disponibilité. Azure examine régulièrement des régions alternatives afin de déterminer si elles doivent devenir des régions recommandées. Elles sont désignées dans le portail Azure par le libellé **Autre**.

## <a name="service-categories-across-region-types"></a>Catégories de services dans les types de régions

Les services Azure sont regroupés en trois catégories : *de base*, *standard* et *stratégiques*. La stratégie générale d’Azure pour le déploiement de services dans une région donnée est principalement pilotée par le type de région, les catégories de service et la demande des clients.

- **De base** : disponibles dans toutes les régions recommandées et alternatives lorsque la région est généralement disponible, ou dans les 90 jours suivant la disponibilité générale d’un nouveau service.
- **Standard** : disponibles dans toutes les régions recommandées dans les 90 jours suivant la disponibilité générale de la région. Disponibilité basée sur la demande dans les régions alternatives, et beaucoup sont déjà déployés dans un grand sous-ensemble de régions alternatives.
- **Stratégiques** (anciennement Spécialisé) : ciblés, souvent axés sur le secteur ou sur du matériel personnalisé. Disponibilité basée sur la demande entre les régions, et beaucoup sont déjà déployés dans un grand sous-ensemble de régions recommandées.

Pour savoir quels services sont déployés dans une région et connaître la feuille de route à venir pour la préversion ou la disponibilité générale des services dans une région, consultez [Produits disponibles par région](https://azure.microsoft.com/global-infrastructure/services/).

Si une offre de service n’est pas disponible dans une région, contactez votre représentant commercial Microsoft pour plus d’informations et pour explorer les options.

| Type de région | Région non précisée | De base | Standard | Strategic | Zones de disponibilité | Résidence des données |
| --- | --- | --- | --- | --- | --- | --- |
| Recommandé | **O** | **O** | **O** | Piloté par la demande | **O** | **O** |
| Alterner | **O** | **O** | Piloté par la demande | Piloté par la demande | N/A | **O** |

## <a name="available-services-by-category"></a>Services disponibles par catégorie

Azure attribue des catégories de services comme de base, standard et stratégiques à la disponibilité générale. Généralement, les services commencent comme des services stratégiques et passent à des services standard et de base à mesure que la demande et l’utilisation augmentent.

Les services Azure sont présentés par catégorie dans les tableaux. Notez que certains services ne sont pas régionaux. Cela signifie qu’ils sont disponibles dans le monde entier, quelle que soit la région. Pour plus d’informations et pour obtenir la liste complète des services non régionaux, consultez [Produits disponibles par région](https://azure.microsoft.com/global-infrastructure/services/).

> [!div class="mx-tableFixed"]
> | ![Une icône indiquant que ce service est un service de base.](media/icon-foundational.svg) De base                           | ![Une icône indiquant que ce service est un service standard.](media/icon-mainstream.svg) Standard                                        | 
> |----------------------------------------|---------------------------------------------------|
> | Comptes de stockage Azure                 | Gestion des API Azure                              | 
> | Azure Application Gateway              | Azure App Configuration                           | 
> | Sauvegarde Azure                           | Azure App Service                                 | 
> | Azure Cosmos DB                        | Azure Automation                                  | 
> | Azure Data Lake Storage Gen2           | Azure Active Directory Domain Services            | 
> | Azure ExpressRoute                     | Azure Bastion                                     | 
> | Adresse IP publique Azure                        | Cache Azure pour Redis                             | 
> | Azure SQL Database                     | Azure Cognitive Services                          | 
> | Azure SQL Managed Instance             | Azure Cognitive Services : Vision par ordinateur         | 
> | Stockage sur disque                           | Azure Cognitive Services : Content Moderator       | 
> | Hubs d'événements Azure                       | Azure Cognitive Services : Face                    | 
> | Azure Key Vault                        | Azure Cognitive Services : Analyse de texte          | 
> | Azure Load Balancer                    | Explorateur de données Azure                               | 
> | Azure Service Bus                      | Azure Database pour MySQL                          | 
> | Azure Service Fabric                   | Azure Database pour PostgreSQL                     | 
> | Stockage Azure : niveaux de stockage blob chaud/froid   | Azure DDoS Protection                       | 
> | Stockage : Disques managés                 | Pare-feu Azure                                    | 
> | Groupes de machines virtuelles identiques Azure       | Azure Firewall Manager                            | 
> | Machines virtuelles Azure                 | Azure Functions                                   | 
> | Machines virtuelles : Azure Dedicated Host | Azure IoT Hub                                     | 
> | Machines virtuelles : Série Av2           | Azure Kubernetes Service (AKS)                    | 
> | Machines virtuelles : Série Bs            | Azure Monitor : Application Insights               | 
> | Machines virtuelles : Série DSv2          | Azure Monitor : Log Analytics                      | 
> | Machines virtuelles : Série DSv3          | Azure Private Link                                | 
> | Machines virtuelles : Série Dv2           | Azure Site Recovery                               | 
> | Machines virtuelles : Série Dv3           | Azure Synapse Analytics                           |     
> | Machines virtuelles : Série ESv3          | Azure Batch                                       | 
> | Machines virtuelles : Série Ev3           | Service cloud Azure : Série M                     | 
> | Réseau virtuel Azure                  | Azure Container Instances                         | 
> | Passerelle VPN Azure                      | Azure Container Registry                          | 
> |                                        | Azure Data Factory                                | 
> |                                        | Azure Event Grid                                  | 
> |                                        | Azure HDInsight                                   |  
> |                                        | Azure Logic Apps                                  | 
> |                                        | Azure Media Services                              | 
> |                                        | Azure Network Watcher                             | 
> |                                        | Stockage Blob Premium                              | 
> |                                        | Stockage de fichiers Premium                             | 
> |                                        | Machines virtuelles : Série Ddsv4                    | 
> |                                        | Machines virtuelles : Série Ddv4                     | 
> |                                        | Machines virtuelles : Série Dsv4                     | 
> |                                        | Machines virtuelles : Série Dv4                      | 
> |                                        | Machines virtuelles : Série Edsv4                    | 
> |                                        | Machines virtuelles : Série Edv4                     | 
> |                                        | Machines virtuelles : Série Esv4                     | 
> |                                        | Machines virtuelles : Série Ev4                      | 
> |                                        | Machines virtuelles : Série Fsv2                     | 
> |                                        | Machines virtuelles : Série M                        | 
> |                                        | WAN virtuel Azure                                 | 

### <a name="strategic-services"></a>Services stratégiques
Comme mentionné précédemment, Azure classe les services en trois catégories : de base, standard et stratégiques. Les catégories de service sont attribuées lors de la mise en disponibilité générale. Souvent, les services démarrent leur cycle de vie en tant que services stratégiques, et les augmentations de la demande et de l’utilisation peuvent leur valoir une promotion au niveau standard ou de base. Le tableau suivant répertorie les services stratégiques. 

> [!div class="mx-tableFixed"]
> | ![Une icône indiquant que ce service est un service stratégique.](media/icon-strategic.svg) Strategic                                          |
> |------------------------------------------------------|
> | API Azure pour FHIR                                   |
> | Azure Analysis Services                              |
> | Azure Blockchain Service                             |
> | Azure Cognitive Services : Le détecteur d’anomalies           |
> | Azure Cognitive Services : Vision personnalisée              |
> | Azure Cognitive Services : Form Recognizer            |
> | Azure Cognitive Services : Lecteur immersif           |
> | Azure Cognitive Services : Language Understanding     |
> | Azure Cognitive Services : Personalizer               |
> | Azure Cognitive Services : QnA Maker                  |
> | Azure Cognitive Services : Services Speech            |
> | Azure Data Share                                     |
> | Azure Databricks                                     |
> | Azure Database for MariaDB                           |
> | Azure Database Migration Service                     |
> | Module de sécurité matériel (HSM) dédié Azure                                  |
> | Azure Digital Twins                                  |
> | Azure Health Bot                                     |
> | Azure HPC Cache                                      |
> | Azure Lab Services                                   |
> | Azure NetApp Files                                   |
> | Azure Red Hat OpenShift                              |
> | Service Azure SignalR                                |
> | Azure Spring Cloud                                   |
> | Azure Stream Analytics                               |
> | Azure Time Series Insights                           |
> | Azure VMware Solution                                |
> | Azure VMware Solution by CloudSimple                 |
> | Azure Spatial Anchors                                |
> | Stockage : Stockage archive                             |
> | Stockage sur disque Ultra Azure                             |
> | Video Indexer                                        |
> | Machines virtuelles : Série DASv4                       |
> | Machines virtuelles : Série Dav4                        |
> | Machines virtuelles : série DCsv2                       |
> | Machines virtuelles : Série EASv4                       |
> | Machines virtuelles : Série Eav4                        |
> | Machines virtuelles : Série HBv1                        |
> | Machines virtuelles : Série HBv2                        |
> | Machines virtuelles : Série HCv1                        |
> | Machines virtuelles : Série H                           |
> | Machines virtuelles : Série LSv2                        |
> | Machines virtuelles : Série Mv2                         |
> | Machines virtuelles : Série NCv3                        |
> | Machines virtuelles : Série NDv2                        |
> | Machines virtuelles : Série NVv3                        |
> | Machines virtuelles : Série NVv4                        | 
> | Machines virtuelles : SAP HANA sur Azure (grandes instances)  |

\*Machines virtuelles prenant en charge les zones de disponibilité : série AV2, série B, série DSv2, série DSv3, série Dv2, série Dv3, série ESv3, série Ev3, série F, série FS, série FSv2, série M.\*

Les services ou machines virtuelles de générations antérieures ne sont pas répertoriés. Pour plus d’informations, voir [Tailles de machines virtuelles des générations précédentes](../virtual-machines/sizes-previous-gen.md).

Pour en savoir plus sur les services en version préliminaire qui ne sont pas encore en disponibilité générale et pour voir la liste de ces services, consultez [Produits disponibles par région](https://azure.microsoft.com/global-infrastructure/services/). Pour obtenir la liste complète des services qui prennent en charge les zones de disponibilité, consultez [Services Azure prenant en charge les zones de disponibilité](az-region.md).

## <a name="next-steps"></a>Étapes suivantes

- [Services Azure prenant en charge les zones de disponibilité](az-region.md)
- [Régions et zones de disponibilité dans Azure](az-overview.md)
