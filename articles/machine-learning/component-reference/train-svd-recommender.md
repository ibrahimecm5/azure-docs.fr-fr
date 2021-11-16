---
title: 'Effectuer l’apprentissage d’un générateur de recommandations SVD : informations de référence sur le composant'
titleSuffix: Azure Machine Learning
description: Découvrez comment utiliser le composant Effectuer l’apprentissage d’un générateur de recommandations SVD d’Azure Machine Learning pour effectuer l’apprentissage d’un générateur de recommandations bayésien avec l’algorithme SVD.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/17/2021
ms.openlocfilehash: 18cab5a14f5dfbd092da9fe9bdde8dca4c129b86
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131566851"
---
# <a name="train-svd-recommender"></a>Entraîner le générateur de recommandations SVD

Cet article explique comment utiliser le composant Effectuer l’apprentissage d’un générateur de recommandations SVD du concepteur Azure Machine Learning. Ce composant permet d’effectuer l’apprentissage d’un modèle de recommandation basé sur l’algorithme de décomposition en valeurs singulières (SVD, Singular Value Decomposition).  

Le composant Effectuer l’apprentissage d’un générateur de recommandations SVD lit un jeu de données de triplets utilisateur-élément-évaluation. Il retourne un générateur de recommandations SVD formé. Vous pouvez ensuite utiliser le modèle entraîné pour prédire des évaluations ou générer des recommandations en connectant le composant [Effectuer le scoring d’un générateur de recommandations SVD](score-svd-recommender.md).  


  
## <a name="more-about-recommendation-models-and-the-svd-recommender"></a>En savoir plus sur les modèles de recommandation et le générateur de recommandations SVD  

L’objectif principal d’un système de recommandation est de recommander un ou plusieurs *éléments* aux *utilisateurs* du système. Un film, un restaurant, un livre ou une chanson peuvent être des exemples d’un élément. Un utilisateur peut être une personne, un groupe de personnes ou une autre entité avec des préférences d’élément.  

Il existe deux approches principales pour les systèmes générateurs de recommandations : 

+ Une approche **basée sur le contenu** utilise des fonctionnalités à la fois pour les utilisateurs et les éléments. Les utilisateurs peuvent être décrits par des propriétés telles que l’âge et le sexe. Les éléments peuvent être décrits par des propriétés telles que l’auteur et le fabricant. Vous trouverez des exemples typiques de systèmes générateurs de recommandations basés sur le contenu sur les réseaux sociaux. 
+ Le **filtrage collaboratif** utilise uniquement les identificateurs des utilisateurs et des éléments. Il obtient des informations implicites sur ces entités à partir d’une matrice (fragmentée) de notations données aux éléments par les utilisateurs. Nous pouvons en apprendre plus sur un utilisateur à partir des éléments qu’il a évalués et des autres utilisateurs qui ont évalué les mêmes éléments.  

Le générateur de recommandations SVD utilise les identificateurs des utilisateurs et des éléments, ainsi qu’une matrice d’évaluations données par les utilisateurs aux éléments. Il s’agit d’un *générateur de recommandations collaboratif*. 

Pour plus d’informations sur le générateur de recommandations SVD, consultez l’article de recherche [Techniques de factorisation de la matrice pour les systèmes de générateur de recommandations](https://datajobs.com/data-science-repo/Recommender-Systems-[Netflix].pdf).


## <a name="how-to-configure-train-svd-recommender"></a>Comment configurer le module Entraîner le générateur de recommandations SVD  

### <a name="prepare-data"></a>Préparer les données

Pour pouvoir utiliser le composant, vous devez faire en sorte que vos données d’entrée soient au format attendu par le modèle de recommandation. Un jeu de données d’entraînement composé de triplets utilisateur-élément-notation est requis.

+ La première colonne contient les identificateurs des utilisateurs.
+ La deuxième colonne contient les identificateurs des éléments.
+ La troisième colonne contient l’évaluation de la paire utilisateur-élément. Les valeurs d’évaluation doivent être de type numérique.  

Le jeu de données **Notations de films** dans le concepteur Azure Machine Learning (sélectionnez **Jeux de données**, puis **Exemples**) illustre le format attendu :

![Classifications des films](media/module/movie-ratings-dataset.png)

À partir de cet exemple, vous pouvez voir qu’un utilisateur unique a évalué plusieurs films. 

### <a name="train-the-model"></a>Effectuer l’apprentissage du modèle

1.  Ajoutez le composant Effectuer l’apprentissage d’un générateur de recommandations SVD à votre pipeline dans le concepteur, puis connectez-le aux données d’apprentissage.  
   
2.  Pour **Nombre de facteurs**, spécifiez le nombre de facteurs à utiliser avec le générateur de recommandations.  
    
    Chaque facteur mesure le degré de relation entre l’utilisateur et l’élément. Le nombre de facteurs est également la dimensionnalité de l’espace de facteurs latent. Quand les nombres d’utilisateurs et d’éléments augmentent, il est préférable de définir un plus grand nombre de facteurs. Toutefois, si ce nombre est trop important, les performances peuvent chuter.
    
3.  **Number of recommendation algorithm iterations** (Nombre d’itérations de l’algorithme de recommandation) indique le nombre de fois que l’algorithme doit traiter les données d’entrée. Plus ce nombre est élevé, plus les prédictions sont justes. Toutefois, un nombre plus élevé signifie un entraînement plus lent. La valeur par défaut est 30.

4.  Pour **Learning rate** (Taux d’apprentissage), entrez un nombre compris entre 0,0 et 2,0 qui définira la taille de pas pour l’apprentissage.

    Le taux d’apprentissage détermine la taille de pas à chaque itération. Si la taille de pas est trop élevée, vous risquez de passer à côté de la solution optimale. Si la taille de pas est trop faible, l’entraînement nécessite plus de temps pour trouver la meilleure solution. 
  
5.  Envoyez le pipeline.  

## <a name="results"></a>Résultats

Une fois l’exécution du pipeline effectuée, si vous souhaitez utiliser le modèle à des fins de scoring, connectez le module [Effectuer l'apprentissage d’un générateur de recommandations SVD](train-svd-recommender.md) à [Noter le générateur de recommandations SVD](score-svd-recommender.md), pour prédire les valeurs des nouveaux exemples d’entrée.

## <a name="next-steps"></a>Étapes suivantes

Consultez [l’ensemble des composants disponibles](component-reference.md) pour Azure Machine Learning. 
