---
title: Copier et transformer des données dans Azure Database pour PostgreSQL
titleSuffix: Azure Data Factory & Azure Synapse
description: Apprenez à copier et à transformer des données dans la Base de données Azure pour PostgreSQL à l’aide d’Azure Data Factory.
ms.author: jianleishen
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: synapse
ms.date: 10/25/2021
ms.openlocfilehash: ea2b0acf7c10f942d77c70a574fb19f0c29ca336
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131473221"
---
# <a name="copy-and-transform-data-in-azure-database-for-postgresql-using-azure-data-factory-or-synapse-analytics"></a>Copier et transformer des données dans la Base de données Azure pour PostgreSQL à l’aide d’Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Cet article indique comment utiliser l’activité de copie dans Azure Data Factory et les pipelines Synapse Analytics pour copier des données depuis et vers la Base de données Azure pour PostgreSQL, et utiliser Data Flow pour transformer les données dans la Base de données Azure pour PostgreSQL. Pour en savoir plus, lisez les articles d’introduction d’[Azure Data Factory](introduction.md) et d’[Azure Synapse Analytics](../synapse-analytics/overview-what-is.md).

Ce connecteur est spécialisé pour le [service Azure Database pour PostgreSQL](../postgresql/overview.md). Pour copier des données à partir d’une base de données PostgreSQL générique locale ou située dans le cloud, utilisez le [connecteur PostgreSQL](connector-postgresql.md).

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Ce connecteur Azure Database pour PostgreSQL est pris en charge pour les activités suivantes :

- [Activité de copie](copy-activity-overview.md) avec [prise en charge de la matrice source/récepteur](copy-activity-overview.md)
- [Mappage de flux de données](concepts-data-flow-overview.md)
- [Activité de recherche](control-flow-lookup-activity.md)

Actuellement, le flux de données dans Azure Data Factory prend en charge la Base de données Azure pour PostgreSQL – Serveur unique, à l’exclusion du Serveur flexible ou Hyperscale (Citus) ; le flux de données dans Azure Synapse Analytics prend en charge toutes les saveurs de PostgreSQL.

## <a name="getting-started"></a>Prise en main

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-a-linked-service-to-azure-database-for-postgresql-using-ui"></a>Créer un service lié à la base de données Azure pour PostgreSQL en utilisant l'interface utilisateur.

Utilisez les étapes suivantes pour créer un service lié à Azure Database pour MySQL dans l’interface utilisateur du portail Azure.

1. Accédez à l’onglet Gérer dans votre espace de travail Azure Data Factory ou Synapse et sélectionnez Services liés, puis cliquez sur Nouveau :

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory).

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="Créez un nouveau service lié avec l’interface utilisateur Azure Data Factory.":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="Créez un nouveau service lié avec l’interface utilisateur Azure Synapse.":::

2. Recherchez PostgreSQL et sélectionnez le connecteur Azure Database pour PostgreSQL.

    :::image type="content" source="media/connector-azure-database-for-postgresql/azure-database-for-postgresql-connector.png" alt-text="Sélectionnez le connecteur Azure database for PostgreSQL.":::    

1. Configurez les informations du service, testez la connexion et créez le nouveau service lié.

    :::image type="content" source="media/connector-azure-database-for-postgresql/configure-azure-database-for-postgresql-linked-service.png" alt-text="Configurez un service lié à la base de données Azure pour PostgreSQL.":::

## <a name="connector-configuration-details"></a>Informations de configuration des connecteurs

Les sections suivantes détaillent les propriétés utilisées pour définir des entités Data Factory propres au connecteur Azure Database pour PostgreSQL.

## <a name="linked-service-properties"></a>Propriétés du service lié

Les propriétés suivantes sont prises en charge pour le service lié Azure Database pour PostgreSQL :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type doit être définie sur : **AzurePostgreSql**. | Oui |
| connectionString | Chaîne de connexion ODBC permettant de se connecter à Azure Database pour PostgreSQL.<br/>Vous pouvez également définir un mot de passe dans Azure Key Vault et extraire la configuration `password` de la chaîne de connexion. Pour plus d’informations, consultez les exemples suivants et [Stocker des informations d’identification dans Azure Key Vault](store-credentials-in-key-vault.md). | Oui |
| connectVia | Cette propriété correspond au [runtime d’intégration](concepts-integration-runtime.md) à utiliser pour la connexion à la banque de données. Vous pouvez utiliser runtime d’intégration Azure ou un runtime d’intégration auto-hébergé (si votre banque de données se trouve dans un réseau privé). À défaut de spécification, le runtime d’intégration Azure par défaut est utilisé. |Non |

