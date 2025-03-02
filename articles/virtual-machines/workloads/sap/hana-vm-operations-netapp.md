---
title: Configuration ANF des machines virtuelles SAP HANA Azure | Microsoft Docs
description: Recommandations en matière de stockage Azure NetApp Files pour SAP HANA.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP, Azure, ANF, HANA, Azure NetApp Files, capture instantanée
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/08/2021
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bb96e0da4e9e724220492821f1418f3d587b63e8
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130222909"
---
# <a name="nfs-v41-volumes-on-azure-netapp-files-for-sap-hana"></a>Volumes NFS v4.1 sur Azure NetApp Files pour SAP HANA

Azure NetApp Files fournit des partages NFS natifs qui peuvent être utilisés pour les volumes **/hana/shared**, **/hana/data** et **/hana/log**. L’utilisation de partages NFS basés sur ANF pour les volumes **/hana/data** et **/hana/log** nécessite l’utilisation du protocole NFS v4.1. Le protocole NFS v3 n’est pas pris en charge pour l’utilisation de volumes **/hana/data** et **/hana/log** quand les partages sont basés sur ANF. 


> [!IMPORTANT]
> L’utilisation du protocole NFS v3 implémenté sur Azure NetApp Files n’est **pas** prise en charge pour **/hana/data** et **/hana/log**. L’utilisation du NFS 4.1 est obligatoire pour les volumes **/hana/data** et **/hana/log** d’un point de vue fonctionnel. En revanche, pour le volume **/hana/shared**, les systèmes NFS v3 ou NFS v4.1 peuvent tous eux être utilisés d’un point de vue fonctionnel.

## <a name="important-considerations"></a>Points importants à prendre en compte

Lorsque vous envisagez d’utiliser Azure NetApp Files pour SAP Netweaver et SAP HANA, tenez compte des importantes considérations suivantes :

