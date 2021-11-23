---
title: Migrer des machines virtuelles VMware vers Azure (sans agent) - PowerShell
description: Découvrez comment exécuter la migration sans agent de machines virtuelles VMware avec Azure Migrate par le biais de PowerShell.
author: rahulg1190
ms.author: rahugup
manager: bsiva
ms.topic: tutorial
ms.date: 08/20/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 0f8b01e54fbb7173a6d9dfba8a99a9f9ffb1aeb9
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132292886"
---
# <a name="migrate-vmware-vms-to-azure-agentless---powershell"></a>Migrer des machines virtuelles VMware vers Azure (sans agent) - PowerShell

Dans cet article, vous allez apprendre à migrer des machines virtuelles VMware découvertes avec la méthode sans agent à l’aide d’Azure PowerShell pour l’outil [Azure Migrate : Server Migration](migrate-services-overview.md#azure-migrate-server-migration-tool).

Vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> * Récupérer des machines virtuelles VMware découvertes dans un projet Azure Migrate.
> * Démarrer la réplication des machines virtuelles
> * Mettre à jour les propriétés de réplication des machines virtuelles.
> * Superviser la réplication.
> * Exécuter une migration de test pour vérifier que tout fonctionne comme prévu.
> * Exécuter une migration de machine virtuelle complète

> [!NOTE]
> Les tutoriels vous montrent le chemin de déploiement le plus simple pour un scénario donné afin que vous puissiez configurer rapidement une preuve de concept. Ils utilisent les options par défaut quand c’est possible et n’affichent pas tous les paramètres et chemins possibles.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/) avant de commencer.

## <a name="1-prerequisites"></a>1. Prérequis

Avant de commencer ce didacticiel, vous devez :

1. Suivre le [Tutoriel : découvrir les machines virtuelles VMware avec Server Assessment](tutorial-discover-vmware.md) pour préparer Azure et VMware en vue de la migration.
2. Suivre le [Tutoriel : évaluer les machines virtuelles VMware pour les migrer vers des machines virtuelles Azure](./tutorial-assess-vmware-azure-vm.md) avant de les migrer vers Azure.
3. [Installer le module Az PowerShell](/powershell/azure/install-az-ps)

## <a name="2-install-azure-migrate-powershell-module"></a>2. Installer le module PowerShell Azure Migrate

Le module PowerShell Azure Migrate est disponible dans Azure PowerShell (`Az`). Exécutez la commande `Get-InstalledModule -Name Az.Migrate` pour vérifier si le module PowerShell Azure Migrate est installé sur votre ordinateur.  

## <a name="3-sign-in-to-your-microsoft-azure-subscription"></a>3. Se connecter à un abonnement Microsoft Azure

Connectez-vous à votre abonnement Azure avec l’applet de commande [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount).

```azurepowershell-interactive
Connect-AzAccount
```

### <a name="select-your-azure-subscription"></a>Sélectionner votre abonnement Azure

Utilisez l’applet de commande [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription) pour obtenir la liste des abonnements Azure auxquels vous avez accès. Sélectionnez l’abonnement Azure contenant le projet Azure Migrate que vous voulez utiliser à l’aide de l’applet de commande [Set-AzContext](/powershell/module/az.accounts/set-azcontext).

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="4-retrieve-the-azure-migrate-project"></a>4. Récupérer le projet Azure Migrate

Un projet de Azure Migrate est utilisé pour stocker les métadonnées de découverte, d’évaluation et de migration collectées à partir de l’environnement que vous évaluez ou migrez.
Dans un projet, vous pouvez suivre les ressources découvertes, orchestrer les évaluations et effectuer des migrations.

Dans le cadre des conditions préalables, vous devoir avoir déjà créé un projet Azure Migrate. Utilisez l’applet de commande [Get-AzMigrateProject](/powershell/module/az.migrate/get-azmigrateproject) pour récupérer les détails d’un projet Azure Migrate. Vous devez spécifier le nom du projet Azure Migrate (`Name`) et le nom du groupe de ressources de ce projet (`ResourceGroupName`).

```azurepowershell-interactive
# Get resource group of the Azure Migrate project
$ResourceGroup = Get-AzResourceGroup -Name MyResourceGroup
```
```azurepowershell-interactive
# Get details of the Azure Migrate project
$MigrateProject = Get-AzMigrateProject -Name MyMigrateProject -ResourceGroupName $ResourceGroup.ResourceGroupName
```
```azurepowershell-interactive
# View Azure Migrate project details
Write-Output $MigrateProject
```

## <a name="5-retrieve-discovered-vms-in-an-azure-migrate-project"></a>5. Récupérer des machines virtuelles découvertes dans un projet Azure Migrate

Azure Migrate utilise une [appliance Azure Migrate légère](migrate-appliance-architecture.md). Dans le cadre des conditions préalables, vous devez avoir déployé l’appliance Azure Migrate comme machine virtuelle VMware.

Pour récupérer une machine virtuelle VMware spécifique dans un projet Azure Migrate, spécifiez le nom de ce dernier (`ProjectName`), son groupe de ressources (`ResourceGroupName`) et le nom de la machine virtuelle (`DisplayName`).


