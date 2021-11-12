---
title: 'Régression de forêt d’arbres de décision : référence du composant'
titleSuffix: Azure Machine Learning
description: Découvrez comment utiliser le composant de régression de forêt d’arbres de décision dans Azure Machine Learning pour créer un modèle de régression basé sur un ensemble d’arbres de décision.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/22/2020
ms.openlocfilehash: cc3d1752dbf28d5564aefb309a0cf5ae4a48cd5a
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131566704"
---
# <a name="decision-forest-regression-component"></a>composant Régression de forêt d’arbres de décision

Cet article décrit un composant dans le concepteur Azure Machine Learning.

Utilisez ce composant pour créer un modèle de régression basé sur un ensemble d’arbres de décision.

Une fois que vous avez configuré le modèle, vous devez le former à l’aide d’un jeu de données étiqueté et du composant [Entraîner un modèle](./train-model.md). Le modèle ainsi formé permet ensuite d’effectuer des prédictions. 

## <a name="how-it-works"></a>Fonctionnement

Les arbres de décision sont des modèles non paramétriques qui effectuent une séquence de tests simples pour chaque instance, en traversant une structure de données d’arbre binaire jusqu’à atteindre un nœud terminal (décision).

Les arbres de décision présentent les avantages suivants :

- Ils sont efficaces en matière de calcul et d’utilisation de la mémoire, lors de la formation et de la prédiction.

- Ils peuvent représenter des limites de décisions non linéaires.

- Ils effectuent la sélection des fonctionnalités intégrées ainsi que leur classification et sont résilients en présence de fonctionnalités bruyantes.

Ce modèle de régression se compose d’un ensemble d’arbres de décision. Chaque arbre d’une forêt de décision de régression génère une distribution gaussienne sous la forme d’une prédiction. Une agrégation est effectuée sur l’ensemble des arbres, afin de trouver la distribution gaussienne la plus proche de la distribution combinée, pour tous les arbres du modèle.

