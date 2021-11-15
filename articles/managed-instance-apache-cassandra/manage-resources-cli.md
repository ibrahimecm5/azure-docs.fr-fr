---
title: Gérer les ressources avec Azure CLI - Azure Resource Manager | Microsoft Docs
description: Découvrez les commandes courantes permettant d’automatiser la gestion de votre instance managée Azure pour Apache Cassandra à l’aide d’Azure CLI.
author: TheovanKraay
ms.service: managed-instance-apache-cassandra
ms.topic: how-to
ms.date: 11/02/2021
ms.author: thvankra
ms.custom: devx-track-azurecli, seo-azure-cli, ignite-fall-2021
keywords: interface cli azure resource manager
ms.openlocfilehash: 1cb28336c27a6f470a305917a267fc9c9320620a
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2021
ms.locfileid: "131892460"
---
# <a name="manage-azure-managed-instance-for-apache-cassandra-resources-using-azure-cli"></a>Gérer les ressources Azure Managed Instance pour Apache Cassandra avec Azure CLI

Cet article décrit les commandes courantes permettant d’automatiser la gestion de vos clusters Azure Managed Instance pour Apache Cassandra à l’aide d’Azure CLI.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

> [!IMPORTANT]
> Pour cet article, vous avez besoin d’Azure CLI version 2.30.0 ou ultérieure. Si vous utilisez Azure Cloud Shell, la version la plus récente est déjà installée.
>
> Les ressources managées Azure Managed Instance pour Apache Cassandra ne peuvent pas être renommées, car cela enfreint les règles de traitement des URI de ressources par Azure Resource Manager.

## <a name="azure-managed-instance-for-apache-cassandra-clusters"></a>Clusters Azure Managed Instance pour Apache Cassandra

Les sections suivantes montrent comment gérer les clusters Azure Managed Instance pour Apache Cassandra, notamment :

