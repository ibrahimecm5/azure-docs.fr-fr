---
title: Configurer un écouteur DNN pour le groupe de disponibilité
description: Découvrez comment configurer un écouteur de nom de réseau distribué (DNN) pour remplacer votre écouteur de nom de réseau virtuel (VNN) et acheminer le trafic vers votre groupe de disponibilité Always On sur Microsoft SQL Server sur une machine virtuelle Azure.
services: virtual-machines-windows
documentationcenter: na
author: rajeshsetlem
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/10/2021
ms.author: rsetlem
ms.reviewer: mathoma
ms.openlocfilehash: f4a79ae5b1083bf9091486e9f4d87c6fdad14747
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132156897"
---
# <a name="configure-a-dnn-listener-for-an-availability-group"></a>Configurer un écouteur DNN pour un groupe de disponibilité
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!TIP]
> Éliminez la nécessité de disposer d’un nom de réseau distribué pour votre groupe de disponibilité Always On en créant vos machines virtuelles SQL Server dans [plusieurs sous-réseaux](availability-group-manually-configure-prerequisites-tutorial-multi-subnet.md) au sein du même réseau virtuel Azure.

Avec Microsoft SQL Server sur des machines virtuelles Azure dans un seul sous-réseau, le nom de réseau distribué (DNN) achemine le trafic vers la ressource en cluster appropriée. Il permet de se connecter plus facilement à un groupe de disponibilité Always On qu’avec l’écouteur de nom de réseau virtuel (VNN), sans avoir à utiliser Azure Load Balancer.

Cet article vous apprend à configurer un écouteur DNN pour remplacer l’écouteur VNN et à acheminer le trafic vers votre groupe de disponibilité avec Microsoft SQL Server sur des machines virtuelles Azure pour la haute disponibilité et la récupération d’urgence (HADR).


Pour une autre option de connectivité, utilisez plutôt un [écouteur VNN et Azure Load Balancer](availability-group-vnn-azure-load-balancer-configure.md).

## <a name="overview"></a>Vue d’ensemble

Un écouteur DNN (Distributed Network Name) remplace l’écouteur du groupe de disponibilité VNN (nom de réseau virtuel traditionnel) lorsqu’il est utilisé avec des [groupes de disponibilité Always On sur des machines virtuelles SQL Server](availability-group-overview.md). Cela annule la nécessité d’un Azure Load Balancer pour acheminer le trafic, ce qui simplifie le déploiement, la maintenance et l’amélioration du basculement.

Utilisez l’écouteur DNN pour remplacer un écouteur VNN existant, ou utilisez-le conjointement avec un écouteur VNN existant afin que votre groupe de disponibilité dispose de deux points de connexion distincts : l’un utilisant le nom de l’écouteur VNN (et le port s’il n’est pas défini par défaut) et l’autre à l’aide du nom et du port de l’écouteur DNN.

