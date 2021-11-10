---
title: Fonctionnalité T-SQL dans le pool Synapse SQL
description: Liste des fonctionnalités Transact-SQL disponibles dans Synapse SQL.
services: synapse analytics
author: jovanpop-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: jovanpop
ms.reviewer: jrasnick
ms.openlocfilehash: 074f6dffae5067676e073916018126c5c2f07d85
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131850562"
---
# <a name="transact-sql-features-supported-in-azure-synapse-sql"></a>Fonctionnalités Transact-SQL prises en charge dans Azure Synapse SQL

Azure Synapse SQL est un service d’analytique Big Data qui vous permet d’interroger et d’analyser vos données à l’aide du langage T-SQL. Vous pouvez utiliser un dialecte standard du langage SQL conforme à la norme ANSI et utilisé dans SQL Server et Azure SQL Database pour l’analyse des données. 

Le langage Transact-SQL est utilisé dans le pool SQL serverless, sachant qu’un modèle dédié peut référencer différents objets et présenter des différences au niveau de l’ensemble des fonctionnalités prises en charge. Dans cette page, vous trouverez les principales différences qui existent entre les modèles de consommation Synapse SQL au niveau du langage Transact-SQL.

## <a name="database-objects"></a>Objets de base de données

Les modèles de consommation dans Synapse SQL permettent d’utiliser des objets de base de données différents. Le tableau suivant compare les types d’objets pris en charge :

