---
title: Questions fréquentes (FAQ) sur NetApp Files | Microsoft Docs
description: Contient les réponses à certaines questions fréquemment posées sur la gestion de la capacité dans Azure NetApp Files.
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
author: b-juche
ms.author: b-juche
ms.date: 10/11/2021
ms.openlocfilehash: 1f28b7b590ef2a7a3894f9eff7eff7199167afc3
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130270789"
---
# <a name="capacity-management-faqs-for-azure-netapp-files"></a>FAQ sur la gestion de la capacité dans Azure NetApp Files

Cet article répond aux questions fréquemment posées (FAQ) sur la gestion de la capacité dans Azure NetApp Files.

## <a name="how-do-i-monitor-usage-for-capacity-pool-and-volume-of-azure-netapp-files"></a>Comment surveiller l’utilisation du pool de capacité et du volume Azure NetApp Files ? 

Azure NetApp Files fournit des métriques de pool de capacité et de volume d’utilisation. Vous pouvez également utiliser Azure Monitor pour surveiller l’utilisation d’Azure NetApp Files. Consultez [Métriques pour Azure NetApp Files](azure-netapp-files-metrics.md) pour plus d’informations. 

## <a name="how-do-i-determine-if-a-directory-is-approaching-the-limit-size"></a>Comment déterminer si un répertoire approche de la taille limite ?

Vous pouvez utiliser la commande `stat` à partir d’un client pour voir si un répertoire approche de la [limite de taille maximale](azure-netapp-files-resource-limits.md#resource-limits) pour des métadonnées de répertoire (320 Mo).
Consultez [Limites des ressources pour Azure NetApp Files](azure-netapp-files-resource-limits.md#directory-limit) pour connaître la limite et le calcul. 

## <a name="does-snapshot-space-count-towards-the-usable--provisioned-capacity-of-a-volume"></a>L’espace de capture instantanée est-il pris en compte dans la capacité utilisable ou approvisionnée d’un volume ?

Oui, la [capacité de capture instantanée consommée](azure-netapp-files-cost-model.md#capacity-consumption-of-snapshots) est comptabilisée dans l’espace approvisionné du volume. En cas de saturation du volume, envisagez d’effectuer les actions suivantes :

* [Redimensionnez le volume](azure-netapp-files-resize-capacity-pools-or-volumes.md).
* [Supprimez les captures instantanées plus anciennes](snapshots-delete.md) pour libérer de l’espace dans le volume d’hébergement. 

## <a name="does-azure-netapp-files-support-auto-grow-for-volumes-or-capacity-pools"></a>Azure NetApp Files prend-il en charge la croissance automatique pour des volumes ou des pools de capacité ?

Non, les volumes et le pool de capacité d’Azure NetApp Files ne connaissent pas de croissance automatique lors de la saturation. Consultez [Modèle de coût pour Azure NetApp Files](azure-netapp-files-cost-model.md).   

Vous pouvez utiliser l’[outil pris en charge par la communauté ANFCapacityManager Logic Apps](https://github.com/ANFTechTeam/ANFCapacityManager) pour gérer les règles d’alerte basées sur la capacité. L’outil peut augmenter automatiquement la taille des volumes pour les empêcher de manquer d’espace.

## <a name="does-the-destination-volume-of-a-replication-count-towards-hard-volume-quota"></a>Le volume de destination d’une réplication est-il pris en compte pour le quota de volume en dur ?  

Non, le volume de destination d’une réplication n’est pas pris en compte pour le quota de volume en dur.

## <a name="can-i-manage-azure-netapp-files-through-azure-storage-explorer"></a>Puis-je gérer Azure NetApp Files via l’Explorateur Stockage Azure ?

Non. Azure NetApp Files n’est pas pris en charge par l’Explorateur Stockage Azure.

## <a name="next-steps"></a>Étapes suivantes  

- [Création d’une demande de support Azure](../azure-portal/supportability/how-to-create-azure-support-request.md)
- [FAQ sur la mise en réseau](faq-networking.md)
- [Forum aux questions sur la sécurité](faq-security.md)
- [Questions fréquentes (FAQ) relatives aux performances](faq-performance.md)
- [FAQ relatifs au NFS](faq-nfs.md)
- [Questions fréquentes sur SMB](faq-smb.md)
- [Questions fréquentes sur la migration et la protection des données](faq-data-migration-protection.md)
- [FAQ sur la sauvegarde Azure NetApp Files](faq-backup.md)
- [FAQ relatives à la résilience des applications](faq-application-resilience.md)
- [Forum aux questions sur l’intégration](faq-integration.md)
