---
title: 'Arbre de décision optimisé multiclasse : référence du composant'
titleSuffix: Azure Machine Learning
description: Découvrez comment utiliser le composant de l’arbre de décision optimisé multiclasse dans Azure Machine Learning pour créer un classifieur à l’aide de données étiquetées.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/19/2020
ms.openlocfilehash: 2c354e5a77adcb9910bf7f277950868ee96e1104
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131567186"
---
# <a name="multiclass-boosted-decision-tree"></a>Arbre de décision optimisé à plusieurs classes

Cet article décrit un composant dans le concepteur Azure Machine Learning.

Ce composant vous permet de créer un modèle Machine Learning reposant sur l’algorithme d’arbres de décision optimisés.

Un arbre de décision optimisé est une méthode d’apprentissage d’ensemble dans laquelle le second arbre corrige les erreurs du premier arbre, le troisième arbre corrige les erreurs du premier et du deuxième arbres, et ainsi de suite. Les prédictions sont basées sur l’ensemble des arborescences.

## <a name="how-to-configure"></a>Comment configurer 

Ce composant crée un modèle de classification non formé. Étant donné que la classification est une méthode d’apprentissage supervisée, vous avez besoin d’un *jeu de données étiqueté* incluant une colonne étiquette avec une valeur pour toutes les lignes.

Vous pouvez effectuer l’apprentissage de ce type de modèle à l’aide de l’article [Entraîner le modèle](././train-model.md). 

1.  Ajoutez le composant **Arbre de décision optimisé multiclasse** à votre pipeline.

1.  Spécifiez le mode d’apprentissage du modèle en définissant l’option **Créer un mode d’apprentissage**.

    + **Single Parameter** (Paramètre unique) : si vous savez comment vous voulez configurer le modèle, vous pouvez fournir un ensemble spécifique de valeurs comme arguments.
    
    + **Plage de paramètres** : Sélectionnez cette option si vous n’êtes pas sûr des paramètres à choisir et que vous souhaitez exécuter un balayage des paramètres. Sélectionnez la plage de valeurs sur laquelle vous souhaitez effectuer l'itération. Le module [Optimiser les hyperparamètres du modèle](tune-model-hyperparameters.md) effectue alors une itération sur toutes les combinaisons possibles des paramètres que vous avez fournis pour déterminer les hyperparamètres qui produisent les résultats optimaux.  

1. **Maximum number of leaves per tree** (Nombre maximal de nœuds terminaux par arbre) limite le nombre maximal de nœuds terminaux qui peuvent être créés dans un arbre quelconque.
    
    En augmentant cette valeur, vous augmentez potentiellement la taille de l’arbre et vous bénéficiez d’un surcroît de précision, au risque d’un surajustement et d’un temps d’apprentissage plus long.
  
1. **Minimum number of samples per leaf node** (Nombre minimal d’échantillons par nœud terminal), indique le nombre de cas requis pour la création d’un nœud terminal dans un arbre.  

    Plus cette valeur est grande, plus le seuil de création de règles augmente. Par exemple, la valeur par défaut de 1, un seul cas suffit à entraîner la création d’une règle. Si la valeur passe à 5, les données d’apprentissage doivent contenir au moins cinq cas remplissant les mêmes conditions.

1. **Learning rate** (taux d’apprentissage) définit la taille de l’étape pendant l’apprentissage. Entrez un nombre compris entre 0 et 1.

    Le taux d’apprentissage détermine la vitesse à laquelle l’apprentissage converge vers une solution optimale. Si la taille de pas est trop élevée, vous risquez de passer à côté de la solution optimale. Si la taille de pas est trop faible, l’apprentissage nécessite plus de temps pour converger vers la meilleure solution.

1. **Number of trees constructed** (Nombre d’arbres construits) indique le nombre total d’arbres de décision à créer dans l’ensemble. En créant plusieurs arbres de décision, vous pouvez obtenir une meilleure couverture, mais cette opération augmente la durée d’apprentissage.

1. **Random number seed** (Valeur de départ aléatoire) définit un entier non négatif à utiliser comme valeur initiale aléatoire. La spécification d’une valeur de départ garantit la reproductibilité entre les exécutions qui présentent les mêmes données et paramètres.  

    La valeur de départ aléatoire est définie par défaut sur 42. Les exécutions successives utilisant des valeurs de départ aléatoires différentes peuvent avoir des résultats différents.

1. Effectuez l’apprentissage du modèle :

    + Si vous définissez **Create trainer mode** (Créer un mode d’apprentissage) sur **Single Parameter** (Paramètre unique), connectez un jeu de données balisé au module [Entraîner le composant](train-model.md).  
  
    + Si vous définissez **Créer un mode d’entraînement** sur **Plage de paramètres**, connectez un jeu de données avec balises et entraînez le modèle en utilisant [Optimiser les hyperparamètres du modèle](tune-model-hyperparameters.md).  
  
    > [!NOTE]
    > 
    > Si vous transmettez une plage de paramètres à [Entraîner le modèle](train-model.md), elle utilise uniquement la valeur par défaut dans la liste de paramètres unique.  
    > 
    > Si vous transmettez un ensemble unique de valeurs de paramètre au composant [Optimiser les hyperparamètres du composant](tune-model-hyperparameters.md), quand il attend une plage de paramètres pour chaque paramètre, il ignore les valeurs et utilise les valeurs par défaut pour l’apprenant.  
    > 
    > Si vous sélectionnez l’option **Plage de paramètres** et que vous entrez une valeur unique pour un paramètre, cette valeur unique que vous avez spécifiée est utilisée tout au long du balayage, même si d’autres paramètres changent dans une plage de valeurs.

## <a name="next-steps"></a>Étapes suivantes

Consultez les [composants disponibles](component-reference.md) pour Azure Machine Learning. 
