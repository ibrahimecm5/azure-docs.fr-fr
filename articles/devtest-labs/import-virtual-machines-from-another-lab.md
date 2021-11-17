---
title: Importer des machines virtuelles à partir d’un autre labo
description: Découvrez comment importer des machines virtuelles d’un laboratoire à un autre dans Azure DevTest Labs.
ms.topic: how-to
ms.date: 11/08/2021
ms.openlocfilehash: 50f17183caa7da86ec6704af35129ed8bd707d5a
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132059844"
---
# <a name="import-virtual-machines-from-one-lab-to-another"></a>Importer des machines virtuelles d’un laboratoire à un autre

Cet article explique et décrit comment importer des machines virtuelles d’un laboratoire DevTest Labs à un autre.

## <a name="scenarios"></a>Scénarios
Voici quelques scénarios où vous avez besoin d’importer des machines virtuelles d’un laboratoire à un autre :

- Un individu passe d’un groupe à un autre et souhaite faire transférer son bureau de développement au laboratoire de la nouvelle équipe.
- Le groupe a atteint un [quota au niveau de l’abonnement](../azure-resource-manager/management/azure-subscription-service-limits.md) et veut diviser les équipes en plusieurs abonnements Azure.
- L’entreprise passe à Azure ExpressRoute ou à une autre topologie réseau et l’équipe veut déplacer les machines virtuelles afin qu’elles utilisent cette nouvelle infrastructure.

## <a name="requirements-and-constraints"></a>Exigences et contraintes

Le processus d’importation importe les machines virtuelles du laboratoire source dans le laboratoire de destination. Vous pouvez éventuellement renommer la machine virtuelle dans le processus. Le processus d’importation inclut toutes les dépendances telles que les disques, les planifications et les paramètres réseau.

Le processus est une opération de copie, et non une opération de déplacement, qui peut prendre beaucoup de temps. La durée de l’importation dépend en partie des facteurs suivants :

- Nombre et taille des disques attachés à la machine source
- Distance entre les régions source et de destination

Une fois l’importation terminée, le processus arrête la machine virtuelle source et démarre la nouvelle machine virtuelle en cours d’exécution dans le laboratoire de destination.

Il existe plusieurs exigences et contraintes pour l’importation de machines virtuelles d’un laboratoire à un autre :

- Vous pouvez importer des machines virtuelles entre des abonnements et entre des régions, mais les abonnements doivent être associés au même locataire Azure Active Directory.
- Les machines virtuelles ne doivent pas être dans un état pouvant être revendiqué dans le laboratoire source.
- Vous devez être propriétaire de la machine virtuelle du laboratoire source et propriétaire du laboratoire de destination.
- Actuellement, cette fonctionnalité est prise en charge seulement via PowerShell et l’API REST.

## <a name="use-powershell-to-import-one-or-all-lab-vms"></a>Utiliser PowerShell pour importer une ou toutes les machines virtuelles de laboratoire

Téléchargez et exécutez [ImportVirtualMachines.ps1](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImportVirtualMachines). Vous pouvez utiliser le script pour importer une seule machine virtuelle ou toutes les machines virtuelles du laboratoire source dans le laboratoire de destination.

Pour exécuter ce script PowerShell, identifiez les abonnements et les laboratoires source et de destination, ainsi que la machine virtuelle source. Si vous le souhaitez, fournissez un nouveau nom pour la machine virtuelle de destination.

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source lab>"`
                            -SourceDevTestLabName "<Name of the source lab>"`
                            -SourceVirtualMachineName "<Name of the VM to import from the source lab>" `
                            -DestinationSubscriptionId "<ID of the subscription that contains the destination lab>"`
                            -DestinationDevTestLabName "<Name of the destination lab>"`
                            -DestinationVirtualMachineName "<Optional: specify a new name for the imported VM in the destination lab>"
```
Si vous ne spécifiez pas de machine virtuelle source, le script importe automatiquement toutes les machines virtuelles dans le laboratoire source.

## <a name="use-http-rest-to-import-a-vm"></a>Utiliser HTTP REST pour importer une machine virtuelle

L’appel REST est simple. Vous donnez les informations permettant d’identifier les ressources source et de destination. L’opération s’effectue sur la ressource du laboratoire de destination.

```http
POST https://management.azure.com/subscriptions/<DestinationSubscriptionID>/resourceGroups/<DestinationResourceGroup>/providers/Microsoft.DevTestLab/labs/<DestinationLab>/ImportVirtualMachine?api-version=2017-04-26-preview
{
   sourceVirtualMachineResourceId: "/subscriptions/<SourceSubscriptionID>/resourcegroups/<SourceResourceGroup>/providers/microsoft.devtestlab/labs/<SourceLab>/virtualmachines/<NameofVMTobeImported>",
   destinationVirtualMachineName: "<NewNameForImportedVM>"
}
```

## <a name="next-steps"></a>Étapes suivantes

- [Définir des stratégies pour un laboratoire](devtest-lab-set-lab-policy.md)
- [Forum aux questions sur DevTest Labs](devtest-lab-faq.yml)
