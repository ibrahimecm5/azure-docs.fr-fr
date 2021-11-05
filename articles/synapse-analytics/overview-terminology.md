---
title: Terminologie – Azure Synapse Analytics
description: Guide de référence pour l’utilisateur d’Azure Synapse Analytics
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 11/02/2021
ms.author: saveenr
ms.reviewer: jrasnick
ms.custom: ignite-fall-2021
ms.openlocfilehash: 5d525d3657a48e01240721e4c868fa481db58dc0
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131074702"
---
# <a name="azure-synapse-analytics-terminology"></a>Terminologie Azure Synapse Analytics

Ce document présente les concepts de base d’Azure Synapse Analytics.

## <a name="basics"></a>Informations de base

Un **espace de travail Synapse** est une limite de collaboration sécurisable pour l’analytique d’entreprise basée sur le cloud dans Azure. Un espace de travail est déployé dans une région spécifique et est associé à un compte ADLS Gen2 et à un système de fichiers (pour le stockage des données temporaires). Un espace de travail se trouve sous un groupe de ressources.

Un espace de travail vous permet d’effectuer des analyses avec SQL et Apache Spark. Les ressources disponibles pour les analyses SQL et Spark sont organisées en **pools** SQL et Spark.

## <a name="linked-services"></a>Services liés

Un espace de travail peut contenir un nombre quelconque de **services liés**, essentiellement des chaînes de connexion qui définissent les informations de connexion nécessaires à l’espace de travail pour se connecter à des ressources externes.

## <a name="synapse-sql"></a>SQL Synapse

**Synapse SQL** permet d’effectuer des analyses basées sur T-SQL dans un espace de travail Synapse. Synapse SQL dispose de deux modèles de consommation : dédié et serverless.  Pour le modèle dédié, utilisez des **pools SQL** dédiés. Un espace de travail peut avoir un nombre quelconque de ces pools. Pour utiliser le modèle serverless, utilisez les **pools SQL serverless**. Chaque espace de travail possède un de ces pools.

À l’intérieur de Synapse Studio, vous pouvez travailler avec des pools SQL en exécutant des **scripts SQL**.

## <a name="apache-spark-for-synapse"></a>Apache Spark pour Synapse

Pour utiliser les analyses Spark, créez et utilisez des **pools Apache Spark serverless** dans votre espace de travail Synapse. Quand vous commencez à utiliser un pool Spark, les espaces de travail créent une **session Spark** pour gérer les ressources associées à cette session.

Il existe deux façons d’utiliser Spark dans Synapse :

* **Notebooks Spark** pour l’ingénierie et la science des données avec Scala, PySpark, C# et SparkSQL
* **Définitions de travaux Spark** pour l’exécution de travaux Spark par lots à l’aide de fichiers jar.

## <a name="pipelines"></a>Pipelines

Les pipelines représente la manière dont Azure Synapse assure l’intégration de données, ce qui vous permet de déplacer des données entre les services et d’orchestrer des activités.

* **Pipeline** – Regroupement logique d’activités qui effectuent une tâche ensemble.
* **Activités** – Définition des actions à effectuer sur des données au sein d’un pipeline, telles que la copie de données, l’exécution d’un notebook ou d’un script SQL.
* **Flux de données** – Type spécifique d’activité qui fournit une expérience sans code permettant d’effectuer une transformation de données qui utilise Synapse Spark en arrière-plan.
* **Déclencheur** : Exécute un pipeline. Il peut être exécuté manuellement ou automatiquement (planification, fenêtre bascule ou événement).
* **Jeu de données d’intégration** : Vue de données nommée qui pointe ou référence simplement les données à utiliser dans une activité en guise d’entrée et de sortie. Elle appartient à un service lié.

## <a name="data-explorer-preview"></a>Data Explorer (préversion)

L'explorateur de données Azure Synapse offre aux clients une expérience d'interrogation interactive permettant de dégager des informations à partir des données de journal et de télémétrie.

* Les **pools Data Explorer** sont des clusters dédiés qui incluent au moins deux nœuds de calcul avec un stockage SSD local (cache chaud) pour optimiser les performances des requêtes, et un stockage de plusieurs objets blob (cache froid) pour la persistance.
* Les **bases de données Data Explorer** sont hébergées dans des pools Data Explorer. Il s’agit d’entités logiques composées de collections de tables et d’autres objets de base de données. Vous pouvez avoir plusieurs bases de données par pool.
* Les **tables** sont des objets de base de données qui contiennent des données organisées à l’aide d’un modèle de données relationnelles classique. Les données sont stockées dans des enregistrements qui adhèrent au schéma de table bien défini de Data Explorer qui définit une liste triée de colonnes, chaque colonne ayant un nom et un type de données scalaires. Les types de données scalaires peuvent être structurés (*int*, *real*, *datetime* ou *timespan*), semi-structurés (*dynamic*) ou en texte libre (*string*). Le type dynamic est semblable au JSON car il peut contenir une valeur scalaire unique, un tableau ou un dictionnaire de ces valeurs.
* Les **tables externes** sont des tables qui référencent une source de données de stockage ou SQL située en dehors de la base de données Data Explorer. Tout comme les tables non externes, les tables externes ont un schéma bien défini (une liste triée de paires Nom de colonne/Type de données). Cependant, contrairement aux tables Data Explorer où les données sont ingérées dans des pools Data Explorer, les tables externes opèrent sur les données qui sont stockées et gérées en dehors des pools. Les tables externes ne conservent pas de données et sont utilisées pour interroger ou exporter des données vers un magasin de données externe.

## <a name="next-steps"></a>Étapes suivantes

* [Bien démarrer avec Azure Synapse Analytics](get-started.md)
* [Créer un espace de travail](quickstart-create-workspace.md)
* [Utiliser un pool SQL serverless](quickstart-sql-on-demand.md)
* [Créer un pool d’explorateur de données à l’aide de Synapse Studio](data-explorer/data-explorer-create-pool-studio.md)
