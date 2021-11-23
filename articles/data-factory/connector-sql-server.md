---
title: Copier et transformer des données vers et depuis SQL Server
titleSuffix: Azure Data Factory & Azure Synapse
description: Apprenez à copier et à transformer des données vers et depuis une base de données SQL Server locale ou située dans une machine virtuelle Azure à l'aide de pipelines Azure Data Factory ou Azure Synapse Analytics.
ms.author: jianleishen
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/29/2021
ms.openlocfilehash: 575f6ff04e2e3ea006ec700e43205bce2d42c7a4
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132319545"
---
# <a name="copy-and-transform-data-to-and-from-sql-server-by-using-azure-data-factory-or-azure-synapse-analytics"></a>Copier et transformer des données vers et depuis SQL Server à l'aide d'Azure Data Factory ou d’Azure Synapse Analytics

> [!div class="op_single_selector" title1="Sélectionnez la version Azure Data Factory que vous utilisez :"]
> * [Version 1](v1/data-factory-sqlserver-connector.md)
> * [Version actuelle](connector-sql-server.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Cet article explique comment utiliser l'activité Copy dans les pipelines Azure Data Factory et Azure Synapse pour copier des données vers et depuis une base de données SQL Server, et utiliser Data Flow pour transformer les données dans la base de données SQL Server.  Pour en savoir plus, lisez l’article d’introduction pour [Azure Data Factory](introduction.md) ou [Azure Synapse Analytics](../synapse-analytics/overview-what-is.md).

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Ce connecteur SQL Server est pris en charge pour les activités suivantes :

- [Activité Copy](copy-activity-overview.md) avec [prise en charge de la matrice source/du récepteur](copy-activity-overview.md)
- [Mappage de flux de données](concepts-data-flow-overview.md)
- [Activité de recherche](control-flow-lookup-activity.md)
- [Activité GetMetadata](control-flow-get-metadata-activity.md)

Vous pouvez copier des données d’une base de données SQL Server vers tout magasin de données récepteur pris en charge. Vous pouvez aussi copier des données à partir de tout magasin de données source pris en charge vers une base de données SQL Server. Pour obtenir la liste des banques de données prises en charge en tant que sources ou récepteurs par l’activité de copie, consultez le tableau [banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).

Plus précisément, ce connecteur SQL Server prend en charge :

- SQL Server version 2005 et versions ultérieures.
- La copie des données à l'aide de l'authentification SQL ou Windows
- En tant que source, la récupération de données à l’aide d’une requête SQL ou d’une procédure stockée. Vous pouvez également choisir de copier en parallèle à partir de la source SQL Server. Pour plus d’informations, consultez la section [Copier en parallèle à partir de la base de données SQL](#parallel-copy-from-sql-database).
- En tant que récepteur, la création automatique de la table de destination si elle n’existe pas, en fonction du schéma source, l’ajout de données à une table ou l’appel d’une procédure stockée avec une logique personnalisée pendant la copie. 

[SQL Server Express LocalDB](/sql/database-engine/configure-windows/sql-server-express-localdb) n’est pas pris en charge.


## <a name="prerequisites"></a>Prérequis

[!INCLUDE [data-factory-v2-integration-runtime-requirements](includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Bien démarrer

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-a-sql-server-linked-service-using-ui"></a>Créer un service lié SQL Server à l’aide de l’interface utilisateur

Utilisez les étapes suivantes pour créer un service lié SQL Server dans l’interface utilisateur du portail Azure.

1. Accédez à l’onglet Gérer dans votre espace de travail Azure Data Factory ou Synapse et sélectionnez Services liés, puis cliquez sur Nouveau :

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory).

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="Capture d’écran montrant la création d’un service lié avec l’interface utilisateur Azure Data Factory.":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="Capture d’écran de la création d’un nouveau service lié avec l’interface utilisateur Azure Synapse.":::

2. Recherchez SQL et sélectionnez le connecteur SQL Server.

    :::image type="content" source="media/connector-sql-server/sql-server-connector.png" alt-text="Capture d’écran du connecteur SQL Server.":::    

1. Configurez les informations du service, testez la connexion et créez le nouveau service lié.

    :::image type="content" source="media/connector-sql-server/configure-sql-server-linked-service.png" alt-text="Capture d’écran de la configuration d’un service lié SQL Server.":::

## <a name="connector-configuration-details"></a>Informations de configuration des connecteurs

Les sections suivantes fournissent des informations sur les propriétés utilisées pour définir des entités de pipelines Data Factory et Synapse propres au connecteur de base de données SQL Server.

## <a name="linked-service-properties"></a>Propriétés du service lié

Les propriétés suivantes sont prises en charge pour le service lié SQL Server :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type doit être définie sur **SqlServer**. | Oui |
| connectionString |Spécifiez les informations **connectionString** nécessaires pour établir une connexion à la base de données SQL Server à l’aide de l’authentification SQL ou de l’authentification Windows. Consultez les exemples suivants.<br/>Vous pouvez également placer un mot de passe dans Azure Key Vault. En cas d’authentification SQL, extrayez la configuration `password` de la chaîne de connexion. Pour plus d’informations, consultez l’exemple JSON figurant après le tableau et l’article [Stocker des informations d’identification dans Azure Key Vault](store-credentials-in-key-vault.md). |Oui |
| userName |Spécifiez un nom d’utilisateur si vous utilisez l’authentification Windows. Exemple : **domainname\\username**. |Non |
| mot de passe |Spécifiez un mot de passe pour le compte d’utilisateur que vous avez spécifié pour le nom d’utilisateur. Marquez ce champ comme **SecureString** pour le stocker en toute sécurité. Vous pouvez également [référencer un secret stocké dans Azure Key Vault](store-credentials-in-key-vault.md). |Non |
| alwaysEncryptedSettings | Spécifiez les informations **alwaysencryptedsettings** nécessaires pour permettre à Always Encrypted de protéger les données sensibles stockées dans SQL Server à l’aide d’une identité managée ou d’un principal de service. Pour plus d’informations, consultez l’exemple JSON figurant après le tableau et la section [Utilisation d’Always Encrypted](#using-always-encrypted). S’il n’est pas spécifié, le paramètre par défaut Always Encrypted est désactivé. |Non |
| connectVia | Ce [runtime d'intégration](concepts-integration-runtime.md) permet de se connecter au magasin de données. Pour plus d’informations, consultez la section [Conditions préalables](#prerequisites). À défaut de spécification, l’Azure Integration Runtime par défaut est utilisé. |Non |

> [!NOTE]
> - SQL Server [**Always Encrypted**](/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-ver15&preserve-view=true) n'est actuellement pas pris en charge dans le flux de données. 
> - L’authentification Windows n’est pas prise en charge dans le flux de données.

>[!TIP]
>Si vous rencontrez une erreur avec le code d’erreur « UserErrorFailedToConnectToSqlServer » et un message tel que « La limite de session de la base de données XXX a été atteinte », ajoutez `Pooling=false` à votre chaîne de connexion, puis réessayez.

**Exemple 1 : Utilisation de l'authentification SQL**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemple 2 : Utiliser l’authentification SQL avec un mot de passe dans Azure Key Vault**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;",
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemple 3 : Utilisation de l'authentification Windows**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=True;",
            "userName": "<domain\\username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemple 4 : utiliser Always Encrypted**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;"
        },
        "alwaysEncryptedSettings": {
            "alwaysEncryptedAkvAuthType": "ServicePrincipal",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriétés du jeu de données

Pour obtenir la liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article sur les [jeux de données](concepts-datasets-linked-services.md). Cette section fournit une liste des propriétés prises en charge par le jeu de données SQL Server.

Pour copier des données depuis et vers la base de données SQL Server, les propriétés suivantes sont prises en charge :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type du jeu de données doit être définie sur **SqlServerTable**. | Oui |
| schéma | Nom du schéma. |Non pour Source, Oui pour Récepteur  |
| table | Nom de la table/vue. |Non pour Source, Oui pour Récepteur  |
| tableName | Nom de la table/vue avec schéma. Cette propriété est prise en charge pour la compatibilité descendante. Pour les nouvelles charges de travail, utilisez `schema` et `table`. | Non pour Source, Oui pour Récepteur |

**Exemple**

```json
{
    "name": "SQLServerDataset",
    "properties":
    {
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "<SQL Server linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "schema": "<schema_name>",
            "table": "<table_name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l'article [Pipelines](concepts-pipelines-activities.md). Cette section fournit une liste de propriétés prises en charge par la source et le récepteur SQL Server.

### <a name="sql-server-as-a-source"></a>SQL Server en tant que source

>[!TIP]
>Pour savoir comment charger efficacement des données à partir de SQL Server à l’aide du partitionnement, consultez [Copier en parallèle à partir de SQL Server](#parallel-copy-from-sql-database).

Pour copier des données à partir de SQL Server, définissez **SqlSource** comme type source dans l’activité de copie. Les propriétés prises en charge dans la section source de l'activité de copie sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type de la source de l'activité de copie doit être définie sur **SqlSource**. | Oui |
| sqlReaderQuery |Utiliser la requête SQL personnalisée pour lire les données. par exemple `select * from MyTable`. |Non |
| sqlReaderStoredProcedureName |Cette propriété est le nom de la procédure stockée qui lit les données dans la table source. La dernière instruction SQL doit être une instruction SELECT dans la procédure stockée. |Non |
| storedProcedureParameters |Ces paramètres concernent la procédure stockée.<br/>Les valeurs autorisées sont des paires de noms ou de valeurs. Les noms et la casse des paramètres doivent correspondre aux noms et à la casse des paramètres de procédure stockée. |Non |
| isolationLevel | Spécifie le comportement de verrouillage des transactions pour la source SQL. Les valeurs autorisées sont les suivantes : **ReadCommitted**, **ReadUncommitted**, **RepeatableRead**, **Serializable**, **Snapshot**. S’il n’est pas spécifié, le niveau d’isolation par défaut de la base de données est utilisé. Pour plus d’informations, consultez [ce document](/dotnet/api/system.data.isolationlevel). | Non |
| partitionOptions | Spécifie les options de partitionnement des données utilisées pour charger des données à partir de SQL Server. <br>Les valeurs autorisées sont les suivantes : **None** (valeur par défaut), **PhysicalPartitionsOfTable** et **DynamicRange**.<br>Lorsqu’une option de partition est activée (donc, autre que `None`), le degré de parallélisme pour charger simultanément des données à partir de SQL Server est contrôlé par le paramètre [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) sur l’activité de copie. | Non |
| partitionSettings | Spécifiez le groupe de paramètres pour le partitionnement des données. <br>S’applique lorsque l’option de partitionnement n’est pas `None`. | Non |
| ***Sous `partitionSettings`:*** | | |
| partitionColumnName | Spécifiez le nom de la colonne source **en type entier ou date/DateHeure** (`int`, `smallint`, `bigint`, `date`, `smalldatetime`, `datetime`, `datetime2` ou `datetimeoffset`) qu’utilisera le partitionnement par plages de valeurs pour la copie en parallèle. S’il n’est pas spécifié, l’index ou la clé primaire de la table seront automatiquement détectés et utilisés en tant que colonne de partition.<br>S’applique lorsque l’option de partitionnement est `DynamicRange`. Si vous utilisez une requête pour récupérer des données sources, utilisez `?AdfDynamicRangePartitionCondition ` dans la clause WHERE. Pour obtenir un exemple, consultez la section [Copier en parallèle à partir de la base de données SQL](#parallel-copy-from-sql-database). | Non |
| partitionUpperBound | Valeur maximale de la colonne de partition pour le fractionnement de la plage de partition. Cette valeur est utilisée pour décider du stride de la partition, et non pour filtrer les lignes de la table. Toutes les lignes de la table ou du résultat de la requête seront partitionnées et copiées. Si la valeur n’est pas spécifiée, l’activité de copie la détecte automatiquement.  <br>S’applique lorsque l’option de partitionnement est `DynamicRange`. Pour obtenir un exemple, consultez la section [Copier en parallèle à partir de la base de données SQL](#parallel-copy-from-sql-database). | Non |
| partitionLowerBound | Valeur minimale de la colonne de partition pour le fractionnement de la plage de partition. Cette valeur est utilisée pour décider du stride de la partition, et non pour filtrer les lignes de la table. Toutes les lignes de la table ou du résultat de la requête seront partitionnées et copiées. Si la valeur n’est pas spécifiée, l’activité de copie la détecte automatiquement.<br>S’applique lorsque l’option de partitionnement est `DynamicRange`. Pour obtenir un exemple, consultez la section [Copier en parallèle à partir de la base de données SQL](#parallel-copy-from-sql-database). | Non |

**Notez les points suivants :**

- Si **sqlReaderQuery** est spécifié pour **SqlSource**, l’activité de copie exécute cette requête sur la source SQL Server pour obtenir les données. Vous pouvez également spécifier une procédure stockée en spécifiant **sqlReaderStoredProcedureName** et **storedProcedureParameters** si la procédure stockée accepte des paramètres.
- Lorsque vous utilisez une procédure stockée dans la source pour récupérer des données, sachez que si votre procédure stockée est conçue pour renvoyer un schéma différent quand une valeur de paramètre différente est entrée, vous risquez d’échouer ou d’obtenir un résultat inattendu lors de l’importation d’un schéma à partir de l’interface utilisateur ou lors de la copie de données dans la base de données SQL avec création de table automatique.

**Exemple : Utiliser une requête SQL**

```json
"activities":[
    {
        "name": "CopyFromSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SQL Server input dataset name>",
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
                "type": "SqlSource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**Exemple : Utilisation d'une procédure stockée**

```json
"activities":[
    {
        "name": "CopyFromSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SQL Server input dataset name>",
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
                "type": "SqlSource",
                "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
                "storedProcedureParameters": {
                    "stringData": { "value": "str3" },
                    "identifier": { "value": "$$Text.Format('{0:yyyy}', <datetime parameter>)", "type": "Int"}
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**Définition de la procédure stockée**

```sql
CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
(
    @stringData varchar(20),
    @identifier int
)
AS
SET NOCOUNT ON;
BEGIN
    select *
    from dbo.UnitTestSrcTable
    where dbo.UnitTestSrcTable.stringData != stringData
    and dbo.UnitTestSrcTable.identifier != identifier
END
GO
```

### <a name="sql-server-as-a-sink"></a>SQL Server en tant que récepteur

> [!TIP]
> Pour en savoir plus sur les bonnes pratiques, les configurations et les comportements d’écriture pris en charge, consultez l’article [Bonnes pratiques de chargement de données dans SQL Server](#best-practice-for-loading-data-into-sql-server).

Pour copier des données vers SQL Server, définissez **SqlSink** comme type de récepteur dans l’activité de copie. Les propriétés prises en charge dans la section récepteur de l'activité de copie sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type du récepteur de l'activité de copie doit être définie sur **SqlSink**. | Oui |
| preCopyScript |Cette propriété spécifie une requête SQL que l’activité de copie doit exécuter avant l’écriture de données dans SQL Server. Elle n'est appelée qu'une seule fois par copie. Vous pouvez utiliser cette propriété pour nettoyer des données préchargées. |Non |
| tableOption | Spécifie si [la table du récepteur doit être créée automatiquement](copy-activity-overview.md#auto-create-sink-tables) si elle n’existe pas en fonction du schéma source. La création automatique de la table n’est pas prise en charge quand le récepteur spécifie une procédure stockée. Les valeurs autorisées sont `none` (par défaut) et `autoCreate`. |Non |
| sqlWriterStoredProcedureName | Nom de la procédure stockée qui définit comment appliquer des données sources dans une table cible. <br/>Cette procédure stockée est *appelée par lot*. Pour les opérations qui ne s’exécutent qu’une seule fois et qui n’ont rien à voir avec les données sources (par exemple, supprimer ou tronquer), utilisez la propriété `preCopyScript`.<br>Voir l’exemple dans la section [Appel d’une procédure stockée à partir d’un récepteur SQL](#invoke-a-stored-procedure-from-a-sql-sink). | Non |
| storedProcedureTableTypeParameterName |Nom du paramètre du type de table spécifié dans la procédure stockée.  |Non |
| sqlWriterTableType |Nom du type de table à utiliser dans la procédure stockée. L'activité de copie rend les données déplacées disponibles dans une table temporaire avec ce type de table. Le code de procédure stockée peut ensuite fusionner les données copiées avec les données existantes. |Non |
| storedProcedureParameters |Paramètres de la procédure stockée.<br/>Les valeurs autorisées sont des paires de noms et de valeurs. Les noms et la casse des paramètres doivent correspondre aux noms et à la casse des paramètres de la procédure stockée. | Non |
| writeBatchSize |Nombre de lignes à insérer dans la table SQL *par lot*.<br/>Les valeurs autorisées sont des entiers pour le nombre de lignes. Par défaut, le service détermine de façon dynamique la taille de lot appropriée selon la taille de ligne. |Non |
| writeBatchTimeout |Cette propriété spécifie le délai d'attente avant expiration de l'opération d'insertion de lot.<br/>Les valeurs autorisées sont celles qui expriment un intervalle de temps. Exemple : « 00:30:00 » pour 30 minutes. Si aucune valeur n’est spécifiée, le délai d’expiration est par défaut « 02:00:00 ». |Non |
| maxConcurrentConnections |La limite supérieure de connexions simultanées établies au magasin de données pendant l’exécution de l’activité. Spécifiez une valeur uniquement lorsque vous souhaitez limiter les connexions simultanées.| Non |

**Exemple 1 : Ajout de données**

```json
"activities":[
    {
        "name": "CopyToSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<SQL Server output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlSink",
                "tableOption": "autoCreate",
                "writeBatchSize": 100000
            }
        }
    }
]
```

**Exemple 2 : Appeler une procédure stockée pendant la copie**

Pour en savoir plus, consultez [Appel d'une procédure stockée à partir d'un récepteur SQL](#invoke-a-stored-procedure-from-a-sql-sink).

```json
"activities":[
    {
        "name": "CopyToSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<SQL Server output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlSink",
                "sqlWriterStoredProcedureName": "CopyTestStoredProcedureWithParameters",
                "storedProcedureTableTypeParameterName": "MyTable",
                "sqlWriterTableType": "MyTableType",
                "storedProcedureParameters": {
                    "identifier": { "value": "1", "type": "Int" },
                    "stringData": { "value": "str1" }
                }
            }
        }
    }
]
```

## <a name="parallel-copy-from-sql-database"></a>Copier en parallèle à partir de la base de données SQL

Le connecteur SQL Server dans l’activité de copie propose un partitionnement de données intégré pour copier des données en parallèle. Vous trouverez des options de partitionnement de données dans l’onglet **Source** de l’activité de copie.

:::image type="content" source="./media/connector-sql-server/connector-sql-partition-options.png" alt-text="Capture d’écran représentant les options de partition":::

Lorsque vous activez la copie partitionnée, l’activité de copie exécute des requêtes en parallèle sur votre source SQL Server pour charger des données par partitions. Le degré de parallélisme est contrôlé via le paramètre [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) sur l’activité de copie. Par exemple, si vous définissez `parallelCopies` sur la valeur quatre, le service génère et exécute simultanément quatre requêtes selon l’option de partition et les paramètres que vous avez spécifiés, chacune récupérant une partie des données de votre serveur SQL.

Il vous est recommandé d’activer la copie en parallèle avec partitionnement des données notamment lorsque vous chargez une grande quantité de données à partir de votre serveur SQL. Voici quelques suggestions de configurations pour différents scénarios. Lors de la copie de données dans un magasin de données basé sur un fichier, il est recommandé d’écrire les données dans un dossier sous la forme de plusieurs fichiers (spécifiez uniquement le nom du dossier). Les performances seront meilleures qu’avec l’écriture de données dans un seul fichier.

| Scénario                                                     | Paramètres suggérés                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Chargement complet à partir d’une table volumineuse, avec des partitions physiques.        | **Option de partition** : Partitions physiques de la table. <br><br/>Pendant l’exécution, le service détecte automatiquement les partitions physiques et copie les données par partition. <br><br/>Pour vérifier si votre table possède, ou non, une partition physique, vous pouvez vous reporter à [cette requête](#sample-query-to-check-physical-partition). |
| Chargement complet d’une table volumineuse, sans partitions physiques, avec une colonne d’entiers ou DateHeure pour le partitionnement des données. | **Options de partition** : Partition dynamique par spécification de plages de valeurs.<br>**Colonne de partition** (facultatif) : Spécifiez la colonne utilisée pour partitionner les données. Si la valeur n’est pas spécifiée, la colonne de la clé primaire est utilisée.<br/>**Limite supérieure de partition** et **limite inférieure de partition** (facultatif) : Spécifiez si vous souhaitez déterminer le stride de la partition. Cela ne permet pas de filtrer les lignes de la table ; toutes les lignes de la table sont partitionnées et copiées. S’il n’est pas spécifié, l’activité Copy détecte automatiquement les valeurs et peut prendre beaucoup de temps en fonction des valeurs MIN et MAX. Il est recommandé de fournir une limite supérieure et une limite inférieure. <br><br>Par exemple, si les valeurs de la colonne de partition « ID » sont comprises entre 1 et 100, et que vous définissez la limite inférieure à 20 et la limite supérieure à 80, avec la copie parallèle sur 4, le service récupère des données par 4 partitions, les ID dans la plage <=20, [21, 50], [51, 80] et >=81, respectivement. |
| Chargement d’une grande quantité de données à l’aide d’une requête personnalisée, sans partitions physiques, et avec une colonne d’entiers ou de date/DateHeure pour le partitionnement des données. | **Options de partition** : Partition dynamique par spécification de plages de valeurs.<br>**Requête**: `SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`.<br>**Colonne de partition** : Spécifiez la colonne utilisée pour partitionner les données.<br>**Limite supérieure de partition** et **limite inférieure de partition** (facultatif) : Spécifiez si vous souhaitez déterminer le stride de la partition. Cela ne permet pas de filtrer les lignes de la table ; toutes les lignes du résultat de la requête sont partitionnées et copiées. Si la valeur n’est pas spécifiée, l’activité de copie la détecte automatiquement.<br><br>Lors de l’exécution, le service remplace `?AdfRangePartitionColumnName` par le nom réel de la colonne et les plages de valeurs de chaque partition et les envoie à SQL Server. <br>Par exemple, si les valeurs de la colonne de partition « ID » sont comprises entre 1 et 100, et que vous définissez la limite inférieure à 20 et la limite supérieure à 80, avec la copie parallèle sur 4, le service récupère des données par 4 partitions, les ID dans la plage <=20, [21, 50], [51, 80] et >=81, respectivement. <br><br>Voici d’autres exemples de requêtes pour différents scénarios :<br> 1. Interroger l’ensemble de la table : <br>`SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition`<br> 2. Interroger une table avec une sélection de colonnes et des filtres de la clause WHERE supplémentaires : <br>`SELECT <column_list> FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`<br> 3. Effectuer une requête avec des sous-requêtes : <br>`SELECT <column_list> FROM (<your_sub_query>) AS T WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`<br> 4. Effectuer une requête avec une partition dans une sous-requête : <br>`SELECT <column_list> FROM (SELECT <your_sub_query_column_list> FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition) AS T`
|

Meilleures pratiques pour charger des données avec l’option de partition :

1. Choisissez une colonne distinctive comme colonne de partition (p. ex. : clé primaire ou clé unique) pour éviter l’asymétrie des données. 
2. Si la table possède une partition intégrée, utilisez l’option de partition « Partitions physiques de la table » pour obtenir de meilleures performances.    
3. Si vous utilisez Azure Integration Runtime pour copier des données, vous pouvez définir des « [unités d’intégration de données (DIU)](copy-activity-performance-features.md#data-integration-units) » plus grandes (>4) pour utiliser davantage de ressources de calcul. Vérifiez les scénarios applicables ici.
4. Le « [degré de parallélisme de copie](copy-activity-performance-features.md#parallel-copy) » contrôle le nombre de partitions : un nombre trop élevé nuit parfois aux performances. Il est recommandé de définir ce nombre selon (DIU ou nombre de nœuds d'IR auto-hébergé) * (2 à 4).

**Exemple : chargement complet à partir d’une table volumineuse, avec des partitions physiques**

```json
"source": {
    "type": "SqlSource",
    "partitionOption": "PhysicalPartitionsOfTable"
}
```

**Exemple : requête avec partition dynamique par spécification de plages de valeurs**

```json
"source": {
    "type": "SqlSource",
    "query": "SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column (optional) to decide the partition stride, not as data filter>",
        "partitionLowerBound": "<lower_value_of_partition_column (optional) to decide the partition stride, not as data filter>"
    }
}
```

### <a name="sample-query-to-check-physical-partition"></a>Exemple de requête pour vérifier une partition physique

```sql
SELECT DISTINCT s.name AS SchemaName, t.name AS TableName, pf.name AS PartitionFunctionName, c.name AS ColumnName, iif(pf.name is null, 'no', 'yes') AS HasPartition
FROM sys.tables AS t
LEFT JOIN sys.objects AS o ON t.object_id = o.object_id
LEFT JOIN sys.schemas AS s ON o.schema_id = s.schema_id
LEFT JOIN sys.indexes AS i ON t.object_id = i.object_id 
LEFT JOIN sys.index_columns AS ic ON ic.partition_ordinal > 0 AND ic.index_id = i.index_id AND ic.object_id = t.object_id 
LEFT JOIN sys.columns AS c ON c.object_id = ic.object_id AND c.column_id = ic.column_id 
LEFT JOIN sys.partition_schemes ps ON i.data_space_id = ps.data_space_id 
LEFT JOIN sys.partition_functions pf ON pf.function_id = ps.function_id 
WHERE s.name='[your schema]' AND t.name = '[your table name]'
```

Si la table a une partition physique, vous voyez « HasPartition » avec la valeur « yes » (oui) comme suit.

:::image type="content" source="./media/connector-azure-sql-database/sql-query-result.png" alt-text="Résultat d’une requête SQL":::

## <a name="best-practice-for-loading-data-into-sql-server"></a>Bonnes pratiques de chargement de données dans SQL Server

Lors de la copie de données dans SQL Server, vous pouvez exiger un comportement d’écriture différent :

- [Append](#append-data): Mes données sources contiennent uniquement de nouveaux enregistrements.
- [Upsert](#upsert-data) : Mes données sources contiennent à la fois des insertions et des mises à jour.
- [Remplacer](#overwrite-the-entire-table) : Je veux recharger la totalité de la table de dimension à chaque fois.
- [Écrire avec une logique personnalisée](#write-data-with-custom-logic) : J’ai besoin d’un traitement supplémentaire avant l’insertion finale dans la table de destination.

Consultez les sections correspondantes pour savoir comment effectuer des configurations et connaître les bonnes pratiques associées.

### <a name="append-data"></a>Ajout de données

L’ajout de données est le comportement par défaut de ce connecteur de récepteur SQL Server. Le service effectue une insertion en bloc pour écrire efficacement dans votre table. Vous pouvez configurer la source et le récepteur en conséquence dans l’activité de copie.

### <a name="upsert-data"></a>Effectuer un upsert de données

**Option 1 :** Lorsque vous avez une grande quantité de données à copier, vous pouvez charger en masse tous les enregistrements dans une table de mise en lots à l’aide de l’activité de copie, puis exécuter une activité de procédure stockée pour appliquer une instruction [MERGE](/sql/t-sql/statements/merge-transact-sql) ou INSERT/UPDATE en une seule fois. 

Actuellement, l’activité de copie ne prend pas en charge en mode natif le chargement des données dans une table temporaire de base de données. Il existe une méthode avancée pour le mettre en place en combinant plusieurs activités. Pour en savoir plus à ce sujet, consultez [Optimize Azure SQL Database Bulk Upsert scenarios](https://github.com/scoriani/azuresqlbulkupsert) (Optimisation des scénarios d’upsert en masse Azure SQL Database). Vous trouverez ci-dessous un exemple d’utilisation d’une table permanente comme mise en lots.

Par exemple, vous pouvez créer un pipeline avec une **activité Copy** chaînée avec une **activité de procédure stockée**. La première activité copie des données de votre banque source vers une table de mise en lots SQL Server, par exemple **UpsertStagingTable**, comme nom de table dans le jeu de données. La seconde activité appelle ensuite une procédure stockée pour fusionner les données sources de la table de mise en lots dans la table cible et nettoyer la table de mise en lots.

:::image type="content" source="./media/connector-azure-sql-database/azure-sql-database-upsert.png" alt-text="Upsert":::

Dans votre base de données, définissez une procédure stockée avec la logique MERGE, comme dans l’exemple suivant, qui est pointée à partir de l’activité de procédure stockée précédente. Supposons que la cible est la table **Marketing** comportant trois colonnes : **ProfileID**, **State** et **Category**. Effectuez l’opération d’upsert sur la colonne **ProfileID**.

```sql
CREATE PROCEDURE [dbo].[spMergeData]
AS
BEGIN
    MERGE TargetTable AS target
    USING UpsertStagingTable AS source
    ON (target.[ProfileID] = source.[ProfileID])
    WHEN MATCHED THEN
        UPDATE SET State = source.State
    WHEN NOT matched THEN
        INSERT ([ProfileID], [State], [Category])
      VALUES (source.ProfileID, source.State, source.Category);
    
    TRUNCATE TABLE UpsertStagingTable
END
```

**Option 2 :** Vous pouvez choisir d’[appeler une procédure stockée dans l’activité de copie](#invoke-a-stored-procedure-from-a-sql-sink). Cette approche exécute chaque lot (tel que gouverné par la propriété `writeBatchSize`) de la table source au lieu d’utiliser l’insertion en bloc comme approche par défaut dans l’activité de copie.

### <a name="overwrite-the-entire-table"></a>Remplacer l’intégralité de la table

Vous pouvez configurer la propriété **preCopyScript** dans un récepteur d’activité de copie. Dans le cas présent, pour chaque activité Copy qui s’exécute, le service exécute d’abord le script. Il exécute ensuite la copie pour insérer les données. Par exemple, pour remplacer l’intégralité de la table par les données les plus récentes, spécifiez un script pour d’abord supprimer tous les enregistrements avant de charger en bloc les nouvelles données à partir de la source.

### <a name="write-data-with-custom-logic"></a>Écrire des données avec une logique personnalisée

Les étapes permettant d’écrire des données à l’aide d’une logique personnalisée sont semblables à celles décrites dans la section [Effectuer un upsert de données](#upsert-data). Quand vous devez appliquer un traitement supplémentaire avant l’insertion finale des données sources dans la table de destination, vous pouvez effectuer le chargement vers une table de mise en lots, puis appeler une activité de procédure stockée ou appeler une procédure stockée dans le récepteur de l’activité de copie pour appliquer les données.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a><a name="invoke-a-stored-procedure-from-a-sql-sink"></a> Appel d'une procédure stockée à partir d'un récepteur SQL

Quand vous copiez des données dans une base de données SQL Server, vous pouvez également configurer et appeler une procédure stockée spécifiée par l’utilisateur avec des paramètres supplémentaires sur chaque lot de la table source. La fonction de procédure stockée tire parti des [paramètres table](/dotnet/framework/data/adonet/sql/table-valued-parameters).  Notez que le service encapsule automatiquement la procédure stockée dans sa propre transaction. Par conséquent, toute transaction créée à l’intérieur de la procédure stockée devient une transaction imbriquée, ce qui peut avoir des implications pour la gestion des exceptions.

Vous pouvez utiliser une procédure stockée à la place des mécanismes de copie intégrée. Par exemple, quand vous souhaitez appliquer un traitement supplémentaire avant l’insertion finale de données sources dans la table de destination. Fusionner des colonnes, rechercher des valeurs supplémentaires et effectuer des insertions dans plusieurs tables sont des exemples de traitement supplémentaire.

L’exemple suivant montre comment utiliser une procédure stockée pour effectuer une opération upsert simple dans une table de la base de données SQL Server. Supposons que les données d’entrée et la table réceptrice **Marketing** ont trois colonnes : **ProfileID**, **State** et **Category**. Effectuez l’opération upsert basée sur la colonne **ProfileID** et appliquez-la uniquement à une catégorie spécifique appelée « ProductA ».

1. Dans votre base de données, définissez le type de table avec le même nom que **sqlWriterTableType**. Le schéma du type de table doit être identique au schéma retourné par vos données d'entrée.

    ```sql
    CREATE TYPE [dbo].[MarketingType] AS TABLE(
        [ProfileID] [varchar](256) NOT NULL,
        [State] [varchar](256) NOT NULL,
        [Category] [varchar](256) NOT NULL
    )
    ```

2. Dans votre base de données, définissez la procédure stockée portant le même nom que **sqlWriterStoredProcedureName**. Elle gère les données d’entrée à partir de la source que vous avez spécifiée et les fusionne dans la table de sortie. Le nom de paramètre du type de table de la procédure stockée doit être identique au **tableName** défini dans le jeu de données.

    ```sql
    CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @category varchar(256)
    AS
    BEGIN
    MERGE [dbo].[Marketing] AS target
    USING @Marketing AS source
    ON (target.ProfileID = source.ProfileID and target.Category = @category)
    WHEN MATCHED THEN
        UPDATE SET State = source.State
    WHEN NOT MATCHED THEN
        INSERT (ProfileID, State, Category)
        VALUES (source.ProfileID, source.State, source.Category);
    END
    ```

3. Définissez la section **Récepteur SQL** de l’activité de copie comme suit :

    ```json
    "sink": {
        "type": "SqlSink",
        "sqlWriterStoredProcedureName": "spOverwriteMarketing",
        "storedProcedureTableTypeParameterName": "Marketing",
        "sqlWriterTableType": "MarketingType",
        "storedProcedureParameters": {
            "category": {
                "value": "ProductA"
            }
        }
    }
    ```

## <a name="mapping-data-flow-properties"></a>Propriétés du mappage de flux de données

Lors de la transformation de données dans le flux de données de mappage, vous pouvez lire et écrire dans les tables de la base de données SQL Server. Pour plus d’informations, consultez la [transformation de la source](data-flow-source.md) et la [transformation du récepteur](data-flow-sink.md) dans le flux de données de mappage.

> [!NOTE]
> Pour accéder à une instance SQL Server locale, vous devez utiliser le [réseau virtuel managé](managed-virtual-network-private-endpoint.md) de l’espace de travail Azure Data Factory ou Synapse via un point de terminaison privé. Pour connaître les étapes détaillées, reportez-vous à ce [tutoriel](tutorial-managed-virtual-network-on-premise-sql-server.md).

### <a name="source-transformation"></a>Transformation de la source

Le tableau ci-dessous répertorie les propriétés prises en charge par la source SQL Server. Vous pouvez modifier ces propriétés sous l’onglet **Options de la source**.

| Nom | Description | Obligatoire | Valeurs autorisées | Propriété du script de flux de données |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Table de charge de travail | Si vous sélectionnez Table comme entrée, le flux de données extrait toutes les données de la table spécifiée dans le jeu de données. | Non | - |- |
| Requête | Si vous sélectionnez Requête comme entrée, spécifiez une requête SQL pour extraire des données de la source, qui remplace toute table que vous spécifiez dans le jeu de données. L’utilisation de requêtes est un excellent moyen de réduire le nombre de lignes pour les tests ou les recherches.<br><br>La clause **Order By** n’est pas prise en charge, mais vous pouvez définir une instruction SELECT FROM complète. Vous pouvez également utiliser des fonctions de table définies par l’utilisateur. **select * from udfGetData()** est une fonction UDF dans SQL qui retourne une table que vous pouvez utiliser dans le flux de données.<br>Exemple de requête : `Select * from MyTable where customerId > 1000 and customerId < 2000`.| Non | String | query |
| Taille du lot | Spécifiez la taille de lot que doivent avoir les lectures créées à partir d’un large volume de données. | Non | Integer | batchSize |
| Niveau d’isolation | Choisissez l’un des niveaux d’isolation suivants :<br>– Lecture validée.<br>– Lecture non validée (par défaut).<br>– Lecture renouvelable.<br>– Sérialisable.<br>– Aucun (ignorer le niveau d’isolation). | Non | <small>READ_COMMITTED<br/>READ_UNCOMMITTED<br/>REPEATABLE_READ<br/>SERIALIZABLE<br/>NONE</small> |isolationLevel |

#### <a name="sql-server-source-script-example"></a>Exemple de script source SQL Server

Lorsque vous utilisez le type de source SQL Server, le script de flux de données associé est le suivant :

```
source(allowSchemaDrift: true,
    validateSchema: false,
    isolationLevel: 'READ_UNCOMMITTED',
    query: 'select * from MYTABLE',
    format: 'query') ~> SQLSource
```

### <a name="sink-transformation"></a>Transformation du récepteur

Le tableau ci-dessous répertorie les propriétés prises en charge par le récepteur SQL Server. Vous pouvez modifier ces propriétés sous l’onglet **Options du récepteur**.

| Name | Description | Obligatoire | Valeurs autorisées | Propriété du script de flux de données |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Mettre à jour la méthode | Spécifiez les opérations autorisées sur la destination de votre base de données. Par défaut, seules les insertions sont autorisées.<br>Pour mettre à jour, effectuer un upsert ou supprimer des lignes, une [transformation de modification de ligne](data-flow-alter-row.md) est requise afin de baliser les lignes relatives à ces actions. | Oui | `true` ou `false` | deletable <br/>insertable <br/>updateable <br/>upsertable |
| Colonnes clés | Pour les mises à jour, les opérations upsert et les suppressions, une ou plusieurs colonnes clés doivent être définies afin de déterminer la ligne à modifier.<br>Le nom de colonne que vous choisissez comme clé sera utilisé dans le cadre des opérations suivantes de mise à jour, d’upsert et de suppression. Vous devez donc choisir une colonne qui existe dans le mappage du récepteur. | Non | Array | clés |
| Ignorer l’écriture des colonnes clés | Si vous ne souhaitez pas écrire la valeur dans la colonne clé, sélectionnez « Ignorer l’écriture des colonnes clés ». | Non | `true` ou `false` | skipKeyWrites |
| Action table |Détermine si toutes les lignes de la table de destination doivent être recréées ou supprimées avant l’écriture.<br>- **Aucun** : Aucune action ne sera effectuée sur la table.<br>- **Recréer** : La table sera supprimée et recréée. Obligatoire en cas de création dynamique d’une nouvelle table.<br>- **Tronquer** : Toutes les lignes de la table cible seront supprimées. | Non | `true` ou `false` | recreate<br/>truncate |
| Taille du lot | Spécifiez le nombre de lignes écrites dans chaque lot. Les plus grandes tailles de lot améliorent la compression et l’optimisation de la mémoire, mais risquent de lever des exceptions de type mémoire insuffisante lors de la mise en cache des données. | Non | Integer | batchSize |
| Pré et post-scripts SQL | Spécifiez des scripts SQL multilignes qui s’exécutent avant (prétraitement) et après (post-traitement) l’écriture de données dans votre base de données de réception. | Non | String | preSQLs<br>postSQLs |

#### <a name="sql-server-sink-script-example"></a>Exemples de script de récepteur SQL Server

Lorsque vous utilisez le type de récepteur SQL Server, le script de flux de données associé est le suivant :

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
    skipDuplicateMapOutputs: true) ~> SQLSink
```

## <a name="data-type-mapping-for-sql-server"></a>Mappage de type de données pour SQL Server

Quand vous copiez des données depuis et vers SQL Server, les mappages suivants sont utilisés de types de données SQL Server en types de données intermédiaires Azure Data Factory. Les pipelines Synapse, qui implémentent Data Factory, utilisent les mêmes mappages.  Pour découvrir comment l’activité de copie mappe le schéma et le type de données la source au récepteur, consultez [Mappage de schéma dans l’activité de copie](copy-activity-schema-and-type-mapping.md).

| Type de données SQL Server | Type de données intermédiaires d’Azure Data Factory |
|:--- |:--- |
| bigint |Int64 |
| binary |Byte[] |
| bit |Boolean |
| char |String, Char[] |
| Date |DateTime |
| Datetime |DateTime |
| datetime2 |DateTime |
| Datetimeoffset |DateTimeOffset |
| Decimal |Decimal |
| FILESTREAM attribute (varbinary(max)) |Byte[] |
| Float |Double |
| image |Byte[] |
| int |Int32 |
| money |Decimal |
| NCHAR |String, Char[] |
| ntext |String, Char[] |
| numeric |Decimal |
| NVARCHAR |String, Char[] |
| real |Unique |
| rowversion |Byte[] |
| smalldatetime |DateTime |
| SMALLINT |Int16 |
| SMALLMONEY |Decimal |
| sql_variant |Object |
| text |String, Char[] |
| time |TimeSpan |
| timestamp |Byte[] |
| TINYINT |Int16 |
| UNIQUEIDENTIFIER |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| Xml |String |

>[!NOTE]
> Pour les types de données mappés avec le type intermédiaire Decimal,l’activité de copie prend actuellement en charge une précision maximale de 28. Si vous disposez de données qui nécessitent une précision supérieure à 28, pensez à les convertir en chaîne dans une requête SQL.

## <a name="lookup-activity-properties"></a>Propriétés de l’activité Lookup

Pour en savoir plus sur les propriétés, consultez [Activité Lookup](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Propriétés de l’activité GetMetadata

Pour en savoir plus sur les propriétés, consultez [Activité GetMetadata](control-flow-get-metadata-activity.md). 

## <a name="using-always-encrypted"></a>Utilisation d’Always Encrypted

Quand vous copiez des données depuis/vers SQL Server avec [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine), effectuez les étapes suivantes : 

1. Stockez la valeur [Clé principale de colonne (CMK)](/sql/relational-databases/security/encryption/create-and-store-column-master-keys-always-encrypted?view=sql-server-ver15&preserve-view=true) dans un coffre [Azure Key Vault](../key-vault/general/overview.md). En savoir plus sur la [configuration d’Always Encrypted à l’aide d’Azure Key Vault](../azure-sql/database/always-encrypted-azure-key-vault-configure.md?tabs=azure-powershell)

2. Vérifiez que le coffre de clés où la valeur [Clé principale de colonne (CMK)](/sql/relational-databases/security/encryption/create-and-store-column-master-keys-always-encrypted?view=sql-server-ver15&preserve-view=true) est stockée est totalement accessible. Consultez cet [article](/sql/relational-databases/security/encryption/create-and-store-column-master-keys-always-encrypted?view=sql-server-ver15&preserve-view=true#key-vaults) pour connaître les autorisations requises.

3. Créez un service lié pour vous connecter à votre base de données SQL et activez la fonction « Always Encrypted » en utilisant une identité managée ou un principal de service. 


>[!NOTE]
>SQL Server [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) prend en charge les scénarios suivants : 
>1. Les magasins de données sources ou récepteurs utilisent l’identité managée ou le principal de service comme type d’authentification du fournisseur de clés.
>2. Les magasins de données sources et récepteurs utilisent l’identité managée comme type d’authentification du fournisseur de clés.
>3. Les magasins de données sources et récepteurs utilisent le même principal de service que le type d’authentification du fournisseur de clés.

## <a name="troubleshoot-connection-issues"></a>Résoudre les problèmes de connexion

1. Configurez votre instance SQL Server pour qu’elle accepte les connexions à distance. Démarrez **SQL Server Management Studio**, cliquez avec le bouton droit sur **Serveur**, puis sélectionnez **Propriétés**. Sélectionnez **Connexions** dans la liste, puis cochez la case **Autoriser les accès distants à ce serveur**.

    :::image type="content" source="media/copy-data-to-from-sql-server/AllowRemoteConnections.png" alt-text="Activer des connexions à distance":::

    Pour obtenir des instructions détaillées, consultez [Configurer l’option de configuration du serveur d’accès à distance](/sql/database-engine/configure-windows/configure-the-remote-access-server-configuration-option).

2. Démarrez le **Gestionnaire de configuration SQL Server**. Développez **Configuration du réseau SQL Server** pour l’instance souhaitée, puis sélectionnez **Protocoles pour MSSQLSERVER**. Des protocoles s’affichent dans le volet droit. Activez TCP/IP en cliquant avec le bouton droit sur **TCP/IP**, puis en sélectionnant **Activer**.

    :::image type="content" source="./media/copy-data-to-from-sql-server/EnableTCPProptocol.png" alt-text="Activer TCP/IP":::

    Pour obtenir des informations supplémentaires et découvrir d’autres façons d’activer le protocole TCP/IP, consultez [Activer ou désactiver un protocole réseau de serveur](/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol).

3. Dans la même fenêtre, double-cliquez sur **TCP/IP** pour lancer la fenêtre des **propriétés TCP/IP**.
4. Allez sous l’onglet **Adresses IP** . Faites défiler l’écran vers le bas jusqu’à la section **IPAll**. Notez le **port TCP**. La valeur par défaut est **1433**.
5. Créez une **règle de Pare-feu Windows** sur l’ordinateur pour autoriser le trafic à entrer par ce port. 
6. **Vérifiez la connexion** : Pour vous connecter à SQL Server en utilisant un nom complet, utilisez SQL Server Management Studio à partir d’un autre ordinateur. par exemple `"<machine>.<domain>.corp.<company>.com,1433"`.

## <a name="next-steps"></a>Étapes suivantes
Consultez les [magasins de données pris en charge](copy-activity-overview.md#supported-data-stores-and-formats) pour obtenir la liste des sources et magasins de données pris en charge en tant que récepteurs par l’activité de copie.
