---
title: Répliquer des machines virtuelles Azure exécutées dans un groupe de placement de proximité
description: Découvrez comment répliquer des machines virtuelles Azure exécutées dans un groupe de placement de proximité avec Azure Site Recovery.
author: Sharmistha-Rai
manager: gaggupta
ms.topic: how-to
ms.date: 02/11/2021
ms.openlocfilehash: c721014e2f0a61e46ad599bccdc26388aa706a3a
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131444949"
---
# <a name="replicate-virtual-machines-running-in-a-proximity-placement-group-to-another-region"></a>Répliquer des machines virtuelles exécutées dans un groupe de placement de proximité dans une autre région

Cet article explique comment répliquer, basculer et restaurer des machines virtuelles Azure exécutées dans un groupe de placement de proximité vers une région secondaire.

Les [groupes de placement de proximité](../virtual-machines/windows/proximity-placement-groups-portal.md) sont une capacité de regroupement logique dans Machines virtuelles Microsoft Azure. Vous pouvez les utiliser pour réduire la latence du réseau entre machines virtuelles associée à vos applications. 

Lorsque des machines virtuelles sont déployées au sein du même groupe de placement de proximité, elles sont physiquement le plus près possible les unes des autres. Les groupes de placement de proximité sont utiles pour répondre aux exigences des charges de travail sensibles à la latence.

## <a name="disaster-recovery-with-proximity-placement-groups"></a>Récupération d’urgence avec les groupes de placement de proximité

Dans un scénario classique, vous pouvez faire en sorte que vos machines virtuelles s’exécutent dans un groupe de placement de proximité pour éviter la latence du réseau entre les niveaux de votre application. Bien que cette approche puisse fournir une latence réseau optimale pour votre application, vous souhaiterez peut-être protéger ces applications en utilisant Azure Site Recovery pour toute défaillance au niveau régional. 

Site Recovery réplique les données d’une région Azure vers une autre région. Il affiche les machines dans la région de récupération d’urgence (DR) en cas de basculement.

## <a name="considerations"></a>Considérations

- La meilleure solution consiste à basculer et à restaurer les machines virtuelles dans un groupe de placement de proximité. S’il existe une contrainte d’allocation de capacité à cause de laquelle nous ne pouvons pas afficher les machines virtuelles à l’intérieur d’un groupe de placement de proximité, le basculement et la restauration automatique s’exécutent toujours, mais les machines virtuelles sont créées en dehors du groupe de placement de proximité.
- Si un groupe à haute disponibilité est épinglé à un groupe de placement de proximité et que les machines virtuelles du groupe à haute disponibilité ont une contrainte d’allocation pendant le basculement ou la restauration, les machines virtuelles seront créées en dehors du groupe à haute disponibilité et du groupe de placement de proximité.
- Site Recovery pour les groupes de placement de proximité n’est pas pris en charge pour les disques non managés.

> [!NOTE]
> Azure Site Recovery ne prend pas en charge la restauration à partir de disques managés pour les scénarios de migration de Hyper-V vers Azure. La restauration de groupes de placement de proximité d’Azure vers Hyper-V n’est pas prise en charge.

## <a name="set-up-disaster-recovery-for-vms-in-proximity-placement-groups-via-the-azure-portal"></a>Configurer la récupération d’urgence pour les machines virtuelles appartenant à des groupes de placement de proximité via le portail Azure

### <a name="azure-to-azure"></a>Azure vers Azure

Vous pouvez choisir d’activer la réplication pour une machine virtuelle via la page de récupération d’urgence de la machine virtuelle. Sinon, vous pouvez activer la réplication en vous accédant à un coffre précréé, en accédant à la section Site Recovery, puis en activant la réplication. Voyons comment configurer des machines virtuelles Site Recovery dans un groupe de placement de proximité à l’aide des deux approches.

Pour sélectionner un groupe de placement de proximité dans la région DR tout en activant la réplication via la page de DR de la machine virtuelle IaaS (infrastructure as a service) :

