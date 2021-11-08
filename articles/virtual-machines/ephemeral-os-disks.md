---
title: Disques de système d’exploitation éphémères
description: En savoir plus sur les disques de système d’exploitation éphémères pour les machines virtuelles Azure.
author: Aarthi-Vijayaraghavan
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 07/23/2020
ms.author: aarthiv
ms.subservice: disks
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6ab8c0d7d1e547d564ddc3329858ddc49d51185b
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131448972"
---
# <a name="ephemeral-os-disks-for-azure-vms"></a>Disques de système d’exploitation éphémères pour les machines virtuelles Azure

**S’applique à :** :heavy_check_mark: Machines virtuelles Linux :heavy_check_mark: Machines virtuelles Windows :heavy_check_mark: Groupes identiques flexibles :heavy_check_mark: Groupes identiques uniformes

Les disques de système d’exploitation éphémères sont créés sur le stockage local de la machine virtuelle (VM) et ne sont pas enregistrés dans le Stockage Azure à distance. Les disques de système d’exploitation éphémères conviennent particulièrement bien aux charges de travail sans état, car les applications tolèrent les défaillances individuelles des machines virtuelles, mais sont plus sensibles au temps de déploiement et à la réinitialisation des instances individuelles de machines virtuelles. Comparé à un disque de système d’exploitation standard, un disque éphémère offre une latence plus faible pour les opérations de lecture/écriture et permet une réinitialisation plus rapide des machines virtuelles. 
 
Les principales caractéristiques des disques éphémères sont les suivantes : 
- Idéal pour les applications sans état.
- Prise en charge par la Place de marché, par les images personnalisées et par [Azure Compute Gallery](./shared-image-galleries.md) (anciennement appelé Shared Image Gallery).
- Capacité de réinitialisation rapide de leurs machines virtuelles et instances de groupe identique à l’état de démarrage d’origine.  
- Faible latence, comparable à celle d’un disque temporaire. 
- Les disques de système d’exploitation éphémères sont gratuits, c’est-à-dire qu’ils n’impliquent aucun coût de stockage.
- Disponible dans toutes les régions Azure.  

 
Différences clés entre les disques de système d’exploitation persistants et éphémères :

