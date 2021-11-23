---
title: Haute disponibilité des machines virtuelles Azure pour SAP NW sur RHEL avec NFS sur Azure Files | Microsoft Docs
description: Créez une haute disponibilité pour SAP NW sur des machines virtuelles Azure RHEL avec NFS sur Azure Files.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
tags: azure-resource-manager
ms.service: virtual-machines-sap
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 11/16/2021
ms.author: radeltch
ms.openlocfilehash: badc6696936b3cdf99b91a5abf0d95217ec9b2e7
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132557371"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-red-hat-enterprise-linux-with-nfs-on-azure-files"></a>Haute disponibilité pour SAP NetWeaver sur les machines virtuelles Azure sur Red Hat Enterprise Linux avec NFS sur Azure Files

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[afs-azure-doc]:../../../storage/files/storage-files-introduction.md
[afs-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=storage&regions=all

[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2772999]:https://launchpad.support.sap.com/#/notes/2772999
[2009879]:https://launchpad.support.sap.com/#/notes/2009879
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736
[2360818]:https://launchpad.support.sap.com/#/notes/2360818

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html


[sap-hana-ha]:sap-hana-high-availability-rhel.md


Cet article explique la manière de déployer et configurer des machines virtuelles, d’installer le framework du cluster et d’installer un système SAP NetWeaver à haute disponibilité à l’aide de [NFS sur Azure Files](../../../storage/files/files-nfs-protocol.md). Les exemples de configuration utilisent des machines virtuelles qui s’exécutent sur Red Hat Enterprise Linux (RHEL).  

## <a name="prerequisites"></a>Prérequis

* [Documentation Azure Files][afs-azure-doc] 
* Note SAP [1928533], qui contient :
  * une liste des tailles de machines virtuelles Azure prises en charge pour le déploiement de logiciels SAP
  * des informations importantes sur la capacité en fonction de la taille des machines virtuelles Azure
  * les logiciels SAP pris en charge et les combinaisons entre système d’exploitation et base de données
  * la version du noyau SAP requise pour Windows et Linux sur Microsoft Azure
* La note SAP [2015553] répertorie les conditions préalables au déploiement de logiciels SAP pris en charge par SAP sur Azure.
* La note SAP [2002167] recommande des paramètres de système d’exploitation pour Red Hat Enterprise Linux 7.x.
* La note SAP [2772999] recommande des paramètres de système d’exploitation pour Red Hat Enterprise Linux 8.x.
* La note SAP [2009879] conseille sur SAP HANA pour Red Hat Enterprise Linux
* La note SAP [2178632] contient des informations détaillées sur toutes les métriques de surveillance rapportées pour SAP sur Azure.
* La note SAP [2191498] contient la version requise de l’agent hôte SAP pour Linux sur Azure.
* La note SAP [2243692] contient des informations sur les licences SAP sur Linux dans Azure.
* La note SAP [1999351] contient des informations de dépannage supplémentaires pour l’extension d’analyse Azure améliorée pour SAP.
* Le [WIKI de la communauté SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) contient toutes les notes SAP requises pour Linux.
* [Planification et implémentation de machines virtuelles Azure pour SAP sur Linux][planning-guide]
* [Déploiement de machines virtuelles Azure pour SAP sur Linux][deployment-guide]
* [Déploiement SGBD de machines virtuelles Azure pour SAP sur Linux][dbms-guide]
* [SAP Netweaver dans le cluster pacemaker](https://access.redhat.com/articles/3150081)
* Documentation RHEL générale
  * [Vue d’ensemble des modules complémentaires de haute disponibilité](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Administration des modules complémentaires de haute disponibilité](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Référence des modules complémentaires de haute disponibilité](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [Configuration d’ASC/ERS pour SAP Netweaver avec des ressources autonomes dans RHEL 7.5](https://access.redhat.com/articles/3569681)
  * [Configuration de SAP S/4HANA ASCS/ERS avec Standalone Enqueue Server 2 (ENSA2) dans Pacemaker sur RHEL](https://access.redhat.com/articles/3974941)
* Documentation RHEL spécifique à Azure :
  * [Stratégies de prise en charge des clusters à haute disponibilité RHEL - Machines virtuelles Microsoft Azure en tant que membres du cluster](https://access.redhat.com/articles/3131341)
  * [Installation et configuration d’un cluster à haute disponibilité Red Hat Enterprise Linux 7.4 (et versions ultérieures) sur Microsoft Azure](https://access.redhat.com/articles/3252491)

## <a name="overview"></a>Vue d’ensemble

Pour déployer la couche Application SAP NetWeaver, vous avez besoin de répertoires partagés comme `/sapmnt/SID` et `/usr/sap/trans` dans l’environnement. En outre, lors du déploiement d’un système SAP à haute disponibilité, vous avez besoin de protéger et rendre hautement disponibles des systèmes de fichiers comme `/sapmnt/SID` et `/usr/sap/SID/ASCS`.

Vous pouvez maintenant placer ces systèmes de fichiers sur [NFS sur Azure Files](../../../storage/files/files-nfs-protocol.md). NFS sur Azure Files est une solution de stockage à haute disponibilité. Cette solution offre un stockage redondant interzone (ZRS) synchrone et convient aux instances SAP ASCS/ERS déployées sur Zones de disponibilité Azure.  Vous avez quand même besoin d’un cluster Pacemaker pour protéger les composants à point de défaillance unique comme les services centraux SAP NetWeaver (ASCS/SCS).  

Les exemples de configuration et les commandes d’installation utilisent les numéros d’instance suivants :

| Nom de l’instance | Numéro d’instance |
| ---------------- | ------------------ |
| Services centraux ABAP SAP (ASCS) | 00 |
| ERS | 01 |
| Server d’applications principal (PAS) | 02 |
| Serveur d’applications supplémentaire (AAS) | 03 |
| Identificateur système SAP | NW1 |

:::image type="complex" source="./media/high-availability-guide-rhel/high-availability-guide-rhel-nfs-azure-files.png" alt-text="Haute disponibilité SAP NetWeaver avec NFS sur Azure Files":::
   Ce diagramme illustre une architecture à haute disponibilité SAP NetWeaver standard. Les systèmes de fichiers « sapmnt » et « saptrans » sont déployés sur des partages NFS sur Azure Files. Les services centraux SAP sont protégés par un cluster Pacemaker. Les machines virtuelles en cluster se trouvent derrière un équilibreur de charge Azure. Les partages NFS sont montés par le biais d’un point de terminaison privé.
:::image-end:::

## <a name="prepare-infrastructure"></a>Préparer l’infrastructure

Ce document part du principe que vous avez déjà déployé un [réseau virtuel Azure](../../../virtual-network/virtual-networks-overview.md), un sous-réseau et un groupe de ressources.

1. Déployez vos machines virtuelles. Vous pouvez déployer des machines virtuelles dans des groupes à haute disponibilité ou dans des zones de disponibilité, si la région Azure prend en charge ces options. Si vous avez besoin d’adresses IP supplémentaires pour vos machines virtuelles, déployez et attachez une seconde carte réseau. N’ajoutez pas d’adresses IP secondaires à la carte réseau principale. [L’adresse IP flottante Azure Load Balancer ne prend pas en charge ce scénario](../../../load-balancer/load-balancer-multivip-overview.md#limitations).  
 
2. Pour vos adresses IP virtuelles, déployez et configurez un [équilibreur de charge](../../../load-balancer/load-balancer-overview.md) Azure. Il est recommandé d’utiliser un [équilibreur de charge standard](../../../load-balancer/quickstart-load-balancer-standard-public-portal.md). 

    1. Configurez deux adresses IP front-end : une pour ASCS ( `10.90.90.10` ) et une pour ERS (`10.90.90.9`).
    2. Créez un pool back-end et ajoutez les deux machines virtuelles, qui feront partie du cluster.
    3. Créez la sonde d’intégrité pour ASCS. Le port de la sonde est le port `62000`. Créez le port de la sonde pour ERS. Le port de la sonde ERS est le port `62101`. Quand vous configurez les ressources Pacemaker plus tard, vous devez utiliser des ports de sonde qui correspondent.
    4. Configurez les règles d’équilibrage de charge pour ASCS et ERS. Sélectionnez les adresses IP frontales, les sondes d’intégrité et le pool back-end correspondants. Sélectionnez des ports à haute disponibilité, augmentez le délai d’inactivité à 30 minutes et activez adresse IP flottante.

### <a name="deploy-azure-files-storage-account-and-nfs-shares"></a>Déployer un compte de stockage Azure Files et des partages NFS 

NFS sur Azure Files s’exécute sur un [stockage Azure Files Premium][afs-azure-doc]. Avant de configurer NFS sur Azure Files, consultez le [guide pratique pour créer un partage NFS](../../../storage/files/storage-files-how-to-create-nfs-shares.md?tabs=azure-portal).    

Il existe deux options de redondance au sein d’une région Azure :

- [Stockage localement redondant (LRS)](../../../storage/common/storage-redundancy.md#locally-redundant-storage), qui offre une réplication de données locale, dans la zone et synchrone.
- [Stockage redondant interzone (ZRS)](../../../storage/common/storage-redundancy.md#zone-redundant-storage) qui réplique vos données de façon synchrone dans les trois [zones de disponibilité](../../../availability-zones/az-overview.md) de la région.

Vérifiez si votre région Azure sélectionnée propose NFS 4.1 sur Azure Files avec la redondance appropriée. Passez en revue la [disponibilité d’Azure Files par région Azure][afs-avail-matrix] sous **Stockage Fichier Premium**. Si votre scénario bénéficie de ZRS, [vérifiez que les partages de fichiers Premium avec ZRS sont pris en charge dans votre région Azure](../../../storage/common/storage-redundancy.md#zone-redundant-storage).

Il est recommandé d’accéder à votre compte Stockage Azure par le biais d’un [point de terminaison privé Azure](../../../storage/files/storage-files-networking-endpoints.md?tabs=azure-portal). Veillez à déployer le point de terminaison du compte de stockage Azure Files et les machines virtuelles, sur lesquels vous devez monter les partages NFS, dans le même réseau virtuel Azure ou dans des réseaux virtuels Azure appairés.

1. Déployez un compte Stockage Fichier nommé `sapafsnfs`. Dans cet exemple, nous utilisons ZRS. Si vous ne connaissez pas bien le processus, consultez [Créer un compte de stockage](../../../storage/files/storage-how-to-create-file-share.md?tabs=azure-portal#create-a-storage-account) dans le portail Azure.
1. Sous l’onglet **Bases**, utilisez ces paramètres :

    1. Pour **Nom du compte de stockage**, entrez `sapafsnfs`.
    
    1. Pour **Performances**, sélectionnez **Premium**.
    
    1. Pour **Type de compte Premium**, sélectionnez **FileStorage**.
    
    1. Pour **Réplication**, sélectionnez redondance de zone (ZRS).

1. Sélectionnez **Suivant**.

1. Sous l’onglet **Avancé**, décochez **Exiger un transfert sécurisé pour les opérations d’API REST**. Si vous ne décochez pas cette option, vous ne pouvez pas monter le partage NFS sur votre machine virtuelle. L’opération de montage expirera.

1. Sélectionnez **Suivant**.

1. Dans la section **Mise en réseau**, configurez ces paramètres : 

    1. Sous **Connectivité réseau**, pour **Méthode de connectivité**, sélectionnez **Point de terminaison privé**.  
    
    1. Sous **Point de terminaison privé**, sélectionnez **Ajouter un point de terminaison privé**.
    
1. Dans le volet **Créer un point de terminaison privé**, sélectionnez vos **abonnement**, **groupe de ressources** et **emplacement**. 
    
    Pour **Nom**, entrez `sapafsnfs_pe`.
        
    Pour **Sous-ressource de stockage**, sélectionnez **fichier**.
        
    Sous **Mise en réseau**, pour **Réseau virtuel**, sélectionnez le réseau virtuel et le sous-réseau à utiliser. Là encore, vous pouvez utiliser le réseau virtuel dans lequel se trouvent vos machines virtuelles SAP ou un réseau virtuel appairé.
       
    Sous **Intégration de DNS privé**, acceptez l’option par défaut **Oui** pour **Intégrer à une zone DNS privée**. Veillez à sélectionner votre **zone DNS privée**.
        
    Sélectionnez **OK**.
        
1. Encore sous l’onglet **Mise en réseau**, sélectionnez **Suivant**.

1. Sous l’onglet **Protection des données**, conservez tous les paramètres par défaut. 

1. Sélectionnez **Vérifier + créer** pour valider votre configuration.

1. Attendez que la validation se termine. Corrigez les problèmes avant de continuer.

1. Sous l’onglet **Review + create (Vérifier + créer)** , sélectionnez **Créer**.


Ensuite, déployez les partages NFS dans le compte de stockage que vous avez créé. Dans cet exemple, il existe deux partages NFS, `sapnw1` et `saptrans`.    
1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Sélectionnez ou recherchez des **comptes de Stockage**. 

1. Dans la page **Comptes de stockage**, sélectionnez **sapafsnfs**.

1.  Dans le menu de ressource pour **sapafsnfs**, sélectionnez **Partages de fichiers** sous **Stockage de données**.

1. Dans la page **Partages de fichiers**, sélectionnez **Partage de fichiers**.

   1. Pour **Nom**, entrez `sapnw1`, `saptrans`.
   1.  Sélectionnez une taille de partage appropriée. Par exemple, **128 Go**.  Tenez compte de la taille des données stockées sur le partage, des besoins en termes d’IOPS et de débit.  Pour plus d’informations, consultez [Cibles des partages de fichiers Azure](../../../storage/files/storage-files-scale-targets.md#azure-file-share-scale-targets).
   1. Sélectionnez le protocole **NFS**.
   1. Sélectionnez **Pas de Squash racine**.  Sinon, quand vous montez les partages sur vos machines virtuelles, vous ne pouvez pas voir le propriétaire ou le groupe du fichier.

   > [!IMPORTANT]
   > La taille de partage ci-dessus n’est qu’un exemple. Veillez à dimensionner vos partages de manière appropriée. Taille non seulement basée sur la taille des données stockées sur le partage, mais aussi sur les exigences en termes d’IOPS et de débit. Pour plus d’informations, consultez [Cibles des partages de fichiers Azure](../../../storage/files/storage-files-scale-targets.md#azure-file-share-scale-targets).  

   Les systèmes de fichiers SAP qui n’ont pas besoin d’être montés par le biais de NFS peuvent également être déployés sur un [stockage sur disque Azure](../../disks-types.md#premium-ssds). Dans cet exemple, vous pouvez déployer `/usr/sap/NW1/D02` et `/usr/sap/NW1/D03` sur un stockage sur disque Azure. 

### <a name="important-considerations-for-nfs-on-azure-files-shares"></a>Considérations importantes pour NFS sur des partages Azure Files

Lorsque vous planifiez votre déploiement avec NFS sur Azure Files, tenez compte des points importants suivants :  

- La taille de partage minimale s’élève à 100 Gio. Vous payez uniquement pour la [capacité des partages provisionnés](../../../storage/files/understanding-billing.md#provisioned-model). 
- Dimensionnez vos partages NFS non seulement en fonction de vos besoins en capacité, mais aussi de vos besoins en termes d’IOPS et de débit. Pour plus d’informations, consultez [Cibles des partages de fichiers Azure](../../../storage/files/storage-files-scale-targets.md#azure-file-share-scale-targets).
- Testez la charge de travail pour valider votre dimensionnement et vérifier que vos objectifs de performances sont atteints. Pour savoir comment résoudre les problèmes de performances avec NFS sur Azure Files, consultez [Résoudre les problèmes de performances des partages de fichiers Azure](../../../storage/files/storage-troubleshooting-files-performance.md).
- Pour les systèmes SAP J2EE, il n’est pas possible de placer `/usr/sap/<SID>` sur NFS sur Azure Files.
- Si votre système SAP a une charge importante de programmes de traitement par lots, vous risquez d’avoir des millions de journaux de travaux. Si les journaux des programmes de traitement par lots SAP sont stockés dans le système de fichiers, portez une attention particulière au dimensionnement du partage `sapmnt`. À partir de SAP_BASIS 7.52, le comportement par défaut des journaux des programmes de traitement par lots doit être stocké dans la base de données. Pour plus d’informations, consultez [Journal des travaux dans la base de données][2360818].     
- Déployez un partage `sapmnt` distinct pour chaque système SAP.
- N’utilisez pas le partage `sapmnt` pour une autre activité, comme des interfaces ou `saptrans`.
- N’utilisez pas le partage `saptrans` pour une autre activité, comme des interfaces ou `sapmnt`.
- Évitez de regrouper les partages d’un trop grand nombre de systèmes SAP dans un seul compte de stockage. Il existe également des [cibles de mise à l’échelle des performances des comptes de Stockage](../../../storage/files/storage-files-scale-targets.md#storage-account-scale-targets). Veillez à ne pas dépasser les limites du compte de stockage, également.
- En général, ne regroupez pas les partages de plus de 5 systèmes SAP dans un seul compte de stockage. Cette règle permet d’éviter de dépasser les limites de comptes de stockage et de simplifier l’analyse des performances.   
- En général, évitez de mélanger des partages pour les systèmes SAP hors production et de production dans le même compte de stockage.
- Nous vous recommandons de déployer sur RHEL 8.4 ou une version ultérieure pour tirer parti des [améliorations apportées aux clients NFS](../../../storage/files/storage-troubleshooting-files-nfs.md#ls-hangs-for-large-directory-enumeration-on-some-kernels).   
- Utilisez un point de terminaison privé. Dans l’éventualité peu probable d’une défaillance zonale, vos sessions NFS sont automatiquement redirigées vers une zone saine. Vous n’avez pas besoin de remonter les partages NFS sur vos machines virtuelles.
- Si vous déployez vos machines virtuelles sur Zones de disponibilité, utilisez un [compte de stockage avec ZRS](../../../storage/common/storage-redundancy.md#zone-redundant-storage) dans les régions Azure qui prennent en charge ZRS. 
- Azure Files ne prend actuellement pas en charge la réplication interrégionale automatique pour les scénarios de reprise d’activité après sinistre.

## <a name="setting-up-ascs"></a>Configuration de (A)SCS

Dans cet exemple, vous déployez les ressources manuellement par le biais du [portail Azure](https://portal.azure.com/#home).

### <a name="deploy-azure-load-balancer-via-azure-portal"></a>Déployer Azure Load Balancer par le biais du portail Azure

Après avoir déployé les machines virtuelles de votre système SAP, créez un équilibreur de charge. Ensuite, utilisez les machines virtuelles dans le pool back-end.

1. Créez un équilibreur de charge standard interne.
   1. Créer les adresses IP de serveurs frontaux
      1. Adresse IP 10.90.90.10 pour l’instance ASCS
         1. Ouvrir l’équilibrage de charge, sélectionner le pool d’adresses IP frontal et cliquer sur Ajouter
         1. Entrez le nom du nouveau pool d’adresses IP front-end (par exemple, **frontend.NW1.ASCS**).
         1. Définissez Affectation sur Statique et entrez l’adresse IP (par exemple, **10.90.90.10**).
         1. Cliquez sur OK
      1. Adresse IP 10.90.90.9 pour les instances ASCS ERS
         * Répétez les étapes du point « a » pour créer une adresse IP pour l’instance ERS (par exemple, **10.90.90.9** et **frontend.NW1.ERS**).
   1. Créer le pool principal
      1. Ouvrir l’équilibrage de charge, sélectionner les pools principaux et cliquer sur Ajouter
      1. Entrez le nom du nouveau pool de back-ends (par exemple, **backend.NW1**).
      1. Cliquer sur Ajouter une machine virtuelle
      1. Sélectionner une machine virtuelle
      1. Sélectionnez les machines virtuelles du cluster (A)SCS et leurs adresses IP.
      1. Cliquez sur Ajouter.  
      
   1. Créer les sondes d’intégrité
      1. Port 620 **00** pour l’instance ASCS
         1. Ouvrir l’équilibrage de charge, sélectionner les sondes d’intégrité et cliquer sur Ajouter
         1. Entrez le nom de la nouvelle sonde d’intégrité (par exemple, **health.NW1.ASCS**).
         1. Sélectionner le protocole TCP et le port 620 **00**, et conserver un intervalle de 5 et un seuil de défaillance sur le plan de l’intégrité de 2
         1. Cliquez sur OK
      1. Port 621 **01** pour les instances ASCS ERS
            * Répétez les étapes du point « c » afin de créer une sonde d’intégrité pour l’instance ERS (par exemple, 621 **01** et **health.NW1.ERS**).
   1. Règles d’équilibrage de charge
      1. Créer un pool principal pour l’instance ASCS
         1. Ouvrir l’équilibreur de charge, sélectionner les règles d’équilibrage de charge et cliquer sur Ajouter
         1. Entrez le nom de la nouvelle règle d’équilibreur de charge (par exemple, **lb.NW1.ASCS**).
         1. Sélectionnez l’adresse IP front-end pour l’instance ASCS, le pool de back-ends et la sonde d’intégrité créés précédemment (par exemple, **frontend.NW1.ASCS**, **backend.NW1** et **health.NW1.ASCS**).
         1. Sélectionnez **Ports haute disponibilité**
         1. **Veiller à activer IP flottante**
         1. Cliquez sur OK
         * Répétez les étapes ci-dessus pour créer des règles d’équilibrage de charge pour ERS (par exemple **lb.NW1.ERS**).
1. Sinon, si votre scénario requiert l’équilibrage de charge de base (interne), procédez comme suit :  
   1. Créer les adresses IP de serveurs frontaux
      1. Adresse IP 10.90.90.10 pour l’instance ASCS
         1. Ouvrir l’équilibrage de charge, sélectionner le pool d’adresses IP frontal et cliquer sur Ajouter
         1. Entrez le nom du nouveau pool d’adresses IP front-end (par exemple, **frontend.NW1.ASCS**).
         1. Définissez Affectation sur Statique et entrez l’adresse IP (par exemple, **10.90.90.10**).
         1. Cliquez sur OK
      1. Adresse IP 10.90.90.9 pour les instances ASCS ERS
         * Répétez les étapes du point « a » pour créer une adresse IP pour l’instance ERS (par exemple, **10.90.90.9** et **frontend.NW1.ERS**).
   1. Créer le pool principal
      1. Ouvrir l’équilibrage de charge, sélectionner les pools principaux et cliquer sur Ajouter
      1. Entrez le nom du nouveau pool de back-ends (par exemple, **backend.NW1**).
      1. Cliquer sur Ajouter une machine virtuelle
      1. Sélectionner le groupe à haute disponibilité créé précédemment pour l’instance ASCS 
      1. Sélectionner les machines virtuelles du cluster (A)SCS
      1. Cliquez sur OK
   1. Créer les sondes d’intégrité
      1. Port 620 **00** pour l’instance ASCS
         1. Ouvrir l’équilibrage de charge, sélectionner les sondes d’intégrité et cliquer sur Ajouter
         1. Entrez le nom de la nouvelle sonde d’intégrité (par exemple, **health.NW1.ASCS**).
         1. Sélectionner le protocole TCP et le port 620 **00**, et conserver un intervalle de 5 et un seuil de défaillance sur le plan de l’intégrité de 2
         1. Cliquez sur OK
      1. Port 621 **01** pour les instances ASCS ERS
            * Répétez les étapes du point « c » afin de créer une sonde d’intégrité pour l’instance ERS (par exemple, 621 **01** et **health.NW1.ERS**).
   1. Règles d’équilibrage de charge
      1. TCP 32 **00** pour l’instance ASCS
         1. Ouvrir l’équilibreur de charge, sélectionner les règles d’équilibrage de charge et cliquer sur Ajouter
         1. Entrez le nom de la nouvelle règle d’équilibreur de charge (par exemple, **lb.NW1.ASCS.3200**).
         1. Sélectionnez l’adresse IP front-end pour l’instance ASCS, le pool de back-ends et la sonde d’intégrité créés précédemment (par exemple, **frontend.NW1.ASCS**).
         1. Conserver le protocole **TCP** et indiquer le port **3200**
         1. Augmenter le délai d’inactivité à 30 minutes
         1. **Veiller à activer IP flottante**
         1. Cliquez sur OK
      1. Ports supplémentaires pour l’instance ASCS
         * Répéter les étapes du point « d » pour les ports 36 **00**, 39 **00**, 81 **00**, 5 **00** 13, 5 **00** 14, 5 **00** 16 et TCP pour l’instance ASCS
      1. Ports supplémentaires pour les instances ASCS ERS
         * Répéter les étapes du point « d » pour les ports 32 **01**, 33 **01**, 5 **01** 13, 5 **01** 14, 5 **01** 16 et TCP pour les instances ASCS ERS

      
      > [!IMPORTANT]
      > Une adresse IP flottante n’est pas prise en charge sur une configuration IP secondaire de carte réseau pour des scénarios d’équilibrage de charge. Pour plus d’informations, consultez [Limitations d’équilibreur de charge Azure](../../../load-balancer/load-balancer-multivip-overview.md#limitations). Si vous avez besoin d’une adresse IP supplémentaire pour la machine virtuelle, déployez une deuxième carte réseau.  

      > [!Note]
      > Lorsque des machines virtuelles sans adresse IP publique sont placées dans le pool principal d’Azure Standard Load Balancer interne (aucune adresse IP publique), il n’y a pas de connectivité Internet sortante, sauf si une configuration supplémentaire est effectuée pour autoriser le routage vers des points de terminaison publics. Pour savoir plus en détails comment bénéficier d’une connectivité sortante, voir [Connectivité des points de terminaison publics pour les machines virtuelles avec Azure Standard Load Balancer dans les scénarios de haute disponibilité SAP](./high-availability-guide-standard-load-balancer-outbound-connections.md).  

      > [!IMPORTANT]
      > N’activez pas les timestamps TCP sur des machines virtuelles Azure placées derrière Azure Load Balancer. L’activation des timestamps TCP entraîne l’échec des sondes d’intégrité. Définissez le paramètre **net.ipv4.tcp_timestamps** sur **0**. Pour plus d’informations, consultez [Load Balancer health probes](../../../load-balancer/load-balancer-custom-probe-overview.md) (Sondes d’intégrité Load Balancer).


### <a name="create-pacemaker-cluster"></a>Créer le cluster Pacemaker

Suivez les étapes décrites sur la page [Configuration de Pacemaker sur Red Hat Enterprise Linux dans Azure](high-availability-guide-rhel-pacemaker.md) pour créer un cluster Pacemaker de base pour ce serveur (A)SCS.

### <a name="prepare-for-sap-netweaver-installation"></a>Préparer l’installation de SAP NetWeaver

Les éléments suivants sont précédés de **[A]** (applicable à tous les nœuds), de **[1]** (applicable uniquement au nœud 1) ou de **[2]** (applicable uniquement au nœud 2).

1. **[A]** Configurer la résolution de nom d’hôte

   Vous pouvez utiliser un serveur DNS ou modifier le fichier /etc/hosts sur tous les nœuds. Cet exemple montre comment utiliser le fichier /etc/hosts.
   Remplacez l’adresse IP et le nom d’hôte dans les commandes suivantes

    ```bash
    sudo vi /etc/hosts
    ```

   Insérez les lignes suivantes dans le fichier /etc/hosts. Modifiez l’adresse IP et le nom d’hôte en fonction de votre environnement

    ```bash
     # IP address of cluster node 1
     10.90.90.7    sap-cl1
     # IP address of cluster node 2
     10.90.90.8     sap-cl2
     # IP address of the load balancer frontend configuration for SAP Netweaver ASCS
     10.90.90.10   sapascs
     # IP address of the load balancer frontend configuration for SAP Netweaver ERS
     10.90.90.9    sapers
    ```

2. **[A]** Installer le client NFS et autres exigences

    ```bash
    sudo yum -y install nfs-utils resource-agents resource-agents-sap
    ```

3. **[1]** Créez les répertoires SAP sur le partage NFS.  
   Montez temporairement le partage NFS **sapnw1** sur l’une des machines virtuelles et créez les répertoires SAP à utiliser comme points de montage imbriqués.  

    ```bash
    # mount temporarily the volume
    sudo mkdir -p /saptmp
    sudo mount -t nfs sapnfs.file.core.windows.net:/sapnfsafs/sapnw1 /saptmp -o vers=4,minorversion=1,sec=sys
    # create the SAP directories
    sudo cd /saptmp
    sudo mkdir -p sapmntNW1
    sudo mkdir -p usrsapNW1ascs
    sudo mkdir -p usrsapNW1ers
    sudo mkdir -p usrsapNW1sys
    # unmount the volume and delete the temporary directory
    cd ..
    sudo umount /saptmp
    sudo rmdir /saptmp
    ``` 

4. **[A]** Créer les répertoires partagés

    ```bash
    sudo mkdir -p /sapmnt/NW1
    sudo mkdir -p /usr/sap/trans
    sudo mkdir -p /usr/sap/NW1/SYS
    sudo mkdir -p /usr/sap/NW1/ASCS00
    sudo mkdir -p /usr/sap/NW1/ERS01
    
    sudo chattr +i /sapmnt/NW1
    sudo chattr +i /usr/sap/trans
    sudo chattr +i /usr/sap/NW1/SYS
    sudo chattr +i /usr/sap/NW1/ASCS00
    sudo chattr +i /usr/sap/NW1/ERS01
    ```

5. **[A]** Vérifier la version de ressource-agents-sap

   Vérifiez que la version du package de ressources-agents-sap installé est au moins 3.9.5-124.el7
    ```
    sudo yum info resource-agents-sap
   ```


6. **[A]** Ajouter des entrées de montage

    ```bash
    vi /etc/fstab
    # Add the following lines to fstab, save and exit
    sapnfs.file.core.windows.net:/sapnfsafs/saptrans /usr/sap/trans  nfs vers=4,minorversion=1,sec=sys  0  0
    sapnfs.file.core.windows.net:/sapnfsafs/sapnw1/sapmntNW1 /sapmnt/NW1  nfs vers=4,minorversion=1,sec=sys  0  0
    sapnfs.file.core.windows.net:/sapnfsafs/sapnw1/usrsapNW1sys/ /usr/sap/NW1/SYS  nfs vers=4,minorversion=1,sec=sys  0  0
    
    # Mount the file systems
    mount -a 
    ```

7. **[A]** Configurer le fichier SWAP

    ```bash
    sudo vi /etc/waagent.conf
    
    # Set the property ResourceDisk.EnableSwap to y
    # Create and use swapfile on resource disk.
    ResourceDisk.EnableSwap=y
    
    # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
    # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
    # Size of the swapfile.
    ResourceDisk.SwapSizeMB=2000
    ```

   Redémarrer l’Agent pour activer la modification

    ```bash
    sudo service waagent restart
    ```

8. **[A]** Configuration de RHEL

   Configurez RHEL comme décrit dans la note SAP [2002167] pour RHEL 7.x ou la note SAP [2772999] pour RHEL 8.x.  

### <a name="installing-sap-netweaver-ascsers"></a>Installation de SAP NetWeaver ASC/ERS

1. **[1]** Configurer les propriétés de cluster par défaut

    ```bash
    # If using RHEL 7.X
    pcs resource defaults resource-stickiness=1
    pcs resource defaults migration-threshold=3
    # If using RHEL 8.X
    pcs resource defaults update resource-stickiness=1
    pcs resource defaults update migration-threshold=3
    ```

1. **[1]** Créer une ressource IP virtuelle et la sonde d’intégrité pour l’instance ASCS

    ```bash
    sudo pcs node standby sap-cl2
   
    sudo pcs resource create fs_NW1_ASCS Filesystem device='sapnfs.file.core.windows.net:/sapnfsafs/sapnw1/usrsapNW1ascs' \
      directory='/usr/sap/NW1/ASCS00' fstype='nfs' force_unmount=safe options='sec=sys,vers=4.1' \
      op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
      --group g-NW1_ASCS
   
    sudo pcs resource create vip_NW1_ASCS IPaddr2 \
      ip=10.90.90.10 cidr_netmask=24 \
      --group g-NW1_ASCS
   
    sudo pcs resource create nc_NW1_ASCS azure-lb port=62000 \
      --group g-NW1_ASCS
    ```

   Vérifiez que l’état du cluster est OK et que toutes les ressources sont démarrées. Le nœud sur lequel les ressources s’exécutent n’a aucune importance.

    ```bash
    sudo pcs status
    
    # Node sap-cl2: standby
    # Online: [ sap-cl1 ]
    #
    # Full list of resources:
    #
    # rsc_st_azure    (stonith:fence_azure_arm):      Started sap-cl1
    #  Resource Group: g-NW1_ASCS
    #      fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started sap-cl1 
    #      nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started sap-cl1
    #      vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started sap-cl1
    ```

1. **[1]** Installer SAP NetWeaver ASCS  

   Installez SAP NetWeaver ASCS comme racine sur le premier nœud à l’aide d’un nom d’hôte virtuel mappé à l’adresse IP de la configuration front-end de l’équilibreur de charge pour l’instance ASCS, par exemple **sapascs**, **10.90.90.10**, et du numéro d’instance utilisé pour la sonde de l’équilibreur de charge, par exemple **00**.

   Vous pouvez utiliser le paramètre sapinst SAPINST_REMOTE_ACCESS_USER pour autoriser un utilisateur non racine à se connecter à sapinst.

    ```bash
    # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again.
    sudo firewall-cmd --zone=public  --add-port=4237/tcp
   
    sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=<virtual_hostname>
    ```

    Si aucun sous-dossier n’est créé dans /usr/sap/**NW1**/ASCS **00** lors de l’installation, essayez de définir le propriétaire et le groupe du dossier ASCS **00**, puis réessayez.

    ```bash
    sudo chown nw1adm /usr/sap/NW1/ASCS00
    sudo chgrp sapsys /usr/sap/NW1/ASCS00
    ```

1. **[1]** Créer une ressource IP virtuelle et la sonde d’intégrité pour l’instance ERS

    ```bash
    sudo pcs node unstandby sap-cl2
    sudo pcs node standby sap-cl1
    
    sudo pcs resource create fs_NW1_AERS Filesystem device='sapnfs.file.core.windows.net:/sapnfsafs/sapnw1/usrsapNW1ers' \
      directory='/usr/sap/NW1/ERS01' fstype='nfs' force_unmount=safe options='sec=sys,vers=4.1' \
      op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-NW1_AERS
   
    sudo pcs resource create vip_NW1_AERS IPaddr2 \
      ip=10.90.90.9 cidr_netmask=24 \
     --group g-NW1_AERS
   
    sudo pcs resource create nc_NW1_AERS azure-lb port=62101 \
     --group g-NW1_AERS
    ```
 
   Vérifiez que l’état du cluster est OK et que toutes les ressources sont démarrées. Le nœud sur lequel les ressources s’exécutent n’a aucune importance.

    ```bash
    sudo pcs status
   
    # Node sap-cl1: standby
    # Online: [ sap-cl2 ]
    #
    # Full list of resources:
    #
    # rsc_st_azure    (stonith:fence_azure_arm):      Started sap-cl2
    #  Resource Group: g-NW1_ASCS
    #      fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started sap-cl2
    #      nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started sap-cl2
    #      vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started sap-cl2
    #  Resource Group: g-NW1_AERS
    #      fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started sap-cl2
    #      nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started sap-cl2
    #      vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started sap-cl2
    ```

1. **[2]** Installer SAP NetWeaver ERS  

   Installez SAP NetWeaver ERS comme racine sur le deuxième nœud à l’aide d’un nom d’hôte virtuel mappé à l’adresse IP de la configuration frontend de l’équilibreur de charge pour l’ERS, par exemple **sapers**, **10.90.90.9**, et du numéro d’instance que vous avez utilisé pour la sonde de l’équilibreur de charge, par exemple **01**.

   Vous pouvez utiliser le paramètre sapinst SAPINST_REMOTE_ACCESS_USER pour autoriser un utilisateur non racine à se connecter à sapinst.

    ```bash
    # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again.
    sudo firewall-cmd --zone=public  --add-port=4237/tcp

    sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=<virtual_hostname>
    ```

   Si aucun sous-dossier n’est créé dans /usr/sap/**NW1**/ERS **01** lors de l’installation, essayez de définir le propriétaire et le groupe du dossier ERS **01**, puis réessayez.

    ```
    sudo chown qaadm /usr/sap/NW1/ERS01
    sudo chgrp sapsys /usr/sap/NW1/ERS01
    ```

1. **[1]** Adapter les profils d’instance ASCS/SCS et ERS

   * Profil ASCS/SCS

    ```bash
    sudo vi /sapmnt/NW1/profile/NW1_ASCS00_sapascs
   
    # Change the restart command to a start command
    #Restart_Program_01 = local $(_EN) pf=$(_PF)
    Start_Program_01 = local $(_EN) pf=$(_PF)
   
    # Add the keep alive parameter, if using ENSA1
    enque/encni/set_so_keepalive = true
    ```

   Pour ENSA1 et ENSA2, assurez-vous que les `keepalive`Paramètres de système d’exploitation sont définis comme décrit dans la note SAP [1410736](https://launchpad.support.sap.com/#/notes/1410736).  

   * Profil ERS

    ```bash
    sudo vi /sapmnt/NW1/profile/NW1_ERS01_sapers
   
    # Change the restart command to a start command
    #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
    Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
    # remove Autostart from ERS profile
    # Autostart = 1
    ```

1. **[A]** Configurer Keep Alive

   La communication entre le serveur d’applications SAP NetWeaver et l’ASCS/SCS est routée par l’intermédiaire d’un équilibreur de charge logiciel. L’équilibreur de charge déconnecte les connexions inactives après un délai configurable. Pour éviter cela, vous devez définir un paramètre dans le profil SAP NetWeaver ASCS/SCS, si vous utilisez ENSA1. Modifiez les paramètres `keepalive` du système Linux sur tous les serveurs SAP pour ENSA1/ENSA2. Pour plus d’informations, consultez la [Note SAP 1410736][1410736].

    ```bash
    # Change the Linux system configuration
    sudo sysctl net.ipv4.tcp_keepalive_time=300
    ```

1. **[A]** Mettre à jour le fichier /usr/sap/sapservices

   Pour empêcher le démarrage des instances par le script de démarrage sapinit, toutes les instances gérées par Pacemaker doivent être commentées à partir du fichier /usr/sap/sapservices. Ne commentez pas l’instance SAP HANA si elle sera utilisée avec HANA SR.

    ```bash
    sudo vi /usr/sap/sapservices
   
    # On the node where you installed the ASCS, comment out the following line
    # LD_LIBRARY_PATH=/usr/sap/NW1/ASCS00/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/NW1/ASCS00/exe/sapstartsrv pf=/usr/sap/NW1/SYS/profile/NW1_ASCS00_sapascs -D -u nw1adm
   
    # On the node where you installed the ERS, comment out the following line
    # LD_LIBRARY_PATH=/usr/sap/NW1/ERS01/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/NW1/ERS01/exe/sapstartsrv pf=/usr/sap/NW1/ERS01/profile/NW1_ERS01_sapers -D -u nw1adm
    ```

1. **[1]** Créer les ressources de cluster SAP

   Si vous utilisez l’architecture de serveur de file d’attente 1 (ENSA1), définissez les ressources comme suit :

    ```bash
    sudo pcs property set maintenance-mode=true
    
    sudo pcs resource create rsc_sap_NW1_ASCS00 SAPInstance \
     InstanceName=NW1_ASCS00_sapascs START_PROFILE="/sapmnt/NW1/profile/NW1_ASCS00_sapascs" \
     AUTOMATIC_RECOVER=false \
     meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
     op monitor interval=20 on-fail=restart timeout=60 \
     op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW1_ASCS
   
    sudo pcs resource meta g-NW1_ASCS resource-stickiness=3000

    sudo pcs resource create rsc_sap_NW1_ERS01 SAPInstance \
     InstanceName=NW1_ERS01_sapers START_PROFILE="/sapmnt/NW1/profile/NW1_ERS01_sapers" \
     AUTOMATIC_RECOVER=false IS_ERS=true \
     op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW1_AERS
     
    sudo pcs constraint colocation add g-NW1_AERS with g-NW1_ASCS -5000
    sudo pcs constraint location rsc_sap_NW1_ASCS00 rule score=2000 runs_ers_NW1 eq 1
    sudo pcs constraint order start g-NW1_ASCS then stop g-NW1_AERS kind=Optional symmetrical=false
    
    sudo pcs node unstandby sap-cl1
    sudo pcs property set maintenance-mode=false
    ```

   SAP a introduit la prise en charge du serveur de file d’attente 2, y compris la réplication, avec SAP NW 7.52. À partir de la plateforme ABAP 1809, le serveur de file d’attente 2 est installé par défaut. Consultez la note SAP [2630416](https://launchpad.support.sap.com/#/notes/2630416) pour plus d’informations sur la prise en charge du serveur de file d’attente 2.
   Si vous utilisez l’architecture de serveur de file d’attente 2 ([ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)), installez l’agent de ressource resource-agents-sap-4.1.1-12.el7.x86_64 ou une version ultérieure et définissez les ressources comme suit :

    ```bash
    sudo pcs property set maintenance-mode=true
    
    sudo pcs resource create rsc_sap_NW1_ASCS00 SAPInstance \
    InstanceName=NW1_ASCS00_anftstsapvh START_PROFILE="/sapmnt/NW1/profile/NW1_ASCS00_anftstsapvh" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-NW1_ASCS
   
    sudo pcs resource meta g-NW1_ASCS resource-stickiness=3000

    sudo pcs resource create rsc_sap_NW1_ERS01 SAPInstance \
    InstanceName=NW1_ERS01_sapers START_PROFILE="/sapmnt/NW1/profile/NW1_ERS01_sapers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-NW1_AERS
      
    sudo pcs resource meta rsc_sap_NW1_ERS01  resource-stickiness=3000

    sudo pcs constraint colocation add g-NW1_AERS with g-NW1_ASCS -5000
    sudo pcs constraint order start g-NW1_ASCS then start g-NW1_AERS kind=Optional symmetrical=false
    sudo pcs constraint order start g-NW1_ASCS then stop g-NW1_AERS kind=Optional symmetrical=false
   
    sudo pcs node unstandby sap-cl1
    sudo pcs property set maintenance-mode=false
    ```

   Si vous effectuez une mise à niveau à partir d’une version antérieure et que vous passez au serveur de file d’attente 2, consultez la note SAP [2641322](https://launchpad.support.sap.com/#/notes/2641322). 

   > [!NOTE]
   > Les délais d’expiration de la configuration ci-dessus sont simplement des exemples et doivent être adaptés à la configuration SAP spécifique. 

   Vérifiez que l’état du cluster est OK et que toutes les ressources sont démarrées. Le nœud sur lequel les ressources s’exécutent n’a aucune importance.

    ```bash
    sudo pcs status
    
    # Online: [ sap-cl1 sap-cl2 ]
    #
    # Full list of resources:
    #
    # rsc_st_azure    (stonith:fence_azure_arm):      Started sap-cl2
    #  Resource Group: g-NW1_ASCS
    #      fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started sap-cl2
    #      nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started sap-cl2
    #      vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started sap-cl2
    #      rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started sap-cl2
    #  Resource Group: g-NW1_AERS
    #      fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started sap-cl1
    #      nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started sap-cl1
    #      vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started sap-cl1
    #      rsc_sap_NW1_ERS01  (ocf::heartbeat:SAPInstance):   Started sap-cl1
   ```

1. **[A]** Ajouter des règles de pare-feu pour ASCS et ERS sur les deux nœuds.

    ```bash
    # Probe Port of ASCS
    sudo firewall-cmd --zone=public --add-port=62000/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=62000/tcp
    sudo firewall-cmd --zone=public --add-port=3200/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3200/tcp
    sudo firewall-cmd --zone=public --add-port=3600/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3600/tcp
    sudo firewall-cmd --zone=public --add-port=3900/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3900/tcp
    sudo firewall-cmd --zone=public --add-port=8100/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=8100/tcp
    sudo firewall-cmd --zone=public --add-port=50013/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=50013/tcp
    sudo firewall-cmd --zone=public --add-port=50014/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=50014/tcp
    sudo firewall-cmd --zone=public --add-port=50016/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=50016/tcp
    # Probe Port of ERS
    sudo firewall-cmd --zone=public --add-port=62101/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=62101/tcp
    sudo firewall-cmd --zone=public --add-port=3201/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3201/tcp
    sudo firewall-cmd --zone=public --add-port=3301/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3301/tcp
    sudo firewall-cmd --zone=public --add-port=50113/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=50113/tcp
    sudo firewall-cmd --zone=public --add-port=50114/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=50114/tcp
    sudo firewall-cmd --zone=public --add-port=50116/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=50116/tcp
    ```

## <a name="sap-netweaver-application-server-preparation"></a><a name="2d6008b0-685d-426c-b59e-6cd281fd45d7"></a>Préparation du serveur d’applications SAP NetWeaver

   Certaines bases de données exigent que l’installation de l’instance de base de données soit exécutée sur un serveur d’applications. Préparez les machines virtuelles de serveur d’applications pour pouvoir les utiliser dans ce cas de figure.  

   Les étapes ci-dessous partent du principe que vous installez le serveur d’applications sur un serveur différent des serveurs ASCS/SCS et HANA. Dans le cas contraire, certaines des étapes ci-dessous (par exemple la configuration de la résolution de nom d’hôte) ne sont pas nécessaires.  

   Les éléments suivants sont précédés de [**A]** (applicable à PAS et à AAS), de **[P]** (applicable uniquement à PAS) ou de **[S]** (applicable uniquement à AAS).  

1. **[A]** Configurer la résolution de noms d’hôte Vous pouvez utiliser un serveur DNS ou modifier le fichier /etc/hosts sur tous les nœuds. Cet exemple montre comment utiliser le fichier /etc/hosts.
   Remplacez l’adresse IP et le nom d’hôte dans les commandes suivantes :  

    ```bash
    sudo vi /etc/hosts
    ```

   Insérez les lignes suivantes dans le fichier /etc/hosts. Changez l’adresse IP et le nom d’hôte en fonction de votre environnement.

    ```bash
    10.90.90.7    sap-cl1
    10.90.90.8    sap-cl2
    # IP address of the load balancer frontend configuration for SAP Netweaver ASCS
    10.90.90.10   sapascs
    # IP address of the load balancer frontend configuration for SAP Netweaver ERS
    10.90.90.9    sapers
    10.90.90.12   sapa01
    10.90.90.13   sapa02
    ```

1. **[A]** Créer le répertoire sapmnt
   
    ```bash
    sudo mkdir -p /sapmnt/NW1
    sudo mkdir -p /usr/sap/trans

    sudo chattr +i /sapmnt/NW1
    sudo chattr +i /usr/sap/trans
   ```

1. **[A]** Installer le client NFS et autres exigences  

    ```bash
    sudo yum -y install nfs-utils uuidd
    ```

1. **[A]** Ajouter des entrées de montage  

    ```bash
    vi /etc/fstab
    # Add the following lines to fstab, save and exit
    sapnfs.file.core.windows.net:/sapnfsafs/saptrans /usr/sap/trans  nfs vers=4,minorversion=1,sec=sys  0  0
    sapnfs.file.core.windows.net:/sapnfsafs/sapnw1/sapmntNW1 /sapmnt/NW1  nfs vers=4,minorversion=1,sec=sys  0  0
    
    # Mount the file systems
    mount -a 
    ```   

1. **[A]** Configurer le fichier SWAP
 
    ```bash
    sudo vi /etc/waagent.conf
   
    # Set the property ResourceDisk.EnableSwap to y
    # Create and use swapfile on resource disk.
    ResourceDisk.EnableSwap=y
   
    # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
    # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
    # Size of the swapfile.
    ResourceDisk.SwapSizeMB=2000
    ```

   Redémarrer l’Agent pour activer la modification

    ```bash
    sudo service waagent restart
    ```

## <a name="install-database"></a>Installer la base de données

Dans cet exemple, SAP NetWeaver est installé sur SAP HANA. Vous pouvez utiliser n’importe quelle base de données prise en charge pour cette installation. Pour plus d’informations sur l’installation de SAP HANA dans Azure, consultez [Haute disponibilité de SAP HANA sur des machines virtuelles Azure sur Red Hat Enterprise Linux][sap-hana-ha]. For a list of supported databases, see [SAP Note 1928533][1928533].

Installez l’instance de base de données SAP NetWeaver en tant que racine à l’aide d’un nom d’hôte virtuel mappé à l’adresse IP de la configuration frontend d’équilibreur de charge pour la base de données.

Vous pouvez utiliser le paramètre sapinst SAPINST_REMOTE_ACCESS_USER pour autoriser un utilisateur non racine à se connecter à sapinst.

   ```bash
   # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin
   ```

## <a name="sap-netweaver-application-server-installation"></a>Installation de serveur d’applications SAP NetWeaver

Suivez ces étapes pour installer un serveur d’applications SAP.

1. **[A]** Préparez le serveur d’applications.

   Pour préparer le serveur d’applications, suivez la procédure décrite dans la section [Préparation du serveur d’applications SAP NetWeaver](high-availability-guide-rhel-nfs-azure-files.md#2d6008b0-685d-426c-b59e-6cd281fd45d7) ci-dessus.

1. **[A]** Installez le serveur d’applications SAP NetWeaver.  

   Installer un serveur d’applications SAP NetWeaver principal ou supplémentaire.

   Vous pouvez utiliser le paramètre sapinst SAPINST_REMOTE_ACCESS_USER pour autoriser un utilisateur non racine à se connecter à sapinst.

    ```bash
    sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin
    ```

1. **[A]** Mettre à jour la banque d’informations sécurisée SAP HANA

   Mettez à jour la banque d’informations sécurisée SAP HANA pour qu’elle pointe vers le nom virtuel de la configuration de la réplication système SAP HANA.

   Exécutez la commande suivante pour lister les entrées sous la forme `<sapsid>adm`.

    ```bash
    hdbuserstore List
    ```

   La sortie doit répertorier toutes les entrées et ressembler à ce qui suit
    ```bash
    DATA FILE       : /home/nw1adm/.hdb/sapa01/SSFS_HDB.DAT
    KEY FILE        : /home/nw1adm/.hdb/sapa01/SSFS_HDB.KEY
   
    KEY DEFAULT
      ENV : 10.90.90.5:30313
      USER: SAPABAP1
      DATABASE: NW1
    ```

   Dans cet exemple, l’adresse IP de l’entrée par défaut pointe vers la machine virtuelle, et non l’équilibreur de charge. Modifiez l’entrée de sorte qu’elle pointe vers le nom d’hôte virtuel de l’équilibreur de charge. Veillez à utiliser le même port et le même nom de base de données. Par exemple, `30313` et `NW1` dans l’exemple de sortie.


    ```bash
    su - nw1adm
    hdbuserstore SET DEFAULT nw1db:30313@NW1 SAPABAP1 <password of ABAP schema>
    ```

## <a name="test-cluster-setup"></a>Tester la configuration du cluster

Testez minutieusement votre cluster Pacemaker. [Exécutez les tests de basculement classiques](./high-availability-guide-rhel.md#test-the-cluster-setup).

## <a name="next-steps"></a>Étapes suivantes

* [Guide de haute disponibilité pour SAP NW sur les machines virtuelles Azure sur RHEL pour les applications SAP multi-SID](./high-availability-guide-rhel-multi-sid.md)
* [Planification et implémentation de machines virtuelles Azure pour SAP][planning-guide]
* [Déploiement de machines virtuelles Azure pour SAP][deployment-guide]
* [Déploiement SGBD de machines virtuelles Azure pour SAP][dbms-guide]
* Pour savoir comment établir une haute disponibilité et planifier la récupération d’urgence de SAP HANA sur Azure (grandes instances), consultez [Haute disponibilité et récupération d’urgence de SAP HANA (grandes instances) sur Azure](hana-overview-high-availability-disaster-recovery.md).
* Pour savoir comment établir une haute disponibilité et planifier la récupération d’urgence de SAP HANA sur des machines virtuelles Azure, consultez [Haute disponibilité de SAP HANA sur des machines virtuelles Azure][sap-hana-ha]