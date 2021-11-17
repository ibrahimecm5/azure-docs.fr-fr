---
title: 'Démarrage rapide : Utiliser l’interface CLI pour créer un cluster Azure Managed Instance pour Apache Cassandra'
description: Utilisez ce guide de démarrage rapide pour créer un cluster Azure Managed Instance pour Apache Cassandra avec Azure CLI.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 795ad31b83f3a6f1acdd8bcc7561e6d75556b311
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2021
ms.locfileid: "131892416"
---
# <a name="quickstart-create-an-azure-managed-instance-for-apache-cassandra-cluster-using-azure-cli"></a>Guide de démarrage rapide : Créer un cluster Azure Managed Instance pour Apache Cassandra à l’aide d’Azure CLI

Azure Managed Instance pour Apache Cassandra offre des opérations de déploiement et de mise à l’échelle automatisées pour les centres de données Apache Cassandra open source managés. Ce service vous aide à accélérer les scénarios hybrides et à réduire la maintenance en cours.

Ce guide de démarrage rapide vous montre comment utiliser les commandes Azure CLI pour créer un cluster avec Azure Managed Instance pour Apache Cassandra. Il montre également comment créer un centre de données, et comment effectuer un scale-up ou un scale-down dans le centre de données.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

* Un [réseau virtuel Azure](../virtual-network/virtual-networks-overview.md) connecté à votre environnement autohébergé ou local. Pour plus d’informations sur la connexion d’environnements locaux à Azure, consultez l’article [Connecter un réseau local à Azure](/azure/architecture/reference-architectures/hybrid-networking/).

* Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

> [!IMPORTANT]
> Pour cet article, vous avez besoin d’Azure CLI version 2.30.0 ou ultérieure. Si vous utilisez Azure Cloud Shell, sachez que la version la plus récente est déjà installée.

## <a name="create-a-managed-instance-cluster"></a><a id="create-cluster"></a>Créer un cluster Managed Instance

