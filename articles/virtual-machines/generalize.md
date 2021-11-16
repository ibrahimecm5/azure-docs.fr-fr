---
title: Généraliser une machine virtuelle avant de créer une image
description: Généralisez une machine virtuelle pour supprimer les informations spécifiques à la machine avant de créer une image.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 06/16/2021
ms.author: cynthn
ms.custom: portal
ms.openlocfilehash: e28ec0a10431316f7c5658ec006a7136f5388ff3
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131444608"
---
# <a name="remove-machine-specific-information-by-generalizing-a-vm-before-creating-an-image"></a>Supprimer les informations spécifiques à la machine en généralisant une machine virtuelle avant de créer une image

La généralisation d’une machine virtuelle n’est pas nécessaire pour créer une image dans une galerie [Azure Compute Gallery](shared-image-galleries.md#generalized-and-specialized-images), sauf si vous souhaitez spécifiquement créer une image généralisée. La généralisation est requise lors de la création d’une image managée en dehors d’une galerie.

La généralisation supprime les informations spécifiques à la machine afin que l’image puisse être utilisée pour créer plusieurs machines virtuelles. Une fois la machine virtuelle généralisée, vous devez indiquer à la plateforme que la machine virtuelle a été généralisée afin que la séquence de démarrage puisse être définie correctement. Une fois qu’une machine virtuelle est généralisée, elle ne doit pas être redémarrée.


## <a name="linux"></a>Linux

Commencez par déprovisionner la machine virtuelle à l’aide de l’agent de machine virtuelle Azure pour supprimer les fichiers et les données propres à la machine. Utilisez la commande `waagent` avec le paramètre `-deprovision+user` sur votre machine virtuelle Linux source. Pour plus d’informations, consultez le [Guide d’utilisateur de l’agent Linux Azure](./extensions/agent-linux.md). Ce processus est irréversible.

1. Connectez-vous à votre machine virtuelle Linux avec un client SSH.
2. Dans la fenêtre SSH, entrez la commande suivante :
   ```bash
    sudo waagent -deprovision+user
   ```
   > [!NOTE]
   > Exécutez uniquement cette commande sur une machine virtuelle que vous allez capturer en tant qu’image. Cette commande ne garantit pas que l’image est exempte de toute information sensible ou qu’elle convient pour la redistribution. Le paramètre `+user` supprime également le dernier compte d’utilisateur provisionné. Pour garder les informations d’identification du compte d’utilisateur dans la machine virtuelle, utilisez uniquement `-deprovision`.
 
3. Tapez **Y** pour continuer. Vous pouvez ajouter le paramètre `-force` pour éviter cette étape de confirmation.
4. Une fois la commande exécutée, entrez **exit** pour fermer le client SSH.  La machine virtuelle est toujours en cours d’exécution à ce stade.


Ensuite, la machine virtuelle doit être marquée comme généralisée sur la plateforme. 

```azurecli-interactive
az vm generalize \
   --resource-group myResourceGroup \
   --name myVM
```

## <a name="windows"></a>Windows 

Sysprep supprime toutes vos informations de compte personnel et de sécurité, puis prépare la machine en vue de son utilisation en tant qu’image. Pour plus d’informations sur Sysprep, voir [Vue d’ensemble de Sysprep](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview).

Vérifiez que les rôles serveur exécutés sur la machine sont pris en charge par Sysprep. Pour plus d’informations, voir [Prise en charge de Sysprep pour les rôles serveur](/windows-hardware/manufacture/desktop/sysprep-support-for-server-roles) et [Scénarios non pris en charge](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview#unsupported-scenarios). 

> [!IMPORTANT]
> Après que vous avez exécuté Sysprep sur une machine virtuelle, celle-ci est considérée comme *généralisée* et ne peut plus être redémarrée. Le processus de généralisation d’une machine virtuelle n’est pas réversible. Si vous devez conserver le fonctionnement de machine virtuelle d’origine, vous devez créer une [copie de la machine virtuelle](./windows/create-vm-specialized.md#option-3-copy-an-existing-azure-vm) et généraliser la copie. 
>
> Sysprep requiert le déchiffrement complet des lecteurs. Si vous avez activé le chiffrement sur votre machine virtuelle, désactivez-le avant d’exécuter Sysprep.
>
> Si vous prévoyez d’exécuter Sysprep avant de charger votre disque dur virtuel sur Azure pour la première fois, vérifiez que vous avez [préparé votre machine virtuelle](./windows/prepare-for-upload-vhd-image.md).  
> 
> 

Pour généraliser votre machine virtuelle Windows, procédez comme suit :

1. Connectez-vous à votre machine virtuelle Windows.
   
2. Ouvrez une fenêtre d’invite de commandes en tant qu’administrateur. 

3. Supprimez le répertoire Panther (C:\Windows\Panther). Remplacez ensuite le répertoire par %windir%\system32\sysprep, puis exécutez `sysprep.exe`.
   
4. Dans la boîte de dialogue **Outil de préparation du système**, sélectionnez **Entrer en mode OOBE (Out-of-Box Experience)** , puis activez la case à cocher **Généraliser**.
   
5. Dans **Options d’arrêt**, sélectionnez **Arrêter**.
   
6. Sélectionnez **OK**.
   
    :::image type="content" source="windows/media/upload-generalized-managed/sysprepgeneral.png" alt-text="![Démarrer Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)":::

6. Une fois l’exécution de Sysprep terminée, la machine virtuelle est arrêtée. Ne redémarrez pas la machine virtuelle.

> [!TIP]
> **Facultatif** Utilisez [DISM](/windows-hardware/manufacture/desktop/dism-optimize-image-command-line-options) pour optimiser votre image et réduire la durée du premier démarrage de votre machine virtuelle.
>
> Pour optimiser votre image, montez votre VHD en double-cliquant dessus dans l’Explorateur Windows, puis exécutez DISM avec le paramètre `/optimize-image`.
>
> ```cmd
> DISM /image:D:\ /optimize-image /boot
> ```
> Où D: est le chemin d’accès au VHD monté.
>
> L’exécution de `DISM /optimize-image` doit être la dernière modification apportée à votre VHD. Si vous apportez des modifications à votre VHD avant le déploiement, vous devrez réexécuter `DISM /optimize-image`.

Une fois que Sysprep a terminé, définissez l’état de la machine virtuelle sur la valeur **Généralisé**.
   
```azurepowershell-interactive
Set-AzVm -ResourceGroupName $rgName -Name $vmName -Generalized
```
