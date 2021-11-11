---
title: Utiliser Azure Image Builder avec une galerie pour machines virtuelles Windows
description: Créer des versions d'images Azure Shared Gallery à l'aide d'Azure Image Builder et d'Azure PowerShell.
author: kof-f
ms.author: kofiforson
ms.reviewer: cynthn
ms.date: 03/02/2021
ms.topic: how-to
ms.service: virtual-machines
ms.subervice: image-builder
ms.colletion: windows
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 224a24de18060568ef8b5ba86f9da8354fab8ddd
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131462876"
---
# <a name="create-a-windows-image-and-distribute-it-to-an-azure-compute-gallery"></a>Créer une image Windows et la distribuer à une galerie Azure Compute Gallery 

**S’applique à :** :heavy_check_mark : Machines virtuelles Windows 

Cet article explique comment utiliser Azure Image Builder et Azure PowerShell pour créer une version d'image dans une galerie [Azure Compute Gallery](../shared-image-galleries.md) (anciennement Shared Image Gallery) avant de distribuer l'image globalement. Vous pouvez également effectuer cette opération à l'aide d'[Azure CLI](../linux/image-builder-gallery.md).

Nous utiliserons un modèle .json.pour configurer l’image. Le fichier .json utilisé est le suivant : [armTemplateWinSIG.json](https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/armTemplateWinSIG.json). Nous allons télécharger et modifier une version locale du modèle. Cet article part donc du principe qu'une session PowerShell locale est utilisée.

Pour distribuer l’image à une galerie Azure Compute Gallery, le modèle utilise [sharedImage](../linux/image-builder-json.md#distribute-sharedimage) en tant que valeur de la section `distribute` du modèle.

Azure Image Builder exécute automatiquement sysprep pour généraliser l'image. Il s'agit d'une commande générique sysprep, que vous pouvez [écraser](../linux/image-builder-troubleshoot.md#vms-created-from-aib-images-do-not-create-successfully) si nécessaire. 

Tenez compte du nombre de personnalisations que vous effectuez. Vous pouvez exécuter la commande Sysprep un nombre limité de fois sur une seule image de Windows. Après avoir atteint la limite Sysprep, vous devez recréer votre image Windows. Pour plus d'informations, consultez [Nombre d'exécutions maximum de Sysprep](/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation#limits-on-how-many-times-you-can-run-sysprep). 


## <a name="register-the-features"></a>Inscrire les fonctionnalités
Pour utiliser Azure Image Builder, vous devez inscrire cette fonctionnalité.

Vérifiez les inscriptions de votre fournisseur. Assurez-vous que chacun renvoie `Registered`.

```powershell
Get-AzResourceProvider -ProviderNamespace Microsoft.VirtualMachineImages | Format-table -Property ResourceTypes,RegistrationState
Get-AzResourceProvider -ProviderNamespace Microsoft.Storage | Format-table -Property ResourceTypes,RegistrationState 
Get-AzResourceProvider -ProviderNamespace Microsoft.Compute | Format-table -Property ResourceTypes,RegistrationState
Get-AzResourceProvider -ProviderNamespace Microsoft.KeyVault | Format-table -Property ResourceTypes,RegistrationState
Get-AzResourceProvider -ProviderNamespace Microsoft.Network | Format-table -Property ResourceTypes,RegistrationState
```

S'ils ne renvoient pas `Registered`, utilisez ce qui suit pour inscrire les fournisseurs :

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.VirtualMachineImages
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
Register-AzResourceProvider -ProviderNamespace Microsoft.KeyVault
Register-AzResourceProvider -ProviderNamespace Microsoft.Network
```

Installer des modules PowerShell :
```powerShell
'Az.ImageBuilder', 'Az.ManagedServiceIdentity' | ForEach-Object {Install-Module -Name $_ -AllowPrerelease}
```

## <a name="create-variables"></a>Créer des variables

Nous allons utiliser certains éléments d’information à plusieurs reprises, donc nous allons créer des variables pour les stocker. Remplacez la valeur des variables, comme `username` et `vmpassword`, par vos propres informations.

```powershell
# Get existing context
$currentAzContext = Get-AzContext

# Get your current subscription ID. 
$subscriptionID=$currentAzContext.Subscription.Id

# Destination image resource group
$imageResourceGroup="aibwinsig"

# Location
$location="westus"

# Image distribution metadata reference name
$runOutputName="aibCustWinManImg02ro"

# Image template name
$imageTemplateName="helloImageTemplateWin02ps"

# Distribution properties object name (runOutput).
# This gives you the properties of the managed image on completion.
$runOutputName="winclientR01"

# Create a resource group for Image Template and Azure Compute Gallery
New-AzResourceGroup `
   -Name $imageResourceGroup `
   -Location $location
```


## <a name="create-a-user-assigned-identity-and-set-permissions-on-the-resource-group"></a>Créer une identité affectée par l’utilisateur et définir des autorisations sur le groupe de ressources
Image Builder utilise l’[identité de l’utilisateur](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md) fournie pour injecter l’image dans la galerie Azure Compute Gallery. Dans cet exemple, vous allez créer une définition de rôle Azure qui dispose des actions granulaires pour distribuer l’image à la galerie SIG. La définition de rôle sera ensuite attribuée à l’identité managée affectée par l’utilisateur.

```powershell
# setup role def names, these need to be unique
$timeInt=$(get-date -UFormat "%s")
$imageRoleDefName="Azure Image Builder Image Def"+$timeInt
$identityName="aibIdentity"+$timeInt

## Add AZ PS module to support AzUserAssignedIdentity
Install-Module -Name Az.ManagedServiceIdentity

# create identity
New-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName

$identityNameResourceId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).Id
$identityNamePrincipalId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).PrincipalId
```


### <a name="assign-permissions-for-identity-to-distribute-images"></a>Attribuer des autorisations d’identité pour distribuer des images

Cette commande permet de télécharger un modèle de définition de rôle Azure et de mettre à jour le modèle avec les paramètres spécifiés précédemment.

```powershell
$aibRoleImageCreationUrl="https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json"
$aibRoleImageCreationPath = "aibRoleImageCreation.json"

