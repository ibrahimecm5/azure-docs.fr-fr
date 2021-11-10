---
title: Limitations relatives au registre Azure SQL Database
description: Limitations de la fonctionnalité de registre dans Azure SQL Database
ms.date: 09/09/2021
ms.service: sql-database
ms.subservice: security
ms.reviewer: vanto
ms.topic: conceptual
author: rothja
ms.author: jroth
ms.openlocfilehash: 684ca40f1469b826029f1b0bcc51e33ae0d9c9a5
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132053687"
---
# <a name="limitations-for-azure-sql-database-ledger"></a>Limitations relatives au registre Azure SQL Database

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Le Registre Azure SQL Database est actuellement en préversion publique.

Cet article fournit une vue d’ensemble des limitations des tables de registre utilisées avec Azure SQL Database.

## <a name="limitations"></a>Limites

| Fonction | Limitation |
| :--- | :--- |
| Désactivation de la [base de données du registre](ledger-database-ledger.md)   | Après avoir été activée, une base de données de registre ne peut pas être désactivée. |
| Nombre maximal de colonnes | Quand une [table de registre pouvant être mise à jour](ledger-updatable-ledger-tables.md) est créée, elle ajoute quatre colonnes [GENERATED ALWAYS](/sql/t-sql/statements/create-table-transact-sql#generate-always-columns) à la table de registre. Une [table de registre en ajout seul](ledger-append-only-ledger-tables.md) ajoute deux colonnes à la table de registre. Ces nouvelles colonnes sont décomptées par rapport au nombre maximal de colonnes prises en charge dans SQL Database (1 024). |
| Types de données restreints | Les types de données XML, SqlVariant, définis par l’utilisateur et FILESTREAM ne sont pas pris en charge. |
| Tables en mémoire | Les tables en mémoire ne sont pas prises en charge. |
| Jeux de colonnes éparses | Les jeux de colonnes éparses ne sont pas pris en charge. |
| Troncation du registre | La suppression des données anciennes dans les [tables de registre en ajout seul](ledger-append-only-ledger-tables.md) ou la table d’historique de [tables de registre pouvant être mises à jour](ledger-updatable-ledger-tables.md) n’est pas prise en charge. |
| Conversion de tables existantes en tables de registre | Les tables existantes dans une base de données qui ne sont pas compatibles avec le registre ne peuvent pas être converties en tables du registre. |
|Prise en charge du stockage localement redondant (LRS) pour la [gestion automatisée des synthèses](ledger-digest-management-and-database-verification.md) | La gestion automatisée des synthèses avec les tables de registre à l’aide d’[objets blob immuables Stockage Azure](../../storage/blobs/immutable-storage-overview.md) n’offre pas la possibilité pour les utilisateurs d’utiliser des comptes [LRS](../../storage/common/storage-redundancy.md#locally-redundant-storage).|

## <a name="remarks"></a>Remarques

- Lorsqu’une base de données de registre est créée, toutes les nouvelles tables créées par défaut (sans spécifier la clause `APPEND_ONLY = ON`) dans la base de données sont des [tables de registre pouvant être mises à jour](ledger-updatable-ledger-tables.md). Pour créer des [tables de registre en ajout seul](ledger-append-only-ledger-tables.md), utilisez des instructions [CREATE TABLE (Transact-SQL)](/sql/t-sql/statements/create-table-transact-sql).
- Les tables du registre ne peuvent pas être des FILETABLE.
- Les tables du registre ne peuvent pas avoir d’index de recherche en texte intégral.
- Il est impossible de renommer les tables du registre.
- Les tables du registre ne peuvent pas être déplacées vers un autre schéma.
- Seules des colonnes pouvant accepter la valeur Null peuvent être ajoutées à des tables de registre, et seulement quand WITH VALUES n’est pas spécifié.
- Les colonnes des tables de registre ne peuvent pas être supprimées.
- Seules les colonnes calculées déterministes sont autorisées pour les tables du registre.
- Les colonnes existantes ne peuvent pas être modifiées d’une manière qui modifie le format de cette colonne.
  - Nous autorisons la modification de ce qui suit :
    - la possibilité de valeurs nulles ;
    - Classement des colonnes nvarchar/ntext et quand la page de codes ne change pas pour les colonnes de type char/text
    - Longueur des colonnes de longueur variable
    - Caractère épars
- SWITCH IN/OUT n’est pas autorisé pour les tables du registre.
- Les sauvegardes à long terme (LTR) ne sont pas prises en charge pour les bases de données qui ont `LEDGER = ON`.
- Un versioning `LEDGER` ou `SYSTEM_VERSIONING` ne peut pas être désactivé pour les tables de registre.
- Les API `UPDATETEXT` et `WRITETEXT` ne peuvent pas être utilisées sur des tables de registre.
- Une transaction peut mettre à jour jusqu’à 200 tables de registre.
- Pour les tables de registre pouvant être mises à jour, nous héritons de toutes les limitations des tables temporelles.
- Le suivi des modifications n’est pas autorisé sur les tables du registre.
- Les tables de registre ne peuvent pas avoir d’index rowstore non-cluster quand elles ont un index columnstore clusterisé.

## <a name="next-steps"></a>Étapes suivantes

- [Tables de registre pouvant être mises à jour](ledger-updatable-ledger-tables.md)
- [Tables de registre d’ajout uniquement](ledger-append-only-ledger-tables.md)
- [Registre de base de données](ledger-database-ledger.md)
- [Gestion des synthèses et vérification de base de données](ledger-digest-management-and-database-verification.md)