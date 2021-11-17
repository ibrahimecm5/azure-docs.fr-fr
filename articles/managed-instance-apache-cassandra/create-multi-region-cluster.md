---
title: 'Démarrage rapide : Configurer un cluster multirégion avec Azure Managed Instance pour Apache Cassandra'
description: Ce guide de démarrage rapide vous montre comment configurer un cluster multirégion avec Azure Managed Instance pour Apache Cassandra.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: fe2ab4a780e07f8d8325c2881d562a379ec16b97
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2021
ms.locfileid: "131894390"
---
# <a name="quickstart-create-a-multi-region-cluster-with-azure-managed-instance-for-apache-cassandra"></a>Démarrage rapide : Créer un cluster multirégion avec Azure Managed Instance pour Apache Cassandra

Azure Managed Instance pour Apache Cassandra offre des opérations de déploiement et de mise à l’échelle automatisées pour les centres de données Apache Cassandra open source managés. Ce service vous aide à accélérer les scénarios hybrides et à réduire la maintenance en cours.

Ce guide de démarrage rapide montre comment utiliser les commandes Azure CLI pour configurer un cluster multirégion dans Azure.  

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

* Pour cet article, vous avez besoin d’Azure CLI version 2.30.0 ou ultérieure. Si vous utilisez Azure Cloud Shell, sachez que la version la plus récente est déjà installée.

* Un [réseau virtuel Azure](../virtual-network/virtual-networks-overview.md) connecté à votre environnement autohébergé ou local. Pour plus d’informations sur la connexion d’environnements locaux à Azure, consultez l’article [Connecter un réseau local à Azure](/azure/architecture/reference-architectures/hybrid-networking/).

## <a name="setting-up-the-network-environment"></a><a id="create-account"></a>Configurer l’environnement réseau

Étant donné que tous les centres de données provisionnés avec ce service doivent être déployés sur des sous-réseaux dédiés par injection dans le réseau virtuel, il est nécessaire de configurer le peering réseau appropriée avant le déploiement afin de garantir le bon fonctionnement de votre cluster multirégion. Nous allons créer un cluster avec deux centres de données dans des régions distinctes : USA Est et USA Est 2. Tout d’abord, nous avons besoin de créer les réseaux virtuels pour chaque région. 

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

1. Créez un groupe de ressources nommé « cassandra-mi-multi-region ».

   ```azurecli-interactive
   az group create -l eastus2 -n cassandra-mi-multi-region
   ```

1. Créez le premier réseau virtuel dans USA Est 2 avec un sous-réseau dédié :

   ```azurecli-interactive
   az network vnet create \
     -n vnetEastUs2 \
     -l eastus2 \
     -g cassandra-mi-multi-region \
     --address-prefix 10.0.0.0/16 \
     --subnet-name dedicated-subnet
   ```

1. Créez maintenant le deuxième réseau virtuel dans USA Est, également avec un sous-réseau dédié :

   ```azurecli-interactive
    az network vnet create \
      -n vnetEastUs \
      -l eastus \
      -g cassandra-mi-multi-region \
      --address-prefix 192.168.0.0/16 \
      --subnet-name dedicated-subnet
   ```

   > [!NOTE]
   > Nous ajoutons explicitement différentes plages d’adresses IP pour éviter toute erreur lors du peering. 

1. Nous avons maintenant besoin d’appairer le premier réseau virtuel avec le deuxième réseau virtuel :

   ```azurecli-interactive
   az network vnet peering create \
     -g cassandra-mi-multi-region \
     -n MyVnet1ToMyVnet2 \
     --vnet-name vnetEastUs2 \
     --remote-vnet vnetEastUs \
     --allow-vnet-access \
     --allow-forwarded-traffic
   ```