* [Créer un cluster Managed Instance](#create-cluster)
* [Supprimer un cluster Managed Instance](#delete-cluster)
* [Obtenir les détails du cluster](#get-cluster-details)
* [Obtenir le statut des nœuds de cluster](#get-cluster-status)
* [Lister les clusters par groupe de ressources](#list-clusters-resource-group)
* [Lister les clusters par ID d’abonnement](#list-clusters-subscription)

### <a name="create-a-managed-instance-cluster"></a><a id="create-cluster"></a>Créer un cluster Managed Instance

Créez un cluster Azure Managed Instance pour Apache Cassandra à l’aide de la commande [az managed-cassandra cluster create](/cli/azure/managed-cassandra/cluster?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_cluster_create) :

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'
location='West US'
delegatedManagementSubnetId='/subscriptions/<subscription id>/resourceGroups/customer-vnet-rg/providers/Microsoft.Network/virtualNetworks/customer-vnet/subnets/management'
initialCassandraAdminPassword='myPassword'

# You can override the cluster name if the original name is not legal for an Azure resource.
# overrideClusterName='ClusterNameIllegalForAzureResource'
# the default Cassandra version is v3.11

az managed-cassandra cluster create \
    --cluster-name $clusterName \
    --resource-group $resourceGroupName \
    --location $location \
    --delegated-management-subnet-id $delegatedManagementSubnetId \
    --initial-cassandra-admin-password $initialCassandraAdminPassword \
```

### <a name="delete-a-managed-instance-cluster"></a><a id="delete-cluster"></a>Supprimer un cluster Managed Instance

Supprimez un cluster à l’aide de la commande [az managed-cassandra cluster delete](/cli/azure/managed-cassandra/cluster?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_cluster_delete) :

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'

az managed-cassandra cluster delete \
    --cluster-name $clusterName \
    --resource-group $resourceGroupName
```

### <a name="get-the-cluster-details"></a><a id="get-cluster-details"></a>Obtenir les détails du cluster

Récupérez les détails du cluster à l’aide de la commande [az managed-cassandra cluster show](/cli/azure/managed-cassandra/cluster?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_cluster_show) :

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'

az managed-cassandra cluster show \
    --cluster-name $clusterName \
    --resource-group $resourceGroupName
```

### <a name="get-the-cluster-node-status"></a><a id="get-cluster-status"></a>Obtenir le statut des nœuds de cluster

Récupérez les détails du nœud de cluster à l’aide de la commande [az managed-cassandra cluster node-status](/cli/azure/managed-cassandra/cluster?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_cluster_node_status) :

```azurecli-interactive
clusterName='cassandra-hybrid-cluster'
resourceGroupName='MyResourceGroup'

az managed-cassandra cluster status \
    --cluster-name $clusterName \
    --resource-group $resourceGroupName
```

### <a name="list-the-clusters-by-resource-group"></a><a id="list-clusters-resource-group"></a>Lister les clusters par groupe de ressources

Répertoriez les clusters par groupe de ressources à l’aide de la commande [az managed-cassandra cluster list](/cli/azure/managed-cassandra/cluster?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_cluster_list) :

```azurecli-interactive
subscriptionId='MySubscriptionId'
resourceGroupName='MyResourceGroup'

az managed-cassandra cluster list\
    --resource-group $resourceGroupName
```

### <a name="list-clusters-by-subscription-id"></a><a id="list-clusters-subscription"></a>Lister les clusters par ID d’abonnement

Répertoriez les clusters par ID d’abonnement à l’aide de la commande [az managed-cassandra cluster list](/cli/azure/managed-cassandra?view=azure-cli-latest&preserve-view=true) :

```azurecli-interactive
# set your subscription id
az account set -s <subscriptionID>

az managed-cassandra cluster list
```

## <a name="the-managed-instance-datacenters"></a><a id="managed-instance-datacenter"></a>Les centres de données d’instance managée

Les sections suivantes montrent comment gérer les centres de données Azure Managed Instance pour Apache Cassandra, notamment :

* [Créer un centre de données](#create-datacenter)
* [Supprimer un centre de données](#delete-datacenter)
* [Obtenir les détails d’un centre de données](#get-datacenter-details)
* [Obtenir les centres de données dans un cluster](#get-datacenters-cluster)
* [Mettre à jour ou à l’échelle un centre de données](#update-datacenter)
* [Mettre à jour la configuration Cassandra](#update-yaml)

### <a name="create-a-datacenter"></a><a id="create-datacenter"></a>Créer un centre de données

Créez un centre de données à l’aide de la commande [az managed-cassandra datacenter create](/cli/azure/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_datacenter_create) :

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'
dataCenterName='dc1'
dataCenterLocation='eastus2'
delegatedSubnetId='/subscriptions/<SubscriptionID>/resourceGroups/customer-vnet-rg/providers/Microsoft.Network/virtualNetworks/customer-vnet/subnets/dc1-subnet'

az managed-cassandra datacenter create \
    --resource-group $resourceGroupName \
    --cluster-name $clusterName \
    --data-center-name $dataCenterName \
    --data-center-location $dataCenterLocation \
    --delegated-subnet-id $delegatedSubnetId \
    --node-count 3 
```

### <a name="delete-a-datacenter"></a><a id="delete-datacenter"></a>Supprimer un centre de données

Supprimez un centre de données à l’aide de la commande [az managed-cassandra datacenter delete](/cli/azure/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_datacenter_delete) :

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'
dataCenterName='dc1'

az managed-cassandra datacenter delete \
    --resource-group $resourceGroupName \
    --cluster-name $clusterName \
    --data-center-name $dataCenterName 
```

> [!WARNING]
> Si vous avez plus d’un centre de données dans votre cluster, vous devez commencer par supprimer toute référence au centre de données que vous essayez de supprimer dans tous les [paramètres de stratégie de réplication d’espace de clés](https://docs.datastax.com/en/cassandra-oss/3.x/cassandra/operations/opsChangeKSStrategy.html). Cette commande échoue s’il reste des références au centre de données dans des espaces de clés au sein de votre cluster. 

### <a name="get-datacenter-details"></a><a id="get-datacenter-details"></a>Obtenir les détails d’un centre de données

Récupérez des informations sur le centre de données à l’aide de la commande [az managed-cassandra datacenter show](/cli/azure/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_datacenter_show) :

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'
dataCenterName='dc1'

az managed-cassandra datacenter show \
    --resource-group $resourceGroupName \
    --cluster-name $clusterName \
    --data-center-name $dataCenterName 
```

### <a name="update-or-scale-a-datacenter"></a><a id="update-datacenter"></a>Mettre à jour ou à l’échelle un centre de données

Mettez à jour ou à l’échelle un centre de données (pour mettre à l’échelle, modifiez la valeur nodeCount) à l’aide de la commande [az managed-cassandra datacenter update](/cli/azure/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_datacenter_update) :

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'
dataCenterName='dc1'
dataCenterLocation='eastus'

az managed-cassandra datacenter update \
    --resource-group $resourceGroupName \
    --cluster-name $clusterName \
    --data-center-name $dataCenterName \
    --node-count 13 
```

### <a name="update-cassandra-configuration"></a><a id="update-yaml"></a>Mettre à jour la configuration Cassandra

Vous pouvez changer la configuration Cassandra dans un centre de données à l’aide de la commande [az managed-cassandra datacenter update](/cli/azure/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_datacenter_update). Vous devrez encoder en base64 le fragment YAML à l’aide d’un [outil en ligne](https://www.base64encode.org/). Voici les paramètres YAML qui sont pris en charge :

- column_index_size_in_kb
- compaction_throughput_mb_per_sec
- read_request_timeout_in_ms
- range_request_timeout_in_ms
- aggregated_request_timeout_in_ms
- write_request_timeout_in_ms
- internode_compression
- batchlog_replay_throttle_in_kb

Par exemple, le fragment YAML suivant :

```yaml
column_index_size_in_kb: 16
read_request_timeout_in_ms: 10000
```

Une fois l’encodage terminé, le fragment YAML est converti comme suit : `Y29sdW1uX2luZGV4X3NpemVfaW5fa2I6IDE2CnJlYWRfcmVxdWVzdF90aW1lb3V0X2luX21zOiAxMDAwMA==`. 

Voir ci-dessous :

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'
dataCenterName='dc1'
dataCenterLocation='eastus'
yamlFragment='Y29sdW1uX2luZGV4X3NpemVfaW5fa2I6IDE2CnJlYWRfcmVxdWVzdF90aW1lb3V0X2luX21zOiAxMDAwMA=='

az managed-cassandra datacenter update \
    --resource-group $resourceGroupName \
    --cluster-name $clusterName \
    --data-center-name $dataCenterName \
    --base64-encoded-cassandra-yaml-fragment $yamlFragment
```

### <a name="get-the-datacenters-in-a-cluster"></a><a id="get-datacenters-cluster"></a>Obtenir les centres de données dans un cluster

Récupérez les centres de données dans un cluster à l’aide de la commande [az managed-cassandra datacenter list](/cli/azure/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_datacenter_list) :

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'

az managed-cassandra datacenter list \
    --resource-group $resourceGroupName \
    --cluster-name $clusterName
```

## <a name="next-steps"></a>Étapes suivantes

* [Créer un cluster Managed Instance à partir du portail Azure](create-cluster-portal.md)
* [Déployer un cluster Apache Spark managé avec Azure Databricks](deploy-cluster-databricks.md)
