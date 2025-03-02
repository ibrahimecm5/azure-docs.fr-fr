---
title: Déploiement SGBD de machines virtuelles IBM Db2 Azure pour charge de travail SAP | Microsoft Docs
description: Déploiement SGBD de machines virtuelles Azure IBM Db2 pour charge de travail SAP
services: virtual-machines-linux,virtual-machines-windows
author: msjuergent
manager: bburns
tags: azure-resource-manager
keywords: Azure, Db2, SAP, IBM
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/17/2021
ms.author: juergent
ms.custom: H1Hack27Feb2017, ignite-fall-2021
ms.openlocfilehash: 8740e2969030c331ffd5b45fcd88b73975ef3e63
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131013077"
---
# <a name="ibm-db2-azure-virtual-machines-dbms-deployment-for-sap-workload"></a>Déploiement SGBD de machines virtuelles Azure IBM Db2 pour charge de travail SAP

Avec Microsoft Azure, vous pouvez migrer votre application SAP existante exécutée sur IBM Db2 pour Linux, UNIX et Windows (LUW) vers les machines virtuelles Azure. Avec SAP sur IBM Db2 pour LUW, les administrateurs et les développeurs peuvent utiliser les outils de développement et d’administration disponibles en local.
Des informations générales sur l’exécution de SAP Business Suite sur IBM Db2 pour LUW sont disponibles dans SAP Community Network (SCN) à l’adresse suivante : <https://www.sap.com/community/topic/db2-for-linux-unix-and-windows.html>.

Pour des informations et des mises à jour supplémentaires de SAP sur Db2 pour LUW sur Azure, voir la Note de SAP [2233094]. 

Voici les divers articles publiés sur la charge de travail SAP sur Azure.  Il est recommandé de démarrer avec [Charge de travail SAP sur Azure : prise en main](./get-started.md), puis de choisir les sujets qui vous intéresse

Les notes SAP suivantes sont en lien avec SAP sur Azure quant au domaine traité dans ce document :

| Numéro de la note |Intitulé |
| --- |--- |
| [1928533] |Applications SAP sur Azure : produits et types de machines virtuelles Azure pris en charge |
| [2015553] |SAP sur Microsoft Azure : prérequis pour le support |
| [1999351] |Résolution des problèmes de surveillance Azure améliorée pour SAP |
| [2178632] |Métriques de surveillance clés pour SAP sur Microsoft Azure |
| [1409604] |Virtualisation sur Windows : supervision améliorée |
| [2191498] |SAP sur Linux avec Azure : supervision améliorée |
| [2233094] |DB6 : exécution d’applications SAP sur Azure à l’aide d’IBM DB2 pour Linux, UNIX et Windows - Informations supplémentaires |
| [2243692] |Linux sur machine virtuelle Microsoft Azure (IaaS) : problèmes de licence SAP |
| [1984787] |SUSE LINUX Enterprise Server 12 Notes d'installation |
| [2002167] |Red Hat Enterprise Linux 7.x : installation et mise à niveau |
| [1597355] |Recommandations relatives à l’espace d’échange pour Linux |

Condition préalable à ce document, vous devez avoir lu le document [Facteurs à prendre en compte pour le déploiement SGBD des machines virtuelles Azure pour la charge de travail SAP](dbms_guide_general.md) et d’autres guides de la [documentation sur la charge de travail SAP sur Azure](./get-started.md). 


## <a name="ibm-db2-for-linux-unix-and-windows-version-support"></a>Prise en charge des versions IBM Db2 pour Linux, UNIX et Windows
SAP sur IBM Db2 pour LUW est pris en charge sur les services de machines virtuelles Microsoft Azure à compter de la version Db2 10.5.

Pour plus d’informations sur les produits SAP et les types de machines virtuelles Azure pris en charge, consultez la note SAP [1928533].

## <a name="ibm-db2-for-linux-unix-and-windows-configuration-guidelines-for-sap-installations-in-azure-vms"></a>Instructions de configuration IBM Db2 pour Linux, UNIX et Windows pour les installations SAP sur des machines virtuelles Azure
### <a name="storage-configuration"></a>Configuration du stockage
Pour obtenir une vue d’ensemble des types de stockage Azure pour la charge de travail SAP, consultez l’article [Types de stockage Azure pour une charge de travail SAP](./planning-guide-storage.md). Tous les fichiers de base de données doivent être stockés sur les disques montés du stockage de blocs Azure (Windows : NFFS, Linux : xfs ou ext3). Les volumes partagés distants comme les services Azure dans les scénarios répertoriés **NE sont PAS** pris en charge pour les fichiers de base de données Db2 : 

* [Service de fichiers Microsoft Azure](/archive/blogs/windowsazurestorage/introducing-microsoft-azure-file-service) pour tous les systèmes d’exploitation invités

* [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) pour Db2 exécuté dans le système d’exploitation invité Windows. 

Les volumes partagés distants comme les services Azure dans les scénarios répertoriés sont pris en charge pour les fichiers de base de données Db2 : 
 
