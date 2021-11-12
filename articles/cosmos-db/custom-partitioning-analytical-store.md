---
title: Partitionnement personnalisé dans Azure Synapse Link pour Azure Cosmos DB (version préliminaire)
description: Le partitionnement personnalisé vous permet de partitionner les données du magasin analytique sur les champs couramment utilisés comme filtres dans les requêtes analytiques, ce qui améliore les performances des requêtes.
author: Rodrigossz
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: rosouz
ms.custom: ignite-fall-2021
ms.openlocfilehash: 7534149aacc76bebdcf591471d08b120b885e625
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131426123"
---
# <a name="custom-partitioning-in-azure-synapse-link-for-azure-cosmos-db-preview"></a>Partitionnement personnalisé dans Azure Synapse Link pour Azure Cosmos DB (version préliminaire)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Le partitionnement personnalisé vous permet de partitionner des données de magasin analytique sur des champs couramment utilisés comme filtres dans des requêtes analytiques, ce qui améliore les performances des requêtes.

Dans cet article, vous allez apprendre à partitionner vos données dans le magasin analytique Azure Cosmos DB à l’aide de clés qui sont essentielles pour vos charges de travail analytiques. Elle explique également comment tirer parti des performances de requête améliorées avec le nettoyage de partition. Vous allez également découvrir comment le magasin partitionné permet d’améliorer les performances des requêtes lorsque vos charges de travail comportent un nombre important de mises à jour ou de suppressions.

> [!IMPORTANT]
> La fonctionnalité de partitionnement personnalisé est actuellement en version préliminaire publique. Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

> [!NOTE]
> Les comptes Azure Cosmos DB doivent avoir [Azure Synapse Link](synapse-link.md) activé pour tirer parti du partitionnement personnalisé. Le partitionnement personnalisé est actuellement pris en charge pour Azure Synapse Spark 2.0 uniquement.

## <a name="how-does-it-work"></a>Comment fonctionne-t-il ?

Le partitionnement du magasin analytique est indépendant du partitionnement dans le magasin transactionnel. Par défaut, le magasin analytique n’est pas partitionné. Si vous souhaitez interroger fréquemment le magasin analytique sur la base de champs tels que Date, Heure, Catégorie, etc., vous pouvez tirer parti du partitionnement personnalisé pour créer un magasin partitionné distinct basé sur ces clés. Vous pouvez choisir un champ unique ou une combinaison de champs de votre jeu de données en tant que clé de partition du magasin analytique.

Vous pouvez déclencher le partitionnement à partir d’un bloc-notes Spark Azure Synapse en utilisant Azure Synapse Link. Vous pouvez planifier son exécution en tant que travail en arrière-plan une ou deux fois par jour, mais pouvez l’exécuter plus souvent si nécessaire. 

> [!NOTE]
> Le magasin partitionné pointe vers le compte de stockage principal ADLS Gen2 qui est lié à l’espace de travail Azure Synapse.

:::image type="content" source="./media/custom-partitioning-analytical-store/partitioned-store-architecture.png" alt-text="Architecture du magasin partitionné dans Azure Synapse Link pour Azure Cosmos DB" lightbox="./media/custom-partitioning-analytical-store/partitioned-store-architecture.png" border="false":::

Le magasin partitionné contient des données analytiques Azure Cosmos DB jusqu’au dernier timestamp d’exécution de votre travail de partitionnement. Lorsque vous interrogez vos données analytiques à l’aide des filtres de clé de partition dans Synapse Spark, Synapse Link fusionne automatiquement les données du magasin partitionné avec les données les plus récentes du magasin analytique. Vous obtenez ainsi les résultats les plus récents pour vos requêtes. Bien qu’il fusionne les données avant d’effectuer des requêtes, le delta n’est pas réécrit dans le magasin partitionné. Au fur et à mesure que le delta entre les données du magasin analytique et du magasin partitionné s’étend, les temps de requête sur les données partitionnées peuvent varier. Le déclenchement plus fréquent du travail de partitionnement réduira ce delta. Chaque fois que vous exécutez le travail de partitionnement, seules les modifications incrémentielles dans le magasin analytique sont traitées, et non le jeu de données complet.