```azurepowershell-interactive
# Get a specific VMware VM in an Azure Migrate project
$DiscoveredServer = Get-AzMigrateDiscoveredServer -ProjectName $MigrateProject.Name -ResourceGroupName $ResourceGroup.ResourceGroupName -DisplayName MyTestVM | Format-Table DisplayName, Name, Type

# View discovered server details
Write-Output $DiscoveredServer
```

Dans le cadre de ce tutoriel, nous allons migrer cette machine virtuelle.

Vous pouvez également récupérer toutes les machines virtuelles VMware dans un projet Azure Migrate à l’aide des paramètres (`ProjectName`) et (`ResourceGroupName`).

```azurepowershell-interactive
# Get all VMware VMs in an Azure Migrate project
$DiscoveredServers = Get-AzMigrateDiscoveredServer -ProjectName $MigrateProject.Name -ResourceGroupName $ResourceGroup.ResourceGroupName
```

Si vous avez plusieurs appliances dans un projet Azure Migrate, vous pouvez utiliser les paramètres (`ProjectName`), (`ResourceGroupName`) et (`ApplianceName`) pour récupérer toutes les machines virtuelles découvertes à l’aide d’une appliance Azure Migrate spécifique.

```azurepowershell-interactive
# Get all VMware VMs discovered by an Azure Migrate Appliance in an Azure Migrate project
$DiscoveredServers = Get-AzMigrateDiscoveredServer -ProjectName $MigrateProject.Name -ResourceGroupName $ResourceGroup.ResourceGroupName -ApplianceName MyMigrateAppliance

```

## <a name="6-initialize-replication-infrastructure"></a>6. Initialiser l’infrastructure de réplication

