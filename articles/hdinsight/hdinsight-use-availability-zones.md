---
title: Créer un cluster Azure HDInsight qui utilise des zones de disponibilité
description: Apprenez à créer un cluster Azure HDInsight qui utilise des zones de disponibilité.
ms.service: hdinsight
ms.topic: how-to
ms.custom: references_regions
ms.date: 09/01/2021
ms.openlocfilehash: 37d15255e469c369086954f08a1401a82641d838
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132281813"
---
# <a name="create-an-hdinsight-cluster-that-uses-availability-zones-preview"></a>Créer un cluster HDInsight qui utilise des zones de disponibilité (préversion)

Un cluster Azure HDInsight est constitué de plusieurs nœuds (nœuds principaux, nœuds de travail, nœuds de passerelle et nœuds Zookeeper). Par défaut, dans une région qui prend en charge les zones de disponibilité, l’utilisateur n’a aucun contrôle sur la zone de disponibilité dans laquelle les nœuds de cluster sont approvisionnés. 

Avec cette nouvelle fonctionnalité de zone de disponibilité, l’utilisateur peut maintenant spécifier la zone de disponibilité qui doit héberger tous les nœuds du cluster HDInsight. Les nœuds de cluster sont physiquement séparés d’une autre zone de disponibilité et sont isolés des échecs dans d’autres zones de disponibilité de la même région. Ce modèle de déploiement fournit également une connectivité réseau peu coûteuse et à faible latence au sein du cluster. 

La réplication de ce modèle de déploiement dans plusieurs zones de disponibilité peut fournir un niveau de disponibilité plus élevé pour la protection contre les défaillances matérielles.

Cet article explique la procédure de création d’un cluster HDInsight dans une zone de disponibilité et l’utilisation de cette fonctionnalité pour obtenir une disponibilité plus élevée. 

## <a name="before-you-begin"></a>Avant de commencer
La fonctionnalité Zone de disponibilité n’est prise en charge que pour les clusters créés après le 15 juin. Les paramètres de la zone de disponibilité ne peuvent pas être mis à jour une fois le cluster créé. Vous ne pouvez pas non plus mettre à jour un cluster de zones de non-disponibilité existant pour utiliser des zones de disponibilité.

## <a name="prerequisites-and-region-availability"></a>Configuration requise et disponibilité de la région
Configuration requise :

 - Les clusters doivent être créés sous un réseau virtuel personnalisé. 
 - Vous devez apporter votre propre base de données SQL pour la base de données Ambari et le metastore externe (comme le metastore Hive) afin de pouvoir configurer ces bases de données dans la même zone de disponibilité. 

Les clusters HDInsight peuvent actuellement être créés à l’aide de zones de disponibilité dans les régions suivantes :

 - Australie Est
 - Brésil Sud
 - Centre du Canada
 - USA Centre
 - USA Est
 - USA Est 2
 - France Centre
 - Allemagne Centre-Ouest
 - Japon Est
 - Europe Nord
 - Asie Sud-Est
 - États-Unis - partie centrale méridionale
 - Sud du Royaume-Uni
 - Gouvernement américain - Virginie
 - Europe Ouest
 - USA Ouest 2

## <a name="overview-of-availability-zones-for-hdinsight-clusters"></a>Vue d’ensemble des zones de disponibilité pour les clusters HDInsight

Les zones de disponibilité sont des emplacements physiques uniques dans une région. Chaque zone de disponibilité est composée d’un ou de plusieurs centres de données équipés d’une alimentation, d’un système de refroidissement et d’un réseau indépendants. Dans Azure, une région contient au moins deux zones de disponibilité. Cette séparation physique des zones de disponibilité dans une région protège les applications et les données des défaillances du centre de données. Pour plus d’informations, consultez [Que sont les zones de disponibilité dans Azure](../availability-zones/az-overview.md).