1. Accédez à la machine virtuelle. Dans le panneau de gauche, sous **Opérations**, sélectionnez **Récupération d’urgence**.
2. Sous l’onglet **Paramètres de base**, choisissez la région DR vers laquelle vous souhaitez répliquer la machine virtuelle. Accédez à **Paramètres avancés**.
3. Vous pouvez voir le groupe de placement de proximité de votre machine virtuelle, ainsi que l’option permettant de sélectionner un groupe de placement de proximité dans la région DR. Site Recovery vous donne également la possibilité d’utiliser un groupe de placement de proximité qu’il crée pour vous si vous choisissez d’utiliser cette option par défaut. 
 
   Choisissez le groupe de placement de proximité de votre choix. Ensuite, sélectionnez **Examiner + démarrer la réplication**.

   :::image type="content" source="media/how-to-enable-replication-proximity-placement-groups/proximity-placement-group-a2a-1.png" alt-text="Capture d’écran montrant des paramètres avancés pour activer la réplication.":::

Pour sélectionner un groupe de placement de proximité dans la région DR tout en activant la réplication via la page du coffre :

1. Accédez à votre coffre Recovery Services, puis à l’onglet **Site Recovery**.
2. Sélectionnez  **+Activer Site Recovery**. Sélectionnez ensuite **1 : Activer la réplication** sous **Machines virtuelles Azure** (parce que vous souhaitez répliquer une machine virtuelle Azure).
3. Renseignez les champs obligatoires de l’onglet **Source**, puis sélectionnez **Suivant**.
4. Sous l’onglet **Machines virtuelles**, sélectionnez la liste des machines virtuelles pour lesquelles vous souhaitez activer la réplication, puis sélectionnez **Suivant**.
5. Vous pouvez voir l’option permettant de sélectionner un groupe de placement de proximité dans la région DR. Site Recovery vous donne également la possibilité d’utiliser un groupe de placement de proximité qu’il crée pour vous si vous choisissez d’utiliser cette option par défaut. 

   Choisissez le groupe de placement de proximité de votre choix, puis passez à l’activation de la réplication.

   :::image type="content" source="media/how-to-enable-replication-proximity-placement-groups/proximity-placement-group-a2a-2.png" alt-text="Capture d’écran montrant des sélections pour personnaliser les paramètres de la cible.":::

Vous pouvez facilement mettre à jour la sélection d’un groupe de placement de proximité dans la région de récupération d’urgence une fois la réplication activée pour la machine virtuelle :

1. Accédez à la machine virtuelle. Dans le panneau de gauche, sous **Opérations**, sélectionnez **Récupération d’urgence**.
2. Accédez au volet **Calcul**, puis sélectionnez **Modifier**.
3. Vous pouvez voir les options permettant de modifier plusieurs paramètres cibles, y compris le groupe de placement de proximité cible. Choisissez le groupe de placement de proximité dans lequel vous souhaitez que la machine virtuelle bascule, puis sélectionnez **Enregistrer**.

### <a name="vmware-to-azure"></a>VMware vers Azure

Vous pouvez configurer un groupe de placement de proximité pour la machine virtuelle cible après avoir activé la réplication pour la machine virtuelle. Assurez-vous de créer séparément le groupe de placement de proximité dans la région cible en fonction de vos besoins. 

Vous pouvez facilement mettre à jour la sélection d’un groupe de placement de proximité dans la région de récupération d’urgence une fois la réplication activée pour la machine virtuelle :

1. Sélectionnez la machine virtuelle dans le coffre. Dans le panneau de gauche, sous **Opérations**, sélectionnez **Récupération d’urgence**.
2. Accédez au volet **Calcul et réseau**, puis sélectionnez **Modifier**.
3. Vous pouvez voir les options permettant de modifier plusieurs paramètres cibles, y compris le groupe de placement de proximité cible. Choisissez le groupe de placement de proximité dans lequel vous souhaitez que la machine virtuelle bascule, puis sélectionnez **Enregistrer**.

   :::image type="content" source="media/how-to-enable-replication-proximity-placement-groups/proximity-placement-groups-update-v2a.png" alt-text="Capture d’écran illustrant les sélections de calcul et de réseau pour VMware vers Azure.":::

### <a name="hyper-v-to-azure"></a>Hyper-V vers Azure

Vous pouvez configurer un groupe de placement de proximité pour la machine virtuelle cible après avoir activé la réplication pour la machine virtuelle. Assurez-vous de créer séparément le groupe de placement de proximité dans la région cible en fonction de vos besoins. 

Vous pouvez facilement mettre à jour la sélection d’un groupe de placement de proximité dans la région de récupération d’urgence une fois la réplication activée pour la machine virtuelle :