* L’hébergement de fichiers journaux et de données Db2 basés sur un système d’exploitation invité Linux sur des partages NFS hébergés sur Azure NetApp Files est pris en charge.

Si vous utilisez des disques basés sur le stockage d’objets blob de pages Azure ou de la fonctionnalité Disques managés, les instructions figurant dans [Facteurs à prendre en compte pour le déploiement SGBD des machines virtuelles Azure pour la charge de travail SAP](dbms_guide_general.md) s’appliquent également aux déploiements avec le SGBD Db2.

Comme expliqué précédemment dans la partie générale du document, des quotas existent en ce qui concerne le débit d’E/S par seconde pour les disques Azure. Les quotas exacts dépendent du type de machine virtuelle utilisé. La liste des types de machines virtuelles et de leurs quotas est disponible [ici (Linux)][virtual-machines-sizes-linux] et [ici (Windows)][virtual-machines-sizes-windows].

Tant que le quota actuel d’E/S par seconde par disque est suffisant, il est possible de stocker tous les fichiers de base de données sur un seul disque monté. Alors que vous devez toujours séparer les fichiers de données et les fichiers journaux de transactions sur différents disques/disques durs virtuels.

Pour en savoir plus sur les considérations relatives aux performances, consultez également, dans les guides d’installation SAP, le chapitre portant sur la sécurité des données et les considérations sur les performances pour les répertoires de base de données.

Vous pouvez également utiliser des pools de stockage Windows (fonctionnalité uniquement disponible dans Windows Server 2012 et versions ultérieures), comme décrit dans [Facteurs à prendre en compte pour le déploiement SGBD des machines virtuelles Azure pour la charge de travail SAP](dbms_guide_general.md), ou LVM ou mdadm sur Linux afin de créer une unité logique volumineuse sur plusieurs disques montés.

<!-- log_dir, sapdata and saptmp are terms in the SAP and DB2 world and now spelling errors -->

Pour une machine virtuelle Azure de la série M, la latence d’écriture dans les journaux d’activité de transactions peut être réduite par plusieurs facteurs, comparée aux performances du stockage Premium Azure, lors de l’utilisation de l’Accélérateur d’écriture Azure. Par conséquent, vous devez déployer l’Accélérateur d’écriture Azure pour les disques durs virtuels qui constituent le volume des journaux des transactions Db2. Les détails peuvent être consultés dans le document [Accélérateur des écritures](../../how-to-enable-write-accelerator.md).

