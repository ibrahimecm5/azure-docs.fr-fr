---
title: Vue d’ensemble du cluster de basculement Windows Server
description: 'Découvrez les différences observables avec la technologie de cluster de basculement Windows Server lorsqu’elle est utilisée avec SQL Server sur des machines virtuelles Azure, telles que les groupes de disponibilité et les instances de cluster de basculement. '
services: virtual-machines
documentationCenter: na
author: rajeshsetlem
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/10/2021
ms.author: rsetlem
ms.reviewer: mathoma
ms.openlocfilehash: 66899b7b4c5a9cb77b7545d671ac27433f927d5a
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132156872"
---
# <a name="windows-server-failover-cluster-with-sql-server-on-azure-vms"></a>Cluster de basculement Windows Server avec SQL Server sur des machines virtuelles Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Cet article décrit les différences observables lors de l’utilisation de la fonctionnalité de cluster de basculement Windows Server avec SQL Server sur des machines virtuelles Azure pour la haute disponibilité et la récupération d’urgence (HADR), comme pour les groupes de disponibilité Always On ou les instances de cluster de basculement (FCI). 

Pour en savoir plus sur la fonctionnalité Windows proprement dite, consultez la [documentation sur le cluster de basculement Windows Server](/windows-server/failover-clustering/failover-clustering-overview).

## <a name="overview"></a>Vue d’ensemble

Les solutions de haute disponibilité SQL Server sur Windows, telles que les groupes de disponibilité Always On ou les instances de cluster de basculement, s’appuient sur le service de clustering de basculement Windows Server sous-jacent.

Le service de cluster supervise les connexions réseau et l’intégrité des nœuds du cluster. Cette supervision s’ajoute aux contrôles d’intégrité effectués par SQL Server dans le cadre de la fonctionnalité de groupe de disponibilité ou d’instance de cluster de basculement. Si le service de cluster ne parvient pas à atteindre le nœud, ou si le groupe de disponibilité ou le rôle FCI du cluster n’est plus sain, le service de cluster lance des actions de récupération appropriées afin de récupérer et de remettre en ligne les applications et les services, soit sur le même nœud, soit sur un autre nœud du cluster.

## <a name="cluster-health-monitoring"></a>Supervision de l’intégrité du cluster

Pour assurer une haute disponibilité, le cluster doit garantir l’intégrité des différents composants qui forment la solution de cluster. Le service de cluster supervise l’intégrité du cluster en fonction d’un certain nombre de paramètres système et réseau, afin de détecter les défaillances et d’y répondre. 

La définition du seuil pour la déclaration d’une défaillance est importante afin de parvenir à un équilibre entre la réponse rapide à une défaillance et la prévention des fausses défaillances.

Il existe deux stratégies de supervision :

| Surveillance  | Description |
|-|-|
| Aggressive | Permet de rapidement détecter les défaillances et récupérer les défaillances matérielles, ce qui offre les plus hauts niveaux de disponibilité. Le service de cluster et SQL Server sont tous deux moins tolérants envers les défaillances temporaires et, dans certains cas, sont susceptibles de faire basculer prématurément les ressources en cas de pannes temporaires. Une fois la défaillance détectée, l’action corrective qui suit peut prendre davantage de temps. |
| Souple | Fournit une détection des défaillances plus indulgente, avec une plus grande tolérance envers les courts problèmes réseau temporaires. Évite les défaillances temporaires, mais introduit également le risque de retarder la détection d’une défaillance réelle. |

