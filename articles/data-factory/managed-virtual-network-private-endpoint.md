---
title: Réseau virtuel managé et points de terminaison privés managés
description: Découvrez le réseau virtuel managé et les points de terminaison privés managés dans Azure Data Factory.
ms.author: lle
author: lrtoyou1223
ms.service: data-factory
ms.subservice: integration-runtime
ms.topic: conceptual
ms.custom: seo-lt-2019, references_regions, devx-track-azurepowershell
ms.date: 09/28/2021
ms.openlocfilehash: c4baf3ee8fdb26bd361dfafbaa29953f6d1d13f8
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131469544"
---
# <a name="azure-data-factory-managed-virtual-network"></a>Réseau virtuel managé Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Cet article décrit le réseau virtuel managé et les points de terminaison privés managés dans Azure Data Factory.


## <a name="managed-virtual-network"></a>Réseau virtuel managé

Quand vous créez un runtime Azure Integration Runtime au sein d’un réseau virtuel managé Azure Data Factory, ce runtime est provisionné avec le réseau virtuel managé et il tire parti des points de terminaison privés pour se connecter en toute sécurité aux magasins de données pris en charge. 

La création d’un runtime Azure Integration Runtime au sein d’un réseau virtuel managé garantit l’isolation et la sécurisation du processus d’intégration des données. 

Avantages de l’utilisation du réseau virtuel managé :

- Avec un réseau virtuel managé, vous pouvez déplacer la charge liée à la gestion du réseau virtuel vers Azure Data Factory. Vous n’avez pas besoin de créer un sous-réseau pour Azure Integration Runtime qui pourrait finalement utiliser de nombreuses adresses IP privées à partir de votre réseau virtuel et nécessiter une planification préalable de l’infrastructure réseau. 
- Il ne demande pas de connaissances approfondies en réseau Azure pour effectuer des intégrations de données de façon sécurisée. Au contraire, le démarrage avec l’opération ETL sécurisée est très simplifié pour les ingénieurs de données. 
- Le réseau virtuel managé ainsi que les points de terminaison privés managés assurent une protection contre l’exfiltration des données. 

> [!IMPORTANT]
>Actuellement, le réseau virtuel managé n’est pris en charge que dans la même région qu’Azure Data Factory.

> [!Note]
>Le runtime d’intégration Azure global existant ne peut pas basculer vers le runtime d’intégration Azure dans un réseau virtuel managé Azure Data Factory, et inversement.
 

:::image type="content" source="./media/managed-vnet/managed-vnet-architecture-diagram.png" alt-text="Architecture de réseau virtuel managé ADF":::

## <a name="managed-private-endpoints"></a>Points de terminaison privés managés

Les points de terminaison privés managés sont des points de terminaison privés créés sur le réseau virtuel managé Azure Data Factory qui établissent une liaison privée vers des ressources Azure. Azure Data Factory gère ces points de terminaison privés à votre place. 

:::image type="content" source="./media/tutorial-copy-data-portal-private/new-managed-private-endpoint.png" alt-text="Nouveau point de terminaison privé managé":::

Azure Data Factory prend en charge les liaisons privées. Une liaison privée vous permet d’accéder aux services Azure (PaaS), tels que Stockage Azure, Azure Cosmos DB, Microsoft Azure Synapse Analytics.

Quand vous utilisez une liaison privée, le trafic entre vos magasins de données et le réseau virtuel managé transite intégralement par le réseau principal de Microsoft. Une liaison privée assure une protection contre les risques liés à l’exfiltration des données. Vous établissez une liaison privée vers une ressource en créant un point de terminaison privé.

Le point de terminaison privé utilise une adresse IP privée sur le réseau virtuel managé pour y placer de fait le service. Les points de terminaison privés sont mappés à une ressource spécifique dans Azure, et non à l’ensemble du service. Les clients peuvent limiter la connectivité à une ressource spécifique approuvée par leur organisation. Apprenez-en davantage sur [les liaisons privées et les points de terminaison privés](../private-link/index.yml).

> [!NOTE]
> Nous vous recommandons de créer des points de terminaison privés managés pour vous connecter à toutes vos sources de données Azure. 
 
> [!WARNING]
> Si un magasin de données PaaS (Blob, ADLS Gen2, Azure Synapse Analytics) a un point de terminaison privé déjà créé, et même s’il autorise l’accès à partir de tous les réseaux, ADF peut uniquement y accéder à l’aide d’un point de terminaison privé managé. En l’absence de point de terminaison privé, vous devez en créer un dans de tels scénarios. 

