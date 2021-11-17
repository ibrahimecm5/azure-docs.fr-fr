---
title: Utilisation du composant Effectuer le scoring d’un modèle d’image
titleSuffix: Azure Machine Learning
description: Découvrez comment utiliser le composant Effectuer le scoring d’un modèle d’image d’Azure Machine Learning pour générer des prédictions avec un modèle d’image entraîné.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/26/2020
ms.openlocfilehash: 57cd0fb2b4c64010955f189b9cf28ced54883637
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131566804"
---
# <a name="score-image-model"></a>Scorer un modèle d’image

Cet article décrit un composant du concepteur Azure Machine Learning.

Ce composant permet de générer des prédictions avec un modèle d’image entraîné sur des données d’image d’entrée.

## <a name="how-to-configure-score-image-model"></a>Comment configurer le module Score Image Model

1. Ajoutez le composant **Effectuer le scoring d’un modèle d’image** à votre pipeline.

2. Attachez un modèle d’image entraîné et un jeu de données contenant des données d’image d’entrée. 

    Les données doivent être de type ImageDirectory. Pour savoir comment obtenir un répertoire d’images, consultez le composant [Convertir en répertoire d’images](convert-to-image-directory.md). Le schéma du jeu de données d’entrée doit correspondre aussi généralement au schéma des données utilisées pour effectuer l’apprentissage du modèle.

3. Envoyez le pipeline.

## <a name="results"></a>Résultats

Si, après avoir généré un ensemble de scores avec le composant [Effectuer le scoring d’un modèle d’image](score-image-model.md), vous souhaitez générer un ensemble de métriques pour évaluer l’exactitude (niveau de performance) du modèle, vous pouvez connecter ce composant et le jeu de données scoré au composant [Évaluer un modèle](evaluate-model.md). 

### <a name="publish-scores-as-a-web-service"></a>Publier des scores en tant que service web

Une utilisation courante du scoring consiste à retourner la sortie en tant que partie d’un service web prédictif. Pour plus d’informations, consultez [ce didacticiel](../tutorial-designer-automobile-price-deploy.md) sur le déploiement d’un point de terminaison en temps réel basé sur un pipeline dans le concepteur Azure Machine Learning.

## <a name="next-steps"></a>Étapes suivantes

Consultez [l’ensemble des composants disponibles](component-reference.md) pour Azure Machine Learning.