---
title: 'Effectuer l’apprentissage du modèle de détection d’anomalie : référence du composant'
titleSuffix: Azure Machine Learning
description: Découvrez comment utiliser le composant Effectuer l’apprentissage du modèle de détection d’anomalie pour créer un modèle de détection d’anomalie formé.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 269769c8bc80986230cbbee4225a38523f605724
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131566864"
---
# <a name="train-anomaly-detection-model-component"></a>Composant Effectuer l’apprentissage du modèle de détection d’anomalie

Cet article explique comment utiliser le composant Entraîner le modèle de détection d’anomalie dans le concepteur Azure Machine Learning pour créer un modèle de détection d’anomalie entraîné.

Le composant prend comme entrée un ensemble de paramètres pour un modèle de détection d’anomalie et un jeu de données sans étiquettes. Il renvoie un modèle de détection d’anomalie formé, ainsi qu’un jeu d’étiquettes pour les données d’apprentissage.  

Pour plus d’informations sur les algorithmes de détection d’anomalie fournis dans le concepteur, consultez [Détection d’anomalie reposant sur l’ACP](pca-based-anomaly-detection.md).  

## <a name="how-to-configure-train-anomaly-detection-model"></a>Comment configurer le module Effectuer l’apprentissage du modèle de détection d’anomalie 

1.  Ajoutez le composant **Effectuer l’apprentissage du modèle de détection d’anomalie** à votre pipeline dans le concepteur. Ce composant figure dans la catégorie **Détection d’anomalie**.

2. Connectez l’un des composants conçus pour la détection d’anomalie, par exemple [Détection d’anomalie reposant sur l’ACP](pca-based-anomaly-detection.md).

    Les autres types de modèles ne sont pas pris en charge. Quand vous exécutez le pipeline, vous obtenez l’erreur « Tous les modèles doivent avoir le même type d’apprenant. »  

3.  Configurez le composant de détection d’anomalie en choisissant la colonne d’étiquette et en définissant d’autres paramètres spécifiques à l’algorithme.  

4.  Attachez un jeu de données d’entraînement à l’entrée côté droit du module **Entraîner le modèle de détection des anomalies**.  

5.  Envoyez le pipeline.  

## <a name="results"></a>Résultats

Une fois l’apprentissage terminé :

+ Pour afficher les paramètres du modèle, cliquez avec le bouton droit sur le composant, puis sélectionnez **Visualiser**. 

+ Pour créer des prédictions, utilisez le composant [Noter le modèle](score-model.md) avec les nouvelles données d’entrée.

+ Pour enregistrer un instantané du modèle entraîné, sélectionnez le composant. Sélectionnez ensuite l’icône **Enregistrer le jeu de données** sous l’onglet **Sorties + journaux** dans le volet droit.   

 
## <a name="next-steps"></a>Étapes suivantes

Consultez les [composants disponibles](component-reference.md) pour Azure Machine Learning. 

Pour obtenir la liste des erreurs spécifiques aux composants du concepteur, consultez [Exceptions et codes d’erreur du concepteur](designer-error-codes.md).
'