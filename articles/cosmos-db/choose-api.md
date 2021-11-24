---
title: Choisir une API dans Azure Cosmos DB
description: Découvrez comment choisir entre les API SQL/Core, MongoDB, Cassandra, Gremlin et Table dans Azure Cosmos DB en fonction des exigences de votre charge de travail.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/12/2021
ms.openlocfilehash: e120d4fa4d85ba2d52821460da1aa62eafd0455d
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132158019"
---
# <a name="choose-an-api-in-azure-cosmos-db"></a>Choisir une API dans Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB est une base de données NoSQL complètement managée pour le développement d’applications modernes. Azure Cosmos DB prend en charge l’administration de bases de données avec une gestion, des mises à jour et des mises à jour correctives automatiques. Il traite également la gestion de la capacité avec des options économiques de mise à l’échelle automatique et serverless qui répondent aux besoins de l’application pour faire correspondre la capacité à la demande.

## <a name="apis-in-azure-cosmos-db"></a>Les API dans Azure Cosmos DB

Azure Cosmos DB offre plusieurs API de base de données, notamment l’API Core (SQL), l’API pour MongoDB, l’API Cassandra, l’API Gremlin et l’API Table. À l’aide de ces API, vous pouvez modéliser des données concrètes à l’aide de modèles de données de documents, de clés-valeurs, de graphes et de familles de colonnes. Ces API permettent à vos applications de traiter Azure Cosmos DB comme s’il s’agissait de diverses autres technologies de bases de données, sans la surcharge inhérente à la gestion, et approches de mise à l’échelle. À l’aide de ces API, Azure Cosmos DB vous aide à utiliser les écosystèmes, les outils et les compétences dont vous disposez déjà pour la modélisation et l’interrogation des données.

Toutes les API offrent une mise à l’échelle automatique du stockage et du débit, de la flexibilité et des garanties de performances. Il n’y a pas d’API idéale et vous pouvez choisir l’API qui vous convient pour générer votre application. Cet article vous aidera à choisir une API en fonction des besoins de votre charge de travail et de votre équipe.

## <a name="considerations-when-choosing-an-api"></a>Aspects à prendre en compte lors du choix d’une API

L’API Core (SQL) est native dans Azure Cosmos DB.

L’API pour MongoDB, Cassandra, Gremlin et Table implémente le protocole filaire des moteurs de base de données open source. Ces API conviennent mieux si les conditions suivantes sont remplies :

* Si vous avez des applications MongoDB, Cassandra ou Gremlin existantes.
* Si vous ne souhaitez pas réécrire la totalité de votre couche d’accès aux données.
* Si vous souhaitez utiliser l’écosystème de développement open source, les pilotes clients, l’expertise et les ressources pour votre base de données.
* Si vous souhaitez utiliser les fonctionnalités clés d’Azure Cosmos DB, telles que la distribution à l’échelle mondiale, la mise à l’échelle élastique du stockage et du débit, les performances, la faible latence, la possibilité d’exécuter une charge de travail transactionnelle et analytique, et utiliser une plateforme complètement managée.
* Si vous développez des applications modernes dans un environnement contenant plusieurs clouds.

Vous pouvez créer de nouvelles applications avec ces API ou migrer vos données existantes. Pour exécuter les applications migrées, modifiez la chaîne de connexion de votre application et continuez comme avant. Lorsque vous migrez des applications existantes, veillez à évaluer la prise en charge des fonctionnalités par ces API.

En fonction de votre charge de travail, vous devez choisir l’API qui répond à vos exigences. L’illustration suivante montre un organigramme sur la manière de choisir l’API appropriée lors de la création d’applications ou de la migration d’applications existantes vers Azure Cosmos DB :

:::image type="content" source="./media/choose-api/choose-api-decision-tree.png" alt-text="Arbre de décision pour le choix d’une API dans Azure Cosmos DB." lightbox="./media/choose-api/choose-api-decision-tree.png":::

## <a name="coresql-api"></a>API Core (SQL)

Cette API stocke les données au format de document. Elle offre la meilleure expérience de bout en bout, car nous disposons d’un contrôle total sur l’interface, le service et les bibliothèques clientes du SDK. Toutes les nouvelles fonctionnalités déployées dans Azure Cosmos DB sont d’abord disponibles sur les comptes de l’API SQL. Les comptes de l’API SQL Azure Cosmos DB prennent en charge l’interrogation des éléments à l’aide de la syntaxe SQL (Structured Query Language), l’un des langages de requête les plus familiers et les plus populaires pour interroger des objets JSON. Pour en savoir plus, consultez le module Learn [API SQL Azure Cosmos DB](/learn/modules/intro-to-azure-cosmos-db-core-api/) et l’article [Bien démarrer avec les requêtes SQL](sql-query-getting-started.md).