1. Connectez-vous au [portail Azure](https://portal.azure.com/)

1. Définissez l’ID de votre abonnement dans Azure CLI :

   ```azurecli-interactive
   az account set -s <Subscription_ID>
   ```

1. Créez ensuite un réseau virtuel avec un sous-réseau dédié dans votre groupe de ressources :

   ```azurecli-interactive
   az network vnet create -n <VNet_Name> -l eastus2 -g <Resource_Group_Name> --subnet-name <Subnet Name>
   ```

   > [!NOTE]
   > Le déploiement d’une instance Azure Managed Instance pour Apache Cassandra nécessite un accès à Internet. Le déploiement échoue dans les environnements où l’accès à Internet est limité. Vérifiez que vous ne bloquez pas l’accès dans votre réseau virtuel aux services Azure essentiels qui sont nécessaires au bon fonctionnement de Managed Instance pour Apache Cassandra :
   > - Stockage Azure
   > - Azure KeyVault
   > - Groupes de machines virtuelles identiques Azure
   > - Surveillance Azure
   > - Azure Active Directory
   > - Sécurité Azure

1. Appliquez au réseau virtuel certaines autorisations spéciales qui sont demandées par l’instance managée. Utilisez la commande `az role assignment create`, en remplaçant `<subscriptionID>`, `<resourceGroupName>` et `<vnetName>` par les valeurs appropriées :

   ```azurecli-interactive
   az role assignment create \
     --assignee a232010e-820c-4083-83bb-3ace5fc29d0b \
     --role 4d97b98b-1d4f-4787-a291-c67834d212e7 \
     --scope /subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Network/virtualNetworks/<vnetName>
   ```

   > [!NOTE]
   > Les valeurs `assignee` et `role` de la commande précédente sont des valeurs fixes. Entrez ces valeurs exactement comme indiqué dans la commande. Sinon, cela entraînera des erreurs lors de la création du cluster. Si vous rencontrez des erreurs lors de l’exécution de cette commande, vous ne disposez peut-être pas des autorisations nécessaires pour l’exécuter. Contactez votre administrateur pour obtenir les autorisations.

1. Ensuite, créez le cluster dans votre réseau virtuel nouvellement créé à l’aide de la commande [az managed-cassandra cluster create](/cli/azure/managed-cassandra/cluster?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_cluster_create). Exécutez la commande suivante comme valeur de la variable `delegatedManagementSubnetId` :

   > [!NOTE]
   > La valeur de la variable `delegatedManagementSubnetId` que vous allez fournir ci-dessous est exactement la même que la valeur de `--scope` que vous avez fournie dans la commande ci-dessus :

   ```azurecli-interactive
   resourceGroupName='<Resource_Group_Name>'
   clusterName='<Cluster_Name>'
   location='eastus2'
   delegatedManagementSubnetId='/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/<VNet name>/subnets/<subnet name>'
   initialCassandraAdminPassword='myPassword'
    
   az managed-cassandra cluster create \
     --cluster-name $clusterName \
     --resource-group $resourceGroupName \
     --location $location \
     --delegated-management-subnet-id $delegatedManagementSubnetId \
     --initial-cassandra-admin-password $initialCassandraAdminPassword \
     --debug
   ```

1. Enfin, créez un centre de connaissances pour le cluster avec trois nœuds (référence SKU de machine virtuelle Standard D8s v4, avec quatre disques P30 attachés pour chaque nœud) en utilisant la commande [az managed-cassandra datacenter create](/cli/azure/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_datacenter_create) :

   ```azurecli-interactive
   dataCenterName='dc1'
   dataCenterLocation='eastus2'
   virtualMachineSKU='Standard_D8s_v4'
   noOfDisksPerNode=4
     
   az managed-cassandra datacenter create \
     --resource-group $resourceGroupName \
     --cluster-name $clusterName \
     --data-center-name $dataCenterName \
     --data-center-location $dataCenterLocation \
     --delegated-subnet-id $delegatedManagementSubnetId \
     --node-count 3 \
     --sku $virtualMachineSKU \
     --disk-capacity $noOfDisksPerNode \
     --availability-zone false
   ```

   > [!NOTE]
   > Vous pouvez choisir la valeur de `--sku` parmi les références SKU suivantes disponibles :
   >
   > - Standard_E8s_v4
   > - Standard_E16s_v4 
   > - Standard_E20s_v4
   > - Standard_E32s_v4 
   > - Standard_DS13_v2
   > - Standard_DS14_v2
   > - Standard_D8s_v4
   > - Standard_D16s_v4
   > - Standard_D32s_v4 
   > 
   > Vous pouvez également voir que le paramètre `--availability-zone` est défini sur `false`. Pour activer les zones de disponibilité, définissez cette valeur sur `true`. 

   > [!WARNING]
   > Les zones de disponibilité ne sont pas prises en charge dans toutes les régions. Les déploiements échouent si vous sélectionnez une région où les zones de disponibilité ne sont pas prises en charge. Consultez [cette page](/azure/availability-zones/az-overview#azure-regions-with-availability-zones) pour connaître les régions prises en charge. La réussite du déploiement des zones de disponibilité dépend également de la disponibilité des ressources de calcul dans toutes les zones de la région donnée. Les déploiements peuvent échouer si la référence SKU que vous avez sélectionnée, ou capacité, n’est pas disponible dans toutes les zones. 

1. Une fois le centre de données créé, vous pouvez effectuer un scale-up ou un scale-down des nœuds qu’il contientn exécutez la commande [az managed-cassandra datacenter update](/cli/azure/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_datacenter_update). Remplacez la valeur du paramètre `node-count` par la valeur souhaitée :

   ```azurecli-interactive
   resourceGroupName='<Resource_Group_Name>'
   clusterName='<Cluster Name>'
   dataCenterName='dc1'
   dataCenterLocation='eastus2'
    
   az managed-cassandra datacenter update \
     --resource-group $resourceGroupName \
     --cluster-name $clusterName \
     --data-center-name $dataCenterName \
     --node-count 9 
   ```

## <a name="connect-to-your-cluster"></a>Se connecter au cluster

Azure Managed Instance pour Apache Cassandra ne crée pas de nœuds avec des adresses IP publiques. Pour vous connecter à votre nouveau cluster Cassandra, vous devez créer une autre ressource dans le réseau virtuel. Cette ressource peut être une application ou bien une machine virtuelle sur laquelle [CQLSH](https://cassandra.apache.org/doc/latest/cassandra/tools/cqlsh.html), l’outil de requête open source d’Apache, est installé. Vous pouvez utiliser un [modèle Resource Manager](https://azure.microsoft.com/resources/templates/vm-simple-linux/) pour déployer une machine virtuelle Ubuntu. Après avoir déployé la machine, utilisez SSH pour vous y connecter et installez CQLSH comme indiqué dans les commandes suivantes :

```bash
# Install default-jre and default-jdk
sudo apt update
sudo apt install openjdk-8-jdk openjdk-8-jre

# Install the Cassandra libraries in order to get CQLSH:
echo "deb http://www.apache.org/dist/cassandra/debian 311x main" | sudo tee -a /etc/apt/sources.list.d/cassandra.sources.list
curl https://downloads.apache.org/cassandra/KEYS | sudo apt-key add -
sudo apt-get update
sudo apt-get install cassandra

# Export the SSL variables:
export SSL_VERSION=TLSv1_2
export SSL_VALIDATE=false

# Connect to CQLSH (replace <IP> with the private IP addresses of the nodes in your Datacenter):
host=("<IP>" "<IP>" "<IP>")
cqlsh $host 9042 -u cassandra -p cassandra --ssl
```

## <a name="troubleshooting"></a>Dépannage

Si vous rencontrez une erreur lors de l’application des autorisations à votre réseau virtuel avec Azure CLI, comme *Utilisateur ou principal de service introuvable dans la base de données de graphe pour 'e5007d2c-4b13-4a74-9b6a-605d99f03501'* , vous pouvez appliquer la même autorisation manuellement à partir du portail Azure. Découvrez comment procéder [ici](add-service-principal.md).

> [!NOTE] 
> L’attribution de rôle Azure Cosmos DB est utilisée à des fins de déploiement uniquement. Azure Managed Instance pour Apache Cassandra n’a aucune dépendance back-end sur Azure Cosmos DB.  

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous n’avez plus besoin du groupe de ressources, de l’instance managée ni d’aucune des ressources associées, vous pouvez les supprimer avec la commande `az group delete` :

```azurecli-interactive
az group delete --name <Resource_Group_Name>
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez appris à créer un cluster Azure Managed Instance pour Apache Cassandra avec Azure CLI. Vous pouvez maintenant commencer à utiliser le cluster :

> [!div class="nextstepaction"]
> [Déployer un cluster Apache Spark managé avec Azure Databricks](deploy-cluster-databricks.md)
