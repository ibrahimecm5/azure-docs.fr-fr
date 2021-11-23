---
title: Configurer des sauvegardes manuelles pour les fichiers NetApp Azure | Microsoft Docs
description: Décrit comment configurer des sauvegardes manuelles pour des volumes de Azure NetApp Files.
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
ms.date: 11/10/2021
ms.author: b-juche
ms.openlocfilehash: 5b5d2cafbbd70e63e2b3a039e94f1fc66106203c
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132319589"
---
# <a name="configure-manual-backups-for-azure-netapp-files"></a>Configurer des sauvegardes manuelles pour les fichiers NetApp Azure 

La sauvegarde Azure NetApp Files prend en charge les sauvegardes *basées sur des stratégies* (planifiées) et les sauvegardes *manuelles* (à la demande) au niveau du volume. Vous pouvez utiliser les deux types de sauvegarde dans le même volume. Pendant le processus de configuration, vous allez activer la fonctionnalité de sauvegarde pour un volume Azure NetApp Files avant de pouvoir effectuer des sauvegardes manuelles ou des sauvegardes basées sur des stratégies. 

Cet article vous montre comment configurer les sauvegardes manuelles. Pour obtenir une configuration de sauvegarde basée sur des stratégies, consultez [configurer des sauvegardes basées sur des stratégies](backup-configure-policy-based.md).  

> [!IMPORTANT]
> La fonctionnalité de sauvegarde d’Azure NetApp Files est actuellement en préversion. Vous devez envoyer une demande de liste d’attente pour accéder à la fonctionnalité via la page **[Préversion publique de la sauvegarde Azure NetApp Files](https://aka.ms/anfbackuppreviewsignup)** . Attendez de recevoir un e-mail de confirmation officiel de l’équipe Azure NetApp Files avant d’utiliser la fonctionnalité de sauvegarde Azure NetApp Files.

## <a name="about-manual-backups"></a>À propos des sauvegardes manuelles  

Chaque volume Azure NetApp Files doit disposer de la fonctionnalité de sauvegarde activée avant toute sauvegarde (basée sur une stratégie ou manuelle).   

Après avoir activé la fonctionnalité de sauvegarde, vous avez la possibilité de sauvegarder manuellement un volume. Une sauvegarde manuelle prend un instantané à un moment donné du système de fichiers actif et sauvegarde cette capture instantanée sur le compte de stockage Azure.

La liste suivante récapitule les comportements de sauvegarde manuelle :  

* Vous pouvez créer des sauvegardes manuelles sur un volume même si le volume est déjà activé pour la sauvegarde et configuré avec des stratégies de sauvegarde.  Toutefois, il ne peut y avoir qu’une seule demande de sauvegarde manuelle en suspens pour le volume. Si vous affectez une stratégie de sauvegarde et si le transfert de ligne de base est toujours en cours, la création d’une sauvegarde manuelle est bloquée jusqu’à ce que le transfert de base de référence soit terminé.

* À moins que vous ne spécifiiez un instantané existant à utiliser pour une sauvegarde, la création d’une sauvegarde manuelle génère automatiquement un instantané sur le volume. L’instantané est ensuite transféré vers le stockage Azure. L’instantané créé sur le volume est conservé jusqu’à la création de la sauvegarde manuelle suivante. Au cours de l’opération de sauvegarde manuelle suivante, les instantanés plus anciens seront nettoyés. Vous ne pouvez pas supprimer l’instantané généré pour la dernière sauvegarde manuelle. 

## <a name="enable-backup-functionality"></a>Activer les fonctionnalités de sauvegarde

Si vous ne l’avez pas fait, activez la fonctionnalité de sauvegarde pour le volume avant de créer des sauvegardes manuelles : 

1. Allez dans **Volumes** et sélectionnez le volume pour lequel vous voulez activer la sauvegarde.
2. Sélectionnez **Configurer**.
3. Dans la page Configuration des sauvegardes, faites basculer le paramètre **Activé** sur **On**.   
    Le champ du coffre est prérempli. 
4. Cliquez sur **OK**.   

![Capture d’écran montrant le paramètre activé de la fenêtre Configurer les sauvegardes.](../media/azure-netapp-files/backup-configure-enabled.png)

## <a name="create-a-manual-backup-for-a-volume"></a>Créer une sauvegarde manuelle d’un volume

1. Allez dans **Volumes** et sélectionnez le volume pour lequel vous voulez créer une sauvegarde manuelle.
2. Sélectionnez **Ajouter une sauvegarde**.
3. Dans la fenêtre Nouvelle sauvegarde qui s’affiche :   

    1. Spécifiez un nom de sauvegarde dans le champ **Nom**.   
    
        * Les noms de sauvegarde manuelle doivent contenir entre 3 et 256 caractères.   
        * En tant que meilleure pratique, il est recommandé d’ajouter un préfixe au format suivant avant le nom réel de la sauvegarde. Cela vous permet d’identifier la sauvegarde manuelle si le volume est supprimé (avec les sauvegardes conservées).   

            `NetAppAccountName-CapacityPoolName-VolumeName`   

            Par exemple, supposons que le compte NetApp est `account1` , le pool de capacité est `pool1` , le nom du volume est `vol1` . Une sauvegarde manuelle peut être nommée comme suit :    

            `account1-pool1-vol1-backup1`   

            Si vous utilisez une forme plus petite pour le nom de la sauvegarde, assurez-vous qu’elle contient toujours des informations qui identifient le compte NetApp, le pool de capacité et le nom du volume à afficher dans la liste de sauvegarde.
            
    2. Si vous souhaitez utiliser un instantané existant pour la sauvegarde, sélectionnez l’option **utiliser un instantané existant**.  Lorsque vous utilisez cette option, assurez-vous que le champ Nom correspond au nom d’instantané existant utilisé pour la sauvegarde. 

4. Cliquez sur **Créer**. 

    Lorsque vous créez une sauvegarde manuelle, un instantané est également créé sur le volume à l’aide du même nom que celui que vous avez spécifié pour la sauvegarde. Cet instantané représente l'état actuel du système de fichiers actif. Il est transféré vers le stockage Azure. Une fois la sauvegarde terminée, l’entrée de sauvegarde manuelle apparaît dans la liste des sauvegardes du volume.

![Capture d’écran montrant la fenêtre Nouvelle sauvegarde.](../media/azure-netapp-files/backup-new.png)


## <a name="next-steps"></a>Étapes suivantes  

* [Présentation de la sauvegarde Azure NetApp Files](backup-introduction.md)
* [Exigences et considérations relatives à la sauvegarde Azure NetApp Files](backup-requirements-considerations.md)
* [Limites des ressources pour Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Configuration de sauvegardes basées sur une stratégie](backup-configure-policy-based.md)
* [Gestion des stratégies de sauvegarde](backup-manage-policies.md)
* [Recherche dans les sauvegardes](backup-search.md)
* [Restaurer une sauvegarde sur un nouveau volume](backup-restore-new-volume.md)
* [Désactivation de la fonctionnalité de sauvegarde pour un volume](backup-disable.md)
* [Supprimer des sauvegardes d’un volume](backup-delete.md)
* [Métriques de sauvegarde d’un volume](azure-netapp-files-metrics.md#volume-backup-metrics)
* [FAQ sur la sauvegarde Azure NetApp Files](faq-backup.md)


