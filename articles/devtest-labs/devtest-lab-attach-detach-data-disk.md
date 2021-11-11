---
title: Ajouter un disque de données existant à une machine virtuelle de laboratoire
description: Découvrez comment attacher ou détacher un disque de données lab sur une machine virtuelle de laboratoire dans Azure DevTest Labs
ms.topic: how-to
ms.date: 10/26/2021
ms.openlocfilehash: 8afd2fff50a893fb293d7b419c4839c34487c32d
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131453925"
---
# <a name="attach-or-detach-a-lab-data-disk-to-a-lab-virtual-machine-in-azure-devtest-labs"></a>Attacher ou détacher un disque de données lab sur une machine virtuelle de laboratoire dans Azure DevTest Labs

Vous pouvez créer et attacher un nouveau [disque de données](../virtual-machines/managed-disks-overview.md) lab à une machine virtuelle Azure de laboratoire. Le disque de données peut ensuite être détaché, et soit supprimé, rattaché ou attaché à une autre machine virtuelle de laboratoire que vous possédez. Cette fonctionnalité est pratique pour la gestion de stockage ou de logiciels en dehors de chaque machine virtuelle individuelle.

Dans cet article, vous allez apprendre à attacher et détacher un disque de données à une machine virtuelle de laboratoire.

## <a name="prerequisites"></a>Prérequis

Votre machine virtuelle de labo doit être en cours d’exécution. La taille de la machine virtuelle détermine le nombre de disques de données que vous pouvez attacher. Pour en savoir plus, voir la rubrique [Tailles de machines virtuelles](../virtual-machines/sizes.md).

## <a name="attach-a-new-data-disk"></a>Associer un nouveau disque de données

Suivez ces étapes pour créer et attacher un disque de données managé à une machine virtuelle dans Azure DevTest Labs.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

1. Accédez à votre laboratoire dans **DevTest Labs**.

1. Sélectionnez votre machine virtuelle en cours d’exécution.

1. Sur la page **Machines virtuelles**, sous **Paramètres**, sélectionnez **Disques**.
 
1. Sélectionnez **Attacher un nouveau disque**.

    :::image type="content" source="./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-new.png" alt-text="Capture d’écran de l’attachement d’un nouveau disque de données à une machine virtuelle.":::

1. Dans la page **Ajouter un nouveau disque**, fournissez les informations suivantes : 

    |Propriété | Description |
    |---|---|
    |Nom|Entrez un nom unique.|
    |Type de disque| Dans la liste déroulante, sélectionnez un [type de disque](../virtual-machines/disks-types.md).|
    |Taille (Gio)|Entrez une taille en gigaoctets.|

    :::image type="content" source="./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-new-form.png" alt-text="Capture d’écran du formulaire « attacher un nouveau disque » complet.":::

1. Sélectionnez **OK**.

1. Vous êtes redirigé vers la page de la **machine virtuelle**. Affichez votre disque attaché sous **Disques de données**.

    :::image type="content" source="./media/devtest-lab-attach-detach-data-disk/devtest-lab-attached-data-disk.png" alt-text="Capture d’écran du disque attaché s’affichant sous Disques de données.":::

## <a name="detach-a-data-disk"></a>Détachement d'un disque de données

Le fait de détacher le disque lab le supprime de la machine virtuelle de labo, mais il est conservé dans le stockage pour une utilisation ultérieure.

### <a name="detach-from-the-vms-management-page"></a>Détacher un disque à partir de la page de gestion de la machine virtuelle

1. Accédez à votre laboratoire dans **DevTest Labs**.

1. Sélectionnez votre machine virtuelle en cours d’exécution avec un disque de données attaché.

1. Sur la page **Machines virtuelles**, sous **Paramètres**, sélectionnez **Disques**.

1. Sous **Disques de données**, sélectionnez le disque de données que vous souhaitez détacher.

    :::image type="content" source="./media/devtest-lab-attach-detach-data-disk/devtest-lab-detach-button.png" alt-text="Capture d’écran de sélection des disques de données pour une machine virtuelle.":::

1. Dans la page **Disque de données**, sélectionnez **Détacher**.

    :::image type="content" source="./media/devtest-lab-attach-detach-data-disk/devtest-lab-detach-data-disk-2.png" alt-text="Capture d’écran montrant le volet d’informations d’un disque avec l’action « Détacher » mise en évidence.":::

1. Sélectionnez **OK** pour confirmer que vous souhaitez détacher le disque de données. Le disque est détaché et disponible pour être attaché à une autre machine virtuelle. 

### <a name="detach-from-the-labs-management-page"></a>Détacher un disque à partir de la page de gestion du laboratoire

1. Accédez à votre laboratoire dans **DevTest Labs**.

1. Sous **Mon laboratoire**, sélectionnez **Mes disques de données**.

1. Sélectionnez les points de suspension ( **...** ), puis sélectionnez **Détacher** pour le disque que vous souhaitez détacher.

    :::image type="content" source="./media/devtest-lab-attach-detach-data-disk/devtest-lab-detach-data-disk.png" alt-text="Capture d’écran du détachement d’un disque de données.":::

1. Sélectionnez **Oui** pour confirmer que vous souhaitez le détacher.

   > [!NOTE]
   > Si un disque de données est déjà détaché, vous pouvez choisir de le supprimer de votre liste de disques de données disponibles en sélectionnant **Supprimer**.

## <a name="attach-an-existing-disk"></a>Association d'un disque existant

Suivez ces étapes pour attacher un disque de données disponible à une machine virtuelle en cours d’exécution. 

1. Accédez à votre laboratoire dans **DevTest Labs**.

1. Sélectionnez votre machine virtuelle en cours d’exécution.

1. Sur la page **Machines virtuelles**, sous **Paramètres**, sélectionnez **Disques**.

1. Sélectionnez **Attachement d’un disque existant**.

    :::image type="content" source="./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-existing-button.png" alt-text="Capture d’écran montrant les paramètres « Disques » et « Attacher un disque existant » sélectionnés.":::

1. Dans la page **Attacher un disque existant**, sélectionnez un disque, puis **OK**. Au bout de quelques instants, le disque de données est attaché à la machine virtuelle et apparaît dans la liste des **Disques de données** pour cette celle-ci.

    :::image type="content" source="./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-existing.png" alt-text="Capture d’écran de l’attachement d’un disque de données existant à une machine virtuelle.":::

## <a name="upgrade-an-unmanaged-data-disk"></a>Mettre à niveau un disque de données non managé

Si vous disposez d’une machine virtuelle avec des disques de données non managés, vous pouvez convertir la machine virtuelle pour qu’elle utilise des disques managés. Ce processus convertit le disque du système d’exploitation ainsi que tous les autres disques de données attachés.

Commencez par [détacher le disque de données](#detach-a-data-disk) de la machine virtuelle non managée. Ensuite, [rattachez le disque](#attach-an-existing-disk) à une machine virtuelle managée pour le mettre automatiquement à niveau en le faisant passer de disque non managé à disque managé.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur la gestion des disques de données des [machines virtuelles revendicables](devtest-lab-add-claimable-vm.md#unclaim-a-vm).
