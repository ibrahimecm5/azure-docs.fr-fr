---
title: Script CLI - Créer une instance d’Azure Database pour MySQL - Serveur flexible dans un réseau virtuel
description: Cet exemple de script Azure CLI montre comment créer une instance d’Azure Database pour MySQL - Serveur flexible dans un réseau virtuel (méthode de connectivité d’accès public) et comment se connecter au serveur à partir d’une machine virtuelle du réseau virtuel.
author: shreyaaithal
ms.author: shaithal
ms.service: mysql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 09/15/2021
ms.openlocfilehash: 4d7972293652d765cdb1686ff7759a41104f8229
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131844237"
---
# <a name="create-an-azure-database-for-mysql---flexible-server-in-a-vnet-using-azure-cli"></a>Créer une instance d’Azure Database pour MySQL - Serveur flexible dans un réseau virtuel avec Azure CLI

Cet exemple de script CLI crée une instance d’Azure Database pour MySQL - Serveur flexible dans un réseau virtuel ([méthode de connectivité d’accès privé](../concepts-networking-vnet.md)) et se connecte au serveur à partir d’une machine virtuelle appartenant au réseau virtuel.

> [!NOTE] 
> Une fois le serveur créé, la méthode de connectivité ne peut pas être modifiée. Par exemple, si vous créez un serveur avec *Accès privé (intégration au réseau virtuel)* , vous ne pourrez pas passer à *Accès public (adresses IP autorisées)* à l’issue de cette création. Pour en savoir plus sur les méthodes de connectivité, consultez [Concepts relatifs au réseau](../concepts-networking.md).

[!INCLUDE [flexible-server-free-trial-note](../../includes/flexible-server-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment](../../../../includes/azure-cli-prepare-your-environment.md)]

- Cet article nécessite la version 2.0 ou ultérieure d’Azure CLI. Si vous utilisez Azure Cloud Shell, la version la plus récente est déjà installée. 

## <a name="sample-script"></a>Exemple de script

Remplacez les lignes mises en surbrillance dans le script par vos valeurs de variables.

[!code-azurecli-interactive[main](../../../../cli_scripts/mysql/flexible-server/create-server-private-access/create-connect-server-in-vnet.sh?highlight=7,10 "Create and Connect to an Azure Database for MySQL - Flexible Server (General Purpose SKU) in VNet")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement

Une fois que l’exemple de script a été exécuté, vous pouvez utiliser l’extrait de code suivant pour nettoyer les ressources.

[!code-azurecli-interactive[main](../../../../cli_scripts/mysql/flexible-server/create-server-private-access/clean-up-resources.sh "Clean up resources.")]

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes. Chaque commande du tableau renvoie à une documentation spécifique.

| **Commande** | **Remarques** |
|---|---|
|[az group create](/cli/azure/group#az_group_create)|Crée un groupe de ressources dans lequel toutes les ressources sont stockées|
|[az mysql flexible-server create](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_create)|Crée un serveur flexible qui héberge les bases de données.|
|[az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create)|Crée un sous-réseau au sein du réseau virtuel.|
|[az vm create](/cli/azure/vm#az_vm_create)|Crée une machine virtuelle Azure.|
|[az vm open-port](/cli/azure/vm#az_vm_open_port)|Ouvre une machine virtuelle au trafic entrant sur les ports spécifiés.|
|[az group delete](/cli/azure/group#az_group_delete) | Supprime un groupe de ressources, y compris toutes les ressources imbriquées.|

## <a name="next-steps"></a>Étapes suivantes

- Pour essayer d’autres scripts : [Exemples Azure CLI pour Azure Database pour MySQL - Serveur flexible](../sample-scripts-azure-cli.md)
- Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](/cli/azure).