IBM Db2 LUW 11.5 propose la prise en charge d’une taille de secteur de 4 ko. Pour les anciennes versions de Db2, une taille de secteur de 512 octets doit être utilisée. Les disques SSD Premium sont de 4 ko en natif et ont une émulation de 512 octets. Le disque Ultra utilise une taille de secteur de 4 ko par défaut. Vous pouvez activer une taille de secteur de 512 octets lors de la création d’un disque Ultra. Les détails sont disponibles dans [Utilisation de disques Ultra Azure](../../disks-enable-ultra-ssd.md#deploy-an-ultra-disk---512-byte-sector-size). Cette taille de secteur de 512 octets est requise pour les versions d’IBM Db2 LUW inférieures à 11.5.

Sur Windows et pour des pools de stockage pour les chemins de stockage Db2 pour vos répertoires `log_dir`, `sapdata` et `saptmp`, vous devez spécifier une taille de secteur de disque physique de 512 octets. Quand vous utilisez des pools de stockage Windows, vous devez créer les pools de stockage manuellement par le biais de l’interface de ligne de commande en utilisant le paramètre `-LogicalSectorSizeDefault`. Pour plus d’informations, consultez <https://technet.microsoft.com/itpro/powershell/windows/storage/new-storagepool>.


## <a name="recommendation-on-vm-and-disk-structure-for-ibm-db2-deployment"></a>Recommandation relative à la structure des machines virtuelles et des disques pour le déploiement IBM Db2

Les applications IBM Db2 pour SAP NetWeaver sont prises en charge sur tous les types de machines virtuelles figurant dans la note de support SAP [1928533].  Les familles de machines virtuelles recommandées pour l’exécution de la base de données IBM Db2 sont Esd_v4/Eas_v4/Es_v3 et la série M/M_v2 pour les bases de données de plusieurs téraoctets. Les performances d’écriture sur le disque du journal des transactions IBM Db2 peuvent être améliorées en activant l’accélérateur d’écriture de la série M. 

Voici une configuration de base pour différentes tailles et utilisations de SAP sur des déploiements Db2 de petite à grande taille. La liste est basée sur le stockage Premium Azure. Toutefois, Azure ultra Disk est aussi entièrement pris en charge avec DB2 et peut également être utilisé. Utilisez les valeurs pour la capacité, le débit en rafale et les IOPS en rafale pour définir la configuration de disque Ultra. Vous pouvez limiter le nombre d’IOPS pour /db2/```<SID>```/log_dir à environ 5 000 IOPS. 

#### <a name="extra-small-sap-system-database-size-50---200-gb-example-solution-manager"></a>Système SAP très petit : taille de base de données de 50 à 200 Go : exemple d’un gestionnaire de solutions
| Nom/taille de machine virtuelle |Point de montage Db2 |Disque Premium Azure |Nbre de disques |E/S par seconde |Débit [Mo/s] |Taille [Go] |IOPS en rafale |Débit rafale [Go] | Taille de l’entrelacement | Mise en cache |
| --- | --- | --- | :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
|E4ds_v4 |/db2 |P6 |1 |240  |50  |64  |3 500  |170  ||  |
|Processeur virtuel : 4 |/db2/```<SID>```/sapdata |P10 |2 |1 000  |200  |256  |7 000  |340  |256 Ko |Lecture seule |
|RAM : 32 Gio |/db2/```<SID>```/saptmp |P6 |1 |240  |50  |128  |3 500  |170  | ||
| |/db2/```<SID>```/log_dir |P6 |2 |480  |100  |128  |7 000  |340  |64 Ko ||
| |/db2/```<SID>```/offline_log_dir |P10 |1 |500  |100  |128  |3 500  |170  || |

#### <a name="small-sap-system-database-size-200---750-gb-small-business-suite"></a>Petit système SAP : taille de base de données 200 à 750 Go : Small Business Suite
| Nom/taille de machine virtuelle |Point de montage Db2 |Disque Premium Azure |Nbre de disques |E/S par seconde |Débit [Mo/s] |Taille [Go] |IOPS en rafale |Débit rafale [Go] | Taille de l’entrelacement | Mise en cache |
| --- | --- | --- | :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
|E16ds_v4 |/db2 |P6 |1 |240  |50  |64  |3 500  |170  || |
|Processeur virtuel : 16 |/db2/```<SID>```/sapdata |P15 |4 |4 400  |500  |1 024  |14 000  |680  |256 Ko |Lecture seule |
|RAM : 128 Go |/db2/```<SID>```/saptmp |P6 |2 |480  |100  |128  |7 000  |340  |128 Ko ||
| |/db2/```<SID>```/log_dir |P15 |2 |2 200  |250  |512  |7 000  |340  |64 Ko ||
| |/db2/```<SID>```/offline_log_dir |P10 |1 |500  |100  |128  |3 500  |170  ||| 

#### <a name="medium-sap-system-database-size-500---1000-gb-small-business-suite"></a>Système SAP moyen : taille de base de données 500 à 1 000 Go : Small Business Suite
| Nom/taille de machine virtuelle |Point de montage Db2 |Disque Premium Azure |Nbre de disques |E/S par seconde |Débit [Mo/s] |Taille [Go] |IOPS en rafale |Débit rafale [Go] | Taille de l’entrelacement | Mise en cache |
| --- | --- | --- | :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
|E32ds_v4 |/db2 |P6 |1 |240  |50  |64  |3 500  |170  || |
|Processeur virtuel : 32 |/db2/```<SID>```/sapdata |P30 |2 |10 000  |400  |2 048  |10 000  |400  |256 Ko |Lecture seule |
|RAM : 256 Gio |/db2/```<SID>```/saptmp |P10 |2 |1 000  |200  |256  |7 000  |340  |128 Ko ||
| |/db2/```<SID>```/log_dir |P20 |2 |4 600  |300  |1 024  |7 000  |340  |64 Ko ||
| |/db2/```<SID>```/offline_log_dir |P15 |1 |1 100  |125  |256  |3 500  |170  ||| 

#### <a name="large-sap-system-database-size-750---2000-gb-business-suite"></a>Système SAP volumineux : taille de base de données 750 à 2 000 Go : Business Suite
| Nom/taille de machine virtuelle |Point de montage Db2 |Disque Premium Azure |Nbre de disques |E/S par seconde |Débit [Mo/s] |Taille [Go] |IOPS en rafale |Débit rafale [Go] | Taille de l’entrelacement | Mise en cache |
| --- | --- | --- | :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
|E64ds_v4 |/db2 |P6 |1 |240  |50  |64  |3 500  |170  || |
|Processeur virtuel : 64 |/db2/```<SID>```/sapdata |P30 |4 |20 000  |800  |4.096  |20 000  |800  |256 Ko |Lecture seule |
|RAM : 504 Gio |/db2/```<SID>```/saptmp |P15 |2 |2 200  |250  |512  |7 000  |340  |128 Ko ||
| |/db2/```<SID>```/log_dir |P20 |4 |9 200  |600  |2 048  |14 000  |680  |64 Ko ||
| |/db2/```<SID>```/offline_log_dir |P20 |1 |2 300  |150  |512  |3 500  |170  || |

#### <a name="large-multi-terabyte-sap-system-database-size-2-tb-global-business-suite-system"></a>Système SAP volumineux de plusieurs téraoctets : base de données de 2 To et plus : système Global Business Suite
| Nom/taille de machine virtuelle |Point de montage Db2 |Disque Premium Azure |Nbre de disques |E/S par seconde |Débit [Mo/s] |Taille [Go] |IOPS en rafale |Débit rafale [Go] | Taille de l’entrelacement | Mise en cache |
| --- | --- | --- | :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
|M128s |/db2 |P10 |1 |500  |100  |128  |3 500  |170  || |
|Processeur virtuel : 128 |/db2/```<SID>```/sapdata |P40 |4 |30,000  |1 000  |8 192  |30,000  |1 000  |256 Ko |Lecture seule |
|RAM :  2 048 Gio |/db2/```<SID>```/saptmp |P20 |2 |4 600  |300  |1 024  |7 000  |340  |128 Ko ||
| |/db2/```<SID>```/log_dir |P30 |4 |20 000  |800  |4.096  |20 000  |800  |64 Ko |WriteAccelerator |
| |/db2/```<SID>```/offline_log_dir |P30 |1 |5 000  |200  |1 024  |5 000  |200  || |


### <a name="using-azure-netapp-files"></a>Utilisation d’Azure NetApp Files
L’utilisation de volumes NFS v4.1 basés sur Azure NetApp Files (ANF) est prise en charge avec IBM Db2, hébergé sur un système d’exploitation invité Suse ou Red Hat Linux. Vous devez créer au moins quatre volumes différents, comme suit :

- Volume partagé pour saptmp1, sapmnt, usr_sap, ```<sid>```_home, db2```<sid>```_home, db2_software
- Un volume de données pour les fichiers sapdata1 à sapdatan
- Un volume de fichier journal pour le répertoire des journaux de restauration par progression
- Un volume pour les archives et les sauvegardes des journaux

Un cinquième volume potentiel peut être un volume ANF que vous utilisez pour des sauvegardes à long terme supplémentaires que vous utilisez pour l’instantané et le stockage des captures instantanées dans le magasin d’objets blob Azure.

La configuration peut ressembler à ce qui suit.

![Exemple de configuration Db2 à l’aide d’ANF](./media/dbms_guide_ibm/anf-configuration-example.png)


Le niveau de performance et la taille des volumes hébergés ANF doivent être choisis en fonction des exigences de performances. Toutefois, nous vous recommandons d’utiliser le niveau de performance Ultra pour le volume de données et de journaux. La combinaison de types de stockage de bloc et de stockage partagé pour le volume de données et de journaux n’est pas prise en charge.

En ce qui concerne les options de montage, le montage de ces volumes peut ressembler à ce qui suit (vous devez remplacer ```<SID>``` et ```<sid>``` par le SID de votre système SAP) :

```
vi /etc/idmapd.conf   
 # Example
 [General]
 Domain = defaultv4iddomain.com
 [Mapping]
 Nobody-User = nobody
 Nobody-Group = nobody

mount -t nfs -o rw,hard,sync,rsize=1048576,wsize=1048576,sec=sys,vers=4.1,tcp 172.17.10.4:/db2shared /mnt 
mkdir -p /db2/Software /db2/AN1/saptmp /usr/sap/<SID> /sapmnt/<SID> /home/<sid>adm /db2/db2<sid> /db2/<SID>/db2_software
mkdir -p /mnt/Software /mnt/saptmp  /mnt/usr_sap /mnt/sapmnt /mnt/<sid>_home /mnt/db2_software /mnt/db2<sid>
umount /mnt

mount -t nfs -o rw,hard,sync,rsize=1048576,wsize=1048576,sec=sys,vers=4.1,tcp 172.17.10.4:/db2data /mnt
mkdir -p /db2/AN1/sapdata/sapdata1 /db2/AN1/sapdata/sapdata2 /db2/AN1/sapdata/sapdata3 /db2/AN1/sapdata/sapdata4
mkdir -p /mnt/sapdata1 /mnt/sapdata2 /mnt/sapdata3 /mnt/sapdata4
umount /mnt

mount -t nfs -o rw,hard,sync,rsize=1048576,wsize=1048576,sec=sys,vers=4.1,tcp 172.17.10.4:/db2log /mnt 
mkdir /db2/AN1/log_dir
mkdir /mnt/log_dir
umount /mnt

mount -t nfs -o rw,hard,sync,rsize=1048576,wsize=1048576,sec=sys,vers=4.1,tcp 172.17.10.4:/db2backup /mnt
mkdir /db2/AN1/backup
mkdir /mnt/backup
mkdir /db2/AN1/offline_log_dir /db2/AN1/db2dump
mkdir /mnt/offline_log_dir /mnt/db2dump
umount /mnt
```

>[!NOTE]
> Les options de montage Hard et Sync sont requises.


### <a name="backuprestore"></a>Sauvegarde/restauration
La fonctionnalité de sauvegarde/restauration d’IBM Db2 pour LUW est prise en charge de la même façon que sur les systèmes d’exploitation Windows Server et Hyper-V standard.

Assurez-vous de disposer d’une stratégie de sauvegarde de base de données valide en place. 

À l’instar des déploiements sur système nu, les performances de sauvegarde/restauration dépendent du nombre de volumes pouvant être lus en parallèle et du débit éventuel de ces volumes. En outre, la consommation d’UC par la compression de sauvegarde peut jouer un rôle significatif sur les machines virtuelles ayant jusqu’à huit threads d’UC. Par conséquent, on peut partir des hypothèses suivantes :

* Moins il y a de disques utilisés pour stocker les unités de base de données, plus le débit global de lecture est réduit
* Moins il y a de threads UC dans la machine virtuelle, plus l’impact de la compression de sauvegarde est grave
* Moins il y a de cibles (répertoires d’agrégation, disques) d’écriture de la sauvegarde, plus le débit est faible

Pour augmenter le nombre de cibles d’écriture, il est possible d’utiliser/de combiner deux options selon vos besoins :

* Agréger le volume cible de sauvegarde sur plusieurs disques pour améliorer le débit d’E/S par seconde sur ce volume agrégé par bandes
* Utiliser plusieurs répertoires cible d’écriture de la sauvegarde

>[!NOTE]
>Db2 sur Windows ne prend pas en charge la technologie Windows VSS. Par conséquent, la sauvegarde de machine virtuelle cohérente par rapport à l’application du service de sauvegarde Azure ne peut pas être exploitée pour les machines virtuelles dans lequel le SGBD Db2 est déployé.

### <a name="high-availability-and-disaster-recovery"></a>Haute disponibilité et récupération d’urgence

#### <a name="linux-pacemaker"></a>Linux Pacemaker

La fonctionnalité HADR (haute disponibilité et récupération d’urgence) Db2 avec Pacemaker est prise en charge. Les systèmes d’exploitation SLES et RHEL sont pris en charge. Cette configuration active la haute disponibilité d’IBM Db2 pour SAP. Guides de déploiement :
* SLES : [Haute disponibilité d’IBM Db2 LUW sur les machines virtuelles Azure sur SUSE Linux Enterprise Server avec Pacemaker](dbms-guide-ha-ibm.md) 
* RHEL : [Haute disponibilité d’IBM Db2 LUW sur les machines virtuelles Azure sur Red Hat Enterprise Linux Server](high-availability-guide-rhel-ibm-db2-luw.md)

#### <a name="windows-cluster-server"></a>Windows Cluster Server

Microsoft Cluster Server (MSCS) n’est pas pris en charge.

La fonction HADR (haute disponibilité et récupération d’urgence) Db2 est prise en charge. Si les machines virtuelles de la configuration haute disponibilité disposent de la résolution de noms de travail, l’installation dans Azure ne diffère pas d’une installation effectuée en local. Il est déconseillé de se fier uniquement à la résolution IP.

N’utilisez pas la géoréplication pour les comptes de stockage qui stockent les disques de base de données. Pour plus d’informations, consultez le document [Facteurs à prendre en compte pour le déploiement SGBD des machines virtuelles Azure pour la charge de travail SAP](dbms_guide_general.md). 

### <a name="accelerated-networking"></a>Mise en réseau accélérée
Pour les déploiements Db2 sur Windows, il est fortement recommandé d’utiliser la fonctionnalité Azure de mise en réseau accélérée, comme décrit dans le document [Mise en réseau accélérée Azure](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/). Consultez aussi les recommandations dans [Facteurs à prendre en compte pour le déploiement SGBD des machines virtuelles Azure pour la charge de travail SAP](dbms_guide_general.md). 


### <a name="specifics-for-linux-deployments"></a>Caractéristiques pour les déploiements Linux
Tant que le quota actuel d’E/S par seconde par disque est suffisant, il est possible de stocker tous les fichiers de base de données sur un seul disque. Alors que vous devez toujours séparer les fichiers de données et les fichiers journaux de transactions sur différents disques.

Si le débit d’E/S ou IOPS d’un seul disque dur virtuel Azure n’est pas suffisant, vous pouvez utiliser MDADM ou LVM (Logical Volume Manager) comme décrit dans le document [Facteurs à prendre en compte pour le déploiement SGBD des machines virtuelles Azure pour la charge de travail SAP](dbms_guide_general.md) pour créer une unité logique volumineuse sur plusieurs disques.
Pour les disques contenant les chemins d’accès de stockage Db2 pour vos données SAP et répertoires SAPTMP, vous devez spécifier une taille de secteur de disque physique de 512 Ko.

<!-- sapdata and saptmp are terms in the SAP and DB2 world and now spelling errors -->


### <a name="other"></a>Autres
Tous les autres sujets généraux, notamment les groupes à haute disponibilité Azure ou la surveillance SAP, s’appliquent à Oracle Database, comme décrit dans [Facteurs à prendre en compte pour le déploiement SGBD des machines virtuelles Azure pour la charge de travail SAP](dbms_guide_general.md) pour le déploiement de machines virtuelles IBM.

## <a name="next-steps"></a>Étapes suivantes
Lire l’article 

- [Facteurs à prendre en compte pour le déploiement SGBD des machines virtuelles Azure pour la charge de travail SAP](dbms_guide_general.md)


[767598]:https://launchpad.support.sap.com/#/notes/767598
[773830]:https://launchpad.support.sap.com/#/notes/773830
[826037]:https://launchpad.support.sap.com/#/notes/826037
[965908]:https://launchpad.support.sap.com/#/notes/965908
[1031096]:https://launchpad.support.sap.com/#/notes/1031096
[1114181]:https://launchpad.support.sap.com/#/notes/1114181
[1139904]:https://launchpad.support.sap.com/#/notes/1139904
[1173395]:https://launchpad.support.sap.com/#/notes/1173395
[1245200]:https://launchpad.support.sap.com/#/notes/1245200
[1409604]:https://launchpad.support.sap.com/#/notes/1409604
[1558958]:https://launchpad.support.sap.com/#/notes/1558958
[1585981]:https://launchpad.support.sap.com/#/notes/1585981
[1588316]:https://launchpad.support.sap.com/#/notes/1588316
[1590719]:https://launchpad.support.sap.com/#/notes/1590719
[1597355]:https://launchpad.support.sap.com/#/notes/1597355
[1605680]:https://launchpad.support.sap.com/#/notes/1605680
[1619720]:https://launchpad.support.sap.com/#/notes/1619720
[1619726]:https://launchpad.support.sap.com/#/notes/1619726
[1619967]:https://launchpad.support.sap.com/#/notes/1619967
[1750510]:https://launchpad.support.sap.com/#/notes/1750510
[1752266]:https://launchpad.support.sap.com/#/notes/1752266
[1757924]:https://launchpad.support.sap.com/#/notes/1757924
[1757928]:https://launchpad.support.sap.com/#/notes/1757928
[1758182]:https://launchpad.support.sap.com/#/notes/1758182
[1758496]:https://launchpad.support.sap.com/#/notes/1758496
[1772688]:https://launchpad.support.sap.com/#/notes/1772688
[1814258]:https://launchpad.support.sap.com/#/notes/1814258
[1882376]:https://launchpad.support.sap.com/#/notes/1882376
[1909114]:https://launchpad.support.sap.com/#/notes/1909114
[1922555]:https://launchpad.support.sap.com/#/notes/1922555
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1941500]:https://launchpad.support.sap.com/#/notes/1941500
[1956005]:https://launchpad.support.sap.com/#/notes/1956005
[1973241]:https://launchpad.support.sap.com/#/notes/1973241
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2039619]:https://launchpad.support.sap.com/#/notes/2039619
[2069760]:https://launchpad.support.sap.com/#/notes/2069760
[2121797]:https://launchpad.support.sap.com/#/notes/2121797
[2134316]:https://launchpad.support.sap.com/#/notes/2134316
[2171857]:https://launchpad.support.sap.com/#/notes/2171857
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[dbms-guide]:dbms-guide.md 
[dbms-guide-2.1]:dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f 
[dbms-guide-2.2]:dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 
[dbms-guide-2.3]:dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 
[dbms-guide-2]:dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 
[dbms-guide-3]:dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 
[dbms-guide-5.5.1]:dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268 
[dbms-guide-5.5.2]:dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b 
[dbms-guide-5.6]:dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 
[dbms-guide-5.8]:dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30 
[dbms-guide-5]:dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737 
[dbms-guide-8.4.1]:dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573 
[dbms-guide-8.4.2]:dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d 
[dbms-guide-8.4.3]:dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c 
[dbms-guide-8.4.4]:dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818 
[dbms-guide-900-sap-cache-server-on-premises]:dbms-guide.md#642f746c-e4d4-489d-bf63-73e80177a0a8
[dbms-guide-managed-disks]:dbms-guide.md#f42c6cb5-d563-484d-9667-b07ae51bce29

