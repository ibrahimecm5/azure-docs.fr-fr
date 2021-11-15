---
title: Créer une image personnalisée à partir d’un fichier de disque dur virtuel à l’aide d’Azure PowerShell
description: Automatisez la création d’une image personnalisée dans Azure DevTest Labs à partir d’un fichier de disque dur virtuel à l’aide de PowerShell.
ms.topic: how-to
ms.date: 10/24/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2185309194b04d1b76ca84daea19e92e7c017463
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131469038"
---
# <a name="create-a-custom-image-from-a-vhd-file-with-powershell"></a>Créer une image personnalisée à partir d’un fichier de disque dur virtuel avec PowerShell

[!INCLUDE [devtest-lab-create-custom-image-from-vhd-selector](../../includes/devtest-lab-create-custom-image-from-vhd-selector.md)]

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

[!INCLUDE [devtest-lab-upload-vhd-options](../../includes/devtest-lab-upload-vhd-options.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="powershell-steps"></a>étapes PowerShell

La procédure suivante décrit comment créer une image personnalisée à partir d’un fichier de disque dur virtuel à l’aide d’Azure PowerShell :

1. Lors d’une invite de commandes PowerShell, connectez-vous à votre compte Azure avec la cmdlet **Connect-AzAccount** :

   ```powershell
   Connect-AzAccount
   ```

1. Sélectionnez votre abonnement Azure avec la cmdlet **Select-AzSubscription**. Remplacez \<subscription ID> par votre ID GUID d’abonnement.

   ```powershell
   $subscriptionId = '<subscription ID>'
   Select-AzSubscription -SubscriptionId $subscriptionId
   ```

1. Récupérez l’objet de laboratoire en appelant l’applet de commande **Get-AzResource**. Remplacez les espaces réservés \<lab resource group name> et \<lab name> par les noms de votre propre groupe de ressources et de votre labo.

   ```powershell
   $labRg = '<lab resource group name>'
   $labName = '<lab name>'
   $lab = Get-AzResource -ResourceId ('/subscriptions/' + $subscriptionId + '/resourceGroups/' + $labRg + '/providers/Microsoft.DevTestLab/labs/' + $labName)
   ```

1. Remplacez l’espace réservé à la variable **$vhdUri** par l’URI de votre fichier VHD chargé. Vous pouvez récupérer l’URI du fichier VHD à partir de sa page blob dans le compte de stockage du labo dans le Portail Azure. Voici un exemple d’URI de disque dur virtuel : `https://acontosolab1234.blob.core.windows.net/uploads/myvhd.vhd`.

   ```powershell
   $vhdUri = '<VHD URI>'
   ```

1. Créez l’image personnalisée à l’aide de la cmdlet **New-AzResourceGroupDeployment**. Remplacez les espaces réservés \<custom image name> et \<custom image description> par le nom et la description de votre choix.

   ```powershell
   $customImageName = '<custom image name>'
   $customImageDescription = '<custom image description>'

   $parameters = @{existingLabName="$($lab.Name)"; existingVhdUri=$vhdUri; imageOsType='windows'; isVhdSysPrepped=$false; imageName=$customImageName; imageDescription=$customImageDescription}

   New-AzResourceGroupDeployment -ResourceGroupName $lab.ResourceGroupName -Name CreateCustomImage -TemplateUri 'https://raw.githubusercontent.com/Azure/azure-devtestlab/master/samples/DevTestLabs/QuickStartTemplates/201-dtl-create-customimage-from-vhd/azuredeploy.json' -TemplateParameterObject $parameters
   ```

## <a name="complete-powershell-script"></a>Compléter le script PowerShell

La combinaison des étapes précédentes produit le script PowerShell suivant qui crée une image personnalisée à partir d’un fichier VHD. Pour utiliser le script, remplacez les espaces réservés suivants par vos propres valeurs :

- \<subscription ID>
- \<lab resource group name>
- \<lab name>
- \<VHD URI>
- \<custom image name>
- \<custom image description>

```powershell
# Log in to your Azure account.
Connect-AzAccount

# Select the desired Azure subscription.
$subscriptionId = '<subscription ID>'
Select-AzSubscription -SubscriptionId $subscriptionId

# Get the lab object.
$labRg = '<lab resource group name>'
$labName = '<lab name>'
$lab = Get-AzResource -ResourceId ('/subscriptions/' + $subscriptionId + '/resourceGroups/' + $labRg + '/providers/Microsoft.DevTestLab/labs/' + $labName)

# Set the URI of the VHD file.
$vhdUri = '<VHD URI>'

# Set the custom image name and description values.
$customImageName = '<custom image name>'
$customImageDescription = '<custom image description>'

# Set up the parameters object.
$parameters = @{existingLabName="$($lab.Name)"; existingVhdUri=$vhdUri; imageOsType='windows'; isVhdSysPrepped=$false; imageName=$customImageName; imageDescription=$customImageDescription}

# Create the custom image.
New-AzResourceGroupDeployment -ResourceGroupName $lab.ResourceGroupName -Name CreateCustomImage -TemplateUri 'https://raw.githubusercontent.com/Azure/azure-devtestlab/master/samples/DevTestLabs/QuickStartTemplates/201-dtl-create-customimage-from-vhd/azuredeploy.json' -TemplateParameterObject $parameters
```

## <a name="next-steps"></a>Étapes suivantes

- [Comparer les images personnalisées et les formules dans DevTest Labs](devtest-lab-comparing-vm-base-image-types.md)
- [Copying Custom Images between Azure DevTest Labs (Copie d’images personnalisées entre plusieurs Azure DevTest Labs)](https://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)
