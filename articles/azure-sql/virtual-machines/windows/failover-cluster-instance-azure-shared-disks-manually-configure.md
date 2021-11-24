---
title: Créer une instance FCI avec des disques partagés Azure
description: Utiliser des disques partagés Azure pour créer une instance de cluster de basculement(FCI) avec SQL Server sur des machines virtuelles Azure.
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
ms.openlocfilehash: 142c84fa2006e6a5a99fc2997d62f79376758339
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132158553"
---
# <a name="create-an-fci-with-azure-shared-disks-sql-server-on-azure-vms"></a>Créer une instance FCI avec des disques partagés Azure (SQL Server sur les machines virtuelles Azure)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!TIP]
> Évitez le recours à une instance Azure Load Balancer et à un nom de réseau distribué pour votre instance de cluster de basculement en créant vos machines virtuelles SQL Server dans [plusieurs sous-réseaux](failover-cluster-instance-prepare-vm.md#subnets) au sein du même réseau virtuel Azure.

Cet article explique comment créer une instance de cluster de basculement (FCI) à l’aide de disques partagés Azure avec SQL Server sur des machines virtuelles Azure. 

Pour plus d’informations, consultez une présentation de [l’instance FCI avec SQL Server sur les machines virtuelles Azure](failover-cluster-instance-overview.md) et [les meilleures pratiques de cluster](hadr-cluster-best-practices.md). 

> [!NOTE]
> Il est maintenant possible d’effectuer un lift-and-shift de votre solution d’instance de cluster de basculement vers SQL Server sur des machines virtuelles Azure à l’aide d’Azure Migrate. Pour en savoir plus, consultez [Migrer une instance de cluster de basculement](../../migration-guides/virtual-machines/sql-server-failover-cluster-instance-to-sql-on-azure-vm.md). 

## <a name="prerequisites"></a>Prérequis 

Avant de suivre les instructions décrites dans cet article, vous devez déjà disposer des éléments suivants :

- Un abonnement Azure. Démarrer [gratuitement](https://azure.microsoft.com/free/). 
- [Deux ou plusieurs machines virtuelles Windows Azure préparées](failover-cluster-instance-prepare-vm.md) dans un groupe à haute disponibilité ou des zones de disponibilité 
- Un compte qui dispose des autorisations nécessaires pour créer des objets sur les machines virtuelles Azure et dans Active Directory.
- La version la plus récente [d’Azure PowerShell](/powershell/azure/install-az-ps). 

## <a name="add-azure-shared-disk"></a>Ajouter un disque partagé Azure

[Déployez un disque SSD Premium managé sur lequel la fonctionnalité de disque partagé est activée](../../../virtual-machines/disks-shared-enable.md#deploy-a-premium-ssd-as-a-shared-disk). Définissez `maxShares` sur **aligner sur le nombre de nœuds de cluster** pour rendre le disque partageable sur tous les nœuds d’instance de cluster de basculement. 

## <a name="attach-shared-disk-to-vms"></a>Attachement d’un disque partagé à des machines virtuelles

Une fois que vous avez déployé un disque partagé avec maxShares > 1, vous pouvez monter le disque sur les machines virtuelles qui feront office de nœuds dans le cluster. 

Pour attacher le disque partagé à vos machines virtuelles SQL Server, procédez comme suit : 

1. Sur le Portail Azure, sélectionnez la machine virtuelle à laquelle vous allez attacher le disque partagé. 
1. Sélectionnez **Disques** dans le panneau **Paramètres**. 
1. Sélectionnez **Attacher des disques existants** pour attacher le disque partagé à la machine virtuelle. 
1. Choisissez le disque partagé dans la liste déroulante **Nom du disque**. 
1. Sélectionnez **Enregistrer**.
1. Répétez ces étapes pour chacune des machines virtuelles SQL Server servant de nœuds de cluster. 

Au bout de quelques instants, le disque de données est attaché à la machine virtuelle et apparaît dans la liste des disques de données de celle-ci.

## <a name="initialize-shared-disk"></a>Initialisation du disque partagé

Une fois le disque partagé attaché sur toutes les machines virtuelles, vous pouvez initialiser les disques des machines virtuelles qui feront office de nœuds dans le cluster. Initialisez les disques sur **toutes** les machines virtuelles. 


Pour initialiser les disques de vos machines virtuelles SQL Server, procédez comme suit : 
 
1. Connectez-vous à l’une des machines virtuelles.
2. Au sein de la machine virtuelle, ouvrez le menu **Démarrer** et tapez **diskmgmt.msc** dans la zone de recherche pour ouvrir la console **Gestion des disques**.
3. L’outil Gestion des disques détermine que votre nouveau disque n’est pas initialisé et affiche la fenêtre **Initialiser le disque**.
4. Vérifiez que le nouveau disque est sélectionné, puis sélectionnez **OK** pour l’initialiser.
5. Le nouveau disque apparaît comme **non alloué**. Cliquez avec le bouton droit n’importe où sur le disque, puis sélectionnez **Nouveau volume simple**. La fenêtre **Assistant Création d’un volume simple** s’ouvre.
6. Exécutez l’Assistant en conservant tous les paramètres par défaut. Quand vous avez terminé, sélectionnez **Terminer**.
7. Fermez **Gestion des disques**.
8. Une fenêtre contextuelle s’affiche et vous demande de formater le nouveau disque pour que vous puissiez l’utiliser. Sélectionnez **Formater le disque**.
9. Dans la fenêtre **Formater un nouveau disque**, vérifiez les paramètres, puis sélectionnez **Démarrer**.
10. Un avertissement vous informe que le formatage des disques efface toutes les données. Sélectionnez **OK**.
11. Une fois le formatage terminé, sélectionnez **OK**.
12. Répétez ces étapes sur chacune des machines virtuelles SQL Server qui prendront part à l’instance FCI. 

## <a name="create-windows-failover-cluster"></a>Création d’un cluster de basculement Windows

La procédure de création de votre cluster de basculement Windows Server varie selon que vous avez déployé vos machines virtuelles SQL Server sur un seul ou plusieurs sous-réseaux. Suivez les étapes indiquées dans le tutoriel du scénario à [plusieurs sous-réseaux](availability-group-manually-configure-tutorial-multi-subnet.md#add-failover-cluster-feature) ou celui du scénario à [un seul sous-réseau](availability-group-manually-configure-tutorial-single-subnet.md#create-the-cluster). Ces tutoriels servent à créer un groupe de disponibilité, mais la procédure de création du cluster est la même. 

## <a name="configure-quorum"></a>Configurer un quorum

Étant donné que le témoin de disque est l’option de quorum la plus résiliente et que la solution FCI utilise des disques partagés Azure, il est recommandé de configurer un témoin de disque comme solution de quorum. 

Si vous avez un nombre pair de votes dans le cluster, configurez la [Solution de quorum](hadr-cluster-quorum-configure-how-to.md) qui correspond le mieux aux besoins de votre entreprise. Pour plus d’informations, consultez [Quorum avec les machines virtuelles SQL Server](hadr-windows-server-failover-cluster-overview.md#quorum). 

## <a name="validate-cluster"></a>Valider le cluster

Validez le cluster sur l’une des machines virtuelles avec l’interface utilisateur Gestionnaire du cluster de basculement ou PowerShell. 

Pour valider le cluster avec l’interface utilisateur, procédez comme suit : 

1. Sous **Gestionnaire de serveur**, sélectionnez **Outils**, puis **Gestionnaire du cluster de basculement**.
1. Sous **Gestionnaire du cluster de basculement**, sélectionnez **Action**, puis **Valider la configuration**.
1. Sélectionnez **Suivant**.
1. Sous **Sélectionner des serveurs ou un cluster**, entrez le nom des deux machines virtuelles.
1. Sous **Options de test**, sélectionnez **Exécuter uniquement les tests que je sélectionne**. 
1. Sélectionnez **Suivant**.
1. Sous **Sélection des tests**, sélectionnez tous les tests *sauf* **Stockage**.
1. Sélectionnez **Suivant**.
1. Sous **Confirmation**, sélectionnez **Suivant**.  L’assistant **Valider une configuration** exécute les tests de validation.


Pour valider le cluster avec PowerShell, exécutez le script suivant à partir d’une session PowerShell d’administrateur sur l’une des machines virtuelles :

```powershell
Test-Cluster –Node ("<node1>","<node2>") –Include "Inventory", "Network", "System Configuration"
```

## <a name="test-cluster-failover"></a>Tester le basculement de cluster

Testez le basculement de votre cluster. Dans le **Gestionnaire du cluster de basculement**, cliquez avec le bouton droit sur votre cluster et sélectionnez **Autres actions** > **Déplacer une ressource de cluster principale** > **Sélectionner le nœud** et sélectionnez l’autre nœud du cluster. Déplacez la ressource de cluster principale vers chaque nœud du cluster, puis replacez-la sur le nœud principal. Assurez-vous que vous pouvez déplacer le cluster vers chaque nœud avant d’installer SQL Server.

:::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/test-cluster-failover.png" alt-text="Testez le basculement du cluster en déplaçant la ressource principale sur les autres nœuds":::

## <a name="add-shared-disks-to-cluster"></a>Ajout de disques partagés au cluster

Utilisez le Gestionnaire du cluster de basculement pour ajouter au cluster les disques partagés Azure attachés. 

Pour ajouter des disques à votre cluster, procédez comme suit : 

1. Dans le tableau de bord **Gestionnaire de serveur**, sélectionnez **Outils**, puis sélectionnez **Gestionnaire du cluster de basculement**.
1. Sélectionnez le cluster et développez-le dans le volet de navigation. 
1. Sélectionnez **Stockage**, puis **Disques**. 
1. Cliquez avec le bouton droit sur **Disques**, puis sélectionnez **Ajouter un disque** : 

    :::image type="content" source="./media/failover-cluster-instance-azure-shared-disk-manually-configure/cluster-add-disk.png" alt-text="Ajout du disque.":::

1. Dans la fenêtre **Ajouter des disques à un cluster**, choisissez le disque partagé Azure.  Sélectionnez **OK**.
 
    :::image type="content" source="./media/failover-cluster-instance-azure-shared-disk-manually-configure/cluster-select-shared-disk.png" alt-text="Sélection du disque.":::

1. Une fois ajouté au cluster, le disque partagé apparaît dans la Gestionnaire du cluster de basculement.

    :::image type="content" source="./media/failover-cluster-instance-azure-shared-disk-manually-configure/cluster-shared-disk.png" alt-text="Disque de cluster":::



## <a name="create-sql-server-fci"></a>Créer l’instance de cluster de basculement SQL Server

Après avoir configuré le cluster de basculement et tous les composants du cluster, notamment le stockage, vous pouvez créer l’instance de cluster de basculement SQL Server.

1. Se connecte au premier ordinateur virtuel à l’aide du protocole RDP (Remote Desktop Protocol).

1. Dans le **Gestionnaire du cluster de basculement**, vérifiez que toutes les ressources principales du cluster se trouvent sur la première machine virtuelle. Si nécessaire, déplacez les disques vers cette machine virtuelle.

1. Recherchez le support d’installation. Si la machine virtuelle utilise l’une des images Azure Marketplace, le support se situe sous `C:\SQLServer_<version number>_Full`. 

1. Sélectionnez **Configuration**.

1. Dans le **Centre d’installation SQL Server**, sélectionnez **Installation**.

1. Sélectionnez **Installation d’un nouveau cluster de basculement SQL Server**. Suivez les instructions de l’Assistant pour installer l’instance de cluster de basculement SQL Server.

1. Sur la page **Sélection du disque de cluster**, sélectionnez tous les disques partagés qui étaient attachés à la machine virtuelle. 

    :::image type="content" source="./media/failover-cluster-instance-azure-shared-disk-manually-configure/sql-install-cluster-disk-selection.png" alt-text="Sélection du disque du cluster":::

1. Sur la page **Configuration réseau du cluster**, l’adresse IP à indiquer varie selon que vos machines virtuelles SQL Server ont été déployées sur un seul ou plusieurs sous-réseaux. 

   1. Pour un **environnement à un seul sous-réseau**, indiquez l’adresse IP que vous prévoyez d’ajouter à l’instance [Azure Load Balancer](failover-cluster-instance-vnn-azure-load-balancer-configure.md).
   1. Pour un **environnement à plusieurs sous-réseaux**, indiquez l’adresse IP secondaire dans le sous-réseau de la _première_ machine virtuelle SQL Server que vous aviez désignée comme [adresse IP du nom réseau de l’instance de cluster de basculement](failover-cluster-instance-prepare-vm.md#assign-secondary-ip-addresses) :

   :::image type="content" source="./media/failover-cluster-instance-azure-shared-disk-manually-configure/sql-install-cluster-network-secondary-ip-vm-1.png" alt-text="Indication de l’adresse IP secondaire dans le sous-réseau de la première machine virtuelle SQL Server désignée comme adresse IP du nom réseau de l’instance de cluster de basculement.":::

1. Sur la page **Configuration du moteur de base de données**, assurez-vous que les répertoires de base de données se trouvent sur le ou les disques partagés Azure. 

1. Une fois les instructions de l’Assistant suivies, le programme d’installation installe l’instance FCI SQL Server sur le premier nœud.

1. Une fois l’instance FCI installée sur le premier nœud, connectez-vous au deuxième nœud à l’aide du protocole RDP.

1. Dans le **Centre d’installation SQL Server**, sélectionnez **Installation**.

1. Sélectionnez **Ajouter un nœud à un cluster de basculement SQL Server**. Suivez les instructions de l’Assistant pour installer SQL Server et ajouter le nœud à l’instance FCI.

1. Pour un scénario à plusieurs sous-réseaux : dans **Configuration réseau du cluster**, entrez l’adresse IP secondaire dans le sous-réseau du _deuxième_ sous-réseau de machine virtuelle SQL Server que vous aviez désigné comme [adresse IP du nom réseau de l’instance de cluster de basculement](failover-cluster-instance-prepare-vm.md#assign-secondary-ip-addresses).

    :::image type="content" source="./media/failover-cluster-instance-azure-shared-disk-manually-configure/sql-install-cluster-network-secondary-ip-vm-2.png" alt-text="Indication de l’adresse IP secondaire dans le sous-réseau du deuxième sous-réseau de machine virtuelle SQL Server désigné comme adresse IP du nom réseau de l’instance de cluster de basculement.":::

    Lorsque vous sélectionnez **Suivant** dans **Configuration réseau du cluster**, le programme d’installation affiche une boîte de dialogue indiquant que l’installation de SQL Server a détecté plusieurs sous-réseaux (cf. image d’exemple).  Sélectionnez **Oui** pour confirmer. 

    :::image type="content" source="./media/failover-cluster-instance-azure-shared-disk-manually-configure/sql-install-multi-subnet-confirmation.png" alt-text="Confirmation de sous-réseaux multiples.":::

1. Une fois les instructions de l’Assistant suivies, le programme d’installation ajoute le deuxième nœud FCI SQL Server. 

1. Répétez ces étapes sur les autres machines virtuelles SQL Server qui prendront part à l’instance de cluster de basculement SQL Server. 


>[!NOTE]
> Les images de la galerie de la Place de marché Azure sont fournies avec SQL Server Management Studio. Si vous n’avez pas utilisé une image de la place de marché, [téléchargez SQL Server Management Studio (SSMS)](/sql/ssms/ownload-sql-server-management-studio-ssms).


## <a name="register-with-sql-iaas-extension"></a>S’inscrire à l’extension IaaS SQL 

Pour gérer votre machine virtuelle SQL Server sur le portail, inscrivez-la auprès de l’extension Agent IaaS SQL dans le [mode d’administration léger](sql-agent-extension-manually-register-single-vm.md#lightweight-mode), qui est actuellement le seul pris en charge avec l’instance FCI et SQL Server sur les machines virtuelles Azure. 

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
- Seule l’inscription auprès de l’extension SQL IaaS Agent en [mode d’administration léger](sql-server-iaas-agent-extension-automate-management.md#management-modes) est prise en charge.

## <a name="next-steps"></a>Étapes suivantes

Si les disques partagés Azure ne sont pas la solution de stockage FCI appropriée pour vous, envisagez de créer votre instance FCI à l’aide de [partages de fichiers Premium](failover-cluster-instance-premium-file-share-manually-configure.md) ou d’[espaces de stockage direct](failover-cluster-instance-storage-spaces-direct-manually-configure.md) à la place. 


Pour en savoir plus, consultez :

- [Cluster de basculement Windows Server avec SQL Server sur des machines virtuelles Azure](hadr-windows-server-failover-cluster-overview.md)
- [Instances de cluster de basculement avec SQL Server sur des machines virtuelles Azure](failover-cluster-instance-overview.md)
- [Vue d’ensemble des instances de cluster de basculement](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
- [Paramètres HADR pour SQL Server sur les machines virtuelles Azure](hadr-cluster-best-practices.md)
