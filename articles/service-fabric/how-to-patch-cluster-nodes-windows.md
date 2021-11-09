---
title: Corriger le système d’exploitation Windows dans votre cluster Service Fabric
description: Voici comment activer les mises à niveau automatiques de l’image du système d’exploitation pour corriger les nœuds de cluster Service Fabric s’exécutant sur Windows.
ms.topic: how-to
ms.date: 10/19/2021
ms.openlocfilehash: 940a0af02f5355e9d28bb057798004fc963dfeca
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131019397"
---
# <a name="patch-the-windows-operating-system-in-your-service-fabric-cluster"></a>Corriger le système d’exploitation Windows dans votre cluster Service Fabric

L’obtention de [mises à niveau automatiques de l’image du système d’exploitation sur Virtual Machine Scale Sets](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md) est la meilleure façon d’assurer la mise à jour corrective de votre système d’exploitation dans Azure. Le groupe de machines virtuelles identiques basé sur des mises à niveau d’images de système d’exploitation automatiques nécessitent une durabilité Silver ou supérieure sur un groupe identique.

## <a name="requirements-for-automatic-os-image-upgrades-by-virtual-machine-scale-sets"></a>Conditions requises pour les mises à niveau automatiques de l’image du système d’exploitation par Virtual Machine Scale Sets

- Le [niveau de durabilité](service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster) de Service Fabric est Silver ou Gold, et non Bronze.
- L’extension Service Fabric sur la définition du modèle de groupe identique doit avoir TypeHandlerVersion 1.1 ou version ultérieure.
- Le niveau de durabilité doit être identique au cluster Service Fabric et à l’extension de Service Fabric de la définition du modèle de groupe identique.
- Une sonde d’intégrité supplémentaire ou l’utilisation de l’extension d’intégrité d’application pour Virtual Machine Scale Sets n’est pas nécessaire.
- Les types de nœuds sans état sont la seule exception, avec une durabilité Bronze, mais les mises à niveau automatiques de l’image du système d’exploitation peuvent toujours y être configurées. Pour plus d’informations, consultez [Déployer un cluster Azure Service Fabric avec des types de nœuds sans état](service-fabric-stateless-node-types.md).

Assurez-vous que les paramètres de durabilité ne sont pas incompatibles avec le cluster Service Fabric et l’extension Service Fabric, car une incompatibilité entraînera des erreurs de mise à niveau. Les niveaux de durabilité peuvent être modifiés selon les instructions indiquées sur[cette page](service-fabric-cluster-capacity.md#changing-durability-levels).

Avec la durabilité Bronze, la mise à jour automatique de l'image du système d'exploitation n'est pas disponible. Bien que l'[application Patch Orchestration](service-fabric-patch-orchestration-application.md) (destinée uniquement aux clusters hébergés non-Azure) *ne soit pas recommandée* pour les niveaux de durabilité Argent ou supérieurs, il s’agit de votre seule option pour automatiser les mises à jour Windows en ce qui concerne les domaines de mise à niveau de Service Fabric.

Si vous souhaitez passer de l’application Patch Orchestration à la mise à niveau automatique de l’image du système d’exploitation, vous devez d’abord interrompre l’utilisation de l’application Patch Orchestration Application.

## <a name="enable-auto-os-upgrades-and-disable-windows-update"></a>Activer les mises à niveau automatiques du système d’exploitation et désactivez Windows Update

Lorsque vous activez les mises à jour automatiques du système d’exploitation, vous devez également désactiver Windows Update dans le modèle de déploiement. Une fois ces modifications déployées, toutes les machines du groupe identique sont réinitialisées et le groupe identique est activé pour les mises à jour automatiques.

> [!IMPORTANT]
> Service Fabric ne prend pas en charge les mises à niveau dans une machine virtuelle où « Windows Update » applique les correctifs du système d’exploitation sans remplacer le disque du système d’exploitation.


1. Activez les mises à niveau automatiques de l’image du système d’exploitation et désactivez Windows Update dans le modèle de déploiement :
 
    ```json
    "virtualMachineProfile": { 
        "properties": {
          "upgradePolicy": {
            "automaticOSUpgradePolicy": {
              "enableAutomaticOSUpgrade":  true
            }
          }
        }
      }
    ```
    
    ```json
    "virtualMachineProfile": { 
        "osProfile": { 
            "windowsConfiguration": { 
                "enableAutomaticUpdates": false 
            }
        }
    }
    ```

    ```azurepowershell-interactive
    Update-AzVmss -ResourceGroupName $resourceGroupName -VMScaleSetName $scaleSetName -AutomaticOSUpgrade $true -EnableAutomaticUpdate $false
    ```

1. Mettre à jour le modèle de groupe identique. Après ce changement de configuration, une réinitialisation de toutes les machines est nécessaire pour mettre à jour le modèle de groupe identique, afin que le changement soit pris en compte.

    ```azurepowershell-interactive
    $scaleSet = Get-AzVmssVM -ResourceGroupName $resourceGroupName -VMScaleSetName $scaleSetName
    $instances = foreach($vm in $scaleSet)
    {
        Set-AzVmssVM -ResourceGroupName $resourceGroupName -VMScaleSetName $scaleSetName -InstanceId $vm.InstanceID -Reimage
    }
    ```

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment activer les [mises à niveau automatiques de l’image du système d’exploitation sur Virtual Machine Scale Sets](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md).