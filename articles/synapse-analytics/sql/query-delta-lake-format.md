---
title: Interroger des fichiers Delta Lake à l’aide d’un pool SQL serverless
description: Dans cet article, vous apprendrez comment interroger des fichiers stockés au format Apache Delta Lake à l’aide d’un pool SQL serverless.
services: synapse analytics
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 07/15/2021
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: jrasnick, wiassaf
ms.custom: ignite-fall-2021
ms.openlocfilehash: e103cad62f213cdd39c92c8743753aede7cc0a75
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131018618"
---
# <a name="query-delta-lake-files-using-serverless-sql-pool-in-azure-synapse-analytics"></a>Interroger des fichiers Delta Lake à l’aide d’un pool SQL dans Azure Synapse Analytics

Dans cet article, vous apprendrez comment écrire une requête à l’aide du pool Synapse SQL serverless pour lire les fichiers Apache Delta Lake.
Delta Lake est une couche de stockage open source qui apporte des transactions ACID (atomicité, cohérence, isolation et durabilité) à Apache Spark et aux charges de travail Big Data.

Le pool SQL serverless de l’espace de travail Synapse vous permet de lire les données stockées au format Delta Lake et de les traiter dans des outils de création de rapports. Un pool SQL serverless peut lire les fichiers Delta Lake créés à l’aide d’Apache Spark, Azure Databricks ou tout autre producteur du format Delta Lake.

Les pools Apache Spark dans Azure Synapse permettent aux ingénieurs des données de modifier les fichiers Delta Lake à l’aide de Scala, PySpark et .NET. Les pools SQL serverless aident les analystes de données à créer des rapports sur les fichiers Delta Lake créés par des ingénieurs de données.

## <a name="quickstart-example"></a>Exemple de démarrage rapide

La fonction [OPENROWSET](develop-openrowset.md) vous permet de lire le contenu d’un fichier Delta Lake en indiquant l’URL de votre dossier racine.

### <a name="read-delta-lake-folder"></a>Lire le dossier Delta Lake

Le moyen le plus simple d’afficher le contenu de votre `DELTA`fichier consiste à spécifier l’URL du fichier dans la fonction [OPENROWSET](develop-openrowset.md) et à spécifier le format `DELTA`. Si le fichier est disponible publiquement ou si votre identité Azure AD peut y accéder, vous devriez pouvoir voir le contenu du fichier à l’aide d’une requête comme celle montrée dans l’exemple suivant :

```sql
SELECT TOP 10 *
FROM OPENROWSET(
    BULK 'https://sqlondemandstorage.blob.core.windows.net/delta-lake/covid/',
    FORMAT = 'delta') as rows;
```

Les noms de colonnes et les types de données sont automatiquement lus à partir des fichiers Delta Lake. La fonction `OPENROWSET` utilise les meilleurs types de suppositions comme VARCHAR(1000) pour les colonnes de chaîne.

L’URI de la fonction `OPENROWSET` doit référencer le dossier racine Delta Lake qui contient un sous-dossier appelé `_delta_log`.

> [!div class="mx-imgBorder"]
>![Dossier ECDC COVID-19 Delta Lake](./media/shared/covid-delta-lake-studio.png)

Si vous ne disposez pas de ce sous-dossier, vous n’utilisez pas le format Delta Lake. Vous pouvez convertir vos fichiers Parquet bruts dans le dossier au format Delta Lake à l’aide du script Python Apache Spark suivant :