- La taille de pool de capacité minimale est de 4 Tio.  
- La taille de volume minimale est de 100 Gio
- Azure NetApp Files et toutes les machines virtuelles, où les volumes Azure NetApp Files sont montés, doivent être déployés dans le même réseau virtuel Azure ou dans des [réseaux virtuels appairés](../../../virtual-network/virtual-network-peering-overview.md) de la même région.
- Il est important que les machines virtuelles soient déployées à proximité du stockage Azure NetApp pour des raisons de faible latence.  
- Le réseau virtuel sélectionné doit avoir un sous-réseau délégué à Azure NetApp Files.
- Vérifiez que la latence entre le serveur de base de données et le volume ANF est mesurée et inférieure à 1 milliseconde.
- Le débit d’un volume NetApp Azure est une fonction du quota de volume et du niveau de service, comme décrit dans [Niveau de service pour Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-service-levels.md). Lors du dimensionnement de volumes HANA Azure NetApp, assurez-vous que le débit obtenu répond à la configuration système requise pour HANA. Vous pouvez également envisager d’utiliser un [pool de capacité QoS manuel](../../../azure-netapp-files/azure-netapp-files-understand-storage-hierarchy.md#manual-qos-type) dans lequel la capacité et le débit du volume peuvent être configurés et mis à l’échelle indépendamment (pour découvrir des exemples spécifiques relatifs à la plateforme SAP Hana, consultez [ce document](../../../azure-netapp-files/azure-netapp-files-understand-storage-hierarchy.md#manual-qos-type))
- Essayez de « consolider » les volumes pour obtenir des performances plus élevées dans un volume plus grand ; par exemple, utilisez un volume pour /sapmnt, /usr/sap/trans, … dans la mesure du possible.  
- Azure NetApp Files propose une [stratégie d’exportation](../../../azure-netapp-files/azure-netapp-files-configure-export-policy.md) : vous pouvez contrôler les clients autorisés, le type d’accès (lecture et écriture, lecture seule, etc.). 
- La fonctionnalité Azure NetApp Files ne tient pas encore compte des zones. Actuellement, la fonctionnalité Azure NetApp Files n’est pas déployée dans toutes les zones de disponibilité d’une région Azure. Méfiez-vous de l’impact potentiel sur les temps de latence dans certaines régions Azure.   
- L’ID d’utilisateur pour <b>sid</b>adm et l’ID de groupe pour `sapsys` sur les machines virtuelles doivent correspondre à la configuration dans Azure NetApp Files. 

> [!IMPORTANT]
> Pour les charges de travail SAP HANA, une latence faible est critique. Collaborez avec votre représentant Microsoft pour vous assurer que les machines virtuelles et les volumes Azure NetApp Files soient déployés à proximité.  

> [!IMPORTANT]
> En cas d’incompatibilité entre l’ID d’utilisateur pour <b>sid</b>adm et l’ID de groupe pour `sapsys` entre la machine virtuelle et la configuration Azure NetApp, les autorisations pour les fichiers sur les volumes NetApp Azure, montés sur les machines virtuelles, s’afficheront en tant que `nobody`. Veillez à spécifier l’ID d’utilisateur correct pour <b>sid</b>adm et l’ID de groupe pour `sapsys`, lors de l’[intégration d’un nouveau système](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u) à Azure NetApp Files.


## <a name="sizing-for-hana-database-on-azure-netapp-files"></a>Dimensionnement de la base de données HANA sur Azure NetApp Files

Le débit d’un volume NetApp Azure est fonction de la taille du volume et du niveau de service, comme décrit dans [Niveaux de service pour Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-service-levels.md). 

Il est important de comprendre la relation entre les performance et la taille, et qu’un point de terminaison de stockage du service présente des limites physiques. Chaque point de terminaison de stockage est injecté de façon dynamique dans le [sous-réseau délégué Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md) lors de la création du volume, et reçoit une adresse IP. Selon la capacité disponible et la logique de déploiement, des volumes Azure NetApp Files peuvent partager un point de terminaison de stockage

Le tableau ci-dessous montre qu’il peut être judicieux de créer un grand volume « Standard » pour stocker les sauvegardes, et qu’il n’est pas judicieux de créer un volume « Ultra » d’une taille supérieure à 12 To, car la capacité de bande passante physique maximale d’un volume unique serait dépassée. 

Le débit d’écriture maximal pour un volume et une session Linux unique est compris entre 1,2 et 1,4 Go/s. Si vous avez besoin d’un débit plus élevé pour /hana/data, vous pouvez utiliser le partitionnement de volume de données SAP HANA pour agréger par bandes l’activité d’E/S pendant le rechargement des données ou les points d’enregistrement HANA sur plusieurs fichiers de données HANA situés sur plusieurs partages NFS. Pour améliorer le débit de lecture, l’option de montage nconnect NFS peut être utilisée. Pour plus d’informations sur les performances du service Azure NetApp Files et nconnect, consultez l’article [Bonnes pratiques pour les options de montage NFS Linux pour Azure NetApp Files](../../../azure-netapp-files/performance-linux-mount-options.md). Pour plus d’informations sur l’agrégation par bandes des volumes de données HANA, consultez ces articles :

- [Guide de l’administrateur HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.05/en-US/40b2b2a880ec4df7bac16eae3daef756.html?q=hana%20data%20volume%20partitioning)
- [Blog sur SAP HANA – Partitionnement des volumes de données](https://blogs.sap.com/2020/10/07/sap-hana-partitioning-data-volumes/)
- [Note SAP N° 2400005](https://launchpad.support.sap.com/#/notes/2400005)
- [Note SAP n° 2700123](https://launchpad.support.sap.com/#/notes/2700123)


| Taille  | Débit Standard | Débit Premium | Débit Ultra |
| --- | --- | --- | --- |
| 1 To | 16 Mo/s | 64 Mo/s | 128 Mo/s |
| 2 To | 32 Mo/s | 128 Mo/s | 256 Mo/s |
| 4 To | 64 Mo/s | 256 Mo/s | 512 Mo/s |
| 10 To | 160 Mo/s | 640 Mo/s | 1 280 Mo/s |
| 15 To | 240 Mo/s | 960 Mo/s | 1 400 Mo/sec<sup>1</sup> |
| 20 To | 320 Mo/s | 1 280 Mo/s | 1 400 Mo/sec<sup>1</sup> |
| 40 To | 640 Mo/s | 1 400 Mo/sec<sup>1</sup> | 1 400 Mo/sec<sup>1</sup> |

<sup>1</sup> : limites de débit de lecture d’écriture ou de session unique (si l’option de montage NFS nconnect n’est pas utilisée) 

Il est important de comprendre que les données sont écrites sur les mêmes disques SSD sur le back-end de stockage. Le quota de performances du pool de capacité a été créé afin de pouvoir gérer l’environnement.
Les KPI de stockage sont identiques pour toutes les tailles de base de données HANA. Dans presque tous les cas, cette hypothèse ne reflète pas la réalité et les attentes du client. La taille des systèmes HANA ne signifie pas nécessairement qu’un petit système nécessite un faible débit de stockage et qu’un grand système nécessite un débit de stockage élevé, mais en général nous pouvons nous attendre à des exigences de débit plus élevées pour les instances de base de données HANA plus volumineuses. En raison des règles de dimensionnement de SAP pour le matériel sous-jacent, ces instances HANA plus volumineuses fournissent également davantage de ressources de processeur et un parallélisme plus élevé dans des tâches telles que le chargement des données après un redémarrage d’instances. Par conséquent, les tailles de volume adoptées doivent répondre aux attentes et aux besoins des clients, et ne doivent pas être motivées uniquement par de pures exigences de capacité.

Lorsque vous concevez l’infrastructure pour SAP dans Azure, vous devez connaître certaines exigences de débit de stockage minimal (pour les systèmes de production) de SAP, qui se traduisent par les caractéristiques de débit minimal suivantes :

| Type de volume et type d’E/S | KPI minimal exigé par SAP | Niveau de service Premium | Niveau de service Ultra |
| --- | --- | --- | --- |
| Écriture du volume de journal | 250 Mo/s | 4 To | 2 To |
| Écriture du volume de données | 250 Mo/s | 4 To | 2 To |
| Lecture du volume de données | 400 Mo/s | 6,3 To | 3,2 To |

Étant donné que les trois KPI sont exigés, le volume **/hana/data** doit être dimensionné vers la plus grande capacité afin de respecter les exigences de lecture minimales. Lorsque vous utilisez des pools de capacité QoS manuels, la taille et le débit des volumes peuvent être définis indépendamment. Étant donné que la capacité et le débit sont établis à partir du même pool de capacité, le niveau de service et la taille du pool doivent être suffisamment importants pour fournir les performances totales attendues (voir l’exemple [ici](../../../azure-netapp-files/azure-netapp-files-understand-storage-hierarchy.md#manual-qos-type))

Pour les systèmes HANA, qui ne nécessitent pas de bande passante élevée, le débit du volume ANF peut être réduit d’une taille de volume inférieure ou, en cas de QoS manuelle, en ajustant le débit directement. Dans le cas où un système HANA nécessite un débit plus élevé, le volume peut être adapté en redimensionnant la capacité en ligne. Aucun KPI n’est défini pour les volumes de sauvegarde. Toutefois, le débit du volume de sauvegarde est essentiel pour un environnement performant. Les performances des volumes de journal et de données doivent être conçues afin de répondre aux attentes des clients.

> [!IMPORTANT]
> Indépendamment de la capacité que vous déployez sur un volume NFS unique, le débit est supposé atteindre un plateau dans la plage de bande passante de 1,2 à 1,4 Go/s utilisée par un consommateur au cours d’une session. Cela concerne l’architecture sous-jacente de l’offre ANF et aux limites de session Linux associées relatives à NFS. Les valeurs de performances et de débit décrites dans l’article [Résultats des tests de performances pour Azure NetApp Files](../../../azure-netapp-files/performance-benchmarks-linux.md) ont été effectuées sur un volume NFS partagé avec plusieurs machines virtuelles clientes et, par conséquent, avec plusieurs sessions. Ce scénario est différent du scénario que nous mesurons dans SAP. Nous y mesurons le débit d’une seule machine virtuelle par rapport à un volume NFS. Hébergé sur ANF.

Pour respecter les exigences de débit minimal SAP pour les données et le journal, et conformément aux instructions pour **/hana/shared**, les tailles recommandées ressemblent à ceci :

| Volume | Taille<br /> Niveau de stockage Premium | Taille<br /> Niveau de stockage Ultra | Protocole NFS pris en charge |
| --- | --- | --- |
| /hana/log/ | 4 Tio | 2 Tio | v4.1 |
| /hana/data | 6,3 Tio | 3,2 Tio | v4.1 |
| /hana/shared scale-up | Min (1 To, 1 x RAM)  | Min (1 To, 1 x RAM) | v3 ou v4.1 |
| /hana/shared scale-out | 1 x RAM de nœud Worker<br /> par quatre nœuds Worker  | 1 x RAM de nœud Worker<br /> par quatre nœuds Worker  | v3 ou v4.1 |
| /hana/logbackup | 3 x RAM  | 3 x RAM | v3 ou v4.1 |
| /hana/backup | 2 x RAM  | 2 x RAM | v3 ou v4.1 |

Pour tous les volumes, NFS v4.1 est fortement recommandé.

Les tailles des volumes de sauvegarde sont des estimations. Des exigences précises doivent être définies en fonction de la charge de travail et des processus d’exploitation. Pour les sauvegardes, vous pouvez consolider de nombreux volumes pour différentes instances de SAP HANA sur un (ou deux) volumes plus grands, qui peuvent avoir un niveau de service ANF inférieur.

> [!NOTE]
> Les recommandations relatives aux tailles Azure NetApp Files qui sont indiquées dans ce document ciblent la configuration minimale stipulée par SAP à ses fournisseurs d’infrastructure. Dans les déploiements de clients et les scénarios de charge de travail réels, cela peut ne pas suffire. Prenez ces recommandations en tant que point de départ et adaptez-les en fonction des exigences de votre charge de travail spécifique.  

Par conséquent, vous pouvez envisager de déployer un débit similaire pour les volumes ANF tels qu’ils sont déjà listés pour les disques de stockage Ultra. Prenez également en compte les tailles listées pour les volumes des différentes références SKU de machine virtuelle, comme déjà indiqué dans les tables de disques Ultra.

> [!TIP]
> Vous pouvez redimensionner les volumes Azure NetApp Files de manière dynamique, sans avoir à `unmount` les volumes, à arrêter les machines virtuelles ou à arrêter SAP HANA. Cela permet de répondre aux exigences à la fois attendues et imprévues de votre application en matière de débit.

La documentation sur le déploiement d’une configuration de scale-out de SAP HANA avec un nœud de secours à l’aide de volumes NFS v4.1 hébergés dans ANF est publiée dans [Scale-out de SAP HANA avec le nœud de secours sur les machines virtuelles Azure avec Azure NetApp Files sur SUSE Linux Enterprise Server](./sap-hana-scale-out-standby-netapp-files-suse.md).


## <a name="availability"></a>Disponibilité
Les mises à jour et mises à niveau du système ANF sont appliquées sans affecter l’environnement du client. Le [contrat SLA défini est de 99,99 %](https://azure.microsoft.com/support/legal/sla/netapp/).


## <a name="volumes-and-ip-addresses-and-capacity-pools"></a>Volumes et adresses IP et pools de capacité
Avec ANF, il est important de comprendre comment l’infrastructure sous-jacente est créée. Un pool de capacité n’est qu’une construction qui fournit un budget de capacité et de performance, ainsi qu’une unité de facturation, en fonction de son niveau de service. Un pool de capacité n’a pas de relation physique avec l’infrastructure sous-jacente. Lorsque vous créez un volume sur le service, un point de terminaison de stockage est créé. Une adresse IP unique est attribuée à ce point de terminaison de stockage pour fournir un accès aux données du volume. Si vous créez plusieurs volumes, ceux-ci sont distribués au sein du parc de matériel nu sous-jacent lié à ce point de terminaison de stockage. ANF a une logique qui distribue automatiquement les charges de travail des clients une fois que les volumes et/ou la capacité du stockage configuré atteignent un niveau prédéfini interne. Vous pouvez rencontrer de tels cas, car un nouveau point de terminaison de stockage, avec une nouvelle adresse IP, est créé automatiquement pour accéder aux volumes. Le service Azure NetApp Files (ANF) ne permet pas au client de contrôler cette logique de distribution.

## <a name="log-volume-and-log-backup-volume"></a>Volume du journal et volume de sauvegarde du journal
Le « volume du journal » ( **/hana/log**) est utilisé pour écrire le journal de phase de restauration par progression en ligne. Il existe donc des fichiers ouverts sur ce volume, et cela n’a pas de sens de créer une capture instantanée de ce volume. Les fichiers journaux de phase de restauration par progression en ligne sont archivés ou sauvegardés sur le volume de sauvegarde du journal une fois que le fichier journal de phase de restauration par progression en ligne est plein ou qu’une sauvegarde du journal de phase de restauration par progression est exécutée. Pour garantir des performances de sauvegarde raisonnables, le volume de sauvegarde du journal nécessite un bon débit. Pour optimiser les coûts de stockage, il peut être judicieux de consolider le volume de sauvegarde du journal de plusieurs instances HANA, afin que plusieurs instances HANA utilise le même volume et écrivent leurs sauvegardes dans des répertoires différents. Avec une telle consolidation, vous pouvez obtenir un débit plus élevé, car vous devez augmenter la taille du volume. 

Il en va de même pour le volume sur lequel vous écrivez des sauvegardes complètes de base de données HANA.  
 

## <a name="backup"></a>Sauvegarde
En plus des sauvegardes en streaming et de la sauvegarde de bases de données SAP HANA par le service Sauvegarde Azure, comme décrit dans l’article [Guide de sauvegarde pour SAP HANA sur Machines Virtuelles Azure](../../../backup/sap-hana-db-about.md), Azure NetApp Files offre la possibilité d’effectuer des sauvegardes de captures instantanées basées sur le stockage. 

SAP HANA prend en charge :

- Prise en charge des sauvegardes de captures instantanées basées sur le stockage pour un système à conteneur unique avec SAP HANA 1.0 SPS7 et versions ultérieures 
- Prise en charge des sauvegardes de captures instantanées basées sur le stockage pour les environnements HANA de conteneur de plusieurs bases de données avec un seul tenant (locataire) avec SAP HANA 2.0 SPS1 et versions ultérieures
- Prise en charge des sauvegardes de captures instantanées basées sur le stockage pour les environnements HANA de conteneur de plusieurs bases de données avec plusieurs tenants (locataires) avec SAP HANA 2.0 SPS4 et versions ultérieures


La création de sauvegardes de captures instantanées basées sur le stockage est une procédure simple en quatre étapes. 
1. Création d’une capture instantanée de base de données HANA (interne) : activité qui doit être effectuée par vous-même ou par des outils. 
1. SAP HANA écrit les données dans les fichiers de données pour créer un état cohérent sur le stockage : HANA effectue cette étape suite à la création d’une capture instantanée HANA.
1. Création d’une capture instantanée sur le volume **/hana/data** sur le stockage : étape qui doit être effectuée par vous-même ou par des outils. Il n’est pas nécessaire d’effectuer de capture instantanée sur le volume **/hana/log**.
1. Suppression de la capture instantanée de base de données HANA (interne) et reprise des opérations normales : étape qui doit être effectuée par vous-même ou par des outils.

> [!WARNING]
> Ne pas effectuer la dernière étape aura un impact sérieux sur la demande en mémoire de SAP HANA et peut entraîner son arrêt.

```
BACKUP DATA FOR FULL SYSTEM CREATE SNAPSHOT COMMENT 'SNAPSHOT-2019-03-18:11:00';
```

![Sauvegarde de capture instantanée ANF pour SAP HANA](media/hana-vm-operations-netapp/storage-snapshot-scenario.png)

```
az netappfiles snapshot create -g mygroup --account-name myaccname --pool-name mypoolname --volume-name myvolname --name mysnapname 
```

![Sauvegarde de capture instantanée ANF pour SAP HANA Partie2](media/hana-vm-operations-netapp/storage-snapshot.png)

```
BACKUP DATA FOR FULL SYSTEM CLOSE SNAPSHOT BACKUP_ID 47110815 SUCCESSFUL SNAPSHOT-2020-08-18:11:00';
```

Cette procédure de sauvegarde de capture instantanée peut être gérée de diverses façons à l’aide de différents outils. par exemple avec le script Python « ntaphana_azure.py » disponible sur GitHub [https://github.com/netapp/ntaphana](https://github.com/netapp/ntaphana). Il s’agit d’un exemple de code, fourni « tel quel » sans aucune maintenance ni prise en charge.



> [!CAUTION]
> Une capture instantanée en elle-même n’est pas une sauvegarde protégée, car elle se trouve sur le même stockage physique que le volume dont vous venez d’effectuer une capture instantanée. Il est obligatoire de « protéger » au moins une capture instantanée par jour à un autre emplacement. Cela peut être fait dans le même environnement, dans une région Azure distante ou dans le stockage d’objets blob Azure.

Solutions disponibles pour la sauvegarde cohérente des applications basées sur des captures instantanées de stockage :

- L’outil [Captures instantanées cohérentes Azure Application (AzAcSnap)](../../../azure-netapp-files/azacsnap-introduction.md) de Microsoft est un outil de ligne de commande qui offre la protection des données pour les bases de données tierces et gérant toute l’orchestration requise pour les placer dans un état cohérent de l’application avant de prendre une capture instantanée et les renvoyées ensuite à un état opérationnel. AzAcSnap prend en charge les sauvegardes basées sur des captures instantanées pour une grande instance HANA, ainsi que le service Azure NetApp Files. Pour plus d’informations, consultez l’article Qu’est-ce que l’outil Captures instantanées cohérentes Azure Application ? 
- Pour les utilisateurs de produits de sauvegarde Commvault, l’autre option est Commvault IntelliSnap V.11.21 et versions ultérieures. Cette version de Commvault (ou les versions ultérieures) offre une prise en charge des captures instantanées Azure NetApp Files. L’article [Commvault IntelliSnap 11.21](https://documentation.commvault.com/11.21/essential/116350_getting_started_with_backup_and_restore_operations_for_azure_netapp_file_services_smb_shares_and_nfs_shares.html) fournit plus d’informations.



### <a name="back-up-the-snapshot-using-azure-blob-storage"></a>Sauvegarder la capture instantanée à l’aide du stockage d’objets blob Azure
La sauvegarde dans le stockage d’objets blob Azure est une méthode économique et rapide pour enregistrer les sauvegardes de captures instantanées de stockage de base de données HANA basées sur ANF. Pour enregistrer les captures instantanées dans le Stockage Blob Azure, l’outil préconisé est AzCopy. Téléchargez la dernière version de cet outil et installez-la par exemple dans le répertoire bin où est installé le script Python de GitHub.
Téléchargez la dernière version de l’outil AzCopy :

```
root # wget -O azcopy_v10.tar.gz https://aka.ms/downloadazcopy-v10-linux && tar -xf azcopy_v10.tar.gz --strip-components=1
Saving to: ‘azcopy_v10.tar.gz’
```

La fonctionnalité la plus avancée est l’option SYNC. Si vous utilisez l’option SYNC, azcopy maintient la synchronisation entre les répertoires source et de destination. L’utilisation du paramètre **--delete-destination** est importante. Sans ce paramètre, azcopy ne supprime pas les fichiers sur le site de destination, et l’utilisation de l’espace côté destination augmente. Créez un conteneur d’objets blob de blocs dans votre compte Stockage Azure. Ensuite, créez la clé SAS pour le conteneur d’objets blob et synchronisez le dossier de captures instantanées avec le conteneur d’objets blob Azure.

Par exemple, si une capture instantanée quotidienne doit être synchronisée avec le conteneur d’objets blob Azure pour protéger les données, et que seule cette capture instantanée doit être conservée, vous pouvez utiliser la commande ci-dessous.

```
root # > azcopy sync '/hana/data/SID/mnt00001/.snapshot' 'https://azacsnaptmytestblob01.blob.core.windows.net/abc?sv=2021-02-02&ss=bfqt&srt=sco&sp=rwdlacup&se=2021-02-04T08:25:26Z&st=2021-02-04T00:25:26Z&spr=https&sig=abcdefghijklmnopqrstuvwxyz' --recursive=true --delete-destination=true
```

## <a name="next-steps"></a>Étapes suivantes
Lisez l’article suivant :

- [Haute disponibilité de SAP HANA pour les machines virtuelles Azure](./sap-hana-availability-overview.md)
