---
title: 'Liste de vérification : Meilleures pratiques et recommandations'
description: Fournit une check-list rapide qui vous permet de passer en revue les bonnes pratiques et recommandations pour optimiser les performances de votre serveur SQL Server dans une instance de SQL Server sur les machines virtuelles Azure.
services: virtual-machines-windows
documentationcenter: na
author: bluefooted
editor: ''
tags: azure-service-management
ms.service: virtual-machines-sql
ms.subservice: performance
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/01/2021
ms.author: pamela
ms.custom: contperf-fy21q3
ms.reviewer: mathoma
ms.openlocfilehash: 54830fd4052e6121551d6246d6b325e6036b4800
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132551384"
---
# <a name="checklist-best-practices-for-sql-server-on-azure-vms"></a>Liste de vérification : Meilleures pratiques relatives à SQL Server sur les machines virtuelles Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Cet article fournit une check-list rapide sous la forme d’une série de bonnes pratiques et de recommandations pour optimiser les performances de votre serveur SQL Server dans une instance de SQL Server sur les machines virtuelles Azure. 

Pour des détails complets, consultez les autres articles de cette série : [Liste de vérification](performance-guidelines-best-practices-checklist.md), [Taille de machine virtuelle](performance-guidelines-best-practices-vm-size.md), [Stockage](performance-guidelines-best-practices-storage.md), [Sécurité](security-considerations-best-practices.md), [Configuration HADR](hadr-cluster-best-practices.md) et [Collecter une ligne de base](performance-guidelines-best-practices-collect-baseline.md). 

Activez la fonctionnalité [SQL Assessment pour SQL Server sur des machines virtuelles Azure](sql-assessment-for-sql-vm.md). Votre serveur SQL sera évalué par rapport aux meilleures pratiques connues et les résultats seront affichés sur la [page de gestion des machines virtuelles SQL](manage-sql-vm-portal.md) du portail Azure.


## <a name="overview"></a>Vue d’ensemble

Quand vous exécutez SQL Server sur les machines virtuelles Azure, utilisez les options de réglage des performances de base de données qui s’appliquent à SQL Server dans les environnements serveur locaux. Toutefois, les performances d’une base de données relationnelle dans un cloud public dépendent de nombreux facteurs, par exemple la taille d’une machine virtuelle et la configuration des disques de données.

Il existe généralement un compromis entre l’optimisation des coûts et l’optimisation des performances. Cette série de bonnes pratiques sur les performances vise à obtenir les *meilleures* performances possibles pour SQL Server sur les machines virtuelles Azure. Si votre charge de travail est moindre, vous n’aurez peut-être pas besoin de toutes les optimisations recommandées. Tenez compte de vos besoins de performances, des coûts et des modèles de charges de travail lors de l’évaluation de ces recommandations.

## <a name="vm-size"></a>Taille de la machine virtuelle

Voici une check-list rapide des bonnes pratiques relatives à la taille d’une machine virtuelle pour l’exécution d’un serveur SQL Server dans une instance de SQL Server sur les machines virtuelles Azure : 