> [!CAUTION]
> Le comportement du routage en cas d’utilisation d’un écouteur DNN est différent quand vous utilisez un écouteur VNN. N’utilisez pas le port 1433. Pour en savoir plus, consultez la section [Considérations relatives au port](#port-considerations), plus loin dans cet article.

## <a name="prerequisites"></a>Prérequis

Avant d’effectuer les étapes décrites dans cet article, vous devez déjà disposer des éléments suivants :

- SQL Server à partir de [SQL Server 2019 CU8](https://support.microsoft.com/topic/cumulative-update-8-for-sql-server-2019-ed7f79d9-a3f0-a5c2-0bef-d0b7961d2d72) et versions ultérieures, [SQL Server 2017 CU25](https://support.microsoft.com/topic/kb5003830-cumulative-update-25-for-sql-server-2017-357b80dc-43b5-447c-b544-7503eee189e9) et versions ultérieures, ou [SQL Server 2016 SP3](https://support.microsoft.com/topic/kb5003279-sql-server-2016-service-pack-3-release-information-46ab9543-5cf9-464d-bd63-796279591c31) et versions ultérieures sur Windows Server 2016 et versions ultérieures.
- Avoir décidé que le nom du réseau distribué est l’option de [connectivité appropriée pour votre solution HADR](hadr-cluster-best-practices.md#connectivity).
- Avoir configuré votre [groupe de disponibilité Always On](availability-group-overview.md). 
- Avoir installé la version la plus récente de [PowerShell](/powershell/azure/install-az-ps). 
- Avoir identifié le seul port à utiliser pour l’écouteur DNN. Le port utilisé pour un écouteur DNN doit être unique parmi tous les réplicas du groupe de disponibilité ou de l’instance de cluster de basculement.  Aucune autre connexion ne peut partager le même port.



## <a name="create-script"></a>Création du script

Utilisez PowerShell pour créer la ressource de nom de réseau distribué (DNN) et l’associer à votre groupe de disponibilité.

Pour ce faire, procédez comme suit :

1. Ouvrez un éditeur de texte, tel que le Bloc-notes.
1. Copiez et collez le script suivant :

   ```powershell
   param (
      [Parameter(Mandatory=$true)][string]$Ag,
      [Parameter(Mandatory=$true)][string]$Dns,
      [Parameter(Mandatory=$true)][string]$Port
   )
   
   Write-Host "Add a DNN listener for availability group $Ag with DNS name $Dns and port $Port"
   
   $ErrorActionPreference = "Stop"
   
   # create the DNN resource with the port as the resource name
   Add-ClusterResource -Name $Port -ResourceType "Distributed Network Name" -Group $Ag 
   
   # set the DNS name of the DNN resource
   Get-ClusterResource -Name $Port | Set-ClusterParameter -Name DnsName -Value $Dns 
   
   # start the DNN resource
   Start-ClusterResource -Name $Port
   
   
   $Dep = Get-ClusterResourceDependency -Resource $Ag
   if ( $Dep.DependencyExpression -match '\s*\((.*)\)\s*' )
   {
   $DepStr = "$($Matches.1) or [$Port]"
   }
   else
   {
   $DepStr = "[$Port]"
   }
   
   Write-Host "$DepStr"
   
   # add the Dependency from availability group resource to the DNN resource
   Set-ClusterResourceDependency -Resource $Ag -Dependency "$DepStr"
   
   
   #bounce the AG resource
   Stop-ClusterResource -Name $Ag
   Start-ClusterResource -Name $Ag
   ```

1. Enregistrez le script sous la forme d’un fichier de `.ps1`, tel que `add_dnn_listener.ps1`.

## <a name="execute-script"></a>Exécutez le script

Pour créer l’écouteur DNN, exécutez le script en passant les paramètres pour le nom du groupe de disponibilité, le nom de l’écouteur et le port.

Par exemple, en supposant que le nom du groupe de disponibilité `ag1`, le nom de l’écouteur `dnnlsnr`et le port de l’écouteur `6789`, procédez comme suit :

1. Ouvrez un outil d’interface de ligne de commande, tel que l’invite de commandes ou PowerShell.
1. Accédez à l’emplacement où vous avez enregistré le script `.ps1` , tel que c:\Documents.
1. Exécutez le script : ```add_dnn_listener.ps1 <ag name> <listener-name> <listener port>```. Par exemple :

   ```console
   c:\Documents> add_dnn_listener.ps1 ag1 dnnlsnr 6789
   ```

## <a name="verify-listener"></a>Vérifiez l’écouteur

Utilisez SQL Server Management Studio ou Transact-SQL pour confirmer que votre écouteur DNN est correctement créé.

### <a name="sql-server-management-studio"></a>SQL Server Management Studio

Développez **écouteurs de groupe de disponibilité** dans [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) pour afficher votre écouteur DNN :

:::image type="content" source="media/availability-group-distributed-network-name-dnn-listener-configure/dnn-listener-in-ssms.png" alt-text="Affichez l’écouteur DNN sous les écouteurs de groupe de disponibilité dans SQL Server Management Studio (SSMS)":::

### <a name="transact-sql"></a>Transact-SQL

Utilisez Transact-SQL pour afficher l’état de l’écouteur DNN :

```sql
SELECT * FROM SYS.AVAILABILITY_GROUP_LISTENERS
```

La valeur `1` pour `is_distributed_network_name` indique que l’écouteur est un écouteur DNN (Distributed Network Name) :

:::image type="content" source="media/availability-group-distributed-network-name-dnn-listener-configure/dnn-listener-tsql.png" alt-text="Utilisez sys.availability_group_listeners pour identifier les écouteurs DNN qui ont une valeur de 1 dans is_distributed_network_name":::

## <a name="update-connection-string"></a>Mettre à jour une chaîne de connexion

Mettez à jour la chaîne de connexion pour toutes les applications qui doivent se connecter à l’écouteur DNN. La chaîne de connexion à l’écouteur DNN doit fournir le numéro de port DNN et spécifier `MultiSubnetFailover=True` dans la chaîne de connexion. Si le client SQL ne prend pas en charge le paramètre `MultiSubnetFailover=True`, il n’est pas compatible avec un écouteur DNN.  

Voici un exemple de chaîne de connexion pour le nom d’écouteur **DNN_Listener** et le port 6789 : 

`DataSource=DNN_Listener,6789,MultiSubnetFailover=True`

## <a name="test-failover"></a>Test de basculement

Testez le basculement du groupe de disponibilité pour garantir la fonctionnalité.

Pour tester le basculement, procédez comme suit :

1. Connectez-vous à l’écouteur DNN ou à l’un des réplicas à l’aide de [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms).
1. Développez **Groupe de disponibilité Always On** dans **Explorateur d’objets**.
1. Cliquez avec le bouton droit sur le groupe de disponibilité, puis choisissez **Type de basculement** pour ouvrir l’**Assistant basculement**.
1. Suivez les invites pour choisir une cible de basculement et faire basculer le groupe de disponibilité sur un réplica secondaire.
1. Vérifiez que la base de données est dans un état synchronisé sur le nouveau réplica principal.
1. (Facultatif) Effectuez une restauration automatique vers le réplica principal d’origine ou un autre réplica secondaire.

## <a name="test-connectivity"></a>Tester la connectivité

Pour tester la connectivité à votre écouteur DNN, procédez comme suit :

1. Ouvrez [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms).
1. Connectez-vous à votre écouteur DNN.
1. Ouvrez une nouvelle fenêtre de requête et vérifiez le réplica auquel vous êtes connecté en exécutant `SELECT @@SERVERNAME`.
1. Faites basculer le groupe de disponibilité sur un autre réplica.
1. Après un laps de temps raisonnable, exécutez `SELECT @@SERVERNAME` pour confirmer que votre groupe de disponibilité est maintenant hébergé sur un autre réplica.

## <a name="limitations"></a>Limites

- Les écouteurs DNN **DOIVENT** être configurés avec un seul port.  Le port ne doit pas être partagé avec une autre connexion sur un réplica.
- Le client qui se connecte à l’écouteur DNN doit prendre en charge le paramètre `MultiSubnetFailover=True` dans la chaîne de connexion. 
- Il peut y avoir plus de points à prendre en compte lorsque vous travaillez avec d’autres fonctionnalités de Microsoft SQL Server et un groupe de disponibilité avec un DNN. Pour plus d’informations, consultez [AG avec l’interopérabilité de DNN](availability-group-dnn-interoperability.md). 

## <a name="port-considerations"></a>Considérations relatives au port

Les écouteurs DNN sont conçus pour écouter toutes les adresses IP, mais sur un seul port spécifique. La résolution de l’entrée DNS du nom de l’écouteur doit correspondre aux adresses de tous les réplicas du groupe de disponibilité. Cela s’effectue automatiquement à l’aide du script PowerShell fourni dans la section [Créer un script](#create-script). Dans la mesure où les écouteurs DNN acceptent les connexions sur toutes les adresses IP, il est essentiel que le port d’écoute soit unique et qu’il ne soit utilisé par aucun autre réplica du groupe de disponibilité. Dans la mesure où SQL Server écoute par défaut sur le port 1433, directement ou via le service SQL Browser, l’utilisation du port 1433 pour l’écouteur DNN est vivement déconseillée. 

## <a name="next-steps"></a>Étapes suivantes

Une fois le groupe de disponibilité déployé, envisagez d’optimiser les [paramètres HADR pour SQL Server sur les machines virtuelles Azure](hadr-cluster-best-practices.md). 


Pour en savoir plus, consultez :

- [Cluster de basculement Windows Server avec SQL Server sur des machines virtuelles Azure](hadr-windows-server-failover-cluster-overview.md)
- [Groupes de disponibilité Always On avec SQL Server sur les machines virtuelles Azure](availability-group-overview.md)
- [Vue d’ensemble des groupes de disponibilité Always On](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)
