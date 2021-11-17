---
title: 'Effectuer le scoring du modèle : référence du composant'
titleSuffix: Azure Machine Learning
description: Découvrez comment utiliser le composant Effectuer le scoring du modèle dans Azure Machine Learning pour générer des prédictions à l’aide d’un modèle de classification ou de régression formé.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: 83e466e7218b327bbd871c6693466a10744c9b79
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131566698"
---
# <a name="score-model"></a>Noter un modèle

Cet article décrit un composant dans le concepteur Azure Machine Learning.

Utilisez ce composant pour générer des prédictions à l’aide d’un modèle de classification ou de régression formé.

## <a name="how-to-use"></a>Utilisation

1. Ajoutez le composant **Effectuer le scoring d’un modèle** à votre pipeline.

2. Joignez un modèle entraîné et un jeu de données contenant de nouvelles données d’entrée. 

    Les données doivent être dans un format compatible avec le type de modèle entraîné que vous utilisez. Le schéma du jeu de données d’entrée doit correspondre aussi généralement au schéma des données utilisées pour effectuer l’apprentissage du modèle.

3. Envoyez le pipeline.

## <a name="results"></a>Résultats

Une fois que vous avez généré un jeu de résultats à l’aide du module [Noter le modèle](./score-model.md) :

+ Pour générer un ensemble de métriques utilisées pour évaluer la précision du modèle (performance), vous pouvez connecter le jeu de données évalué au module [Évaluer le modèle](./evaluate-model.md). 
+ Cliquez avec le bouton droit sur le composant et sélectionnez **Visualiser** pour afficher un échantillon des résultats.
<!-- + To Save the results to a dataset. -->

Le score, ou la valeur prédite, peut être indiqué dans différents formats en fonction du modèle et de vos données d’entrée :

- Pour les modèles de classification, le module [Noter le modèle](./score-model.md) génère une valeur prédite pour la classe, ainsi que la probabilité de la valeur prédite.
- Pour les modèles de régression, le module [Noter le modèle](./score-model.md) génère simplement la valeur numérique prédite.


## <a name="publish-scores-as-a-web-service"></a>Publier des scores en tant que service web

Une utilisation courante du scoring consiste à retourner la sortie en tant que partie d’un service web prédictif. Pour plus d’informations, consultez [ce didacticiel](../tutorial-designer-automobile-price-deploy.md) sur le déploiement d’un point de terminaison en temps réel basé sur un pipeline dans le concepteur Azure Machine Learning.

## <a name="next-steps"></a>Étapes suivantes

Consultez les [composants disponibles](component-reference.md) pour Azure Machine Learning.