1. Sélectionnez la machine virtuelle dans le coffre. Dans le panneau de gauche, sous **Opérations**, sélectionnez **Récupération d’urgence**.
2. Accédez au volet **Calcul et réseau**, puis sélectionnez **Modifier**.
3. Vous pouvez voir les options permettant de modifier plusieurs paramètres cibles, y compris le groupe de placement de proximité cible. Choisissez le groupe de placement de proximité dans lequel vous souhaitez que la machine virtuelle bascule, puis sélectionnez **Enregistrer**.

   :::image type="content" source="media/how-to-enable-replication-proximity-placement-groups/proximity-placement-groups-update-h2a.png" alt-text="Capture d’écran illustrant les sélections de calcul et de réseau pour Hyper-V vers Azure.":::

## <a name="set-up-disaster-recovery-for-vms-in-proximity-placement-groups-via-powershell"></a>Configurer la récupération d’urgence pour les machines virtuelles appartenant à des groupes de placement de proximité via PowerShell

### <a name="prerequisites"></a>Prérequis 

- Assurez-vous que vous disposez du module Azure PowerShell Az. Si vous devez installer ou mettre à niveau Azure PowerShell, suivez le [guide d’installation et de configuration d’Azure PowerShell](/powershell/azure/install-az-ps).
- La version Azure PowerShell Az minimale doit être 4.1.0. Pour vérifier la version actuelle, utilisez la commande suivante :

    ```
    Get-InstalledModule -Name Az
    ```

