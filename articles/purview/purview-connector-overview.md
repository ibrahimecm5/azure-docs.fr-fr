---
title: Vue d’ensemble du connecteur Purview
description: Cet article décrit les différentes fonctionnalités et banques de données prises en charge dans Purview
author: linda33wj
ms.author: jingwang
ms.service: purview
ms.subservice: purview-data-map
ms.topic: conceptual
ms.date: 11/05/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 59ba1b5bba00eb84d9a4e5dd20cb9f81771097f1
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2021
ms.locfileid: "132026384"
---
# <a name="supported-data-stores"></a>Magasins de données pris en charge

Purview prend en charge les banques de données suivantes. Sélectionnez chaque banque de données pour découvrir le détail des fonctionnalités prises en charge et des configurations correspondantes.

## <a name="purview-data-sources"></a>Sources de données Purview

|**Catégorie**|  **Banque de données**  |**Métadonnées techniques** |**Classification** |**Traçabilité** | **Stratégie d'accès** |
|---|---|---|---|---|---|
| Azure | [Stockage Blob Azure](register-scan-azure-blob-storage-source.md)| [Oui](register-scan-azure-blob-storage-source.md#register) | [Oui](register-scan-azure-blob-storage-source.md#scan)| Limité* | [Oui](how-to-access-policies-storage.md) |
||    [Azure Cosmos DB](register-scan-azure-cosmos-database.md)| [Oui](register-scan-azure-cosmos-database.md#register) | [Oui](register-scan-azure-cosmos-database.md#scan)|Non*|Non|
||    [Explorateur de données Azure](register-scan-azure-data-explorer.md)| [Oui](register-scan-azure-data-explorer.md#register) | [Oui](register-scan-azure-data-explorer.md#scan)| Non* | Non |
||    [Azure Data Lake Storage Gen1](register-scan-adls-gen1.md)| [Oui](register-scan-adls-gen1.md#register) | [Oui](register-scan-adls-gen1.md#scan)| Limité* | Non |
||    [Azure Data Lake Storage Gen2](register-scan-adls-gen2.md)| [Oui](register-scan-adls-gen2.md#register) | [Oui](register-scan-adls-gen2.md#scan)| Limité* | [Oui](how-to-access-policies-storage.md) |
|| [Azure Database pour MySQL](register-scan-azure-mysql-database.md) | [Oui](register-scan-azure-mysql-database.md#register) | [Oui](register-scan-azure-mysql-database.md#scan) | Non* | Non |
|| [Base de données Azure pour PostgreSQL](register-scan-azure-postgresql.md) | [Oui](register-scan-azure-postgresql.md#register) | [Oui](register-scan-azure-postgresql.md#scan) | Non* | Non |
||    [Pool SQL dédié Azure (anciennement SQL DW)](register-scan-azure-synapse-analytics.md)| [Oui](register-scan-azure-synapse-analytics.md#register) | [Oui](register-scan-azure-synapse-analytics.md#scan)| Non* | Non |
||    [Azure Files](register-scan-azure-files-storage-source.md)|[Oui](register-scan-azure-files-storage-source.md#register) | [Oui](register-scan-azure-files-storage-source.md#scan) | Limité* |  Non |
||    [Azure SQL Database](register-scan-azure-sql-database.md)| [Oui](register-scan-azure-sql-database.md#register) |[Oui](register-scan-azure-sql-database.md#scan)| Non* | Non |
||    [Azure SQL Database Managed Instance](register-scan-azure-sql-database-managed-instance.md)|  [Oui](register-scan-azure-sql-database-managed-instance.md#scan) | [Oui](register-scan-azure-sql-database-managed-instance.md#scan) | Non* | Non |
||    [Azure Synapse Analytics (Espace de travail)](register-scan-synapse-workspace.md)| [Oui](register-scan-synapse-workspace.md#register) | [Oui](register-scan-synapse-workspace.md#scan)| [Oui – Pipelines Synapse](how-to-lineage-azure-synapse-analytics.md)| Non|
|Base de données| [Amazon RDS](register-scan-amazon-rds.md) | [Oui](register-scan-amazon-rds.md#register-an-amazon-rds-data-source) | [Oui](register-scan-amazon-rds.md#scan-an-amazon-rds-database) | Non | Non |
||    [Cassandra](register-scan-cassandra-source.md)|[Oui](register-scan-cassandra-source.md#register) | Non | [Oui](how-to-lineage-cassandra.md)| Non|
||    [Google BigQuery](register-scan-google-bigquery-source.md)| [Oui](register-scan-google-bigquery-source.md#register)| Non | [Oui](how-to-lineage-google-bigquery.md)| Non|
|| [Base de données de metastore Hive](register-scan-hive-metastore-source.md) | [Oui](register-scan-hive-metastore-source.md#register) | Non | Oui* | Non|
|| [MySQL](register-scan-mysql.md) | [Oui](register-scan-mysql.md#register) | Non | [Oui](register-scan-mysql.md#scan) | Non |
|| [Oracle](register-scan-oracle-source.md) | [Oui](register-scan-oracle-source.md#register)|  Non | [Oui*](how-to-lineage-oracle.md) | Non|
|| [PostgreSQL](register-scan-postgresql.md) | [Oui](register-scan-postgresql.md#register) | Non | [Oui](register-scan-postgresql.md#scan) | Non |
||    [SQL Server](register-scan-on-premises-sql-server.md)| [Oui](register-scan-on-premises-sql-server.md#register) |[Oui](register-scan-on-premises-sql-server.md#scan) | Non* | Non|
||    [Teradata](register-scan-teradata-source.md)| [Oui](register-scan-teradata-source.md#register)|  Non | [Oui*](how-to-lineage-teradata.md) | Non|
|Fichier|[Amazon S3](register-scan-amazon-s3.md)|[Oui](register-scan-amazon-s3.md)| [Oui](register-scan-amazon-s3.md)| Limité* | Non|
|Services et applications|    [Erwin](register-scan-erwin-source.md)| [Oui](register-scan-erwin-source.md#register)| Non | [Oui](how-to-lineage-erwin.md)| Non|
||    [Looker](register-scan-looker-source.md)| [Oui](register-scan-looker-source.md#register)| Non | [Oui](how-to-lineage-looker.md)| Non|
||    [Power BI](register-scan-power-bi-tenant.md)| [Oui](register-scan-power-bi-tenant.md#register)| Non | [Oui](how-to-lineage-powerbi.md)| Non|
|| [Salesforce](register-scan-salesforce.md) | [Oui](register-scan-salesforce.md#register) | Non | Non | Non |
||    [SAP ECC](register-scan-sapecc-source.md)| [Oui](register-scan-sapecc-source.md#register) | Non | [Oui*](how-to-lineage-sapecc.md) | Non|
|| [SAP S/4HANA pour machines virtuelles Linux sur Azure](register-scan-saps4hana-source.md) | [Oui](register-scan-saps4hana-source.md#register)| Non | [Oui*](how-to-lineage-sapecc.md) | Non|

\* Outre la traçabilité des ressources dans la source de données, la traçabilité est également prise en charge si le jeu de données est utilisé en tant que source/récepteur dans [Data Factory](how-to-link-azure-data-factory.md) ou [Pipeline Synapse](how-to-lineage-azure-synapse-analytics.md).

> [!NOTE]
> Actuellement, Purview ne peut pas analyser une ressource contenant `/`, `\` ou `#` dans son nom. Pour étendre votre analyse et éviter les ressources de scan qui contiennent ces caractères dans le nom de la ressource, utilisez l’exemple dans [Enregistrer et scanner une base de données Azure SQL Database](register-scan-azure-sql-database.md#creating-the-scan).

## <a name="scan-regions"></a>Analyser des régions
Voici une liste de toutes les régions de la source de données Azure (centre de données) sur lesquelles l’analyseur Purview s’exécute. Si votre source de données Azure se trouve dans une région qui ne figure pas dans cette liste, l’analyseur s’exécutera dans la région de votre instance de Purview.

### <a name="purview-scanner-regions"></a>Régions de l’analyseur Purview

- EastUs
- EastUs2 
- SouthCentralUS
- WestUs
- WestUs2
- AsieSudEst
- WestEurope
- NorthEurope
- UkSouth
- AustraliaEast
- CanadaCentral
- BrazilSouth
- CentralIndia
- JaponEst
- SouthAfricaNorth
- FranceCentral
- KoreaCentral
- CentralUS
- NorthCentralUS
- EastAsia
- USACentreOuest
- AustralieSudEst

## <a name="next-steps"></a>Étapes suivantes

- [Inscrire et analyser une source Stockage Blob Azure](register-scan-azure-blob-storage-source.md)