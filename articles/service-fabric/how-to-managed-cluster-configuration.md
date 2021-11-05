---
title: Configurer votre cluster managé Service Fabric
description: Découvrez comment configurer votre cluster managé Service Fabric pour les mises à niveau automatiques du système d’exploitation, les règles de groupe de sécurité réseau et bien plus encore.
ms.topic: how-to
ms.date: 10/25/2021
ms.openlocfilehash: 808b7e96778821c695ed5fca08648e95f4290959
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131060627"
---
# <a name="service-fabric-managed-cluster-configuration-options"></a>Options de configuration du cluster Service Fabric géré

En plus de sélectionner la [référence SKU du cluster managé Service Fabric](overview-managed-cluster.md#service-fabric-managed-cluster-skus) lors de la création de votre cluster, il existe d’autres façons de le configurer, notamment :

* Ajout d’une [extension de groupe de machines virtuelles identiques](how-to-managed-cluster-vmss-extension.md) à un type de nœud
* Configuration de la [répartition des zones de disponibilité](how-to-managed-cluster-availability-zones.md) d’un cluster
* Configuration de [paramètres réseau](how-to-managed-cluster-networking.md) de cluster
* Configurer un type de nœud pour les [groupes de machines virtuelles identiques de grande taille](how-to-managed-cluster-large-virtual-machine-scale-sets.md)
* Configuration de l’[identité managée](how-to-managed-identity-managed-cluster-virtual-machine-scale-sets.md) sur des types de nœuds de cluster
* Activation du [chiffrement du disque de données et du système d’exploitation](how-to-enable-managed-cluster-disk-encryption.md) sur des nœuds de cluster
* Configurer la [mise à l’échelle automatique](how-to-managed-cluster-autoscale.md) sur un type de nœud secondaire
* [Mettre à l’échelle un type de nœud manuellement](how-to-managed-cluster-modify-node-type.md#scale-a-node-type-manually-with-portal)
* Activer les [mises à niveau de l’image du système d’exploitation](how-to-managed-cluster-modify-node-type.md#enable-automatic-os-image-upgrades) sur des types de noeud de cluster
* Modifier l’[image du système d’exploitation](how-to-managed-cluster-modify-node-type.md#modify-the-os-image-for-a-node-type-with-portal) utilisée pour un type de nœud
* Configurer les [propriétés de sélection élective](how-to-managed-cluster-modify-node-type.md#configure-placement-properties-for-a-node-type-with-portal) d’un type de nœud
* Sélection de la SKU du [type de disque managé](how-to-managed-cluster-managed-disk.md) du cluster
* Configuration des [options de mise à niveau](how-to-managed-cluster-upgrades.md) du cluster pour les mises à jour du runtime


## <a name="next-steps"></a>Étapes suivantes

[Vue d’ensemble des clusters managés Service Fabric](overview-managed-cluster.md)

[Service Fabric cluster templates](https://github.com/Azure-Samples/service-fabric-cluster-templates) (Modèles de cluster Service Fabric)
