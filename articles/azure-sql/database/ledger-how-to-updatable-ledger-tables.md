---
title: Créer et utiliser des tables de registre pouvant être mises à jour
description: Découvrez comment créer et utiliser des tables de registre pouvant être mises à jour dans Azure SQL Database.
ms.date: 09/09/2021
ms.service: sql-database
ms.subservice: security
ms.reviewer: vanto
ms.topic: how-to
author: rothja
ms.author: jroth
ms.openlocfilehash: 39d7373fc3106501588d98c2b2e0177e92dd6176
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132060564"
---
# <a name="create-and-use-updatable-ledger-tables"></a>Créer et utiliser des tables de registre pouvant être mises à jour

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Le Registre Azure SQL Database est actuellement en préversion publique.

Cet article explique comment créer une [table de registre pouvant être mise à jour](ledger-updatable-ledger-tables.md) dans Azure SQL Database. Ensuite, vous insérerez des valeurs dans votre table de registre pouvant être mise à jour, puis mettrez les données à jour. Enfin, vous visualiserez les résultats en utilisant la vue du registre. Nous utiliserons l’exemple d’une application bancaire qui suit le solde des comptes de ses clients. Notre exemple vous donne un aperçu pratique de la relation entre la table de registre pouvant être mise à jour et la table d’historique correspondante et la vue du registre correspondantes.

## <a name="prerequisites"></a>Prérequis

- Azure SQL Database avec registre activé. Si vous n’avez pas encore créé de base de données dans Azure SQL Database, consultez [Démarrage rapide : Créer une base de données dans Azure SQL Database avec le registre activé](ledger-create-a-single-database-with-ledger-enabled.md).
- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) ou [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio).

## <a name="create-an-updatable-ledger-table"></a>Créer une table de registre pouvant être mise à jour

Nous allons créer une table de solde de compte avec le schéma suivant.

| Nom de la colonne | Type de données      | Description                         |
| ----------- | -------------- | ----------------------------------- |
| CustomerID  | int            | ID client - Clé primaire en cluster |
| LastName    | varchar (50)   | Nom du client                  |
| FirstName   | varchar (50)   | Prénom du client                 |
| Balance     | decimal (10,2) | Solde du compte                     |

