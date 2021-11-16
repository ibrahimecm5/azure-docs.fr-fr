---
title: 'Démarrage rapide : créer un cluster Azure Managed Instance pour Apache Cassandra à partir du portail Azure'
description: Ce démarrage rapide vous montre comment créer un cluster Azure Managed Instance pour Apache Cassandra à l’aide du portail Azure.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 8c266f1fc338ca35b05730d9f737a836bdf8949d
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2021
ms.locfileid: "131893136"
---
# <a name="quickstart-create-an-azure-managed-instance-for-apache-cassandra-cluster-from-the-azure-portal"></a>Démarrage rapide : créer un cluster Azure Managed Instance pour Apache Cassandra à partir du portail Azure

Azure Managed Instance pour Apache Cassandra offre des opérations de déploiement et de mise à l’échelle automatisées pour les centres de données Apache Cassandra open source managés, ce qui permet d’accélérer les scénarios hybrides et de réduire la maintenance continue.

Ce démarrage rapide montre comment utiliser le portail Azure pour créer un cluster Azure Managed Instance pour Apache Cassandra.

## <a name="prerequisites"></a>Prérequis

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="create-a-managed-instance-cluster"></a><a id="create-account"></a>Créer un cluster Manage Instance

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

1. Dans la barre de recherche, recherchez **Managed Instance pour Apache Cassandra** et sélectionnez le résultat.

   :::image type="content" source="./media/create-cluster-portal/search-portal.png" alt-text="Recherchez Managed Instance pour Apache Cassandra." lightbox="./media/create-cluster-portal/search-portal.png" border="true":::

1. Sélectionnez le bouton **Créer un cluster Managed Instance pour Apache Cassandra**.

   :::image type="content" source="./media/create-cluster-portal/create-cluster.png" alt-text="Créez le cluster." lightbox="./media/create-cluster-portal/create-cluster.png" border="true":::

1. Dans le volet **Créer Managed Instance pour Apache Cassandra**, entrez les informations suivantes :

   * **Abonnement** : sélectionnez votre abonnement Azure dans la liste déroulante.
   * **Groupe de ressources** : indiquez si vous souhaitez créer un groupe de ressources ou utiliser un groupe existant. Un groupe de ressources est un conteneur réunissant les ressources associées d’une solution Azure. Pour plus d’informations, consultez l’article de présentation [Groupes de ressources Azure](../azure-resource-manager/management/overview.md).
   * **Nom de cluster** : entrez un nom pour votre cluster.
   * **Emplacement** : emplacement où votre cluster sera déployé.
   * **Mot de passe d’administrateur Cassandra initial** : mot de passe utilisé pour créer le cluster.
   * **Confirmer le mot de passe d’administrateur Cassandra** : entrez à nouveau votre mot de passe.
   * **Réseau virtuel** : sélectionnez un réseau virtuel existant, ou créez-en un, et un sous-réseau. 
   * **Affecter des rôles** : les réseaux virtuels nécessitent des autorisations spéciales pour permettre le déploiement de clusters Cassandra managés. Laissez cette case cochée si vous créez un réseau virtuel ou si vous utilisez un réseau virtuel existant sans autorisations appliquées. Si vous utilisez un réseau virtuel sur lequel vous avez déjà déployé des clusters Cassandra Managed Instance, décochez cette option.

   :::image type="content" source="./media/create-cluster-portal/create-cluster-page.png" alt-text="Remplissez le formulaire de création du cluster." lightbox="./media/create-cluster-portal/create-cluster-page.png" border="true":::

   > [!NOTE]
   > Le déploiement d’une instance Azure Managed Instance pour Apache Cassandra nécessite un accès à Internet. Le déploiement échoue dans les environnements où l’accès à Internet est limité. Vérifiez que vous ne bloquez pas l’accès dans votre réseau virtuel aux services Azure suivants essentiels et nécessaires au bon fonctionnement de Managed Cassandra. Pour plus d’informations, consultez [Règles de trafic réseau sortant requises](network-rules.md).
   > - Stockage Azure
   > - Azure KeyVault
   > - Groupes de machines virtuelles identiques Azure
   > - Surveillance Azure
   > - Azure Active Directory
   > - Sécurité Azure

