---
title: 'Entrer les données manuellement : référence du composant'
titleSuffix: Azure Machine Learning
description: Découvrez comment utiliser le composant Entrer des données manuellement dans Azure Machine Learning pour créer un petit jeu de données en saisissant des valeurs. Le jeu de données peut avoir plusieurs colonnes.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 836b43a26f89856f2a3c122c5f5ab1f81d0680db
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131567193"
---
# <a name="enter-data-manually-component"></a>Composant Entrer les données manuellement

Cet article décrit un composant dans le concepteur Azure Machine Learning.

Utilisez le composant **Entrer des données manuellement** pour créer un petit jeu de données en tapant des valeurs. Le jeu de données peut avoir plusieurs colonnes.
  
Ce composant peut être utile dans des scénarios tels que les suivants :  
  
- Génération d’un petit ensemble de valeurs à des fins de test  
- Création d’une courte liste d’étiquettes  
- Saisie d’une liste de noms de colonnes à insérer dans un jeu de données

## <a name="create-a-dataset"></a>Créer un jeu de données 
  
1. Ajoutez le composant [Entrer des données manuellement](./enter-data-manually.md) à votre pipeline. Vous trouverez ce composant dans la catégorie **Data Input and Output** (Entrée et sortie de données) d’Azure Machine Learning. 
  
1. Pour **DataFormat**, sélectionnez l’une des options suivantes. Ces options déterminent comment les données que vous fournissez doivent être analysées. Les exigences relatives à chaque format sont considérablement différentes. Veillez par conséquent à lire les rubriques connexes.  
  
   - **ARFF** : format de fichier de relation d’attribut, utilisé par Weka.   
   - **CSV** : format de valeurs séparées par des virgules. Pour plus d’informations, consultez [Convertir au format CSV](./convert-to-csv.md).    
   - **SVMLight** : format utilisé par Vowpal Wabbit et d’autres infrastructures de Machine Learning.    
   - **TSV** : format de valeurs séparées par des tabulations.

   Si vous choisissez un format et ne fournissez pas de données conformes aux spécifications de format, une erreur d’exécution se produit.
  
1. Cliquez à l’intérieur de la zone de texte **Données** pour commencer à entrer des données. Les formats suivants requièrent une attention particulière :  
  
   - **CSV** : pour créer plusieurs colonnes, collez du texte séparé par des virgules, ou entrez plusieurs colonnes en utilisant des virgules entre les champs.
  
     Si vous sélectionnez l’option **HasHeader**, vous pouvez utiliser la première ligne de valeurs comme en-tête de colonne.  
  
     Si vous désélectionnez cette option, les noms de colonnes (Col1, Col2 et ainsi de suite) sont utilisés. Vous pouvez ajouter ou changer des noms de colonnes ultérieurement à l’aide de [Modifier les métadonnées](./edit-metadata.md).  
  
   - **TSV** : pour créer plusieurs colonnes, collez du texte séparé par des tabulations, ou entrez plusieurs colonnes en utilisant des tabulations entre les champs.  
  
     Si vous sélectionnez l’option **HasHeader**, vous pouvez utiliser la première ligne de valeurs comme en-tête de colonne.  
  
     Si vous désélectionnez cette option, les noms de colonnes (Col1, Col2 et ainsi de suite) sont utilisés. Vous pouvez ajouter ou changer des noms de colonnes ultérieurement à l’aide de [Modifier les métadonnées](./edit-metadata.md).  
  
   - **ARFF** : collez dans un fichier de format ARFF existant. Si vous entrez des valeurs directement, veillez à ajouter l’en-tête facultatif et les champs d’attribut obligatoires au début des données. 

     Par exemple, les lignes d’en-tête et d’attribut suivantes peuvent être ajoutées à une liste simple. L’en-tête de colonne serait `SampleText`. Notez que le type chaîne n’est pas pris en charge.
    
     ```text
     % Title: SampleText.ARFF  
     % Source: Enter Data component  
     @ATTRIBUTE SampleText NUMERIC  
     @DATA  
     \<type first data row here>  
     ```

   - **SVMLight** : entrez ou collez des valeurs en utilisant le format SVMLight.  
  
     Par exemple, l’exemple suivant représente les deux premières lignes du jeu de données Blood Donation, au format SVMLight :  
  
     ```text  
     # features are [Recency], [Frequency], [Monetary], [Time]  
     1 1:2 2:50 3:12500 4:98   
     1 1:0 2:13 3:3250 4:28   
     ```  
  
     Lorsque vous exécutez le composant [Entrer des données manuellement](./enter-data-manually.md), ces lignes sont converties en un jeu de données de colonnes et de valeurs d’index comme suit :  
  
     |Col1|Col2|Col3|Col4|Étiquettes|  
     |-|-|-|-|-|  
     |0,00016|0,004|0,999961|0,00784|1|  
     |0|0,004|0,999955|0,008615|1|  
  
1. Sélectionnez la touche Entrée après chaque ligne, pour aller à la ligne.      
     
   Si vous sélectionnez Entrée plusieurs fois pour ajouter plusieurs lignes de fin vides, les lignes vides sont supprimées ou tronquées.  
  
   Si vous créez des lignes avec des valeurs manquantes, vous pouvez toujours les filtrer ultérieurement.  
  
1. Connectez le port de sortie à d’autres composants, puis exécutez le pipeline.  
  
   Pour afficher le jeu de données, cliquez avec le bouton droit sur le composant, puis sélectionnez **Visualiser**.

## <a name="next-steps"></a>Étapes suivantes

Consultez les [composants disponibles](component-reference.md) pour Azure Machine Learning. 