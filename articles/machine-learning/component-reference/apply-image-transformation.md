---
title: Appliquer une transformation d’image
titleSuffix: Azure Machine Learning
description: Découvrez comment utiliser le composant Appliquer une transformation d’image pour appliquer une transformation d’image à un répertoire d’images.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/26/2020
ms.openlocfilehash: 45929ecdcc4327a12e2b2867a0cb0c95ca78a3fd
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131567196"
---
# <a name="apply-image-transformation"></a>Appliquer une transformation d’image 

Cet article explique comment utiliser le composant Appliquer une transformation d’image du concepteur Azure Machine Learning pour modifier un répertoire d’images d’entrée en fonction de la transformation d’image spécifiée.  

Vous devez connecter un composant [Lancer une transformation d’image](init-image-transformation.md) pour spécifier la transformation. Vous pouvez ensuite appliquer cette transformation au répertoire d’images d’entrée du composant Appliquer une transformation d’image.

## <a name="how-to-use-apply-image-transformation"></a>Comment utiliser le module Apply Image Transformation  

1. Ajoutez le composant **Appliquer une transformation** à votre pipeline. Vous trouverez ce composant dans la catégorie *Vision par ordinateur/Transformation de données d’image*. 

2. Connectez la sortie du module **Init Image Transformation** à l’entrée gauche du module **Apply Image Transformation**.

     > [!NOTE]
     > Seule la transformation d’image générée par le composant [Lancer la transformation d’image](init-image-transformation.md) est acceptée pour ce composant. Pour tout autre genre de transformation, connectez-le au module [Apply Transformation](apply-transformation.md) (Appliquer une transformation). Sinon, une exception « InvalidTransformationDirectoryError » est levée.


3. Connectez le répertoire d’images à transformer.

4. Pour **Mode**, spécifiez la finalité de la transformation d’entrée : « Pour l’entraînement » ou « Pour l’inférence ». 

   Si vous sélectionnez **Pour l’entraînement**, toutes les transformations que vous spécifiez dans Init Image Transformation vont être appliquées.

   Si vous sélectionnez **Pour l’inférence**, les transformations telles que la création aléatoire d’échantillons vont être exclues avant d’être appliquées. En effet, les opérations de transformation visant à créer des échantillons de manière aléatoire, par exemple « Random horizontal flip » (Retournement horizontal aléatoire), sont utilisées pour l’augmentation des données dans l’entraînement. Ces opérations de transformation doivent être supprimées de l’inférence, car les échantillons de l’inférence doivent être corrigés pour rendre la prédiction et l’évaluation plus précises.

   > [!NOTE]
   > Les transformations qui vont être exclues en mode **Pour l’inférence** sont les suivantes : Rognage redimensionné aléatoire, Rognage aléatoire, Retournement horizontal aléatoire, Retournement vertical aléatoire, Rotation aléatoire, Affinement aléatoire, Niveaux de gris aléatoires, Perspective aléatoire, Effacement aléatoire.

5. Pour appliquer une transformation d’image à un nouveau répertoire d’images, soumettez le pipeline.  

### <a name="component-parameters"></a>Paramètres de composant

| Nom | Plage | Type | Default                   | Description                              |
| ---- | ----- | ---- | ------------------------- | ---------------------------------------- |
| Mode | Quelconque   | Mode | (Nécessite une précision de la part de l’utilisateur) | Finalité de la transformation d’entrée. Vous devez exclure les opérations de transformation « aléatoires » de l’inférence, mais les conserver pour l’entraînement |

### <a name="expected-inputs"></a>Entrées attendues  

| Nom                       | Type                    | Description                       |
| -------------------------- | ----------------------- | --------------------------------- |
| Transformation d’image d’entrée | TransformationDirectory | Transformation d’image d’entrée        |
| Répertoire d’images d’entrée      | ImageDirectory          | Répertoire d’images à transformer |

### <a name="outputs"></a>Sorties  

| Nom                   | Type           | Description            |
| ---------------------- | -------------- | ---------------------- |
| Répertoire d’images de sortie | ImageDirectory | Répertoire d’images de sortie |

## <a name="next-steps"></a>Étapes suivantes

Consultez [l’ensemble des composants disponibles](component-reference.md) pour Azure Machine Learning. 