# download config
Invoke-WebRequest -Uri $aibRoleImageCreationUrl -OutFile $aibRoleImageCreationPath -UseBasicParsing

((Get-Content -path $aibRoleImageCreationPath -Raw) -replace '<subscriptionID>',$subscriptionID) | Set-Content -Path $aibRoleImageCreationPath
((Get-Content -path $aibRoleImageCreationPath -Raw) -replace '<rgName>', $imageResourceGroup) | Set-Content -Path $aibRoleImageCreationPath
((Get-Content -path $aibRoleImageCreationPath -Raw) -replace 'Azure Image Builder Service Image Creation Role', $imageRoleDefName) | Set-Content -Path $aibRoleImageCreationPath

# create role definition
New-AzRoleDefinition -InputFile  ./aibRoleImageCreation.json

# grant role definition to image builder service principal
New-AzRoleAssignment -ObjectId $identityNamePrincipalId -RoleDefinitionName $imageRoleDefName -Scope "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"

### NOTE: If you see this error: 'New-AzRoleDefinition: Role definition limit exceeded. No more role definitions can be created.' See this article to resolve:
https://docs.microsoft.com/azure/role-based-access-control/troubleshooting
```


## <a name="create-the-azure-compute-gallery"></a>Créer la galerie Azure Compute Gallery

Pour utiliser Image Builder avec une galerie Azure Compute Gallery, vous devez disposer d’une galerie et d’une définition d’image existantes. Image Builder ne va pas créer la galerie et la définition d’image pour vous.

Si vous n’avez pas encore de définition d’image et de galerie à utiliser, commencez par les créer. Commencez par créer une galerie.

```powershell
# Gallery name
$sigGalleryName= "myIBSIG"

# Image definition name
$imageDefName ="winSvrimage"

# additional replication region
$replRegion2="eastus"

# Create the gallery
New-AzGallery `
   -GalleryName $sigGalleryName `
   -ResourceGroupName $imageResourceGroup  `
   -Location $location

# Create the image definition
New-AzGalleryImageDefinition `
   -GalleryName $sigGalleryName `
   -ResourceGroupName $imageResourceGroup `
   -Location $location `
   -Name $imageDefName `
   -OsState generalized `
   -OsType Windows `
   -Publisher 'myCompany' `
   -Offer 'WindowsServer' `
   -Sku 'WinSrv2019'
```



## <a name="download-and-configure-the-template"></a>Télécharger et configurer le modèle

Téléchargez le modèle .json et configurez-le avec vos variables.

```powershell

$templateFilePath = "armTemplateWinSIG.json"

Invoke-WebRequest `
   -Uri "https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/armTemplateWinSIG.json" `
   -OutFile $templateFilePath `
   -UseBasicParsing

(Get-Content -path $templateFilePath -Raw ) `
   -replace '<subscriptionID>',$subscriptionID | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<rgName>',$imageResourceGroup | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<runOutputName>',$runOutputName | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<imageDefName>',$imageDefName | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<sharedImageGalName>',$sigGalleryName | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<region1>',$location | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<region2>',$replRegion2 | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<imgBuilderId>',$identityNameResourceId) | Set-Content -Path $templateFilePath
```


## <a name="create-the-image-version"></a>Créer la version de l’image

Votre modèle doit être envoyé au service. Cela permettra de télécharger tous les artefacts dépendants, comme les scripts, et de les stocker dans le groupe de ressources intermédiaire, avec le préfixe *IT_* .

```powershell
New-AzResourceGroupDeployment `
   -ResourceGroupName $imageResourceGroup `
   -TemplateFile $templateFilePath `
   -ApiVersion "2020-02-14" `
   -imageTemplateName $imageTemplateName `
   -svclocation $location
```