|   | Dédié | Sans serveur |
| --- | --- | --- |
| **Tables** | [Oui](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true) | Non, le modèle serverless peut uniquement interroger des données externes placées dans le [stockage Azure](#storage-options). |
| **Views** | [Oui](/sql/t-sql/statements/create-view-transact-sql?view=azure-sqldw-latest&preserve-view=true). Les vues peuvent utiliser les [éléments de langage de requête](#query-language) qui sont disponibles dans le modèle dédié. | [Oui](/sql/t-sql/statements/create-view-transact-sql?view=azure-sqldw-latest&preserve-view=true). Les vues peuvent utiliser les [éléments de langage de requête](#query-language) qui sont disponibles dans le modèle serverless. |
| **Schémas** | [Oui](/sql/t-sql/statements/create-schema-transact-sql?view=azure-sqldw-latest&preserve-view=true) | [Oui](/sql/t-sql/statements/create-schema-transact-sql?view=azure-sqldw-latest&preserve-view=true) |
| **Tables temporaires** | [Oui](../sql-data-warehouse/sql-data-warehouse-tables-temporary.md?context=/azure/synapse-analytics/context/context) | Non |
| **Procédures** | [Oui](/sql/t-sql/statements/create-procedure-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Oui |
| **Fonctions** | [Oui](/sql/t-sql/statements/create-function-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true) | Oui, uniquement les fonctions table inlined. |
| **Déclencheurs** | Non | Non |
| **Tables externes** | [Oui](/sql/t-sql/statements/create-external-table-transact-sql?view=azure-sqldw-latest&preserve-view=true). Voir les [formats de données](#data-formats) pris en charge. | [Oui](/sql/t-sql/statements/create-external-table-transact-sql?view=azure-sqldw-latest&preserve-view=true). Voir les [formats de données](#data-formats) pris en charge. |
| **Mise en cache des requêtes** | Oui, sous plusieurs formes (mise en cache SSD, en mémoire et ResultSet). En outre, la vue matérialisée est prise en charge | Non |
| **Variables de table** | [Non](/sql/t-sql/data-types/table-transact-sql?view=azure-sqldw-latest&preserve-view=true), utilisez des tables temporaires | Non |
| **[Distribution de tables](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?context=/azure/synapse-analytics/context/context)**               | Oui | Non |
| **[Index de table](../sql-data-warehouse/sql-data-warehouse-tables-index.md?context=/azure/synapse-analytics/context/context)**                           | Oui | Non |
| **[Partitions de table](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?context=/azure/synapse-analytics/context/context)**                     | Oui | Non |
| **[Statistiques](develop-tables-statistics.md)**            | Oui | Oui |
| **[Gestion des charges de travail, classes de ressources et contrôle d’accès concurrentiel](../sql-data-warehouse/resource-classes-for-workload-management.md?context=/azure/synapse-analytics/context/context)** | Oui    | Non |
| **Contrôle des coûts** | Oui, à l’aide d’actions de scale-up et de scale-down. | Oui, à l’aide de la [procédure T-SQL ou du portail Azure](./data-processed.md#cost-control). |

## <a name="query-language"></a>Langage de requête

Les fonctionnalités prises en charge des langages de requête utilisés dans Synapse SQL peuvent différer selon le modèle de consommation. Le tableau suivant présente les différences les plus importantes qui existent entre les dialectes Transact-SQL au niveau du langage de requête :

|   | Dédié | Sans serveur |
| --- | --- | --- |
| **Instruction SELECT** | Oui. Les clauses de requête Transact-SQL [FOR XML/FOR JSON](/sql/t-sql/queries/select-for-clause-transact-sql?view=azure-sqldw-latest&preserve-view=true), [MATCH](/sql/t-sql/queries/match-sql-graph?view=azure-sqldw-latest&preserve-view=true) et OFFSET/FETCH ne sont pas prises en charge. | Oui. Les clauses de requête Transact-SQL [FOR XML](/sql/t-sql/queries/select-for-clause-transact-sql?view=azure-sqldw-latest&preserve-view=true), [MATCH](/sql/t-sql/queries/match-sql-graph?view=azure-sqldw-latest&preserve-view=true), [PREDICT](/sql/t-sql/queries/predict-transact-sql?view=azure-sqldw-latest&preserve-view=true), GROUPNG SETS, ainsi que les indicateurs de requête, ne sont pas pris en charge. |
| **Instruction INSERT** | Oui | Non |
| **Instruction UPDATE** | Oui | Non |
| **Instruction DELETE** | Oui | Non |
| **Instruction MERGE** | Oui ([préversion](/sql/t-sql/statements/merge-transact-sql?view=azure-sqldw-latest&preserve-view=true)) | Non |
| **[Transactions](develop-transactions.md)** | Oui | Oui, applicable sur des objets de métadonnées. |
| **[Étiquettes](develop-label.md)** | Oui | Non |
| **Chargement des données** | Oui. L’utilitaire recommandé est l’instruction [COPY](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true). Toutefois, le système prend également en charge le chargement en masse (BCP) et [CETAS](/sql/t-sql/statements/create-external-table-as-select-transact-sql?view=azure-sqldw-latest&preserve-view=true) pour le chargement des données. | Non |
| **Exportation de données** | Oui. Avec [CETAS](/sql/t-sql/statements/create-external-table-as-select-transact-sql?view=azure-sqldw-latest&preserve-view=true). | Oui. Avec [CETAS](/sql/t-sql/statements/create-external-table-as-select-transact-sql?view=azure-sqldw-latest&preserve-view=true). |
| **Types** | Oui, tous les types Transact-SQL, à l’exception de [cursor](/sql/t-sql/data-types/cursor-transact-sql?view=azure-sqldw-latest&preserve-view=true), [hierarchyid](/sql/t-sql/data-types/hierarchyid-data-type-method-reference?view=azure-sqldw-latest&preserve-view=true), [ntext, text et image](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?view=azure-sqldw-latest&preserve-view=true), [rowversion](/sql/t-sql/data-types/rowversion-transact-sql?view=azure-sqldw-latest&preserve-view=true), [spatial](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql?view=azure-sqldw-latest&preserve-view=true), [sql\_variant](/sql/t-sql/data-types/sql-variant-transact-sql?view=azure-sqldw-latest&preserve-view=true) et [xml](/sql/t-sql/xml/xml-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Oui, tous les types Transact-SQL, à l’exception de [cursor](/sql/t-sql/data-types/cursor-transact-sql?view=azure-sqldw-latest&preserve-view=true), [hierarchyid](/sql/t-sql/data-types/hierarchyid-data-type-method-reference?view=azure-sqldw-latest&preserve-view=true), [ntext, text et image](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?view=azure-sqldw-latest&preserve-view=true), [rowversion](/sql/t-sql/data-types/rowversion-transact-sql?view=azure-sqldw-latest&preserve-view=true), [spatial](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql?view=azure-sqldw-latest&preserve-view=true), [sql\_variant](/sql/t-sql/data-types/sql-variant-transact-sql?view=azure-sqldw-latest&preserve-view=true), [xml](/sql/t-sql/xml/xml-transact-sql?view=azure-sqldw-latest&preserve-view=true), ainsi que le type Table |
| **Requêtes entre plusieurs bases de données** | Non | Oui, y compris l’instruction [USE](/sql/t-sql/language-elements/use-transact-sql?view=azure-sqldw-latest&preserve-view=true). |
| **Fonctions intégrées (analyse)** | Oui, toutes les fonctions Transact-SQL de type [analytique](/sql/t-sql/functions/analytic-functions-transact-sql?view=azure-sqldw-latest&preserve-view=true), conversion, [date et heure](/sql/t-sql/functions/date-and-time-data-types-and-functions-transact-sql?view=azure-sqldw-latest&preserve-view=true), logique et [mathématique](/sql/t-sql/functions/mathematical-functions-transact-sql?view=azure-sqldw-latest&preserve-view=true), à l’exception de [CHOOSE](/sql/t-sql/functions/logical-functions-choose-transact-sql?view=azure-sqldw-latest&preserve-view=true) et [PARSE](/sql/t-sql/functions/parse-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Oui, toutes les fonctions Transact-SQL de type [analytique](/sql/t-sql/functions/analytic-functions-transact-sql?view=azure-sqldw-latest&preserve-view=true), conversion, [date et heure](/sql/t-sql/functions/date-and-time-data-types-and-functions-transact-sql?view=azure-sqldw-latest&preserve-view=true), logique et [mathématique](/sql/t-sql/functions/mathematical-functions-transact-sql?view=azure-sqldw-latest&preserve-view=true). |
| **Fonctions intégrées ([chaîne](/sql/t-sql/functions/string-functions-transact-sql))** | Oui. Toutes les fonctions Transact-SQL de type [chaîne](/sql/t-sql/functions/string-functions-transact-sql?view=azure-sqldw-latest&preserve-view=true), [JSON](/sql/t-sql/functions/json-functions-transact-sql?view=azure-sqldw-latest&preserve-view=true) et classement, à l’exception de [STRING_ESCAPE](/sql/t-sql/functions/string-escape-transact-sql?view=azure-sqldw-latest&preserve-view=true) et [TRANSLATE](/sql/t-sql/functions/translate-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Oui. Toutes les fonctions Transact-SQL de type [chaîne](/sql/t-sql/functions/string-functions-transact-sql?view=azure-sqldw-latest&preserve-view=true), [JSON](/sql/t-sql/functions/json-functions-transact-sql?view=azure-sqldw-latest&preserve-view=true) et classement |
| **Fonctions intégrées ([chiffrement](/sql/t-sql/functions/cryptographic-functions-transact-sql))** | Certains | Non |
| **Fonctions table intégrées** | Oui, les [fonctions d’ensemble de lignes Transact-SQL](/sql/t-sql/functions/functions?view=azure-sqldw-latest&preserve-view=true#rowset-functions), sauf [OPENXML](/sql/t-sql/functions/openxml-transact-sql?view=azure-sqldw-latest&preserve-view=true), [OPENDATASOURCE](/sql/t-sql/functions/opendatasource-transact-sql?view=azure-sqldw-latest&preserve-view=true), [OPENQUERY](/sql/t-sql/functions/openquery-transact-sql?view=azure-sqldw-latest&preserve-view=true) et [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Oui, les [fonctions d’ensemble de lignes Transact-SQL](/sql/t-sql/functions/functions?view=azure-sqldw-latest&preserve-view=true#rowset-functions), sauf [OPENXML](/sql/t-sql/functions/openxml-transact-sql?view=azure-sqldw-latest&preserve-view=true), [OPENDATASOURCE](/sql/t-sql/functions/opendatasource-transact-sql?view=azure-sqldw-latest&preserve-view=true) et [OPENQUERY](/sql/t-sql/functions/openquery-transact-sql?view=azure-sqldw-latest&preserve-view=true)  |
| **Agrégats** |  Agrégats Transact-SQL intégrés, sauf [CHECKSUM_AGG](/sql/t-sql/functions/checksum-agg-transact-sql?view=azure-sqldw-latest&preserve-view=true) et [GROUPING_ID](/sql/t-sql/functions/grouping-id-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Agrégats Transact-SQL intégrés. |
| **Opérateurs** | Oui, tous les [opérateurs Transact-SQL](/sql/t-sql/language-elements/operators-transact-sql?view=azure-sqldw-latest&preserve-view=true) sauf [!>](/sql/t-sql/language-elements/not-greater-than-transact-sql?view=azure-sqldw-latest&preserve-view=true) et [!<](/sql/t-sql/language-elements/not-less-than-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Oui, tous les [opérateurs Transact-SQL](/sql/t-sql/language-elements/operators-transact-sql?view=azure-sqldw-latest&preserve-view=true)  |
| **Contrôle du flux** | Oui. Toutes les [instructions de contrôle de flux Transact-SQL](/sql/t-sql/language-elements/control-of-flow?view=azure-sqldw-latest&preserve-view=true), à l’exception de [CONTINUE](/sql/t-sql/language-elements/continue-transact-sql?view=azure-sqldw-latest&preserve-view=true), [GOTO](/sql/t-sql/language-elements/goto-transact-sql?view=azure-sqldw-latest&preserve-view=true), [RETURN](/sql/t-sql/language-elements/return-transact-sql?view=azure-sqldw-latest&preserve-view=true), [USE](/sql/t-sql/language-elements/use-transact-sql?view=azure-sqldw-latest&preserve-view=true) et [WAITFOR](/sql/t-sql/language-elements/waitfor-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Oui. Toutes les [instructions de contrôle de flux Transact-SQL](/sql/t-sql/language-elements/control-of-flow?view=azure-sqldw-latest&preserve-view=true) la requête SELECT dans la condition `WHILE (...)` |
| **Instructions DDL (CREATE, ALTER, DROP)** | Oui. Toutes les instructions DDL Transact-SQL applicables aux types d’objets pris en charge | Oui. Toutes les instructions DDL Transact-SQL applicables aux types d’objets pris en charge |

## <a name="security"></a>Sécurité

Les pools Synapse SQL vous permettent d’utiliser des fonctionnalités de sécurité intégrées pour sécuriser vos données et contrôler l’accès. Le tableau suivant montre les principales différences qui existent entre les différents modèles de consommation Synapse SQL.

|   | Dédié | Sans serveur |
| --- | --- | --- |
| **Connexions** | N/A (seuls les utilisateurs contenus sont pris en charge dans les bases de données) | Oui |
| **Utilisateurs** |  N/A (seuls les utilisateurs contenus sont pris en charge dans les bases de données) | Oui |
| **[Utilisateurs contenus](/sql/relational-databases/security/contained-database-users-making-your-database-portable?view=azure-sqldw-latest&preserve-view=true)** | Oui. **Remarque :** Un seul utilisateur Azure AD peut avoir le rôle d’administrateur non restreint. | Non |
| **Authentification par nom d’utilisateur/mot de passe SQL**| Oui | Oui |
| **Authentification Azure Active Directory (Azure AD)**| Oui, utilisateurs Azure AD | Oui, connexions et utilisateurs Azure AD |
| **Authentification directe Azure Active Directory (Azure AD) pour le stockage** | Oui | Oui |
| **Authentification par jeton SAS pour le stockage** | Non | Oui, avec [DATABASE SCOPED CREDENTIAL](/sql/t-sql/statements/create-database-scoped-credential-transact-sql?view=azure-sqldw-latest&preserve-view=true) dans [EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql?view=azure-sqldw-latest&preserve-view=true) ou avec [CREDENTIAL](/sql/t-sql/statements/create-credential-transact-sql?view=azure-sqldw-latest&preserve-view=true) au niveau de l’instance. |
| **Authentification par clé d’accès de stockage** | Oui, avec [DATABASE SCOPED CREDENTIAL](/sql/t-sql/statements/create-database-scoped-credential-transact-sql?view=azure-sqldw-latest&preserve-view=true) dans [EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Non |
| **Authentification par [identité managée](../../data-factory/data-factory-service-identity.md?context=/azure/synapse-analytics/context/context&tabs=synapse-analytics) pour le stockage** | Oui, avec des [informations d’identification Managed Service Identity](../../azure-sql/database/vnet-service-endpoint-rule-overview.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&preserve-view=true&toc=%2fazure%2fsynapse-analytics%2ftoc.json&view=azure-sqldw-latest&preserve-view=true) | Oui, avec des informations d’identification `Managed Identity`. |
| **Authentification avec l’identité de l’application de stockage** | [Oui](/sql/t-sql/statements/create-external-data-source-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Non |
| **Rôles de niveau serveur** | Non | Oui, sysadmin, public et d’autres rôles de serveur sont pris en charge |
| **SERVER SCOPED CREDENTIAL** | Non | Oui |
| **Autorisations - [Au niveau du serveur](/sql/relational-databases/security/authentication-access/server-level-roles)** | Non | Oui |
| **Rôles inclus dans l’étendue de la base de données** | Oui | Oui |
| **DATABASE SCOPED CREDENTIAL** | Oui | Oui |
| **Autorisations - [Au niveau de la base de données](/sql/relational-databases/security/authentication-access/database-level-roles?view=azure-sqldw-latest&preserve-view=true)** | Oui | Oui |
| **Autorisations - Au niveau du schéma** | Oui, y compris la possibilité d’accorder (GRANT), de refuser (DENY) et de révoquer (REVOKE) des autorisations pour les utilisateurs/connexions au niveau du schéma | Oui, y compris la possibilité d’accorder (GRANT), de refuser (DENY) et de révoquer (REVOKE) des autorisations pour les utilisateurs/connexions au niveau du schéma |
| **Autorisations - Au niveau objet** | Oui, y compris la possibilité d’accorder (GRANT), de refuser (DENY) et de révoquer (REVOKE) des autorisations pour les utilisateurs | Oui, y compris la possibilité d’accorder (GRANT), de refuser (DENY) et de révoquer (REVOKE) des autorisations pour les utilisateurs/connexions au niveau des objets système pris en charge |
| **Autorisations - [Sécurité au niveau des colonnes](../sql-data-warehouse/column-level-security.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)** | Oui | Oui |
| **Fonctions d’identité et de sécurité** | Certaines fonctions et certains opérateurs de sécurité Transact-SQL : `CURRENT_USER`, `HAS_DBACCESS`, `IS_MEMBER`, `IS_ROLEMEMBER`, `SESSION_USER`, `SUSER_NAME`, `SUSER_SNAME`, `SYSTEM_USER`, `USER`, `USER_NAME`, `EXECUTE AS` `OPEN/CLOSE MASTER KEY` | Certaines fonctions et certains opérateurs de sécurité Transact-SQL : `CURRENT_USER`, `HAS_DBACCESS`, `HAS_PERMS_BY_NAME`, `IS_MEMBER', 'IS_ROLEMEMBER`, `IS_SRVROLEMEMBER`, `SESSION_USER`, `SESSION_CONTEXT`, `SUSER_NAME`, `SUSER_SNAME`, `SYSTEM_USER`, `USER`, `USER_NAME`, `EXECUTE AS` et `REVERT`. Les fonctions de sécurité ne peuvent pas être utilisées pour interroger des données externes (stockez le résultat dans une variable qui peut être utilisée dans la requête).  |
| **Sécurité au niveau des lignes** | [Oui](/sql/relational-databases/security/row-level-security?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Non |
| **Chiffrement transparent des données (TDE)** | [Oui](../../azure-sql/database/transparent-data-encryption-tde-overview.md) | Non | 
| **Découverte et classification des données** | [Oui](../../azure-sql/database/data-discovery-and-classification-overview.md) | Non |
| **Évaluation des vulnérabilités** | [Oui](../../azure-sql/database/sql-vulnerability-assessment.md) | Non |
| **Protection avancée contre les menaces** | [Oui](../../azure-sql/database/threat-detection-overview.md)
| **Audit** | [Oui](../../azure-sql/database/auditing-overview.md) | Non |
| **[Règles de pare-feu](../security/synapse-workspace-ip-firewall.md)**| Oui | Oui |
| **[Point de terminaison privé](../security/synapse-workspace-managed-private-endpoints.md)**| Oui | Oui |

Le pool SQL dédie et le pool SQL serverless utilisent le langage Transact-SQL standard pour interroger les données. Pour plus d’informations sur ces différences, consultez la [documentation de référence sur le langage Transact-SQL](/sql/t-sql/language-reference).

## <a name="tools"></a>Outils

Vous pouvez utiliser divers outils pour vous connecter à Synapse SQL en vue d’interroger les données.

|   | Dédié | Sans serveur |
| --- | --- | --- |
| **Synapse Studio** | Oui, les scripts SQL | Oui, les scripts SQL |
| **Power BI** | Oui | [Oui](tutorial-connect-power-bi-desktop.md) |
| **Azure Analysis Services** | Oui | Oui |
| **Azure Data Studio** | Oui | Oui, version 1.18.0 ou supérieure. Les scripts SQL et les notebooks SQL sont pris en charge. |
| **SQL Server Management Studio** | Oui | Oui, version 18.5 ou supérieure |

> [!NOTE]
> Vous pouvez utiliser SSMS pour vous connecter au pool SQL serverless et interroger. Il est partiellement pris en charge à partir de la version 18.5 ; vous pouvez l’utiliser pour vous connecter et interroger uniquement.

La plupart des applications utilisent le langage Transact-SQL standard pour interroger les modèles de consommation dédiés et serverless de Synapse SQL.

## <a name="storage-options"></a>Options de stockage

Les données qui sont analysées peuvent être stockées sur différents types de stockage. Le tableau suivant liste toutes les options de stockage disponibles :

|   | Dédié | Sans serveur |
| --- | --- | --- |
| **Stockage interne** | Oui | Non |
| **Azure Data Lake v2** | Oui | Oui |
| **Stockage Blob Azure** | Oui | Oui |
| **Azure SQL (distant)** | Non | Non |
| **Stockage transactionnel Azure CosmosDB** | Non | Non |
| **Stockage analytique Azure CosmosDB** | Non | Oui, avec [Synapse Link](../../cosmos-db/synapse-link.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) |
| **Tables Apache Spark (dans un espace de travail)** | Non | Tables PARQUET uniquement à l’aide de la [synchronisation de métadonnées](develop-storage-files-spark-tables.md) |
| **Tables Apache Spark (distantes)** | Non | Non |
| **Tables Databricks (distantes)** | Non | Non |

## <a name="data-formats"></a>Formats de données

Les données qui sont analysées peuvent être stockées dans différents formats de stockage. Le tableau suivant liste tous les formats de données qui peuvent être analysés :

|   | Dédié | Sans serveur |
| --- | --- | --- |
| **Delimited** | [Oui](/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest&preserve-view=true) | [Oui](query-single-csv-file.md) |
| **CSV** | Oui, (les délimiteurs multicaractères ne sont pas pris en charge) | [Oui](query-single-csv-file.md) |
| **Parquet** | [Oui](/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest&preserve-view=true) | [Oui](query-parquet-files.md), y compris les fichiers comprenant des [types imbriqués](query-parquet-nested-types.md) |
| **Hive ORC** | [Oui](/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Non |
| **Hive RC** | [Oui](/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Non |
| **JSON** | Oui | [Oui](query-json-files.md) |
| **Avro** | Non | Non |
| **[Delta-lake](https://delta.io/)** | Non | [Oui](query-delta-lake-format.md) |
| **[CDM](/common-data-model/)** | Non | Non |

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur les bonnes pratiques concernant les pools SQL dédiés et les pools SQL serverless, consultez les articles suivants :

- [Bonnes pratiques pour les pools SQL dédiés](best-practices-dedicated-sql-pool.md)
- [Bonnes pratiques pour les pools SQL serverless](best-practices-serverless-sql-pool.md)
