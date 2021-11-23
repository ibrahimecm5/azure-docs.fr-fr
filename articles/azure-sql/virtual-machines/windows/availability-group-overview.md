---
title: Vue d’ensemble des groupes de disponibilité Always On SQL Server
description: Cet article présente les groupes de disponibilité Always On de SQL Server sur les machines virtuelles Azure.
services: virtual-machines
documentationCenter: na
author: rajeshsetlem
editor: monicar
tags: azure-service-management
ms.assetid: 601eebb1-fc2c-4f5b-9c05-0e6ffd0e5334
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: overview
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/10/2021
ms.author: rsetlem
ms.custom: seo-lt-2019
ms.reviewer: mathoma
ms.openlocfilehash: 21aef2227768d49da9a5eab5f4e772441c9f15b0
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132158671"
---
# <a name="always-on-availability-group-on-sql-server-on-azure-vms"></a>Groupes de disponibilité Always On sur SQL Server sur les machines virtuelles Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Cet article présente les groupes de disponibilité Always On pour SQL Server sur les machines virtuelles Azure. 

## <a name="overview"></a>Vue d’ensemble

Les groupes de disponibilité Always On sur les machines virtuelles Azure sont similaires à des [groupes de disponibilité Always On locaux](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) et s’appuient sur le [cluster de basculement Windows Server](hadr-windows-server-failover-cluster-overview.md) sous-jacent. Toutefois, étant donné que les machines virtuelles sont hébergées dans Azure, il existe également quelques points supplémentaires à prendre en considération, comme la redondance des machines virtuelles et le routage du trafic sur le réseau Azure. 

Le diagramme suivant illustre un groupe de disponibilité pour SQL Server sur les machines virtuelles Azure :

![Groupe de disponibilité](./media/availability-group-overview/00-EndstateSampleNoELB.png)

> [!NOTE]
> Il est désormais possible d’effectuer un lift-and-shift de votre solution de groupe de disponibilité vers SQL Server sur des machines virtuelles Azure à l’aide d’Azure Migrate. Pour plus d’informations, consultez [Migrer un groupe de disponibilité](../../migration-guides/virtual-machines/sql-server-availability-group-to-sql-on-azure-vm.md). 

## <a name="vm-redundancy"></a>Redondance des machines virtuelles 

Pour accroître la redondance et la haute disponibilité, les machines virtuelles SQL Server doivent se trouver dans le même [groupe à haute disponibilité](../../../virtual-machines/availability-set-overview.md) ou dans des [zones de disponibilité](../../../availability-zones/az-overview.md) différentes.

Le fait de placer un ensemble de machines virtuelles dans un même groupe à haute disponibilité protège contre les pannes qui se produisent au sein d’un centre de données en raison d’un équipement défaillant (les machines virtuelles au sein d’un groupe à haute disponibilité ne partagent pas les ressources) ou de mises à jour (les machines virtuelles au sein d’un groupe à haute disponibilité ne sont pas mises à jour en même temps). 

Les zones de disponibilité protègent contre la défaillance d’un centre de données dans son intégralité, chaque zone représentant un ensemble de centres de données au sein d’une région.  En faisant en sorte de placer les ressources dans différentes zones de disponibilité, aucune panne au niveau d’un centre de données ne peut mettre toutes vos machines virtuelles hors connexion.

Pendant la création de machines virtuelles Azure, vous devez choisir entre configurer des groupes à haute disponibilité ou des zones de disponibilité.  Une machine virtuelle Azure ne peut pas faire partie des deux. 

Si les zones de disponibilité peuvent fournir une meilleure disponibilité que les groupes à haute disponibilité (99,99 % contre 99,95 %), les performances doivent également être prises en considération. Les machines virtuelles d’un groupe à haute disponibilité peuvent être placées dans un [groupe de placement de proximité](../../../virtual-machines/co-location.md) qui garantit qu’elles sont proches les unes des autres, réduisant ainsi la latence réseau entre elles. Les machines virtuelles situées dans différentes zones de disponibilité auront une plus grande latence réseau entre elles, ce qui peut augmenter le temps nécessaire à la synchronisation des données entre le réplica principal et les réplicas secondaires. Cela peut entraîner des retards sur le réplica principal et accroître le risque de perte de données en cas de basculement non planifié. Il est important de tester la solution proposée en charge et de vérifier qu’elle respecte les contrats SLA en termes de performances et de disponibilité.

