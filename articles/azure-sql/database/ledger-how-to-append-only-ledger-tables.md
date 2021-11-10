---
title: Créer et utiliser des tables de registre d’ajout uniquement
description: Découvrez comment créer et utiliser des tables de registre d’ajout uniquement dans Azure SQL Database.
ms.date: 09/09/2021
ms.service: sql-database
ms.subservice: security
ms.reviewer: vanto
ms.topic: how-to
author: rothja
ms.author: jroth
ms.openlocfilehash: 09b9d4dceabff4b26c586b0035f4c8b8d4c2ba0a
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132063569"
---
# <a name="create-and-use-append-only-ledger-tables"></a>Créer et utiliser des tables de registre d’ajout uniquement

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Le Registre Azure SQL Database est actuellement en préversion publique.

Cet article explique comment créer une [table de registre d’ajout uniquement](ledger-append-only-ledger-tables.md) dans Azure SQL Database. Ensuite, vous allez insérer des valeurs dans votre table de registre d’ajout uniquement, puis tenter de mettre à jour les données. Enfin, vous visualiserez les résultats en utilisant l’affichage du registre. Nous allons utiliser l’exemple d’un système d’accès à une installation avec une clé de carte, qui est un modèle de système d’ajout uniquement. Notre exemple vous donne un aperçu pratique de la relation entre la table de registre d’ajout uniquement et la vue de registre correspondante.

Pour plus d’informations, consultez [Tables de registre d’ajout uniquement](ledger-append-only-ledger-tables.md).

## <a name="prerequisites"></a>Prérequis

- Azure SQL Database avec registre activé. Si vous n’avez pas encore créé de base de données dans Azure SQL Database, consultez [Démarrage rapide : Créer une base de données dans Azure SQL Database avec le registre activé](ledger-create-a-single-database-with-ledger-enabled.md).
- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) ou [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio).

## <a name="create-an-append-only-ledger-table"></a>Créer une table de registre d’ajout uniquement

Nous créons une table `KeyCardEvents` avec le schéma suivant.

| Nom de la colonne | Type de données | Description |
|--|--|--|
| EmployeeID | int | ID unique de l’employé qui accède au bâtiment |
| AccessOperationDescription | nvarchar (MAX) | Opération d’accès de l’employé |
| Timestamp | datetime2 | Date et heure auxquelles l’employé a accédé au bâtiment |

> [!IMPORTANT]
> La création de tables de registre d’ajout uniquement nécessite l’autorisation **ENABLE LEDGER**. Pour plus d’informations sur les autorisations associées aux tables du registre, consultez [Autorisations](/sql/relational-databases/security/permissions-database-engine#asdbpermissions). 

1. Utilisez [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) ou [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio) pour créer un schéma et une table appelés `[AccessControl].[KeyCardEvents]`.

   ```sql
   CREATE SCHEMA [AccessControl] 
   CREATE TABLE [AccessControl].[KeyCardEvents]
       (
           [EmployeeID] INT NOT NULL,
           [AccessOperationDescription] NVARCHAR (MAX) NOT NULL,
           [Timestamp] Datetime2 NOT NULL
       )
       WITH (
          LEDGER = ON (
                       APPEND_ONLY = ON
                       )
         );
   ```

1. Ajoutez un nouvel événement d’accès au bâtiment dans la table `[AccessControl].[KeyCardEvents]` avec les valeurs suivantes.

   ```sql
   INSERT INTO [AccessControl].[KeyCardEvents]
   VALUES ('43869', 'Building42', '2020-05-02T19:58:47.1234567')
   ```

1. Consultez le contenu de votre table KeyCardEvents et spécifiez les colonnes [GENERATED ALWAYS](/sql/t-sql/statements/create-table-transact-sql#generate-always-columns) qui sont ajoutées à votre [table de registre d’ajout uniquement](ledger-append-only-ledger-tables.md).

   ```sql
   SELECT *
        ,[ledger_start_transaction_id]
        ,[ledger_start_sequence_number]
   FROM [AccessControl].[KeyCardEvents]
   ```

   :::image type="content" source="media/ledger/append-only-how-to-keycardevent-table.png" alt-text="Capture d’écran montrant les résultats de l’interrogation de la table KeyCardEvents.":::

1. Essayez de mettre à jour la table `KeyCardEvents` en remplaçant la valeur `EmployeeID` `43869` par `34184.`

   ```sql
   UPDATE [AccessControl].[KeyCardEvents] SET [EmployeeID] = 34184
   ```

   Vous recevrez un message d’erreur indiquant que les mises à jour ne sont pas autorisées pour votre table de registre d’ajout uniquement.

   :::image type="content" source="media/ledger/append-only-how-to-1.png" alt-text="Capture d’écran montrant le message d’erreur d’ajout uniquement.":::

## <a name="next-steps"></a>Étapes suivantes

- [Registre de base de données](ledger-database-ledger.md) 
- [Gestion des synthèses et vérification de base de données](ledger-digest-management-and-database-verification.md)
- [Tables de registre d’ajout uniquement](ledger-append-only-ledger-tables.md) 
- [Tables de registre pouvant être mises à jour](ledger-updatable-ledger-tables.md)
- [Créer et utiliser des tables de registre pouvant être mises à jour](ledger-how-to-updatable-ledger-tables.md)
- [Accéder aux synthèses stockées dans Registre confidentiel Azure](ledger-how-to-access-acl-digest.md)
- [Vérifier une table du registre pour détecter une falsification](ledger-verify-database.md)