1. Pour connecter les deux réseaux virtuels, créez un autre peering entre le deuxième réseau virtuel et le premier :

   ```azurecli-interactive
   az network vnet peering create \
     -g cassandra-mi-multi-region \
     -n MyVnet2ToMyVnet1 \
     --vnet-name vnetEastUs \
     --remote-vnet vnetEastUs2 \
     --allow-vnet-access \
     --allow-forwarded-traffic  
   ```

   > [!NOTE]
   > Si vous ajoutez d’autres régions, chaque réseau virtuel nécessite un appairage depuis lui-même vers tous les autres réseaux virtuels et depuis tous les autres réseaux virtuels vers lui. 

1. Examinez la sortie de la commande précédente pour vérifier que la valeur de « peeringState » est maintenant « Connected ». Vous pouvez aussi le vérifier en exécutant la commande suivante :

   ```azurecli-interactive
   az network vnet peering show \
     --name MyVnet1ToMyVnet2 \
     --resource-group cassandra-mi-multi-region \
     --vnet-name vnetEastUs2 \
     --query peeringState
   ``` 

1. Ensuite, appliquez des autorisations spéciales aux deux réseaux virtuels, exigés par Azure Managed Instance pour Apache Cassandra. Exécutez la commande suivante en remplaçant `<SubscriptionID>` par votre ID d’abonnement :

   ```azurecli-interactive
   az role assignment create \
     --assignee a232010e-820c-4083-83bb-3ace5fc29d0b \
     --role 4d97b98b-1d4f-4787-a291-c67834d212e7 \
     --scope /subscriptions/<SubscriptionID>/resourceGroups/cassandra-mi-multi-region/providers/Microsoft.Network/virtualNetworks/vnetEastUs2

   az role assignment create     \
     --assignee a232010e-820c-4083-83bb-3ace5fc29d0b \
     --role 4d97b98b-1d4f-4787-a291-c67834d212e7 \
     --scope /subscriptions/<SubscriptionID>/resourceGroups/cassandra-mi-multi-region/providers/Microsoft.Network/virtualNetworks/vnetEastUs
    ```
   > [!NOTE]
   > Les valeurs `assignee` et `role` de la commande précédente sont des valeurs fixes. Entrez ces valeurs exactement comme indiqué dans la commande. Sinon, cela entraînera des erreurs lors de la création du cluster. Si vous rencontrez des erreurs lors de l’exécution de cette commande, vous ne disposez peut-être pas des autorisations nécessaires pour l’exécuter. Contactez votre administrateur pour obtenir les autorisations.

## <a name="create-a-multi-region-cluster"></a><a id="create-account"></a>Créer un cluster multirégion

1. Maintenant que nous avons les réseaux appropriés en place, nous sommes prêts à déployer la ressource de cluster (remplacez `<Subscription ID>` par votre ID d’abonnement). L’installation peut prendre entre 5 et 10 minutes :

   ```azurecli-interactive
   resourceGroupName='cassandra-mi-multi-region'
   clusterName='test-multi-region'
   location='eastus2'
   delegatedManagementSubnetId='/subscriptions/<SubscriptionID>/resourceGroups/cassandra-mi-multi-region/providers/Microsoft.Network/virtualNetworks/vnetEastUs2/subnets/dedicated-subnet'
   initialCassandraAdminPassword='myPassword'
        
    az managed-cassandra cluster create \
      --cluster-name $clusterName \
      --resource-group $resourceGroupName \
      --location $location \
      --delegated-management-subnet-id $delegatedManagementSubnetId \
      --initial-cassandra-admin-password $initialCassandraAdminPassword \
      --debug
   ```

1. Quand la ressource de cluster est créée, vous êtes prêt à créer un centre de données. Commencez par créer un centre de données dans la région USA Est 2 (remplacez `<SubscriptionID>` par votre ID d’abonnement). Cette opération peut nécessiter jusqu’à 10 minutes :

   ```azurecli-interactive
   resourceGroupName='cassandra-mi-multi-region'
   clusterName='test-multi-region'
   dataCenterName='dc-eastus2'
   dataCenterLocation='eastus2'
   delegatedManagementSubnetId='/subscriptions/<SubscriptionID>/resourceGroups/cassandra-mi-multi-region/providers/Microsoft.Network/virtualNetworks/vnetEastUs2/subnets/dedicated-subnet'
        
    az managed-cassandra datacenter create \
       --resource-group $resourceGroupName \
       --cluster-name $clusterName \
       --data-center-name $dataCenterName \
       --data-center-location $dataCenterLocation \
       --delegated-subnet-id $delegatedManagementSubnetId \
       --node-count 3
   ```

