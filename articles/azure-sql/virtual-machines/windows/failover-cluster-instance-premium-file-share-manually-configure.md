---
title: Créer un ICF avec partage de fichiers premium
description: Cet article explique comment créer une instance de cluster de basculement(ICF) SQL Server sur des machines virtuelles Azure.
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
ms.openlocfilehash: 8d9e94ae12600223c6f54fb75cbc6c9648ecf7f9
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132156975"
---
# <a name="create-an-fci-with-a-premium-file-share-sql-server-on-azure-vms"></a>Créer un ICF avec un partage de fichiers premium (SQL Server sur les machines virtuelles Azure)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!TIP]
> Évitez le recours à une instance Azure Load Balancer et à un nom de réseau distribué pour votre instance de cluster de basculement en créant vos machines virtuelles SQL Server dans [plusieurs sous-réseaux](failover-cluster-instance-prepare-vm.md#subnets) au sein du même réseau virtuel Azure.


Cet article explique comment créer une instance de cluster de basculement (ICF) avec SQL Server sur des machines virtuelles (VM) Azure à l’aide d’un [partage de fichiers premium](../../../storage/files/storage-how-to-create-file-share.md).

Les partages de fichiers premium s’appuient sur des disques SSD. Ils fournissent en permanence des partages de fichiers à faible latence entièrement pris en charge pour une utilisation avec des instances de cluster de basculement pour SQL Server 2012 ou version ultérieure sur Windows Server 2012 ou version ultérieure. Les partages de fichiers Premium vous offrent une plus grande flexibilité, ce qui vous permet de redimensionner et de mettre à l’échelle un partage de fichiers sans temps d’arrêt.

Pour plus d’informations, consultez une présentation de [ICF avec SQL Server sur les machines virtuelles Azure](failover-cluster-instance-overview.md) et les [meilleures pratiques de cluster](hadr-cluster-best-practices.md). 

> [!NOTE]
> Il est maintenant possible d’effectuer un lift-and-shift de votre solution d’instance de cluster de basculement vers SQL Server sur des machines virtuelles Azure à l’aide d’Azure Migrate. Pour en savoir plus, consultez [Migrer une instance de cluster de basculement](../../migration-guides/virtual-machines/sql-server-failover-cluster-instance-to-sql-on-azure-vm.md). 

## <a name="prerequisites"></a>Prérequis

Avant de suivre les instructions décrites dans cet article, vous devez déjà disposer des éléments suivants :

- Un abonnement Azure.
- Un compte qui dispose des autorisations nécessaires pour créer des objets sur les machines virtuelles Azure et dans Active Directory.
- [Deux ou plusieurs machines virtuelles Windows Azure préparées](failover-cluster-instance-prepare-vm.md) dans un [groupe à haute disponibilité](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set) ou différentes [zones de disponibilité](../../../virtual-machines/windows/create-portal-availability-zone.md#confirm-zone-for-managed-disk-and-ip-address).
- Un [partage de fichiers Premium](../../../storage/files/storage-how-to-create-file-share.md) à utiliser comme lecteur en cluster, en fonction du quota de stockage de votre base de données pour vos fichiers de données.
- La dernière version de [PowerShell](/powershell/azure/install-az-ps). 

## <a name="mount-premium-file-share"></a>Monter le partage de fichiers Premium

Pour monter votre partage de fichiers premium, procédez comme suit : 

1. Connectez-vous au [portail Azure](https://portal.azure.com). et accédez à votre compte de stockage.
1. Accédez à **Partages de fichiers** sous **Stockage de données**, puis sélectionnez le partage de fichiers Premium que vous souhaitez utiliser pour votre stockage SQL.
1. Sélectionnez **Connecter** pour afficher la chaîne de connexion de votre partage de fichiers.
1. Dans la liste déroulante, sélectionnez la lettre de lecteur que vous souhaitez utiliser, choisissez **Clé de compte de stockage** comme méthode d’authentification, puis copiez le bloc de code dans un éditeur de texte comme le Bloc-notes.

   :::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/premium-file-storage-commands.png" alt-text="Copie de la commande PowerShell sur le portail de connexion au partage de fichiers":::

1. Utilisez le protocole bureau distant (RDP) pour établir une connexion à la machine virtuelle SQL Server à l’aide du **compte que votre instance de cluster de basculement SQL Server utilisera pour le compte de service**.
1. Ouvrez une console de commande PowerShell d’administration.
1. Exécutez la commande que vous avez copiée dans votre éditeur de texte sur le portail de partage de fichiers.
1. Accédez au partage avec l’Explorateur de fichiers ou la boîte de dialogue **Exécuter** (Windows+R sur votre clavier). Utilisez le chemin d’accès réseau `\\storageaccountname.file.core.windows.net\filesharename`. Par exemple : `\\sqlvmstorageaccount.file.core.windows.net\sqlpremiumfileshare`
1. Créez au moins un dossier sur le partage de fichiers auquel vous venez de vous connecter afin d’y placer vos fichiers de données SQL.
1. Répétez ces étapes sur chaque machine virtuelle SQL Server qui fera partie du cluster.

  > [!IMPORTANT]
  > Envisagez d’utiliser un partage de fichiers distinct pour les fichiers de sauvegarde pour préserver les opérations d’entrée/sortie par seconde (IOPS) et d’espace de ce partage pour les fichiers de données et les fichiers journaux. Vous pouvez utiliser un partage de fichiers Premium ou Standard pour les fichiers de sauvegarde.

## <a name="create-windows-failover-cluster"></a>Créer un cluster de basculement Windows

La procédure de création de votre cluster de basculement Windows Server varient selon que vous avez déployé vos machines virtuelles SQL Server sur un seul ou plusieurs sous-réseaux. Suivez les étapes indiquées dans le tutoriel du scénario à [plusieurs sous-réseaux](availability-group-manually-configure-tutorial-multi-subnet.md#add-failover-cluster-feature) ou celui du scénario à [un seul sous-réseau](availability-group-manually-configure-tutorial-single-subnet.md#create-the-cluster). Ces tutoriels servent à créer un groupe de disponibilité, mais la procédure de création du cluster est la même. 


## <a name="configure-quorum"></a>Configurer un quorum

Le témoin cloud est la solution de quorum recommandée pour ce type de configuration de cluster pour SQL Server sur machines virtuelles Azure.  

Si vous avez un nombre pair de votes dans le cluster, configurez la [Solution de quorum](hadr-cluster-quorum-configure-how-to.md) qui correspond le mieux aux besoins de votre entreprise. Pour plus d’informations, consultez [Quorum avec les machines virtuelles SQL Server](hadr-windows-server-failover-cluster-overview.md#quorum). 

## <a name="validate-cluster"></a>Valider le cluster

Validez le cluster sur l’une des machines virtuelles avec l’interface utilisateur du Gestionnaire du cluster de basculement ou PowerShell.

Pour valider le cluster à l’aide de l’interface utilisateur, procédez comme suit sur l’une des machines virtuelles :

1. Sous **Gestionnaire de serveur**, sélectionnez **Outils**, puis **Gestionnaire du cluster de basculement**.
1. Sous **Gestionnaire du cluster de basculement**, sélectionnez **Action**, puis **Valider la configuration**.
1. Sélectionnez **Suivant**.
1. Sous **Sélectionner des serveurs ou un cluster**, entrez le nom des deux machines virtuelles.
1. Sous **Options de test**, sélectionnez **Exécuter uniquement les tests que je sélectionne**. 
1. Sélectionnez **Suivant**.
1. Sous **Sélection des tests**, sélectionnez tous les tests à l’exception de **Stockage** et **Espaces de stockage direct**, comme illustré ici :

   :::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/cluster-validation.png" alt-text="Sélectionner les tests de validation du cluster":::

1. Sélectionnez **Suivant**.
1. Sous **Confirmation**, sélectionnez **Suivant**. L’assistant **Valider une configuration** exécute les tests de validation.


Pour valider le cluster avec PowerShell, exécutez le script suivant à partir d’une session PowerShell d’administrateur sur l’une des machines virtuelles :

```powershell
Test-Cluster –Node ("<node1>","<node2>") –Include "Inventory", "Network", "System Configuration"
```



## <a name="test-cluster-failover"></a>Tester le basculement de cluster

Testez le basculement de votre cluster. Dans le **Gestionnaire du cluster de basculement**, cliquez avec le bouton droit sur votre cluster et sélectionnez **Autres actions** > **Déplacer une ressource de cluster principale** > **Sélectionner le nœud** et sélectionnez l’autre nœud du cluster. Déplacez la ressource de cluster principale vers chaque nœud du cluster, puis replacez-la sur le nœud principal. Si vous parvenez à déplacer le cluster vers chaque nœud, vous êtes prêt à installer SQL Server.  

:::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/test-cluster-failover.png" alt-text="Testez le basculement du cluster en déplaçant la ressource principale sur les autres nœuds":::


## <a name="create-sql-server-fci"></a>Créer l’instance de cluster de basculement SQL Server

Après avoir configuré le cluster de basculement, vous pouvez créer l’instance de cluster de basculement SQL Server.

1. Connectez-vous à la première machine virtuelle avec RDP.

1. Dans le **Gestionnaire du cluster de basculement**, vérifiez que toutes les ressources principales du cluster se trouvent sur la première machine virtuelle. Si nécessaire, déplacez toutes les ressources vers cette machine virtuelle.

1. Recherchez le support d’installation. Si la machine virtuelle utilise l’une des images Azure Marketplace, le support se situe sous `C:\SQLServer_<version number>_Full`. 

1. Sélectionnez **Configuration**.

1. Dans le **Centre d’installation SQL Server**, sélectionnez **Installation**.

1. Sélectionnez **Installation du nouveau cluster de basculement SQL Server**, puis suivez les instructions dans l’assistant pour installer l’ICF SQL Server.

1. Sur la page **Configuration réseau du cluster**, l’adresse IP à indiquer varie selon que vos machines virtuelles SQL Server ont été déployées sur un seul ou plusieurs sous-réseaux. 

   1. Pour un **environnement à un seul sous-réseau**, indiquez l’adresse IP que vous prévoyez d’ajouter à l’instance [Azure Load Balancer](failover-cluster-instance-vnn-azure-load-balancer-configure.md)
   1. Pour un **environnement à plusieurs sous-réseaux**, indiquez l’adresse IP secondaire dans le sous-réseau de la _première_ machine virtuelle SQL Server que vous aviez désignée comme [adresse IP du nom réseau de l’instance de cluster de basculement](failover-cluster-instance-prepare-vm.md#assign-secondary-ip-addresses) :

   :::image type="content" source="./media/failover-cluster-instance-azure-shared-disk-manually-configure/sql-install-cluster-network-secondary-ip-vm-1.png" alt-text="Fournissez l’adresse IP secondaire dans le sous-réseau de la première machine virtuelle SQL Server que vous avez précédemment désignée comme adresse IP du nom réseau de l’instance de cluster de basculement":::

1. Dans une **Configuration de moteur de base de données**, les répertoires de données doivent se trouver sur le partage de fichiers premium. Entrez le chemin d’accès complet du partage, sous la forme suivante : `\\storageaccountname.file.core.windows.net\filesharename\foldername`. Un avertissement s’affiche, vous indiquant que vous avez spécifié un serveur de fichiers comme répertoire de données. Cet avertissement est attendu. Vérifiez que le compte d’utilisateur avec lequel vous avez accédé par RPD à la machine virtuelle lorsque vous avez conservé le partage de fichiers est celui que le service SQL Server utilise pour éviter d’éventuelles défaillances.

   :::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/use-file-share-as-data-directories.png" alt-text="Utilisez le partage de fichiers en tant que répertoires de données SQL":::

1. Une fois que vous avez terminé les étapes de l’assistant, le programme d’installation installe une instance de cluster de basculement SQL Server sur le premier nœud.

1. Une fois l’instance de cluster de basculement installée sur le premier nœud, connectez-vous au deuxième nœud à l’aide du protocole RDP.

1. Dans le **Centre d’installation SQL Server**, sélectionnez **Installation**.

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

Pour gérer votre machine virtuelle SQL Server à partir du portail, inscrivez-la auprès de l’extension SQL IaaS Agent en [mode d’administration légère](sql-agent-extension-manually-register-single-vm.md#lightweight-mode), seul mode actuellement pris en charge avec FCI et SQL Server sur machines virtuelles Azure. 

Inscrire la machine virtuelle SQL Server en mode léger avec PowerShell (le type de licence peut être `PAYG` ou `AHUB`) :

```powershell-interactive
# Get the existing compute VM
$vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
         
# Register SQL VM with 'Lightweight' SQL IaaS agent
New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
   -LicenseType ???? -SqlManagementType LightWeight  
```

## <a name="configure-connectivity"></a>Configurer la connectivité

Si vous avez déployé vos machines virtuelles SQL Server dans plusieurs sous-réseaux, ignorez cette étape. Si vous avez déployé vos machines virtuelles SQL Server sur un seul sous-réseau, vous devez configurer un composant supplémentaire pour acheminer le trafic vers votre instance FCI. Vous pouvez configurer un nom de réseau virtuel avec une instance Azure Load Balancer ou un nom de réseau distribué pour une instance de cluster de basculement. [Passez en revue les différences entre les deux](hadr-windows-server-failover-cluster-overview.md#virtual-network-name-vnn), puis déployez un [nom de réseau distribué](failover-cluster-instance-distributed-network-name-dnn-configure.md) ou un [nom de réseau virtuel et Azure Load Balancer](failover-cluster-instance-vnn-azure-load-balancer-configure.md) pour votre instance de cluster de basculement.  

## <a name="limitations"></a>Limites

- Le MSDTC (Microsoft Distributed Transaction Coordinator) n’est pas pris en charge sur Windows Server 2016 et antérieur. 
- Filestream n’est pas pris en charge pour un cluster de basculement avec un partage de fichiers Premium. Pour utiliser le flux de fichier, déployez votre cluster en utilisant des [Espaces de stockage direct](failover-cluster-instance-storage-spaces-direct-manually-configure.md) ou les [Disques partagés Azure](failover-cluster-instance-azure-shared-disks-manually-configure.md) à la place.
- Seule l’inscription auprès de l’extension SQL IaaS Agent en [mode d’administration léger](sql-server-iaas-agent-extension-automate-management.md#management-modes) est prise en charge. 
- Les captures instantanées de base de données ne sont actuellement pas prises en charge avec [Azure Files en raison des limitations liées aux fichiers partiellement alloués](/rest/api/storageservices/features-not-supported-by-the-azure-file-service).
- Étant donné que les instantanés de base de données ne sont pas pris en charge, CHECKDB pour les bases de données utilisateur revient à CHECKDB WITH TABLOCK. TABLOCK limite les vérifications effectuées ; DBCC CHECKCATALOG n'est pas exécuté sur la base de données et les données Service Broker ne sont pas validées.
- CHECKDB sur la base de données MASTER et MSDB n’est pas pris en charge. 
- Les bases de données utilisant la fonctionnalité OLTP en mémoire ne sont pas prises en charge sur une instance de cluster de basculement déployée avec un partage de fichiers Premium. Si votre entreprise a besoin d’OLTP en mémoire, envisagez de déployer votre FCI avec des [disques partagés Azure](failover-cluster-instance-azure-shared-disks-manually-configure.md) ou des [espaces de stockage direct](failover-cluster-instance-storage-spaces-direct-manually-configure.md).

## <a name="next-steps"></a>Étapes suivantes

Si les partages de fichiers Premium ne sont pas la solution de stockage ICF appropriée, envisagez de créer votre ICF à l’aide des [Disques partagés Azure](failover-cluster-instance-azure-shared-disks-manually-configure.md) ou [Espaces de stockage direct](failover-cluster-instance-storage-spaces-direct-manually-configure.md) à la place. 

Pour en savoir plus, consultez :

- [Cluster de basculement Windows Server avec SQL Server sur des machines virtuelles Azure](hadr-windows-server-failover-cluster-overview.md)
- [Instances de cluster de basculement avec SQL Server sur des machines virtuelles Azure](failover-cluster-instance-overview.md)
- [Vue d’ensemble des instances de cluster de basculement](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
- [Paramètres HADR pour SQL Server sur les machines virtuelles Azure](hadr-cluster-best-practices.md)

