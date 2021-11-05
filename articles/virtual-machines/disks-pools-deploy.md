---
title: Déployer un pool de disques Azure (préversion)
description: Apprenez à déployer un pool de disques Azure.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: rogarana
ms.subservice: disks
ms.custom: ignite-fall-2021
ms.openlocfilehash: 7230bf83f5ca203aa40cb043b3ea02d983ba7a4a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131022185"
---
# <a name="deploy-an-azure-disk-pool-preview"></a>Déployer un pool de disques Azure (préversion)

Cet article explique comment déployer et configurer un pool de disques Azure (préversion). Avant de déployer un pool de disques, consultez les articles consacrés à la [conception](disks-pools.md) et à la [planification](disks-pools-planning.md).

Pour qu'un pool de disques fonctionne correctement, procédez comme suit :
- Inscrivez votre abonnement pour la préversion.
- Déléguez un sous-réseau à votre pool de disques.
- Attribuez au fournisseur de ressources du pool de disques des autorisations de contrôle d'accès en fonction du rôle (RBAC) pour la gestion de vos disques.
- Créez le pool de disques.
    - Ajoutez des disques à votre pool de disques.


## <a name="prerequisites"></a>Configuration requise

Pour déployer correctement un pool de disques, vous devez disposer des éléments suivants :

- Un ensemble de disques managés à ajouter à un pool de disques
- Un réseau virtuel avec un sous-réseau dédié déployé pour votre pool de disques

Si vous comptez utiliser le module Azure PowerShell, installez la [version 6.1.0 ou une version ultérieure](/powershell/module/az.diskpool/?view=azps-6.1.0&preserve-view=true).

Si vous comptez utiliser Azure CLI, installez [la dernière version](/cli/azure/disk-pool).

## <a name="register-your-subscription-for-the-preview"></a>Inscrire votre abonnement pour la préversion

Inscrivez votre abonnement auprès du fournisseur **Microsoft.StoragePool** pour pouvoir créer et utiliser des pools de disques.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
1. Dans le menu du portail Azure, recherchez et sélectionnez **Abonnements**.
1. Sélectionnez l'abonnement à utiliser pour les pools de disques.
1. Dans le menu de gauche, sous **Paramètres**, sélectionnez **Fournisseurs de ressources**.
1. Recherchez le fournisseur de ressources **Microsoft.StoragePool** et sélectionnez **Inscrire**.

Une fois votre abonnement inscrit, vous pouvez déployer un pool de disques.

## <a name="delegate-subnet-permission"></a>Autorisation de déléguer un sous-réseau

Pour que votre pool de disques fonctionne avec vos ordinateurs clients, vous devez déléguer un sous-réseau à votre pool de disques Azure. Lorsque de la création d'un pool de disques, vous devez spécifier un réseau virtuel et le sous-réseau délégué. Vous pouvez créer un nouveau sous-réseau, ou utiliser un sous-réseau existant et le déléguer au fournisseur de ressources **Microsoft.StoragePool/diskPools**.

1. Accédez au volet Réseaux virtuels du portail Azure et sélectionnez le réseau virtuel à utiliser pour le pool de disques.
1. Sélectionnez **Sous-réseaux** dans le volet des réseaux virtuels et sélectionnez le bouton **+Sous-réseau**.
1. Créez un sous-réseau en renseignant les champs obligatoires suivants dans le volet **Ajouter un sous-réseau** :      - Délégation de sous-réseau : sélectionnez Microsoft.StoragePool

Pour plus d'informations sur la délégation de sous-réseau, consultez [Ajouter ou supprimer une délégation de sous-réseau](../virtual-network/manage-subnet-delegation.md).

## <a name="assign-storagepool-resource-provider-permissions"></a>Attribuer des autorisations de fournisseur de ressources StoragePool

Pour qu'un disque puisse être utilisé dans un pool de disques, il doit remplir les conditions suivantes :

