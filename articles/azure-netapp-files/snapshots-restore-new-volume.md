---
title: Restaurer un instantané sur un nouveau volume à l’aide d’Azure NetApp Files | Microsoft Docs
description: Décrit comment créer un volume à partir d’un instantané à l’aide d’Azure NetApp Files.
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
ms.date: 09/16/2021
ms.author: b-juche
ms.openlocfilehash: 1d6c74acffe68fb75fbad72a4e0ad052f4c4cef0
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132327421"
---
# <a name="restore-a-snapshot-to-a-new-volume-using-azure-netapp-files"></a>Restaurer un instantané sur un nouveau volume à l’aide d’Azure NetApp Files

Les [instantanés](snapshots-introduction.md) autorisent la récupération jusqu’à une date et heure des volumes. Actuellement, vous pouvez restaurer un instantané seulement sur un nouveau volume. 

## <a name="steps"></a>Étapes

1. Dans le panneau Volume, sélectionnez **Captures instantanées** pour afficher la liste des captures instantanées. 
2. Cliquez avec le bouton droit sur la capture instantanée, puis sélectionnez l’option de menu **Restaurer sur un nouveau volume**.  

    ![Capture d’écran montrant le menu Restaurer un nouveau volume.](../media/azure-netapp-files/azure-netapp-files-snapshot-restore-to-new-volume.png)

3. Dans la fenêtre Créer un volume, spécifiez les informations concernant le nouveau volume :  
    * **Nom**   
        Spécifiez le nom du volume que vous créez.  
        
        Le nom doit être unique au sein d’un groupe de ressources. Il doit comprendre au moins trois caractères.  Vous pouvez utiliser tous les caractères alphanumériques.

    * **Quota**  
        Spécifiez la quantité de stockage logique à allouer au volume.  

    ![Capture instantanée montrant la fenêtre Créer un volume.](../media/azure-netapp-files/snapshot-restore-new-volume.png) 

4. Cliquez sur **Vérifier + créer**.  Cliquez sur **Créer**.   
    Le nouveau volume utilise le même protocole que celui qu’utilise la capture instantanée.   
    Le nouveau volume sur lequel l’instantané est restauré apparaît dans le panneau Volumes.

## <a name="next-steps"></a>Étapes suivantes

* [En savoir plus sur les instantanés](snapshots-introduction.md)
* [Surveiller les métriques de volume et d’instantané](azure-netapp-files-metrics.md#volumes)
* [Résoudre les problèmes de stratégies d’instantanés](troubleshoot-snapshot-policies.md)
* [Limites des ressources pour Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Vidéo sur les instantanés Azure NetApp Files](https://www.youtube.com/watch?v=uxbTXhtXCkw)
* [Vue d’ensemble des instantanés Azure NetApp Files](https://anfcommunity.com/2021/01/31/azure-netapp-files-snapshot-overview/)