> [!IMPORTANT]
> La création de tables de registre pouvant être mises à jour nécessite l’autorisation **ENABLE LEDGER**. Pour plus d’informations sur les autorisations associées aux tables du registre, consultez [Autorisations](/sql/relational-databases/security/permissions-database-engine#asdbpermissions). 

1. Utilisez [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) ou [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio) pour créer un schéma et une table appelés `[Account].[Balance]`.

   ```sql
   CREATE SCHEMA [Account]
   GO
   
   CREATE TABLE [Account].[Balance]
   (
       [CustomerID] INT NOT NULL PRIMARY KEY CLUSTERED,
       [LastName] VARCHAR (50) NOT NULL,
       [FirstName] VARCHAR (50) NOT NULL,
       [Balance] DECIMAL (10,2) NOT NULL
   )
   WITH 
   (
    SYSTEM_VERSIONING = ON,
    LEDGER = ON
   );
   GO
   ```

    > [!NOTE]
    > La spécification de l’argument `LEDGER = ON` est facultative si vous avez activé une base de données du registre lorsque vous avez créé votre base de données dans SQL Database.
    >
    > Dans l’exemple précédent, le système génère les noms des colonnes [GENERATED ALWAYS](/sql/t-sql/statements/create-table-transact-sql#generate-always-columns) dans la table, le nom de la [vue du registre](ledger-updatable-ledger-tables.md#ledger-view) et les noms des [colonnes de la vue du registre](ledger-updatable-ledger-tables.md#ledger-view-schema).
    >
    > Les noms des colonnes de la vue du registre peuvent être personnalisés quand vous créez la table à l’aide du paramètre `<ledger_view_option>` avec l’instruction [CREATE TABLE (Transact-SQL)](/sql/t-sql/statements/create-table-transact-sql?view=azuresqldb-current&preserve-view=true). Les colonnes `GENERATED ALWAYS` et le nom de la [table d’historique](ledger-updatable-ledger-tables.md#history-table), peuvent être personnalisés. Pour plus d’informations, consultez [Options de la vue de registre](/sql/t-sql/statements/create-table-transact-sql?view=azuresqldb-current&preserve-view=true#ledger-view-options) et les exemples correspondants dans [CREATE TABLE (Transact-SQL)](/sql/t-sql/statements/create-table-transact-sql?view=azuresqldb-current&preserve-view=true##x-creating-a-updatable-ledger-table).

1. Lors de la création de la [table de registre pouvant être mise à jour](ledger-updatable-ledger-tables.md), la table d’historique et la vue du registre correspondantes sont également créées. Exécutez les commandes T-SQL suivantes pour afficher la nouvelle table et la nouvelle vue.

   ```sql
   SELECT 
   ts.[name] + '.' + t.[name] AS [ledger_table_name]
   , hs.[name] + '.' + h.[name] AS [history_table_name]
   , vs.[name] + '.' + v.[name] AS [ledger_view_name]
   FROM sys.tables AS t
   JOIN sys.tables AS h ON (h.[object_id] = t.[history_table_id])
   JOIN sys.views v ON (v.[object_id] = t.[ledger_view_id])
   JOIN sys.schemas ts ON (ts.[schema_id] = t.[schema_id])
   JOIN sys.schemas hs ON (hs.[schema_id] = h.[schema_id])
   JOIN sys.schemas vs ON (vs.[schema_id] = v.[schema_id])
   ```

   :::image type="content" source="media/ledger/ledger-updatable-how-to-new-tables.png" alt-text="Capture d’écran illustrant l’interrogation de nouvelles tables du registre.":::

1. Insérez le nom de `Nick Jones` en tant que nouveau client avec un solde d’ouverture de 50 USD.

   ```sql
   INSERT INTO [Account].[Balance]
   VALUES (1, 'Jones', 'Nick', 50)
   ```

1. Insérez les noms de `John Smith`, `Joe Smith` et `Mary Michaels` en tant que nouveaux clients avec des soldes d’ouverture de 500 USD, 30 USD et 200 USD respectivement.

   ```sql
   INSERT INTO [Account].[Balance]
   VALUES (2, 'Smith', 'John', 500),
   (3, 'Smith', 'Joe', 30),
   (4, 'Michaels', 'Mary', 200)
   ```

1. Affichez la table de registre pouvant être mise à jour `[Account].[Balance]` et spécifiez les colonnes [GENERATED ALWAYS](/sql/t-sql/statements/create-table-transact-sql#generate-always-columns) ajoutées à la table.

   ```sql
   SELECT * 
         ,[ledger_start_transaction_id]
         ,[ledger_end_transaction_id]
         ,[ledger_start_sequence_number]
         ,[ledger_end_sequence_number]
   FROM [Account].[Balance] 
   ```

   Dans la fenêtre des résultats, vous verrez d’abord les valeurs insérées par vos commandes T-SQL, ainsi que les métadonnées système utilisées pour la traçabilité des données.

   - La colonne `ledger_start_transaction_id` indique l’ID de transaction unique associé à la transaction qui a inséré les données. Comme `John`, `Joe` et `Mary` ont été insérés lors de la même transaction, ils partagent le même ID de transaction.
   - La colonne `ledger_start_sequence_number` indique l’ordre dans lequel les valeurs ont été insérées par la transaction.

      :::image type="content" source="media/ledger/sql-updatable-how-to-1.png" alt-text="Capture d’écran illustrant l’exemple de table du registre no 1.":::

1. Mettez à jour le solde de `Nick` de `50` à `100`.

   ```sql
   UPDATE [Account].[Balance] SET [Balance] = 100
   WHERE [CustomerID] = 1
   ```

1. Copiez le nom unique de votre table d’historique. Vous aurez besoin de ces informations à l’étape suivante.

   ```sql
   SELECT 
   ts.[name] + '.' + t.[name] AS [ledger_table_name]
   , hs.[name] + '.' + h.[name] AS [history_table_name]
   , vs.[name] + '.' + v.[name] AS [ledger_view_name]
   FROM sys.tables AS t
   JOIN sys.tables AS h ON (h.[object_id] = t.[history_table_id])
   JOIN sys.views v ON (v.[object_id] = t.[ledger_view_id])
   JOIN sys.schemas ts ON (ts.[schema_id] = t.[schema_id])
   JOIN sys.schemas hs ON (hs.[schema_id] = h.[schema_id])
   JOIN sys.schemas vs ON (vs.[schema_id] = v.[schema_id])
   ```

   :::image type="content" source="media/ledger/sql-updatable-how-to-2.png" alt-text="Capture d’écran illustrant l’exemple de table du registre no 2.":::

1. Affichez la table de registre pouvant être mise à jour `[Account].[Balance]`, ainsi que la table d’historique et la vue du registre correspondantes.

   > [!IMPORTANT]
   > Remplacez `<history_table_name>` par le nom que vous avez copié à l’étape précédente.

   ```sql
   SELECT * 
         ,[ledger_start_transaction_id]
         ,[ledger_end_transaction_id]
         ,[ledger_start_sequence_number]
         ,[ledger_end_sequence_number]
   FROM [Account].[Balance] 
   GO
   
   SELECT * FROM [<Your unique history table name>]
   GO 
   
   SELECT * FROM Account.Balance_Ledger
   ORDER BY ledger_transaction_id
   GO
   ```

   > [!TIP]
   > Nous vous recommandons d’interroger l’historique des modifications à l’aide de la [vue du registre](ledger-updatable-ledger-tables.md#ledger-view) et non de la [table d’historique](ledger-updatable-ledger-tables.md#history-table).

1. Le solde du compte de `Nick` a été mis à jour avec succès dans la table de registre pouvant être mise à jour avec la valeur `100`.
1. La table d’historique affiche maintenant le solde précédent de `50` pour `Nick`.
1. La vue du registre indique que la mise à jour de la table du registre est une opération `DELETE` sur la ligne d’origine contenant `50`. Le solde avec l’opération `INSERT` correspondante d’une nouvelle ligne contenant `100` montre le nouveau solde de `Nick`.

   :::image type="content" source="media/ledger/sql-updatable-how-to-3.png" alt-text="Capture d’écran illustrant l’exemple de table du registre no 3.":::


## <a name="next-steps"></a>Étapes suivantes

- [Registre de base de données](ledger-database-ledger.md)
- [Gestion des synthèses et vérification de base de données](ledger-digest-management-and-database-verification.md) 
- [Tables de registre pouvant être mises à jour](ledger-updatable-ledger-tables.md)
- [Tables de registre avec ajout uniquement](ledger-append-only-ledger-tables.md) 
- [Créer et utiliser des tables de registre d’ajout uniquement](ledger-how-to-append-only-ledger-tables.md) 
- [Accéder aux synthèses stockées dans Registre confidentiel Azure](ledger-how-to-access-acl-digest.md)
- [Vérifier une table du registre pour détecter une falsification](ledger-verify-database.md)