- Le fournisseur de ressources **StoragePool** doit disposer d'un rôle RBAC avec autorisations de **lecture** et d'**écriture** pour chacun des disques managés du pool de disques.
- Il doit s’agir d’un disque SSD premium, d’un disque SSD standard ou d’un disque ultra dans la même zone de disponibilité que le pool de disques.
    - Les disques Ultra doivent avoir une taille de secteur de disque de 512 octets.
- Les pools de disques ne peuvent pas être configurés pour contenir à la fois des disques SSD premium et standards. Un pool de disques configuré pour les disques ultra-ne peut contenir que des disques ultra. De même, un pool de disques configuré pour les disques SSD premium ou standards ne peut contenir que des disques SSD standards et premium.
- Il doit s'agir d'un disque partagé dont la valeur maxShares est supérieure ou égale à deux.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
1. Recherchez et sélectionnez le groupe de ressources qui contient les disques ou chaque disque proprement dit.
1. Sélectionnez **Contrôle d’accès (IAM)** .
1. Sélectionnez **Ajouter une attribution de rôle (préversion)** et choisissez **Opérateur de pool de disques** dans la liste des rôles.

    Si vous préférez, vous pouvez créer votre propre rôle personnalisé à la place. Un rôle personnalisé associé à des pools de disques doit disposer des autorisations RBAC suivantes pour fonctionner : **Microsoft.Compute/disks/write** et **Microsoft.Compute/disks/read**.

1. Pour **Attribuer l’accès à**, sélectionnez **Utilisateur, groupe ou principal du service**.
1. Sélectionnez **+ Sélectionner des membres**, puis recherchez le **fournisseur de ressources StoragePool**, sélectionnez-le et enregistrez.

## <a name="create-a-disk-pool"></a>Créer un pool de disques

Pour des performances optimales, déployez le pool de disques dans la même zone de disponibilité que vos clients. Si vous déployez un pool de disques pour un cloud Azure VMware Solution et que vous avez besoin d'aide pour identifier la zone de disponibilité, complétez ce [formulaire](https://aka.ms/DiskPoolCollocate).

# <a name="portal"></a>[Portail](#tab/azure-portal)

1. Recherchez et sélectionnez **Pool de disques**.
1. Sélectionnez **+ Ajouter** pour créer un pool de disques.
1. Fournissez les informations demandées, et sélectionnez la même région et la même zone de disponibilité que pour les clients qui utiliseront le pool de disques.
1. Sélectionnez le sous-réseau qui a été délégué au fournisseur de ressources **StoragePool**, et son réseau virtuel associé.
1. Sélectionnez **Suivant** pour ajouter des disques à votre pool de disques.

    :::image type="content" source="media/disks-pools-deploy/create-a-disk-pool.png" alt-text="Capture d'écran du volet de base pour la création d'un pool de disques.":::

### <a name="add-disks"></a>Ajoute des disques

#### <a name="prerequisites"></a>Configuration requise

Pour ajouter un disque, celui-ci doit remplir les conditions suivantes :

- Il doit s’agir d’un disque SSD premium, d’un disque SSD standard ou d’un disque ultra dans la même zone de disponibilité que le pool de disques.
    - Actuellement, vous pouvez uniquement ajouter des disques SSD premium et standards dans le portail. Les disques Ultra doivent être ajoutés à l'aide du module Azure PowerShell ou d'Azure CLI.
    - Les disques Ultra doivent avoir une taille de secteur de disque de 512 octets.
- Il doit s'agir d'un disque partagé dont la valeur maxShares est supérieure ou égale à deux.
- Les pools de disques ne peuvent pas être configurés pour contenir à la fois des disques SSD premium et standards. Un pool de disques configuré pour les disques ultra-ne peut contenir que des disques ultra. De même, un pool de disques configuré pour les disques SSD premium ou standards ne peut contenir que des disques SSD standards et premium.
- Vous devez accorder des autorisations RBAC au fournisseur de ressources du pool de disques pour gérer le disque que vous prévoyez d'ajouter.

Si votre disque répond à ces exigences, vous pouvez l'ajouter à un pool de disques en sélectionnant **+Ajouter un disque** dans le volet des pools de disques.

