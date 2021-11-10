---
title: Vérifier une table du registre pour détecter une falsification
description: Cet article explique comment vérifier si une table Azure SQL Database a été falsifiée.
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: how-to
author: rothja
ms.author: jroth
ms.reviewer: vanto
ms.date: 09/09/2021
ms.openlocfilehash: 272138471499cbd62085301df8e518ea47a7ddc3
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132058843"
---
# <a name="verify-a-ledger-table-to-detect-tampering"></a>Vérifier une table du registre pour détecter une falsification

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Le Registre Azure SQL Database est actuellement en préversion publique.

Cet article explique comment vérifier l’intégrité des données dans les tables de votre registre Azure SQL Database. Si vous avez sélectionné l’option **Activer le stockage de synthèse automatique** lorsque vous avez [créé votre base de données dans SQL Database](ledger-create-a-single-database-with-ledger-enabled.md), suivez les instructions du portail Azure pour générer automatiquement le script Transact-SQL (T-SQL) nécessaire pour vérifier le registre de base de données dans l’[éditeur de requête](connect-query-portal.md). Sinon, suivez les instructions T-SQL à l’aide de [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) ou d’[Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio).

## <a name="prerequisites"></a>Prérequis

- Avoir un abonnement Azure actif. Si vous n’en avez pas, [créez un compte gratuit](https://azure.microsoft.com/free/).
- [Créer une base de données dans SQL Database avec un registre activé](ledger-create-a-single-database-with-ledger-enabled.md)
- [Créer et utiliser des tables de registre pouvant être mises à jour](ledger-how-to-updatable-ledger-tables.md) ou [créer et utiliser des tables de registre par ajout uniquement](ledger-how-to-append-only-ledger-tables.md)

## <a name="run-ledger-verification-for-sql-database"></a>Exécuter une vérification de registre pour SQL Database

# <a name="portal"></a>[Portail](#tab/azure-portal)

1. Ouvrez le [portail Azure](https://portal.azure.com/), sélectionnez **Toutes les ressources**, puis recherchez la base de données que vous souhaitez vérifier. Sélectionnez cette base de données dans SQL Database.

     :::image type="content" source="media/ledger/ledger-portal-all-resources.png" alt-text="Capture d’écran montant le portail Azure avec l’onglet Toutes les ressources sélectionné.":::

1. Dans **Sécurité**, sélectionnez l’option **Registre**.

   :::image type="content" source="media/ledger/ledger-portal-manage-ledger.png" alt-text="Capture d’écran montant le portail Azure avec l’onglet Registre de sécurité sélectionné.":::

1. Dans le volet **Registre**, sélectionnez **</> Vérifier la base de données**, puis l’icône **Copie** dans le texte pré-rempli dans la fenêtre.

     :::image type="content" source="media/ledger/ledger-portal-verify.png" alt-text="Bouton Vérifier la base de données du portail Azure":::

   > > [!IMPORTANT]
   > Si vous n’avez pas configuré le stockage de synthèse automatique pour les synthèses de votre base de données et que vous gérez manuellement les synthèses, ne copiez pas ce script. Passez à l’étape 6.

1. Ouvrez l’**Éditeur de requête** dans le menu de gauche.

     :::image type="content" source="media/ledger/ledger-portal-open-query-editor.png" alt-text="Capture d’écran montrant l’option de menu Éditeur de requête du portail Azure.":::

1. Dans l’éditeur de requête, collez le script T-SQL que vous avez copié à l’étape 3, puis sélectionnez **Exécuter**. Passez à l’étape 8.

   :::image type="content" source="media/ledger/ledger-portal-run-query-editor.png" alt-text="Capture d’écran montrant l’option Exécuter de l’éditeur de requête dans le portail Azure pour vérifier la base de données.":::

1. Si vous utilisez le stockage de synthèse manuel, entrez le T-SQL suivant dans l’éditeur de requête pour récupérer votre dernière synthèse de base de données. Copiez la synthèse à partir des résultats renvoyés pour l’étape suivante.

   ```sql
   EXECUTE sp_generate_database_ledger_digest
   ```
   
1. Dans l’éditeur de requête, collez le T-SQL suivant, en remplaçant `<database_digest>` par la synthèse que vous avez copiée à l’étape 6, puis sélectionnez **Exécuter**.

   ```sql
   EXECUTE sp_verify_database_ledger N'<database_digest>'
   ```

1. La vérification renvoie les messages suivants dans la fenêtre **Résultats**.

   - Si votre base de données n’a pas été falsifiée, le message est :

       ```output
       Ledger verification successful
       ```

   - Si votre base de données a été falsifiée, l’erreur suivante apparaît dans la fenêtre **Messages**.
  
       ```output
       Failed to execute query. Error: The hash of block xxxx in the database ledger does not match the hash provided in the digest for this block.
       ```

# <a name="t-sql-using-automatic-digest-storage"></a>[T-SQL avec stockage de synthèse automatique](#tab/t-sql-automatic)

1. Connectez-vous à votre base de données à l’aide de [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) ou d’[Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio).

1. Créez une requête avec l’instruction T-SQL suivante :

   ```sql
   DECLARE @digest_locations NVARCHAR(MAX) = (SELECT * FROM sys.database_ledger_digest_locations FOR JSON AUTO, INCLUDE_NULL_VALUES);SELECT @digest_locations as digest_locations;
   BEGIN TRY
       EXEC sys.sp_verify_database_ledger_from_digest_storage @digest_locations;
       SELECT 'Ledger verification succeeded.' AS Result;
   END TRY
   BEGIN CATCH
       THROW;
   END CATCH
   ```

1. exécutez la requête. Vous verrez que **digest_locations** renvoie l’emplacement actuel de stockage de vos synthèses de base de données et tout éventuel emplacement précédent. **Result** renvoie la réussite ou la défaillance de la vérification du registre.

   :::image type="content" source="media/ledger/verification_script_exectution.png" alt-text="Capture d’écran de l’exécution de la vérification du registre avec Azure Data Studio.":::

1. Ouvrez l’ensemble de résultats de **digest_locations** pour afficher les emplacements de vos synthèses. L’exemple suivant montre deux emplacements de stockage de synthèse pour cette base de données : 

   - **path** indique l’emplacement des synthèses.
   - **last_digest_block_id** indique l’ID de bloc de la dernière synthèse stockée dans l’emplacement **path**.
   - **is_current** indique si l’emplacement dans **path** est l’actuel (true) ou le précédent (false).

       ```json
       [
        {
            "path": "https:\/\/digest1.blob.core.windows.net\/sqldbledgerdigests\/janderstestportal2server\/jandersnewdb\/2021-05-20T04:39:47.6570000",
            "last_digest_block_id": 10016,
            "is_current": true
        },
        {
            "path": "https:\/\/jandersneweracl.confidential-ledger.azure.com\/sqldbledgerdigests\/janderstestportal2server\/jandersnewdb\/2021-05-20T04:39:47.6570000",
            "last_digest_block_id": 1704,
            "is_current": false
        }
       ]
       ```

   > [!IMPORTANT]
   > Lorsque vous exécutez la vérification du registre, inspectez l’emplacement de **digest_locations** pour vous assurer que les synthèses utilisées dans la vérification sont récupérés à partir des emplacements attendus. Vous devez vous assurer qu’un utilisateur privilégié n’a pas remplacé les emplacements de stockage de synthèse par un emplacement de stockage non protégé, tel que le Stockage Azure, sans avoir configuré et verrouillé de stratégie d’immuabilité.

1. La vérification renvoie le message suivant dans la fenêtre **Résultats**.

   - Si votre base de données n’a pas été falsifiée, le message est :

       ```output
       Ledger verification successful
       ```

   - Si votre base de données a été falsifiée, l’erreur suivante apparaît dans la fenêtre **Messages** :
  
       ```output
       Failed to execute query. Error: The hash of block xxxx in the database ledger doesn't match the hash provided in the digest for this block.
       ```

# <a name="t-sql-using-manual-digest-storage"></a>[T-SQL avec le stockage de synthèse manuel](#tab/t-sql-manual)

1. Connectez-vous à votre base de données à l’aide de [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) ou d’[Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio).
1. Créez une requête avec l’instruction T-SQL suivante :

   ```sql
   /****** This will retrieve the latest digest file  ******/
   EXECUTE sp_generate_database_ledger_digest
   ```

1. exécutez la requête. Les résultats contiennent la dernière synthèse de base de données et représentent le hachage de la base de données au point actuel dans le temps. Copiez le contenu des résultats à utiliser à l’étape suivante.

   :::image type="content" source="media/ledger/ledger-retrieve-digest.png" alt-text="Capture d’écran montrant les résultats de récupération des synthèses avec Azure Data Studio.":::

1. Créez une requête avec l’instruction T-SQL suivante. Remplacez `<YOUR DATABASE DIGEST>` par la synthèse que vous avez copiée à l’étape précédente.

   ```
   /****** Verifies the integrity of the ledger using the referenced digest  ******/
   EXECUTE sp_verify_database_ledger N'
   <YOUR DATABASE DIGEST>
   '
   ```

1. exécutez la requête. La fenêtre **Messages** contient le message de réussite suivant.

   :::image type="content" source="media/ledger/ledger-verify-message.png" alt-text="Capture d’écran montrant le message après l’exécution de la requête T-SQL pour la vérification du registre à l’aide d’Azure Data Studio.":::

   > [!TIP]
   > L’exécution de la vérification du registre avec la dernière synthèse vérifie uniquement la base de données entre le moment où la synthèse a été générée et l’exécution de la vérification. Pour vérifier que les données historiques dans votre base de données n’ont pas été falsifiées, exécutez la vérification à l’aide de plusieurs fichiers de synthèse de base de données. Commencez par le point dans le temps pour lequel vous souhaitez vérifier la base de données. Un exemple de vérification transmettant plusieurs synthèses ressemblerait à la requête suivante.

   ```
   EXECUTE sp_verify_database_ledger N'
   [
       {
           "database_name":  "ledgerdb",
           "block_id":  0,
           "hash":  "0xDC160697D823C51377F97020796486A59047EBDBF77C3E8F94EEE0FFF7B38A6A",
           "last_transaction_commit_time":  "2020-11-12T18:01:56.6200000",
           "digest_time":  "2020-11-12T18:39:27.7385724"
       },
       {
           "database_name":  "ledgerdb",
           "block_id":  1,
           "hash":  "0xE5BE97FDFFA4A16ADF7301C8B2BEBC4BAE5895CD76785D699B815ED2653D9EF8",
           "last_transaction_commit_time":  "2020-11-12T18:39:35.6633333",
           "digest_time":  "2020-11-12T18:43:30.4701575"
       }
   ]
   ```

---

## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble du registre Azure SQL Database](ledger-overview.md)
- [Registre SQL Database](ledger-database-ledger.md)
- [Gestion des synthèses et vérification de base de données](ledger-digest-management-and-database-verification.md)
- [Tables de registre d’ajout uniquement](ledger-append-only-ledger-tables.md)
- [Tables de registre pouvant être mises à jour](ledger-updatable-ledger-tables.md)
- [Accéder aux synthèses stockées dans Registre confidentiel Azure](ledger-how-to-access-acl-digest.md)
