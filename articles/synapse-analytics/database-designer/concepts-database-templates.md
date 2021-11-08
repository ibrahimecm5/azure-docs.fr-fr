---
title: Concepts des modèles de base de données Azure Synapse
description: En savoir plus sur les modèles de base de données dans Azure Synapse
author: gsaurer
ms.author: gesaur
ms.service: synapse-analytics
ms.subservice: database-editor
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: template-concept, ignite-fall-2021
ms.openlocfilehash: 628c815905f5c8afb29df028d7ba74020b941043
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131441302"
---
# <a name="lake-database-templates"></a>Modèles de base de données de lac

Azure Synapse Analytics fournit des modèles de base de données standardisés pour différents secteurs d’activité et qui peuvent être utilisés et facilement créer un modèle de base de données par organisation. Ces modèles contiennent des métadonnées riches pour arriver à comprendre un modèle de données. Utilisez ces modèles pour créer votre base de données de lac et utilisez le runtime d’analyse Azure Synapse pour fournir des informations aux utilisateurs.

Découvrez les concepts liés aux modèles de base de données de lac dans Azure Synapse. Utilisez ces modèles pour créer une base de données avec des métadonnées enrichies afin de mieux comprendre les données et améliorer votre productivité.  

## <a name="business-area-templates"></a>Modèles par secteur d’activité  

Les modèles par secteur d’activité offrent la vue la plus complète et granulaire des données pour une entreprise ou un sujet en particulier. Les modèles par secteur d’activité sont également désignés sous le terme de zone de sujet ou de modèles de domaine. Les modèles par secteur d’activité contiennent des tables et des colonnes adaptées à une entreprise au sein d’un secteur défini. Les gestionnaires de données, l’équipe de gouvernance des données et les équipes commerciales au sein de l’organisation peuvent utiliser les modèles par secteur d’activité pour créer un schéma de données d’entreprise qui facilite la communication détaillée des besoins et de l’étendue. Chaque modèle par secteur d’activité est construit à partir d’un ensemble commun d’entités à partir du modèle de base de données d’entreprise correspondant pour s’assurer que les modèles d’entreprise disposeront de clés, d’attributs et de définitions communs et cohérents avec les autres modèles du secteur. Par exemple, la comptabilité et la création de rapports financiers, le marketing, le budget et les prévisions financières sont des modèles destinés à de nombreux secteurs d’activité, comme la vente au détail ou les banques. 

![Exemple de modèle par secteur d’activité](./media/concepts-database-templates/business-area-template-example.png)

## <a name="enterprise-templates"></a>Modèles pour l’entreprise 

Les modèles de base de données pour entreprises contiennent un sous-ensemble de tables susceptibles d’intéresser une organisation active dans un secteur donné. Il offre une vue d’ensemble et décrit la connectivité entre les modèles associés. Ces modèles servent d’accélérateur pour de nombreux types de projets de très grande taille. Par exemple, le modèle bancaire possède un modèle d’entreprise appelé Banque. 

![Exemple de modèle d’entreprise](./media/concepts-database-templates/enterprise-template-example.png)

## <a name="table"></a>Table

Une table est un objet avec une existence indépendante et qui peut être distingué d’autres objets. Par exemple, Customer, Store, Channel, etc.

## <a name="column"></a>Colonne

Chaque table est décrite par un ensemble de colonnes. Chaque colonne comporte un nom, une description, un type de données et est associée à une table. Il y a environ 30 000 colonnes dans les modèles de base de données. Par exemple, CustomerId est une colonne de la table Customer.

## <a name="primary-key"></a>Clé primaire

La clé primaire permet d’identifier de manière unique l’ensemble de la table. Elle doit identifier de manière unique les tuples dans une table. Par exemple, une clé de la colonne CustomerId applique l’unicité, afin d’identifier chaque client dans la table Customer.

## <a name="foreign-key"></a>Clé étrangère

Une clé étrangère est une colonne ou une combinaison de colonnes dont les valeurs correspondent à une clé primaire dans une autre table. Elle permet d’établir un lien entre deux tables. Par exemple, CustomerId dans la table Transaction représente le client qui a effectué une transaction. Une clé étrangère a toujours une relation avec une clé primaire, par exemple la clé primaire CustomerId dans la table Customer.

## <a name="composite-key"></a>Clé composite

Une clé composite est composée d’au moins deux colonnes qui sont requises pour identifier de manière unique une table. Par exemple, dans une table Order, OrderNumber et ProductId peuvent être requis pour identifier un enregistrement de manière unique.

## <a name="relationships"></a>Relations

Les relations sont des associations ou des interactions entre deux tables. Par exemple, les tables Customer et CustomerEmail sont liées l’un à l’autre. La relation implique deux tables. Il y a une table parent et une table enfant, souvent connectées par une clé étrangère. On peut dire que la relation est de table à table.

## <a name="table-partitions"></a>Partitions de table

La base de données de lac permet de partitionner les données sous-jacentes d’une table pour obtenir de meilleures performances. Vous pouvez définir la configuration des partitions dans les paramètres de stockage d’une table dans l’éditeur de base de données.

## <a name="next-steps"></a>Étapes suivantes

Continuez à explorer les fonctionnalités du concepteur de base de données en suivant les liens ci-dessous.
- [Démarrage rapide](quick-start-create-lake-database.md)
- [Concept de base de données de lac](concepts-lake-database.md)