[Azure Migrate : Migration de serveur](migrate-services-overview.md#azure-migrate-server-migration-tool) s’appuie sur plusieurs ressources Azure pour la migration de machines virtuelles. L’outil Migration de serveur provisionne les ressources suivantes dans le même groupe de ressources que le projet.

- **Service Bus** : Migration de serveur utilise Service Bus pour envoyer des messages d’orchestration de réplication à l’appliance.
- **Compte de stockage de la passerelle** : Server Migration utilise le compte de stockage de la passerelle pour stocker des informations d’état relatives aux machines virtuelles en cours de réplication.
- **Compte de stockage de journal** : l’appliance Azure Migrate charge les journaux de réplication pour les machines virtuelles vers un compte de stockage de journal. Azure Migrate applique les informations de réplication aux disques managés de réplica.
- **Coffre de clés** : l’appliance Azure Migrate utilise le coffre de clés afin de gérer les chaînes de connexion pour Service Bus, et des clés d’accès pour les comptes de stockage utilisés dans la réplication.

Avant de répliquer la première machine virtuelle dans le projet Azure Migrate, exécutez la commande suivante pour approvisionner l’infrastructure de réplication. Cette commande approvisionne et configure les ressources précitées afin que vous puissiez commencer à migrer vos machines virtuelles VMware.

> [!NOTE]
> Un projet Azure Migrate prend en charge les migrations vers une seule région Azure. Une fois que vous avez exécuté ce script, vous ne pouvez pas changer la région cible vers laquelle vous voulez migrer vos machines virtuelles VMware.
> Vous devez exécuter la commande `Initialize-AzMigrateReplicationInfrastructure` si vous configurez une nouvelle appliance dans votre projet Azure Migrate.

Dans cet article, nous allons initialiser l’infrastructure de réplication afin de pouvoir migrer nos machines virtuelles vers la région `Central US`.

```azurepowershell-interactive
# Initialize replication infrastructure for the current Migrate project
Initialize-AzMigrateReplicationInfrastructure -ResourceGroupName $ResourceGroup.ResourceGroupName -ProjectName $MigrateProject. Name -Scenario agentlessVMware -TargetRegion "CentralUS" 

```

## <a name="7-replicate-vms"></a>7. Répliquer des machines virtuelles

Une fois la découverte et l’initialisation de l’infrastructure de réplication terminées, vous pouvez commencer la réplication de machines virtuelles VMware sur Azure. Vous pouvez exécuter jusqu’à 500 réplications simultanément.

Vous pouvez spécifier les propriétés de réplication comme suit.

**Paramètre** | **Type** | **Description**
--- | --- | ---
 Abonnement et groupe de ressources cibles |  Obligatoire | Spécifiez l’abonnement et le groupe de ressources vers lesquels la machine virtuelle doit être migrée en utilisant le paramètre (`TargetResourceGroupId`).
 Réseau virtuel et sous-réseau cibles | Obligatoire | Spécifiez l’ID du réseau virtuel Azure et le nom du sous-réseau vers lesquels la machine virtuelle doit être migrée en utilisant les paramètres (`TargetNetworkId`) et (`TargetSubnetName`), respectivement.
 ID de machine | Obligatoire | Spécifiez l’ID de l’ordinateur découvert qui doit être répliqué et migré. Utilisez (`InputObject`) pour spécifier l’objet de machine virtuelle détecté pour la réplication.  
 Nom de la machine virtuelle cible | Obligatoire | Spécifiez le nom de la machine virtuelle Azure à créer en utilisant le paramètre (`TargetVMName`). 
 Taille de machine virtuelle cible | Obligatoire | Spécifiez la taille de machine virtuelle Azure à utiliser pour la machine virtuelle de réplication en utilisant le paramètre (`TargetVMSize`). Par exemple, pour migrer une machine virtuelle vers la machine virtuelle D2_v2 dans Azure, spécifiez « Standard_D2_v2 » comme valeur de (`TargetVMSize`). 
 Licence | Obligatoire | Si vous souhaitez utiliser Azure Hybrid Benefit pour vos ordinateurs Windows Server qui sont couverts par des abonnements Software Assurance ou Windows Server actifs, spécifiez **WindowsServer** comme valeur du paramètre (`LicenseType`). Sinon, spécifiez **NoLicenseType** comme valeur. 
 Disque de système d’exploitation | Obligatoire | Spécifiez l’identificateur unique du disque qui contient le chargeur de démarrage et le programme d’installation du système d’exploitation. L’ID de disque à utiliser est la propriété UUID (identificateur unique) pour le disque récupéré à l’aide de la cmdlet [AzMigrateDiscoveredServer](/powershell/module/az.migrate/get-azmigratediscoveredserver).
 Type du disque | Obligatoire | Spécifiez le nom de l’équilibreur de charge à créer. 
 Redondance d’infrastructure | Facultatif | Spécifiez l’option de redondance d’infrastructure comme suit. <br/><br/> - **Zone de disponibilité** pour épingler la machine migrée à une Zone de disponibilité spécifique dans la région. Utilisez cette option pour distribuer les serveurs qui forment une couche Application à plusieurs nœuds entre des Zones de disponibilité. Cette option est disponible uniquement si la région cible sélectionnée pour la migration prend en charge la fonctionnalité Zones de disponibilité. Pour utiliser des zones de disponibilité, spécifiez la valeur de zone de disponibilité souhaitée pour le paramètre (`TargetAvailabilityZone`). <br/> - **Groupe à haute disponibilité** pour placer la machine migrée dans un groupe à haute disponibilité. Pour utiliser cette option, le groupe de ressources cible qui a été sélectionné doit comporter un ou plusieurs groupes à haute disponibilité. Pour utiliser un groupe à haute disponibilité, spécifiez son ID pour le paramètre (`TargetAvailabilitySet`). 
 Compte de stockage de diagnostics de démarrage | Facultatif | Pour utiliser un compte de stockage de diagnostics de démarrage, spécifiez l’ID pour le paramètre (`TargetBootDiagnosticStorageAccount`). <br/> - Le compte de stockage utilisé pour les diagnostics de démarrage doit se trouver dans le même abonnement que celui vers lequel vous migrez vos machines virtuelles. <br/> - Par défaut, aucune valeur n’est définie pour ce paramètre. 
 Étiquettes | Facultatif | Ajoutez des étiquettes à vos machines virtuelles migrées, disques et cartes réseau. <br/>  Utilisez (`Tag`) pour ajouter des étiquettes aux machines virtuelles, disques et cartes réseau. <br/> or <br/> Utilisez (`VMTag`) pour ajouter des étiquettes à vos machines virtuelles migrées.<br/> Utilisez (`DiskTag`) pour ajouter des étiquettes aux disques. <br/> Utilisez (`NicTag`) pour ajouter des étiquettes aux interface réseau. <br/> Par exemple, ajoutez les étiquettes nécessaires à une variable $tags et passez cette variable dans le paramètre obligatoire.  $tags = @{Organization=”Contoso”}



### <a name="replicate-vms-with-all-disks"></a>Répliquer des machines virtuelles avec tous les disques

Dans ce tutoriel, nous allons répliquer tous les disques de la machine virtuelle découverte et définir un nouveau nom pour celle-ci dans Azure. Nous spécifions le premier disque du serveur découvert comme disque de système d’exploitation et nous migrons tous les disques sous forme de disque HDD Standard. Le disque du système d’exploitation est le disque qui contient le chargeur de démarrage et le programme d’installation du système d’exploitation. L’applet de commande retourne une tâche qui peut être suivie pour superviser l’état de l’opération.

```azurepowershell-interactive
# Retrieve the resource group that you want to migrate to
$TargetResourceGroup = Get-AzResourceGroup -Name MyTargetResourceGroup
```

```azurepowershell-interactive
# Retrieve the Azure virtual network and subnet that you want to migrate to
$TargetVirtualNetwork = Get-AzVirtualNetwork -Name MyVirtualNetwork
```
```azurepowershell-interactive
# Start replication for a discovered VM in an Azure Migrate project
$MigrateJob =  New-AzMigrateServerReplication -InputObject $DiscoveredServer -TargetResourceGroupId $TargetResourceGroup.ResourceId -TargetNetworkId $TargetVirtualNetwork.Id -LicenseType NoLicenseType -OSDiskID $DiscoveredServer.Disk[0].Uuid -TargetSubnetName $TargetVirtualNetwork.Subnets[0].Name -DiskType Standard_LRS -TargetVMName MyMigratedTestVM -TargetVMSize Standard_DS2_v2
```
```azurepowershell-interactive
# Track job status to check for completion
while (($MigrateJob.State -eq 'InProgress') -or ($MigrateJob.State -eq 'NotStarted')){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $MigrateJob = Get-AzMigrateJob -InputObject $MigrateJob
}
#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded".
Write-Output $MigrateJob.State
```

### <a name="replicate-vms-with-select-disks"></a>Répliquer des machines virtuelles avec une sélection de disques

Vous pouvez également répliquer de manière sélective les disques de la machine virtuelle découverte en utilisant la cmdlet [New-AzMigrateDiskMapping](/powershell/module/az.migrate/new-azmigratediskmapping) et en fournissant le résultat comme entrée du paramètre (`DiskToInclude`) dans la cmdlet [New-AzMigrateServerReplication](/powershell/module/az.migrate/new-azmigrateserverreplication). Vous pouvez également utiliser la cmdlet [New-AzMigrateDiskMapping](/powershell/module/az.migrate/new-azmigratediskmapping) afin de spécifier différents types de disques cibles pour chaque disque à répliquer.

Spécifiez des valeurs pour les paramètres suivants de la cmdlet [New-AzMigrateDiskMapping](/powershell/module/az.migrate/new-azmigratediskmapping).

- **DiskId** : spécifiez l’identificateur unique du disque à migrer. L’ID de disque à utiliser est la propriété UUID (identificateur unique) pour le disque récupéré à l’aide de la cmdlet [AzMigrateDiscoveredServer](/powershell/module/az.migrate/get-azmigratediscoveredserver).
- **IsOSDisk** : spécifiez « true » si le disque à migrer est le disque du système d’exploitation de la machine virtuelle ; sinon, « false ».
- **DiskType** : spécifiez le type de disque à utiliser dans Azure.

Dans l’exemple suivant, nous allons répliquer uniquement deux disques de la machine virtuelle découverte. Nous allons spécifier le disque du système d’exploitation et utiliser différents types de disques pour chaque disque à répliquer. L’applet de commande retourne une tâche qui peut être suivie pour superviser l’état de l’opération.

```azurepowershell-interactive
# View disk details of the discovered server
Write-Output $DiscoveredServer.Disk
```

```azurepowershell-interactive
# Create a new disk mapping for the disks to be replicated
$DisksToReplicate = @()
$OSDisk = New-AzMigrateDiskMapping -DiskID $DiscoveredServer.Disk[0].Uuid -DiskType StandardSSD_LRS -IsOSDisk true
$DataDisk = New-AzMigrateDiskMapping -DiskID $DiscoveredServer.Disk[1].Uuid -DiskType Premium_LRS -IsOSDisk false

$DisksToReplicate += $OSDisk
$DisksToReplicate += $DataDisk
```

```azurepowershell-interactive
# Retrieve the resource group that you want to migrate to
$TargetResourceGroup = Get-AzResourceGroup -Name MyTargetResourceGroup
```

```azurepowershell-interactive
# Retrieve the Azure virtual network and subnet that you want to migrate to
$TargetVirtualNetwork = Get-AzVirtualNetwork -Name MyVirtualNetwork
```

```azurepowershell-interactive
# Start replication for the VM
$MigrateJob =  New-AzMigrateServerReplication -InputObject $DiscoveredServer -TargetResourceGroupId $TargetResourceGroup.ResourceId -TargetNetworkId $TargetVirtualNetwork.Id -LicenseType NoLicenseType -DiskToInclude $DisksToReplicate -TargetSubnetName $TargetVirtualNetwork.Subnets[0].Name -TargetVMName MyMigratedTestVM -TargetVMSize Standard_DS2_v2
```

```azurepowershell-interactive
# Track job status to check for completion
while (($MigrateJob.State -eq 'InProgress') -or ($MigrateJob.State -eq 'NotStarted')){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $MigrateJob = Get-AzMigrateJob -InputObject $MigrateJob
}
# Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded".
Write-Output $MigrateJob.State
```

## <a name="8-monitor-replication"></a>8. Superviser la réplication

La réplication se déroule comme suit :

- Une fois le travail Démarrer la réplication terminé, les machines commencent leur réplication initiale vers Azure.
- Lors de la réplication initiale, une capture instantanée de machine virtuelle est créée. Les données de disque de la capture instantanée sont répliquées sur des disques managés de réplica dans Azure.
- Au terme de la réplication initiale, la réplication différentielle commence. Les modifications incrémentielles apportées aux disques locaux sont répliquées régulièrement sur les disques de réplica dans Azure.

Suivez l’état de la réplication à l’aide de l’applet de commande [Get-AzMigrateServerReplication](/powershell/module/az.migrate/get-azmigrateserverreplication).



```azurepowershell-interactive
# List replicating VMs and filter the result for selecting a replicating VM. This cmdlet will not return all properties of the replicating VM.
$ReplicatingServer = Get-AzMigrateServerReplication -ProjectName $MigrateProject.Name -ResourceGroupName $ResourceGroup.ResourceGroupName -MachineName MyTestVM
```
```azurepowershell-interactive
# Retrieve all properties of a replicating VM 
$ReplicatingServer = Get-AzMigrateServerReplication -TargetObjectID $ReplicatingServer.Id
```

Vous pouvez suivre les propriétés d’**état de la migration** et de **description de l’état de la migration** dans la sortie.

- Pour la réplication initiale, les valeurs des propriétés d’**état de la migration** et de **description de l’état de la migration** sont `InitialSeedingInProgress` et `Initial replication` respectivement.
- Pendant une réplication différentielle, les valeurs des propriétés d’**état de la migration** et de **description de l’état de la migration** sont `Replicating` et `Ready to migrate` respectivement.
- Une fois la migration effectuée, les valeurs des propriétés d’**état de la migration** et de **description de l’état de la migration** sont `Migration succeeded` et `Migrated` respectivement.

```Output
AllowedOperation            : {DisableMigration, TestMigrate, Migrate}
CurrentJobId                : /Subscriptions/xxx/resourceGroups/xxx/providers/Micr
                              osoft.RecoveryServices/vaults/xxx/replicationJobs/None
CurrentJobName              : None
CurrentJobStartTime         : 1/1/1753 1:01:01 AM
EventCorrelationId          : 9d435c55-4660-41a5-a8ed-dd74213d85fa
Health                      : Normal
HealthError                 : {}
Id                          : /Subscriptions/xxx/resourceGroups/xxx/providers/Micr
                              osoft.RecoveryServices/vaults/xxx/replicationFabrics/xxx/replicationProtectionContainers/xxx/
                              replicationMigrationItems/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_5009e941-3e40-
                              39b2-1e14-f90584522703
LastTestMigrationStatus     :
LastTestMigrationTime       :
Location                    :
MachineName                 : MyTestVM
MigrationState              : InitialSeedingInProgress
MigrationStateDescription   : Initial replication
Name                        : 10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_5009e941-3e40-39b2-1e14-f90584522703
PolicyFriendlyName          : xxx
PolicyId                    : /Subscriptions/xxx/resourceGroups/xxx/providers/Micr
                              osoft.RecoveryServices/vaults/xxx/replicationPolicies/xxx
ProviderSpecificDetail      : Microsoft.Azure.PowerShell.Cmdlets.Migrate.Models.Api20180110.VMwareCbtMigrationDetails
TestMigrateState            : None
TestMigrateStateDescription : None
Type                        : Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems
```

Pour plus d’informations sur la progression d’une réplication, exécutez l’applet de commande suivante.

```azurepowershell-interactive
Write-Output $replicatingserver.ProviderSpecificDetail
```

Vous pouvez suivre l’avancement de la réplication initiale à l’aide des propriétés de **pourcentage d’avancement de l’amorçage initial** dans la sortie.

```Output
    "DataMoverRunAsAccountId": "/subscriptions/xxx/resourceGroups/xxx/providers/Microsoft.OffAzure/VMwareSites/xxx/runasaccounts/xxx",
    "FirmwareType":  "BIOS",
    "InitialSeedingProgressPercentage": 20,
    "InstanceType":  "VMwareCbt",
    "LastRecoveryPointReceived":  "\/Date(1601733591427)\/",
    "LicenseType":  "NoLicenseType",
    "MigrationProgressPercentage":  null,
    "MigrationRecoveryPointId":  null,
    "OSType":  "Windows",
    "PerformAutoResync":  "true",
```

La réplication se déroule comme suit :

- Une fois le travail Démarrer la réplication terminé, les machines commencent leur réplication initiale vers Azure.
- Lors de la réplication initiale, une capture instantanée de machine virtuelle est créée. Les données de disque de la capture instantanée sont répliquées sur des disques managés de réplica dans Azure.
- Au terme de la réplication initiale, la réplication différentielle commence. Les modifications incrémentielles apportées aux disques locaux sont répliquées régulièrement sur les disques de réplica dans Azure.

## <a name="9-retrieve-the-status-of-a-job"></a>9. Récupérer l’état d’une tâche

Vous pouvez superviser l’état d’une tâche à l’aide de l’applet de commande [Get-AzMigrateJob](/powershell/module/az.migrate/get-azmigratejob).

```azurepowershell-interactive
# Retrieve the updated status for a job
$job = Get-AzMigrateJob -InputObject $job
```

## <a name="10-update-properties-of-a-replicating-vm"></a>10. Mettre à jour les propriétés d’une machine virtuelle de réplication

L’outil [Azure Migrate : Migration de serveur](migrate-services-overview.md#azure-migrate-server-migration-tool) vous permet de changer des propriétés cibles, comme le nom, la taille, le groupe de ressources, la configuration de la carte réseau, etc., pour une machine virtuelle de réplication.

Les propriétés suivantes peuvent être mises à jour pour une machine virtuelle.


**Paramètre** | **Type** | **Description**
--- | --- | ---
Nom de la machine virtuelle | Facultatif | Spécifiez le nom de la machine virtuelle Azure à créer en utilisant le paramètre [`TargetVMName`]. 
Taille de la machine virtuelle | Facultatif | Spécifiez la taille de machine virtuelle Azure à utiliser pour la machine virtuelle de réplication en utilisant le paramètre [`TargetVMSize`]. Par exemple, pour migrer une machine virtuelle vers la machine virtuelle D2_v2 dans Azure, spécifiez `Standard_D2_v2` comme valeur de [`TargetVMSize`].
Réseau virtuel | Facultatif | Spécifiez l’ID du réseau virtuel Azure vers lequel la machine virtuelle doit être migrée en utilisant le paramètre [`TargetNetworkId`]. 
Groupe de ressources | Facultatif | La configuration de la carte réseau peut être spécifiée avec l’applet de commande [New-AzMigrateNicMapping](/powershell/module/az.migrate/new-azmigratenicmapping). Une entrée pour le paramètre [`NicToUpdate`] dans la cmdlet [Set-AzMigrateServerReplication](/powershell/module/az.migrate/set-azmigrateserverreplication) est ensuite passée à l’objet. <br/><br/> - **Changer l’allocation d’adresses IP** : afin de spécifier une adresse IP statique pour une carte réseau, fournissez l’adresse IPv4 à utiliser comme adresse IP statique pour la machine virtuelle avec le paramètre [`TargetNicIP`]. Pour attribuer dynamiquement une adresse IP pour une carte réseau, indiquez `auto` comme valeur du paramètre **TargetNicIP**. <br/> - Utilisez les valeurs `Primary`, `Secondary` ou `DoNotCreate` pour le paramètre [`TargetNicSelectionType`] afin de spécifier si la carte réseau doit être principale ou secondaire, ou si elle ne doit pas être créée sur la machine virtuelle migrée. Une seule carte réseau peut être spécifiée comme carte réseau principale pour la machine virtuelle. <br/> - Pour créer une carte réseau principale, vous devez également spécifier les autres cartes réseau qui doivent être secondaires ou qui ne doivent pas être créées sur la machine virtuelle migrée. <br/> - Pour changer le sous-réseau de la carte réseau, spécifiez le nom du sous-réseau avec le paramètre [`TargetNicSubnet`].
Interface réseau | Facultatif | Spécifiez le nom de la machine virtuelle Azure à créer en utilisant le paramètre [`TargetVMName`]. 
Zone de disponibilité | Facultatif | Pour utiliser des zones de disponibilité, spécifiez la valeur de zone de disponibilité souhaitée pour le paramètre [`TargetAvailabilityZone`]. 
Groupe à haute disponibilité | Facultatif | Pour utiliser un groupe à haute disponibilité, spécifiez son ID pour le paramètre [`TargetAvailabilitySet`]. 
Étiquettes | Facultatif | Pour mettre à jour les étiquettes, utilisez les paramètres [`UpdateTag`] ou [`UpdateVMTag`], [`UpdateDiskTag`], [`UpdateNicTag`], et spécifiez le type de l’opération de mise à jour des étiquettes [`UpdateTagOperation`] ou [`UpdateVMTagOperation`], [`UpdateDiskTagOperation`], [`UpdateNicTagOperation`].   L’opération de mise à jour des étiquettes peut prendre les valeurs suivantes : merge (fusionner), delete (supprimer) et replace (remplacer). <br/> Utilisez [`UpdateTag`] pour mettre à jour toutes les étiquettes des machines virtuelles, des disques et des cartes réseau. <br/> Utilisez [`UpdateVMTag`] pour mettre à jour les étiquettes des machines virtuelles. <br/> Utilisez [`UpdateDiskTag`] pour mettre à jour les étiquettes des disques. <br/> Utilisez [`UpdateNicTag`] pour mettre à jour les étiquettes des cartes réseau. <br/> Utilisez [`UpdateTagOperation`] pour mettre à jour l’opération effectuée sur toutes les étiquettes des machines virtuelles, des disques et des cartes réseau. <br/>  Utilisez [`UpdateVMTagOperation`] pour mettre à jour les étiquettes des machines virtuelles. <br/> Utilisez [`UpdateDiskTagOperation`] pour mettre à jour les étiquettes des disques. <br/> Utilisez [`UpdateNicTagOperation`] pour mettre à jour les étiquettes des cartes réseau. <br/> <br/> L’option *replace* remplace l’ensemble complet des étiquettes existantes par un nouvel ensemble. <br/> L’option *merge* permet d’ajouter des étiquettes avec de nouveaux noms et de mettre à jour les valeurs des étiquettes avec des noms existants. <br/> L’option *delete* permet de supprimer des étiquettes de manière sélective en fonction de noms ou de paires nom/valeur spécifiés. 
Disque(s) | Facultatif | Pour le disque de système d’exploitation : <br/> Mettez à jour le nom du disque de système d’exploitation en utilisant le paramètre [`TargetDiskName`].  <br/><br/> Pour mettre à jour plusieurs disques : <br/>  Utilisez [Set-AzMigrateDiskMapping](/powershell/module/az.migrate/set-azmigratediskmapping) pour définir les noms de disque sur une variable *$DiskMapping*, et utilisez le paramètre [`DiskToUpdate`] auquel vous passez la variable. <br/> <br/> **Remarque :** L’ID de disque à utiliser dans [Set-AzMigrateDiskMapping](/powershell/module/az.migrate/set-azmigratediskmapping) est la propriété UUID (identificateur unique) du disque que vous obtenez à l’aide de l’applet de commande  [Get-AzMigrateDiscoveredServer](/powershell/module/az.migrate/get-azmigratediscoveredserver). 
Nom de la ou des cartes réseau | Facultatif | Utilisez [New-AzMigrateNicMapping](/powershell/module/az.migrate/new-azmigratenicmapping) pour définir les noms de carte réseau sur une variable *$NICMapping*, et utilisez le paramètre [`NICToUpdate`] auquel vous passez la variable.

La cmdlet [Get-AzMigrateServerReplication](/powershell/module/az.migrate/get-azmigrateserverreplication) retourne un travail qui peut être suivi pour superviser l’état de l’opération.

```azurepowershell-interactive
# List replicating VMs and filter the result for selecting a replicating VM. This cmdlet will not return all properties of the replicating VM.
$ReplicatingServer = Get-AzMigrateServerReplication -ProjectName $MigrateProject.Name -ResourceGroupName $ResourceGroup.ResourceGroupName -MachineName MyTestVM
```

```azurepowershell-interactive
# Retrieve all properties of a replicating VM 
$ReplicatingServer = Get-AzMigrateServerReplication -TargetObjectID $ReplicatingServer.Id

# View NIC details of the replicating server
Write-Output $ReplicatingServer.ProviderSpecificDetail.VMNic
```

Dans l’exemple suivant, nous allons mettre à jour la configuration de la carte réseau en définissant la première carte réseau comme carte principale et en lui affectant une adresse IP statique. Nous allons ignorer la deuxième carte réseau pour la migration, mettre à jour le nom et la taille de la machine virtuelle cible, et personnaliser les noms des cartes réseau.

```azurepowershell-interactive
# Specify the NIC properties to be updated for a replicating VM.
$NicMapping = @()
$NicMapping1 = New-AzMigrateNicMapping -NicId $ReplicatingServer.ProviderSpecificDetail.VMNic[0].NicId -TargetNicIP ###.###.###.### -TargetNicSelectionType Primary TargetNicName "ContosoNic_1"
$NicMapping2 = New-AzMigrateNicMapping -NicId $ReplicatingServer.ProviderSpecificDetail.VMNic[1].NicId -TargetNicSelectionType DoNotCreate - TargetNicName "ContosoNic_2"

$NicMapping += $NicMapping1
$NicMapping += $NicMapping2
```
```azurepowershell-interactive
# Update the name, size and NIC configuration of a replicating server
$UpdateJob = Set-AzMigrateServerReplication -InputObject $ReplicatingServer -TargetVMSize Standard_DS13_v2 -TargetVMName MyMigratedVM -NicToUpdate $NicMapping
```

Dans l’exemple suivant, nous personnalisons le nom des disques.

```azurepowershell-interactive
# Customize the Disk names for a replicating VM
$OSDisk = Set-AzMigrateDiskMapping -DiskID "6000C294-1217-dec3-bc18-81f117220424" -DiskName "ContosoDisk_1" 
$DataDisk1= Set-AzMigrateDiskMapping -DiskID "6000C292-79b9-bbdc-fb8a-f1fa8dbeff84" -DiskName "ContosoDisk_2" 
$DiskMapping = $OSDisk, $DataDisk1 
```

```azurepowershell-interactive
# Update the disk names for a replicating server
$UpdateJob = Set-AzMigrateServerReplication InputObject $ReplicatingServer DiskToUpdate $DiskMapping 
 ```

Dans l’exemple suivant, nous ajoutons des étiquettes aux machines virtuelles de réplication.

```azurepowershell-interactive
# Update all tags across virtual machines, disks, and NICs.
Set-azmigrateserverreplication UpdateTag $UpdateTag UpdateTagOperation Merge/Replace/Delete

# Update virtual machines tags
Set-azmigrateserverreplication UpdateVMTag $UpdateVMTag UpdateVMTagOperation Merge/Replace/Delete 
```
Utilisez l’exemple suivant pour suivre l’état du travail.

```azurepowershell-interactive
# Track job status to check for completion
while (($UpdateJob.State -eq 'InProgress') -or ($UpdateJob.State -eq 'NotStarted')){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $UpdateJob = Get-AzMigrateJob -InputObject $UpdateJob
}
# Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded".
Write-Output $UpdateJob.State
```

## <a name="11-run-a-test-migration"></a>11. Exécuter un test de migration

Quand la réplication différentielle commence, vous pouvez exécuter une migration de test pour les machines virtuelles avant d’exécuter une migration complète vers Azure. Nous vous recommandons vivement d’effectuer une migration de test au moins une fois pour chaque machine avant sa migration. L’applet de commande retourne une tâche qui peut être suivie pour superviser l’état de l’opération.

- L’exécution d’une migration de test permet de vérifier que la migration fonctionnera comme prévu. Une migration de test n’a pas d’impact sur la machine locale, qui reste opérationnelle et qui continue la réplication.
- Une migration de test simule la migration en créant une machine virtuelle Azure à l’aide de données répliquées (il s’agit en général d’une migration vers un réseau virtuel hors production dans votre abonnement Azure).
- Vous pouvez utiliser la machine virtuelle Azure de test répliquée pour valider la migration, effectuer des tests d’applications et résoudre les éventuels problèmes avant la migration complète.

Sélectionnez le réseau virtuel Azure à utiliser pour le test en spécifiant l’ID du réseau virtuel à l’aide du paramètre [`TestNetworkID`].

```azurepowershell-interactive
# Retrieve the Azure virtual network created for testing
$TestVirtualNetwork = Get-AzVirtualNetwork -Name MyTestVirtualNetwork
```
```azurepowershell-interactive
# Start test migration for a replicating server
$TestMigrationJob = Start-AzMigrateTestMigration -InputObject $ReplicatingServer -TestNetworkID $TestVirtualNetwork.Id
```
```azurepowershell-interactive
# Track job status to check for completion
while (($TestMigrationJob.State -eq 'InProgress') -or ($TestMigrationJob.State -eq 'NotStarted')){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $TestMigrationJob = Get-AzMigrateJob -InputObject $TestMigrationJob
}
# Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded".
Write-Output $TestMigrationJob.State
```

Une fois le test terminé, nettoyez la migration de test à l’aide de l’applet de commande [Start-AzMigrateTestMigrationCleanup](/powershell/module/az.migrate/start-azmigratetestmigrationcleanup). L’applet de commande retourne une tâche qui peut être suivie pour superviser l’état de l’opération.

```azurepowershell-interactive
# Clean-up test migration for a replicating server
$CleanupTestMigrationJob = Start-AzMigrateTestMigrationCleanup -InputObject $ReplicatingServer
```
```azurepowershell-interactive
# Track job status to check for completion
while (($CleanupTestMigrationJob.State -eq "InProgress") -or ($CleanupTestMigrationJob.State -eq "NotStarted")){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $CleanupTestMigrationJob = Get-AzMigrateJob -InputObject $CleanupTestMigrationJob
}
# Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded".
Write-Output $CleanupTestMigrationJob.State
```

## <a name="12-migrate-vms"></a>12. Migrer des machines virtuelles

Après avoir vérifié que la migration de test fonctionne comme prévu, vous pouvez migrer le serveur de réplication à l’aide de l’applet de commande suivante. L’applet de commande retourne une tâche qui peut être suivie pour superviser l’état de l’opération.

Si vous ne voulez pas arrêter le serveur source, n’utilisez pas le paramètre [`TurnOffSourceServer`].

```azurepowershell-interactive
# Start migration for a replicating server and turn off source server as part of migration
$MigrateJob = Start-AzMigrateServerMigration -InputObject $ReplicatingServer -TurnOffSourceServer
```
```azurepowershell-interactive
# Track job status to check for completion
while (($MigrateJob.State -eq 'InProgress') -or ($MigrateJob.State -eq 'NotStarted')){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $MigrateJob = Get-AzMigrateJob -InputObject $MigrateJob
}
#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded".
Write-Output $MigrateJob.State
```

## <a name="13-complete-the-migration"></a>13. Effectuer la migration

1. Une fois la migration terminée, arrêtez la réplication de la machine locale et nettoyez les informations d’état de réplication de la machine virtuelle à l’aide de l’applet de commande suivante. L’applet de commande retourne une tâche qui peut être suivie pour superviser l’état de l’opération.

   ```azurepowershell-interactive
   # Stop replication for a migrated server
   $StopReplicationJob = Remove-AzMigrateServerReplication -InputObject $ReplicatingServer
   ```
   ```azurepowershell-interactive
   # Track job status to check for completion
   while (($StopReplicationJob.State -eq 'InProgress') -or ($StopReplicationJob.State -eq 'NotStarted')){
           #If the job hasn't completed, sleep for 10 seconds before checking the job status again
           sleep 10;
           $StopReplicationJob = Get-AzMigrateJob -InputObject $StopReplicationJob
   }
   # Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded".
   Write-Output $StopReplicationJob.State
   ```

1. Installez l’agent [Linux](../virtual-machines/extensions/agent-linux.md) sur les machines migrées si la machine exécute le système d’exploitation Linux. Nous installons automatiquement l’agent de machine virtuelle pour les machines virtuelles Windows pendant la migration.
1. Effectuez les éventuels ajustements post-migration de l’application, comme la mise à jour des chaînes de connexion de base de données et les configurations du serveur web.
1. Effectuez les tests finaux de réception de l’application et de la migration sur l’application migrée qui s’exécute maintenant dans Azure.
1. Réduisez le trafic vers l’instance de machine virtuelle Azure migrée.
1. Supprimez les machines virtuelles locales de votre inventaire des machines virtuelles locales.
1. Supprimez les machines virtuelles locales des sauvegardes locales.
1. Mettez à jour la documentation interne en y mentionnant le nouvel emplacement et la nouvelle adresse IP des machines virtuelles Azure.

## <a name="14-post-migration-best-practices"></a>14. Bonnes pratiques après la migration

- Pour une meilleure résilience :
    - Sécurisez les données en sauvegardant les machines virtuelles Azure avec le service Sauvegarde Azure. [Plus d’informations](../backup/quick-backup-vm-portal.md)
    - Conservez les charges de travail en cours d’exécution et disponibles en continu en répliquant des machines virtuelles Azure vers une région secondaire avec Site Recovery. [Plus d’informations](../site-recovery/azure-to-azure-tutorial-enable-replication.md)
- Pour renforcer la sécurité :
    - Verrouillez et limitez l’accès du trafic entrant grâce à l’[administration juste-à-temps de Microsoft Defender pour le cloud](../security-center/security-center-just-in-time.md).
    - Limitez le trafic réseau vers les points de terminaison de gestion avec des [groupes de sécurité réseau](../virtual-network/network-security-groups-overview.md).
    - Déployez [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md) pour sécuriser les disques, et protégez les données contre le vol et les accès non autorisés.
    - Pour en savoir plus sur la [sécurisation des ressources IaaS](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/), consultez le site [Microsoft Defender pour le cloud](https://azure.microsoft.com/services/security-center/).
- Pour la surveillance et la gestion :
-  Envisagez de déployer [Azure Cost Management](../cost-management-billing/cost-management-billing-overview.md) pour surveiller l’utilisation et les coûts des ressources.
