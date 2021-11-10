---
title: Créer un groupe identique à partir d’une version d’image spécialisée à l’aide d’Azure CLI
description: Créez un groupe identique à l’aide d’une version d’image spécialisée dans une Azure Compute Gallery à l’aide d’Azure CLI.
author: cynthn
ms.service: virtual-machine-scale-sets
ms.subservice: shared-image-gallery
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 05/01/2020
ms.author: cynthn
ms.reviewer: mimckitt
ms.custom: devx-track-azurecli
ms.openlocfilehash: ae75ac06b49bd5faa7851dd7cfc2f2a236fd3952
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131441242"
---
# <a name="create-a-scale-set-using-a-specialized-image-version-with-the-azure-cli"></a>Créer un groupe identique à l’aide d’une version d’image spécialisée avec Azure CLI

**S’applique à :** :heavy_check_mark: Machines virtuelles Linux :heavy_check_mark: Machines virtuelles Windows :heavy_check_mark: Groupes identiques uniformes

Créez un groupe identique à partir d’une [version d’image spécialisée](../virtual-machines/shared-image-galleries.md#generalized-and-specialized-images) stockée dans une Azure Compute Gallery. Si vous souhaitez créer un groupe identique à l’aide d’une version d’image généralisée, consultez [Créer un groupe identique à partir d’une image généralisée](instance-generalized-image-version-cli.md).

Si vous choisissez d’installer et d’utiliser l’interface CLI localement, ce tutoriel demande au minimum la version 2.4.0 d’Azure CLI. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI]( /cli/azure/install-azure-cli).

Remplacez les noms de ressources en fonction des besoins dans cet exemple. 

Répertoriez les définitions d’images d’une galerie avec la commande [az sig image-definition list](/cli/azure/sig/image-definition#az_sig_image_definition_list) pour voir le nom et l’ID des définitions.

```azurecli-interactive 
resourceGroup=myGalleryRG
gallery=myGallery
az sig image-definition list \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --query "[].[name, id]" \
   --output tsv
```

Créez le groupe identique avec [`az vmss create`](/cli/azure/vmss#az_vmss_create), en utilisant le paramètre `--specialized` pour indiquer que l’image est une image spécialisée.

Utilisez l’ID de définition d’image pour `--image` afin de créer les instances de groupe identique à partir de la version la plus récente de l’image disponible. Vous pouvez également créer les instances de groupe identique à partir d’une version spécifique en fournissant l’ID de version de l’image pour `--image`. Souvenez-vous que l’utilisation d’une version d’image spécifique signifie que l’automatisation peut échouer si cette version d’image spécifique n’est pas disponible car elle a été effacée ou supprimée de la région. Nous vous recommandons d’utiliser l’ID de définition d’image pour créer votre machine virtuelle, sauf si une version d’image spécifique est requise.

Dans cet exemple, nous créons des instances à partir de la dernière version de l’image *myImageDefinition*.

```azurecli
az group create --name myResourceGroup --location eastus
az vmss create \
   --resource-group myResourceGroup \
   --name myScaleSet \
   --image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition" \
   --specialized
```


## <a name="next-steps"></a>Étapes suivantes
[Générateur d’image Azure (préversion)](../virtual-machines/image-builder-overview.md) permet d’automatiser la création d’une version de l’image. Vous pouvez même l’utiliser pour mettre à jour et [créer une nouvelle version de l’image à partir d’une image existante](../virtual-machines/linux/image-builder-gallery-update-image-version.md). 

Vous pouvez également créer la ressource Azure Compute Gallery en utilisant des modèles. Plusieurs modèles de démarrage rapide Azure sont disponibles : 

- [Créer une galerie Azure Compute Gallery](https://azure.microsoft.com/resources/templates/sig-create/)
- [Créer une définition d’image dans une galerie Azure Compute Gallery](https://azure.microsoft.com/resources/templates/sig-image-definition-create/)
- [Créer une version d’image dans une instance Azure Compute Gallery](https://azure.microsoft.com/resources/templates/sig-image-version-create/)