---
title: Tables de registre en ajout seul dans Azure SQL Database
description: Cet article fournit des informations sur le schéma et les vues de table de registre en ajout seul dans Azure SQL Database.
ms.date: 09/09/2021
ms.service: sql-database
ms.subservice: security
ms.reviewer: vanto
ms.topic: conceptual
author: rothja
ms.author: jroth
ms.openlocfilehash: 595b42bb924d67a5db3a21d44c670dbcd25c61d7
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132055792"
---
# <a name="azure-sql-database-append-only-ledger-tables"></a>Tables de registre en ajout seul dans Azure SQL Database

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Le Registre Azure SQL Database est actuellement en préversion publique.

Les tables de registre en ajout seul autorisent seulement les opérations `INSERT` sur vos tables, ce qui garantit que les utilisateurs privilégiés, par exemple les administrateurs de base de données, ne peuvent pas modifier les données à l’aide d’opérations de [langage de manipulation de données](/sql/t-sql/queries/queries) traditionnelles. Les tables de registre en ajout seul sont idéales pour les systèmes qui ne mettent pas à jour ou ne suppriment pas les enregistrements, par exemple les systèmes d’informations de sécurité et gestion d’événements ou les systèmes blockchain dans lesquels les données doivent être répliquées à partir du blockchain sur une base de données. Comme aucune opération `UPDATE` ou `DELETE` n’est effectuée sur une table en ajout seul, vous n’avez pas besoin de la table d’historique correspondante, contrairement aux [tables de registre pouvant être mises à jour](ledger-updatable-ledger-tables.md).

:::image type="content" source="media/ledger/ledger-table-architecture-append-only.png" alt-text="Diagramme qui montre l’architecture des tables de registre.":::

Vous pouvez créer une table de registre en ajout seul en spécifiant l’argument `LEDGER = ON` dans votre instruction [CREATE TABLE (Transact-SQL)](/sql/t-sql/statements/create-table-transact-sql) avec l’option `APPEND_ONLY = ON`.

> [!IMPORTANT]
> Une fois qu’une table a été créée comme table de registre, elle ne peut pas être redéfinie comme table sans fonctionnalités de registre. En conséquence, un attaquant ne pourra pas temporairement supprimer les fonctionnalités de registre, effectuer des changements, puis réactiver les fonctionnalités de registre.

### <a name="append-only-ledger-table-schema"></a>Schéma de la table de registre d’ajout uniquement

Une table d’ajout uniquement doit avoir les colonnes [GENERATED ALWAYS](/sql/t-sql/statements/create-table-transact-sql#generate-always-columns) suivantes qui contiennent des métadonnées indiquant les transactions qui ont modifié la table et l’ordre des opérations par lesquelles les lignes ont été mises à jour par la transaction. Quand vous créez une table de registre en ajout seul, des colonnes `GENERATED ALWAYS` sont créées dans votre table de registre. Ces données sont utiles pour comprendre comment les données ont été insérées au fil du temps.

Si vous ne spécifiez pas les définitions des colonnes `GENERATED ALWAYS` dans l’instruction [CREATE TABLE](/sql/t-sql/statements/create-table-transact-sql), le système les ajoute automatiquement, en utilisant les noms par défaut suivants.

| Nom de colonne par défaut | Type de données | Description |
|--|--|--|
| ledger_start_transaction_id | bigint | ID de la transaction qui a créé une version de ligne |
| ledger_start_sequence_number | bigint | Numéro de séquence d’une opération au sein d’une transaction qui a créé une version de ligne |

## <a name="ledger-view"></a>Vue de registre

Pour chaque table de registre d’ajout uniquement, le système génère automatiquement une vue, appelée vue de registre. La vue de registre indique toutes les insertions de ligne qui se sont produites sur la table. La vue de registre est principalement utile pour les [tables de registre pouvant être mises à jour](ledger-updatable-ledger-tables.md), plutôt que pour les tables de registre en ajout seul, car ces dernières n’ont pas de fonctionnalités `UPDATE` ou `DELETE`. La vue de registre des tables de registre d’ajout uniquement est disponible pour assurer la cohérence entre les tables de registre d’ajout uniquement et les tables pouvant être mises à jour.

### <a name="ledger-view-schema"></a>Schéma de la vue de registre

> [!NOTE]
> Les noms des colonnes de la vue de registre peuvent être personnalisés quand vous créez la table à l’aide du paramètre `<ledger_view_option>` avec l’instruction [CREATE TABLE (Transact-SQL)](/sql/t-sql/statements/create-table-transact-sql?view=azuresqldb-current&preserve-view=true). Pour plus d’informations, consultez [Options de la vue de registre](/sql/t-sql/statements/create-table-transact-sql?view=azuresqldb-current&preserve-view=true#ledger-view-options) et les exemples correspondants dans [CREATE TABLE (Transact-SQL)](/sql/t-sql/statements/create-table-transact-sql?view=azuresqldb-current&preserve-view=true).

| Nom de colonne par défaut | Type de données | Description |
| --- | --- | --- |
| ledger_transaction_id | bigint | ID de la transaction qui a créé ou supprimé une version de ligne. |
| ledger_sequence_number | bigint | Numéro de séquence d’une opération au niveau des lignes dans la transaction sur la table. |
| ledger_operation_type_id | TINYINT | Contient `0` (**INSERT**) ou `1` (**DELETE**). L’insertion d’une ligne dans la table de registre produit une nouvelle ligne dans la vue de registre qui contient `0` dans cette colonne. La suppression d’une ligne de la table de registre produit une nouvelle ligne dans la vue de registre qui contient `1` dans cette colonne. La mise à jour d’une ligne dans la table de registre produit deux nouvelles lignes dans la vue de registre. Une ligne qui contient `1` (**DELETE**) et une autre ligne qui contient `1` (**INSERT**) dans cette colonne. Une opération DELETE ne doit pas avoir lieu sur une table de registre en ajout seul. |
| ledger_operation_type_desc | nvarchar(128) | Contient `INSERT` ou `DELETE`. Pour plus d’informations, consultez la ligne précédente. |

## <a name="next-steps"></a>Étapes suivantes

- [Créer et utiliser des tables de registre d’ajout uniquement](ledger-how-to-append-only-ledger-tables.md)
- [Créer et utiliser des tables de registre pouvant être mises à jour](ledger-how-to-updatable-ledger-tables.md)
