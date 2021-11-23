---
title: Clusters managés Service Fabric
description: Les clusters managés Service Fabric sont une évolution du modèle de ressource de cluster Azure Service Fabric qui simplifie le déploiement et la gestion de cluster.
ms.topic: overview
ms.date: 10/22/2021
ms.openlocfilehash: 5c94254e470e625a844b345c452e252fcd69b626
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132308121"
---
# <a name="service-fabric-managed-clusters"></a>Clusters managés Service Fabric

Les clusters gérés Service Fabric sont une évolution du modèle de ressource de cluster Azure Service Fabric qui simplifie votre expérience de déploiement et de gestion de cluster.

Le modèle Azure Resource Model (ARM) pour les clusters Service Fabric traditionnels vous oblige à définir une ressource de cluster avec un certain nombre de ressources de support, qui doivent toutes être « câblées » correctement (lors du déploiement et tout au long du cycle de vie du cluster) afin que le cluster et vos services fonctionnent correctement. En revanche, le modèle d’encapsulation pour clusters managés Service Fabric est constitué d’une seule ressource de *cluster managé Service Fabric*. Toutes les ressources sous-jacentes pour le cluster sont extraites et managées par Azure en votre nom.

**Modèle de cluster traditionnel Service Fabric**
![Modèle de cluster traditionnel Service Fabric][sf-composition]

**Modèle de cluster managé Service Fabric**
![Modèle de cluster managé Service Fabric][sf-encapsulation]

En termes de taille et de complexité, le modèle ARM pour un cluster managé Service Fabric est d’environ 100 lignes au format JSON, par opposition à quelques lignes 1 000 requises pour définir un cluster Service Fabric classique :

| Ressources Service Fabric | Ressources de cluster managé Service Fabric |
|----------|-----------|
| Cluster Service Fabric | Cluster managé Service Fabric |
| Groupe(s) de machines virtuelles identiques | |
| Équilibrage de charge | |
| Adresse IP publique | |
| Compte(s) de stockage | |
| Réseau virtuel | |

## <a name="service-fabric-managed-cluster-advantages"></a>Avantages du cluster managé Service Fabric
Les clusters managés Service Fabric offrent un certain nombre d’avantages par rapport aux clusters traditionnels, notamment :

**Déploiement et gestion simplifiés des clusters**
- Déployer et gérer une seule ressource Azure
- Gestion des certificats de cluster et rotation automatique tous les 90 jours
- Opérations de mise à l’échelle simplifiées
- Prise en charge de la mise à niveau automatique de l’image de système d’exploitation
- Prise en charge des changements de référence SKU de système d’exploitation sur place

**Empêcher les erreurs opérationnelles**
- Empêcher les incompatibilités de configuration avec les ressources sous-jacentes
- Bloquer les opérations non sécurisées (telles que la suppression d’un nœud seed)

**Meilleures pratiques par défaut**
- Paramètres de fiabilité et de durabilité simplifiés

Il n’y a aucun autre coût pour les clusters managés Service Fabric que celui des ressources sous-jacentes nécessaires au cluster, et le même contrat SLA Service Fabric s’applique aux clusters managés.

> [!NOTE]
> Il n’existe aucun chemin de migration entre les clusters Service Fabric existants et les clusters managés. Vous avez besoin de créer un cluster managé Service Fabric pour utiliser ce nouveau type de ressource.

## <a name="service-fabric-managed-cluster-skus"></a>SKU de cluster managé Service Fabric

Les clusters managés Service Fabric sont disponibles la fois dans les SKU de base et standard.

| Fonctionnalité | De base | standard |
| ------- | ----- | -------- |
| Ressource réseau (SKU pour [Load Balancer](../load-balancer/skus.md), [adresse IP publique](../virtual-network/ip-services/public-ip-addresses.md)) | De base | standard |
| Nombre minimal de nœuds (instance de machine virtuelle) | 3 | 5 |
| Nombre maximal de nœuds par type de nœud | 100 | 1 000 |
| Nombre maximal de types de nœuds | 1 | 20 |
| Ajouter/supprimer des types de nœuds | Non | Oui |
| Redondance de zone | Non | Oui |

## <a name="feature-support"></a>Prise en charge des fonctionnalités

Les fonctionnalités des clusters managés vont continuer à évoluer. Pour plus d’informations, consultez la [documentation sur les options de configuration des clusters managés](how-to-managed-cluster-configuration.md) ou les [modèles Bicep et ARM](/azure/templates/microsoft.servicefabric/allversions).


## <a name="next-steps"></a>Étapes suivantes

Pour commencer à utiliser des clusters managés Service Fabric, essayez le démarrage rapide :

> [!div class="nextstepaction"]
> [Créer un cluster managé Service Fabric](quickstart-managed-cluster-template.md)

Et les informations de référence expliquant [comment configurer votre cluster managé](how-to-managed-cluster-configuration.md)

[sf-composition]: ./media/overview-managed-cluster/sfrp-composition-resource.png
[sf-encapsulation]: ./media/overview-managed-cluster/sfrp-encapsulated-resource.png