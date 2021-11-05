---
title: Guide de démarrage rapide sur les modèles de base de données et la base de données de lac Azure Synapse
description: Guide de démarrage rapide sur l’utilisation des modèles de base de données et de la base de données de lac Synapse
author: gsaurer
ms.author: gesaur
ms.service: synapse-analytics
ms.subservice: database-editor
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: template-concept, ignite-fall-2021
ms.openlocfilehash: 4db8799afdaf4a69278ce40c76baf05ab0eb1eee
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131467185"
---
# <a name="quickstart-create-a-new-lake-database-leveraging-database-templates"></a>Démarrage rapide : Créer une base de données de lac en tirant parti des modèles de base de données

Ce guide de démarrage rapide vous présente un scénario de bout en bout sur la façon dont vous pouvez appliquer les modèles de base de données pour créer une base de données de lac, aligner des données sur votre nouveau modèle et utiliser l’expérience intégrée pour analyser les données. 

## <a name="prerequisites"></a>Prérequis
- Au minimum, des autorisations de rôle utilisateur Synapse sont requises pour explorer un modèle de base de données de lac à partir de la Galerie.
- Des autorisations d’administrateur ou de contributeur Synapse sont requises sur l’espace de travail Synapse pour créer une base de données de lac.
- Des autorisations de contributeur de données de blob de stockage sont requises sur le lac de données.

## <a name="create-a-lake-database-from-database-templates"></a>Créer une base de données de lac à partir de modèles de base de données

Utilisez la nouvelle fonctionnalité de modèles de base de données (préversion) pour créer une base de données de lac que vous pouvez utiliser pour configurer votre modèle de données pour la base de données. 

Pour notre scénario, nous allons utiliser le modèle de base de données Retail (vente au détail) et sélectionner les entités suivantes : 
 - **RetailProduct** (produit vendu au détail) : un produit est tout ce qui peut être proposé à un marché qui peut répondre aux besoins des clients potentiels. Ce produit est la somme de tous les attributs physiques, psychologiques, symboliques et de service qui lui sont associés.
 - **Transaction** : niveau le plus bas de l’activité du client ou de travail exécutable.
Une transaction se compose d’un ou de plusieurs événements discrets.
 - **TransactionLineItem** (élément de ligne de transaction) : les composants d’une transaction sont répartis par produit et par quantité, à raison d’un par élément de ligne.
 - **Party** (partie) : une partie est une personne, une organisation, une entité juridique, une organisation sociale ou une unité commerciale présentant un intérêt pour l’entreprise.
 - **Customer** (client) : un client est une personne ou une entité légale qui a ou a acheté un produit ou service.
 - **Channel** (canal) : un canal est un moyen par lequel les produits ou services sont vendus et/ou distribués.
 - 
Le moyen le plus simple de les trouver consiste à utiliser la zone de recherche au-dessus des différents secteurs d’activité qui contiennent les tables. 
 
![Exemple de modèle de base de données](./media/quick-start-create-lake-database/model-example.png)

 
## <a name="configure-lake-database"></a>Configurer la base de données de lac
 
Une fois que vous avez créé la base de données, assurez-vous que le compte de stockage et le chemin du fichier sont définis sur un emplacement où vous souhaitez stocker les données. Le chemin est défini par défaut sur le compte de stockage principal au sein de Synapse Analytics, mais vous pouvez le changer en fonction de vos besoins. 
  
 ![Exemple de base de données de lac](./media/quick-start-create-lake-database/lake-database-example.png)
 
Pour enregistrer votre disposition et la rendre disponible dans Synapse, publiez toutes les modifications. Cette étape termine la configuration de la base de données de lac et la rend disponible pour tous les composants dans Synapse Analytics et à l’extérieur. 

## <a name="ingest-data-to-lake-database"></a>Ingestion de données dans la base de données de lac

Pour ingérer des données dans la base de données de lac, vous pouvez exécuter des [pipelines](../data-integration/data-integration-data-lake.md) avec des mappages de flux de données sans code, qui disposent d’un connecteur de **base de données d’espace de travail** pour charger les données directement dans la table de base de données. Vous pouvez également utiliser les notebooks Spark interactifs pour ingérer des données dans les tables de base de données de lac :

```Spark
%%sql
INSERT INTO `retail_mil`.`customer` VALUES (1,'2021-02-18',1022,557,101,'Tailspin Toys (Head Office)','Waldemar Fisar',90410,466);
```

## <a name="query-the-data"></a>Interroger les données

Une fois la base de données de lac créée, il existe différentes façons d’interroger les données. Nous prenons en charge SQL-Ondemand dans Synapse, qui comprend automatiquement le format de base de données de lac créé et expose les données via ce dernier. 

```sql
SELECT TOP (100) [ProductId]
,[ProductName]
,[ProductDescription]
,[ProductInternalName]
,[ItemSku]
,[PrimaryBrandId]
FROM [Retail_mil].[dbo].[RetailProduct]
```

L’autre façon d’accéder aux données dans Synapse consiste à ouvrir un nouveau notebook Spark et à y utiliser l’expérience intégrée :

```spark
df = spark.sql("SELECT * FROM `Retail_mil`.`RetailProduct`")
df.show(10)
```

## <a name="train-machine-learning-models"></a>Effectuer l’apprentissage de modèles Machine Learning

Vous pouvez utiliser la base de données de lac pour entraîner vos modèles Machine Learning et évaluer les données. Pour plus d’informations, consulter [Entraîner des modèles Machine Learning](../machine-learning/tutorial-automl.md). 

## <a name="next-steps"></a>Étapes suivantes

Continuez à explorer les fonctionnalités du concepteur de base de données en suivant les liens ci-dessous.
 - [Concept de modèles de base de données](concepts-database-templates.md)
 - [Concept de base de données de lac](concepts-lake-database.md)
 - [Comment créer une base de données de lac](create-empty-lake-database.md)