[dbms-guide-figure-100]:media/virtual-machines-shared-sap-dbms-guide/100_storage_account_types.png
[dbms-guide-figure-200]:media/virtual-machines-shared-sap-dbms-guide/200-ha-set-for-dbms-ha.png
[dbms-guide-figure-300]:media/virtual-machines-shared-sap-dbms-guide/300-reference-config-iaas.png
[dbms-guide-figure-400]:media/virtual-machines-shared-sap-dbms-guide/400-sql-2012-backup-to-blob-storage.png
[dbms-guide-figure-500]:media/virtual-machines-shared-sap-dbms-guide/500-sql-2012-backup-to-blob-storage-different-containers.png
[dbms-guide-figure-600]:media/virtual-machines-shared-sap-dbms-guide/600-iaas-maxdb.png
[dbms-guide-figure-700]:media/virtual-machines-shared-sap-dbms-guide/700-livecach-prod.png
[dbms-guide-figure-800]:media/virtual-machines-shared-sap-dbms-guide/800-azure-vm-sap-content-server.png
[dbms-guide-figure-900]:media/virtual-machines-shared-sap-dbms-guide/900-sap-cache-server-on-premises.png

[deployment-guide]:deployment-guide.md 
[deployment-guide-2.2]:deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94 
[deployment-guide-3.1.2]:deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab 
[deployment-guide-3.2]:deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281 
[deployment-guide-3.3]:deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2 
[deployment-guide-3.4]:deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1 
[deployment-guide-3]:deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e 
[deployment-guide-4.1]:deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7 
[deployment-guide-4.2]:deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e 
[deployment-guide-4.3]:deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc 
[deployment-guide-4.4.2]:deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542 
[deployment-guide-4.4]:deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d 
[deployment-guide-4.5.1]:deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4 
[deployment-guide-4.5.2]:deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f 
[deployment-guide-4.5]:deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca 
[deployment-guide-5.1]:deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2 
[deployment-guide-5.2]:deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1
[deployment-guide-5.3]:deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8 

