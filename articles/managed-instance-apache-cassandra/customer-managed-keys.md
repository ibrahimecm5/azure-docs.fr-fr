---
title: Clés gérées par le client Azure Managed Instance pour Apache Cassandra
description: Clés gérées par le client
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: conceptual
ms.date: 10/29/2021
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 4cf92148accf1d07aaafc0a1640de36e2884a9c5
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2021
ms.locfileid: "131893440"
---
# <a name="customer-managed-keys---overview"></a>Vue d’ensemble des clés gérées par le client

Azure Managed Instance pour Apache Cassandra offre la possibilité de chiffrer les données sur le disque à l’aide de votre propre clé. Cet article explique comment implémenter des clés gérées par le client avec Azure Key Vault.

## <a name="prerequisites"></a>Prérequis

- Configurez un secret à l’aide de Azure Key Vault. Apprenez-en davantage sur Azure Key Vault [ici](/azure/key-vault/secrets/about-secrets).
- Déployez un réseau virtuel dans votre groupe de ressources et appliquez le rôle de collaborateur de réseau avec le principal du service Azure Cosmos DB en tant que membre. Pour plus d’informations, consultez [Créer un cluster Azure Managed Instance pour Apache Cassandra à l’aide d’Azure CLI](create-cluster-cli.md). 

> [!IMPORTANT]
> Pour cet article, vous avez besoin d’Azure CLI version 2.30.0 ou ultérieure. Si vous utilisez Azure Cloud Shell, sachez que la version la plus récente est déjà installée.

## <a name="create-a-cluster-with-system-assigned-identity"></a><a id="create-cluster"></a>Créer un cluster avec une identité affectée par le système

   > [!NOTE]
   > Comme indiqué dans les prérequis, pour éviter un échec du déploiement, vérifiez que vous avez appliqué le rôle approprié à votre réseau virtuel avant de tenter de déployer un cluster d’instances gérées :
   > ```azurecli-interactive  
   >     az role assignment create \
   >     --assignee a232010e-820c-4083-83bb-3ace5fc29d0b \
   >     --role 4d97b98b-1d4f-4787-a291-c67834d212e7 \
   >     --scope /subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Network/virtualNetworks/<vnetName>
   > ```

1. Créez un cluster en spécifiant le type d’identité SystemAssigned, en remplaçant `<subscriptionID>`, `<resourceGroupName>`, `<vnetName>` et `<subnetName>` par les valeurs appropriées :

    ```azurecli-interactive    
    subnet="/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Network/virtualNetworks/<vnetName>/subnets/<subnetName>"
    cluster="thvankra-cmk-test-wcus"
    group="thvankra-nova-cmk-test"
    region="westcentralus"
    password="PlaceholderPassword"
    
    az managed-cassandra cluster create \
        --identity-type SystemAssigned \
        --resource-group $group \
        --location $region \
        --cluster-name $cluster \
        --delegated-management-subnet-id $subnet \
        --initial-cassandra-admin-password $password
    ```

1. Récupérer les informations d’identité du cluster créé

    ```azurecli-interactive
    az managed-cassandra cluster show -c $cluster -g $group
    ```

    La sortie inclut une section d’identité comme ci-dessous. Copiez `principalId` pour l’utiliser plus tard :

    ```shell
      "identity": {
        "principalId": "1aa51c7f-196a-4013-a656-1ccabfdc54e0",
        "tenantId": "72f988bf-86f1-41af-91ab-2d7cd011db47",
        "type": "SystemAssigned"
      }
    ```
 
1. Dans Azure Key Vault, créez une stratégie d’accès à vos clés :

   :::image type="content" source="./media/cmk/key-vault-access-policy-1.png" alt-text="Stratégie d’accès à Key Vault 1" lightbox="./media/cmk/key-vault-access-policy-1.png" border="true":::

1. Assignez les autorisations de clés `get`, `wrap` et `unwrap` sur le coffre de clés au `principalId` de cluster extrait ci-dessus. Dans le portail, vous pouvez également rechercher l’ID de principal du cluster à l’aide du nom du cluster :
 

   :::image type="content" source="./media/cmk/key-vault-access-policy-2.png" alt-text="Stratégie d’accès Key Vault 2" lightbox="./media/cmk/key-vault-access-policy-2.png" border="true":::

   > [!WARNING]
   > Vérifiez que la protection contre la purge est activée dans le coffre de clés. Sinon, les déploiements de centre de données échouent. 

1. Après avoir cliqué sur `add` pour ajouter la stratégie d’accès, veillez à l’enregistrer :

   :::image type="content" source="./media/cmk/save.png" alt-text="Enregistrer la stratégie d'accès" lightbox="./media/cmk/key-vault-access-policy-2.png" border="true":::

1. Pour récupérer l’identificateur de clé, sélectionnez votre clé :

   :::image type="content" source="./media/cmk/select-key.png" alt-text="Sélectionner une clé" lightbox="./media/cmk/key-identifier-1.png" border="true":::

1. Cliquez sur la version actuelle :

   :::image type="content" source="./media/cmk/current-version.png" alt-text="Sélectionner la version actuelle" lightbox="./media/cmk/key-identifier-1.png" border="true":::

1. Enregistrez l’identificateur de clé pour une utilisation ultérieure :

   :::image type="content" source="./media/cmk/key-identifier-2.png" alt-text="Identificateur de clé, étape 2" lightbox="./media/cmk/key-identifier-1.png" border="true":::


1. Créez le centre de données en remplaçant `<key identifier>` par la même clé (URI copié à l’étape précédente) pour le chiffrement de disque managé (managed-disk-customer-key-uri) et de stockage de sauvegarde (backup-storage-customer-key-uri) comme indiqué ci-dessous (utilisez la même valeur pour `subnet` que celle utilisée précédemment) : 

    ```azurecli-interactive
    managedDiskKeyUri = "<key identifier>"
    backupStorageKeyUri = "<key identifier>"
    group="thvankra-nova-cmk-test"
    region="westcentralus"
    cluster="thvankra-cmk-test-2"
    dc="dc1"
    nodecount=3
    subnet="/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Network/virtualNetworks/<vnetName>/subnets/<subnetName>"
        
    az managed-cassandra datacenter create \
        --resource-group $group \
        --cluster-name $cluster \
        --data-center-name $dc \
        --managed-disk-customer-key-uri $managedDiskKeyUri \
        --backup-storage-customer-key-uri $backupStorageKeyUri \
        --node-count $nodecount \
        --delegated-subnet-id $subnet \
        --data-center-location $region \
        --sku Standard_DS14_v2
    ```

1. Vous pouvez affecter une identité à un cluster existant sans informations d’identité, comme indiqué ci-dessous :

    ```azurecli-interactive
    az managed-cassandra cluster update --identity-type SystemAssigned -g $group -c $cluster
    ```

## <a name="rotating-the-key"></a><a id="update-cluster"></a>Rotation de la clé

1. Voici la commande permettant de mettre à jour la clé :

    ```azurecli-interactive
    managedDiskKeyUri = "<key identifier>"
    backupStorageKeyUri = "<key identifier>"
    
    az managed-cassandra datacenter update \
        --resource-group $group \
        --cluster-name $cluster \ 
        --data-center-name $dc \
        --managed-disk-customer-key-uri $managedDiskKeyUri \
        --backup-storage-customer-key-uri $backupStorageKeyUri
    ```