---
title: Exécuter des scripts sur une machine virtuelle Windows dans Azure à l’aide de commandes d’exécution d’action
description: Cette rubrique explique comment exécuter des scripts PowerShell au sein d’une machine virtuelle Windows Azure à l’aide de la fonctionnalité Run Command
services: automation
ms.service: virtual-machines
ms.collection: windows
author: cynthn
ms.author: cynthn
ms.date: 10/28/2021
ms.topic: how-to
ms.reviewer: jushiman
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b9501004578068f2c7841fd5f52091f50e97924b
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131441093"
---
# <a name="run-scripts-in-your-windows-vm-by-using-action-run-commands"></a>Exécuter des scripts dans votre machine virtuelle Windows à l’aide de commandes d’exécution d’action

La fonctionnalité Run Command utilise l’agent de machine virtuelle pour exécuter des scripts PowerShell au sein d’une machine virtuelle Windows Azure. Vous pouvez utiliser ces scripts pour la gestion générale des ordinateurs ou des applications. Ils peuvent vous aider à diagnostiquer et corriger rapidement les problèmes de réseau et d’accès aux machines virtuelles et à rétablir l’état de la machine virtuelle.


## <a name="benefits"></a>Avantages

Vous pouvez accéder à vos machines virtuelles de plusieurs façons. Run Command peut exécuter à distance des scripts sur vos machines virtuelles à l’aide de l’agent de machine virtuelle. Vous utilisez Run Command par le biais du Portail Azure, de l’[API REST](/rest/api/compute/virtual-machines-run-commands/run-command) ou de [PowerShell](/powershell/module/az.compute/invoke-azvmruncommand) pour machines virtuelles Windows.

Cette fonctionnalité est utile dans tous les scénarios où vous souhaitez exécuter un script sur une machine virtuelle. C’est l’une des seules manières de détecter et de corriger un problème sur une machine virtuelle qui n’a pas de port RDP ou SSH ouvert en raison d’une configuration incorrecte du réseau ou de l’utilisateur administratif.

## <a name="restrictions"></a>Restrictions

Les restrictions suivantes s’appliquent lorsque vous utilisez Run Command :

* La sortie est limitée aux derniers 4 096 octets.
* La durée minimale d’exécution d’un script est d’environ 20 secondes.
* Les scripts s’exécutent en tant que système sous Windows.
* Vous ne pouvez exécuter qu’un script à la fois.
* Les scripts qui demandent des informations (mode interactif) ne sont pas pris en charge.
* Vous ne pouvez pas annuler un script en cours d’exécution.
* La durée maximale d’exécution d’un script est de 90 minutes. Le script expire après ce délai.
* Une connectivité sortante à partir de la machine virtuelle est nécessaire pour retourner les résultats du script.
* Il n’est pas recommandé d’exécuter un script qui provoquera l’arrêt ou la mise à jour de l’agent de machine virtuelle. Cela peut maintenir l’extension dans un état de transition, ce qui entraîne l’expiration du délai d’attente.

