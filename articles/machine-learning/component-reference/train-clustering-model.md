---
title: 'Effectuer l’apprentissage du modèle de clustering : référence du composant'
titleSuffix: Azure Machine Learning
description: Découvrez comment utiliser le composant Effectuer l’apprentissage du modèle de clustering dans Azure Machine Learning pour effectuer l’apprentissage de modèles de clustering.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/17/2021
ms.openlocfilehash: 16d20e71dba5c83093ec572aad4910ec9a89ba06
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131566862"
---
# <a name="train-clustering-model"></a>Entraîner un modèle de clustering

Cet article décrit un composant dans le concepteur Azure Machine Learning.

Utilisez ce composant pour effectuer l’apprentissage d’un modèle de clustering.

Le composant prend un modèle de clustering non formé que vous avez déjà configuré à l’aide du composant [Clustering k-moyennes](k-means-clustering.md), et effectue l’apprentissage du modèle à l’aide d’un jeu de données étiqueté ou non. Le composant crée un modèle formé que vous pouvez utiliser pour la prédiction, ainsi qu’un ensemble d’affectations de cluster pour chaque cas dans les données d’apprentissage.

> [!NOTE]
> Il est impossible d’effectuer l’apprentissage d’un modèle de clustering à l’aide du composant [Effectuer l’apprentissage du modèle](train-model.md) qui est le composant générique pour l’apprentissage de modèles Machine Learning. Cela est dû au fait que le module [Train Model](train-model.md) (Entraîner le modèle) fonctionne uniquement avec les algorithmes d’apprentissage supervisé. L’algorithme de clustering k-moyennes et les autres algorithmes de clustering permettent un apprentissage non supervisé, ce qui signifie que l’algorithme peut apprendre à partir de données sans étiquette.  
  
## <a name="how-to-use-train-clustering-model"></a>Entraînement d’un modèle de clustering  

1.  Ajoutez le composant **Effectuer l’apprentissage du modèle de clustering** à votre pipeline dans le concepteur. Vous trouverez ce composant sous **Composants Machine Learning** dans la catégorie **Apprentissage**.  
  
2. Ajoutez le composant [Clustering k-moyennes](k-means-clustering.md) ou un autre composant personnalisé qui crée un modèle de clustering compatible, puis définissez les paramètres du modèle de clustering.  
    
3.  Joignez un jeu de données d’entraînement à l’entrée à droite du module **Train Clustering Model** (Entraîner un modèle de clustering).
  
5.  Dans **Jeu de colonnes**, sélectionnez les colonnes du jeu de données à utiliser lors de la création de clusters. Veillez à sélectionner les colonnes qui constituent de bonnes fonctionnalités : par exemple, évitez d’utiliser les ID ou d’autres colonnes qui ont des valeurs uniques ou des colonnes qui ont toutes les mêmes valeurs.

    Si une étiquette est disponible, vous pouvez l’utiliser comme une fonctionnalité ou l’ignorer.  
  
6. Sélectionnez l’option **Check for Append or Uncheck for Result Only** (Cocher pour ajouter ou décocher pour les résultats uniquement) si vous souhaitez exporter les données d’apprentissage avec la nouvelle étiquette de cluster.

    Si vous désélectionnez cette option, seuls les affectations de cluster sont générées. 

7. Envoyez le pipeline ou cliquez sur le composant **Effectuer l’apprentissage du modèle de clustering**, puis sélectionnez **Exécuter la sélection**.  
  
### <a name="results"></a>Résultats

Une fois l’apprentissage terminé :

+ Pour enregistrer un instantané du modèle entraîné, sélectionnez l’onglet **Sorties** dans le panneau droit du composant **Effectuer l’apprentissage du modèle**. Sélectionnez l’icône **Inscrire le jeu de données** pour enregistrer le modèle en tant que composant réutilisable.

+ Pour générer des scores à partir du modèle, utilisez l’option [Attribuer des données à des clusters](assign-data-to-clusters.md).

> [!NOTE]
> Si vous devez déployer le modèle formé dans le concepteur, assurez-vous que l’option [Attribuer des données à des clusters](assign-data-to-clusters.md) plutôt que **Modèle de score** est associée à l’entrée du [composant Sortie du service web](web-service-input-output.md) dans le pipeline d’inférence.

## <a name="next-steps"></a>Étapes suivantes

Consultez les [composants disponibles](component-reference.md) pour Azure Machine Learning. 