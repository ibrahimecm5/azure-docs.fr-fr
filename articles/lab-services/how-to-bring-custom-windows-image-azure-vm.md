---
title: Guide pratique pour apporter une image personnalisée Windows à partir d’une machine virtuelle Azure
description: Décrit comment récupérer une image Windows personnalisée à partir d’une machine virtuelle Azure.
ms.date: 07/27/2021
ms.topic: how-to
ms.openlocfilehash: b91e5dbf872c7d265871d1f2a435dde9246b5582
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130180853"
---
# <a name="bring-a-windows-custom-image-from-an-azure-virtual-machine"></a>Apporter une image personnalisée Windows à partir d’une machine virtuelle Azure

Les étapes de cet article montrent comment importer une image personnalisée à partir d’une [machine virtuelle Azure](https://azure.microsoft.com/services/virtual-machines/). Avec cette approche, vous configurez une image sur une machine virtuelle Azure et importez cette image dans une galerie d’images partagée afin qu’elle puisse être utilisée dans Azure Lab Services. Avant d’utiliser cette approche pour créer une image personnalisée, lisez [Approches recommandées pour créer des images personnalisées](approaches-for-custom-image-creation.md) afin de déterminer la meilleure approche pour votre scénario.

## <a name="prerequisites"></a>Prérequis

Pour effectuer les étapes indiquées dans cet article, vous devrez disposer de l’autorisation de créer une machine virtuelle Azure dans l’abonnement Azure de votre établissement scolaire.

## <a name="prepare-a-custom-image-on-an-azure-vm"></a>Préparez une image personnalisée sur une machine virtuelle Azure

1. Créez une machine virtuelle Azure à l’aide du [portail Azure](../virtual-machines/windows/quick-create-portal.md), de [PowerShell](../virtual-machines/windows/quick-create-powershell.md), de l’interface [Azure CLI](../virtual-machines/windows/quick-create-cli.md)ou d’un [modèle Azure Resource Manager](../virtual-machines/windows/quick-create-template.md).
    
    - Lorsque vous spécifiez les paramètres du disque, assurez-vous que la taille du disque n’est *pas* supérieure à 128 Go.
    
1. Installez les logiciels et apportez les modifications de configuration nécessaires à l’image de la machine virtuelle Azure.

1. Si vous le souhaitez, vous pouvez généraliser l’image. Exécutez [SysPrep](../virtual-machines/generalize.md#windows) si vous devez créer une image généralisée. Dans le cas contraire, si vous créez une image spécialisée, vous pouvez passer à l’étape suivante.

    Créez une image spécialisée si vous souhaitez conserver les profils utilisateur et les informations propres à la machine. Pour plus d’informations sur les différences existant entre les images généralisées et les images spécialisées, consultez [Images généralisées et spécialisées](../virtual-machines/shared-image-galleries.md#generalized-and-specialized-images).

## <a name="import-the-custom-image-into-a-shared-image-gallery"></a>Importez l’image personnalisée dans une galerie d’images partagées

1. Dans une galerie d’images partagées, [créez une définition d’image](../virtual-machines/image-version.md) ou choisissez une définition d’image existante.
     - Choisissez **Gen 1** pour la **génération de la machine virtuelle**.
     - Indiquez si vous créez une image **spécialisée** ou **généralisée** pour l'**état du système d’exploitation**.

    Pour plus d’informations sur les valeurs que vous pouvez spécifier pour une définition d’image, consultez [Définitions d’image](../virtual-machines/shared-image-galleries.md#image-definitions). 
    
    Vous pouvez également choisir d’utiliser une définition d’image existante, et créer une nouvelle version de votre image personnalisée.
    
1. [Créez une version d’image](../virtual-machines/image-version.md).
    - La propriété **Numéro de version** utilise le format suivant : *VersionMajeure.VersionMineure.Patch*.   
    - Pour la **Source**, sélectionnez **Disques et/ou captures instantanées** à partir de la liste déroulante.
    - Pour la propriété **Disque de système d’exploitation**, choisissez le disque de votre machine virtuelle Azure que vous avez créé dans les étapes précédentes.

    Vous pouvez également importer votre image personnalisée d’une machine virtuelle Azure vers une galerie d’images partagée au moyen de PowerShell. Pour plus d’informations, consultez le script et le fichier ReadMe dans [Importer une image vers le script de la galerie d’images partagée](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/BringImageToSharedImageGallery/).

## <a name="create-a-lab"></a>Création d’un laboratoire

[Créez le labo](tutorial-setup-classroom-lab.md) dans les Services Lab et sélectionnez l’image personnalisée à partir de la galerie d’images partagée.

## <a name="next-steps"></a>Étapes suivantes

* [Vue d’ensemble de Shared Image Gallery](../virtual-machines/shared-image-galleries.md)
* [Attacher ou détacher une galerie d’images partagées](how-to-attach-detach-shared-image-gallery.md)
* [Utiliser une galerie d’images partagées](how-to-use-shared-image-gallery.md)