[deployment-guide-configure-monitoring-scenario-1]:deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b 
[deployment-guide-configure-proxy]:deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d 
[deployment-guide-figure-100]:media/virtual-machines-shared-sap-deployment-guide/100-deploy-vm-image.png
[deployment-guide-figure-1000]:media/virtual-machines-shared-sap-deployment-guide/1000-service-properties.png
[deployment-guide-figure-11]:deployment-guide.md#figure-11
[deployment-guide-figure-1100]:media/virtual-machines-shared-sap-deployment-guide/1100-azperflib.png
[deployment-guide-figure-1200]:media/virtual-machines-shared-sap-deployment-guide/1200-cmd-test-login.png
[deployment-guide-figure-1300]:media/virtual-machines-shared-sap-deployment-guide/1300-cmd-test-executed.png
[deployment-guide-figure-14]:deployment-guide.md#figure-14
[deployment-guide-figure-1400]:media/virtual-machines-shared-sap-deployment-guide/1400-azperflib-error-servicenotstarted.png
[deployment-guide-figure-300]:media/virtual-machines-shared-sap-deployment-guide/300-deploy-private-image.png
[deployment-guide-figure-400]:media/virtual-machines-shared-sap-deployment-guide/400-deploy-using-disk.png
[deployment-guide-figure-5]:deployment-guide.md#figure-5
[deployment-guide-figure-50]:media/virtual-machines-shared-sap-deployment-guide/50-forced-tunneling-suse.png
[deployment-guide-figure-500]:media/virtual-machines-shared-sap-deployment-guide/500-install-powershell.png
[deployment-guide-figure-6]:deployment-guide.md#figure-6
[deployment-guide-figure-600]:media/virtual-machines-shared-sap-deployment-guide/600-powershell-version.png
[deployment-guide-figure-7]:deployment-guide.md#figure-7
[deployment-guide-figure-700]:media/virtual-machines-shared-sap-deployment-guide/700-install-powershell-installed.png
[deployment-guide-figure-760]:media/virtual-machines-shared-sap-deployment-guide/760-azure-cli-version.png
[deployment-guide-figure-900]:media/virtual-machines-shared-sap-deployment-guide/900-cmd-update-executed.png
[deployment-guide-figure-azure-cli-installed]:deployment-guide.md#402488e5-f9bb-4b29-8063-1c5f52a892d0
[deployment-guide-figure-azure-cli-version]:deployment-guide.md#0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda
[deployment-guide-install-vm-agent-windows]:deployment-guide.md#b2db5c9a-a076-42c6-9835-16945868e866
[deployment-guide-troubleshooting-chapter]:deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b