|                             | Disque de système d’exploitation persistant                          | Disque de système d’exploitation éphémère                              |
|-----------------------------|---------------------------------------------|------------------------------------------------|
| **Limite de taille du disque de système d’exploitation**      | 2 Tio                                                                                        | 2 Tio, ou la taille du cache correspondant à la taille de la machine virtuelle si elle est plus faible. Pour la **taille du cache en Gio**, consultez [DS](sizes-general.md), [ES](sizes-memory.md), [M](sizes-memory.md), [FS](sizes-compute.md), and [GS](sizes-previous-gen.md#gs-series)              |
| **Tailles de machines virtuelles prises en charge**          | Tous                                                                                          | Tailles de machine virtuelle prenant en charge le Stockage Premium, par exemple DSv1, DSv2, DSv3, Esv3, Fs, FsV2, GS, M, Mdsv2,Bs, Dav4 ou Eav4                                               |
| **Prise en charge du type de disque**           | Disque de système d’exploitation managé et non managé                                                                | Disque de système d’exploitation managé uniquement                                                               |
| **Prise en charge des régions**              | Toutes les régions                                                                                  | Toutes les régions                              |
| **Persistance des données**            | Les données écrites sur le disque de système d’exploitation sont stockées dans le Stockage Azure                                  | Les données écrites sur le disque de système d’exploitation sont stockées sur le stockage local de la machine virtuelle et ne sont pas persistantes dans le Stockage Azure. |
| **État arrêté/libéré**      | Les machines virtuelles et instances de groupe identique peuvent être arrêtées-libérées et redémarrées à partir de l’état arrêté-libéré | Les machines virtuelles et instances de groupe identique ne peuvent pas être arrêtées-libérées                                  |
| **Prise en charge des disques de système d’exploitation spécialisés** | Oui                                                                                          | Non                                                                                 |
| **Redimensionnement du disque de système d’exploitation**              | Pris en charge durant la création de la machine virtuelle et une fois que la machine virtuelle est arrêtée-libérée                                | Prise en charge lors de la création d’une machine virtuelle uniquement                                                  |
| **Redimensionnement des machines virtuelles**   | Les données du disque de système d’exploitation sont conservées                                                                    | Les données du disque de système d’exploitation sont supprimées, et le système d’exploitation est reprovisionné       
| **Emplacement du fichier d’échange**   | Pour Windows, le fichier d’échange est stocké sur le disque de ressources                                              | Sur Windows, le fichier d’échange est stocké sur le disque de système d’exploitation (pour le placement dans le cache du système d’exploitation comme sur le disque temporaire).   |

## <a name="size-requirements"></a>Exigences de taille

Vous pouvez choisir de déployer le disque de système d’exploitation éphémère dans le cache ou sur le disque temporaire de la machine virtuelle.
La taille du disque de système d’exploitation de l’image doit être inférieure ou égale à celle du disque temporaire ou du cache de la taille de machine virtuelle choisie.

Supposons par exemple que vous souhaitiez opter pour le **placement dans le cache du système d’exploitation**. La taille des images Windows Server standard de la place de marché est d’environ 127 Gio. Il vous faut donc une taille de machine virtuelle dont le cache est supérieur ou égal à 127 Gio. La référence Standard_DS3_v2 possède une taille de cache de 127 Gio, ce qui est suffisant. Dans ce cas, la référence Standard_DS3_v2 est la taille minimale de la série DSv2 que vous pouvez utiliser avec cette image. 

Supposons maintenant que vous souhaitiez opter pour le **placement sur le disque temporaire**. La taille de l’image du serveur Ubuntu standard de la place de marché est d’environ 30 Gio. Pour pouvoir activer le disque de système d’exploitation éphémère sur le disque temporaire, vous devez disposer d’un disque temporaire dont la taille est supérieure ou égale à 30 Gio. Standard_B4ms possède une taille de disque temporaire de 32 Gio, qui est adaptée au disque de système d’exploitation de 30 Gio. Lors de la création de la machine virtuelle, l’espace du disque temporaire sera de 2 Gio. 
> [!Important] 
> Si vous optez pour le placement sur le disque temporaire, la taille finale du disque temporaire est égale à la taille initiale du disque temporaire moins la taille de l’image de système d’exploitation.

Les images Linux Windows Server de base de la place de marché qui sont signalées par `[smallsize]` font généralement environ 30 Gio. Elles peuvent utiliser la plupart des tailles de machine virtuelle disponibles.
Les disques éphémères exigent également que la taille de machine virtuelle prenne en charge le **Stockage Premium**. Les tailles ont généralement (mais pas toujours) un `s` dans le nom, comme DSv2 et EsV3. Pour plus d’informations, consultez [Tailles de machine virtuelle Azure](sizes.md) pour plus d’informations sur les tailles prenant en charge le stockage Premium.

## <a name="ephemeral-os-disks-can-now-be-stored-on-tempresource-disks"></a>Possibilité de stocker les disques de système d’exploitation éphémères sur des disques temporaires ou des disques de ressources
Les disques de système d’exploitation éphémères peuvent maintenant être stockés dans un cache de disque ou sur un disque temporaire ou un disque de ressources de la machine virtuelle. Cette fonctionnalité permet de créer des disques de système d’exploitation éphémères pour toutes les machines virtuelles, qui ne possèdent pas de cache ou ne disposent pas d’un cache insuffisant (par exemple Dav3, Dav4, Eav4 et Eav3) mais comportent un disque temporaire suffisant pour héberger le disque de système d’exploitation éphémère.
[DiffDiskPlacement](/rest/api/compute/virtualmachines/list#diffdiskplacement) est la nouvelle propriété servant à spécifier l’emplacement du disque de système d’exploitation éphémère. Avec cette fonctionnalité, lorsqu’une machine virtuelle Windows est provisionnée, nous configurons le fichier d’échange pour qu’il se trouve sur le disque du système d’exploitation.

## <a name="portal"></a>Portail

Sur le Portail Azure, vous pouvez choisir d’utiliser des disques éphémères lors du déploiement d’une machine virtuelle ou de groupes de machines virtuelles identiques en ouvrant la section **Avancé** de l’onglet **Disques**. Concernant le placement du disque de système d’exploitation éphémère, sélectionnez **Placement dans le cache du système d’exploitation** ou **Placement sur le disque temporaire**.

![Capture d’écran montrant le bouton radio pour choisir d’utiliser un disque de système d’exploitation éphémère](./media/virtual-machines-common-ephemeral/ephemeral-portal-temp.png)
 

Si l’option permettant d’utiliser un disque éphémère ou de choisir le placement dans le cache du système d’exploitation ou sur le disque temporaire est grisée, vous avez peut-être sélectionné une taille de machine virtuelle qui ne possède pas une taille de cache ou de disque temporaire supérieure à l’image de système d’exploitation ou qui ne prend pas en charge le Stockage Premium. Revenez à la page **Notions de base** et essayez de choisir une autre taille de machine virtuelle.

## <a name="scale-set-template-deployment"></a>Déploiement de modèle de groupe identique  
Le processus de création d’un groupe identique utilisant un disque de système d’exploitation éphémère consiste à ajouter la propriété `diffDiskSettings` au type de ressource `Microsoft.Compute/virtualMachineScaleSets/virtualMachineProfile` dans le modèle. En outre, la stratégie de mise en cache doit être définie sur `ReadOnly` pour le disque de système d’exploitation éphémère. Vous pouvez remplacer le placement par `CacheDisk`, correspondant au cache du système d’exploitation.

```json
{ 
  "type": "Microsoft.Compute/virtualMachineScaleSets", 
  "name": "myScaleSet", 
  "location": "East US 2", 
  "apiVersion": "2019-12-01", 
  "sku": { 
    "name": "Standard_DS2_v2", 
    "capacity": "2" 
  }, 
  "properties": { 
    "upgradePolicy": { 
      "mode": "Automatic" 
    }, 
    "virtualMachineProfile": { 
       "storageProfile": { 
        "osDisk": { 
          "diffDiskSettings": { 
            "option": "Local" ,
            "placement": "ResourceDisk"
          }, 
          "caching": "ReadOnly", 
          "createOption": "FromImage" 
        }, 
        "imageReference":  { 
          "publisher": "Canonical", 
          "offer": "UbuntuServer", 
          "sku": "16.04-LTS", 
          "version": "latest" 
        } 
      }, 
      "osProfile": { 
        "computerNamePrefix": "myvmss", 
        "adminUsername": "azureuser", 
        "adminPassword": "P@ssw0rd!" 
      } 
    } 
  } 
}  
```

## <a name="vm-template-deployment"></a>Déploiement de modèle de machine virtuelle 
Vous pouvez déployer une machine virtuelle avec un disque de système d’exploitation éphémère à l’aide d’un modèle. Le processus de création d’une machine virtuelle utilisant des disques de système d’exploitation éphémères consiste à ajouter la propriété `diffDiskSettings` au type de ressource Microsoft.Compute/virtualMachines dans le modèle. En outre, la stratégie de mise en cache doit être définie sur `ReadOnly` pour le disque de système d’exploitation éphémère. Vous pouvez remplacer l’option de placement par `CacheDisk`, correspondant au cache du système d’exploitation.

```json
{ 
  "type": "Microsoft.Compute/virtualMachines", 
  "name": "myVirtualMachine", 
  "location": "East US 2", 
  "apiVersion": "2019-12-01", 
  "properties": { 
       "storageProfile": { 
            "osDisk": { 
              "diffDiskSettings": { 
                "option": "Local" ,
                "placement": "ResourceDisk"
              }, 
              "caching": "ReadOnly", 
              "createOption": "FromImage" 
            }, 
            "imageReference": { 
                "publisher": "MicrosoftWindowsServer", 
                "offer": "WindowsServer", 
                "sku": "2016-Datacenter-smalldisk", 
                "version": "latest" 
            }, 
            "hardwareProfile": { 
                 "vmSize": "Standard_DS2_v2" 
             } 
      }, 
      "osProfile": { 
        "computerNamePrefix": "myvirtualmachine", 
        "adminUsername": "azureuser", 
        "adminPassword": "P@ssw0rd!" 
      } 
    } 
 } 
```

> [!NOTE] 
> L’option de placement du disque de système d’exploitation éphémère (cache de disque, disque temporaire ou disque de ressources de la machine virtuelle) sera bientôt disponible sur PowerShell et l’interface CLI.

## <a name="powershell"></a>PowerShell
Pour utiliser un disque éphémère pour un déploiement de machine virtuelle avec PowerShell, utilisez [Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk) dans votre configuration de machine virtuelle. Définissez `-DiffDiskSetting` sur `Local` et `-Caching` sur `ReadOnly`.     
```powershell
Set-AzVMOSDisk -DiffDiskSetting Local -Caching ReadOnly
```
Pour les déploiements de groupes identiques, utilisez l’applet de commande [Set-AzVmssStorageProfile](/powershell/module/az.compute/set-azvmssstorageprofile) dans votre configuration. Définissez `-DiffDiskSetting` sur `Local` et `-Caching` sur `ReadOnly`.

```powershell
Set-AzVmssStorageProfile -DiffDiskSetting Local -OsDiskCaching ReadOnly
```

## <a name="cli"></a>Interface de ligne de commande

Pour utiliser un disque éphémère pour un déploiement de machine virtuelle par CLI, définissez le paramètre `--ephemeral-os-disk` dans [az vm create](/cli/azure/vm#az_vm_create) sur `true` et le paramètre `--os-disk-caching` sur `ReadOnly`.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --ephemeral-os-disk true \
  --os-disk-caching ReadOnly \
  --admin-username azureuser \
  --generate-ssh-keys
```

Pour les groupes identiques, vous utilisez le même paramètre `--ephemeral-os-disk true` pour [az-vmss-create](/cli/azure/vmss#az_vmss_create) et définissez le paramètre `--os-disk-caching` sur `ReadOnly`.

## <a name="reimage-a-vm-using-rest"></a>Réinitialiser une machine virtuelle à l’aide de REST
Il est possible de réinitialiser une instance de machine virtuelle avec un disque de système d’exploitation éphémère à l’aide de l’API REST (cf. ci-dessous) et sur le Portail Azure en accédant au volet Vue d’ensemble de la machine virtuelle. Pour les groupes identiques, cette opération est déjà disponible avec PowerShell, l’interface CLI et le portail.

```
POST https://management.azure.com/subscriptions/{sub-
id}/resourceGroups/{rgName}/providers/Microsoft.Compute/VirtualMachines/{vmName}/reimage?a pi-version=2019-12-01" 
```
 
## <a name="frequently-asked-questions"></a>Forum aux questions

**Q : Quelle est la taille des disques de système d’exploitation locaux ?**

R : Nous prenons en charge la plateforme, Shared Image Gallery et les images personnalisées, jusqu’à la taille du cache de la machine virtuelle pour le placement dans le cache du système d’exploitation et jusqu’à la taille du disque temporaire pour le placement sur le disque temporaire, où toutes les opérations de lecture/écriture sur le disque de système d’exploitation sont effectuées localement, sur le même nœud que la machine virtuelle. 

**Q : Le disque de système d’exploitation éphémère peut-il être redimensionné ?**

A : Non, une fois le disque de système d’exploitation éphémère configuré, il ne peut plus être redimensionné. 

**Q : Le placement du disque de système d’exploitation éphémère peut-il être modifié après la création de la machine virtuelle ?**

R : Non. Une fois le disque de système d’exploitation éphémère provisionné, son placement ne peut plus être modifié. Vous pouvez cependant recréer la machine virtuelle au moyen du déploiement de modèle ARM, de PowerShell ou de l’interface CLI en mettant à jour le placement du disque de système d’exploitation comme vous le souhaitez. Cette opération entraîne la récréation de la machine virtuelle, la suppression des données du disque de système d’exploitation et le reprovisionnement du système d’exploitation.

**Q : Un disque temporaire est-il créé si la taille de l’image est égale à la taille du disque temporaire de la taille de machine virtuelle sélectionnée ?**

R : Non. Dans ce cas, aucun lecteur de disque temporaire n’est créé.

**Q : Les disques de système d’exploitation éphémères sont-ils pris en charge sur les machines virtuelles basse priorité et les machines virtuelles spot ?**

A : Oui. Il n’existe aucune option d’arrêt-désallocation pour les machines virtuelles éphémères. Les utilisateurs doivent les supprimer au lieu de les désallouer.

**Q : Puis-je attacher un disque managé à une machine virtuelle éphémère ?**

A : Oui, vous pouvez attacher un disque de données managé à une machine virtuelle qui utilise un disque de système d’exploitation éphémère. 

**Q : Toutes les tailles de machine virtuelle sont-elles prises en charge par les disques de système d’exploitation éphémères ?**

A : Non, la plupart des tailles de machine virtuelle Stockage Premium sont prises en charge (DS, ES, FS, GS, M, etc.). Pour savoir si une taille de machine virtuelle particulière prend en charge les disques de système d’exploitation éphémères, vous pouvez :

Appelez la cmdlet PowerShell `Get-AzComputeResourceSku`.
```azurepowershell-interactive
 
$vmSizes=Get-AzComputeResourceSku | where{$_.ResourceType -eq 'virtualMachines' -and $_.Locations.Contains('CentralUSEUAP')} 

foreach($vmSize in $vmSizes)
{
   foreach($capability in $vmSize.capabilities)
   {
       if($capability.Name -eq 'EphemeralOSDiskSupported' -and $capability.Value -eq 'true')
       {
           $vmSize
       }
   }
}
```
 
**Q : Le disque de système d’exploitation éphémère peut-il être appliqué aux machines virtuelles et groupes identiques existants ?**

A : Non, le disque de système d’exploitation éphémère peut uniquement être utilisé au cours de la création de machines virtuelles et de groupes identiques. 

**Q : Peut-on combiner des disques de système d’exploitation normaux et éphémères dans un groupe identique ?**

A : Non, vous ne pouvez pas combiner des instances de disques de système d’exploitation éphémères et persistants dans le même groupe identique. 

**Q : Le disque de système d’exploitation éphémère peut-il être créé à l’aide de Powershell ou de l’interface CLI ?**

R: Oui. Vous pouvez créer des machines virtuelles avec un disque de système d’exploitation éphémère avec l’API REST, des modèles, PowerShell et l’interface CLI.

**Q : Quelles fonctionnalités ne sont pas prises en charge avec le disque de système d’exploitation éphémère ?**

A : Les disques éphémères ne prennent pas en charge :
- Capture des images de machine virtuelle
- Captures instantanées de disque 
- Azure Disk Encryption 
- Sauvegarde Azure
- Azure Site Recovery  
- Échange du disque de système d’exploitation 

> [!NOTE]
> 
> Le disque éphémère n’est pas accessible par le biais du portail. Vous recevez une erreur « Ressource introuvable » ou « 404 » si vous tentez d’y accéder. Cette erreur est attendue.
> 
 
## <a name="next-steps"></a>Étapes suivantes
Vous pouvez créer une machine virtuelle avec un disque de système d’exploitation éphémère à l’aide [d’Azure CLI](/cli/azure/vm#az_vm_create).