---
title: Limites des ressources pour Azure NetApp Files | Microsoft Docs
description: Décrit les limites des ressources Azure NetApp Files et la manière de demander une augmentation de ces limites.
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
ms.topic: conceptual
ms.date: 11/09/2021
ms.author: b-juche
ms.openlocfilehash: 1565ab6ecd0fe1c2f79237115c08f8d14091c158
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132158990"
---
# <a name="resource-limits-for-azure-netapp-files"></a>Limites des ressources pour Azure NetApp Files

Comprendre les limites des ressources pour Azure NetApp Files vous permet de gérer vos volumes.

## <a name="resource-limits"></a>Limites des ressources

Le tableau suivant décrit les limites des ressources pour Azure NetApp Files :

|  Ressource  |  Limite par défaut  |  Ajustable via une demande de support  |
|----------------|---------------------|--------------------------------------|
|  [Quota de capacité régional par abonnement](regional-capacity-quota.md)   |  25 Tio  |  Oui  |
|  Nombre de comptes NetApp par région Azure par abonnement  |  10    |  Oui   |
|  Nombre de pools de capacités par compte NetApp   |    25     |   Oui   |
|  Nombre de volumes par abonnement   |    500     |   Oui   |
|  Nombre de volumes par pool de capacités     |    500   |    Oui     |
|  Nombre d’instantanés par volume       |    255     |    Non        |
|  Nombre de sous-réseaux délégués à Azure NetApp Files (Microsoft.NetApp/volumes) par réseau virtuel Azure    |   1   |    Non    |
|  Nombre d’adresses IP utilisées dans un réseau virtuel (notamment les réseaux virtuels immédiatement appairés) avec Azure NetApp Files   |    1 000   |    Non   |
|  Taille minimale d’un pool de capacités   |  4 Tio     |    Non  |
|  Taille maximale d’un pool de capacités    |  500 Tio   |   Non   |
|  Taille minimale d’un volume    |    100 Gio    |    Non    |
|  Taille maximale d’un volume     |    100 Tio    |    Non    |
|  Taille maximale d’un fichier unique     |    16 Tio    |    Non    |    
|  Taille maximale des métadonnées de répertoire dans un répertoire unique      |    320 Mo    |    Non    |    
|  Nombre maximal de fichiers dans un seul répertoire  | *Environ* 4 millions. <br> Voir [Déterminer si un répertoire approche de la taille limite](#directory-limit).  |    Non    |   
|  Nombre maximal de fichiers ([maxfiles](#maxfiles)) par volume     |    100 millions    |    Oui    |    
|  Nombre maximal de règles de stratégie d’exportation par volume     |    5  |    Non    | 
|  Débit minimum attribué pour un volume avec Qualité de service manuelle     |    1 Mio/s   |    Non    |    
|  Débit maximum attribué pour un volume avec Qualité de service manuelle     |    4 500 Mio/s    |    Non    |    
|  Nombre de volumes de protection des données de réplication inter-région (volumes de destination)     |    10    |    Oui    |     
|  Nombre maximum de sauvegardes basées sur des politiques (planifiées) par volume  | <ul><li> Nombre de rétention quotidienne : 1 (minimum) à 1019 (maximum) </li> <li> Nombre de rétention hebdomadaire : 1 (minimum) à 1019 (maximum) </li> <li> Nombre de rétention mensuelle : 1 (minimum) à 1019 (maximum) </ol></li> <br> Les nombres maximaux de rétentions de sauvegarde quotidiens, hebdomadaires et mensuels *combinés* sont 1019.  |  N  |
|  Taille maximale du volume protégé  |  100 Tio  |  N  |
|  Nombre maximal de volumes pouvant être sauvegardés par abonnement   |  5  |  Y  |
|  Nombre maximal de sauvegardes manuelles par volume et par jour |  5  |  Y  |

Pour plus d’informations, consultez [Questions fréquentes (FAQ) sur la gestion de la capacité](faq-capacity-management.md).

## <a name="determine-if-a-directory-is-approaching-the-limit-size"></a>Déterminer si un répertoire approche de la taille limite<a name="directory-limit"></a>  

Vous pouvez utiliser la commande `stat` à partir d’un client pour voir si la taille d’un annuaire approche de la limite maximale pour des métadonnées d’annuaire (320 Mo).   

Pour un annuaire de 320 Mo, les blocs sont au nombre de 655 360, d’une taille 512 octets chacun  (soit, 320 x 1 024 x 1 024/512). Ce nombre se traduit par environ 4 millions de fichiers au maximum pour un annuaire de 320 Mo. Toutefois, le nombre de fichiers maximal réel peut être inférieur, en fonction de facteurs tels que le nombre de fichiers contenant des caractères non-ASCII dans l’annuaire. Par conséquent, vous devez utiliser la commande `stat` comme suit pour déterminer si votre annuaire approche de sa limite.  

Exemples :

```console
[makam@cycrh6rtp07 ~]$ stat bin
File: 'bin'
Size: 4096            Blocks: 8          IO Block: 65536  directory

[makam@cycrh6rtp07 ~]$ stat tmp
File: 'tmp'
Size: 12288           Blocks: 24         IO Block: 65536  directory
 
[makam@cycrh6rtp07 ~]$ stat tmp1
File: 'tmp1'
Size: 4096            Blocks: 8          IO Block: 65536  directory
```

## <a name="maxfiles-limits"></a>Limites maxfiles <a name="maxfiles"></a> 

Les volumes Azure NetApp Files ont une limite appelée *maxfiles*. La limite maxfiles correspond au nombre de fichiers qu’un volume peut contenir. Les systèmes de fichiers Linux se réfèrent à la limite en tant qu’*inodes*. La limite maxfiles d’un volume Azure NetApp Files est indexée en fonction de la taille (du quota) du volume. La limite maxfiles d’un volume augmente ou diminue au rythme de 20 millions de fichiers par Tio de taille de volume provisionnée. 

Le service ajuste dynamiquement la limite maxfiles d’un volume en fonction de sa taille provisionnée. Par exemple, un volume configuré initialement avec une taille de 1 Tio a une limite maxfiles de 20 millions. Les modifications ultérieures apportées à la taille du volume entraînent une réajustement automatique de la limite maxfiles selon les règles suivantes : 

|    Taille du volume (quota)     |  Réajustement automatique de la limite maxfiles    |
|----------------------------|-------------------|
|    <= 1 Tio                |    20 millions     |
|    > 1 Tio mais <= 2 Tio    |    40 millions     |
|    > 2 Tio mais <= 3 Tio    |    60 millions     |
|    > 3 Tio mais <= 4 Tio    |    80 millions     |
|    > 4 Tio                 |    100 millions    |

Si vous avez déjà alloué au moins 4 Tio de quota pour un volume, vous pouvez initier une [demande de support](#request-limit-increase) afin d’augmenter la limite maxfiles (inodes) au-delà de 100 millions. Tous les 100 millions de fichiers que vous augmentez (ou une fraction de ceux-ci), vous devez augmenter le quota de volume correspondant de 4 Tio.  Par exemple, si vous augmentez la limite maxfiles de 100 millions de fichiers à 200 millions de fichiers (ou n’importe quel nombre dans cette plage), vous devez augmenter le quota de volume de 4 Tio à 8 Tio.

Vous pouvez augmenter la limite maxfiles à 500 millions si votre quota de volume est d’au moins 20 Tio. <!-- ANF-11854 --> 

## <a name="request-limit-increase"></a>Augmentation de la limite de demandes

Vous pouvez créer une demande de support Azure pour augmenter les limites ajustables à partir du tableau [Limites de ressource](#resource-limits). 

1. Sous **Nouvelle demande de support**, sélectionnez **Suport + dépannage**.   

2. Sous l’onglet **Description du problème**, fournissez les informations requises :
    1. Pour **Type de problème**, sélectionnez **Limites du service et de l’abonnement (quotas)** .
    2. Pour **Abonnement**, sélectionnez votre abonnement. 
    3. Pour **Type de quota**, sélectionnez **Stockage : limites d’Azure NetApp Files**.  

    ![Capture d’écran montrant l’onglet Description du problème.](../media/azure-netapp-files/support-problem-descriptions.png)

3. Sous l’onglet **Détails supplémentaires**, cliquez sur **Entrer les détails** dans le champ Détails de la demande.  

    ![Capture d’écran montrant l’onglet Détails et le champ Entrer les détails.](../media/azure-netapp-files/quota-additional-details.png)

4. Pour demander une augmentation de la limite, fournissez les informations suivantes dans la fenêtre Détails du quota qui s’affiche :
    1. Sous **Type de quota**, sélectionnez le type de ressource que vous souhaitez augmenter.  
        Par exemple :   
        * *Quota de capacité régional par abonnement (TiB)*
        * *Nombre de comptes NetApp par région Azure par abonnement*
        * *Nombre de volumes par abonnement*

    2. Dans **Région demandée**, sélectionnez votre région.   
        Les tailles actuelles et par défaut sont affichées sous État du quota.
    
    3. Entrez une valeur pour demander une augmentation pour le type de quota que vous avez spécifié.
    
    ![Capture d’écran montrant comment afficher et demander une augmentation du quota régional.](../media/azure-netapp-files/quota-details-regional-request.png)

5. Cliquez sur **Enregistrer et continuer**. Cliquez sur **Vérifier + créer** pour créer la demande.

## <a name="next-steps"></a>Étapes suivantes  

- [Comprendre la hiérarchie de stockage d’Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
- [Modèle de coût pour Azure NetApp Files](azure-netapp-files-cost-model.md)
- [Quota de capacité régionale pour Azure NetApp Files](regional-capacity-quota.md)
- [Demander l’accès à une région pour Azure NetApp Files](request-region-access.md)
