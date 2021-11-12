---
title: 'Convertir au format CSV : référence du composant'
titleSuffix: Azure Machine Learning
description: Découvrez comment utiliser le composant Convertir au format CSV dans le concepteur Azure Machine Learning pour convertir un jeu de données en fichier CSV réutilisable.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: 9f3d86c823305f5b39766d677b288c339cbf3d7b
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131566769"
---
# <a name="convert-to-csv-component"></a>Composant Convertir au format CSV

Cet article décrit un composant dans le concepteur Azure Machine Learning.

Utilisez ce composant pour convertir un jeu de données dans un fichier au format CSV qui peut être téléchargé, exporté ou partagé avec des composants de script R ou Python.

### <a name="more-about-the-csv-format"></a>Plus d’informations sur le format CSV 

Le format CSV, qui signifie « valeurs séparées par des virgules », est un format de fichier utilisé par de nombreux outils de Machine Learning externes. CSV est un format d’échange courant lorsque vous utilisez des langages open source tels que R ou Python.

Même si vous effectuez la majeure partie de votre travail dans Azure Machine Learning, il peut parfois s’avérer pratique de convertir votre jeu de données au format CSV pour l’utiliser dans des outils externes. Par exemple :

+ Téléchargez le fichier CSV pour l’ouvrir avec Excel, ou importez-le dans une base de données relationnelle.  
+ Enregistrez le fichier CSV dans le stockage cloud et connectez-vous à celui-ci depuis Power BI pour créer des visualisations.  
+ Utilisez le format CSV pour préparer des données pour une utilisation dans R et Python. 

Lorsque vous convertissez un jeu de données au format CSV, le fichier csv est enregistré dans votre espace de travail Azure ML. Vous pouvez utiliser un utilitaire de stockage Azure pour ouvrir et utiliser le fichier directement. Vous pouvez également accéder au fichier CSV dans le concepteur en sélectionnant le composant **Convertir au format CSV**, puis l’icône d’histogramme sous l’onglet **Sorties** dans le panneau droit pour afficher la sortie. Vous pouvez télécharger le fichier CSV à partir du dossier de résultats dans un répertoire local.  

## <a name="how-to-configure-convert-to-csv"></a>Comment configurer Convertir au format CSV


1.  Ajoutez le composant Convertir au format CSV à votre pipeline. Ce composant se trouve dans le groupe **Transformation des données** dans le concepteur. 

2. Connectez-le à n’importe quel composant générant un jeu de données.   
  
3.  Envoyez le pipeline.

### <a name="results"></a>Résultats
  

Sélectionnez l’onglet **Sorties** dans le panneau droit de **Convertir au format CSV**, puis l’une de ces icônes sous **Port outputs** (Sorties de port).  

+ **Inscrire le jeu de données** : Sélectionnez l’icône et réenregistrez le fichier CSV dans l’espace de travail Azure ML comme un autre jeu de données. Vous pouvez trouver le jeu de données en tant que composant dans l’arborescence des composants sous la section **Mes jeux de données**.

 + **Voir la sortie** : Sélectionnez l’icône représentant un œil et suivez les instructions pour parcourir le dossier **Results_dataset**, puis téléchargez le fichier data.csv.

## <a name="next-steps"></a>Étapes suivantes

Consultez les [composants disponibles](component-reference.md) pour Azure Machine Learning. 