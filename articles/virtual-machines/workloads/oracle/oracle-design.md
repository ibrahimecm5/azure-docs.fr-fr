---
title: Concevoir et implémenter une base de données Oracle sur Azure | Microsoft Docs
description: Concevez et implémentez une base de données Oracle dans votre environnement Azure.
author: dbakevlar
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 10/15/2021
ms.author: kegorman
ms.reviewer: tigorman
ms.openlocfilehash: 3a5b7d99c0995ae0e91056520945c0ed1a78d136
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130223041"
---
# <a name="design-and-implement-an-oracle-database-in-azure"></a>Concevoir et implémenter une base de données Oracle dans Azure

**S’applique à :** :heavy_check_mark: Machines virtuelles Linux 

## <a name="assumptions"></a>Hypothèses

- Vous prévoyez de migrer une base de données Oracle locale vers Azure.
- Vous avez le [Pack Diagnostics](https://docs.oracle.com/cd/E11857_01/license.111/e11987/database_management.htm) ou l’[Automatic Workload Repository (AWR)](https://www.oracle.com/technetwork/database/manageability/info/other-manageability/wp-self-managing-database18c-4412450.pdf) pour la base de données Oracle que vous cherchez à migrer
- Vous comprenez les différentes métriques contenues dans Oracle.
- Vous avez une connaissance élémentaire des performances d’application et de l’utilisation de la plateforme.

## <a name="goals"></a>Objectifs

- Comprendre comment optimiser votre déploiement Oracle sur Azure
- Explorer les options de réglage des performances d’une base de données Oracle dans un environnement Azure
- Définir des attentes claires précis entre les limites du réglage physique via une architecture et les avantages ou le réglage logique du code de la base de données, (SQL) et la conception globale de la base de données.

## <a name="the-differences-between-an-on-premises-and-azure-implementation"></a>Différences entre une implémentation locale et une implémentation Azure 

Voici quelques éléments importants à prendre en compte lorsque vous migrez des applications locales vers Azure. 

L’une des principales différences est que, dans une implémentation Azure, les ressources, telles que les machines virtuelles, les disques et les réseaux virtuels, sont partagées avec les autres clients. Les ressources peuvent aussi être limitées en fonction des critères définis. Au lieu de se concentrer sur ce qui permet d’éviter un échec (temps moyen entre les défaillances), Azure est plus axé sur la survie à un échec (temps moyen de récupération).

Le tableau suivant liste certaines des différences qui existent entre une implémentation locale et une implémentation Azure d’une base de données Oracle.


|  | Implémentation locale | Implémentation d’Azure |
| --- | --- | --- |
| **Mise en réseau** |LAN/WAN  |SDN (Software-Defined Networking)|
| **Groupe de sécurité** |Outils de restriction d’adresse IP/de port |[Groupe de sécurité réseau](https://azure.microsoft.com/blog/network-security-groups) |
| **Résilience** |MTBF (temps moyen entre les défaillances) |MTTR (temps moyen de récupération)|
| **Maintenance planifiée** |Correctifs/mises à niveau|[Groupes à haute disponibilité](/previous-versions/azure/virtual-machines/windows/infrastructure-example) (correctifs/mises à niveau gérés par Azure) |
| **Ressource** |Dédié  |Partagée avec d’autres clients|
| **Régions** |Centres de données |[Paires de régions](../../regions.md#region-pairs)|
| **Stockage** |SAN/disques physiques |[Stockage géré par Azure](https://azure.microsoft.com/pricing/details/managed-disks/?v=17.23h)|
| **Mettre à l'échelle** |Mise à l’échelle verticale |Mise à l’échelle horizontale|


### <a name="requirements"></a>Spécifications

- Déterminez l’utilisation réelle du processeur, car Oracle est concédé sous licence par cœur, et le dimensionnement des besoins en processeurs virtuels peut être une étape essentielle pour réaliser es économies. 
- Déterminez la taille, le stockage de sauvegarde et le taux de croissance de la base de données.
- Déterminez les E/S par seconde requises, que vous pouvez estimer en vous basant sur les rapports Oracle Statspack et AWR ou d’autres outils de surveillance du stockage au niveau du système d’exploitation.

## <a name="configuration-options"></a>Options de configuration

Quatre zones potentielles que vous pouvez paramétrer pour améliorer les performances dans un environnement Azure :

- Taille de la machine virtuelle
- Débit du réseau
- Types de disque et configurations
- Paramètres de cache des disques

### <a name="generate-an-awr-report"></a>Générer un rapport AWR

Si vous disposez d’une base de données Oracle Enterprise Edition que vous envisagez de migrer vers Azure, vous avez plusieurs options. Si vous avez le [Pack Diagnostics](https://www.oracle.com/technetwork/oem/pdf/511880.pdf) pour vos instances Oracle, vous pouvez exécuter le rapport AWR Oracle pour obtenir les métriques (E/S par seconde, Mbits/s, Gibits/s, etc.). Pour ces bases de données sans licence Pack Diagnostics ou pour une base de données Standard Edition, les mêmes métriques importantes peuvent être collectées avec un rapport Statspack après la collecte des instantanés manuels.  La principale différence entre ces deux méthodes de création de rapports réside dans le fait que les rapports AWR sont automatiquement collectés et fournissent plus d’informations sur la base de données que les anciens rapports Statspack.

Pour comparer, vous pouvez exécuter votre rapport AWR lors de charges de travail normales et maximales. Pour collecter la charge de travail la plus précise, prévoyez un rapport sur une fenêtre étendue d’une semaine au lieu de 24 heures, et tenez compte du fait qu’un rapport AWR fournit des moyennes dans le cadre de ses calculs.  Pour une migration de centre de données, nous vous recommandons de rassembler des rapports pour le dimensionnement des systèmes de production et d’estimer les copies de base de données restantes utilisées pour les tests utilisateur, les tests, le développement, etc., par pourcentages (UAT équivalent à la production, aux tests et au développement à 50 % du dimensionnement de production, etc.)

Par défaut, le référentiel AWR conserve 8 jours de données et prend des instantanés toutes les heures.  Pour exécuter un rapport AWR à partir de la ligne de commande, vous pouvez effectuer les opérations suivantes dans un terminal :

```bash
$ sqlplus / as sysdba
SQL> @$ORACLE_HOME/rdbms/admin/awrrpt.sql;
```

### <a name="key-metrics"></a>Mesures clés

Le rapport demandera les informations suivantes :
- Type de rapport : HTML ou texte, (HTML en version 12.1, avec plus d’informations qu’en format texte.)
- Nombre de jours d’instantanés à afficher (un instantané était créé chaque heure, un rapport d’une semaine comporte 168 ID d’instantanés différents)
- Valeur SnapshotID de début de la fenêtre de rapport.
- Valeur SnapshotID de fin de la fenêtre de rapport.
- Nom du rapport à créer par le script AWR.

Si vous exécutez le script AWR sur un cluster d’application réel (RAC), le rapport de ligne de commande est awrgrpt.sql au lieu de awrrpt.sql.  Le rapport « g » créera un rapport pour tous les nœuds de la base de données RAC dans un rapport unique au lieu de créer un rapport sur chaque nœud RAC.

Voici les métriques que vous pouvez obtenir avec le rapport AWR :

- Nom de la base de données, nom de l’instance et nom d’hôte
- Version de la base de données, (prise en charge par Oracle)
- Processeur/cœurs
- SGA/PGA, (et conseillers qui vous préviennent en cas de sous-dimensionnement)
- Quantité totale de mémoire en Go
- % occupation processeur
- Processeurs de base de données
- E/S par seconde (lecture/écriture)
- Mbits/s (lecture/écriture)
- Débit du réseau
- Taux de latence réseau (élevée/basse)
- Premiers événements d'attente 
- Paramètres de la base de données
- Base de données de type RAC, Exadata, avec des fonctionnalités ou des configurations avancées ?

### <a name="virtual-machine-size"></a>Taille de la machine virtuelle

#### <a name="1-estimate-vm-size-based-on-cpu-memory-and-io-usage-from-the-awr-report"></a>1. Estimer la taille de la machine virtuelle en fonction des données d’utilisation du processeur, de la mémoire ou des E/S du rapport AWR

Vous pouvez examiner les cinq principaux événements de premier plan expirés, qui indiquent l’emplacement des goulots d’étranglement.

Par exemple, dans le diagramme suivant, la synchronisation des fichiers journaux se trouve en haut. Cela indique le nombre d’attentes nécessaires pour que le LGWR écrive le tampon journal dans le fichier journal Redo. Ces résultats indiquent qu’un stockage ou des disques plus performants sont nécessaires. Le diagramme montre aussi le nombre de processeurs (cœurs) et la quantité de mémoire.

![Capture d’écran montrant la synchronisation des fichiers journaux en haut du tableau.](./media/oracle-design/cpu_memory_info.png)

Le diagramme suivant montre le nombre total d’E/S de lecture et d’écriture. 59 Go ont été lus et 247,3 Go ont été écrits au moment de la création du rapport.

![Capture d’écran montrant le nombre total d’E/S de lecture et d’écriture.](./media/oracle-design/io_info.png)

#### <a name="2-choose-a-vm"></a>2. Choisir une machine virtuelle

À partir des informations collectées dans le rapport AWR, l’étape suivante consiste à choisir une machine virtuelle de taille similaire qui répond à vos besoins. Vous trouverez la liste des machines virtuelles disponibles dans l’article [Mémoire optimisée](../../sizes-memory.md).

#### <a name="3-fine-tune-the-vm-sizing-with-a-similar-vm-series-based-on-the-acu"></a>3. Ajuster la taille de la machine virtuelle aux séries de machines virtuelles similaires en fonction de l’ACU

Une fois que vous avez choisi la machine virtuelle, faites attention à sa valeur ACU. Vous choisirez peut-être une autre machine virtuelle en fonction de la valeur ACU, qui répondra mieux à vos besoins. Pour plus d’informations, consultez [Unité de calcul Azure (ACU)](../../acu.md).

![Capture d’écran de la page Unités ACU](./media/oracle-design/acu_units.png)

### <a name="network-throughput"></a>Débit du réseau

Le diagramme suivant montre la relation entre le débit et le nombre d’E/S par seconde :

![Capture d’écran du débit](./media/oracle-design/throughput.png)

Le débit réseau total est estimé selon les informations suivantes  :
- Trafic SQL*Net
- Mbits/s x nombre de serveurs (flux sortant tel qu’Oracle Data Guard)
- Autres facteurs, comme la réplication de l’application

![Capture d’écran du débit SQL*Net](./media/oracle-design/sqlnet_info.png)

Selon vos besoins en bande passante réseau, vous pouvez choisir différents types de passerelles, notamment une passerelle de base, VpnGw ou Azure ExpressRoute. Pour plus d’informations, consultez [Tarification Passerelle VPN](https://azure.microsoft.com/pricing/details/vpn-gateway/?v=17.23h).

**Recommandations**

- La latence réseau est supérieure par rapport à un déploiement local. La diminution des allers-retours réseau peut considérablement améliorer les performances.
- Pour réduire le nombre d’allers-retours, regroupez les applications avec un nombre élevé de transactions et les applications « bavardes » sur une même machine virtuelle.
- Utilisez des machines virtuelles avec la [mise en réseau accélérée](../../../virtual-network/create-vm-accelerated-networking-cli.md) pour améliorer les performances réseau.
- Pour certaines distributions Linux, pensez à activer la [prise en charge de TRIM/UNMAP](/previous-versions/azure/virtual-machines/linux/configure-lvm#trimunmap-support).
- Installez [Oracle Enterprise Manager](https://www.oracle.com/technetwork/oem/enterprise-manager/overview/index.html) sur une machine virtuelle distincte.
- Les Huge Pages ne sont pas activées par défaut sur Linux. Envisagez d’activer les Huge Pages et définissez `use_large_pages = ONLY` sur Oracle DB. Cela peut aider à améliorer les performances. Pour plus d’informations, consultez [USE_LARGE_PAGES](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/refrn/USE_LARGE_PAGES.html#GUID-1B0F4D27-8222-439E-A01D-E50758C88390).

### <a name="disk-types-and-configurations"></a>Types de disque et configurations

- *Disques de système d'exploitation par défaut* : ces types de disques permettent la persistance et la mise en cache des données. Ils sont optimisés pour un accès au système d’exploitation au moment du démarrage, mais ils ne sont pas conçus pour les charges de travail transactionnelles ou d’entrepôt de données (analytiques).

- *Disques managés* : Azure gère les comptes de stockage que vous utilisez pour vos disques de machine virtuelle. Vous spécifiez le type de disque (le plus souvent SSD Premium pour les charges de travail Oracle) et la taille du disque requise. Azure crée et gère le disque pour vous.  Un disque SSD Premium géré est disponible uniquement pour les séries de machines virtuelles optimisées en mémoire et spécialement conçues. Une fois que vous avez choisi une taille de machine virtuelle, le menu affiche uniquement les références SKU de stockage Premium disponibles qui sont associées à cette taille de machine virtuelle.

![Capture d’écran de la page de disque managé](./media/oracle-design/premium_disk01.png)

Une fois que vous avez configuré votre stockage sur une machine virtuelle, vous pouvez tester la charge des disques avant de créer une base de données. Le fait de connaître la latence et le débit du taux d’E/S peut vous aider à déterminer si les machines virtuelles prennent en charge le débit attendu avec les cibles de latence.

Divers outils sont dédiés au test de charge d’application, comme Oracle Orion, Sysbench, SLOB et Fio.

Réexécutez le test de charge après avoir déployé une base de données Oracle. Démarrez vos charges de travail normales et maximales pour voir la base de référence de votre environnement.  Soyez réaliste lors du test de la charge de travail : il n’est pas judicieux d’exécuter une charge de travail très différente de celle que vous allez réellement exécuter sur la machine virtuelle.

Oracle étant une base de données qui consomment beaucoup d’E/S, il est important de dimensionner le stockage en fonction du taux d’E/S plutôt que de la taille de stockage. Par exemple, si le taux d’E/S par seconde nécessaire est de 5 000, alors que vous n’avez besoin que de 200 Go, vous pouvez quand même obtenir le disque Premium de classe P30, même s’il est fourni avec plus de 200 Go de stockage.

Le taux d’E/S par seconde se trouve dans le rapport AWR. Il est déterminé par le journal Redo, les lectures physiques et le taux d’écritures.  Vérifiez toujours que la série de machines virtuelles choisie peut également gérer la demande d’E/S de la charge de travail.  Si la machine virtuelle affiche une limite d’E/S inférieure à celle du stockage, la limite maximale est définie par la machine virtuelle.

![Capture d’écran de la page Rapport AWR](./media/oracle-design/awr_report.png)

Par exemple : la restauration par progression fait 12 200 000 octets par seconde, ce qui est égal à 11,63 Mbits/s.
Le nombre d’E/S par seconde est égal à 12 200 000/2 358 = 5 174.

Une fois que vous avez une vision claire de vos besoins en E/S, vous pouvez choisir une combinaison de disques mieux adaptée à ces besoins.

**Recommandations**

- Pour l’espace disque logique de données, répartissez la charge de travail d’E/S sur plusieurs disques à l’aide du stockage managé ou d’Oracle ASM.
- Utilisez la compression avancée Oracle pour réduire les E/S (données et index).
- Placez les journaux d’activité de restauration par progression ainsi que les espaces disques logiques Temp et Undo sur des disques de données distincts.
- Ne placez pas de fichiers d’application sur les disques du système d’exploitation par défaut (/dev/sda). Ces disques sont optimisés pour les démarrages rapides de machine virtuelle et risquent de ne pas fournir de performances optimales pour votre application.
- Lorsque vous utilisez des machines virtuelles de série M sur le stockage Premium, activez [l’Accélérateur d'écriture](../../how-to-enable-write-accelerator.md) sur le disque des journaux d’activité de rétablissement.
- Déplacez les journaux d’activité de rétablissement avec une latence élevée vers un disque UItra.

### <a name="disk-cache-settings"></a>Paramètres de cache des disques

Il existe trois options pour la mise en cache de l’hôte, mais pour une base de données Oracle, seule la mise en cache ReadOnly est recommandée pour une charge de travail de base de données.  Le mode ReadWrite peut introduire des vulnérabilités significatives dans un fichier de données, car l’objectif d’une écriture de base de données est d’enregistrer les informations dans le fichier de données, et non de les mettre en cache.

Contrairement à un système de fichiers ou une application, pour une base de données, la recommandation pour la mise en cache de l’hôte est *ReadOnly* : toutes les demandes sont mises en cache pour les lectures ultérieures. toutes les écritures continuent d’être écrites sur le disque.

**Recommandations**

Pour optimiser le débit, il est recommandé de commencer par **ReadOnly** (lecture seule) pour la mise en cache de l’hôte, lorsque cela est possible. Pour le stockage Premium, vous devez désactiver les barrières lorsque vous montez le système de fichiers conformément aux options **Lecture seule**. Mettez à jour le fichier `/etc/fstab` avec l’UUID pour les disques.

![Capture d’écran de la page disque managé qui affiche les options ReadOnly et None.](./media/oracle-design/premium_disk02.png)

- Pour les **disques du système d’exploitation**, utilisez **SSD Premium avec mise en cache de l’hôte en Lecture/Écriture**.
- Pour les **disques de données** qui contiennent des fichiers de données Oracle, des tempFiles, des controlfiles, des fichiers de suivi des modifications de bloc, des types de données BFILE, des fichiers pour les tables externes et des journaux flashback, utilisez un **SSD Premium avec une mise en cache de l’hôte en lecture seule**.
- Pour les **disques de données contenant des fichiers journaux de restauration par progression en ligne d’Oracle**, utilisez un **SSD Premium ou UltraDisk sans mise en cache de l’hôte (Aucun)** . Les fichiers journaux de restauration par progression archivés et les backupsets RMAN d’Oracle peuvent également se trouver avec les fichiers journaux de restauration par progression en ligne. Notez que la mise en cache de l’hôte est limitée à 4095 Gio : n’allouez pas de SSD Premium supérieur à P50 avec la mise en cache de l’hôte. Si vous avez besoin de plus de 4 Tio de stockage, entrelacez plusieurs disques SSD Premium en RAID-0 en utilisant Linux LVM2 ou Oracle ASM.

Si les charges de travail varient considérablement durant la journée et que la charge de travail d’E/S peut prendre en charge ces variations, un disque SSD Premium P1-P20 en mode rafale peut fournir les performances requises pendant les chargements par lots de nuit ou les demandes d’E/S limitées.  

## <a name="security"></a>Sécurité

Une fois votre environnement Azure configuré, l’étape suivante consiste à sécuriser votre réseau. Voici quelques recommandations :

- *Stratégie du groupe de sécurité réseau* : le groupe de sécurité réseau peut être défini par un sous-réseau ou une carte réseau. Il est plus simple de contrôler la sécurité de l’accès au niveau du sous-réseau et de forcer le routage pour les éléments tels que les pare-feu d’application.

- *Serveur de rebond (jumpbox)*  : pour un accès plus sécurisé, les administrateurs ne doivent pas se connecter directement au service d'application ou à la base de données. Une jumpbox sert de média entre la machine de l’administrateur et les ressources Azure.
![Capture d’écran de la page relative à la topologie Jumpbox](./media/oracle-design/jumpbox.png)

    L’accès à la machine de l’administrateur doit être limité à l’adresse IP de la jumpbox. La jumpbox doit avoir accès à l’application et à la base de données.

- *Réseau privé* (sous-réseaux) : il est recommandé de placer le service d'application et la base de données sur des sous-réseaux distincts afin de mieux contrôler la stratégie de groupe de sécurité réseau.


## <a name="additional-reading"></a>Documentation supplémentaire

- [Configurer Oracle ASM](configure-oracle-asm.md)
- [Implémenter Oracle Data Guard sur une machine virtuelle Linux Azure](configure-oracle-dataguard.md)
- [Implémenter Oracle Golden Gate sur une machine virtuelle Linux Azure](configure-oracle-golden-gate.md)
- [Sauvegarde et récupération Oracle](./oracle-overview.md)

## <a name="next-steps"></a>Étapes suivantes

- [Tutoriel : Créer des machines virtuelles hautement disponibles](../../linux/create-cli-complete.md)
- [Explorer des exemples Azure CLI de déploiement de machines virtuelles](https://github.com/Azure-Samples/azure-cli-samples/tree/master/virtual-machine)
