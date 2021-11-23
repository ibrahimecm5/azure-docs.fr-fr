---
title: Haute disponibilité des machines virtuelles Azure pour SAP NW sur SLES avec NFS sur Azure Files | Microsoft Docs
description: Guide de haute disponibilité pour SAP NetWeaver sur SUSE Linux Enterprise Server avec NFS sur Azure Files pour les applications SAP
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-sap
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 11/16/2021
ms.author: radeltch
ms.openlocfilehash: 3000f5414c51b1ce7842367bb4449c69fa9aadfa
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132557366"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-suse-linux-enterprise-server-with-nfs-on-azure-files"></a>Haute disponibilité pour SAP NetWeaver sur les machines virtuelles Azure sur SUSE Linux Enterprise Server avec NFS sur Azure Files  

[dbms-guide]:dbms_guide_general.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[afs-azure-doc]:../../../storage/files/storage-files-introduction.md
[afs-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=storage&regions=all

[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[2578899]:https://launchpad.support.sap.com/#/notes/2578899
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736
[2360818]:https://launchpad.support.sap.com/#/notes/2360818


[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[suse-ha-guide]:https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
[suse-drbd-guide]:https://www.suse.com/documentation/sle-ha-12/singlehtml/book_sleha_techguides/book_sleha_techguides.html
[suse-relnotes]:https://www.suse.com/releasenotes/index.html

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md

Cet article explique la manière de déployer et configurer des machines virtuelles, d’installer le framework du cluster et d’installer un système SAP NetWeaver à haute disponibilité à l’aide de [NFS sur Azure Files](../../../storage/files/files-nfs-protocol.md). Les exemples de configuration utilisent des machines virtuelles qui s’exécutent sur SUSE Linux Enterprise Server (SLES).      

## <a name="prerequisites"></a>Prérequis  

* [Documentation Azure Files][afs-azure-doc] 
* Note SAP [1928533][1928533], qui contient :  
  * une liste des tailles de machines virtuelles Azure prises en charge pour le déploiement de logiciels SAP
  * des informations importantes sur la capacité en fonction de la taille des machines virtuelles Azure
  * les logiciels SAP pris en charge et les combinaisons entre système d’exploitation et base de données
  * la version du noyau SAP requise pour Windows et Linux sur Microsoft Azure
* La note SAP [2015553][2015553] répertorie les conditions préalables au déploiement de logiciels SAP pris en charge par SAP sur Azure.
* La note SAP [2205917][2205917] contient des paramètres de système d’exploitation recommandés pour SUSE Linux Enterprise Server pour les applications SAP
* La note SAP [2178632][2178632] contient des informations détaillées sur toutes les métriques de surveillance rapportées pour SAP sur Azure.
* La note SAP [2191498][2191498] contient la version requise de l’agent hôte SAP pour Linux sur Azure.
* La note SAP [2243692][2243692] contient des informations sur les licences SAP sur Linux dans Azure.
* La note SAP [1984787][1984787] contient des informations sur SUSE Linux Enterprise Server 12.
* La note SAP [2578899][2578899] contient des informations sur SUSE Linux Enterprise Server 15.
* La note SAP [1999351][1999351] contient des informations de dépannage supplémentaires pour l’extension d’analyse Azure améliorée pour SAP.
* Le [WIKI de la communauté SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) contient toutes les notes SAP requises pour Linux.
* [Planification et implémentation de machines virtuelles Azure pour SAP sur Linux][planning-guide]
* [Déploiement de machines virtuelles Azure pour SAP sur Linux][deployment-guide]
* [Déploiement SGBD de machines virtuelles Azure pour SAP sur Linux][dbms-guide]
* [Guides sur les meilleures pratiques pour SUSE SAP HA][suse-ha-guide] Les guides contiennent toutes les informations nécessaires pour configurer la réplication locale des systèmes Netweaver HP et SAP HANA. Utilisez ces guides comme planning de référence. Ils fournissent des informations beaucoup plus détaillées.
* [Notes de publication de SUSE High Availability Extension][suse-relnotes]


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

:::image type="complex" source="./media/high-availability-guide-suse/high-availability-guide-suse-nfs-azure-files.png" alt-text="Haute disponibilité SAP NetWeaver avec NFS sur Azure Files":::
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
- Nous vous recommandons de déployer sur SLES 15 SP2 ou une version ultérieure pour tirer parti des [améliorations apportées aux clients NFS](../../../storage/files/storage-troubleshooting-files-nfs.md#ls-hangs-for-large-directory-enumeration-on-some-kernels).     
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

Suivez les étapes décrites à la page [Configuration de Pacemaker sur SUSE Linux Enterprise Server dans Azure](high-availability-guide-suse-pacemaker.md) pour créer un cluster Pacemaker de base pour SAP (A)SCS.

### <a name="installation"></a>Installation

Les éléments suivants sont précédés de **[A]** (applicable à tous les nœuds), de **[1]** (applicable uniquement au nœud 1) ou de **[2]** (applicable uniquement au nœud 2).

1. **[A]** Installez la dernière version du connecteur SUSE.

    ```bash
    sudo zypper install sap-suse-cluster-connector
    ```

   > [!NOTE]
   > Le problème connu lié à l’utilisation d’un tiret dans les noms d’hôtes est résolu avec la version **3.1.1** du package **sap-suse-cluster-connector**. Veillez à utiliser au moins la version 3.1.1 du package sap-suse-cluster-connector si vous utilisez des nœuds de cluster avec un tiret dans le nom d’hôte. Dans le cas contraire, le cluster ne fonctionnera pas. 

   Vérifiez que vous avez installé la nouvelle version du connecteur de cluster SUSE SAP. L’ancienne version s’appelle sap_suse_cluster_connector et la nouvelle **sap-suse-cluster-connector**.  

2. **[A]** Mettre à jour les agents de ressources SAP  
   
   Un correctif pour les agents de ressources est nécessaire pour utiliser la nouvelle configuration décrite dans cet article. Vous pouvez vérifier si le correctif est déjà installé avec la commande suivante.

    ```bash
    sudo grep 'parameter name="IS_ERS"' /usr/lib/ocf/resource.d/heartbeat/SAPInstance
    ```

   La sortie doit ressembler à ce qui suit :

    ```bash
    <parameter name="IS_ERS" unique="0" required="0">;
    ```

   Si la commande grep ne trouve pas le paramètre IS_ERS, vous devez installer le correctif logiciel répertorié dans la [page de téléchargement SUSE](https://download.suse.com/patch/finder/#bu=suse&familyId=&productId=&dateRange=&startDate=&endDate=&priority=&architecture=&keywords=resource-agents).


3. **[A]** Configurer la résolution de nom d’hôte

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

4. **[1]** Créez les répertoires SAP sur le partage NFS.  
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

## <a name="prepare-for-sap-netweaver-installation"></a>Préparer l’installation de SAP NetWeaver

1. **[A]** Créer les répertoires partagés

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

2. **[A]** Montez les systèmes de fichiers, qui ne seront pas contrôlés par le cluster Pacemaker.  

    ```bash
    vi /etc/fstab
    # Add the following lines to fstab, save and exit
    sapnfs.file.core.windows.net:/sapnfsafs/saptrans /usr/sap/trans  nfs vers=4,minorversion=1,sec=sys  0  0
    sapnfs.file.core.windows.net:/sapnfsafs/sapnw1/sapmntNW1 /sapmnt/NW1  nfs vers=4,minorversion=1,sec=sys  0  0
    sapnfs.file.core.windows.net:/sapnfsafs/sapnw1/usrsapNW1sys/ /usr/sap/NW1/SYS  nfs vers=4,minorversion=1,sec=sys  0  0
    
    # Mount the file systems
    mount -a 
    ```

3. **[A]** Configurer le fichier SWAP

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

### <a name="installing-sap-netweaver-ascsers"></a>Installation de SAP NetWeaver ASC/ERS

1. **[1]** Créer une ressource IP virtuelle et la sonde d’intégrité pour l’instance ASCS

   > [!IMPORTANT]
   > Nous vous recommandons d’utiliser l’agent de ressources azure-lb, qui fait partie du package resource-agents, avec la configuration requise suivante pour la version du package :
   > - Pour SLES 12 SP4/SP5, la version minimum est resource-agents-4.3.018.a7fb5035-3.30.1.  
   > - Pour SLES 15 et les versions ultérieures, la version minimale est resource-agents-4.3.0184.6ee15eb2-4.13.1.  
   

    ```bash
    sudo crm node standby sap-cl2
    sudo crm configure primitive fs_NW1_ASCS Filesystem device='sapnfs.file.core.windows.net:/sapnfsafs/sapnw1/usrsapNW1ascs' directory='/usr/sap/NW1/ASCS00' fstype='nfs' options='sec=sys,vers=4.1' \
      op start timeout=60s interval=0 \
      op stop timeout=60s interval=0 \
      op monitor interval=20s timeout=40s
    
    sudo crm configure primitive vip_NW1_ASCS IPaddr2 \
      params ip=10.90.90.10 cidr_netmask=24 \
      op monitor interval=10 timeout=20
    
    sudo crm configure primitive nc_NW1_ASCS azure-lb port=62000
    
    sudo crm configure group g-NW1_ASCS fs_NW1_ASCS nc_NW1_ASCS vip_NW1_ASCS \
       meta resource-stickiness=3000
    ```

   Vérifiez que l’état du cluster est OK et que toutes les ressources sont démarrées. Le nœud sur lequel les ressources s’exécutent n’a aucune importance.

    ```bash 
    sudo crm_mon -r
    # Node sap-cl2: standby
    # Online: [ sap-cl1 ]
    #
    # Full list of resources:
    #
    # stonith-sbd     (stonith:external/sbd): Started sap-cl1
    # Resource Group: g-NW1_ASCS
    #  fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started sap-cl1
    #  nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started sap-cl1
    #  vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started sap-cl1
   
    ```
  
2. **[1]** Installer SAP NetWeaver ASCS  

   Installez SAP NetWeaver ASCS comme racine sur le premier nœud à l’aide d’un nom d’hôte virtuel mappé à l’adresse IP de la configuration front-end de l’équilibreur de charge pour l’instance ASCS, par exemple ***sapascs** _, _*_10.90.90.10_*_, et du numéro d’instance utilisé pour la sonde de l’équilibreur de charge, par exemple _*_00_**.

   Vous pouvez utiliser le paramètre sapinst SAPINST_REMOTE_ACCESS_USER pour autoriser un utilisateur non racine à se connecter à sapinst. Vous pouvez utiliser le paramètre SAPINST_USE_HOSTNAME pour installer SAP à l’aide du nom d’hôte virtuel.

    ```bash
    sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=<virtual_hostname>
    ```

   Si aucun sous-dossier n’est créé dans /usr/sap/**NW1**/ASCS **00** lors de l’installation, essayez de définir le propriétaire et le groupe du dossier ASCS **00**, puis réessayez. 

    ```bash
    chown nw1adm /usr/sap/NW1/ASCS00
    chgrp sapsys /usr/sap/NW1/ASCS00
    ```

3. **[1]** Créer une ressource IP virtuelle et la sonde d’intégrité pour l’instance ERS

    ```bash
    sudo crm node online sap-cl2
    sudo crm node standby sap-cl1
    sudo crm configure primitive fs_NW1_ERS Filesystem device='sapnfs.file.core.windows.net:/sapnfsafs/sapnw1/usrsapNW1ers' directory='/usr/sap/NW1/ERS01' fstype='nfs' options='sec=sys,vers=4.1' \
      op start timeout=60s interval=0 \
      op stop timeout=60s interval=0 \
      op monitor interval=20s timeout=40s
   
    sudo crm configure primitive vip_NW1_ERS IPaddr2 \
      params ip=10.90.90.9 cidr_netmask=24 \
      op monitor interval=10 timeout=20
   
    sudo crm configure primitive nc_NW1_ERS azure-lb port=62101
    
    sudo crm configure group g-NW1_ERS fs_NW1_ERS nc_NW1_ERS vip_NW1_ERS
    ```

   Vérifiez que l’état du cluster est OK et que toutes les ressources sont démarrées. Le nœud sur lequel les ressources s’exécutent n’a aucune importance.

    ```bash
    sudo crm_mon -r
   
    # Node sap-cl1: standby
    # Online: [ sap-cl2 ]
    # 
    # Full list of resources:
    #
    # stonith-sbd     (stonith:external/sbd): Started sap-cl2
    #  Resource Group: g-NW1_ASCS
    #      fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started sap-cl2
    #      nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started sap-cl2
    #      vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started sap-cl2
    #  Resource Group: g-NW1_ERS
    #      fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started sap-cl2 
    #      nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started sap-cl2
    #      vip_NW1_ERS  (ocf::heartbeat:IPaddr2):     Started sap-cl2
    ```

4. **[2]** Installer SAP NetWeaver ERS

   Installez SAP NetWeaver ERS comme racine sur le deuxième nœud à l’aide d’un nom d’hôte virtuel mappé à l’adresse IP de la configuration frontend de l’équilibreur de charge pour l’ERS, par exemple **sapers**, **10.90.90.9**, et du numéro d’instance que vous avez utilisé pour la sonde de l’équilibreur de charge, par exemple **01**.

   Vous pouvez utiliser le paramètre sapinst SAPINST_REMOTE_ACCESS_USER pour autoriser un utilisateur non racine à se connecter à sapinst. Vous pouvez utiliser le paramètre SAPINST_USE_HOSTNAME pour installer SAP à l’aide du nom d’hôte virtuel.

    ```bash
    <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=virtual_hostname
    ```

   > [!NOTE]
   > Utilisez SWPM SP 20 PL 05 ou ultérieur. Les versions antérieures ne définissent pas les autorisations correctement et l’installation échouera.

   Si aucun sous-dossier n’est créé dans /usr/sap/**NW1**/ERS **01** lors de l’installation, essayez de définir le propriétaire et le groupe du dossier ERS **01**, puis réessayez.

    ```bash
    chown nw1adm /usr/sap/NW1/ERS01
    chgrp sapsys /usr/sap/NW1/ERS01
    ```

5. **[1]** Adapter les profils d’instance ASCS/SCS et ERS
 
   * Profil ASCS/SCS

    ```bash
    sudo vi /sapmnt/NW1/profile/NW1_ASCS00_sapascs
    
    # Change the restart command to a start command
    #Restart_Program_01 = local $(_EN) pf=$(_PF)
    Start_Program_01 = local $(_EN) pf=$(_PF)
    
    # Add the following lines
    service/halib = $(DIR_CT_RUN)/saphascriptco.so
    service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector
    
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
    
    # Add the following lines
    service/halib = $(DIR_CT_RUN)/saphascriptco.so
    service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector
    
    # remove Autostart from ERS profile
    # Autostart = 1
    ```

6. **[A]** Configurer Keep Alive

   La communication entre le serveur d’applications SAP NetWeaver et l’ASCS/SCS est routée par l’intermédiaire d’un équilibreur de charge logiciel. L’équilibreur de charge déconnecte les connexions inactives après un délai configurable. Pour éviter cela, vous devez définir un paramètre dans le profil SAP NetWeaver ASCS/SCS, si vous utilisez ENSA1. Modifiez les paramètres `keepalive` du système Linux sur tous les serveurs SAP pour ENSA1/ENSA2. Pour plus d’informations, consultez la [Note SAP 1410736][1410736].

    ```bash
    # Change the Linux system configuration
    sudo sysctl net.ipv4.tcp_keepalive_time=300
    ```

7. **[A]** Configurer les utilisateurs SAP après l’installation

    ```bash
    # Add sidadm to the haclient group
    sudo usermod -aG haclient nw1adm
    ```

8. **[1]** Ajoutez les services ASCS et ERS SAP au fichier `sapservice`

   Ajoutez l’entrée de service ASCS au deuxième nœud et copiez l’entrée de service ERS dans le premier nœud.

    ```bash
    cat /usr/sap/sapservices | grep ASCS00 | sudo ssh sap-cl2 "cat >>/usr/sap/sapservices"
    sudo ssh sap-cl2 "cat /usr/sap/sapservices" | grep ERS01 | sudo tee -a /usr/sap/sapservices
    ```

9. **[1]** Créer les ressources de cluster SAP

   Si vous utilisez l’architecture de serveur de file d’attente 1 (ENSA1), définissez les ressources comme suit :

    ```bash
    sudo crm configure property maintenance-mode="true"
   
    sudo crm configure primitive rsc_sap_NW1_ASCS00 SAPInstance \
     operations \$id=rsc_sap_NW1_ASCS00-operations \
     op monitor interval=11 timeout=60 on-fail=restart \
     params InstanceName=NW1_ASCS00_sapascs START_PROFILE="/sapmnt/NW1/profile/NW1_ASCS00_sapascs" \
     AUTOMATIC_RECOVER=false \
     meta resource-stickiness=5000 failure-timeout=60 migration-threshold=1 priority=10
   
    sudo crm configure primitive rsc_sap_NW1_ERS01 SAPInstance \
     operations \$id=rsc_sap_NW1_ERS01-operations \
     op monitor interval=11 timeout=60 on-fail=restart \
     params InstanceName=NW1_ERS01_sapers START_PROFILE="/sapmnt/NW1/profile/NW1_ERS01_sapers" AUTOMATIC_RECOVER=false IS_ERS=true \
     meta priority=1000
   
    sudo crm configure modgroup g-NW1_ASCS add rsc_sap_NW1_ASCS00
    sudo crm configure modgroup g-NW1_ERS add rsc_sap_NW1_ERS01
   
    sudo crm configure colocation col_sap_NW1_no_both -5000: g-NW1_ERS g-NW1_ASCS
    sudo crm configure location loc_sap_NW1_failover_to_ers rsc_sap_NW1_ASCS00 rule 2000: runs_ers_NW1 eq 1
    sudo crm configure order ord_sap_NW1_first_start_ascs Optional: rsc_sap_NW1_ASCS00:start rsc_sap_NW1_ERS01:stop symmetrical=false
   
    sudo crm node online sap-cl1
    sudo crm configure property maintenance-mode="false"
    ```

   SAP a introduit la prise en charge du serveur de file d’attente 2, y compris la réplication, avec SAP NW 7.52. À partir de la plateforme ABAP 1809, le serveur de file d’attente 2 est installé par défaut. Consultez la note SAP [2630416](https://launchpad.support.sap.com/#/notes/2630416) pour plus d’informations sur la prise en charge du serveur de file d’attente 2.  

   Si vous utilisez l’architecture de serveur de file d’attente 2 ([ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)), définissez les ressources comme suit :

    ```bash
    sudo crm configure property maintenance-mode="true"
   
    sudo crm configure primitive rsc_sap_NW1_ASCS00 SAPInstance \
     operations \$id=rsc_sap_NW1_ASCS00-operations \
     op monitor interval=11 timeout=60 on-fail=restart \
     params InstanceName=NW1_ASCS00_sapascs START_PROFILE="/sapmnt/NW1/profile/NW1_ASCS00_sapascs" \
     AUTOMATIC_RECOVER=false \
     meta resource-stickiness=5000
   
    sudo crm configure primitive rsc_sap_NW1_ERS01 SAPInstance \
     operations \$id=rsc_sap_NW1_ERS01-operations \
     op monitor interval=11 timeout=60 on-fail=restart \
     params InstanceName=NW1_ERS01_sapers START_PROFILE="/sapmnt/NW1/profile/NW1_ERS01_sapers" AUTOMATIC_RECOVER=false IS_ERS=true
   
    sudo crm configure modgroup g-NW1_ASCS add rsc_sap_NW1_ASCS00
    sudo crm configure modgroup g-NW1_ERS add rsc_sap_NW1_ERS01
    
    sudo crm configure colocation col_sap_NW1_no_both -5000: g-NW1_ERS g-NW1_ASCS
    sudo crm configure order ord_sap_NW1_first_start_ascs Optional: rsc_sap_NW1_ASCS00:start rsc_sap_NW1_ERS01:stop symmetrical=false
   
    sudo crm node online sap-cl1
    sudo crm configure property maintenance-mode="false"
    ```

   Si vous effectuez une mise à niveau à partir d’une version antérieure et que vous passez au serveur de file d’attente 2, consultez la note SAP [2641019](https://launchpad.support.sap.com/#/notes/2641019). 

   Vérifiez que l’état du cluster est OK et que toutes les ressources sont démarrées. Le nœud sur lequel les ressources s’exécutent n’a aucune importance.

    ```bash
    sudo crm_mon -r
    # Full list of resources:
    # 
    # stonith-sbd     (stonith:external/sbd): Started sap-cl2
    #  Resource Group: g-NW1_ASCS
    #      fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started sap-cl1
    #      nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started sap-cl1
    #      vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started sap-cl1
    #      rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started sap-cl1
    #  Resource Group: g-NW1_ERS
    #      fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started sap-cl2
    #      nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started sap-cl2
    #      vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started sap-cl2
    #      rsc_sap_NW1_ERS01  (ocf::heartbeat:SAPInstance):   Started sap-cl1
    ```

## <a name="sap-netweaver-application-server-preparation"></a><a name="2d6008b0-685d-426c-b59e-6cd281fd45d7"></a>Préparation du serveur d’applications SAP NetWeaver 

Certaines bases de données exigent que l’installation de l’instance de base de données soit exécutée sur un serveur d’applications. Préparez les machines virtuelles de serveur d’applications pour pouvoir les utiliser dans ce cas de figure.

Les étapes ci-dessous partent du principe que vous installez le serveur d’applications sur un serveur différent des serveurs ASCS/SCS et HANA. Dans le cas contraire, certaines des étapes ci-dessous (par exemple la configuration de la résolution de nom d’hôte) ne sont pas nécessaires.

Les éléments suivants sont précédés de [**A]** (applicable à PAS et à AAS), de **[P]** (applicable uniquement à PAS) ou de **[S]** (applicable uniquement à AAS).


1. **[A]** Configurer le système d’exploitation

   Réduisez la taille du cache d’intégrité. Pour plus d’informations, consultez [Faibles performances en écriture sur les serveurs SLES 11/12 avec une grande quantité de RAM](https://www.suse.com/support/kb/doc/?id=7010287).

    ```bash
    sudo vi /etc/sysctl.conf
    # Change/set the following settings
    vm.dirty_bytes = 629145600
    vm.dirty_background_bytes = 314572800
    ```

1. **[A]** Configurer la résolution de nom d’hôte

   Vous pouvez utiliser un serveur DNS ou modifier le fichier /etc/hosts sur tous les nœuds. Cet exemple montre comment utiliser le fichier /etc/hosts.
   Remplacez l’adresse IP et le nom d’hôte dans les commandes suivantes

    ```bash
    sudo vi /etc/hosts
    ```

   Insérez les lignes suivantes dans le fichier /etc/hosts. Modifiez l’adresse IP et le nom d’hôte en fonction de votre environnement

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

1. **[A]** Montez les systèmes de fichiers.

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

Dans cet exemple, SAP NetWeaver est installé sur SAP HANA. Vous pouvez utiliser n’importe quelle base de données prise en charge pour cette installation. Pour plus d’informations sur l’installation de SAP HANA dans Azure, consultez [Haute disponibilité de SAP HANA sur des machines virtuelles Azure][sap-hana-ha]. Pour obtenir la liste des bases de données prises en charge, consultez la [note SAP 1928533][1928533].

Installez l’instance de base de données SAP NetWeaver en tant que racine à l’aide d’un nom d’hôte virtuel mappé à l’adresse IP de la configuration frontend d’équilibreur de charge pour la base de données.  
Vous pouvez utiliser le paramètre sapinst SAPINST_REMOTE_ACCESS_USER pour autoriser un utilisateur non racine à se connecter à sapinst.

   ```bash
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin
   ```

## <a name="sap-netweaver-application-server-installation"></a>Installation de serveur d’applications SAP NetWeaver

Suivez ces étapes pour installer un serveur d’applications SAP.

1. **[A]** Préparer le serveur d’applications Pour préparer le serveur d’applications, suivez la procédure décrite dans la section [Préparation du serveur d’applications SAP NetWeaver](high-availability-guide-suse-nfs-azure-files.md#2d6008b0-685d-426c-b59e-6cd281fd45d7) ci-dessus.

2. **[A]** Installez le serveur d’applications SAP NetWeaver.  
   Installer un serveur d’applications SAP NetWeaver principal ou supplémentaire.

   Vous pouvez utiliser le paramètre sapinst SAPINST_REMOTE_ACCESS_USER pour autoriser un utilisateur non racine à se connecter à sapinst.

    ```bash
    sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin
   ```

3. **[A]** Mettre à jour la banque d’informations sécurisée SAP HANA

   Mettez à jour la banque d’informations sécurisée SAP HANA pour qu’elle pointe vers le nom virtuel de la configuration de la réplication système SAP HANA.

   Exécutez la commande suivante pour répertorier les entrées
    ```bash
    hdbuserstore List
    ```

   La commande doit lister toutes les entrées et ressembler à ceci :
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

Testez minutieusement votre cluster Pacemaker. [Exécutez les tests de basculement classiques](./high-availability-guide-suse.md#test-the-cluster-setup).

## <a name="next-steps"></a>Étapes suivantes

* [Guide de haute disponibilité pour SAP NW sur les machines virtuelles Azure sur SLES pour les applications SAP multi-SID](./high-availability-guide-suse-multi-sid.md)
* [Planification et implémentation de machines virtuelles Azure pour SAP][planning-guide]
* [Déploiement de machines virtuelles Azure pour SAP][deployment-guide]
* [Déploiement SGBD de machines virtuelles Azure pour SAP][dbms-guide]
* Pour savoir comment établir une haute disponibilité et planifier la récupération d’urgence de SAP HANA sur des machines virtuelles Azure, consultez [Haute disponibilité de SAP HANA sur des machines virtuelles Azure][sap-hana-ha]