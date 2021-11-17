---
title: 'Attribuer des données au cluster : référence du composant'
titleSuffix: Azure Machine Learning
description: Découvrez comment utiliser le composant Attribuer des données à des clusters dans Azure Machine Learning pour effectuer le scoring du modèle de clustering.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/19/2019
ms.openlocfilehash: fa639154b1f22364fa0923a4162b4d4aaf19c9d3
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131566739"
---
# <a name="component-assign-data-to-clusters"></a>Composant : attribuer des données à des clusters

Cet article décrit comment utiliser le composant *Attribuer des données à des clusters* dans le concepteur Azure Machine Learning. Le composant génère des prédictions via un modèle de clustering qui a été formé avec l’algorithme de *clustering k-moyennes*.

Le composant Attribuer des données à des clusters retourne un jeu de données contenant les attributions probables de chaque nouveau point de données. 

## <a name="how-to-use-assign-data-to-clusters"></a>Comment utiliser le module Attribuer des données à des clusters
  
1. Dans le concepteur Azure Machine Learning, recherchez un modèle de clustering entraîné précédemment. Vous pouvez créer et former un modèle de clustering à l’aide d’une des méthodes suivantes :  
  
    - Configurer l’algorithme de clustering k-moyennes à l’aide du composant [Clustering k-moyennes](k-means-clustering.md) et effectuer l’apprentissage du modèle à l’aide d’un jeu de données et du composant Effectuer l’apprentissage du modèle de clustering (cet article).  
  
    - Vous pouvez également ajouter un modèle de clustering formé existant à partir du groupe **Modèles enregistrés** dans votre espace de travail.

2. Attacher le modèle formé au port d’entrée gauche du module **Attribuer des données à des clusters**.  

3. Attachez un nouveau jeu de données en tant qu’entrée. 

   Dans ce jeu de données, les étiquettes sont facultatives. En règle générale, le clustering est une méthode d’apprentissage non supervisée. Vous n’êtes pas censé connaître les catégories en avance. Toutefois, les colonnes d’entrée doivent être identiques aux colonnes utilisées lors de la formation du modèle de clustering. Dans le cas contraire, une erreur se produit.

    > [!TIP]
    > Pour réduire le nombre de colonnes rédigées dans le concepteur à partir des prédictions de cluster, utilisez [Sélectionner les colonnes dans le jeu de données](select-columns-in-dataset.md), puis sélectionnez un sous-ensemble de colonnes. 
    
4. Cochez la case **Cocher pour ajouter ou décocher pour résultats uniquement** si vous voulez que les résultats comprennent le jeu de données d’entrée entier, y compris une colonne qui affiche les résultats (attributions de cluster).
  
    Si vous décochez cette case, seuls les résultats sont retournés. Cette option peut être utile lorsque vous créez des prédictions dans le cadre d’un service web.
  
5.  Envoyez le pipeline.  
  
### <a name="results"></a>Résultats

+  Pour afficher les valeurs dans le jeu de données, cliquez avec le bouton droit sur le composant, puis sélectionnez **Visualiser**. Vous pouvez aussi sélectionner le composant et basculer vers l’onglet **Sorties** dans le panneau droit, cliquer sur l’icône d’histogramme dans **Sorties de port** pour visualiser le résultat.
