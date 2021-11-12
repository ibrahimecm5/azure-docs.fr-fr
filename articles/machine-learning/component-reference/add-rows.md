---
title: 'Ajouter des lignes : référence de composant'
titleSuffix: Azure Machine Learning
description: Découvrez comment utiliser le composant Ajouter des lignes dans le concepteur Azure Machine Learning pour concaténer deux jeux de données.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 856821f810397f6b14b383281d2e566b9c6e16d2
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131566936"
---
# <a name="add-rows-component"></a>Composant Ajouter des lignes

Cet article décrit un composant dans le concepteur Azure Machine Learning.

Utilisez ce composant pour concaténer deux jeux de données. Dans la concaténation, les lignes du deuxième jeu de données sont ajoutées à la fin du premier jeu de données.  
  
La concaténation de lignes est utile dans des scénarios tels que les suivants :  
  
+ Vous avez généré une série de statistiques d’évaluation, et vous souhaitez les combiner dans une table pour simplifier la génération de rapports.  
  
+ Vous utilisez des jeux de données différents, et vous souhaitez combiner les jeux de données pour créer un jeu de données final.  

## <a name="how-to-use-add-rows"></a>Procédure d’utilisation de Ajouter des lignes  

Pour concaténer des lignes de deux jeux de données, les lignes doivent avoir exactement le même schéma. Cela signifie le même nombre de colonnes et le même type de données dans les colonnes.

1.  Faites glisser le composant **Ajouter des lignes** dans votre pipeline (il se trouve sous **Transformation des données**).

2. Connectez les jeux de données aux deux ports d’entrée. Le jeu de données que vous souhaitez ajouter doit être connecté au deuxième port (droite). 
  
3.  Envoyez le pipeline. Le nombre de lignes dans le jeu de données de sortie doit être égal à la somme des lignes de deux jeux de données d’entrée.

    Si vous ajoutez le même jeu de données aux deux entrées du composant **Ajouter des lignes**, le jeu de données est dupliqué. 

## <a name="next-steps"></a>Étapes suivantes

Consultez les [composants disponibles](component-reference.md) pour Azure Machine Learning. 