Pour générer l'image, vous devez appeler « Run » sur le modèle.

```powershell
Invoke-AzResourceAction `
   -ResourceName $imageTemplateName `
   -ResourceGroupName $imageResourceGroup `
   -ResourceType Microsoft.VirtualMachineImages/imageTemplates `
   -ApiVersion "2020-02-14" `
   -Action Run
```

La création de l’image et sa réplication dans les deux régions peuvent prendre un certain temps. Attendez la fin de cette partie pour passer à la création d’une machine virtuelle.

Pour plus d'informations sur les options d'automatisation de l'obtention de l'état de génération d'image, consultez le fichier [Lisez-moi]
```powershell
Get-AzImageBuilderTemplate -ImageTemplateName $imageTemplateName -ResourceGroupName $imageResourceGroup |
  Select-Object -Property Name, LastRunStatusRunState, LastRunStatusMessage, ProvisioningState
```

## <a name="create-the-vm"></a>Création de la machine virtuelle

Créez une machine virtuelle à partir de la version de l’image créée par le Générateur d’images Azure.

Procurez-vous la version de l'image que vous avez créée.
```powershell
$imageVersion = Get-AzGalleryImageVersion `
   -ResourceGroupName $imageResourceGroup `
   -GalleryName $sigGalleryName `
   -GalleryImageDefinitionName $imageDefName
```

Créez la machine virtuelle dans la deuxième région où l'image a été répliquée.

```powershell
$vmResourceGroup = "myResourceGroup"
$vmName = "myVMfromImage"

# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a resource group
New-AzResourceGroup -Name $vmResourceGroup -Location $replRegion2

# Network pieces
$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork -ResourceGroupName $vmResourceGroup -Location $replRegion2 `
  -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig
$pip = New-AzPublicIpAddress -ResourceGroupName $vmResourceGroup -Location $replRegion2 `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $vmResourceGroup -Location $replRegion2 `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface -Name myNic -ResourceGroupName $vmResourceGroup -Location $replRegion2 `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration using $imageVersion.Id to specify the image
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize Standard_D1_v2 | `
Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred | `
Set-AzVMSourceImage -Id $imageVersion.Id | `
Add-AzVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzVM -ResourceGroupName $vmResourceGroup -Location $replRegion2 -VM $vmConfig
```

## <a name="verify-the-customization"></a>Vérifier la personnalisation
Créez une connexion Bureau à distance à la machine virtuelle avec le nom d’utilisateur et le mot de passe définis lors de la création de la machine virtuelle. Dans la machine virtuelle, ouvrez une invite de commande et tapez :

```console
dir c:\
```

Le répertoire nommé `buildActions`, créé pendant la personnalisation de l’image, devrait apparaître.


## <a name="clean-up-resources"></a>Nettoyer les ressources
Si vous souhaitez maintenant essayer de repersonnaliser la version de l’image pour créer une nouvelle version de la même image, **ignorez cette étape** et passez à [Utiliser le Générateur d’images Azure pour créer une autre version de l’image](image-builder-gallery-update-image-version.md).


L’image créée ainsi que tous les autres fichiers de ressources seront ainsi supprimés. Terminez ce déploiement avant de supprimer les ressources.

Supprimez d'abord le modèle de groupe de ressources, sinon le groupe de ressources intermédiaire (*IT_* ) utilisé par AIB ne sera pas nettoyé.

Procurez-vous l'ID de ressource du modèle d'image. 

```powerShell
$resTemplateId = Get-AzResource -ResourceName $imageTemplateName -ResourceGroupName $imageResourceGroup -ResourceType Microsoft.VirtualMachineImages/imageTemplates -ApiVersion "2020-02-14"
```

Supprimez le modèle d'image.

```powerShell
Remove-AzResource -ResourceId $resTemplateId.ResourceId -Force
```

Supprimer une attribution de rôle

```powerShell
Remove-AzRoleAssignment -ObjectId $identityNamePrincipalId -RoleDefinitionName $imageRoleDefName -Scope "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"
```

Supprimer des définitions

```powerShell
Remove-AzRoleDefinition -Name "$identityNamePrincipalId" -Force -Scope "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"
```

Supprimer une identité

```powerShell
Remove-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName -Force
```

Supprimez le groupe de ressources.

```powerShell
Remove-AzResourceGroup $imageResourceGroup -Force
```

## <a name="next-steps"></a>Étapes suivantes

Pour savoir comment mettre à jour la version de l’image créée, voir [Utiliser le Générateur d’images Azure pour créer une autre version de l’image](image-builder-gallery-update-image-version.md).
