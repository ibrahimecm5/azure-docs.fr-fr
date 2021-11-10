---
title: Registre de base de données
description: Cet article fournit des informations sur les tables de base de données de registre et les vues associées dans Azure SQL Database.
ms.date: 09/09/2021
ms.service: sql-database
ms.subservice: security
ms.reviewer: vanto
ms.topic: conceptual
author: rothja
ms.author: jroth
ms.openlocfilehash: f5273208104853a276ae78e8debc685fa2a05c4d
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132062205"
---
# <a name="what-is-the-database-ledger"></a>Qu’est-ce que le registre de base de données ?

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Le Registre Azure SQL Database est actuellement en préversion publique.

Le registre de base de données fait partie de la fonctionnalité de registre d’Azure SQL Database. Le registre de base de données capture de manière incrémentielle l’état d’une base de données à mesure qu’elle évolue, pendant que des mises à jour se produisent sur les tables du registre. Il utilise logiquement une blockchain et des [structures de données d’arborescence Merkle](/archive/msdn-magazine/2018/march/blockchain-blockchain-fundamentals). 

Pour capturer l’état de la base de données, le registre de base de données stocke une entrée pour chaque transaction. Il capture les métadonnées relatives à la transaction, telles que son horodatage de validation et l’identité de l’utilisateur qui l’a exécutée. Il capture également la racine de l’arborescence Merkle des lignes mises à jour dans chaque table de registre. Ces entrées sont ensuite ajoutées à une structure de données inviolable pour permettre la vérification de l’intégrité à l’avenir.

:::image type="content" source="media/ledger/merkle-tree.png" alt-text="Diagramme qui montre une arborescence Merkle pour la fonctionnalité de registre.":::

Pour plus d’informations sur la façon dont le registre Azure SQL Database fournit l’intégrité des données, consultez [Gestion des synthèses et vérification de base de données](ledger-digest-management-and-database-verification.md).

## <a name="where-are-database-transaction-and-block-data-stored"></a>Où sont stockées les données de bloc et de transaction de base de données ?

Les données relatives aux transactions et aux blocs sont stockées physiquement sous forme de lignes dans deux vues catalogue système :

- [sys.database_ledger_transactions](/sql/relational-databases/system-catalog-views/sys-database-ledger-transactions-transact-sql) : tient à jour une ligne avec les informations de chaque transaction dans le registre de base de données, notamment l’ID du bloc auquel cette transaction appartient et l’ordinal de la transaction dans le bloc. 
- [sys.database_ledger_blocks](/sql/relational-databases/system-catalog-views/sys-database-ledger-blocks-transact-sql) : tient à jour une ligne pour chaque bloc du registre, notamment la racine de l’arborescence Merkle sur les transactions au sein du bloc et le hachage du bloc précédent pour former une blockchain.

Pour afficher le registre de base de données, exécutez les instructions T-SQL suivantes dans [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) ou [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio).

> [!IMPORTANT]
> L’affichage du registre de base de données nécessite l’autorisation **VIEW LEDGER CONTENT**. Pour plus d’informations sur les autorisations associées aux tables du registre, consultez [Autorisations](/sql/relational-databases/security/permissions-database-engine#asdbpermissions). 

```sql
SELECT * FROM sys.database_ledger_transactions
GO

SELECT * FROM sys.database_ledger_blocks
GO
```

L’exemple de table de registre suivant se compose de quatre transactions qui constituent un bloc dans la blockchain du registre de base de données :

:::image type="content" source="media/ledger/database-ledger-1.png" alt-text="Capture d’écran d’un exemple de table de registre.":::

Un bloc est fermé toutes les 30 secondes, ou lorsque l’utilisateur génère manuellement une synthèse de la base de données en exécutant la procédure stockée [sys.sp_generate_database_ledger_digest](/sql/relational-databases/system-stored-procedures/sys-sp-generate-database-ledger-digest-transact-sql). 

Lorsqu’un bloc est fermé, les nouvelles transactions sont insérées dans un nouveau bloc. Le processus de génération de bloc effectue alors les opérations suivantes :

1. Il récupère toutes les transactions qui appartiennent au bloc *fermé* à partir de la file d’attente en mémoire et de la vue catalogue système [sys.database_ledger_transactions](/sql/relational-databases/system-catalog-views/sys-database-ledger-transactions-transact-sql).
1. Il calcule la racine de l’arborescence Merkle sur ces transactions et le hachage du bloc précédent.
1. Il rend persistant le bloc fermé dans la vue catalogue système [sys.database_ledger_blocks](/sql/relational-databases/system-catalog-views/sys-database-ledger-blocks-transact-sql). 

Étant donné qu’il s’agit d’une mise à jour de table ordinaire, sa durabilité est automatiquement garantie par le système. Pour conserver la chaîne unique de blocs, cette opération est à thread unique. Mais elle est également efficace, car elle calcule uniquement les hachages sur les informations de transaction et se produit de façon asynchrone. Elle n’affecte pas les performances des transactions.   

## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble du registre Azure SQL Database](ledger-overview.md) 
- [Vues catalogue liées à la sécurité (Transact-SQL)](/sql/relational-databases/system-catalog-views/security-catalog-views-transact-sql)
