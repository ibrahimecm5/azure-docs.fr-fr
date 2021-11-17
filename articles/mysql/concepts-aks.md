---
title: Se connecter à Azure Kubernetes Service - Azure Database pour MySQL
description: Découvrez comment connecter Azure Kubernetes Service à Azure Database pour MySQL
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 07/14/2020
ms.openlocfilehash: 0906c255c3fd77be4de670a980bdc0a2f108c121
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132347101"
---
# <a name="best-practices-for-azure-kubernetes-service-and-azure-database-for-mysql"></a>Meilleures pratiques pour Azure Kubernetes Service et Azure Database pour MySQL

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

Azure Kubernetes Service (AKS) fournit un cluster Kubernetes managé que vous pouvez utiliser dans Azure. Voici quelques options à prendre en compte lors de l’utilisation conjointe d’AKS et d’Azure Database pour MySQL dans la création d’une application.

## <a name="create-database-before-creating-the-aks-cluster"></a>Créer une base de données avant de créer le cluster AKS

Azure Database pour MySQL a deux options de déploiement :

- Serveur unique
- Serveur flexible

L’option Serveur unique prend en charge une seule zone de disponibilité et l’option Serveur flexible prend en charge plusieurs zones de disponibilité. En revanche, AKS prend également en charge l’activation de zones de disponibilité uniques ou multiples.  Commencez par créer le serveur de base de données pour afficher la zone de disponibilité dans laquelle se trouve le serveur, puis créez les clusters AKS dans la même zone de disponibilité. Cela permet d’améliorer les performances de l’application en réduisant la latence réseau.

## <a name="use-accelerated-networking"></a>Utiliser la mise en réseau accélérée

Utilisez des machines virtuelles sous-jacentes dotées de la mise en réseau accélérée dans votre cluster AKS. Lorsque la mise en réseau accélérée est activée sur une machine virtuelle, celle-ci bénéficie d’une latence plus faible, d’une diminution de l’instabilité et d’une utilisation moins importante du processeur. Apprenez-en davantage sur le fonctionnement de la mise en réseau accélérée, les versions de système d’exploitation prises en charge et les instances de machine virtuelle compatibles pour [Linux](../virtual-network/create-vm-accelerated-networking-cli.md).

À partir de novembre 2018, AKS prend en charge la mise en réseau accélérée sur ces instances de machine virtuelle prises en charge. La mise en réseau accélérée est activée par défaut sur les nouveaux clusters AKS qui utilisent ces machines virtuelles.

Vous pouvez vérifier si votre cluster AKS dispose de la mise en réseau accélérée de la façon suivante :

1. Accédez au portail Azure et sélectionnez votre cluster AKS.
2. Sélectionnez l’onglet Propriétés.
3. Copiez le nom du **Groupe de ressources d’infrastructure**.
4. Utilisez la barre de recherche du portail pour localiser et ouvrir le groupe de ressources d’infrastructure.
5. Sélectionnez une machine virtuelle dans ce groupe de ressources.
6. Accédez à l’onglet **Mise en réseau** de la machine virtuelle.
7. Vérifiez que la **Mise en réseau accélérée** est activée.

Ou via Azure CLI, en utilisant les deux commandes suivantes :

```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --query "nodeResourceGroup"
```

La sortie correspond au groupe de ressources que crée AKS et contenant l'interface réseau. Prenez le nom « nodeResourceGroup » et utilisez-le dans la commande suivante. **EnableAcceleratedNetworking** sera true ou false :

```azurecli
az network nic list --resource-group nodeResourceGroup -o table
```

## <a name="use-azure-premium-fileshare"></a>Utiliser le partage de fichiers Azure Premium

 Utilisez le [partage de fichiers Azure Premium](../storage/files/storage-how-to-create-file-share.md?tabs=azure-portal) pour un stockage persistant qui peut être utilisé par un ou plusieurs pods et provisionné de manière dynamique ou statique. Le partage de fichiers Azure Premium offre des performances optimales pour votre application si vous prévoyez un grand nombre d’opérations d’E/S sur le stockage de fichiers. Pour en savoir plus, consultez [comment activer Azure Files](../aks/azure-files-dynamic-pv.md).

## <a name="next-steps"></a>Étapes suivantes

- [Créer un cluster Azure Kubernetes Service](../aks/kubernetes-walkthrough.md)