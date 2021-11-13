---
title: Résoudre les erreurs de réplication inter-région pour Azure NetApp Files | Microsoft Docs
description: Décrit les messages d’erreur et les résolutions qui peuvent vous aider à résoudre les problèmes de réplication inter-région pour Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 03/10/2021
ms.author: b-juche
ms.openlocfilehash: bae0ed7fc15843c50af9dca860367d2074af33ec
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130256270"
---
# <a name="troubleshoot-cross-region-replication-errors"></a>Résoudre les problèmes de réplication interrégionale

Cet article décrit les messages d’erreur et les résolutions qui peuvent vous aider à résoudre les problèmes de réplication inter-région pour Azure NetApp Files. 

## <a name="errors-creating-replication"></a>Erreurs lors de la création de la réplication  

|     Message d’erreur    |     Résolution    |
|-|-|
|     `Volume {0} cannot   be used as source because it is already in replication`    |     Vous ne pouvez pas créer de réplication avec un volume source qui figure déjà dans une relation de réplication de données.    |
|     `Peered region   '{0}' is not accepted`    |     Vous tentez de créer une réplication entre des régions non appairées.    |
|     `RemoteVolumeResource   '{0}' of wrong type '{1}'`    |     Vérifiez que l’ID de ressource distant est un ID de ressource de volume.    |

## <a name="errors-authorizing-volume"></a>Erreur lors de l’autorisation du volume  

|     Message d’erreur    |     Résolution    |
|-|-|
|     `Missing value   for 'AuthorizeSourceReplication'`    |     `RemoteResourceID` est manquant ou n’est pas valide dans l’interface utilisateur ou la requête d’API (corrigez le message d’erreur).    |
|     `Missing value   for 'RemoteVolumeResourceId'`    |     `RemoteResourceID` est manquant ou n’est pas valide dans l’interface utilisateur ou la requête d’API.    |
|     `Data   Protection volume not found for RemoteVolumeResourceId: {remoteResourceId}`    |     Vérifiez si `RemoteResourceID` est correct ou existe pour l’utilisateur.    |
|     `Remote volume   '{0}' is not configured for replication`    |     Le volume de destination n’est pas un volume de protection des données.    |
|     `Remote volume   '{0}' does not have source volume '{1}' as RemoteVolumeResourceId`    |     Le volume de protection des données n’a pas ce volume source dans son ID de ressource distante (l’ID source entré est incorrect).    |
|     `The   destination volume replication creation failed (message: {0})`    |     Cette erreur indique une erreur de serveur. Contactez le support technique.    |

## <a name="errors-deleting-replication"></a>Erreurs lors de la suppression de la réplication

|     Message d’erreur    |     Résolution    |
|-|-|
|     `Replication   cannot be deleted, mirror state needs to be in status: Broken before deleting`    |     Vérifiez si la réplication a été arrêtée ou si elle n’est pas initialisée et inactive (échec d’initialisation).    |
|     `Cannot delete   source replication`    |     La suppression de la réplication du côté source n’est pas autorisée. Veillez à supprimer la réplication du côté destination.    |

## <a name="errors-deleting-volume"></a>Erreurs de suppression du volume

|     Message d’erreur    |     Résolution    |
|-|-|
| `Volume is a member of an active volume replication relationship`  |  Supprimez la réplication avant de supprimer le volume. Consultez [Supprimer des réplications](cross-region-replication-delete.md). Cette opération nécessite de supprimer l’appairage avant de supprimer la réplication du volume. |
| `Volume with replication cannot be deleted`  |  Supprimez la réplication avant de supprimer le volume. Consultez [Supprimer des réplications](cross-region-replication-delete.md). Cette opération nécessite de supprimer l’appairage avant de supprimer la réplication du volume. 

## <a name="errors-resyncing-volume"></a>Erreurs de resynchronisation de volume

|     Message d’erreur    |     Résolution    |
|-|-|
|     `Volume Replication is in invalid status: (Mirrored|Uninitialized) for operation: 'ResyncReplication'`     |     Vérifiez si la réplication du volume est à l’état « Arrêtée ».    |

## <a name="errors-deleting-snapshot"></a>Erreur de suppression d’instantané 

|     Message d’erreur    |     Résolution    |
|-|-|
|     `Snapshot   cannot be deleted, parent volume is a Data Protection volume with a   replication object`    |     Vérifiez si vous avez arrêté la réplication du volume si vous souhaitez supprimer cet instantané.    |
|     `Cannot delete   volume replication generated snapshot`    |     La suppression des instantanés de référence de réplication n’est pas autorisée.    |

## <a name="errors-resizing-volumes"></a>Erreurs de redimensionnement de volume

|     Message d’erreur    |     Résolution    |
|-|-|
|   La tentative de redimensionnement d’un volume source échoue avec l’erreur `"PoolSizeTooSmall","message":"Pool size too small for total volume size."`  |  Vérifiez que vous disposez de suffisamment d’espace dans les pools de capacité pour les volumes source et de destination de la réplication entre les régions. Lorsque vous redimensionnez le volume source, le volume de destination est automatiquement redimensionné. Toutefois, si le pool de capacité hébergeant le volume de destination n’a pas assez de marge, le redimensionnement des volumes source et de destination échouera. Consultez [Redimensionner un volume de destination de réplication entre régions](azure-netapp-files-resize-capacity-pools-or-volumes.md#resize-a-cross-region-replication-destination-volume) pour plus de détails.   |

## <a name="next-steps"></a>Étapes suivantes  

* [Réplication entre régions](cross-region-replication-introduction.md)
* [Configuration requise et considérations pour la réplication inter-région](cross-region-replication-requirements-considerations.md)
* [Créer une réplication de volume](cross-region-replication-create-peering.md)
* [Afficher l’état d’intégrité de la relation de réplication](cross-region-replication-display-health-status.md)
* [Gérer la reprise d’activité après sinistre](cross-region-replication-manage-disaster-recovery.md)
* [Redimensionner un volume de destination de réplication entre régions](azure-netapp-files-resize-capacity-pools-or-volumes.md#resize-a-cross-region-replication-destination-volume)
* [Résoudre les problèmes de réplication inter-région](troubleshoot-cross-region-replication.md)
