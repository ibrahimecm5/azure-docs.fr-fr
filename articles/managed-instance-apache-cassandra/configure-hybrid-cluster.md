---
title: 'Démarrage rapide : Configurer un cluster hybride avec Azure Managed Instance pour Apache Cassandra'
description: Ce guide de démarrage rapide vous montre comment configurer un cluster hybride avec Azure Managed Instance pour Apache Cassandra.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 31b6f0459f2fb9b04646667abfda039760e5fcc0
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2021
ms.locfileid: "131892435"
---
# <a name="quickstart-configure-a-hybrid-cluster-with-azure-managed-instance-for-apache-cassandra"></a>Démarrage rapide : Configurer un cluster hybride avec Azure Managed Instance pour Apache Cassandra

Azure Managed Instance pour Apache Cassandra offre des opérations de déploiement et de mise à l’échelle automatisées pour les centres de données Apache Cassandra open source managés. Ce service vous aide à accélérer les scénarios hybrides et à réduire la maintenance en cours.

Ce guide de démarrage rapide montre comment utiliser les commandes Azure CLI pour configurer un cluster hybride. Si vous avez déjà des centres de données dans un environnement local ou autohébergé, vous pouvez ajouter d’autres centres de données à ce cluster et gérer tous les clusters avec Azure Managed Instance pour Apache Cassandra.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

* Pour cet article, vous avez besoin d’Azure CLI version 2.30.0 ou ultérieure. Si vous utilisez Azure Cloud Shell, sachez que la version la plus récente est déjà installée.

* Un [réseau virtuel Azure](../virtual-network/virtual-networks-overview.md) connecté à votre environnement autohébergé ou local. Pour plus d’informations sur la connexion d’environnements locaux à Azure, consultez l’article [Connecter un réseau local à Azure](/azure/architecture/reference-architectures/hybrid-networking/).

## <a name="configure-a-hybrid-cluster"></a><a id="create-account"></a>Configurer un cluster hybride

