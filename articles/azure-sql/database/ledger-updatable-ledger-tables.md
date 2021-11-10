---
title: Tables de registre Azure SQL Database pouvant être mises à jour
description: Cet article fournit des informations sur les tables de registre pouvant être mises à jour, sur le schéma du registre et sur les vues du registre dans Azure SQL Database.
ms.date: 09/09/2021
ms.service: sql-database
ms.subservice: security
ms.reviewer: vanto
ms.topic: conceptual
author: rothja
ms.author: jroth
ms.openlocfilehash: 3010ba83f8d45083213f3725626f92b111fb839d
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132056187"
---
# <a name="azure-sql-database-updatable-ledger-tables"></a>Tables de registre Azure SQL Database pouvant être mises à jour

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Le Registre Azure SQL Database est actuellement en préversion publique.

Les tables de registre pouvant être mises à jour sont les tables versionnées par le système sur lesquelles les utilisateurs peuvent effectuer des mises à jour et des suppressions tout en fournissant également des fonctionnalités de preuve de falsification. Lorsque des mises à jour ou des suppressions se produisent, toutes les versions antérieures d’une ligne sont conservées dans une table secondaire, appelée table d’historique. La table d’historique reflète le schéma de la table de registre pouvant être mise à jour. Lorsqu’une ligne est mise à jour, la dernière version de la ligne reste dans la table de registre, tandis que sa version antérieure est insérée dans la table d’historique par le système, de manière transparente pour l’application. 

:::image type="content" source="media/ledger/ledger-table-architecture.png" alt-text="Diagramme illustrant l’architecture de table de registre.":::

## <a name="updatable-ledger-tables-vs-temporal-tables"></a>Tables de registre pouvant être mises à jour et tables temporelles

Les tables de registre pouvant être mises à jour et les [tables temporelles](/sql/relational-databases/tables/temporal-tables) sont des tables avec version système, pour lesquelles le moteur de base de données capture les versions de ligne historiques dans les tables d’historique secondaires. Les deux technologies offrent des avantages uniques. Les tables de registre pouvant être mises à jour permettent de vérifier la falsification des données actuelles et historiques. Les tables temporelles prennent en charge l’interrogation des données stockées à tout moment plutôt qu’uniquement les données correctes au moment actuel.

Vous pouvez utiliser les deux technologies conjointement en créant des tables qui sont à la fois des tables de registre et des tables temporelles pouvant être mises à jour. Vous pouvez créer une table de registre pouvant être mise à jour de deux manières :

- Lorsque vous créez une nouvelle base de données dans le portail Azure, en sélectionnant **Activer le registre sur toutes les futures tables de cette base de données** pendant la configuration du registre, ou en spécifiant l’argument `LEDGER = ON` dans votre instruction [CREATE DATABASE (Transact-SQL)](/sql/t-sql/statements/create-database-transact-sql). Cette action crée une base de données de registre et garantit que toutes les futures tables créées dans votre base de données sont des tables de registre pouvant être mises à jour par défaut.
- Lorsque vous créez une table dans une base de données où le registre n’est pas activé au niveau de la base de données, en spécifiant l’argument `LEDGER = ON` dans votre instruction [CREATE TABLE (Transact-SQL)](/sql/t-sql/statements/create-table-transact-sql).

Pour plus d’informations sur les options disponibles lors de la spécification de l’argument `LEDGER` dans votre instruction T-SQL, consultez [CREATE TABLE (Transact-SQL)](/sql/t-sql/statements/create-table-transact-sql).

> [!IMPORTANT]
> Une fois créée, une table de registre ne peut pas être convertie en table qui n’est pas une table de registre. En conséquence, un attaquant ne pourra pas temporairement supprimer les fonctionnalités sur une table de registre, effectuer des changements, puis réactiver les fonctionnalités de registre. 

### <a name="updatable-ledger-table-schema"></a>Schéma de la table de registre pouvant être mise à jour

