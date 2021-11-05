---
title: Codes d’erreur pour les instances de groupes de machines virtuelles identiques et les machines virtuelles Azure Spot
description: Découvrez les codes d’erreur que vous pouvez voir lorsque vous utilisez des instances de groupes de machines virtuelles identiques et des machines virtuelles Azure Spot.
author: cynthn
ms.service: virtual-machines
ms.subservice: spot
ms.workload: infrastructure-services
ms.topic: troubleshooting
ms.date: 03/25/2020
ms.author: cynthn
ms.openlocfilehash: b38730604072fdf61c551ade8e147e18a487a160
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131082743"
---
# <a name="error-messages-for-azure-spot-virtual-machines-and-scale-sets"></a>Messages d’erreur pour les groupes de machines virtuelles identiques et les machines virtuelles Azure Spot

**S’applique à :** :heavy_check_mark: Machines virtuelles Linux :heavy_check_mark: Machines virtuelles Windows :heavy_check_mark: Groupes identiques flexibles :heavy_check_mark: Groupes identiques uniformes

Voici quelques codes d’erreur que vous pouvez recevoir lorsque vous utilisez des groupes de machines virtuelles identiques et des machines virtuelles Azure Spot.


| Clé | Message | Description |
|-----|---------|-------------|
| SkuNotAvailable | Le niveau demandé pour la ressource « \<resource\> » n’est pas disponible dans l’emplacement « \<location\> » pour l’abonnement « \<subscriptionID\> ». Essayez un autre niveau ou déployez sur un autre emplacement. | Il n’y a pas assez de capacité de machines virtuelles Azure Spot sur cet emplacement pour créer votre machine virtuelle ou votre instance de groupe de machines virtuelles identiques. |
| EvictionPolicyCanBeSetOnlyOnAzureSpotVirtualMachines  |  Une stratégie d’éviction peut être définie seulement sur des machines virtuelles Azure Spot. | Cette machine virtuelle n’étant pas une machine virtuelle Azure Spot, vous ne pouvez pas définir la stratégie d’éviction. |
| AzureSpotVMNotSupportedInAvailabilitySet  |  Les machines virtuelles Azure Spot ne sont pas prises en charge dans les groupes à haute disponibilité. | Vous devez choisir d’utiliser une machine virtuelle Azure Spot ou une machine virtuelle dans un groupe à haute disponibilité ; vous ne pouvez pas choisir les deux. |
| AzureSpotFeatureNotEnabledForSubscription  |  L’abonnement n’est pas activé avec la fonctionnalité de machine virtuelle Azure Spot. | Utilisez un abonnement qui prend en charge les machines virtuelles Azure spot. |
| VMPriorityCannotBeApplied  |  Impossible d’appliquer la valeur de priorité spécifiée « {0} » à la machine virtuelle « {1} », car aucune priorité n’a été spécifiée à la création de la machine virtuelle. | Spécifiez la priorité lors de la création de la machine virtuelle. |
| SpotPriceGreaterThanProvidedMaxPrice  |  Impossible d’effectuer l’opération « {0} », car le prix maximal (« {1} USD ») fourni est inférieur au prix Spot actuel (« {2} USD ») pour la taille de machine virtuelle Azure Spot (« {3} »). | Sélectionnez un prix maximal supérieur. Pour plus d’informations, consultez les informations sur les prix pour [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) ou [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/).|
| MaxPriceValueInvalid  |  Valeur du prix maximal non valide. Les seules valeurs prises en charge pour le prix maximal sont -1 ou un chiffre décimal supérieur à zéro. Une valeur de prix maximal égale à -1 indique que la machine virtuelle Azure Spot n’est pas supprimée pour des raisons de prix. | Entrez un prix maximal valide. Pour plus d’informations, consultez les prix pour [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) ou [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). |
| MaxPriceChangeNotAllowedForAllocatedVMs | Il n’est pas autorisé de changer le prix maximal quand la machine virtuelle « {0} » est allouée. Désallouez-la et réessayez. | Arrêtez\désallouez la machine virtuelle afin que vous puissiez changer de prix maximal. |
| MaxPriceChangeNotAllowed | Il n’est pas autorisé de changer le prix maximal. | Vous ne pouvez pas changer de prix maximal pour cette machine virtuelle. |
| AzureSpotIsNotSupportedForThisAPIVersion  |  La machine virtuelle Azure Spot n’est pas prise en charge pour cette version d’API. | La version d’API doit être 2019-03-01. |
| AzureSpotIsNotSupportedForThisVMSize  |  La machine virtuelle Azure Spot n’est pas prise en charge pour cette taille de machine virtuelle {0}. | Sélectionnez une autre taille de machine virtuelle. Pour plus d’informations, consultez [Machines virtuelles Azure Spot](./spot-vms.md). |
| MaxPriceIsSupportedOnlyForAzureSpotVirtualMachines  |  Le prix maximal est pris en charge uniquement pour les machines virtuelles Azure Spot. | Pour plus d’informations, consultez [Machines virtuelles Azure Spot](./spot-vms.md). |
| MoveResourcesWithAzureSpotVMNotSupported  |  La demande de déplacement de ressources contient une machine virtuelle Azure Spot. Non pris en charge. Consultez les détails de l’erreur pour connaître les ID des machines virtuelles. | Vous ne pouvez pas déplacer des machines virtuelles Azure spot. |
| MoveResourcesWithAzureSpotVmssNotSupported  |  La demande de déplacement de ressources contient un groupe de machines virtuelles identiques Azure Spot. Non pris en charge. Consultez les détails de l’erreur pour connaître les ID des groupes de machines virtuelles identiques. | Vous ne pouvez pas déplacer des instances de groupe de machines virtuelles Azure spot. |
| AzureSpotVMNotSupportedInVmssWithVMOrchestrationMode | Les machines virtuelles Azure Spot ne sont pas prises en charge dans les groupes de machines virtuelles identiques avec le mode d’orchestration de machine virtuelle. | Définissez le mode d’orchestration sur le groupe de machines virtuelles identiques pour utiliser des instances de groupe de machines virtuelles Azure Spot. |
| SpotRestorationIsNotSupportedForThisAPIVersion | La fonctionnalité de restauration par spots n’est pas prise en charge pour cette version d’API. |  Pour un groupe identique existant, effectuez un PATCH à l’aide de l’API version 2021-07-01 ou ultérieure. <br><br> Pour les nouveaux déploiements de groupes identiques, ajoutez la propriété suivante au modèle Azure Resource Manager à l’aide de l’API version 2021-07-01 ou ultérieure : <br><br> :::image type="content" source="media/spot/spot-try-restore-error-codes-1.png" alt-text="Exemple de code d’erreur pour utiliser la version d’API correcte.":::| 
| SpotRestorationIsSupportedOnlyForAzureSpotScaleSets | La fonctionnalité de restauration par spots est prise en charge uniquement pour les groupes de machines virtuelles spot identiques Azure. | La fonctionnalité de restauration par spots est prise en charge uniquement pour les groupes de machines virtuelles spot identiques Azure. Pour utiliser cette fonctionnalité, déployez Azure Spot à l’aide des groupes de machines virtuelles identiques. | 


**Étapes suivantes** Pour plus d’informations, consultez [Machines virtuelles Spot](./spot-vms.md).
