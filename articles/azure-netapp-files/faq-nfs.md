---
title: FAQ sur NFS pour Azure NetApp Files | Microsoft Docs
description: Réponses aux questions fréquemment posées sur le forum aux questions (FAQ) relatif au protocole NFS d’Azure NetApp Files.
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
author: b-juche
ms.author: b-juche
ms.date: 10/19/2021
ms.openlocfilehash: e9c38f573a613debbb9f3e3c6b1da00ab23f92f1
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130270717"
---
# <a name="nfs-faqs-for-azure-netapp-files"></a>FAQ sur NFS pour Azure NetApp Files

Cet article répond aux questions fréquemment posées sur le forum aux questions (FAQ) relatif au protocole NFS d’Azure NetApp Files.

## <a name="i-want-to-have-a-volume-mounted-automatically-when-an-azure-vm-is-started-or-rebooted--how-do-i-configure-my-host-for-persistent-nfs-volumes"></a>Je veux monter un volume automatiquement lorsqu’une machine virtuelle Azure est démarrée ou redémarrée.  Comment configurer mon hôte pour les volumes NFS persistants ?

Pour qu’un volume NFS soit monté automatiquement au moment du démarrage ou du redémarrage de la machine virtuelle, ajoutez une entrée au fichier `/etc/fstab` sur l’hôte. 

Consultez [Monter ou démonter un volume pour des machines virtuelles Windows ou Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md) pour plus d’informations.  

## <a name="what-nfs-version-does-azure-netapp-files-support"></a>Quelle version de NFS Azure NetApp Files prend-il en charge ?

Azure NetApp Files prend en charge NFSv3 et NFSv4.1. Vous pouvez [créer un volume](azure-netapp-files-create-volumes.md) à l’aide de l’une de ces versions NFS. 

## <a name="how-do-i-enable-root-squashing"></a>Comment activer le root squash ?

Vous pouvez spécifier si le compte racine peut accéder ou non au volume à l’aide de la stratégie d’exportation du volume. Pour plus d’informations, consultez [Configurer une stratégie d’exportation pour un volume NFS](azure-netapp-files-configure-export-policy.md).

## <a name="can-i-use-the-same-file-path-volume-creation-token-for-multiple-volumes"></a>Puis-je utiliser le même chemin d’accès de fichier (jeton de création de volume) pour plusieurs volumes ?

Oui, vous pouvez. Toutefois, le chemin d’accès au fichier doit être unique au sein de chaque sous-réseau.     

## <a name="when-i-try-to-access-nfs-volumes-through-a-windows-client-why-does-the-client-take-a-long-time-to-search-folders-and-subfolders"></a>Lorsque j’essaie d’accéder à des volumes NFS via un client Windows, pourquoi le client met-il beaucoup de temps à rechercher des dossiers et des sous-dossiers ?

Assurez-vous que `CaseSensitiveLookup` est activé sur le client Windows pour accélérer la recherche de dossiers et de sous-dossiers :

1. Utilisez la commande PowerShell suivante pour activer CaseSensitiveLookup :   
    `Set-NfsClientConfiguration -CaseSensitiveLookup 1`    
2. Montez le volume sur le serveur Windows.   
    Exemple :   
    `Mount -o rsize=1024 -o wsize=1024 -o mtype=hard \\10.x.x.x\testvol X:*`

## <a name="how-does-azure-netapp-files-support-nfsv41-file-locking"></a>Comment Azure NetApp Files prend-il en charge le verrouillage de fichiers NFSv 4.1 ? 

Pour les clients NFSv 4.1, Azure NetApp Files prend en charge le mécanisme de verrouillage de fichiers NFSv 4.1 qui maintient l’état de tous les verrous de fichier sous un modèle basé sur un bail. 

Selon la norme RFC 3530, Azure NetApp Files définit une période de bail unique pour l’ensemble des états conservés par un client NFS. Si le client ne renouvelle pas son bail au cours de la période définie, tous les états associés à son bail sont libérés par le serveur.  

Par exemple, si un client montant un volume cesse de répondre ou se bloque au-delà des délais d’attente, les verrous sont libérés. Le client peut renouveler son bail de manière explicite ou implicite en effectuant des opérations telles que la lecture d’un fichier.   

Une période de grâce définit une période de traitement spécial durant laquelle les clients peuvent tenter de récupérer leur état de verrouillage lors d’une récupération de serveur. Le délai d’expiration par défaut des baux est de 30 secondes, avec une période de grâce de 45 secondes. Passé ce délai, le bail du client est libéré. 

## <a name="next-steps"></a>Étapes suivantes  

- [Questions fréquentes (FAQ) sur Microsoft Azure ExpressRoute](../expressroute/expressroute-faqs.md)
- [Questions fréquentes (FAQ) sur les réseaux virtuels Microsoft Azure](../virtual-network/virtual-networks-faq.md)
- [Création d’une demande de support Azure](../azure-portal/supportability/how-to-create-azure-support-request.md)
- [Azure Data Box](../databox/index.yml)
- [Questions fréquentes (FAQ) sur les performances SMB pour Azure NetApp Files](azure-netapp-files-smb-performance.md)
- [FAQ sur la mise en réseau](faq-networking.md)
- [Forum aux questions sur la sécurité](faq-security.md)
- [Questions fréquentes (FAQ) relatives aux performances](faq-performance.md)
- [Questions fréquentes sur SMB](faq-smb.md)
- [Questions fréquentes (FAQ) sur la gestion de la capacité](faq-capacity-management.md)
- [Questions fréquentes sur la migration et la protection des données](faq-data-migration-protection.md)
- [FAQ sur la sauvegarde Azure NetApp Files](faq-backup.md)
- [FAQ relatives à la résilience des applications](faq-application-resilience.md)
- [Forum aux questions sur l’intégration](faq-integration.md)