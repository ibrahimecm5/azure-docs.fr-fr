---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: rogarana
ms.openlocfilehash: 70fa104ef285ecffe11940d04deb451a9cdf39ee
ms.sourcegitcommit: 1a0fe16ad7befc51c6a8dc5ea1fe9987f33611a1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131866718"
---
Les modifications apportées au partage de fichiers Azure avec le portail Azure ou SMB ne sont pas immédiatement détectées et répliquées comme le sont des modifications apportées au point de terminaison de serveur. Azure Files n’a pas encore de notifications ou journalisation des modifications. Il n’existe donc aucun moyen de lancer automatiquement une session de synchronisation lorsque des fichiers sont modifiés. Sur Windows Server, Azure File Sync utilise la [journalisation du nombre de séquences de mise à jour de Windows](/windows/win32/fileio/change-journals) pour lancer automatiquement une session de synchronisation quand des fichiers changent.

Pour détecter les modifications apportées au partage de fichiers Azure, Azure File Sync a une tâche planifiée appelée *tâche de détection des modifications*. Une tâche de détection des modifications énumère tous les fichiers inclus dans le partage de fichiers, puis les compare à la version de synchronisation de ces fichiers. Lorsque la tâche de détection des modifications détermine que des fichiers ont changé, Azure File Sync lance une session de synchronisation. La tâche de détection des modifications est lancée toutes les 24 heures. Étant donné que la tâche de détection des modifications fonctionne en énumérant chaque fichier dans le partage de fichiers Azure, elle prend plus de temps dans les espaces de noms de grande taille que dans les plus petits. Pour des espaces de noms de grande taille, plus de 24 heures peuvent être nécessaires pour déterminer les fichiers qui ont été modifiés.

Pour synchroniser immédiatement les fichiers qui ont été modifiés dans le partage de fichiers Azure, le cmdlet PowerShell **Invoke-AzStorageSyncChangeDetection** permet de lancer manuellement la détection des modifications apportées au partage de fichiers Azure. Cette cmdlet est destinée aux scénarios dans lesquels un certain type de processus automatisé apporte des modifications au partage de fichiers Azure ou dans lesquels les modifications sont apportées par un administrateur (comme le déplacement de fichiers et de répertoires dans le partage). Pour les changements d’utilisateurs finaux, il est recommandé d’installer l’agent Azure File Sync dans une machine virtuelle IaaS et de demander aux utilisateurs finaux d’accéder au partage de fichiers via la machine virtuelle IaaS. Ainsi, toutes les modifications sont rapidement synchronisées avec d’autres agents, sans qu’il soit nécessaire d’utiliser la cmdlet Invoke-AzStorageSyncChangeDetection. Pour plus d’informations, consultez la documentation [Invoke-AzStorageSyncChangeDetection](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection).

>[!NOTE]
>L’applet de commande PowerShell **Invoke-AzStorageSyncChangeDetection** ne peut détecter qu’un maximum de 10 000 éléments. Pour les autres limitations, consultez la documentation [Invoke-AzStorageSyncChangeDetection](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection).

>[!NOTE]
>Les modifications apportées à un partage de fichiers Azure avec REST ne mettent pas à jour l’heure de dernière modification de SMB et ne sont pas visibles en tant que modifications par synchronisation.

Nous étudions la possibilité d’ajouter la détection des modifications pour un partage de fichiers Azure de la même façon que le nombre de séquences de mise à jour (USN) pour les volumes sur Windows Server. Aidez-nous à faire passer le développement de cette fonctionnalité en priorité en votant pour celle-ci via la page [Commentaires de la communauté Azure](https://feedback.azure.com/d365community/idea/26f8aa9d-3725-ec11-b6e6-000d3a4f0f84).