- Utilisez des tailles de machine virtuelle offrant au moins 4 processeurs virtuels, par exemple les séries [Standard_M8-4ms](../../../virtual-machines/m-series.md), [E4ds_v4](../../../virtual-machines/edv4-edsv4-series.md#edv4-series) et [DS12_v2](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15), ou versions ultérieures. 
- Utilisez des tailles de machine virtuelle [à mémoire optimisée](../../../virtual-machines/sizes-memory.md) pour optimiser les performances des charges de travail SQL Server. 
- Les séries [DSv2 11 à 15](../../../virtual-machines/dv2-dsv2-series-memory.md), [Edsv4](../../../virtual-machines/edv4-edsv4-series.md), [M-](../../../virtual-machines/m-series.md) et [Mv2-](../../../virtual-machines/mv2-series.md) offrent le ratio optimal mémoire/vCore requis pour les charges de travail OLTP. Les machines virtuelles des deux séries M offrent le plus haut ratio mémoire/vCore requis pour les charges de travail critiques. Elles sont également idéales pour les charges de travail d’entrepôt de données. 
- Choisissez éventuellement un ratio mémoire/vCore plus élevé pour les charges de travail critiques et celles qui sont relatives aux entrepôt de données. 
- Utilisez les images de machine virtuelle de la Place de marché Azure, car les paramètres et les options de stockage de SQL Server sont configurés pour optimiser les performances de SQL Server. 
- Collectez les caractéristiques de performances de la charge de travail cible, et utilisez-les pour déterminer la taille de machine virtuelle appropriée pour votre entreprise.
- Utilisez l’outil de [recommandation SKU](https://www.microsoft.com/download/details.aspx?id=53595) [Assistant Migration de données](/sql/dma/dma-sku-recommend-sql-db) pour trouver la taille de machine virtuelle adaptée à votre charge de travail SQL Server existante.

Pour en savoir plus, consultez les [bonnes pratiques relatives à la taille d’une machine virtuelle](performance-guidelines-best-practices-vm-size.md). 

## <a name="storage"></a>Stockage

Voici une check-list rapide des bonnes pratiques relatives à la configuration du stockage pour l’exécution d’un serveur SQL Server dans une instance de SQL Server sur les machines virtuelles Azure : 

- Surveillez l’application et [déterminez les besoins en bande passante et de latence de stockage](../../../virtual-machines/premium-storage-performance.md#counters-to-measure-application-performance-requirements) pour les fichiers de données, de journal et tempdb SQL Server avant de choisir le type de disque. 
- Pour optimiser les performances du stockage, prévoyez les IOPS non mises en cache les plus élevées disponibles, et utilisez la mise en cache des données en tant que fonctionnalité de performance pour les lectures de données, tout en évitant les [plafonnements/limitations applicables à la machine virtuelle et aux disques](../../../virtual-machines/premium-storage-performance.md#throttling).
- Placez les fichiers de données, de journal et tempdb sur des lecteurs distincts.
    - Pour le lecteur de données, utiliser uniquement les [disques Premium P30 et P40](../../../virtual-machines/disks-types.md#premium-ssds) pour garantir la disponibilité de la prise en charge du cache
    - Pour le lecteur de journaux, prévoyez une capacité suffisante et testez les performances par rapport aux coûts quand vous évaluez les [disques premium P30 - P80](../../../virtual-machines/disks-types.md#premium-ssds).
      - Si la latence de stockage en millisecondes est requise, utilisez des [Disques Ultra Azure](../../../virtual-machines/disks-types.md#ultra-disks) pour le journal des transactions. 
      - Pour les déploiements de machines virtuelles de la série M, utilisez l’[accélérateur d’écriture](../../../virtual-machines/how-to-enable-write-accelerator.md) à la place des disques Ultra Azure.
    - Placez [tempdb](/sql/relational-databases/databases/tempdb-database) sur le lecteur SSD éphémère local (par défaut : `D:\`) pour la plupart des charges de travail SQL Server, après avoir choisi la taille de machine virtuelle optimale. 
      - Si la capacité du lecteur local n’est pas suffisante pour tempdb, pensez à redimensionner la machine virtuelle. Consultez les [Stratégies de mise en cache de fichiers de données](performance-guidelines-best-practices-storage.md#data-file-caching-policies) pour plus d’informations.
- Entrelacez plusieurs disques de données Azure à l’aide d'[espaces de stockage](/windows-server/storage/storage-spaces/overview) pour augmenter la bande passante d’E/S jusqu’aux limites de débit et d’IOPS de la machine virtuelle cible.
- Définissez la [mise en cache de l’hôte](../../../virtual-machines/disks-performance.md#virtual-machine-uncached-vs-cached-limits) en lecture seule pour les disques de fichiers de données.
- Définissez la [mise en cache de l’hôte](../../../virtual-machines/disks-performance.md#virtual-machine-uncached-vs-cached-limits) sur aucun pour les disques de fichiers journaux.
    - N’activez pas la mise en cache en lecture/écriture sur les disques qui contiennent des fichiers SQL Server. 
    - Arrêtez toujours le service SQL Server avant de modifier les paramètres de cache de votre disque.
- Pour les charges de travail de développement et test, utilisez le service Stockage Standard. Il n’est pas recommandé d’utiliser HDD/SDD Standard pour les charges de travail de production.
- Le [bursting de disque basé sur les crédits](../../../virtual-machines/disk-bursting.md#credit-based-bursting) (P1-P20) ne doit être pris en compte que pour les charges de travail de dev/test et les systèmes départementaux plus petits.
- Configurez le compte de stockage dans la même région que la machine virtuelle SQL Azure. 
- Désactivez le stockage géoredondant Azure (géoréplication) et utilisez LRS (stockage local redondant) sur le compte de stockage.
- Formatez votre disque de données afin d’utiliser une taille d’unité d’allocation de 64 ko pour tous les fichiers de données placés sur un lecteur autre que le lecteur `D:\` temporaire (dont la valeur par défaut est de 4 ko). Les machines virtuelles SQL Server déployées via la Place de marché Azure sont fournies avec des disques de données formatés avec une taille d’unité d’allocation et un entrelacement pour le pool de stockage défini sur 64 Ko. 


Pour en savoir plus, consultez les [bonnes pratiques relatives au stockage](performance-guidelines-best-practices-storage.md). 

## <a name="sql-server-features"></a>fonctionnalités SQL Server

Voici une liste de vérification rapide des meilleures pratiques pour les paramètres de configuration de SQL Server lorsque vous exécutez vos instances SQL dans une machine virtuelle Azure en production : 

- Activer la [compression des pages de la base de données](/sql/relational-databases/data-compression/data-compression), le cas échéant.
- Activer la [compression des sauvegardes](/sql/relational-databases/backup-restore/backup-compression-sql-server).
- Activer l’[initialisation instantanée de fichiers](/sql/relational-databases/databases/database-instant-file-initialization) pour les fichiers de données.
- Limiter la [croissance automatique](/troubleshoot/sql/admin/considerations-autogrow-autoshrink#considerations-for-autogrow) de la base de données.
- Désactiver la [réduction automatique](/troubleshoot/sql/admin/considerations-autogrow-autoshrink#considerations-for-auto_shrink) de la base de données.
- Désactiver la fermeture automatique de la base de données.
- Déplacer toutes les bases de données vers des disques de données, y compris les [bases de données système](/sql/relational-databases/databases/move-system-databases).
- Déplacer les répertoires des journaux d’erreurs et des fichiers de trace SQL Server vers des disques de données.
- Configurer les emplacements par défaut des fichiers de sauvegarde et de base de données.
- Définir la [limite de mémoire SQL Server](/sql/database-engine/configure-windows/server-memory-server-configuration-options#use-) maximale afin de laisser suffisamment de mémoire pour le système d’exploitation. ([Utilisez Mémoire\Octets disponibles](/sql/relational-databases/performance-monitor/monitor-memory-usage) pour surveiller l’intégrité de la mémoire du système d’exploitation.)
- Activer le [verrouillage des pages en mémoire](/sql/database-engine/configure-windows/enable-the-lock-pages-in-memory-option-windows).
- Activer l’[optimisation des charges de travail ad hoc](/sql/database-engine/configure-windows/optimize-for-ad-hoc-workloads-server-configuration-option) pour les environnements OLTP lourds.
- Évaluer et appliquer les [dernières mises à jour cumulatives](/sql/database-engine/install-windows/latest-updates-for-microsoft-sql-server) pour les versions installées de SQL Server.
- Activer [Magasin des requêtes](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) sur toutes les bases de données SQL Server de production [suivant les meilleures pratiques](/sql/relational-databases/performance/best-practice-with-the-query-store).
- Activer le [réglage automatique](/sql/relational-databases/automatic-tuning/automatic-tuning) sur les bases de données d’application critiques.
- S’assurer que toutes les [meilleures pratiques tempdb](/sql/relational-databases/databases/tempdb-database#optimizing-tempdb-performance-in-sql-server) sont respectées.
- Placer tempdb sur le lecteur éphémère D:/.
- [Utiliser le nombre de fichiers recommandé](/troubleshoot/sql/performance/recommendations-reduce-allocation-contention#resolution), à l’aide de plusieurs fichiers de données tempdb en commençant par un fichier par cœur et jusqu’à huit fichiers.
- Planifier des tâches SQL Server Agent pour exécuter les tâches [DBCC CHECKDB](/sql/t-sql/database-console-commands/dbcc-checkdb-transact-sql#a-checking-both-the-current-and-another-database), [index reorganize](/sql/relational-databases/indexes/reorganize-and-rebuild-indexes#reorganize-an-index), [index rebuild](/sql/relational-databases/indexes/reorganize-and-rebuild-indexes#rebuild-an-index) et [update statistics](/sql/t-sql/statements/update-statistics-transact-sql#examples).
- Surveiller et gérer l’intégrité et la taille du [fichier journal de transactions](/sql/relational-databases/logs/manage-the-size-of-the-transaction-log-file#Recommendations) de SQL Server.
- Tirer parti de toutes les nouvelles [fonctionnalités de SQL Server](/sql/sql-server/what-s-new-in-sql-server-ver15) disponibles pour la version utilisée.
- Connaître les différences de [fonctionnalités prises en charge](/sql/sql-server/editions-and-components-of-sql-server-version-15) entre les éditions que vous envisagez de déployer.

## <a name="azure-features"></a>Fonctionnalités Azure

Voici une liste de vérification rapide des meilleures pratiques pour obtenir une aide spécifique à Azure lorsque vous exécutez votre instance SQL Server sur machines virtuelles Azure :

- S’inscrire auprès de l’[extension d’agent SQL IaaS](sql-agent-extension-manually-register-single-vm.md) pour déverrouiller un certain nombre d’[avantages liés aux fonctionnalités](sql-server-iaas-agent-extension-automate-management.md#feature-benefits).
- Tirer parti de la meilleure [stratégie de sauvegarde et de restauration](backup-restore.md#decision-matrix) pour votre charge de travail SQL Server.
- S’assurer que les [performances réseau accélérées sont activées](../../../virtual-network/create-vm-accelerated-networking-cli.md#portal-creation) sur la machine virtuelle.
- Tirer parti de [Microsoft Defender pour le cloud](../../../security-center/index.yml) pour améliorer la posture de sécurité globale de votre déploiement de machines virtuelles.
- Tirer parti de [Microsoft Defender pour le cloud](../../../security-center/azure-defender.md), intégré à [Microsoft Defender pour le cloud](https://azure.microsoft.com/services/security-center/), pour une [couverture spécifique des machines virtuelles SQL Server](../../../security-center/defender-for-sql-introduction.md), notamment des évaluations des vulnérabilités et un accès juste-à-temps, ce qui réduit la surface d’attaque tout en permettant aux utilisateurs légitimes d’accéder aux machines virtuelles, le cas échéant. Pour plus d’informations, consultez [Évaluation des vulnérabilités](../../../security-center/defender-for-sql-on-machines-vulnerability-assessment.md), [Activer l’évaluation des vulnérabilités pour les machines virtuelles SQL Server](../../../security-center/defender-for-sql-on-machines-vulnerability-assessment.md) et [Accès juste-à-temps](../../../security-center/just-in-time-explained.md). 
- Tirer parti d’[Azure Advisor](../../../advisor/advisor-overview.md) pour répondre aux recommandations en matière de [performances](../../../advisor/advisor-performance-recommendations.md), de [coûts](../../../advisor/advisor-cost-recommendations.md), de [fiabilité](../../../advisor/advisor-high-availability-recommendations.md), d’[excellence opérationnelle](../../../advisor/advisor-operational-excellence-recommendations.md) et de[ sécurité](../../../advisor/advisor-security-recommendations.md).
- Tirer parti d’[Azure Monitor](../../../azure-monitor/vm/monitor-virtual-machine.md) pour collecter les données de télémétrie de votre environnement SQL Server, les analyser et agir en conséquence. Cela comprend l’identification des problèmes d’infrastructure avec [VM insights](../../../azure-monitor/vm/vminsights-overview.md) et la surveillance des données avec [Log Analytics](../../../azure-monitor/logs/log-query-overview.md) pour des diagnostics plus approfondis.
- Activer l’[arrêt automatique](../../../automation/automation-solution-vm-management.md) pour les environnements de développement et de test. 
- Implémenter une solution de haute disponibilité et récupération d’urgence (HADR) qui répond à vos SLA en matière de continuité des activités. Consultez les [options HADR](business-continuity-high-availability-disaster-recovery-hadr-overview.md#deployment-architectures) disponibles pour SQL Server sur machines virtuelles Azure. 
- Utiliser le portail Azure (support + dépannage) pour évaluer l’historique et l’[intégrité des ressources](../../../service-health/resource-health-overview.md) ; envoyer de nouvelles demandes de support le cas échéant.

## <a name="hadr-configuration"></a>Configuration HADR

Les fonctionnalités de haute disponibilité et récupération d’urgence (HADR), telles que le [groupe de disponibilité Always On](availability-group-overview.md) et l’[instance de cluster de basculement](failover-cluster-instance-overview.md), reposent sur la technologie de [cluster de basculement Windows Server](hadr-windows-server-failover-cluster-overview.md) sous-jacente. Examinez les meilleures pratiques pour modifier vos paramètres HADR afin de mieux prendre en charge l’environnement cloud. 

Pour votre cluster Windows, prenez en compte les meilleures pratiques suivantes : 

* Déployez vos machines virtuelles SQL Server sur plusieurs sous-réseaux lorsque cela est possible afin d’éviter de dépendre d’un équilibreur de charge Azure ou d’un nom de réseau distribué (DNN) pour acheminer le trafic vers votre solution HADR. 
* Définissez des paramètres moins agressifs pour le cluster afin d’éviter les pannes inattendues dues à des défaillances momentanées du réseau ou à la maintenance de la plateforme Azure. Pour plus d’informations, consultez les [paramètres de pulsation et de seuil](hadr-cluster-best-practices.md#heartbeat-and-threshold). Pour Windows Server 2012 ou version ultérieure, utilisez les valeurs recommandées suivantes : 
   - **SameSubnetDelay** : 1 seconde
   - **SameSubnetThreshold** : 40 pulsations
   - **CrossSubnetDelay** : 1 seconde
   - **CrossSubnetThreshold** : 40 pulsations.
* Placez vos machines virtuelles dans un groupe à haute disponibilité ou dans différentes zones de disponibilité.  Pour plus d’informations, consultez les [paramètres de disponibilité des machines virtuelles](hadr-cluster-best-practices.md#vm-availability-settings). 
* Utilisez une seule carte réseau par nœud de cluster et un seul sous-réseau. 
* Configurez le [vote de quorum](hadr-cluster-best-practices.md#quorum-voting) du cluster pour utiliser trois votes ou plus (toujours par nombre impair). N’attribuez pas de votes à des régions de récupération d’urgence. 
* Surveillez attentivement les [limites de ressources](hadr-cluster-best-practices.md#resource-limits) pour éviter les redémarrages ou les basculements inattendus dus à des contraintes de ressources.
   - Assurez-vous que le système d’exploitation, les pilotes et SQL Server disposent de la version la plus récente. 
   - Optimisez les performances de SQL Server sur les machines virtuelles Azure. Consultez les autres sections de cet article pour en savoir plus. 
   - Réduisez ou répartissez la charge de travail pour éviter d’atteindre les limites de ressources. 
   - Passez à une machine virtuelle ou à un disque dont les limites sont plus élevées pour éviter les contraintes. 

Pour votre groupe de disponibilité SQL Server ou votre instance de cluster de basculement, tenez compte des meilleures pratiques suivantes : 

* Si vous rencontrez fréquemment des échecs inattendus, suivez les meilleures pratiques en matière de performances décrites dans la suite de cet article. 
* Si l’optimisation des performances de la machine virtuelle SQL Server ne résout pas vos pannes inattendues, envisagez de [relâcher la surveillance](hadr-cluster-best-practices.md#relaxed-monitoring) du groupe de disponibilité ou de l’instance de cluster de basculement. Toutefois, cela ne résout pas la source sous-jacente du problème et peut masquer les symptômes en réduisant la probabilité d’échec. Vous devrez peut-être encore examiner et résoudre la cause racine sous-jacente. Pour Windows Server 2012 ou version ultérieure, utilisez les valeurs recommandées suivantes : 
   - **Délai d’expiration du bail** : Utilisez cette équation pour calculer la valeur maximale du délai d’expiration du bail :   
    `Lease timeout < (2 * SameSubnetThreshold * SameSubnetDelay)`.    
    Commencez par 40 secondes. Si vous utilisez les valeurs assouplies de `SameSubnetThreshold` et `SameSubnetDelay` qui ont été recommandées précédemment, ne dépassez pas 80 secondes pour la valeur du délai d’expiration du bail. 
   - **Nombre maximal d’échecs au cours d’une période spécifiée** : vous pouvez définir cette valeur sur 6.
   - **Délai d’attente de vérification** : vous pouvez définir initialement cette valeur sur 60000, et l’ajuster si nécessaire. 
* Lorsque vous utilisez le nom de réseau virtuel (VNN) et Azure Load Balancer pour vous connecter à votre solution HADR, spécifiez `MultiSubnetFailover = true` dans la chaîne de connexion, même si votre cluster s’étend sur un seul sous-réseau. 
   - Si le client ne prend pas en charge `MultiSubnetFailover = True`, vous devrez peut-être définir `RegisterAllProvidersIP = 0` et `HostRecordTTL = 300` pour mettre en cache les informations d’identification du client pour des durées plus courtes. Toutefois, cela peut entraîner des requêtes supplémentaires sur le serveur DNS. 
- Pour vous connecter à votre solution HADR à l’aide du nom de réseau distribué (DNN), tenez compte des points suivants :
   - Vous devez utiliser un pilote client qui prend en charge `MultiSubnetFailover = True`, et ce paramètre doit figurer dans la chaîne de connexion. 
   - Utilisez un port DNN unique dans la chaîne de connexion lorsque vous vous connectez à l’écouteur DNN pour un groupe de disponibilité. 
- Utilisez une chaîne de connexion de mise en miroir de bases de données pour un groupe de disponibilité de base afin de contourner le besoin d’un équilibreur de charge ou d’un DNN. 
- Validez la taille de secteur de vos disques durs virtuels avant de déployer votre solution de haute disponibilité pour éviter les E/S mal alignées. Pour plus d’informations, voir [KB3009974](https://support.microsoft.com/topic/kb3009974-fix-slow-synchronization-when-disks-have-different-sector-sizes-for-primary-and-secondary-replica-log-files-in-sql-server-ag-and-logshipping-environments-ed181bf3-ce80-b6d0-f268-34135711043c). 


Pour en savoir plus, consultez les [meilleures pratiques relatives à la haute disponibilité et récupération d’urgence](hadr-cluster-best-practices.md). 


## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus, consultez les autres articles de cette série :

- [Taille de la machine virtuelle](performance-guidelines-best-practices-vm-size.md)
- [Stockage](performance-guidelines-best-practices-storage.md)
- [Sécurité](security-considerations-best-practices.md)
- [Paramètres HADR](hadr-cluster-best-practices.md)
- [Collecter une ligne de base](performance-guidelines-best-practices-collect-baseline.md)

Pour connaître les meilleures pratiques en matière de sécurité, consultez [Considérations relatives à la sécurité de SQL Server sur les machines virtuelles Azure](security-considerations-best-practices.md).

Envisagez d’activer la fonctionnalité [SQL Assessment pour SQL Server sur des machines virtuelles Azure](sql-assessment-for-sql-vm.md).

Consultez d’autres articles relatifs aux machines virtuelles avec SQL Server à la page [Vue d’ensemble de SQL Server sur les machines virtuelles Azure](sql-server-on-azure-vm-iaas-what-is-overview.md). Si vous avez des questions sur les machines virtuelles SQL Server, consultez le [Forum aux Questions](frequently-asked-questions-faq.yml).