Une table de registre pouvant être mise à jour doit avoir les colonnes [GENERATED ALWAYS](/sql/t-sql/statements/create-table-transact-sql#generate-always-columns) suivantes qui contiennent des métadonnées indiquant les transactions qui ont modifié la table et l’ordre des opérations par lesquelles les lignes ont été mises à jour par la transaction. Ces données sont utiles pour comprendre comment les données ont été insérées au fil du temps.

> [!NOTE]
> Si vous ne spécifiez pas les colonnes `GENERATED ALWAYS` requises de la table de registre et de la table d’historique de registre dans l’instruction [CREATE TABLE (Transact-SQL)](/sql/t-sql/statements/create-table-transact-sql?view=azuresqldb-current&preserve-view=true), le système ajoute automatiquement les colonnes et utilise les noms par défaut suivants. Pour plus d’informations, consultez les exemples dans [Création d’une table de registre pouvant être mise à jour](/sql/t-sql/statements/create-table-transact-sql?view=azuresqldb-current&preserve-view=true#x-creating-a-updatable-ledger-table).

| Nom de colonne par défaut | Type de données | Description |
| --- | --- | --- |
| ledger_start_transaction_id | bigint | ID de la transaction qui a créé une version de ligne |
| ledger_end_transaction_id | bigint | ID de la transaction qui a supprimé une version de ligne |
| ledger_start_sequence_number | bigint | Numéro de séquence d’une opération au sein d’une transaction qui a créé une version de ligne |
| ledger_end_sequence_number | bigint | Numéro de séquence d’une opération au sein d’une transaction qui a supprimé une version de ligne |

## <a name="history-table"></a>Table d’historique

La table d’historique est créée automatiquement lors de la création d’une table de registre pouvant être mise à jour. La table d’historique capture les valeurs historiques des lignes modifiées en raison des mises à jour et des suppressions dans la table de registre pouvant être mise à jour. Le schéma de la table d’historique reflète celui de la table de registre pouvant être mise à jour à laquelle il est associé.

Lorsque vous créez une table de registre pouvant être mise à jour, vous pouvez spécifier le nom du schéma qui doit contenir votre table d’historique et le nom de la table d’historique, ou faire en sorte que le système génère le nom de la table d’historique et l’ajoute au même schéma que la table de registre. Les tables d’historique avec des noms générés par le système sont appelées des tables d’historique anonymes. La convention d’affectation de noms pour une table d’historique anonyme est `<schema>`.`<updatableledgertablename>`.MSSQL_LedgerHistoryFor_`<GUID>`.

## <a name="ledger-view"></a>Vue du registre

Pour chaque table de registre pouvant être mise à jour, le système génère automatiquement une vue, appelée vue de registre. La vue du registre est une jointure de la table de registre pouvant être mise à jour et de la table d’historique associée. La vue du registre signale toutes les modifications de ligne qui se sont produites dans la table de registre pouvant être mise à jour en joignant les données d’historique dans la table d’historique. Cette vue permet aux utilisateurs, à leurs partenaires ou aux auditeurs d’analyser toutes les opérations historiques et de détecter les falsifications potentielles. Chaque opération de ligne est accompagnée de l’ID de la transaction d’action, ainsi qu’une indication sur la nature de l’opération, `DELETE` ou `INSERT`. Les utilisateurs peuvent récupérer davantage d’informations sur le moment où la transaction a été exécutée et sur l’identité de l’utilisateur qui l’a exécutée, et les mettre en corrélation avec d’autres opérations effectuées par cette transaction.

Par exemple, si vous souhaitez effectuer le suivi de l’historique des transactions pour un scénario d’opérations bancaires, la vue du registre fournit une chronique des transactions dans le temps. Avec la vue de registre, vous n’êtes pas obligé d’afficher indépendamment la table de registre et les tables d’historique pouvant être mises à jour, ni de créer votre propre vue.

Pour obtenir un exemple d’utilisation de la vue du registre, consultez [Créer et utiliser des tables de registre pouvant être mises à jour](ledger-how-to-updatable-ledger-tables.md).

Le schéma de la vue du registre reflète les colonnes définies dans la table de registre pouvant être mise à jour et d’historique, mais les colonnes [GENERATED ALWAYS](/sql/t-sql/statements/create-table-transact-sql#generate-always-columns) sont différentes de celles des tables de registre pouvant être mises à jour et d’historique.

### <a name="ledger-view-schema"></a>Schéma de vue du registre

> [!NOTE]
> Les noms des colonnes de la vue de registre peuvent être personnalisés quand vous créez la table à l’aide du paramètre `<ledger_view_option>` avec l’instruction [CREATE TABLE (Transact-SQL)](/sql/t-sql/statements/create-table-transact-sql?view=azuresqldb-current&preserve-view=true). Pour plus d’informations, consultez [Options de la vue de registre](/sql/t-sql/statements/create-table-transact-sql?view=azuresqldb-current&preserve-view=true#ledger-view-options) et les exemples correspondants dans [CREATE TABLE (Transact-SQL)](/sql/t-sql/statements/create-table-transact-sql?view=azuresqldb-current&preserve-view=true).

| Nom de colonne par défaut | Type de données | Description |
| --- | --- | --- |
| ledger_transaction_id | bigint | ID de la transaction qui a créé ou supprimé une version de ligne. |
| ledger_sequence_number | bigint | Numéro de séquence d’une opération au niveau des lignes dans la transaction sur la table. |
| ledger_operation_type_id | TINYINT | Contient `0` (**INSERT**) ou `1` (**DELETE**). L’insertion d’une ligne dans la table de registre produit une nouvelle ligne dans la vue de registre qui contient `0` dans cette colonne. La suppression d’une ligne de la table de registre produit une nouvelle ligne dans la vue de registre qui contient `1` dans cette colonne. La mise à jour d’une ligne dans la table de registre produit deux nouvelles lignes dans la vue de registre. Une ligne qui contient `1` (**DELETE**) et une autre ligne qui contient `1` (**INSERT**) dans cette colonne. |
| ledger_operation_type_desc | nvarchar(128) | Contient `INSERT` ou `DELETE`. Pour plus d’informations, consultez la ligne précédente. |

## <a name="next-steps"></a>Étapes suivantes
 
- [Créer et utiliser des tables de registre pouvant être mises à jour](ledger-how-to-updatable-ledger-tables.md)
- [Créer et utiliser des tables de registre d’ajout uniquement](ledger-how-to-append-only-ledger-tables.md) 
