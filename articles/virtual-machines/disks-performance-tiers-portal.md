---
title: Modification du niveau de performance des disques managés Azure avec le Portail Azure
description: Découvrez comment modifier le niveau de performance de nouveaux disques managés et de disques existants avec le Portail Azure.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 09/02/2021
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 1320ec212a94245e42c63f583d37b33eaa76224e
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123433293"
---
# <a name="change-your-performance-tier-using-the-azure-portal"></a>Modification du niveau de performance avec le Portail Azure

**S’applique à :** :heavy_check_mark: Machines virtuelles Linux :heavy_check_mark: Machines virtuelles Windows :heavy_check_mark: Groupes identiques flexibles :heavy_check_mark: Groupes identiques uniformes

[!INCLUDE [virtual-machines-disks-performance-tiers-intro](../../includes/virtual-machines-disks-performance-tiers-intro.md)]

## <a name="restrictions"></a>Restrictions

[!INCLUDE [virtual-machines-disks-performance-tiers-restrictions](../../includes/virtual-machines-disks-performance-tiers-restrictions.md)]

## <a name="getting-started"></a>Prise en main

### <a name="new-disks"></a>Nouveaux disques

La procédure suivante montre comment modifier le niveau de performance d’un disque lors de sa création initiale :

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
1. Accédez à la machine virtuelle pour laquelle vous souhaitez créer un disque.
1. Lorsque vous sélectionnez le nouveau disque, commencez par choisir la taille dont vous avez besoin.
1. Une fois que vous avez sélectionné une taille, sélectionnez un autre niveau de performance pour modifier ses performances.
1. Sélectionnez **OK** pour créer le disque.

:::image type="content" source="media/disks-performance-tiers-portal/new-disk-change-performance-tier.png" alt-text="Capture d’écran du panneau de création de disque, avec un disque et la liste déroulante du niveau de performance surlignés" lightbox="media/disks-performance-tiers-portal/performance-tier-settings.png":::


### <a name="change-the-performance-tier-of-an-existing-disk-without-downtime"></a>Modifier le niveau de performance d’un disque existant sans temps d’arrêt

Vous pouvez également modifier votre niveau de performance sans temps d’arrêt. Ainsi, vous n’avez pas besoin de libérer votre machine virtuelle ou de détacher votre disque pour modifier le niveau.

### <a name="prerequisites"></a>Prérequis

Vous devez activer la fonctionnalité pour votre abonnement avant de pouvoir modifier le niveau de performance d'un disque sans temps d'arrêt. Suivez les étapes ci-dessous pour activer la fonctionnalité pour votre abonnement :

1.  Exécutez la commande suivante pour inscrire la fonctionnalité pour votre abonnement

    ```azurecli
    az feature register --namespace Microsoft.Compute --name LiveTierChange
    ```
 
1.  Vérifiez que l'état de l'inscription est **Inscrit** (cela peut prendre quelques minutes) à l'aide de la commande suivante avant d'essayer la fonctionnalité.

    ```azurecli
    az feature show --namespace Microsoft.Compute --name LiveTierChange
    ```

### <a name="change-performance-tier"></a>Changer le niveau de performance

Maintenant que la fonctionnalité a été inscrite, vous pouvez modifier les niveaux de performances du disque concerné sans temps d’arrêt.

1. Connectez-vous au portail Azure à partir du lien suivant : [https://aka.ms/diskPerfTiersPreview](https://aka.ms/diskPerfTiersPreview).
1. Accédez à la machine virtuelle contenant le disque que vous souhaitez modifier.
1. Sélectionnez votre disque.
1. Sélectionnez **Taille + performances**.
1. Dans la liste déroulante **Niveau de performance**, sélectionnez un niveau différent du niveau de performance actuel du disque.
1. Sélectionnez **Redimensionner**.

:::image type="content" source="media/disks-performance-tiers-portal/change-tier-existing-disk.png" alt-text="Capture d’écran du panneau Taille + performances, avec le niveau de performance surligné" lightbox="media/disks-performance-tiers-portal/performance-tier-settings.png":::

## <a name="next-steps"></a>Étapes suivantes

Si vous avez besoin de redimensionner un disque pour tirer parti de niveaux de performance supérieurs, consultez les articles suivants :

- [Étendre des disques durs virtuels sur une machine virtuelle Linux avec Azure CLI](linux/expand-disks.md)
- [Développer un disque managé attaché à une machine virtuelle Windows](windows/expand-os-disk.md)
