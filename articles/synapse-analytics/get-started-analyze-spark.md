---
title: 'Démarrage rapide : Bien démarrer avec l’analyse Spark'
description: Dans ce tutoriel, vous allez voir comment analyser des données avec Apache Spark.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: spark
ms.topic: tutorial
ms.date: 03/24/2021
ms.openlocfilehash: e0d430c393637ff11e7315716b3fdd9818a61b8b
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131467147"
---
# <a name="analyze-with-apache-spark"></a>Analyser avec Apache Spark

Dans ce tutoriel, vous allez découvrir les étapes de base permettant de charger et d’analyser des données avec Apache Spark pour Azure Synapse.

## <a name="create-a-serverless-apache-spark-pool"></a>Créer un pool Apache Spark serverless

1. Dans Synapse Studio, dans le volet de gauche, sélectionnez **Gérer** > **Pools Apache Spark**.
1. Sélectionnez **Nouveau** 
1. Pour **Nom du pool Apache Spark**, entrez **Spark1**.
1. Pour **Taille du nœud**, entrez **Petite**.
1. Pour **Nombre de nœuds**, définissez la valeur minimale 3 et la valeur maximale 3
1. Sélectionnez **Vérifier + créer** > **Créer**. Votre pool Apache Spark sera prêt en quelques secondes.

## <a name="understanding-serverless-apache-spark-pools"></a>Comprendre les pools Apache Spark serverless

Un pool Spark serverless est un moyen d’indiquer comment un utilisateur souhaite travailler avec Spark. Lorsque vous commencez à utiliser un pool, une session Spark est créée si nécessaire. Le pool contrôle le nombre de ressources Spark utilisées par cette session ainsi que la durée de la session avant qu’elle ne s’arrête automatiquement. Vous payez pour les ressources Spark utilisées pendant cette session et non pour le pool lui-même. De cette façon, un pool Spark vous permet d’utiliser Spark sans vous soucier de la gestion des clusters. Cela fonctionne de la même manière qu’un pool SQL serverless.

## <a name="analyze-nyc-taxi-data-with-a-spark-pool"></a>Analyser les données de NYC Taxi avec un pool Spark

1. Dans Synapse Studio, accédez au hub **Développer**
2. Créer une nouvelle instance Notebook
3. Créez une nouvelle cellule de code et collez-y le code suivant.
    ```py
    %%pyspark
    df = spark.read.load('abfss://users@contosolake.dfs.core.windows.net/NYCTripSmall.parquet', format='parquet')
    display(df.limit(10))
    ```
1. Dans le notebook, dans le menu **Attacher à**, choisissez le pool Spark serverless **Spark1** que nous avons créé précédemment.
1. Sélectionnez **Exécuter** dans la cellule. Synapse démarre une nouvelle session Spark pour exécuter cette cellule si nécessaire. Si une nouvelle session Spark est nécessaire, sa création prend environ deux secondes. 
1. Si vous souhaitez simplement voir le schéma du dataframe, exécutez une cellule avec le code suivant :

    ```py
    %%pyspark
    df.printSchema()
    ```

## <a name="load-the-nyc-taxi-data-into-the-spark-nyctaxi-database"></a>Charger les données NYC Taxi dans la base de données Spark appelée nyctaxi

Les données sont disponibles via le dataframe nommé **df**. Chargez-les dans une base de données Spark nommée **nyctaxi**.

1. Ajoutez une nouvelle cellule de code au notebook, puis entrez le code suivant :

    ```py
    %%pyspark
    spark.sql("CREATE DATABASE IF NOT EXISTS nyctaxi")
    df.write.mode("overwrite").saveAsTable("nyctaxi.trip")
    ```
## <a name="analyze-the-nyc-taxi-data-using-spark-and-notebooks"></a>Analyser les données des taxis de New York à l’aide de Spark et des notebooks

1. Créez une nouvelle cellule de code, puis entrez le code suivant. 

   ```py
   %%pyspark
   df = spark.sql("SELECT * FROM nyctaxi.trip") 
   display(df)
   ```

1. Exécutez la cellule pour afficher les données NYC Taxi que nous avons chargées dans la base de données Spark **nyctaxi**.
1. Créez une nouvelle cellule de code, puis entrez le code suivant. Nous allons analyser ces données et enregistrer les résultats dans une table appelée **nyctaxi.passengercountstats**.

   ```py
   %%pyspark
   df = spark.sql("""
      SELECT PassengerCount,
          SUM(TripDistanceMiles) as SumTripDistance,
          AVG(TripDistanceMiles) as AvgTripDistance
      FROM nyctaxi.trip
      WHERE TripDistanceMiles > 0 AND PassengerCount > 0
      GROUP BY PassengerCount
      ORDER BY PassengerCount
   """) 
   display(df)
   df.write.saveAsTable("nyctaxi.passengercountstats")
   ```

1. Dans les résultats de la cellule, sélectionnez **Graphique** pour afficher les données visualisées.


## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Analyser des données avec un pool SQL dédié](get-started-analyze-sql-pool.md)