## <a name="connectivity"></a>Connectivité

Pour relier l’expérience locale de connexion à votre écouteur de groupe de disponibilité, déployez vos machines virtuelles SQL Server sur [plusieurs sous-réseaux](availability-group-manually-configure-prerequisites-tutorial-multi-subnet.md) au sein du même réseau virtuel. Quand vous avez plusieurs réseaux, vous n’avez pas besoin d’une dépendance supplémentaire sur un équilibreur de charge Azure ou d’un nom de réseau distribué (DNN) pour router votre trafic vers votre écouteur. 

Si vous déployez vos machines virtuelles SQL Server dans un même sous-réseau, vous pouvez configurer un nom de réseau virtuel (VNN) et un équilibreur de charge Azure, ou un nom de réseau distribué (DNN) pour router le trafic vers votre écouteur de groupe de disponibilité. [Passez en revue les différences entre les deux](hadr-windows-server-failover-cluster-overview.md), puis déployez un [nom de réseau distribué (DNN)](availability-group-distributed-network-name-dnn-listener-configure.md) ou un [nom de réseau virtuel (VNN)](availability-group-vnn-azure-load-balancer-configure.md) pour votre groupe de disponibilité. 

La plupart des fonctionnalités de SQL Server fonctionnent de façon transparente avec les groupes de disponibilité lors de l’utilisation du DNN, mais certaines fonctionnalités peuvent nécessiter une attention particulière. Pour plus d’informations, consultez [Interopérabilité des groupes de disponibilité et de DNN](availability-group-dnn-interoperability.md). 

Il est également important de noter qu’il y a des différences de comportement entre les fonctionnalités de l’écouteur VNN et de l’écouteur DNN : 

- **Temps de basculement** : Le temps de basculement est plus rapide lors de l’utilisation d’un écouteur DNN, car il n’est pas nécessaire d’attendre que l’équilibreur de charge réseau détecte l’événement de défaillance et change son routage. 
- **Connexions existantes** : les connexions à une *base de données spécifique* au sein d’un groupe de disponibilité de basculement qui bascule se ferment, mais d’autres connexions au réplica principal restent ouvertes puisque le DNN reste en ligne pendant le processus de basculement. Ce comportement diffère de l’environnement VNN traditionnel, où toutes les connexions au réplica principal se ferment généralement quand le groupe de disponibilité bascule, que l’écouteur est mis hors connexion et que le réplica principal passe au rôle secondaire. Lors de l’utilisation d’un écouteur DNN, il peut être nécessaire d’ajuster les chaînes de connexion d’application pour garantir que les connexions sont redirigées vers le nouveau réplica principal après un basculement.
- **Transactions ouvertes** : les transactions ouvertes sur une base de données dans un groupe de disponibilité de basculement se ferment et sont restaurées, et vous devez vous reconnecter *manuellement*. Par exemple, dans SQL Server Management Studio, fermez la fenêtre de requête et ouvrez-en une nouvelle. 