[deploy-template-cli]:../../../resource-group-template-deploy-cli.md
[deploy-template-portal]:../../../resource-group-template-deploy-portal.md
[deploy-template-powershell]:../../../resource-group-template-deploy.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md
[getting-started-dbms]:get-started.md#1343ffe1-8021-4ce6-a08d-3a1553a4db82
[getting-started-deployment]:get-started.md#6aadadd2-76b5-46d8-8713-e8d63630e955
[getting-started-planning]:get-started.md#3da0389e-708b-4e82-b2a2-e92f132df89c

[getting-started-windows-classic]:../../virtual-machines-windows-classic-sap-get-started.md
[getting-started-windows-classic-dbms]:../../virtual-machines-windows-classic-sap-get-started.md#c5b77a14-f6b4-44e9-acab-4d28ff72a930
[getting-started-windows-classic-deployment]:../../virtual-machines-windows-classic-sap-get-started.md#f84ea6ce-bbb4-41f7-9965-34d31b0098ea
[getting-started-windows-classic-dr]:../../virtual-machines-windows-classic-sap-get-started.md#cff10b4a-01a5-4dc3-94b6-afb8e55757d3
[getting-started-windows-classic-ha-sios]:../../virtual-machines-windows-classic-sap-get-started.md#4bb7512c-0fa0-4227-9853-4004281b1037
[getting-started-windows-classic-planning]:../../virtual-machines-windows-classic-sap-get-started.md#f2a5e9d8-49e4-419e-9900-af783173481c