1. Sélectionnez ensuite l’onglet **Centre de données**.

1. Entrez les informations suivantes :

   * **Nom du centre de données** : sélectionnez votre abonnement Azure dans la liste déroulante.
   * **Zone de disponibilité** : cochez cette case si vous souhaitez activer les zones de disponibilité.
   * **Taille de la référence (SKU)**  : choisissez une taills de référence SKU de machine virtuelle parmi celles disponibles.
   * **Nombre de disques** : choisissez le nombre de disques P30 à attacher à chaque nœud Cassandra.
   * **Nombre de nœuds** : choisissez le nombre de nœuds Cassandra à déployer sur ce centre de centres.

   :::image type="content" source="./media/create-cluster-portal/create-datacenter-page.png" alt-text="Passer en revue le récapitulatif pour créer le centre de données." lightbox="./media/create-cluster-portal/create-datacenter-page.png" border="true":::

   > [!WARNING]
   > Les zones de disponibilité ne sont pas prises en charge dans toutes les régions. Les déploiements échouent si vous sélectionnez une région où les zones de disponibilité ne sont pas prises en charge. Consultez [cette page](/azure/availability-zones/az-overview#azure-regions-with-availability-zones) pour connaître les régions prises en charge. La réussite du déploiement des zones de disponibilité dépend également de la disponibilité des ressources de calcul dans toutes les zones de la région donnée. Les déploiements peuvent échouer si la référence SKU que vous avez sélectionnée, ou capacité, n’est pas disponible dans toutes les zones. 

1. Ensuite, cliquez sur **Vérifier + créer** > **Créer**.

   > [!NOTE]
   > La création du cluster peut prendre jusqu’à 15 minutes.

   :::image type="content" source="./media/create-cluster-portal/review-create.png" alt-text="Passez en revue le résumé pour créer le cluster." lightbox="./media/create-cluster-portal/review-create.png" border="true":::

1. Une fois le déploiement terminé, vérifiez votre groupe de ressources pour voir le cluster Manage Instance nouvellement créé :

   :::image type="content" source="./media/create-cluster-portal/managed-instance.png" alt-text="Page de vue d’ensemble après la création du cluster." lightbox="./media/create-cluster-portal/managed-instance.png" border="true":::

1. Pour parcourir les nœuds de cluster, accédez à la ressource de cluster et ouvrez le volet **Centre de données** :

   :::image type="content" source="./media/create-cluster-portal/datacenter-1.png" alt-text="Afficher les nœuds du centre de centres." lightbox="./media/create-cluster-portal/datacenter-1.png" border="true":::

<!-- ## <a id="create-account"></a>Add a datacenter

1. To add another datacenter, click the add button in the **Data Center** pane:

   :::image type="content" source="./media/create-cluster-portal/add-datacenter.png" alt-text="Click on add datacenter." lightbox="./media/create-cluster-portal/add-datacenter.png" border="true":::

   > [!WARNING]
   > If you are adding a datacenter in a different region, you will need to select a different virtual network. You will also need to ensure that this virtual network has connectivity to the primary region's virtual network created above (and any other virtual networks that are hosting datacenters within the managed instance cluster). Take a look at [this article](/azure/virtual-network/tutorial-connect-virtual-networks-portal#peer-virtual-networks) to learn how to peer virtual networks using Azure portal. You also need to make sure you have applied the appropriate role to your virtual network before attempting to deploy a managed instance cluster, using the below CLI command.
   >
   > ```azurecli-interactive  
   >     az role assignment create \
   >     --assignee a232010e-820c-4083-83bb-3ace5fc29d0b \
   >     --role 4d97b98b-1d4f-4787-a291-c67834d212e7 \
   >     --scope /subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Network/virtualNetworks/<vnetName>
   > ```

1. Fill in the appropriate fields:

   * **Datacenter name** - From the drop-down, select your Azure subscription.
   * **Availability zone** - Check this box if you want availability zones to be enabled in this datacenter.
   * **Location** - Location where your datacenter will be deployed to.
   * **SKU Size** - Choose from the available Virtual Machine SKU sizes.
   * **No. of disks** - Choose the number of p30 disks to be attached to each Cassandra node.
   * **SKU Size** - Choose the number of Cassandra nodes that will be deployed to this datacenter.
   * **Virtual Network** - Select an Exiting Virtual Network and Subnet.  

   :::image type="content" source="./media/create-cluster-portal/add-datacenter-2.png" alt-text="Add Datacenter." lightbox="./media/create-cluster-portal/add-datacenter-2.png" border="true":::

   > [!WARNING]
   > Notice that we do not allow creation of a new virtual network when adding a datacenter. You need to choose an existing virtual network, and as mentioned above, you need to ensure there is connectivity between the target subnets where datacenters will be deployed. You also need to apply the appropriate role to the VNet to allow deployment (see above).

1. When the datacenter is deployed, you should be able to view all datacenter information in the **Data Center** pane:

   :::image type="content" source="./media/create-cluster-portal/multi-datacenter.png" alt-text="View the cluster resources." lightbox="./media/create-cluster-portal/multi-datacenter.png" border="true":::

## Troubleshooting

If you encounter an error when applying permissions to your Virtual Network using Azure CLI, such as *Cannot find user or service principal in graph database for 'e5007d2c-4b13-4a74-9b6a-605d99f03501'*, you can apply the same permission manually from the Azure portal. Learn how to do this [here](add-service-principal.md).

> [!NOTE] 
> The Azure Cosmos DB role assignment is used for deployment purposes only. Azure Managed Instanced for Apache Cassandra has no backend dependencies on Azure Cosmos DB.   -->

## <a name="connecting-to-your-cluster"></a>Connexion à votre cluster

Azure Managed Instance pour Apache Cassandra ne crée pas de nœuds avec des adresses IP publiques. Donc, pour vous connecter à votre cluster Cassandra nouvellement créé, vous devez créer une autre ressource dans le réseau virtuel. Il peut s’agir d’une application ou d’une machine virtuelle sur laquelle [CQLSH](https://cassandra.apache.org/doc/latest/cassandra/tools/cqlsh.html), l’outil de requête open source d’Apache, est installé. Vous pouvez utiliser un [modèle](https://azure.microsoft.com/resources/templates/vm-simple-linux/) pour déployer une machine virtuelle Ubuntu. Une fois la machine déployée, utilisez SSH pour vous y connecter et installez CQLSH à l’aide des commandes ci-dessous :

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
initial_admin_password="Password provided when creating the cluster"
cqlsh $host 9042 -u cassandra -p $initial_admin_password --ssl
```


## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous ne comptez pas continuer à utiliser ce cluster Managed Instance, supprimez-le en effectuant les étapes suivantes :

1. Dans le menu de gauche du portail Azure, sélectionnez **Groupes de ressources**.
1. Dans la liste, sélectionnez le groupe de ressources créé pour ce guide de démarrage rapide.
1. Dans le volet **Vue d’ensemble** du groupe de ressources, sélectionnez **Supprimer un groupe de ressources**.
1. Dans la fenêtre suivante, entrez le nom du groupe de ressources à supprimer, puis sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez appris à créer un cluster Azure Managed Instance pour Apache Cassandra à l’aide du portail Azure. Vous pouvez maintenant commencer à utiliser le cluster :

> [!div class="nextstepaction"]
> [Déployer un cluster Apache Spark managé avec Azure Databricks](deploy-cluster-databricks.md)
