---
title: Créer une machine virtuelle Windows avec le Générateur d’images Azure avec PowerShell
description: Créer une machine virtuelle Windows avec le module PowerShell de Générateur d’images Azure.
author: kof-f
ms.author: kofiforson
ms.reviewer: cynthn
ms.date: 03/02/2021
ms.topic: how-to
ms.service: virtual-machines
ms.subervice: image-builder
ms.colletion: windows
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: cb061e152e34cc83b210907cc2e43017d85e1c97
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131449124"
---
# <a name="create-a-windows-vm-with-azure-image-builder-using-powershell"></a>Créer une machine virtuelle Windows avec le Générateur d’images Azure avec PowerShell

**S’applique à :** :heavy_check_mark : Machines virtuelles Windows 

Cet article vous montre comment créer une image Windows personnalisée à l’aide du module PowerShell du Générateur d’images de machine virtuelle Azure.


## <a name="prerequisites"></a>Prérequis

Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.

Si vous choisissez d’utiliser PowerShell localement, cet article vous demande d’installer le module Az PowerShell et de vous connecter à votre compte Azure avec l’applet de commande [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount). Pour en savoir plus sur l’installation du module Az PowerShell, consultez [Installer Azure PowerShell](/powershell/azure/install-az-ps).

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Si vous avez plusieurs abonnements Azure, sélectionnez l’abonnement approprié dans lequel les ressources doivent être facturées. Sélectionnez un abonnement spécifique avec l’applet de commande [Set-AzContext](/powershell/module/az.accounts/set-azcontext).

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

### <a name="register-features"></a>Inscrire des fonctionnalités

Inscrivez les fournisseurs de ressources suivants avec votre abonnement Azure s’ils ne sont pas déjà inscrits.

- Microsoft.Compute
- Microsoft.KeyVault
- Microsoft.Storage
- Microsoft.Network
- Microsoft.VirtualMachineImages

```azurepowershell-interactive
Get-AzResourceProvider -ProviderNamespace Microsoft.Compute, Microsoft.KeyVault, Microsoft.Storage, Microsoft.VirtualMachineImages, Microsoft.Network |
  Where-Object RegistrationState -ne Registered |
    Register-AzResourceProvider
```

## <a name="define-variables"></a>Définir des variables

Vous utiliserez plusieurs types d’informations de façon répétée. Créez des variables pour stocker les informations.

```azurepowershell-interactive
# Destination image resource group name
$imageResourceGroup = 'myWinImgBuilderRG'

# Azure region
$location = 'WestUS2'

# Name of the image to be created
$imageTemplateName = 'myWinImage'

# Distribution properties of the managed image upon completion
$runOutputName = 'myDistResults'
```

Créez une variable pour votre ID d’abonnement Azure. Pour confirmer que la variable `subscriptionID` contient votre ID d’abonnement, vous pouvez exécuter la deuxième ligne de l’exemple suivant.