1. Connectez-vous au [portail Azure](https://portal.azure.com/) et accédez à votre ressource de réseau virtuel.

1. Ouvrez l’onglet **Sous-réseaux** et créez un sous-réseau. Pour en savoir plus sur les champs du formulaire **Ajouter un sous-réseau**, consultez l’article [Réseau virtuel](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet) :

   :::image type="content" source="./media/configure-hybrid-cluster/subnet.png" alt-text="Ajouter un sous-réseau à votre réseau virtuel." lightbox="./media/configure-hybrid-cluster/subnet.png" border="true":::
    <!-- ![image](./media/configure-hybrid-cluster/subnet.png) -->

   > [!NOTE]
   > Le déploiement d’une instance Azure Managed Instance pour Apache Cassandra nécessite un accès à Internet. Le déploiement échoue dans les environnements où l’accès à Internet est limité. Vérifiez que vous ne bloquez pas l’accès dans votre réseau virtuel aux services Azure suivants essentiels et nécessaires au bon fonctionnement de Managed Cassandra. Vous pouvez également trouver une liste complète des dépendances de port et d’adresse IP[ici](network-rules.md). 
   > - Stockage Azure
   > - Azure KeyVault
   > - Groupes de machines virtuelles identiques Azure
   > - Surveillance Azure
   > - Azure Active Directory
   > - Sécurité Azure

1. À présent, nous allons utiliser Azure CLI pour appliquer certaines autorisations spéciales, qui sont requises par Cassandra Managed Instance, au réseau virtuel et au sous-réseau. Utilisez la commande `az role assignment create`, en remplaçant `<subscriptionID>`, `<resourceGroupName>` et `<vnetName>` par les valeurs appropriées :

   ```azurecli-interactive
   az role assignment create \
     --assignee a232010e-820c-4083-83bb-3ace5fc29d0b \
     --role 4d97b98b-1d4f-4787-a291-c67834d212e7 \
     --scope /subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Network/virtualNetworks/<vnetName>
   ```

   > [!NOTE]
   > Les valeurs `assignee` et `role` dans la commande précédente correspondent aux identificateurs fixes du principal de service et du rôle, respectivement.

1. Nous allons ensuite configurer des ressources pour le cluster hybride. Dans la mesure où vous avez déjà un cluster, le nom du cluster est ici simplement une ressource logique permettant d’identifier le nom de votre cluster existant. Veillez à spécifier le nom du cluster existant quand vous définissez les variables `clusterName` et `clusterNameOverride` dans le script suivant. 
 
   Vous avez également besoin, au minimum, des nœuds seed de votre centre de données existant et des certificats gossip requis pour le chiffrement de nœud à nœud. Azure Managed Instance pour Apache Cassandra nécessite le chiffrement de nœud à nœud pour la communication entre les centres de données. Si vous n’avez pas de chiffrement de nœud à nœud implémenté dans votre cluster existant, vous devez l’implémenter. Consultez la documentation [ici](https://docs.datastax.com/en/cassandra-oss/3.x/cassandra/configuration/secureSSLNodeToNode.html). Vous devez fournir le chemin d’accès à l’emplacement des certificats. Chaque certificat doit être au format PEM, par exemple `-----BEGIN CERTIFICATE-----\n...PEM format 1...\n-----END CERTIFICATE-----`. En général, il existe deux façons d’implémenter des certificats :

   1. Certificats auto-signés. Cela signifie qu’il s’agit d’un certificat privé et public (sans autorité de certification) pour chaque nœud. Dans ce cas, nous avons besoin de tous les certificats publics.

   1. Certificats signés par une autorité de certification. Il peut s’agir d’une autorité de certification auto-signée ou même d’une autorité de certification publique. Dans ce cas, nous avons besoin du certificat d’autorité de certification racine (reportez-vous aux instructions sur la [préparation des certificats SSL pour la production](https://docs.datastax.com/en/cassandra-oss/3.x/cassandra/configuration/secureSSLCertWithCA.html)) et à tous les intermédiaires (le cas échéant).

   Éventuellement, si vous avez également implémenté des certificats client à nœud (voir [ici](https://docs.datastax.com/en/cassandra-oss/3.x/cassandra/configuration/secureSSLClientToNode.html)), vous devez également les fournir dans le même format lors de la création du cluster hybride. Voir l’exemple ci-dessous.

   > [!NOTE]
   > La valeur de la variable `delegatedManagementSubnetId` fournie ci-dessous est exactement la même que la valeur de `--scope` que vous avez fournie dans la commande ci-dessus :

   ```azurecli-interactive
   resourceGroupName='MyResourceGroup'
   clusterName='cassandra-hybrid-cluster-legal-name'
   clusterNameOverride='cassandra-hybrid-cluster-illegal-name'
   location='eastus2'
   delegatedManagementSubnetId='/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Network/virtualNetworks/<vnetName>/subnets/<subnetName>'
    
   # You can override the cluster name if the original name is not legal for an Azure resource:
   # overrideClusterName='ClusterNameIllegalForAzureResource'
   # the default cassandra version will be v3.11
    
   az managed-cassandra cluster create \
     --cluster-name $clusterName \
     --resource-group $resourceGroupName \
     --location $location \
     --delegated-management-subnet-id $delegatedManagementSubnetId \
     --external-seed-nodes 10.52.221.2 10.52.221.3 10.52.221.4 \
     --external-gossip-certificates /usr/csuser/clouddrive/rootCa.pem /usr/csuser/clouddrive/gossipKeyStore.crt_signed
     # optional - add your existing datacenter's client-to-node certificates (if implemented):
     # --client-certificates /usr/csuser/clouddrive/rootCa.pem /usr/csuser/clouddrive/nodeKeyStore.crt_signed 
   ```

   > [!NOTE]
   > Si votre cluster a déjà un chiffrement de nœud à nœud et de client à nœud, vous devez savoir où vos certificats SSL client et/ou gossip existants sont conservés. Si ce n’est pas le cas, essayez d’exécuter `keytool -list -keystore <keystore-path> -rfc -storepass <password>` pour lister les certificats. 

1. Une fois la ressource cluster créée, exécutez la commande suivante pour afficher les détails de l’installation du cluster :

   ```azurecli-interactive
   resourceGroupName='MyResourceGroup'
   clusterName='cassandra-hybrid-cluster'
    
   az managed-cassandra cluster show \
      --cluster-name $clusterName \
      --resource-group $resourceGroupName \
   ```

1. La commande ci-dessus retourne des informations sur l’environnement Managed Instance. Vous avez besoin de certificats gossip pour pouvoir installer ces derniers sur le magasin de confiance des nœuds de votre centre de données existant. La capture d’écran suivante montre la sortie de la commande précédente et le format des certificats :

   :::image type="content" source="./media/configure-hybrid-cluster/show-cluster.png" alt-text="Obtenir les détails des certificats à partir du cluster." lightbox="./media/configure-hybrid-cluster/show-cluster.png" border="true":::
    <!-- ![image](./media/configure-hybrid-cluster/show-cluster.png) -->

   > [!NOTE]
   > Les certificats renvoyés à partir de la commande ci-dessus contiennent des sauts de ligne représentés sous forme de texte, par exemple `\r\n`. Vous devez copier chaque certificat dans un fichier et le mettre en forme avant de tenter de l’importer dans le magasin de confiance de votre centre de données existant.

   > [!TIP]
   > Copiez la valeur de tableau `gossipCertificates` indiquée dans la capture d’écran ci-dessus dans un fichier, puis utilisez le script bash suivant (vous devez [télécharger et installer jq](https://stedolan.github.io/jq/download/) pour votre plateforme) pour formater les certificats et créer des fichiers PEM distincts pour chacun.
   >
   > ```bash
   > readarray -t cert_array < <(jq -c '.[]' gossipCertificates.txt)
   > # iterate through the certs array, format each cert, write to a numbered file.
   > num=0
   > filename=""
   > for item in "${cert_array[@]}"; do
   >   let num=num+1
   >   filename="cert$num.pem"
   >   cert=$(jq '.pem' <<< $item)
   >   echo -e $cert >> $filename
   >   sed -e '1d' -e '$d' -i $filename
   > done
   > ```

1. Créez maintenant un centre de données dans le cluster hybride. Veillez à remplacer les valeurs des variables par les détails de votre cluster :

   ```azurecli-interactive
   resourceGroupName='MyResourceGroup'
   clusterName='cassandra-hybrid-cluster'
   dataCenterName='dc1'
   dataCenterLocation='eastus2'
    
   az managed-cassandra datacenter create \
     --resource-group $resourceGroupName \
     --cluster-name $clusterName \
     --data-center-name $dataCenterName \
     --data-center-location $dataCenterLocation \
     --delegated-subnet-id $delegatedManagementSubnetId \
     --node-count 9 
   ```

1. Une fois le centre de données créé, exécutez la commande datacenter show pour voir les détails du centre de données :

   ```azurecli-interactive
   resourceGroupName='MyResourceGroup'
   clusterName='cassandra-hybrid-cluster'
   dataCenterName='dc1'
    
   az managed-cassandra datacenter show \
     --resource-group $resourceGroupName \
     --cluster-name $clusterName \
     --data-center-name $dataCenterName 
   ```

1. La commande précédente retourne les nœuds seed du nouveau centre de données : 

   :::image type="content" source="./media/configure-hybrid-cluster/show-datacenter.png" alt-text="Obtenir les détails du centre de données." lightbox="./media/configure-hybrid-cluster/show-datacenter.png" border="true":::
    <!-- ![image](./media/configure-hybrid-cluster/show-datacenter.png) -->

1. Ajoutez maintenant les nouveaux nœuds seed du centre de données à la [configuration des nœuds seed](https://docs.datastax.com/en/cassandra-oss/3.0/cassandra/configuration/configCassandra_yaml.html#configCassandra_yaml__seed_provider) de votre centre de données existant dans le fichier [cassandra.yaml](https://docs.datastax.com/en/cassandra-oss/3.0/cassandra/configuration/configCassandra_yaml.html). Et installez les certificats gossip des instances gérées que vous avez collectés précédemment dans le magasin de confiance pour chaque nœud de votre cluster existant, à l’aide de la commande `keytool` pour chaque certificat :

   ```bash
   keytool -importcert -keystore generic-server-truststore.jks -alias CassandraMI -file cert1.pem -noprompt -keypass myPass -storepass truststorePass
   ```

   > [!NOTE]
   > Si vous souhaitez ajouter d’autres centres de données, répétez les étapes ci-dessus, mais uniquement pour les nœuds seed. 

1. Pour finir, exécutez la requête CQL suivante pour mettre à jour la stratégie de réplication dans chaque espace de clés et inclure ainsi tous les centres de données dans le cluster :

   ```bash
   ALTER KEYSPACE "ks" WITH REPLICATION = {'class': 'NetworkTopologyStrategy', 'on-premise-dc': 3, 'managed-instance-dc': 3};
   ```

   Vous devez également mettre à jour les tables de mots de passe :

   ```bash
   ALTER KEYSPACE "system_auth" WITH REPLICATION = {'class': 'NetworkTopologyStrategy', 'on-premise-dc': 3, 'managed-instance-dc': 3}
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

Dans ce guide de démarrage rapide, vous avez appris à créer un cluster hybride en utilisant Azure CLI et Azure Managed Instance pour Apache Cassandra. Vous pouvez maintenant commencer à utiliser le cluster.

> [!div class="nextstepaction"]
> [Gérer les ressources Azure Managed Instance pour Apache Cassandra à l’aide d’Azure CLI](manage-resources-cli.md)