[ha-guide-classic]:https://go.microsoft.com/fwlink/?LinkId=613056

[install-extension-cli]:virtual-machines-linux-enable-aem.md

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png

[msdn-set-azurermvmaemextension]:https://msdn.microsoft.com/library/azure/mt670598.aspx

[planning-guide]:planning-guide.md 
[planning-guide-1.2]:planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff 
[planning-guide-11]:planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058 
[planning-guide-11.4.1]:planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77 
[planning-guide-11.5]:planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f 
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803 
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10 
[planning-guide-3.1]:planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a 
[planning-guide-3.2.1]:planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358 
[planning-guide-3.2.2]:planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560 
[planning-guide-3.2.3]:planning-guide.md#18810088-f9be-4c97-958a-27996255c665 
[planning-guide-3.2]:planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77 
[planning-guide-3.3.2]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 
[planning-guide-5.1.1]:planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53 
[planning-guide-5.1.2]:planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c 
[planning-guide-5.2.1]:planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef 
[planning-guide-5.2.2]:planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3 
[planning-guide-5.2]:planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7 
[planning-guide-5.3.1]:planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79 
[planning-guide-5.3.2]:planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a 
[planning-guide-5.4.2]:planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1 
[planning-guide-5.5.1]:planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646 
[planning-guide-5.5.3]:planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d 
[planning-guide-7.1]:planning-guide.md#3e9c3690-da67-421a-bc3f-12c520d99a30 
[planning-guide-7]:planning-guide.md#96a77628-a05e-475d-9df3-fb82217e8f14 
[planning-guide-9.1]:planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3 
[planning-guide-azure-premium-storage]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 