Voici un exemple de chaîne de connexion typique : `Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;Password=<Password>`. Selon votre cas de figure, vous pouvez définir d’autres propriétés :

| Propriété | Description | Options | Obligatoire |
|:--- |:--- |:--- |:--- |
| EncryptionMethod (EM)| La méthode utilisée par le pilote pour chiffrer les données envoyées entre le pilote et le serveur de base de données. Par exemple, `EncryptionMethod=<0/1/6>;`| 0 (aucun chiffrement) **(par défaut)** / 1 (SSL) / 6 (RequestSSL) | Non |
| ValidateServerCertificate (VSC) | Détermine si le pilote valide le certificat envoyé par le serveur de base de données lorsque le chiffrement SSL est activé (EncryptionMethod = 1). Par exemple, `ValidateServerCertificate=<0/1>;`| 0 (désactivé) **(par défaut)** / 1 (activé) | Non |

**Exemple** :

```json
{
    "name": "AzurePostgreSqlLinkedService",
    "properties": {
        "type": "AzurePostgreSql",
        "typeProperties": {
            "connectionString": "Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;Password=<Password>"
        }
    }
}
```

**Exemple** :

***Stockage du mot de passe dans Azure Key Vault***

```json
{
    "name": "AzurePostgreSqlLinkedService",
    "properties": {
        "type": "AzurePostgreSql",
        "typeProperties": {
            "connectionString": "Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;",
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Propriétés du jeu de données

Pour obtenir la liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article sur les [jeux de données](concepts-datasets-linked-services.md). Cette section fournit la liste des propriétés prises en charge par le jeu de données Azure Database pour PostgreSQL.

Pour copier des données d’Azure Database pour PostgreSQL, affectez la valeur **AzurePostgreSqlTable** à la propriété type du jeu de données. Les propriétés prises en charge sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type du jeu de données doit être définie sur **AzurePostgreSqlTable** | Oui |
| tableName | Nom de la table | Non (si « query » dans la source de l’activité est spécifié) |

**Exemple** :

```json
{
    "name": "AzurePostgreSqlDataset",
    "properties": {
        "type": "AzurePostgreSqlTable",
        "linkedServiceName": {
            "referenceName": "<AzurePostgreSql linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie

Pour obtenir la liste complète des sections et propriétés disponibles pour la définition des activités, consultez [Pipelines et activités](concepts-pipelines-activities.md). Cette section fournit la liste des propriétés prises en charge par une source Azure Database pour PostgreSQL.

### <a name="azure-database-for-postgresql-as-source"></a>Azure Database pour PostgreSQL en tant que source

Pour copier des données d’Azure Database pour PostgreSQL, affectez la valeur **AzurePostgreSqlSource** au type source de l’activité de copie. Les propriétés prises en charge dans la section **source** de l’activité de copie sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type de la source de l’activité de copie doit être définie sur **AzurePostgreSqlSource** | Oui |
| query | Utiliser la requête SQL personnalisée pour lire les données. Par exemple, `SELECT * FROM mytable` ou `SELECT * FROM "MyTable"`. Notez que dans PostgreSQL, le nom de l’entité est traité comme insensible à la casse s’il n’est pas placé entre guillemets. | Non (si la propriété tableName du jeu de données est spécifiée) |
| partitionOptions | Spécifie les options de partitionnement des données utilisées pour charger des données à partir d’Azure SQL Database. <br>Les valeurs autorisées sont les suivantes : **None** (valeur par défaut), **PhysicalPartitionsOfTable** et **DynamicRange**.<br>Lorsqu’une option de partition est activée (autrement dit, pas `None`), le degré de parallélisme pour charger simultanément des données à partir d’une instance Azure SQL Database est contrôlé par le paramètre [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) de l’activité de copie. | Non |
| partitionSettings | Spécifiez le groupe de paramètres pour le partitionnement des données. <br>S’applique lorsque l’option de partitionnement n’est pas `None`. | Non |
| ***Sous `partitionSettings`:*** | | |
| partitionNames | La liste des partitions physiques qui doivent être copiées. <br>S’applique lorsque l’option de partitionnement est `PhysicalPartitionsOfTable`. Si vous utilisez une requête pour récupérer des données sources, utilisez `?AdfTabularPartitionName` dans la clause WHERE. Pour obtenir un exemple, consultez la section [Copier en parallèle à partir de la Base de données pour PostgreSQL](#parallel-copy-from-azure-database-for-postgresql). | Non |
| partitionColumnName | Spécifiez le nom de la colonne source **en type entier ou date/DateHeure** (`int`, `smallint`, `bigint`, `date`, `timestamp without time zone`, `timestamp with time zone` ou `time without time zone`) qu’utilisera le partitionnement par plages de valeurs pour la copie en parallèle. S’il n’est pas spécifié, la clé primaire de la table sera automatiquement détectée et utilisée en tant que colonne de partition.<br>S’applique lorsque l’option de partitionnement est `DynamicRange`. Si vous utilisez une requête pour récupérer des données sources, utilisez `?AdfRangePartitionColumnName ` dans la clause WHERE. Pour obtenir un exemple, consultez la section [Copier en parallèle à partir de la Base de données pour PostgreSQL](#parallel-copy-from-azure-database-for-postgresql). | Non |
| partitionUpperBound | Valeur maximale de la colonne de partition à partir de laquelle copier des données. <br>S’applique lorsque l’option de partitionnement est `DynamicRange`. Si vous utilisez une requête pour récupérer des données sources, utilisez `?AdfRangePartitionUpbound` dans la clause WHERE. Pour obtenir un exemple, consultez la section [Copier en parallèle à partir de la Base de données pour PostgreSQL](#parallel-copy-from-azure-database-for-postgresql). | Non |
| partitionLowerBound | Valeur minimale de la colonne de partition à partir de laquelle copier des données. <br>S’applique lorsque l’option de partitionnement est `DynamicRange`. Si vous utilisez une requête pour récupérer des données sources, utilisez `?AdfRangePartitionLowbound` dans la clause WHERE. Pour obtenir un exemple, consultez la section [Copier en parallèle à partir de la Base de données pour PostgreSQL](#parallel-copy-from-azure-database-for-postgresql). | Non |

**Exemple** :

```json
"activities":[
    {
        "name": "CopyFromAzurePostgreSql",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<AzurePostgreSql input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "AzurePostgreSqlSource",
                "query": "<custom query e.g. SELECT * FROM mytable>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-database-for-postgresql-as-sink"></a>Azure Database pour PostgreSQL en tant que récepteur

Pour copier des données vers Azure Database pour PostgreSQL, les propriétés suivantes sont prises en charge dans la section **sink** de l’activité de copie :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type du récepteur d’activité de copie doit être définie sur **AzurePostgreSQLSink**. | Oui |
| preCopyScript | Spécifiez une requête SQL pour l’activité de copie à exécuter avant d'écrire des données dans Azure Database pour PostgreSQL à chaque exécution. Vous pouvez utiliser cette propriété pour nettoyer des données préchargées. | Non |
| writeMethod | Méthode utilisée pour écrire des données dans Azure Database pour PostgreSQL.<br>Les valeurs autorisées sont **CopyCommand** (valeur par défaut et la plus performante) et **BulkInsert**. | Non |
| writeBatchSize | Nombre de lignes chargées dans Azure Database pour PostgreSQL par lot.<br>La valeur autorisée est un entier qui représente le nombre de lignes. | Non (valeur par défaut : 1 000 000) |
| writeBatchTimeout | Temps d’attente pour que l’opération d’insertion de lot soit terminée avant d’expirer.<br>Les valeurs autorisées sont des intervalles de temps. Exemple : 00:30:00 (30 minutes). | Non (valeur par défaut : 00:30:00) |

**Exemple** :

```json
"activities":[
    {
        "name": "CopyToAzureDatabaseForPostgreSQL",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure PostgreSQL output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzurePostgreSQLSink",
                "preCopyScript": "<custom SQL script>",
                "writeMethod": "CopyCommand",
                "writeBatchSize": 1000000
            }
        }
    }
]
```

## <a name="parallel-copy-from-azure-database-for-postgresql"></a>Copie parallèle à partir de la Base de données Azure pour PostgreSQL

Le connecteur de la Base de données Azure dans l’activité de copie propose un partitionnement de données intégré pour copier des données en parallèle. Vous trouverez des options de partitionnement de données dans l’onglet **Source** de l’activité de copie.

![Capture d’écran représentant les options de partition](.\media\connector-azure-database-for-postgresql/connector-postgresql-partition-options.png)

Lorsque vous activez la copie partitionnée, l’activité de copie exécute des requêtes en parallèle sur la Base de données Azure pour la source PostgreSQL afin de charger des données par partitions. Le degré de parallélisme est contrôlé via le paramètre [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) sur l’activité de copie. Par exemple, si vous définissez `parallelCopies` sur quatre, le service génère et exécute simultanément quatre requêtes en fonction de l’option de partition et des paramètres que vous avez spécifiés, et chaque requête récupère une partie des données à partir de votre Base de données Azure pour PostgreSQL.

Il vous est recommandé d’activer la copie en parallèle avec partitionnement des données, notamment lorsque vous chargez une grande quantité de données à partir de votre Base de données Azure pour PostgreSQL. Voici quelques suggestions de configurations pour différents scénarios. Lors de la copie de données dans un magasin de données basé sur un fichier, il est recommandé d’écrire les données dans un dossier sous la forme de plusieurs fichiers (spécifiez uniquement le nom du dossier). Les performances seront meilleures qu’avec l’écriture de données dans un seul fichier.

| Scénario                                                     | Paramètres suggérés                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Chargement complet à partir d’une table volumineuse, avec des partitions physiques.        | **Option de partition** : Partitions physiques de la table. <br><br/>Pendant l’exécution, le service détecte automatiquement les partitions physiques et copie les données par partition. |
| Chargement complet d’une table volumineuse, sans partitions physiques, avec une colonne entière pour le partitionnement des données. | **Options de partition** : Partition dynamique par spécification de plages de valeurs.<br>**Colonne de partition** : Spécifiez la colonne utilisée pour partitionner les données. Si la valeur n’est pas spécifiée, la colonne de la clé primaire est utilisée. |
| Chargement d’une grande quantité de données à l’aide d’une requête personnalisée, avec des partitions physiques. | **Option de partition** : Partitions physiques de la table.<br>**Requête**: `SELECT * FROM ?AdfTabularPartitionName WHERE <your_additional_where_clause>`.<br>**Nom de la partition** : Spécifiez le(s) nom(s) de partition à copier. Si ce n’est pas spécifié, le service détecte automatiquement les partitions physiques de la table que vous avez spécifiée dans le jeu de données PostgreSQL.<br><br>Pendant l’exécution, le service remplace `?AdfTabularPartitionName` par le nom réel de la partition et l’envoie à Azure Database pour PostgreSQL. |
| Chargement d’une grande quantité de données à l’aide d’une requête personnalisée, sans partitions physiques, et avec une colonne entière pour le partitionnement des données. | **Options de partition** : Partition dynamique par spécification de plages de valeurs.<br>**Requête**: `SELECT * FROM ?AdfTabularPartitionName WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`.<br>**Colonne de partition** : Spécifiez la colonne utilisée pour partitionner les données. Vous pouvez procéder au partitionnement par rapport à la colonne avec le type de données entier ou date/DateHeure.<br>**Limite supérieure de partition** et **limite inférieure de partition** : Indiquez si vous souhaitez filtrer le contenu par rapport à la colonne de partition pour récupérer uniquement les données entre les plages inférieure et supérieure.<br><br>Lors de l’exécution, le service remplace `?AdfRangePartitionColumnName` `?AdfRangePartitionUpbound`, et `?AdfRangePartitionLowbound` par le nom réel de la colonne et les plages de valeurs de chaque partition et les envoie à Azure Database pour PostgreSQL. <br>Par exemple, si votre colonne de partition « ID » est définie sur une limite inférieure de 1 et une limite supérieure de 80, avec une copie en parallèle définie sur 4, le service récupère les données via 4 partitions. Les ID sont inclus entre [1,20], [21, 40], [41, 60] et [61, 80], respectivement. |

Meilleures pratiques pour charger des données avec l’option de partition :

1. Choisissez une colonne distinctive comme colonne de partition (p. ex. : clé primaire ou clé unique) pour éviter l’asymétrie des données. 
2. Si la table possède une partition intégrée, utilisez l’option de partition « Partitions physiques de la table » pour obtenir de meilleures performances.    
3. Si vous utilisez Azure Integration Runtime pour copier des données, vous pouvez définir des « [unités d’intégration de données (DIU)](copy-activity-performance-features.md#data-integration-units) » plus grandes (>4) pour utiliser davantage de ressources de calcul. Vérifiez les scénarios applicables ici.
4. Le « [degré de parallélisme de copie](copy-activity-performance-features.md#parallel-copy) » contrôle le nombre de partitions : un nombre trop élevé nuit parfois aux performances. Il est recommandé de définir ce nombre selon (DIU ou nombre de nœuds d'IR auto-hébergé) * (2 à 4).

**Exemple : chargement complet à partir d’une table volumineuse, avec des partitions physiques**

```json
"source": {
    "type": "AzurePostgreSqlSource",
    "partitionOption": "PhysicalPartitionsOfTable"
}
```

**Exemple : requête avec partition dynamique par spécification de plages de valeurs**

```json
"source": {
    "type": "AzurePostgreSqlSource",
    "query": "SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column (optional) to decide the partition stride, not as data filter>",
        "partitionLowerBound": "<lower_value_of_partition_column (optional) to decide the partition stride, not as data filter>"
    }
}
```

## <a name="mapping-data-flow-properties"></a>Propriétés du mappage de flux de données

Lors de la transformation de données dans le flux de données de mappage, vous pouvez lire et écrire dans des tables à partir d’Azure Database pour PostgreSQL. Pour plus d’informations, consultez la [transformation de la source](data-flow-source.md) et la [transformation du récepteur](data-flow-sink.md) dans le flux de données de mappage. Vous pouvez choisir d’utiliser un jeu de données Azure Database pour PostgreSQL ou un [jeu de données inlined](data-flow-source.md#inline-datasets) en tant que type de source et de récepteur.

### <a name="source-transformation"></a>Transformation de la source

Le tableau ci-dessous répertorie les propriétés prises en charge par une source Azure Database pour PostgreSQL. Vous pouvez modifier ces propriétés sous l’onglet **Options de la source**.

| Nom | Description | Obligatoire | Valeurs autorisées | Propriété du script de flux de données |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Table de charge de travail | Si vous sélectionnez Table comme entrée, le flux de données extrait toutes les données de la table spécifiée dans le jeu de données. | Non | - |*(pour le jeu de données inlined uniquement)*<br>tableName |
| Requête | Si vous sélectionnez Requête comme entrée, spécifiez une requête SQL pour extraire des données de la source, qui remplace toute table que vous spécifiez dans le jeu de données. L’utilisation de requêtes est un excellent moyen de réduire le nombre de lignes pour les tests ou les recherches.<br><br>La clause **Order By** n’est pas prise en charge, mais vous pouvez définir une instruction SELECT FROM complète. Vous pouvez également utiliser des fonctions de table définies par l’utilisateur. **select * from udfGetData()** est une fonction UDF dans SQL qui retourne une table que vous pouvez utiliser dans le flux de données.<br>Exemple de requête : `select * from mytable where customerId > 1000 and customerId < 2000` ou `select * from "MyTable"`. Notez que dans PostgreSQL, le nom de l’entité est traité comme insensible à la casse s’il n’est pas placé entre guillemets.| Non | String | query |
| Taille du lot | Spécifiez la taille que doivent avoir les lots créés à partir d’un large volume de données. | Non | Integer | batchSize |
| Niveau d’isolation | Choisissez l’un des niveaux d’isolation suivants :<br>– Lecture validée.<br>– Lecture non validée (par défaut).<br>– Lecture renouvelable.<br>– Sérialisable.<br>– Aucun (ignorer le niveau d’isolation). | Non | <small>READ_COMMITTED<br/>READ_UNCOMMITTED<br/>REPEATABLE_READ<br/>SERIALIZABLE<br/>NONE</small> |isolationLevel |

#### <a name="azure-database-for-postgresql-source-script-example"></a>Exemple de script de source Azure Database pour PostgreSQL

Quand vous utilisez Azure Database pour PostgreSQL comme type de source, le script de flux de données associé est le suivant :

```
source(allowSchemaDrift: true,
    validateSchema: false,
    isolationLevel: 'READ_UNCOMMITTED',
    query: 'select * from mytable',
    format: 'query') ~> AzurePostgreSQLSource
```

### <a name="sink-transformation"></a>Transformation du récepteur

Le tableau ci-dessous répertorie les propriétés prises en charge par un récepteur Azure Database pour PostgreSQL. Vous pouvez modifier ces propriétés sous l’onglet **Options du récepteur**.

| Name | Description | Obligatoire | Valeurs autorisées | Propriété du script de flux de données |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Mettre à jour la méthode | Spécifiez les opérations autorisées sur la destination de votre base de données. Par défaut, seules les insertions sont autorisées.<br>Pour mettre à jour, effectuer un upsert ou supprimer des lignes, une [transformation de modification de ligne](data-flow-alter-row.md) est requise afin de baliser les lignes relatives à ces actions. | Oui | `true` ou `false` | deletable <br/>insertable <br/>updateable <br/>upsertable |
| Colonnes clés | Pour les mises à jour, les opérations upsert et les suppressions, une ou plusieurs colonnes clés doivent être définies afin de déterminer la ligne à modifier.<br>Le nom de colonne que vous choisissez comme clé sera utilisé dans le cadre des opérations suivantes de mise à jour, d’upsert et de suppression. Vous devez donc choisir une colonne qui existe dans le mappage du récepteur. | Non | Array | clés |
| Ignorer l’écriture des colonnes clés | Si vous ne souhaitez pas écrire la valeur dans la colonne clé, sélectionnez « Ignorer l’écriture des colonnes clés ». | Non | `true` ou `false` | skipKeyWrites |
| Action table |Détermine si toutes les lignes de la table de destination doivent être recréées ou supprimées avant l’écriture.<br>- **Aucun** : Aucune action ne sera effectuée sur la table.<br>- **Recréer** : La table sera supprimée et recréée. Obligatoire en cas de création dynamique d’une nouvelle table.<br>- **Tronquer** : Toutes les lignes de la table cible seront supprimées. | Non | `true` ou `false` | recreate<br/>truncate |
| Taille du lot | Spécifiez le nombre de lignes écrites dans chaque lot. Les plus grandes tailles de lot améliorent la compression et l’optimisation de la mémoire, mais risquent de lever des exceptions de type mémoire insuffisante lors de la mise en cache des données. | Non | Integer | batchSize |
| Pré et post-scripts SQL | Spécifiez des scripts SQL multilignes qui s’exécutent avant (prétraitement) et après (post-traitement) l’écriture de données dans votre base de données de réception. | Non | String | preSQLs<br>postSQLs |

#### <a name="azure-database-for-postgresql-sink-script-example"></a>Exemple de script de récepteur Azure Database pour PostgreSQL

Quand vous utilisez Azure Database pour PostgreSQL comme type de récepteur, le script de flux de données associé est le suivant :

```
IncomingStream sink(allowSchemaDrift: true,
    validateSchema: false,
    deletable:false,
    insertable:true,
    updateable:true,
    upsertable:true,
    keys:['keyColumn'],
    format: 'table',
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> AzurePostgreSQLSink
```

## <a name="lookup-activity-properties"></a>Propriétés de l’activité Lookup

Pour plus d’informations sur les propriétés, consultez [Activité de recherche](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Étapes suivantes
Consultez les [magasins de données pris en charge](copy-activity-overview.md#supported-data-stores-and-formats) pour obtenir la liste des sources et magasins de données pris en charge en tant que récepteurs par l’activité de copie.
