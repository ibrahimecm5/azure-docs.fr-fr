---
title: Créer un instantané à la demande à l’aide de Azure NetApp Files | Microsoft Docs
description: Décrit comment créer des captures instantanées à la demande à l’aide d’Azure NetApp Files.
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
ms.topic: how-to
ms.date: 10/25/2021
ms.author: b-juche
ms.openlocfilehash: c6bbc11fc77de516d2e163d1463444f2a3c0210e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131069935"
---
# <a name="create-an-on-demand-snapshot-for-a-volume"></a>Créer un instantané à la demande pour un volume

Azure NetApp Files prend en charge la création de [captures instantanées](snapshots-introduction.md) à la demande et l’utilisation de stratégies de capture instantanée pour planifier la création automatique de captures instantanées. Vous pouvez également restaurer un [instantané sur un nouveau volume](snapshots-restore-new-volume.md), [restaurer un fichier unique à l’aide d’un client](snapshots-restore-file-client.md), ou [rétablir un volume existant à l’aide d’un instantané](snapshots-revert-volume.md). Cet article explique comment créer un instantané à la demande pour un volume. 

> [!NOTE] 
> * Pour plus d’informations sur la gestion des instantanés dans la réplication entre les régions, consultez [Configuration requise et considérations pour la réplication entre régions](cross-region-replication-requirements-considerations.md).
> * Vous pouvez utiliser l’outil Azure Application Consistent Snapshot (AzAcSnap) afin d’activer la protection des données pour les bases de données tierces. Pour plus d’informations, consultez la [présentation de l’outil Azure Application Consistent Snapshot](azacsnap-introduction.md).
 
## <a name="steps"></a>Étapes

1.  Accédez au volume pour lequel vous souhaitez créer une capture instantanée. Cliquez sur **Captures instantanées**.

    ![Capture d’écran montrant comment accéder au panneau des captures instantanées.](../media/azure-netapp-files/azure-netapp-files-navigate-to-snapshots.png)

2.  Cliquez sur **+ Ajouter une capture instantanée** afin de créer une capture instantanée à la demande pour un volume.

    ![Capture d’écran montrant comment ajouter une capture instantanée.](../media/azure-netapp-files/azure-netapp-files-add-snapshot.png)

3.  Dans la fenêtre Nouvel instantané, spécifiez un nom pour l’instantané que vous créez.   

    ![Capture d’écran montrant la Nouvelle fenêtre de capture instantanée.](../media/azure-netapp-files/azure-netapp-files-new-snapshot.png)

4. Cliquez sur **OK**. 

## <a name="next-steps"></a>Étapes suivantes

* [En savoir plus sur les captures instantanées](snapshots-introduction.md)
* [Gérer les stratégies de captures instantanées avec Azure NetApp Files](snapshots-manage-policy.md)
* [Limites des ressources pour Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Vidéo sur les instantanés Azure NetApp Files](https://www.youtube.com/watch?v=uxbTXhtXCkw&feature=youtu.be)
* [Qu’est-ce que l’outil Azure Application Consistent Snapshot Tool ?](azacsnap-introduction.md)