1. Ensuite, créez un centre de données dans la région USA Est (remplacez `<SubscriptionID>` par votre ID d’abonnement) :

   ```azurecli-interactive
   resourceGroupName='cassandra-mi-multi-region'
   clusterName='test-multi-region'
   dataCenterName='dc-eastus'
   dataCenterLocation='eastus'
   delegatedManagementSubnetId='/subscriptions/<SubscriptionID>/resourceGroups/cassandra-mi-multi-region/providers/Microsoft.Network/virtualNetworks/vnetEastUs/subnets/dedicated-subnet'
        
    az managed-cassandra datacenter create \
      --resource-group $resourceGroupName \
      --cluster-name $clusterName \
      --data-center-name $dataCenterName \
      --data-center-location $dataCenterLocation \
      --delegated-subnet-id $delegatedManagementSubnetId \
      --node-count 3 
   ```

1. Une fois le deuxième centre de données créé, obtenez l’état du nœud pour vérifier que tous les nœuds Cassandra sont correctement apparus :

    ```azurecli-interactive
    resourceGroupName='cassandra-mi-multi-region'
    clusterName='test-multi-region'
    
    az managed-cassandra cluster node-status \
       --cluster-name $clusterName \
       --resource-group $resourceGroupName
    ```

1. Pour finir, [connectez-vous à votre cluster](create-cluster-cli.md#connect-to-your-cluster) en utilisant CQLSH, puis exécutez la requête CQL suivante pour mettre à jour la stratégie de réplication dans chaque espace de clés et inclure ainsi tous les centres de données dans le cluster :

   ```bash
   ALTER KEYSPACE "ks" WITH REPLICATION = {'class': 'NetworkTopologyStrategy', 'dc-eastus2': 3, 'dc-eastus': 3};
   ```
   Vous devez également mettre à jour les tables de mots de passe :

   ```bash
    ALTER KEYSPACE "system_auth" WITH REPLICATION = {'class': 'NetworkTopologyStrategy', 'dc-eastus2': 3, 'dc-eastus': 3}
   ```

## <a name="troubleshooting"></a>Dépannage

Si vous rencontrez une erreur lors de l’application des autorisations à votre réseau virtuel avec Azure CLI, comme *Utilisateur ou principal de service introuvable dans la base de données de graphe pour 'e5007d2c-4b13-4a74-9b6a-605d99f03501'* , vous pouvez appliquer la même autorisation manuellement à partir du portail Azure. Découvrez comment procéder [ici](add-service-principal.md).

> [!NOTE] 
> L’attribution de rôle Azure Cosmos DB est utilisée à des fins de déploiement uniquement. Azure Managed Instance pour Apache Cassandra n’a aucune dépendance back-end sur Azure Cosmos DB.  

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous ne comptez pas continuer à utiliser ce cluster Managed Instance, supprimez-le en effectuant les étapes suivantes :

1. Dans le menu de gauche du portail Azure, sélectionnez **Groupes de ressources**.
1. Dans la liste, sélectionnez le groupe de ressources créé pour ce guide de démarrage rapide.
1. Dans le volet **Vue d’ensemble** du groupe de ressources, sélectionnez **Supprimer un groupe de ressources**.
1. Dans la fenêtre suivante, entrez le nom du groupe de ressources à supprimer, puis sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez appris à créer un cluster multirégion en utilisant Azure CLI et Azure Managed Instance pour Apache Cassandra. Vous pouvez maintenant commencer à utiliser le cluster.

> [!div class="nextstepaction"]
> [Gérer les ressources Azure Managed Instance pour Apache Cassandra à l’aide d’Azure CLI](manage-resources-cli.md)