> [!NOTE]
> Pour fonctionner correctement, Run Command a besoin d’une connectivité (port 443) aux IP publiques Azure. Si l’extension n’a pas accès à ces points de terminaison, il se peut que les scripts s’exécutent correctement, mais qu’ils ne retournent pas les résultats. Si vous bloquez le trafic sur la machine virtuelle, vous pouvez utiliser des [balises de service](../../virtual-network/network-security-groups-overview.md#service-tags) pour autoriser le trafic à destination des IP publiques Azure en utilisant la balise `AzureCloud`.
> 
> La fonctionnalité Exécuter la commande ne fonctionne pas si l’état de l’agent de machine virtuelle n’est PAS PRÊT. Vérifiez l’état de l’agent dans les propriétés de la machine virtuelle dans le portail Azure.

## <a name="available-commands"></a>Commandes disponibles

Ce tableau affiche la liste des commandes disponibles pour les machines virtuelles Windows. Vous pouvez utiliser la commande **RunPowerShellScript** pour exécuter n’importe quel script personnalisé de votre choix. Quand vous utilisez Azure CLI ou PowerShell pour exécuter une commande, la valeur que vous fournissez pour le paramètre `--command-id` ou `-CommandId` doit être l’une des valeurs listées ci-dessous. Quand vous spécifiez une valeur qui n’est pas une commande disponible, vous recevez cette erreur :

```error
The entity was not found in this Azure location
```
<br>

| **Nom** | **Description** |
|---|---|
| **RunPowerShellScript** | Exécute un script PowerShell. |
| **DisableNLA** | Désactiver l’authentification au niveau du réseau |
| **DisableWindowsUpdate** | Désactiver les mises à jour automatiques de Windows Update |
| **EnableAdminAccount** | Vérifie si le compte Administrateur local est désactivé, et si tel est le cas, l’active. |
| **EnableEMS** | Active EMS |
| **EnableRemotePS** | Configure la machine pour activer PowerShell à distance. |
| **EnableWindowsUpdate** | Activer les mises à jour automatiques de Windows Update |
| **IPConfig** | Affiche des informations détaillées pour l’adresse IP, le masque de sous-réseau et la passerelle par défaut de chaque adaptateur lié à TCP/IP. |
| **RDPSetting** | Vérifie les paramètres du registre et les paramètres de la stratégie de domaine. Suggère les actions de la stratégie si la machine fait partie d’un domaine ou remplace les paramètres par les valeurs par défaut. |
| **ResetRDPCert** | Supprime le certificat TLS/SSL lié à l’écouteur RDP et restaure les valeurs par défaut pour la sécurité de l’écouteur RDP. Utilisez ce script si vous rencontrez des problèmes avec le certificat. |
| **SetRDPPort** | Définit le numéro de port par défaut ou spécifié par l’utilisateur pour les connexions Bureau à distance. Active les règles de pare-feu pour l’accès entrant au port. |

## <a name="azure-cli"></a>Azure CLI

L’exemple suivant utilise la commande [az vm run-command](/cli/azure/vm/run-command#az_vm_run_command_invoke) pour exécuter un script d’interpréteur de commandes sur une machine virtuelle Windows Azure.

```azurecli-interactive
# script.ps1
#   param(
#       [string]$arg1,
#       [string]$arg2
#   )
#   Write-Host This is a sample script with parameters $arg1 and $arg2

az vm run-command invoke  --command-id RunPowerShellScript --name win-vm -g my-resource-group \
    --scripts @script.ps1 --parameters "arg1=somefoo" "arg2=somebar"
```

## <a name="azure-portal"></a>Portail Azure

Accédez à une machine virtuelle dans le [Portail Azure](https://portal.azure.com) et sélectionnez **Run command** dans le menu de gauche sous **Opérations**. Une liste des commandes pouvant s’exécuter sur la machine virtuelle s’affiche.

![Liste des commandes](./media/run-command/run-command-list.png)

Choisissez une commande à exécuter. Certaines des commandes peuvent avoir des paramètres d’entrée facultatifs ou obligatoires. Pour ces commandes, les paramètres sont présentés en tant que champs de texte vous permettant de fournir les valeurs d’entrée. Pour chaque commande, vous pouvez voir le script en cours d’exécution en développant **Afficher le script**. La commande **RunPowerShellScript** est différente des autres commandes, car elle vous permet de fournir votre propre script personnalisé.

> [!NOTE]
> Les commandes intégrées ne sont pas modifiables.

Une fois que vous avez choisi la commande, sélectionnez **Exécuter** pour exécuter le script. Une fois le script terminé, il renvoie la sortie et toutes les erreurs dans la fenêtre de sortie. La capture d’écran suivante montre un exemple de sortie de l’exécution de la commande **RDPSettings**.

![Sortie du script d’une commande Run](./media/run-command/run-command-script-output.png)

## <a name="powershell"></a>PowerShell

L’exemple suivant utilise l’applet de commande [Invoke-AzVMRunCommand](/powershell/module/az.compute/invoke-azvmruncommand) pour exécuter un script PowerShell sur une machine virtuelle Azure. Pour la cmdlet, le script référencé dans le paramètre `-ScriptPath` doit se situer au même emplacement qu'elle.

```azurepowershell-interactive
Invoke-AzVMRunCommand -ResourceGroupName '<myResourceGroup>' -Name '<myVMName>' -CommandId 'RunPowerShellScript' -ScriptPath '<pathToScript>' -Parameter @{"arg1" = "var1";"arg2" = "var2"}
```

## <a name="limiting-access-to-run-command"></a>Limitation de l’accès à la commande Run

Le listage des commandes d’exécution ou l’affichage des détails d’une commande nécessite l’autorisation `Microsoft.Compute/locations/runCommands/read` au niveau de l’abonnement. Le rôle intégré [Lecteur](../../role-based-access-control/built-in-roles.md#reader) et les niveaux supérieurs disposent de cette autorisation.

L’exécution d’une commande nécessite l’autorisation `Microsoft.Compute/virtualMachines/runCommand/action`. Le rôle [Contributeur de machines virtuelles](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) et les niveaux supérieurs disposent de cette autorisation.

Vous pouvez utiliser un des [rôles intégrés](../../role-based-access-control/built-in-roles.md) ou créer un [rôle personnalisé](../../role-based-access-control/custom-roles.md) afin d’exécuter Run Command.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les autres méthodes pour exécuter à distance des commandes et des scripts dans votre machine virtuelle, consultez [Exécuter des scripts dans votre machine virtuelle Windows](run-scripts-in-vm.md).