Une connexion de point de terminaison privé est créée dans un état « en attente » quand vous créez un point de terminaison privé managé dans Azure Data Factory. Un workflow d’approbation est lancé. Le propriétaire de la ressource de liaison privée est responsable de l’approbation ou du refus de la connexion.

:::image type="content" source="./media/tutorial-copy-data-portal-private/manage-private-endpoint.png" alt-text="Point de terminaison privé managé":::

Si le propriétaire approuve la connexion, la liaison privée est établie. S’il la refuse, la liaison privée n’est pas établie. Dans les deux cas, le point de terminaison privé managé est mis à jour avec l’état de la connexion.

:::image type="content" source="./media/tutorial-copy-data-portal-private/approve-private-endpoint.png" alt-text="Approuver le point de terminaison privé managé":::

Seule une instance de point de terminaison privé managé dans un état approuvé peut envoyer du trafic vers une ressource de liaison privée donnée.

## <a name="interactive-authoring"></a>Création interactive
Les options de création interactive sont utilisées pour des fonctionnalités telles que tester la connexion, parcourir la liste des dossiers et la liste des tables, obtenir un schéma et afficher un aperçu des données. Vous pouvez activer la création interactive lors de la création ou de la modification d’un runtime d’intégration Azure figurant dans un réseau virtuel géré par ADF. Le service back-end pré-allouera le calcul pour les fonctionnalités de création interactive. Sinon, le calcul sera alloué chaque fois qu’une opération interactive sera exécutée, ce qui prendra plus de temps. La durée de vie (TTL) pour la création interactive est de 60 minutes, ce qui signifie qu’elle sera automatiquement désactivée 60 minutes après de la dernière opération de création interactive.

:::image type="content" source="./media/managed-vnet/interactive-authoring.png" alt-text="Création interactive":::

## <a name="activity-execution-time-using-managed-virtual-network"></a>Durée de l’activité en utilisant un réseau virtuel managé
En raison de sa conception, le runtime d’intégration Azure dans un réseau virtuel managé passe plus de temps en file d’attente qu’un runtime d’intégration Azure global. En effet, comme nous ne réservons pas de nœud de calcul par fabrique de données, il y a un temps de préchauffage (ou mise en route) avant le démarrage de chaque activité, qui se produit principalement au niveau de la jointure de réseau virtuel plutôt que du runtime d’intégration Azure. Pour les activités autres que de copie, dont les activités de pipeline et les activités externes, une durée de vie (TTL) de 60 minutes est appliquée lorsque vous les déclenchez pour la première fois. Dans cette durée de vie, le temps en file d’attente est plus court, car le nœud est déjà préchauffé. 
> [!NOTE]
> L’activité de copie ne prend pas encore en compte la durée de vie.

> [!NOTE]
> 2 DIU pour l’activité de copie n’est pas pris en charge dans le réseau virtuel géré.

## <a name="create-managed-virtual-network-via-azure-powershell"></a>Créer un réseau virtuel managé via Azure PowerShell
```powershell
$subscriptionId = ""
$resourceGroupName = ""
$factoryName = ""
$managedPrivateEndpointName = ""
$integrationRuntimeName = ""
$apiVersion = "2018-06-01"
$privateLinkResourceId = ""

$vnetResourceId = "subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/managedVirtualNetworks/default"
$privateEndpointResourceId = "subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/managedVirtualNetworks/default/managedprivateendpoints/${managedPrivateEndpointName}"
$integrationRuntimeResourceId = "subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/integrationRuntimes/${integrationRuntimeName}"

# Create managed Virtual Network resource
New-AzResource -ApiVersion "${apiVersion}" -ResourceId "${vnetResourceId}" -Properties @{}

# Create managed private endpoint resource
New-AzResource -ApiVersion "${apiVersion}" -ResourceId "${privateEndpointResourceId}" -Properties @{
        privateLinkResourceId = "${privateLinkResourceId}"
        groupId = "blob"
    }

# Create integration runtime resource enabled with VNET
New-AzResource -ApiVersion "${apiVersion}" -ResourceId "${integrationRuntimeResourceId}" -Properties @{
        type = "Managed"
        typeProperties = @{
            computeProperties = @{
                location = "AutoResolve"
                dataFlowProperties = @{
                    computeType = "General"
                    coreCount = 8
                    timeToLive = 0
                }
            }
        }
        managedVirtualNetwork = @{
            type = "ManagedVirtualNetworkReference"
            referenceName = "default"
        }
    }

```

