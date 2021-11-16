---
title: ResNet
titleSuffix: Azure Machine Learning
description: Découvrez comment créer un modèle de classification d’images dans le concepteur Azure Machine Learning à l’aide de l’algorithme ResNet.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/26/2020
ms.openlocfilehash: c6be11cc4dafb5423d38bc869fc8b915b48814ce
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131566697"
---
# <a name="resnet"></a>ResNet

Cet article explique comment utiliser le composant **ResNet** du concepteur Azure Machine Learning pour créer un modèle de classification d’images avec l’algorithme ResNet.  

Cet algorithme de classification est une méthode d’apprentissage supervisé qui nécessite un jeu de données étiqueté. 
> [!NOTE]
> Ce composant ne prend pas en charge le jeu de données étiqueté généré à partir du composant *Étiquetage de données* dans le studio, mais seulement le répertoire d’images étiqueté généré à partir du composant [Convertir en répertoire d’images](convert-to-image-directory.md). 

Pour entraîner un modèle, indiquez ce dernier ainsi qu’un répertoire d’images étiquetées en tant qu’entrées du module [Train Pytorch Model](train-pytorch-model.md). Vous pouvez ensuite utiliser le modèle entraîné pour prédire les valeurs des nouveaux exemples d’entrée à l’aide du module [Score Image Model](score-image-model.md).

### <a name="more-about-resnet"></a>En savoir plus sur ResNet

Pour plus d’informations, consultez [cet article](https://pytorch.org/vision/stable/models.html#torchvision.models.resnext101_32x8d) sur ResNet.

## <a name="how-to-configure-resnet"></a>Guide pratique pour configurer ResNet

1.  Ajoutez le composant **ResNet** à votre pipeline dans le concepteur.  

2.  Pour **Model name** (Nom du modèle), indiquez le nom d’une structure ResNet spécifique que vous pouvez sélectionner parmi les structures resnet prises en charge : « resnet18 », « resnet34 », « resnet50 », « resnet101 », « resnet152 », « resnext50\_32x4d », « resnext101\_32x8d », « wide_resnet50\_2 », « wide_resnet101\_2 ».

3.  Pour **Pretrained** (Préentraîné), indiquez si vous souhaitez utiliser un modèle préentraîné sur ImageNet. Si cette option est sélectionnée, vous pouvez affiner le modèle en fonction du modèle préentraîné sélectionné. Si cette option est désélectionnée, vous pouvez commencer l’entraînement à partir de zéro.

4.  Pour **Zero init residual** (Initialisation avec des zéros des branches résiduelles), indiquez si la dernière couche de traitement par lots doit être initialisée avec des zéros dans chaque branche résiduelle. Si cette option est sélectionnée, la branche résiduelle commence par des zéros, et chaque bloc résiduel se comporte comme une identité. Cela peut aider à la convergence pour les lots de grande taille, conformément à https://arxiv.org/abs/1706.02677.

5.  Connectez la sortie du composant **ResNet** et du composant de jeu de données d’images d’apprentissage et de validation au composant [Effectuer l’apprentissage d’un modèle PyTorch](train-pytorch-model.md). 

6.  Envoyez le pipeline.

## <a name="results"></a>Résultats

Une fois l'exécution du pipeline effectuée, si vous souhaitez utiliser le modèle à des fins de scoring, connectez le module [Train PyTorch Model](train-pytorch-model.md) au module [Score Image Model](score-image-model.md) (Scorer un modèle d'image) pour prédire les valeurs des nouveaux exemples d'entrée.

## <a name="technical-notes"></a>Notes techniques  

###  <a name="component-parameters"></a>Paramètres de composant  

| Nom       | Plage | Type    | Default           | Description                              |
| ---------- | ----- | ------- | ----------------- | ---------------------------------------- |
| Nom du modèle | Quelconque   | Mode    | resnext101\_32x8d | Nom d’une structure ResNet spécifique       |
| Pretrained (Préentraîné) | Quelconque   | Boolean | True              | Spécifie si un modèle préentraîné doit être utilisé ou non sur ImageNet |
| Zero init residual (Initialisation avec des zéros des branches résiduelles) | Quelconque | Boolean | False | Spécifie si la dernière couche de traitement par lots doit être initialisée avec des zéros dans chaque branche résiduelle |
|            |       |         |                   |                                          |

###  <a name="output"></a>Output  

| Nom            | Type                    | Description                              |
| --------------- | ----------------------- | ---------------------------------------- |
| Untrained model (Modèle non entraîné) | UntrainedModelDirectory | Modèle ResNet non entraîné qui peut être connecté au module Train Pytorch Model. |

## <a name="next-steps"></a>Étapes suivantes

Consultez [l’ensemble des composants disponibles](component-reference.md) pour Azure Machine Learning. 