---
title: 'Régression logistique multiclasse : informations de référence sur le composant'
titleSuffix: Azure Machine Learning
description: Découvrez comment utiliser le composant Régression logistique multiclasse du concepteur Azure Machine Learning pour prédire plusieurs valeurs.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/22/2020
ms.openlocfilehash: a5c823b2fc2464907ef224bc3a34d5150dfe3ba2
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131566874"
---
# <a name="multiclass-logistic-regression-component"></a>Composant Régression logistique multiclasse

Cet article décrit un composant du concepteur Azure Machine Learning.

Ce composant permet de créer un modèle de régression logistique servant à prédire plusieurs valeurs.

La classification à l’aide de la régression logistique est une méthode d’apprentissage supervisé qui nécessite donc un jeu de données étiqueté. Pour effectuer l’apprentissage d’un modèle, vous pouvez fournir le modèle et le jeu de données étiqueté en entrée d’un composant du type [Effectuer l’apprentissage d’un modèle](./train-model.md). Le modèle entraîné est ensuite utilisable pour prédire les valeurs de nouveaux exemples d’entrées.

Azure Machine Learning fournit également un composant [Régression logistique à deux classes](./two-class-logistic-regression.md), adapté à la classification des variables binaires ou dichotomiques.

## <a name="about-multiclass-logistic-regression"></a>Présentation de la régression logistique multiclasse

La régression logistique est une méthode bien connue en statistiques qui sert à prédire la probabilité d’un résultat et qui est fréquemment utilisée pour les tâches de classification. L’algorithme prédit la probabilité d’occurrence d’un événement en ajustant les données à une fonction logistique. 

Dans une régression logistique multiclasse, le classifieur permet de prédire plusieurs résultats.

## <a name="configure-a-multiclass-logistic-regression"></a>Configurer une régression logistique multiclasse

1. Ajoutez le composant **Régression logistique multiclasse** au pipeline.

2. Spécifiez le mode d’apprentissage du modèle en définissant l’option **Créer un mode d’apprentissage**.

    + **Single Parameter** (Paramètre unique) : sélectionnez cette option si vous savez comment vous voulez configurer le modèle, et fournissez un ensemble spécifique de valeurs en tant qu’arguments.

    + **Plage de paramètres** : Sélectionnez cette option si vous n’êtes pas sûr des paramètres à choisir et que vous souhaitez exécuter un balayage des paramètres. Sélectionnez la plage de valeurs sur laquelle vous souhaitez effectuer l’itération. Le module [Optimiser les hyperparamètres du modèle](tune-model-hyperparameters.md) effectue alors une itération sur toutes les combinaisons possibles des paramètres que vous avez fournis pour déterminer les hyperparamètres qui produisent les résultats optimaux.  

3. **Tolérance d’optimisation** : spécifiez la valeur de seuil de convergence de l’optimiseur. Si l’amélioration entre les itérations est inférieure à ce seuil, l’algorithme s’arrête et renvoie le modèle actuel.

4. **L1 regularization weight** (Pondération de régularisation L1), **L2 regularization weight** (L2 regularization weight) : saisissez une valeur à utiliser pour les paramètres de régularisation L1 et L2. La définition d’une valeur non nulle est recommandée pour les deux options.

    La régularisation est une méthode permettant d’empêcher le surajustement en pénalisant les modèles qui présentent des valeurs de coefficient extrêmes. La régularisation fonctionne en ajoutant la pénalité associée aux valeurs de coefficient à l’erreur de l’hypothèse. Un modèle précis avec des valeurs de coefficient extrêmes est pénalisé davantage, alors qu’un modèle moins précis avec des valeurs plus conservatrices est moins pénalisé.

     Les régularisations L1 et L2 ont différents effets et cas d’utilisation. La régularisation L1 est applicable aux modèles dispersés, ce qui est utile lorsque vous travaillez avec des données de grande dimension. En revanche, la régularisation L2 est préférable pour les données qui ne sont pas dispersées.  Cet algorithme prend en charge une combinaison linéaire de valeurs de régularisation L1 et L2 : autrement dit, si `x = L1` et `y = L2`, `ax + by = c` définit l’étendue linéaire des termes de régularisation.

     Différentes combinaisons linéaires de termes L1 et L2 ont été imaginées pour les modèles de régression logistique, comme la [régularisation de réseau élastique](https://wikipedia.org/wiki/Elastic_net_regularization).

6. **Random number seed** (Valeur de départ aléatoire) : saisissez une valeur entière à utiliser comme valeur initiale pour l’algorithme si vous souhaitez que les résultats puissent se répéter à chaque exécution. Sinon, une valeur d’horloge système est utilisée comme valeur initiale, ce qui peut générer des résultats légèrement différents dans les diverses exécutions du même pipeline.

8. Connectez un jeu de données étiqueté et entraînez le modèle :

    + Si vous définissez **Créer un mode d’apprentissage** sur **Un seul paramètre**, connectez un jeu de données étiqueté au composant [Effectuer l’apprentissage d’un modèle](train-model.md).  
  
    + Si vous définissez **Créer un mode d’entraînement** sur **Plage de paramètres**, connectez un jeu de données avec balises et entraînez le modèle en utilisant [Optimiser les hyperparamètres du modèle](tune-model-hyperparameters.md).  
  
    > [!NOTE]
    > 
    > Si vous transmettez une plage de paramètres à [Entraîner le modèle](train-model.md), elle utilise uniquement la valeur par défaut dans la liste de paramètres unique.  
    > 
    > Si vous transmettez un seul ensemble de valeurs de paramètre au composant [Ajuster les hyperparamètres d’un modèle](tune-model-hyperparameters.md), il ignore les valeurs et utilise les valeurs par défaut de l’apprenant quand il attend une plage de paramètres pour chaque paramètre.  
    > 
    > Si vous sélectionnez l’option **Plage de paramètres** et que vous entrez une valeur unique pour un paramètre, cette valeur unique que vous avez spécifiée est utilisée tout au long du balayage, même si d’autres paramètres changent sur une plage de valeurs.

9. Envoyez le pipeline.



## <a name="next-steps"></a>Étapes suivantes

Consultez [l’ensemble des composants disponibles](component-reference.md) pour Azure Machine Learning. 