## <a name="limitations-and-known-issues"></a>Limitations et problèmes connus

### <a name="supported-data-sources"></a>Sources de données prises en charge
Les sources de données suivantes prennent en charge les points de terminaison privés natifs, et peuvent être connectées via une liaison privée à partir d’un réseau virtuel managé ADF.
- Stockage Blob Azure (ne comprend pas de compte de stockage V1)
- Recherche cognitive Azure
- API SQL Azure Cosmos DB
- Azure Data Lake Storage Gen2
- Azure Database for MariaDB
- Azure Database pour MySQL
- Azure Database pour PostgreSQL
- Azure Files (ne comprend pas de compte de stockage V1)
- Azure Key Vault
- Azure Machine Learning
- Service Azure Private Link
- Azure Purview
- Azure SQL Database (sans Azure SQL Managed Instance)
- Azure Synapse Analytics
- Stockage Table Azure (ne comprend pas de compte de stockage V1)

> [!Note]
> Vous pouvez toujours accéder à toutes les sources de données prises en charge par Data Factory via un réseau public.

> [!NOTE]
> Étant donné qu’Azure SQL Managed Instance ne prend pas en charge actuellement le point de terminaison privé natif, vous pouvez y accéder à partir d’un réseau virtuel managé à l’aide d’un service lié privé et d’un équilibreur de charge. Consultez le tutoriel [Guide pratique pour accéder à une instance managée Microsoft Azure SQL à partir d’un VNET managé Data Factory en utilisant un point de terminaison privé](tutorial-managed-virtual-network-sql-managed-instance.md).

### <a name="on-premises-data-sources"></a>Sources de données locales
Pour accéder à des sources de données locales à partir d’un réseau virtuel managé en utilisant un point de terminaison privé, consultez le tutoriel [Guide pratique pour accéder à un serveur SQL Server local à partir d’un VNET managé Data Factory en utilisant un point de terminaison privé](tutorial-managed-virtual-network-on-premise-sql-server.md).

### <a name="azure-data-factory-managed-virtual-network-is-available-in-the-following-azure-regions"></a>Le réseau virtuel managé Azure Data Factory est disponible dans les régions Azure suivantes :
En règle générale, le réseau virtuel managé est disponible pour toutes les régions Azure Data Factory, sauf :
- Inde Sud


### <a name="outbound-communications-through-public-endpoint-from-adf-managed-virtual-network"></a>Communications sortantes via un point de terminaison public à partir du réseau virtuel managé ADF
- Tous les ports sont ouverts pour les communications sortantes.
- Le Stockage Azure et Azure Data Lake Gen2 ne sont pas pris en charge pour une connexion via un point de terminaison public à partir du réseau virtuel managé ADF.

### <a name="linked-service-creation-of-azure-key-vault"></a>Création d’un service lié à Azure Key Vault 
- Lorsque vous créez un service lié pour Azure Key Vault, il n’existe aucune référence Azure Integration Runtime. Vous ne pouvez donc pas créer de point de terminaison privé pendant la création du service lié d’Azure Key Vault. Toutefois, lorsque vous créez un service lié pour des magasins de données qui fait référence au service lié Azure Key Vault et que ce service lié fait référence à Azure Integration Runtime avec un réseau virtuel managé activé, vous pouvez créer un point de terminaison privé pour le service lié Azure Key Vault lors de la création. 
- L’opération **Tester la connexion** du service lié d’Azure Key Vault valide uniquement le format d’URL, mais n’effectue aucune opération sur le réseau.
- La colonne **Utilisant un point de terminaison privé** est toujours indiquée comme vide, même si vous créez un point de terminaison privé pour Azure Key Vault.

### <a name="linked-service-creation-of-azure-hdi"></a>Création d’un service lié de HDI Azure
- La colonne **Utilisation d’un point de terminaison privé** s’affiche toujours vide, même si vous créez un point de terminaison privé pour HDI à l’aide du service de liaison privée et d’un équilibreur de charge avec transfert de port.

:::image type="content" source="./media/managed-vnet/akv-pe.png" alt-text="Point de terminaison privé pour AKV":::

## <a name="next-steps"></a>Étapes suivantes

- Tutoriel : [Générer un pipeline de copie à l’aide d’un réseau virtuel managé et de points de terminaison privés](tutorial-copy-data-portal-private.md) 
- Tutoriel : [Générer un pipeline de dataflow de mappage à l’aide d’un réseau virtuel managé et de points de terminaison privés](tutorial-data-flow-private.md)