```azurepowershell-interactive
# Your Azure Subscription ID
$subscriptionID = (Get-AzContext).Subscription.Id
Write-Output $subscriptionID
```

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un [groupe de ressources Azure](../../azure-resource-manager/management/overview.md) avec l’applet de commande [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Un groupe de ressources est un conteneur logique dans lequel les ressources Azure sont déployées et gérées en tant que groupe.

L’exemple suivant crée un groupe de ressources en fonction du nom de la variable `$imageResourceGroup` dans la région spécifiée de la variable `$location`. Ce groupe de ressources sert à stocker l’artefact de modèle de configuration d’image et l’image.

```azurepowershell-interactive
New-AzResourceGroup -Name $imageResourceGroup -Location $location
```

## <a name="create-user-identity-and-set-role-permissions"></a>Créer une identité d’utilisateur et définir des autorisations de rôle

Accordez des autorisations au Générateur d’images Azure pour créer des images dans le groupe de ressources spécifié à l’aide de l’exemple suivant. Sans cette autorisation, le processus de génération de l’image ne se termine pas correctement.

Créer des variables pour les noms de définition de rôle et d’identité. Ces valeurs doivent être uniques.

```azurepowershell-interactive
[int]$timeInt = $(Get-Date -UFormat '%s')
$imageRoleDefName = "Azure Image Builder Image Def $timeInt"
$identityName = "myIdentity$timeInt"
```

Créez une identité d’utilisateur.

```azurepowershell-interactive
New-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName
```

Stockez les ressources d’identité et les ID du principal dans les variables.

```azurepowershell-interactive
$identityNameResourceId = (Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).Id
$identityNamePrincipalId = (Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).PrincipalId
```

### <a name="assign-permissions-for-identity-to-distribute-images"></a>Attribuer des autorisations d’identité pour distribuer des images

Téléchargez le fichier de configuration .json et modifiez-le en fonction des paramètres définis dans cet article.

```azurepowershell-interactive
$myRoleImageCreationUrl = 'https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json'
$myRoleImageCreationPath = "$env:TEMP\myRoleImageCreation.json"

Invoke-WebRequest -Uri $myRoleImageCreationUrl -OutFile $myRoleImageCreationPath -UseBasicParsing

$Content = Get-Content -Path $myRoleImageCreationPath -Raw
$Content = $Content -replace '<subscriptionID>', $subscriptionID
$Content = $Content -replace '<rgName>', $imageResourceGroup
$Content = $Content -replace 'Azure Image Builder Service Image Creation Role', $imageRoleDefName
$Content | Out-File -FilePath $myRoleImageCreationPath -Force
```

Créez la définition de rôle.

```azurepowershell-interactive
New-AzRoleDefinition -InputFile $myRoleImageCreationPath
```

Accordez la définition de rôle au principal du Générateur d’images.

```azurepowershell-interactive
$RoleAssignParams = @{
  ObjectId = $identityNamePrincipalId
  RoleDefinitionName = $imageRoleDefName
  Scope = "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"
}
New-AzRoleAssignment @RoleAssignParams
```

> [!NOTE]
> Si vous recevez le message d’erreur : « _New-AzRoleDefinition : Limite de définitions de rôle dépassée. Aucune autre définition de rôle ne peut être créée._  », consultez [résoudre les problèmes DANS Azure RBAC](../../role-based-access-control/troubleshooting.md).

## <a name="create-an-azure-compute-gallery-formerly-known-as-shared-image-gallery"></a>Créer une galerie Azure Compute Gallery (anciennement appelée Shared Image Gallery)

Créez la galerie.

```azurepowershell-interactive
$myGalleryName = 'myImageGallery'
$imageDefName = 'winSvrImages'

New-AzGallery -GalleryName $myGalleryName -ResourceGroupName $imageResourceGroup -Location $location
```

Créez une définition de galerie.

```azurepowershell-interactive
$GalleryParams = @{
  GalleryName = $myGalleryName
  ResourceGroupName = $imageResourceGroup
  Location = $location
  Name = $imageDefName
  OsState = 'generalized'
  OsType = 'Windows'
  Publisher = 'myCo'
  Offer = 'Windows'
  Sku = 'Win2019'
}
New-AzGalleryImageDefinition @GalleryParams
```

## <a name="create-an-image"></a>Créer une image

Créez un objet source pour le Générateur d’images Azure. Pour connaître les valeurs de paramètres, consultez [Rechercher des images de machine virtuelle Windows sur la Place de marché Microsoft Azure avec Azure PowerShell](./cli-ps-findimage.md).

```azurepowershell-interactive
$SrcObjParams = @{
  SourceTypePlatformImage = $true
  Publisher = 'MicrosoftWindowsServer'
  Offer = 'WindowsServer'
  Sku = '2019-Datacenter'
  Version = 'latest'
}
$srcPlatform = New-AzImageBuilderSourceObject @SrcObjParams
```

Créez un objet de distribution pour le Générateur d’images Azure.

```azurepowershell-interactive
$disObjParams = @{
  SharedImageDistributor = $true
  ArtifactTag = @{tag='dis-share'}
  GalleryImageId = "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup/providers/Microsoft.Compute/galleries/$myGalleryName/images/$imageDefName"
  ReplicationRegion = $location
  RunOutputName = $runOutputName
  ExcludeFromLatest = $false
}
$disSharedImg = New-AzImageBuilderDistributorObject @disObjParams
```

Créez un objet de personnalisation pour le Générateur d’images Azure.

```azurepowershell-interactive
$ImgCustomParams01 = @{
  PowerShellCustomizer = $true
  CustomizerName = 'settingUpMgmtAgtPath'
  RunElevated = $false
  Inline = @("mkdir c:\\buildActions", "mkdir c:\\buildArtifacts", "echo Azure-Image-Builder-Was-Here  > c:\\buildActions\\buildActionsOutput.txt")
}
$Customizer01 = New-AzImageBuilderCustomizerObject @ImgCustomParams01
```

Créez un deuxième objet de personnalisation pour le Générateur d’images Azure.

```azurepowershell-interactive
$ImgCustomParams02 = @{
  FileCustomizer = $true
  CustomizerName = 'downloadBuildArtifacts'
  Destination = 'c:\\buildArtifacts\\index.html'
  SourceUri = 'https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/exampleArtifacts/buildArtifacts/index.html'
}
$Customizer02 = New-AzImageBuilderCustomizerObject @ImgCustomParams02
```

Créez un modèle Azure pour le Générateur d’images Azure.

```azurepowershell-interactive
$ImgTemplateParams = @{
  ImageTemplateName = $imageTemplateName
  ResourceGroupName = $imageResourceGroup
  Source = $srcPlatform
  Distribute = $disSharedImg
  Customize = $Customizer01, $Customizer02
  Location = $location
  UserAssignedIdentityId = $identityNameResourceId
}
New-AzImageBuilderTemplate @ImgTemplateParams
```

Une fois l’exécution terminée, un message est retourné et un modèle de configuration pour le Générateur d’images Azure est créé dans `$imageResourceGroup`.

Pour déterminer si le processus de création du modèle a réussi, vous pouvez utiliser l’exemple suivant.

```azurepowershell-interactive
Get-AzImageBuilderTemplate -ImageTemplateName $imageTemplateName -ResourceGroupName $imageResourceGroup |
  Select-Object -Property Name, LastRunStatusRunState, LastRunStatusMessage, ProvisioningState
```

En outre, en arrière-plan, le générateur d’images crée un groupe de ressources de mise en lots dans votre abonnement. Ce groupe de ressources est utilisé pour générer l’image. Il est au format : `IT_<DestinationResourceGroup>_<TemplateName>`.

> [!WARNING]
> Ne supprimez pas directement le groupe de ressources de mise en lots. Supprimez l’artefact de modèle d’image ; cela supprimera le groupe de ressources de mise en lots.

Si le service signale un échec lors de la soumission du modèle de configuration d’image,

- Consultez [Résolution des problèmes liés aux défaillances de création d’image de machine virtuelle Azure](../linux/image-builder-troubleshoot.md).
- Supprimez le modèle à l’aide de l’exemple suivant avant de réessayer.

```azurepowershell-interactive
Remove-AzImageBuilderTemplate -ImageTemplateName $imageTemplateName -ResourceGroupName $imageResourceGroup
```

## <a name="start-the-image-build"></a>Lancer la génération de l’image

Envoyez la configuration de l’image au service Générateur d’images de la machine virtuelle.

```azurepowershell-interactive
Start-AzImageBuilderTemplate -ResourceGroupName $imageResourceGroup -Name $imageTemplateName
```

Attendez la fin du processus de génération de l’image. Cette étape peut prendre une heure.

Si vous rencontrez des problèmes, consultez [Résolution des problèmes liés aux défaillances de création d’image de machine virtuelle Azure](../linux/image-builder-troubleshoot.md).

## <a name="create-a-vm"></a>Créer une machine virtuelle

Stockez les informations d’identification de connexion pour la machine virtuelle dans une variable. Le mot de passe doit être complexe.

```azurepowershell-interactive
$Cred = Get-Credential
```

Créez la machine virtuelle avec l’image que vous avez créée.

```azurepowershell-interactive
$ArtifactId = (Get-AzImageBuilderRunOutput -ImageTemplateName $imageTemplateName -ResourceGroupName $imageResourceGroup).ArtifactId

New-AzVM -ResourceGroupName $imageResourceGroup -Image $ArtifactId -Name myWinVM01 -Credential $Cred
```

## <a name="verify-the-customizations"></a>Vérifier les personnalisations

Créez une connexion Bureau à distance à la machine virtuelle avec le nom d’utilisateur et le mot de passe définis lors de la création de la machine virtuelle. À l’intérieur de la machine virtuelle, ouvrez PowerShell et exécutez `Get-Content` comme indiqué dans l’exemple suivant :

```azurepowershell-interactive
Get-Content -Path C:\buildActions\buildActionsOutput.txt
```

Vous devez voir la sortie en fonction du contenu du fichier créé pendant le processus de personnalisation de l’image.

```Output
Azure-Image-Builder-Was-Here
```

Dans la même session PowerShell, pour vérifier que la deuxième personnalisation a réussi, recherchez la présence du fichier `c:\buildArtifacts\index.html` comme illustré dans l’exemple suivant :

```azurepowershell-interactive
Get-ChildItem c:\buildArtifacts\
```

Le résultat doit être une liste de répertoires affichant le fichier téléchargé pendant le processus de personnalisation de l’image.

```Output
    Directory: C:\buildArtifacts

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a---          29/01/2021    10:04            276 index.html
```


## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous n’avez pas besoin des ressources que vous avez créées dans cet article, vous pouvez les supprimer en exécutant l’exemple suivant.

### <a name="delete-the-image-builder-template"></a>Supprimer le modèle de générateur d’images

```azurepowershell-interactive
Remove-AzImageBuilderTemplate -ResourceGroupName $imageResourceGroup -Name $imageTemplateName
```

### <a name="delete-the-image-resource-group"></a>Supprimer le groupe de ressources d’image

> [!CAUTION]
> L’exemple suivant supprime le groupe de ressources spécifié et toutes les ressources qu’il contient.
> Si des ressources en dehors du cadre de cet article existent dans le groupe de ressources spécifié, elles seront également supprimées.

```azurepowershell-interactive
Remove-AzResourceGroup -Name $imageResourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les composants du fichier .json utilisé dans cet article, voir [Référence des modèles du Générateur d’images](../linux/image-builder-json.md).