:::image type="content" source="media/disks-pools-deploy/create-a-disk-pool-disks-blade.png" alt-text="Capture d'écran du volet des disques lors de la création d'un pool de disques dans lequel Ajouter un disque est mis en surbrillance.":::

### <a name="enable-iscsi"></a>Activer iSCSI

1. Sélectionnez le volet **iSCSI**.
1. Sélectionnez **Activer iSCSI**.
1. Entrez le nom de la cible iSCSI ; le nom qualifié de la cible iSCSI sera généré en fonction de ce nom.
    - Par défaut, le mode ACL est défini sur **Dynamique**. Pour utiliser votre pool de disques en tant que solution de stockage pour Azure VMware Solution, le mode ACL doit être défini sur **Dynamique**.
1. Sélectionnez **Revoir + créer**.

    :::image type="content" source="media/disks-pools-deploy/create-a-disk-pool-iscsi-blade.png" alt-text="Capture d'écran du volet iSCSI lors de la création d'un pool de disques.":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Le script effectue ce qui suit :
- Il installe le module nécessaire à la création et à l'utilisation de pools de disques.
- Il crée un disque et lui attribue des autorisations RBAC. Si vous l'avez déjà fait, vous pouvez commenter ces sections du script.
- Il crée un pool de disques et y ajoute le disque.
- Il crée et active une cible iSCSI.

Remplacez les variables de ce script par vos propres variables avant d'exécuter le script. Vous devez également le modifier pour utiliser un disque dur existant si vous avez complété le formulaire Disque Ultra.

```azurepowershell
# Install the required module for Disk Pool
Install-Module -Name Az.DiskPool -RequiredVersion 0.3.0 -Repository PSGallery

# Sign in to the Azure account and setup the variables
$subscriptionID = "<yourSubID>"
Set-AzContext -Subscription $subscriptionID
$resourceGroupName= "<yourResourceGroupName>"
$location = "<desiredRegion>"
$diskName = "<desiredDiskName>"
$availabilityZone = "<desiredAvailabilityZone>"
$subnetId='<yourSubnetID>'
$diskPoolName = "<desiredDiskPoolName>"
$iscsiTargetName = "<desirediSCSITargetName>" # This will be used to generate the iSCSI target IQN name
$lunName = "<desiredLunName>"

# You can skip this step if you have already created the disk and assigned proper RBAC permission to the resource group the disk is deployed to
$diskconfig = New-AzDiskConfig -Location $location -DiskSizeGB 1024 -AccountType Premium_LRS -CreateOption Empty -zone $availabilityZone -MaxSharesCount 2
$disk = New-AzDisk -ResourceGroupName $resourceGroupName -DiskName $diskName -Disk $diskconfig
$diskId = $disk.Id
$scopeDef = "/subscriptions/" + $subscriptionId + "/resourceGroups/" + $resourceGroupName
$rpId = (Get-AzADServicePrincipal -SearchString "StoragePool Resource Provider").id

New-AzRoleAssignment -ObjectId $rpId -RoleDefinitionName "Virtual Machine Contributor" -Scope $scopeDef

# Create a Disk Pool
# If you want to create a disk pool configured for ultra disks, add -AdditionalCapability "DiskPool.Disk.Sku.UltraSSD_LRS" to the command
New-AzDiskPool -Name $diskPoolName -ResourceGroupName $resourceGroupName -Location $location -SubnetId $subnetId -AvailabilityZone $availabilityZone -SkuName Standard_S1
$diskpool = Get-AzDiskPool -ResourceGroupName $resourceGroupName -Name $DiskPoolName

# Add disks to the Disk Pool
Update-AzDiskPool -ResourceGroupName $resourceGroupName -Name $diskPoolName -DiskId $diskId
$lun = New-AzDiskPoolIscsiLunObject -ManagedDiskAzureResourceId $diskId -Name $lunName

# Create an iSCSI Target and expose the disks as iSCSI LUNs
New-AzDiskPoolIscsiTarget -DiskPoolName $diskPoolName -Name $iscsiTargetName -ResourceGroupName $resourceGroupName -Lun $lun -AclMode Dynamic

Write-Output "Print details of the iSCSI target exposed on Disk Pool"

Get-AzDiskPoolIscsiTarget -name $iscsiTargetName -DiskPoolName $diskPoolName -ResourceGroupName $resourceGroupName | fl
```


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Le script effectue ce qui suit :
- Il installe l'extension nécessaire à la création et à l'utilisation de pools de disques.
- Il crée un disque et lui attribue des autorisations RBAC. Si vous l'avez déjà fait, vous pouvez commenter ces sections du script.
- Il crée un pool de disques et y ajoute le disque.
- Il crée et active une cible iSCSI.