Des paramètres agressifs dans un environnement de cluster dans le cloud peuvent entraîner des défaillances prématurées et des interruptions plus longues. Par conséquent, une stratégie de supervision souple est recommandée pour les clusters de basculement sur les machines virtuelles Azure. Pour en savoir plus sur le réglage des paramètres de seuil, consultez les [bonnes pratiques en matière de clusters](hadr-cluster-best-practices.md#relaxed-monitoring). 

## <a name="cluster-heartbeat"></a>Pulsation de cluster

Voici les principaux paramètres qui affectent la pulsation du cluster et la détection de l’intégrité entre les nœuds :

| Paramètre | Description |
|-|-|
| Retarder | Définit la fréquence à laquelle les pulsations de cluster sont envoyées entre les nœuds. Le délai correspond au nombre de secondes avant l’envoi de la pulsation suivante. Dans le même cluster, il peut y avoir différents paramètres de délai configurés entre les nœuds du même sous-réseau, et entre les nœuds de sous-réseaux différents. |
| Seuil | Le seuil est le nombre de pulsations qui peuvent être manquées avant que le cluster ne prenne l’action de récupération. Dans le même cluster, il peut y avoir différents paramètres de délai configurés entre les nœuds du même sous-réseau, et entre les nœuds de sous-réseaux différents. |

Les valeurs par défaut de ces paramètres peuvent être trop basses pour les environnements cloud, et peuvent entraîner des défaillances inutiles dues à des problèmes réseau temporaires. Pour bénéficier d’une plus grande tolérance, utilisez des paramètres de seuil souples pour les clusters de basculement dans les machines virtuelles Azure. Pour plus d’informations, consultez les [bonnes pratiques en matière de clusters](hadr-cluster-best-practices.md#heartbeat-and-threshold). 

## <a name="quorum"></a>Quorum

Bien qu’un cluster à deux nœuds puisse fonctionner sans [ressource de quorum](/windows-server/storage/storage-spaces/understand-quorum), les clients sont strictement tenus d’utiliser une ressource de quorum pour prendre en charge la production. La validation du cluster ne transmet pas de cluster sans ressource de quorum. 

Techniquement, un cluster à trois nœuds peut survivre à la perte d’un seul nœud (jusqu’à deux nœuds) sans ressource de quorum. Cependant, une fois que le cluster a atteint deux nœuds, il existe un risque que les ressources de cluster soient déconnectées pour empêcher un scénario Split-Brain si un nœud est perdu ou qu’un problème de communication surgit entre les nœuds. La configuration d’une ressource de quorum permet aux ressources de cluster de rester en ligne avec un seul nœud en ligne.

Le témoin de disque est l’option de quorum la plus résiliente, mais pour utiliser un témoin de disque sur SQL Server sur une machine virtuelle Azure, vous devez utiliser un disque partagé Azure qui impose certaines limitations à la solution de haute disponibilité. Par conséquent, utilisez un témoin de disque lorsque vous configurez votre instance de cluster de basculement avec des disques partagés Azure ; sinon, utilisez un témoin de cloud dans la mesure du possible. 

Le tableau suivant liste les options de quorum disponibles pour SQL Server sur les machines virtuelles Azure : 

|  |[Témoin cloud](/windows-server/failover-clustering/deploy-cloud-witness) |[Témoin de disque](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum) |[Témoin de partage de fichiers](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)  |
|---------|---------|---------|---------|
|**Systèmes d’exploitation pris en charge**| Windows Server 2016+ |Tous | Tous|
| **Description** | Un témoin de cloud est un type de témoin de quorum de cluster de basculement qui utilise Microsoft Azure pour fournir un vote sur le quorum du cluster. La taille par défaut est d’environ 1 Mo et contient uniquement l’horodatage. Un témoin de cloud est idéal pour les déploiements sur plusieurs sites, dans plusieurs zones et plusieurs régions. Utilisez un témoin de cloud dans la mesure du possible, sauf si vous disposez d’une solution de cluster de basculement avec stockage partagé. | Un témoin de disque est un petit disque en cluster qui se trouve dans le groupe de stockage disponible du cluster. Ce disque est hautement disponible et peut basculer d’un nœud vers un autre. Il contient une copie de la base de données du cluster, dont la taille par défaut est inférieure à 1 Go. Le témoin de disque est l’option de quorum par défaut pour tous les clusters qui utilisent des disques partagés Azure (ou toute solution de disque partagé comme SCSI, iSCSI ou SAN Fiber Channel partagé).  Un volume partagé en cluster ne peut pas être utilisé comme témoin de disque. Configurer un disque partagé Azure comme témoin de disque.  | Un témoin de partage de fichiers est un partage de fichiers SMB qui est généralement configuré sur un serveur de fichiers exécutant Windows Server. Il conserve les informations de clustering dans un fichier witness.log, mais ne stocke pas de copie de la base de données de clusters. Dans Azure, vous pouvez configurer un partage de fichiers sur une machine virtuelle distincte dans le même réseau virtuel. Utilisez un témoin de partage de fichiers si un témoin de disque ou un témoin de cloud n’est pas disponible dans votre environnement. | 

Pour bien démarrer, consultez [Configurer un quorum de cluster](hadr-cluster-quorum-configure-how-to.md). 


## <a name="virtual-network-name-vnn"></a>Nom de réseau virtuel (VNN)

Pour correspondre à l’expérience locale de connexion à votre écouteur de groupe de disponibilité ou instance de cluster de basculement, déployez vos machines virtuelles SQL Server sur plusieurs sous-réseaux au sein du même réseau virtuel. Le fait de disposer de plusieurs sous-réseaux nie la nécessité de la dépendance supplémentaire sur un Azure Load Balancer pour acheminer le trafic vers votre solution HADR.  Pour plus d’informations, consultez [Groupe de disponibilité à plusieurs sous-réseaux](availability-group-manually-configure-prerequisites-tutorial-multi-subnet.md) et [Instance de cluster de basculement à plusieurs sous-réseaux](failover-cluster-instance-prepare-vm.md#subnets). 

Dans un environnement local traditionnel, les ressources de cluster telles que les instances de cluster de basculement ou les groupes de disponibilité Always On s’appuient sur le nom du réseau virtuel pour router le trafic vers la cible appropriée, soit l’instance de cluster de basculement, soit l’écouteur du groupe de disponibilité Always On. Le nom virtuel lie l’adresse IP dans le système DNS, et les clients peuvent utiliser le nom virtuel ou l’adresse IP pour se connecter à leur cible de haute disponibilité, quel que soit le nœud actuellement propriétaire de la ressource. Le VNN est un nom de réseau et une adresse gérés par le cluster, et le service de cluster déplace l’adresse réseau d’un nœud à un autre pendant un événement de basculement. En cas de défaillance, l’adresse est placée hors connexion sur le réplica principal d’origine et mise en ligne sur le nouveau réplica principal.

Sur les machines virtuelles Azure dans un seul sous-réseau, un composant supplémentaire est nécessaire pour acheminer le trafic du client vers le nom de réseau virtuel de la ressource de cluster (instance de cluster de basculement ou écouteur d’un groupe de disponibilité). Dans Azure, un équilibreur de charge contient l’adresse IP du VNN sur lequel s’appuient les ressources SQL Server de cluster, et est nécessaire pour router le trafic vers la cible de haute disponibilité appropriée. L’équilibreur de charge détecte également les défaillances au niveau des composants réseau, et déplace l’adresse vers un nouvel hôte. 

L’équilibreur de charge distribue les flux entrants arrivant au frontal, puis achemine ce trafic vers les instances définies par le pool principal. Vous configurez le flux de trafic à l’aide de règles d’équilibrage de charge et de sondes d’intégrité. Avec une instance de cluster de basculement SQL Server, les instances de pool de back-ends sont les machines virtuelles Azure exécutant SQL Server, tandis qu’avec des groupes de disponibilité le pool de back-ends est l’écouteur. Il y a un léger délai de basculement lorsque vous utilisez l’équilibreur de charge, car la sonde d’intégrité effectue des vérifications actives toutes les 10 secondes par défaut. 

Pour commencer, découvrez comment configurer Azure Load Balancer pour une [instance de cluster de basculement](failover-cluster-instance-vnn-azure-load-balancer-configure.md) ou un [groupe de disponibilité](availability-group-vnn-azure-load-balancer-configure.md). 

**Systèmes d’exploitation pris en charge** : Tous   
**Version de SQL pris en charge** : Tous   
**Solution HADR prise en charge** : Instance de cluster de basculement et groupes de disponibilité   

La configuration du VNN peut être laborieuse, il s’agit d’une source de défaillance supplémentaire, cela peut entraîner un retard de détection des défaillances, et il existe une surcharge et un coût associés à la gestion de la ressource supplémentaire. Pour répondre à certaines de ces limitations, SQL Server a introduit la prise en charge de la fonctionnalité Nom du réseau distribué. 

## <a name="distributed-network-name-dnn"></a>Nom de réseau distribué (DNN)

Pour correspondre à l’expérience locale de connexion à votre écouteur de groupe de disponibilité ou instance de cluster de basculement, déployez vos machines virtuelles SQL Server sur plusieurs sous-réseaux au sein du même réseau virtuel. Le fait de disposer de plusieurs sous-réseaux nie la nécessité de la dépendance supplémentaire sur un DNN pour acheminer le trafic vers votre solution HADR. Pour plus d’informations, consultez [Groupe de disponibilité à plusieurs sous-réseaux](availability-group-manually-configure-prerequisites-tutorial-multi-subnet.md) et [Instance de cluster de basculement à plusieurs sous-réseaux](failover-cluster-instance-prepare-vm.md#subnets). 

Pour les machines virtuelles SQL Server déployées sur un sous-réseau unique, la fonctionnalité de nom du réseau distribué permet aux clients SQL Server de se connecter d’une autre façon à l’instance de cluster de basculement ou au groupe de disponibilité SQL Server sans utiliser d’équilibreur de charge. La fonctionnalité DNN est disponible à partir de [SQL Server 2016 SP3](https://support.microsoft.com/topic/kb5003279-sql-server-2016-service-pack-3-release-information-46ab9543-5cf9-464d-bd63-796279591c31), [SQL Server 2017 CU25](https://support.microsoft.com/topic/kb5003830-cumulative-update-25-for-sql-server-2017-357b80dc-43b5-447c-b544-7503eee189e9) et [SQL Server 2019 CU8](https://support.microsoft.com/topic/cumulative-update-8-for-sql-server-2019-ed7f79d9-a3f0-a5c2-0bef-d0b7961d2d72) sur Windows Server 2016 et versions ultérieures.

Quand une ressource DNN est créée, le cluster lie le nom DNS à l’adresse IP de tous les nœuds du cluster. Le client essaiera de se connecter à chaque adresse IP de cette liste pour trouver la ressource à laquelle se connecter. Vous pouvez accélérer ce processus en spécifiant `MultiSubnetFailover=True` dans la chaîne de connexion. Ce paramètre indique au fournisseur d’essayer toutes les adresses IP en parallèle, afin que le client puisse se connecter instantanément à la FCI ou à l’écouteur. 

Un nom de réseau distribué est recommandé sur un équilibreur de charge dans la mesure du possible pour les raisons suivantes : 
- La solution de bout en bout est plus robuste, car vous n’avez plus à gérer la ressource d’équilibreur de charge. 
- L’élimination des sondes de l’équilibreur de charge réduit la durée du basculement. 
- Le DNN simplifie l’approvisionnement et la gestion de l’instance de cluster de basculement ou de l’écouteur de groupe de disponibilité avec SQL Server sur des machines virtuelles Azure. 

La plupart des fonctionnalités SQL Server fonctionnent de manière transparente avec les FCI et les groupes de disponibilité lors de l’utilisation du DNN, mais certaines fonctionnalités peuvent nécessiter une attention particulière. 

**Systèmes d’exploitation pris en charge** : Windows Server 2016 et versions ultérieures   
**Version de SQL pris en charge** : SQL Server 2019 CU2 (FCI) et SQL Server 2019 CU8 (AG)   
**Solution HADR prise en charge** : Instance de cluster de basculement et groupes de disponibilité   

Pour commencer, apprenez à configurer une ressource de nom de réseau distribué pour une [instance de cluster de basculement](failover-cluster-instance-distributed-network-name-dnn-configure.md) ou un [groupe de disponibilité](availability-group-distributed-network-name-dnn-listener-configure.md).

Des considérations supplémentaires sont à prendre en compte lors de l’utilisation du nom du réseau distribué avec d’autres fonctionnalités de SQL Server. Pour en savoir plus, consultez [Interopérabilité FCI et DNN](failover-cluster-instance-dnn-interoperability.md) et [Interopérabilité AG et DNN](availability-group-dnn-interoperability.md). 

## <a name="recovery-actions"></a>Actions de récupération

Le service de cluster prend des mesures correctives lorsqu’une défaillance est détectée. Il peut s’agir du redémarrage de la ressource sur le nœud existant ou de son basculement sur un autre nœud. Une fois les mesures correctives lancées, elles peuvent prendre un certain temps. 

Par exemple, un groupe de disponibilité redémarré est mis en ligne selon la séquence suivante : 

1. L’adresse IP de l’écouteur bascule en ligne
1. Le nom réseau de l’écouteur bascule en ligne
1. Le groupe de disponibilité bascule en ligne
1. Les bases de données individuelles subissent la récupération, ce qui peut prendre un certain temps qui dépendra de différents facteurs tels que la longueur du journal de restauration par progression. Les connexions sont routées par l’écouteur seulement une fois que la base de données est entièrement récupérée. Pour plus d’informations, consultez [Estimation du temps de basculement (RTO)](/sql/database-engine/availability-groups/windows/monitor-performance-for-always-on-availability-groups). 

Étant donné que la récupération peut prendre un certain temps, la supervision agressive définie pour détecter une défaillance en 20 secondes peut entraîner une interruption de plusieurs minutes si un événement temporaire se produit (par exemple une [maintenance de machine virtuelle Azure](#azure-platform-maintenance) visant à préserver la mémoire). Le paramétrage de la supervision sur une valeur plus souple de 40 secondes peut aider à éviter une interruption de service plus longue. 

Pour en savoir plus sur le réglage des paramètres de seuil, consultez les [bonnes pratiques en matière de clusters](hadr-cluster-best-practices.md). 


## <a name="node-location"></a>Emplacement du nœud

Les nœuds d’un cluster Windows sur des machines virtuelles dans Azure peuvent être séparés physiquement dans la même région Azure ou dans des régions différentes. La distance peut introduire une latence du réseau, un peu comme si les nœuds du cluster étaient répartis entre différents emplacements de vos propres installations. Dans les environnements cloud, la différence réside dans le fait qu’au sein d’une région vous n’avez peut-être pas conscience de la distance entre les nœuds.  En outre, d’autres facteurs tels que les composants physiques et virtuels, le nombre de tronçons, et ainsi de suite, peuvent également contribuer à une latence accrue. Si la latence entre les nœuds est un souci, placez les nœuds du cluster dans un [groupe de placement de proximité](../../../virtual-machines/co-location.md) afin de garantir la proximité du réseau.

## <a name="resource-limits"></a>Limites des ressources

Quand vous configurez une machine virtuelle Azure, vous déterminez les limites de ressources informatiques pour le processeur, la mémoire et les E/S. Les charges de travail qui requièrent des ressources allant au-delà des limites de disques ou des machines virtuelles Azure achetées peuvent entraîner des problèmes de performances de machine virtuelle. La dégradation des performances peut entraîner l’échec d’un contrôle d’intégrité pour le service de cluster ou pour la fonctionnalité de haute disponibilité SQL Server. Les goulots d’étranglement de ressources peuvent faire apparaître le nœud ou la ressource comme étant hors ligne pour le cluster ou SQL Server. 

Les opérations d’E/S SQL intensives, ou les opérations de maintenance telles que les sauvegardes ou la maintenance de statistiques ou d’index, peuvent amener la machine virtuelle ou le disque à atteindre des limites de débit d’*IOPS* ou de *Mbits/s*, ce qui pourrait rendre SQL Server non réactif à un contrôle *IsAlive/LooksAlive*. 

Si votre ordinateur SQL Server rencontre des basculements inattendus, vérifiez que vous respectez toutes les [bonnes pratiques en matière de performances](performance-guidelines-best-practices-checklist.md) et supervisez le serveur afin de détecter les limitations au niveau du disque ou de la machine virtuelle. 

## <a name="azure-platform-maintenance"></a>Maintenance de la plateforme Azure

Comme tout autre service cloud, Azure met régulièrement à jour sa plateforme pour améliorer la fiabilité, le niveau de performance et la sécurité de l’infrastructure hôte des machines virtuelles. L’objectif de ces mises à jour comprend l’application d’une mise à jour corrective aux composants logiciels de l’environnement d’hébergement, la mise à niveau des composants réseau et la désactivation du matériel.

La plupart des mises à jour de la plateforme n’affectent pas les machines virtuelles clientes. Lorsqu’une mise à jour sans impact n’est pas possible, Azure choisit alors le mécanisme ayant le moins d’impact pour les machines virtuelles clientes. La plupart des maintenances ayant un impact mettent la machine virtuelle en pause pendant moins de 10 secondes. Dans certains cas, Azure utilise des mécanismes de maintenance avec préservation de la mémoire. Ces mécanismes interrompent la machine virtuelle pendant 30 secondes au maximum et conservent la mémoire dans la RAM. Une fois la machine virtuelle redémarrée, son horloge est synchronisée automatiquement.

La maintenance avec préservation de la mémoire fonctionne avec plus de 90 % des machines virtuelles Azure. Elle ne fonctionne pas pour les modèles de série G, M, N et H. Azure utilise de plus en plus les technologies de migration dynamique et améliore les mécanismes de maintenance avec préservation de la mémoire pour réduire la durée de la mise en pause. Lorsque la machine virtuelle fait l’objet d’une migration dynamique vers un autre hôte, certaines charges de travail sensibles telles que SQL Server peuvent subir une légère détérioration des performances au cours des quelques minutes qui précèdent la mise en pause de la machine virtuelle.

Un goulot d’étranglement des ressources lors de la maintenance de la plateforme peut faire apparaître le groupe de disponibilité ou l’instance de cluster de basculement comme étant hors ligne pour le service de cluster. Pour plus d’informations, consultez la section [Limites de ressources](#resource-limits) de cet article. 

Si vous utilisez une supervision de cluster agressive, une pause de machine virtuelle étendue peut déclencher un basculement. Un basculement entraîne souvent un temps d’arrêt plus long que la pause de maintenance. Il est donc recommandé d’adopter une supervision souple afin d’éviter de déclencher un basculement pendant que la machine virtuelle est en pause pour maintenance. Pour plus d’informations sur la définition des seuils de cluster dans les machines virtuelles Azure, consultez [Bonnes pratiques en matière de clusters](hadr-cluster-best-practices.md).

## <a name="limitations"></a>Limites

Tenez compte des limitations suivantes lorsque vous utilisez l’instance FCI ou des groupes de disponibilité et SQL Server sur des machines virtuelles Azure. 

### <a name="msdtc"></a>MSDTC 

La solution Machines virtuelles Azure prend en charge Microsoft Distributed Transaction Coordinator (MSDTC) sur Windows Server 2019, avec un stockage sur les volumes partagés en cluster (CSV) et [Azure Standard Load Balancer](../../../load-balancer/load-balancer-overview.md) ou sur des machines virtuelles SQL Server qui utilisent des disques partagés Azure. 

Concernant Machines virtuelles Azure, MSDTC n’est pas pris en charge pour Windows Server 2016 ou versions antérieures avec des volumes partagés en cluster pour la raison suivante :

- La ressource MSDTC en cluster n’est pas configurable pour utiliser le stockage partagé. Sur Windows Server 2016, si vous créez une ressource MSDTC, celle-ci n’affiche pas le stockage partagé qui est disponible pour l’utilisation, et cela même si le stockage est disponible. Ce problème a été résolu dans Windows Server 2019.
- L’équilibreur de charge de base ne gère pas les ports RPC.



## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous vous êtes familiarisé avec les différences observables lors de l’utilisation d’un cluster de basculement Windows avec SQL Server sur des machines virtuelles Azure, découvrez les fonctionnalités de haute disponibilité offertes par les [groupes de disponibilité](availability-group-overview.md) ou les [instances de cluster de basculement](failover-cluster-instance-overview.md). Si vous êtes prêt à commencer, consultez les [bonnes pratiques](hadr-cluster-best-practices.md) pour connaître les recommandations an matière de configuration. 