Pour en savoir plus sur l’infrastructure théorique de cet algorithme et son implémentation, consultez cet article : [Decision Forests: A Unified Framework for Classification, Regression, Density Estimation, Manifold Learning and Semi-Supervised Learning](https://www.microsoft.com/en-us/research/publication/decision-forests-a-unified-framework-for-classification-regression-density-estimation-manifold-learning-and-semi-supervised-learning/?from=http%3A%2F%2Fresearch.microsoft.com%2Fapps%2Fpubs%2Fdefault.aspx%3Fid%3D158806#) (Forêts de décision : Structure unifiée de classification, de régression, d’estimation de la densité, d’apprentissage polyvalent et d’apprentissage semi-supervisé)

## <a name="how-to-configure-decision-forest-regression-model"></a>Comment configurer le modèle de régression de forêt de décision

1. Ajoutez le composant **Régression de forêt de décision** au pipeline. Vous pouvez rechercher le composant dans le concepteur sous **Machine Learning**, **Initialiser le modèle** et **Régression**.

2. Ouvrez les propriétés du composant et, pour **Méthode de rééchantillonnage**, choisissez la méthode utilisée pour créer des arbres individuels.  **Bagging** ou **Réplication**.

    - **Bagging** : le bagging, également appelé *agrégation boostrap*, Chaque arbre d’une forêt de décision de régression génère une distribution gaussienne, sous la forme d’une prédiction. L’agrégation consiste à trouver une distribution gaussienne dont les deux premiers moments correspondent à des moments du mélange de distributions gaussiennes obtenu via la combinaison de toutes les distributions gaussiennes retournées par des arbres individuels.

         Pour en savoir plus, consultez l’entrée Wikipedia relative au [Bootstrap Aggregating](https://wikipedia.org/wiki/Bootstrap_aggregating).

    - **Réplication** : avec la réplication, l’apprentissage de chacun des arbres est effectué sur les mêmes données d’entrée. Le prédicat fractionné utilisé pour chaque nœud d’arbre est toujours déterminé de manière aléatoire ; les arbres sont variés.

         Pour en savoir plus sur le processus d’apprentissage avec l’option **Répliquer**, voir [Decision Forests for Computer Vision and Medical Image Analysis (Forêts de décision pour la vision par ordinateur et l’analyse des images médicales) Criminisi and J. Shotton. Springer 2013](https://research.microsoft.com/projects/decisionforests/).

3. Spécifiez le mode d’apprentissage du modèle en définissant l’option **Créer un mode d’apprentissage**.

    - **Single Parameter** (Paramètre unique)

      si vous savez comment vous voulez configurer le modèle, vous pouvez fournir un ensemble spécifique de valeurs comme arguments. Vous l’avez peut-être découvert ces valeurs par expérimentation, ou vous les avez reçues à titre de guide.

    - **Plage de paramètres** : Sélectionnez cette option si vous n’êtes pas sûr des paramètres à choisir et que vous souhaitez exécuter un balayage des paramètres. Sélectionnez la plage de valeurs sur laquelle vous souhaitez effectuer l’itération. Le module [Optimiser les hyperparamètres du modèle](tune-model-hyperparameters.md) effectue alors une itération sur toutes les combinaisons possibles des paramètres que vous avez fournis pour déterminer les hyperparamètres qui produisent les résultats optimaux. 



4. Dans le champ **Number of trees constructed** (Nombre d’arbres construits), indiquez le nombre total d’arbres de décision à créer dans l’ensemble. En créant plusieurs arbres de décision, vous pouvez obtenir une meilleure couverture, mais cette opération augmente la durée d’apprentissage.

    > [!TIP]
    > Toutefois, si vous définissez la valeur sur 1, cela signifie qu’un seul arbre est généré (celui qui inclut le jeu de paramètres initial) et qu’aucune autre itération n’est effectuée.

5. Dans le champ **Profondeur maximale des arbres de décision**, tapez un nombre pour limiter la profondeur de chacun des arbres de décision. Le fait d’augmenter la profondeur de l’arbre est susceptible d’améliorer la précision, au prix d’un risque de surajustement et d’augmentation du temps d’apprentissage.

6. Dans le champ **Nombre de fractionnements aléatoires par nœud**, tapez le nombre de fractionnements à utiliser pour créer chacun des nœuds de l’arbre. Le *fractionnement* consiste à diviser de manière aléatoire les fonctionnalités à chaque niveau de l’arbre (nœud).

7. Dans le champ **Nombre minimal d’échantillons par nœud terminal**, indiquez le nombre minimal de cas requis pour la création d’un nœud terminal dans un arbre.

     Plus cette valeur est grande, plus le seuil de création de règles augmente. Par exemple, la valeur par défaut de 1, un seul cas suffit à entraîner la création d’une règle. Si la valeur passe à 5, les données d’apprentissage doivent contenir au moins cinq cas remplissant les mêmes conditions.


9. Entraînez le modèle :

    + Si vous définissez **Create trainer mode** (Créer un mode d’apprentissage) sur **Single Parameter** (Paramètre unique), connectez un jeu de données balisé au module [Entraîner le composant](train-model.md).  
  
    + Si vous définissez **Créer un mode d’entraînement** sur **Plage de paramètres**, connectez un jeu de données avec balises et entraînez le modèle en utilisant [Optimiser les hyperparamètres du modèle](tune-model-hyperparameters.md).  
  
    > [!NOTE]
    > 
    > Si vous transmettez une plage de paramètres à [Entraîner le modèle](train-model.md), elle utilise uniquement la valeur par défaut dans la liste de paramètres unique.  
    > 
    > Si vous transmettez un ensemble unique de valeurs de paramètre au module [Optimiser les hyperparamètres du composant](tune-model-hyperparameters.md), quand il attend une plage de paramètres pour chaque paramètre, il ignore les valeurs et utilise les valeurs par défaut pour l’apprenant.  
    > 
    > Si vous sélectionnez l’option **Plage de paramètres** et que vous entrez une valeur unique pour un paramètre, cette valeur unique que vous avez spécifiée est utilisée tout au long du balayage, même si d’autres paramètres changent sur une plage de valeurs.

   

10. Envoyez le pipeline.

### <a name="results"></a>Résultats

Une fois l’apprentissage terminé :

+ Pour enregistrer un instantané du modèle entraîné, sélectionnez le composant d’entraînement, puis basculez vers l’onglet **Sorties** dans le panneau droit. Cliquez sur l’icône **Inscrire le modèle**.  Vous pouvez rechercher le modèle enregistré en tant que composant dans l’arborescence des composants. 

## <a name="next-steps"></a>Étapes suivantes

Consultez les [composants disponibles](component-reference.md) pour Azure Machine Learning. 