Remplacez les variables de ce script par vos propres variables avant d'exécuter le script. Vous devez également le modifier pour utiliser un disque dur existant si vous avez complété le formulaire Disque Ultra.

```azurecli
# Add disk pool CLI extension
az extension add -n diskpool

#az extension add -s https://azcliprod.blob.core.windows.net/cli-extensions/diskpool-0.2.0-py3-none-any.whl

#Select subscription
az account set --subscription "<yourSubscription>"

##Initialize input parameters
resourceGroupName='<yourRGName>'
location='<desiredRegion>'
zone=<desiredZone>
subnetId='<yourSubnetID>'
diskName='<desiredDiskName>'
diskPoolName='<desiredDiskPoolName>'
targetName='<desirediSCSITargetName>'
lunName='<desiredLunName>'

#You can skip this step if you have already created the disk and assigned permission in the prerequisite step. Below is an example for premium disks.
az disk create --name $diskName --resource-group $resourceGroupName --zone $zone --location $location --sku Premium_LRS --max-shares 2 --size-gb 1024

#You can deploy all your disks into one resource group and assign StoragePool Resource Provider permission to the group
storagePoolObjectId=$(az ad sp list --filter "displayName eq 'StoragePool Resource Provider'" --query "[0].objectId" -o json)
storagePoolObjectId="${storagePoolObjectId%"}"
storagePoolObjectId="${storagePoolObjectId#"}"

az role assignment create --assignee-object-id $storagePoolObjectId --role "Virtual Machine Contributor" --resource-group $resourceGroupName

#Create a disk pool
#To create a disk pool configured for ultra disks, add --additional-capabilities "DiskPool.Disk.Sku.UltraSSD_LRS" to your command
az disk-pool create --name $diskPoolName \
--resource-group $resourceGroupName \
--location $location \
--availability-zones $zone \
--subnet-id $subnetId \
--sku name="Standard_S1" \

#Initialize an iSCSI target. You can have 1 iSCSI target per disk pool
az disk-pool iscsi-target create --name $targetName \
--disk-pool-name $diskPoolName \
--resource-group $resourceGroupName \
--acl-mode Dynamic

#Add the disk to disk pool
diskId=$(az disk show --name $diskName --resource-group $resourceGroupName --query "id" -o json)
diskId="${diskId%"}"
diskId="${diskId#"}"

az disk-pool update --name $diskPoolName --resource-group $resourceGroupName --disks $diskId

#Expose disks added in the Disk Pool as iSCSI Lun 
az disk-pool iscsi-target update --name $targetName \
 --disk-pool-name $diskPoolName \
 --resource-group $resourceGroupName \
 --luns name=$lunName managed-disk-azure-resource-id=$diskId
```
---

## <a name="next-steps"></a>Étapes suivantes

- Si vous rencontrez des problèmes lors du déploiement d'un pool de disques, consultez [Résoudre les problèmes liés aux pools de disques Azure (préversion)](disks-pools-troubleshoot.md).
- [Attacher des pools de disques à des hôtes Azure VMware Solution (préversion)](../azure-vmware/attach-disk-pools-to-azure-vmware-solution-hosts.md).
- [Gérer un pool de disques](disks-pools-manage.md).