La configuration d’un écouteur VNN dans Azure nécessite un équilibreur de charge. Il existe deux options principales pour les équilibreurs de charge dans Azure : externe (public) ou interne. L’équilibreur de charge externe (public) est accessible sur Internet et est associé à une adresse IP virtuelle publique accessible via Internet. Un équilibreur de charge interne prend en charge seulement des clients qui sont au sein du même réseau virtuel. Pour les deux types d’équilibreurs de charge, vous devez activer [Retour direct du serveur](../../../load-balancer/load-balancer-multivip-overview.md#rule-type-2-backend-port-reuse-by-using-floating-ip). 

Vous pouvez encore vous connecter à chaque réplica de disponibilité séparément en vous connectant directement à l’instance de service. En outre, puisque les groupes de disponibilité sont à compatibilité descendante avec les clients de mise en miroir de bases de données, vous pouvez vous connecter aux réplicas de disponibilité comme les serveurs partenaires de mise en miroir de bases de données tant que les réplicas sont configurés de façon similaire à la mise en miroir de bases de données :

* Il y a un réplica principal et un réplica secondaire.
* Le réplica secondaire est configuré comme non lisible (option **Secondaire accessible en lecture** définie sur **Non**).

Voici un exemple de chaîne de connexion cliente, qui correspond à cette configuration de type mise en miroir de bases de données, utilisant ADO.NET ou SQL Server Native Client :

```console
Data Source=ReplicaServer1;Failover Partner=ReplicaServer2;Initial Catalog=AvailabilityDatabase;
```

Pour plus d’informations sur la connectivité client, consultez :

* [Utilisation de mots clés de chaîne de connexion avec SQL Server Native Client](/sql/relational-databases/native-client/applications/using-connection-string-keywords-with-sql-server-native-client)
* [Connecter des clients à une session de mise en miroir de bases de données (SQL Server)](/sql/database-engine/database-mirroring/connect-clients-to-a-database-mirroring-session-sql-server)
* [Connexion à l’écouteur du groupe de disponibilité dans un environnement hybride](/archive/blogs/sqlalwayson/connecting-to-availability-group-listener-in-hybrid-it)
* [Écouteurs de groupe de disponibilité, connectivité client et basculement d’application (SQL Server)](/sql/database-engine/availability-groups/windows/listeners-client-connectivity-application-failover)
* [Utilisation de chaînes de connexion de mise en miroir de bases de données avec des groupes de disponibilité](/sql/database-engine/availability-groups/windows/listeners-client-connectivity-application-failover)

## <a name="lease-mechanism"></a>Mécanisme de bail 

Pour SQL Server, la DLL de ressource du groupe de disponibilité détermine l’état d’intégrité du groupe de disponibilité à l’aide du mécanisme de bail et de la détection de l’état d’intégrité AlwaysOn. La DLL de ressource du groupe de disponibilité expose l’intégrité des ressources via l’opération *IsAlive*. Le moniteur de ressource interroge IsAlive à l’intervalle de pulsation du cluster, qui est défini par les valeurs **CrossSubnetDelay** et **SameSubnetDelay** au niveau du cluster. Sur un nœud principal, le service de cluster lance un basculement chaque fois que l’appel de IsAlive à la DLL de ressource indique que le groupe de disponibilité n’est pas sain.

La DLL de ressource du groupe de disponibilité supervise l’état des composants internes de SQL Server. Sp_server_diagnostics signale l’intégrité de ces composants à SQL Server selon un intervalle contrôlé par **HealthCheckTimeout**.

Contrairement à d’autres mécanismes de basculement, l’instance SQL Server joue un rôle actif dans le mécanisme de bail. Le mécanisme de bail est utilisé comme validation *LooksAlive* entre l’hôte des ressources du cluster et le processus SQL Server. Le mécanisme est utilisé pour garantir que les deux côtés (le service de cluster et le service SQL Server) sont fréquemment en contact dans le but de vérifier l’état de l’autre et empêcher un scénario de type Split-Brain.

Lors de la configuration d’un groupe de disponibilité dans des machines virtuelles Azure, il est souvent nécessaire de configurer ces seuils différemment de la façon dont ils seraient configurés dans un environnement local. Pour configurer les paramètres de seuil conformément aux bonnes pratiques pour les machines virtuelles Azure, consultez les [bonnes pratiques pour les clusters](hadr-cluster-best-practices.md). 


## <a name="network-configuration"></a>Configuration réseau  

Déployez vos machines virtuelles SQL Server sur plusieurs sous-réseaux autant que possible pour éviter la dépendance sur un équilibreur de charge Azure ou un nom de réseau distribué (DNN) afin d’acheminer le trafic vers votre écouteur de groupe de disponibilité. 

Sur un cluster de basculement de machine virtuelle Azure, nous recommandons une seule carte réseau par serveur (nœud de cluster). Le réseau Azure a une redondance physique, ce qui rend inutiles les cartes réseau supplémentaires sur un cluster de basculement de machine virtuelle Azure. Même si le rapport de validation du cluster émet un avertissement indiquant que les nœuds sont accessibles sur un seul réseau, vous pouvez ignorer ce dernier sans problème sur les clusters de basculement de machine virtuelle Azure.

## <a name="basic-availability-group"></a>Groupe de disponibilité de base

Comme le groupe de disponibilité de base n’autorise pas plus d’un réplica secondaire et qu’il n’y a pas d’accès en lecture au réplica secondaire, vous pouvez utiliser les chaînes de connexion de mise en miroir de bases de données pour les groupes de disponibilité de base. En utilisant la chaîne de connexion, vous n’avez plus besoin d’avoir des écouteurs. Ne plus dépendre des écouteurs est utile pour les groupes de disponibilité sur les machines virtuelles Azure, car un équilibreur de charge ou l’ajout d’adresses IP supplémentaires à l’équilibreur de charge ne sont plus nécessaires quand vous avez plusieurs écouteurs pour des bases de données supplémentaires. 

Par exemple, pour se connecter explicitement en utilisant TCP/IP à la base de données de groupe de disponibilité AdventureWorks sur Réplica_A ou Réplica_B d’un groupe de disponibilité de base (ou de tout groupe de disponibilité qui n’a qu’un seul réplica secondaire et où l’accès en lecture n’est pas autorisé dans le réplica secondaire), une application cliente peut fournir la chaîne de connexion de mise en miroir de base de données pour se connecter au groupe de disponibilité.

`Server=Replica_A; Failover_Partner=Replica_B; Database=AdventureWorks; Network=dbmssocn`


## <a name="deployment-options"></a>Options de déploiement

Il existe plusieurs options pour déployer un groupe de disponibilité sur SQL Server sur des machines virtuelles Azure, certaines comportant davantage d’automatisation que d’autres. 

Le tableau suivant fournit une comparaison des options disponibles :

| | [Portail Azure](availability-group-azure-portal-configure.md), | [Azure CLI/PowerShell](./availability-group-az-commandline-configure.md) | [Modèles de démarrage rapide](availability-group-quickstart-template-configure.md) | [Manuel (un seul sous-réseau)](availability-group-manually-configure-prerequisites-tutorial-single-subnet.md) | [Manuel (plusieurs sous-réseaux)](availability-group-manually-configure-prerequisites-tutorial-multi-subnet.md)
|---------|---------|---------|---------|---------|
|**Version SQL Server** |2016 + |2016 +|2016 +|2012 +|2012 +| 
|**Édition de SQL Server** |Entreprise |Entreprise |Entreprise |Entreprise, Standard|Entreprise, Standard|
|**Version de Windows Server**| 2016 + | 2016 + | 2016 + | Tous| Tous|
|**Crée le cluster à votre place**|Oui|Oui | Oui |Non| Non| 
|**Crée le groupe de disponibilité à votre place** |Oui |Non|Non|Non| Non| 
|**Crée un écouteur et un équilibreur de charge de manière indépendante** |Non|Non|Non|Oui|N/A|
|**Possibilité de créer un écouteur DNN à l’aide de cette méthode ?**|Non|Non|Non|Oui|N/A|
|**Configuration de quorum WSFC**|Témoin de cloud|Témoin de cloud|Témoin de cloud|Tous|Tous|
|**Reprise d’activité avec plusieurs régions** |Non|Non|Non|Oui|Oui|
|**Prise en charge de plusieurs sous-réseaux** |Non|Non|Non|N/A|Oui|
|**Prise en charge d'un domaine d'application existant**|Oui|Oui|Oui|Oui|Oui|
|**Reprise d’activité avec plusieurs zones dans la même région**|Oui|Oui|Oui|Oui|Oui|
|**Groupe de disponibilité distribué sans domaine d’application**|Non|Non|Non|Oui| Oui| 
|**Groupe de disponibilité distribué sans cluster** |Non|Non|Non|Oui|Oui|
|**Nécessite un équilibreur de charge ou DNN**| Oui | Oui | Oui | Oui | Non|

## <a name="next-steps"></a>Étapes suivantes

Passez en revue les [bonnes pratiques pour HADR](hadr-cluster-best-practices.md), puis commencez à déployer votre groupe de disponibilité avec le [portail Azure](availability-group-azure-portal-configure.md), [Azure CLI/PowerShell](./availability-group-az-commandline-configure.md), les [modèles de démarrage rapide](availability-group-quickstart-template-configure.md) ou [manuellement](availability-group-manually-configure-prerequisites-tutorial-single-subnet.md).

Vous pouvez également déployer un [groupe de disponibilité sans cluster](availability-group-clusterless-workgroup-configure.md) ou un groupe de disponibilité dans [plusieurs régions](availability-group-manually-configure-multiple-regions.md).

Pour en savoir plus, consultez :

- [Cluster de basculement Windows Server avec SQL Server sur des machines virtuelles Azure](hadr-windows-server-failover-cluster-overview.md)
- [Vue d’ensemble des groupes de disponibilité Always On](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)
