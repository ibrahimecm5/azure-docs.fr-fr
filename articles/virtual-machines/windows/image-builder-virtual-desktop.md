---
title: 'Image Builder : Créer une image Azure Virtual Desktop'
description: Créez une image de machine virtuelle Azure pour Windows Virtual Desktop à l’aide d’Azure Image Builder dans PowerShell.
author: kof-f
ms.author: kofiforson
ms.reviewer: cynthn
ms.date: 05/12/2021
ms.topic: article
ms.service: virtual-machines
ms.collection: windows
ms.subservice: image-builder
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 046bec93d3a9ae0ffef8b189470f412ab357943d
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131018486"
---
# <a name="create-a-azure-virtual-desktop-image-using-azure-vm-image-builder-and-powershell"></a>Créer une image Windows Virtual Desktop à l’aide d’Azure VM Image Builder et PowerShell

**S’applique à :** :heavy_check_mark : Machines virtuelles Windows 

Cet article vous montre comment créer une image Azure Virtual Desktop avec ces personnalisations :

* Installation de [FsLogix](https://github.com/DeanCefola/Azure-WVD/blob/master/PowerShell/FSLogixSetup.ps1).
* Exécution d’un [script d’optimisation de Windows Virtual Desktop](https://github.com/The-Virtual-Desktop-Team/Virtual-Desktop-Optimization-Tool) provenant du référentiel de la communauté.
* Installation de [Microsoft Teams](../../virtual-desktop/teams-on-avd.md).
* [Restart](../linux/image-builder-json.md?bc=%2fazure%2fvirtual-machines%2fwindows%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#windows-restart-customizer)
* Exécution de [Windows Update](../linux/image-builder-json.md?bc=%2fazure%2fvirtual-machines%2fwindows%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#windows-update-customizer).

Nous allons vous montrer comment automatiser cette opération à l’aide d’Azure VM Image Builder et distribuer l’image à une [galerie d’images partagées](../shared-image-galleries.md), où vous pouvez la répliquer vers d’autres régions, contrôler l’échelle et partager l’image à l’intérieur et à l’extérieur de vos organisations.


Pour simplifier le déploiement d’une configuration Image Builder, cet exemple utilise un modèle Azure Resource Manager dans lequel est imbriqué le modèle Image Builder. Cela vous donne d’autres avantages, comme des variables et des entrées de paramètres. Vous pouvez également transmettre des paramètres à partir de la ligne de commande.

Cet article est conçu comme un exercice de copier-coller.

> [!NOTE]
> Les scripts d’installation des applications se trouvent sur [GitHub](https://github.com/danielsollondon/azvmimagebuilder/tree/master/solutions/14_Building_Images_WVD). Ils sont destinés uniquement à des fins d’illustration et de test, et non aux charges de travail de production. 

## <a name="tips-for-building-windows-images"></a>Conseils pour la création d’images Windows 

- Taille de la machine virtuelle : La taille de machine virtuelle par défaut est `Standard_D1_v2`, ce qui n’est pas adapté à Windows. Utilisez une taille `Standard_D2_v2` ou supérieure.
- Cet exemple utilise les [scripts du personnalisateur PowerShell](../linux/image-builder-json.md). Vous devez utiliser ces paramètres, sinon la compilation ne répondra plus.

    ```json
      "runElevated": true,
      "runAsSystem": true,
    ```

    Par exemple :

    ```json
      {
          "type": "PowerShell",
          "name": "installFsLogix",
          "runElevated": true,
          "runAsSystem": true,
          "scriptUri": "https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/14_Building_Images_WVD/0_installConfFsLogix.ps1"
    ```
- Commentez votre code : Le journal de génération d’AIB (customization.log) est extrêmement détaillé. Si vous commentez vos scripts en utilisant la cmdlet « write-host », ces commentaires seront envoyés dans les journaux et faciliteront la résolution des problèmes.

    ```PowerShell
     write-host 'AIB Customization: Starting OS Optimizations script'
    ```

- Codes de sortie : AIB s’attend à ce que tous les scripts renvoient un code de sortie 0. Tout code de sortie différent de zéro entraîne l’échec de la personnalisation et l’arrêt de la compilation. Si vous avez des scripts complexes, ajoutez une instrumentation et émettez des codes de sortie, qui s’afficheront dans le fichier customization.log.

    ```PowerShell
     Write-Host "Exit code: " $LASTEXITCODE
    ```
- Test : testez et testez encore votre code avant sur une machine virtuelle autonome, assurez-vous qu’il n’y a pas d’invites utilisateur, que vous utilisez le bon privilège, etc.

- Mise en réseau : `Set-NetAdapterAdvancedProperty`. En cours d’introduction dans le script d’optimisation, mais la génération du fichier AIB échoue, car elle déconnecte le réseau. Cela est commenté et en cours d’investigation.

## <a name="prerequisites"></a>Prérequis

Vous devez avoir installé les dernières applets de commande Azure PowerShell ; consultez [Vue d’ensemble d’Azure PowerShell](/powershell/azure/overview) pour connaître les détails d’installation.

```PowerShell
# check you are registered for the providers, ensure RegistrationState is set to 'Registered'.
Get-AzResourceProvider -ProviderNamespace Microsoft.VirtualMachineImages
Get-AzResourceProvider -ProviderNamespace Microsoft.Storage 
Get-AzResourceProvider -ProviderNamespace Microsoft.Compute
Get-AzResourceProvider -ProviderNamespace Microsoft.KeyVault

# If they do not show as registered, run the commented out code below.

## Register-AzResourceProvider -ProviderNamespace Microsoft.VirtualMachineImages
## Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
## Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
## Register-AzResourceProvider -ProviderNamespace Microsoft.KeyVault
```

## <a name="set-up-environment-and-variables"></a>Configurer l’environnement et les variables

```azurepowershell-interactive
# Step 1: Import module
Import-Module Az.Accounts

# Step 2: get existing context
$currentAzContext = Get-AzContext

# destination image resource group
$imageResourceGroup="wvdImageDemoRg"

# location (see possible locations in main docs)
$location="westus2"

# your subscription, this will get your current subscription
$subscriptionID=$currentAzContext.Subscription.Id

# image template name
$imageTemplateName="wvd10ImageTemplate01"

# distribution properties object name (runOutput), i.e. this gives you the properties of the managed image on completion
$runOutputName="sigOutput"

# create resource group
New-AzResourceGroup -Name $imageResourceGroup -Location $location
```

## <a name="permissions-user-identity-and-role"></a>Autorisations, identité de l’utilisateur et rôle 


 Créez une identité d’utilisateur.

```azurepowershell-interactive
# setup role def names, these need to be unique
$timeInt=$(get-date -UFormat "%s")
$imageRoleDefName="Azure Image Builder Image Def"+$timeInt
$idenityName="aibIdentity"+$timeInt

## Add AZ PS modules to support AzUserAssignedIdentity and Az AIB
'Az.ImageBuilder', 'Az.ManagedServiceIdentity' | ForEach-Object {Install-Module -Name $_ -AllowPrerelease}

# create identity
New-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $idenityName

$idenityNameResourceId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $idenityName).Id
$idenityNamePrincipalId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $idenityName).PrincipalId

```

Attribuez des autorisations à l’identité pour distribuer des images. Cette commande permet de télécharger et mettre à jour le modèle avec les paramètres spécifiés précédemment.

```azurepowershell-interactive
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
New-AzRoleAssignment -ObjectId $idenityNamePrincipalId -RoleDefinitionName $imageRoleDefName -Scope "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"
```

> [!NOTE] 
> Si vous voyez l’erreur suivante : « New-AzRoleDefinition : Limite de définition de rôle dépassée. Plus aucune définition de rôle ne peut être créée. », Consultez [Résoudre les problèmes liés à Azure RBAC](../../role-based-access-control/troubleshooting.md).


## <a name="create-the-shared-image-gallery"></a>Créer la galerie Shared Image Gallery 

Si vous n’avez pas encore de galerie d’images partagées, vous devez en créer une.

```azurepowershell-interactive
$sigGalleryName= "myaibsig01"
$imageDefName ="win10wvd"

# create gallery
New-AzGallery -GalleryName $sigGalleryName -ResourceGroupName $imageResourceGroup  -Location $location

# create gallery definition
New-AzGalleryImageDefinition -GalleryName $sigGalleryName -ResourceGroupName $imageResourceGroup -Location $location -Name $imageDefName -OsState generalized -OsType Windows -Publisher 'myCo' -Offer 'Windows' -Sku '10wvd'

```

## <a name="configure-the-image-template"></a>Configurer le modèle d’image

Pour cet exemple, nous disposons d’un modèle prêt à télécharger et à mettre à jour le modèle avec les paramètres spécifiés précédemment. Il installera FsLogix, les optimisations du système d’exploitation et Microsoft Teams et exécutera Windows Update à la fin.

Si vous ouvrez le modèle, vous pouvez voir dans la propriété source l’image qui est utilisée. Dans cet exemple, il s’agit d’une image Win 10 multisession. 

### <a name="windows-10-images"></a>Images Windows 10
Il existe deux grands types d’images que vous devez connaître : les images multisession et les images à session unique.

Les images multisession sont destinées à une utilisation groupée. Voici un exemple des détails de l’image dans Azure :

```json
"publisher": "MicrosoftWindowsDesktop",
"offer": "Windows-10",
"sku": "20h2-evd",
"version": "latest"
```

Les images à session unique sont destinées à une utilisation individuelle. Voici un exemple des détails de l’image dans Azure :

```json
"publisher": "MicrosoftWindowsDesktop",
"offer": "Windows-10",
"sku": "19h2-ent",
"version": "latest"
```

Vous pouvez également modifier les images Win10 disponibles :

```azurepowershell-interactive
Get-AzVMImageSku -Location westus2 -PublisherName MicrosoftWindowsDesktop -Offer windows-10
```

## <a name="download-template-and-configure"></a>Télécharger le modèle et le configurer

Maintenant, vous devez télécharger le modèle et le configurer pour votre utilisation.

```azurepowershell-interactive
$templateUrl="https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/14_Building_Images_WVD/armTemplateWVD.json"
$templateFilePath = "armTemplateWVD.json"

Invoke-WebRequest -Uri $templateUrl -OutFile $templateFilePath -UseBasicParsing

((Get-Content -path $templateFilePath -Raw) -replace '<subscriptionID>',$subscriptionID) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<rgName>',$imageResourceGroup) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<region>',$location) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<runOutputName>',$runOutputName) | Set-Content -Path $templateFilePath

((Get-Content -path $templateFilePath -Raw) -replace '<imageDefName>',$imageDefName) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<sharedImageGalName>',$sigGalleryName) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<region1>',$location) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<imgBuilderId>',$idenityNameResourceId) | Set-Content -Path $templateFilePath

```

N’hésitez pas à consulter le [modèle](https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/14_Building_Images_WVD/armTemplateWVD.json), tout le code est visible.


## <a name="submit-the-template"></a>Envoyer le modèle

Votre modèle doit être envoyé au service. Cela permettra de télécharger tous les artefacts dépendants, comme les scripts, de les valider, de vérifier les autorisations et de les stocker dans le groupe de ressources intermédiaire, avec le préfixe *IT_* .

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName $imageResourceGroup -TemplateFile $templateFilePath -api-version "2020-02-14" -imageTemplateName $imageTemplateName -svclocation $location

# Optional - if you have any errors running the above, run:
$getStatus=$(Get-AzImageBuilderTemplate -ResourceGroupName $imageResourceGroup -Name $imageTemplateName)
$getStatus.ProvisioningErrorCode 
$getStatus.ProvisioningErrorMessage
```
 
## <a name="build-the-image"></a>Créer l’image
```azurepowershell-interactive
Start-AzImageBuilderTemplate -ResourceGroupName $imageResourceGroup -Name $imageTemplateName -NoWait
```

> [!NOTE]
> La commande n’attend pas que le service Image Builder termine la compilation de l’image, vous pouvez interroger l’état ci-dessous.

```azurepowershell-interactive
$getStatus=$(Get-AzImageBuilderTemplate -ResourceGroupName $imageResourceGroup -Name $imageTemplateName)

# this shows all the properties
$getStatus | Format-List -Property *

# these show the status the build
$getStatus.LastRunStatusRunState 
$getStatus.LastRunStatusMessage
$getStatus.LastRunStatusRunSubState
```
## <a name="create-a-vm"></a>Créer une machine virtuelle
Maintenant que la build est terminée, vous pouvez générer une machine virtuelle à partir de l’image. Utilisez les exemples disponibles dans [New-AzVM (Az.Compute)](/powershell/module/az.compute/new-azvm#examples).

## <a name="clean-up"></a>Nettoyage

Supprimez d’abord le modèle de groupe de ressources. Ne supprimez pas simplement l’ensemble du groupe de ressources, sinon le groupe de ressources intermédiaire (*IT_* ) utilisé par AIB ne sera pas nettoyé.

Supprimez le modèle d’image.

```azurepowershell-interactive
Remove-AzImageBuilderTemplate -ResourceGroupName $imageResourceGroup -Name wvd10ImageTemplate
```

Supprimez l’attribution de rôle.

```azurepowershell-interactive
Remove-AzRoleAssignment -ObjectId $idenityNamePrincipalId -RoleDefinitionName $imageRoleDefName -Scope "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"

## remove definitions
Remove-AzRoleDefinition -Name "$idenityNamePrincipalId" -Force -Scope "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"

## delete identity
Remove-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $idenityName -Force
```

Supprimez le groupe de ressources.

```azurepowershell-interactive
Remove-AzResourceGroup $imageResourceGroup -Force
```

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez essayer d’autres exemples [sur GitHub](https://github.com/azure/azvmimagebuilder/tree/master/quickquickstarts).