Si vous effectuez une migration à partir d’autres bases de données telles qu’Oracle, DynamoDB, HBase, etc. et si vous souhaitez utiliser les technologies modernes pour générer vos applications, l’API SQL est l’option recommandée. L’API SQL prend en charge les analytiques et offre une isolation des performances entre les charges de travail opérationnelles et analytiques.

### <a name="capacity-planning-for-migration-to-api-for-sqlcoreapi"></a>Planification de la capacité pour la migration vers l’API pour l’API SQL (Core)

Vous essayez de faire de la planification de capacité pour une migration vers Azure Cosmos DB SQL API à partir d'un cluster de base de données existant ? Vous pouvez utiliser les informations sur votre cluster de bases de données existantes pour la planification de la capacité.
* Si vous ne connaissez que le nombre de vCores et de serveurs présents dans votre cluster de bases de données existant, lisez [Estimation des unités de requête à l’aide de vCores ou de processeurs virtuels](convert-vcore-to-request-unit.md).
* Si vous connaissez les taux de requêtes typiques de votre charge de travail de base de données actuelle, lisez [Estimation des unités de requête à l’aide du planificateur de capacité Azure Cosmos DB](estimate-ru-with-capacity-planner.md)

## <a name="api-for-mongodb"></a>API pour MongoDB

Cette API stocke les données dans une structure de document, via le format BSON. Elle est compatible avec le protocole filaire MongoDB. Toutefois, elle n’utilise pas de code lié à MongoDB en mode natif. Cette API est un bon choix si vous souhaitez utiliser l’écosystème et les compétences MongoDB plus larges, sans compromis concernant l’utilisation des fonctionnalités Azure Cosmos DB telles que la mise à l’échelle, la haute disponibilité, la géoréplication, les emplacements d’écritures multiples, la gestion automatique et transparente des partitions, la réplication transparente entre les magasins opérationnels et analytiques et bien plus encore.

Vous pouvez utiliser vos applications MongoDB existantes avec l’API pour MongoDB en modifiant simplement la chaîne de connexion. Vous pouvez déplacer des données existantes à l’aide d’outils MongoDB natifs tels que mongodump et mongorestore ou à l’aide de notre outil de migration de base de données Azure. Les outils tels que l’interpréteur de commandes MongoDB, [MongoDB Compass](mongodb/connect-using-compass.md) et [Robo3T](mongodb/connect-using-robomongo.md) peuvent exécuter des requêtes et utiliser des données comme c’est le cas avec MongoDB en mode natif.

L’API pour MongoDB est compatible avec les versions serveur 4.0, 3.6 et 3.2 de MongoDB. La version de serveur 4.0 est recommandée, car elle offre les meilleures performances et une prise en charge complète des fonctionnalités. Pour plus d’informations, consultez l’article sur l’[API pour MongoDB](mongodb/mongodb-introduction.md).

### <a name="capacity-planning-for-migration-to-api-for-mongodb"></a>Planification de la capacité pour la migration vers l’API pour MongoDB

Vous essayez de faire de la planification de capacité pour une migration vers Azure Cosmos DB SQL API à partir d'un cluster de base de données existant ? Vous pouvez utiliser les informations sur votre cluster de bases de données existantes pour la planification de la capacité.
* Si vous ne connaissez que le nombre de vCores et de serveurs présents dans votre cluster de bases de données existant, lisez [Estimation des unités de requête à l’aide de vCores ou de processeurs virtuels](convert-vcore-to-request-unit.md).
* Si vous connaissez les taux de requêtes typiques de votre charge de travail de base de données actuelle, lisez [Estimation des unités de requête à l’aide du planificateur de capacité Azure Cosmos DB](./mongodb/estimate-ru-capacity-planner.md).

## <a name="cassandra-api"></a>API Cassandra

Cette API stocke les données dans un schéma orienté colonne. Apache Cassandra offre une approche de mise à l’échelle horizontale et hautement distribuée permettant de stocker de gros volumes de données tout en offrant une approche flexible à un schéma orienté colonne. L’API Cassandra dans Azure Cosmos DB s’aligne sur cette philosophie pour se rapprocher des bases de données NoSQL distribuées. L’API Cassandra est compatible au niveau du protocole filaire avec Apache Cassandra. Vous devez envisager d’utiliser l’API Cassandra si vous souhaitez tirer parti de l’élasticité et de la nature complètement managée d’Azure Cosmos DB tout en utilisant la plupart des fonctionnalités, outils et écosystèmes Apache Cassandra natifs. Cela signifie que sur les API Cassandra vous n’avez pas besoin de gérer le système d’exploitation, les machines virtuelles Java, le récupérateur de mémoire, les performances de lecture/écriture, les nœuds, les clusters, etc.