```python
%%pyspark
from delta.tables import *
deltaTable = DeltaTable.convertToDelta(spark, "parquet.`abfss://delta-lake@sqlondemandstorage.dfs.core.windows.net/covid`")
```

Pour améliorer les performances de vos requêtes, envisagez de spécifier des types explicites dans [la clause `WITH`](#explicitly-specify-schema).

> [!NOTE]
> Le pool SQL Synapse serverless utilise l’inférence de schéma pour déterminer automatiquement les colonnes et leurs types. Les règles d’inférence de schéma sont les mêmes que celles utilisées pour les fichiers Parquet.
> Pour le mappage de type Delta Lake en type SQL natif, sélectionnez [Mappage de type pour Parquet](develop-openrowset.md#type-mapping-for-parquet). 

Assurez-vous de pouvoir accéder à votre fichier. Si votre fichier est protégé par une clé SAP ou une identité Azure personnalisée, vous devez configurer les [informations d’identification au niveau du serveur pour la connexion SQL](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#server-scoped-credential).

> [!IMPORTANT]
> Assurez-vous que vous utilisez un classement de base de données UTF-8 (par exemple `Latin1_General_100_BIN2_UTF8`), car les valeurs de chaîne dans les fichiers Delta Lake sont encodées à l’aide d’un encodage UTF-8.
> Une incompatibilité entre l’encodage de texte dans le fichier Delta Lake et le classement peut entraîner des erreurs de conversion inattendues.
> Vous pouvez facilement modifier le classement par défaut de la base de données actuelle à l’aide de l’instruction T-SQL suivante : `alter database current collate Latin1_General_100_BIN2_UTF8`

### <a name="data-source-usage"></a>Utilisation d’une source de données

Les exemples précédents ont utilisé le chemin d’accès complet au fichier. Vous pouvez également créer une source de données externe avec l’emplacement qui pointe vers le dossier racine du stockage. Une fois que vous avez créé la source de données externe, utilisez la source de données et le chemin d’accès relatif au fichier dans la fonction `OPENROWSET`. De cette façon, vous n’avez pas besoin d’utiliser l’URI absolu complet pour vos fichiers. Vous pouvez également définir des informations d’identification personnalisées pour accéder à l’emplacement de stockage.

> [!IMPORTANT]
> Les sources de données ne peuvent être créées que dans des bases de données personnalisées (et non dans la base de données Master ou dans les bases de données répliquées à partir de pools Apache Spark). 

Pour utiliser les exemples ci-dessous, vous devez effectuer l’étape suivante :
1. **Créez une base de données** avec une source de données qui fait référence au compte de stockage [NYC Yellow Taxi](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/). 
1. Initialisez les objets en exécutant le [script d’installation](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) sur la base de données que vous avez créée à l’étape 1. Ce script crée les sources de données, les informations d’identification étendues à la base de données et les formats de fichiers externes utilisés dans ces exemples.

Si vous avez créé votre base de données et basculé le contexte vers votre base de données (à l’aide de l'instruction `USE database_name` ou de la liste déroulante pour sélectionner une base de données dans un éditeur de requête), vous pouvez créer votre source de données externe contenant l’URI racine de votre jeu de données et l’utiliser pour interroger les fichiers Delta Lake :

```sql
CREATE EXTERNAL DATA SOURCE DeltaLakeStorage
WITH ( LOCATION = 'https://sqlondemandstorage.blob.core.windows.net/delta-lake/' );
GO

SELECT TOP 10 *
FROM OPENROWSET(
        BULK 'covid',
        DATA_SOURCE = 'DeltaLakeStorage',
        FORMAT = 'delta'
    ) as rows;
```

Si une source de données est protégée par une clé SAS ou une identité personnalisée, vous pouvez configurer la [source de données avec des informations d’identification dans l’étendue de la base de données](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#database-scoped-credential).

### <a name="explicitly-specify-schema"></a>Spécifier explicitement le schéma

`OPENROWSET` vous permet de spécifier explicitement les colonnes que vous souhaitez lire à partir du fichier à l’aide de la clause `WITH` :

```sql
SELECT TOP 10 *
FROM OPENROWSET(
        BULK 'covid',
        DATA_SOURCE = 'DeltaLakeStorage',
        FORMAT = 'delta'
    )
    WITH ( date_rep date,
           cases int,
           geo_id varchar(6)
           ) as rows;
```

Avec la spécification explicite du schéma du jeu de résultats, vous pouvez réduire les tailles de type et utiliser les types plus précis VARCHAR(6) pour les colonnes de chaîne au lieu d’un VARCHAR(1000) pessimiste. La minimisation des types peut améliorer considérablement les performances de vos requêtes.

> [!IMPORTANT]
> Veillez à spécifier explicitement un classement UTF-8 (par exemple `Latin1_General_100_BIN2_UTF8`) pour toutes les colonnes de chaîne dans la clause `WITH`, ou définissez un classement UTF-8 au niveau de la base de données.
> Une incompatibilité entre l’encodage de texte dans le fichier et le classement de colonne de chaîne peut entraîner des erreurs de conversion inattendues.
> Vous pouvez facilement modifier le classement par défaut de la base de données actuelle à l’aide de l’instruction T-SQL suivante : `alter database current collate Latin1_General_100_BIN2_UTF8`
> Vous pouvez facilement définir le classement sur les types de colonne à l’aide de la définition suivante : `geo_id varchar(6) collate Latin1_General_100_BIN2_UTF8`

## <a name="dataset"></a>Dataset

Le jeu de données [NYC Yellow Taxi](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/) est utilisé dans cet exemple. Vous pouvez interroger les fichiers Parquet de la même façon que vous [lisez des fichiers CSV](query-parquet-files.md). La seule différence réside dans le fait que le paramètre `FILEFORMAT` doit être défini sur `PARQUET`. Les exemples de cet article montrent les spécificités de lecture des fichiers Parquet.


### <a name="query-partitioned-data"></a>Interroger des données partitionnées
Le jeu de données fourni dans cet exemple est divisé (partitionné) en sous-dossiers distincts.
Contrairement au format [Parquet](query-parquet-files.md), vous n’avez pas besoin de cibler des partitions spécifiques à l’aide de la fonction `FILEPATH`. `OPENROWSET` identifie les colonnes de partitionnement dans votre structure de dossiers Delta Lake et vous permet d’interroger directement les données à l’aide de ces colonnes. Cet exemple montre les montants par année, par mois et par type de paiement pour les trois premiers mois de 2017.

```sql
SELECT
        YEAR(pickup_datetime) AS year,
        passenger_count,
        COUNT(*) AS cnt