## <a name="when-to-use"></a>Quand l’utiliser ?

L’utilisation d’un magasin partitionné est facultative lors de l’interrogation de données analytiques dans Azure Cosmos DB. Vous pouvez interroger directement les mêmes données à l’aide de Synapse Link avec le magasin analytique existant. Vous pouvez activer le magasin partitionné si vous avez les exigences suivantes :
* Filtres de requêtes analytiques courants pouvant être utilisés comme colonnes de partition
* Colonnes de partition à faible cardinalité
* Une colonne de partition distribue les données de manière égale entre les partitions
* Volume élevé d’opérations de mise à jour ou de suppression
* Ingestion de données lente 

À l’exception des charges de travail répondant aux exigences ci-dessus, si vous interrogez des données actives à l’aide de filtres de requête différents des clés de partition, nous vous recommandons d’interroger directement le magasin analytique. C’est particulièrement vrai si les travaux de partitionnement ne sont pas planifiés pour s’exécuter fréquemment.

## <a name="benefits"></a>Avantages

### <a name="reduced-data-scanning-from-partition-pruning"></a>Analyse des données réduite à partir du nettoyage de partition

Étant donné que les données correspondant à chaque clé de partition unique sont colocalisées dans le magasin partitionné, lorsque vous utilisez la clé de partition en tant que filtre de requête, les exécutions de requête peuvent nettoyer les données sous-jacentes et analyser uniquement les données requises. En analysant les données limitées, le nettoyage de partition améliore les performances des requêtes analytiques.

### <a name="flexibility-to-partition-your-analytical-data"></a>Flexibilité pour partitionner vos données analytiques

Vous pouvez avoir plusieurs stratégies de partitionnement pour un conteneur de magasin analytique donné. Vous pouvez utiliser des clés de partition composites ou séparées en fonction de vos besoins en matière de requêtes. Pour obtenir de l’aide à ce sujet, consultez les stratégies de partition. 

### <a name="query-performance-improvements"></a>Améliorations des performances des requêtes

En plus des améliorations apportées aux requêtes par le biais du nettoyage de partition, le partitionnement personnalisé permet également d’améliorer les performances des requêtes pour les charges de travail suivantes :

* **Mettre à jour/supprimer des charges de travail lourdes** : au lieu de suivre plusieurs versions d’enregistrements dans le magasin analytique et de les charger lors de chaque exécution de requête, le magasin partitionné contient uniquement la dernière version des données. Cela améliore considérablement les performances des requêtes en cas de charges de travail volumineuses de mise à jour/suppression.

* **Ralentissement des charges de travail d’ingestion de données** : le partitionnement compacte les données analytiques. Par conséquent, si votre charge de travail est en cours d’ingestion de données, cette compression peut entraîner de meilleures performances de requête.

### <a name="transactional-guarantee"></a>Garantie transactionnelle

Il est important de noter que le partitionnement personnalisé assure une garantie transactionnelle complète. Le chemin d'accès de la requête n’est pas bloqué durant l’exécution du partitionnement. Chaque exécution de requête lit les données partitionnées à partir du dernier partitionnement réussi. Elle lit les données les plus récentes provenant du magasin analytique, ce qui permet de s’assurer que les requêtes retournent toujours les données les plus récentes disponibles lors de l’utilisation du magasin partitionné.

## <a name="security"></a>Sécurité

Si vous avez configuré des [points de terminaison privés managés](analytical-store-private-endpoints.md) pour votre magasin analytique, pour garantir l’isolement réseau pour le magasin partitionné, nous vous recommandons d’ajouter également des points de terminaison privés managés pour le magasin partitionné. Le magasin partitionné est un compte de stockage principal associé à votre espace de travail Synapse.

