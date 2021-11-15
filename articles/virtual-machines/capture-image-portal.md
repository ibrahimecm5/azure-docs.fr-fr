---
title: Capturer une image de machine virtuelle à l’aide du portail
description: Créez une image de machine virtuelle à l’aide du portail Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 06/21/2021
ms.author: cynthn
ms.custom: portal
ms.openlocfilehash: bfab2d65de8b15ac72fe3c3b536b76ef14f046b4
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131437293"
---
# <a name="create-an-image-of-a-vm-in-the-portal"></a>Créer une image de machine virtuelle dans le portail

Vous pouvez créer une image à partir d’une machine virtuelle, puis l’utiliser pour créer plusieurs machines virtuelles.

Pour les images stockées dans Azure Compute Gallery (anciennement appelé Shared Image Gallery), vous pouvez utiliser des machines virtuelles sur lesquelles des comptes ont déjà été créés (machines virtuelles spécialisées), ou vous pouvez généraliser la machine virtuelle avant de créer l’image afin de supprimer les comptes de machine et les autres informations spécifiques aux machines. Pour généraliser une machine virtuelle, consultez [Généraliser une machine virtuelle Windows](generalize.md). Pour plus d’informations, consultez [Images généralisées et spécialisées](shared-image-galleries.md#generalized-and-specialized-images).


## <a name="capture-a-vm-in-the-portal"></a>Capturer une machine virtuelle dans le portail 

1. Accédez au [portail Azure](https://portal.azure.com), puis recherchez et sélectionnez **Machines virtuelles**.

2. Sélectionnez votre machine virtuelle dans la liste.

3. Dans la page **Machine virtuelle** relative à la machine virtuelle, dans le menu supérieur, sélectionnez **Capturer**.

   La page **Créer une image** s’affiche.

5. Pour **Groupe de ressources**, sélectionnez **Créer** et entrez un nom, ou sélectionnez un groupe de ressources à utiliser dans la liste déroulante. Si vous souhaitez utiliser une galerie existante, sélectionnez le groupe de ressources de la galerie à utiliser.

1. Pour créer l’image dans une galerie, sélectionnez **Oui, la partager dans une galerie sous forme de version d’image.** .
    
   Pour créer uniquement une image managée, sélectionnez **Non, capturer uniquement une image managée.** . La machine virtuelle doit avoir été généralisée pour créer une image managée. La seule autre information obligatoire est le nom de l’image.

6. Pour supprimer la machine virtuelle source une fois que l’image a été créée, sélectionnez **Supprimer automatiquement cette machine virtuelle après avoir créé l’image**. Ce n’est pas recommandé.

1. Pour les **Détails de la galerie**, sélectionnez la galerie ou créez une galerie en sélectionnant **Créer**.

1. Dans **État du système d’exploitation**, sélectionnez généralisé ou spécialisé. Pour plus d’informations, consultez [Images généralisées et spécialisées](shared-image-galleries.md#generalized-and-specialized-images).
 
1. Sélectionnez une définition d’image, ou sélectionnez **Créer**, puis indiquez le nom et les informations spécifiques à la [définition d’image](shared-image-galleries.md#image-definitions).

1. Entrez un numéro de [version d’image](shared-image-galleries.md#image-versions). S’il s’agit de la première version de cette image, tapez *1.0.0*.

1. Si vous souhaitez inclure cette version quand vous spécifiez *La plus récente* pour la version de l’image, ne cochez pas **Exclure de la plus récente**.

1. Sélectionnez une date de **fin de vie**. Cette date permet de suivre le moment où les anciennes images doivent être mises hors service.

1. Sous [Réplication](shared-image-galleries.md#replication), sélectionnez un nombre de réplicas par défaut, puis sélectionnez les régions supplémentaires où vous souhaitez que votre image soit répliquée.

8. Quand vous avez terminé, sélectionnez **Vérifier + créer**.

1. Une fois la validation réussie, sélectionnez **Créer** pour créer l’image.



## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble des galeries Azure Compute](shared-image-galleries.md) 