> [!NOTE]
> Vérifiez que vous avez l’ID unique du groupe de placement de proximité cible sous la main. La commande que vous utilisez varie selon que vous [créez un groupe de placement de proximité](../virtual-machines/windows/proximity-placement-groups.md#create-a-proximity-placement-group) ou que vous [utilisez un groupe de placement de proximité existant](../virtual-machines/windows/proximity-placement-groups.md#list-proximity-placement-groups).

### <a name="azure-to-azure"></a>Azure vers Azure

1. [Connectez-vous à votre compte et définissez votre abonnement](./azure-to-azure-powershell.md#sign-in-to-your-microsoft-azure-subscription).
2. [Récupérez les détails de la machine virtuelle que vous envisagez de répliquer](./azure-to-azure-powershell.md#get-details-of-the-virtual-machine-to-be-replicated).
3. [Créez votre coffre Recovery Services](./azure-to-azure-powershell.md#create-a-recovery-services-vault) et [définissez le contexte du coffre](./azure-to-azure-powershell.md#set-the-vault-context).
4. Préparez le coffre pour démarrer la machine virtuelle de réplication. Cette étape implique la [création d’un objet Azure Service Fabric](./azure-to-azure-powershell.md#create-a-site-recovery-fabric-object-to-represent-the-primary-source-region) pour les régions principales et de récupération.
5. [Créez un conteneur de protection Site Recovery](./azure-to-azure-powershell.md#create-a-site-recovery-protection-container-in-the-primary-fabric) pour la structure principale et la structure de récupération.
6. [Créez une stratégie de réplication](./azure-to-azure-powershell.md#create-a-replication-policy).
7. [Créez un mappage de conteneurs de protection entre les conteneurs de protection principal et de récupération](./azure-to-azure-powershell.md#create-a-protection-container-mapping-between-the-primary-and-recovery-protection-container) et [créez un mappage de conteneurs de protection pour la restauration](./azure-to-azure-powershell.md#create-a-protection-container-mapping-for-failback-reverse-replication-after-a-failover).
8. [Créez un compte de stockage de cache](./azure-to-azure-powershell.md#create-cache-storage-account-and-target-storage-account).
9. [Créez les mappages de réseau requis](./azure-to-azure-powershell.md#create-network-mappings).
10. Répliquez une machine virtuelle Azure avec des disques managés à l’aide de la cmdlet PowerShell suivante :

    ```azurepowershell
    #Get the resource group that the virtual machine must be created in when it's failed over.
    $RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

    #Specify replication properties for each disk of the VM that will be replicated (create disk replication configuration).
    #Make sure to replace the variable $OSdiskName with the OS disk name.

    #OS Disk
    $OSdisk = Get-AzDisk -DiskName $OSdiskName -ResourceGroupName "A2AdemoRG"
    $OSdiskId = $OSdisk.Id
    $RecoveryOSDiskAccountType = $OSdisk.Sku.Name
    $RecoveryReplicaDiskAccountType = $OSdisk.Sku.Name

    $OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id -DiskId $OSdiskId -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType $RecoveryReplicaDiskAccountType -RecoveryTargetDiskAccountType $RecoveryOSDiskAccountType

    #Make sure to replace the variable $datadiskName with the data disk name.

    #Data disk
    $datadisk = Get-AzDisk -DiskName $datadiskName -ResourceGroupName "A2AdemoRG"
    $datadiskId1 = $datadisk[0].Id
    $RecoveryReplicaDiskAccountType = $datadisk[0].Sku.Name
    $RecoveryTargetDiskAccountType = $datadisk[0].Sku.Name

    $DataDisk1ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id -DiskId $datadiskId1 -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType $RecoveryReplicaDiskAccountType -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

    #Create a list of disk replication configuration objects for the disks of the virtual machine that will be replicated.

    $diskconfigs = @()
    $diskconfigs += $OSDiskReplicationConfig, $DataDisk1ReplicationConfig

    #Start replication by creating a replication protected item. Use a GUID for the name of the replication protected item to ensure uniqueness of the name.

    $TempASRJob = New-AzRecoveryServicesAsrReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryProximityPlacementGroupId $targetPpg.Id
    ```

    Lorsque vous activez la réplication pour plusieurs disques de données, utilisez la cmdlet PowerShell suivante :

    ```azurepowershell
    #Get the resource group that the virtual machine must be created in when it's failed over.
    $RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

    #Specify replication properties for each disk of the VM that will be replicated (create disk replication configuration).
    #Make sure to replace the variable $OSdiskName with the OS disk name.

    #OS Disk
    $OSdisk = Get-AzDisk -DiskName $OSdiskName -ResourceGroupName "A2AdemoRG"
    $OSdiskId = $OSdisk.Id
    $RecoveryOSDiskAccountType = $OSdisk.Sku.Name
    $RecoveryReplicaDiskAccountType = $OSdisk.Sku.Name

    $OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id -DiskId $OSdiskId -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType $RecoveryReplicaDiskAccountType -RecoveryTargetDiskAccountType $RecoveryOSDiskAccountType

    $diskconfigs = @()
    $diskconfigs += $OSDiskReplicationConfig

    #Data disk

    # Add data disks
    Foreach( $disk in $VM.StorageProfile.DataDisks)
    {
        $datadisk = Get-AzDisk -DiskName $datadiskName -ResourceGroupName "A2AdemoRG"
        $dataDiskId1 = $datadisk[0].Id
        $RecoveryReplicaDiskAccountType = $datadisk[0].Sku.Name
        $RecoveryTargetDiskAccountType = $datadisk[0].Sku.Name
        $DataDisk1ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id `
             -DiskId $dataDiskId1 -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType $RecoveryReplicaDiskAccountType `
             -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType
        $diskconfigs += $DataDisk1ReplicationConfig
    }

    #Start replication by creating a replication protected item. Use a GUID for the name of the replication protected item to ensure uniqueness of the name.

    $TempASRJob = New-AzRecoveryServicesAsrReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryProximityPlacementGroupId $targetPpg.Id
    ```

    Lorsque vous activez la réplication de zone à zone avec un groupe de placement de proximité, la commande de démarrage de la réplication est échangée avec la cmdlet PowerShell :

    ```azurepowershell
    $TempASRJob = New-AzRecoveryServicesAsrReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryProximityPlacementGroupId $targetPpg.Id -RecoveryAvailabilityZone "2"
    ```

    Une fois que l’opération de démarrage de la réplication a réussi, les données de la machine virtuelle sont répliquées vers la région de récupération.

    Le processus de réplication démarre en envoyant une copie des disques de réplication de la machine virtuelle dans la région de récupération. Cette phase est appelée phase de *réplication initiale*.

    Une fois cette phase terminée, la réplication passe à la phase de *synchronisation différentielle*. À ce stade, la machine virtuelle est protégée et vous pouvez effectuer une opération de basculement test sur celle-ci. L’état de réplication de l’élément répliqué qui représente la machine virtuelle passe à l’état Protégé après la fin de la réplication initiale.

    Surveillez l’état de réplication et l’intégrité de la réplication de la machine virtuelle en obtenant les détails de l’élément protégé par réplication qui lui correspond :

    ```azurepowershell
    Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $PrimaryProtContainer | Select FriendlyName, ProtectionState, ReplicationHealth
    ```

11. [Effectuez, validez et nettoyez un test de basculement](./azure-to-azure-powershell.md#do-a-test-failover-validate-and-cleanup-test-failover).
12. [Basculez la machine virtuelle](./azure-to-azure-powershell.md#fail-over-to-azure).
13. Reprotégez et restaurez la région source à l’aide de la cmdlet PowerShell suivante :

    ```azurepowershell
    #Create a cache storage account for replication logs in the primary region.
    $WestUSCacheStorageAccount = New-AzStorageAccount -Name "a2acachestoragewestus" -ResourceGroupName "A2AdemoRG" -Location 'West US' -SkuName Standard_LRS -Kind Storage


    #Use the recovery protection container, the new cache storage account in West US, and the source region VM resource group. 
    Update-AzRecoveryServicesAsrProtectionDirection -ReplicationProtectedItem $ReplicationProtectedItem -AzureToAzure -ProtectionContainerMapping $WusToEusPCMapping -LogStorageAccountId $WestUSCacheStorageAccount.Id -RecoveryResourceGroupID $sourceVMResourcegroup.ResourceId -RecoveryProximityPlacementGroupId $vm.ProximityPlacementGroup.Id
    ```

14. [Désactivez la réplication](./azure-to-azure-powershell.md#disable-replication).

### <a name="vmware-to-azure"></a>VMware vers Azure

1. [Préparez vos serveurs VMware locaux](./vmware-azure-tutorial-prepare-on-premises.md) à la récupération d’urgence vers Azure.
2. [Connectez-vous à votre compte et définissez votre abonnement](./vmware-azure-disaster-recovery-powershell.md#log-into-azure).
3. [Configurez un coffre Recovery Services](./vmware-azure-disaster-recovery-powershell.md#set-up-a-recovery-services-vault) et [définissez son contexte](./vmware-azure-disaster-recovery-powershell.md#set-the-vault-context).
4. [Validez l’inscription de votre coffre](./vmware-azure-disaster-recovery-powershell.md#validate-vault-registration).
5. [Créez une stratégie de réplication](./vmware-azure-disaster-recovery-powershell.md#create-a-replication-policy).
6. [Ajoutez un serveur vCenter et découvrez les machines virtuelles](./vmware-azure-disaster-recovery-powershell.md#add-a-vcenter-server-and-discover-vms), puis [créez des comptes de stockage pour la réplication](./vmware-azure-disaster-recovery-powershell.md#create-storage-accounts-for-replication).
7. Répliquez des machines virtuelles VMware et vérifiez les détails à l’aide de la cmdlet PowerShell suivante :

   ```azurepowershell
   #Get the target resource group to be used.
   $ResourceGroup = Get-AzResourceGroup -Name "VMwareToAzureDrPs"

   #Get the target virtual network to be used.
   $RecoveryVnet = Get-AzVirtualNetwork -Name "ASR-vnet" -ResourceGroupName "asrrg"

   #Get the protection container mapping for the replication policy named ReplicationPolicy.
   $PolicyMap = Get-AzRecoveryServicesAsrProtectionContainerMapping -ProtectionContainer $ProtectionContainer | where PolicyFriendlyName -eq "ReplicationPolicy"

   #Get the protectable item that corresponds to the virtual machine CentOSVM1.
   $VM1 = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "CentOSVM1"

   # Enable replication for virtual machine CentOSVM1 by using the Az.RecoveryServices module 2.0.0 onward to replicate to managed disks.
   # The name specified for the replicated item needs to be unique within the protection container. Use a random GUID to ensure uniqueness.
   $Job_EnableReplication1 = New-AzRecoveryServicesAsrReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM1 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -ProcessServer $ProcessServers[1] -Account $AccountHandles[2] -RecoveryResourceGroupId $ResourceGroup.ResourceId -logStorageAccountId $LogStorageAccount.Id -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1" -RecoveryProximityPlacementGroupId $targetPpg.Id
   ```

8. Vérifiez l’état de réplication et l’intégrité de la réplication de la machine virtuelle en utilisant la cmdlet `Get-ASRReplicationProtectedItem` :

   ```azurepowershell
   Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $ProtectionContainer | Select FriendlyName, ProtectionState, ReplicationHealth
   ```

9. [Configurez les paramètres de basculement](./vmware-azure-disaster-recovery-powershell.md#configure-failover-settings).
10. [Exécuter un test de basculement](./vmware-azure-disaster-recovery-powershell.md#run-a-test-failover).
11. [Basculez vers Azure](./vmware-azure-disaster-recovery-powershell.md#fail-over-to-azure).

### <a name="hyper-v-to-azure"></a>Hyper-V vers Azure

1. [Préparez vos serveurs Hyper-V locaux](./hyper-v-prepare-on-premises-tutorial.md) à la récupération d’urgence vers Azure.
2. [Connectez-vous à Azure](./hyper-v-azure-powershell-resource-manager.md#step-1-sign-in-to-your-azure-account).
3. [Configurez votre coffre](./hyper-v-azure-powershell-resource-manager.md#step-2-set-up-the-vault) et [définissez le contexte du coffre Recovery Services](./hyper-v-azure-powershell-resource-manager.md#step-3-set-the-recovery-services-vault-context).
4. [Créez un site Hyper-V](./hyper-v-azure-powershell-resource-manager.md#step-4-create-a-hyper-v-site).
5. [Installez le fournisseur et l’agent](./hyper-v-azure-powershell-resource-manager.md#step-5-install-the-provider-and-agent).
6. [Créez une stratégie de réplication](./hyper-v-azure-powershell-resource-manager.md#step-6-create-a-replication-policy).
7. Activez la réplication en procédant comme suit : 
    
   a. Récupérez l’élément protégeable correspondant à la machine virtuelle que vous souhaitez protéger :

      ```azurepowershell
      $VMFriendlyName = "Fabrikam-app"          #Name of the VM
      $ProtectableItem = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName
      ```
   b. Protéger la machine virtuelle. Si la machine virtuelle que vous protégez compte plusieurs disques, spécifiez le disque du système d’exploitation avec le paramètre `OSDiskName` :
    
      ```azurepowershell
      $OSType = "Windows"          # "Windows" or "Linux"
      $DRjob = New-AzRecoveryServicesAsrReplicationProtectedItem -ProtectableItem $VM -Name $VM.Name -ProtectionContainerMapping $ProtectionContainerMapping -RecoveryAzureStorageAccountId     $StorageAccountID -OSDiskName $OSDiskNameList[$i] -OS $OSType -RecoveryResourceGroupId $ResourceGroupID -RecoveryProximityPlacementGroupId $targetPpg.Id
      ```
   c. Attendez que les machines virtuelles basculent à l’état protégé après la réplication initiale. Ce processus prendra un certain temps compte tenu de certains facteurs, notamment la quantité de données à répliquer et la bande passante en amont disponible vers Azure. 
   
      Lorsque l’état protégé est en place, `State` et `StateDescription` de la tâche sont mis à jour comme suit : 
    
      ```azurepowershell
      $DRjob = Get-AzRecoveryServicesAsrJob -Job $DRjob
      $DRjob | Select-Object -ExpandProperty State

      $DRjob | Select-Object -ExpandProperty StateDescription
      ```
   d. Mettez à jour les propriétés de récupération (telles que la taille du rôle de machine virtuelle) et le réseau Azure auquel attacher la carte réseau de la machine virtuelle après le basculement :

      ```azurepowershell
      $nw1 = Get-AzVirtualNetwork -Name "FailoverNw" -ResourceGroupName "MyRG"

      $VMFriendlyName = "Fabrikam-App"

      $rpi = Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

      $UpdateJob = Set-AzRecoveryServicesAsrReplicationProtectedItem -InputObject $rpi -PrimaryNic $VM.NicDetailsList[0].NicId -RecoveryNetworkId $nw1.Id -RecoveryNicSubnetName $nw1.Subnets[0].Name

      $UpdateJob = Get-AzRecoveryServicesAsrJob -Job $UpdateJob

      $UpdateJob | Select-Object -ExpandProperty state

      Get-AzRecoveryServicesAsrJob -Job $job | Select-Object -ExpandProperty state
      ```
8. [Exécuter un test de basculement](./hyper-v-azure-powershell-resource-manager.md#step-8-run-a-test-failover).


## <a name="next-steps"></a>Étapes suivantes

Pour effectuer une reprotection et une restauration de VMware vers Azure, consultez [Préparer la reprotection et la restauration automatique des machines virtuelles VMWare](./vmware-azure-prepare-failback.md).

Pour basculer d’Hyper-V vers Azure, consultez [Effectuer un basculement depuis le site local vers Azure](./site-recovery-failover.md). Pour effectuer une restauration automatique, consultez [Exécuter une restauration automatique pour les machines virtuelles Hyper-V](./hyper-v-azure-failback.md).

Pour plus d’informations, consultez [Basculement dans Site Recovery](site-recovery-failover.md).
