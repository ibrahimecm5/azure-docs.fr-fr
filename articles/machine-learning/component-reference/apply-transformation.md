---
title: 'Appliquer une transformation : référence du composant'
titleSuffix: Azure Machine Learning
description: Découvrez comment utiliser le composant Appliquer une transformation dans Azure Machine Learning pour modifier un jeu de données d’entrée en fonction d’une transformation précédemment calculée.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/26/2020
ms.openlocfilehash: 3091c4d85594f5bcb4236f04f8d58e21dfe125de
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131566933"
---
# <a name="apply-transformation-component"></a>Composant Appliquer une transformation

Cet article décrit un composant dans le concepteur Azure Machine Learning.

Utilisez ce composant pour modifier un jeu de données d’entrée en fonction d’une transformation précédemment calculée. Ce composant est nécessaire si vous devez mettre à jour des transformations dans des pipelines d’inférence.

Par exemple, si vous avez utilisé des scores z pour normaliser vos données de formation à l’aide du composant **Normaliser les données**, vous pouvez utiliser la valeur de score z qui a été calculée également pour la formation pendant la phase de scoring. Dans Azure Machine Learning, vous pouvez enregistrer la méthode de normalisation en tant que transformation, puis utiliser le module **Appliquer une transformation** pour appliquer le score z aux données d’entrée avant le calcul de score.

## <a name="how-to-save-transformations"></a>Enregistrer des transformations

Le concepteur vous permet d’enregistrer des transformations de données, comme des **jeux de données**, pour vous permettre de les utiliser dans d'autres pipelines.

1. Sélectionnez un composant de transformation de données qui s’est exécuté correctement.

1. Sélectionnez l'onglet **Sorties + journaux d'activité**.

1. Recherchez la sortie de la transformation, puis sélectionnez **Inscrire le jeu de données** pour l’enregistrer en tant que composant dans la catégorie **Jeux de données** de la palette des composants.

## <a name="how-to-use-apply-transformation"></a>Comment utiliser le module Appliquer une transformation  
  
1. Ajoutez le composant **Appliquer une transformation** à votre pipeline. Ce composant figure dans la section **Scoring et évaluation d’un modèle** de la palette des composants. 
  
1. Recherchez la transformation enregistrée souhaitée sous **Jeux de données** dans la palette des composants.

1. Connectez la sortie de la transformation enregistrée au port d’entrée gauche du composant **Appliquer une transformation**.

    Le jeu de données doit avoir exactement le même schéma (nombre de colonnes, noms de colonnes, types de données) que le jeu de données pour lequel la transformation a initialement été conçue.  
  
1. Connectez la sortie du jeu de données du composant souhaité au port d’entrée droit du composant **Appliquer une transformation**.
  
1. Pour appliquer une transformation au nouveau jeu de données, envoyez le pipeline.  

> [!IMPORTANT]
> Pour vous assurer que la transformation mise à jour dans les pipelines d’apprentissage est également réalisable dans les pipelines d’inférence, vous devez suivre les étapes ci-dessous chaque fois qu’une transformation est mise à jour dans le pipeline d’apprentissage :
> 1. Dans le pipeline d’apprentissage, inscrivez la sortie de la [Transformation de sélection de colonnes](select-columns-transform.md) en tant que jeu de données.
> ![Inscrire un jeu de données d’une sortie de composant](media/module/select-columns-transform-register-dataset.png)
> 1. Dans le pipeline d’inférence, supprimez le composant **TD-** et remplacez-le par le jeu de données inscrit à l’étape précédente.
> ![Remplacer le composant TD](media/module/replace-tranformation-directory.png)

## <a name="next-steps"></a>Étapes suivantes

Consultez les [composants disponibles](component-reference.md) pour Azure Machine Learning. 