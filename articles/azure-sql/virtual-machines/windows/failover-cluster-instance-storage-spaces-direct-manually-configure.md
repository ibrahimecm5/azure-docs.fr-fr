---
title: Créer une FCI avec Espaces de stockage direct
description: Utiliser Espaces de stockage direct pour créer une instance de cluster de basculement (FCI) avec SQL Server sur des machines virtuelles Azure.
services: virtual-machines
documentationCenter: na
author: rajeshsetlem
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.custom: na, devx-track-azurepowershell
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/10/2021
ms.author: rsetlem
ms.reviewer: mathoma
ms.openlocfilehash: 56a939e11c9daabeb44da7fa79b6e05841401ab3
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132158881"
---
# <a name="create-an-fci-with-storage-spaces-direct-sql-server-on-azure-vms"></a>Créer une FCI avec Espaces de stockage direct (SQL Server sur machines virtuelles Azure)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!TIP]
> Évitez le recours à une instance Azure Load Balancer et à un nom de réseau distribué pour votre instance de cluster de basculement en créant vos machines virtuelles SQL Server dans [plusieurs sous-réseaux](failover-cluster-instance-prepare-vm.md#subnets) au sein du même réseau virtuel Azure.

Cet article explique comment créer une instance de cluster de basculement (FCI) à l’aide d’[Espaces de stockage direct](/windows-server/storage/storage-spaces/storage-spaces-direct-overview) avec SQL Server sur des machines virtuelles Azure. Espaces de stockage direct agit comme un réseau de zone de stockage virtuel (VSAN) basé sur logiciel qui synchronise le stockage (disques de données) entre les nœuds (machines virtuelles Azure) dans un cluster Windows. 

Pour plus d’informations, consultez une présentation de [ICF avec SQL Server sur les machines virtuelles Azure](failover-cluster-instance-overview.md) et les [meilleures pratiques de cluster](hadr-cluster-best-practices.md). 

> [!NOTE]
> Il est maintenant possible d’effectuer un lift-and-shift de votre solution d’instance de cluster de basculement vers SQL Server sur des machines virtuelles Azure à l’aide d’Azure Migrate. Pour en savoir plus, consultez [Migrer une instance de cluster de basculement](../../migration-guides/virtual-machines/sql-server-failover-cluster-instance-to-sql-on-azure-vm.md). 


## <a name="overview"></a>Vue d’ensemble 

La technologie [Espaces de stockage direct (S2D)](/windows-server/storage/storage-spaces/storage-spaces-direct-overview) prend en charge deux types d’architectures : convergée et hyperconvergée. Une infrastructure hyperconvergée place le stockage sur les mêmes serveurs que ceux qui hébergent l’application en cluster, de sorte que le stockage se trouve sur chaque nœud FCI SQL Server. 

Le diagramme suivant montre la solution complète, laquelle utilise des espaces de stockage direct hyperconvergés avec SQL Server sur des machines virtuelles Azure : 

![Diagramme de la solution complète, utilisant des espaces de stockage direct hyperconvergés](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/00-sql-fci-s2d-complete-solution.png)

Le diagramme précédent montre les ressources suivantes dans le même groupe de ressources :

- Deux machines virtuelles dans un cluster de basculement Windows Server. Lorsqu’une machine virtuelle se trouve dans un cluster de basculement, elle est également désignée comme *nœud* ou *nœud de cluster*.
- Chaque machine virtuelle possède au moins deux disques de données.
- La technologie Espaces de stockage direct synchronise les données sur le disque de données et présente le stockage synchronisé en tant que pool de stockage.
- Le pool de stockage présente un volume partagé de cluster (CSV) au cluster de basculement.
- Le rôle du cluster de l’instance de cluster de basculement SQL Server utilise le volume partagé de cluster pour les lecteurs de données.
- Un équilibreur de charge Azure pour contenir l’adresse IP de l’instance de cluster de basculement SQL Server pour un scénario de sous-réseau unique.
- Un groupe à haute disponibilité Azure contient toutes les ressources.

 > [!NOTE]
> Vous pouvez créer la solution complète dans Azure à partir d’un modèle. Un exemple de modèle est disponible sur la page [Modèles de démarrage rapide Azure (en anglais)](https://github.com/MSBrett/azure-quickstart-templates/tree/master/sql-server-2016-fci-existing-vnet-and-ad) de GitHub. Cet exemple n’est pas conçu ni testé pour une charge de travail spécifique. Vous pouvez exécuter le modèle pour créer une instance de cluster de basculement SQL Server avec le stockage Espaces de stockage direct connecté à votre domaine. Vous pouvez évaluer le modèle et le modifier selon vos besoins.


## <a name="prerequisites"></a>Prérequis

Avant de suivre les instructions décrites dans cet article, vous devez déjà disposer des éléments suivants :

- Un abonnement Azure. Démarrer [gratuitement](https://azure.microsoft.com/free/). 
- [Deux machines virtuelles Windows Azure préparées ou plus](failover-cluster-instance-prepare-vm.md) dans un [groupe à haute disponibilité](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set).
- Un compte qui dispose des autorisations nécessaires pour créer des objets sur les machines virtuelles Azure et dans Active Directory.
- La dernière version de [PowerShell](/powershell/azure/install-az-ps). 

## <a name="create-windows-failover-cluster"></a>Créer un cluster de basculement Windows

La procédure de création de votre cluster de basculement Windows Server varient selon que vous avez déployé vos machines virtuelles SQL Server sur un seul ou plusieurs sous-réseaux. Suivez les étapes indiquées dans le tutoriel du scénario à [plusieurs sous-réseaux](availability-group-manually-configure-tutorial-multi-subnet.md#add-failover-cluster-feature) ou celui du scénario à [un seul sous-réseau](availability-group-manually-configure-tutorial-single-subnet.md#create-the-cluster). Ces tutoriels servent à créer un groupe de disponibilité, mais la procédure de création du cluster est la même. 

## <a name="configure-quorum"></a>Configurer un quorum

Bien que le témoin de disque soit l’option de quorum la plus résiliente, il n’est pas pris en charge pour les instances de cluster de basculement configurées avec les espaces de stockage direct. Par conséquent, le témoin cloud est la solution de quorum recommandée pour ce type de configuration de cluster pour SQL Server sur les machines virtuelles Azure.

Si vous avez un nombre pair de votes dans le cluster, configurez la [Solution de quorum](hadr-cluster-quorum-configure-how-to.md) qui correspond le mieux aux besoins de votre entreprise. Pour plus d’informations, consultez [Quorum avec les machines virtuelles SQL Server](hadr-windows-server-failover-cluster-overview.md#quorum). 

## <a name="validate-the-cluster"></a>Valider le cluster

Validez le cluster dans l’interface utilisateur Gestionnaire du cluster de basculement ou à l’aide de PowerShell.

Pour valider le cluster à l’aide de l’interface utilisateur, procédez comme suit sur l’une des machines virtuelles :

1. Sous **Gestionnaire de serveur**, sélectionnez **Outils**, puis **Gestionnaire du cluster de basculement**.
1. Sous **Gestionnaire du cluster de basculement**, sélectionnez **Action**, puis **Valider la configuration**.
1. Sélectionnez **Suivant**.
1. Sous **Sélectionner des serveurs ou un cluster**, entrez le nom des deux machines virtuelles.
1. Sous **Options de test**, sélectionnez **Exécuter uniquement les tests que je sélectionne**. 
1. Sélectionnez **Suivant**.
1. Sous **Sélection des tests**, sélectionnez tous les tests à l’exception de **Stockage**, comme illustré ici :

   ![Sélectionner les tests de validation du cluster](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/10-validate-cluster-test.png)

1. Sélectionnez **Suivant**.
1. Sous **Confirmation**, sélectionnez **Suivant**.

    L’assistant **Valider une configuration** exécute les tests de validation.

Pour valider le cluster avec PowerShell, exécutez le script suivant à partir d’une session PowerShell d’administrateur sur l’une des machines virtuelles :

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Storage Spaces Direct", "Inventory", "Network", "System Configuration"
   ```


## <a name="add-storage"></a>Ajouter du stockage

Les disques Espaces de stockage direct doivent être vides. Ils ne peuvent pas contenir de partitions ou d’autres données. Pour nettoyer les disques, suivez les instructions de la rubrique [Déployer des espaces de stockage direct](/windows-server/storage/storage-spaces/deploy-storage-spaces-direct#step-31-clean-drives).

1. [Activer les espaces de stockage direct](/windows-server/storage/storage-spaces/deploy-storage-spaces-direct#step-35-enable-storage-spaces-direct).

   Le script PowerShell suivant active les Espaces de stockage direct :  

   ```powershell
   Enable-ClusterS2D
   ```

   Dans le **Gestionnaire du cluster de basculement**, vous pouvez maintenant voir le pool de stockage.

1. [Créez un volume](/windows-server/storage/storage-spaces/deploy-storage-spaces-direct#step-36-create-volumes).

   La technologie Espaces de stockage direct crée automatiquement un pool de stockage lorsque vous l’activez. Vous êtes maintenant prêt à créer un volume. La cmdlet PowerShell `New-Volume` automatise le processus de création du volume. Ce processus comprend la mise en forme, l’ajout du volume au cluster et la création d’un CSV. Cet exemple crée un volume partagé de cluster de 800 gigaoctets (Go) :

   ```powershell
   New-Volume -StoragePoolFriendlyName S2D* -FriendlyName VDisk01 -FileSystem CSVFS_REFS -Size 800GB
   ```   

   Une fois que vous avez exécuté la commande précédente, un volume de 800 Go est monté en tant que ressource de cluster. Le volume se trouve sous `C:\ClusterStorage\Volume1\`.

   Cette capture d’écran montre un CSV avec Espaces de stockage direct :

   ![Capture d’écran d’un volume partagé de cluster avec Espaces de stockage direct](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/15-cluster-shared-volume.png)



## <a name="test-cluster-failover"></a>Tester le basculement de cluster

Testez le basculement de votre cluster. Dans le **Gestionnaire du cluster de basculement**, cliquez avec le bouton droit sur votre cluster et sélectionnez **Autres actions** > **Déplacer une ressource de cluster principale** > **Sélectionner le nœud** et sélectionnez l’autre nœud du cluster. Déplacez la ressource de cluster principale vers chaque nœud du cluster, puis replacez-la sur le nœud principal. Si vous parvenez à déplacer le cluster vers chaque nœud, vous êtes prêt à installer SQL Server.  

:::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/test-cluster-failover.png" alt-text="Testez le basculement du cluster en déplaçant la ressource principale sur les autres nœuds":::

## <a name="create-sql-server-fci"></a>Créer l’instance de cluster de basculement SQL Server

Après avoir configuré le cluster de basculement et tous les composants du cluster, notamment le stockage, vous pouvez créer l’instance de cluster de basculement SQL Server.

1. Connectez-vous à la première machine virtuelle avec RDP.

1. Dans **Gestionnaire du cluster de basculement**, vérifiez que toutes les ressource de cluster principales se trouvent sur la première machine virtuelle. Si nécessaire, déplacez toutes les ressources vers cette machine virtuelle.

1. Recherchez le support d’installation. Si la machine virtuelle utilise l’une des images Azure Marketplace, le support se situe sous `C:\SQLServer_<version number>_Full`. Sélectionnez **Configuration**.

1. Dans le **Centre d’installation SQL Server**, sélectionnez **Installation**.

1. Sélectionnez **Installation d’un nouveau cluster de basculement SQL Server**. Suivez les instructions de l’Assistant pour installer l’instance de cluster de basculement SQL Server.

1. Sur la page **Configuration réseau du cluster**, l’adresse IP à indiquer varie selon que vos machines virtuelles SQL Server ont été déployées sur un seul ou plusieurs sous-réseaux. 

   1. Pour un **environnement à un seul sous-réseau**, indiquez l’adresse IP que vous prévoyez d’ajouter à l’instance [Azure Load Balancer](failover-cluster-instance-vnn-azure-load-balancer-configure.md)
   1. Pour un **environnement à plusieurs sous-réseaux**, indiquez l’adresse IP secondaire dans le sous-réseau de la _première_ machine virtuelle SQL Server que vous aviez désignée comme [adresse IP du nom réseau de l’instance de cluster de basculement](failover-cluster-instance-prepare-vm.md#assign-secondary-ip-addresses) :

   :::image type="content" source="./media/failover-cluster-instance-azure-shared-disk-manually-configure/sql-install-cluster-network-secondary-ip-vm-1.png" alt-text="Fournissez l’adresse IP secondaire dans le sous-réseau de la première machine virtuelle SQL Server que vous avez précédemment désignée comme adresse IP du nom réseau de l’instance de cluster de basculement":::

1. Dans **Configuration du moteur de base de données**, les répertoires de données de l’instance de cluster de basculement doivent se trouver sur un stockage en cluster. Avec Espaces de stockage direct, il ne s’agit pas d’un disque partagé, mais d’un point de montage vers un volume sur chaque serveur. La technologie Espaces de stockage direct synchronise le volume entre les deux nœuds. Le volume est présenté au cluster en tant que CSV. Utilisez le point de montage du volume partagé de cluster pour les répertoires de données.

   ![Répertoires de données](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/20-data-dicrectories.png)

1. Une fois que l’Assistant a terminé, le programme d’installation installe une FCI SQL Server sur le premier nœud.

1. Une fois l’instance de cluster de basculement installée sur le premier nœud, connectez-vous au deuxième nœud à l’aide du protocole RDP.

1. Ouvrez le **Centre d’installation SQL Server**. Sélectionnez **Installation**.

1. Sélectionnez **Ajouter un nœud à un cluster de basculement SQL Server**. Suivez les instructions de l’Assistant pour installer SQL Server et ajouter le nœud à l’instance de cluster de basculement.

1. Pour un scénario à plusieurs sous-réseaux : dans **Configuration réseau du cluster**, entrez l’adresse IP secondaire dans le sous-réseau de la _deuxième_ machine virtuelle SQL Server que vous aviez désignée comme [adresse IP du nom réseau de l’instance de cluster de basculement](failover-cluster-instance-prepare-vm.md#assign-secondary-ip-addresses)

    :::image type="content" source="./media/failover-cluster-instance-azure-shared-disk-manually-configure/sql-install-cluster-network-secondary-ip-vm-2.png" alt-text="Entrez l’adresse IP secondaire dans le sous-réseau de la deuxième machine virtuelle SQL Server que vous avez précédemment désignée comme adresse IP du nom réseau de l’instance de cluster de basculement":::

    Lorsque vous sélectionnez **Suivant** dans **Configuration réseau du cluster**, le programme d’installation affiche une boîte de dialogue indiquant que l’installation de SQL Server a détecté plusieurs sous-réseaux (cf. image d’exemple).  Sélectionnez **Oui** pour confirmer. 

    :::image type="content" source="./media/failover-cluster-instance-azure-shared-disk-manually-configure/sql-install-multi-subnet-confirmation.png" alt-text="Confirmation de sous-réseaux multiples":::

1. Une fois les instructions de l’Assistant suivies, le programme d’installation ajoute le deuxième nœud FCI SQL Server. 

1. Répétez ces étapes sur les autres nœuds que vous voulez ajouter à l’instance du cluster de basculement du SQL Server. 


>[!NOTE]
> Les images de la galerie de la Place de marché Azure sont fournies avec SQL Server Management Studio. Si vous n’avez pas utilisé une image de la place de marché, [téléchargez SQL Server Management Studio (SSMS)](/sql/ssms/ownload-sql-server-management-studio-ssms).


## <a name="register-with-sql-iaas-extension"></a>S’inscrire à l’extension IaaS SQL 

Pour gérer votre machine virtuelle SQL Server à partir du portail, inscrivez-la auprès de l’extension SQL IaaS Agent dans le [mode d’administration léger](sql-agent-extension-manually-register-single-vm.md#lightweight-mode), actuellement le seul mode pris en charge avec FCI et SQL Server sur les machines virtuelles Azure. 


Inscrire la machine virtuelle SQL Server en mode léger avec PowerShell (le type de licence peut être `PAYG` ou `AHUB`) :

```powershell-interactive
# Get the existing compute VM
$vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
         
# Register SQL VM with 'Lightweight' SQL IaaS agent
New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
   -LicenseType PAYG -SqlManagementType LightWeight  
```

## <a name="configure-connectivity"></a>Configurer la connectivité

Si vous avez déployé vos machines virtuelles SQL Server dans plusieurs sous-réseaux, ignorez cette étape. Si vous avez déployé vos machines virtuelles SQL Server sur un seul sous-réseau, vous devez configurer un composant supplémentaire pour acheminer le trafic vers votre instance FCI. Vous pouvez configurer un nom de réseau virtuel avec une instance Azure Load Balancer ou un nom de réseau distribué pour une instance de cluster de basculement. [Passez en revue les différences entre les deux](hadr-windows-server-failover-cluster-overview.md#virtual-network-name-vnn), puis déployez un [nom de réseau distribué](failover-cluster-instance-distributed-network-name-dnn-configure.md) ou un [nom de réseau virtuel et Azure Load Balancer](failover-cluster-instance-vnn-azure-load-balancer-configure.md) pour votre instance de cluster de basculement.  


## <a name="limitations"></a>Limites

- Les machines virtuelles Azure prennent en charge Microsoft Distributed Transaction Coordinator (MSDTC) sur Windows Server 2019 avec un stockage sur des CSV et un [équilibreur de charge standard](../../../load-balancer/load-balancer-overview.md). MSDTC n’est pas pris en charge sur Windows Server 2016 ni les versions antérieures. 
- Les disques qui ont été joints en tant que disques au format NTFS peuvent être utilisés avec Espaces de stockage direct uniquement si l’option d’éligibilité du disque est décochée ou désactivée lorsque le stockage est ajouté au cluster. 
- Seule l’inscription auprès de l’extension SQL IaaS Agent en [mode d’administration léger](sql-server-iaas-agent-extension-automate-management.md#management-modes) est prise en charge.
- Les instances de cluster de basculement utilisant les espaces de stockage direct comme stockage partagé ne prennent pas en charge l’utilisation d’un témoin de disque pour le quorum du cluster. Utilisez un témoin cloud à la place. 

## <a name="next-steps"></a>Étapes suivantes

Si Espaces de stockage direct n’est pas la solution de stockage FCI appropriée, envisagez de créer votre FCI à l’aide de [disques partagés Azure](failover-cluster-instance-azure-shared-disks-manually-configure.md) ou de [partages de fichiers Premium](failover-cluster-instance-premium-file-share-manually-configure.md) à la place. 

Pour en savoir plus, consultez :

- [Cluster de basculement Windows Server avec SQL Server sur des machines virtuelles Azure](hadr-windows-server-failover-cluster-overview.md)
- [Instances de cluster de basculement avec SQL Server sur des machines virtuelles Azure](failover-cluster-instance-overview.md)
- [Vue d’ensemble des instances de cluster de basculement](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
- [Paramètres HADR pour SQL Server sur les machines virtuelles Azure](hadr-cluster-best-practices.md)