[planning-guide-figure-100]:media/virtual-machines-shared-sap-planning-guide/100-single-vm-in-azure.png
[planning-guide-figure-1300]:media/virtual-machines-shared-sap-planning-guide/1300-ref-config-iaas-for-sap.png
[planning-guide-figure-1400]:media/virtual-machines-shared-sap-planning-guide/1400-attach-detach-disks.png
[planning-guide-figure-1600]:media/virtual-machines-shared-sap-planning-guide/1600-firewall-port-rule.png
[planning-guide-figure-1700]:media/virtual-machines-shared-sap-planning-guide/1700-single-vm-demo.png
[planning-guide-figure-1900]:media/virtual-machines-shared-sap-planning-guide/1900-vm-set-vnet.png
[planning-guide-figure-200]:media/virtual-machines-shared-sap-planning-guide/200-multiple-vms-in-azure.png
[planning-guide-figure-2100]:media/virtual-machines-shared-sap-planning-guide/2100-s2s.png
[planning-guide-figure-2200]:media/virtual-machines-shared-sap-planning-guide/2200-network-printing.png
[planning-guide-figure-2300]:media/virtual-machines-shared-sap-planning-guide/2300-sapgui-stms.png
[planning-guide-figure-2400]:media/virtual-machines-shared-sap-planning-guide/2400-vm-extension-overview.png
[planning-guide-figure-2500]:media/virtual-machines-shared-sap-planning-guide/2500-vm-extension-details.png
[planning-guide-figure-2600]:media/virtual-machines-shared-sap-planning-guide/2600-sap-router-connection.png
[planning-guide-figure-2700]:media/virtual-machines-shared-sap-planning-guide/2700-exposed-sap-portal.png
[planning-guide-figure-2800]:media/virtual-machines-shared-sap-planning-guide/2800-endpoint-config.png
[planning-guide-figure-2900]:media/virtual-machines-shared-sap-planning-guide/2900-azure-ha-sap-ha.png
[planning-guide-figure-300]:media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png
[planning-guide-figure-3000]:media/virtual-machines-shared-sap-planning-guide/3000-sap-ha-on-azure.png
[planning-guide-figure-3200]:media/virtual-machines-shared-sap-planning-guide/3200-sap-ha-with-sql.png
[planning-guide-figure-400]:media/virtual-machines-shared-sap-planning-guide/400-vm-services.png
[planning-guide-figure-600]:media/virtual-machines-shared-sap-planning-guide/600-s2s-details.png
[planning-guide-figure-700]:media/virtual-machines-shared-sap-planning-guide/700-decision-tree-deploy-to-azure.png
[planning-guide-figure-800]:media/virtual-machines-shared-sap-planning-guide/800-portal-vm-overview.png
[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd 
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f 

[powershell-install-configure]: /powershell/azure/azurerm/install-azurerm-ps
[resource-group-authoring-templates]:../../../resource-group-authoring-templates.md
[resource-group-overview]:../../../azure-resource-manager/management/overview.md
[resource-groups-networking]:../../../networking/networking-overview.md
[sap-pam]:https://support.sap.com/pam 
[sap-templates-2-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fapplication-workloads%2Fsap%2Fsap-2-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-2-tier-os-disk]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk%2Fazuredeploy.json
[sap-templates-2-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-3-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image%2Fazuredeploy.json
[storage-azure-cli]:../../../storage/common/storage-azure-cli.md
[storage-azure-cli-copy-blobs]:../../../storage/common/storage-azure-cli.md#copy-blobs
[storage-introduction]:../../../storage/common/storage-introduction.md
[storage-powershell-guide-full-copy-vhd]:../../../storage/common/storage-powershell-guide-full.md#how-to-copy-blobs-from-one-storage-container-to-another
[storage-premium-storage-preview-portal]:../../disks-types.md