De même, si vous avez configuré des [clés managées par le client sur le magasin analytique](how-to-setup-cmk.md#is-it-possible-to-use-customer-managed-keys-in-conjunction-with-the-azure-cosmos-db-analytical-store), vous devez les activer directement sur le compte de stockage principal de l’espace de travail Synapse, qui est également le magasin partitionné.

## <a name="partitioning-strategies"></a>Stratégies de partitionnement
Vous pouvez utiliser une ou plusieurs clés de partition pour vos données analytiques. Si vous utilisez plusieurs clés de partition, vous trouverez ci-dessous des recommandations sur la façon de partitionner les données : 
   - **Utilisation de clés composites :**

     Supposons que vous souhaitez effectuer fréquemment des requêtes basées sur clé1 et clé2. 
      
     Par exemple, « Requête pour tous les enregistrements où DateLecture = ’2021-10-08’ et Emplacement = ’Sydney’ ». 
       
     Dans ce cas, l’utilisation de clés composites est plus efficace pour rechercher tous les enregistrements correspondant à DateLecture, et ceux correspondant à Emplacement parmi ceux correspondant à DateLecture. 
       
     Exemples d’options de configuration :      
     ```python
     .option("spark.cosmos.asns.partition.keys", "ReadDate String, Location String") \
     .option("spark.cosmos.asns.basePath", "/mnt/CosmosDBPartitionedStore/") \
     ```
      
     Maintenant, sur le magasin partitionné ci-dessus, si vous voulez seulement faire une requête basée sur le filtre « Emplacement » :      
     * Vous pouvez interroger directement le magasin analytique. Le magasin partitionné analyse tous les enregistrements par DateLecture, puis par Emplacement. 
     Ainsi, en fonction de votre charge de travail et de la cardinalité de vos données analytiques, vous pouvez obtenir de meilleurs résultats en interrogeant directement le magasin analytique. 
     * Vous pouvez également exécuter un autre travail de partition pour partitionner également en fonction de « Emplacement » sur le même magasin partitionné.
                           
  *  **Utilisation de plusieurs clés séparément :**
     
     Supposons que vous souhaitez interroger fréquemment, tantôt sur la base de « DateLecture », tantôt sur la base de « Emplacement ». 
     
     Par exemple, 
     - Recherchez tous les enregistrements où DateLecture = ’2021-10-08’
     - Recherchez tous les enregistrements où Emplacement = ’Sydney’
     
     Exécutez deux travaux de partition avec des clés de partition telles que celles définies ci-dessous pour ce scénario :      
     
     Travail 1 :
     ```python
     .option("spark.cosmos.asns.partition.keys", "ReadDate String") \
     .option("spark.cosmos.asns.basePath", "/mnt/CosmosDBPartitionedStore/") \
     ```                  
     Travail 2 : 
     ```python
     .option("spark.cosmos.asns.partition.keys", "Location String") \
     .option("spark.cosmos.asns.basePath", "/mnt/CosmosDBPartitionedStore/") \
     ```        
     Notez qu’il n’est pas efficace d’interroger fréquemment sur la base des filtres « DateLecture » et « Emplacement », sur le partitionnement ci-dessus. Dans ce cas, des clés composites produiront de meilleures performances de requête. 
      
## <a name="limitations"></a>Limites

* Le partitionnement personnalisé n’est disponible que pour Azure Synapse Spark. Le partitionnement personnalisé n’est actuellement pas pris en charge pour les pools de SQL sans serveur.

* Le magasin actuellement partitionné ne peut pointer que vers le compte de stockage principal associé à l’espace de travail Synapse. La sélection de comptes de stockage personnalisés n’est pas prise en charge à ce stade.

* LE partitionnement personnalisé n’est disponible que pour l’API SQL dans Cosmos DB. Les API pour Mongo DB, Gremlin et Cassandra ne sont pas prises en charge pour l’instant. 

## <a name="pricing"></a>Tarifs

En plus de la [tarification pour Azure Synapse Link](synapse-link.md#pricing), vous devez régler les frais suivants lors de l’utilisation du partitionnement personnalisé :

* Vous êtes [facturé](https://azure.microsoft.com/pricing/details/synapse-analytics/#pricing) pour l’utilisation de pools Synapse Apache Spark lorsque vous exécutez des travaux de partitionnement sur le magasin analytique.

* Les données partitionnées sont stockées dans le compte Azure Data Lake Storage Gen2 principal associé à votre espace de travail Azure Synapse Analytics. Vous devrez assumer les coûts associés à l’utilisation du stockage et des transactions ADLS Gen2. Ces coûts sont déterminés par le stockage requis pour les données analytiques partitionnées et les données traitées pour les requêtes analytiques dans Synapse, respectivement. Pour plus d’informations sur la tarification, consultez la page [Tarification d’Azure Data Lake Storage](https://azure.microsoft.com/pricing/details/storage/data-lake/).

## <a name="frequently-asked-questions"></a>Forum aux questions

### <a name="how-often-should-i-run-the-custom-partitioning-job"></a>À quelle fréquence dois-je exécuter le travail de partitionnement personnalisé ?

Il existe plusieurs facteurs tels que le volume de données incrémentiel, les exigences de latence de requête, etc., qui déterminent la fréquence à laquelle vous pouvez exécuter le travail de partitionnement personnalisé. Il peut être exécuté une fois par jour ou une fois par heure. Vous souhaiterez peut-être planifier le travail de partitionnement plus souvent si le volume de données entrant est élevé et que la latence de requête attendue est faible. Vous devez également accumuler d’abord des données incrémentielles dans le magasin analytique pour que le nettoyage de partition soit effectif.

### <a name="do-the-query-results-include-latest-data-while-the-partitioning-job-execution-is-in-progress"></a>Les résultats de la requête incluent-ils les données les plus récentes pendant que l’exécution du travail de partitionnement est en cours ?

Oui, le partitionnement personnalisé fournit une garantie transactionnelle complète. Ainsi, les résultats de la requête à tout moment combinent les données partitionnées existantes avec les données de fin qui renvoient le dernier jeu de données du magasin analytique.

### <a name="can-the-custom-partitioning-make-use-of-linked-service-authentication-on-azure-synapse-analytics"></a>Le partitionnement personnalisé peut-il utiliser l’authentification du service lié sur Azure Synapse Analytics ?

Oui, l’authentification du service lié peut être utilisée pour le partitionnement du magasin analytique.

### <a name="can-i-change-the-partition-key-for-a-given-container-at-a-later-point-in-time"></a>Puis-je modifier ultérieurement la clé de partition d’un conteneur donné ?

Oui, la clé de partition pour le conteneur donné peut être modifiée et la nouvelle définition de clé de partition créera un nouveau magasin partitionné.

> [!NOTE]
> La définition de la clé de partition fait partie du chemin d’accès du magasin partitionné.

### <a name="can-different-partition-keys-point-to-the-same-basepath"></a>Des clés de partition différentes peuvent-elles pointer vers le même BasePath ?

Oui, vous pouvez spécifier plusieurs clés de partition sur le même magasin partitionné, comme ci-dessous : 

```python
.option("spark.cosmos.asns.partition.keys", "ReadDate String, Location String") \
.option("spark.cosmos.asns.basePath", "/mnt/CosmosDBPartitionedStore/") \
```

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus, consultez les documents suivants :

* [Configurez le partitionnement personnalisé](configure-custom-partitioning.md) pour partitionner les données du magasin analytique.
* [Synapse Link pour Azure Cosmos DB](synapse-link.md)
* [Vue d’ensemble du magasin analytique Azure Cosmos DB](analytical-store-introduction.md)
* [Prise en main d’Azure Synapse Link pour Azure Cosmos DB](configure-synapse-link.md)
* [Forum aux questions sur Azure Synapse Link pour Azure Cosmos DB](synapse-link-frequently-asked-questions.yml)
* [Cas d’utilisation d’Azure Synapse Link pour Azure Cosmos DB](synapse-link-use-cases.md)
