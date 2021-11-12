---
title: Totaliser les données
titleSuffix: Azure Machine Learning
description: Découvrez comment utiliser le composant Synthétiser les données dans Azure Machine Learning afin de générer un rapport de statistiques descriptives de base pour les colonnes d’un jeu de données.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 01/27/2020
ms.openlocfilehash: 459804fb7c3a962b7d3ee29928984f435f0dea06
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131566824"
---
# <a name="summarize-data"></a>Totaliser les données

Cet article décrit un composant du concepteur Azure Machine Learning.

Utilisez le composant Synthétiser les données pour créer un ensemble de mesures statistiques standard qui décrivent chaque colonne de la table d’entrée.

Les statistiques récapitulatives sont utiles pour comprendre les caractéristiques du jeu de données complet. Par exemple, vous pouvez vous poser les questions suivantes :

- Combien y a-t-il de valeurs manquantes dans chaque colonne ?
- Combien y a-t-il de valeurs uniques dans une colonne de caractéristiques ?
- Quels sont la moyenne et l’écart type de chaque colonne ?

Le composant calcule les scores importants de chaque colonne et retourne une ligne de statistiques récapitulatives pour chaque variable (colonne de données) fournie comme entrée.

## <a name="how-to-configure-summarize-data"></a>Comment configurer le module Synthétiser les données  

1. Ajoutez le composant **Synthétiser les données** à votre pipeline. Ce composant se trouve dans la catégorie **Fonctions statistiques** du concepteur.

1. Connectez le jeu de données pour lequel vous voulez générez un rapport.

    Si vous voulez créer un rapport sur certaines colonnes seulement, utilisez le composant [Sélectionner des colonnes dans le jeu de données](select-columns-in-dataset.md) pour projeter un sous-ensemble de colonnes à utiliser.

1. Aucun paramètre supplémentaire n’est nécessaire. Par défaut, le composant analyse toutes les colonnes fournies en entrée et, en fonction du type des valeurs dans les colonnes, génère un ensemble de statistiques pertinent, comme décrit dans la section [Résultats](#results).

1. Envoyez le pipeline.

## <a name="results"></a>Résultats

Le rapport du composant peut comprendre les statistiques suivantes. 

|Nom de la colonne|Description|
|------|------|  
|**Fonctionnalité**|Nom de la colonne|
|**Count**|Nombre total des lignes|
|**Unique Value Count**|Nombre de valeurs uniques dans la colonne|
|**Missing Value Count**|Nombre de valeurs uniques dans la colonne|
|**Min**|Valeur la plus basse de la colonne|  
|**Max**|Valeur la plus haute de la colonne|
|**Mean**|Moyenne de toutes les valeurs de colonne|
|**Mean Deviation**|Écart moyen des valeurs de colonne|
|**1st Quartile**|Valeur au premier quartile|
|**Median**|Valeur de colonne médiane|
|**3rd Quartile**|Valeur au troisième quartile|
|**Mode**|Mode des valeurs de colonne|
|**Plage**|Entier représentant le nombre de valeurs comprises entre les valeurs maximale et minimale|
|**Sample Variance**|Variance de la colonne. Voir la section Note|
|**Sample Standard Deviation**|Écart type de la colonne. Voir la section Note|
|**Sample Skewness**|Asymétrie de la colonne. Voir la section Note|
|**Sample Kurtosis**|Kurtosis de la colonne. Voir la section Note|
|**P0.5**|0,5e centile|
|**P1**|1er centile|
|**P5**|5e centile|
|**P95**|95e centile|
|**P99.5**|99,5e centile |

## <a name="technical-notes"></a>Notes techniques

- Pour les colonnes non numériques, seules les valeurs de Count, d'Unique value count et de Missing value count sont calculées. Pour les autres statistiques, une valeur Null est renvoyée.

- Les colonnes contenant des valeurs booléennes sont traitées à l'aide des règles suivantes :

    - Lors du calcul de Min, un opérateur logique AND est appliqué.
    
    - Lors du calcul de Max, un opérateur logique OR est appliqué.
    
    - Lors du calcul de Range, le composant vérifie d’abord si le nombre de valeurs uniques dans la colonne est égal à 2.
    
    - Lors du calcul des statistiques nécessitant des calculs à virgule flottante, les valeurs True sont traitées comme 1,0, et les valeurs False comme 0,0.

## <a name="next-steps"></a>Étapes suivantes

Consultez les [composants disponibles](component-reference.md) pour Azure Machine Learning.  
