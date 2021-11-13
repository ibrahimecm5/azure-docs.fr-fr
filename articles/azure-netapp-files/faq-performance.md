---
title: FAQ sur les performances pour Azure NetApp Files | Microsoft Docs
description: Réponses aux questions fréquemment posées sur les performances d’Azure NetApp Files.
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
author: b-juche
ms.author: b-juche
ms.date: 10/11/2021
ms.openlocfilehash: e7b489a2f9960b39fcb8035889b47468c2af0478
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130270771"
---
# <a name="performance-faqs-for-azure-netapp-files"></a>FAQ sur les performances d’Azure NetApp Files

Cet article contient les réponses à certaines questions fréquemment posées sur les performances d’Azure NetApp Files.

## <a name="what-should-i-do-to-optimize-or-tune-azure-netapp-files-performance"></a>Que puis-je faire pour optimiser ou ajuster les performances d’Azure NetApp Files ?

Vous pouvez effectuer les actions suivantes en fonction de vos exigences en matière de performances : 
- Assurez-vous que la taille de la machine virtuelle est adaptée.
- Activez la mise en réseau accélérée pour la machine virtuelle.
- Sélectionnez un niveau de service et une taille adaptés pour le pool de capacité.
- Créez un volume avec la taille de quota de votre choix pour la capacité et les performances.

## <a name="how-do-i-convert-throughput-based-service-levels-of-azure-netapp-files-to-iops"></a>Comment convertir les niveaux de service basés sur le débit d’Azure NetApp Files en IOPS ?

Vous pouvez convertir les Mo/s en IOPS à l’aide de la formule suivante :  

`IOPS = (MBps Throughput / KB per IO) * 1024`

## <a name="how-do-i-change-the-service-level-of-a-volume"></a>Comment modifier le niveau de service d’un volume ?

Vous pouvez modifier le niveau de service d’un volume existant en déplaçant le volume vers un autre pool de capacité qui utilise le [niveau de service](azure-netapp-files-service-levels.md) souhaité pour le volume. Consultez [Changer dynamiquement le niveau de service d’un volume](dynamic-change-volume-service-level.md). 

## <a name="how-do-i-monitor-azure-netapp-files-performance"></a>Comment surveiller les performances d’Azure NetApp Files ?

Azure NetApp Files fournit des métriques de performance des volumes. Vous pouvez également utiliser Azure Monitor pour surveiller les métriques d’utilisation pour Azure NetApp Files.  Consultez [Métriques pour Azure NetApp Files](azure-netapp-files-metrics.md) pour obtenir la liste des métriques de performance pour Azure NetApp Files.

## <a name="whats-the-performance-impact-of-kerberos-on-nfsv41"></a>Quel est l’impact de Kerberos sur les performances de NFSv4.1 ?

Pour plus d’informations sur les options de sécurité pour NFSv4.1, les vecteurs de performances testés et l’impact sur les performances attendu, consultez [Impact de Kerberos sur les performances des volumes NFSv4.1](performance-impact-kerberos.md). 

## <a name="does-azure-netapp-files-support-smb-direct"></a>Azure NetApp Files prend-il en charge SMB Direct ?

Non, Azure NetApp Files ne prend pas en charge SMB Direct. 

## <a name="is-nic-teaming-supported-in-azure"></a>L’association de cartes réseau est-elle prise en charge dans Azure ?

L’association de cartes réseau n’est pas prise en charge dans Azure. Bien que plusieurs interfaces réseau soient prises en charge sur les machines virtuelles Azure, elles représentent une construction logique plutôt qu’une construction physique. Ainsi, elles ne fournissent aucune tolérance de panne.  En outre, la bande passante disponible pour une machine virtuelle Azure est calculée pour la machine elle-même, et non pour une interface réseau individuelle.

## <a name="are-jumbo-frames-supported"></a>Les trames Jumbo sont-elles prises en charge ?

Les trames Jumbo ne sont pas prises en charge avec les machines virtuelles Azure.

## <a name="next-steps"></a>Étapes suivantes  

- [Impact de Kerberos sur les performances des volumes NFSv4.1](performance-impact-kerberos.md)
- [Considérations sur les performances pour Azure NetApp Files](azure-netapp-files-performance-considerations.md    )
- [Recommandations sur les tests de performances pour Azure NetApp Files](azure-netapp-files-performance-metrics-volumes.md )
- [Test d’évaluation des performances pour Linux](performance-benchmarks-linux.md)
- [Impact de Kerberos sur les performances des volumes NFSv4.1](performance-impact-kerberos.md)
- [Création d’une demande de support Azure](../azure-portal/supportability/how-to-create-azure-support-request.md)
- [FAQ sur la mise en réseau](faq-networking.md)
- [Forum aux questions sur la sécurité](faq-security.md)
- [FAQ relatifs au NFS](faq-nfs.md)
- [Questions fréquentes sur SMB](faq-smb.md)
- [Questions fréquentes (FAQ) sur la gestion de la capacité](faq-capacity-management.md)
- [Questions fréquentes sur la migration et la protection des données](faq-data-migration-protection.md)
- [FAQ sur la sauvegarde Azure NetApp Files](faq-backup.md)
- [FAQ relatives à la résilience des applications](faq-application-resilience.md)
- [Forum aux questions sur l’intégration](faq-integration.md)