FROM  
    OPENROWSET(
        BULK 'yellow',
        DATA_SOURCE = 'DeltaLakeStorage',
        FORMAT='DELTA'
    ) nyc
WHERE
    nyc.year = 2017
    AND nyc.month IN (1, 2, 3)
    AND pickup_datetime BETWEEN CAST('1/1/2017' AS datetime) AND CAST('3/31/2017' AS datetime)
GROUP BY
    passenger_count,
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime),
    passenger_count;
```

La fonction `OPENROWSET` éliminera les partitions qui ne correspondent pas à `year` et `month` dans la clause where. Cette technique de nettoyage de fichiers/partitions réduira considérablement votre jeu de données, améliorera les performances et réduira le coût de la requête.

Le nom du dossier dans la fonction `OPENROWSET` (`yellow` dans cet exemple) est concaténé à l’aide de `LOCATION` la source de données `DeltaLakeStorage`, et doit faire référence au dossier racine Delta Lake qui contient un sous-dossier appelé `_delta_log`.

> [!div class="mx-imgBorder"]
>![Dossier Yellow Taxi Delta Lake](./media/shared/yellow-taxi-delta-lake.png)

Si vous ne disposez pas de ce sous-dossier, vous n’utilisez pas le format Delta Lake. Vous pouvez convertir vos fichiers Parquet bruts dans le dossier au format Delta Lake à l’aide du script Python Apache Spark suivant :

```python
%%pyspark
from delta.tables import *
deltaTable = DeltaTable.convertToDelta(spark, "parquet.`abfss://delta-lake@sqlondemandstorage.dfs.core.windows.net/yellow`", "year INT, month INT")
```

Le deuxième argument de la fonction `DeltaTable.convertToDeltaLake` représente les colonnes de partitionnement (année et mois) qui font partie du modèle de dossier (`year=*/month=*` dans cet exemple) et leurs types.

## <a name="limitations"></a>Limites

- Consultez les limitations et problèmes connus dans la [page d’aide relative aux pools SQL serverless Synapse](resources-self-help-sql-on-demand.md#delta-lake).
- À l’heure actuelle, le pool Spark et le pool SQL serverless dans Azure Synapse Analytics prennent en charge le format Delta Lake. Les pools SQL serverless ne prennent pas en charge la mise à jour des fichiers Delta Lake. Seules les tables au format Parquet sont partagées à partir des pools Spark vers un pool SQL serverless. Pour plus d’informations, consultez [Tables Spark partagées](../metadata/table.md#shared-spark-tables).

## <a name="next-steps"></a>Étapes suivantes

Passez à l’article suivant pour savoir comment [interroger des types imbriqués Parquet](query-parquet-nested-types.md).
Si vous souhaitez continuer à créer une solution Delta Lake, découvrez comment créer des [vues](create-use-views.md#delta-lake-views) ou des [tables externes](create-use-external-tables.md#delta-lake-external-table) dans le dossier Delta Lake.

## <a name="see-also"></a>Voir aussi

- [Présentation de Delta Lake](../spark/apache-spark-what-is-delta-lake.md)
- [Apprenez comment utiliser Delta Lake dans les pools Apache Spark pour Azure Synapse Analytics](../spark/apache-spark-delta-lake-overview.md)
- [Bonnes pratiques pour Azure Databricks Delta Lake](/azure/databricks/best-practices-index)
- [Page de documentation sur Delta Lake](https://docs.delta.io/latest/delta-intro.html)
- [Problèmes connus et limitations](resources-self-help-sql-on-demand.md#delta-lake)