Les clusters Azure HDInsight peuvent être configurés pour être déployés dans une zone de disponibilité. Tous les nœuds de ce cluster HDInsight, y compris les deux nœuds principaux, trois nœuds Zookeeper, deux nœuds de passerelle et les nœuds Worker sont placés dans la zone de disponibilité spécifiée.  Par exemple, il existe trois zones de disponibilité dans la région USA Est. Un cluster HDInsight dans la région USA Est peut être créé avec tous les nœuds de la zone de disponibilité 1. 

L’utilisation de zones de disponibilité avec un cluster HDInsight de cette manière peut offrir des avantages en matière de performances et de coûts : 

 - Meilleures performances en raison d’une connectivité réseau à faible latence
 - Réduction des coûts : le transfert de données au sein d’une même zone de disponibilité est gratuit. Entre les zones de disponibilité, le transfert de données entraîne un coût de mise en réseau supplémentaire. 

Si votre application nécessite une haute disponibilité sur plusieurs zones de disponibilité, vous pouvez créer un cluster HDInsight principal dans une zone de disponibilité et créer un cluster HDInsight secondaire dans une zone de disponibilité différente avec une taille minimale pour réduire les coûts. Avec cette conception, si l’une des autres zones de disponibilité tombe en panne, ce cluster HDInsight n’est pas affecté. Si cette zone de disponibilité tombe en panne, les clients doivent basculer les clusters secondaires d’une autre zone de disponibilité vers la zone principale, acheminer la charge de travail vers ce nouveau cluster principal et augmenter rapidement la taille du cluster pour récupérer le traitement des données.   

## <a name="create-an-hdinsight-cluster-using-availability-zone"></a>Créer un cluster HDInsight à l’aide d’une zone de disponibilité
Vous pouvez utiliser le modèle Azure Resource Manager (ARM) pour lancer un cluster HDInsight dans une zone de disponibilité spécifiée. 

Dans la section des ressources, vous devez ajouter une section de « zones » et spécifier la zone de disponibilité dans laquelle vous souhaitez déployer ce cluster. 

```json
   "resources": [
        {
            "type": "Microsoft.HDInsight/clusters",
            "apiVersion": "2018-06-01-preview",
            "name": "[parameters('cluster name')]",
            "location": "East US 2",
            "zones": [
                "1"
            ],
```
 
## <a name="verify-nodes-within-one-availability-zone-across-zones"></a>Vérifier les nœuds dans une zone de disponibilité entre les zones
Lorsque le cluster HDInsight est prêt, vous pouvez vérifier l’emplacement pour afficher la zone de disponibilité dans laquelle il est déployé.

:::image type="content" source="./media/hdinsight-use-availability-zones/cluster-availability-zone-info.png" alt-text="Capture d’écran montrant des informations sur la zone de disponibilité dans Vue d'ensemble du cluster" border="true":::

**Obtenir une réponse d’API** : 

```json
 [
        {
            "location": "East US 2",
            "zones": [
                "1"
            ],
```

## <a name="scale-up-the-cluster"></a>Effectuer un scale-up du cluster
Vous pouvez effectuer un scale-up d’un cluster HDInsight avec plus de nœuds Worker. Les nœuds Worker récemment ajoutés sont placés dans la même zone de disponibilité de ce cluster. 

**Limitations** : 

 - La fonctionnalité Zone de disponibilité de HDInsight ne prend pas en charge les clusters qui nécessitent des disques managés (cluster Kafka et fonctionnalité d’écriture accélérée activés sur le cluster HBase) 

## <a name="best-practices"></a>Meilleures pratiques

 - Sauvegardez régulièrement les configurations dans la base de données Ambari. 
 - Implémentez une logique pour acheminer facilement la charge de travail vers un cluster secondaire.

## <a name="when-az-goes-down-what-to-expect"></a>Que faire lorsqu’une zone de disponibilité tombe en panne
 - Vous ne pouvez pas utiliser SSH pour accéder à ce cluster
 - Vous ne pouvez pas supprimer, ni effectuer de scale-up ou scale-down de ce cluster
 - Vous ne pouvez pas envoyer de travaux ni voir l’historique des travaux
 - Vous pouvez toujours envoyer une nouvelle requête de création de cluster dans une autre région