Vous pouvez utiliser les pilotes client Apache Cassandra pour vous connecter à l’API Cassandra. L’API Cassandra vous permet d’interagir avec les données à l’aide du langage CQL (Cassandra Query Language), des outils comme l’interpréteur de commandes CQL et les pilotes clients Cassandra que vous connaissez déjà. L’API Cassandra prend actuellement en charge les scénarios OLTP uniquement. À l’aide de l’API Cassandra, vous pouvez également utiliser les fonctionnalités uniques d’Azure Cosmos DB telles que le flux de modification. Pour plus d’informations, consultez l’article sur l’[API Cassandra](cassandra-introduction.md).

## <a name="gremlin-api"></a>API Gremlin

Cette API permet aux utilisateurs d’effectuer des requêtes de graphe et de stocker les données en tant qu’arêtes et sommets. Utilisez cette API pour les scénarios impliquant des données dynamiques, des données avec des relations complexes, des données trop complexes pour être modélisées à l’aide de bases de données relationnelles, et si vous souhaitez utiliser les compétences et l’écosystème Gremlin existants. L’API Gremlin d’Azure Cosmos DB combine la puissance des algorithmes de base de données de graphe avec une infrastructure managée et hautement scalable. Elle fournit une solution unique et flexible aux problèmes de données les plus courants associés à l’absence de flexibilité et aux approches relationnelles. L’API Gremlin prend actuellement en charge les scénarios OLTP uniquement.

L’API Gremlin d’Azure Cosmos DB repose sur [Apache TinkerPop](https://tinkerpop.apache.org/), un framework de calcul de graphe. L’API Gremlin utilise le même langage de requête Graph pour ingérer et interroger les données. Elle utilise la stratégie de partition d’Azure Cosmos DB pour effectuer les opérations de lecture et d’écriture à partir du moteur de base de données Graph. L’API Gremlin a une prise en charge du protocole filaire avec la version open source de Gremlin. Vous pouvez donc utiliser les SDK Gremlin open source pour générer votre application. L’API Gremlin Azure Cosmos DB fonctionne également avec Apache Spark et [GraphFrames](https://github.com/graphframes/graphframes) pour les scénarios de complexes utilisant des graphes analytiques. Pour plus d’informations, consultez l’article sur l’[API Gremlin](graph-introduction.md).

## <a name="table-api"></a>API de table

Cette API stocke les données au format clé/valeur. Si vous utilisez actuellement le Stockage Table Azure, vous pouvez constater des limitations en matière de latence, de mise à l’échelle, de débit, de distribution à l’échelle mondiale, de gestion des index, et les faibles performances en matière de requêtes. L’API Table surmonte ces limitations et il est recommandé de migrer votre application si vous souhaitez tirer parti des avantages d’Azure Cosmos DB. L’API Table prend en charge uniquement les scénarios OLTP.

Les applications écrites pour le Stockage Table Azure peuvent migrer vers l’API Table avec peu de modification de code, et tirer parti des fonctionnalités Premium. Pour plus d’informations, consultez l’article sur l’[API Table](table/introduction.md).

## <a name="next-steps"></a>Étapes suivantes

* [Prise en main de l’API SQL Azure Cosmos DB](create-sql-api-dotnet.md)
* [Bien démarrer avec l’API Azure Cosmos DB pour MongoDB](mongodb/create-mongodb-nodejs.md)
* [Prise en main de l’API Cassandra Azure Cosmos DB](cassandra/manage-data-dotnet.md)
* [Prise en main de l’API Gremlin Azure Cosmos DB](create-graph-dotnet.md)
* [Prise en main de l’API Table Azure Cosmos DB](create-table-dotnet.md)
* Vous tentez d’effectuer une planification de la capacité pour une migration vers Azure Cosmos DB ? Vous pouvez utiliser les informations sur votre cluster de bases de données existant pour la planification de la capacité.
    * Si vous ne connaissez que le nombre de vCores et de serveurs présents dans votre cluster de bases de données existant, lisez [Estimation des unités de requête à l’aide de vCores ou de processeurs virtuels](convert-vcore-to-request-unit.md) 
    * Si vous connaissez les taux de requêtes typiques de votre charge de travail de base de données actuelle, lisez la section concernant l’[estimation des unités de requête à l’aide du planificateur de capacité Azure Cosmos DB](estimate-ru-with-capacity-planner.md)
