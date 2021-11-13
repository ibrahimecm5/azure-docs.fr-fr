---
title: FAQ sur la protection et la migration des données pour Azure NetApp Files | Microsoft Docs
description: Réponses aux questions fréquemment posées sur la protection et la migration des données Azure NetApp Files.
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
author: b-juche
ms.author: b-juche
ms.date: 10/11/2021
ms.openlocfilehash: c6422b99087b8239ea6d21c0f5186edd5df1c247
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130270718"
---
# <a name="data-migration-and-protection-faqs-for-azure-netapp-files"></a>FAQ sur la protection et la migration des données pour Azure NetApp Files

Cet article contient des réponses aux questions fréquemment posées sur la protection et la migration des données Azure NetApp Files.

## <a name="how-do-i-migrate-data-to-azure-netapp-files"></a>Comment migrer des données vers Azure NetApp Files ?
Azure NetApp Files fournit les volumes NFS et SMB.  Vous pouvez utiliser n’importe quel outil de copie basé sur le fichier pour migrer des données vers le service. 

NetApp offre une solution SaaS, [NetApp Cloud Sync](https://cloud.netapp.com/cloud-sync-service).  La solution vous permet de répliquer les données NFS ou SMB dans des exportations NFS ou partages SMB d’Azure NetApp Files. 

Vous pouvez également utiliser un large éventail d’outils gratuits pour copier les données. Pour NFS, vous pouvez utiliser des outils de charge de travail, comme [rsync](https://rsync.samba.org/examples.html) pour copier et synchroniser les données source dans un volume Azure NetApp Files. Pour SMB, vous pouvez utiliser des charges de travail [robocopy](/windows-server/administration/windows-commands/robocopy) de la même manière.  Ces outils peuvent également répliquer les autorisations de fichier ou de dossier. 

La configuration requise pour la migration de données en local vers Azure NetApp Files est la suivante : 

- Vérifiez qu’Azure NetApp Files est disponible dans la région Azure cible.
- Validez la connectivité réseau entre la source et l’adresse IP du volume Azure NetApp Files cible. Le transfert de données entre la machine locale et le service Azure NetApp Files est pris en charge via ExpressRoute.
- Créez le volume Azure NetApp Files cible.
- Transférez les données de la source vers le volume cible à l’aide de votre outil de copie de fichiers par défaut.

## <a name="where-does-azure-netapp-files-store-customer-data"></a>Où Azure NetApp Files stocke-t-il les données client ?   

Par défaut, vos données restent dans la région dans laquelle vous déployez vos volumes Azure NetApp Files. Toutefois, vous pouvez choisir de répliquer vos données volume par volume vers les régions de destination disponibles à l’aide de la [réplication entre les régions](cross-region-replication-introduction.md).

## <a name="how-do-i-create-a-copy-of-an-azure-netapp-files-volume-in-another-azure-region"></a>Comment créer une copie d’un volume Azure NetApp Files dans une autre région Azure ?
    
Azure NetApp Files fournit les volumes NFS et SMB.  N’importe quel outil de copie basé sur le fichier peut être utilisé pour répliquer des données entre régions Azure. 

La fonctionnalité de [réplication inter-région](cross-region-replication-introduction.md) vous permet de répliquer de façon asynchrone des données à partir d’un volume Azure NetApp Files (source) d’une région vers un autre volume Azure NetApp Files (destination) d’une autre région.  En outre, vous pouvez [créer un nouveau volume à l’aide d’une capture instantanée d’un volume existant](snapshots-restore-new-volume.md).

NetApp offre une solution SaaS, [NetApp Cloud Sync](https://cloud.netapp.com/cloud-sync-service).  La solution vous permet de répliquer les données NFS ou SMB dans des exportations NFS ou partages SMB d’Azure NetApp Files. 

Vous pouvez également utiliser un large éventail d’outils gratuits pour copier les données. Pour NFS, vous pouvez utiliser des outils de charge de travail, comme [rsync](https://rsync.samba.org/examples.html) pour copier et synchroniser les données source dans un volume Azure NetApp Files. Pour SMB, vous pouvez utiliser des charges de travail [robocopy](/windows-server/administration/windows-commands/robocopy) de la même manière.  Ces outils peuvent également répliquer les autorisations de fichier ou de dossier. 

La configuration requise pour la réplication d’un volume Azure NetApp Files vers une autre région Azure est la suivante : 
- Vérifiez qu’Azure NetApp Files est disponible dans la région Azure cible.
- Validez la connectivité réseau entre les réseaux virtuels dans chaque région. Actuellement, le peering global entre réseaux virtuels n’est pas pris en charge.  Vous pouvez établir la connectivité entre réseaux virtuels en les liant à un circuit ExpressRoute ou à l’aide d’une connexion S2S VPN. 
- Créez le volume Azure NetApp Files cible.
- Transférez les données de la source vers le volume cible à l’aide de votre outil de copie de fichiers par défaut.

## <a name="is-migration-with-azure-data-box-supported"></a>Est-ce que la migration avec Azure Data Box est prise en charge ?

Non. Azure Data Box ne prend pas en charge Azure NetApp Files actuellement. 

## <a name="is-migration-with-azure-importexport-service-supported"></a>Est-ce que la migration avec le service Azure Import/Export est prise en charge ?

Non. Le service Azure Import/Export ne prend pas en charge Azure NetApp Files actuellement.

## <a name="next-steps"></a>Étapes suivantes  

- [Création d’une demande de support Azure](../azure-portal/supportability/how-to-create-azure-support-request.md)
- [Azure Data Box](../databox/index.yml)
- [FAQ sur la mise en réseau](faq-networking.md)
- [Forum aux questions sur la sécurité](faq-security.md)
- [Questions fréquentes (FAQ) relatives aux performances](faq-performance.md)
- [FAQ relatifs au NFS](faq-nfs.md)
- [Questions fréquentes sur SMB](faq-smb.md)
- [Questions fréquentes (FAQ) sur la gestion de la capacité](faq-capacity-management.md)
- [FAQ sur la sauvegarde Azure NetApp Files](faq-backup.md)
- [FAQ relatives à la résilience des applications](faq-application-resilience.md)
- [Forum aux questions sur l’intégration](faq-integration.md)
