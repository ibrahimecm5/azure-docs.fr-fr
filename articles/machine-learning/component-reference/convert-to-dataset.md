---
title: 'Convertir en jeu de données : référence du composant'
titleSuffix: Azure Machine Learning
description: Découvrez comment utiliser le composant Convertir en jeu de données dans le concepteur Azure Machine Learning pour convertir une entrée de données au format de jeu de données interne.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: e86c7c6f6a5eb3c4b3a388dab4680d2856a80b43
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131566768"
---
# <a name="convert-to-dataset"></a>Convertir en jeu de données

Cet article explique comment utiliser le composant Convertir en jeu de données du concepteur Azure Machine Learning pour convertir des données d’un pipeline au format interne du concepteur.
  
La conversion n’est pas obligatoire dans la plupart des cas. Azure Machine Learning convertit implicitement les données au format de jeu de données natif quand une opération est effectuée sur les données. 

Nous recommandons d’enregistrer les données au format de jeu de données si vous avez effectué une normalisation ou un nettoyage sur un jeu de données et que vous souhaitez vous assurer que les modifications sont utilisées dans d’autres pipelines.  
  
> [!NOTE]
> Le module Convertir en jeu de données change uniquement le format des données. Il n’enregistre pas une nouvelle copie des données dans l’espace de travail. Pour enregistrer le jeu de données, double-cliquez sur le port de sortie, sélectionnez **Enregistrer comme jeu de données**, puis entrez un nouveau nom.  
  
## <a name="how-to-use-convert-to-dataset"></a>Comment utiliser le module Convertir en jeu de données  

Nous vous recommandons d’utiliser le composant [Modifier les métadonnées](edit-metadata.md) pour préparer le jeu de données avant d’utiliser le composant Convertir en jeu de données. Vous pouvez ajouter ou changer des noms de colonnes, ajuster des types de données et effectuer d’autres modifications éventuellement requises.

1.  Ajoutez le composant Convertir en jeu de données à votre pipeline. Ce composant se trouve dans la catégorie **Transformation des données** dans le concepteur. 

2. Connectez-le à n’importe quel composant qui génère un jeu de données.   

    Tant que les données sont [tabulaires](/python/api/azureml-core/azureml.data.tabulardataset), vous pouvez les convertir en jeu de données. Cela inclut les données chargées via le module [Importer des données](import-data.md), les données créées via le module [Entrer des données manuellement](enter-data-manually.md) ou les jeux de données transformés via le module [Appliquer une transformation](apply-transformation.md).

3.  Dans la liste déroulante **Action**, indiquez si vous souhaitez effectuer un nettoyage des données avant d’enregistrer le jeu de données :  
  
    - **Aucun** :  utilisez les données telles quelles.  
  
    - **Définir une valeur manquante** : affectez une valeur spécifique à une valeur manquante dans le jeu de données. L’espace réservé par défaut est le caractère point d’interrogation (?), mais vous pouvez utiliser l’option **Valeur manquante personnalisée** pour entrer une autre valeur. Par exemple, si vous entrez **Taxi** pour **Valeur manquante personnalisée**, toutes les instances de **Taxi** dans le jeu de données sont remplacées par la valeur manquante.
  
    - **Remplacer des valeurs** : utilisez cette option pour spécifier une seule valeur exacte à remplacer par toute autre valeur exacte. Vous pouvez remplacer des valeurs manquantes ou des valeurs personnalisées en définissant la méthode **Remplacer** :

      - **Manquant** : choisissez cette option pour remplacer des valeurs manquantes dans le jeu de données d’entrée. Pour **Nouvelle valeur**, entrez la valeur qui remplacera les valeurs manquantes.
      - **Personnalisé** : choisissez cette option pour remplacer des valeurs personnalisées dans le jeu de données d’entrée. Pour **Valeur personnalisée**, entrez la valeur que vous souhaitez rechercher. Par exemple, si vos données contiennent la chaîne `obs` en guise d’espace réservé pour les valeurs manquantes, vous entrez `obs`. Pour **Nouvelle valeur**, entrez la nouvelle valeur qui remplacera la chaîne d’origine.
  
    Notez que l’opération **Remplacer des valeurs** s’applique uniquement aux correspondances exactes. Par exemple, ces chaînes ne sont pas affectées : `obs.`, `obsolete`.  
 
  
5.  Envoyez le pipeline.  

## <a name="results"></a>Résultats

+  Pour enregistrer le jeu de données résultant sous un nouveau nom, sélectionnez l’icône **Inscrire le jeu de données** sous l’onglet **Sorties** dans le volet droit du composant.  
  
## <a name="technical-notes"></a>Notes techniques  

-   Tout composant qui accepte un jeu de données comme entrée peut également prendre des données dans le fichier CSV ou TSV. Avant l’exécution de tout code de composant, les entrées sont prétraitées. Le prétraitement équivaut à exécuter le composant Convertir en jeu de données sur l’entrée.  
  
-   Vous ne pouvez pas effectuer la conversion du format SVMLight en jeu de données.  
  
-   Quand vous spécifiez une opération de remplacement personnalisé, l’opération de recherche et de remplacement s’applique à des valeurs complètes. Les correspondances partielles ne sont pas autorisées. Par exemple, vous pouvez remplacer un 3 par -1 ou 33, mais vous ne pouvez pas remplacer un 3 dans un nombre à deux chiffres, tel que 35.  
  
-   Pour les opérations de remplacement personnalisé, le remplacement échoue silencieusement si vous utilisez comme remplacement tout caractère qui n’est pas conforme au type de données actuel de la colonne.  

  
## <a name="next-steps"></a>Étapes suivantes

Consultez les [composants disponibles](component-reference.md) pour Azure Machine Learning.