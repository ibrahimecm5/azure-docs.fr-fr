---
title: Comparaison entre des images personnalisées et des formules
description: Découvrez les différences entre les images personnalisées et les formules comme bases de machine virtuelle afin de déterminer laquelle correspond le mieux à votre environnement.
ms.topic: conceptual
ms.date: 08/26/2021
ms.openlocfilehash: 1eca53c8d38dc7cd51b7a91c2e40518e5adfc05f
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132286599"
---
# <a name="compare-custom-images-and-formulas-in-devtest-labs"></a>Comparer les images personnalisées et les formules dans DevTest Labs
Les [images personnalisées](devtest-lab-create-template.md) et les [formules](devtest-lab-manage-formulas.md) peuvent être utilisées comme bases pour [créer des machines virtuelles de labo](devtest-lab-add-vm.md). La principale différence entre les images personnalisées et les formules est qu’une image personnalisée est simplement une image basée sur un disque dur virtuel (VHD). Une formule est une image basée sur un disque dur virtuel avec des paramètres préconfigurés. Les paramètres préconfigurés peuvent inclure la taille de machine virtuelle, le réseau virtuel, le sous-réseau et les artefacts. Ces paramètres préconfigurés sont définis avec les valeurs par défaut que vous pouvez remplacer lors de la création de la machine virtuelle. 

Dans cet article, vous allez découvrir les avantages et les inconvénients de l’utilisation des images personnalisées par rapport à l’utilisation de formules.  Vous pouvez également consulter le [Guide pratique de création d’une image personnalisée à partir d’une machine virtuelle](devtest-lab-create-custom-image-from-vm-using-portal.md) et les questions fréquentes [FAQ DevTest Labs](devtest-lab-faq.yml).

## <a name="custom-image-benefits"></a>Avantages des images personnalisées
Les images personnalisées représentent un moyen statique et immuable de créer des machines virtuelles dans un environnement de votre choix. 

|Avantages|Inconvénients|
|----|----|
|<li>Le provisionnement d’une machine virtuelle à partir d’une image personnalisée est rapide, car rien ne change une fois que la machine virtuelle est lancée à partir de l’image. En d’autres termes, aucun paramètre ne doit être appliqué, car l’image personnalisée est simplement une image sans paramètres. <li>Les machines virtuelles créées à partir d’une même image personnalisée sont identiques.|<li>Si vous avez besoin de mettre à jour certains aspects de l’image personnalisée, vous devez recréer l’image. |

## <a name="formula-benefits"></a>Avantages des formules
  
Les formules représentent un moyen dynamique de créer des machines virtuelles à partir de la configuration et des paramètres souhaités.

|Avantages|Inconvénients|
|----|----|
|<li>Les modifications de l'environnement peuvent être capturées à la volée au moyen d’artefacts. Par exemple, si vous souhaitez qu’une machine virtuelle soit installée avec les éléments les plus récents de votre pipeline de mise en production ou si vous souhaitez inscrire le code le plus récent à partir de votre référentiel, utilisez une formule. La formule peut spécifier un artefact qui déploie les derniers éléments ou inscrit le dernier code, ainsi que l’image de base cible. Chaque fois que cette formule est utilisée pour créer des machines virtuelles, les éléments et le code les plus récents sont déployés ou inscrits sur la machine virtuelle.  <li>À la différence des images personnalisées, les formules peuvent définir des paramètres par défaut, comme la taille des machines virtuelles et les paramètres de réseau virtuel.  <li>Les paramètres enregistrés dans une formule apparaissent comme valeurs par défaut, mais peuvent être modifiés à la création de la machine virtuelle. |<li> La création d’une machine virtuelle à partir d’une formule peut prendre plus de temps qu’à partir d’une image personnalisée.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]
