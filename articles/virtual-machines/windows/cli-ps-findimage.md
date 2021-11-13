---
title: Rechercher et utiliser des informations sur le plan d’achat de la Place de marché à l’aide de PowerShell
description: Utilisez Azure PowerShell pour rechercher des URN d’images et des paramètres de plan d’achat, tels que l’éditeur, l’offre, la référence SKU et la version, pour les images de machine virtuelle de la Place de marché.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 03/17/2021
ms.author: cynthn
ms.custom: contperf-fy21q3, devx-track-azurepowershell
ms.openlocfilehash: b445b52e1e4a3131f7830d6ea167046109d0e2af
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131466748"
---
# <a name="find-and-use-azure-marketplace-vm-images-with-azure-powershell"></a>Rechercher et utiliser des images de machine virtuelle de la Place de marché Azure avec Azure PowerShell

**S’applique à :** :heavy_check_mark: Machines virtuelles Linux :heavy_check_mark: Machines virtuelles Windows :heavy_check_mark: Groupes identiques flexibles :heavy_check_mark: Groupes identiques uniformes


Cet article décrit comment utiliser Azure PowerShell pour rechercher des images de machine virtuelle sur la Place de marché Microsoft Azure. Vous pouvez ensuite spécifier des informations sur une image et un plan de la Place de marché lorsque vous créez une machine virtuelle.

