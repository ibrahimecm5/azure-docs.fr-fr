---
title: Créer une machine virtuelle à partir d’une image généralisée dans une galerie
description: Créez une machine virtuelle à partir d’une image généralisée dans une galerie.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 08/31/2021
ms.author: cynthn
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: a9b1dc77eb8eaae9e634cb362beed527a74a84be
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131474512"
---
# <a name="create-a-vm-from-a-generalized-image-version"></a>Créer une machine virtuelle à partir d’une version d’image généralisée

Créez une machine virtuelle à partir d’une [version d’image généralisée](./shared-image-galleries.md#generalized-and-specialized-images) stockée dans une galerie Azure Compute Gallery (anciennement Shared Image Gallery). Si vous souhaitez créer une machine virtuelle en utilisant une image spécialisée, consultez [Créer une machine virtuelle à partir d’une image spécialisée](vm-specialized-image-version.md). 


### <a name="portal"></a>[Portail](#tab/portal)

Vous pouvez maintenant créer une ou plusieurs machines virtuelles. Cet exemple crée une machine virtuelle nommée *myVM*, dans le groupe *myResourceGroup* du centre de données *USA Est*.

1. Accédez à la définition de votre image. Vous pouvez utiliser le filtre de ressources pour afficher toutes les définitions d’images disponibles.
1. Dans la page de définition de votre image, sélectionnez **Créer une machine virtuelle** dans le menu situé en haut.
1. Pour **Groupe de ressources**, sélectionnez **Créer** et entrez *myResourceGroup* comme nom.
1. Dans **Nom de la machine virtuelle**, tapez *myVM*.
1. Pour **Région**, sélectionnez *USA Est*.
1. Pour **Options de disponibilité**, conservez la valeur par défaut *Aucune redondance d’infrastructure nécessaire*.
1. La valeur sous **Image** est automatiquement renseignée avec la version d’image `latest` si vous avez démarré à partir de la page de la définition d’image.
1. Pour **Taille**, choisissez une taille de machine virtuelle dans la liste des tailles disponibles, puis **Sélectionner**.
1. Sous **Compte d’administrateur**, vous devez fournir un nom d’utilisateur (par exemple, *azureuser*) et un mot de passe ou une clé SSH. Le mot de passe doit contenir au moins 12 caractères et satisfaire aux [exigences de complexité définies](./windows/faq.yml#what-are-the-password-requirements-when-creating-a-vm-).
1. Si vous souhaitez autoriser l’accès à distance à la machine virtuelle, sous **Ports d’entrée publics**, choisissez **Autoriser les ports sélectionnés**, puis sélectionnez **SSH (22)** ou **RDP (3389)** dans la liste déroulante. Si vous ne souhaitez pas autoriser l’accès à distance à la machine virtuelle, conservez la sélection **Aucun** pour **Ports d’entrée publics**.
1. Lorsque vous avez terminé, sélectionnez le bouton **Vérifier + créer** en bas de la page.
1. Une fois la machine virtuelle validée, sélectionnez **Créer** en bas de la page pour démarrer le déploiement.

### <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/cli)


Répertoriez les définitions d’images d’une galerie avec la commande [az sig image-definition list](/cli/azure/sig/image-definition#az_sig_image_definition_list) pour voir le nom et l’ID des définitions.

```azurecli-interactive 
resourceGroup=myGalleryRG
gallery=myGallery
az sig image-definition list --resource-group $resourceGroup --gallery-name $gallery --query "[].[name, id]" --output tsv
```

Créez une machine virtuelle à l’aide de la commande [az vm create](/cli/azure/vm#az_vm_create). Pour utiliser la version la plus récente de l’image, définissez `--image` sur l’ID de la définition d’image. 

L’exemple ci-dessous est destiné à la création d’une image VMsecured Linux avec SSH. Pour Windows ou pour sécuriser une machine virtuelle Linux avec un mot de passe, supprimez `--generate-ssh-keys` pour être invité à entrer un mot de passe. Si vous souhaitez fournir un mot de passe directement, remplacez `--generate-ssh-keys` par `--admin-password`. Remplacez les noms de ressources en fonction des besoins dans cet exemple. 

```azurecli-interactive 
imgDef="/subscriptions/<subscription ID where the gallery is located>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition"
vmResourceGroup=myResourceGroup
location=eastus
vmName=myVM
adminUsername=azureuser


az group create --name $vmResourceGroup --location $location

az vm create\
   --resource-group $vmResourceGroup \
   --name $vmName \
   --image $imgDef \
   --admin-username $adminUsername \
   --generate-ssh-keys
```

Vous pouvez également utiliser une version spécifique en utilisant l’ID de version de l’image pour le paramètre `--image`. Par exemple, pour utiliser la version de l’image *1.0.0* tapez : `--image "/subscriptions/<subscription ID where the gallery is located>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition/versions/1.0.0"`.

### <a name="powershell"></a>[PowerShell](#tab/powershell)

Une fois que vous disposez d’une version d’image généralisée, vous pouvez créer une ou plusieurs machines virtuelles. Utilisation de l’applet de commande [New-AzVM](/powershell/module/az.compute/new-azvm). 

Dans cet exemple, nous utilisons l’ID de définition d’image pour nous assurer que la nouvelle machine virtuelle utilise la version la plus récente d’une image. Vous pouvez également utiliser une version spécifique en utilisant l’ID de version de l’image pour `Set-AzVMSourceImage -Id`. Par exemple, pour utiliser la version de l’image *1.0.0* tapez : `Set-AzVMSourceImage -Id "/subscriptions/<subscription ID where the gallery is located>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition/versions/1.0.0"`. 

Souvenez-vous que l’utilisation d’une version d’image spécifique signifie que l’automatisation peut échouer si cette version d’image spécifique n’est pas disponible car elle a été effacée ou supprimée de la région. Nous vous recommandons d’utiliser l’ID de définition d’image pour créer votre machine virtuelle, sauf si une version d’image spécifique est requise.

Remplacez les noms de ressources en fonction des besoins dans ces exemples. 

**Jeu de paramètres simplifié**

Vous pouvez utiliser le jeu de paramètres simplifié défini pour créer rapidement une machine virtuelle à partir d’une image. Le jeu de paramètres simplifié utilise le nom de la machine virtuelle pour créer automatiquement certaines des ressources requises, telles qu’un réseau virtuel et une adresse IP publique pour vous. 

```azurepowershell-interactive
# Create some variables for the new VM 
$resourceGroup = "myResourceGroup"
$location = "South Central US"
$vmName = "myVMfromImage"

# Get the image. Replace the name of your resource group, gallery, and image definition. This will create the VM from the latest image version available.

$imageDefinition = Get-AzGalleryImageDefinition `
   -GalleryName myGallery `
   -ResourceGroupName myResourceGroup `
   -Name myImageDefinition

# Create user object
$cred = Get-Credential `
   -Message "Enter a username and password for the virtual machine."

# Create a resource group
New-AzResourceGroup `
   -Name $resourceGroup `
   -Location $location

New-AzVM `
   -ResourceGroupName $resourceGroup `
   -Location $location `
   -Name $vmName `
   -Image $imageDefinition.Id
   -Credential $cred
```



**Jeu de paramètres complet**

Vous pouvez créer une machine virtuelle à l’aide de ressources spécifiques en utilisant jeu de paramètres complet.

```azurepowershell-interactive
# Create some variables for the new VM 
$resourceGroup = "myResourceGroup"
$location = "South Central US"
$vmName = "myVMfromImage"

# Get the image. Replace the name of your resource group, gallery, and image definition. This will create the VM from the latest image version available.

$imageDefinition = Get-AzGalleryImageDefinition `
   -GalleryName myGallery `
   -ResourceGroupName myResourceGroup `
   -Name myImageDefinition

# Create user object
$cred = Get-Credential `
   -Message "Enter a username and password for the virtual machine."

# Create a resource group
New-AzResourceGroup `
   -Name $resourceGroup `
   -Location $location

# Network pieces
$subnetConfig = New-AzVirtualNetworkSubnetConfig `
   -Name mySubnet `
   -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork `
   -ResourceGroupName $resourceGroup `
   -Location $location `
   -Name MYvNET `
   -AddressPrefix 192.168.0.0/16 `
   -Subnet $subnetConfig
$pip = New-AzPublicIpAddress `
   -ResourceGroupName $resourceGroup `
   -Location $location `
  -Name "mypublicdns$(Get-Random)" `
  -AllocationMethod Static `
  -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig `
   -Name myNetworkSecurityGroupRuleRDP  `
   -Protocol Tcp `
  -Direction Inbound `
   -Priority 1000 `
   -SourceAddressPrefix * `
   -SourcePortRange * `
   -DestinationAddressPrefix * `
   -DestinationPortRange 3389 `
   -Access Allow
$nsg = New-AzNetworkSecurityGroup `
   -ResourceGroupName $resourceGroup `
   -Location $location `
  -Name myNetworkSecurityGroup `
  -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface `
   -Name myNic `
   -ResourceGroupName $resourceGroup `
   -Location $location `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id `
  -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration using $imageDefinition.Id to use the latest image version.
$vmConfig = New-AzVMConfig `
   -VMName $vmName `
   -VMSize Standard_D1_v2 | `
   Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred | `
   Set-AzVMSourceImage -Id $imageDefinition.Id | `
   Add-AzVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzVM `
   -ResourceGroupName $resourceGroup `
   -Location $location `
   -VM $vmConfig
```

### <a name="rest"></a>[REST](#tab/rest)

Crée un réseau virtuel.

```rest
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/virtualNetworks/{vNetName}?api-version=2020-05-01

{
    "properties": {
        "addressSpace": {
            "addressPrefixes": [
                "10.0.0.0/16"
            ]
        }
    },
    "location": "eastus"
}
```

Créez un sous-réseau.

```rest
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/virtualNetworks/{vNetName}/subnets/{subnetName}?api-version=2020-05-01

{
    "properties": {
        "addressPrefix": "10.0.0.0/16"
    },
}
```

Créez une adresse IP publique.

```rest
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/publicIPAddresses/{pIPName}?api-version=2020-11-01

{
  "location": "eastus"
}

```

Créer des groupes de sécurité réseau

```rest
# @name vmNSG
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}?api-version=2020-11-01

{
  "properties": {
    "securityRules": [
      {
        "name": "AllowSSH",
        "properties": {
          "protocol": "Tcp",
          "sourceAddressPrefix": "*",
          "destinationAddressPrefix": "*",
          "access": "Allow",
          "destinationPortRange": "3389",
          "sourcePortRange": "*",
          "priority": 1000,
          "direction": "Inbound"
        }
      }
    ]
  },
  "location": "eastus"
}
```

Créez une carte réseau.

```rest
# @name vmNIC
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkInterfaces/{nicName}?api-version=2020-05-01

{
    "properties": {
        "enableAcceleratedNetworking": true,
        "networkSecurityGroup": {
          "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}"
        },
        "ipConfigurations": [
            {
                "name": "ipconfig1",
                "properties": {
                    "subnet": {
                        "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/virtualNetworks/{vNetName}/subnets/{subNetName}",
                    },
                    "publicIPAddress": {
                        "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/publicIPAddresses/{pipName}"
          }
                }
            }
        ]
    },
    "location": "eastus",
}
```
Créez une machine virtuelle Linux. La section `oSProfile` contient des détails spécifiques au système d’exploitation. Consultez l’exemple de code suivant pour la syntaxe Windows.

```rest
# @name vm
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}?api-version=2020-06-01

{
    "location": "eastus",
    "properties": {
        "hardwareProfile": {
            "vmSize": "Standard_DS3_v2"
        },
        "storageProfile": {
            "imageReference": {
                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/galleries/{galleryName}/images/{galleryImageName}/versions/{versionNumber}"
            },
            "osDisk": {
                "caching": "ReadWrite",
                "managedDisk": {
                    "storageAccountType": "Standard_LRS"
                },
                "createOption": "FromImage"
            }
        },
        "osProfile": {
            "adminUsername": "{your-username}",
            "computerName": "myVM",
            "linuxConfiguration": {
                "ssh": {
                    "publicKeys": [
                        {
                            "path": "/home/{your-username}/.ssh/authorized_keys",
                            "keyData": "{sshKey}",
                        }
                    ]
                },
                "disablePasswordAuthentication": true
            }
        },
        "networkProfile": {
            "networkInterfaces": [
                {
                    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkInterfaces/{nicName}",
                }
            ]
        }
    },
}

```

Créer une machine virtuelle Windows

```rest
# @name vm
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}?api-version=2020-06-01

{
    "location": "eastus",
    "properties": {
        "hardwareProfile": {
            "vmSize": "Standard_DS3_v2"
        },
        "storageProfile": {
            "imageReference": {
                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/galleries/{galleryName}/images/{galleryImageName}/versions/{versionNumber}"
            },
            "osDisk": {
                "caching": "ReadWrite",
                "managedDisk": {
                    "storageAccountType": "Standard_LRS"
                },
                "createOption": "FromImage"
            }
        },
        "osProfile": {
            "adminUsername": "{your-username}",
            "computerName": "myVM",
            "adminPassword": "{your-password}"
        },
        "networkProfile": {
            "networkInterfaces": [
                {
                    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkInterfaces/{nicName}",
                }
            ]
        }
    },
```

---

**Étapes suivantes**

[Générateur d’image Azure (préversion)](./image-builder-overview.md) permet d’automatiser la création d’une version de l’image. Vous pouvez même l’utiliser pour mettre à jour et [créer une nouvelle version de l’image à partir d’une image existante](./linux/image-builder-gallery-update-image-version.md).