---
title: FAQ sur l’intégration pour Azure NetApp Files | Microsoft Docs
description: Réponses aux questions fréquemment posées (FAQ) sur l’utilisation d’autres produits ou services avec Azure NetApp Files.
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
author: b-juche
ms.author: b-juche
ms.date: 10/11/2021
ms.openlocfilehash: c48a35b4578488fdab5fc55cdca4e19d97a003d3
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130270751"
---
# <a name="integration-faqs-for-azure-netapp-files"></a>FAQ sur l’intégration pour Azure NetApp Files

Cet article répond aux questions fréquemment posées sur l’utilisation d’autres produits ou services avec Azure NetApp Files.  

## <a name="can-i-use-azure-netapp-files-nfs-or-smb-volumes-with-azure-vmware-solution-avs"></a>Puis-je utiliser des volumes NFS ou SMB d’Azure NetApp Files avec Azure VMware Solution (AVS) ?

Vous pouvez monter des volumes NFS d’Azure NetApp Files sur des machines virtuelles Linux ou des machines virtuelles Windows AVS. Vous pouvez mapper des partages SMB d’Azure NetApp Files à des machines virtuelles Windows AVS. Pour plus d’informations, consultez [Azure NetApp Files avec Azure VMware Solution]( ../azure-vmware/netapp-files-with-azure-vmware-solution.md).  

## <a name="what-regions-are-supported-for-using-azure-netapp-files-nfs-or-smb-volumes-with-azure-vmware-solution-avs"></a>Quelles sont les régions prises en charge pour l’utilisation de volumes NFS ou SMB d’Azure NetApp Files avec Azure VMware Solution (AVS) ?

L’utilisation de volumes NFS ou SMB d’Azure NetApp Files avec AVS pour les *montages de systèmes d’exploitation invités* est prise en charge dans [toutes les régions où AVS et ANF sont activés](https://azure.microsoft.com/global-infrastructure/services/?products=azure-vmware,netapp).

## <a name="does-azure-netapp-files-work-with-azure-policy"></a>Azure NetApp Files fonctionne-t-il avec Azure Policy ?

Oui. Azure NetApp Files est un service interne. Il adhère pleinement aux normes du fournisseur de ressources Azure. Par conséquent, Azure NetApp Files peut être intégré à Azure Policy par le biais de *définitions de stratégie personnalisées*. Pour plus d’informations sur la façon d’implémenter des stratégies personnalisées pour Azure NetApp Files, consultez [Azure Policy désormais disponible pour Azure NetApp Files](https://techcommunity.microsoft.com/t5/azure/azure-policy-now-available-for-azure-netapp-files/m-p/2282258) sur Microsoft Tech Community. 

## <a name="which-unicode-character-encoding-is-supported-by-azure-netapp-files-for-the-creation-and-display-of-file-and-directory-names"></a>Quel codage de caractères Unicode est pris en charge par Azure NetApp Files pour la création et l’affichage des noms de fichiers et de répertoires ?   

Azure NetApp Files prend uniquement en charge les noms de fichiers et de répertoires qui sont encodés avec le format de codage de caractères Unicode UTF-8 pour les volumes NFS et SMB.

Si vous essayez de créer des fichiers ou des répertoires dont les noms utilisent des caractères supplémentaires ou des paires de substitutions, tels que des caractères non standard et des Emoji qui ne sont pas pris en charge par UTF-8, l’opération échoue. Dans ce cas, une erreur d’un client Windows peut indiquer « Le nom de fichier que vous avez spécifié n’est pas valide ou est trop long. Spécifiez un autre nom de fichier. » 

## <a name="next-steps"></a>Étapes suivantes  

- [Création d’une demande de support Azure](../azure-portal/supportability/how-to-create-azure-support-request.md)
- [FAQ sur la mise en réseau](faq-networking.md)
- [Forum aux questions sur la sécurité](faq-security.md)
- [Questions fréquentes (FAQ) relatives aux performances](faq-performance.md)
- [FAQ relatifs au NFS](faq-nfs.md)
- [Questions fréquentes sur SMB](faq-smb.md)
- [Questions fréquentes (FAQ) sur la gestion de la capacité](faq-capacity-management.md)
- [Questions fréquentes sur la migration et la protection des données](faq-data-migration-protection.md)
- [FAQ sur la sauvegarde Azure NetApp Files](faq-backup.md)
- [FAQ relatives à la résilience des applications](faq-application-resilience.md)