Vous pouvez également parcourir les images et offres disponibles à l’aide de la [Place de marché Azure](https://azuremarketplace.microsoft.com/) ou de l’interface [Azure CLI](../linux/cli-ps-findimage.md). 

## <a name="terminology"></a>Terminologie

Une image de Place de Marché dans Azure a les attributs suivants :

* **Éditeur** : organisation qui a créé l’image. Exemples : Canonical, MicrosoftWindowsServer
* **Offre** : nom du groupe d’images associées créé par un éditeur. Exemples : UbuntuServer, WindowsServer
* **Référence SKU** : instance d’une offre, par exemple une version majeure d’une distribution. Exemples : 18.04-LTS, 2019-Datacenter
* **Version** : numéro de version d’une référence SKU d’image. 

Ces valeurs peuvent être transmises individuellement ou en tant qu’*URN* d’image, combinant les valeurs séparées par le signe deux-points (:). Par exemple : *Éditeur*:*Offre*:*SKU*:*Version*. Vous pouvez remplacer le numéro de version dans l’URN par `latest` pour utiliser la version la plus récente de l’image. 

Si l’éditeur d’images fournit des conditions de licences et d’achat supplémentaires, vous devez les accepter avant de pouvoir utiliser l’image.  Pour plus d’informations, consultez [Accepter les conditions du plan d’achat](#accept-purchase-plan-terms).

## <a name="list-images"></a>Répertorier des images

Vous pouvez utiliser PowerShell pour affiner la liste d’images. Remplacez les valeurs des variables en fonction de vos besoins.

1. Répertoriez les éditeurs d’images à l’aide de la commande [Get-AzVMImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher).
    
    ```powershell
    $locName="<location>"
    Get-AzVMImagePublisher -Location $locName | Select PublisherName
    ```
1. Répertoriez les offres d’un éditeur donné à l’aide de la commande [Get-AzVMImageOffer](/powershell/module/az.compute/get-azvmimageoffer).
    
    ```powershell
    $pubName="<publisher>"
    Get-AzVMImageOffer -Location $locName -PublisherName $pubName | Select Offer
    ```
1. Pour un éditeur et une offre donnés, répertoriez les références SKU disponibles à l’aide de la commande [Get-AzVMImageSku](/powershell/module/az.compute/get-azvmimagesku).
    
    ```powershell
    $offerName="<offer>"
    Get-AzVMImageSku -Location $locName -PublisherName $pubName -Offer $offerName | Select Skus
    ```
1. Pour une référence SKU, répertoriez les versions de l’image à l’aide de la commande [Get-AzVMImage](/powershell/module/az.compute/get-azvmimage).

    ```powershell
    $skuName="<SKU>"
    Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Sku $skuName | Select Version
    ```
    Vous pouvez également utiliser `latest` si vous souhaitez utiliser l’image la plus récente et non une version antérieure spécifique.


Maintenant, vous pouvez combiner l’éditeur, l’offre, la référence SKU et la version sélectionnés en un schéma URN (valeurs séparées par :). Transmettez cet URN avec le paramètre `-Image` lorsque vous créez une machine virtuelle à l'aide de la cmdlet [New-AzVM](/powershell/module/az.compute/new-azvm). Vous pouvez également remplacer le numéro de version dans l’URN par `latest` pour obtenir la version la plus récente de l’image.

Si vous déployez une machine virtuelle avec un modèle Resource Manager, vous définissez alors les paramètres d’image de manière individuelle dans les propriétés `imageReference`. Consultez la [référence de modèle](/azure/templates/microsoft.compute/virtualmachines).


## <a name="view-purchase-plan-properties"></a>Afficher les propriétés du plan d’achat

Certaines images de machine virtuelle dans la Place de marché Microsoft Azure ont des conditions de licence et d’achat supplémentaires que vous devez accepter pour pouvoir les déployer par programme. Vous devez accepter les conditions de l’image une fois par abonnement.

Pour afficher les informations du plan d’achat d’une image, exécutez la cmdlet `Get-AzVMImage`. Si la propriété `PurchasePlan` dans la sortie n’est pas `null`, l’image a des conditions que vous devez accepter avant le déploiement par programmation.  

Par exemple, l’image *Windows Server 2016 Datacenter* ne possède pas de conditions supplémentaires, car l’information `PurchasePlan` est `null` :

```powershell
$version = "2016.127.20170406"
Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Skus $skuName -Version $version
```

La sortie ressemble à l'exemple suivant :

```output
Id               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/MicrosoftWindowsServer/ArtifactTypes/VMImage/Offers/WindowsServer/Skus/2016-Datacenter/Versions/2019.0.20190115
Location         : westus
PublisherName    : MicrosoftWindowsServer
Offer            : WindowsServer
Skus             : 2019-Datacenter
Version          : 2019.0.20190115
FilterExpression :
Name             : 2019.0.20190115
OSDiskImage      : {
                     "operatingSystem": "Windows"
                   }
PurchasePlan     : null
DataDiskImages   : []

```

L’exemple ci-dessous montre une commande similaire pour l’image *Data Science Virtual Machine - Windows 2016*, ayant les propriétés `PurchasePlan` suivantes : `name`, `product` et `publisher`. Certaines images ont également une propriété `promotion code`. Pour déployer cette image, consultez les sections suivantes pour accepter les conditions et activer le déploiement par programmation.

```powershell
Get-AzVMImage -Location "westus" -PublisherName "microsoft-ads" -Offer "windows-data-science-vm" -Skus "windows2016" -Version "0.2.02"
```

La sortie ressemble à l'exemple suivant :

```
Id               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/microsoft-ads/ArtifactTypes/VMImage/Offers/windows-data-science-vm/Skus/windows2016/Versions/19.01.14
Location         : westus
PublisherName    : microsoft-ads
Offer            : windows-data-science-vm
Skus             : windows2016
Version          : 19.01.14
FilterExpression :
Name             : 19.01.14
OSDiskImage      : {
                     "operatingSystem": "Windows"
                   }
PurchasePlan     : {
                     "publisher": "microsoft-ads",
                     "name": "windows2016",
                     "product": "windows-data-science-vm"
                   }
DataDiskImages   : []

```

Pour afficher les termes du contrat de licence, utilisez la cmdlet [Get-AzMarketplaceterms](/powershell/module/az.marketplaceordering/get-azmarketplaceterms) et transmettez les paramètres de plan d'achat. La sortie contient un lien vers les conditions de l’image de la Marketplace et indique si vous les avez acceptées précédemment. Veillez à utiliser uniquement des lettres minuscules dans les valeurs de paramètre.

```powershell
Get-AzMarketplaceterms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016"
```

La sortie ressemble à l'exemple suivant :

```output
Publisher         : microsoft-ads
Product           : windows-data-science-vm
Plan              : windows2016
LicenseTextLink   : https://storelegalterms.blob.core.windows.net/legalterms/3E5ED_legalterms_MICROSOFT%253a2DADS%253a24WINDOWS%253a2DDATA%253a2DSCIENCE%253a2DVM%253a24WINDOWS2016%253a24OC5SKMQOXSED66BBSNTF4XRCS4XLOHP7QMPV54DQU7JCBZWYFP35IDPOWTUKXUC7ZAG7W6ZMDD6NHWNKUIVSYBZUTZ245F44SU5AD7Q.txt
PrivacyPolicyLink : https://www.microsoft.com/EN-US/privacystatement/OnlineServices/Default.aspx
Signature         : 2UMWH6PHSAIM4U22HXPXW25AL2NHUJ7Y7GRV27EBL6SUIDURGMYG6IIDO3P47FFIBBDFHZHSQTR7PNK6VIIRYJRQ3WXSE6BTNUNENXA
Accepted          : False
Signdate          : 1/25/2019 7:43:00 PM
```

## <a name="accept-purchase-plan-terms"></a>Accepter les conditions du plan d’achat

Utilisez la cmdlet [Set-AzMarketplaceterms](/powershell/module/az.marketplaceordering/set-azmarketplaceterms) pour accepter ou rejeter les conditions. Vous ne devez accepter qu’une fois les conditions par abonnement pour l’image. Veillez à utiliser uniquement des lettres minuscules dans les valeurs de paramètre. 

```powershell
$agreementTerms=Get-AzMarketplaceterms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016"

Set-AzMarketplaceTerms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016" -Terms $agreementTerms -Accept
```



```output
Publisher         : microsoft-ads
Product           : windows-data-science-vm
Plan              : windows2016
LicenseTextLink   : https://storelegalterms.blob.core.windows.net/legalterms/3E5ED_legalterms_MICROSOFT%253a2DADS%253a24WINDOWS%253a2DDATA%253a2DSCIENCE%253a2DV
                    M%253a24WINDOWS2016%253a24OC5SKMQOXSED66BBSNTF4XRCS4XLOHP7QMPV54DQU7JCBZWYFP35IDPOWTUKXUC7ZAG7W6ZMDD6NHWNKUIVSYBZUTZ245F44SU5AD7Q.txt
PrivacyPolicyLink : https://www.microsoft.com/EN-US/privacystatement/OnlineServices/Default.aspx
Signature         : XXXXXXK3MNJ5SROEG2BYDA2YGECU33GXTD3UFPLPC4BAVKAUL3PDYL3KBKBLG4ZCDJZVNSA7KJWTGMDSYDD6KRLV3LV274DLBXXXXXX
Accepted          : True
Signdate          : 2/23/2018 7:49:31 PM
```


## <a name="create-a-new-vm-from-a-marketplace-image"></a>Créer une machine virtuelle à partir d’une image de la Place de marché

Si vous avez déjà les informations sur l’image que vous souhaitez utiliser, vous pouvez transmettre ces informations dans l’applet de commande [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage) pour ajouter les informations sur l’image à la configuration de la machine virtuelle. Pour rechercher et lister les images disponibles sur la Place de marché, consultez les sections suivantes.

Certaines images payantes vous demandent également de fournir des informations sur le plan d’achat à l’aide de [Set-AzVMPlan](/powershell/module/az.compute/set-azvmplan). 

```powershell
...

$vmConfig = New-AzVMConfig -VMName "myVM" -VMSize Standard_D1

# Set the Marketplace image
$offerName = "windows-data-science-vm"
$skuName = "windows2016"
$version = "19.01.14"
$vmConfig = Set-AzVMSourceImage -VM $vmConfig -PublisherName $publisherName -Offer $offerName -Skus $skuName -Version $version

# Set the Marketplace plan information, if needed
$publisherName = "microsoft-ads"
$productName = "windows-data-science-vm"
$planName = "windows2016"
$vmConfig = Set-AzVMPlan -VM $vmConfig -Publisher $publisherName -Product $productName -Name $planName

...
```

Vous transmettez ensuite la configuration de la machine virtuelle en même temps que les autres objets de configuration à l’applet de commande `New-AzVM`. Pour obtenir un exemple détaillé de l’utilisation d’une configuration de machine virtuelle avec PowerShell, consultez ce [script](https://github.com/Azure/azure-docs-powershell-samples/blob/master/virtual-machine/create-vm-detailed/create-windows-vm-detailed.ps1).

Si vous recevez un message concernant l’acceptation des conditions de l’image, consultez la section précédente [Accepter les conditions du plan d’achat](#accept-purchase-plan-terms).

## <a name="create-a-new-vm-from-a-vhd-with-purchase-plan-information"></a>Créer une machine virtuelle à partir d’un disque dur virtuel avec des informations de plan d’achat

Si vous disposez d’un VHD existant qui a été créé à l’aide d’une image de la Place de marché Azure, vous devrez peut-être fournir les informations du plan d’achat lors de la création d’une machine virtuelle à partir de ce VHD.

Si vous avez toujours la machine virtuelle d’origine, ou une autre machine virtuelle créée à partir de la même image, vous pouvez obtenir le nom du plan, l’éditeur et les informations sur le produit à l’aide de Get-AzVM. Cet exemple obtient une machine virtuelle nommée *myVM* dans le groupe de ressources *myResourceGroup*, puis affiche les informations du plan d’achat.

```azurepowershell-interactive
$vm = Get-azvm `
   -ResourceGroupName myResourceGroup `
   -Name myVM
$vm.Plan
```

Si vous n’avez pas obtenu les informations du plan avant la suppression de la machine virtuelle d’origine, vous pouvez effectuer une [demande de support](https://ms.portal.azure.com/#create/Microsoft.Support). Les techniciens de support auront besoin du nom de la machine virtuelle, de l’ID d’abonnement et de l’horodatage de l’opération de suppression.

Pour créer une machine virtuelle à l’aide d’un VHD, consultez l’article [Créer une machine virtuelle à partir d’un VHD spécialisé](create-vm-specialized.md) et ajoutez une ligne pour ajouter les informations du plan à la configuration de la machine virtuelle à l’aide de [Set-AzVMPlan](/powershell/module/az.compute/set-azvmplan), comme ceci :

```azurepowershell-interactive
$vmConfig = Set-AzVMPlan `
   -VM $vmConfig `
   -Publisher "publisherName" `
   -Product "productName" `
   -Name "planName"
```


## <a name="next-steps"></a>Étapes suivantes

Pour créer rapidement une machine virtuelle avec la cmdlet `New-AzVM` en utilisant des informations d’image de base, consultez [Créer une machine virtuelle Windows avec PowerShell](quick-create-powershell.md).

Pour plus d’informations sur l’utilisation d’images personnalisées de la Place de marché dans une galerie Azure Compute Gallery (auparavant appelée Shared Image Gallery), consultez [Spécifier des informations de plan d’achat de la Place de marché Azure lors de la création d’images](../marketplace-images.md).
